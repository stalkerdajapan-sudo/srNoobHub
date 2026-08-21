--[[
    SCRIPT: NoobHub - Hitbox Adjuster
    AUTOR: SrNoob
    VERSÃO: 1.0 - SIMPLIFICADO
]]

local player = game.Players.LocalPlayer
local userInputService = game:GetService("UserInputService")

-- ============================================
-- CONFIGURAÇÕES
-- ============================================
local hitboxSize = 2.5
local maxHitbox = 5000

-- ============================================
-- CRIANDO GUI
-- ============================================
local gui = Instance.new("ScreenGui")
gui.Name = "NoobHub"
gui.ResetOnSpawn = false
gui.Parent = player.PlayerGui

-- Frame Principal (Amarelo)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 300, 0, 200)
mainFrame.Position = UDim2.new(0.5, -150, 0.5, -100)
mainFrame.BackgroundColor3 = Color3.fromRGB(255, 215, 0) -- Amarelo
mainFrame.BackgroundTransparency = 0.15
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = gui

-- Arredondamento
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = mainFrame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 50)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
title.BackgroundTransparency = 0.3
title.Text = "✦ NoobHub ✦"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = mainFrame

-- Label Hitbox
local hitboxLabel = Instance.new("TextLabel")
hitboxLabel.Size = UDim2.new(0.4, -10, 0, 30)
hitboxLabel.Position = UDim2.new(0, 10, 0, 70)
hitboxLabel.BackgroundTransparency = 1
hitboxLabel.Text = "📐 Hitbox:"
hitboxLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
hitboxLabel.TextScaled = true
hitboxLabel.Font = Enum.Font.GothamBold
hitboxLabel.Parent = mainFrame

-- TextBox Hitbox
local hitboxBox = Instance.new("TextBox")
hitboxBox.Size = UDim2.new(0.4, -10, 0, 35)
hitboxBox.Position = UDim2.new(0.5, 10, 0, 67)
hitboxBox.BackgroundColor3 = Color3.fromRGB(30, 30, 50)
hitboxBox.BackgroundTransparency = 0.3
hitboxBox.Text = "2.5"
hitboxBox.TextColor3 = Color3.fromRGB(255, 215, 0)
hitboxBox.TextScaled = true
hitboxBox.Font = Enum.Font.GothamBold
hitboxBox.Parent = mainFrame

-- Arredondar TextBox
local boxCorner = Instance.new("UICorner")
boxCorner.CornerRadius = UDim.new(0, 8)
boxCorner.Parent = hitboxBox

-- Label de status
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -20, 0, 30)
statusLabel.Position = UDim2.new(0, 10, 0, 130)
statusLabel.BackgroundTransparency = 1
statusLabel.Text = "Hitbox: 2.5"
statusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
statusLabel.TextScaled = true
statusLabel.Font = Enum.Font.Gotham
statusLabel.Parent = mainFrame

-- Label de limite
local limitLabel = Instance.new("TextLabel")
limitLabel.Size = UDim2.new(1, -20, 0, 20)
limitLabel.Position = UDim2.new(0, 10, 0, 165)
limitLabel.BackgroundTransparency = 1
limitLabel.Text = "Limite: 5000"
limitLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
limitLabel.TextScaled = true
limitLabel.Font = Enum.Font.Gotham
limitLabel.Parent = mainFrame

-- Função para atualizar hitbox
hitboxBox.FocusLost:Connect(function()
    local value = tonumber(hitboxBox.Text)
    if value then
        if value <= maxHitbox and value >= 0.1 then
            hitboxSize = value
            statusLabel.Text = "Hitbox: " .. string.format("%.1f", hitboxSize)
            hitboxBox.Text = string.format("%.1f", hitboxSize)
        else
            hitboxBox.Text = string.format("%.1f", hitboxSize)
        end
    else
        hitboxBox.Text = string.format("%.1f", hitboxSize)
    end
end)

-- Função de Drag
local function createDragButton(frame)
    local dragBtn = Instance.new("TextButton")
    dragBtn.Size = UDim2.new(0, 40, 0, 40)
    dragBtn.Position = UDim2.new(0, 0, 0, 0)
    dragBtn.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
    dragBtn.BackgroundTransparency = 0.2
    dragBtn.Text = "≡"
    dragBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    dragBtn.TextScaled = true
    dragBtn.Font = Enum.Font.GothamBold
    dragBtn.Parent = frame
    
    local dragging = false
    local startPos, startMouse
    
    dragBtn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            startPos = frame.Position
            startMouse = input.Position
        end
    end)
    
    dragBtn.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    
    dragBtn.InputChanged:Connect(function(input)
        if (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) and dragging then
            local delta = input.Position - startMouse
            frame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)
end

createDragButton(mainFrame)

-- Botão Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, 40, 0, 40)
minimizeBtn.Position = UDim2.new(1, -45, 0, 5)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
minimizeBtn.BackgroundTransparency = 0.2
minimizeBtn.Text = "−"
minimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeBtn.TextScaled = true
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.Parent = mainFrame

local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0, 8)
minCorner.Parent = minimizeBtn

local isMinimized = false
local guiElements = {}

for _, child in pairs(mainFrame:GetChildren()) do
    if child ~= title and child ~= minimizeBtn then
        table.insert(guiElements, child)
    end
end

minimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        mainFrame.Size = UDim2.new(0, 300, 0, 50)
        minimizeBtn.Text = "+"
        for _, element in pairs(guiElements) do
            element.Visible = false
        end
    else
        mainFrame.Size = UDim2.new(0, 300, 0, 200)
        minimizeBtn.Text = "−"
        for _, element in pairs(guiElements) do
            element.Visible = true
        end
    end
end)

-- ============================================
-- NOTIFICAÇÃO
-- ============================================
local function showNotification(text)
    local notif = Instance.new("TextLabel")
    notif.Size = UDim2.new(0, 300, 0, 40)
    notif.Position = UDim2.new(0.5, -150, 0, 10)
    notif.BackgroundColor3 = Color3.fromRGB(255, 215, 0)
    notif.BackgroundTransparency = 0.1
    notif.Text = text
    notif.TextColor3 = Color3.fromRGB(255, 255, 255)
    notif.TextScaled = true
    notif.Font = Enum.Font.GothamBold
    notif.Parent = gui
    
    local notifCorner = Instance.new("UICorner")
    notifCorner.CornerRadius = UDim.new(0, 10)
    notifCorner.Parent = notif
    
    game:GetService("Debris"):AddItem(notif, 3)
end

-- ============================================
-- INFORMAÇÕES
-- ============================================
print("═══════════════════════════════════")
print("✦ NoobHub Carregado! ✦")
print("📐 Hitbox ajustável")
print("Limite máximo: 5000")
print("═══════════════════════════════════")

showNotification("✦ NoobHub Carregado! ✦")
showNotification("📐 Hitbox: " .. hitboxSize)

-- Atualizar título com hitbox atual
title.Text = "✦ NoobHub - " .. string.format("%.1f", hitboxSize) .. " ✦"

-- Atualizar hitbox quando mudar
hitboxBox.FocusLost:Connect(function()
    title.Text = "✦ NoobHub - " .. string.format("%.1f", hitboxSize) .. " 
