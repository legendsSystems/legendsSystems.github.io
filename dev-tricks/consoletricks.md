---
description: >-
  Here you will find some helpful F8 commands.  This will add a new menu to the
  F8 list called "legendsSystems" with additional options
---

# consoleTricks

{% code title="F8 Console" %}
```properties
devgui_cmd "legendsSystems/DevTools" "nui_devtools"
devgui_cmd "legendsSystems/Commands/Keybinds List" "bind"
devgui_cmd "legendsSystems/Commands/Cmd List" "cmdlist"

devgui_cmd "legendsSystems/Commands/Permissions/Aces" "list_aces"
devgui_cmd "legendsSystems/Commands/Permissions/Principals" "list_principals"

devgui_cmd "legendsSystems/Tools/Resource Monitor/On" "resmon true"
devgui_cmd "legendsSystems/Tools/Resource Monitor/Off" "resmon false"

devgui_cmd "legendsSystems/Tools/Developer Mode/On" "developer true"
devgui_cmd "legendsSystems/Tools/Developer Mode/Off" "developer false"
devgui_cmd "legendsSystems/Tests/Mini Con" "con_miniconChannels script:*"

devgui_cmd "legendsSystems/Tests/Stats Bar/On" "cl_drawperf true"
devgui_cmd "legendsSystems/Tests/Stats Bar/Off" "cl_drawperf false"

```
{% endcode %}
