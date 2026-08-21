--[[
    Script de Reverse Aimbot (LocalScript)
    Descrição: Move a câmera para longe dos jogadores quando eles entram no FOV.
    Criado para Roblox Luau.
]]

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

-- Jogador Local
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Referências da GUI
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local DragBar = Instance.new("Frame")
local TitleLabel = Instance.new("TextLabel")
local MinimizeButton = Instance.new("TextButton")
local ToggleButton = Instance.new("TextButton")
local StatusLabel = Instance.new("TextLabel")
local FOVSlider = Instance.new("Frame")
local FOVLabel = Instance.new("TextLabel")
local FOVValue = Instance.new("TextLabel")
local SmoothSlider = Instance.new("Frame")
local SmoothLabel = Instance.new("TextLabel")
local SmoothValue = Instance.new("TextLabel")

-- Referências dos sliders
local FOVSliderBar
local FOVSliderButton
local SmoothSliderBar
local SmoothSliderButton

-- Configurações do Script
local Settings = {
    Enabled = false,        -- Ligado/Desligado
    FOV = 120,             -- Raio do campo de visão em graus
    Smoothness = 5,        -- Suavidade (maior = mais suave porém mais lento)
    MinFOV = 30,
    MaxFOV = 180,
    MinSmoothness = 1,
    MaxSmoothness = 20
}

-- Configurações da GUI
local GUI = {
    Minimized = false,     -- Minimizado?
    Dragging = false,      -- Arrastando?
    DragOffset = Vector2.new(0, 0)  -- Offset do arraste
}

-- Variáveis de Estado
local CurrentTarget = nil
local LastTargetPosition = Vector3.new(0, 0, 0)

--[[ Funções Auxiliares ]]

-- Converte graus para radianos
local function DegToRad(deg)
    return deg * math.pi / 180
end

-- Calcula o ângulo entre dois vetores
local function GetAngleBetweenVectors(vec1, vec2)
    return math.deg(math.acos(math.clamp(vec1:Dot(vec2) / (vec1.Magnitude * vec2.Magnitude), -1, 1)))
end

--[[ Lógica Principal do Reverse Aimbot ]]

-- Encontra o jogador mais próximo da mira dentro do FOV
local function GetTargetWithinFOV()
    if not Settings.Enabled then return nil end
    
    local mouseDirection = Mouse.UnitRay.Direction
    local closestAngle = Settings.FOV
    local target = nil
    local targetPosition = Vector3.new(0, 0, 0)
    
    -- Verifica todos os jogadores
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end  -- Pula o próprio jogador
        if not player.Character or not player.Character:FindFirstChild("Head") then continue end
        
        local head = player.Character.Head
        local headPosition = head.Position
        
        -- Calcula o ângulo entre a direção do mouse e a cabeça do jogador
        local directionToHead = (headPosition - Camera.CFrame.Position).Unit
        local angle = GetAngleBetweenVectors(mouseDirection, directionToHead)
        
        -- Verifica se está dentro do FOV e é o mais próximo
        if angle < closestAngle then
            closestAngle = angle
            target = player
            targetPosition = headPosition
        end
    end
    
    if target then
        CurrentTarget = target
        LastTargetPosition = targetPosition
        return target, targetPosition
    end
    
    CurrentTarget = nil
    return nil
end

-- Calcula o ponto anti-mira (direção oposta ao alvo)
local function GetAntiAimPoint(targetPos)
    local cameraPos = Camera.CFrame.Position
    local directionToTarget = (targetPos - cameraPos).Unit
    
    -- Move a câmera na direção oposta ao alvo
    local antiDirection = -directionToTarget * 10  -- Multiplicador para força do efeito
    return cameraPos + antiDirection
end

-- Aplica movimento suave na câmera
local function SmoothCameraMovement(targetPoint, smoothness)
    local currentCFrame = Camera.CFrame
    local currentPos = currentCFrame.Position
    local targetPos = targetPoint
    
    -- Interpola a posição
    local newPos = currentPos:Lerp(targetPos, 1 / smoothness)
    
    -- Olha para o ponto alvo
    local newCFrame = CFrame.lookAt(newPos, currentCFrame.Position + currentCFrame.LookVector * 100)
    
    Camera.CFrame = newCFrame
