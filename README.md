local UserInputService = game:GetService("UserInputService")

local points = {} -- เก็บจุดที่บันทึก
local recording = false

-- GUI เปิด/ปิดบันทึก
local ScreenGui = Instance.new("ScreenGui", game.Players.LocalPlayer.PlayerGui)
local RecordButton = Instance.new("TextButton")
RecordButton.Size = UDim2.new(0.3, 0, 0.1, 0)
RecordButton.Position = UDim2.new(0.35, 0, 0.85, 0)
RecordButton.Text = "Start Recording"
RecordButton.Parent = ScreenGui

-- สลับโหมดบันทึก
RecordButton.Activated:Connect(function()
    recording = not recording
    RecordButton.Text = recording and "Recording..." or "Start Recording"
    if not recording then
        print("บันทึกเสร็จแล้ว ✅ พิกัดทั้งหมด:")
        for i, p in ipairs(points) do
            print(i, ":", "Vector2.new("..p.X..", "..p.Y..")")
        end
    end
end)

-- ฟังการแตะหน้าจอ
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if recording and input.UserInputType == Enum.UserInputType.Touch then
        table.insert(points, input.Position)
        print("บันทึกจุด:", input.Position)
    end
end)
