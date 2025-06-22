-- LocalScript for item/NPC ESP with colored labels
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- ตารางกำหนดชื่อที่ต้องการ + สี
local colorConfig = {
    Mythic        = Color3.fromRGB(255, 0, 0),
    ["Brainrot God"] = {rainbow = true},
    Secert        = Color3.fromRGB(0, 0, 0),
}

-- สร้าง BillboardGui และ Label ให้กับวัตถุตรงเงื่อนไข
local function addLabel(obj)
    local name = obj.Name
    if not colorConfig[name] then return end
    if obj:FindFirstChild("ESPLabel") then return end

    local root = obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChildWhichIsA("BasePart")
    if not root then return end

    local gui = Instance.new("BillboardGui", obj)
    gui.Name = "ESPLabel"
    gui.Adornee = root
    gui.Size = UDim2.new(0, 120, 0, 30)
    gui.StudsOffset = Vector3.new(0, 2.5, 0)
    gui.AlwaysOnTop = true

    local label = Instance.new("TextLabel", gui)
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = name
    label.Font = Enum.Font.GothamBold
    label.TextScaled = true
    label.TextStrokeTransparency = 0.4

    local cfg = colorConfig[name]
    if cfg.rainbow then
        -- ไล่เฉดสีรุ้ง
        label.TextColor3 = Color3.new(1, 0, 0)
        -- Tween ไล่สีเรนโบว์
        coroutine.wrap(function()
            while label.Parent and cfg.rainbow do
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

-- ลบ label เมื่อตัวหาย
local function removeLabel(obj)
    local gui = obj:FindFirstChild("ESPLabel")
    if gui then gui:Destroy() end
end

-- ตรวจจับวัตถุที่ spawn หรือหาย
Workspace.DescendantAdded:Connect(function(obj)
    addLabel(obj)
end)
Workspace.DescendantRemoving:Connect(function(obj)
    removeLabel(obj)
end)

-- เรียกเริ่มต้น
for _, obj in ipairs(Workspace:GetDescendants()) do
    addLabel(obj)
end
