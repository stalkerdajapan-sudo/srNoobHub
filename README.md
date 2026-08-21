--[[
    NoobHub - God Mode com Frame Separado
    Seu corpo real fica jogando, frame fica no topo do mapa
]]

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NoobHub"
ScreenGui.Parent = Player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- Criar Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 250, 0, 80)
MainFrame.Position = UDim2.new(0.5, -125, 0.1, -40)
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

-- Botão God Mode
local GodButton = Instance.new("TextButton")
GodButton.Size = UDim2.new(0.9, 0, 0, 30)
GodButton.Position = UDim2.new(0.05, 0, 0.5, 0)
GodButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
GodButton.BorderSizePixel = 0
GodButton.Text = "🛡️ ATIVAR GOD MODE"
GodButton.TextColor3 = Color3.fromRGB(255, 255, 255)
GodButton.TextSize = 12
GodButton.Font = Enum.Font.GothamBold
GodButton.Parent = MainFrame

local GodCorner = Instance.new("UICorner")
GodCorner.CornerRadius = UDim.new(0, 5)
GodCorner.Parent = GodButton

-- Variáveis
local isGodMode = false
local isMinimized = false
local originalSize = MainFrame.Size
local fakeCharacter = nil
local realCharacter = nil
local cameraOffset = Vector3.new(0, 500, 0) -- Altura do frame no topo

-- Função para criar personagem falso
local function createFakeCharacter()
    if fakeCharacter and fakeCharacter.Parent then
        fakeCharacter:Destroy()
    end
    
    realCharacter = Player.Character
    if not realCharacter then return end
    
    -- Clonar personagem real
    fakeCharacter = realCharacter:Clone()
    
    -- Remover scripts do clone
    for _, descendant in pairs(fakeCharacter:GetDescendants()) do
        if descendant:IsA("Script") or descendant:IsA("LocalScript") then
            descendant:Destroy()
        end
    end
    
    -- Posicionar personagem falso no topo
    local realRoot = realCharacter:FindFirstChild("HumanoidRootPart")
    if realRoot then
        fakeCharacter:PivotTo(CFrame.new(realRoot.Position + cameraOffset))
    end
    
    fakeCharacter.Parent = workspace
    
    -- Desativar física do fake
    for _, part in pairs(fakeCharacter:GetDescendants()) do
        if part:IsA("BasePart") then
            part.Anchored = true
            part.CanCollide = false
            part.Transparency = 0 -- Visível
        end
    end
    
    return fakeCharacter
end

-- Função para ativar God Mode
local function activateGodMode()
    if isGodMode then return end
    
    realCharacter = Player.Character
    if not realCharacter or not realCharacter:FindFirstChild("Humanoid") or not realCharacter:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    isGodMode = true
    GodButton.Text = "🛡️ DESATIVAR GOD MODE"
    GodButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    
    -- Vida infinita no personagem real
    local humanoid = realCharacter:FindFirstChild("Humanoid")
    humanoid.MaxHealth = math.huge
    humanoid.Health = math.huge
    
    -- Criar personagem falso no topo
    fakeCharacter = createFakeCharacter()
    
    -- Manter fake no topo enquanto modo deus ativo
    RunService.Heartbeat:Connect(function()
        if isGodMode and fakeCharacter and fakeCharacter.Parent and realCharacter and realCharacter.Parent then
            local realRoot = realCharacter:FindFirstChild("HumanoidRootPart")
            local fakeRoot = fakeCharacter:FindFirstChild("HumanoidRootPart")
            
            if realRoot and fakeRoot then
                -- Atualizar posição do fake para seguir o real (no topo)
                fakeRoot.CFrame = CFrame.new(realRoot.Position + cameraOffset)
                
                -- Sincronizar animações
                local realHumanoid = realCharacter:FindFirstChild("Humanoid")
                local fakeHumanoid = fakeCharacter:FindFirstChild("Humanoid")
                
                if realHumanoid and fakeHumanoid then
                    fakeHumanoid.WalkSpeed = realHumanoid.WalkSpeed
                    fakeHumanoid.Jump = realHumanoid.Jump
                end
            end
        end
    end)
    
    -- Proteger contra dano
    realCharacter:FindFirstChild("Humanoid").HealthChanged:Connect(function(health)
        if isGodMode and health < math.huge then
            realCharacter:FindFirstChild("Humanoid").Health = math.huge
        end
    end)
end

-- Função para desativar God Mode
local function deactivateGodMode()
    if not isGodMode then return end
    
    isGodMode = false
    GodButton.Text = "🛡️ ATIVAR GOD MODE"
    GodButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    
    -- Restaurar vida normal
    if realCharacter and realCharacter:FindFirstChild("Humanoid") then
        realCharacter:FindFirstChild("Humanoid").MaxHealth = 100
        realCharacter:FindFirstChild("Humanoid").Health = 100
    end
    
    -- Remover personagem falso
    if fakeCharacter and fakeCharacter.Parent then
        fakeCharacter:Destroy()
    end
    fakeCharacter = nil
end

-- Toggle God Mode
GodButton.MouseButton1Click:Connect(function()
    if isGodMode then
        deactivateGodMode()
    else
        activateGodMode()
    end
end)

-- Minimizar
MinimizeButton.MouseButton1Click:Connect(function()
    if isMinimized then
        MainFrame.Size = originalSize
        GodButton.Visible = true
        MinimizeButton.Text = "—"
        isMinimized = false
    else
        MainFrame.Size = UDim2.new(0, 250, 0, 35)
        GodButton.Visible = false
        MinimizeButton.Text = "+"
        isMinimized = true
    end
end)

-- Limpar quando destruir
ScreenGui.Destroying:Connect(function()
    deactivateGodMode()
end)

print("⚡ NoobHub - God Mode com Frame Separado carregado!")
