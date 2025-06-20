for _, npc in pairs(workspace:GetDescendants()) do
    if npc:IsA("Model") and npc:FindFirstChildOfClass("Humanoid") and not game.Players:GetPlayerFromCharacter(npc) then
        local hrp = npc:FindFirstChild("HumanoidRootPart")
        if hrp then
            -- ล็อกตำแหน่ง
            hrp.Anchored = true
        end

        -- หยุดเดิน
        local humanoid = npc:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = 0
            humanoid.JumpPower = 0
        end

        -- ลบ AI ถ้ามี
        for _, v in pairs(npc:GetChildren()) do
            if v:IsA("Script") or v:IsA("LocalScript") then
                v:Destroy()
            end
        end
    end
end