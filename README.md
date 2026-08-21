--[[
    NoobHub - Survive Natural Disaster
    Script Completo com GUI Moderna Amarela
    Sistema Medroso + Cheats + GUI Minimizável
]]

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local Mouse = Player:GetMouse()
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NoobHub"
ScreenGui.Parent = Player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- Criar Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 320, 0, 500)
MainFrame.Position = UDim2.new(0.5, -160, 0.5, -250)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

-- Corner para o MainFrame
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

-- Barra de título
local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Size = UDim2.new(1, 0, 0, 45)
TitleBar.BackgroundColor3 = Color3.fromRGB(255, 180, 0)
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 12)
TitleCorner.Parent = TitleBar

-- Título
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Size = UDim2.new(0.6, 0, 1, 0)
Title.Position = UDim2.new(0.05, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "⚡ NoobHub"
Title.TextColor3 = Color3.fromRGB(20, 20, 20)
Title.TextSize = 20
Title.Font = Enum.Font.GothamBlack
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TitleBar

-- Botão minimizar
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Name = "MinimizeButton"
MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
MinimizeButton.Position = UDim2.new(0.87, 0, 0.15, 0)
MinimizeButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Text = "—"
MinimizeButton.TextColor3 = Color3.fromRGB(255, 180, 0)
MinimizeButton.TextSize = 20
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Parent = TitleBar

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 5)
MinCorner.Parent = MinimizeButton

-- Container para conteúdo
local ContentContainer = Instance.new("ScrollingFrame")
ContentContainer.Name = "ContentContainer"
ContentContainer.Size = UDim2.new(1, -20, 1, -55)
ContentContainer.Position = UDim2.new(0, 10, 0, 50)
ContentContainer.BackgroundTransparency = 1
ContentContainer.BorderSizePixel = 0
ContentContainer.ScrollBarThickness = 4
ContentContainer.ScrollBarImageColor3 = Color3.fromRGB(255, 180, 0)
ContentContainer.CanvasSize = UDim2.new(0, 0, 0, 650)
ContentContainer.Parent = MainFrame

-- Variáveis de estado
local isMinimized = false
local originalSize = MainFrame.Size
local isFleeSystemActive = false
local fleeConnections = {}
local threatLoop = nil
local lastHealth = nil

-- Função para criar separadores
local function createSeparator(yPosition)
    local Separator = Instance.new("Frame")
    Separator.Size = UDim2.new(1, -20, 0, 2)
    Separator.Position = UDim2.new(0, 10, 0, yPosition)
    Separator.BackgroundColor3 = Color3.fromRGB(255, 180, 0)
    Separator.BorderSizePixel = 0
    Separator.Parent = ContentContainer
    return Separator
end

-- Função para criar labels de seção
local function createSection(text, yPosition)
    local SectionLabel = Instance.new("TextLabel")
    SectionLabel.Size = UDim2.new(1, -20, 0, 25)
    SectionLabel.Position = UDim2.new(0, 10, 0, yPosition)
    SectionLabel.BackgroundTransparency = 1
    SectionLabel.Text = text
    SectionLabel.TextColor3 = Color3.fromRGB(255, 180, 0)
    SectionLabel.TextSize = 14
    SectionLabel.Font = Enum.Font.GothamBold
    SectionLabel.TextXAlignment = Enum.TextXAlignment.Left
    SectionLabel.Parent = ContentContainer
    return SectionLabel
end

-- Função para criar botões
local function createButton(text, yPosition, callback, color)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, -20, 0, 35)
    Button.Position = UDim2.new(0, 10, 0, yPosition)
    Button.BackgroundColor3 = color or Color3.fromRGB(40, 40, 40)
    Button.BorderSizePixel = 0
    Button.Text = text
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 13
    Button.Font = Enum.Font.Gotham
    Button.AutoButtonColor = true
    Button.Parent = ContentContainer
    
    local ButtonCorner = Instance.new("UICorner")
    ButtonCorner.CornerRadius = UDim.new(0, 5)
    ButtonCorner.Parent = Button
    
    if callback then
        Button.MouseButton1Click:Connect(callback)
    end
    
    return Button
end

