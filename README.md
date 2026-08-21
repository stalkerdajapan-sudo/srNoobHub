--[[
    NoobHub - Auto Bond para Dead Rails
    Detecta e pega Bond da Bring automaticamente
    Com sistema de ativar e desativar
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

-- Criar Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 250, 0, 150)
MainFrame.Position = UDim2.new(0.5, -125, 0.1, -75)
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
StatusLabel.Position = UDim2.new(0.05, 0, 0.3, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "Status: 🔴 Desativado"
StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
StatusLabel.TextSize = 13
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.Parent = MainFrame

-- Bond Count Label
local BondCountLabel = Instance.new("TextLabel")
BondCountLabel.Size = UDim2.new(0.9, 0, 0, 25)
BondCountLabel.Position = UDim2.new(0.05, 0, 0.5, 0)
BondCountLabel.BackgroundTransparency = 1
BondCountLabel.Text = "Bonds Coletados: 0"
BondCountLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
BondCountLabel.TextSize = 13
BondCountLabel.Font = Enum.Font.Gotham
BondCountLabel.TextXAlignment = Enum.TextXAlignment.Left
BondCountLabel.Parent = MainFrame

-- Botão Auto Bond
local AutoBondButton = Instance.new("TextButton")
AutoBondButton.Size = UDim2.new(0.9, 0, 0, 35)
AutoBondButton.Position = UDim2.new(0.05, 0, 0.7, 0)
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
local bondCount = 0
local autoBondConnections = {}
local bondDetected = nil
local collectingBond = false

-- Função para detectar Bond da Bring
local function detectBond()
    local bondItems = {}
    
    -- Procurar por Bonds no workspace
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") or obj:IsA("Model") or obj:IsA("Tool") then
            local name = obj.Name:lower()
            
            -- Diferentes nomes possíveis para Bond
            if name:find("bond") or 
               name:find("bring") or 
               name:find("dinheiro") or
               name:find("money") or
               name:find("cash") or
               name:find("dollar") or
               name:find("nota") or
               name:find("cédula") or
               name:find("cedula") or
               name:find("real") or
               name:find("reais") then
                
                -- Verificar se é um item coletável
                local isCollectable = true
                
                -- Se for Model, verificar se tem partes
                if obj:IsA("Model") then
                    local hasParts = false
                    for _, child in pairs(obj:GetChildren()) do
                        if child:IsA("BasePart") then
                            hasParts = true
                            break
                        end
                    end
                    if not hasParts then
                        isCollectable = false
                    end
                end
                
                if isCollectable then
                    table.insert(bondItems, obj)
                end
            end
        end
    end
    
    -- Também procurar por ProximityPrompts ou ClickDetectors relacionados a Bond
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("ProximityPrompt") or obj:IsA("ClickDetector") then
            local parent = obj.Parent
            if parent then
                local name = parent.Name:lower()
                if name:find("bond") or name:find("bring") or name:find("dinheiro") or name:find("money") then
                    table.insert(bondItems, parent)
                end
            end
        end
    end
    
    return bondItems
end

-- Função para coletar Bond
local function collectBond(bond)
    if collectingBond then return end
    collectingBond = true
    
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        collectingBond = false
        return
    end
    
    local rootPart = character.HumanoidRootPart
    local humanoid = character:FindFirstChild("Humanoid")
    
    -- Encontrar posição do Bond
    local bondPosition = nil
    local bondPart = nil
    
    if bond:IsA("BasePart") then
        bondPosition = bond.Position
        bondPart = bond
    elseif bond:IsA("Model") then
        local primaryPart = bond.PrimaryPart
        if primaryPart then
            bondPosition = primaryPart.Position
            bondPart = primaryPart
        else
            -- Procurar primeira parte
            for _, child in pairs(bond:GetChildren()) do
                if child:IsA("BasePart") then
                    bondPosition = child.Position
                    bondPart = child
                    break
                end
            end
        end
    end
    
    if not bondPosition then
        collectingBond = false
        return
    end
    
    -- Teleportar para o Bond
    local teleportPosition = bondPosition + Vector3.new(0, 3, 0)
    rootPart.CFrame = CFrame.new(teleportPosition)
    
    -- Tentar pegar o Bond
    task.wait(0.5)
    
    -- Tentar diferentes métodos de coleta
    if bond:IsA("Tool") then
        -- Pegar Tool
        humanoid:EquipTool(bond)
    end
    
    if bondPart then
        -- Tentar tocar no Bond
        local touchPart = Instance.new("Part")
        touchPart.Size = Vector3.new(1, 1, 1)
        touchPart.Position = bondPosition
        touchPart.Anchored = true
        touchPart.CanCollide = false
        touchPart.Transparency = 1
        touchPart.Parent = Workspace
        
        -- Simular toque
        if bondPart:FindFirstChild("TouchInterest") then
            firetouchinterest(rootPart, bondPart, 0)
            firetouchinterest(rootPart, bondPart, 1)
        end
        
        touchPart:Destroy()
    end
    
    -- Procurar por ProximityPrompt
    local proximityPrompt = bond:FindFirstChild("ProximityPrompt")
    if proximityPrompt then
        proximityPrompt:InputHoldBegin()
        task.wait(0.5)
        proximityPrompt:InputHoldEnd()
    end
    
    -- Procurar por ClickDetector
    local clickDetector = bond:FindFirstChild("ClickDetector")
    if clickDetector then
        fireclickdetector(clickDetector)
    end
    
    bondCount = bondCount + 1
    BondCountLabel.Text = "Bonds Coletados: " .. bondCount
    
    task.wait(0.5)
    collectingBond = false
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
    print("Procurando Bonds da Bring...")
    
    -- Loop principal de detecção
    autoBondConnections[#autoBondConnections + 1] = RunService.Heartbeat:Connect(function()
        if isAutoBondActive and not collectingBond then
            local bonds = detectBond()
            
            if #bonds > 0 then
                -- Encontrar Bond mais próximo
                local character = Player.Character
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local rootPart = character.HumanoidRootPart
                    local nearestBond = nil
                    local nearestDistance = math.huge
                    
                    for _, bond in pairs(bonds) do
                        local bondPosition = nil
                        
                        if bond:IsA("BasePart") then
                            bondPosition = bond.Position
                        elseif bond:IsA("Model") and bond.PrimaryPart then
                            bondPosition = bond.PrimaryPart.Position
                        end
                        
                        if bondPosition then
                            local distance = (rootPart.Position - bondPosition).Magnitude
                            if distance < nearestDistance then
                                nearestDistance = distance
                                nearestBond = bond
                            end
                        end
                    end
                    
                    if nearestBond then
                        print("💵 Bond encontrado! Coletando...")
                        bondDetected = nearestBond
                        collectBond(nearestBond)
                    end
                end
            end
        end
    end)
    
    -- Monitorar novos Bonds que aparecem
    autoBondConnections[#autoBondConnections + 1] = Workspace.DescendantAdded:Connect(function(descendant)
        if isAutoBondActive then
            local name = descendant.Name:lower()
            if name:find("bond") or name:find("bring") or name:find("dinheiro") or name:find("money") then
                print("💵 Novo Bond detectado!")
                task.wait(0.5)
                if isAutoBondActive and not collectingBond then
                    collectBond(descendant)
                end
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
    print("Total de Bonds coletados: " .. bondCount)
    
    -- Desconectar loops
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
        BondCountLabel.Visible = true
        AutoBondButton.Visible = true
        MinimizeButton.Text = "—"
        isMinimized = false
    else
        MainFrame.Size = UDim2.new(0, 250, 0, 35)
        StatusLabel.Visible = false
        BondCountLabel.Visible = false
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
    end
end)

print("⚡ NoobHub - Auto Bond para Dead Rails carregado!")
print("🎯 Sistema de coleta automática de Bonds da Bring pronto!")
print("💵 Ative o Auto Bond para começar a coletar!")
