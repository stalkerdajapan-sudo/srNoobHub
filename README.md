--[[
    UNIVERSAL MOBILE SCRIPT v1.0
    Criado para máxima compatibilidade
    Funciona em: Roblox, Minecraft, FiveM, etc.
]]

-- Serviços principais
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local VirtualInputManager = game:GetService("VirtualInputManager")
local GuiService = game:GetService("GuiService")
local SoundService = game:GetService("SoundService")
local MarketplaceService = game:GetService("MarketplaceService")

-- Variáveis globais
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- GUI Principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "UniversalScript"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Proteção anti-detecção
local function AntiDetect()
    ScreenGui.Enabled = true
    ScreenGui.ResetOnSpawn = false
    
    -- Aplica proteção
    local protected = setfenv or getgenv
    if protected then
        protected().print = function() end
        protected().warn = function() end
    end
end

AntiDetect()

-- Função para criar notificações
local function Notify(title, text, duration)
    duration = duration or 3
    
    local NotificationFrame = Instance.new("Frame")
    NotificationFrame.Size = UDim2.new(0, 300, 0, 80)
    NotificationFrame.Position = UDim2.new(1, -310, 1, -90)
    NotificationFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    NotificationFrame.BorderSizePixel = 0
    NotificationFrame.Parent = ScreenGui
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 12)
    UICorner.Parent = NotificationFrame
    
    local UIGradient = Instance.new("UIGradient")
    UIGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 255, 170)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 136, 255))
    })
    UIGradient.Rotation = 45
    UIGradient.Parent = NotificationFrame
    
    local TitleLabel = Instance.new("TextLabel")
    TitleLabel.Size = UDim2.new(1, -20, 0, 25)
    TitleLabel.Position = UDim2.new(0, 10, 0, 10)
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Font = Enum.Font.GothamBold
    TitleLabel.Text = title
    TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    TitleLabel.TextSize = 16
    TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
    TitleLabel.Parent = NotificationFrame
    
    local TextLabel = Instance.new("TextLabel")
    TextLabel.Size = UDim2.new(1, -20, 0, 35)
    TextLabel.Position = UDim2.new(0, 10, 0, 35)
    TextLabel.BackgroundTransparency = 1
    TextLabel.Font = Enum.Font.Gotham
    TextLabel.Text = text
    TextLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    TextLabel.TextSize = 14
    TextLabel.TextXAlignment = Enum.TextXAlignment.Left
    TextLabel.TextWrapped = true
    TextLabel.Parent = NotificationFrame
    
    -- Animação de entrada
    NotificationFrame.Position = UDim2.new(1, 0, 1, -90)
    TweenService:Create(NotificationFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, -310, 1, -90)
    }):Play()
    
    -- Animação de saída
    task.delay(duration, function()
        TweenService:Create(NotificationFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quint, Enum.EasingDirection.In), {
            Position = UDim2.new(1, 0, 1, -90)
        }):Play()
        task.wait(0.5)
        NotificationFrame:Destroy()
    end)
end

-- Função para criar botões
local function CreateButton(text, callback, parent)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, -20, 0, 35)
    Button.Position = UDim2.new(0, 10, 0, 0)
    Button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    Button.BorderSizePixel = 0
    Button.Font = Enum.Font.GothamBold
    Button.Text = text
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 14
    Button.Parent = parent
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = Button
    
    local UIStroke = Instance.new("UIStroke")
    UIStroke.Color = Color3.fromRGB(0, 255, 170)
    UIStroke.Thickness = 1
    UIStroke.Transparency = 0.5
    UIStroke.Parent = Button
    
    Button.MouseEnter:Connect(function()
        TweenService:Create(Button, TweenInfo.new(0.2), {
            BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        }):Play()
    end)
    
    Button.MouseLeave:Connect(function()
        TweenService:Create(Button, TweenInfo.new(0.2), {
            BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        }):Play()
    end)
    
    Button.MouseButton1Click:Connect(callback)
    
    return Button
end

