-- โหลด Kavo UI
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local RunService = game:GetService("RunService")

local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("โก๋เคน333", "DarkTheme")

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

-----------------------------------------
-- Tab 1: Teleport
local TeleportTab = Window:NewTab("Teleport")
local TeleportSection = TeleportTab:NewSection("Player Teleport")

local players = {}
local selectedPlayer

local function refreshPlayerList()
	table.clear(players)
	for _, v in ipairs(Players:GetPlayers()) do
		table.insert(players, v.Name)
	end
end

refreshPlayerList()

TeleportSection:NewDropdown("Select Player", "Choose a player", players, function(text)
	selectedPlayer = text
end)

TeleportSection:NewButton("Refresh Player List", "Update player list", function()
	refreshPlayerList()
end)

TeleportSection:NewButton("Teleport", "Teleport to selected player", function()
	local target = Players:FindFirstChild(selectedPlayer)
	if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
		LocalPlayer.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame
	end
end)

-----------------------------------------
-- Tab 2: ESP
local ESPTab = Window:NewTab("ESP")
local ESPSection = ESPTab:NewSection("ESP Settings")

getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)

ESPSection:NewToggle("Enable ESP", "Toggle ESP on/off", function(state)
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

for _, p in ipairs(Players:GetPlayers()) do
	setupESP(p)
end
Players.PlayerAdded:Connect(setupESP)

-----------------------------------------
-- Tab 3: Aimbot
local AimbotTab = Window:NewTab("Aimbot")
local AimbotSection = AimbotTab:NewSection("Aimbot Settings")

getgenv().AimbotEnabled = false
local aimPart = "Head"
local fovRadius = 50

AimbotSection:NewToggle("Enable Aimbot", "Toggle aimbot on/off", function(state)
	getgenv().AimbotEnabled = state
end)

local fovCircle = Drawing.new("Circle")
fovCircle.Radius = fovRadius
fovCircle.Color = Color3.new(1, 0, 0)
fovCircle.Thickness = 1
fovCircle.Filled = false
fovCircle.Visible = false

AimbotSection:NewSlider("FOV Radius", "Adjust Aimbot FOV size", 300, 10, fovRadius, function(value)
	fovRadius = value
	fovCircle.Radius = fovRadius
end)

RunService.RenderStepped:Connect(function()
	local cam = workspace.CurrentCamera
	local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
	fovCircle.Position = center
	fovCircle.Visible = getgenv().AimbotEnabled

	if getgenv().AimbotEnabled then
		local closest, shortest = nil, math.huge

		for _, p in pairs(Players:GetPlayers()) do
			if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild(aimPart) then
				local pos, onScreen = cam:WorldToViewportPoint(p.Character[aimPart].Position)
				if onScreen then
					local dist = (Vector2.new(pos.X, pos.Y) - center).Magnitude
					if dist < shortest and dist < fovRadius then
						shortest = dist
						closest = p
					end
				end
			end
		end

		if closest and closest.Character and closest.Character:FindFirstChild(aimPart) then
			cam.CFrame = CFrame.new(cam.CFrame.Position, closest.Character[aimPart].Position)
		end
	end
end)





