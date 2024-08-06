# Configuration

Add additional microphone stands

```lua
Config = {}
Config.DebugPoly = false
Config.MicrophoneStands = {
	[1] = {
		name = "maze_bank",
		spawnProp = false,
		coords = vector3(-322.34, -1966.29, 22.94),
		heading = 316.33,
		length = 3.4,
		width = 3.6,
		data = {
			minZ = 18.00,
			maxZ = 50.00,
			data = {
				range = 150.0, -- range for the voice
			},
		},
	},
	[2] = {
		name = "lostmc",
		spawnProp = false,
		coords = vector3(2510.31, 4087.83, 38.29),
		heading = 46.00,
		length = 1.4,
		width = 1.6,
		data = {
			minZ = 18.00,
			maxZ = 50.00,
			data = {
				range = 150.0, -- range for the voice
			},
		},
	},
	[3] = {
		name = "lostmc",
		spawnProp = true,
		coords = vector3(2527.32, 4114.86, 43.15),
		heading = 246.00,
		length = 1.4,
		width = 1.6,
		data = {
			minZ = 18.00,
			maxZ = 50.00,
			data = {
				range = 150.0, -- range for the voice
			},
		},
	},
}
```
