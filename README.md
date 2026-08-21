--[[
    SCRIPT: Ultimate Educational Cheat Suite
    AUTOR: SrNoob (Fins Educacionais)
    VERSÃO: 10.0 - COMPLETO
    NOTA: Este script é apenas para fins educacionais e demonstração de técnicas
]]

--[[
    ATENÇÃO: Este script contém técnicas avançadas de exploração
    Use APENAS para aprendizado em servidores privados
    Não use em servidores públicos
]]

-- ============================================
-- CONFIGURAÇÕES PRINCIPAIS
-- ============================================
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local runService = game:GetService("RunService")
local userInputService = game:GetService("UserInputService")
local camera = workspace.CurrentCamera
local heartbeat = game:GetService("RunService").Heartbeat

-- ============================================
-- MÓDULO 1: SISTEMA AVANÇADO DE AIMBOT + AIMLOCK
-- ============================================
local Aimbot = {
    active = true,
    smoothness = 0.2,
    fovRadius = 500,
    hitboxSize = 2.5,
    aimPart = "Head",
    targetPlayers = true,
    targetNPCs = true,
    targetTeam = false,
    visibleCheck = true,
    prediction = false,
    predictionAmount = 0.3,
    boneSelection = "Head", -- Head, UpperTorso, HumanoidRootPart
    silentAim = false,
    rcs = false, -- Recoil Control System
}

-- Detectar alvos avançado
function Aimbot:GetTargets()
    local targets = {}
    local players = game.Players:GetPlayers()
    
    -- Players
    if self.targetPlayers then
        for _, plr in pairs(players) do
            if plr ~= player then
                if self.targetTeam and plr.Team == player.Team then continue end
                local char = plr.Character
                if char and char:FindFirstChild("Humanoid") then
                    local humanoid = char:FindFirstChild("Humanoid")
                    if humanoid and humanoid.Health > 0 then
                        local head = char:FindFirstChild("Head")
                        if head then
                            table.insert(targets, {
                                type = "Player",
                                name = plr.Name,
                                character = char,
                                head = head,
                                isPlayer = true,
                                humanoid = humanoid
                            })
                        end
                    end
                end
            end
        end
    end
    
    -- NPCs
    if self.targetNPCs then
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("Model") and obj:FindFirstChild("Humanoid") then
                local humanoid = obj:FindFirstChild("Humanoid")
                if humanoid and humanoid.Health > 0 then
                    local head = obj:FindFirstChild("Head")
                    if head then
                        local isPlayer = false
                        for _, p in pairs(players) do
                            if p.Character == obj then
                                isPlayer = true
                                break
                            end
                        end
                        if not isPlayer then
                            table.insert(targets, {
                                type = "NPC",
                                name = obj.Name,
                                character = obj,
                                head = head,
                                isPlayer = false,
                                humanoid = humanoid
                            })
                        end
                    end
                end
            end
        end
    end
    
    return targets
end

-- Predição de movimento
function Aimbot:GetPredictedPosition(target)
    if not self.prediction then return target.head.Position end
    
    local velocity = target.character:FindFirstChild("HumanoidRootPart").Velocity
    local predictedPos = target.head.Position + (velocity * self.predictionAmount)
    return predictedPos
end

-- Verificação de visibilidade
function Aimbot:IsVisible(position)
    if not self.visibleCheck then return true end
    
    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {player.Character}
    
    local ray = workspace:Raycast(camera.CFrame.Position, position - camera.CFrame.Position, raycastParams)
    return ray == nil or ray.Instance:IsDescendantOf(workspace.Terrain) or ray.Instance:IsDescendantOf(player.Character)
end

-- Função principal de aimbot
function Aimbot:GetClosestTarget()
    local closestTarget = nil
    local closestDistance = math.huge
    local targets = self:GetTargets()
    
    for _, target in pairs(targets) do
        if target.head then
            local targetPos = self:GetPredictedPosition(target)
            local screenPos, onScreen = camera:WorldToScreenPoint(targetPos)
            
            if onScreen then
                local mousePos = Vector2.new(mouse.X, mouse.Y)
                local targetScreenPos = Vector2.new(screenPos.X, screenPos.Y)
                local distance = (mousePos - targetScreenPos).Magnitude
                
                -- Verificar visibilidade
                if not self:IsVisible(targetPos) then continue end
                
                local adjustedDistance = distance / self.hitboxSize
                
                if adjustedDistance < closestDistance and distance < self.fovRadius then
                    closestDistance = adjustedDistance
                    closestTarget = target
                end
            end
        end
    end
    
    return closestTarget, closestDistance
