
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Orion/main/source"))()
local Window = OrionLib:MakeWindow({
    Name = "Main",
    HidePremium = false,
    SaveConfig = false,
    ConfigFolder = "OrionUI",
    IntroText = "Main Menu",
    IntroIcon = nil
})

-- Tema roxo
OrionLib:ChangeColor("Main", Color3.fromRGB(128, 0, 255)) -- Roxo escuro
OrionLib:ChangeColor("Accent", Color3.fromRGB(180, 0, 255)) -- Roxo claro

local Tab = Window:MakeTab({
    Name = "Cheats",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local Section = Tab:AddSection({
    Name = "Hi"
})

Tab:AddToggle({
    Name = "Farm",
    Default = false,
    Callback = function(Value)
        print("Farm:", Value)
    end    
})

Tab:AddSlider({
    Name = "Wow",
    Min = 0,
    Max = 10,
    Default = 6,
    Color = Color3.fromRGB(180, 0, 255),
    Increment = 1,
    Callback = function(Value)
        print("Wow value:", Value)
    end    
})

Tab:AddToggle({
    Name = "Aimbot",
    Default = false,
    Callback = function(Value)
        print("Aimbot:", Value)
    end    
})

Tab:AddButton({
    Name = "esp",
    Callback = function()
        local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local camera = workspace.CurrentCamera
local localPlayer = Players.LocalPlayer

-- Tabela para armazenar os tracers
local espObjects = {}

-- Função para criar ESP para um jogador inimigo
local function createESP(player)
    if espObjects[player] then return end

    local esp = {}

    -- TextLabel (BillboardGui)
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "ESP"
    billboard.Size = UDim2.new(0, 100, 0, 40)
    billboard.StudsOffset = Vector3.new(0, 2, 0)
    billboard.AlwaysOnTop = true

    local text = Instance.new("TextLabel")
    text.Size = UDim2.new(1, 0, 1, 0)
    text.BackgroundTransparency = 1
    text.Text = player.Name
    text.TextColor3 = Color3.fromRGB(255, 0, 0)
    text.TextStrokeTransparency = 0.3
    text.Font = Enum.Font.SourceSansBold
    text.TextScaled = true
    text.Parent = billboard

    -- Linha Trace (Drawing API)
    local tracer = Drawing.new("Line")
    tracer.Color = Color3.fromRGB(255, 0, 0)
    tracer.Thickness = 1.5
    tracer.Transparency = 1
    tracer.Visible = true

    esp.billboard = billboard
    esp.tracer = tracer

    espObjects[player] = esp
end

-- Atualiza ESP
local function updateESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= localPlayer and player.Character and player.Character:FindFirstChild("Head") then
            local isEnemy = player.Team ~= localPlayer.Team
            if isEnemy then
                if not espObjects[player] then
                    createESP(player)
                end

                local esp = espObjects[player]
                local head = player.Character:FindFirstChild("Head")

                if head and esp then
                    -- Billboard
                    if not esp.billboard.Parent then
                        esp.billboard.Adornee = head
                        esp.billboard.Parent = head
                    end

                    -- Linha de trace
                    local headPos, onScreen = camera:WorldToViewportPoint(head.Position)
                    if onScreen then
                        esp.tracer.From = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y)
                        esp.tracer.To = Vector2.new(headPos.X, headPos.Y)
                        esp.tracer.Visible = true
                    else
                        esp.tracer.Visible = false
                    end
                end
            else
                -- Remove ESP se virar aliado
                if espObjects[player] then
                    espObjects[player].billboard:Destroy()
                    espObjects[player].tracer:Remove()
                    espObjects[player] = nil
                end
            end
        elseif espObjects[player] then
            -- Remove ESP se jogador sair ou morrer
            espObjects[player].billboard:Destroy()
            espObjects[player].tracer:Remove()
            espObjects[player] = nil
        end
    end
end

RunService.RenderStepped:Connect(updateESP)

-- Remove ESP quando jogador sair
Players.PlayerRemoving:Connect(function(player)
    if espObjects[player] then
        espObjects[player].billboard:Destroy()
        espObjects[player].tracer:Remove()
        espObjects[player] = nil
    end
end)
})
.
