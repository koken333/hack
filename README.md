 local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local ESPEnabled = true


local function createToggleButton()
	local screenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
	screenGui.Name = "ESP_Toggle_GUI"

	local button = Instance.new("TextButton", screenGui)
	button.Size = UDim2.new(0, 120, 0, 40)
	button.Position = UDim2.new(0, 20, 0, 100)
	button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
	button.Text = "🔍 ESP: ON"
	button.TextColor3 = Color3.new(1,1,1)
	button.TextScaled = true
	button.Font = Enum.Font.SourceSansBold
	button.BackgroundTransparency = 0.2
	button.BorderSizePixel = 2

	button.MouseButton1Click:Connect(function()
		ESPEnabled = not ESPEnabled
		button.Text = ESPEnabled and "🔍 ESP: ON" or "❌ ESP: OFF"
	end)
end

local function createLabel(char, player)
	if not char:FindFirstChild("Head") or char:FindFirstChild("ESP_Name") then return end

	local bill = Instance.new("BillboardGui")
	bill.Name = "ESP_Name"
	bill.Adornee = char.Head
	bill.Size = UDim2.new(0, 200, 0, 30)
	bill.StudsOffset = Vector3.new(0, 2, 0)
	bill.AlwaysOnTop = true
	bill.Parent = char

	local text = Instance.new("TextLabel")
	text.Size = UDim2.new(1, 0, 1, 0)
	text.BackgroundTransparency = 1
	text.TextColor3 = Color3.fromRGB(255, 255, 255)
	text.TextStrokeTransparency = 0
	text.Font = Enum.Font.SourceSansBold
	text.TextScaled = true
	text.Name = "Label"
	text.Parent = bill

	RunService.RenderStepped:Connect(function()
		if ESPEnabled and char:FindFirstChild("HumanoidRootPart") then
			local dist = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
			text.Text = player.Name .. " [" .. tostring(dist) .. " studs]"
			bill.Enabled = true
		elseif not ESPEnabled then
			bill.Enabled = false
		end
	end)
end


local function applyESP(player)
	if player == LocalPlayer then return end

	local function setup(char)
		if not char:FindFirstChild("ESP_Highlight") then
			local hl = Instance.new("Highlight")
			hl.Name = "ESP_Highlight"
			hl.FillColor = Color3.fromRGB(255, 85, 0)
			hl.FillTransparency = 0.3
			hl.OutlineTransparency = 0
			hl.Adornee = char
			hl.Parent = char

			RunService.RenderStepped:Connect(function()
				hl.Enabled = ESPEnabled
			end)
		end

		createLabel(char, player)
	end

	if player.Character then setup(player.Character) end
	player.CharacterAdded:Connect(setup)
end


createToggleButton()
for _, p in ipairs(Players:GetPlayers()) do
	applyESP(p)
end
Players.PlayerAdded:Connect(function(p)
	applyESP(p)
end)
