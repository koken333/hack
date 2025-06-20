local char = game.Players.LocalPlayer.Character
if char and char:FindFirstChild("Humanoid") then
    for _, v in pairs(char.Humanoid:GetChildren()) do
        if v:IsA("NumberValue") then
            print(v.Name .. " = " .. v.Value)
        end
    end
end
BodyHeightScale = 1
BodyWidthScale = 1
BodyDepthScale = 1
HeadScale = 1