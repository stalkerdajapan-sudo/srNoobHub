--[[
    ❤️ SrNoobMega ❤️ - BLOX FRUITS TEST
    Auto Farm + GUI (Versão Teste)
]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Cores
local Colors = {
    Background = Color3.fromRGB(15, 15, 25),
    Secondary = Color3.fromRGB(25, 25, 40),
    Tertiary = Color3.fromRGB(35, 35, 55),
    Accent = Color3.fromRGB(255, 60, 90),
    Accent2 = Color3.fromRGB(90, 140, 255),
    Text = Color3.fromRGB(255, 255, 255),
    TextDark = Color3.fromRGB(130, 130, 155),
    Success = Color3.fromRGB(40, 210, 100),
    Warning = Color3.fromRGB(255, 190, 0),
    Danger = Color3.fromRGB(255, 70, 70),
    Purple = Color3.fromRGB(170, 90, 255),
    Cyan = Color3.fromRGB(0, 210, 210),
    Gold = Color3.fromRGB(255, 215, 0)
}

-- Criar GUI
local SrNoobMega = Instance.new("ScreenGui")
SrNoobMega.Name = "SrNoobMega"
SrNoobMega.Parent = game.CoreGui
SrNoobMega.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
SrNoobMega.ResetOnSpawn = false

-- Container Principal
local MainContainer = Instance.new("Frame")
MainContainer.Name = "MainContainer"
MainContainer.Parent = SrNoobMega
MainContainer.BackgroundColor3 = Colors.Background
MainContainer.BorderSizePixel = 0
MainContainer.Position = UDim2.new(0.5, -150, 0.5, -150)
MainContainer.Size = UDim2.new(0, 300, 0, 300)
MainContainer.ClipsDescendants = true
MainContainer.ZIndex = 2

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainContainer

-- Header
local Header = Instance.new("Frame")
Header.Parent = MainContainer
Header.BackgroundColor3 = Colors.Secondary
Header.BorderSizePixel = 0
Header.Size = UDim2.new(1, 0, 0, 45)
Header.ZIndex = 3

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 12)
HeaderCorner.Parent = Header

local GradientBar = Instance.new("Frame")
GradientBar.Parent = Header
GradientBar.BackgroundColor3 = Colors.Accent
GradientBar.BorderSizePixel = 0
GradientBar.Position = UDim2.new(0, 0, 0, 43)
GradientBar.Size = UDim2.new(1, 0, 0, 2)

local Title = Instance.new("TextLabel")
Title.Parent = Header
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 12, 0, 0)
Title.Size = UDim2.new(0, 180, 0, 45)
Title.Font = Enum.Font.GothamBold
Title.Text = "🍊 Blox Fruits"
Title.TextColor3 = Colors.Gold
Title.TextSize = 14
Title.TextXAlignment = Enum.TextXAlignment.Left

local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Parent = Header
MinimizeButton.BackgroundColor3 = Colors.Warning
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Position = UDim2.new(1, -50, 0, 12)
MinimizeButton.Size = UDim2.new(0, 20, 0, 20)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "—"
MinimizeButton.TextColor3 = Colors.Text
MinimizeButton.TextSize = 12
MinimizeButton.AutoButtonColor = false

local MinimizeCorner = Instance.new("UICorner")
MinimizeCorner.CornerRadius = UDim.new(0, 4)
MinimizeCorner.Parent = MinimizeButton

local CloseButton = Instance.new("TextButton")
CloseButton.Parent = Header
CloseButton.BackgroundColor3 = Colors.Danger
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(1, -25, 0, 12)
CloseButton.Size = UDim2.new(0, 20, 0, 20)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "✕"
CloseButton.TextColor3 = Colors.Text
CloseButton.TextSize = 12
CloseButton.AutoButtonColor = false

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 4)
CloseCorner.Parent = CloseButton

-- Conteúdo
local ContentFrame = Instance.new("Frame")
ContentFrame.Parent = MainContainer
ContentFrame.BackgroundTransparency = 1
ContentFrame.BorderSizePixel = 0
ContentFrame.Position = UDim2.new(0, 0, 0, 50)
ContentFrame.Size = UDim2.new(1, 0, 1, -50)

-- Status do Farm
local FarmStatus = Instance.new("TextLabel")
FarmStatus.Parent = ContentFrame
FarmStatus.BackgroundTransparency = 1
FarmStatus.Position = UDim2.new(0, 10, 0, 10)
FarmStatus.Size = UDim2.new(0, 280, 0, 30)
FarmStatus.Font = Enum.Font.GothamBold
FarmStatus.Text = "Auto Farm: DESATIVADO"
FarmStatus.TextColor3 = Colors.Danger
FarmStatus.TextSize = 13
FarmStatus.TextXAlignment = Enum.TextXAlignment.Center

-- Botão Auto Farm
local FarmButton = Instance.new("TextButton")
FarmButton.Parent = ContentFrame
FarmButton.BackgroundColor3 = Colors.Success
FarmButton.BorderSizePixel = 0
FarmButton.Position = UDim2.new(0, 15, 0, 50)
FarmButton.Size = UDim2.new(0, 270, 0, 45)
FarmButton.Font = Enum.Font.GothamBold
FarmButton.Text = "🌾 ATIVAR AUTO FARM"
FarmButton.TextColor3 = Colors.Text
FarmButton.TextSize = 13
FarmButton.AutoButtonColor = false

local FarmCorner = Instance.new("UICorner")
FarmCorner.CornerRadius = UDim.new(0, 8)
FarmCorner.Parent = FarmButton

