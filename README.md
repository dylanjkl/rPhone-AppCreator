# AppCreator Module

## Overview
The AppCreator module is a comprehensive framework for creating third-party applications within the rPhone system. It provides a clean API for app registration, UI creation, state management, and lifecycle handling[2].

## Features
- **Dynamic UI Creation**: Create UI elements programmatically without pre-defined templates[2].
- **State Management**: Each app maintains its own state that persists during its lifecycle[2].
- **Transition Animations**: Smooth animations when opening/closing apps[2].
- **Resource Cleanup**: Automatic cleanup of UI and connections when apps close[2].
- **Utility Functions**: Helper functions for common UI patterns and interactions[2].

## Module Structure

### Location

Modules/
```
├── Core/
│ ├── AppCreator.lua <- Main framework
│ ├── MessageManager.lua
│ ├── CallManager.lua
│ └── ... other core modules
└── Apps/
├── CalculatorApp.lua <- Example app
└── ... other apps
```

[2]

## Creating an App

### Basic App Structure

```lua
local MyApp = {}

function MyApp.create(AppCreator, SharedState)
    local appConfig = {
        name = "My App",
        icon = "rbxasset://textures/ui/GuiImagePlaceholder.png",
        iconColor = Color3.fromRGB(100, 150, 200),
        description = "Description of my app",
    
        onOpen = function(context)
            -- Called when app opens
            -- Build your UI here
            print(context.ui.container) -- Access the main app UI container
        end,
        
        onClose = function(context)
            -- Called when app closes
            -- Cleanup if needed
        end,
        
        onUpdate = function(context)
            -- Optional: Called every frame
            -- Use for animations or real-time updates
            -- print(context.deltaTime)
        end
    }

    return AppCreator:RegisterApp(appConfig)
end
```

[2]

### Context Object
The `context` object passed to app functions (`onOpen`, `onClose`, `onUpdate`) contains:
- `ui`: A table containing UI references (e.g., `container`, `header`, `content`)[1][2].
- `state`: A table for app-specific state storage. This persists while the app is open[1][2].
- `shared`: The `SharedState` object from the phone system[1][2].
- `utils`: A table of utility functions for UI creation and other common tasks[1][2].
- `deltaTime`: (Only in `onUpdate`) The time elapsed since the last frame[1][2].

## Utility Functions
Accessible via `context.utils`[1][2].

### UI Creation

**createElement**
Creates a new UI instance with specified properties and parent.

```lua
local element = utils.createElement(className, properties, parent)
-- Example:
local frame = utils.createElement("Frame", {
Size = UDim2.new(1, 0, 1, 0),
BackgroundColor3 = Color3.fromRGB(50, 50, 50)
}, context.ui.content)
```

[1][2]

**createButton**
Creates a pre-styled TextButton.

```lua
local button = utils.createButton(text, callback, parent)
-- Example:
local submitBtn = utils.createButton("Submit", function()
print("Button clicked!")
end, context.ui.content)
```

[1][2]

**createTextBox**
Creates a pre-styled TextBox.

```lua
local textBox = utils.createTextBox(placeholder, parent)
-- Example:
local input = utils.createTextBox("Enter text...", context.ui.content)
```

[1][2]

**createLabel**
Creates a pre-styled TextLabel.

```lua
local label = utils.createLabel(text, parent)
-- Example:
local title = utils.createLabel("Welcome!", context.ui.content)
```

[1][2]

**createGrid**
Creates a Frame with a UIGridLayout.

```lua
local grid = utils.createGrid(parent, columns)
-- Example:
local buttonGrid = utils.createGrid(context.ui.content, 4) -- Creates a grid with 4 columns
```

[1][2]

### Other Utilities

**showNotification**
Displays a temporary notification on the phone screen.

```lua
utils.showNotification(message, duration)
-- Example:
utils.showNotification("Settings saved!", 3) -- Shows for 3 seconds

text
```

[1][2]

**animate**
A wrapper for TweenService to animate instance properties.

