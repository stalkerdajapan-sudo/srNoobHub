--[[
    NoobHub - Sistema de XP Simples
    Mata NPC e ganha XP
]]

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NoobHub"
ScreenGui.Parent = Player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- Criar Frame principal (CENTRALIZADO)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 300, 0, 200)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -100)
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

-- Nível Label
local LevelLabel = Instance.new("TextLabel")
LevelLabel.Size = UDim2.new(0.9, 0, 0, 25)
LevelLabel.Position = UDim2.new(0.05, 0, 0.25, 0)
LevelLabel.BackgroundTransparency = 1
LevelLabel.Text = "⭐ Nível: 1"
LevelLabel.TextColor3 = Color3.fromRGB(255, 255, 100)
LevelLabel.TextSize = 14
LevelLabel.Font = Enum.Font.GothamBold
LevelLabel.TextXAlignment = Enum.TextXAlignment.Left
LevelLabel.Parent = MainFrame

-- XP Label
local XPLabel = Instance.new("TextLabel")
XPLabel.Size = UDim2.new(0.9, 0, 0, 25)
XPLabel.Position = UDim2.new(0.05, 0, 0.4, 0)
XPLabel.BackgroundTransparency = 1
XPLabel.Text = "✨ XP: 0/100"
XPLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
XPLabel.TextSize = 14
XPLabel.Font = Enum.Font.Gotham
XPLabel.TextXAlignment = Enum.TextXAlignment.Left
XPLabel.Parent = MainFrame

-- Barra de XP (fundo)
local XPBarBackground = Instance.new("Frame")
XPBarBackground.Size = UDim2.new(0.9, 0, 0, 15)
XPBarBackground.Position = UDim2.new(0.05, 0, 0.55, 0)
XPBarBackground.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
XPBarBackground.BorderSizePixel = 0
XPBarBackground.Parent = MainFrame

local XPBarCorner = Instance.new("UICorner")
XPBarCorner.CornerRadius = UDim.new(0, 5)
XPBarCorner.Parent = XPBarBackground

-- Barra de XP (preenchimento)
local XPBarFill = Instance.new("Frame")
XPBarFill.Size = UDim2.new(0, 0, 1, 0)
XPBarFill.BackgroundColor3 = Color3.fromRGB(255, 180, 0)
XPBarFill.BorderSizePixel = 0
XPBarFill.Parent = XPBarBackground

local XPBarFillCorner = Instance.new("UICorner")
XPBarFillCorner.CornerRadius = UDim.new(0, 5)
XPBarFillCorner.Parent = XPBarFill

-- Botão Auto Farm
local AutoFarmButton = Instance.new("TextButton")
AutoFarmButton.Size = UDim2.new(0.9, 0, 0, 35)
AutoFarmButton.Position = UDim2.new(0.05, 0, 0.72, 0)
AutoFarmButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
AutoFarmButton.BorderSizePixel = 0
AutoFarmButton.Text = "⚔️ ATIVAR AUTO FARM"
AutoFarmButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoFarmButton.TextSize = 12
AutoFarmButton.Font = Enum.Font.GothamBold
AutoFarmButton.Parent = MainFrame

local AutoFarmCorner = Instance.new("UICorner")
AutoFarmCorner.CornerRadius = UDim.new(0, 5)
AutoFarmCorner.Parent = AutoFarmButton

-- Variáveis
local isAutoFarmActive = false
local isMinimized = false
local originalSize = MainFrame.Size
local currentXP = 0
local currentLevel = 1
local xpToNextLevel = 100
local npcsKilled = 0
local autoFarmConnections = {}

-- Função para atualizar barra de XP
local function updateXPBar()
    XPLabel.Text = "✨ XP: " .. currentXP .. "/" .. xpToNextLevel
    LevelLabel.Text = "⭐ Nível: " .. currentLevel
    
    local percent = (currentXP / xpToNextLevel) * 100
    XPBarFill.Size = UDim2.new(percent / 100, 0, 1, 0)
end

-- Função para ganhar XP
local function gainXP(amount)
    currentXP = currentXP + amount
    
    -- Verificar level up
    while currentXP >= xpToNextLevel do
        currentXP = currentXP - xpToNextLevel
        currentLevel = currentLevel + 1
        xpToNextLevel = math.floor(xpToNextLevel * 1.5) -- Aumenta XP necessário
        
        print("🎉 LEVEL UP! Agora você é nível " .. currentLevel)
    end
    
    updateXPBar()
end

