local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("SHARK X HUB", "DarkTheme")

local Tab = Window:NewTab("Players")
local Section = Tab:NewSection("Teleport")

local players = {}
local SelectPlayer

local function refreshPlayerList()
    table.clear(players)
    for _, v in ipairs(game:GetService("Players"):GetPlayers()) do
        table.insert(players, v.Name)
    end
end
refreshPlayerList()

Section:NewDropdown("Select Player", "Choose someone", players, function(text)
    SelectPlayer = text
end)
Section:NewButton("Refresh", "Update player list", refreshPlayerList)
Section:NewButton("Teleport", "Tp to selected player", function()
    local target = game.Players:FindFirstChild(SelectPlayer)
    if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame
    end
end)

-- สร้างปุ่ม Minimize/Restore ที่มุมขวาบนของ MainFrame
local mainFrame = Window.MainFrame
local header = mainFrame:WaitForChild("MainHeader")

local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Name = "MinimizeButton"
minimizeBtn.Text = "-"
minimizeBtn.Font = Enum.Font.SourceSansBold
minimizeBtn.TextColor3 = Color3.new(1, 1, 1)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
minimizeBtn.BorderSizePixel = 0
minimizeBtn.Size = UDim2.new(0, 30, 0, 30)
minimizeBtn.Position = UDim2.new(1, -35, 0, 5)
minimizeBtn.AnchorPoint = Vector2.new(0, 0)
minimizeBtn.ZIndex = 10
minimizeBtn.Parent = header

local isMinimized = false

minimizeBtn.MouseButton1Click:Connect(function()
    if isMinimized == false then
        -- พับ UI: ซ่อนเนื้อหา ย่อขนาด
        for _, child in ipairs(mainFrame:GetChildren()) do
            if child ~= header and child ~= minimizeBtn then
                child.Visible = false
            end
        end
        mainFrame.Size = UDim2.new(0, 300, 0, 40) -- ย่อขนาดเหลือ header อย่างเดียว
        minimizeBtn.Text = "+"
        isMinimized = true
    else
        -- ขยาย UI: แสดงเนื้อหาและคืนขนาด
        for _, child in ipairs(mainFrame:GetChildren()) do
            if child ~= header and child ~= minimizeBtn then
                child.Visible = true
            end
        end
        mainFrame.Size = UDim2.new(0, 420, 0, 600) -- ขนาดปกติ ปรับตาม UI คุณ
        minimizeBtn.Text = "-"
        isMinimized = false
    end
end)