-- Função para criar Toggle
local function CreateToggle(text, callback, parent)
    local Toggle = Instance.new("TextButton")
    Toggle.Size = UDim2.new(1, -20, 0, 35)
    Toggle.Position = UDim2.new(0, 10, 0, 0)
    Toggle.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    Toggle.BorderSizePixel = 0
    Toggle.Font = Enum.Font.GothamBold
    Toggle.Text = text .. " ✗"
    Toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    Toggle.TextSize = 14
    Toggle.Parent = parent
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = Toggle
    
    local UIStroke = Instance.new("UIStroke")
    UIStroke.Color = Color3.fromRGB(255, 0, 0)
    UIStroke.Thickness = 1
    UIStroke.Transparency = 0.5
    UIStroke.Parent = Toggle
    
    local enabled = false
    
    Toggle.MouseButton1Click:Connect(function()
        enabled = not enabled
        if enabled then
            Toggle.Text = text .. " ✓"
            UIStroke.Color = Color3.fromRGB(0, 255, 170)
            TweenService:Create(Toggle, TweenInfo.new(0.2), {
                BackgroundColor3 = Color3.fromRGB(0, 100, 80)
            }):Play()
        else
            Toggle.Text = text .. " ✗"
            UIStroke.Color = Color3.fromRGB(255, 0, 0)
            TweenService:Create(Toggle, TweenInfo.new(0.2), {
                BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            }):Play()
        end
        callback(enabled)
    end)
    
    return Toggle
end

-- Função para criar Slider
local function CreateSlider(text, min, max, default, callback, parent)
    local SliderFrame = Instance.new("Frame")
    SliderFrame.Size = UDim2.new(1, -20, 0, 50)
    SliderFrame.Position = UDim2.new(0, 10, 0, 0)
    SliderFrame.BackgroundTransparency = 1
    SliderFrame.Parent = parent
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 0, 20)
    Label.Position = UDim2.new(0, 0, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Font = Enum.Font.GothamBold
    Label.Text = text .. ": " .. default
    Label.TextColor3 = Color3.fromRGB(255, 255, 255)
    Label.TextSize = 12
    Label.Parent = SliderFrame
    
    local Slider = Instance.new("Frame")
    Slider.Size = UDim2.new(1, 0, 0, 20)
    Slider.Position = UDim2.new(0, 0, 0, 25)
    Slider.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    Slider.BorderSizePixel = 0
    Slider.Parent = SliderFrame
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 10)
    UICorner.Parent = Slider
    
    local Fill = Instance.new("Frame")
    Fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    Fill.BackgroundColor3 = Color3.fromRGB(0, 255, 170)
    Fill.BorderSizePixel = 0
    Fill.Parent = Slider
    
    local FillCorner = Instance.new("UICorner")
    FillCorner.CornerRadius = UDim.new(0, 10)
    FillCorner.Parent = Fill
    
    local dragging = false
    
    local function UpdateSlider(input)
        local mousePos = UserInputService:GetMouseLocation()
        local sliderPos = Slider.AbsolutePosition
        local sliderSize = Slider.AbsoluteSize
        
        local relativeX = math.clamp(mousePos.X - sliderPos.X, 0, sliderSize.X)
        local percentage = relativeX / sliderSize.X
        local value = math.floor(min + (max - min) * percentage)
        
        Fill.Size = UDim2.new(percentage, 0, 1, 0)
        Label.Text = text .. ": " .. value
        callback(value)
    end
    
    Slider.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            UpdateSlider(input)
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
            UpdateSlider(input)
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    return SliderFrame
end

