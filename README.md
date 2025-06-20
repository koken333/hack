
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local camera = Workspace.CurrentCamera

local player = Players.LocalPlayer
local mouse = player:GetMouse()


local FOV = 150
local AimlockEnabled = false
local HighlightTargets = true
local ExcludeName = "ครูดาว"


local circle = Drawing.new("Circle")
circle.Thickness = 2
circle.NumSides = 60
circle.Radius = FOV
circle.Color = Color3.fromRGB(0, 255, 255)
circle.Filled = false
circle.Visible = true
circle.Transparency = 1
circle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)


local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.Name = "AdvancedAimlockUI"

local mainFrame = Instance.new("Frame", screenGui)
mainFrame.Size = UDim2.new(0, 400, 0, 260)
mainFrame.Position = UDim2.new(0, 30, 0.3, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true

local tabFrame = Instance.new("Frame", mainFrame)
tabFrame.Size = UDim2.new(1, 0, 0, 40)
tabFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
tabFrame.BorderSizePixel = 0

local tabButtons = {}
local tabs = {}

local function createTab(name)
    local btn = Instance.new("TextButton", tabFrame)
    btn.Size = UDim2.new(0, 130, 1, 0)
    btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 18

    local content = Instance.new("Frame", mainFrame)
    content.Size = UDim2.new(1, 0, 1, -40)
    content.Position = UDim2.new(0, 0, 0, 40)
    content.BackgroundTransparency = 1
    content.Visible = false

    table.insert(tabButtons, btn)
    table.insert(tabs, content)

    btn.MouseButton1Click:Connect(function()
        for i, tab in pairs(tabs) do
            tab.Visible = (tabs[i] == content)
            tabButtons[i].BackgroundColor3 = (tabs[i] == content) and Color3.fromRGB(90, 90, 90) or Color3.fromRGB(60, 60, 60)
        end
    end)

    return content
end


local aimlockTab = createTab("Aimlock")

local toggleAimlock = Instance.new("TextButton", aimlockTab)
toggleAimlock.Size = UDim2.new(0, 360, 0, 40)
toggleAimlock.Position = UDim2.new(0, 20, 0, 10)
toggleAimlock.Text = "🔘 เปิด Aimlock"
toggleAimlock.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
toggleAimlock.TextColor3 = Color3.new(1, 1, 1)
toggleAimlock.Font = Enum.Font.SourceSansBold
toggleAimlock.TextSize = 20

toggleAimlock.MouseButton1Click:Connect(function()
    AimlockEnabled = not AimlockEnabled
    toggleAimlock.Text = AimlockEnabled and "✅ ปิด Aimlock" or "🔘 เปิด Aimlock"
    toggleAimlock.BackgroundColor3 = AimlockEnabled and Color3.fromRGB(170, 0, 0) or Color3.fromRGB(0, 170, 0)
end)

local fovLabel = Instance.new("TextLabel", aimlockTab)
fovLabel.Size = UDim2.new(0, 360, 0, 30)
fovLabel.Position = UDim2.new(0, 20, 0, 60)
fovLabel.Text = "ขนาดวงกลม: " .. FOV
fovLabel.TextColor3 = Color3.new(1, 1, 1)
fovLabel.Font = Enum.Font.SourceSans
fovLabel.TextSize = 18
fovLabel.BackgroundTransparency = 1

local fovUp = Instance.new("TextButton", aimlockTab)
fovUp.Size = UDim2.new(0, 170, 0, 30)
fovUp.Position = UDim2.new(0, 20, 0, 100)
fovUp.Text = "⬆ เพิ่ม FOV"
fovUp.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
fovUp.TextColor3 = Color3.new(1, 1, 1)
fovUp.Font = Enum.Font.SourceSansBold
fovUp.TextSize = 16

local fovDown = Instance.new("TextButton", aimlockTab)
fovDown.Size = UDim2.new(0, 170, 0, 30)
fovDown.Position = UDim2.new(0, 210, 0, 100)
fovDown.Text = "⬇ ลด FOV"
fovDown.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
fovDown.TextColor3 = Color3.new(1, 1, 1)
fovDown.Font = Enum.Font.SourceSansBold
fovDown.TextSize = 16

fovUp.MouseButton1Click:Connect(function()
    FOV = FOV + 10
    circle.Radius = FOV
    fovLabel.Text = "ขนาดวงกลม: " .. FOV
end)

fovDown.MouseButton1Click:Connect(function()
    FOV = math.max(50, FOV - 10)
    circle.Radius = FOV
    fovLabel.Text = "ขนาดวงกลม: " .. FOV
end)


local otherTab = createTab("อื่นๆ")
local note = Instance.new("TextLabel", otherTab)
note.Size = UDim2.new(1, -20, 0, 100)
note.Position = UDim2.new(0, 10, 0, 10)
note.Text = "📌 เพิ่มฟีเจอร์อื่นได้ เช่น ยิงออโต้, เปลี่ยนสีวงกลม, รายชื่อผู้เล่น ฯลฯ"
note.TextColor3 = Color3.new(1, 1, 1)
note.BackgroundTransparency = 1
note.Font = Enum.Font.SourceSans
note.TextWrapped = true
note.TextSize = 18


tabButtons[1]:Activate()


local function getClosestTarget()
    local closest, shortest = nil, math.huge
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            if not plr.Name:lower():find(ExcludeName:lower()) then
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


