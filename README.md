-- โหลด Kavo UI
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("💡 ESP UI", "DarkTheme")

-- สร้างหน้า UI
local Tab = Window:NewTab("Main")
local Section = Tab:NewSection("ESP Settings")

-- ตัวแปร
local ESPEnabled = false

-- ปุ่มเปิด/ปิด ESP
Section:NewToggle("เปิด/ปิด ESP", "แสดงชื่อ + ระยะทาง", function(state)
    ESPEnabled = state
end)

-- ⚙️ ระบบ ESP
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local function setupESP(player)
    if player == LocalPlayer then return end

    local function onChar(char)
        if char:FindFirstChild("Head") and not char:FindFirstChild("ESP_NameTag") then
            local tag = Instance.new("BillboardGui", char)
            tag.Name = "ESP_NameTag"
            tag.Adornee = char.Head
            tag.Size = UDim2.new(0, 100, 0, 20)
            tag.StudsOffset = Vector3.new(0, 2.5, 0)
            tag.AlwaysOnTop = true

            local text = Instance.new("TextLabel", tag)
            text.Size = UDim2.new(1, 0, 1, 0)
            text.BackgroundTransparency = 1
            text.TextColor3 = Color3.fromRGB(255, 255, 255)
            text.TextStrokeTransparency = 0.5
            text.TextScaled = true
            text.Font = Enum.Font.SourceSans
            text.Text = ""

            RunService.RenderStepped:Connect(function()
                if ESPEnabled and char:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    local dist = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                    text.Text = player.Name .. " [" .. dist .. "]"
                    tag.Enabled = true
                else
                    tag.Enabled = false
                end
            end)
        end
    end

    if player.Character then onChar(player.Character) end
    player.CharacterAdded:Connect(onChar)
end

for _, p in pairs(Players:GetPlayers()) do
    setupESP(p)
end
Players.PlayerAdded:Connect(setupESP)

-- 📦 ปุ่มพับ/โชว์ UI
task.wait(1) -- รอ Kavo UI โหลด
local kavoUI = nil
for _, gui in pairs(LocalPlayer.PlayerGui:GetChildren()) do
    if gui:FindFirstChild("MainFrame") then
        kavoUI = gui
        break
    end
end

if kavoUI then
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 120, 0, 35)
    btn.Position = UDim2.new(0, 20, 0, 20)
    btn.Text = "📦 พับ UI"
    btn.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextScaled = true
    btn.Font = Enum.Font.SourceSansBold
    btn.ZIndex = 999999
    btn.Parent = kavoUI

    local isFolded = false
    btn.MouseButton1Click:Connect(function()
        isFolded = not isFolded
        kavoUI.MainFrame.Visible = not isFolded
        btn.Text = isFolded and "📤 แสดง UI" or "📦 พับ UI"
    end)

    -- 🖱 ลากหน้าต่าง Kavo UI
    local dragging = false
    local dragInput, dragStart, startPos
    local UIS = game:GetService("UserInputService")

    local frame = kavoUI.MainFrame
    frame.Active = true
    frame.Draggable = false

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    RunService.RenderStepped:Connect(function()
        if dragging and dragInput then
            local delta = dragInput.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end