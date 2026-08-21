--[[
    NOOB HUB v5.0 - Sistema de invisibilidade premium
    Drag CORRIGIDO para CELULAR e PC | Cor amarela
]]

-- Limpeza de conexões anteriores
if _G.NoobHub then
    for _, conexao in pairs(_G.NoobHub) do
        pcall(conexao.Disconnect, conexao)
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
local gui = nil
local confirmacaoFechar = false
local timerFechar = nil

-- SISTEMA DE DRAG CORRIGIDO (FUNCIONA NO CELULAR)
local dragging = false
local dragStartPos = nil
local dragStartMouse = nil
local dragConnection = nil
local userInputService = game:GetService("UserInputService")

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
    
    local btn = janela and janela:FindFirstChild("BtnAtivar")
    if btn then
        btn.Text = ativo and "🟢 ATIVADO" or "🔴 DESATIVADO"
        btn.BackgroundColor3 = ativo and Color3.fromRGB(0, 200, 50) or Color3.fromRGB(200, 150, 0)
    end
end

-- Função para minimizar
local function alternarMinimizar()
    minimizado = not minimizado
    local corpo = janela:FindFirstChild("Corpo")
    local bolinha = janela:FindFirstChild("Bolinha")
    
    if minimizado then
        corpo.Visible = false
        bolinha.Visible = true
        janela.Size = UDim2.new(0, 60, 0, 60)
        janela.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
        janela.BorderSizePixel = 3
        janela.BorderColor3 = Color3.fromRGB(255, 255, 255)
    else
        corpo.Visible = true
        bolinha.Visible = false
        janela.Size = UDim2.new(0, 300, 0, 220)
        janela.BackgroundColor3 = Color3.fromRGB(30, 25, 10)
        janela.BorderSizePixel = 3
        janela.BorderColor3 = Color3.fromRGB(255, 200, 0)
    end
end

-- Função para fechar com confirmação dupla
local function solicitarFechamento()
    local btn = janela:FindFirstChild("BtnFechar")
    
    if not confirmacaoFechar then
        confirmacaoFechar = true
        btn.Text = "⚠️ CONFIRMAR"
        btn.BackgroundColor3 = Color3.fromRGB(255, 50, 0)
        
        if timerFechar then
            timerFechar:Disconnect()
        end
        
        timerFechar = game:GetService("RunService").Stepped:Connect(function()
            confirmacaoFechar = false
            btn.Text = "✕"
            btn.BackgroundColor3 = Color3.fromRGB(200, 150, 0)
            timerFechar:Disconnect()
            timerFechar = nil
        end)
        
        task.wait(3)
        
        if confirmacaoFechar then
            confirmacaoFechar = false
            btn.Text = "✕"
            btn.BackgroundColor3 = Color3.fromRGB(200, 150, 0)
            if timerFechar then
                timerFechar:Disconnect()
                timerFechar = nil
            end
        end
    else
        confirmacaoFechar = false
        if timerFechar then
            timerFechar:Disconnect()
            timerFechar = nil
        end
        if gui then
            gui:Destroy()
        end
    end
end

-- SISTEMA DE DRAG CORRIGIDO (FUNCIONA NO CELULAR)
local function iniciarDrag(input)
    -- Detecta tanto mouse quanto toque (celular)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or 
       input.UserInputType == Enum.UserInputType.Touch then
        
        dragging = true
        dragStartPos = Vector2.new(janela.Position.X.Offset, janela.Position.Y.Offset)
        dragStartMouse = Vector2.new(input.Position.X, input.Position.Y)
        
        if dragConnection then
            dragConnection:Disconnect()
        end
        
        -- Usa InputChanged para movimento contínuo
        dragConnection = userInputService.InputChanged:Connect(function(inputChanged)
            if dragging and (inputChanged.UserInputType == Enum.UserInputType.MouseMovement or 
                            inputChanged.UserInputType == Enum.UserInputType.Touch) then
                
                local mousePos = Vector2.new(inputChanged.Position.X, inputChanged.Position.Y)
                local delta = mousePos - dragStartMouse
                
                local newX = dragStartPos.X + delta.X
                local newY = dragStartPos.Y + delta.Y
                
                -- Clamp para não sair da tela
                local screenSize = game:GetService("GuiService"):GetScreenSize()
                local windowSize = janela.AbsoluteSize
                
                newX = math.max(0, math.min(newX, screenSize.X - windowSize.X))
                newY = math.max(0, math.min(newY, screenSize.Y - windowSize.Y))
                
                janela.Position = UDim2.new(0, newX, 0, newY)
            end
        end)
    end
