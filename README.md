-- ESP Simulado para Teste de Anti-Cheat
-- Apenas para uso de desenvolvedor!
-- Coloque em StarterPlayerScripts e controle o acesso

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Configurações
local ESP_COLOR_PLAYERS = Color3.fromRGB(255, 0, 0) -- vermelho para inimigos
local ESP_COLOR_TEAMMATES = Color3.fromRGB(0, 255, 0) -- verde para aliados
local ESP_COLOR_NPCS = Color3.fromRGB(255, 255, 0) -- amarelo para NPCs
local ENABLE_TEAM_CHECK = true

-- Função para criar uma Box ESP em torno do model
local function createESP(model, color)
	if model:FindFirstChild("Head") then
		local highlight = Instance.new("Highlight")
		highlight.Adornee = model
		highlight.FillTransparency = 1
		highlight.OutlineColor = color
		highlight.Parent = model
	end
end

-- Função para aplicar ESP em players
local function addPlayerESP(player)
	if player == LocalPlayer then return end
	player.CharacterAdded:Connect(function(char)
		repeat task.wait() until char:FindFirstChild("Head")

		if ENABLE_TEAM_CHECK and player.Team == LocalPlayer.Team then
			createESP(char, ESP_COLOR_TEAMMATES)
		else
			createESP(char, ESP_COLOR_PLAYERS)
		end
	end)
	if player.Character then
		if ENABLE_TEAM_CHECK and player.Team == LocalPlayer.Team then
			createESP(player.Character, ESP_COLOR_TEAMMATES)
		else
			createESP(player.Character, ESP_COLOR_PLAYERS)
		end
	end
end

-- Função para aplicar ESP em NPCs
local function addNPCESP(npc)
	if npc:FindFirstChild("Humanoid") and npc:FindFirstChild("Head") then
		createESP(npc, ESP_COLOR_NPCS)
	end
end

-- Detecta novos players
for _, plr in ipairs(Players:GetPlayers()) do
	addPlayerESP(plr)
end
Players.PlayerAdded:Connect(addPlayerESP)

-- Detecta NPCs no workspace
for _, obj in ipairs(workspace:GetChildren()) do
	if obj:FindFirstChild("Humanoid") and not Players:GetPlayerFromCharacter(obj) then
		addNPCESP(obj)
	end
end
workspace.ChildAdded:Connect(function(obj)
	if obj:FindFirstChild("Humanoid") and not Players:GetPlayerFromCharacter(obj) then
		addNPCESP(obj)
	end
end)
