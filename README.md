--[[
    SCRIPT: SrNoobHub - Invisibilidade (Server)
    LOCAL: ServerScriptService
    AUTOR: SrNoob
]]

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Criando RemoteEvent para comunicação
local remoteEvent = Instance.new("RemoteEvent")
remoteEvent.Name = "ToggleInvisibility"
remoteEvent.Parent = ReplicatedStorage

-- Variáveis para controlar quem está invisível
local invisiblePlayers = {}

-- Função para tornar um jogador invisível
local function setInvisible(player, isInvisible)
    local character = player.Character
    if not character then return end
    
    if isInvisible then
        -- Salva a transparência original de cada parte
        if not invisiblePlayers[player.UserId] then
            invisiblePlayers[player.UserId] = {}
        end
        
        -- Torna todas as partes do personagem invisíveis
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                -- Salva transparência original se não foi salva ainda
                if not invisiblePlayers[player.UserId][part] then
                    invisiblePlayers[player.UserId][part] = part.Transparency
                end
                part.Transparency = 1
                
                -- Torna também os acessórios invisíveis
                for _, accessory in pairs(character:GetChildren()) do
                    if accessory:IsA("Accessory") or accessory:IsA("Hat") then
                        accessory.Handle.Transparency = 1
                    end
                end
            end
        end
        
        -- Remove o colisor para passar através de objetos
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
        
        print(player.Name .. " ficou invisível!")
        
    else
        -- Restaura a transparência original
        if invisiblePlayers[player.UserId] then
            for part, originalTransparency in pairs(invisiblePlayers[player.UserId]) do
                if part and part.Parent then
                    part.Transparency = originalTransparency
                    part.CanCollide = true
                end
            end
            invisiblePlayers[player.UserId] = nil
        end
        
        -- Restaura acessórios
        if character then
            for _, accessory in pairs(character:GetChildren()) do
                if accessory:IsA("Accessory") or accessory:IsA("Hat") then
                    if accessory.Handle then
                        accessory.Handle.Transparency = 0
                    end
                end
            end
        end
        
        print(player.Name .. " ficou visível!")
    end
end

-- Função para verificar se o jogador está invisível
local function isPlayerInvisible(player)
    return invisiblePlayers[player.UserId] ~= nil
end

-- Evento quando o jogador entra
Players.PlayerAdded:Connect(function(player)
    -- Criar pasta para dados do jogador
    local playerData = Instance.new("Folder")
    playerData.Name = "PlayerData"
    playerData.Parent = player
    
    -- Criar BoolValue para estado de invisibilidade
    local invisState = Instance.new("BoolValue")
    invisState.Name = "IsInvisible"
    invisState.Value = false
    invisState.Parent = playerData
    
    -- Criar IntValue para transparência original (backup)
    local transBackup = Instance.new("IntValue")
    transBackup.Name = "TransparencyBackup"
    transBackup.Value = 0
    transBackup.Parent = playerData
end)

-- Evento quando o personagem carrega
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        -- Se o jogador estava invisível antes de morrer, torna invisível novamente
        task.wait(0.5) -- Aguarda o personagem carregar completamente
        if player:FindFirstChild("PlayerData") and player.PlayerData:FindFirstChild("IsInvisible") then
            if player.PlayerData.IsInvisible.Value then
                setInvisible(player, true)
            end
        end
    end)
end)

-- RemoteEvent para alternar invisibilidade
remoteEvent.OnServerEvent:Connect(function(player)
    if not player then return end
    
    -- Verifica se o jogador tem a pasta de dados
    local playerData = player:FindFirstChild("PlayerData")
    if not playerData then return end
    
    local invisState = playerData:FindFirstChild("IsInvisible")
    if not invisState then return end
    
    -- Alterna o estado
    local newState = not invisState.Value
    invisState.Value = newState
    
    -- Aplica ou remove invisibilidade
    setInvisible(player, newState)
    
    -- Notifica o jogador
    local message = newState and "Você está INVISÍVEL!" or "Você está VISÍVEL!"
    local color = newState and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
    
    -- Envia notificação de volta
    local notificationEvent = ReplicatedStorage:FindFirstChild("SendNotification")
    if notificationEvent then
        notificationEvent:FireClient(player, message, color)
    end
end)

-- Função para verificar se alguém está invisível (para outros scripts)
function getInvisiblePlayers()
    return invisiblePlayers
end

print("Sistema de Invisibilidade carregado!")--[[
    SCRIPT: SrNoobHub - Invisibilidade (Client)
    LOCAL: StarterPlayerScripts
]]

local player = game.Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Criando RemoteEvent para notificações
local notificationEvent = Instance.new("RemoteEvent")
notificationEvent.Name = "SendNotification"
notificationEvent.Parent = ReplicatedStorage

