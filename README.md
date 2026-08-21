--[[
    SrNoobHub - Universal Script
    Sistema de Dupe com Quantidade Configurável
    Versão Final
]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")
local Backpack = Player:WaitForChild("Backpack")

-- Cores
local Colors = {
    Background = Color3.fromRGB(25, 25, 35),
    Secondary = Color3.fromRGB(35, 35, 50),
    Accent = Color3.fromRGB(255, 70, 70),
    Accent2 = Color3.fromRGB(70, 130, 255),
    Text = Color3.fromRGB(255, 255, 255),
    TextDark = Color3.fromRGB(150, 150, 170),
    Success = Color3.fromRGB(50, 200, 100),
    Warning = Color3.fromRGB(255, 200, 0),
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
MainContainer.Size = UDim2.new(0, 300, 0, 450)
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
Title.Size = UDim2.new(0, 200, 0, 50)
Title.Font = Enum.Font.GothamBold
Title.Text = "🔥 SrNoobHub"
Title.TextColor3 = Colors.Text
Title.TextSize = 16
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Subtítulo
local Subtitle = Instance.new("TextLabel")
Subtitle.Name = "Subtitle"
Subtitle.Parent = Header
Subtitle.BackgroundTransparency = 1
Subtitle.Position = UDim2.new(0, 15, 0, 25)
Subtitle.Size = UDim2.new(0, 200, 0, 20)
Subtitle.Font = Enum.Font.Gotham
Subtitle.Text = "Sistema de Dupe"
Subtitle.TextColor3 = Colors.TextDark
Subtitle.TextSize = 10
Subtitle.TextXAlignment = Enum.TextXAlignment.Left

-- Botão Minimizar
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Name = "MinimizeButton"
MinimizeButton.Parent = Header
MinimizeButton.BackgroundColor3 = Colors.Warning
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

-- Função para criar botões
local function CreateButton(parent, text, position, color)
    local button = Instance.new("TextButton")
    button.Parent = parent
    button.BackgroundColor3 = color
    button.BorderSizePixel = 0
    button.Position = position
    button.Size = UDim2.new(0, 270, 0, 40)
    button.Font = Enum.Font.GothamBold
    button.Text = text
    button.TextColor3 = Colors.Text
    button.TextSize = 13
    button.AutoButtonColor = false
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = button
    
    -- Efeito hover
    button.MouseEnter:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.2), {BackgroundColor3 = color:Lerp(Color3.new(1, 1, 1), 0.1)}):Play()
    end)
    
    button.MouseLeave:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.2), {BackgroundColor3 = color}):Play()
    end)
    
    return button
end

-- Função para criar notificações
local function CreateNotification(text, color)
    local notification = Instance.new("Frame")
    notification.Name = "Notification"
    notification.Parent = SrNoobHub
    notification.BackgroundColor3 = color
    notification.BorderSizePixel = 0
    notification.Position = UDim2.new(0.5, -100, 0, 20)
    notification.Size = UDim2.new(0, 200, 0, 40)
    notification.ZIndex = 3
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = notification
    
    local label = Instance.new("TextLabel")
    label.Parent = notification
    label.BackgroundTransparency = 1
    label.Size = UDim2.new(1, 0, 1, 0)
    label.Font = Enum.Font.GothamBold
    label.Text = text
    label.TextColor3 = Colors.Text
    label.TextSize = 12
    
    -- Animar
    notification.Position = UDim2.new(0.5, -100, 0, -40)
    TweenService:Create(notification, TweenInfo.new(0.5, Enum.EasingStyle.Bounce), {Position = UDim2.new(0.5, -100, 0, 20)}):Play()
    
    wait(2)
    TweenService:Create(notification, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -100, 0, -40)}):Play()
    wait(0.3)
    notification:Destroy()
end

