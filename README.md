--[[
    SCRIPT: SrNoobHub - AIMLOCK PRO (Players + NPCs)
    LOCAL: StarterGui (ScreenGui)
    AUTOR: SrNoob
    VERSÃO: 6.0 - DUAL TARGET
]]

local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local runService = game:GetService("RunService")
local userInputService = game:GetService("UserInputService")
local camera = workspace.CurrentCamera
local heartbeat = game:GetService("RunService").Heartbeat

-- CONFIGURAÇÕES DO AIMBOT
local aimbotActive = true
local smoothness = 0.25
local fovRadius = 500
local hitboxSize = 2.5
local aimPart = "Head"
local targetMode = "Both" -- "Players", "NPCs", "Both"

-- Criando a ScreenGui MODERNA
local gui = Instance.new("ScreenGui")
gui.Name = "SrNoobHub"
gui.ResetOnSpawn = false
gui.Parent = player.PlayerGui

-- CORES MODERNAS (Amarelo + Verde + Azul)
local colors = {
    primary = Color3.fromRGB(255, 215, 0),    -- Amarelo dourado
    secondary = Color3.fromRGB(0, 255, 150),   -- Verde neon
    accent = Color3.fromRGB(0, 150, 255),      -- Azul vibrante
    dark = Color3.fromRGB(15, 15, 25),         -- Fundo escuro
    text = Color3.fromRGB(255, 255, 255),      -- Branco
    hover = Color3.fromRGB(255, 230, 50),      -- Amarelo claro
}

-- FUNÇÃO PARA DETECTAR PLAYERS E NPCS
local function getAllTargets()
    local targets = {}
    
    -- 1. DETECTAR PLAYERS
    if targetMode == "Players" or targetMode == "Both" then
        for _, otherPlayer in pairs(game.Players:GetPlayers()) do
            if otherPlayer ~= player then
                local character = otherPlayer.Character
                if character and character:FindFirstChild("Head") then
                    table.insert(targets, {
                        type = "Player",
                        name = otherPlayer.Name,
                        character = character,
                        head = character.Head,
                        isPlayer = true
                    })
                end
            end
        end
    end
    
    -- 2. DETECTAR NPCS (Humanoids)
    if targetMode == "NPCs" or targetMode == "Both" then
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("Model") and obj:FindFirstChild("Humanoid") then
                local humanoid = obj:FindFirstChild("Humanoid")
                if humanoid and humanoid.Health > 0 then
                    local head = obj:FindFirstChild("Head")
                    if head and not obj:FindFirstChild("HumanoidRootPart"):FindFirstChild("Player") then
                        -- Verifica se não é um player
                        local isPlayer = false
                        for _, p in pairs(game.Players:GetPlayers()) do
                            if p.Character == obj then
                                isPlayer = true
                                break
                            end
                        end
                        if not isPlayer then
                            table.insert(targets, {
                                type = "NPC",
                                name = obj.Name or "NPC",
                                character = obj,
                                head = head,
                                isPlayer = false
                            })
                        end
                    end
                end
            end
        end
    end
    
    return targets
end

-- FUNÇÃO DE AIMLOCK MELHORADA (Players + NPCs)
local function getClosestTarget()
    local closestTarget = nil
    local closestDistance = math.huge
    local targets = getAllTargets()
    
    for _, target in pairs(targets) do
        if target.head then
            local headPos = target.head.Position
            local screenPos, onScreen = camera:WorldToScreenPoint(headPos)
            
            if onScreen then
                local mousePos = Vector2.new(mouse.X, mouse.Y)
                local targetPos = Vector2.new(screenPos.X, screenPos.Y)
                local distance = (mousePos - targetPos).Magnitude
                
                local adjustedDistance = distance / hitboxSize
                
                if adjustedDistance < closestDistance and distance < fovRadius then
                    closestDistance = adjustedDistance
                    closestTarget = target
                end
            end
        end
    end
    
    return closestTarget, closestDistance
end

