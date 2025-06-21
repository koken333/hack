-- โหลด Kavo UI
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("โก๋เคน333", "DarkTheme")
local Tab = Window:NewTab("Players")
local Section = Tab:NewSection("Teleport")

-- ปุ่มลอยเรียก UI กลับ (สำหรับมือถือ)
local FloatGui = Instance.new("ScreenGui", PlayerGui)
FloatGui.Name = "OpenUIButtonGui"
FloatGui.ResetOnSpawn = false
FloatGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local OpenUIButton = Instance.new("TextButton")
OpenUIButton.Name = "OpenUIButton"
OpenUIButton.Text = "💥 เปิด UI"
OpenUIButton.Size = UDim2.new(0, 120, 0, 40)
OpenUIButton.Position = UDim2.new(0, 20, 0, 20)
OpenUIButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
OpenUIButton.TextColor3 = Color3.new(1, 1, 1)
OpenUIButton.Font = Enum.Font.SourceSansBold
OpenUIButton.TextScaled = true
OpenUIButton.Visible = false
OpenUIButton.Parent = FloatGui

-- ปิด UI ด้วยปุ่มในหัว
local MainUI = Window.MainFrame
local header = MainUI:WaitForChild("MainHeader")
local closeBtn = header:FindFirstChild("close")
if closeBtn then
	closeBtn.MouseButton1Click:Connect(function()
		MainUI.Visible = false
		OpenUIButton.Visible = true
	end)
end

OpenUIButton.MouseButton1Click:Connect(function()
	MainUI.Visible = true
	OpenUIButton.Visible = false
end)

-------------------- Teleport --------------------
local players = {}
local Select

local function refreshPlayerList()
    table.clear(players)
    for _, v in ipairs(Players:GetPlayers()) do
        table.insert(players, v.Name)
    end
end

refreshPlayerList()

Section:NewDropdown("Select Player", " ", players, function(text)
    Select = text
end)

Section:NewButton("Refresh", " ", refreshPlayerList)

Section:NewButton("Teleport", " ", function()
    local target = Players:FindFirstChild(Select)
    if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame
    end
end)

-------------------- ESP --------------------
local ESPSection = Tab:NewSection("ESP")
getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)
local RunService = game:GetService("RunService")

ESPSection:NewToggle("Enable ESP", "Toggle ESP", function(state)
    getgenv().ESPEnabled = state
end)

local function setupESP(player)
    if player == LocalPlayer then return end

    local function onChar(char)
        char:WaitForChild("Head")
        if char:FindFirstChild("ESP_Tag") then return end

        local tag = Instance.new("BillboardGui", char)
        tag.Name = "ESP_Tag"
        tag.Adornee = char.Head
        tag.Size = UDim2.new(0, 100, 0, 20)
        tag.StudsOffset = Vector3.new(0, 2.5, 0)
        tag.AlwaysOnTop = true

        local label = Instance.new("TextLabel", tag)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.TextColor3 = ESPColor
        label.TextStrokeTransparency = 0.5
        label.TextScaled = true
        label.Font = Enum.Font.SourceSans
        label.Text = player.Name

        local hl = Instance.new("Highlight", char)
        hl.Name = "ESP_Highlight"
        hl.FillColor = ESPColor
        hl.OutlineColor = Color3.new(1,1,1)
        hl.FillTransparency = 0.5
        hl.OutlineTransparency = 0
        hl.Enabled = false

        RunService.RenderStepped:Connect(function()
            if getgenv().ESPEnabled and char.Parent and char:FindFirstChild("HumanoidRootPart")
               and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local dist = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                label.Text = player.Name .. " [" .. dist .. "m]"
                tag.Enabled = true
                hl.Enabled = true
            else
                tag.Enabled = false
                hl.Enabled = false
            end
        end)
    end

    player.CharacterAdded:Connect(onChar)
    if player.Character then onChar(player.Character) end
end

for _, p in ipairs(Players:GetPlayers()) do setupESP(p) end
Players.PlayerAdded:Connect(setupESP)

-------------------- Aimbot --------------------
local AimbotSection = Tab:NewSection("Aimbot")
getgenv().AimbotEnabled = false
local aimPart = "Head"

AimbotSection:NewToggle("Enable Aimbot", "Lock aim to nearest", function(state)
	getgenv().AimbotEnabled = state
end)

local function getClosestTarget()
	local cam = workspace.CurrentCamera
	local closest, shortest = nil, math.huge

	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild(aimPart) then
			local pos = p.Character[aimPart].Position
			local dist = (cam.CFrame.Position - pos).Magnitude
			if dist < shortest then
				shortest = dist
				closest = p
			end
		end
	end

	return closest
end

RunService.RenderStepped:Connect(function()
	if getgenv().AimbotEnabled then
		local cam = workspace.CurrentCamera
		local target = getClosestTarget()
		if target and target.Character and target.Character:FindFirstChild(aimPart) then
			cam.CFrame = CFrame.new(cam.CFrame.Position, target.Character[aimPart].Position)
		end
	end
end)
