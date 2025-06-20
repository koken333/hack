
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("333", "DarkTheme")
local MainTab = Window:NewTab("Debug")
local Section = MainTab:NewSection("ESP Debug")


local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("ESP Hub", "DarkTheme")


local Tab = Window:NewTab("Main")
local Section = Tab:NewSection("Tools")

Section:NewButton("Speed x3", "ทำให้เดินเร็ว", function()
    game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "FoldGUI"

local button = Instance.new("TextButton", gui)
button.Size = UDim2.new(0, 120, 0, 35)
button.Position = UDim2.new(0, 20, 0, 20)
button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
button.Text = "📦 พับ UI"
button.TextColor3 = Color3.new(1,1,1)
button.TextScaled = true
button.Font = Enum.Font.SourceSansBold
button.ZIndex = 999999

local toggled = true
button.MouseButton1Click:Connect(function()
    toggled = not toggled
    for _, gui in ipairs(LocalPlayer.PlayerGui:GetChildren()) do
        if gui:IsA("ScreenGui") and gui.Name ~= "FoldGUI" then
            gui.Enabled = toggled
        end
    end
    button.Text = toggled and "📦 พับ UI" or "📤 แสดง UI"
end)
local ESPEnabled = false


Section:NewToggle("ESP ON/OFF", "Debug ESP Toggle", function(state)
    ESPEnabled = state
    print("[DEBUG] ESPEnabled =", ESPEnabled)
end)


local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local function addHighlight(character)
    if not character:FindFirstChild("ESP_Highlight") then
        print("[DEBUG] Adding Highlight to", character.Name)
        local hl = Instance.new("Highlight")
        hl.Name = "ESP_Highlight"
        hl.Adornee = character
        hl.FillColor = Color3.fromRGB(0, 200, 255)
        hl.FillTransparency = 0.4
        hl.Parent = character

        RunService.RenderStepped:Connect(function()
            hl.Enabled = ESPEnabled
        end)
    end
end

local function addNameTag(character, player)
    if character:FindFirstChild("Head") and not character:FindFirstChild("ESP_NameTag") then
        print("[DEBUG] Adding NameTag to", player.Name)
        local billboard = Instance.new("BillboardGui")
        billboard.Name = "ESP_NameTag"
        billboard.Adornee = character.Head
        billboard.Size = UDim2.new(0, 100, 0, 20)
        billboard.StudsOffset = Vector3.new(0, 2.5, 0)
        billboard.AlwaysOnTop = true
        billboard.Parent = character

        local textLabel = Instance.new("TextLabel", billboard)
        textLabel.Name = "Label"
        textLabel.Size = UDim2.new(1, 0, 1, 0)
        textLabel.BackgroundTransparency = 1
        textLabel.TextColor3 = Color3.new(1, 1, 1)
        textLabel.TextScaled = true
        textLabel.Font = Enum.Font.SourceSans

        RunService.RenderStepped:Connect(function()
            if ESPEnabled and character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local dist = math.floor((character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                textLabel.Text = player.Name .. " [" .. dist .. "]"
                billboard.Enabled = true
            else
                billboard.Enabled = false
            end
        end)
    end
end

local function setupESP(player)
    print("[DEBUG] setupESP called for player:", player.Name)
    if player == LocalPlayer then return end
    local function onChar(char)
        print("[DEBUG] CharacterLoaded for", player.Name)
        addHighlight(char)
        addNameTag(char, player)
    end
    if player.Character then onChar(player.Character) end
    player.CharacterAdded:Connect(onChar)
end


for _, p in pairs(Players:GetPlayers()) do
    setupESP(p)
end
Players.PlayerAdded:Connect(setupESP)