-- FUNÇÃO DE AIMLOCK NA CABEÇA
local function aimlockHead(target)
    if not target or not target.head then return end
    
    local targetPos = target.head.Position
    local currentPos = camera.CFrame.Position
    local newCFrame = CFrame.new(currentPos, targetPos)
    
    if smoothness > 0 then
        camera.CFrame = camera.CFrame:Lerp(newCFrame, smoothness)
    else
        camera.CFrame = newCFrame
    end
end

-- LOOP PRINCIPAL
local currentTarget = nil
local targetDistance = 0

heartbeat:Connect(function()
    if not aimbotActive then return end
    
    local target, dist = getClosestTarget()
    if target then
        currentTarget = target
        targetDistance = dist
        aimlockHead(target)
    else
        currentTarget = nil
        targetDistance = 0
    end
end)

runService.RenderStepped:Connect(function()
    if not aimbotActive then return end
    if currentTarget then
        aimlockHead(currentTarget)
    end
end)

mouse.Move:Connect(function()
    if not aimbotActive then return end
    if currentTarget then
        aimlockHead(currentTarget)
    end
end)

-- GUI MODERNA (Amarelo + Verde + Azul)
local function createDragButton(frame)
    local dragBtn = Instance.new("TextButton")
    dragBtn.Size = UDim2.new(0, 35, 0, 35)
    dragBtn.Position = UDim2.new(0, 0, 0, 0)
    dragBtn.BackgroundColor3 = colors.accent
    dragBtn.BackgroundTransparency = 0.3
    dragBtn.Text = "◆"
    dragBtn.TextColor3 = colors.primary
    dragBtn.TextScaled = true
    dragBtn.Font = Enum.Font.GothamBold
    dragBtn.Parent = frame
    
    local dragging = false
    local startPos, startMouse
    
    dragBtn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            startPos = frame.Position
            startMouse = input.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    dragBtn.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
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

-- FRAME PRINCIPAL MODERNO
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 280, 0, 270)
mainFrame.Position = UDim2.new(0.5, -140, 0.5, -135)
mainFrame.BackgroundColor3 = colors.dark
mainFrame.BackgroundTransparency = 0.05
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = gui

-- Borda gradiente moderna
local borderGradient = Instance.new("UIGradient")
borderGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, colors.primary),
    ColorSequenceKeypoint.new(0.5, colors.secondary),
    ColorSequenceKeypoint.new(1, colors.accent)
})
borderGradient.Parent = mainFrame

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 15)
corner.Parent = mainFrame

-- Sombra
local shadow = Instance.new("ImageLabel")
shadow.Size = UDim2.new(1, 20, 1, 20)
shadow.Position = UDim2.new(0, -10, 0, -10)
shadow.BackgroundTransparency = 1
shadow.Image = "rbxassetid://131604333"
shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
shadow.ImageTransparency = 0.5
shadow.ZIndex = 0
shadow.Parent = mainFrame

-- TÍTULO MODERNO
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 45)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = colors.primary
title.BackgroundTransparency = 0.15
title.Text = "✦ SrNoobHub ✦"
title.TextColor3 = colors.text
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 15)
titleCorner.Parent = title

-- BOTÃO MINIMIZAR
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, 35, 0, 35)
minimizeBtn.Position = UDim2.new(1, -40, 0, 5)
minimizeBtn.BackgroundColor3 = colors.secondary
minimizeBtn.BackgroundTransparency = 0.3
minimizeBtn.Text = "−"
minimizeBtn.TextColor3 = colors.text
minimizeBtn.TextScaled = true
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.Parent = mainFrame

local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0, 8)
minCorner.Parent = minimizeBtn

-- BOTÃO TOGGLE
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 180, 0, 38)
toggleBtn.Position = UDim2.new(0.5, -90, 0, 60)
toggleBtn.BackgroundColor3 = colors.secondary
toggleBtn.Text = "⚡ AIMLOCK: ON"
toggleBtn.TextColor3 = colors.text
toggleBtn.TextScaled = true
toggleBtn.Font = Enum.Font.GothamBold
toggleBtn.Parent = mainFrame

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0, 10)
btnCorner.Parent = toggleBtn

