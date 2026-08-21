--[[
    SrNoobHub - Universal Script
    GUI Moderna com Drag, Minimizar e TextBox
]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Cores
local Colors = {
    Background = Color3.fromRGB(25, 25, 35),
    Secondary = Color3.fromRGB(35, 35, 50),
    Accent = Color3.fromRGB(255, 70, 70),
    Accent2 = Color3.fromRGB(70, 130, 255),
    Text = Color3.fromRGB(255, 255, 255),
    TextDark = Color3.fromRGB(150, 150, 170),
    Success = Color3.fromRGB(50, 200, 100),
    Danger = Color3.fromRGB(255, 80, 80)
}

-- Criar GUI Principal
local SrNoobHub = Instance.new("ScreenGui")
SrNoobHub.Name = "SrNoobHub"
SrNoobHub.Parent = game.CoreGui
SrNoobHub.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Container Principal
local MainContainer = Instance.new("Frame")
MainContainer.Name = "MainContainer"
MainContainer.Parent = SrNoobHub
MainContainer.BackgroundColor3 = Colors.Background
MainContainer.BorderSizePixel = 0
MainContainer.Position = UDim2.new(0.5, -150, 0.5, -175)
MainContainer.Size = UDim2.new(0, 300, 0, 380)
MainContainer.ClipsDescendants = true
MainContainer.ZIndex = 2

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainContainer

-- Header
local Header = Instance.new("Frame")
Header.Name = "Header"
Header.Parent = MainContainer
Header.BackgroundColor3 = Colors.Secondary
Header.BorderSizePixel = 0
Header.Size = UDim2.new(1, 0, 0, 50)

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 12)
HeaderCorner.Parent = Header

-- Barra de gradiente
local GradientBar = Instance.new("Frame")
GradientBar.Name = "GradientBar"
GradientBar.Parent = Header
GradientBar.BackgroundColor3 = Colors.Accent
GradientBar.BorderSizePixel = 0
GradientBar.Position = UDim2.new(0, 0, 0, 48)
GradientBar.Size = UDim2.new(1, 0, 0, 2)

-- Título
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Parent = Header
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 15, 0, 0)
Title.Size = UDim2.new(0, 150, 0, 50)
Title.Font = Enum.Font.GothamBold
Title.Text = "🔥 SrNoobHub"
Title.TextColor3 = Colors.Text
Title.TextSize = 16
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Botão Minimizar
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Name = "MinimizeButton"
MinimizeButton.Parent = Header
MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Position = UDim2.new(1, -55, 0, 15)
MinimizeButton.Size = UDim2.new(0, 20, 0, 20)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "—"
MinimizeButton.TextColor3 = Colors.Text
MinimizeButton.TextSize = 14

local MinimizeCorner = Instance.new("UICorner")
MinimizeCorner.CornerRadius = UDim.new(0, 4)
MinimizeCorner.Parent = MinimizeButton

-- Botão Fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Name = "CloseButton"
CloseButton.Parent = Header
CloseButton.BackgroundColor3 = Colors.Danger
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(1, -30, 0, 15)
CloseButton.Size = UDim2.new(0, 20, 0, 20)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "✕"
CloseButton.TextColor3 = Colors.Text
CloseButton.TextSize = 14

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 4)
CloseCorner.Parent = CloseButton

-- Conteúdo
local ContentFrame = Instance.new("Frame")
ContentFrame.Name = "ContentFrame"
ContentFrame.Parent = MainContainer
ContentFrame.BackgroundTransparency = 1
ContentFrame.BorderSizePixel = 0
ContentFrame.Position = UDim2.new(0, 0, 0, 60)
ContentFrame.Size = UDim2.new(1, 0, 1, -60)

-- Função para criar labels
local function CreateLabel(parent, text, position)
    local label = Instance.new("TextLabel")
    label.Parent = parent
    label.BackgroundTransparency = 1
    label.Position = position
    label.Size = UDim2.new(0, 110, 0, 30)
    label.Font = Enum.Font.Gotham
    label.Text = text
    label.TextColor3 = Colors.Text
    label.TextSize = 12
    label.TextXAlignment = Enum.TextXAlignment.Left
    return label
end

