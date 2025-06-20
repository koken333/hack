
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("My Cheat Hub", "DarkTheme")


local MainTab = Window:NewTab("Main")
local Section = MainTab:NewSection("Functions")


local ESPEnabled = false
local SpeedEnabled = false
local speedValue = 50


Section:NewToggle("ESP", "แสดงชื่อ + ระยะ", function(state)
    ESPEnabled = state
end)


Section:NewToggle("Speed Hack", "เปิด/ปิดความเร็ว", function(state)
    SpeedEnabled = state
end)


Section:NewSlider("ปรับ Speed", "กำหนด WalkSpeed", 200, 16, function(val)
    speedValue = val
end)


local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

function setupESP(player)
    if player == LocalPlayer then return end
    local function onChar(char)
        if char:FindFirstChild("Head") and not char:FindFirstChild("ESP_Name") then
            local tag = Instance.new("BillboardGui", char)
            tag.Name = "ESP_Name"
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
                    text.Text = player.Name .. " [" .. tostring(dist) .. "]"
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


task.spawn(function()
    while true do
        task.wait(0.3)
        if SpeedEnabled then
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char.Humanoid.WalkSpeed = speedValue
            end
        end
    end
end)
