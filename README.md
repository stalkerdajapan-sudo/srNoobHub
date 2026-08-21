--[[
    NoobHub - Tools Malucos
    20+ Tools com GUI arrastável
]]

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NoobHub"
ScreenGui.Parent = Player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- Criar Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 300, 0, 400)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
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
Title.Text = "⚡ NoobHub Tools"
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

-- Container com scroll
local ContentContainer = Instance.new("ScrollingFrame")
ContentContainer.Size = UDim2.new(1, -10, 1, -40)
ContentContainer.Position = UDim2.new(0, 5, 0, 40)
ContentContainer.BackgroundTransparency = 1
ContentContainer.BorderSizePixel = 0
ContentContainer.ScrollBarThickness = 4
ContentContainer.ScrollBarImageColor3 = Color3.fromRGB(255, 180, 0)
ContentContainer.CanvasSize = UDim2.new(0, 0, 0, 1200)
ContentContainer.Parent = MainFrame

-- Variáveis
local isMinimized = false
local originalSize = MainFrame.Size
local flyBody = nil
local flyGyro = nil
local isFlying = false
local isNoclipping = false
local noclipConnection = nil
local speedLoop = nil

-- Função para criar botão
local function createButton(name, yPos, callback, color)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, -10, 0, 35)
    Button.Position = UDim2.new(0, 5, 0, yPos)
    Button.BackgroundColor3 = color or Color3.fromRGB(40, 40, 40)
    Button.BorderSizePixel = 0
    Button.Text = name
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 12
    Button.Font = Enum.Font.GothamBold
    Button.AutoButtonColor = true
    Button.Parent = ContentContainer
    
    local ButtonCorner = Instance.new("UICorner")
    ButtonCorner.CornerRadius = UDim.new(0, 5)
    ButtonCorner.Parent = Button
    
    Button.MouseButton1Click:Connect(callback)
    return Button
end

-- Função para criar separador
local function createSeparator(yPos)
    local Separator = Instance.new("Frame")
    Separator.Size = UDim2.new(1, -10, 0, 2)
    Separator.Position = UDim2.new(0, 5, 0, yPos)
    Separator.BackgroundColor3 = Color3.fromRGB(255, 180, 0)
    Separator.BorderSizePixel = 0
    Separator.Parent = ContentContainer
end

-- Função para criar label de seção
local function createSection(text, yPos)
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -10, 0, 20)
    Label.Position = UDim2.new(0, 5, 0, yPos)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(255, 180, 0)
    Label.TextSize = 14
    Label.Font = Enum.Font.GothamBlack
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = ContentContainer
end

-- Função para pegar personagem
local function getCharacter()
    return Player.Character
end

-- Função para pegar root
local function getRoot()
    local char = getCharacter()
    return char and char:FindFirstChild("HumanoidRootPart")
end

-- Função para pegar humanoid
local function getHumanoid()
    local char = getCharacter()
    return char and char:FindFirstChild("Humanoid")
end

-- Criar as Tools
createSection("🏃 Movimento", 5)

-- 1. Speed
createButton("⚡ Speed (100)", 30, function()
    local hum = getHumanoid()
    if hum then
        hum.WalkSpeed = 100
        task.wait(10)
        hum.WalkSpeed = 16
    end
end, Color3.fromRGB(50, 100, 200))

-- 2. Super Jump
createButton("🦘 Super Jump", 70, function()
    local hum = getHumanoid()
    if hum then
        hum.JumpPower = 150
        task.wait(10)
        hum.JumpPower = 50
    end
end, Color3.fromRGB(50, 100, 200))

