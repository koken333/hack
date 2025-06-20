for _, plr in pairs(game.Players:GetPlayers()) do
   if plr ~= game.Players.LocalPlayer then
      print(plr.Name, plr.Character and plr.Character.Humanoid.Health)
   end
end
