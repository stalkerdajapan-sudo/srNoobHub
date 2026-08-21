--[[
    SCRIPT: SrNoobHub - Aimbot com Drag e Minimizar
    LOCAL: StarterGui (ScreenGui)
    AUTOR: SrNoob
]]

-- Criando a GUI principal
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local runService = game:GetService("RunService")

-- Criando a ScreenGui
local gui = Instance.new("ScreenGui")
gui.Name = "SrNoobHub"
gui.ResetOnSpawn = false
gui.Parent = player.PlayerGui

-- Função para criar botão de drag
local function createDragButton(frame)
    local dragBtn = Instance.new("TextButton")
    dragBtn.Size = UDim2.new(0, 30, 0, 30)
    dragBtn.Position = UDim2.new(0, 0, 0, 0)
    dragBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    dragBtn.BackgroundTransparency = 0.8
    dragBtn.Text = "≡"
    dragBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    dragBtn.TextScaled = true
    dragBtn.Parent = frame
    
    local dragging = false
    local dragInput, startPos, startMouse
    
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

-- Criando o Frame principal
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 200, 0, 150)
mainFrame.Position = UDim2.new(0.5, -100, 0.5, -75)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BackgroundTransparency = 0.1
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = gui

-- Arredondando bordas
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = mainFrame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
title.Text = "SrNoobHub"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = mainFrame

-- Botão Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, 30, 0, 30)
minimizeBtn.Position = UDim2.new(1, -35, 0, 0)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
minimizeBtn.Text = "−"
minimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeBtn.TextScaled = true
minimizeBtn.Parent = mainFrame

local isMinimized = false
local originalSize = mainFrame.Size
local originalHeight = 150

minimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        mainFrame.Size = UDim2.new(0, 200, 0, 30)
        minimizeBtn.Text = "+"
        -- Esconder elementos
        for _, child in pairs(mainFrame:GetChildren()) do
            if child:IsA("TextLabel") and child ~= title and child ~= minimizeBtn then
                child.Visible = false
            elseif child:IsA("TextButton") and child ~= minimizeBtn and child ~= getChildren(mainFrame) then
                child.Visible = false
            end
        end
    else
        mainFrame.Size = UDim2.new(0, 200, 0, 150)
        minimizeBtn.Text = "−"
        for _, child in pairs(mainFrame:GetChildren()) do
            if child:IsA("TextLabel") and child ~= title and child ~= minimizeBtn then
                child.Visible = true
            elseif child:IsA("TextButton") and child ~= minimizeBtn then
                child.Visible = true
            end
        end
    end
end)

-- Botão de Ativar/Desativar Aimbot
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 100, 0, 30)
toggleBtn.Position = UDim2.new(0.5, -50, 0, 50)
toggleBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 0) -- Verde (Ativado)
toggleBtn.Text = "AIMBOT: ON"
toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleBtn.TextScaled = true
toggleBtn.Font = Enum.Font.GothamBold
toggleBtn.Parent = mainFrame

local aimbotActive = true

toggleBtn.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive
    if aimbotActive then
        toggleBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        toggleBtn.Text = "AIMBOT: ON"
    else
        toggleBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        toggleBtn.Text = "AIMBOT: OFF"
    end
end)

-- Label de informações
local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(1, 0, 0, 20)
infoLabel.Position = UDim2.new(0, 0, 0, 90)
infoLabel.BackgroundTransparency = 1
infoLabel.Text = "Clique para ativar/desativar"
infoLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
infoLabel.TextScaled = true
infoLabel.Font = Enum.Font.Gotham
infoLabel.Parent = mainFrame

-- Sistema de Aimbot
local function getClosestPlayer()
    local closestPlayer = nil
    local closestDistance = math.huge
    
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer ~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild("Head") then
            local headPos = otherPlayer.Character.Head.Position
            local screenPos, onScreen = camera:WorldToScreenPoint(headPos)
            
            if onScreen then
                local distance = (Vector2.new(mouse.X, mouse.Y) - Vector2.new(screenPos.X, screenPos.Y)).Magnitude
                if distance < closestDistance and distance < 500 then -- Distância máxima
                    closestDistance = distance
                    closestPlayer = otherPlayer
                end
            end
        end
    end
    
    return closestPlayer
end

local camera = workspace.CurrentCamera

runService.RenderStepped:Connect(function()
    if not aimbotActive then return end
    
    local target = getClosestPlayer()
    if target and target.Character and target.Character:FindFirstChild("Head") then
        local headPos = target.Character.Head.Position
        camera.CFrame = CFrame.new(camera.CFrame.Position, headPos)
    end
end)

-- Criando botão de drag (no título)
createDragButton(mainFrame)

-- Função auxiliar para pegar filhos (usada no minimize)
function getChildren(frame)
    local children = {}
    for _, child in pairs(frame:GetChildren()) do
        if child:IsA("TextButton") then
            table.insert(children, child)
        end
    end
    return children
end

print("SrNoobHub carregado com sucesso!")
