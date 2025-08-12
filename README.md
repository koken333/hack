--// Macro Recorder for KRNL
-- Controls:
--   F5 = Start/Stop Recording
--   F6 = Play Macro
--   F7 = Clear Macro

local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local macro = {}
local recording = false
local lastTime = 0

local function recordEvent(inputType, key, pos)
    table.insert(macro, {
        t = tick() - lastTime,
        type = inputType,
        key = key,
        pos = pos
    })
    lastTime = tick()
end

local function playMacro()
    print("Playing macro...")
    for _, action in ipairs(macro) do
        wait(action.t)
        if action.type == "Key" then
            VirtualInputManager:SendKeyEvent(true, action.key, false, game)
            VirtualInputManager:SendKeyEvent(false, action.key, false, game)
        elseif action.type == "Mouse" then
            VirtualInputManager:SendMouseButtonEvent(action.pos.X, action.pos.Y, 0, true, game, 1)
            VirtualInputManager:SendMouseButtonEvent(action.pos.X, action.pos.Y, 0, false, game, 1)
        end
    end
    print("Macro finished!")
end

-- Listen for keyboard
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end

    -- Hotkeys
    if input.KeyCode == Enum.KeyCode.F5 then
        recording = not recording
        macro = recording and macro or macro
        lastTime = tick()
        print(recording and "Recording started" or "Recording stopped")

    elseif input.KeyCode == Enum.KeyCode.F6 then
        if #macro > 0 then
            playMacro()
        else
            print("No macro recorded!")
        end

    elseif input.KeyCode == Enum.KeyCode.F7 then
        macro = {}
        print("Macro cleared!")
    end

    -- Record keys
    if recording and input.UserInputType == Enum.UserInputType.Keyboard then
        recordEvent("Key", input.KeyCode, nil)
    end
end)

-- Listen for mouse
UserInputService.InputBegan:Connect(function(input)
    if recording and input.UserInputType == Enum.UserInputType.MouseButton1 then
        local mousePos = UserInputService:GetMouseLocation()
        recordEvent("Mouse", nil, mousePos)
    end
end)

print("Macro Recorder Loaded!")
print("F5 = start/stop record | F6 = play macro | F7 = clear macro")
