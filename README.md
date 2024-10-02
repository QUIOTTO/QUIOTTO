local Players = game:GetService("Players")
local player = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- Criação do ScreenGui
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))

-- Criação do Frame
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 300)
frame.Position = UDim2.new(0.5, -100, 0.5, -150)
frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

-- Título da Interface
local titleLabel = Instance.new("TextLabel", frame)
titleLabel.Size = UDim2.new(1, 0, 0, 40)
titleLabel.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
titleLabel.Text = "Eu te amo Sara"
titleLabel.TextColor3 = Color3.fromRGB(255, 20, 147)
titleLabel.TextScaled = true
titleLabel.TextStrokeTransparency = 0.5
titleLabel.BorderSizePixel = 0

-- Botão de Fechar
local closeButton = Instance.new("TextButton", frame)
closeButton.Size = UDim2.new(0, 20, 0, 20)
closeButton.Position = UDim2.new(1, -25, 0, 5)
closeButton.Text = "X"
closeButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeButton.BorderSizePixel = 0
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.MouseButton1Click:Connect(function()
    gui.Enabled = false
end)

-- Função para criar botões
local function createButton(text, position)
    local button = Instance.new("TextButton", frame)
    button.Size = UDim2.new(1, -10, 0, 40)
    button.Position = position
    button.Text = text
    button.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    button.TextColor3 = Color3.fromRGB(255, 20, 147)
    button.BorderSizePixel = 0
    button.TextScaled = true
    button.TextStrokeTransparency = 0.5

    -- Efeito de hover
    button.MouseEnter:Connect(function()
        button.BackgroundColor3 = Color3.fromRGB(120, 120, 120)
    end)
    button.MouseLeave:Connect(function()
        button.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    end)

    return button
end

-- Variáveis de controle
local speedEnabled, jumpEnabled, canDestroy = false, false, false

-- Função para teletransportar o jogador
local function teleportToRandomPlayer()
    local players = Players:GetPlayers()
    if #players > 1 then
        local randomPlayer
        repeat
            randomPlayer = players[math.random(1, #players)]
        until randomPlayer ~= player

        -- Verifica se o jogador aleatório tem um personagem
        if randomPlayer.Character and randomPlayer.Character.PrimaryPart then
            player.Character:SetPrimaryPartCFrame(randomPlayer.Character.PrimaryPart.CFrame)
        end
    else
        print("Não há jogadores suficientes para teletransportar.")
    end
end

-- Criação dos botões
local teleportButton = createButton("Teletransportar", UDim2.new(0, 5, 0, 50))
teleportButton.MouseButton1Click:Connect(teleportToRandomPlayer)

local destroyButton = createButton("Destruir Parte", UDim2.new(0, 5, 0, 100))
destroyButton.MouseButton1Click:Connect(function()
    canDestroy = not canDestroy
    destroyButton.Text = canDestroy and "Clique para Destruir" or "Destruir Parte"
end)

-- Função para remover partes ao clicar
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if not gameProcessedEvent and canDestroy then
        local mouse = player:GetMouse()
        local target = mouse.Target

        if target and target:IsDescendantOf(workspace) then
            target:Destroy()
        end
    end
end)

-- Alternar Super Velocidade
local speedButton = createButton("Super Velocidade", UDim2.new(0, 5, 0, 150))
speedButton.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = speedEnabled and 50 or 16
        speedButton.Text = speedEnabled and "Desativar Velocidade" or "Super Velocidade"
    end
end)

-- Alternar Super Pulo
local jumpButton = createButton("Super Pulo", UDim2.new(0, 5, 0, 200))
jumpButton.MouseButton1Click:Connect(function()
    jumpEnabled = not jumpEnabled
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.JumpPower = jumpEnabled and 100 or 50
        jumpButton.Text = jumpEnabled and "Desativar Pulo" or "Super Pulo"
    end
end)

-- Inicialização da interface
frame.Visible = true
gui.Enabled = true