-- Efeito glow no botão
local glow = Instance.new("ImageLabel")
glow.Size = UDim2.new(1, 10, 1, 10)
glow.Position = UDim2.new(0, -5, 0, -5)
glow.BackgroundTransparency = 1
glow.Image = "rbxassetid://131604333"
glow.ImageColor3 = colors.secondary
glow.ImageTransparency = 0.7
glow.ZIndex = 0
glow.Parent = toggleBtn

toggleBtn.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive
    if aimbotActive then
        toggleBtn.BackgroundColor3 = colors.secondary
        toggleBtn.Text = "⚡ AIMLOCK: ON"
        glow.ImageColor3 = colors.secondary
    else
        toggleBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        toggleBtn.Text = "⛔ AIMLOCK: OFF"
        glow.ImageColor3 = Color3.fromRGB(200, 50, 50)
    end
end)

-- SELECTOR DE ALVO (Players/NPCs/Both)
local targetLabel = Instance.new("TextLabel")
targetLabel.Size = UDim2.new(0.3, -5, 0, 20)
targetLabel.Position = UDim2.new(0, 5, 0, 115)
targetLabel.BackgroundTransparency = 1
targetLabel.Text = "🎯 Alvo:"
targetLabel.TextColor3 = colors.primary
targetLabel.TextScaled = true
targetLabel.Font = Enum.Font.GothamBold
targetLabel.Parent = mainFrame

local targetSelector = Instance.new("TextButton")
targetSelector.Size = UDim2.new(0.5, -5, 0, 25)
targetSelector.Position = UDim2.new(0.45, 5, 0, 112)
targetSelector.BackgroundColor3 = colors.accent
targetSelector.BackgroundTransparency = 0.3
targetSelector.Text = "Ambos"
targetSelector.TextColor3 = colors.text
targetSelector.TextScaled = true
targetSelector.Font = Enum.Font.GothamBold
targetSelector.Parent = mainFrame

local targetCorner = Instance.new("UICorner")
targetCorner.CornerRadius = UDim.new(0, 6)
targetCorner.Parent = targetSelector

local targetModes = {"Ambos", "Players", "NPCs"}
local currentModeIndex = 1

targetSelector.MouseButton1Click:Connect(function()
    currentModeIndex = currentModeIndex % 3 + 1
    local mode = targetModes[currentModeIndex]
    targetSelector.Text = mode
    
    if mode == "Ambos" then
        targetMode = "Both"
        targetSelector.BackgroundColor3 = colors.primary
    elseif mode == "Players" then
        targetMode = "Players"
        targetSelector.BackgroundColor3 = colors.accent
    else
        targetMode = "NPCs"
        targetSelector.BackgroundColor3 = colors.secondary
    end
end)

-- SLIDER HITBOX
local hitboxLabel = Instance.new("TextLabel")
hitboxLabel.Size = UDim2.new(0.4, -5, 0, 20)
hitboxLabel.Position = UDim2.new(0, 5, 0, 150)
hitboxLabel.BackgroundTransparency = 1
hitboxLabel.Text = "📐 Hitbox:"
hitboxLabel.TextColor3 = colors.accent
hitboxLabel.TextScaled = true
hitboxLabel.Font = Enum.Font.GothamBold
hitboxLabel.Parent = mainFrame

local hitboxSlider = Instance.new("TextBox")
hitboxSlider.Size = UDim2.new(0.3, -5, 0, 20)
hitboxSlider.Position = UDim2.new(0.45, 5, 0, 150)
hitboxSlider.BackgroundColor3 = colors.dark
hitboxSlider.BackgroundTransparency = 0.5
hitboxSlider.Text = "2.5"
hitboxSlider.TextColor3 = colors.primary
hitboxSlider.TextScaled = true
hitboxSlider.Font = Enum.Font.GothamBold
hitboxSlider.Parent = mainFrame

local hitboxCorner = Instance.new("UICorner")
hitboxCorner.CornerRadius = UDim.new(0, 6)
hitboxCorner.Parent = hitboxSlider