-- Função para criar TextBox
local function CreateTextBox(parent, position, value, placeholder)
    local textbox = Instance.new("TextBox")
    textbox.Parent = parent
    textbox.BackgroundColor3 = Colors.Secondary
    textbox.BorderSizePixel = 0
    textbox.Position = position
    textbox.Size = UDim2.new(0, 160, 0, 30)
    textbox.Font = Enum.Font.Gotham
    textbox.Text = tostring(value)
    textbox.TextColor3 = Colors.Text
    textbox.TextSize = 12
    textbox.PlaceholderText = placeholder or "Digite o valor..."
    textbox.TextXAlignment = Enum.TextXAlignment.Center
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = textbox
    
    -- Efeito hover
    textbox.MouseEnter:Connect(function()
        TweenService:Create(textbox, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(45, 45, 60)}):Play()
    end)
    
    textbox.MouseLeave:Connect(function()
        TweenService:Create(textbox, TweenInfo.new(0.2), {BackgroundColor3 = Colors.Secondary}):Play()
    end)
    
    -- Efeito focus
    textbox.Focused:Connect(function()
        TweenService:Create(textbox, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(50, 50, 65)}):Play()
    end)
    
    textbox.FocusLost:Connect(function()
        TweenService:Create(textbox, TweenInfo.new(0.2), {BackgroundColor3 = Colors.Secondary}):Play()
    end)
    
    return textbox
end

-- Criar campos de edição
CreateLabel(ContentFrame, "⚡ Velocidade:", UDim2.new(0, 15, 0, 10))
local SpeedInput = CreateTextBox(ContentFrame, UDim2.new(0, 125, 0, 10), 16, "Velocidade (1-100)")

CreateLabel(ContentFrame, "🦘 Pulo:", UDim2.new(0, 15, 0, 50))
local JumpInput = CreateTextBox(ContentFrame, UDim2.new(0, 125, 0, 50), 50, "Pulo (1-200)")

CreateLabel(ContentFrame, "🌍 Gravidade:", UDim2.new(0, 15, 0, 90))
local GravityInput = CreateTextBox(ContentFrame, UDim2.new(0, 125, 0, 90), 196.2, "Gravidade (1-500)")

CreateLabel(ContentFrame, "🏋️ Massa:", UDim2.new(0, 15, 0, 130))
local MassInput = CreateTextBox(ContentFrame, UDim2.new(0, 125, 0, 130), 100, "Massa (1-1000)")

-- Botões
local ApplyButton = Instance.new("TextButton")
ApplyButton.Name = "ApplyButton"
ApplyButton.Parent = ContentFrame
ApplyButton.BackgroundColor3 = Colors.Success
ApplyButton.BorderSizePixel = 0
ApplyButton.Position = UDim2.new(0, 15, 0, 175)
ApplyButton.Size = UDim2.new(0, 130, 0, 35)
ApplyButton.Font = Enum.Font.GothamBold
ApplyButton.Text = "✅ Aplicar"
ApplyButton.TextColor3 = Colors.Text
ApplyButton.TextSize = 13

local ApplyCorner = Instance.new("UICorner")
ApplyCorner.CornerRadius = UDim.new(0, 6)
ApplyCorner.Parent = ApplyButton

-- Efeito hover Apply
ApplyButton.MouseEnter:Connect(function()
    TweenService:Create(ApplyButton, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(40, 180, 90)}):Play()
end)

ApplyButton.MouseLeave:Connect(function()
    TweenService:Create(ApplyButton, TweenInfo.new(0.2), {BackgroundColor3 = Colors.Success}):Play()
end)

local ResetButton = Instance.new("TextButton")
ResetButton.Name = "ResetButton"
ResetButton.Parent = ContentFrame
ResetButton.BackgroundColor3 = Colors.Danger
ResetButton.BorderSizePixel = 0
ResetButton.Position = UDim2.new(0, 155, 0, 175)
ResetButton.Size = UDim2.new(0, 130, 0, 35)
ResetButton.Font = Enum.Font.GothamBold
ResetButton.Text = "🔄 Resetar"
ResetButton.TextColor3 = Colors.Text
ResetButton.TextSize = 13

local ResetCorner = Instance.new("UICorner")
ResetCorner.CornerRadius = UDim.new(0, 6)
ResetCorner.Parent = ResetButton

