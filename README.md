-- ESP Box com botão flutuante para ligar/desligar
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local espEnabled = false
local espBoxes = {}

-- Cria o botão flutuante
local function createToggleButton()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "ESPButtonGUI"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = game:GetService("CoreGui")

    local button = Instance.new("ImageButton")
    button.Size = UDim2.new(0, 50, 0, 50)
    button.Position = UDim2.new(0, 10, 0, 200)
    button.BackgroundTransparency = 1
    button.Image = "rbxassetid://8569322835" -- imagem de botão (ícone de olho estilizado)
    button.Name = "ESPButton"
    button.Parent = screenGui

    button.MouseButton1Click:Connect(function()
        espEnabled = not espEnabled
        if not espEnabled then
            -- Remover todas as boxes
            for _, box in pairs(espBoxes) do
                if box and box.Adornee then
                    box:Destroy()
                end
            end
            espBoxes = {}
        end
    end)
end

-- Cria o box no personagem
local function createESPBox(character)
    local box = Instance.new("BoxHandleAdornment")
    box.Size = Vector3.new(4, 6, 2)
    box.Color3 = Color3.new(1, 0.5, 0) -- Laranja
    box.Transparency = 0.5
    box.ZIndex = 0
    box.AlwaysOnTop = true
    box.Adornee = character:FindFirstChild("HumanoidRootPart")
    box.Parent = game:GetService("CoreGui")
    table.insert(espBoxes, box)
end

-- Atualiza ESP continuamente
RunService.RenderStepped:Connect(function()
    if espEnabled then
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local alreadyHasBox = false
                for _, box in pairs(espBoxes) do
                    if box.Adornee == player.Character:FindFirstChild("HumanoidRootPart") then
                        alreadyHasBox = true
                        break
                    end
                end
                if not alreadyHasBox then
                    createESPBox(player.Character)
                end
            end
        end
    end
end)

-- Criar botão na tela
createToggleButton()
