--[[
    NoobHub - Auto Bond para Dead Rails
    Clica no botão de pegar em todos Bonds
    Mostra quantidade de Bonds no mapa em tempo real
]]

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local VirtualInputManager = game:GetService("VirtualInputManager")

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NoobHub"
ScreenGui.Parent = Player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- Criar Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 250, 0, 180)
MainFrame.Position = UDim2.new(0.5, -125, 0.1, -90)
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
StatusLabel.Position = UDim2.new(0.05, 0, 0.28, 0)
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
BondsOnMapLabel.Position = UDim2.new(0.05, 0, 0.45, 0)
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
BondsCollectedLabel.Position = UDim2.new(0.05, 0, 0.62, 0)
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
AutoBondButton.Position = UDim2.new(0.05, 0, 0.78, 0)
AutoBondButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
AutoBondButton.BorderSizePixel = 0
AutoBondButton.Text = "🎯 ATIVAR AUTO CLICK"
AutoBondButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoBondButton.TextSize = 12
AutoBondButton.Font = Enum.Font.GothamBold
AutoBondButton.Parent = MainFrame

local AutoBondCorner = Instance.new("UICorner")
AutoBondCorner.CornerRadius = UDim.new(0, 5)
AutoBondCorner.Parent = AutoBondButton

-- Variáveis
local isAutoClickActive = false
local isMinimized = false
local originalSize = MainFrame.Size
local bondsCollected = 0
local autoClickConnections = {}
local processedBonds = {} -- Lista de Bonds já clicados

-- Função para detectar todos Bonds no mapa
local function detectAllBonds()
    local bondButtons = {}
    
    -- Procurar por ProximityPrompts que são botões de Bond
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("ProximityPrompt") then
            local parent = obj.Parent
            if parent then
                local parentName = parent.Name:lower()
                local promptName = obj.Name:lower()
                
                if parentName:find("bond") or 
                   parentName:find("bring") or 
                   parentName:find("dinheiro") or 
                   parentName:find("money") or
                   parentName:find("cash") or
                   parentName:find("dollar") or
                   parentName:find("nota") or
                   parentName:find("cedula") or
                   parentName:find("real") or
                   parentName:find("reais") or
                   promptName:find("bond") or
                   promptName:find("bring") or
                   promptName:find("pegar") or
                   promptName:find("coletar") or
                   promptName:find("collect") then
                    
                    -- Verificar se não foi processado
                    if not processedBonds[obj] then
                        table.insert(bondButtons, {
                            prompt = obj,
                            parent = parent
                        })
                    end
                end
            end
        end
        
        -- Procurar por ClickDetectors
        if obj:IsA("ClickDetector") then
            local parent = obj.Parent
            if parent then
                local parentName = parent.Name:lower()
                
                if parentName:find("bond") or 
                   parentName:find("bring") or 
                   parentName:find("dinheiro") or 
                   parentName:find("money") or
                   parentName:find("cash") or
                   parentName:find("dollar") then
                    
                    if not processedBonds[obj] then
                        table.insert(bondButtons, {
                            prompt = nil,
                            clickDetector = obj,
                            parent = parent
                        })
                    end
                end
            end
        end
    end
    
    return bondButtons
end

-- Função para contar Bonds no mapa
local function countBondsOnMap()
    local count = 0
    
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("ProximityPrompt") or obj:IsA("ClickDetector") then
            local parent = obj.Parent
            if parent then
                local parentName = parent.Name:lower()
                local objName = obj.Name:lower()
                
                if parentName:find("bond") or 
                   parentName:find("bring") or 
                   parentName:find("dinheiro") or 
                   parentName:find("money") or
                   parentName:find("cash") or
                   parentName:find("dollar") or
                   parentName:find("nota") or
                   parentName:find("cedula") or
                   parentName:find("real") or
                   parentName:find("reais") or
                   objName:find("bond") or
                   objName:find("bring") or
                   objName:find("pegar") or
                   objName:find("coletar") or
                   objName:find("collect") then
                    count = count + 1
                end
            end
        end
    end
    
    return count
end