end

local function finalizarDrag(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or 
       input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
        if dragConnection then
            dragConnection:Disconnect()
            dragConnection = nil
        end
    end
end

-- CRIAR GUI (COR AMARELA - FUNCIONAL NO CELULAR)
local function criarInterface()
    -- ScreenGui
    gui = Instance.new("ScreenGui")
    gui.Name = "NoobHub"
    gui.Parent = jogador:WaitForChild("PlayerGui")
    gui.ResetOnSpawn = false
    
    -- Janela principal
    janela = Instance.new("Frame")
    janela.Name = "Janela"
    janela.Size = UDim2.new(0, 300, 0, 220)
    janela.Position = UDim2.new(0.5, -150, 0.5, -110)
    janela.BackgroundColor3 = Color3.fromRGB(30, 25, 10)
    janela.BackgroundTransparency = 0.1
    janela.BorderSizePixel = 3
    janela.BorderColor3 = Color3.fromRGB(255, 200, 0)
    janela.ClipsDescendants = true
    janela.Active = true
    janela.Draggable = false
    janela.Parent = gui
    
    -- DRAG EM TODA A JANELA (COM SUPORTE A TOQUE)
    janela.InputBegan:Connect(iniciarDrag)
    janela.InputEnded:Connect(finalizarDrag)
    
    -- Cantos arredondados
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 12)
    corner.Parent = janela
    
    -- Sombra
    local shadow = Instance.new("Frame")
    shadow.Size = UDim2.new(1, 10, 1, 10)
    shadow.Position = UDim2.new(0, -5, 0, -5)
    shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    shadow.BackgroundTransparency = 0.6
    shadow.BorderSizePixel = 0
    shadow.ZIndex = -1
    shadow.Parent = janela
    
    local shadowCorner = Instance.new("UICorner")
    shadowCorner.CornerRadius = UDim.new(0, 14)
    shadowCorner.Parent = shadow
    
    -- BOLINHA (quando minimizado)
    local bolinha = Instance.new("TextButton")
    bolinha.Name = "Bolinha"
    bolinha.Size = UDim2.new(1, 0, 1, 0)
    bolinha.BackgroundTransparency = 1
    bolinha.Text = "⭐"
    bolinha.TextSize = 35
    bolinha.TextColor3 = Color3.fromRGB(255, 255, 255)
    bolinha.TextScaled = true
    bolinha.Visible = false
    bolinha.Parent = janela
    bolinha.MouseButton1Click:Connect(alternarMinimizar)
    bolinha.TouchTap:Connect(alternarMinimizar) -- Suporte celular
    
    -- CORPO DA JANELA
    local corpo = Instance.new("Frame")
    corpo.Name = "Corpo"
    corpo.Size = UDim2.new(1, 0, 1, 0)
    corpo.BackgroundTransparency = 1
    corpo.Parent = janela
    
    -- Barra de título (amarela)
    local tituloBar = Instance.new("Frame")
    tituloBar.Name = "TituloBar"
    tituloBar.Size = UDim2.new(1, 0, 0, 40)
    tituloBar.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
    tituloBar.BackgroundTransparency = 0.2
    tituloBar.Parent = corpo
    
    -- Título
    local titulo = Instance.new("TextLabel")
    titulo.Size = UDim2.new(0.6, 0, 1, 0)
    titulo.Position = UDim2.new(0.05, 0, 0, 0)
    titulo.BackgroundTransparency = 1
    titulo.Text = "⭐ NOOB HUB v5.0"
    titulo.TextColor3 = Color3.fromRGB(255, 200, 0)
    titulo.TextSize = 18
    titulo.TextXAlignment = Enum.TextXAlignment.Left
    titulo.TextYAlignment = Enum.TextYAlignment.Center
    titulo.Font = Enum.Font.GothamBold
    titulo.Parent = tituloBar
    
    -- Botões da barra (amarelos)
    local btnMinimizar = Instance.new("TextButton")
    btnMinimizar.Name = "BtnMinimizar"
    btnMinimizar.Size = UDim2.new(0, 35, 0, 35)
    btnMinimizar.Position = UDim2.new(0.78, 0, 0.06, 0)
    btnMinimizar.Text = "─"
    btnMinimizar.TextSize = 22
    btnMinimizar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnMinimizar.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
    btnMinimizar.BackgroundTransparency = 0.3
    btnMinimizar.BorderSizePixel = 0
    btnMinimizar.Parent = tituloBar
    
    local btnMinCorner = Instance.new("UICorner")
    btnMinCorner.CornerRadius = UDim.new(1, 0)
    btnMinCorner.Parent = btnMinimizar
    btnMinimizar.MouseButton1Click:Connect(alternarMinimizar)
    btnMinimizar.TouchTap:Connect(alternarMinimizar) -- Suporte celular
    
    local btnFechar = Instance.new("TextButton")
    btnFechar.Name = "BtnFechar"
    btnFechar.Size = UDim2.new(0, 35, 0, 35)
    btnFechar.Position = UDim2.new(0.89, 0, 0.06, 0)
    btnFechar.Text = "✕"
    btnFechar.TextSize = 20
    btnFechar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnFechar.BackgroundColor3 = Color3.fromRGB(200, 150, 0)
    btnFechar.BackgroundTransparency = 0.3
    btnFechar.BorderSizePixel = 0
    btnFechar.Parent = tituloBar
    
    local btnCloseCorner = Instance.new("UICorner")
    btnCloseCorner.CornerRadius = UDim.new(1, 0)
    btnCloseCorner.Parent = btnFechar
    btnFechar.MouseButton1Click:Connect(solicitarFechamento)
    btnFechar.TouchTap:Connect(solicitarFechamento) -- Suporte celular
    
    -- Linha divisória (amarela)
    local divider = Instance.new("Frame")
    divider.Size = UDim2.new(0.9, 0, 0, 2)
    divider.Position = UDim2.new(0.05, 0, 0.2, 0)
    divider.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
    divider.BackgroundTransparency = 0.4
    divider.Parent = corpo
    
    -- Status
    local status = Instance.new("TextLabel")
    status.Size = UDim2.new(0.9, 0, 0, 30)
    status.Position = UDim2.new(0.05, 0, 0.28, 0)
    status.BackgroundTransparency = 1
    status.Text = "📌 Clique no botão para ativar"
    status.TextColor3 = Color3.fromRGB(255, 220, 150)
    status.TextSize = 13
    status.TextXAlignment = Enum.TextXAlignment.Left
    status.TextYAlignment = Enum.TextYAlignment.Center
    status.Font = Enum.Font.Gotham
    status.Parent = corpo
    
    -- Info tecla
    local info = Instance.new("TextLabel")
    info.Size = UDim2.new(0.9, 0, 0, 20)
    info.Position = UDim2.new(0.05, 0, 0.42, 0)
    info.BackgroundTransparency = 1
    info.Text = "📱 Toque no botão ou tecla 'G'"
    info.TextColor3 = Color3.fromRGB(200, 180, 100)
    info.TextSize = 12
    info.TextXAlignment = Enum.TextXAlignment.Left
    info.TextYAlignment = Enum.TextYAlignment.Center
    info.Font = Enum.Font.Gotham
    info.Parent = corpo
    
    -- Botão ativar (amarelo)
    local btnAtivar = Instance.new("TextButton")
    btnAtivar.Name = "BtnAtivar"
    btnAtivar.Size = UDim2.new(0.9, 0, 0, 50)
    btnAtivar.Position = UDim2.new(0.05, 0, 0.56, 0)
    btnAtivar.Text = "🔴 DESATIVADO"
    btnAtivar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnAtivar.TextSize = 18
    btnAtivar.BackgroundColor3 = Color3.fromRGB(200, 150, 0)
    btnAtivar.BackgroundTransparency = 0.3
    btnAtivar.BorderSizePixel = 2
    btnAtivar.BorderColor3 = Color3.fromRGB(255, 200, 0)
    btnAtivar.Parent = corpo
    
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 8)
    btnCorner.Parent = btnAtivar
    btnAtivar.MouseButton1Click:Connect(alternarVisibilidade)
    btnAtivar.TouchTap:Connect(alternarVisibilidade) -- Suporte celular
    
    -- Footer (amarelo)
    local footer = Instance.new("TextLabel")
    footer.Size = UDim2.new(0.9, 0, 0, 20)
    footer.Position = UDim2.new(0.05, 0, 0.88, 0)
    footer.BackgroundTransparency = 1
    footer.Text = "✦ NOOB HUB v5.0 ✦"
    footer.TextColor3 = Color3.fromRGB(255, 200, 0)
    footer.TextSize = 10
    footer.TextXAlignment = Enum.TextXAlignment.Right
    footer.TextYAlignment = Enum.TextYAlignment.Bottom
    footer.Font = Enum.Font.GothamBold
    footer.Parent = corpo
    
    -- Efeito de brilho nas bordas (amarelo)
    local glow = Instance.new("Frame")
    glow.Size = UDim2.new(1, 20, 1, 20)
    glow.Position = UDim2.new(0, -10, 0, -10)
    glow.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
    glow.BackgroundTransparency = 0.9
    glow.BorderSizePixel = 0
    glow.ZIndex = -1
    glow.Parent = janela
    
    local glowCorner = Instance.new("UICorner")
    glowCorner.CornerRadius = UDim.new(0, 16)
    glowCorner.Parent = glow
