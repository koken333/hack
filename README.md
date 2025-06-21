local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- โหลด Kavo UI
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("SHARK X HUB", "DarkTheme")

-- เก็บหน้าต่างหลัก
local MainUI = Window.MainFrame
MainUI.Name = "SHARK_MAIN_UI"

-- ปุ่มลอยเรียก UI (ใช้ ScreenGui สำหรับมือถือ)
local FloatGui = Instance.new("ScreenGui", PlayerGui)
FloatGui.Name = "OpenUIButtonGui"
FloatGui.ResetOnSpawn = false
FloatGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local OpenUIButton = Instance.new("TextButton")
OpenUIButton.Name = "OpenUIButton"
OpenUIButton.Text = "🦈 เปิด UI"
OpenUIButton.Size = UDim2.new(0, 120, 0, 40)
OpenUIButton.Position = UDim2.new(0, 20, 0, 20) -- มุมบนซ้ายของจอ
OpenUIButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
OpenUIButton.TextColor3 = Color3.new(1, 1, 1)
OpenUIButton.Font = Enum.Font.SourceSansBold
OpenUIButton.TextScaled = true
OpenUIButton.Visible = false
OpenUIButton.Parent = FloatGui

-- ปิด UI ด้วยปุ่มในหัว
local header = MainUI:WaitForChild("MainHeader")
local closeBtn = header:FindFirstChild("close")
if closeBtn then
	closeBtn.MouseButton1Click:Connect(function()
		MainUI.Visible = false
		OpenUIButton.Visible = true -- แสดงปุ่มเปิดใหม่
	end)
end

-- เปิด UI กลับมา
OpenUIButton.MouseButton1Click:Connect(function()
	MainUI.Visible = true
	OpenUIButton.Visible = false
end)
