# LayerUI

A compact Roblox UI library by **rayware**.

Dark translucent surfaces, subtle reflections, smooth transitions, and a small floating menu. One Lua file. A straightforward API. Your script supplies the behavior.

## Features

- The original Auto Clean glass styling and six built-in themes.
- A loading screen contained inside the window.
- Draggable windows and a draggable compact mode with bounce animations.
- Buttons, toggles, sliders, dropdowns, text inputs, labels, paragraphs, and sections.
- Custom tabs and an optional, editable About tab.
- Animated notifications and script cleanup callbacks.
- Scrolling content for longer menus, with window sizing that follows the available screen space.
- A built-in playground for trying the components :3

## Getting started

```lua
local LayerUI = loadstring(game:HttpGet("https://raw.githubusercontent.com/rayware/LayerUI/refs/heads/main/ui"))()

local Window = LayerUI:CreateWindow({
    Id = "MyScript",
    Title = "My Script",
    Author = "your name",
    Demo = false,
    Theme = "Default",
    AboutText = "A short description of my script.",
})

Window:AddButton({
    Name = "Say hello",
    Callback = function()
        Window:Notify({
            Title = "Hello",
            Content = "Your first LayerUI button works.",
            Duration = 2,
        })
    end,
})
```

Use `:` when calling the library, window, tab, or control methods.

The loading example requires an environment that provides `loadstring` and `game:HttpGet`. In Roblox Studio, put the library in a **ModuleScript** and load it from a **LocalScript** using `require` instead.

## Demo mode

Running the Lua file directly opens a demo with test controls. `CreateWindow` also includes those controls by default.

Set **`Demo = false`** in your own window to start with an empty Main tab. You do not need to delete the demo code from the library.

Creating your window immediately after loading the library suppresses the automatically scheduled demo. If you want to load the library first and create a window later, disable automatic startup **before** loading:

```lua
local env = type(getgenv) == "function" and getgenv() or _G

env.LayerUIConfig = {Demo = false}

local LayerUI = loadstring(game:HttpGet("loadstring(game:HttpGet("https://raw.githubusercontent.com/rayware/LayerUI/refs/heads/main/ui"))()

-- Create your window whenever you need it.
local Window = LayerUI:CreateWindow({
    Title = "My Script",
    Demo = false,
})
```

## Window options

| Option | Default | Purpose |
| --- | --- | --- |
| `Id` | `"LayerUI_Default"` | Window identity. Creating another window with the same ID destroys the previous one. |
| `Title` | `"LayerUI"` | Title in the full window, loading screen, and compact menu. |
| `Author` | `"rayware"` | Name displayed after `by` in your window and loader. |
| `Demo` | `true` | Include the test controls. |
| `Theme` | `"Default"` | Initial built-in theme. |
| `About` | Enabled | Set to `false` to remove About. |
| `AboutText` | Built-in description | Text displayed in About. |
| `AboutTitle` | `"About"` | Display name for the About tab. |
| `MaxHeight` | `460` | Maximum expanded window height in pixels. Longer content scrolls. |

Width is managed by the library. Main and About remain compact when their content fits; larger tabs expand within the height limit.

## Controls

Add controls directly to `Window` to place them in Main. `Window.Main` and custom tabs expose the same methods.

### Button

```lua
Window:AddButton({
    Name = "Run action",
    Description = "Optional supporting text",
    Callback = function()
        print("Clicked")
    end,
})
```

The short form is also supported:

```lua
Window:AddButton("Run action", function()
    print("Clicked")
end)
```

### Toggle

```lua
local Toggle = Window:AddToggle({
    Name = "Enabled",
    Default = false,
    Callback = function(enabled)
        print("Enabled:", enabled)
    end,
})

Toggle:Set(true)         -- Update and call the callback if the value changes.
Toggle:Set(false, true)  -- Update silently.
print(Toggle:Get())
```

### Slider

```lua
local Slider = Window:AddSlider({
    Name = "Speed",
    Min = 0,
    Max = 100,
    Default = 50,
    Step = 5,
    Suffix = " %",
    Callback = function(value)
        print("Value:", value)
    end,
})

Slider:Set(75)
```

`Window:Slider(...)` is an alias for `Window:AddSlider(...)`. `Max` must be greater than `Min`, and `Step` must be positive. Steps are measured from `Min`; values are clamped to the range.