-- 3. Fly
createButton("🚀 Fly", 110, function()
    local root = getRoot()
    local hum = getHumanoid()
    if root and hum then
        if not isFlying then
            isFlying = true
            flyBody = Instance.new("BodyVelocity")
            flyBody.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            flyBody.Velocity = Vector3.new(0, 0, 0)
            flyBody.Parent = root
            
            flyGyro = Instance.new("BodyGyro")
            flyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            flyGyro.CFrame = root.CFrame
            flyGyro.Parent = root
            
            hum.PlatformStand = true
            
            local connection
            connection = RunService.Heartbeat:Connect(function()
                if not isFlying or not root or not flyBody then
                    connection:Disconnect()
                    return
                end
                
                local direction = Vector3.new(0, 0, 0)
                if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                    direction = direction + root.CFrame.LookVector
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                    direction = direction - root.CFrame.LookVector
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.A) then
                    direction = direction - root.CFrame.RightVector
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.D) then
                    direction = direction + root.CFrame.RightVector
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                    direction = direction + Vector3.new(0, 1, 0)
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                    direction = direction - Vector3.new(0, 1, 0)
                end
                
                flyBody.Velocity = direction * 50
            end)
        else
            isFlying = false
            if flyBody then flyBody:Destroy() end
            if flyGyro then flyGyro:Destroy() end
            hum.PlatformStand = false
        end
    end
end, Color3.fromRGB(50, 100, 200))