end

-- Sistema de invisibilidade
local function sistemaInvisibilidade()
    while task.wait() do
        if ativo and torso and humanoide then
            local cfOriginal = torso.CFrame
            local camOffsetOriginal = humanoide.CameraOffset
            
            local cfNovo = cfOriginal * CFrame.new(0, -200000, 0)
            torso.CFrame = cfNovo
            humanoide.CameraOffset = cfNovo:ToObjectSpace(CFrame.new(cfOriginal.Position)).Position
            
            game:GetService("RunService").RenderStepped:Wait()
            
            torso.CFrame = cfOriginal
            humanoide.CameraOffset = camOffsetOriginal
        end
    end
end

-- Tecla 'G' (PC)
local function teclaAtalho()
    local mouse = jogador:GetMouse()
    return mouse.KeyDown:Connect(function(tecla)
        if tecla == "g" then
            alternarVisibilidade()
        end
    end)
end

-- Reconectar
local function reconectarPersonagem()
    return jogador.CharacterAdded:Connect(function()
        ativo = false
        local btn = janela and janela:FindFirstChild("BtnAtivar")
        if btn then
            btn.Text = "🔴 DESATIVADO"
            btn.BackgroundColor3 = Color3.fromRGB(200, 150, 0)
        end
        coletarPartes()
    end)
end

-- INICIALIZAR
coletarPartes()
criarInterface()

local threadInvis = coroutine.create(sistemaInvisibilidade)
coroutine.resume(threadInvis)

local atalho = teclaAtalho()
local reconectar = reconectarPersonagem()

_G.NoobHub = {
    threadInvis,
    atalho,
    reconectar,
    function()
        if timerFechar then
            timerFechar:Disconnect()
            timerFechar = nil
        end
        if dragConnection then
            dragConnection:Disconnect()
            dragConnection = nil
        end
    end
}

print("⭐ NOOB HUB v5.0 CARREGADO!")
print("⭐ Drag CORRIGIDO para CELULAR e PC!")
print("⭐ Arraste tocando em QUALQUER lugar da janela")
print("⭐ Clique em '─' para minimizar")
print("⭐ Clique em '✕' 2x para fechar")
print("⭐ Tecla 'G' ou toque no botão para ativar")