end

-- Loop principal de atualização
local function OnRenderStep()
    if not Settings.Enabled then return end
    
    local target, targetPos = GetTargetWithinFOV()
    if target and targetPos then
        -- Calcula o ponto anti-mira
        local antiPoint = GetAntiAimPoint(targetPos)
        
        -- Move a câmera suavemente para longe
        SmoothCameraMovement(antiPoint, Settings.Smoothness)
    end
end

--[[ Criação da GUI ]]

local function CreateGUI()
    -- ScreenGui
    ScreenGui.Name = "ReverseAimbotGUI"
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    ScreenGui.ResetOnSpawn = false
    
    -- MainFrame (Janela Principal)
    MainFrame.Name = "MainFrame"
    MainFrame.Parent = ScreenGui
    MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    MainFrame.BorderColor3 = Color3.fromRGB(50, 50, 50)
    MainFrame.Size = UDim2.new(0, 300, 0, 250)
    MainFrame.Position = UDim2.new(0.5, -150, 0.5, -125)
    MainFrame.Active = true
    
    -- Drag Bar (Barra para arrastar)
    DragBar.Name = "DragBar"
    DragBar.Parent = MainFrame
    DragBar.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    DragBar.Size = UDim2.new(1, 0, 0, 30)
    
    -- Título
    TitleLabel.Name = "TitleLabel"
    TitleLabel.Parent = DragBar
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Size = UDim2.new(1, -50, 1, 0)
    TitleLabel.Text = "Reverse Aimbot"
    TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
    TitleLabel.Font = Enum.Font.SourceSansBold
    TitleLabel.TextSize = 16
    
    -- Botão Minimizar
    MinimizeButton.Name = "MinimizeButton"
    MinimizeButton.Parent = DragBar
    MinimizeButton.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    MinimizeButton.Size = UDim2.new(0, 30, 1, 0)
    MinimizeButton.Position = UDim2.new(1, -30, 0, 0)
    MinimizeButton.Text = "-"
    MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    MinimizeButton.Font = Enum.Font.SourceSansBold
    MinimizeButton.TextSize = 18
    
    -- Botão Ligar/Desligar
    ToggleButton.Name = "ToggleButton"
    ToggleButton.Parent = MainFrame
    ToggleButton.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    ToggleButton.Size = UDim2.new(0, 100, 0, 30)
    ToggleButton.Position = UDim2.new(0, 20, 0, 45)
    ToggleButton.Text = "Desligar"
    ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleButton.Font = Enum.Font.SourceSansBold
    ToggleButton.TextSize = 14
    
    -- Label de Status
    StatusLabel.Name = "StatusLabel"
    StatusLabel.Parent = MainFrame
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Size = UDim2.new(0, 100, 0, 30)
    StatusLabel.Position = UDim2.new(0, 130, 0, 45)
    StatusLabel.Text = "Status: OFF"
    StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
    StatusLabel.Font = Enum.Font.SourceSansBold
    StatusLabel.TextSize = 14
    
    -- Slider do FOV
    FOVSlider.Name = "FOVSlider"
    FOVSlider.Parent = MainFrame
    FOVSlider.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    FOVSlider.Size = UDim2.new(0, 200, 0, 20)
    FOVSlider.Position = UDim2.new(0, 20, 0, 90)
    
    FOVSliderBar = Instance.new("Frame")
    FOVSliderBar.Name = "SliderBar"
    FOVSliderBar.Parent = FOVSlider
    FOVSliderBar.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
    FOVSliderBar.Size = UDim2.new(Settings.FOV / Settings.MaxFOV, 0, 1, 0)
    
    FOVSliderButton = Instance.new("TextButton")
    FOVSliderButton.Name = "SliderButton"
    FOVSliderButton.Parent = FOVSlider
    FOVSliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    FOVSliderButton.Size = UDim2.new(0, 20, 1, 0)
    FOVSliderButton.Position = UDim2.new(Settings.FOV / Settings.MaxFOV, -10, 0, 0)
    FOVSliderButton.Text = ""
    
    -- Labels do FOV
    FOVLabel.Name = "FOVLabel"
    FOVLabel.Parent = MainFrame
    FOVLabel.BackgroundTransparency = 1
    FOVLabel.Size = UDim2.new(0, 60, 0, 20)
    FOVLabel.Position = UDim2.new(0, 20, 0, 75)
    FOVLabel.Text = "Raio FOV"
    FOVLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    FOVLabel.Font = Enum.Font.SourceSans
    FOVLabel.TextSize = 12
    
    FOVValue.Name = "FOVValue"
    FOVValue.Parent = MainFrame
    FOVValue.BackgroundTransparency = 1
    FOVValue.Size = UDim2.new(0, 50, 0, 20)
    FOVValue.Position = UDim2.new(0, 230, 0, 75)
    FOVValue.Text = tostring(Settings.FOV) .. "°"
    FOVValue.TextColor3 = Color3.fromRGB(255, 255, 255)
    FOVValue.Font = Enum.Font.SourceSans
    FOVValue.TextSize = 12
    
    -- Slider da Suavidade
    SmoothSlider.Name = "SmoothSlider"
    SmoothSlider.Parent = MainFrame
    SmoothSlider.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    SmoothSlider.Size = UDim2.new(0, 200, 0, 20)
    SmoothSlider.Position = UDim2.new(0, 20, 0, 150)
    
    SmoothSliderBar = Instance.new("Frame")
    SmoothSliderBar.Name = "SliderBar"
    SmoothSliderBar.Parent = SmoothSlider
    SmoothSliderBar.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
    SmoothSliderBar.Size = UDim2.new((Settings.Smoothness - Settings.MinSmoothness) / (Settings.MaxSmoothness - Settings.MinSmoothness), 0, 1, 0)
    
    SmoothSliderButton = Instance.new("TextButton")
    SmoothSliderButton.Name = "SliderButton"
    SmoothSliderButton.Parent = SmoothSlider
    SmoothSliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    SmoothSliderButton.Size = UDim2.new(0, 20, 1, 0)
    SmoothSliderButton.Position = UDim2.new((Settings.Smoothness - Settings.MinSmoothness) / (Settings.MaxSmoothness - Settings.MinSmoothness), -10, 0, 0)
    SmoothSliderButton.Text = ""
    
    -- Labels da Suavidade
    SmoothLabel.Name = "SmoothLabel"
    SmoothLabel.Parent = MainFrame
    SmoothLabel.BackgroundTransparency = 1
    SmoothLabel.Size = UDim2.new(0, 80, 0, 20)
    SmoothLabel.Position = UDim2.new(0, 20, 0, 135)
    SmoothLabel.Text = "Suavidade"
    SmoothLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    SmoothLabel.Font = Enum.Font.SourceSans
    SmoothLabel.TextSize = 12
    
    SmoothValue.Name = "SmoothValue"
    SmoothValue.Parent = MainFrame
    SmoothValue.BackgroundTransparency = 1
    SmoothValue.Size = UDim2.new(0, 50, 0, 20)
    SmoothValue.Position = UDim2.new(0, 230, 0, 135)
    SmoothValue.Text = tostring(Settings.Smoothness)
    SmoothValue.TextColor3 = Color3.fromRGB(255, 255, 255)
    SmoothValue.Font = Enum.Font.SourceSans
    SmoothValue.TextSize = 12
    
    return {
        FOVSliderBar = FOVSliderBar,
        FOVSliderButton = FOVSliderButton,
        SmoothSliderBar = SmoothSliderBar,
        SmoothSliderButton = SmoothSliderButton
    }
