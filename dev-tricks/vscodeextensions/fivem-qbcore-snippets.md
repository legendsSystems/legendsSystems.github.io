# FiveM QBCore Snippets

## legendsSystems Fivem Qbcore Snippets

<div align="center">

<img src="https://img.shields.io/github/languages/top/legendsSystems/legendsDocs?color=56BEB8" alt="Github top language">

 

<img src="https://img.shields.io/github/languages/count/legendsSystems/legendsDocs?color=56BEB8" alt="Github language count">

 

<img src="https://img.shields.io/github/repo-size/legendsSystems/legendsDocs?color=56BEB8" alt="Repository size">

 

<img src="https://img.shields.io/github/license/legendsSystems/legendsDocs?color=56BEB8" alt="License">

</div>

### How to Use

> Tab Completion available for all required fields!

This repository contains a collection of code snippets for the FiveM QBCore framework. To use these snippets in Visual Studio Code, follow these steps:

1. Use `qb` to search for the snippet you want to add to your project.&#x20;

### Features

This repository provides a wide range of code snippets to streamline your development process with the FiveM QBCore framework. Here are some of the key features:

* **Client-side Snippets**: Includes snippets for creating client-side events, registering commands, and more.
* **Server-side Snippets**: Offers snippets for server-side events, database interactions, and other server-related functionality.
* **Shared Snippets**: Provides snippets for shared code that can be used on both the client and server sides.
* **Utility Snippets**: Includes snippets for common utility functions, such as vector math, string manipulation, and more.
* **Localization Snippets**: Offers snippets for handling localization and multi-language support.
* **Customizable**: You can easily modify and extend the snippets to suit your specific project requirements.

By using these code snippets, you can save time and increase your productivity while working with the FiveM QBCore framework.

Many snippets available and opinionated for QBCore.

Simple

```lua
local Player = QBCore.Functions.GetPlayer(source)
```

Medium

```lua
QBCore.Functions.Progressbar('name', 'Text that shows in bar', 5000, false, true, { -- Name | Label | Time | useWhileDead | canCancel
    disableMovement = true,
    disableCarMovement = true,
    disableMouse = false,
    disableCombat = true,
}, {
    animDict = 'anim@gangops@facility@servers@',
    anim = 'hotwire',
    flags = 16,
}, {}, {}, function() -- Play When Done
    --Stuff goes here
end, function() -- Play When Cancel
    --Stuff goes here
end)
```

Or for a quick starter on a fresh script

```lua
local QBCore = exports['qb-core']:GetCoreObject()
local PlayerData = QBCore.Functions.GetPlayerData()
local isLoggedIn = LocalPlayer.state.isLoggedIn
```

> \[INFO] If you are using the [okokTextUI](https://github.com/okok/okokTextUI) library, you can use the following code to easily replace your qb-core/client/drawtext.lua file with the below code and use the drawText function but get a okok textUI instead.

```lua
local isOpen = false
local function hideText()
    if isOpen then
        exports['okokTextUI']:Close()
        isOpen = false
    else
        return
    end
end

local function drawText(text, position)
    if type(position) ~= "string" then position = "left" end
    if not isOpen then
        exports['okokTextUI']:Open(text, 'darkblue', 'left')
        isOpen = true
    else
        return
    end
end

local function changeText(text, position)
    if type(position) ~= "string" then position = "left" end
    if isOpen then
        exports['okokTextUI']:Close()
        exports['okokTextUI']:Open(text, 'darkblue', 'left')
    else
        return
    end
end

local function keyPressed()
    CreateThread(function()
        SendNUIMessage({
            action = 'KEY_PRESSED',
        })
        Wait(500)
        hideText()
    end)
end

RegisterNetEvent('qb-core:client:DrawText', function(text, position)
    if not isOpen then
        exports['okokTextUI']:Open(text, 'darkblue', 'left')
        isOpen = true
    else
        return
    end
end)

RegisterNetEvent('qb-core:client:ChangeText', function(text, position)
    if isOpen then
        exports['okokTextUI']:Close()
        exports['okokTextUI']:Open(text, 'darkblue', 'left')
    else
        return
    end
end)

RegisterNetEvent('qb-core:client:HideText', function()
    if isOpen then
        exports['okokTextUI']:Close()
        isOpen = false
    else
        return
    end
end)

RegisterNetEvent('qb-core:client:KeyPressed', function()
    keyPressed()
end)

exports('DrawText', drawText)
exports('ChangeText', changeText)
exports('HideText', hideText)
exports('KeyPressed', keyPressed)
```