-- Efeito hover Reset
ResetButton.MouseEnter:Connect(function()
    TweenService:Create(ResetButton, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(230, 60, 60)}):Play()
end)

ResetButton.MouseLeave:Connect(function()
    TweenService:Create(ResetButton, TweenInfo.new(0.2), {BackgroundColor3 = Colors.Danger}):Play()
end)

-- Drag System
local dragging = false
local dragInput, dragStart, startPos

local function updateDrag(input)
    local delta = input.Position - dragStart
    local newPos = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    TweenService:Create(MainContainer, TweenInfo.new(0.1), {Position = newPos}):Play()
end

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
        updateDrag(input)
    end
end)

-- Minimizar Sistema
local isMinimized = false

MinimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        TweenService:Create(MainContainer, TweenInfo.new(0.3, Enum.EasingStyle.Quart), {Size = UDim2.new(0, 300, 0, 50)}):Play()
        MinimizeButton.Text = "+"
    else
        TweenService:Create(MainContainer, TweenInfo.new(0.3, Enum.EasingStyle.Quart), {Size = UDim2.new(0, 300, 0, 380)}):Play()
        MinimizeButton.Text = "—"
    end
end)

-- Fechar
CloseButton.MouseButton1Click:Connect(function()
    TweenService:Create(MainContainer, TweenInfo.new(0.3, Enum.EasingStyle.Quart), {Size = UDim2.new(0, 0, 0, 0)}):Play()
    wait(0.3)
    SrNoobHub:Destroy()
end)

-- Funções de Aplicação
local function ApplyChanges()
    local speed = tonumber(SpeedInput.Text) or 16
    local jump = tonumber(JumpInput.Text) or 50
    local gravity = tonumber(GravityInput.Text) or 196.2
    local mass = tonumber(MassInput.Text) or 100
    
    -- Limitar valores
    speed = math.clamp(speed, 1, 100)
    jump = math.clamp(jump, 1, 200)
    gravity = math.clamp(gravity, 1, 500)
    mass = math.clamp(mass, 1, 1000)
    
    -- Atualizar TextBox com valores limitados
    SpeedInput.Text = tostring(speed)
    JumpInput.Text = tostring(jump)
    GravityInput.Text = tostring(gravity)
    MassInput.Text = tostring(mass)
    
    if Humanoid then
        Humanoid.WalkSpeed = speed
        Humanoid.JumpPower = jump
        
        -- Gravidade
        local existingGravity = RootPart:FindFirstChild("GravityController")
        if existingGravity then
            existingGravity:Destroy()
        end
        
        local gravityController = Instance.new("BodyForce")
        gravityController.Name = "GravityController"
        gravityController.Force = Vector3.new(0, -gravity * RootPart.AssemblyMass, 0)
        gravityController.Parent = RootPart
        
        -- Massa
        RootPart.CustomPhysicalProperties = PhysicalProperties.new(mass, 0.3, 0.5)
        
        -- Notificação
        print("✅ SrNoobHub: Valores aplicados com sucesso!")
    end
end

local function ResetChanges()
    if Humanoid then
        Humanoid.WalkSpeed = 16
        Humanoid.JumpPower = 50
        
        local existingGravity = RootPart:FindFirstChild("GravityController")
        if existingGravity then
            existingGravity:Destroy()
        end
        
        RootPart.CustomPhysicalProperties = PhysicalProperties.new(100, 0.3, 0.5)
        
        -- Resetar TextBox
        SpeedInput.Text = "16"
        JumpInput.Text = "50"
        GravityInput.Text = "196.2"
        MassInput.Text = "100"
        
        print("🔄 SrNoobHub: Valores resetados!")
    end
end

-- Eventos
ApplyButton.MouseButton1Click:Connect(ApplyChanges)
ResetButton.MouseButton1Click:Connect(ResetChanges)

-- Aplicar com Enter
SpeedInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then ApplyChanges() end
end)

JumpInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then ApplyChanges() end
end)

GravityInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then ApplyChanges() end
end)

MassInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then ApplyChanges() end
end)

-- Auto-atualizar quando respawnar
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    RootPart = Character:WaitForChild("HumanoidRootPart")
    wait(1)
    ApplyChanges()
end)

-- Aplicar automaticamente
wait(1)
ApplyChanges()
