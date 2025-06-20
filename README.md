local ReplicatedStorage = game:GetService("ReplicatedStorage")
local remote = ReplicatedStorage:WaitForChild("ToggleInvisibility")

remote.OnServerEvent:Connect(function(player, invis)
	local character = player.Character
	if not character then return end

	for _, part in pairs(character:GetDescendants()) do
		if part:IsA("BasePart") or part:IsA("Decal") then
			part.Transparency = invis and 1 or 0
		end
	end
end)