### Dropdown

```lua
local Dropdown = Window:AddDropdown({
    Name = "Mode",
    Options = {"Slow", "Basic", "Instant"},
    Default = "Basic",
    Callback = function(value)
        print("Selected:", value)
    end,
})

Dropdown:Set("Instant", true)
```

Options must contain at least one value. `Set` accepts only a listed option.

### Text input

```lua
local Input = Window:AddTextbox({
    Name = "Message",
    Placeholder = "Type something…",
    Default = "",
    Callback = function(text, enterPressed)
        print(text, enterPressed)
    end,
})
```

The callback runs when the input loses focus. `AddTextBox` is an alias for `AddTextbox`.

### Text and sections

```lua
Window:AddSection("General")

local Label = Window:AddLabel("Ready when you are")
Label:Set("Status changed")

local Paragraph = Window:AddParagraph({
    Title = "Information",
    Text = "A longer explanation for your users.",
    Height = 88,
})
Paragraph:Set("Updated explanation")
```

### Control handles

| Method | Purpose |
| --- | --- |
| `control:Get()` | Read the stored value; useful for toggles, sliders, dropdowns, and inputs. |
| `control:Set(value, silent)` | Update a value. For interactive value controls, `silent = true` suppresses the callback. |
| `control:SetVisible(false)` | Hide the control. Pass `true` to show it again. |
| `control:SetDisabled(true)` | Disable interaction callbacks. Pass `false` to re-enable them. |
| `control:Destroy()` | Remove the control. |
| `button:SetName("New name")` | Change a button's caption. |

Toggle, slider, and dropdown defaults do **not** trigger callbacks unless you set `FireOnInit = true`. Callbacks run in separate tasks; callback errors are reported with a LayerUI prefix.

## Tabs

```lua
local Visuals = Window:AddTab("Visuals")

Visuals:AddToggle({
    Name = "Show markers",
    Default = false,
    Callback = function(enabled)
        print(enabled)
    end,
})

-- Call from a button or after the loading animation finishes.
Window:SelectTab("Visuals")
```

`CreateTab` is an alias for `AddTab`. Calling `AddTab` with an existing name returns that tab. With more tabs, the navigation strip scrolls horizontally.

Remove a custom tab with `Window:RemoveTab("Visuals")` or `Visuals:Destroy()`. Finish any active transition before removing a tab. Main is the default tab and cannot be removed through this API.

### About

```lua
-- Hide About at creation:
local Window = LayerUI:CreateWindow({Demo = false, About = false})

-- Or change it later:
Window:SetAbout("My script description.", "Info")

-- Remove it later:
Window:RemoveAbout()
```

`SetAbout` recreates About if you previously removed it.

### System Themes

Available themes: **Default**, **Red**, **Blue**, **Yellow**, **Green**, and **White**.

```lua
Window:SetTheme("Blue")
print(Window:GetTheme())
```

Default uses black glass; White is a light theme. Themes is a permanent system tab. Its small **rayware** credit stays below the theme tiles, independently of your script's `Author` setting. The API does not provide an option to hide or remove either.

This is API-level protection, not tamper protection: a person who edits the Lua source can change it.

## Notifications

```lua
Window:Notify({
    Title = "Item collected",
    Content = "Golden Halo",
    Duration = 2,
})
```

Notifications slide into the bottom-right corner and slide out after their display duration. They appear one at a time; the pending queue is capped at 12. Duration is clamped between 0.5 and 10 seconds.

## Window methods and cleanup

```lua
Window:SetTitle("Updated title")
Window:SetAuthor("your name")

Window:Minimize(true)
Window:Minimize(false)

Window:OnDestroy(function()
    -- Stop your own loops and disconnect your own connections here.
    print("Window closed")
end)

Window:Close()    -- Animated close.
-- Window:Destroy() -- Immediate removal.
```

Tab selection and minimization are ignored during loading or an active transition. Closing the window cleans up the library's connections and animations. Your script should stop its own work in `OnDestroy`.

## Notes

- LayerUI provides interface elements; toggles and sliders do not implement gameplay features on their own.
- Keep callbacks short and handle your script's state explicitly.
- API behavior has been checked with mocked Roblox objects. Rendering, touch behavior, and executor compatibility still need testing in Roblox.

**LayerUI — UI library by rayware.**
