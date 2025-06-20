-- ลูปบังคับให้เล็กตลอดเวลา
while task.wait(0.5) do
    pcall(function()
        local char = game.Players.LocalPlayer.Character
        local humanoid = char:FindFirstChild("Humanoid")
        if humanoid then
            for _, name in ipairs({"BodyHeightScale", "BodyWidthScale", "BodyDepthScale", "HeadScale"}) do
                local scale = humanoid:FindFirstChild(name)
                if scale then
                    scale.Value = 0.5
                end
            end
        end
    end)
end