-- Label de quantidade
local QuantityLabel = Instance.new("TextLabel")
QuantityLabel.Name = "QuantityLabel"
QuantityLabel.Parent = ContentFrame
QuantityLabel.BackgroundTransparency = 1
QuantityLabel.Position = UDim2.new(0, 15, 0, 5)
QuantityLabel.Size = UDim2.new(0, 270, 0, 25)
QuantityLabel.Font = Enum.Font.GothamBold
QuantityLabel.Text = "📊 Quantidade de Duplicação:"
QuantityLabel.TextColor3 = Colors.Text
QuantityLabel.TextSize = 12
QuantityLabel.TextXAlignment = Enum.TextXAlignment.Left

-- TextBox para quantidade
local QuantityInput = Instance.new("TextBox")
QuantityInput.Name = "QuantityInput"
QuantityInput.Parent = ContentFrame
QuantityInput.BackgroundColor3 = Colors.Secondary
QuantityInput.BorderSizePixel = 0
QuantityInput.Position = UDim2.new(0, 15, 0, 35)
QuantityInput.Size = UDim2.new(0, 270, 0, 35)
QuantityInput.Font = Enum.Font.GothamBold
QuantityInput.Text = "1"
QuantityInput.TextColor3 = Colors.Text
QuantityInput.TextSize = 16
QuantityInput.PlaceholderText = "1-10"
QuantityInput.TextXAlignment = Enum.TextXAlignment.Center

local QuantityCorner = Instance.new("UICorner")
QuantityCorner.CornerRadius = UDim.new(0, 8)
QuantityCorner.Parent = QuantityInput

-- Validação da quantidade
QuantityInput.FocusLost:Connect(function(enterPressed)
    local value = tonumber(QuantityInput.Text)
    if value then
        value = math.clamp(math.floor(value), 1, 10)
        QuantityInput.Text = tostring(value)
    else
        QuantityInput.Text = "1"
    end
end)

-- Botões do sistema
local DupeButton = CreateButton(ContentFrame, "💎 DUPE ITEM SELECIONADO", UDim2.new(0, 15, 0, 80), Colors.Accent)
local GrabAllButton = CreateButton(ContentFrame, "🎒 PEGAR TODOS ITENS", UDim2.new(0, 15, 0, 130), Colors.Success)
local DropAllButton = CreateButton(ContentFrame, "📦 DROPAR TODOS ITENS", UDim2.new(0, 15, 0, 180), Colors.Accent2)

-- Info label
local InfoLabel = Instance.new("TextLabel")
InfoLabel.Parent = ContentFrame
InfoLabel.BackgroundTransparency = 1
InfoLabel.Position = UDim2.new(0, 15, 0, 230)
InfoLabel.Size = UDim2.new(0, 270, 0, 30)
InfoLabel.Font = Enum.Font.Gotham
InfoLabel.Text = "⚠️ Quantidade: 1-10 | Use com moderação!"
InfoLabel.TextColor3 = Colors.TextDark
InfoLabel.TextSize = 11
InfoLabel.TextXAlignment = Enum.TextXAlignment.Center

-- Funções do sistema
local function DupeItem(item, quantity)
    if not item then
        CreateNotification("❌ Nenhum item selecionado!", Colors.Danger)
        return
    end
    
    quantity = math.clamp(quantity or 1, 1, 10)
    
    local successCount = 0
    
    for i = 1, quantity do
        local clonedItem = item:Clone()
        
        -- Tentar colocar no Backpack
        if clonedItem:IsA("Tool") then
            clonedItem.Parent = Backpack
            successCount += 1
        elseif clonedItem:IsA("HopperBin") then
            clonedItem.Parent = Backpack
            successCount += 1
        else
            -- Tentar no personagem
            clonedItem.Parent = Character
            successCount += 1
        end
        
        wait(0.05) -- Pequena pausa para não sobrecarregar
    end
    
    if successCount > 0 then
        CreateNotification("✅ " .. successCount .. " itens duplicados!", Colors.Success)
    else
        CreateNotification("❌ Falha ao duplicar!", Colors.Danger)
    end
end

