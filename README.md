local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("gohken", "DarkTheme")

local Tab = Window:NewTab("gohken")
local Section = Tab:NewSection("spaw")

Section:NewButton("spaw", "gohken", function()
    print("Clicked")
end) 
