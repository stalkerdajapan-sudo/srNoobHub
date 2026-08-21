--[[
    NoobHub - Modo Medroso
    Corre quando leva dano (de player ou NPC)
    Sem teleporte, só corre na direção oposta
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
MainFrame.Size = UDim2.new(0, 280, 0, 100)
MainFrame.Position = UDim2.new(0.5, -140, 0.5, -50)
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
StatusLabel.Position = UDim2.new(0.05, 0, 0.35, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "Status: 🔴 Desativado"
StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
StatusLabel.TextSize = 13
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.Parent = MainFrame

-- Botão Modo Medroso
local ScaredButton = Instance.new("TextButton")
ScaredButton.Size = UDim2.new(0.9, 0, 0, 30)
ScaredButton.Position = UDim2.new(0.05, 0, 0.65, 0)
ScaredButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
ScaredButton.BorderSizePixel = 0
ScaredButton.Text = "😨 ATIVAR MODO MEDROSO"
ScaredButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ScaredButton.TextSize = 12
ScaredButton.Font = Enum.Font.GothamBold
ScaredButton.Parent = MainFrame

local ScaredCorner = Instance.new("UICorner")
ScaredCorner.CornerRadius = UDim.new(0, 5)
ScaredCorner.Parent = ScaredButton

-- Variáveis
local isScaredActive = false
local isMinimized = false
local originalSize = MainFrame.Size
local isRunning = false
local lastHealth = nil
local scaredConnections = {}
local runSpeed = 80
local normalSpeed = 16
local runDuration = 3

-- Função para encontrar fonte de dano
local function findDamageSource()
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return nil
    end
    
    local rootPart = character.HumanoidRootPart
    local nearestThreat = nil
    local nearestDistance = math.huge
    
    -- Procurar players próximos
    for _, otherPlayer in pairs(Players:GetPlayers()) do
        if otherPlayer ~= Player then
            local otherCharacter = otherPlayer.Character
            if otherCharacter and otherCharacter:FindFirstChild("HumanoidRootPart") then
                local otherRoot = otherCharacter.HumanoidRootPart
                local distance = (rootPart.Position - otherRoot.Position).Magnitude
                
                if distance < nearestDistance and distance < 30 then
                    nearestDistance = distance
                    nearestThreat = otherRoot.Position
                end
            end
        end
    end
    
    -- Procurar NPCs próximos
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") then
            local humanoid = obj:FindFirstChild("Humanoid")
            local npcRoot = obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChild("Torso")
            
            if npcRoot and humanoid.Health > 0 then
                local isPlayer = false
                for _, player in pairs(Players:GetPlayers()) do
                    if player.Character == obj then
                        isPlayer = true
                        break
                    end
                end
                
                if not isPlayer then
                    local distance = (rootPart.Position - npcRoot.Position).Magnitude
                    if distance < nearestDistance and distance < 30 then
                        nearestDistance = distance
                        nearestThreat = npcRoot.Position
                    end
                end
            end
        end
    end
    
    return nearestThreat
end

-- Função para correr
local function runAway()
    if isRunning then return end
    
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") or not character:FindFirstChild("Humanoid") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    local humanoid = character.Humanoid
    
    -- Encontrar fonte de dano
    local threatPosition = findDamageSource()
    
    if not threatPosition then
        -- Se não achou ameaça, corre em direção aleatória
        threatPosition = rootPart.Position - Vector3.new(math.random(-100, 100), 0, math.random(-100, 100))
    end
    
    -- Calcular direção oposta
    local direction = (rootPart.Position - threatPosition).Unit
    direction = Vector3.new(direction.X, 0, direction.Z)
    
    if direction.Magnitude == 0 then
        direction = Vector3.new(math.random(-1, 1), 0, math.random(-1, 1)).Unit
    end
    
    isRunning = true
    
    -- Aumentar velocidade
    humanoid.WalkSpeed = runSpeed
    
    -- Correr na direção oposta
    local targetPosition = rootPart.Position + direction * 50
    
    -- Mover o personagem
    humanoid:MoveTo(targetPosition)
    
    print("😨 AI! Tomei dano! Correndo!")
    
    -- Esperar e restaurar velocidade
    task.delay(runDuration, function()
        if isScaredActive and character and character:FindFirstChild("Humanoid") then
            character.Humanoid.WalkSpeed = normalSpeed
        end
        isRunning = false
    end)
end

-- Função para iniciar Modo Medroso
local function startScaredMode()
    if isScaredActive then return end
    
    isScaredActive = true
    StatusLabel.Text = "Status: 🟢 Ativado"
    StatusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
    ScaredButton.Text = "😨 DESATIVAR MODO MEDROSO"
    ScaredButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") then
        lastHealth = character.Humanoid.Health
    end
    
    print("😨 Modo Medroso ATIVADO!")
    print("🏃 Vou correr quando tomar dano!")
    
    -- Monitorar dano
    scaredConnections[#scaredConnections + 1] = Player.CharacterAdded:Connect(function(char)
        if isScaredActive then
            local humanoid = char:WaitForChild("Humanoid")
            lastHealth = humanoid.Health
            
            scaredConnections[#scaredConnections + 1] = humanoid.HealthChanged:Connect(function(health)
                if isScaredActive and health < lastHealth then
                    -- Tomou dano!
                    runAway()
                end
                lastHealth = health
            end)
        end
    end)
    
    -- Conectar no personagem atual
    if character and character:FindFirstChild("Humanoid") then
        scaredConnections[#scaredConnections + 1] = character.Humanoid.HealthChanged:Connect(function(health)
            if isScaredActive and health < lastHealth then
                -- Tomou dano!
                runAway()
            end
            lastHealth = health
        end)
    end
end

-- Função para parar Modo Medroso
local function stopScaredMode()
    if not isScaredActive then return end
    
    isScaredActive = false
    StatusLabel.Text = "Status: 🔴 Desativado"
    StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
    ScaredButton.Text = "😨 ATIVAR MODO MEDROSO"
    ScaredButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    
    print("😨 Modo Medroso DESATIVADO!")
    
    -- Desconectar
    for _, conn in pairs(scaredConnections) do
        conn:Disconnect()
    end
    scaredConnections = {}
    
    -- Restaurar velocidade
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") then
        character.Humanoid.WalkSpeed = normalSpeed
    end
end

-- Toggle Modo Medroso
ScaredButton.MouseButton1Click:Connect(function()
    if isScaredActive then
        stopScaredMode()
    else
        startScaredMode()
    end
end)

-- Minimizar
MinimizeButton.MouseButton1Click:Connect(function()
    if isMinimized then
        MainFrame.Size = originalSize
        StatusLabel.Visible = true
        ScaredButton.Visible = true
        MinimizeButton.Text = "—"
        isMinimized = false
    else
        MainFrame.Size = UDim2.new(0, 280, 0, 35)
        StatusLabel.Visible = false
        ScaredButton.Visible = false
        MinimizeButton.Text = "+"
        isMinimized = true
    end
end)

-- Limpar quando destruir
ScreenGui.Destroying:Connect(function()
    stopScaredMode()
end)

print("⚡ NoobHub - Modo Medroso carregado!")
print("😨 Ative para correr quando tomar dano!")
