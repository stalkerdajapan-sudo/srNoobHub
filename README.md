--[[
    NoobHub - Sistema de Alvo
    Todos NPCs te atacam: "MATE ELE É O PLAYER 😡"
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

-- Botão Sistema de Alvo
local TargetButton = Instance.new("TextButton")
TargetButton.Size = UDim2.new(0.9, 0, 0, 30)
TargetButton.Position = UDim2.new(0.05, 0, 0.5, 0)
TargetButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
TargetButton.BorderSizePixel = 0
TargetButton.Text = "😡 ATIVAR MODO ALVO"
TargetButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TargetButton.TextSize = 12
TargetButton.Font = Enum.Font.GothamBold
TargetButton.Parent = MainFrame

local TargetCorner = Instance.new("UICorner")
TargetCorner.CornerRadius = UDim.new(0, 5)
TargetCorner.Parent = TargetButton

-- Variáveis
local isTargetMode = false
local isMinimized = false
local originalSize = MainFrame.Size
local targetConnections = {}
local npcList = {}

-- Função para coletar todos NPCs
local function collectNPCs()
    local npcs = {}
    
    -- Procurar por modelos com Humanoid que não são players
    for _, obj in pairs(workspace:GetDescendants()) do
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
            
            -- Se não é player e tem vida
            if not isPlayer and humanoid.Health > 0 then
                table.insert(npcs, obj)
            end
        end
    end
    
    return npcs
end

-- Função para forçar NPC atacar
local function forceNPCsToAttack()
    if not isTargetMode then return end
    
    local character = Player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    local rootPart = character.HumanoidRootPart
    npcList = collectNPCs()
    
    for _, npc in pairs(npcList) do
        if npc and npc.Parent and isTargetMode then
            local npcHumanoid = npc:FindFirstChild("Humanoid")
            local npcRoot = npc:FindFirstChild("HumanoidRootPart") or npc:FindFirstChild("Torso")
            
            if npcHumanoid and npcRoot then
                -- Fazer NPC olhar para o player
                npcRoot.CFrame = CFrame.lookAt(npcRoot.Position, Vector3.new(rootPart.Position.X, npcRoot.Position.Y, rootPart.Position.Z))
                
                -- Forçar NPC a se mover até o player
                npcHumanoid:MoveTo(rootPart.Position)
                
                -- Aumentar agressividade
                npcHumanoid.WalkSpeed = 25
                npcHumanoid.JumpPower = 60
                
                -- Tentar fazer NPC atacar
                local attackDistance = (npcRoot.Position - rootPart.Position).Magnitude
                
                if attackDistance < 10 then
                    -- Tenta causar dano
                    if npcHumanoid.Health > 0 then
                        -- Simular ataque
                        npcHumanoid:TakeDamage(0) -- Resetar aggro
                        
                        -- Procurar scripts de ataque no NPC
                        for _, descendant in pairs(npc:GetDescendants()) do
                            if descendant:IsA("Script") or descendant:IsA("LocalScript") then
                                -- Tentar ativar funções de ataque
                                pcall(function()
                                    if descendant.Name:lower():find("attack") or 
                                       descendant.Name:lower():find("damage") or
                                       descendant.Name:lower():find("hit") then
                                        descendant.Disabled = false
                                    end
                                end)
                            end
                        end
                    end
                end
            end
        end
    end
end

-- Função para criar tag visual nos NPCs
local function createTargetTags()
    for _, npc in pairs(npcList) do
        if npc and npc.Parent then
            -- Verificar se já tem tag
            local hasTag = false
            for _, child in pairs(npc:GetChildren()) do
                if child:IsA("BillboardGui") and child.Name == "TargetTag" then
                    hasTag = true
                    break
                end
            end
            
            if not hasTag then
                -- Criar BillboardGui
                local billboard = Instance.new("BillboardGui")
                billboard.Name = "TargetTag"
                billboard.Size = UDim2.new(0, 100, 0, 30)
                billboard.StudsOffset = Vector3.new(0, 3, 0)
                billboard.AlwaysOnTop = true
                
                local textLabel = Instance.new("TextLabel")
                textLabel.Size = UDim2.new(1, 0, 1, 0)
                textLabel.BackgroundTransparency = 1
                textLabel.Text = "😡 MATE ELE!"
                textLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
                textLabel.TextSize = 14
                textLabel.Font = Enum.Font.GothamBold
                textLabel.Parent = billboard
                
                billboard.Parent = npc
            end
        end
    end
end

-- Função para ativar sistema de alvo
local function activateTargetMode()
    if isTargetMode then return end
    
    isTargetMode = true
    TargetButton.Text = "😡 DESATIVAR MODO ALVO"
    TargetButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    
    -- Loop principal
    targetConnections[#targetConnections + 1] = RunService.Heartbeat:Connect(function()
        if isTargetMode then
            forceNPCsToAttack()
        end
    end)
    
    -- Atualizar tags
    targetConnections[#targetConnections + 1] = RunService.Heartbeat:Connect(function()
        if isTargetMode then
            createTargetTags()
        end
    end)
    
    -- Loop para dar dano se estiver perto
    targetConnections[#targetConnections + 1] = RunService.Heartbeat:Connect(function()
        if isTargetMode then
            local character = Player.Character
            if character and character:FindFirstChild("Humanoid") then
                local humanoid = character.Humanoid
                local rootPart = character:FindFirstChild("HumanoidRootPart")
                
                if rootPart then
                    for _, npc in pairs(npcList) do
                        if npc and npc.Parent then
                            local npcRoot = npc:FindFirstChild("HumanoidRootPart") or npc:FindFirstChild("Torso")
                            if npcRoot then
                                local distance = (rootPart.Position - npcRoot.Position).Magnitude
                                
                                -- Se NPC está perto, causa dano
                                if distance < 5 then
                                    humanoid:TakeDamage(math.random(5, 15))
                                    
                                    -- Efeito visual
                                    local damageIndicator = Instance.new("Part")
                                    damageIndicator.Size = Vector3.new(1, 1, 1)
                                    damageIndicator.Position = rootPart.Position + Vector3.new(0, 2, 0)
                                    damageIndicator.Anchored = true
                                    damageIndicator.CanCollide = false
                                    damageIndicator.Transparency = 0.5
                                    damageIndicator.Color = Color3.fromRGB(255, 0, 0)
                                    damageIndicator.Parent = workspace
                                    
                                    game:GetService("Debris"):AddItem(damageIndicator, 1)
                                end
                            end
                        end
                    end
                end
            end
        end
    end)
end

-- Função para desativar sistema de alvo
local function deactivateTargetMode()
    if not isTargetMode then return end
    
    isTargetMode = false
    TargetButton.Text = "😡 ATIVAR MODO ALVO"
    TargetButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
    
    -- Desconectar loops
    for _, conn in pairs(targetConnections) do
        conn:Disconnect()
    end
    targetConnections = {}
    
    -- Remover tags
    for _, npc in pairs(npcList) do
        if npc and npc.Parent then
            local tag = npc:FindFirstChild("TargetTag")
            if tag then
                tag:Destroy()
            end
        end
    end
    
    -- Restaurar velocidade dos NPCs
    for _, npc in pairs(npcList) do
        if npc and npc.Parent then
            local humanoid = npc:FindFirstChild("Humanoid")
            if humanoid then
                humanoid.WalkSpeed = 16
                humanoid.JumpPower = 50
            end
        end
    end
end

-- Toggle Sistema de Alvo
TargetButton.MouseButton1Click:Connect(function()
    if isTargetMode then
        deactivateTargetMode()
    else
        activateTargetMode()
    end
end)

-- Minimizar
MinimizeButton.MouseButton1Click:Connect(function()
    if isMinimized then
        MainFrame.Size = originalSize
        TargetButton.Visible = true
        MinimizeButton.Text = "—"
        isMinimized = false
    else
        MainFrame.Size = UDim2.new(0, 250, 0, 35)
        TargetButton.Visible = false
        MinimizeButton.Text = "+"
        isMinimized = true
    end
end)

-- Limpar quando destruir
ScreenGui.Destroying:Connect(function()
    deactivateTargetMode()
end)

print("⚡ NoobHub - Sistema de Alvo carregado!")
print("😡 Todos NPCs vão te atacar!")
