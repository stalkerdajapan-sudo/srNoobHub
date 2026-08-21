--[[
    ❤️ SrNoobMega ❤️ - ADMIN PANEL
    Sistema Server-Side de Administração
]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Cores Premium
local Colors = {
    Background = Color3.fromRGB(10, 10, 18),
    Secondary = Color3.fromRGB(20, 20, 35),
    Tertiary = Color3.fromRGB(30, 30, 50),
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
MainContainer.Position = UDim2.new(0.5, -160, 0.5, -220)
MainContainer.Size = UDim2.new(0, 320, 0, 440)
MainContainer.ClipsDescendants = true
MainContainer.ZIndex = 2

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent = MainContainer

-- Sombra
local Shadow = Instance.new("Frame")
Shadow.Name = "Shadow"
Shadow.Parent = SrNoobMega
Shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Shadow.BackgroundTransparency = 0.6
Shadow.BorderSizePixel = 0
Shadow.Position = UDim2.new(0.5, -157, 0.5, -217)
Shadow.Size = UDim2.new(0, 320, 0, 440)
Shadow.ZIndex = 1

local ShadowCorner = Instance.new("UICorner")
ShadowCorner.CornerRadius = UDim.new(0, 14)
ShadowCorner.Parent = Shadow

-- Header
local Header = Instance.new("Frame")
Header.Parent = MainContainer
Header.BackgroundColor3 = Colors.Secondary
Header.BorderSizePixel = 0
Header.Size = UDim2.new(1, 0, 0, 50)
Header.ZIndex = 3

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 14)
HeaderCorner.Parent = Header

-- Gradiente animado
local GradientBar = Instance.new("Frame")
GradientBar.Parent = Header
GradientBar.BackgroundColor3 = Colors.Accent
GradientBar.BorderSizePixel = 0
GradientBar.Position = UDim2.new(0, 0, 0, 48)
GradientBar.Size = UDim2.new(1, 0, 0, 2)

local function animateGradient()
    spawn(function()
        while true do
            for _, color in pairs({Colors.Accent, Colors.Purple, Colors.Cyan, Colors.Gold, Colors.Accent}) do
                TweenService:Create(GradientBar, TweenInfo.new(0.5), {BackgroundColor3 = color}):Play()
                wait(0.5)
            end
        end
    end)
end
animateGradient()

-- Título
local Title = Instance.new("TextLabel")
Title.Parent = Header
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 15, 0, 0)
Title.Size = UDim2.new(0, 200, 0, 50)
Title.Font = Enum.Font.GothamBlack
Title.Text = "⭐ ADMIN PANEL"
Title.TextColor3 = Colors.Gold
Title.TextSize = 16
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Subtítulo
local Subtitle = Instance.new("TextLabel")
Subtitle.Parent = Header
Subtitle.BackgroundTransparency = 1
Subtitle.Position = UDim2.new(0, 15, 0, 25)
Subtitle.Size = UDim2.new(0, 200, 0, 20)
Subtitle.Font = Enum.Font.Gotham
Subtitle.Text = "Server-Side Control"
Subtitle.TextColor3 = Colors.TextDark
Subtitle.TextSize = 9
Subtitle.TextXAlignment = Enum.TextXAlignment.Left

-- Botão Minimizar
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Parent = Header
MinimizeButton.BackgroundColor3 = Colors.Warning
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Position = UDim2.new(1, -55, 0, 15)
MinimizeButton.Size = UDim2.new(0, 22, 0, 22)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "—"
MinimizeButton.TextColor3 = Colors.Text
MinimizeButton.TextSize = 14

local MinimizeCorner = Instance.new("UICorner")
MinimizeCorner.CornerRadius = UDim.new(0, 5)
MinimizeCorner.Parent = MinimizeButton

-- Botão Fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Parent = Header
CloseButton.BackgroundColor3 = Colors.Danger
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(1, -28, 0, 15)
CloseButton.Size = UDim2.new(0, 22, 0, 22)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "✕"
CloseButton.TextColor3 = Colors.Text
CloseButton.TextSize = 14

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 5)
CloseCorner.Parent = CloseButton

-- Conteúdo
local ContentFrame = Instance.new("Frame")
ContentFrame.Parent = MainContainer
ContentFrame.BackgroundTransparency = 1
ContentFrame.BorderSizePixel = 0
ContentFrame.Position = UDim2.new(0, 0, 0, 55)
ContentFrame.Size = UDim2.new(1, 0, 1, -55)

