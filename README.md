-- Roblox จุดพักใจ - Fur Elise Auto Piano
-- ใช้กับ KRNL

local vim = game:GetService("VirtualInputManager")

-- ฟังก์ชันกดปุ่ม
function pressKey(key, delay)
    vim:SendKeyEvent(true, Enum.KeyCode[key], false, game)
    task.wait(0.08)
    vim:SendKeyEvent(false, Enum.KeyCode[key], false, game)
    task.wait(delay or 0.08)
end

-- โน้ตเพลง Fur Elise (แปลงเป็นปุ่มในเกมจุดพักใจ)
-- แค่ท่อนแรก
local song = {
    {"D",0.15},{"E",0.15},{"D",0.15},{"E",0.15},{"D",0.15},{"B",0.15},{"D",0.15},{"C",0.15},{"A",0.3},
    {"A",0.15},{"C",0.15},{"E",0.15},{"A",0.3},{"B",0.15},{"E",0.15},{"G",0.15},{"B",0.3},
    {"C",0.15},{"E",0.15},{"A",0.15},{"C",0.3},{"B",0.15},{"E",0.15},{"G",0.15},{"B",0.3}
}

-- วนเล่น
while true do
    for _,note in ipairs(song) do
        pressKey(note[1], note[2])
    end
end