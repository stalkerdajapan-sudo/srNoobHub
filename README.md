--[[
    NoobHub - Auto Bond para Dead Rails
    Ativa Remove Event do Bond sem teleportar
    Verifica se é Bond e pega naturalmente
]]

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NoobHub"
ScreenGui.Parent = Player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- Criar Frame principal (CENTRALIZADO)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 300, 0, 200)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -100) -- CENTRO DA TELA
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

-- Barra de título
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 35)
TitleBar.BackgroundColor3 = Color3.fromRGB(255, 180, 0)
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 10)
TitleCorner.Parent = TitleBar

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(0.7, 0, 1, 0)
Title.Position = UDim2.new(0.05, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "⚡ NoobHub"
Title.TextColor3 = Color3.fromRGB(20, 20, 20)
Title.TextSize = 16
Title.Font = Enum.Font.GothamBlack
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TitleBar

-- Botão minimizar
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Size = UDim2.new(0, 25, 0, 25)
MinimizeButton.Position = UDim2.new(0.87, 0, 0.13, 0)
MinimizeButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Text = "—"
MinimizeButton.TextColor3 = Color3.fromRGB(255, 180, 0)
MinimizeButton.TextSize = 16
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Parent = TitleBar

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 5)
MinCorner.Parent = MinimizeButton

