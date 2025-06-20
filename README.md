
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("🔥 ESP/Speed Hub", "DarkTheme")

local MainTab = Window:NewTab("Main")
local Section = MainTab:

task.wait(1) 
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local kavoUI = nil
for _, gui in pairs(LocalPlayer.PlayerGui:GetChildren()) do
    if gui:FindFirstChild("MainFrame") then
        kavoUI = gui
        break
    end
end

if kavoUI then
    local frame = kavoUI:FindFirstChild("MainFrame")
    if frame then
        local UIS = game:GetService("UserInputService")
        local dragging = false
        local dragInput, dragStart, startPos

        frame.Active = true
        frame.Draggable = false 

        frame.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                dragStart = input.Position
                startPos = frame.Position

                input.Changed:Connect(function()
                    if input.UserInputState == Enum.UserInputState.End then
                        dragging = false
                    end
                end)
            end
        end)

        frame.InputChanged:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
                dragInput = input
            end
        end)

        game:GetService("RunService").RenderStepped:Connect(function()
            if dragging and dragInput then
                local delta = dragInput.Position - dragStart
                frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
            end
        end)
    end
end
local ESPEnabled = false
local SpeedEnabled = false
local speedValue = 50

Section:NewToggle("ESP", "แสดงชื่อ + ระยะ", function(state)
    ESPEnabled = state
end)

Section:NewToggle("Speed Hack", "เดินไว", function(state)
    SpeedEnabled = state
end)

Section:NewSlider("Speed", "ปรับความเร็ว", 200, 16, function(val)
    speedValue = val
end)

-- Speed Hack
task.spawn(function()
    while true do
        task.wait(0.2)
        if SpeedEnabled then
            local char = game.Players.LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char.Humanoid.WalkSpeed = speedValue
            end
        end
    end
end)

-- ESP (ชื่อ + ระยะ)
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

function setupESP(player)
    if player == LocalPlayer then return end
    local function onChar(char)
        if char:FindFirstChild("Head") and not char:FindFirstChild("ESP_NameTag") then
            local tag = Instance.new("BillboardGui", char)
            tag.Name = "ESP_NameTag"
            tag.Adornee = char.Head
            tag.Size = UDim2.new(0, 100, 0, 20)
            tag.StudsOffset = Vector3.new(0, 2.5, 0)
            tag.AlwaysOnTop = true

            local text = Instance.new("TextLabel", tag)
            text.Size = UDim2.new(1, 0, 1, 0)
            text.BackgroundTransparency = 1
            text.TextColor3 = Color3.fromRGB(255, 255, 255)
            text.TextScaled = true
            text.Font = Enum.Font.SourceSans
            text.Text = ""

            RunService.RenderStepped:Connect(function()
                if ESPEnabled and char:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    local dist = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                    text.Text = player.Name .. " [" .. dist .. "]"
                    tag.Enabled = true
                else
                    tag.Enabled = false
                end
            end)
        end
    end
    if player.Character then onChar(player.Character) end
    player.CharacterAdded:Connect(onChar)
end

for _, p in pairs(Players:GetPlayers()) do
    setupESP(p)
end
Players.PlayerAdded:Connect(setupESP)

------------------------------------------------
-- 📦 ปุ่มพับ/ขยาย UI Kavo แบบ "โปรสัส"
------------------------------------------------
task.wait(1) -- รอให้ Kavo โหลดก่อน
local kavoUI = nil
for _, gui in pairs(LocalPlayer.PlayerGui:GetChildren()) do
    if gui:FindFirstChild("MainFrame") then
        kavoUI = gui
        break
    end
end

if kavoUI then
    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Size = UDim2.new(0, 120, 0, 35)
    toggleBtn.Position = UDim2.new(0, 20, 0, 20)
    toggleBtn.Text = "📦 พับ UI"
    toggleBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
    toggleBtn.TextColor3 = Color3.new(1, 1, 1)
    toggleBtn.TextScaled = true
    toggleBtn.Font = Enum.Font.SourceSansBold
    toggleBtn.ZIndex = 999999
    toggleBtn.Parent = kavoUI

    local isFolded = false
    toggleBtn.MouseButton1Click:Connect(function()
        isFolded = not isFolded
        local mainFrame = kavoUI:FindFirstChild("MainFrame")
        if mainFrame then
            mainFrame.Visible = not isFolded
            toggleBtn.Text = isFolded and "📤 แสดง UI" or "📦 พับ UI"
        end
    end)
end