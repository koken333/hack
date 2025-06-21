local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)

-- UI ลอยเปิด/ปิด ESP
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ESP_UI"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = PlayerGui

local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleESPButton"
toggleButton.Size = UDim2.new(0, 130, 0, 40)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextScaled = true
toggleButton.Text = "🔍 เปิด ESP"
toggleButton.Parent = screenGui

toggleButton.MouseButton1Click:Connect(function()
	getgenv().ESPEnabled = not getgenv().ESPEnabled
	toggleButton.Text = getgenv().ESPEnabled and "❌ ปิด ESP" or "🔍 เปิด ESP"
end)

-- ฟังก์ชันติด ESP
local function createESP(player)
	if player == LocalPlayer then return end

	local function onCharacterAdded(character)
		-- รอจนกว่าจะมีหัว
		local head = character:WaitForChild("Head", 10)
		if not head then return end

		-- ลบของเก่า ถ้ามี
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

-- ติด ESP ให้ทุกคน
for _, p in pairs(Players:GetPlayers()) do
	createESP(p)
end
Players.PlayerAdded:Connect(createESP)