-- Função para encontrar NPCs
local function findNPCs()
    local npcs = {}
    
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") then
            local humanoid = obj:FindFirstChild("Humanoid")
            local isPlayer = false
            
            -- Verificar se é player
            for _, player in pairs(Players:GetPlayers()) do
                if player.Character == obj then
                    isPlayer = true
                    break
                end
            end
            
            -- Verificar se é NPC com vida
            if not isPlayer and humanoid.Health > 0 then
                table.insert(npcs, obj)
            end
        end
    end
    
    return npcs
end

-- Função para matar NPC
local function killNPC(npc)
    local humanoid = npc:FindFirstChild("Humanoid")
    if humanoid then
        humanoid.Health = 0
        npcsKilled = npcsKilled + 1
        
        -- Ganhar XP aleatório
        local xpGained = math.random(10, 30)
        gainXP(xpGained)
        
        print("⚔️ NPC morto! +" .. xpGained .. " XP")
    end
end

-- Função para atacar NPC
local function attackNPC(npc)
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    local npcRoot = npc:FindFirstChild("HumanoidRootPart") or npc:FindFirstChild("Torso")
    
    if not npcRoot then return end
    
    -- Caminhar até o NPC
    local humanoid = character:FindFirstChild("Humanoid")
    if humanoid then
        humanoid:MoveTo(npcRoot.Position)
    end
    
    -- Verificar distância
    local distance = (rootPart.Position - npcRoot.Position).Magnitude
    
    if distance < 5 then
        -- Matar NPC
        killNPC(npc)
    end
end

-- Função para iniciar Auto Farm
local function startAutoFarm()
    if isAutoFarmActive then return end
    
    isAutoFarmActive = true
    AutoFarmButton.Text = "⚔️ DESATIVAR AUTO FARM"
    AutoFarmButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    
    print("⚔️ Auto Farm ATIVADO!")
    print("🔄 Procurando NPCs para matar...")
    
    -- Loop principal
    autoFarmConnections[#autoFarmConnections + 1] = RunService.Heartbeat:Connect(function()
        if isAutoFarmActive then
            local npcs = findNPCs()
            
            if #npcs > 0 then
                -- Encontrar NPC mais próximo
                local character = Player.Character
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local rootPart = character.HumanoidRootPart
                    local nearestNPC = nil
                    local nearestDistance = math.huge
                    
                    for _, npc in pairs(npcs) do
                        local npcRoot = npc:FindFirstChild("HumanoidRootPart") or npc:FindFirstChild("Torso")
                        if npcRoot then
                            local distance = (rootPart.Position - npcRoot.Position).Magnitude
                            if distance < nearestDistance then
                                nearestDistance = distance
                                nearestNPC = npc
                            end
                        end
                    end
                    
                    if nearestNPC then
                        attackNPC(nearestNPC)
                    end
                end
            end
        end
    end)
end

-- Função para parar Auto Farm
local function stopAutoFarm()
    if not isAutoFarmActive then return end
    
    isAutoFarmActive = false
    AutoFarmButton.Text = "⚔️ ATIVAR AUTO FARM"
    AutoFarmButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    
    print("⚔️ Auto Farm DESATIVADO!")
    print("📊 Total de NPCs mortos: " .. npcsKilled)
    
    for _, conn in pairs(autoFarmConnections) do
        conn:Disconnect()
    end
    autoFarmConnections = {}
end

-- Toggle Auto Farm
AutoFarmButton.MouseButton1Click:Connect(function()
    if isAutoFarmActive then
        stopAutoFarm()
    else
        startAutoFarm()
    end
end)

-- Minimizar
MinimizeButton.MouseButton1Click:Connect(function()
    if isMinimized then
        MainFrame.Size = originalSize
        LevelLabel.Visible = true
        XPLabel.Visible = true
        XPBarBackground.Visible = true
        XPBarFill.Visible = true
        AutoFarmButton.Visible = true
        MinimizeButton.Text = "—"
        isMinimized = false
    else
        MainFrame.Size = UDim2.new(0, 300, 0, 35)
        LevelLabel.Visible = false
        XPLabel.Visible = false
        XPBarBackground.Visible = false
        XPBarFill.Visible = false
        AutoFarmButton.Visible = false
        MinimizeButton.Text = "+"
        isMinimized = true
    end
end)

-- Inicializar barra de XP
updateXPBar()

print("⚡ NoobHub - Sistema de XP carregado!")
print("⚔️ Auto Farm pronto para matar NPCs!")