end

-- ============================================
-- MÓDULO 2: SISTEMA DE WALLHACK
-- ============================================
local Wallhack = {
    active = false,
    espBox = true,
    espName = true,
    espHealth = true,
    espDistance = true,
    espLine = false,
    espChams = false,
    espSkeleton = false,
    espTracer = false,
    espColors = {
        player = Color3.fromRGB(0, 255, 0),
        enemy = Color3.fromRGB(255, 0, 0),
        npc = Color3.fromRGB(255, 255, 0),
        team = Color3.fromRGB(0, 0, 255),
    }
}

-- Criar ESP
function Wallhack:CreateESP()
    if self.active then
        -- ESP Box
        if self.espBox then
            -- Criar caixas ao redor dos jogadores
            for _, plr in pairs(game.Players:GetPlayers()) do
                if plr ~= player and plr.Character then
                    local char = plr.Character
                    local hrp = char:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        local pos = hrp.Position
                        -- Desenhar box (simplificado)
                        -- Em um script real, usaria Drawing ou SurfaceGui
                    end
                end
            end
        end
    end
end

-- ============================================
-- MÓDULO 3: SISTEMA DE SPEED HACK
-- ============================================
local SpeedHack = {
    active = false,
    speedMultiplier = 2.0,
    jumpMultiplier = 1.5,
    walkSpeed = 16,
    jumpPower = 50,
}

function SpeedHack:Apply()
    if not self.active then return end
    
    local char = player.Character
    if char then
        local humanoid = char:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = self.walkSpeed * self.speedMultiplier
            humanoid.JumpPower = self.jumpPower * self.jumpMultiplier
        end
    end
end

-- ============================================
-- MÓDULO 4: SISTEMA DE FLY
-- ============================================
local Fly = {
    active = false,
    speed = 50,
    flyMode = "Fly", -- Fly, Noclip, Both
}

function Fly:Toggle()
    self.active = not self.active
    if self.active then
        self:Start()
    else
        self:Stop()
    end
end

function Fly:Start()
    local char = player.Character
    if not char then return end
    
    local humanoid = char:FindFirstChild("Humanoid")
    if humanoid then
        humanoid.PlatformStand = true
    end
    
    -- Criar BodyVelocity para voo
    local bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(10000, 10000, 10000)
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.Parent = char:FindFirstChild("HumanoidRootPart")
    
    -- Loop de voo
    self.flyLoop = game:GetService("RunService").Heartbeat:Connect(function()
        if not self.active then return end
        
        local rootPart = char:FindFirstChild("HumanoidRootPart")
        if not rootPart then return end
        
        local moveDirection = Vector3.new()
        local cameraCFrame = workspace.CurrentCamera.CFrame
        
        -- WASD Controls
        if userInputService:IsKeyDown(Enum.KeyCode.W) then
            moveDirection = moveDirection + cameraCFrame.LookVector
        end
        if userInputService:IsKeyDown(Enum.KeyCode.S) then
            moveDirection = moveDirection - cameraCFrame.LookVector
        end
        if userInputService:IsKeyDown(Enum.KeyCode.A) then
            moveDirection = moveDirection - cameraCFrame.RightVector
        end
        if userInputService:IsKeyDown(Enum.KeyCode.D) then
            moveDirection = moveDirection + cameraCFrame.RightVector
        end
        if userInputService:IsKeyDown(Enum.KeyCode.Space) then
            moveDirection = moveDirection + Vector3.new(0, 1, 0)
        end
        if userInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
            moveDirection = moveDirection - Vector3.new(0, 1, 0)
        end
        
        if moveDirection.Magnitude > 0 then
            moveDirection = moveDirection.Unit * self.speed
        end
        
        bodyVelocity.Velocity = moveDirection
    end)
end

