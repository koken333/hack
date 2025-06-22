local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

-- กำหนดชื่อไอเท็มที่ต้องการแสดงชื่อ
local targetNames = {
    "Tim Cheese",
    "Brainrot God",
    "Mythic",
    "Secert"
}

-- ฟังก์ชันเช็ควัตถุ
local function isTarget(obj)
    return obj:IsA("Tool") and table.find(targetNames, obj.Name) and obj:FindFirstChild("Handle")
end

-- สร้าง BillboardGui ขนาดคงที่
local function addLabel(obj)
    if obj:FindFirstChild("ESPLabel") then return end

    local handle = obj:FindFirstChild("Handle")
    local gui = Instance.new("BillboardGui")
    gui.Name = "ESPLabel"
    gui.Adornee = handle
    gui.Parent = handle
    gui.AlwaysOnTop = true
    gui.Size = UDim2.new(0, 200, 0, 70)   -- ใหญ่เทียบจอ
    gui.StudsOffset = Vector3.new(0, 3, 0)
    gui.MaxDistance = 300                 -- มองเห็นไกล

    local label = Instance.new("TextLabel", gui)
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = obj.Name
    label.TextScaled = false
    label.Font = Enum.Font.GothamBold
    label.TextSize = 40                    -- ขนาดใหญ่ตามในคลิป
    label.TextStrokeTransparency = 0.4

    -- ปรับสีตามชื่อ
    if obj.Name == "Mythic" then
        label.TextColor3 = Color3.new(1, 0, 0)
    elseif obj.Name == "Brainrot God" then
        coroutine.wrap(function()
            while gui.Parent do
                for h = 0, 1, 0.01 do
                    label.TextColor3 = Color3.fromHSV(h, 1, 1)
                    RunService.RenderStepped:Wait()
                end
            end
        end)()
    elseif obj.Name == "Secert" then
        label.TextColor3 = Color3.new(0, 0, 0)
    else
        label.TextColor3 = Color3.new(1, 1, 1)
    end
end

-- ลบ GUI เมื่อไอเท็มถูกเก็บ / ทำลาย
local function removeLabel(obj)
    local gui = obj:FindFirstChild("Handle") and obj.Handle:FindFirstChild("ESPLabel")
    if gui then gui:Destroy() end
end

-- Hook เมื่อจำลองหรือเพิ่มไอเท็ม
Workspace.DescendantAdded:Connect(function(obj)
    if isTarget(obj) then addLabel(obj) end
end)
Workspace.DescendantRemoving:Connect(function(obj)
    if isTarget(obj) then removeLabel(obj) end
end)

-- สแกนตอนเริ่ม
for _, obj in ipairs(Workspace:GetDescendants()) do
    if isTarget(obj) then addLabel(obj) end
end
