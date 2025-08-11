local lp = game.Players.LocalPlayer
local pg = lp:WaitForChild("PlayerGui")

pg.DescendantAdded:Connect(function(obj)
    if obj:IsA("TextButton") or obj:IsA("ImageButton") then
        print("New button found:", obj.Name, obj.Text or "")
        obj.MouseButton1Click:Connect(function()
            print("Clicked:", obj.Name)
        end)
    end
end)
