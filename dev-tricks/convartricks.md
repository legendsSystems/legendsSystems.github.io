---
description: Use these values in other scripts configurations.
---

# conVar Hacks

{% code title="server.cfg (Dev Server)" %}
```lua
setr CopsRequired 0
setr CopsNeeded 0
```
{% endcode %}

{% code title="server.cfg (Live Server)" %}
```lua
setr CopsRequired 1
setr CopsNeeded 2
```
{% endcode %}

{% code title="config.lua" %}
```lua
Config.CopsRequired = GetConvarInt("CopsRequired", 1) == 1
Config.CopsNeeded = GetConvarInt("CopsNeeded", 2) == 2
```
{% endcode %}

{% hint style="info" %}
This way you can test illegal activities on Dev without having to go back and make changes before you go live
{% endhint %}

{% hint style="warning" %}
Why not use a Boolean value for CopsRequired?  Since LUA processes them both as false, isn't it the same?  Technically yes, but LUA processes Int values much faster than Boolean values.
{% endhint %}
