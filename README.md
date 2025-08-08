--// Anime Vanguards Macro Mobile - No Key
-- ฟีเจอร์: Record, Play, Save/Load, Auto Place, Auto Upgrade, Auto Skip Wave

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UIS = game:GetService("UserInputService")
local VIM = game:GetService("VirtualInputManager")
local HttpService = game:GetService("HttpService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- สร้าง GUI
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "MacroUI"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 250, 0, 330)
frame.Position = UDim2.new(0.35, 0, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)

local title = Instance.new("TextLabel", frame)
title.Text = "Macro / Play"
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 20

-- ปุ่มสร้างง่าย ๆ
local function createButton(text, y)
    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(0, 230, 0, 30)
    btn.Position = UDim2.new(0, 10, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    btn.Text = text
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.SourceSans
    btn.TextSize = 18
    return btn
end

local recordBtn = createButton("Start Record Macro", 40)
local playBtn = createButton("Start Play", 80)
local stopBtn = createButton("Stop Macro", 120)
local saveBtn = createButton("Save Macro", 160)
local loadBtn = createButton("Load Macro", 200)
local autoPlaceBtn = createButton("Toggle Auto Place: OFF", 280)

local inputBox = Instance.new("TextBox", frame)
inputBox.Size = UDim2.new(0, 230, 0, 30)
inputBox.Position = UDim2.new(0, 10, 0, 240)
inputBox.PlaceholderText = "ชื่อไฟล์มาโคร..."
inputBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
inputBox.TextColor3 = Color3.new(1,1,1)
inputBox.Font = Enum.Font.SourceSans
inputBox.TextSize = 18

-- ระบบมาโคร
local macroData = {}
local isRecording = false
local isPlaying = false

recordBtn.MouseButton1Click:Connect(function()
    macroData = {}
    isRecording = not isRecording
    recordBtn.Text = isRecording and "Recording..." or "Start Record Macro"

    if isRecording then
        UIS.InputBegan:Connect(function(input, gp)
            if not gp then
                table.insert(macroData, {key = input.KeyCode, time = tick()})
            end
        end)
    end
end)

playBtn.MouseButton1Click:Connect(function()
    if #macroData == 0 then return end
    isPlaying = true
    local lastTime = macroData[1].time
    for _, v in ipairs(macroData) do
        if not isPlaying then break end
        task.wait(v.time - lastTime)
        VIM:SendKeyEvent(true, v.key, false, game)
        VIM:SendKeyEvent(false, v.key, false, game)
        lastTime = v.time
    end
end)

stopBtn.MouseButton1Click:Connect(function()
    isPlaying = false
end)

saveBtn.MouseButton1Click:Connect(function()
    if inputBox.Text == "" then return end
    writefile(inputBox.Text..".json", HttpService:JSONEncode(macroData))
end)

loadBtn.MouseButton1Click:Connect(function()
    if inputBox.Text == "" then return end
    local data = readfile(inputBox.Text..".json")
    macroData = HttpService:JSONDecode(data)
end)

-- AUTO UPGRADE
local upgradeEvent = ReplicatedStorage:WaitForChild("Remotes"):FindFirstChild("Upgrade")
task.spawn(function()
    while true do
        task.wait(5)
        for _, unit in pairs(workspace.Units:GetChildren()) do
            if unit:FindFirstChild("Owner") and unit.Owner.Value == LocalPlayer then
                upgradeEvent:FireServer(unit)
            end
        end
    end
end)

-- AUTO SKIP WAVE
local skipWaveRemote = ReplicatedStorage:WaitForChild("Remotes"):FindFirstChild("VoteSkip")
task.spawn(function()
    while true do
        task.wait(7)
        skipWaveRemote:FireServer()
    end
end)

-- AUTO PLACE UNIT
local placeRemote = ReplicatedStorage:WaitForChild("Remotes"):FindFirstChild("PlaceUnit")
local autoPlace = false
local placePositions = {Vector3.new(0,0,0)} -- ตำแหน่งที่ต้องการวาง (แก้ได้)

autoPlaceBtn.MouseButton1Click:Connect(function()
    autoPlace = not autoPlace
    autoPlaceBtn.Text = "Toggle Auto Place: " .. (autoPlace and "ON" or "OFF")
end)

task.spawn(function()
    while true do
        task.wait(3)
        if autoPlace then
            for _, pos in ipairs(placePositions) do
                placeRemote:FireServer("Unit1", pos) -- "Unit1" เปลี่ยนเป็นชื่อยูนิตที่คุณต้องการวาง
            end
        end
    end
end)