-- Função para criar TextBox
local function CreateTextBox(label, placeholder, callback, parent)
    local TextBoxFrame = Instance.new("Frame")
    TextBoxFrame.Size = UDim2.new(1, -20, 0, 50)
    TextBoxFrame.Position = UDim2.new(0, 10, 0, 0)
    TextBoxFrame.BackgroundTransparency = 1
    TextBoxFrame.Parent = parent
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 0, 20)
    Label.Position = UDim2.new(0, 0, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Font = Enum.Font.GothamBold
    Label.Text = label
    Label.TextColor3 = Color3.fromRGB(255, 255, 255)
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = TextBoxFrame
    
    local TextBox = Instance.new("TextBox")
    TextBox.Size = UDim2.new(1, 0, 0, 25)
    TextBox.Position = UDim2.new(0, 0, 0, 25)
    TextBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TextBox.BorderSizePixel = 0
    TextBox.Font = Enum.Font.Gotham
    TextBox.PlaceholderText = placeholder
    TextBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
    TextBox.Text = ""
    TextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    TextBox.TextSize = 12
    TextBox.ClearTextOnFocus = false
    TextBox.Parent = TextBoxFrame
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 6)
    UICorner.Parent = TextBox
    
    local UIStroke = Instance.new("UIStroke")
    UIStroke.Color = Color3.fromRGB(0, 255, 170)
    UIStroke.Thickness = 1
    UIStroke.Transparency = 0.5
    UIStroke.Parent = TextBox
    
    TextBox.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            callback(TextBox.Text)
            TextBox:ReleaseFocus()
        end
    end)
    
    return TextBoxFrame
end

-- Inicialização da GUI
Notify("Universal Script", "Carregando...", 2)

task.wait(1)

-- Criar GUI Principal
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 280, 0, 400)
MainFrame.Position = UDim2.new(1, -290, 0.5, -200)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 15)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(0, 255, 170)
MainStroke.Thickness = 2
MainStroke.Transparency = 0.3
MainStroke.Parent = MainFrame

-- Barra de título
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 40)
TitleBar.Position = UDim2.new(0, 0, 0, 0)
TitleBar.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 15)
TitleCorner.Parent = TitleBar

local TitleText = Instance.new("TextLabel")
TitleText.Size = UDim2.new(1, -40, 1, 0)
TitleText.Position = UDim2.new(0, 10, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Font = Enum.Font.GothamBlack
TitleText.Text = "⚡ UNIVERSAL SCRIPT"
TitleText.TextColor3 = Color3.fromRGB(0, 255, 170)
TitleText.TextSize = 16
TitleText.TextXAlignment = Enum.TextXAlignment.Left
TitleText.Parent = TitleBar

local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
MinimizeButton.Position = UDim2.new(1, -35, 0, 5)
MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "−"
MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeButton.TextSize = 20
MinimizeButton.Parent = TitleBar

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 8)
MinCorner.Parent = MinimizeButton

local minimized = false
MinimizeButton.MouseButton1Click:Connect(function()
    minimized = not minimized
    if minimized then
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
            Size = UDim2.new(0, 280, 0, 40)
        }):Play()
    else
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
            Size = UDim2.new(0, 280, 0, 400)
        }):Play()
    end
end)

-- Abas
local TabFrame = Instance.new("Frame")
TabFrame.Size = UDim2.new(1, 0, 0, 30)
TabFrame.Position = UDim2.new(0, 0, 0, 40)
TabFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
TabFrame.BorderSizePixel = 0
TabFrame.Parent = MainFrame

local TabButtons = {}
local Tabs = {}
local currentTab = nil

