--[[
    SCRIPT: Ultimate Mobile Cheat Suite - FINAL
    AUTOR: SrNoob (Fins Educacionais)
    VERSÃO: 12.0 - COM CÍRCULO DE MIRA
    NOTA: Interface touch-friendly com mira circular
]]

local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local runService = game:GetService("RunService")
local userInputService = game:GetService("UserInputService")
local camera = workspace.CurrentCamera
local heartbeat = game:GetService("RunService").Heartbeat
local guiService = game:GetService("GuiService")

-- ============================================
-- VERIFICAÇÃO DE DISPOSITIVO
-- ============================================
local isMobile = userInputService.TouchEnabled

-- ============================================
-- CONFIGURAÇÕES DO AIMBOT
-- ============================================
local Aimbot = {
    active = true,
    smoothness = 0.3,
    fovRadius = 300,
    hitboxSize = 2.5,
    aimPart = "Head",
    targetPlayers = true,
    targetNPCs = true,
    circleSize = 150, -- Tamanho do círculo
    circleStrength = 1.0, -- Força da mira
}

-- ============================================
-- CRIAÇÃO DO CÍRCULO DE MIRA
-- ============================================
local function createAimCircle()
    -- ScreenGui para o círculo
    local circleGui = Instance.new("ScreenGui")
    circleGui.Name = "AimCircle"
    circleGui.ResetOnSpawn = false
    circleGui.Parent = player.PlayerGui
    
    -- Frame transparente para o círculo
    local circleFrame = Instance.new("Frame")
    circleFrame.Size = UDim2.new(0, Aimbot.circleSize, 0, Aimbot.circleSize)
    circleFrame.Position = UDim2.new(0.5, -Aimbot.circleSize/2, 0.5, -Aimbot.circleSize/2)
    circleFrame.BackgroundTransparency = 1
    circleFrame.Parent = circleGui
    
    -- Círculo externo (borda)
    local outerCircle = Instance.new("ImageLabel")
    outerCircle.Size = UDim2.new(1, 0, 1, 0)
    outerCircle.Position = UDim2.new(0, 0, 0, 0)
    outerCircle.BackgroundTransparency = 1
    outerCircle.Image = "rbxassetid://131604333" -- Círculo
    outerCircle.ImageColor3 = Color3.fromRGB(0, 255, 100)
    outerCircle.ImageTransparency = 0.3
    outerCircle.Parent = circleFrame
    
    -- Círculo interno (ponto central)
    local innerCircle = Instance.new("ImageLabel")
    innerCircle.Size = UDim2.new(0.1, 0, 0.1, 0)
    innerCircle.Position = UDim2.new(0.45, 0, 0.45, 0)
    innerCircle.BackgroundTransparency = 1
    innerCircle.Image = "rbxassetid://131604333"
    innerCircle.ImageColor3 = Color3.fromRGB(255, 0, 0)
    innerCircle.ImageTransparency = 0.2
    innerCircle.Parent = circleFrame
    
    -- Linhas de mira (cruz)
    local function createCrossLine(size, position, rotation)
        local line = Instance.new("ImageLabel")
        line.Size = UDim2.new(0, size, 0, 2)
        line.Position = UDim2.new(position.X, position.Y, 0.5, -1)
        line.BackgroundTransparency = 1
        line.Image = "rbxassetid://131604333"
        line.ImageColor3 = Color3.fromRGB(0, 255, 100)
        line.ImageTransparency = 0.4
        line.Rotation = rotation or 0
        line.Parent = circleFrame
        return line
    end
    
    -- Linha superior
    createCrossLine(20, UDim2.new(0.5, -10, 0, -Aimbot.circleSize/2 - 5), 0)
    -- Linha inferior
    createCrossLine(20, UDim2.new(0.5, -10, 0, Aimbot.circleSize/2 + 5), 0)
    -- Linha esquerda
    local lineLeft = Instance.new("ImageLabel")
    lineLeft.Size = UDim2.new(0, 2, 0, 20)
    lineLeft.Position = UDim2.new(0, -Aimbot.circleSize/2 - 5, 0.5, -10)
    lineLeft.BackgroundTransparency = 1
    lineLeft.Image = "rbxassetid://131604333"
    lineLeft.ImageColor3 = Color3.fromRGB(0, 255, 100)
    lineLeft.ImageTransparency = 0.4
    lineLeft.Parent = circleFrame
    -- Linha direita
    local lineRight = Instance.new("ImageLabel")
    lineRight.Size = UDim2.new(0, 2, 0, 20)
    lineRight.Position = UDim2.new(1, 5, 0.5, -10)
    lineRight.BackgroundTransparency = 1
    lineRight.Image = "rbxassetid://131604333"
    lineRight.ImageColor3 = Color3.fromRGB(0, 255, 100)
    lineRight.ImageTransparency = 0.4
    lineRight.Parent = circleFrame
    
    -- Texto "FOV" no círculo
    local fovText = Instance.new("TextLabel")
    fovText.Size = UDim2.new(0, 60, 0, 20)
    fovText.P
