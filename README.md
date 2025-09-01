-- Serviços
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Player = game.Players.LocalPlayer

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PerigoGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

-- Fundo escuro para o texto animado
local Background = Instance.new("Frame")
Background.Size = UDim2.new(1, 0, 1, 0)
Background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Background.BackgroundTransparency = 0.6
Background.Parent = ScreenGui

-- Texto de aviso animado
local TextLabel = Instance.new("TextLabel")
TextLabel.Size = UDim2.new(0, 500, 0, 100)
TextLabel.Position = UDim2.new(0.5, -250, 0.4, -50)
TextLabel.Text = "Olá, esse é um script muito perigoso"
TextLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
TextLabel.TextScaled = true
TextLabel.Font = Enum.Font.SourceSansBold
TextLabel.BackgroundTransparency = 1
TextLabel.TextTransparency = 1 -- invisível no começo
TextLabel.Parent = Background

-- Animar texto e mostrar GUI depois
local function animateAndShow()
	local tween = TweenService:Create(TextLabel, TweenInfo.new(2, Enum.EasingStyle.Bounce, Enum.EasingDirection.Out), {TextTransparency = 0})
	tween:Play()
	tween.Completed:Wait()
	wait(1)
	Background:Destroy()
	MainFrame.Visible = true
end

-- Criar frame container principal para GUI (invisível no começo)
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 350, 0, 400)
MainFrame.Position = UDim2.new(0.3, 0, 0.3, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Parent = ScreenGui

-- Barra de título para arrastar e minimizar
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 30)
TitleBar.Position = UDim2.new(0, 0, 0, 0)
TitleBar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TitleBar.Parent = MainFrame

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, -40, 1, 0)
TitleLabel.Position = UDim2.new(0, 10, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "Zaralho"
TitleLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextScaled = true
TitleLabel.Parent = TitleBar

-- Botão minimizar/maximizar dentro TitleBar
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 40, 1, 0)
minimizeButton.Position = UDim2.new(1, -40, 0, 0)
minimizeButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.Font = Enum.Font.SourceSansBold
minimizeButton.TextScaled = true
minimizeButton.Text = "—"
minimizeButton.Parent = TitleBar

-- Criar botões abas
local tabNames = {"TROLL", "ARMAS", "AIMBOT"}
local buttons = {}

for i, name in ipairs(tabNames) do
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(0, 120, 0, 40)
	button.Position = UDim2.new(0, 20, 0, 50 + (i - 1)*50)
	button.Text = name
	button.TextColor3 = Color3.fromRGB(255, 255, 255)
	button.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	button.Font = Enum.Font.SourceSansBold
	button.TextScaled = true
	button.Parent = MainFrame
	buttons[#buttons + 1] = button
end

-- Frames para listas armas e troll (invisíveis)
local armasLista = Instance.new("Frame")
armasLista.Size = UDim2.new(0, 150, 0, 200)
armasLista.Position = UDim2.new(0, 150, 0, 50)
armasLista.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
armasLista.Visible = false
armasLista.Parent = MainFrame

local armasLayout = Instance.new("UIListLayout")
armasLayout.Padding = UDim.new(0, 5)
armasLayout.Parent = armasLista

local trollLista = Instance.new("Frame")
trollLista.Size = UDim2.new(0, 150, 0, 200)
trollLista.Position = UDim2.new(0, 150, 0, 50)
trollLista.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
trollLista.Visible = false
trollLista.Parent = MainFrame

local trollLayout = Instance.new("UIListLayout")
trollLayout.Padding = UDim.new(0, 5)
trollLayout.Parent = trollLista

-- RemoteEvent para armas
local darArma = ReplicatedStorage:WaitForChild("DarArma")

-- Lista armas disponíveis (nomes devem existir em ServerStorage > Armas)
local armasDisponiveis = {"Pistola", "Espada", "Rifle"}

-- Função criar botão arma
local function criarBotaoArma(nome)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, 0, 0, 30)
	btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.Font = Enum.Font.SourceSansBold
	btn.TextScaled = true
	btn.Text = nome
	btn.Parent = armasLista

	btn.MouseButton1Click:Connect(function()
		darArma:FireServer(nome)
		armasLista.Visible = false
		for _, b in ipairs(buttons) do
			if b.Text == "ARMAS" or b.Text:find("ARMAS:") then
				b.Text = "ARMAS: " .. nome
				break
			end
		end
		wait(2)
		for _, b in ipairs(buttons) do
			if b.Text:find("ARMAS:") then
				b.Text = "ARMAS"
			end
		end
	end)
end

