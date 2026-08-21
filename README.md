--[[
    NoobHub - Auto Bond para Dead Rails
    Solução Corrigida: Teleporta e pega Bonds
    GUI centralizada na tela
]]

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local TweenService = game:GetService("TweenService")

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

-- Função para detectar Bonds (mais abrangente)
local function detectBonds()
    local bonds = {}
    
    -- Procurar em todo workspace
    for _, obj in pairs(Workspace:GetDescendants()) do
        local isValid = false
        local objName = obj.Name:lower()
        local parentName = obj.Parent and obj.Parent.Name:lower() or ""
        
        -- Verificar por múltiplos critérios
        if obj:IsA("ProximityPrompt") then
            isValid = true
        elseif obj:IsA("ClickDetector") then
            isValid = true
        elseif obj:IsA("Tool") then
            isValid = true
        elseif obj:IsA("BasePart") and (
            objName:find("bond") or
            objName:find("bring") or
            objName:find("money") or
            objName:find("cash") or
            objName:find("nota") or
            objName:find("cedula") or
            objName:find("coin") or
            objName:find("bill")
        ) then
            isValid = true
        end
        
        -- Verificar por nomes específicos
        if objName:find("bond") or 
           objName:find("bring") or 
           objName:find("dinheiro") or 
           objName:find("money") or
           objName:find("cash") or
           objName:find("dollar") or
           objName:find("nota") or
           objName:find("cedula") or
           objName:find("coin") or
           objName:find("bill") or
           parentName:find("bond") or
           parentName:find("bring") or
           parentName:find("money") then
            isValid = true
        end
        
        if isValid and obj ~= Player.Character then
            -- Verificar se não foi processado
            local key = tostring(obj)
            if not processedBonds[key] then
                table.insert(bonds, obj)
            end
        end
    end
    
    return bonds
end

-- Função para pegar Bond (solução corrigida)
local function collectBond(bond)
    if not isAutoBondActive then return end
    
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    local humanoid = character:FindFirstChild("Humanoid")
    
    -- Encontrar posição do Bond
    local bondPosition = nil
    local bondCFrame = nil
    
    if bond:IsA("BasePart") then
        bondPosition = bond.Position
        bondCFrame = bond.CFrame
    elseif bond:IsA("Model") then
        if bond.PrimaryPart then
            bondPosition = bond.PrimaryPart.Position
            bondCFrame = bond.PrimaryPart.CFrame
        else
            for _, child in pairs(bond:GetChildren()) do
                if child:IsA("BasePart") then
                    bondPosition = child.Position
                    bondCFrame = child.CFrame
                    break
                end
            end
        end
    elseif bond.Parent then
        if bond.Parent:IsA("BasePart") then
            bondPosition = bond.Parent.Position
            bondCFrame = bond.Parent.CFrame
        elseif bond.Parent:IsA("Model") and bond.Parent.PrimaryPart then
            bondPosition = bond.Parent.PrimaryPart.Position
            bondCFrame = bond.Parent.PrimaryPart.CFrame
        end
    end
    
    if not bondPosition then return end
    
    -- TELEPORTAR EXATAMENTE EM CIMA DO BOND
    rootPart.CFrame = CFrame.new(bondPosition + Vector3.new(0, 2, 0))
    task.wait(0.3)
    
    -- Método 1: Tocar no Bond
    if bond:IsA("BasePart") then
        if bond:FindFirstChild("TouchInterest") then
            firetouchinterest(rootPart, bond, 0)
            firetouchinterest(rootPart, bond, 1)
        end
        -- Tentar tocar em partes do parent
        if bond.Parent and bond.Parent:IsA("Model") then
            for _, part in pairs(bond.Parent:GetChildren()) do
                if part:IsA("BasePart") and part:FindFirstChild("TouchInterest") then
                    firetouchinterest(rootPart, part, 0)
                    firetouchinterest(rootPart, part, 1)
                end
            end
        end
    end
    
    -- Método 2: Procurar e ativar ProximityPrompt
    local prompts = {}
    if bond:IsA("ProximityPrompt") then
        table.insert(prompts, bond)
    end
    
    -- Procurar prompts no bond e nos filhos
    local targetObject = bond
    if bond.Parent and bond.Parent:IsA("Model") then
        targetObject = bond.Parent
    end
    
    for _, child in pairs(targetObject:GetDescendants()) do
        if child:IsA("ProximityPrompt") then
            table.insert(prompts, child)
        end
    end
    
    -- Ativar todos os prompts encontrados
    for _, prompt in pairs(prompts) do
        pcall(function()
            prompt:InputHoldBegin()
            task.wait(0.3)
            prompt:InputHoldEnd()
            fireproximityprompt(prompt)
        end)
    end
    
    -- Método 3: Clicar em ClickDetectors
    local detectors = {}
    if bond:IsA("ClickDetector") then
        table.insert(detectors, bond)
    end
    
    for _, child in pairs(targetObject:GetDescendants()) do
        if child:IsA("ClickDetector") then
            table.insert(detectors, child)
        end
    end
    
    for _, detector in pairs(detectors) do
        pcall(function()
            fireclickdetector(detector)
        end)
    end
    
    -- Método 4: Tentar equipar se for Tool
    if bond:IsA("Tool") then
        pcall(function()
            humanoid:EquipTool(bond)
            task.wait(0.5)
            humanoid:UnequipTools()
        end)
    end
    
    -- Marcar como processado
    processedBonds[tostring(bond)] = true
    bondsCollected = bondsCollected + 1
    BondsCollectedLabel.Text = "✅ Bonds Coletados: " .. bondsCollected
    
    print("💵 Bond coletado com sucesso! Total: " .. bondsCollected)
    
    task.wait(0.5)
end

-- Função para contar Bonds no mapa
local function countBondsOnMap()
    local bonds = detectBonds()
    return #bonds
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
    print("💵 Teleportando e pegando Bonds...")
    
    -- Loop principal
    autoBondConnections[#autoBondConnections + 1] = RunService.Heartbeat:Connect(function()
        if isAutoBondActive then
            -- Atualizar contagem
            local bondCount = countBondsOnMap()
            BondsOnMapLabel.Text = "💵 Bonds no Mapa: " .. bondCount
            
            -- Pegar Bonds
            local bonds = detectBonds()
            for _, bond in pairs(bonds) do
                if isAutoBondActive then
                    collectBond(bond)
                end
            end
        end
    end)
    
    -- Monitorar novos Bonds
    autoBondConnections[#autoBondConnections + 1] = Workspace.DescendantAdded:Connect(function(descendant)
        if isAutoBondActive then
            task.wait(0.5)
            if isAutoBondActive then
                collectBond(descendant)
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

print("⚡ NoobHub - Auto Bond Corrigido carregado!")
print("🎯 Teleporta e pega Bonds automaticamente!")