function Fly:Stop()
    local char = player.Character
    if char then
        local humanoid = char:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.PlatformStand = false
        end
        
        local rootPart = char:FindFirstChild("HumanoidRootPart")
        if rootPart then
            local bv = rootPart:FindFirstChild("BodyVelocity")
            if bv then bv:Destroy() end
        end
    end
    
    if self.flyLoop then
        self.flyLoop:Disconnect()
        self.flyLoop = nil
    end
end

-- ============================================
-- MÓDULO 5: SISTEMA DE TELEPORT
-- ============================================
local Teleport = {
    active = false,
    teleportKey = Enum.KeyCode.T,
    targetPosition = nil,
    memory = {},
}

function Teleport:TeleportToPosition(position)
    local char = player.Character
    if not char then return end
    
    local rootPart = char:FindFirstChild("HumanoidRootPart")
    if rootPart then
        -- Salvar posição atual no histórico
        table.insert(self.memory, rootPart.Position)
        if #self.memory > 10 then
            table.remove(self.memory, 1)
        end
        
        rootPart.CFrame = CFrame.new(position)
    end
end

function Teleport:TeleportBack()
    if #self.memory > 0 then
        local lastPos = self.memory[#self.memory]
        self:TeleportToPosition(lastPos)
        table.remove(self.memory, #self.memory)
    end
end

-- ============================================
-- MÓDULO 6: SISTEMA DE INFINITE YIELD
-- ============================================
local InfiniteYield = {
    active = false,
    yieldMethod = "Loop", -- Loop, Spawn, Task
}

function InfiniteYield:Start()
    self.active = true
    self:YieldLoop()
end

function InfiniteYield:YieldLoop()
    while self.active do
        -- Código que impede yield
        local success, err = pcall(function()
            -- Itera sobre todas as instâncias
            for _, instance in pairs(workspace:GetDescendants()) do
                if instance:IsA("BasePart") then
                    -- Toca em cada parte para prevenir yield
                    local position = instance.Position
                end
            end
        end)
        
        task.wait(0.1)
    end
end

-- ============================================
-- MÓDULO 7: SISTEMA DE ANTI-AFK
-- ============================================
local AntiAFK = {
    active = false,
    methods = {
        "Move", -- Movimentação
        "Chat", -- Enviar mensagem
        "Click", -- Clique do mouse
        "Jump", -- Pular
    },
    currentMethod = 1,
}

function AntiAFK:Start()
    self.active = true
    self:AntiAFKLoop()
end

function AntiAFK:AntiAFKLoop()
    spawn(function()
        while self.active do
            task.wait(30) -- A cada 30 segundos
            
            local method = self.methods[self.currentMethod]
            
            if method == "Move" then
                -- Simular movimento
                local char = player.Character
                if char then
                    local hrp = char:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        local oldPos = hrp.Position
                        hrp.CFrame = hrp.CFrame + Vector3.new(0, 0, 1)
                        task.wait(0.1)
                        hrp.CFrame = CFrame.new(oldPos)
                    end
                end
            elseif method == "Chat" then
                -- Simular chat
                game:GetService("ReplicatedStorage"):FindFirstChild("DefaultChatSystemChatEvents"):FindFirstChild("SayMessageRequest"):FireServer("I am not AFK", "All")
            elseif method == "Click" then
                -- Simular clique
                mouse.Button1Click:Fire()
            elseif method == "Jump" then
                -- Simular pulo
                local char = player.Character
                if char then
                    local humanoid = char:FindFirstChild("Humanoid")
                    if humanoid then
                        humanoid.Jump = true
                    end
                end
            end
            
            self.currentMethod = self.currentMethod % #self.methods + 1
        end
    end)
end

-- ============================================
-- MÓDULO 8: SISTEMA DE ESP (CORRIGIDO)
-- ============================================
local ESP = {
    active = false,
    boxes = {},
    names = {},
    healths = {},
    distances = {},
    lines = {},
}

function ESP:CreateESP()
    if not self.active then return end
    
    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr ~= player then
            local char = plr.Character
            if char then
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    -- Criar box ESP simplificado
                    local pos, onScreen = camera:WorldToScreenPoint(hrp.Position)
                    if onScreen then
                        -- Aqui seria o código para desenhar ESP
                        -- Usando Drawing ou SurfaceGui
                    end
                end
            end
        end
    end
end

-- ============================================
-- MÓDULO 9: SISTEMA DE MACRO
-- ============================================
local Macro = {
    active = false,
    macros = {
        { key = Enum.KeyCode.Q, action = "AutoClick", interval = 0.1 },
        { key = Enum.KeyCode.E, action = "AutoSword", interval = 0.5 },
        { key = Enum.KeyCode.R, action = "AutoHeal", interval = 1 },
    },
}

function Macro:ExecuteMacro(macro)
    if macro.action == "AutoClick" then
        mouse.Button1Click:Fire()
    elseif macro.action == "AutoSword" then
        -- Simular ataque de espada
        local char = player.Character
        if char then
            local tool = char:FindFirstChildWhichIsA("Tool")
            if tool then
                tool:Activate()
            end
        end
    elseif macro.action == "AutoHeal" then
        -- Simular cura
        local char = player.Character
        if char then
            local humanoid = char:FindFirstChild("Humanoid")
            if humanoid then
                humanoid.Health = humanoid.MaxHealth
            end
        end
    end
end

-- ============================================
-- MÓDULO 10: SISTEMA DE GAMBLING (Sorte)
-- ============================================
local Gambling = {
    active = false,
    balance = 0,
    winChance = 0.5,
    multiplier = 2,
}

function Gambling:Bet(amount)
    if amount > self.balance then return false end
    
    local random = math.random()
    local win = random < self.winChance
    
    if win then
        self.balance = self.balance + (amount * self.multiplier)
        return true, amount * self.multiplier
    else
        self.balance = self.balance - amount
        return false, 0
    end
end

-- ============================================
-- GUI PRINCIPAL (Corrigida)
-- ============================================
local gui = Instance.new("ScreenGui")
gui.Name = "UltimateCheat"
gui.ResetOnSpawn = false
gui.Parent = player.PlayerGui

-- Frame Principal (MODERNO)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 350, 0, 500)
mainFrame.Position = UDim2.new(0.5, -175, 0.5, -250)
mainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 20)
mainFrame.BackgroundTransparency = 0.05
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = gui

