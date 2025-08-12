-- Hook FireServer เพื่อตรวจจับ RemoteEvent + พารามิเตอร์
local old; old = hookfunction(game.ReplicatedStorage.RemoteEventName.FireServer, function(self, ...)
    print("Remote:", self.Name)
    print("Params:", ...)
    return old(self, ...)
end)

