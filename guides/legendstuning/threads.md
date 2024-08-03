# Threads

## How to fix resource performance

## Assessment

_Without_ having any markers created, the resource already runs at 0.02-0.03ms, but we are not even doing distance checks yet.

I have created a simple command that just spam creates 500 markers with an offset on each, let's look at the performance now:

Performance is terrible now, and even without rendering _any_ we are above 0.4ms, which is unacceptable.

Let's look at some code now.

```lua
Citizen.CreateThread(function()
    while true do
        Citizen.Wait(0)
        local playerPed = PlayerPedId()
        local coords = GetEntityCoords(playerPed)
        local isInMarker = false
        local lastMarker = nil
        for k, v in pairs(markers) do
            local distance = GetDistanceBetweenCoords(coords.x, coords.y, coords.z, v.coords.x, v.coords.y, v.coords.z, true)
            if distance < Config.DrawDistance and v.shouldDraw() then
                if v.show3D then
                    if distance < Config.Draw3DDistance then
                        DrawText3D(v.coords, v.msg, 0.5)
                    end
                elseif v.type ~= -1 then
                    DrawMarker(v.type, v.coords, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, v.size.x, v.size.y, v.size.z, v.colour.r, v.colour.g, v.colour.b, 100, getOrElse(v.bob, false), true, 2, getOrElse(v.rotate, true), false, false, false)
                end
            end
            if distance < v.size.x and v.shouldDraw() then
                if v.enableE then
                    EnableControlAction(0, 38)
                end
                isInMarker = true
                lastMarker = v
            end
        end

        if isInMarker and not HasAlreadyEnteredMarker then
            HasAlreadyEnteredMarker = true
            TriggerEvent('disc-base:hasEnteredMarker', lastMarker)
        end
        if not hasExited and not isInMarker and HasAlreadyEnteredMarker then
            HasAlreadyEnteredMarker = false
            TriggerEvent('disc-base:hasExitedMarker')
        end
    end
end)
```

Here we can see the "main thread" that works coordinate checks. It loops every thread, and triggers events if you enter or exit on the first time. So how can we improve this?

### Remove the native for coord distance calculation

Natives are _slow_. FiveM has utilities to make checks like those much faster.

`local distance = GetDistanceBetweenCoords(coords.x, coords.y, coords.z, v.coords.x, v.coords.y, v.coords.z, true)`

gets changed to:

`local distance = #(coords - v.coords)`

