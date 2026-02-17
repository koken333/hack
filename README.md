local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("My Study Hub V4", "BloodTheme")

-- SERVICE & VARIABLES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local camera = workspace.CurrentCamera
local localPlayer = Players.LocalPlayer

-- Global Table
_G.MyAimbotSettings = {
    AimbotEnabled = false,
    TargetPart = "Head",
    Smoothness = 0.15,
    FOV = 120,
    FOVVisible = true,
    ESPEnabled = true,
}
local CurrentSettings = _G.MyAimbotSettings

-- DRAWING FOV
local fovCircle = Drawing.new("Circle")
fovCircle.Visible = false
fovCircle.Thickness = 1
fovCircle.Radius = CurrentSettings.FOV
fovCircle.Color = Color3.fromRGB(255, 0, 0)

-- ESP STORAGE
local playerESPDrawings = {}

-- [[ ฟังก์ชันล้างชื่อเมื่อผู้เล่นออก (แก้บั๊กชื่อค้าง) ]] --
local function removeESP(userId)
    if playerESPDrawings[userId] then
        playerESPDrawings[userId].Visible = false
        playerESPDrawings[userId]:Remove()
        playerESPDrawings[userId] = nil
    end
end

Players.PlayerRemoving:Connect(function(player)
    removeESP(player.UserId)
end)

-- ฟังก์ชันจัดการ ESP
local function updatePlayerESP(player)
    if player == localPlayer then return end
    
    local espDrawing = playerESPDrawings[player.UserId]
    if not espDrawing then
        espDrawing = Drawing.new("Text")
        espDrawing.Size = 16
        espDrawing.Center = true
        espDrawing.Outline = true
        espDrawing.Visible = false
        playerESPDrawings[player.UserId] = espDrawing
    end

    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")

    if CurrentSettings.ESPEnabled and hrp and hum and hum.Health > 0 then
        local pos, onscreen = camera:WorldToViewportPoint(hrp.Position)
        if onscreen then
            espDrawing.Position = Vector2.new(pos.X, pos.Y - 30)
            espDrawing.Text = player.Name
            espDrawing.Visible = true
            espDrawing.Color = CurrentSettings.AimbotEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
        else
            espDrawing.Visible = false
        end
    else
        espDrawing.Visible = false
    end
end

-- ฟังก์ชันหาเป้าหมาย
local function getClosestTarget()
    local closestPart = nil
    local shortestDistance = CurrentSettings.FOV
    local mouseLoc = UserInputService:GetMouseLocation()

    for _, p in pairs(Players:GetPlayers()) do
        if p ~= localPlayer and p.Character then
            local part = p.Character:FindFirstChild(CurrentSettings.TargetPart)
            local hum = p.Character:FindFirstChild("Humanoid")

            if part and hum and hum.Health > 0 then
                local screenPos, onScreen = camera:WorldToViewportPoint(part.Position)
                if onScreen then
                    local dist = (Vector2.new(screenPos.X, screenPos.Y) - mouseLoc).Magnitude
                    if dist < shortestDistance then
                        shortestDistance = dist
                        closestPart = part
                    end
                end
            end
        end
    end
    return closestPart
end

-- ====== UI SETUP ======
local MainTab = Window:NewTab("Main")
local VisualTab = Window:NewTab("Visuals")
local SettingTab = Window:NewTab("Settings")

local AimbotSection = MainTab:NewSection("Aimbot Control")
AimbotSection:NewToggle("Enable Aimbot", "Locks onto targets", function(state) CurrentSettings.AimbotEnabled = state end)
AimbotSection:NewSlider("Smoothness", "Speed", 100, 1, function(s) CurrentSettings.Smoothness = s / 100 end)
AimbotSection:NewSlider("FOV Radius", "Circle size", 500, 50, function(s) CurrentSettings.FOV = s end)

local ESPSection = VisualTab:NewSection("ESP Settings")
ESPSection:NewToggle("Enable ESP", "Show Names", function(state) CurrentSettings.ESPEnabled = state end)
ESPSection:NewToggle("Show FOV Circle", "Toggle Visibility", function(state) CurrentSettings.FOVVisible = state end)

local KeySection = SettingTab:NewSection("Keybinds")
KeySection:NewKeybind("Hide/Show UI", "RightControl to Toggle", Enum.KeyCode.RightControl, function() Library:ToggleLib() end)

-- ====== [[ ระบบปุ่มเปิด/ปิดหน้าจอ (Floating Button) ]] ======
local OpenGui = Instance.new("ScreenGui")
local OpenButton = Instance.new("TextButton")

OpenGui.Name = "OpenGui"
OpenGui.Parent = game:GetService("CoreGui")
OpenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

OpenButton.Name = "OpenButton"
OpenButton.Parent = OpenGui
OpenButton.BackgroundColor3 = Color3.fromRGB(150, 0, 0) -- สีเลือดแบบ Kavo
OpenButton.Position = UDim2.new(0, 10, 0, 400)
OpenButton.Size = UDim2.new(0, 80, 0, 30)
OpenButton.Font = Enum.Font.SourceSansBold
OpenButton.Text = "OPEN / HIDE"
OpenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenButton.TextSize = 14
OpenButton.Draggable = true -- มึงลากปุ่มไปวางตรงไหนก็ได้

OpenButton.MouseButton1Click:Connect(function()
    Library:ToggleLib()
end)

-- ====== MAIN LOOP ======
RunService.RenderStepped:Connect(function()
    fovCircle.Position = UserInputService:GetMouseLocation()
    fovCircle.Radius = CurrentSettings.FOV
    fovCircle.Visible = CurrentSettings.AimbotEnabled and CurrentSettings.FOVVisible
    fovCircle.Color = CurrentSettings.AimbotEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)

    if CurrentSettings.AimbotEnabled then
        local target = getClosestTarget()
        if target then
            camera.CFrame = camera.CFrame:Lerp(CFrame.lookAt(camera.CFrame.Position, target.Position), CurrentSettings.Smoothness)
        end
    end

    for _, player in pairs(Players:GetPlayers()) do
        updatePlayerESP(player)
    end
end)
