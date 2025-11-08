
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Criar ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "DevPanel"
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Frame principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 250)
frame.Position = UDim2.new(0.3, 0, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BorderColor3 = Color3.fromRGB(80, 80, 80)
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

-- UI Corner
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = frame

-- Título
local title = Instance.new("TextLabel")
title.Text = "PAINEL DE TESTE"
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(200, 200, 200)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.Parent = frame

-- Função auxiliar de botão
local function criarBotao(texto, ordem)
	local botao = Instance.new("TextButton")
	botao.Size = UDim2.new(1, -20, 0, 30)
	botao.Position = UDim2.new(0, 10, 0, 40 + (ordem * 35))
	botao.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	botao.TextColor3 = Color3.fromRGB(220, 220, 220)
	botao.Text = texto
	botao.Font = Enum.Font.Gotham
	botao.TextSize = 16
	botao.Parent = frame
	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 6)
	corner.Parent = botao
	return botao
end

-- Noclip
local noclipAtivo = false
local noclipBtn = criarBotao("Noclip (Desligado)", 0)
noclipBtn.MouseButton1Click:Connect(function()
	noclipAtivo = not noclipAtivo
	noclipBtn.Text = "Noclip (" .. (noclipAtivo and "Ligado" or "Desligado") .. ")"
end)

game:GetService("RunService").Stepped:Connect(function()
	if noclipAtivo and character then
		for _, part in pairs(character:GetDescendants()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end
end)

-- Fly
local flyAtivo = false
local flyBtn = criarBotao("Fly (Desligado)", 1)
local velocidadeFly = 50
flyBtn.MouseButton1Click:Connect(function()
	flyAtivo = not flyAtivo
	flyBtn.Text = "Fly (" .. (flyAtivo and "Ligado" or "Desligado") .. ")"
end)

-- Campo para velocidade Fly
local flyBox = Instance.new("TextBox")
flyBox.PlaceholderText = "Velocidade Fly"
flyBox.Size = UDim2.new(1, -20, 0, 30)
flyBox.Position = UDim2.new(0, 10, 0, 110)
flyBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
flyBox.TextColor3 = Color3.fromRGB(255, 255, 255)
flyBox.Font = Enum.Font.Gotham
flyBox.TextSize = 16
flyBox.Parent = frame
flyBox.FocusLost:Connect(function()
	local val = tonumber(flyBox.Text)
	if val then
		velocidadeFly = val
	end
end)

-- Movimento Fly simulado
game:GetService("RunService").Heartbeat:Connect(function()
	if flyAtivo and character and humanoid then
		local cam = workspace.CurrentCamera
		character:TranslateBy(cam.CFrame.LookVector * (velocidadeFly / 100))
	end
end)

-- ESP Simulado (Somente exemplo visual)
local espAtivo = false
local espBtn = criarBotao("ESP (Desligado)", 2)
espBtn.MouseButton1Click:Connect(function()
	espAtivo = not espAtivo
	espBtn.Text = "ESP (" .. (espAtivo and "Ligado" or "Desligado") .. ")"
	for _, plr in pairs(game.Players:GetPlayers()) do
		if plr ~= player and plr.Character and plr.Character:FindFirstChild("Head") then
			local highlight = plr.Character:FindFirstChildOfClass("Highlight")
			if espAtivo then
				if not highlight then
					local hl = Instance.new("Highlight")
					hl.FillTransparency = 1
					hl.OutlineColor = Color3.fromRGB(0, 255, 0)
					hl.Parent = plr.Character
				end
			else
				if highlight then highlight:Destroy() end
			end
		end
	end
end)
