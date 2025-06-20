local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local function addESP(character)
    if not character:FindFirstChild("ESP_Highlight") then
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.FillColor = Color3.fromRGB(0, 255, 255)  
        highlight.FillTransparency = 0.25
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.OutlineTransparency = 0
        highlight.Adornee = character
        highlight.Parent = character
    end
end

local function setupESP(player)
    if player == LocalPlayer then return end
    if player.Character then
        addESP(player.Character)
    end
    player.CharacterAdded:Connect(function(char)
        addESP(char)
    end)
end


for _, p in ipairs(Players:GetPlayers()) do
    setupESP(p)
end


Players.PlayerAdded:Connect(function(p)
    setupESP(p)
end)
