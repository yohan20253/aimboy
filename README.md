--// Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

--// Configurações
local ParteParaMirar = "Head" -- Ou "HumanoidRootPart"
local ChecarTime = false -- true = não mira em aliados
local AimbotAtivo = false

--// Interface para mobile
local gui = Instance.new("ScreenGui")
gui.Name = "AimbotMobileUI"
gui.ResetOnSpawn = false
gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local botao = Instance.new("TextButton")
botao.Size = UDim2.new(0, 120, 0, 50)
botao.Position = UDim2.new(1, -130, 1, -60)
botao.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
botao.TextColor3 = Color3.new(1, 1, 1)
botao.Text = "Aimbot: OFF"
botao.Font = Enum.Font.SourceSansBold
botao.TextSize = 20
botao.Parent = gui

--// Função: encontra jogador mais próximo fisicamente
local function JogadorMaisProximo()
	local menorDistancia = math.huge
	local alvoMaisProximo = nil

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(ParteParaMirar) then
			if not ChecarTime or player.Team ~= LocalPlayer.Team then
				local parte = player.Character[ParteParaMirar]
				local distancia = (parte.Position - Camera.CFrame.Position).Magnitude
				if distancia < menorDistancia then
					menorDistancia = distancia
					alvoMaisProximo = parte
				end
			end
		end
	end

	return alvoMaisProximo
end

--// Loop de Aimbot
RunService.RenderStepped:Connect(function()
	if AimbotAtivo then
		local alvo = JogadorMaisProximo()
		if alvo then
			local direcao = (alvo.Position - Camera.CFrame.Position).Unit
			Camera.CFrame = CFrame.new(Camera.CFrame.Position, Camera.CFrame.Position + direcao)
		end
	end
end)

--// Clique do botão ativa/desativa
botao.MouseButton1Click:Connect(function()
	AimbotAtivo = not AimbotAtivo
	botao.Text = AimbotAtivo and "Aimbot: ON" or "Aimbot: OFF"
	botao.BackgroundColor3 = AimbotAtivo and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(30, 30, 30)
end)
