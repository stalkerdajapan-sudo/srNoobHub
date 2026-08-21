--[[
    SrNoobHub - Universal Script
    Criado para fins educacionais
]]

local SrNoobHub = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local UICorner = Instance.new("UICorner")
local Title = Instance.new("TextLabel")
local SpeedLabel = Instance.new("TextLabel")
local SpeedSlider = Instance.new("TextBox")
local JumpLabel = Instance.new("TextLabel")
local JumpSlider = Instance.new("TextBox")
local GravityLabel = Instance.new("TextLabel")
local GravitySlider = Instance.new("TextBox")
local MassLabel = Instance.new("TextLabel")
local MassSlider = Instance.new("TextBox")
local ApplyButton = Instance.new("TextButton")
local ResetButton = Instance.new("TextButton")
local CloseButton = Instance.new("TextButton")

local Player = game.Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Valores padrão
local DefaultSpeed = 16
local DefaultJump = 7.2
local DefaultGravity = 196.2
local DefaultMass = 100

-- Criando Interface
SrNoobHub.Name = "SrNoobHub"
SrNoobHub.Parent = game.CoreGui
SrNoobHub.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

MainFrame.Name = "MainFrame"
MainFrame.Parent = SrNoobHub
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.35, 0, 0.25, 0)
MainFrame.Size = UDim2.new(0, 300, 0, 250)
MainFrame.Active = true
MainFrame.Draggable = true

UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

Title.Name = "Title"
Title.Parent = MainFrame
Title.BackgroundColor3 = Color3.fromRGB(255, 100, 0)
Title.BorderSizePixel = 0
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Font = Enum.Font.SourceSansBold
Title.Text = "🔥 SrNoobHub Universal"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16

local function CreateLabel(parent, position, text)
    local label = Instance.new("TextLabel")
    label.Parent = parent
    label.BackgroundTransparency = 1
    label.Position = position
    label.Size = UDim2.new(0, 100, 0, 20)
    label.Font = Enum.Font.SourceSans
    label.Text = text
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextSize = 13
    label.TextXAlignment = Enum.TextXAlignment.Left
    return label
end

local function CreateTextBox(parent, position, value)
    local textbox = Instance.new("TextBox")
    textbox.Parent = parent
    textbox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    textbox.BorderSizePixel = 0
    textbox.Position = position
    textbox.Size = UDim2.new(0, 80, 0, 20)
    textbox.Font = Enum.Font.SourceSans
    textbox.Text = tostring(value)
    textbox.TextColor3 = Color3.fromRGB(255, 255, 255)
    textbox.TextSize = 13
    return textbox
end

SpeedLabel = CreateLabel(MainFrame, UDim2.new(0, 10, 0, 40), "Velocidade:")
SpeedSlider = CreateTextBox(MainFrame, UDim2.new(0, 200, 0, 40), DefaultSpeed)

JumpLabel = CreateLabel(MainFrame, UDim2.new(0, 10, 0, 70), "Pulo:")
JumpSlider = CreateTextBox(MainFrame, UDim2.new(0, 200, 0, 70), DefaultJump)

GravityLabel = CreateLabel(MainFrame, UDim2.new(0, 10, 0, 100), "Gravidade:")
GravitySlider = CreateTextBox(MainFrame, UDim2.new(0, 200, 0, 100), DefaultGravity)

MassLabel = CreateLabel(MainFrame, UDim2.new(0, 10, 0, 130), "Massa:")
MassSlider = CreateTextBox(MainFrame, UDim2.new(0, 200, 0, 130), DefaultMass)

ApplyButton.Name = "ApplyButton"
ApplyButton.Parent = MainFrame
ApplyButton.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
ApplyButton.BorderSizePixel = 0
ApplyButton.Position = UDim2.new(0, 10, 0, 170)
ApplyButton.Size = UDim2.new(0, 130, 0, 30)
ApplyButton.Font = Enum.Font.SourceSansBold
ApplyButton.Text = "✅ Aplicar"
ApplyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ApplyButton.TextSize = 14

ResetButton.Name = "ResetButton"
ResetButton.Parent = MainFrame
ResetButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
ResetButton.BorderSizePixel = 0
ResetButton.Position = UDim2.new(0, 160, 0, 170)
ResetButton.Size = UDim2.new(0, 130, 0, 30)
ResetButton.Font = Enum.Font.SourceSansBold
ResetButton.Text = "🔄 Resetar"
ResetButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ResetButton.TextSize = 14

CloseButton.Name = "CloseButton"
CloseButton.Parent = MainFrame
CloseButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(0, 270, 0, 0)
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.Text = "✕"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 14

-- Funções
local function ApplyChanges()
    local speed = tonumber(SpeedSlider.Text) or DefaultSpeed
    local jump = tonumber(JumpSlider.Text) or DefaultJump
    local gravity = tonumber(GravitySlider.Text) or DefaultGravity
    local mass = tonumber(MassSlider.Text) or DefaultMass
    
    if Humanoid then
        Humanoid.WalkSpeed = speed
        Humanoid.JumpPower = jump
        
        -- Gravidade customizada
        local gravityScript = Character:FindFirstChild("GravityController")
        if gravityScript then
            gravityScript:Destroy()
        end
        
        local gravityController = Instance.new("BodyForce")
        gravityController.Name = "GravityController"
        gravityController.Force = Vector3.new(0, -gravity * RootPart.AssemblyMass, 0)
        gravityController.Parent = RootPart
        
        -- Massa
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Massless = false
            end
        end
        RootPart.CustomPhysicalProperties = PhysicalProperties.new(mass, 0.3, 0.5)
    end
end

local function ResetChanges()
    if Humanoid then
        Humanoid.WalkSpeed = DefaultSpeed
        Humanoid.JumpPower = DefaultJump
        
        local gravityController = Character:FindFirstChild("GravityController") or RootPart:FindFirstChild("GravityController")
        if gravityController then
            gravityController:Destroy()
        end
        
        RootPart.CustomPhysicalProperties = PhysicalProperties.new(DefaultMass, 0.3, 0.5)
        
        SpeedSlider.Text = tostring(DefaultSpeed)
        JumpSlider.Text = tostring(DefaultJump)
        GravitySlider.Text = tostring(DefaultGravity)
        MassSlider.Text = tostring(DefaultMass)
    end
end

-- Eventos
ApplyButton.MouseButton1Click:Connect(ApplyChanges)
ResetButton.MouseButton1Click:Connect(ResetChanges)
CloseButton.MouseButton1Click:Connect(function()
    SrNoobHub:Destroy()
end)

-- Auto-atualizar quando personagem respawna
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    RootPart = Character:WaitForChild("HumanoidRootPart")
    wait(1)
    ApplyChanges()
end)

-- Aplicar automaticamente ao carregar
wait(1)
ApplyChanges()