Outside render distance we now have the following: ![Improvements outside render distance](https://iam.malding.dev/i/766ff75edd.png)

0.28ms! Wow, we have already shaved of 0.15ms, with a change of one line. Use #(a-b) for distance checks, instead of any natives.

### Splitting up loops:

This doesn't make us happy enough though. Let's split up the threads into two.

```lua
local drawnMarkers = {]
CreateThread(function ()
    while true do
        Citizen.Wait(500)
        local playerPed = PlayerPedId()
        local coords = GetEntityCoords(playerPed)
        local isInMarker = false
        local lastMarker = nil
        drawnMarkers = {}
        for k, v in pairs(markers) do
            local distance = #(coords - v.coords)
            if distance < Config.DrawDistance and v.shouldDraw() then
                table.insert(drawnMarkers, v)
            end
        end
    end
end)

Citizen.CreateThread(function()
    while true do
        Citizen.Wait(0)
        local playerPed = PlayerPedId()
        local coords = GetEntityCoords(playerPed)
        local isInMarker = false
        local lastMarker = nil
        for k, v in pairs(drawnMarkers) do
            local distance = #(coords - v.coords)
            if distance < Config.DrawDistance then
                if v.show3D then
                    if distance < Config.Draw3DDistance then
                        DrawText3D(v.coords, v.msg, 0.5)
                    end
                elseif v.type ~= -1 then
                    DrawMarker(v.type, v.coords, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, v.size.x, v.size.y, v.size.z, v.colour.r, v.colour.g, v.colour.b, 100, getOrElse(v.bob, false), true, 2, getOrElse(v.rotate, true), false, false, false)
                end
            end
            if distance < v.size.x and v.shouldDraw() then
                if v.enableE then
                    EnableControlAction(0, 38)
                end
                isInMarker = true
                lastMarker = v
            end
        end

        if isInMarker and not HasAlreadyEnteredMarker then
            HasAlreadyEnteredMarker = true
            TriggerEvent('disc-base:hasEnteredMarker', lastMarker)
        end
        if not hasExited and not isInMarker and HasAlreadyEnteredMarker then
            HasAlreadyEnteredMarker = false
            TriggerEvent('disc-base:hasExitedMarker')
        end
    end
end)
```

Now we have split this into two threads, you will notice the main thread we looked at before has basically not changed. The only thing we changed is `markers` -> `drawnMarkers` when we start the for loop.

We add a new variable called `drawnMarkers` including all markers that are currently in draw range.

In addition to this, we have added a completely new thread that runs every 500 frames, and repopulates the `drawnMarker` table with the now new entries.

Let's now check the performance:

Now we are starting to see massive performance improvements. Outside render range we are down to 0.06ms, inside a marker directly we are at 0.20ms

Now why is the performance so terrible inside a marker? Let's look at ESX' help notification function.

```lua
ShowHelpNotification = function(msg, thisFrame, beep, duration)
AddTextEntry('esxHelpNotification', msg)

if thisFrame then
    DisplayHelpTextThisFrame('esxHelpNotification', false)
else
    if beep == nil then beep = true end
    BeginTextCommandDisplayHelp('esxHelpNotification')
    EndTextCommandDisplayHelp(0, false, beep, duration or -1)
end
```

This displays a help text inside which is just "text example" in our markers. What this also does is add a text entry _every frame_ together with displaying the help text. A simple improvement would be deleting the line of `AddTextEntry('esxHelpNotification', msg)` and adding the following to the end of the `disc-base:hasEnteredMarker` event:

```lua
if not CurrentMarker.show3D and CurrentMarker.msg then
    AddTextEntry('esxHelpNotification', CurrentMarker.msg)
end
```

This saves about 0.02ms once again, we want to use **the least amount of native calls possible**.

### Digression: Render distances

As of the config file I have here, the default render distance is 30 units. That is _way too much_. Reducing this distance to 10 or 15 units cuts down resource time drastically especially when there is a lot of markers in small range. Reducing our example to 10 units brings it to around 0.06ms

Right now we are just spawning markers close to ourselves. If we were to run, let's say, an amazing RP server that has like 400 markers all across the map somewhere, 0.06ms is still too much. How would we tackle this?

My suggestion would be the following:

1. When registering markers, get the zone this marker is in, and add it to a sub-table of our markers table
2. Only loop markers of your current zone
3. Profit

Adjust the `disc-base:registerMarker` event at the end to the following:

```lua
local zone = GetZoneAtCoords(marker.coords.x, marker.coords.y, marker.coords.z)

if markers[zone] == nil then
    markers[zone] = {}
end

markers[zone][marker.name] = marker
```

Our 500ms thread that initially loops over markers now gets adjusted like this:

```lua
drawnMarkers = {}
local zone = GetZoneAtCoords(coords.x, coords.y, coords.z)
if markers[zone] ~= nil then
    for k, v in pairs(markers[zone]) do
        local distance = #(coords - v.coords)
        if distance < Config.DrawDistance and v.shouldDraw() then
            table.insert(drawnMarkers, v)
        end
    end
end
```

To unregister a marker correctly, also make the following change:

```lua
RegisterNetEvent('disc-base:removeMarker')
AddEventHandler('disc-base:removeMarker', function(name)
    for k, v in pairs(markers) do
        markers[k][name] = nil
    end
end)
```

Now, we will not directly notice a performance improvement, but if we adjust our thread that runs every frame to this, we will see drastic improvements again:

```lua
Citizen.CreateThread(function()
    while true do
        local isInMarker = false
        local lastMarker = nil
        if #drawnMarkers > 0 then
            local playerPed = PlayerPedId()
            local coords = GetEntityCoords(playerPed)
            for k, v in pairs(drawnMarkers) do
                local distance = #(coords - v.coords)
                if distance < Config.DrawDistance then
                    if v.show3D then
                        if distance < Config.Draw3DDistance then
                            DrawText3D(v.coords, v.msg, 0.5)
                        end
                    elseif v.type ~= -1 then
                        DrawMarker(v.type, v.coords, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, v.size.x, v.size.y, v.size.z, v.colour.r, v.colour.g, v.colour.b, 100, getOrElse(v.bob, false), true, 2, getOrElse(v.rotate, true), false, false, false)
                    end
                end
                if distance < v.size.x and v.shouldDraw() then
                    if v.enableE then
                        EnableControlAction(0, 38)
                    end
                    isInMarker = true
                    lastMarker = v
                end
            end
        end

        if isInMarker and not HasAlreadyEnteredMarker then
            HasAlreadyEnteredMarker = true
            TriggerEvent('disc-base:hasEnteredMarker', lastMarker)
        end
        if not hasExited and not isInMarker and HasAlreadyEnteredMarker then
            HasAlreadyEnteredMarker = false
            TriggerEvent('disc-base:hasExitedMarker')
        end

        if CurrentMarker and CurrentMarker.shouldDraw() then
            if not CurrentMarker.show3D and CurrentMarker.msg then
                ShowHelpNotification(CurrentMarker.msg)
            end

            if IsControlJustReleased(0, 38) then
                if CurrentMarker.action ~= nil then
                    CurrentMarker.action(CurrentMarker)
                end
            end
        end

        if #drawnMarkers > 0 then
            Citizen.Wait(1)
        else
            Citizen.Wait(500)
        end
    end
end`
```

We move the other thread into the same thread that checks for the current marker. It does not need to be its own thread, it does just fine in here. However I would recommend some refactor so it just runs a function in here that does the same as this.

Also, we only run natives like grabbing the ped or the coordinates, or _any_ distance checks if there is at least one element in our `drawnMarkers` table. Also, at the end we run a 1 frame wait if that table has more than one element, if not we run a 500 frame wait. This drops the resource time to **0.01ms** if there are no markers drawn.

Now, our performance is still about 0.07ms looking at about 11 markers, but that is acceptable. But we can do more.

## We want more FPS

If we add another key on the markers we put in the `drawnMarker` check, we can save up some more resource:

```lua
for k, v in pairs(markers[zone]) do
    local distance = #(coords - v.coords)
    if distance < Config.DrawDistance and v.shouldDraw() then
        v.distance = distance
        table.insert(drawnMarkers, v)
    end
end
```

Running this, we can shorten the main thread loop to this:

```lua
while true do
    local isInMarker = false
    local lastMarker = nil
    if #drawnMarkers > 0 then
        for k, v in pairs(drawnMarkers) do
            if v.show3D then
                if v.distance < Config.Draw3DDistance then
                    DrawText3D(v.coords, v.msg, 0.5)
                end
            elseif v.type ~= -1 then
                DrawMarker(v.type, v.coords, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, v.size.x, v.size.y, v.size.z, v.colour.r, v.colour.g, v.colour.b, 100, getOrElse(v.bob, false), true, 2, getOrElse(v.rotate, true), false, false, false)
            end
            if v.distance < v.size.x then
                isInMarker = true
                lastMarker = v
            end
        end
    end
...
```

Now, we are dropping as far down as 0.05 when rendering 11 markers, which is fantastic. Other than some adjustments to the waits (i.e. dropping it to 1000 from 500, which is certainly viable) we have achieved massive performance improvements in just a short amount of time.

## Takeaways

* Check if you can split up your threads into multiple threads with longer waits. Filtering out most non-viable markers every 500ms instead of on tick, while keeping the main tick loop is drastic.
* Use the least amount of natives possible. Save where you can.
* Do not repeat yourself, some natives do not need to run every frame!
* See where you can condense threads together if they have the same wait times. You don't need 2 threads that each run every tick, you can just make it one.
* Something that disc-base already applies, work with events! I personally do this a lot in my private projects, an example would be:
  * Instead of checking every x ms if the player is in a vehicle, use the `baseevents` resource that comes with the cfx-server-data and their `enteredVehicle` and `exitedVehicle` events.
* **Refactor your code**, generalize it. A lot of code can be reused in so many other parts of your code.