-- Título da seção
local PlayersTitle = Instance.new("TextLabel")
PlayersTitle.Parent = ContentFrame
PlayersTitle.BackgroundTransparency = 1
PlayersTitle.Position = UDim2.new(0, 15, 0, 5)
PlayersTitle.Size = UDim2.new(0, 200, 0, 20)
PlayersTitle.Font = Enum.Font.GothamBold
PlayersTitle.Text = "👥 JOGADORES ONLINE"
PlayersTitle.TextColor3 = Colors.Accent
PlayersTitle.TextSize = 11
PlayersTitle.TextXAlignment = Enum.TextXAlignment.Left

-- Lista de Players
local PlayerList = Instance.new("ScrollingFrame")
PlayerList.Name = "PlayerList"
PlayerList.Parent = ContentFrame
PlayerList.BackgroundColor3 = Colors.Secondary
PlayerList.BorderSizePixel = 0
PlayerList.Position = UDim2.new(0, 15, 0, 30)
PlayerList.Size = UDim2.new(0, 290, 0, 200)
PlayerList.CanvasSize = UDim2.new(0, 0, 0, 0)
PlayerList.ScrollBarThickness = 4
PlayerList.ScrollBarImageColor3 = Colors.Accent

local ListCorner = Instance.new("UICorner")
ListCorner.CornerRadius = UDim.new(0, 8)
ListCorner.Parent = PlayerList

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = PlayerList
UIListLayout.Padding = UDim.new(0, 4)
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder

local UIPadding = Instance.new("UIPadding")
UIPadding.Parent = PlayerList
UIPadding.PaddingLeft = UDim.new(0, 8)
UIPadding.PaddingRight = UDim.new(0, 8)
UIPadding.PaddingTop = UDim.new(0, 8)
UIPadding.PaddingBottom = UDim.new(0, 8)

-- Player Selecionado
local SelectedPlayer = nil
local SelectedButton = nil

-- Função para atualizar lista
local function UpdatePlayerList()
    -- Limpar lista
    for _, child in pairs(PlayerList:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end
    
    local players = Players:GetPlayers()
    local totalHeight = #players * 32 + (#players - 1) * 4 + 16
    PlayerList.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
    
    for i, otherPlayer in pairs(players) do
        if otherPlayer ~= Player then
            local playerButton = Instance.new("TextButton")
            playerButton.Name = "Player_" .. otherPlayer.Name
            playerButton.Parent = PlayerList
            playerButton.BackgroundColor3 = Colors.Tertiary
            playerButton.BorderSizePixel = 0
            playerButton.Size = UDim2.new(1, -16, 0, 30)
            playerButton.Font = Enum.Font.GothamBold
            playerButton.Text = "👤 " .. otherPlayer.Name
            playerButton.TextColor3 = Colors.Text
            playerButton.TextSize = 11
            playerButton.TextXAlignment = Enum.TextXAlignment.Left
            playerButton.AutoButtonColor = false
            playerButton.LayoutOrder = i
            
            local playerCorner = Instance.new("UICorner")
            playerCorner.CornerRadius = UDim.new(0, 5)
            playerCorner.Parent = playerButton
            
            -- Status do player
            local statusIndicator = Instance.new("Frame")
            statusIndicator.Name = "Status"
            statusIndicator.Parent = playerButton
            statusIndicator.BackgroundColor3 = Colors.Success
            statusIndicator.BorderSizePixel = 0
            statusIndicator.Position = UDim2.new(1, -15, 0, 10)
            statusIndicator.Size = UDim2.new(0, 10, 0, 10)
            statusIndicator.ZIndex = 3
            
            local statusCorner = Instance.new("UICorner")
            statusCorner.CornerRadius = UDim.new(0, 5)
            statusCorner.Parent = statusIndicator
            
            playerButton.MouseEnter:Connect(function()
                TweenService:Create(playerButton, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(40, 40, 60)}):Play()
            end)
            
            playerButton.MouseLeave:Connect(function()
                if SelectedButton ~= playerButton then
                    TweenService:Create(playerButton, TweenInfo.new(0.15), {BackgroundColor3 = Colors.Tertiary}):Play()
                end
            end)
            
            playerButton.MouseButton1Click:Connect(function()
                SelectedPlayer = otherPlayer
                
                -- Resetar todos botões
                for _, child in pairs(PlayerList:GetChildren()) do
                    if child:IsA("TextButton") then
                        child.BackgroundColor3 = Colors.Tertiary
                    end
                end
                
                -- Destacar selecionado
                playerButton.BackgroundColor3 = Colors.Accent
                SelectedButton = playerButton
                
                Notify("Selecionado: " .. otherPlayer.Name, Colors.Cyan)
            end)
        end
    end
end