```lua
local tween = utils.animate(instance, properties, duration)
-- Example:
utils.animate(button, {
BackgroundColor3 = Color3.fromRGB(255, 0, 0)
}, 0.5) -- Animates over 0.5 seconds
```

[1][2]

**connect**
Registers a connection (e.g., an event) to be automatically disconnected when the app closes. This helps prevent memory leaks.

```lua
local myConnection = someEvent:Connect(function() end)
utils.connect(myConnection)
-- or directly:
utils.connect(someEvent:Connect(function() end))
```

[1][2]

**playSound**
Plays a sound from the phone's shared sound library.

```lua
utils.playSound("rPhoneSelect")
```

[1][2]

## Complete Example: Todo List App

```lua
local TodoApp = {}

-- Todo app configuration
local TODO_CONFIG = {
	name = "Todo List",
	namevisible = false,
	icon = "http://www.roblox.com/asset/?id=71935016681164",
	iconColor = Color3.fromRGB(52, 152, 219),
	description = "A simple todo list app",
	id = "todo_app"
}

-- Create todo app
function TodoApp.create(AppCreator, SharedState)
	-- onOpen function - called when app opens
	local function onOpen(context)
		local ui = context.ui
		local state = context.state
		local utils = context.utils

		-- Initialize todo state
		state.todos = state.todos or {}

		-- Create header frame
		local headerFrame = utils.createElement("Frame", {
			Name = "Header",
			Size = UDim2.new(1, -20, 0, 60),
			Position = UDim2.new(0, 10, 0, 10),
			BackgroundColor3 = Color3.fromRGB(60, 60, 60),
			BorderSizePixel = 0
		}, ui.content)

		local headerCorner = utils.createElement("UICorner", {
			CornerRadius = UDim.new(0, 8)
		}, headerFrame)

		-- Input area in header
		local input = utils.createElement("TextBox", {
			Name = "TodoInput",
			Size = UDim2.new(1, -70, 1, -20),
			Position = UDim2.new(0, 10, 0, 10),
			BackgroundColor3 = Color3.fromRGB(80, 80, 80),
			BorderSizePixel = 0,
			Text = "",
			PlaceholderText = "Add todo...",
			TextColor3 = Color3.fromRGB(255, 255, 255),
			PlaceholderColor3 = Color3.fromRGB(150, 150, 150),
			TextSize = 16,
			Font = Enum.Font.SourceSans,
			TextXAlignment = Enum.TextXAlignment.Left,
			ClearTextOnFocus = false
		}, headerFrame)

		local inputCorner = utils.createElement("UICorner", {
			CornerRadius = UDim.new(0, 6)
		}, input)

		-- Add button
		local addBtn = utils.createElement("TextButton", {
			Name = "AddButton",
			Size = UDim2.new(0, 50, 1, -20),
			Position = UDim2.new(1, -60, 0, 10),
			BackgroundColor3 = Color3.fromRGB(52, 152, 219),
			BorderSizePixel = 0,
			Text = "+",
			TextColor3 = Color3.fromRGB(255, 255, 255),
			TextSize = 24,
			Font = Enum.Font.SourceSansBold
		}, headerFrame)

		local addBtnCorner = utils.createElement("UICorner", {
			CornerRadius = UDim.new(0, 6)
		}, addBtn)

		-- Todo list scrolling frame
		local todoList = utils.createElement("ScrollingFrame", {
			Name = "TodoList",
			Size = UDim2.new(1, -20, 1, -90),
			Position = UDim2.new(0, 10, 0, 80),
			BackgroundColor3 = Color3.fromRGB(40, 40, 40),
			BorderSizePixel = 0,
			ScrollBarThickness = 6,
			ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100),
			CanvasSize = UDim2.new(0, 0, 0, 0),
			AutomaticCanvasSize = Enum.AutomaticSize.Y
		}, ui.content)

		local listCorner = utils.createElement("UICorner", {
			CornerRadius = UDim.new(0, 8)
		}, todoList)

		local listLayout = utils.createElement("UIListLayout", {
			Padding = UDim.new(0, 5),
			SortOrder = Enum.SortOrder.LayoutOrder
		}, todoList)

		-- Store references
		state.todoList = todoList
		state.input = input

		-- Helper function to create todo item
		local function createTodoItem(todo)
			local todoFrame = utils.createElement("Frame", {
				Name = "TodoItem_" .. todo.id,
				Size = UDim2.new(1, -10, 0, 50),
				BackgroundColor3 = Color3.fromRGB(60, 60, 60),
				BorderSizePixel = 0,
				LayoutOrder = todo.id
			}, todoList)

			local todoCorner = utils.createElement("UICorner", {
				CornerRadius = UDim.new(0, 6)
			}, todoFrame)

			-- Checkbox
			local checkbox = utils.createElement("TextButton", {
				Name = "Checkbox",
				Size = UDim2.new(0, 40, 1, -10),
				Position = UDim2.new(0, 5, 0, 5),
				BackgroundTransparency = 1,
				Text = todo.completed and "☑" or "☐",
				TextColor3 = todo.completed and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 255, 255),
				TextSize = 24,
				Font = Enum.Font.SourceSansBold
			}, todoFrame)

			-- Todo text label
			local todoLabel = utils.createElement("TextLabel", {
				Name = "TodoText",
				Size = UDim2.new(1, -100, 1, -10),
				Position = UDim2.new(0, 50, 0, 5),
				BackgroundTransparency = 1,
				Text = todo.text,
				TextColor3 = todo.completed and Color3.fromRGB(150, 150, 150) or Color3.fromRGB(255, 255, 255),
				TextSize = 16,
				Font = Enum.Font.SourceSans,
				TextXAlignment = Enum.TextXAlignment.Left,
				TextYAlignment = Enum.TextYAlignment.Center,
				TextStrokeTransparency = todo.completed and 0.5 or 1,
				TextStrokeColor3 = Color3.fromRGB(150, 150, 150)
			}, todoFrame)

			-- Delete button
			local deleteBtn = utils.createElement("TextButton", {
				Name = "DeleteButton",
				Size = UDim2.new(0, 40, 1, -10),
				Position = UDim2.new(1, -45, 0, 5),
				BackgroundColor3 = Color3.fromRGB(255, 85, 85),
				BorderSizePixel = 0,
				Text = "×",
				TextColor3 = Color3.fromRGB(255, 255, 255),
				TextSize = 20,
				Font = Enum.Font.SourceSansBold
			}, todoFrame)

			local deleteBtnCorner = utils.createElement("UICorner", {
				CornerRadius = UDim.new(0, 6)
			}, deleteBtn)

			-- Store todo reference in the frame for easy access
			todoFrame:SetAttribute("TodoId", todo.id)

			-- Checkbox functionality - Fixed to prevent nil reference
			utils.connect(checkbox.Activated:Connect(function()
				utils.playSound("rPhoneSelect")
				
				todo.completed = not todo.completed
				
				-- Update checkbox appearance
				checkbox.Text = todo.completed and "☑" or "☐"
				checkbox.TextColor3 = todo.completed and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 255, 255)
				
				-- Update text appearance
				todoLabel.TextColor3 = todo.completed and Color3.fromRGB(150, 150, 150) or Color3.fromRGB(255, 255, 255)
				todoLabel.TextStrokeTransparency = todo.completed and 0.5 or 1
			end))

			-- Delete functionality
			utils.connect(deleteBtn.Activated:Connect(function()
				utils.playSound("rPhoneSelect")
				
				-- Remove from state
				for i, t in ipairs(state.todos) do
					if t.id == todo.id then
						table.remove(state.todos, i)
						break
					end
				end
				
				-- Remove UI element
				todoFrame:Destroy()
				utils.showNotification("Todo deleted!", 1)
			end))

			-- Button hover effects
			checkbox.MouseEnter:Connect(function()
				utils.animate(checkbox, {
					TextColor3 = checkbox.TextColor3:Lerp(Color3.new(1, 1, 1), 0.3)
				}, 0.1)
			end)

			checkbox.MouseLeave:Connect(function()
				checkbox.TextColor3 = todo.completed and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 255, 255)
			end)

			deleteBtn.MouseEnter:Connect(function()
				utils.animate(deleteBtn, {
					BackgroundColor3 = Color3.fromRGB(255, 120, 120)
				}, 0.1)
			end)

			deleteBtn.MouseLeave:Connect(function()
				utils.animate(deleteBtn, {
					BackgroundColor3 = Color3.fromRGB(255, 85, 85)
				}, 0.1)
			end)
		end

		-- Helper function to add todo
		local function addTodo(text)
			if text and text ~= "" and string.len(string.gsub(text, "%s", "")) > 0 then
				utils.playSound("rPhoneSelect")
				
				local todo = {
					text = text,
					completed = false,
					id = tick() * 1000 -- Use more precise timestamp
				}
				
				table.insert(state.todos, todo)
				createTodoItem(todo)
				
				input.Text = ""
				utils.showNotification("Todo added!", 1)
			end
		end

		-- Add button functionality
		utils.connect(addBtn.Activated:Connect(function()
			addTodo(input.Text)
		end))

		-- Enter key support
		utils.connect(input.FocusLost:Connect(function(enterPressed)
			if enterPressed then
				addTodo(input.Text)
			end
		end))

		-- Add button hover effect
		addBtn.MouseEnter:Connect(function()
			utils.animate(addBtn, {
				BackgroundColor3 = Color3.fromRGB(72, 172, 239)
			}, 0.1)
		end)

		addBtn.MouseLeave:Connect(function()
			utils.animate(addBtn, {
				BackgroundColor3 = Color3.fromRGB(52, 152, 219)
			}, 0.1)
		end)

		-- Load existing todos
		for _, todo in ipairs(state.todos) do
			createTodoItem(todo)
		end

		-- Show welcome notification
		if #state.todos == 0 then
			utils.showNotification("Todo List opened! Add your first todo.", 2)
		end
	end

	-- onClose function - called when app closes
	local function onClose(context)
		-- Clean up if needed
		-- Save state is automatically handled by the framework
	end

	-- Register the todo app
	TODO_CONFIG.onOpen = onOpen
	TODO_CONFIG.onClose = onClose

	return AppCreator:RegisterApp(TODO_CONFIG)
end

return TodoApp
```

