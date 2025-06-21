local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("SHARK X HUB", "DarkTheme")

-- เก็บหน้าต่างหลัก
local MainUI = Window.MainFrame
MainUI.Name = "SHARK_MAIN_UI"

-- ปุ่มลอย (ปุ่มเรียก UI)
local OpenUIButton = Instance.new("TextButton")
OpenUIButton.Name = "OpenUIButton"
OpenUIButton.Text = "🦈 SHARK X"
OpenUIButton.Size = UDim2.new(0, 120, 0, 40)
OpenUIButton.Position = UDim2.new(0, 10, 0, 10) -- มุมบนซ้ายของจอ
OpenUIButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
OpenUIButton.TextColor3 = Color3.new(1, 1, 1)
OpenUIButton.Font = Enum.Font.SourceSansBold
OpenUIButton.TextScaled = true
OpenUIButton.Visible = false
OpenUIButton.Parent = game.CoreGui

-- ปุ่มในหัว UI สำหรับ "ปิด"
local header = MainUI:WaitForChild("MainHeader")
local closeBtn = header:FindFirstChild("close")
if closeBtn then
	closeBtn.MouseButton1Click:Connect(function()
		MainUI.Visible = false
		OpenUIButton.Visible = true -- โชว์ปุ่มลอย
	end)
end

-- คลิกปุ่มลอยเพื่อเปิด UI กลับมา
OpenUIButton.MouseButton1Click:Connect(function()
	MainUI.Visible = true
	OpenUIButton.Visible = false
end)
