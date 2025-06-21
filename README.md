local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)

-- สร้าง GUI ลอยหน้าจอ
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "ESP_UI"
gui.ResetOnSpawn = false

local toggleButton = Instance.new("TextButton", gui)
toggleButton.Size = UDim2.new(0, 120, 0, 40)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.TextScaled = true
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.Text = "ESP: OFF"
toggleButton.ZIndex = 9999

-- เมื่อกดปุ่ม
toggleButton.MouseButton1Click:Connect(function()
    ESPEnabled = not ESPEnabled
    toggleButton.Text = ESPEnabled and "ESP: ON" or "ESP: OFF"
end)

-- ระบบ ESP + Highlight
local function setupESP(player)
    if player == LocalPlayer then return end

    local function onChar(char)
        if not char:FindFirstChild("Head") or char:FindFirstChild("ESP_Tag") then return end

        -- Billboard GUI
        local tag = Instance.new("BillboardGui", char)
        tag.Name = "ESP_Tag"
        tag.Adornee = char.Head
        tag.Size = UDim2.new(0, 100, 0, 20)
        tag.StudsOffset = Vector3.new(0, 2.5, 0)
        tag.AlwaysOnTop = true

        local label = Instance.new("TextLabel", tag)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.TextColor3 = ESPColor
        label.TextStrokeTransparency = 0.5
        label.TextScaled = true
        label.Font = Enum.Font.SourceSans
        label.Text = ""

        -- Highlight
        local hl = Instance.new("Highlight", char)
        hl.Name = "ESP_Highlight"
        hl.FillColor = ESPColor
        hl.OutlineColor = Color3.new(1, 1, 1)
        hl.FillTransparency = 0.5
        hl.OutlineTransparency = 0
        hl.Enabled = false

        -- อัปเดต ESP
        RunService.RenderStepped:Connect(function()
            if char and char.Parent and ESPEnabled and char:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local dist = math.floor((char.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
                label.Text = player.Name .. " [" .. dist .. "m]"
                label.TextColor3 = ESPColor
                tag.Enabled = true
                hl.Enabled = true
            else
                tag.Enabled = false
                hl.Enabled = false
            end
        end)
    end

    if player.Character then onChar(player.Character) end
    player.CharacterAdded:Connect(onChar)
end

-- สร้าง ESP ให้ทุกคน
for _, p in pairs(Players:GetPlayers()) do
    setupESP(p)
end
Players.PlayerAdded:Connect(setupESP)

using UnityEngine;
using UnityEngine.UI;

public class MobileAimLock : MonoBehaviour
{
    public Camera playerCam;
    public float lockRange = 10f;      // ระยะเริ่มต้น
    public float rangeStep = 2f;       // เพิ่ม-ลดทีละเท่าไร
    public Button plusButton, minusButton;

    Transform lockedTarget;
    bool isTouching = false;

    void Start()
    {
        plusButton.onClick.AddListener(() => lockRange += rangeStep);
        minusButton.onClick.AddListener(() => lockRange = Mathf.Max(1f, lockRange - rangeStep));
    }

    void Update()
    {
        HandleTouchInput();

        if (isTouching)
        {
            FindAndLockTarget();
            if (lockedTarget != null)
            {
                SmoothAimAt(lockedTarget.position);
            }
        }
    }

    void HandleTouchInput()
    {
        if (Input.touchCount > 0)
        {
            var touch = Input.touches[0];
            isTouching = touch.phase == TouchPhase.Began || touch.phase == TouchPhase.Stationary || touch.phase == TouchPhase.Moved;
            if (!isTouching) lockedTarget = null;
        }
        else
        {
            isTouching = false;
            lockedTarget = null;
        }
    }

    void FindAndLockTarget()
    {
        Collider[] hits = Physics.OverlapSphere(playerCam.transform.position, lockRange);
        float minAngle = Mathf.Infinity;
        lockedTarget = null;

        foreach (var col in hits)
        {
            if (col.CompareTag("Enemy"))
            {
                Vector3 dir = (col.transform.position - playerCam.transform.position).normalized;
                float angle = Vector3.Angle(playerCam.transform.forward, dir);
                if (angle < minAngle)
                {
                    minAngle = angle;
                    lockedTarget = col.transform;
                }
            }
        }
    }

    void SmoothAimAt(Vector3 targetPos)
    {
        Vector3 dirToTarget = targetPos - playerCam.transform.position;
        Quaternion targetRot = Quaternion.LookRotation(dirToTarget);
        playerCam.transform.rotation = Quaternion.Slerp(playerCam.transform.rotation, targetRot, Time.deltaTime * 10f);
    }
}

