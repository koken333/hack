local gui = game.Players.LocalPlayer:WaitForChild("PlayerGui")

local function scan(obj, indent)
    indent = indent or ""
    print(indent .. obj.Name .. " [" .. obj.ClassName .. "]")
    for _, child in ipairs(obj:GetChildren()) do
        scan(child, indent .. "  ")
    end
end

scan(gui)