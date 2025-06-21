local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- ====== ESP ======
getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)

-- UI ลอยเปิด/ปิด ESP
local espGui = Instance.new("ScreenGui")
espGui.Name = "ESP_UI"
espGui.ResetOnSpawn = false
espGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
espGui.Parent = PlayerGui

local toggleESPButton = Instance.new("TextButton")
toggleESPButton.Name = "ToggleESPButton"
toggleESPButton.Size = UDim2.new(0, 130, 0, 40)
toggleESPButton.Position = UDim2.new(0, 20, 0, 20)
toggleESPButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleESPButton.TextColor3 = Color3.new(1, 1, 1)
toggleESPButton.Font = Enum.Font.SourceSansBold
toggleESPButton.TextScaled = true
toggleESPButton.Text = "🔍 เปิด ESP"
toggleESPButton.Parent = espGui

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
		tag.Size = UDim2.new(0, 100, 0, 20)
		tag.StudsOffset = Vector3.new(0, 2.5, 0)
		tag.AlwaysOnTop = true
		tag.Parent = character

		local label = Instance.new("TextLabel", tag)
		label.Size = UDim2.new(1, 0, 1, 0)
		label.BackgroundTransparency = 1
		label.TextColor3 = ESPColor
		label.TextStrokeTransparency = 0.5
		label.TextScaled = true
		label.Font = Enum.Font.SourceSans
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
local Camera = workspace.CurrentCamera

local fovRadius = 100
local fovCircle = Drawing.new("Circle")
fovCircle.Color = Color3.new(0, 1, 0)
fovCircle.Thickness = 2
fovCircle.Filled = false
fovCircle.Radius = fovRadius
fovCircle.Visible = false

-- UI ปรับ FOV
local fovGui = Instance.new("ScreenGui")
fovGui.Name = "FOV_UI"
fovGui.ResetOnSpawn = false
fovGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
fovGui.Parent = PlayerGui

local fovLabel = Instance.new("TextLabel")
fovLabel.Size = UDim2.new(0, 140, 0, 30)
fovLabel.Position = UDim2.new(0, 20, 0, 70)
fovLabel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
fovLabel.TextColor3 = Color3.new(1, 1, 1)
fovLabel.Text = "FOV Radius: " .. fovRadius
fovLabel.Font = Enum.Font.SourceSansBold
fovLabel.TextScaled = true
fovLabel.Parent = fovGui

local fovInput = Instance.new("TextBox")
fovInput.Size = UDim2.new(0, 140, 0, 30)
fovInput.Position = UDim2.new(0, 20, 0, 110)
fovInput.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
fovInput.TextColor3 = Color3.new(1, 1, 1)
fovInput.Text = tostring(fovRadius)
fovInput.Font = Enum.Font.SourceSansBold
fovInput.TextScaled = true
fovInput.ClearTextOnFocus = false
fovInput.Parent = fovGui

fovInput.FocusLost:Connect(function(enterPressed)
	if enterPressed then
		local val = tonumber(fovInput.Text)
		if val and val >= 10 and val <= 300 then
			fovRadius = val
			fovCircle.Radius = fovRadius
			fovLabel.Text = "FOV Radius: " .. fovRadius
		else
			fovInput.Text = tostring(fovRadius)
		end
	end
end)

RunService.RenderStepped:Connect(function()
	local viewportSize = Camera.ViewportSize
	fovCircle.Position = Vector2.new(viewportSize.X / 2, viewportSize.Y / 2)
	fovCircle.Visible = getgenv().ESPEnabled -- แสดงเฉพาะตอนเปิด ESP เท่านั้น
	fovCircle.Radius = fovRadius
end)