-- Label do player selecionado
local SelectedLabel = Instance.new("TextLabel")
SelectedLabel.Parent = ContentFrame
SelectedLabel.BackgroundTransparency = 1
SelectedLabel.Position = UDim2.new(0, 15, 0, 240)
SelectedLabel.Size = UDim2.new(0, 290, 0, 20)
SelectedLabel.Font = Enum.Font.GothamBold
SelectedLabel.Text = "Selecionado: Nenhum"
SelectedLabel.TextColor3 = Colors.TextDark
SelectedLabel.TextSize = 10
SelectedLabel.TextXAlignment = Enum.TextXAlignment.Center

-- Função para criar botão de ação
local function CreateActionButton(text, color, position, callback)
    local button = Instance.new("TextButton")
    button.Parent = ContentFrame
    button.BackgroundColor3 = color
    button.BorderSizePixel = 0
    button.Position = position
    button.Size = UDim2.new(0, 140, 0, 35)
    button.Font = Enum.Font.GothamBold
    button.Text = text
    button.TextColor3 = Colors.Text
    button.TextSize = 11
    button.AutoButtonColor = false
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 7)
    corner.Parent = button
    
    button.MouseEnter:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.15), {BackgroundColor3 = color:Lerp(Color3.new(1,1,1), 0.15)}):Play()
    end)
    
    button.MouseLeave:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.15), {BackgroundColor3 = color}):Play()
    end)
    
    button.MouseButton1Click:Connect(callback)
    return button
end

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

-- ===== AÇÕES =====

-- Botão KICK
CreateActionButton("👢 KICK", Colors.Danger, UDim2.new(0, 15, 0, 270), function()
    if SelectedPlayer then
        Notify("Kickando " .. SelectedPlayer.Name, Colors.Danger)
        -- Server-side kick
        pcall(function()
            SelectedPlayer:Kick("Kickado pelo Admin Panel")
        end)
    else
        Notify("Selecione um player!", Colors.Warning)
    end
end)

-- Botão KILL
CreateActionButton("💀 KILL", Colors.Accent, UDim2.new(0, 165, 0, 270), function()
    if SelectedPlayer then
        if SelectedPlayer.Character then
            local targetHumanoid = SelectedPlayer.Character:FindFirstChild("Humanoid")
            if targetHumanoid then
                targetHumanoid.Health = 0
                Notify(SelectedPlayer.Name .. " eliminado!", Colors.Accent)
            end
        end
    else
        Notify("Selecione um player!", Colors.Warning)
    end
end)

-- Botão OP (Dar poderes)
CreateActionButton("⚡ DAR OP", Colors.Gold, UDim2.new(0, 15, 0, 315), function()
    if SelectedPlayer then
        Notify("OP para " .. SelectedPlayer.Name, Colors.Gold)
        -- Dar speed e jump
        if SelectedPlayer.Character then
            local targetHumanoid = SelectedPlayer.Character:FindFirstChild("Humanoid")
            if targetHumanoid then
                targetHumanoid.WalkSpeed = 100
                targetHumanoid.JumpPower = 100
                targetHumanoid.MaxHealth = 1000
                targetHumanoid.Health = 1000
            end
        end
    else
        Notify("Selecione um player!", Colors.Warning)
    end
end)

-- Botão REFRESH
CreateActionButton("🔄 ATUALIZAR", Colors.Cyan, UDim2.new(0, 165, 0, 315), function()
    UpdatePlayerList()
    Notify("Lista atualizada!", Colors.Cyan)
end)

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
        TweenService:Create(MainContainer, TweenInfo.new(0.3), {Size = UDim2.new(0, 320, 0, 50)}):Play()
        MinimizeButton.Text = "+"
    else
        TweenService:Create(MainContainer, TweenInfo.new(0.3), {Size = UDim2.new(0, 320, 0, 440)}):Play()
        MinimizeButton.Text = "—"
    end
end)

-- Fechar
CloseButton.MouseButton1Click:Connect(function()
    SrNoobMega:Destroy()
end)

-- Atualizar player selected
SelectedLabel:GetPropertyChangedSignal("Text"):Connect(function()
    if SelectedPlayer then
        SelectedLabel.Text = "Selecionado: " .. SelectedPlayer.Name
    end
end)

-- Auto-atualizar lista
spawn(function()
    while wait(5) do
        if SrNoobMega.Parent then
            UpdatePlayerList()
        end
    end
end)

-- Atualizar personagem
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    RootPart = Character:WaitForChild("HumanoidRootPart")
end)

-- Inicializar
UpdatePlayerList()
Notify("⭐ Admin Panel Carregado!", Colors.Gold)
