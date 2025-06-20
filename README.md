

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local mouse = player:GetMouse()


local ScreenGui = Instance.new("ScreenGui", player.PlayerGui)
local Frame = Instance.new("Frame", ScreenGui)
Frame.Position = UDim2.new(0, 10, 0.7, 0)
Frame.Size = UDim2.new(0, 250, 0, 150)
Frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Frame.Active = true
Frame.Draggable = true

local ToggleButton = Instance.new("TextButton", Frame)
ToggleButton.Size = UDim2.new(0, 230, 0, 40)
ToggleButton.Position = UDim2.new(0, 10, 0, 10)
ToggleButton.Text = "เปิดระบบซื้อออโต้"
ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
ToggleButton.TextColor3 = Color3.new(1,1,1)

local TargetLabel = Instance.new("TextLabel", Frame)
TargetLabel.Position = UDim2.new(0, 10, 0, 60)
TargetLabel.Size = UDim2.new(0, 230, 0, 20)
TargetLabel.Text = "เลือก Monster Target: None"
TargetLabel.TextColor3 = Color3.new(1,1,1)
TargetLabel.BackgroundTransparency = 1

local InputBox = Instance.new("TextBox", Frame)
InputBox.Position = UDim2.new(0, 10, 0, 85)
InputBox.Size = UDim2.new(0, 230, 0, 30)
InputBox.PlaceholderText = "พิมพ์ชื่อมอนสเตอร์ที่ต้องการ"
InputBox.Text = ""


local isEnabled = false
local targetName = nil

ToggleButton.MouseButton1Click:Connect(function()
    isEnabled = not isEnabled
    if isEnabled then
        ToggleButton.Text = "ปิดระบบซื้อออโต้"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
    else
        ToggleButton.Text = "เปิดระบบซื้อออโต้"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
    end
end)

InputBox.FocusLost:Connect(function()
    targetName = InputBox.Text
    if targetName == "" then
        targetName = nil
    end
    TargetLabel.Text = "เลือก Monster Target: " .. (targetName or "None")
end)


local function findAndBuy()
    if not isEnabled or not targetName then return end
    
 
    local monstersFolder = workspace:FindFirstChild("Monsters")
    if not monstersFolder then return end
    
    for _, monster in pairs(monstersFolder:GetChildren()) do
     
        if monster.Name == targetName then
       
            local head = monster:FindFirstChild("Head")
            if head then
                local billboard = head:FindFirstChild("BillboardGui")
                if billboard then
                    local amountLabel = billboard:FindFirstChild("Amount")
                    if amountLabel and tonumber(amountLabel.Text) then
                        local price = tonumber(amountLabel.Text)
                        
                       
                        local char = player.Character
                        if char and char:FindFirstChild("HumanoidRootPart") then
                            local dist = (char.HumanoidRootPart.Position - monster.HumanoidRootPart.Position).Magnitude
                            if dist <= 100 then 
                              
                                local buyEvent = game.ReplicatedStorage:FindFirstChild("BuyMonsterEvent")
                                if buyEvent and buyEvent:IsA("RemoteEvent") then
                                    buyEvent:FireServer(monster)
                                    print("ซื้อ monster:", monster.Name, "ราคา:", price)
                                end
                            else
                                
                                 player.Character.Humanoid:MoveTo(monster.HumanoidRootPart.Position)
                            end
                        end
                    end
                end
            end
        end
    end
end


RunService.Heartbeat:Connect(function(step)
    findAndBuy()
end)

