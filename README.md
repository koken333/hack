-- Roblox จุดพักใจ - เล่นเพลง Free Fire Auto Piano
-- เขียนให้ใช้กับ KRNL

local vim = game:GetService("VirtualInputManager")

-- ฟังก์ชันกดปุ่ม
function pressKey(key)
    vim:SendKeyEvent(true, Enum.KeyCode[key], false, game)
    task.wait(0.1)
    vim:SendKeyEvent(false, Enum.KeyCode[key], false, game)
end

-- โน้ตเพลง Free Fire (แมปกับปุ่มบนคีย์บอร์ดในเกม)
local song = {
    "Q","Q","W","E","Q","Q","W","E",
    "Q","E","T","Y","T","E","W",
    "Q","Q","W","E","Q","Q","W","E",
    "Q","E","T","Y","T","E","W"
}

while true do
    for _,note in ipairs(song) do
        pressKey(note)
        task.wait(0.15) -- จังหวะ
    end
end