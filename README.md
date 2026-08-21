--[[
    NoobHub v2.1 - Sistema de invisibilidade com GUI avançada
    Features: Drag corrigido, Minimizar (bolinha), Confirmação dupla para fechar
]]

-- Limpeza de conexões anteriores
if _G.NoobHub then
    for _, conexao in pairs(_G.NoobHub) do
        if type(conexao) == "function" then
            pcall(conexao)
        else
            pcall(conexao.Disconnect, conexao)
        end
    end
    _G.NoobHub = nil
end

-- Variáveis globais
local jogador = game.Players.LocalPlayer
local personagem = nil
local humanoide = nil
local torso = nil
local partes = {}
local ativo = false
local minimizado = false
local janela = nil
local botao = nil
local botaoMinimizar = nil
local botaoFechar = nil

-- Variáveis para o sistema de drag (corrigido)
local arrastando = false
local dragInput = nil
local dragStart = nil
local startPos = nil

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
    
    if botao then
        botao.Text = ativo and "🟢 ATIVO" or "🔴 INATIVO"
        botao.BackgroundColor3 = ativo and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(255, 0, 200)
    end
end

-- Função para minimizar (vira bolinha)
local function alternarMinimizar()
    minimizado = not minimizado
    
    if minimizado then
        -- Transformar em bolinha
        janela.Size = UDim2.new(0, 50, 0, 50)
        janela.BackgroundColor3 = Color3.fromRGB(255, 0, 200)
        janela.BorderColor3 = Color3.fromRGB(255, 255, 255)
        janela.BorderSizePixel = 2
        
        -- Esconder todos os elementos internos
        for _, filho in pairs(janela:GetChildren()) do
            if filho.Name ~= "BarraTitulo" and filho.Name ~= "BolinhaLabel" then
                filho.Visible = false
            end
        end
        
        -- Mostrar label da bolinha
        local bolinhaLabel = janela:FindFirstChild("BolinhaLabel")
        if not bolinhaLabel then
            bolinhaLabel = Instance.new("TextLabel")
            bolinhaLabel.Name = "BolinhaLabel"
            bolinhaLabel.Size = UDim2.new(1, 0, 1, 0)
            bolinhaLabel.BackgroundTransparency = 1
            bolinhaLabel.Text = "🔵"
            bolinhaLabel.TextSize = 30
            bolinhaLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            bolinhaLabel.TextScaled = true
            bolinhaLabel.Parent = janela
        else
            bolinhaLabel.Visible = true
        end
        
        -- Atualizar barra de título para arrastar a bolinha
        local barraTitulo = janela:FindFirstChild("BarraTitulo")
        if barraTitulo then
            barraTitulo.Size = UDim2.new(1, 0, 1, 0)
            barraTitulo.BackgroundTransparency = 0.3
        end
        
    else
        -- Voltar ao normal
        janela.Size = UDim2.new(0, 280, 0, 200)
        janela.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        janela.BorderColor3 = Color3.fromRGB(255, 0, 200)
        janela.BorderSizePixel = 2
        
        -- Mostrar todos os elementos novamente
        for _, filho in pairs(janela:GetChildren()) do
            if filho.Name ~= "BarraTitulo" and filho.Name ~= "BolinhaLabel" then
                filho.Visible = true
            end
        end
        
        -- Esconder label da bolinha
        local bolinhaLabel = janela:FindFirstChild("BolinhaLabel")
        if bolinhaLabel then
            bolinhaLabel.Visible = false
        end
        
        -- Restaurar barra de título
        local barraTitulo = janela:FindFirstChild("BarraTitulo")
        if barraTitulo then
            barraTitulo.Size = UDim2.new(1, 0, 0, 30)
            barraTitulo.BackgroundTransparency = 0.3
        end
    end
end

-- Função para fechar com confirmação dupla
local function solicitarFechamento()
    if not confirmacaoFechar then
        confirmacaoFechar = true
        if botaoFechar then
            botaoFechar.Text = "⚠️ CONFIRMAR?"
            botaoFechar.BackgroundColor3 = Color3.fromRGB(255, 50, 0)
        end
        
        -- Resetar confirmação após 3 segundos
        if timerFechar then
            timerFechar:Disconnect()
        end
        timerFechar = game:GetService("RunService").Stepped:Connect(function()
            if confirmacaoFechar then
                confirmacaoFechar = false
                if botaoFechar then
                    botaoFechar.Text = "❌ FECHAR"
                    botaoFechar.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
                end
                timerFechar:Disconnect()
                timerFechar = nil
            end
        end)
        task.wait(3)
        if confirmacaoFechar then
            confirmacaoFechar = false
            if botaoFechar then
                botaoFechar.Text = "❌ FECHAR"
                botaoFechar.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
            end
        end
    else
        -- Fechar GUI
        confirmacaoFechar = false
        if timerFechar then
            timerFechar:Disconnect()
            timerFechar = nil
        end
        local gui = janela:FindFirstAncestor("ScreenGui")
        if gui then
            gui:Destroy()
        end
        print("NoobHub fechado!")
    end
