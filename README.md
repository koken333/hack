local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Char:WaitForChild("Humanoid")

-- 🖥️ GUI สร้างใน PlayerGui
local screenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
screenGui.Name = "SpeedUI"

-- กล่องใส่ตัวเลขความเร็ว
local textBox = Instance.new("TextBox", screenGui)
textBox.Position = UDim2.new(0, 20, 0, 100)
textBox.Size = UDim2.new(0, 100, 0, 40)
textBox.PlaceholderText = "Speed"
textBox.Text = ""
textBox.TextScaled = true
textBox.Font = Enum.Font.SourceSansBold
textBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
textBox.TextColor3 = Color3.fromRGB(255, 255, 255)
textBox.BorderSizePixel = 2

-- ปุ่มกดตั้งความเร็ว
local button = Instance.new("TextButton", screenGui)
button.Position = UDim2.new(0, 130, 0, 100)
button.Size = UDim2.new(0, 80, 0, 40)
button.Text = "Set"
button.TextScaled = true
button.Font = Enum.Font.SourceSansBold
button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
button.TextColor3 = Color3.fromRGB(255, 255, 255)
button.BorderSizePixel = 2

-- เมื่อกดปุ่ม Set
button.MouseButton1Click:Connect(function()
	local speed = tonumber(textBox.Text)
	if speed and Humanoid then
		Humanoid.WalkSpeed = speed
	end
end)

-- อัปเดต Character ใหม่เมื่อตาย/รี
LocalPlayer.CharacterAdded:Connect(function(character)
	Char = character
	Humanoid = Char:WaitForChild("Humanoid")
end)