-- Função para criar toggles
local function createToggle(text, yPosition, callback, default)
    local ToggleButton = Instance.new("TextButton")
    ToggleButton.Size = UDim2.new(1, -20, 0, 35)
    ToggleButton.Position = UDim2.new(0, 10, 0, yPosition)
    ToggleButton.BackgroundColor3 = default and Color3.fromRGB(255, 180, 0) or Color3.fromRGB(40, 40, 40)
    ToggleButton.BorderSizePixel = 0
    ToggleButton.Text = text
    ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleButton.TextSize = 13
    ToggleButton.Font = Enum.Font.Gotham
    ToggleButton.AutoButtonColor = false
    ToggleButton.Parent = ContentContainer
    
    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 5)
    ToggleCorner.Parent = ToggleButton
    
    local state = default or false
    
    ToggleButton.MouseButton1Click:Connect(function()
        state = not state
        ToggleButton.BackgroundColor3 = state and Color3.fromRGB(255, 180, 0) or Color3.fromRGB(40, 40, 40)
        if callback then
            callback(state)
        end
    end)
    
    return ToggleButton
end

-- Criar elementos da GUI
createSection("🏃 Movimento", 5)

createButton("⚡ Speed Boost", 35, function()
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") then
        character.Humanoid.WalkSpeed = 50
        task.wait(5)
        character.Humanoid.WalkSpeed = 16
    end
end, Color3.fromRGB(255, 150, 0))

createButton("🦘 Super Jump", 75, function()
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") then
        character.Humanoid.JumpPower = 100
        task.wait(5)
        character.Humanoid.JumpPower = 50
    end
end, Color3.fromRGB(255, 150, 0))

createButton("🚀 Fly Mode", 115, function()
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") and character:FindFirstChild("HumanoidRootPart") then
        local humanoid = character.Humanoid
        local rootPart = character.HumanoidRootPart
        
        if not rootPart:FindFirstChild("BodyGyro") then
            local bodyGyro = Instance.new("BodyGyro")
            local bodyVelocity = Instance.new("BodyVelocity")
            
            bodyGyro.Parent = rootPart
            bodyVelocity.Parent = rootPart
            bodyVelocity.MaxForce = Vector3.new(0, 0, 0)
            
            humanoid.PlatformStand = true
            bodyVelocity.Velocity = Vector3.new(0, 100, 0)
            bodyGyro.CFrame = rootPart.CFrame
            
            task.wait(3)
            bodyVelocity:Destroy()
            bodyGyro:Destroy()
            humanoid.PlatformStand = false
        end
    end
end, Color3.fromRGB(255, 150, 0))

createSeparator(165)

createSection("🛡️ Proteção", 175)

createButton("💪 God Mode", 205, function()
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") then
        character.Humanoid.MaxHealth = math.huge
        character.Humanoid.Health = math.huge
    end
end, Color3.fromRGB(255, 200, 0))

createButton("❤️ Restaurar Vida", 245, function()
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") then
        character.Humanoid.Health = character.Humanoid.MaxHealth
    end
end, Color3.fromRGB(255, 200, 0))

createSeparator(295)

createSection("😨 Sistema Medroso", 305)

-- Status do sistema medroso
local FleeStatusLabel = Instance.new("TextLabel")
FleeStatusLabel.Size = UDim2.new(1, -20, 0, 20)
FleeStatusLabel.Position = UDim2.new(0, 10, 0, 335)
FleeStatusLabel.BackgroundTransparency = 1
FleeStatusLabel.Text = "Status: Desativado"
FleeStatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
FleeStatusLabel.TextSize = 12
FleeStatusLabel.Font = Enum.Font.Gotham
FleeStatusLabel.TextXAlignment = Enum.TextXAlignment.Left
FleeStatusLabel.Parent = ContentContainer

-- Função para limpar conexões do sistema medroso
local function clearFleeConnections()
    for _, conn in pairs(fleeConnections) do
        conn:Disconnect()
    end
    fleeConnections = {}
end

-- Função para fugir
local function fleeFromTarget(targetPosition)
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") or not character:FindFirstChild("Humanoid") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    local humanoid = character.Humanoid
    
    local direction = (rootPart.Position - targetPosition).Unit
    direction = Vector3.new(direction.X, 0, direction.Z)
    
    if direction.Magnitude == 0 then
        direction = Vector3.new(math.random(-100, 100), 0, math.random(-100, 100)).Unit
    end
    
    humanoid.WalkSpeed = 80
    
    local fleePosition = rootPart.Position + direction * 100
    rootPart.CFrame = CFrame.new(fleePosition)
    
    task.delay(3, function()
        if isFleeSystemActive and humanoid then
            humanoid.WalkSpeed = 16
        end
    end)