end

-- Função para atualizar posição do drag (corrigido)
local function updateDrag(input)
    local delta = input.Position - dragStart
    local newX = startPos.X.Offset + delta.X
    local newY = startPos.Y.Offset + delta.Y
    
    -- Manter dentro da tela
    local viewportSize = game:GetService("GuiService"):GetScreenSize()
    local maxX = viewportSize.X - janela.AbsoluteSize.X
    local maxY = viewportSize.Y - janela.AbsoluteSize.Y
    
    newX = math.clamp(newX, 0, maxX)
    newY = math.clamp(newY, 0, maxY)
    
    janela.Position = UDim2.new(0, newX, 0, newY)
end

-- Criar interface gráfica
local function criarInterface()
    -- Criar ScreenGui
    local tela = Instance.new("ScreenGui")
    tela.Name = "NoobHubGUI"
    tela.Parent = jogador:WaitForChild("PlayerGui")
    
    -- Criar frame principal (janela)
    janela = Instance.new("Frame")
    janela.Name = "JanelaPrincipal"
    janela.Size = UDim2.new(0, 280, 0, 200)
    janela.Position = UDim2.new(0.5, -140, 0.5, -100)
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
    
    -- Barra de título (para drag e botões)
    local barraTitulo = Instance.new("Frame")
    barraTitulo.Name = "BarraTitulo"
    barraTitulo.Size = UDim2.new(1, 0, 0, 30)
    barraTitulo.BackgroundColor3 = Color3.fromRGB(255, 0, 200)
    barraTitulo.BackgroundTransparency = 0.3
    barraTitulo.Parent = janela
    
    -- Título
    local titulo = Instance.new("TextLabel")
    titulo.Name = "Titulo"
    titulo.Size = UDim2.new(0.5, 0, 1, 0)
    titulo.Position = UDim2.new(0.05, 0, 0, 0)
    titulo.BackgroundTransparency = 1
    titulo.Text = "🌟 NoobHub v2.1"
    titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
    titulo.TextSize = 14
    titulo.TextXAlignment = Enum.TextXAlignment.Left
    titulo.TextYAlignment = Enum.TextYAlignment.Center
    titulo.Parent = barraTitulo
    
    -- Botão minimizar (bola)
    botaoMinimizar = Instance.new("TextButton")
    botaoMinimizar.Name = "BotaoMinimizar"
    botaoMinimizar.Size = UDim2.new(0, 25, 0, 25)
    botaoMinimizar.Position = UDim2.new(0.8, 0, 0.08, 0)
    botaoMinimizar.Text = "➖"
    botaoMinimizar.TextSize = 14
    botaoMinimizar.TextColor3 = Color3.fromRGB(255, 255, 255)
    botaoMinimizar.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    botaoMinimizar.BackgroundTransparency = 0.3
    botaoMinimizar.BorderSizePixel = 0
    botaoMinimizar.Parent = barraTitulo
    
    local minCorner = Instance.new("UICorner")
    minCorner.CornerRadius = UDim.new(1, 0)
    minCorner.Parent = botaoMinimizar
    
    -- Botão fechar (X)
    botaoFechar = Instance.new("TextButton")
    botaoFechar.Name = "BotaoFechar"
    botaoFechar.Size = UDim2.new(0, 25, 0, 25)
    botaoFechar.Position = UDim2.new(0.9, 0, 0.08, 0)
    botaoFechar.Text = "❌"
    botaoFechar.TextSize = 14
    botaoFechar.TextColor3 = Color3.fromRGB(255, 255, 255)
    botaoFechar.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    botaoFechar.BackgroundTransparency = 0.3
    botaoFechar.BorderSizePixel = 0
    botaoFechar.Parent = barraTitulo
    
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(1, 0)
    closeCorner.Parent = botaoFechar
    
    -- Status
    local statusLabel = Instance.new("TextLabel")
    statusLabel.Name = "Status"
    statusLabel.Size = UDim2.new(0.9, 0, 0, 30)
    statusLabel.Position = UDim2.new(0.05, 0, 0.25, 0)
    statusLabel.BackgroundTransparency = 1
    statusLabel.Text = "🔧 Clique no botão para ativar"
    statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    statusLabel.TextSize = 13
    statusLabel.TextWrapped = true
    statusLabel.Parent = janela
    
    -- Label de informação
    local infoLabel = Instance.new("TextLabel")
    infoLabel.Name = "InfoLabel"
    infoLabel.Size = UDim2.new(0.9, 0, 0, 20)
    infoLabel.Position = UDim2.new(0.05, 0, 0.4, 0)
    infoLabel.BackgroundTransparency = 1
    infoLabel.Text = "Tecla 'G' para alternar"
    infoLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
    infoLabel.TextSize = 11
    infoLabel.TextWrapped = true
    infoLabel.Parent = janela
    
    -- Botão de alternância (principal)
    botao = Instance.new("TextButton")
    botao.Name = "BotaoAlternar"
    botao.Size = UDim2.new(0.9, 0, 0, 35)
    botao.Position = UDim2.new(0.05, 0, 0.55, 0)
    botao.Text = "🔴 INATIVO"
    botao.TextColor3 = Color3.fromRGB(255, 255, 255)
    botao.TextSize = 15
    botao.BackgroundColor3 = Color3.fromRGB(255, 0, 200)
    botao.BackgroundTransparency = 0.2
    botao.BorderSizePixel = 0
    botao.Parent = janela
    
    local botaoCorner = Instance.new("UICorner")
    botaoCorner.CornerRadius = UDim.new(0, 8)
    botaoCorner.Parent = botao
    
    -- SISTEMA DE DRAG CORRIGIDO
    -- Evento para iniciar o drag
    barraTitulo.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = true
            dragStart = input.Position
            startPos = janela.Position
            
            -- Criar conexão para mover durante o drag
            dragInput = game:GetService("RunService").Heartbeat:Connect(function()
                if arrastando then
                    local mouse = jogador:GetMouse()
                    local delta = mouse.X - dragStart.X
                    local deltaY = mouse.Y - dragStart.Y
                    
                    local newX = startPos.X.Offset + delta
                    local newY = startPos.Y.Offset + deltaY
                    
                    -- Manter dentro da tela
                    local viewportSize = game:GetService("GuiService"):GetScreenSize()
                    local maxX = viewportSize.X - janela.AbsoluteSize.X
                    local maxY = viewportSize.Y - janela.AbsoluteSize.Y
                    
                    newX = math.clamp(newX, 0, maxX)
                    newY = math.clamp(newY, 0, maxY)
                    
                    janela.Position = UDim2.new(0, newX, 0, newY)
                end
            end)
        end
    end)
    
    -- Evento para finalizar o drag
    barraTitulo.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = false
            if dragInput then
                dragInput:Disconnect()
                dragInput = nil
            end
        end
    end)
    
    -- Clique do botão minimizar
    botaoMinimizar.MouseButton1Click:Connect(alternarMinimizar)
    
    -- Clique do botão fechar (com confirmação dupla)
    botaoFechar.MouseButton1Click:Connect(solicitarFechamento)
    
    -- Clique do botão principal
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

