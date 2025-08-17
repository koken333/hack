RunService.RenderStepped:Connect(function()
	for _, npc in pairs(workspace:GetDescendants()) do
		if npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then

			if not Players:GetPlayerFromCharacter(npc) then
				if npc.Humanoid.Health > 1 then
					npc.Humanoid.Health = 1
				end
			end
		end
	end
end)
