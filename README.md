local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local maxSpeed = 50
local increment = 1
local speed = 20  

local function setSpeed()
    local char = LocalPlayer.Character
    if char then
        local humanoid = char:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = speed
        end
    end
end

LocalPlayer.CharacterAdded:Connect(function(char)
    char:WaitForChild("Humanoid")
    speed = 16
    setSpeed()
end)

while true do
    if speed < maxSpeed then
        speed = speed + increment
        setSpeed()
    end
    task.wait(0.5)
end