-- Tecla 'G' para alternar
local function teclaAtalho()
    local atalho = jogador:GetMouse().KeyDown:Connect(function(tecla)
        if tecla == "g" then
            alternarVisibilidade()
        end
    end)
    return atalho
end

-- Reconectar quando personagem renascer
local function reconectarPersonagem()
    return jogador.CharacterAdded:Connect(function()
        ativo = false
        if botao then
            botao.Text = "🔴 INATIVO"
            botao.BackgroundColor3 = Color3.fromRGB(255, 0, 200)
        end
        coletarPartes()
    end)
end

-- Inicialização
coletarPartes()
criarInterface()

-- Iniciar threads
local threadInvis = coroutine.create(sistemaInvisibilidade)
coroutine.resume(threadInvis)

local atalhoKey = teclaAtalho()
local reconectar = reconectarPersonagem()

-- Armazenar conexões para limpeza
_G.NoobHub = {
    threadInvis,
    atalhoKey,
    reconectar,
    function() 
        if timerFechar then 
            timerFechar:Disconnect() 
            timerFechar = nil 
        end 
        if dragInput then
            dragInput:Disconnect()
            dragInput = nil
        end
    end
}

print("🌟 NoobHub v2.1 carregado com sucesso!")
print("📌 Sistema de drag completamente corrigido!")
print("📌 Clique em '➖' para minimizar (vira bolinha)")
print("📌 Clique em '❌' (2x) para fechar o GUI")
print("📌 Pressione 'G' para ativar/desativar a invisibilidade")