-- Função para criar notificação na GUI
local function showNotification(message, color)
    -- Procura a GUI do SrNoobHub
    local gui = player.PlayerGui:FindFirstChild("SrNoobHub")
    if not gui then return end
    
    local mainFrame = gui:FindFirstChild("MainFrame")
    if not mainFrame then return end
    
    -- Cria uma notificação temporária
    local notif = Instance.new("TextLabel")
    notif.Size = UDim2.new(1, 0, 0, 30)
    notif.Position = UDim2.new(0, 0, 0, 120)
    notif.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    notif.BackgroundTransparency = 0.5
    notif.Text = message
    notif.TextColor3 = color or Color3.fromRGB(255, 255, 255)
    notif.TextScaled = true
    notif.Font = Enum.Font.GothamBold
    notif.Parent = mainFrame
    
    -- Remove após 2 segundos
    task.wait(2)
    notif:Destroy()
end

-- Evento para receber notificações do servidor
notificationEvent.OnClientEvent:Connect(function(message, color)
    showNotification(message, color)
end)

-- Criando botão na GUI (se não existir)
local function createInvisibilityButton()
    local gui = player.PlayerGui:FindFirstChild("SrNoobHub")
    if not gui then return end
    
    local mainFrame = gui:FindFirstChild("MainFrame")
    if not mainFrame then return end
    
    -- Verifica se o botão já existe
    if mainFrame:FindFirstChild("InvisBtn") then return end
    
    -- Cria o botão de invisibilidade
    local invisBtn = Instance.new("TextButton")
    invisBtn.Name = "InvisBtn"
    invisBtn.Size = UDim2.new(0, 100, 0, 30)
    invisBtn.Position = UDim2.new(0.5, -50, 0, 50) -- Mesma posição do aimbot, substitui
    invisBtn.BackgroundColor3 = Color3.fromRGB(0, 0, 255)
    invisBtn.Text = "INVISÍVEL: OFF"
    invisBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    invisBtn.TextScaled = true
    invisBtn.Font = Enum.Font.GothamBold
    invisBtn.Parent = mainFrame
    
    -- Remove o botão antigo do aimbot (se quiser substituir)
    local oldBtn = mainFrame:FindFirstChild("ToggleBtn")
    if oldBtn then
        oldBtn:Destroy()
    end
    
    local isInvisible = false
    
    invisBtn.MouseButton1Click:Connect(function()
        -- Alterna invisibilidade
        local remote = ReplicatedStorage:FindFirstChild("ToggleInvisibility")
        if remote then
            remote:FireServer()
            
            -- Feedback visual local
            isInvisible = not isInvisible
            if isInvisible then
                invisBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
                invisBtn.Text = "INVISÍVEL: ON"
            else
                invisBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
                invisBtn.Text = "INVISÍVEL: OFF"
            end
        end
    end)
end

-- Aguarda a GUI carregar
task.wait(1)
createInvisibilityButton()

-- Se quiser manter ambos os botões (aimbot e invisibilidade)
local function createBothButtons()
    local gui = player.PlayerGui:FindFirstChild("SrNoobHub")
    if not gui then return end
    
    local mainFrame = gui:FindFirstChild("MainFrame")
    if not mainFrame then return end
    
    -- Botão Aimbot (posição original)
    local aimBtn = Instance.new("TextButton")
    aimBtn.Name = "AimBtn"
    aimBtn.Size = UDim2.new(0, 90, 0, 25)
    aimBtn.Position = UDim2.new(0.05, 0, 0, 50)
    aimBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    aimBtn.Text = "AIM: ON"
    aimBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    aimBtn.TextScaled = true
    aimBtn.Font = Enum.Font.GothamBold
    aimBtn.Parent = mainFrame
    
    -- Botão Invisibilidade
    local invisBtn = Instance.new("TextButton")
    invisBtn.Name = "InvisBtn"
    invisBtn.Size = UDim2.new(0, 90, 0, 25)
    invisBtn.Position = UDim2.new(0.52, 0, 0, 50)
    invisBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    invisBtn.Text = "INV: OFF"
    invisBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    invisBtn.TextScaled = true
    invisBtn.Font = Enum.Font.GothamBold
    invisBtn.Parent = mainFrame
    
    -- Funções dos botões
    local aimActive = true
    aimBtn.MouseButton1Click:Connect(function()
        aimActive = not aimActive
        aimBtn.BackgroundColor3 = aimActive and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
        aimBtn.Text = aimActive and "AIM: ON" or "AIM: OFF"
        
        -- Atualiza o aimbot (precisa modificar o script original)
        _G.AimbotActive = aimActive
    end)
    
    local invisActive = false
    invisBtn.MouseButton1Click:Connect(function()
        local remote = ReplicatedStorage:FindFirstChild("ToggleInvisibility")
        if remote then
            remote:FireServer()
            invisActive = not invisActive
            invisBtn.BackgroundColor3 = invisActive and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
            invisBtn.Text = invisActive and "INV: ON" or "INV: OFF"
        end
    end)
    
    -- Remove o botão antigo (se existir)
    local oldBtn = mainFrame:FindFirstChild("ToggleBtn")
    if oldBtn then
        oldBtn:Destroy()
    end
end

-- Substitua a função createInvisibilityButton por esta se quiser ambos os botões
-- createBothButtons()
