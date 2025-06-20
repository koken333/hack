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
     
        local camera = workspace.CurrentCamera
        local direction = (targetPos - camera.CFrame.Position).unit
        camera.CFrame = CFrame.new(camera.CFrame.Position, camera.CFrame.Position + direction)
       
    end
end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local camera = workspace.CurrentCamera


local function createNameTag(character)
    if character:FindFirstChild("HumanoidRootPart") and not character:FindFirstChild("NameTag") then
        local billboardGui = Instance.new("BillboardGui")
        billboardGui.Name = "NameTag"
        billboardGui.Adornee = character.HumanoidRootPart
        billboardGui.Size = UDim2.new(0, 100, 0, 50)
        billboardGui.StudsOffset = Vector3.new(0, 2, 0)
        billboardGui.AlwaysOnTop = true

        local textLabel = Instance.new("TextLabel", billboardGui)
        textLabel.Size = UDim2.new(1, 0, 1, 0)
        textLabel.BackgroundTransparency = 1
        textLabel.TextColor3 = Color3.new(1, 0, 0) -- สีแดง
        textLabel.TextStrokeColor3 = Color3.new(0, 0, 0)
        textLabel.TextStrokeTransparency = 0
        textLabel.Text = character.Name
        textLabel.Font = Enum.Font.SourceSansBold
        textLabel.TextScaled = true

        billboardGui.Parent = character
    end
end


RunService.Heartbeat:Connect(function()
    for _, enemy in pairs(workspace.Enemies:GetChildren()) do
        if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
            createNameTag(enemy)
        end
    end
end)
