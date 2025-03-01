--[[
   leak by jermz.cc
   join for more script
   https://discord.gg/UgQAPcBtpy
]]--

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local LocalPlayer = Players.LocalPlayer
local Mouse = game.Players.LocalPlayer:GetMouse()
local CamlockState = false
local Prediction = 0.1768521

function FindNearestEnemy()
    local ClosestDistance, ClosestPlayer = math.huge, nil
    local CenterPosition = Vector2.new(game:GetService("GuiService"):GetScreenResolution().X / 2, game:GetService("GuiService"):GetScreenResolution().Y / 2)

    for _, Player in ipairs(game:GetService("Players"):GetPlayers()) do
        if Player ~= LocalPlayer then
            local Character = Player.Character
            if Character and Character:FindFirstChild("HumanoidRootPart") and Character.Humanoid.Health > 0 then
                local Position, IsVisibleOnViewport = game:GetService("Workspace").CurrentCamera:WorldToViewportPoint(Character.HumanoidRootPart.Position)
                if IsVisibleOnViewport then
                    local Distance = (CenterPosition - Vector2.new(Position.X, Position.Y)).Magnitude
                    if Distance < ClosestDistance then
                        ClosestPlayer = Player
                        ClosestDistance = Distance
                    end
                end
            end
        end
    end

    return ClosestPlayer
end

local function createHighlight(target)
    if target and target.Character then
        local highlight = Instance.new("Highlight")
        highlight.Adornee = target.Character
        highlight.FillColor = Color3.fromRGB(255, 0, 0)
        highlight.OutlineColor = Color3.fromRGB(0, 0, 0)
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 0
        highlight.Parent = target.Character
        return highlight
    end
end

local function removeHighlight(target)
    if target and target.Character then
        for _, child in ipairs(target.Character:GetChildren()) do
            if child:IsA("Highlight") then
                child:Destroy()
            end
        end
    end
end

local enemy = nil
local highlightInstance = nil

RunService.Heartbeat:Connect(function()
    if CamlockState and enemy and enemy.Character then
        local camera = workspace.CurrentCamera
        local targetPart = enemy.Character:FindFirstChild("HumanoidRootPart")
        if targetPart then
            camera.CFrame = CFrame.new(camera.CFrame.p, targetPart.Position + targetPart.Velocity * Prediction)
        end
    end
end)

local veerve = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local UICorner = Instance.new("UICorner")
local TextButton = Instance.new("TextButton")
local UICorner_2 = Instance.new("UICorner")

veerve.Name = "veerve"
veerve.Parent = game.CoreGui
veerve.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

Frame.Parent = veerve
Frame.BackgroundColor3 = Color3.fromRGB(165, 42, 42)
Frame.BorderColor3 = Color3.fromRGB(0, 0, 0)
Frame.BorderSizePixel = 0
Frame.Position = UDim2.new(0.5, -50, 0.5, -50) 
Frame.Size = UDim2.new(0, 110, 0, 70) 
Frame.Active = true
Frame.Draggable = true

UICorner.Parent = Frame

TextButton.Parent = Frame
TextButton.BackgroundColor3 = Color3.fromRGB(0, 0, 0) 
TextButton.BorderColor3 = Color3.fromRGB(0, 0, 0)
TextButton.BorderSizePixel = 0
TextButton.Position = UDim2.new(0.1, 0, 0.1, 0)
TextButton.Size = UDim2.new(0.8, 0, 0.8, 0) 
TextButton.Font = Enum.Font.Arcade
TextButton.Text = "Hax.lol Off"
TextButton.TextColor3 = Color3.fromRGB(255, 0, 0) 
TextButton.TextScaled = true
TextButton.TextSize = 11.000
TextButton.TextWrapped = true

TextButton.MouseButton1Click:Connect(function()
    CamlockState = not CamlockState
    TextButton.Text = CamlockState and "jermz.lol On" or "jermz.lol Off"
    
    if CamlockState then
        enemy = FindNearestEnemy()
        if enemy then
            highlightInstance = createHighlight(enemy)
            StarterGui:SetCore("SendNotification", {
                Title = "jermz.lol âœ…",
                Text = "ðŸŽ¯: " .. enemy.DisplayName,
                Duration = 3
            })
        end
    else
        if highlightInstance then
            removeHighlight(enemy)
            highlightInstance = nil
        end
        enemy = nil
    end
end)

UICorner_2.Parent = TextButton

local rainbowColors = {
    Color3.fromRGB(255, 0, 0),     
    Color3.fromRGB(255, 165, 0),   
    Color3.fromRGB(255, 255, 0),  
    Color3.fromRGB(0, 128, 0),     
    Color3.fromRGB(0, 0, 255),     
    Color3.fromRGB(75, 0, 130),    
    Color3.fromRGB(148, 0, 211)  
}

local rainbowIndex = 1
local rainbowCycleSpeed = 0.09

RunService.RenderStepped:Connect(function(deltaTime)
    local index = math.floor(tick() / rainbowCycleSpeed) % #rainbowColors + 1
    Frame.BackgroundColor3 = rainbowColors[index]
end)

StarterGui:SetCore("SendNotification", {
    Title = "Jermz.lol loadedðŸ¤‘",
    Text = "made by veervegoat on dcðŸ",
    Duration = 5
})
