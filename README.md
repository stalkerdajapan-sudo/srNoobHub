--[[
    NoobVisivel - Sistema de invisibilidade para jogadores
    Versão: 1.0
    Autor: Script Editado
]]

-- Limpeza de conexões anteriores
if _G.NoobVisivel then
    for _, conexao in pairs(_G.NoobVisivel) do
        conexao:Disconnect()
    end
    _G.NoobVisivel = nil
end

-- Variáveis globais
local jogador = game.Players.LocalPlayer
local personagem = nil
local humanoide = nil
local torso = nil
local partes = {}
local ativo = false
local janela = nil
local botao = nil
local arrastando = false
local posInicialDrag = nil
local posJanelaDrag = nil

-- Função para coletar partes do personagem
local function coletarPartes()
    personagem = jogador.Character or jogador.CharacterAdded:Wait()
    humanoide = personagem:WaitForChild("Humanoid")
    torso = personagem:WaitForChild("HumanoidRootPart")
    partes = {}
    
    for _, parte in pairs(personagem:GetDescendants()) do
        if parte:IsA("BasePart") and parte.Transparency == 0 then
            table.insert(partes, parte)
        end
    end
end

-- Função para alternar visibilidade
local function alternarVisibilidade()
    ativo = not ativo
    
    for _, parte in pairs(partes) do
        parte.Transparency = parte.Transparency == 0 and 0.5 or 0
    end
    
    -- Atualizar texto do botão
    if botao then
        botao.Text = ativo and "NoobVisivel: ON" or "NoobVisivel: OFF"
    end
end

-- Criar interface gráfica
local function criarInterface()
    -- Criar ScreenGui
    local tela = Instance.new("ScreenGui")
    tela.Name = "NoobVisivelGUI"
    tela.Parent = jogador:WaitForChild("PlayerGui")
    
    -- Criar frame principal (janela)
    janela = Instance.new("Frame")
    janela.Name = "JanelaPrincipal"
    janela.Size = UDim2.new(0, 200, 0, 80)
    janela.Position = UDim2.new(0.5, -100, 0.5, -40)
    janela.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    janela.BackgroundTransparency = 0.1
    janela.BorderSizePixel = 2
    janela.BorderColor3 = Color3.fromRGB(255, 0, 200)
    janela.ClipsDescendants = true
    janela.Parent = tela
    
    -- Adicionar cantos arredondados
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = janela
    
    -- Barra de título (para drag)
    local barraTitulo = Instance.new("Frame")
    barraTitulo.Name = "BarraTitulo"
    barraTitulo.Size = UDim2.new(1, 0, 0, 25)
    barraTitulo.BackgroundColor3 = Color3.fromRGB(255, 0, 200)
    barraTitulo.BackgroundTransparency = 0.3
    barraTitulo.Parent = janela
    
    -- Título da janela
    local titulo = Instance.new("TextLabel")
    titulo.Name = "Titulo"
    titulo.Size = UDim2.new(1, 0, 1, 0)
    titulo.BackgroundTransparency = 1
    titulo.Text = "NoobVisivel v1.0"
    titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
    titulo.TextSize = 14
    titulo.TextXAlignment = Enum.TextXAlignment.Left
    titulo.TextYAlignment = Enum.TextYAlignment.Center
    titulo.Parent = barraTitulo
    
    -- Texto de status
    local statusLabel = Instance.new("TextLabel")
    statusLabel.Name = "Status"
    statusLabel.Size = UDim2.new(0.8, 0, 0, 30)
    statusLabel.Position = UDim2.new(0.1, 0, 0.4, 0)
    statusLabel.BackgroundTransparency = 1
    statusLabel.Text = "Clique no botão para ativar"
    statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    statusLabel.TextSize = 12
    statusLabel.TextWrapped = true
    statusLabel.Parent = janela
    
    -- Botão de alternância
    botao = Instance.new("TextButton")
    botao.Name = "BotaoAlternar"
    botao.Size = UDim2.new(0.8, 0, 0, 30)
    botao.Position = UDim2.new(0.1, 0, 0.7, -15)
    botao.Text = "NoobVisivel: OFF"
    botao.TextColor3 = Color3.fromRGB(255, 255, 255)
    botao.TextSize = 14
    botao.BackgroundColor3 = Color3.fromRGB(255, 0, 200)
    botao.BackgroundTransparency = 0.2
    botao.BorderSizePixel = 0
    botao.Parent = janela
    
    -- Cantos arredondados no botão
    local botaoCorner = Instance.new("UICorner")
    botaoCorner.CornerRadius = UDim.new(0, 5)
    botaoCorner.Parent = botao
    
    -- Sistema de drag (arrastar janela)
    barraTitulo.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = true
            posInicialDrag = input.Position
            posJanelaDrag = janela.Position
        end
    end)
    
    barraTitulo.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and arrastando then
            local delta = input.Position - posInicialDrag
            janela.Position = UDim2.new(
                posJanelaDrag.X.Scale, 
                posJanelaDrag.X.Offset + delta.X,
                posJanelaDrag.Y.Scale, 
                posJanelaDrag.Y.Offset + delta.Y
            )
        end
    end)
    
    barraTitulo.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = false
        end
    end)
    
    -- Clique do botão
    botao.MouseButton1Click:Connect(alternarVisibilidade)
end

-- Sistema principal de invisibilidade (loop)
local function sistemaInvisibilidade()
    while task.wait() do
        if ativo and torso and humanoide then
            local cfOriginal = torso.CFrame
            local camOffsetOriginal = humanoide.CameraOffset
            
            -- Mover personagem para baixo
            local cfNovo = cfOriginal * CFrame.new(0, -200000, 0)
            torso.CFrame = cfNovo
            humanoide.CameraOffset = cfNovo:ToObjectSpace(CFrame.new(cfOriginal.Position)).Position
            
            -- Aguardar um frame
            game:GetService("RunService").RenderStepped:Wait()
            
            -- Restaurar posição original
            torso.CFrame = cfOriginal
            humanoide.CameraOffset = camOffsetOriginal
        end
    end
end

-- Reconectar quando personagem renascer
jogador.CharacterAdded:Connect(function()
    ativo = false
    if botao then
        botao.Text = "NoobVisivel: OFF"
    end
    coletarPartes()
end)

-- Inicialização
coletarPartes()
criarInterface()

-- Iniciar thread de invisibilidade
local threadInvis = coroutine.create(sistemaInvisibilidade)
coroutine.resume(threadInvis)

-- Armazenar conexões para limpeza
_G.NoobVisivel = {
    threadInvis
}

print("NoobVisivel carregado com sucesso!")
print("Pressione o botão na tela ou tecla 'G' para ativar/desativar")
