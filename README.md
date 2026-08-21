--[[
    ❤️ SrNoobMega ❤️ - TOOLS HUB
    Sistema de Criação de Tools
]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")
local Backpack = Player:WaitForChild("Backpack")

-- Cores Modernas
local Colors = {
    Background = Color3.fromRGB(15, 15, 25),
    Secondary = Color3.fromRGB(25, 25, 40),
    Accent = Color3.fromRGB(255, 50, 80),
    Accent2 = Color3.fromRGB(80, 140, 255),
    Text = Color3.fromRGB(255, 255, 255),
    TextDark = Color3.fromRGB(140, 140, 160),
    Success = Color3.fromRGB(40, 200, 90),
    Warning = Color3.fromRGB(255, 190, 0),
    Danger = Color3.fromRGB(255, 70, 70),
    Purple = Color3.fromRGB(160, 80, 255),
    Cyan = Color3.fromRGB(0, 200, 200)
}

-- Criar GUI
local SrNoobMega = Instance.new("ScreenGui")
SrNoobMega.Name = "SrNoobMega"
SrNoobMega.Parent = game.CoreGui
SrNoobMega.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
SrNoobMega.ResetOnSpawn = false

-- Container Principal
local MainContainer = Instance.new("Frame")
MainContainer.Name = "MainContainer"
MainContainer.Parent = SrNoobMega
MainContainer.BackgroundColor3 = Colors.Background
MainContainer.BorderSizePixel = 0
MainContainer.Position = UDim2.new(0.5, -130, 0.5, -150)
MainContainer.Size = UDim2.new(0, 260, 0, 280)
MainContainer.ClipsDescendants = true
MainContainer.ZIndex = 2

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainContainer

-- Header
local Header = Instance.new("Frame")
Header.Parent = MainContainer
Header.BackgroundColor3 = Colors.Secondary
Header.BorderSizePixel = 0
Header.Size = UDim2.new(1, 0, 0, 40)

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 12)
HeaderCorner.Parent = Header

local GradientBar = Instance.new("Frame")
GradientBar.Parent = Header
GradientBar.BackgroundColor3 = Colors.Accent
GradientBar.BorderSizePixel = 0
GradientBar.Position = UDim2.new(0, 0, 0, 38)
GradientBar.Size = UDim2.new(1, 0, 0, 2)

local Title = Instance.new("TextLabel")
Title.Parent = Header
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 10, 0, 0)
Title.Size = UDim2.new(0, 160, 0, 40)
Title.Font = Enum.Font.GothamBold
Title.Text = "❤️ Tools Hub"
Title.TextColor3 = Colors.Text
Title.TextSize = 12
Title.TextXAlignment = Enum.TextXAlignment.Left

local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Parent = Header
MinimizeButton.BackgroundColor3 = Colors.Warning
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Position = UDim2.new(1, -45, 0, 10)
MinimizeButton.Size = UDim2.new(0, 20, 0, 20)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "—"
MinimizeButton.TextColor3 = Colors.Text
MinimizeButton.TextSize = 12

local MinimizeCorner = Instance.new("UICorner")
MinimizeCorner.CornerRadius = UDim.new(0, 4)
MinimizeCorner.Parent = MinimizeButton

local CloseButton = Instance.new("TextButton")
CloseButton.Parent = Header
CloseButton.BackgroundColor3 = Colors.Danger
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(1, -20, 0, 10)
CloseButton.Size = UDim2.new(0, 20, 0, 20)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "✕"
CloseButton.TextColor3 = Colors.Text
CloseButton.TextSize = 12

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 4)
CloseCorner.Parent = CloseButton

-- Conteúdo
local ContentFrame = Instance.new("Frame")
ContentFrame.Parent = MainContainer
ContentFrame.BackgroundTransparency = 1
ContentFrame.BorderSizePixel = 0
ContentFrame.Position = UDim2.new(0, 0, 0, 45)
ContentFrame.Size = UDim2.new(1, 0, 1, -45)

-- Função criar botão
local function CreateButton(text, color, callback, position)
    local button = Instance.new("TextButton")
    button.Parent = ContentFrame
    button.BackgroundColor3 = color
    button.BorderSizePixel = 0
    button.Position = position
    button.Size = UDim2.new(0, 240, 0, 35)
    button.Font = Enum.Font.GothamBold
    button.Text = text
    button.TextColor3 = Colors.Text
    button.TextSize = 11
    button.AutoButtonColor = false
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = button
    
    button.MouseEnter:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.15), {BackgroundColor3 = color:Lerp(Color3.new(1,1,1), 0.15)}):Play()
    end)
    
    button.MouseLeave:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.15), {BackgroundColor3 = color}):Play()
    end)
    
    button.MouseButton1Click:Connect(callback)
    return button
end

-- Notificação
local function Notify(text, color)
    local notif = Instance.new("Frame")
    notif.Parent = SrNoobMega
    notif.BackgroundColor3 = color
    notif.BorderSizePixel = 0
    notif.Position = UDim2.new(0.5, -70, 0, 10)
    notif.Size = UDim2.new(0, 140, 0, 28)
    notif.ZIndex = 5
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 5)
    corner.Parent = notif
    
    local label = Instance.new("TextLabel")
    label.Parent = notif
    label.BackgroundTransparency = 1
    label.Size = UDim2.new(1, 0, 1, 0)
    label.Font = Enum.Font.GothamBold
    label.Text = text
    label.TextColor3 = Colors.Text
    label.TextSize = 9
    
    notif.Position = UDim2.new(0.5, -70, 0, -28)
    TweenService:Create(notif, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -70, 0, 10)}):Play()
    wait(1.5)
    TweenService:Create(notif, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -70, 0, -28)}):Play()
    wait(0.3)
    notif:Destroy()
