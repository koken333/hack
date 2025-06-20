
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local camera = workspace.CurrentCamera

local player = Players.LocalPlayer
local ESPEnabled = true
local AimlockEnabled = false
local FOV = 150


local circle = Drawing.new("Circle")
circle.Thickness = 2
circle.NumSides = 64
circle.Filled = false
circle.Transparency = 1
circle.Color = Color3.fromRGB(255, 0, 0)
circle.Radius = FOV
circle.Visible = true


local gui = Instance.new("ScreenGui")
gui.Name = "TouchAimbotUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = player:WaitForChild("PlayerGui")

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 320, 0, 420)
mainFrame.Position = UDim2.new(0.05, 0, 0.3, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(120, 100, 90)
mainFrame.BackgroundTransparency = 0
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = gui

local function createLabelButtonPair(titleText, buttonText, positionY, callback)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0, 140, 0, 50)
    label.Position = UDim2.new(0, 10, 0, positionY)
    label.Text = titleText
    label.TextColor3 = Color3.new(1, 1, 1)
    label.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    label.Font = Enum.Font.SourceSansBold
    label.TextSize = 22
    label.Parent = mainFrame

    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 160, 0, 50)
    button.Position = UDim2.new(0, 150, 0, positionY)
    button.Text = buttonText
    button.TextColor3 = Color3.new(0, 0, 0)
    button.BackgroundColor3 = Color3.fromRGB(150, 240, 255)
    button.Font = Enum.Font.SourceSansBold
    button.TextSize = 20
    button.AutoButtonColor = true
    button.Parent = mainFrame
    button.MouseButton1Click:Connect(callback)
    return button
end

createLabelButtonPair("Aimbot", "ที่เปิดปิด aim bot", 30, function()
    AimlockEnabled = not AimlockEnabled
end)

createLabelButtonPair("แสดงชื่อผู้เล่น", "ปิดเปิดแสดงชื่อผู้เล่น", 100, function()
    ESPEnabled = not ESPEnabled
    for _, gui in pairs(workspace:GetDescendants()) do
        if gui:IsA("BillboardGui") and gui.Name == "ESPName" then
            gui.Enabled = ESPEnabled
        end
    end
end)

createLabelButtonPair("วงกลม", "ปรับขนาดวงกลม", 170, function()
    FOV = FOV + 20
    if FOV > 300 then FOV = 100 end
    circle.Radius = FOV
end)


local function createESP3D(target)
    if target:FindFirstChild("ESPName") then return end
    local gui = Instance.new("BillboardGui")
    gui.Name = "ESPName"
    gui.Size = UDim2.new(0, 100, 0, 30)
    gui.AlwaysOnTop = true
    gui.StudsOffset = Vector3.new(0, 3, 0)
    gui.Adornee = target:FindFirstChild("Head")
    gui.Parent = target

    local nameLabel = Instance.new("TextLabel")
    nameLabel.Size = UDim2.new(1, 0, 1, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = target.Name
    nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    nameLabel.TextStrokeTransparency = 0.5
    nameLabel.TextScaled = true
    nameLabel.Font = Enum.Font.SourceSansBold
    nameLabel.Parent = gui
end

for _, plr in pairs(Players:GetPlayers()) do
    if plr ~= player then
        plr.CharacterAdded:Connect(function(char)
            char:WaitForChild("Head")
            createESP3D(char)
        end)
        if plr.Character and plr.Character:FindFirstChild("Head") then
            createESP3D(plr.Character)
        end
    end
end

Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function(char)
        char:WaitForChild("Head")
        createESP3D(char)
    end)
end)


local function getClosestTarget()
    local closest, shortest = nil, math.huge
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            if not plr.Name:lower():find("ครูดาว") then
                local pos, onScreen = camera:WorldToViewportPoint(plr.Character.HumanoidRootPart.Position)
                local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y/2)).Magnitude
                if onScreen and dist < FOV and dist < shortest then
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