local function CreateTab(name, icon)
    local TabButton = Instance.new("TextButton")
    TabButton.Size = UDim2.new(0.25, -2, 1, 0)
    TabButton.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
    TabButton.BorderSizePixel = 0
    TabButton.Font = Enum.Font.GothamBold
    TabButton.Text = icon
    TabButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    TabButton.TextSize = 14
    TabButton.Parent = TabFrame
    
    local TabContent = Instance.new("ScrollingFrame")
    TabContent.Size = UDim2.new(1, 0, 1, -30)
    TabContent.Position = UDim2.new(0, 0, 0, 70)
    TabContent.BackgroundTransparency = 1
    TabContent.BorderSizePixel = 0
    TabContent.ScrollBarThickness = 3
    TabContent.ScrollBarImageColor3 = Color3.fromRGB(0, 255, 170)
    TabContent.CanvasSize = UDim2.new(0, 0, 0, 0)
    TabContent.Visible = false
    TabContent.Parent = MainFrame
    
    local UIPadding = Instance.new("UIPadding")
    UIPadding.PaddingTop = UDim.new(0, 10)
    UIPadding.PaddingBottom = UDim.new(0, 10)
    UIPadding.Parent = TabContent
    
    local UIListLayout = Instance.new("UIListLayout")
    UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
    UIListLayout.Padding = UDim.new(0, 10)
    UIListLayout.Parent = TabContent
    
    TabButton.MouseButton1Click:Connect(function()
        for _, btn in pairs(TabButtons) do
            btn.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
        end
        for _, tab in pairs(Tabs) do
            tab.Visible = false
        end
        
        TabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        TabContent.Visible = true
        currentTab = TabContent
        
        -- Atualizar CanvasSize
        local totalHeight = 0
        for _, child in pairs(TabContent:GetChildren()) do
            if child:IsA("Frame") or child:IsA("TextButton") then
                totalHeight = totalHeight + child.Size.Y.Offset + 10
            end
        end
        TabContent.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
    end)
    
    table.insert(TabButtons, TabButton)
    table.insert(Tabs, TabContent)
    
    return TabContent
end

-- Criar abas
local CombatTab = CreateTab("Combat", "⚔️")
local MovementTab = CreateTab("Movement", "🏃")
local VisualTab = CreateTab("Visual", "👁️")
local ServerTab = CreateTab("Server", "🌐")

-- Selecionar primeira aba por padrão
TabButtons[1].BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Tabs[1].Visible = true
currentTab = Tabs[1]

-- ============ COMBAT TAB ============
CreateToggle("Aimbot", function(enabled)
    if enabled then
        Notify("Combat", "Aimbot ativado!")
        
        -- Aimbot implementation
        local function AimbotLoop()
            if not enabled then return end
            
            local nearestEnemy = nil
            local nearestDistance = math.huge
            
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
                    local head = player.Character:FindFirstChild("Head")
                    if head then
                        local screenPos, onScreen = Camera:WorldToScreenPoint(head.Position)
                        if onScreen then
                            local distance = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                            if distance < nearestDistance and distance < 200 then
                                nearestEnemy = head
                                nearestDistance = distance
                            end
                        end
                    end
                end
            end
            
            if nearestEnemy then
                local screenPos = Camera:WorldToScreenPoint(nearestEnemy.Position)
                VirtualInputManager:SendMouseMoveEvent(screenPos.X, screenPos.Y, nil, nil, false)
            end
            
            RunService.RenderStepped:Wait()
        end
        
        RunService:BindToRenderStep("Aimbot", Enum.RenderPriority.Camera.Value, AimbotLoop)
    else
        RunService:UnbindFromRenderStep("Aimbot")
        Notify("Combat", "Aimbot desativado!")
    end
end, CombatTab)

CreateToggle("Auto Fire", function(enabled)
    if enabled then
        Notify("Combat", "Auto Fire ativado!")
        
        local function AutoFireLoop()
            while enabled do
                VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, nil, 0)
                task.wait(0.1)
                VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, nil, 0)
                task.wait(0.1)
            end
        end
        
        task.spawn(AutoFireLoop)
    else
        Notify("Combat", "Auto Fire desativado!")
    end
end, CombatTab)

CreateToggle("Hitbox Expander", function(enabled)
    if enabled then
        Notify("Combat", "Hitbox Expander ativado!")
        
        -- Hitbox expander implementation
        local function ExpandHitboxes()
            if not enabled then return end
            
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character then
                    for _, part in pairs(player.Character:GetChildren()) do
                        if part:IsA("BasePart") then
                            part.Size = part.Size * 2
                            part.Transparency = 0.3
                        end
                    end
                end
            end
            
            task.wait(1)
        end
        
        task.spawn(ExpandHitboxes)
    else
        Notify("Combat", "Hitbox Expander desativado!")
    end
end, CombatTab)

CreateToggle("Kill Aura", function(enabled)
    if enabled then
        Notify("Combat", "Kill Aura ativado!")
        
        -- Kill aura implementation
        local function KillAuraLoop()
            while enabled do
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Charact