-- Função para clicar no botão de pegar
local function clickBondButton(bondData)
    if not isAutoClickActive then return end
    
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    local humanoid = character:FindFirstChild("Humanoid")
    
    -- Encontrar posição do Bond
    local bondPosition = nil
    
    if bondData.parent:IsA("BasePart") then
        bondPosition = bondData.parent.Position
    elseif bondData.parent:IsA("Model") then
        local primaryPart = bondData.parent.PrimaryPart
        if primaryPart then
            bondPosition = primaryPart.Position
        else
            for _, child in pairs(bondData.parent:GetChildren()) do
                if child:IsA("BasePart") then
                    bondPosition = child.Position
                    break
                end
            end
        end
    end
    
    if not bondPosition then return end
    
    -- Teleportar para perto do Bond (mas mantendo distância para o prompt aparecer)
    local teleportPosition = bondPosition + Vector3.new(0, 2, 5)
    rootPart.CFrame = CFrame.new(teleportPosition, bondPosition)
    
    task.wait(0.3)
    
    -- Tentar ativar ProximityPrompt
    if bondData.prompt then
        pcall(function()
            -- Simular segurar o prompt
            bondData.prompt:InputHoldBegin()
            task.wait(0.5)
            bondData.prompt:InputHoldEnd()
            
            -- Tentar trigger direto
            fireproximityprompt(bondData.prompt)
        end)
    end
    
    -- Tentar clicar no ClickDetector
    if bondData.clickDetector then
        pcall(function()
            fireclickdetector(bondData.clickDetector)
        end)
    end
    
    -- Tentar clicar com o mouse
    if bondData.parent:IsA("BasePart") then
        -- Posicionar câmera no Bond
        local camera = Workspace.CurrentCamera
        if camera then
            camera.CFrame = CFrame.new(teleportPosition, bondPosition)
        end
        
        -- Simular clique
        VirtualInputManager:SendMouseButtonEvent(
            0,
            0,
            0,
            true,
            game:GetService("UserInputService"):GetMouseLocation(),
            0
        )
        
        task.wait(0.1)
        
        VirtualInputManager:SendMouseButtonEvent(
            0,
            0,
            0,
            false,
            game:GetService("UserInputService"):GetMouseLocation(),
            0
        )
    end
    
    -- Marcar como processado
    processedBonds[bondData.prompt or bondData.clickDetector or bondData.parent] = true
    
    bondsCollected = bondsCollected + 1
    BondsCollectedLabel.Text = "✅ Bonds Coletados: " .. bondsCollected
    
    print("💵 Bond coletado! Total: " .. bondsCollected)
    
    task.wait(0.5)
end

-- Função para iniciar Auto Click
local function startAutoClick()
    if isAutoClickActive then return end
    
    isAutoClickActive = true
    StatusLabel.Text = "Status: 🟢 Ativado"
    StatusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
    AutoBondButton.Text = "🎯 DESATIVAR AUTO CLICK"
    AutoBondButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    
    print("🎯 Auto Click ATIVADO!")
    print("💰 Clicando em todos Bonds automaticamente...")
    
    -- Loop principal para atualizar contagem e clicar
    autoClickConnections[#autoClickConnections + 1] = RunService.Heartbeat:Connect(function()
        if isAutoClickActive then
            -- Atualizar contagem de Bonds no mapa
            local bondCount = countBondsOnMap()
            BondsOnMapLabel.Text = "💵 Bonds no Mapa: " .. bondCount
            
            -- Detectar e clicar em Bonds
            local bonds = detectAllBonds()
            
            for _, bondData in pairs(bonds) do
                if isAutoClickActive then
                    clickBondButton(bondData)
                end
            end
        end
    end)
    
    -- Monitorar novos Bonds
    autoClickConnections[#autoClickConnections + 1] = Workspace.DescendantAdded:Connect(function(descendant)
        if isAutoClickActive then
            if descendant:IsA("ProximityPrompt") or descendant:IsA("ClickDetector") then
                local parent = descendant.Parent
                if parent then
                    local parentName = parent.Name:lower()
                    local descName = descendant.Name:lower()
                    
                    if parentName:find("bond") or 
                       parentName:find("bring") or 
                       parentName:find("dinheiro") or 
                       parentName:find("money") or
                       parentName:find("cash") or
                       descName:find("bond") or
                       descName:find("bring") or
                       descName:find("pegar") or
                       descName:find("coletar") then
                        
                        print("💵 Novo Bond detectado!")
                        task.wait(0.5)
                        
                        if isAutoClickActive then
                            local bondData = {
                                prompt = descendant:IsA("ProximityPrompt") and descendant or nil,
                                clickDetector = descendant:IsA("ClickDetector") and descendant or nil,
                                parent = parent
                            }
                            clickBondButton(bondData)
                        end
                    end
                end
            end
        end
    end)
end

-- Função para parar Auto Click
local function stopAutoClick()
    if not isAutoClickActive then return end
    
    isAutoClickActive = false
    StatusLabel.Text = "Status: 🔴 Desativado"
    StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
    AutoBondButton.Text = "🎯 ATIVAR AUTO CLICK"
    AutoBondButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    
    print("🎯 Auto Click DESATIVADO!")
    print("✅ Total de Bonds coletados: " .. bondsCollected)
    
    -- Desconectar loops
    for _, conn in pairs(autoClickConnections) do
        conn:Disconnect()
    end
    autoClickConnections = {}
end

-- Toggle Auto Click
AutoBondButton.MouseButton1Click:Connect(function()
    if isAutoClickActive then
        stopAutoClick()
    else
        startAutoClick()
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
        MainFrame.Size = UDim2.new(0, 250, 0, 35)
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
    stopAutoClick()
end)

-- Reconectar quando morrer
Player.CharacterAdded:Connect(function(character)
    if isAutoClickActive then
        task.wait(2)
        print("🎯 Personagem renasceu, continuando Auto Click...")
        processedBonds = {} -- Resetar Bonds processados
    end
end)

print("⚡ NoobHub - Auto Click para Bonds carregado!")
print("🎯 Sistema de clique automático nos Bonds pronto!")
