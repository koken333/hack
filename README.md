local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")

-- กำหนดรายชื่อ NPC ที่เกี่ยวข้องกับ Brainrot
local targetNPCs = {
    "Tim Cheese",
    -- เพิ่มชื่อ NPC อื่น ๆ ที่เกี่ยวข้องที่นี่
}

-- ฟังก์ชันสำหรับตรวจสอบว่า NPC เป็นหนึ่งในที่เราต้องการหรือไม่
local function isTargetNPC(npc)
    for _, name in ipairs(targetNPCs) do
        if npc.Name == name then
            return true
        end
    end
    return false
end

-- ฟังก์ชันสำหรับเพิ่ม Label ให้กับ NPC
local function addLabel(npc)
    if npc:FindFirstChild("ESPLabel") then return end
    local root = npc:FindFirstChild("HumanoidRootPart") or npc:FindFirstChildWhichIsA("BasePart")
    if not root then return end

    local gui = Instance.new("BillboardGui")
    gui.Name = "ESPLabel"
    gui.Parent = root
    gui.Adornee = root
    gui.Size = UDim2.new(0, 200, 0, 50)
    gui.StudsOffset = Vector3.new(0, 3, 0)
    gui.AlwaysOnTop = true

    local label = Instance.new("TextLabel", gui)
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = npc.Name
    label.TextScaled = true
    label.Font = Enum.Font.GothamBold
    label.TextStrokeTransparency = 0.4
    label.TextColor3 = Color3.fromRGB(255, 0, 0) -- สีแดงสำหรับ NPC ที่เกี่ยวข้องกับ Brainrot
end

-- ตรวจจับ NPC ที่ถูกเพิ่มเข้ามาใน Workspace
Workspace.DescendantAdded:Connect(function(obj)
    if obj:IsA("Model") and isTargetNPC(obj) then
        addLabel(obj)
    end
end)

-- ตรวจจับ NPC ที่ถูกลบออกจาก Workspace
Workspace.DescendantRemoving:Connect(function(obj)
    if obj:IsA("Model") and isTargetNPC(obj) then
        local gui = obj:FindFirstChild("HumanoidRootPart"):FindFirstChild("ESPLabel")
        if gui then
            gui:Destroy()
        end
    end
end
