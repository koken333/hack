local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")

-- ชื่อ NPC ที่เราจะจับ
local colorConfig = {
    Mythic        = Color3.fromRGB(255, 0, 0),
    ["Brainrot God"] = {rainbow = true},
    Secert        = Color3.fromRGB(0, 0, 0),
}

-- ตรวจโครงสร้างว่าคือ NPC ใช่มั้ย
local function isTargetNPC(obj)
    return obj:IsA("Model") and colorConfig[obj.Name]
end

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

    local cfg = colorConfig[npc.Name]
    if cfg.rainbow then
        coroutine.wrap(function()
            while gui.Parent do
                for hue = 0, 1, 0.01 do
                    label.TextColor3 = Color3.fromHSV(hue, 1, 1)
                    RunService.RenderStepped:Wait()
                end
            end
        end)()
    else
        label.TextColor3 = cfg
    end
end

local function removeLabel(npc)
    local root = npc:FindFirstChild("HumanoidRootPart") or npc:FindFirstChildWhichIsA("BasePart")
    if root then
        local gui = root:FindFirstChild("ESPLabel")
        if gui then gui:Destroy() end
    end
end

-- ตรวจจับ NPC spawn/remove
Workspace.DescendantAdded:Connect(function(obj)
    local npc = obj:FindFirstAncestorOfClass("Model")
    if npc and isTargetNPC(npc) then
        addLabel(npc)
    end
end)
Workspace.DescendantRemoving:Connect(function(obj)
    local npc = obj:FindFirstAncestorOfClass("Model")
    if npc and colorConfig[npc.Name] then
        removeLabel(npc)
    end
end)

-- เริ่มต้นสแกนตอนโค้ดโหลด
for _, npc in ipairs(Workspace:GetDescendants()) do
    if isTargetNPC(npc) then addLabel(npc) end
en
