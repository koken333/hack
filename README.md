local vim = game:GetService("VirtualInputManager")

function pressKey(key, delay)
    vim:SendKeyEvent(true, Enum.KeyCode[key], false, game)
    task.wait(0.06)  -- กดปุ่มค้างสั้นลงหน่อยให้จังหวะมันเร็วขึ้น
    vim:SendKeyEvent(false, Enum.KeyCode[key], false, game)
    task.wait(delay or 0.06)
end

local song = {
    {"D",0.1},{"E",0.1},{"D",0.2},
    {"E",0.1},{"D",0.1},{"B",0.2},
    {"D",0.1},{"C",0.1},{"A",0.2},
    {"A",0.1},{"C",0.1},{"E",0.2},
    {"A",0.1},{"B",0.1},{"E",0.2},
    {"G",0.1},{"B",0.1},{"C",0.2},
    {"E",0.1},{"A",0.1},{"C",0.2},
    {"B",0.1},{"E",0.1},{"G",0.2},
    {"B",0.3}
}

while true do
    for _,note in ipairs(song) do
        pressKey(note[1], note[2])
    end
end