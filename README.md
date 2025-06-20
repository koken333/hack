local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local function createLabel(char, player)
	if char:FindFirstChild("Head") and not char:FindFirstChild("ESP_Name") then
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
			if char and char:FindFirstChild("Head") and char:FindFirstChild("HumanoidRootPart") then
				local distance = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
				text.Text = player.Name .. " [" .. tostring(distance) .. " studs]"
			end
		end)
	end
end


local function applyESP(player)
	if player == LocalPlayer then return end

	local function setup(char)
		-- Highlight
		if not char:FindFirstChild("ESP_Highlight") then
			local highlight = Instance.new("Highlight")
			highlight.Name = "ESP_Highlight"
			highlight.FillColor = Color3.fromRGB(255, 85, 0)
			highlight.FillTransparency = 0.3
			highlight.OutlineTransparency = 0
			highlight.Adornee = char
			highlight.Parent = char
		end

		-- Label
		createLabel(char, player)
	end

	if player.Character then setup(player.Character) end
	player.CharacterAdded:Connect(setup)
end


for _, p in ipairs(Players:GetPlayers()) do
	applyESP(p)
end

-- ติด ESP ให้ผู้เล่นใหม่
Players.PlayerAdded:Connect(function(p)
	applyESP(p)
end)
