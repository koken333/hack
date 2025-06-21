local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("SHARK X HUB", "DarkTheme")
local Tab = Window:NewTab("Players")
-- Teleport Section
local TPSection = Tab:NewSection("Teleport")

local players = {}
local SelectPlayer
local function refreshPlayerList()
    table.clear(players)
    for _,v in ipairs(game:GetService("Players"):GetPlayers()) do
        table.insert(players, v.Name)
    end
end
refreshPlayerList()

TPSection:NewDropdown("Select Player", "Choose someone", players, function(text) SelectPlayer = text end)
TPSection:NewButton("Refresh", "Update player list", refreshPlayerList)
TPSection:NewButton("Teleport", "Tp to selected player", function()
    local target = game.Players:FindFirstChild(SelectPlayer)
    if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame =
            target.Character.HumanoidRootPart.CFrame
    end
end)

-- ESP Section
local ESPSection = Tab:NewSection("ESP (Toggle)")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)

ESPSection:NewToggle("Enable ESP", "Toggle ESP display", function(state)
    getgenv().ESPEnabled = state
end)

local function setupESP(player)
    if player == LocalPlayer then return end
    local function onCharacter(char)
        char:WaitForChild("Head")
        if char:FindFirstChild("ESP_Tag") then return end
        -- Create BillboardGui
        local tag = Instance.new("BillboardGui", char)
        tag.Name = "ESP_Tag"
        tag.Adornee = char.Head
        tag.Size = UDim2.new(0,100,0,20)
        tag.StudsOffset = Vector3.new(0,2.5,0)
        tag.AlwaysOnTop = true
        local label = Instance.new("TextLabel", tag)
        label.Size = UDim2.new(1,0,1,0)
        label.BackgroundTransparency = 1
        label.TextColor3 = ESPColor
        label.TextStrokeTransparency = 0.5
        label.TextScaled = true
        label.Font = Enum.Font.SourceSans
        label.Text = player.Name
        -- Highlight
        local hl = Instance.new("Highlight", char)
        hl.Name = "ESP_Highlight"
        hl.FillColor = ESPColor
        hl.OutlineColor = Color3.new(1,1,1)
        hl.FillTransparency = 0.5
        hl.OutlineTransparency = 0
        hl.Enabled = false

        RunService.RenderStepped:Connect(function()
            if getgenv().ESPEnabled and char.Parent and char:FindFirstChild("HumanoidRootPart")
               and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local dist = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                label.Text = player.Name.." ["..dist.."m]"
                tag.Enabled = true
                hl.Enabled = true
            else
                tag.Enabled = false
                hl.Enabled = false
            end
        end)
    end

    player.CharacterAdded:Connect(onCharacter)
    if player.Character then onCharacter(player.Character) end
end

for _,p in ipairs(Players:GetPlayers()) do setupESP(p) end
Players.PlayerAdded:Connect(setupESP)
