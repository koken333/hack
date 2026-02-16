local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("My Study Hub", "BloodTheme")

-- SERVICE & VARIABLES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local camera = workspace.CurrentCamera
local localPlayer = Players.LocalPlayer

-- Global Table สำหรับเก็บค่าคอนฟิก
_G.MyAimbotSettings = {
    AimbotEnabled = false,
    TargetPart = "Head",
    Smoothness = 0.15,
    FOV = 120,
    ESPEnabled = true,
}

local CurrentSettings = _G.MyAimbotSettings

-- DRAWING FOV (วงกลม)
local fovCircle = Drawing.new("Circle")
fovCircle.Visible = true
fovCircle.Thickness = 1
fovCircle.Radius = CurrentSettings.FOV
fovCircle.Color = Color3.fromRGB(255, 0, 0)

-- ESP STORAGE
local playerESPDrawings = {}

-- ฟังก์ชันจัดการ ESP (เหมือนเดิม)
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
local AimbotSection = MainTab:NewSection("Aimbot (E = Toggle | F = Switch)")
local ESPSection = MainTab:NewSection("ESP Settings")

-- ปุ่ม Toggle ใน UI
local aimbotToggleUI = AimbotSection:NewToggle("Enable Aimbot", "Locks onto targets", function(state)
    CurrentSettings.AimbotEnabled = state
end)

-- Dropdown ใน UI
local targetDropdownUI = AimbotSection:NewDropdown("Target Part", "Select where to lock", {"Head", "HumanoidRootPart"}, function(currentOption)
    CurrentSettings.TargetPart = currentOption
end)

AimbotSection:NewSlider("Smoothness", "Speed", 100, 1, function(s) CurrentSettings.Smoothness = s / 100 end)
AimbotSection:NewSlider("FOV Radius", "Circle size", 500, 50, function(s) CurrentSettings.FOV = s end)
ESPSection:NewToggle("Enable ESP", "Show Names", function(state) CurrentSettings.ESPEnabled = state end)

-- ====== KEYBIND LOGIC (E และ F) ======
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end -- ไม่ทำงานถ้ากำลังพิมพ์แชท

    -- กด E เพื่อเปิด/ปิด Aimbot
    if input.KeyCode == Enum.KeyCode.E then
        CurrentSettings.AimbotEnabled = not CurrentSettings.AimbotEnabled
        -- อัปเดตสถานะใน UI ให้ตรงกับที่เรากดปุ่ม (Optional)
        Library:Notify("Aimbot: " .. (CurrentSettings.AimbotEnabled and "ON" or "OFF"))
    end

    -- กด F เพื่อสลับหัว/ตัว
    if input.KeyCode == Enum.KeyCode.F then
        if CurrentSettings.TargetPart == "Head" then
            CurrentSettings.TargetPart = "HumanoidRootPart"
        else
            CurrentSettings.TargetPart = "Head"
        end
        Library:Notify("Targeting: " .. CurrentSettings.TargetPart)
    end
end)

-- ====== MAIN LOOP ======
RunService.RenderStepped:Connect(function()
    fovCircle.Position = UserInputService:GetMouseLocation()
    fovCircle.Radius = CurrentSettings.FOV
    fovCircle.Visible = CurrentSettings.AimbotEnabled
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
