-- 🔧 Dependencies
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- 🖥️ สร้าง GUI
local screenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 200, 0, 300)
frame.Position = UDim2.new(0, 10, 0, 10)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)

local uiList = Instance.new("UIListLayout", frame)
uiList.SortOrder = Enum.SortOrder.LayoutOrder
uiList.Padding = UDim.new(0, 4)

-- 🎯 ฟังก์ชัน refresh รายชื่อผู้เล่น
local function refreshPlayers()
    for _, child in ipairs(frame:GetChildren()) do
        if child:IsA("TextButton") then child:Destroy() end
    end
    for _, pl in ipairs(Players:GetPlayers()) do
        if pl ~= LocalPlayer and pl.Character and pl.Character:FindFirstChild("HumanoidRootPart") then
            local btn = Instance.new("TextButton")
            btn.Name = pl.Name .. "_btn"
            btn.Size = UDim2.new(1, -8, 0, 30)
            btn.Text = pl.Name
            btn.Parent = frame
            btn.MouseButton1Click:Connect(function()
                local target = pl.Character:FindFirstChild("HumanoidRootPart")
                if target and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    LocalPlayer.Character.HumanoidRootPart.CFrame = target.CFrame * CFrame.new(2, 0, 0)
                end
            end)
        end
    end
end

-- 🔄 รีเฟรชเมื่อผู้เล่นเข้า–ออก
Players.PlayerAdded:Connect(refreshPlayers)
Players.PlayerRemoving:Connect(refreshPlayers)
refreshPlayers()
