-- Item ESP สำหรับ KRNL / Executor
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- ฟิลเตอร์เพื่อเลือกไอเท็ม เช่น ชื่อคลาส หรือโฟลเดอร์
local function isItem(obj)
    return obj:IsA("Part") or obj:IsA("MeshPart") or obj:IsA("UnionOperation")
end

-- สร้าง label ให้ไอเท็ม
local function addLabel(item)
    if item:FindFirstChild("ESPLabel") then return end

    local billboard = Instance.new("BillboardGui")
    billboard.Name = "ESPLabel"
    billboard.Adornee = item
    billboard.Size = UDim2.new(0, 100, 0, 30)
    billboard.AlwaysOnTop = true
    billboard.StudsOffset = Vector3.new(0, 2, 0)
    billboard.Parent = item

    local label = Instance.new("TextLabel", billboard)
    label.BackgroundTransparency = 1
    label.Size = UDim2.new(1, 0, 1, 0)
    label.Text = item.Name
    label.TextColor3 = Color3.new(1, 0.5, 1)
    label.TextStrokeTransparency = 0.5
    label.TextScaled = true
    label.Font = Enum.Font.GothamBold
end

-- ลบ label เมื่อไอเท็มหาย
local function removeLabel(item)
    local billboard = item:FindFirstChild("ESPLabel")
    if billboard then billboard:Destroy() end
end

-- ตรวจจับและทำ ESP เมื่อมีวัตถุใน Workspace แก้ไข
local function scanItems()
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if isItem(obj) then
            addLabel(obj)
        end
    end
end

-- ตรวจจับเมื่อตัวใหม่ spawn / ถูกลบ
Workspace.DescendantAdded:Connect(function(obj)
    if isItem(obj) then
        addLabel(obj)
    end
end)
Workspace.DescendantRemoving:Connect(function(obj)
    if isItem(obj) then
        removeLabel(obj)
    end
end)

-- เรียกสแกนตอนเปิด script
scanItems()