local function GrabAllItems()
    local itemsFound = 0
    local itemsToGrab = {}
    
    -- Procurar itens no ReplicatedStorage
    for _, item in pairs(ReplicatedStorage:GetDescendants()) do
        if item:IsA("Tool") or item:IsA("HopperBin") then
            table.insert(itemsToGrab, item)
            itemsFound += 1
        end
    end
    
    -- Procurar em outros lugares comuns
    local workspace = game:GetService("Workspace")
    for _, item in pairs(workspace:GetDescendants()) do
        if (item:IsA("Tool") or item:IsA("HopperBin")) and not item:IsDescendantOf(Character) then
            table.insert(itemsToGrab, item)
            itemsFound += 1
        end
    end
    
    -- Clonar e adicionar ao inventário
    for _, item in pairs(itemsToGrab) do
        local clonedItem = item:Clone()
        if clonedItem:IsA("Tool") or clonedItem:IsA("HopperBin") then
            clonedItem.Parent = Backpack
        else
            clonedItem.Parent = Character
        end
        wait(0.01)
    end
    
    if itemsFound > 0 then
        CreateNotification("🎒 Pegou " .. itemsFound .. " itens!", Colors.Success)
    else
        CreateNotification("❌ Nenhum item encontrado!", Colors.Danger)
    end
end

local function DropAllItems()
    local droppedItems = 0
    
    -- Dropar itens do Backpack
    for _, item in pairs(Backpack:GetChildren()) do
        if item:IsA("Tool") or item:IsA("HopperBin") then
            item.Parent = Character
            wait(0.1)
            if item:IsA("Tool") then
                Humanoid:UnequipTools()
            end
            item.Parent = workspace
            if item:IsA("Tool") and item.Handle then
                item.Handle.Position = RootPart.Position + Vector3.new(math.random(-5, 5), 0, math.random(-5, 5))
            end
            droppedItems += 1
        end
    end
    
    -- Dropar itens do personagem
    for _, item in pairs(Character:GetChildren()) do
        if (item:IsA("Tool") or item:IsA("HopperBin")) and item.Name ~= "Humanoid" then
            item.Parent = workspace
            if item:IsA("Tool") and item.Handle then
                item.Handle.Position = RootPart.Position + Vector3.new(math.random(-5, 5), 0, math.random(-5, 5))
            end
            droppedItems += 1
        end
    end
    
    if droppedItems > 0 then
        CreateNotification("📦 Dropou " .. droppedItems .. " itens!", Colors.Warning)
    else
        CreateNotification("❌ Nenhum item para dropar!", Colors.Danger)
    end
end

-- Sistema de seleção de item
local selectedItem = nil

-- Função para duplicar item selecionado
local function OnItemSelected(item)
    selectedItem = item
    CreateNotification("💎 Item selecionado: " .. item.Name, Colors.Accent2)
end

-- Conectar eventos
DupeButton.MouseButton1Click:Connect(function()
    local quantity = tonumber(QuantityInput.Text) or 1
    quantity = math.clamp(math.floor(quantity), 1, 10)
    
    if selectedItem then
        DupeItem(selectedItem, quantity)
    else
        -- Tentar pegar item equipado
        local equippedTool = Character:FindFirstChildOfClass("Tool")
        if equippedTool then
            DupeItem(equippedTool, quantity)
        else
            CreateNotification("❌ Selecione um item primeiro!", Colors.Danger)
        end
    end
end)

GrabAllButton.MouseButton1Click:Connect(GrabAllItems)
DropAllButton.MouseButton1Click:Connect(DropAllItems)

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
        TweenService:Create(MainContainer, TweenInfo.new(0.3, Enum.EasingStyle.Quart), {Size = UDim2.new(0, 300, 0, 450)}):Play()
        MinimizeButton.Text = "—"
    end
end)

-- Fechar
CloseButton.MouseButton1Click:Connect(function()
    TweenService:Create(MainContainer, TweenInfo.new(0.3, Enum.EasingStyle.Quart), {Size = UDim2.new(0, 0, 0, 0)}):Play()
    wait(0.3)
    SrNoobHub:Destroy()
end)

-- Atualizar personagem quando respawnar
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    RootPart = Character:WaitForChild("HumanoidRootPart")
end)

-- Notificação inicial
wait(1)
CreateNotification("🔥 SrNoobHub Carregado!", Colors.Success)