-- Status Label
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(0.9, 0, 0, 25)
StatusLabel.Position = UDim2.new(0.05, 0, 0.25, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "Status: 🔴 Desativado"
StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
StatusLabel.TextSize = 13
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.Parent = MainFrame

-- Label de Bonds no mapa
local BondsOnMapLabel = Instance.new("TextLabel")
BondsOnMapLabel.Size = UDim2.new(0.9, 0, 0, 25)
BondsOnMapLabel.Position = UDim2.new(0.05, 0, 0.4, 0)
BondsOnMapLabel.BackgroundTransparency = 1
BondsOnMapLabel.Text = "💵 Bonds no Mapa: 0"
BondsOnMapLabel.TextColor3 = Color3.fromRGB(255, 255, 100)
BondsOnMapLabel.TextSize = 13
BondsOnMapLabel.Font = Enum.Font.GothamBold
BondsOnMapLabel.TextXAlignment = Enum.TextXAlignment.Left
BondsOnMapLabel.Parent = MainFrame

-- Label de Bonds coletados
local BondsCollectedLabel = Instance.new("TextLabel")
BondsCollectedLabel.Size = UDim2.new(0.9, 0, 0, 25)
BondsCollectedLabel.Position = UDim2.new(0.05, 0, 0.55, 0)
BondsCollectedLabel.BackgroundTransparency = 1
BondsCollectedLabel.Text = "✅ Bonds Coletados: 0"
BondsCollectedLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
BondsCollectedLabel.TextSize = 13
BondsCollectedLabel.Font = Enum.Font.GothamBold
BondsCollectedLabel.TextXAlignment = Enum.TextXAlignment.Left
BondsCollectedLabel.Parent = MainFrame

-- Botão Auto Bond
local AutoBondButton = Instance.new("TextButton")
AutoBondButton.Size = UDim2.new(0.9, 0, 0, 35)
AutoBondButton.Position = UDim2.new(0.05, 0, 0.72, 0)
AutoBondButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
AutoBondButton.BorderSizePixel = 0
AutoBondButton.Text = "🎯 ATIVAR AUTO BOND"
AutoBondButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoBondButton.TextSize = 12
AutoBondButton.Font = Enum.Font.GothamBold
AutoBondButton.Parent = MainFrame

local AutoBondCorner = Instance.new("UICorner")
AutoBondCorner.CornerRadius = UDim.new(0, 5)
AutoBondCorner.Parent = AutoBondButton

-- Variáveis
local isAutoBondActive = false
local isMinimized = false
local originalSize = MainFrame.Size
local bondsCollected = 0
local autoBondConnections = {}
local processedBonds = {}

-- Função para verificar se é um Bond
local function isBond(obj)
    if not obj or not obj.Parent then return false end
    
    local objName = obj.Name:lower()
    local parentName = obj.Parent.Name:lower()
    local grandparentName = obj.Parent.Parent and obj.Parent.Parent.Name:lower() or ""
    
    -- Verificar por nomes conhecidos
    local bondNames = {
        "bond", "bring", "dinheiro", "money", "cash", 
        "dollar", "nota", "cedula", "coin", "bill",
        "dinheiro_", "nota_", "money_", "cash_", "bond_"
    }
    
    for _, name in pairs(bondNames) do
        if objName:find(name) or parentName:find(name) or grandparentName:find(name) then
            return true
        end
    end
    
    -- Verificar se é um RemotesEvent relacionado a coleta
    if obj:IsA("RemoteEvent") then
        local eventName = objName:lower()
        if eventName:find("collect") or 
           eventName:find("pick") or 
           eventName:find("grab") or 
           eventName:find("get") or 
           eventName:find("remove") or 
           eventName:find("bond") or
           eventName:find("money") or
           eventName:find("bring") then
            return true
        end
    end
    
    -- Verificar se é ProximityPrompt ou ClickDetector
    if obj:IsA("ProximityPrompt") or obj:IsA("ClickDetector") then
        if objName:find("bond") or 
           objName:find("bring") or 
           objName:find("pegar") or 
           objName:find("coletar") or 
           objName:find("collect") or
           parentName:find("bond") or
           parentName:find("bring") then
            return true
        end
    end
    
    -- Verificar se é uma parte com TouchInterest
    if obj:IsA("BasePart") and obj:FindFirstChild("TouchInterest") then
        if objName:find("bond") or 
           objName:find("bring") or 
           objName:find("money") or
           objName:find("cash") or
           parentName:find("bond") or
           parentName:find("bring") then
            return true
        end
    end
    
    return false
end

-- Função para encontrar e ativar RemotesEvents de coleta
local function findAndActivateCollectionEvents()
    local collectionEvents = {}
    
    -- Procurar RemotesEvents no workspace e em serviços
    local searchPlaces = {
        Workspace,
        game:GetService("ReplicatedStorage"),
        game:GetService("ReplicatedFirst"),
        game:GetService("Players")
    }
    
    for _, place in pairs(searchPlaces) do
        for _, obj in pairs(place:GetDescendants()) do
            if obj:IsA("RemoteEvent") then
                local eventName = obj.Name:lower()
                if eventName:find("collect") or 
                   eventName:find("pick") or 
                   eventName:find("grab") or 
                   eventName:find("get") or 
                   eventName:find("remove") or 
                   eventName:find("bond") or
                   eventName:find("money") or
                   eventName:find("bring") or
                   eventName:find("take") then
                    table.insert(collectionEvents, obj)
                end
            end
        end
    end
    
    return collectionEvents
end

-- Função para ativar RemotesEvents de coleta
local function activateCollectionEvents()
    local events = findAndActivateCollectionEvents()
    local character = Player.Character
    
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    
    for _, event in pairs(events) do
        pcall(function()
            -- Tentar ativar o evento de diferentes formas
            event:FireServer()
            
            -- Tentar com argumentos
            event:FireServer(rootPart)
            event:FireServer(character)
            event:FireServer(rootPart.Position)
            
            -- Tentar com o Bond mais próximo
            local nearestBond = findNearestBond()
            if nearestBond then
                event:FireServer(nearestBond)
                event:FireServer(nearestBond, rootPart)
            end
        end)
    end
end

-- Função para encontrar Bond mais próximo
local function findNearestBond()
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return nil
    end
    
    local rootPart = character.HumanoidRootPart
    local nearestBond = nil
    local nearestDistance = math.huge
    
    for _, obj in pairs(Workspace:GetDescendants()) do
        if isBond(obj) then
            local bondPosition = nil
            
            if obj:IsA("BasePart") then
                bondPosition = obj.Position
            elseif obj:IsA("Model") and obj.PrimaryPart then
                bondPosition = obj.PrimaryPart.Position
            elseif obj.Parent and obj.Parent:IsA("BasePart") then
                bondPosition = obj.Parent.Position
            end
            
            if bondPosition then
                local distance = (rootPart.Position - bondPosition).Magnitude
                if distance < nearestDistance then
                    nearestDistance = distance
                    nearestBond = obj
                end
            end
        end
    end
    
    return nearestBond
end

-- Função para pegar Bond sem teleportar
local function collectBondWithoutTeleport(bond)
    if not isAutoBondActive then return end
    
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    local humanoid = character:FindFirstChild("Humanoid")
    
    -- Ativar RemotesEvents de coleta
    local events = findAndActivateCollectionEvents()
    for _, event in pairs(events) do
        pcall(function()
            event:FireServer(bond)
            event:FireServer(bond, rootPart)
            event:FireServer(bond, character)
        end)
    end
    
    -- Ativar ProximityPrompt sem teleportar
    if bond:IsA("ProximityPrompt") then
        pcall(function()
            bond:InputHoldBegin()
            task.wait(0.3)
            bond:InputHoldEnd()
            fireproximityprompt(bond)
        end)
    end
    
    -- Procurar ProximityPrompt nos filhos
    if bond.Parent then
        for _, child in pairs(bond.Parent:GetDescendants()) do
            if child:IsA("ProximityPrompt") then
                pcall(function()
                    child:InputHoldBegin()
                    task.wait(0.3)
                    child:InputHoldEnd()
                    fireproximityprompt(child)
                end)
            end
            if child:IsA("ClickDetector") then
                pcall(function()
                    fireclickdetector(child)
                end)
            end
        end
    end
    
    -- Ativar TouchInterest sem teleportar
    if bond:IsA("BasePart") and bond:FindFirstChild("TouchInterest") then
        firetouchinterest(rootPart, bond, 0)
        firetouchinterest(rootPart, bond, 1)
    end
    
    -- Marcar como processado
    processedBonds[tostring(bond)] = true
    bondsCollected = bondsCollected + 1
    BondsCollectedLabel.Text = "✅ Bonds Coletados: " .. bondsCollected
    
    print("💵 Bond ativado! Total: " .. bondsCollected)
end

-- Função para contar Bonds no mapa
local function countBondsOnMap()
    local count = 0
    
    for _, obj in pairs(Workspace:GetDescendants()) do
        if isBond(obj) and not processedBonds[tostring(obj)] then
            count = count + 1
        end
    end
    
    return count
end

-- Função para iniciar Auto Bond
local function startAutoBond()
    if isAutoBondActive then return end
    
    isAutoBondActive = true
    StatusLabel.Text = "Status: 🟢 Ativado"
    StatusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
    AutoBondButton.Text = "🎯 DESATIVAR AUTO BOND"
    AutoBondButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    
    print("🎯 Auto Bond ATIVADO!")
    print("💵 Ativando RemotesEvents de coleta...")
    
    -- Loop principal
    autoBondConnections[#autoBondConnections + 1] = RunService.Heartbeat:Connect(function()
        if isAutoBondActive then
            -- Atualizar contagem
            local bondCount = countBondsOnMap()
            BondsOnMapLabel.Text = "💵 Bonds no Mapa: " .. bondCount
            
            -- Ativar eventos de coleta
            activateCollectionEvents()
            
            -- Pegar Bonds sem teleportar
            for _, obj in pairs(Workspace:GetDescendants()) do
                if isAutoBondActive and isBond(obj) and not processedBonds[tostring(obj)] then
                    collectBondWithoutTeleport(obj)
                end
            end
        end
    end)
    
    -- Monitorar novos Bonds
    autoBondConnections[#autoBondConnections + 1] = Workspace.DescendantAdded:Connect(function(descendant)
        if isAutoBondActive and isBond(descendant) and not processedBonds[tostring(descendant)] then
            task.wait(0.3)
            if isAutoBondActive then
                collectBondWithoutTeleport(descendant)
            end
        end
    end)
end

-- Função para parar Auto Bond
local function stopAutoBond()
    if not isAutoBondActive then return end
    
    isAutoBondActive = false
    StatusLabel.Text = "Status: 🔴 Desativado"
    StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
    AutoBondButton.Text = "🎯 ATIVAR AUTO BOND"
    AutoBondButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    
    print("🎯 Auto Bond DESATIVADO!")
    print("✅ Total de Bonds coletados: " .. bondsCollected)
    
    for _, conn in pairs(autoBondConnections) do
        conn:Disconnect()
    end
    autoBondConnections = {}
end

-- Toggle Auto Bond
AutoBondButton.MouseButton1Click:Connect(function()
    if isAutoBondActive then
        stopAutoBond()
    else
        startAutoBond()
    end
end)

-- Minimizar
MinimizeButton.MouseButton1Click:Connect(function()
    if isMinimized then
        MainFrame.Size = originalSize
        StatusLabel.Visible = true
        BondsOnMapLabel.Visible = true
        BondsCollectedLabel.Visible = true
        AutoBondButton.Visible = true
        MinimizeButton.Text = "—"
        isMinimized = false
    else
        MainFrame.Size = UDim2.new(0, 300, 0, 35)
        StatusLabel.Visible = false
        BondsOnMapLabel.Visible = false
        BondsCollectedLabel.Visible = false
        AutoBondButton.Visible = false
        MinimizeButton.Text = "+"
        isMinimized = true
    end
end)

-- Limpar quando destruir
ScreenGui.Destroying:Connect(function()
    stopAutoBond()
end)

-- Reconectar quando morrer
Player.CharacterAdded:Connect(function(character)
    if isAutoBondActive then
        task.wait(2)
        print("🎯 Personagem renasceu, continuando Auto Bond...")
        processedBonds = {}
    end
end)

print("⚡ NoobHub - Auto Bond sem Teleporte carregado!")
print("🎯 Ativa RemotesEvents de coleta sem teleportar!")
print("💵 Verifica e pega Bonds naturalmente!")