-- Criar botões das armas
for _, armaNome in ipairs(armasDisponiveis) do
	criarBotaoArma(armaNome)
end

-- Função criar botão jogador (para kill)
local function matarJogador(jogador)
	if jogador.Character and jogador.Character:FindFirstChild("Humanoid") then
		jogador.Character.Humanoid.Health = 0
	end
end

local function criarBotaoJogador(nomeJogador, jogadorObj)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, 0, 0, 30)
	btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.Font = Enum.Font.SourceSansBold
	btn.TextScaled = true
	btn.Text = nomeJogador
	btn.Parent = trollLista

	btn.MouseButton1Click:Connect(function()
		matarJogador(jogadorObj)
		trollLista.Visible = false
		for _, b in ipairs(buttons) do
			if b.Text == "TROLL" or b.Text:find("TROLL:") then
				b.Text = "TROLL: KILL"
				break
			end
		end
		wait(2)
		for _, b in ipairs(buttons) do
			if b.Text:find("TROLL:") then
				b.Text = "TROLL"
			end
		end
	end)
end

-- Atualizar lista de jogadores no trollLista
local function atualizarJogadores()
	-- Limpar lista atual
	for _, child in pairs(trollLista:GetChildren()) do
		if child:IsA("TextButton") then
			child:Destroy()
		end
	end

	for _, jogador in ipairs(game.Players:GetPlayers()) do
		if jogador ~= Player then
			criarBotaoJogador(jogador.Name, jogador)
		end
	end
end

-- Controlar visibilidade de abas e listas
local function esconderListas()
	armasLista.Visible = false
	trollLista.Visible = false
end

local function esconderAbas()
	for _, btn in ipairs(buttons) do
		btn.Visible = false
	end
	esconderListas()
end

local function mostrarAbas()
	for _, btn in ipairs(buttons) do
		btn.Visible = true
	end
end

-- Variável para aimbot
local aimbotEnabled = false
local aimbotConnection = nil

local function getClosestPlayer()
	local closestPlayer = nil
	local shortestDistance = math.huge
	local camera = workspace.CurrentCamera

	for _, player in pairs(game.Players:GetPlayers()) do
		if player ~= Player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
			local screenPos, onScreen = camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
			if onScreen then
				local mousePos = UserInputService:GetMouseLocation()
				local distance = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
				if distance < shortestDistance then
					shortestDistance = distance
					closestPlayer = player
				end
			end
		end
	end

	return closestPlayer
end

local function startAimbot()
	if aimbotConnection then return end
	aimbotConnection = RunService.RenderStepped:Connect(function()
		local target = getClosestPlayer()
		if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
			local hrp = target.Character.HumanoidRootPart
			workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, hrp.Position)
		end
	end)
end

local function stopAimbot()
	if aimbotConnection then
		aimbotConnection:Disconnect()
		aimbotConnection = nil
	end
end

-- Mostrar/Esconder listas ao clicar nos botões das abas
for _, btn in ipairs(buttons) do
	btn.MouseButton1Click:Connect(function()
		esconderListas()
		if btn.Text == "AIMBOT" then
			aimbotEnabled = not aimbotEnabled
			if aimbotEnabled then
				btn.Text = "AIMBOT: ON"
				startAimbot()
			else
				btn.Text = "AIMBOT"
				stopAimbot()
			end
		elseif btn.Text == "ARMAS" then
			armasLista.Visible = not armasLista.Visible
		elseif btn.Text == "TROLL" then
			atualizarJogadores()
			trollLista.Visible = not trollLista.Visible
		end
	end)
end

-- Botão minimizar/maximizar funcionalidade
local guiMinimized = false

local function setGuiVisibility(visible)
	for _, btn in ipairs(buttons) do
		btn.Visible = visible
	end
	armasLista.Visible = false
	trollLista.Visible = false
end

minimizeButton.MouseButton1Click:Connect(function()
	guiMinimized = not guiMinimized
	if guiMinimized then
		setGuiVisibility(false)
		minimizeButton.Text = "▢"
	else
		setGuiVisibility(true)
		minimizeButton.Text = "—"
	end
end)

-- Sistema para arrastar a GUI pela barra de título
local dragging = false
local dragInput
local dragStart
local startPos

local function update(input)
	local delta = input.Position - dragStart
	MainFrame.Position = UDim2.new(
		startPos.X.Scale,
		startPos.X.Offset + delta.X,
		startPos.Y.Scale,
		startPos.Y.Offset + delta.Y
	)
end

TitleBar.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

TitleBar.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		update(input)
	end
end)

-- Iniciar animação e mostrar GUI
animateAndShow()
