--[[ 
File: AntiCheatTester.lua 
Place this script in a LocalScript for testing in Roblox Studio 
]]

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

-- Variables
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local camera = Workspace.CurrentCamera

local cheatsEnabled = {
    SpeedHack = false,
    NoClip = false,
    AntiAFK = false,
    ESP = false,
    AimBot = false
}

local activeHighlights = {} -- Tracks active ESP highlights
local lockedTarget = nil -- Tracks the Aimbot target

-- GUI Setup
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.ResetOnSpawn = false
screenGui.Name = "CheatTestingGUI"

local mainFrame = Instance.new("Frame", screenGui)
mainFrame.Size = UDim2.new(0.3, 0, 0.5, 0)
mainFrame.Position = UDim2.new(0.35, 0, 0.25, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainFrame.Visible = true

local closeButton = Instance.new("TextButton", mainFrame)
closeButton.Size = UDim2.new(0.1, 0, 0.1, 0)
closeButton.Position = UDim2.new(0.9, 0, 0)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)

local minimizeButton = Instance.new("TextButton", screenGui)
minimizeButton.Size = UDim2.new(0.05, 0, 0.05, 0)
minimizeButton.Position = UDim2.new(0.9, 0, 0.9, 0)
minimizeButton.Text = "+"
minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
minimizeButton.Visible = false

local cheatLabels = {"Speed Hack", "No Clip", "Anti AFK", "ESP", "AimBot"}
local cheatButtons = {}

for i, label in ipairs(cheatLabels) do
    local textLabel = Instance.new("TextLabel", mainFrame)
    textLabel.Size = UDim2.new(0.6, 0, 0.1, 0)
    textLabel.Position = UDim2.new(0.05, 0, 0.1 * (i - 1), 0)
    textLabel.Text = label
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    textLabel.BackgroundTransparency = 1

    local button = Instance.new("TextButton", mainFrame)
    button.Size = UDim2.new(0.3, 0, 0.1, 0)
    button.Position = UDim2.new(0.7, 0, 0.1 * (i - 1), 0)
    button.Text = "OFF"
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.BackgroundColor3 = Color3.fromRGB(255, 0, 0)

    cheatButtons[label] = button
end

closeButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
    minimizeButton.Visible = true
end)

minimizeButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = true
    minimizeButton.Visible = false
end)

-- Feature Toggles
local function toggleFeature(label)
    cheatsEnabled[label] = not cheatsEnabled[label]
    local button = cheatButtons[label]
    button.Text = cheatsEnabled[label] and "ON" or "OFF"
    button.BackgroundColor3 = cheatsEnabled[label] and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
end

for label, button in pairs(cheatButtons) do
    button.MouseButton1Click:Connect(function()
        toggleFeature(label)
    end)
end

-- Speed Hack
RunService.RenderStepped:Connect(function()
    if cheatsEnabled.SpeedHack then
        humanoid.WalkSpeed = 80 -- 5x speed
    else
        humanoid.WalkSpeed = 16 -- Default speed
    end
end)

-- No Clip
RunService.Stepped:Connect(function()
    if cheatsEnabled.NoClip then
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    else
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
    end
end)

-- Anti AFK
spawn(function()
    while wait(20) do
        if cheatsEnabled.AntiAFK then
            game:GetService("VirtualUser"):CaptureController()
            game:GetService("VirtualUser"):ClickButton2(Vector2.new())
        end
    end
end)

-- ESP System
local function createESP(target)
    if target and not activeHighlights[target] then
        local highlight = Instance.new("Highlight")
        highlight.Parent = target.Character
        highlight.FillColor = Color3.fromRGB(255, 0, 0)
        highlight.FillTransparency = 0.5
        highlight.OutlineColor = Color3.fromRGB(0, 0, 0)
        activeHighlights[target] = highlight
    end
end

local function destroyESP(target)
    if activeHighlights[target] then
        activeHighlights[target]:Destroy()
        activeHighlights[target] = nil
    end
end

local function updateESP()
    for _, target in ipairs(Players:GetPlayers()) do
        if cheatsEnabled.ESP then
            createESP(target)
        else
            destroyESP(target)
        end
    end
end

Players.PlayerAdded:Connect(function(target)
    if cheatsEnabled.ESP then
        createESP(target)
    end
end)

Players.PlayerRemoving:Connect(function(target)
    destroyESP(target)
end)

RunService.Heartbeat:Connect(function()
    if cheatsEnabled.ESP then
        updateESP()
    end
end)

-- AimBot
local function lockOntoNearestPlayer()
    local closestPlayer = nil
    local closestDistance = math.huge

    for _, target in ipairs(Players:GetPlayers()) do
        if target ~= player and target.Character and target.Character:FindFirstChild("Head") then
            local distance = (target.Character.Head.Position - character.Head.Position).Magnitude
            if distance < closestDistance then
                closestPlayer = target
                closestDistance = distance
            end
        end
    end

    if closestPlayer then
        lockedTarget = closestPlayer.Character.Head
    else
        lockedTarget = nil
    end
end

cheatButtons["AimBot"].MouseButton1Click:Connect(function()
    toggleFeature("AimBot")

    if cheatsEnabled.AimBot then
        lockOntoNearestPlayer()
    else
        lockedTarget = nil
    end
end)

RunService.RenderStepped:Connect(function()
    if cheatsEnabled.AimBot and lockedTarget then
        camera.CFrame = CFrame.new(camera.CFrame.Position, lockedTarget.Position)
    end
end)
