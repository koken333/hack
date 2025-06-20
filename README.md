
local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "FireGUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true


local fireButton = Instance.new("TextButton")
fireButton.Size = UDim2.new(0, 120, 0, 120)
fireButton.Position = UDim2.new(1, -140, 1, -160) -- มุมขวาล่าง
fireButton.Text = "ยิง!!"
fireButton.BackgroundColor3 = Color3.fromRGB(255, 80, 80)
fireButton.TextColor3 = Color3.new(1, 1, 1)
fireButton.TextScaled = true
fireButton.Font = Enum.Font.SourceSansBold
fireButton.Parent = gui


local function shoot()
    print("🔫 ยิงไปแล้ว!") 
    -- ตัวอย่าง: game.ReplicatedStorage.Shoot:FireServer()
end


local isFiring = false
fireButton.MouseButton1Down:Connect(function()
    isFiring = true
    while isFiring do
        shoot()
        task.wait(0.05) -- ความเร็วในการยิง
    end
end)

fireButton.MouseButton1Up:Connect(function()
    isFiring = false
end)


fireButton.TouchTap:Connect(function()
    isFiring = true
    while isFiring do
        shoot()
        task.wait(0.05)
    end
end)

UserInputService.TouchEnded:Connect(function()
    isFiring = false
end)

