local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local Camera = workspace.CurrentCamera

-- ฟังก์ชันสร้างปุ่มสวยๆ
local function createButton(parent, name, text, posY)
	local cam = Camera
	local closest, shortest = nil, math.huge

	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer 
			and p.Character 
			and p.Character:FindFirstChild(aimPart) 
			and not LocalPlayer:IsFriendsWith(p.UserId) then

			local pos, onScreen = cam:WorldToViewportPoint(p.Character[aimPart].Position)
			if onScreen then
				local distToCenter = (Vector2.new(pos.X, pos.Y) - Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)).Magnitude
				if distToCenter < shortest and distToCenter < fovRadius then
					shortest = distToCenter
					closest = p
				end
			end
		end
	end

	return closest
end
local function createLabel(parent, text, posY)
	local lbl = Instance.new("TextLabel")
	lbl.Size = UDim2.new(0, 150, 0, 30)
	lbl.Position = UDim2.new(0, 20, 0, posY)
	lbl.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
	lbl.BorderSizePixel = 0
	lbl.TextColor3 = Color3.new(1, 1, 1)
	lbl.Font = Enum.Font.GothamSemibold
	lbl.TextScaled = true
	lbl.Text = text
	lbl.Parent = parent
	return lbl
end

local function createTextbox(parent, defaultText, posY)
	local tb = Instance.new("TextBox")
	tb.Size = UDim2.new(0, 150, 0, 40)
	tb.Position = UDim2.new(0, 20, 0, posY)
	tb.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
	tb.BorderSizePixel = 0
	tb.TextColor3 = Color3.new(1, 1, 1)
	tb.Font = Enum.Font.GothamSemibold
	tb.TextScaled = true
	tb.ClearTextOnFocus = false
	tb.Text = tostring(defaultText)
	tb.Parent = parent
	return tb
end

-- ====== ESP ======
getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)

local espGui = Instance.new("ScreenGui")
espGui.Name = "ESP_UI"
espGui.ResetOnSpawn = false
espGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
espGui.Parent = PlayerGui

local toggleESPButton = createButton(espGui, "ToggleESPButton", "🔍 เปิด ESP", 20)
toggleESPButton.MouseButton1Click:Connect(function()
	getgenv().ESPEnabled = not getgenv().ESPEnabled
	toggleESPButton.Text = getgenv().ESPEnabled and "❌ ปิด ESP" or "🔍 เปิด ESP"
end)

local function createESP(player)
	if player == LocalPlayer then return end

	local function onCharacterAdded(character)
		local head = character:WaitForChild("Head", 10)
		if not head then return end

		local oldTag = character:FindFirstChild("ESP_Tag")
		if oldTag then oldTag:Destroy() end

		local tag = Instance.new("BillboardGui")
		tag.Name = "ESP_Tag"
		tag.Adornee = head
		tag.Size = UDim2.new(0, 120, 0, 25)
		tag.StudsOffset = Vector3.new(0, 2.7, 0)
		tag.AlwaysOnTop = true
		tag.Parent = character

		local label = Instance.new("TextLabel", tag)
		label.Size = UDim2.new(1, 0, 1, 0)
		label.BackgroundTransparency = 1
		label.TextColor3 = ESPColor
		label.TextStrokeTransparency = 0.4
		label.TextScaled = true
		label.Font = Enum.Font.GothamSemibold
		label.Text = player.Name

		RunService.RenderStepped:Connect(function()
			if getgenv().ESPEnabled and character.Parent and character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				local dist = (character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
				label.Text = player.Name .. " [" .. math.floor(dist) .. "m]"
				tag.Enabled = true
			else
				tag.Enabled = false
			end
		end)
	end

	player.CharacterAdded:Connect(onCharacterAdded)
	if player.Character then
		onCharacterAdded(player.Character)
	end
end

for _, p in pairs(Players:GetPlayers()) do
	createESP(p)
end
Players.PlayerAdded:Connect(createESP)

-- ====== FOV ======
local fovRadius = 100
local fovCircle = Drawing.new("Circle")
fovCircle.Color = Color3.new(0, 1, 0)
fovCircle.Thickness = 2
fovCircle.Filled = false
fovCircle.Radius = fovRadius
fovCircle.Visible = false

local fovGui = Instance.new("ScreenGui")
fovGui.Name = "FOV_UI"
fovGui.ResetOnSpawn = false
fovGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
fovGui.Parent = PlayerGui

local toggleFOVButton = createButton(fovGui, "ToggleFOVButton", "👁️ เปิด FOV", 75)

local fovLabel = createLabel(fovGui, "FOV Radius: " .. fovRadius, 125)
local fovInput = createTextbox(fovGui, fovRadius, 165)

toggleFOVButton.MouseButton1Click:Connect(function()
	fovCircle.Visible = not fovCircle.Visible
	toggleFOVButton.Text = fovCircle.Visible and "❌ ปิด FOV" or "👁️ เปิด FOV"
end)

fovInput.FocusLost:Connect(function(enterPressed)
	if enterPressed then
		local val = tonumber(fovInput.Text)
		if val and val >= 0 and val <= 500 then
			fovRadius = val
			if fovCircle then
				fovCircle:Remove()
			end
			fovCircle = Drawing.new("Circle")
			fovCircle.Color = Color3.new(0, 1, 0)
			fovCircle.Thickness = 2
			fovCircle.Filled = false
			fovCircle.Radius = fovRadius
			fovCircle.Visible = toggleFOVButton.Text == "❌ ปิด FOV"
			fovLabel.Text = "FOV Radius: " .. fovRadius
		else
			fovInput.Text = tostring(fovRadius)
		end
	end
end)

RunService.RenderStepped:Connect(function()
	local viewportSize = Camera.ViewportSize
	fovCircle.Position = Vector2.new(viewportSize.X / 2, viewportSize.Y / 2)
	fovCircle.Radius = fovRadius
end)

-- ====== AIMBOT ======
getgenv().AimbotEnabled = false
local aimPart = "Head"

local aimbotGui = Instance.new("ScreenGui")
aimbotGui.Name = "Aimbot_UI"
aimbotGui.ResetOnSpawn = false
aimbotGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
aimbotGui.Parent = PlayerGui

local toggleAimbotButton = createButton(aimbotGui, "ToggleAimbotButton", "🎯 เปิด Aimbot", 210)
toggleAimbotButton.MouseButton1Click:Connect(function()
	getgenv().AimbotEnabled = not getgenv().AimbotEnabled
	toggleAimbotButton.Text = getgenv().AimbotEnabled and "❌ ปิด Aimbot" or "🎯 เปิด Aimbot"
end)

local function getClosestTarget()
	local cam = Camera
	local closest, shortest = nil, math.huge

	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild(aimPart) then
			local pos, onScreen = cam:WorldToViewportPoint(p.Character[aimPart].Position)
			if onScreen then
				local distToCenter = (Vector2.new(pos.X, pos.Y) - Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)).Magnitude
				if distToCenter < shortest and distToCenter < fovRadius then
					shortest = distToCenter
					closest = p
				end
			end
		end
	end

	return closest
end

RunService.RenderStepped:Connect(function()
	if getgenv().AimbotEnabled then
		local target = getClosestTarget()
		if target and target.Character and target.Character:FindFirstChild(aimPart) then
			Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character[aimPart].Position)
		end
	end
end)

