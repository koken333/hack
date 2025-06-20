local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")


local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "FlyUI"
gui.ResetOnSpawn = false

local button = Instance.new("TextButton", gui)
button.Size = UDim2.new(0, 140, 0, 60)
button.Position = UDim2.new(1, -160, 1, -180)
button.Text = "🛸 เปิดโหมดบิน"
button.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
button.TextScaled = true
button.Font = Enum.Font.SourceSansBold
button.TextColor3 = Color3.new(1, 1, 1)


local flying = false
local flySpeed = 50
local direction = Vector3.zero


local function updateFly()
	if not character or not character:FindFirstChild("HumanoidRootPart") then return end
	humanoidRootPart.Velocity = direction * flySpeed
end


button.MouseButton1Click:Connect(function()
	flying = not flying
	button.Text = flying and "🛸 ปิดโหมดบิน" or "🛸 เปิดโหมดบิน"
end)


UIS.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Keyboard then
		if input.KeyCode == Enum.KeyCode.W then direction = Vector3.new(0, 0, -1) end
		if input.KeyCode == Enum.KeyCode.S then direction = Vector3.new(0, 0, 1) end
		if input.KeyCode == Enum.KeyCode.A then direction = Vector3.new(-1, 0, 0) end
		if input.KeyCode == Enum.KeyCode.D then direction = Vector3.new(1, 0, 0) end
		if input.KeyCode == Enum.KeyCode.Space then direction = Vector3.new(0, 1, 0) end
		if input.KeyCode == Enum.KeyCode.LeftControl then direction = Vector3.new(0, -1, 0) end
	end
end)

UIS.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Keyboard then
		direction = Vector3.zero
	end
end)


RunService.RenderStepped:Connect(function()
	if flying then
		updateFly()
	end
end)