end

--[[ Funções de Interação dos Sliders ]]

local function SetupSlider(sliderFrame, sliderBar, sliderButton, minValue, maxValue, valueType, updateFunction)
    local isDragging = false
    
    sliderButton.MouseButton1Down:Connect(function()
        isDragging = true
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            isDragging = false
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if not isDragging then return end
        if input.UserInputType ~= Enum.UserInputType.Mouse then return end
        
        local mousePos = UserInputService:GetMouseLocation().X
        local sliderPos = sliderFrame.AbsolutePosition.X
        local sliderSize = sliderFrame.AbsoluteSize.X
        
        local percent = math.clamp((mousePos - sliderPos) / sliderSize, 0, 1)
        local value = math.round(minValue + percent * (maxValue - minValue))
        
        if valueType == "FOV" then
            Settings.FOV = value
            FOVValue.Text = tostring(value) .. "°"
        elseif valueType == "Smoothness" then
            Settings.Smoothness = value
            SmoothValue.Text = tostring(value)
        end
        
        sliderBar.Size = UDim2.new(percent, 0, 1, 0)
        sliderButton.Position = UDim2.new(percent, -10, 0, 0)
        
        if updateFunction then updateFunction(value) end
    end)
end

--[[ Eventos da GUI ]]

local function SetupGUI(guiRefs)
    -- Botão Ligar/Desligar
    ToggleButton.MouseButton1Click:Connect(function()
        Settings.Enabled = not Settings.Enabled
        
        if Settings.Enabled then
            ToggleButton.Text = "Ligar"
            ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
            StatusLabel.Text = "Status: ON"
            StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
        else
            ToggleButton.Text = "Desligar"
            ToggleButton.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
            StatusLabel.Text = "Status: OFF"
            StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        end
    end)
    
    -- Botão Minimizar
    MinimizeButton.MouseButton1Click:Connect(function()
        GUI.Minimized = not GUI.Minimized
        if GUI.Minimized then
            MainFrame.Size = UDim2.new(0, 300, 0, 30)
            MinimizeButton.Text = "+"
            -- Esconde todos os elementos exceto a barra de arraste
            ToggleButton.Visible = false
            StatusLabel.Visible = false
            FOVLabel.Visible = false
            FOVValue.Visible = false
            FOVSlider.Visible = false
            SmoothLabel.Visible = false
            SmoothValue.Visible = false
            SmoothSlider.Visible = false
        else
            MainFrame.Size = UDim2.new(0, 300, 0, 250)
            MinimizeButton.Text = "-"
            ToggleButton.Visible = true
            StatusLabel.Visible = true
            FOVLabel.Visible = true
            FOVValue.Visible = true
            FOVSlider.Visible = true
            SmoothLabel.Visible = true
            SmoothValue.Visible = true
            SmoothSlider.Visible = true
        end
    end)
    
    -- Funcionalidade de Arrastar
    DragBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            GUI.Dragging = true
            GUI.DragOffset = Vector2.new(input.Position.X - MainFrame.AbsolutePosition.X, input.Position.Y - MainFrame.AbsolutePosition.Y)
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Mouse and GUI.Dragging then
            local newX = input.Position.X - GUI.DragOffset.X
            local newY = input.Position.Y - GUI.DragOffset.Y
            -- Limita na tela
            newX = math.clamp(newX, 0, UserInputService.ViewSizeX - MainFrame.AbsoluteSize.X)
            newY = math.clamp(newY, 0, UserInputService.ViewSizeY - MainFrame.AbsoluteSize.Y)
            MainFrame.Position = UDim2.new(0, newX, 0, newY)
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            GUI.Dragging = false
        end
    end)
    
    -- Configura os sliders
    SetupSlider(FOVSlider, guiRefs.FOVSliderBar, guiRefs.FOVSliderButton, Settings.MinFOV, Settings.MaxFOV, "FOV")
    SetupSlider(SmoothSlider, guiRefs.SmoothSliderBar, guiRefs.SmoothSliderButton, Settings.MinSmoothness, Settings.MaxSmoothness, "Smoothness")
end

--[[ Inicialização do Script ]]

local function Init()
    -- Cria a GUI e pega as referências
    local guiRefs = CreateGUI()
    SetupGUI(guiRefs)
    
    -- Conecta o loop principal
    RunService.RenderStepped:Connect(OnRenderStep)
end

-- Inicia o script
Init()