-- Arredondamento
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 15)
corner.Parent = mainFrame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 50)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(255, 215, 0)
title.BackgroundTransparency = 0.2
title.Text = "✦ ULTIMATE CHEAT SUITE ✦"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = mainFrame

-- ScrollFrame para múltiplos módulos
local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Size = UDim2.new(1, 0, 1, -50)
scrollFrame.Position = UDim2.new(0, 0, 0, 50)
scrollFrame.BackgroundTransparency = 1
scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 800)
scrollFrame.ScrollBarThickness = 8
scrollFrame.Parent = mainFrame

-- Função para criar botões modulares
local function createButton(text, callback, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 40)
    btn.Position = UDim2.new(0.05, 0, 0, 0)
    btn.BackgroundColor3 = color or Color3.fromRGB(50, 50, 80)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextScaled = true
    btn.Font = Enum.Font.GothamBold
    btn.Parent = scrollFrame
    
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 8)
    btnCorner.Parent = btn
    
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- Layout automático
local yPos = 10
local function addButton(text, callback, color)
    local btn = createButton(text, callback, color)
    btn.Position = UDim2.new(0.05, 0, 0, yPos)
    yPos = yPos + 50
    return btn
end

-- ============================================
-- GUI CONTROLES (CORRIGIDOS)
-- ============================================

-- Aimbot Control
addButton("🎯 Toggle Aimbot", function()
    Aimbot.active = not Aimbot.active
end, Color3.fromRGB(0, 200, 0))

-- Fly Control
addButton("✈️ Toggle Fly", function()
    Fly:Toggle()
end, Color3.fromRGB(0, 150, 255))

-- Speed Hack
addButton("⚡ Toggle Speed Hack", function()
    SpeedHack.active = not SpeedHack.active
end, Color3.fromRGB(255, 200, 0))

-- Wallhack
addButton("👁️ Toggle Wallhack", function()
    Wallhack.active = not Wallhack.active
end, Color3.fromRGB(255, 0, 255))

-- ESP
addButton("📊 Toggle ESP", function()
    ESP.active = not ESP.active
end, Color3.fromRGB(0, 255, 255))

-- Anti-AFK
addButton("💤 Toggle Anti-AFK", function()
    AntiAFK.active = not AntiAFK.active
 
