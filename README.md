-- LocalScript (StarterPlayerScripts)
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "SmartFarmUI"

local button = Instance.new("TextButton", gui)
button.Size = UDim2.new(0, 180, 0, 40)
button.Position = UDim2.new(0, 0.8, 0, 0)
button.Text = "Ativar SmartFarm"
button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
button.TextColor3 = Color3.new(1, 1, 1)
button.Font = Enum.Font.SourceSansBold
button.TextSize = 16
Instance.new("UICorner", button)

local ativo = false

-- Lista de missões por nível
local Missoes = {
	{nivel = 1, inimigo = "Bandit", npc = "BanditQuestGiver", pos = Vector3.new(1060, 17, 1548)},
	{nivel = 10, inimigo = "Monkey", npc = "JungleQuest", pos = Vector3.new(-1600, 36, 145)},
	{nivel = 15, inimigo = "Gorilla", npc = "JungleQuest", pos = Vector3.new(-1600, 36, 145)},
	{nivel = 20, inimigo = "Pirate", npc = "BuggyQuest1", pos = Vector3.new(-1145, 4, 3825)},
	{nivel = 30, inimigo = "Brute", npc = "BuggyQuest1", pos = Vector3.new(-1145, 4, 3825)},
	{nivel = 40, inimigo = "Desert Bandit", npc = "DesertQuest", pos = Vector3.new(924, 7, 4495)},
	{nivel = 60, inimigo = "Snow Bandit", npc = "SnowQuest", pos = Vector3.new(1389, 87, -1297)},
	{nivel = 75, inimigo = "Chief Petty Officer", npc = "MarineQuest2", pos = Vector3.new(-5035, 29, 4326)}
}

local function getMissao()
	local nivel = player.Stats and player.Stats.Level and player.Stats.Level.Value or 1
	local selecionada = Missoes[1]
	for _, m in ipairs(Missoes) do
		if nivel >= m.nivel then
			selecionada = m
		end
	end
	return selecionada
end

local function aceitarQuest()
	local m = getMissao()
	local npc = Workspace:FindFirstChild(m.npc)
	if npc and npc:FindFirstChild("Head") then
		player.Character.HumanoidRootPart.CFrame = CFrame.new(m.pos + Vector3.new(0, 3, 0))
		wait(1)
		local prompt = npc:FindFirstChildWhichIsA("ProximityPrompt", true)
		if prompt then
			fireproximityprompt(prompt)
			wait(1)
		end
	end
end

local function atacar()
	local m = getMissao()
	while ativo do
		for _, npc in pairs(Workspace.Enemies:GetChildren()) do
			if npc.Name == m.inimigo and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 then
				player.Character.HumanoidRootPart.CFrame = npc.HumanoidRootPart.CFrame + Vector3.new(0, 6, 0)
				while npc.Humanoid.Health > 0 and ativo do
					local tool = player.Character:FindFirstChildOfClass("Tool")
					if tool then
						tool:Activate()
					end
					wait(0.2)
				end
			end
		end
		wait(1)
	end
end

button.MouseButton1Click:Connect(function()
	ativo = not ativo
	button.Text = ativo and "Parar SmartFarm" or "Ativar SmartFarm"
	if ativo then
		aceitarQuest()
		task.spawn(atacar)
	end
end)