[2]

## Best Practices

### 1. State Management
- Use `context.state` to store app-specific data that needs to persist while the app is open[2].
- Initialize state with default values in `onOpen` if it's the first time the app is opened in a session (e.g., `state.myList = state.myList or {}`)[2].
- State is automatically managed by AppCreator and cleared when the app is fully closed and its instance destroyed[1].

### 2. Resource Management
- Crucially, use `utils.connect(event:Connect(function() ... end))` for all event connections (like button clicks, property changes, etc.)[1][2]. This ensures they are automatically disconnected when the app closes, preventing memory leaks[2].
- Any UI elements created should be parented to `context.ui.content` or its descendants. They will be destroyed automatically when the app closes[1].
- If you create resources not managed by `utils.connect` or parented to the app's UI, ensure they are cleaned up in the `onClose` function[2].

### 3. UI Design
- Aim to follow the rPhone's existing design language (e.g., dark themes, rounded corners, similar control styles) for a cohesive user experience[2].
- Use consistent spacing and sizing for UI elements[2].
- Provide clear visual feedback for user interactions (e.g., button hover/press states, loading indicators)[2]. The `utils.createButton` already has built-in hover effects[1].

### 4. Performance
- For long lists of items, use `ScrollingFrame` as demonstrated in the Todo List example[2].
- Avoid creating an excessive number of UI elements simultaneously, especially if they are complex. Consider pagination or virtual scrolling for very large datasets[2].
- Only use the `onUpdate(context)` function if per-frame updates are strictly necessary (e.g., for custom animations or real-time data polling). Overuse can impact performance[2].

