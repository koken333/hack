
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer


local function createESP(player)
    if player == LocalPlayer then return end
    player.CharacterAdded:Connect(function(character)
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.FillColor = Color3.fromRGB(255, 0, 0) 
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.FillTransparency = 0.3
        highlight.OutlineTransparency = 0
        highlight.Adornee = character
        highlight.Parent = character
    end)
end


for _, player in ipairs(Players:GetPlayers()) do
    createESP(player)
end


Players.PlayerAdded:Connect(function(player)
    createESP(player)
end)