end

-- ===== CRIAÇÃO DE TOOLS =====

-- TP TOOL
CreateButton("🎯 CRIAR TP TOOL", Colors.Cyan, function()
    local tool = Instance.new("Tool")
    tool.Name = "TP Tool"
    tool.RequiresHandle = false
    tool.ToolTip = "Toque para teleportar"
    
    local function teleport()
        local mouse = Player:GetMouse()
        if mouse.Target then
            RootPart.CFrame = CFrame.new(mouse.Hit.Position + Vector3.new(0, 3, 0))
            Notify("Teleportado!", Colors.Cyan)
        end
    end
    
    tool.Activated:Connect(teleport)
    tool.Parent = Backpack
    Notify("TP Tool criada!", Colors.Cyan)
end, UDim2.new(0, 10, 0, 10))

-- FLY TOOL
CreateButton("🕊️ CRIAR FLY TOOL", Colors.Accent, function()
    local tool = Instance.new("Tool")
    tool.Name = "Fly Tool"
    tool.RequiresHandle = false
    tool.ToolTip = "Ativar/Desativar Fly"
    
    local flyEnabled = false
    local flyConnection
    
    local function toggleFly()
        flyEnabled = not flyEnabled
        
        if flyEnabled then
            Notify("Fly ON!", Colors.Success)
            
            flyConnection = game:GetService("RunService").Heartbeat:Connect(function()
                if flyEnabled and Character and RootPart then
                    local direction = Vector3.new()
                    local moveVector = Humanoid.MoveDirection
                    
                    if moveVector.Magnitude > 0 then
                        direction = moveVector
                    end
                    
                    if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                        direction += Vector3.new(0, 1, 0)
                    end
                    if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                        direction -= Vector3.new(0, 1, 0)
                    end
                    
                    if direction.Magnitude > 0 then
                        RootPart.Velocity = direction.Unit * 30
                    else
                        RootPart.Velocity = Vector3.new(0, 0, 0)
                    end
                end
            end)
        else
            Notify("Fly OFF!", Colors.Danger)
            if flyConnection then
                flyConnection:Disconnect()
            end
            RootPart.Velocity = Vector3.new(0, 0, 0)
        end
    end
    
    tool.Activated:Connect(toggleFly)
    tool.Parent = Backpack
    Notify("Fly Tool criada!", Colors.Accent)
end, UDim2.new(0, 10, 0, 55))

-- ESPADA CLÁSSICA
CreateButton("⚔️ CRIAR ESPADA", Colors.Warning, function()
    local tool = Instance.new("Tool")
    tool.Name = "Classic Sword"
    tool.ToolTip = "Espada Clássica do Roblox"
    
    -- Criar Handle
    local handle = Instance.new("Part")
    handle.Name = "Handle"
    handle.Size = Vector3.new(0.5, 0.5, 2)
    handle.Shape = Enum.PartType.Cylinder
    handle.BrickColor = BrickColor.new("Dark stone grey")
    handle.Material = Enum.Material.Metal
    handle.Position = RootPart.Position + Vector3.new(0, 1, 0)
    handle.Parent = tool
    
    -- Criar Lâmina
    local blade = Instance.new("Part")
    blade.Name = "Blade"
    blade.Size = Vector3.new(0.3, 0.1, 3)
    blade.BrickColor = BrickColor.new("Medium stone grey")
    blade.Material = Enum.Material.Metal
    blade.Position = handle.Position + Vector3.new(0, 0, 1.5)
    blade.Parent = tool
    
    -- Soldar lâmina ao handle
    local weld = Instance.new("Weld")
    weld.Part0 = handle
    weld.Part1 = blade
    weld.C0 = CFrame.new(0, 0, 1.5)
    weld.Parent = handle
    
    -- Dano da espada
    local damage = 10
    
    tool.Activated:Connect(function()
        local anim = Instance.new("Animation")
        anim.AnimationId = "rbxassetid://000000000"
        
        -- Sistema de dano
        local function dealDamage(target)
            if target and target.Parent then
                local targetHumanoid = target.Parent:FindFirstChild("Humanoid")
                if targetHumanoid then
                    targetHumanoid:TakeDamage(damage)
                    Notify("Dealt " .. damage .. " damage!", Colors.Warning)
                end
            end
        end
        
        -- Verificar alvo
        local mouse = Player:GetMouse()
        if mouse.Target then
            dealDamage(mouse.Target)
        end
    end)
    
    tool.Parent = Backpack
    Notify("Espada criada!", Colors.Warning)
end, UDim2.new(0, 10, 0, 100))

-- Drag System
local dragging = false
local dragInput, dragStart, startPos

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
        local delta = input.Position - dragStart
        MainContainer.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Minimizar
local isMinimized = false
MinimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        TweenService:Create(MainContainer, TweenInfo.new(0.3), {Size = UDim2.new(0, 260, 0, 40)}):Play()
        MinimizeButton.Text = "+"
    else
        TweenService:Create(MainContainer, TweenInfo.new(0.3), {Size = UDim2.new(0, 260, 0, 280)}):Play()
        MinimizeButton.Text = "—"
    end
end)

-- Fechar
CloseButton.MouseButton1Click:Connect(function()
    SrNoobMega:Destroy()
end)

-- Atualizar personagem
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    RootPart = Character:WaitForChild("HumanoidRootPart")
end)

Notify("❤️ Tools Hub Carregado!", Colors.Success)