hitboxSlider.FocusLost:Connect(function()
    local value = tonumber(hitboxSlider.Text)
    if value and value >= 0.5 and value <= 10 then
        hitboxSize = value
    else
        hitboxSlider.Text = tostring(hitboxSize)
    end
end)

-- SLIDER SENSIBILIDADE
local sensLabel = Instance.new("TextLabel")
sensLabel.Size = UDim2.new(0.4, -5, 0, 20)
sensLabel.Position = UDim2.new(0, 5, 0, 175)
sensLabel.BackgroundTransparency = 1
sensLabel.Text = "⚡ Suavidade:"
sensLabel.TextColor3 = colors.secondary
sensLabel.TextScaled = true
sensLabel.Font = Enum.Font.GothamBold
sensLabel.Parent = mainFrame

local sensSlider = Instance.new("TextBox")
sensSlider.Size = UDim2.new(0.3, -5, 0, 20)
sensSlider.Position = UDim2.new(0.45, 5, 0, 175)
sensSlider.BackgroundColor3 = colors.dark
sensSlider.BackgroundTransparency = 0.5
sensSlider.Text = "0.25"
sensSlider.TextColor3 = colors.primary
sensSlider.TextScaled = true
sensSlider.Font = Enum.Font.GothamBold
sensSlider.Parent = mainFrame

local sensCorner = Instance.new("UICorner")
sensCorner.CornerRadius = UDim.new(0, 6)
sensCorner.Parent = sensSlider

sensSlider.FocusLost:Connect(function()
    local value = tonumber(sensSlider.Text)
    if value and value >= 0.01 and value <= 1 then
        smoothness = value
    else
        sensSlider.Text = tostring(smoothness)
    end
end)

-- SLIDER FOV
local fovLabel = Instance.new("TextLabel")
fovLabel.Size = UDim2.new(0.4, -5, 0, 20)
fovLabel.Position = UDim2.new(0, 5, 0, 200)
fovLabel.BackgroundTransparency = 1
fovLabel.Text = "👁️ FOV:"
fovLabel.TextColor3 = colors.primary
fovLabel.TextScaled = true
fovLabel.Font = Enum.Font.GothamBold
fovLabel.Parent = mainFrame

local fovSlider = Instance.new("TextBox")
fovSlider.Size = UDim2.new(0.3, -5, 0, 20)
fovSlider.Position = UDim2.new(0.45, 5, 0, 200)
fovSlider.BackgroundColor3 = colors.dark
fovSlider.BackgroundTransparency = 0.5
fovSlider.Text = "500"
fovSlider.TextColor3 = colors.primary
fovSlider.TextScaled = true
fovSlider.Font = Enum.Font.GothamBold
fovSlider.Parent = mainFrame

local fovCorner = Instance.new("UICorner")
fovCorner.CornerRadius = UDim.new(0, 6)
fovCorner.Parent = fovSlider

fovSlider.FocusLost:Connect(function()
    local value = tonumber(fovSlider.Text)
    if value and value >= 50 and value <= 1000 then
        fovRadius = value
    else
        fovSlider.Text = tostring(fovRadius)
    end
end)

-- STATUS LABEL
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, 0, 0, 30)
statusLabel.Position = UDim2.new(0, 0, 0, 230)
statusLabel.BackgroundTransparency = 1
statusLabel.Text = "💡 F1 toggle | Alvo: Nenhum"
statusLabel.TextColor3 = colors.text
statusLabel.TextScaled = true
statusLabel.Font = Enum.Font.Gotham
statusLabel.Parent = mainFrame

-- ATUALIZAR STATUS
runService.RenderStepped:Connect(function()
    if not aimbotActive then
        statusLabel.Text = "💡 F1 toggle | AIMLOCK: OFF"
        return
    end
    
    if currentTarget then
        local tipo = currentTarget.isPlayer and "👤" or "🤖"
        local hitboxStatus = string.format("(H:%.1f)", hitboxSize)
        statusLabel.Text = string.format("%s %s %s (%.0fpx)", 
            tipo, currentTarget.name, hitboxStatus, targetDistance)
    else
        statusLabel.Text = "💡 F1 toggle | Procurando alvos..."
    end
end)