### 5. Error Handling
Wrap critical sections of your app's logic, especially in `onOpen`, with `pcall` to catch errors gracefully and prevent the entire app from crashing. You can use `utils.showNotification` to inform the user.

```lua
onOpen = function(context)
    local success, err = pcall(function()
    -- Your app's initialization code
    -- Example: local data = utils.jsonDecode(someRiskyOperation())
    end)
    
    if not success then
        print("Error in My App:", err)
        context.utils.showNotification("Error: " .. tostring(err), 5)
        -- Optionally, you might want to close the app or show an error screen
    end
end
```

[2]

## Integration

### Adding to rPhone System
1. Create your app module (e.g., `MyApp.lua`) following the structure shown in "Basic App Structure"[2].
2. Place your app module file typically in a designated apps folder (e.g., `Modules/Apps/` as suggested in "Module Structure")[2].
3. The main rPhone client script should be configured to require modules from this folder and call their `create(AppCreator, SharedState)` function after `AppCreator` and `SharedState` are initialized[2]. The `AppCreator:RegisterApp` call within your app's `create` function will handle adding its icon to the home screen[1].

### Manual Registration
If you are integrating outside of an automated loading system, you would typically do the following:

```lua
-- In your main client script or phone initialization script

-- Assuming 'AppCreator' module is located at 'game.ReplicatedStorage.Modules.Core.AppCreator'
local AppCreator = require(game.ReplicatedStorage.Modules.Core.AppCreator)
-- Assuming 'MyApp' module is at 'game.ReplicatedStorage.Modules.Apps.MyApp'
local MyApp = require(game.ReplicatedStorage.Modules.Apps.MyApp)
-- Assuming 'SharedState' is already initialized and available
-- local SharedState = ...

-- Initialize AppCreator with the shared state
AppCreator:Init(SharedState)

-- Create/register your app
local myAppId = MyApp.create(AppCreator, SharedState)
if myAppId then
    print("My App registered with ID:", myAppId)
else
    print("Failed to register My App")
end
```

