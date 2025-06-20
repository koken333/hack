local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer


local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = player:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 320, 0, 150)
Frame.Position = UDim2.new(0, 15, 0.8, 0)
Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "ระบบซื้อมอนสเตอร์อัตโนมัติ"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 22
Title.Parent = Frame

local InputBox = Instance.new("TextBox")
InputBox.Size = UDim2.new(1, -20, 0, 40)
InputBox.Position = UDim2.new(0, 10, 0, 40)
InputBox.PlaceholderText = "พิมพ์ชื่อมอนสเตอร์ที่ต้องการซื้อ"
InputBox.ClearTextOnFocus = false
InputBox.Text = ""
InputBox.Parent = Frame

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(1, -20, 0, 50)
ToggleButton.Position = UDim2.new(0, 10, 0, 90)
ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Text = "เปิดระบบซื้ออัตโนมัติ"
ToggleButton.Parent = Frame

local isEnabled = false
local targetName = nil

ToggleButton.MouseButton1Click:Connect(function()
    isEnabled = not isEnabled
    if isEnabled then
        ToggleButton.Text = "ปิดระบบซื้ออัตโนมัติ"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
        targetName = InputBox.Text
        print("ตั้งเป้าหมายซื้อเป็น:", targetName)
    else
        ToggleButton.Text = "เปิดระบบซื้ออัตโนมัติ"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
    end
end)

InputBox.FocusLost:Connect(function()
    if isEnabled then
        targetName = InputBox.Text
        print("เปลี่ยนเป้าหมายซื้อเป็น:", targetName)
    end
end)


local buyEvent = ReplicatedStorage:WaitForChild("BuyMonsterEvent")

local function autoBuy()
    if not isEnabled or not targetName or targetName == "" then return end

    local monstersFolder = workspace:FindFirstChild("Monsters")
    if not monstersFolder then return end

    for _, monster in pairs(monstersFolder:GetChildren()) do
        if monster.Name == targetName then
           
            buyEvent:FireServer(monster)
            print("ซื้อมอนสเตอร์ชื่อ:", monster.Name)
            wait(1) 
        end
    end
end

RunService.Heartbeat:Connect(function()
    autoBuy()
end)


