# Configuration

Add additional microphone stands

```lua
Config.MicrophoneZones = {
    [1] = {
        name = "vinewood_bowl", -- unique name of the zone
        coords = vector3(683.37, 569.31, 130.46), -- coords of the created boxzone
        length = 3.4, -- length of the created boxzone
        width = 3.6, -- width of the created boxzone
        spawnProp = true, -- if set to true, it will let you spawn the prop at location
        data = {
            debugPoly = Config.Showzone, 
            heading = 340, -- heading from created boxzone
            minZ = 127.86, --minZ from the created boxzone
            maxZ = 131.86, -- maxZ from the created boxzone
            data = {
                range = 50.0 -- range for the voice at that particular boxzone
            }
        }
    }
}
```
