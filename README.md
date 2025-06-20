
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local camera = workspace.CurrentCamera

local player = Players.LocalPlayer
local mouse = player:GetMouse()


local FOV = 150
local AimlockEnabled = false


local circle = Drawing.new("Circle")
circle.Thickness = 2
circle.NumSides = 60
circle.Radius = FOV
circle.Color = Color3.fromRGB(255, 255, 0)
circle.Filled = false
circle.Visible = true
circle.Transparency = 1
circle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)


local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.Name = "AimlockUI"

local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 250, 0, 160)
frame.Position = UDim2.new(0, 30, 0.75, 0)
frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Text = "🔫 Aimlock Settings"
title.Size = UDim2.new(1, 0, 0, 30)
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SourceSansBold
title.TextSize = 22

local toggleButton = Instance.new("TextButton", frame)
toggleButton.Size = UDim2.new(1, -20, 0, 40)
toggleButton.Position = UDim2.new(0, 10, 0, 40)
toggleButton.Text = "🔘 เปิด Aimlock"
toggleButton.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextSize = 18

toggleButton.MouseButton1Click:Connect(function()
    AimlockEnabled = not AimlockEnabled
    toggleButton.Text = AimlockEnabled and "✅ ปิด Aimlock" or "🔘 เปิด Aimlock"
    toggleButton.BackgroundColor3 = AimlockEnabled and Color3.fromRGB(170, 0, 0) or Color3.fromRGB(0, 170, 0)
end)

local fovLabel = Instance.new("TextLabel", frame)
fovLabel.Text = "ขนาดวงกลม: " .. FOV
fovLabel.Size = UDim2.new(1, -20, 0, 20)
fovLabel.Position = UDim2.new(0, 10, 0, 90)
fovLabel.TextColor3 = Color3.new(1, 1, 1)
fovLabel.BackgroundTransparency = 1
fovLabel.Font = Enum.Font.SourceSans
tfovLabel.TextSize = 16

local up = Instance.new("TextButton", frame)
up.Text = "⬆ เพิ่ม FOV"
up.Size = UDim2.new(0.5, -15, 0, 30)
up.Position = UDim2.new(0, 10, 0, 120)
up.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
up.TextColor3 = Color3.new(1, 1, 1)
up.Font = Enum.Font.SourceSansBold
up.TextSize = 16

local down = Instance.new("TextButton", frame)
down.Text = "⬇ ลด FOV"
down.Size = UDim2.new(0.5, -15, 0, 30)
down.Position = UDim2.new(0.5, 5, 0, 120)
down.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
down.TextColor3 = Color3.new(1, 1, 1)
down.Font = Enum.Font.SourceSansBold
down.TextSize = 16

up.MouseButton1Click:Connect(function()
    FOV = FOV + 10
    circle.Radius = FOV
    fovLabel.Text = "ขนาดวงกลม: " .. FOV
end)

down.MouseButton1Click:Connect(function()
    FOV = math.max(50, FOV - 10)
    circle.Radius = FOV
    fovLabel.Text = "ขนาดวงกลม: " .. FOV
end)


local function getClosestTarget()
    local closest, shortest = nil, math.huge
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            if not plr.Name:lower():find("ครูดาว") then
                local pos, onscreen = camera:WorldToViewportPoint(plr.Character.HumanoidRootPart.Position)
                local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y/2)).Magnitude
                if onscreen and dist < FOV and dist < shortest then
                    shortest = dist
                    closest = plr
                end
            end
        end
    end
    return closest
end

RunService.RenderStepped:Connect(function()
    circle.Position = Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y/2)

    if AimlockEnabled then
        local target = getClosestTarget()
        if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
            camera.CFrame = CFrame.new(camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
        end
    end
end)