end

-- Função para verificar ameaças
local function checkForThreats()
    if not isFleeSystemActive then return end
    
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    
    for _, otherPlayer in pairs(Players:GetPlayers()) do
        if otherPlayer ~= Player and isFleeSystemActive then
            local otherCharacter = otherPlayer.Character
            if otherCharacter and otherCharacter:FindFirstChild("HumanoidRootPart") then
                local otherRoot = otherCharacter.HumanoidRootPart
                local distance = (rootPart.Position - otherRoot.Position).Magnitude
                
                if distance < 30 then
                    fleeFromTarget(otherRoot.Position)
                    break
                end
            end
        end
    end
end

-- Função para iniciar sistema medroso
local function startFleeSystem()
    if isFleeSystemActive then return end
    
    isFleeSystemActive = true
    FleeStatusLabel.Text = "Status: 🟢 Ativado"
    FleeStatusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
    
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") then
        lastHealth = character.Humanoid.Health
    end
    
    threatLoop = RunService.Heartbeat:Connect(function()
        if isFleeSystemActive then
            checkForThreats()
        end
    end)
    
    fleeConnections[#fleeConnections + 1] = Player.CharacterAdded:Connect(function(char)
        if isFleeSystemActive then
            local humanoid = char:WaitForChild("Humanoid")
            lastHealth = humanoid.Health
            
            fleeConnections[#fleeConnections + 1] = humanoid.HealthChanged:Connect(function(health)
                if isFleeSystemActive and health < lastHealth then
                    fleeFromTarget(Vector3.new(math.random(-1000, 1000), 0, math.random(-1000, 1000)))
                end
                lastHealth = health
            end)
        end
    end)
end

-- Função para parar sistema medroso
local function stopFleeSystem()
    if not isFleeSystemActive then return end
    
    isFleeSystemActive = false
    FleeStatusLabel.Text = "Status: 🔴 Desativado"
    FleeStatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
    
    if threatLoop then
        threatLoop:Disconnect()
        threatLoop = nil
    end
    
    clearFleeConnections()
    
    local character = Player.Character
    if character and character:FindFirstChild("Humanoid") then
        character.Humanoid.WalkSpeed = 16
    end
end

-- Toggle do sistema medroso
createToggle("😨 Ativar Modo Medroso", 365, function(state)
    if state then
        startFleeSystem()
    else
        stopFleeSystem()
    end
end, false)

createSeparator(415)

createSection("🔧 Utilidades", 425)

createButton("🔄 Resetar Personagem", 455, function()
    local character = Player.Character
    if character then
        character:BreakJoints()
    end
end, Color3.fromRGB(255, 100, 0))

createButton("📍 Teleportar para Spawn", 495, function()
    local character = Player.Character
    local rootPart = character and character:FindFirstChild("HumanoidRootPart")
    
    if rootPart then
        local spawnPoints = {}
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("SpawnLocation") then
                table.insert(spawnPoints, obj)
            end
        end
        
        if #spawnPoints > 0 then
            local spawn = spawnPoints[math.random(1, #spawnPoints)]
            rootPart.CFrame = spawn.CFrame + Vector3.new(0, 3, 0)
        else
            rootPart.CFrame = CFrame.new(0, 10, 0)
        end
    end
end, Color3.fromRGB(255, 100, 0))

-- Botão minimizar connection
MinimizeButton.MouseButton1Click:Connect(function()
    if isMinimized then
        -- Maximizar
        MainFrame.Size = originalSize
        ContentContainer.Visible = true
        MinimizeButton.Text = "—"
        isMinimized = false
    else
        -- Minimizar
        MainFrame.Size = UDim2.new(0, 320, 0, 45)
        ContentContainer.Visible = false
        MinimizeButton.Text = "+"
        isMinimized = true
    end
end)

-- Conexão para limpar quando o script for destruído
ScreenGui.Destroying:Connect(function()
    stopFleeSystem()
end)

print("⚡ NoobHub carregado com sucesso!")
print("😨 Sistema Medroso incluído!")
