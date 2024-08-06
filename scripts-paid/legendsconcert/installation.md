---
description: >-
  Welcome to the legendsConcert installation guide. Here, you will learn how to
  fully install our asset to ensure a smooth and trouble-free setup for your
  FiveM server. By carefully following each step
---

# Installation

{% hint style="info" %}
If you encounter any issues during the installation, please do not hesitate to reach out for assistance. Open a ticket in our Discord server, and our dedicated support team will be ready to help you resolve any problems. We're committed to ensuring that your setup process is as smooth and trouble-free as possible, so feel free to contact us with any questions or concerns you may have.
{% endhint %}

## Download the resource

After purchasing the script from our store head over to [**Keymaster**](https://keymaster.fivem.net/asset-grants). Here, you will find the assets you have acquired. Download the script **legendsConcert** to your environment

{% hint style="danger" %}
The script will not work if the asset is not purchased and present on your Keymaster account. Additionally, please be aware that if you transfer these assets, you will not be able to receive them back, and the script will cease to function.
{% endhint %}

## Download the dependencies

To make sure the asset works as it should, there are a few scripts you must download. These extra scripts are key for the asset to run well and fit into your FiveM server. Be sure to get all the needed dependencies listed in the documentation to ensure a smooth and fully working setup.

| Dependency | Link                                                                                     |
| ---------- | ---------------------------------------------------------------------------------------- |
| pma-voice  | [https://github.com/AvarianKnight/pma-voice](https://github.com/AvarianKnight/pma-voice) |
| PolyZone   | [https://github.com/mkafrin/PolyZone](https://github.com/mkafrin/PolyZone)               |

## Add items to inventory script

{% tabs %}
{% tab title="qb-inventory" %}
{% code title="items.lua" %}
```lua

microphone = {
		name = "microphone",
		label = "Microphone",
		weight = 100,
		type = "item",
		image = "microphone.png",
		unique = true,
		useable = true,
		shouldClose = true,
		description = "A microphone used for concerts and other events.",
	},
```
{% endcode %}
{% endtab %}

{% tab title="qs-inventory" %}
```lua
microphone = {
		name = "microphone",
		label = "Microphone",
		weight = 100,
		type = "item",
		image = "microphone.png",
		unique = true,
		useable = true,
		shouldClose = true,
		description = "A microphone used for concerts and other events.",
	},
```
{% endtab %}

{% tab title="ox_inventory" %}
```lua
microphone = {
		name = "microphone",
		label = "Microphone",
		weight = 100,
		type = "item",
		image = "microphone.png",
		unique = true,
		useable = true,
		shouldClose = true,
		description = "A microphone used for concerts and other events.",
	},		
```
{% endtab %}
{% endtabs %}

## Copy image to inventory script

{% tabs %}
{% tab title="qb-inventory" %}
Copy the microphone.png to your qb-inventory/html/images directory
{% endtab %}

{% tab title="qs-inventory" %}
Copy the microphone.png to your qs-inventory/html/images directory
{% endtab %}

{% tab title="ox_inventory" %}
Copy the microphone.png to your ox\_inventory/web/images directory
{% endtab %}
{% endtabs %}

## Ensure the resource

To get legendsConcert running smoothly on your FiveM server, it's important to start the scripts in the right order. This ensures everything loads correctly, avoiding problems and making sure the system works well.

```properties
# Start your core
ensure qb-core

# Before you start legendsConcert make sure to start PolyZone and pma-voice
ensure PolyZone
ensure pma-voice

# Then start the resource
ensure legendsConcert
```

## Start the server

You can now start your server and enjoy the script. Additionally, you can configure the script further to match your preferences. For more information, refer to the configuration section.
