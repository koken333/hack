-- // KRNL Path Recorder + Replay //
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

local path = {}
local recording = false

local function addPoint(pos)
    table.insert(path, pos)
    print("Point added:", pos)
end

local function walkPath()
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hum = char:WaitForChild("Humanoid")
    for i, pos in ipairs(path) do
        hum:MoveTo(pos)
        hum.MoveToFinished:Wait()
        wait(0.1)
    end
    print("Replay finished!")
end

-- บันทึกพิกัดเมื่อคลิกซ้าย
Mouse.Button1Down:Connect(function()
    if recording then
        addPoint(Mouse.Hit.p)
    end
end)

-- ปุ่มควบคุม
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end

    if input.KeyCode == Enum.KeyCode.R then
        recording = not recording
        print(recording and "Recording started!" or "Recording stopped!")
    
    elseif input.KeyCode == Enum.KeyCode.P then
        if #path == 0 then
            print("No path recorded!")
            return
        end
        print("Replaying path...")
        walkPath()
    
    elseif input.KeyCode == Enum.KeyCode.C then
        path = {}
        print("Path cleared!")
    end
end)

print("Path Recorder Loaded! R = start/stop record, P = replay, C = clear")