-- Info
local InfoLabel = Instance.new("TextLabel")
InfoLabel.Parent = ContentFrame
InfoLabel.BackgroundTransparency = 1
InfoLabel.Position = UDim2.new(0, 10, 0, 110)
InfoLabel.Size = UDim2.new(0, 280, 0, 40)
InfoLabel.Font = Enum.Font.Gotham
InfoLabel.Text = "TESTE - Auto Farm Básico\nProcura inimigos próximos"
InfoLabel.TextColor3 = Colors.TextDark
InfoLabel.TextSize = 10
InfoLabel.TextXAlignment = Enum.TextXAlignment.Center
InfoLabel.TextYAlignment = Enum.TextYAlignment.Center

-- Sistema de Auto Farm
local farmEnabled = false
local farmConnection

local function FindNearestEnemy()
    local nearest = nil
    local shortestDistance = 100 -- Alcance máximo
    
    for _, object in pairs(workspace:GetDescendants()) do
        if object:IsA("Model") and object:FindFirstChild("Humanoid") and object:FindFirstChild("HumanoidRootPart") then
            local humanoid = object:FindFirstChild("Humanoid")
            local root = object:FindFirstChild("HumanoidRootPart")
            
            -- Verificar se é inimigo (não é player)
            if humanoid.Health > 0 and not Players:GetPlayerFromCharacter(object) then
                local distance = (RootPart.Position - root.Position).Magnitude
                if distance < shortestDistance then
                    shortestDistance = distance
                    nearest = object
                end
            end
        end
    end
    
    return nearest
end

local function StartFarm()
    farmEnabled = true
    FarmStatus.Text = "Auto Farm: ATIVADO"
    FarmStatus.TextColor3 = Colors.Success
    FarmButton.Text = "⏹️ PARAR AUTO FARM"
    FarmButton.BackgroundColor3 = Colors.Danger
    
    Notify("Auto Farm ATIVADO!", Colors.Success)
    
    farmConnection = RunService.Heartbeat:Connect(function()
        if not farmEnabled or not Character or not RootPart or not Humanoid then
            return
        end
        
        local enemy = FindNearestEnemy()
        
        if enemy then
            local enemyRoot = enemy:FindFirstChild("HumanoidRootPart")
            local enemyHumanoid = enemy:FindFirstChild("Humanoid")
            
            if enemyRoot and enemyHumanoid and enemyHumanoid.Health > 0 then
                -- Mover até o inimigo
                Humanoid:MoveTo(enemyRoot.Position)
                
                -- Atacar quando perto
                if (RootPart.Position - enemyRoot.Position).Magnitude < 10 then
                    -- Usar ataques básicos
                    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Z, false, nil)
                    wait(0.1)
                    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Z, false, nil)
                    
                    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.X, false, nil)
                    wait(0.1)
                    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.X, false, nil)
                end
            end
        else
            -- Procurar inimigos andando
            local randomX = math.random(-50, 50)
            local randomZ = math.random(-50, 50)
            Humanoid:MoveTo(Vector3.new(randomX, 0, randomZ))
        end
    end)
end

local function StopFarm()
    farmEnabled = false
    if farmConnection then
        farmConnection:Disconnect()
    end
    
    FarmStatus.Text = "Auto Farm: DESATIVADO"
    FarmStatus.TextColor3 = Colors.Danger
    FarmButton.Text = "🌾 ATIVAR AUTO FARM"
    FarmButton.BackgroundColor3 = Colors.Success
    
    Notify("Auto Farm DESATIVADO!", Colors.Danger)
end

FarmButton.MouseButton1Click:Connect(function()
    if farmEnabled then
        StopFarm()
    else
        StartFarm()
    end
end)

-- Notificação
local function Notify(text, color)
    local notif = Instance.new("Frame")
    notif.Parent = SrNoobMega
    notif.BackgroundColor3 = color
    notif.BorderSizePixel = 0
    notif.Position = UDim2.new(0.5, -80, 0, 10)
    notif.Size = UDim2.new(0, 160, 0, 30)
    notif.ZIndex = 10
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = notif
    
    local label = Instance.new("TextLabel")
    label.Parent = notif
    label.BackgroundTransparency = 1
    label.Size = UDim2.new(1, 0, 1, 0)
    label.Font = Enum.Font.GothamBold
    label.Text = text
    label.TextColor3 = Colors.Text
    label.TextSize = 10
    
    notif.Position = UDim2.new(0.5, -80, 0, -30)
    TweenService:Create(notif, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -80, 0, 10)}):Play()
    wait(1.5)
    TweenService:Create(notif, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -80, 0, -30)}):Play()
    wait(0.3)
    notif:Destroy()
end

-- Drag System
local dragging = false
local dragInput, dragStart, startPos

Header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainContainer.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

Header.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        MainContainer.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Minimizar
local isMinimized = false
MinimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        TweenService:Create(MainContainer, TweenInfo.new(0.3), {Size = UDim2.new(0, 300, 0, 45)}):Play()
        MinimizeButton.Text = "+"
    else
        TweenService:Create(MainContainer, TweenInfo.new(0.3), {Size = UDim2.new(0, 300, 0, 300)}):Play()
        MinimizeButton.Text = "—"
    end
end)

-- Fechar
CloseButton.MouseButton1Click:Connect(function()
    StopFarm()
    SrNoobMega:Destroy()
end)

-- Atualizar personagem
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    RootPart = Character:WaitForChild("HumanoidRootPart")
    
    if farmEnabled then
        StopFarm()
        wait(1)
        StartFarm()
    end
end)

Notify("🍊 Blox Fruits Test Carregado!", Colors.Gold)
