-- โหลด Kavo UI
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("🔥 ESP/Speed Hub", "DarkTheme")

-- แท็บและหัวข้อ
local MainTab = Window:NewTab("Main")
local Section = MainTab:NewSection("Cheat Tools")

-- ตัวแปรหลัก
local ESPEnabled = false
local SpeedEnabled = false
local speedValue = 50

-- ปุ่ม & สไลด์ใน GUI
Section:NewToggle("ESP", "เปิด/ปิด ชื่อ+ระยะ", function(v)
    ESPEnabled = v
end)

Section:NewToggle("Speed Hack", "เปิด/ปิด ความเร็ว", function(v)
    SpeedEnabled = v
end)

Section:NewSlider("ปรับความเร็ว", "เปลี่ยน WalkSpeed", 200, 16, function(v)
    speedValue = v
end)

-- ระบบ Speed Hack
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

-- ระบบ ESP
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local function setupESP(player)
    if player == LocalPlayer then return end

    local function onChar(char)
        if char:FindFirstChild("Head") and not char:FindFirstChild("ESP_NameTag") then
            local tag = Instance.new("BillboardGui", char)
            tag.Name = "ESP_NameTag"
            tag.Adornee = char.Head
            tag.Size = UDim2.new(0, 100, 0, 