[2]

## Debugging
- Utilize `print()` statements within your app's `onOpen`, `onClose`, `onUpdate`, and callback functions to trace execution flow and inspect variable values. These will output to the Roblox Developer Console[2].
- Check for errors reported in the `onOpen`/`onClose` functions, especially if using `pcall` as recommended for error handling[2].
- Ensure all UI elements are correctly parented. If an element doesn't appear, verify its `Parent` property is set to a visible Frame within the app's UI structure (e.g., `context.ui.content`)[2].
- If the rPhone system has a debug mode (e.g., in a `CallManager` or main controller), enabling it might provide more logs about app state transitions or errors[2].

## Limitations
- Apps operate within a sandboxed UI container (`Shared.AppContainer`) and cannot directly modify core phone UI or other apps' UIs beyond their designated area[1][2].
- Direct access to other apps' internal states is not provided[2]. Communication would need to be mediated by the core phone system if such a feature exists.
- Functionality is limited to the utilities provided by `AppCreator` and the shared phone services. Apps cannot arbitrarily access all game services unless explicitly passed or allowed by the `SharedState` or utility functions[2].
- Persistent storage across game sessions (e.g., saving app data after the player leaves and rejoins) is not inherently part of this `AppCreator` module and would need to be implemented separately (e.g., using Roblox DataStores) and potentially exposed via `SharedState` or app lifecycle events[2].

## Future Enhancements
(Potential ideas for expanding the AppCreator or rPhone system)
- **App-to-App Communication:** A standardized system for apps to send messages or data to each other[2].
- **Persistent Storage API:** A simplified API for apps to save and load data that persists across sessions[2].
- **Custom App Permissions:** A system to define and request specific permissions (e.g., access to contacts, location if applicable)[2].
- **App Marketplace/Management:** UI for users to "install" or "uninstall" apps if apps are not all loaded by default[2].
- **Extended UI Components:** More pre-styled utility functions for creating common UI patterns like dialogs, dropdowns, sliders, etc[2].
- **Background Tasks/Services:** A way for apps to perform actions even when not actively open (with limitations).

