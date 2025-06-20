local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local ESPEnabled = true

-- สร้าง UI ปุ่มเปิด/ปิด ESP บนหน้าจอ (มือถือ/PC)
local function createToggleButton()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "ESP_Toggle_GUI"
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 100, 0, 35)
    button.Position = UDim2.new(0, 20, 0, 100)
    button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
    button.Text = "ESP: ON"
    button.TextColor3 = Color3.new(1, 1, 1)
    button.TextScaled = true
    button.Font = Enum.Font.SourceSansBold
    button.BackgroundTransparency = 0.2
    button.BorderSizePixel = 2
    button.Parent = screenGui

    button.MouseButton1Click:Connect(function()
        ESPEnabled = not ESPEnabled
        button.Text = ESPEnabled and "ESP: ON" or "ESP: OFF"
    end)
end

-- สร้าง Highlight เรืองแสงให้ตัวละคร
local function addHighlight(character)
    if not character:FindFirstChild("ESP_Highlight") then
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.Adornee = character
        highlight.FillColor = Color3.fromRGB(0, 170, 255) -- สีฟ้าเรืองแสง
        highlight.FillTransparency = 0.4
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.OutlineTransparency = 0
        highlight.Parent = character

        -- ควบคุมเปิด/ปิด Highlight ตาม ESPEnabled
        RunService.RenderStepped:Connect(function()
            highlight.Enabled = ESPEnabled
        end)
    end
end

-- สร้าง BillboardGUI แสดงชื่อ + ระยะทาง ตัวหนังสือเล็ก
local function addNameTag(character, player)
    if character:FindFirstChild("Head") and not character:FindFirstChild("ESP_NameTag") then
        local billboard = Instance.new("BillboardGui")
        billboard.Name = "ESP_NameTag"
        billboard.Adornee = character.Head
        billboard.Size = UDim2.new(0, 100, 0, 20)
        billboard.StudsOffset = Vector3.new(0, 2.5, 0)
        billboard.AlwaysOnTop = true
        billboard.Parent = character

        local textLabel = Instance.new("TextLabel")
        textLabel.Name = "Label"
        textLabel.Size = UDim2.new(1, 0, 1, 0)
        textLabel.BackgroundTransparency = 1
        textLabel.TextColor3 = Color3.new(1, 1, 1)
        textLabel.TextStrokeTransparency = 0.7
        textLabel.Font = Enum.Font.SourceSans
        textLabel.TextScaled = true
        textLabel.Parent = billboard

        -- อัพเดตชื่อ + ระยะทางทุกเฟรม
        RunService.RenderStepped:Connect(function()
            if ESPEnabled and character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local dist = math.floor((character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                textLabel.Text = player.Name .. " [" .. tostring(dist) .. " studs]"
                billboard.Enabled = true
            else
                billboard.Enabled = false
            end
        end)
    end
end

-- ฟังก์ชันหลัก ใส่ ESP ให้ผู้เล่น
local function applyESP(player)
    if player == LocalPlayer then return end
    local function onCharacter(character)
        addHighlight(character)
        addNameTag(character, player)
    end

    if player.Character then
        onCharacter(player.Character)
    end
    player.CharacterAdded:Connect(onCharacter)
end

-- สร้าง UI ปุ่ม
createToggleButton()

-- ใส่ ESP ให้ผู้เล่นที่อยู่ในเกมแล้ว
for _, player in pairs(Players:GetPlayers()) do
    applyESP(player)
end

-- ใส่ ESP ให้ผู้เล่นใหม่ที่เข้ามา
Players.PlayerAdded:Connect(applyESP)
