# Garry's Mod Lua VGUI

Use when synthesizing or changing VGUI interfaces. Design panels around their function, identify their functional subpanels, and register those panels in separate client files. Use with the [addon guide](../SKILL.md), [structure subskill](structure.md), and [formatting subskill](format.md).

## Identify the panel's purpose and subfunctions

- Start with what the user needs to see or do. Identify the panel's inputs, displayed state, available actions, and how it communicates results.
- Identify distinct subfunctions before building the panel: for example, searching a list, browsing results, inspecting a selection, or editing its settings.
- Give each functional subpanel responsibility for its own controls, layout, and local interaction state. Keep the main panel responsible for composing subpanels and coordinating their interactions.
- Separate functional subpanels even when they currently have only one caller. Do not bury all interface behavior in a single large main-panel initializer.
- Standard labels, buttons, and layout containers can remain ordinary child controls. A functional subpanel represents a coherent part of the interface, rather than every individual control or Lua helper method.

## Register each main panel and functional subpanel

- Define each custom main panel and functional subpanel using a local panel table and register it with `vgui.Register`. Use the API's exact capitalization. Choose an appropriate existing base such as `DFrame`, `DPanel`, or `DScrollPanel`.
- Use distinct addon-prefixed class names, such as `MyAddonInventory`, `MyAddonItemList`, and `MyAddonItemDetails`.
- Put each registered panel in its own descriptively named Lua file under `myaddon/client/derma/`, relative to the addon's `lua/` directory. For example, `lua/myaddon/client/derma/panel.lua`.
- Create subpanels through their registered class names and parent them to their owning panel. Do not duplicate their implementations or redefine them inside the main panel's file.
- Keep panel registration separate from opening the interface. Including a panel definition should register its class; the menu-opening path should create instances when needed.

Example organization for an inventory interface:

```text
lua/myaddon/client/
  menu.lua
  derma/
    inventory.lua
    item_list.lua
    item_details.lua
```

Here, `inventory.lua` registers the main panel, `item_list.lua` registers the browsing subpanel, and `item_details.lua` registers the selected-item subpanel. `menu.lua` handles opening the registered main panel. Add only the subfunctions the requested interface needs.

The [panel customization documentation](https://wiki.facepunch.com/gmod/Panel_Customization) describes registration and creation through `vgui.Create` or the parent's `Add` method.

## Keep loading and panel communication explicit

- Send all required panel files to clients and include them on the client through the existing loader. The `client/derma/` directory does not load or distribute files automatically.
- Register custom base panels before derived panels. Register functional subpanels before creating a main-panel instance that uses them; keep the include order explicit.
- Expose small methods for supplying data and callbacks for reporting user actions. Let the main panel connect the list's selection event to the details panel, for example, instead of having siblings reach into each other's internal controls.
- Keep mutable per-instance state on the panel instance. Define how selection, filters, and unsaved edits behave when data changes or the interface is reopened.
- Keep server requests and authoritative decisions behind the addon's existing API. A disabled or hidden button is not a permission check; follow the [security subskill](security.md).
- Create child controls during initialization, update them when data changes, and keep painting focused on drawing. Avoid rebuilding panels or scanning large datasets every paint/frame; use the [performance subskill](performance.md).
- Clean up panel-owned hooks, timers, and pending work when the panel is removed. Validate the intended panel before applying asynchronous results, following the [runtime subskill](runtime.md).

## Manual verification

Use the [manual testing subskill](testing.md). Open the interface in game and exercise each main-panel action and subpanel interaction. Check empty and populated data, selection changes, resizing, supported screen sizes, closing and reopening, and delayed responses after removal. Confirm panel classes load without errors and reopening does not duplicate controls, hooks, or timers. Do not create automated tests or mock VGUI environments.
