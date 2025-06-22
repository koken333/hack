for _, gui in ipairs(game.Players.LocalPlayer.PlayerGui:GetDescendants()) do
    if (gui:IsA("TextLabel") or gui:IsA("TextButton")) and gui.Text:find("Epic") then

        -- ✅ วิธีใช้ UITextSizeConstraint
        gui.TextScaled = true
        local constr = gui:FindFirstChildOfClass("UITextSizeConstraint")
        if not constr then
            constr = Instance.new("UITextSizeConstraint", gui)
        end
        constr.MinTextSize = 30   -- ปรับให้ใหญ่ขึ้น เช่น 30px
        constr.MaxTextSize = 80   -- จำกัดสูงสุดไม่ให้ใหญ่เกิน
        -- หรือจะปรับตรงๆ:
        -- gui.TextSize = 60
        
        print("ปรับขนาด Epic!:", gui:GetFullName())
    end
end
