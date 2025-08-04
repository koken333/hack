-- LocalScript บน KRNL
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")

local plr = Players.LocalPlayer
local recordListener, releaseListener

-- Storage structure
local eventHistory = {}
local recording = false
local lastToggle = 0

-- UI Setup
local screenGui = Instance.new("ScreenGui"); screenGui.Name = "MacroRecorderUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = plr:WaitForChild("PlayerGui")

local btn = Instance.new("TextButton")
btn.Size = UDim2.fromScale(0.15,0.07)
btn.Position = UDim2.fromScale(0.42, 0.05)
btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
btn.TextColor3 = Color3.new(1,1,1)
btn.TextScaled = true
btn.Font = Enum.Font.GothamBold
btn.Text = "Record"
btn.Parent = screenGui

-- Helper: disconnect listener and clear table to prevent leak
local function cleanup()
    if recordListener then recordListener:Disconnect(); recordListener = nil end
    if releaseListener then releaseListener:Disconnect(); releaseListener = nil end
    recording = false
    eventHistory = {}
    collectgarbage("collect")
end

-- Debounce toggle function
local function toggleRecord()
    if tick() - lastToggle < 0.5 then return end
    lastToggle = tick()

    if not recording then
        -- เริ่ม Record
        eventHistory = {}
        recording = true
        btn.Text = "Stop"

        VirtualUser:StartRecording()
        -- ฟังกด + ปล่อย
        recordListener = UserInputService.InputBegan:Connect(function(input, gpe)
            if gpe then return end
            table.insert(eventHistory, {
                t = tick(),
                Type = input.UserInputType,
                KeyCode = input.KeyCode and input.KeyCode.Name or nil,
                IsDown = true,
                MouseX = input.Position and input.Position.X or nil,
                MouseY = input.Position and input.Position.Y or nil
            })
        end)
        releaseListener = UserInputService.InputEnded:Connect(function(input, gpe)
            if gpe then return end
            table.insert(eventHistory, {
                t = tick(),
                Type = input.UserInputType,
                KeyCode = input.KeyCode and input.KeyCode.Name or nil,
                IsDown = false,
                MouseX = input.Position and input.Position.X or nil,
                MouseY = input.Position and input.Position.Y or nil
            })
        end)

    else
        -- Stop record
        VirtualUser:StopRecording()
        btn.Text = "Play"
        recording = false
        -- disconnect listeners
        cleanup()
    end
end

btn.MouseButton1Click:Connect(toggleRecord)

-- Playback function with debris/frames for safety
local function playback(loopCount)
    loopCount = loopCount or 1
    local n = #eventHistory
    if n == 0 then return end

    for i = 1, loopCount do
        local start = tick()
        for _, evt in ipairs(eventHistory) do
            local dt = evt.t - start
            if dt > 0 then task.wait(dt) end

            if evt.Type == Enum.UserInputType.Keyboard then
                if evt.IsDown then
                    VirtualUser:SetKeyDown(evt.KeyCode)
                else
                    VirtualUser:SetKeyUp(evt.KeyCode)
                end
            elseif evt.Type == Enum.UserInputType.MouseButton1 or evt.Type == Enum.UserInputType.MouseButton2 then
                -- Mouse click
                VirtualUser:MoveMouse(evt.MouseX, evt.MouseY, workspace.CurrentCamera.CFrame)
                if evt.IsDown then
                    VirtualUser:ClickButton1(evt.MouseX, evt.MouseY, workspace.CurrentCamera.CFrame)
                end
            end

            -- ถ้า loop เกิน 10k events ให้พักเพื่อไม่ให้ช้า/ค้าง UI
            if _ % 1000 == 0 then
                RunService.Heartbeat:Wait()
            end
        end
        start = tick()
    end
end

-- กดแป้น "P" เพื่อเล่นซ้ำ
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.P then
        playback(1)
    end
end)

-- Save to JSON (อาจใช้กับ Synapse KRNL functions)
local jsonString = HttpService:JSONEncode(eventHistory)
print("Event JSON:", jsonString)
-- โหลดคืน: local tableRestore = HttpService:JSONDecode(jsonString)