-- DRAG BUTTON
createDragButton(mainFrame)

-- TECLA F1
userInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.F1 then
        toggleBtn.MouseButton1Click:Fire()
    end
end)

-- FUNÇÃO DE MINIMIZAR
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
        mainFrame.Size = UDim2.new(0, 280, 0, 45)
        minimizeBtn.Text = "+"
        for _, element in pairs(guiElements) do
            element.Visible = false
        end
    else
        mainFrame.Size = UDim2.new(0, 280, 0, 270)
        minimizeBtn.Text = "−"
        for _, element in pairs(guiElements) do
            element.Visible = true
        end
    end
end)

-- SISTEMA DE NOTIFICAÇÃO MODERNA
local function createNotification(text, color)
    local notif = Instance.new("TextLabel")
    notif.Size = UDim2.new(0, 300, 0, 40)
    notif.Position = UDim2.new(0.5, -150, 0, 10)
    notif.BackgroundColor3 = color or colors.primary
    notif.Text = text
    notif.TextColor3 = colors.text
    notif.TextScaled = true
    notif.Font = Enum.Font.GothamBold
    notif.BackgroundTransparency = 0.1
    notif.Parent = gui
    
    local notifCorner = Instance.new("UICorner")
    notifCorner.CornerRadius = UDim.new(0, 10)
    notifCorner.Parent = notif
    
    local glowNotif = Instance.new("UIGradient")
    glowNotif.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, colors.primary),
        ColorSequenceKeypoint.new(0.5, colors.secondary),
        ColorSequenceKeypoint.new(1, colors.accent)
    })
    glowNotif.Parent = notif
    
    game:GetService("Debris"):AddItem(notif, 3)
end

-- NOTIFICAÇÕES INICIAIS
createNotification("✦ SrNoobHub v6.0 - AIMLOCK PRO ✦", colors.primary)
createNotification("🎯 Players + NPCs detectados!", colors.secondary)
createNotification("📐 Hitbox ajustável | F1 para toggle", colors.accent)

-- LOOP DE SEGURANÇA
spawn(function()
    while wait(5) do
        if aimbotActive and not currentTarget then
            local target, dist = getClosestTarget()
            if target then
                currentTarget = target
                targetDistance = dist
                local tipo = target.isPlayer and "Player" or "NPC"
                print("🎯 ALVO ENCONTRADO: " .. target.name .. " (" .. tipo .. ")")
            end
        end
    end
end)

-- INFO CONSOLE
print("═══════════════════════════════════")
print("✨ SrNoobHub v6.0 - AIMLOCK PRO ✨")
print("═══════════════════════════════════")
print("🎯 Players + NPCs detectados!")
print("👤 Players e 🤖 NPCs")
print("📐 Hitbox ajustável: " .. hitboxSize)
print("⚡ Suavidade: " .. smoothness)
print("👁️ FOV: " .. fovRadius)
print("💡 F1 para ativar/desativar")
print("═══════════════════════════════════")  
        -- Atualiza o aimbot (precisa modificar o script original)
        _G.AimbotActive = aimActive
    end)
    
    local invisActive = false
    invisBtn.MouseButton1Click:Connect(function()
        local remote = ReplicatedStorage:FindFirstChild("ToggleInvisibility")
        if remote then
            remote:FireServer()
            invisActive = not invisActive
            invisBtn.BackgroundColor3 = invisActive and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
            invisBtn.Text = invisActive and "INV: ON" or "INV: OFF"
        end
    end)
    
    -- Remove o botão antigo (se existir)
    local oldBtn = mainFrame:FindFirstChild("ToggleBtn")
    if oldBtn then
        oldBtn:Destroy()
    end
end

-- Substitua a função createInvisibilityButton por esta se quiser ambos os botões
-- createBothButtons()
