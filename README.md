local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

while true do
    wait(0.01)
    local closestEnemy = nil
    local shortestDistance = math.huge

    for _, enemy in pairs(game.Workspace.Enemies:GetChildren()) do
        if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
            local enemyPos = enemy.HumanoidRootPart.Position
            local playerPos = player.Character.HumanoidRootPart.Position
            local distance = (enemyPos - playerPos).magnitude
            if distance < shortestDistance then
                shortestDistance = distance
                closestEnemy = enemy
            end
        end
    end

    if closestEnemy then
        local targetPos = closestEnemy.HumanoidRootPart.Position
        mouse.Target = closestEnemy.HumanoidRootPart 
    end
end

