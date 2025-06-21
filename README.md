local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("SHARK X HUB", "DarkTheme")
local Tab = Window:NewTab("Players")
local Section = Tab:NewSection("Teleport")

local players = {}
local Select

local function refreshPlayerList()
    table.clear(players)
    for i, v in ipairs(game:GetService("Players"):GetPlayers()) do
        table.insert(players, v.Name)
    end
end

refreshPlayerList()

Section:NewDropdown("Select Player", " ", players, function(text)
    Select = text
end)

Section:NewButton("Refresh", " ", function()
    refreshPlayerList()
end)

Section:NewButton("Teleport", " ", function()
    if Select and game.Players:FindFirstChild(Select) and game.Players[Select].Character and game.Players[Select].Character:FindFirstChild("HumanoidRootPart") then
        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = game.Players[Select].Character.HumanoidRootPart.CFrame
    end
end)

-- ====================
-- ESP Section
-- ====================
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local ESPEnabled = true -- เปิดอัตโนมัติ ถ้าต้องการ toggle ให้สร้างปุ่มเปิด-ปิดเพิ่มเติม
local ESPColor = Color3.fromRGB(0, 255, 0)

local function setupESP(player)
    if player == LocalPlayer then return end
    player.CharacterAdded:Connect(function(char)
        char:WaitForChild("Head")
        if char:FindFirstChild("ESP_Tag") then return end

        local tag = Instance.new("BillboardGui", char)
        tag.Name = "ESP_Tag"
        tag.Adornee = char.Head
        tag.Size = UDim2.new(0, 100, 0, 20)
        tag.StudsOffset = Vector3.new(0, 2.5, 0)
        tag.AlwaysOnTop = true

        local label = Instance.new("TextLabel", tag)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.TextColor3 = ESPColor
        label.TextStrokeTransparency = 0.5
        label.TextScaled = true
        label.Font = Enum.Font.SourceSans
        label.Text = player.Name

        local hl = Instance.new("Highlight", char)
        hl.Name = "ESP_Highlight"
        hl.FillColor = ESPColor
        hl.OutlineColor = Color3.new(1, 1, 1)
        hl.FillTransparency = 0.5
        hl.OutlineTransparency = 0
        hl.Enabled = false

        RunService.RenderStepped:Connect(function()
            if ESPEnabled and char.Parent and char:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local dist = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                label.Text = player.Name .. " [" .. dist .. "m]"
                tag.Enabled = true
                hl.Enabled = true
            else
                tag.Enabled = false
                hl.Enabled = false
            end
        end)
    end)

    if player.Character then
        setupESP(player)
    end
end

for _, p in ipairs(Players:GetPlayers()) do
    setupESP(p)
end
Players.PlayerAdded:Connect(setupESP)
