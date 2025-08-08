----// GUI Macro Anime Vanguards (Mobile AFK)
local UIS = game:GetService("UserInputService")
local VIM = game:GetService("VirtualInputManager")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "MacroUI"

local frame = Instance.new("Frame", gui)
frame.Position = UDim2.new(0.3, 0, 0.3, 0)
frame.Size = UDim2.new(0, 200, 0, 250)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0

local function createButton(text, yPos)
	local btn = Instance.new("TextButton", frame)
	btn.Text = text
	btn.Size = UDim2.new(0, 180, 0, 30)
	btn.Position = UDim2.new(0, 10, 0, yPos)
	btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = Enum.Font.SourceSans
	btn.TextSize = 20
	return btn
end

local startBtn = createButton("Start Macro", 10)
local stopBtn = createButton("Stop Macro", 50)
local recordBtn = createButton("Record", 90)
local saveBtn = createButton("Save", 130)
local loadBtn = createButton("Load", 170)

local inputBox = Instance.new("TextBox", frame)
inputBox.PlaceholderText = "ใส่ชื่อแมคโคร..."
inputBox.Size = UDim2.new(0, 180, 0, 30)
inputBox.Position = UDim2.new(0, 10, 0, 210)
inputBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
inputBox.TextColor3 = Color3.new(1,1,1)
inputBox.Font = Enum.Font.SourceSans
inputBox.TextSize = 18

--// ระบบมาโคร
local isRunning = false
local macroData = {}

recordBtn.MouseButton1Click:Connect(function()
	macroData = {}
	UIS.InputBegan:Connect(function(input, gp)
		if not gp and input.UserInputType == Enum.UserInputType.Keyboard then
			table.insert(macroData, {key = input.KeyCode, time = tick()})
		end
	end)
end)

startBtn.MouseButton1Click:Connect(function()
	if #macroData == 0 then return end
	isRunning = true
	local lastTime = macroData[1].time
	for i, v in ipairs(macroData) do
		if not isRunning then break end
		task.wait(v.time - lastTime)
		VIM:SendKeyEvent(true, v.key, false, game)
		VIM:SendKeyEvent(false, v.key, false, game)
		lastTime = v.time
	end
end)

stopBtn.MouseButton1Click:Connect(function()
	isRunning = false
end)

saveBtn.MouseButton1Click:Connect(function()
	if inputBox.Text == "" then return end
	writefile(inputBox.Text..".json", game:GetService("HttpService"):JSONEncode(macroData))
end)

loadBtn.MouseButton1Click:Connect(function()
	if inputBox.Text == "" then return end
	local data = readfile(inputBox.Text..".json")
	macroData = game:GetService("HttpService"):JSONDecode(data)
end)