-- 4. Noclip
createButton("👻 Noclip", 150, function()
    if not isNoclipping then
        isNoclipping = true
        noclipConnection = RunService.Stepped:Connect(function()
            local char = getCharacter()
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    else
        isNoclipping = false
        if noclipConnection then
            noclipConnection:Disconnect()
        end
        local char = getCharacter()
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end, Color3.fromRGB(50, 100, 200))

createSeparator(195)

createSection("💪 Poder", 200)

-- 5. God Mode
createButton("🛡️ God Mode", 225, function()
    local hum = getHumanoid()
    if hum then
        hum.MaxHealth = math.huge
        hum.Health = math.huge
    end
end, Color3.fromRGB(200, 150, 0))

-- 6. Força
createButton("💥 Força Bruta", 265, function()
    local char = getCharacter()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Massless = false
                part.Material = Enum.Material.DiamondPlate
            end
        end
    end
end, Color3.fromRGB(200, 150, 0))

-- 7. Invisível
createButton("👁️ Invisível", 305, function()
    local char = getCharacter()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Transparency = 1
            end
        end
    end
end, Color3.fromRGB(200, 150, 0))

-- 8. Gigante
createButton("🎈 Gigante", 345, function()
    local char = getCharacter()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Size = part.Size * 2
            end
        end
    end
end, Color3.fromRGB(200, 150, 0))

-- 9. Pequeno
createButton("🐜 Pequeno", 385, function()
    local char = getCharacter()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Size = part.Size * 0.5
            end
        end
    end
end, Color3.fromRGB(200, 150, 0))

createSeparator(430)

createSection("🎯 Ataque", 435)

-- 10. Explosão
createButton("💣 Explosão", 460, function()
    local root = getRoot()
    if root then
        local explosion = Instance.new("Explosion")
        explosion.Position = root.Position
        explosion.BlastRadius = 50
        explosion.DestroyJointRadiusPercent = 0
        explosion.Parent = Workspace
    end
end, Color3.fromRGB(200, 50, 50))

-- 11. Raio Laser
createButton("🔫 Raio Laser", 500, function()
    local root = getRoot()
    if root then
        local beam = Instance.new("Beam")
        beam.Name = "LaserBeam"
        
        local startPart = Instance.new("Part")
        startPart.Size = Vector3.new(1, 1, 1)
        startPart.Position = root.Position
        startPart.Anchored = true
        startPart.CanCollide = false
        startPart.Transparency = 1
        startPart.Parent = Workspace
        
        local endPart = Instance.new("Part")
        endPart.Size = Vector3.new(1, 1, 1)
        endPart.Position = root.Position + root.CFrame.LookVector * 50
        endPart.Anchored = true
        endPart.CanCollide = false
        endPart.Transparency = 1
        endPart.Parent = Workspace
        
        beam.Attachment0 = Instance.new("Attachment", startPart)
        beam.Attachment1 = Instance.new("Attachment", endPart)
        beam.Color = ColorSequence.new(Color3.fromRGB(255, 0, 0))
        beam.Width0 = 0.5
        beam.Width1 = 0.5
        beam.Parent = Workspace
        
        game:GetService("Debris"):AddItem(startPart, 3)
        game:GetService("Debris"):AddItem(endPart, 3)
        game:GetService("Debris"):AddItem(beam, 3)
    end
end, Color3.fromRGB(200, 50, 50))

-- 12. Meteoros
createButton("☄️ Chuva de Meteoros", 540, function()
    local root = getRoot()
    if root then
        for i = 1, 10 do
            task.spawn(function()
                local meteor = Instance.new("Part")
                meteor.Size = Vector3.new(5, 5, 5)
                meteor.Shape = Enum.PartType.Ball
                meteor.Material = Enum.Material.Neon
                meteor.Color = Color3.fromRGB(255, 100, 0)
                meteor.Anchored = false
                meteor.CanCollide = true
                meteor.Position = root.Position + Vector3.new(math.random(-50, 50), 100, math.random(-50, 50))
                meteor.Parent = Workspace
                
                game:GetService("Debris"):AddItem(meteor, 10)
            end)
        end
    end
end, Color3.fromRGB(200, 50, 50))

-- 13. Teleporte Aleatório
createButton("🌀 Teleporte Aleatório", 580, function()
    local root = getRoot()
    if root then
        root.CFrame = CFrame.new(
            root.Position + Vector3.new(math.random(-100, 100), 0, math.random(-100, 100))
        )
    end
end, Color3.fromRGB(200, 50, 50))

createSeparator(625)

createSection("🔧 Utilidades", 630)

-- 14. Anti-AFK
createButton("⏰ Anti-AFK", 655, function()
    local VirtualUser = game:GetService("VirtualUser")
    Player.Idled:Connect(function()
        VirtualUser:Button2Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
        task.wait(1)
        VirtualUser:Button2Up(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
    end)
end, Color3.fromRGB(100, 100, 100))

-- 15. Curar
createButton("❤️ Curar", 695, function()
    local hum = getHumanoid()
    if hum then
        hum.Health = hum.MaxHealth
    end
end, Color3.fromRGB(100, 100, 100))

-- 16. Resetar
createButton("🔄 Resetar", 735, function()
    local char = getCharacter()
    if char then
        char:BreakJoints()
    end
end, Color3.fromRGB(100, 100, 100))

-- 17. Ficar Colorido
createButton("🌈 Colorido", 775, function()
    local char = getCharacter()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Color = Color3.fromHSV(math.random(), 1, 1)
            end
        end
    end
end, Color3.fromRGB(100, 100, 100))

-- 18. Neon
createButton("💡 Modo Neon", 815, function()
    local char = getCharacter()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Material = Enum.Material.Neon
                part.Color = Color3.fromRGB(255, 0, 255)
            end
        end
    end
end, Color3.fromRGB(100, 100, 100))

-- 19. Gravidade Zero
createButton("🌌 Gravidade Zero", 855, function()
    Workspace.Gravity = 0
    task.wait(10)
    Workspace.Gravity = 196.2
end, Color3.fromRGB(100, 100, 100))

-- 20. Fogo
createButton("🔥 Pegar Fogo", 895, function()
    local char = getCharacter()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                local fire = Instance.new("Fire")
                fire.Color = Color3.fromRGB(255, 100, 0)
                fire.Heat = 25
                fire.Size = 10
                fire.Parent = part
            end
        end
    end
end, Color3.fromRGB(100, 100, 100))

-- 21. Fumaça
createButton("💨 Fumaça", 935, function()
    local root = getRoot()
    if root then
        local smoke = Instance.new("Smoke")
        smoke.Color = Color3.fromRGB(100, 100, 100)
        smoke.Opacity = 0.5
        smoke.RiseVelocity = 10
        smoke.Size = 20
        smoke.Parent = root
    end
end, Color3.fromRGB(100, 100, 100))

-- 22. Sparkles
createButton("✨ Brilho", 975, function()
    local char = getCharacter()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                local sparkles = Instance.new("Sparkles")
                sparkles.Color = Color3.fromRGB(255, 255, 0)
                sparkles.Parent = part
            end
        end
    end
end, Color3.fromRGB(100, 100, 100))

-- Minimizar
MinimizeButton.MouseButton1Click:Connect(function()
    if isMinimized then
        MainFrame.Size = originalSize
        ContentContainer.Visible = true
        MinimizeButton.Text = "—"
        isMinimized = false
    else
        MainFrame.Size = UDim2.new(0, 300, 0, 35)
        ContentContainer.Visible = false
        MinimizeButton.Text = "+"
        isMinimized = true
    end
end)

print("⚡ NoobHub - 22 Tools Malucas carregadas!")
