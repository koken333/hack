
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("🎯 ESP UI", "DarkTheme")


local Tab = Window:NewTab("Main")
local Section = Tab:NewSection("ESP Settings")


local ESPEnabled = false
local ESPColor = Color3.fromRGB(255, 255, 255) -- สีเริ่มต้น: ขาว


Section:NewToggle("เปิด/ปิด ESP", "แสดงชื่อ + ระยะทาง", function(state)
    ESPEnabled = state
end)


Section:NewDropdown("เลือกสี ESP", "เปลี่ยนสีตัวหนังสือ", {"ขาว", "แดง", "เขียว", "น้ำเงิน", "เหลือง"}, function(choice)
    if choice == "ขาว" then
        ESPColor = Color3.fromRGB(255, 255, 255)
    elseif choice == "แดง" then
        ESPColor = Color3.fromRGB(255, 0, 0)
    elseif choice == "เขียว" then
        ESPColor = Color3.fromRGB(0, 255, 0)
    elseif choice == "น้ำเงิน" then
        ESPColor = Color3.fromRGB(0, 170, 255)
    elseif choice == "เหลือง" then
        ESPColor = Color3.fromRGB(255, 255, 0)
    end
end)

-- ⚙️ ระบบ EPS
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
            tag.Size = UDim2.new(0, 100, 0, 20)
            tag.StudsOffset = Vector3.new(0, 2.5, 0)
            tag.AlwaysOnTop = true

            local text = Instance.new("TextLabel", tag)
            text.Size = UDim2.new(1, 0, 1, 0)
            text.BackgroundTransparency = 1
            text.TextColor3 = ESPColor
            text.TextScaled = true
            text.Font = Enum.Font.SourceSans
            text.Text = ""

            
            RunService.RenderStepped:Connect(function()
                if ESPEnabled and char:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    local dist = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                    text.Text = player.Name .. " [" .. dist .. "m]"
                    text.TextColor3 = ESPColor
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