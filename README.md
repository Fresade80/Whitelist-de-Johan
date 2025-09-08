local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

getgenv().AutoKillTargets = getgenv().AutoKillTargets or {}
getgenv().AutoKillEnabled = true

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoKillMenu"
screenGui.ResetOnSpawn = false
screenGui.Parent = game.CoreGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0,650,0,400)
mainFrame.Position = UDim2.new(0.5,-325,0.5,-200)
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0,10)
corner.Parent = mainFrame

-- Título (botón para minimizar y arrastrar)
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1,0,0,30)
titleBar.BackgroundColor3 = Color3.fromRGB(255,0,0)
titleBar.Parent = mainFrame

local titleButton = Instance.new("TextButton")
titleButton.Size = UDim2.new(1,0,1,0)
titleButton.BackgroundTransparency = 1
titleButton.Text = "Johan's script"
titleButton.TextColor3 = Color3.fromRGB(255,255,255)
titleButton.Font = Enum.Font.GothamBold
titleButton.TextSize = 20
titleButton.TextXAlignment = Enum.TextXAlignment.Center
titleButton.Parent = titleBar

local buttonContainer = Instance.new("Frame")
buttonContainer.Size = UDim2.new(0,400,1,-30)
buttonContainer.Position = UDim2.new(0,0,0,30)
buttonContainer.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
buttonContainer.BorderSizePixel = 0
buttonContainer.Parent = mainFrame

-- Botones
local buttonNames = {
    "Activar pegar muerto",
    "Auto Kill a todos",
    "Auto Kill a jugadores",
    "Auto Reset",
    "Espectear"
}

local yPosition = 20
local buttons = {}

-- Función para cambiar el color del botón (verde y original)
local function toggleButtonColor(button)
    if button.BackgroundColor3 == Color3.fromRGB(0, 255, 0) then
        button.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    else
        button.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    end
end

for i, name in ipairs(buttonNames) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0,380,0,40)
    btn.Position = UDim2.new(0,10,0, yPosition)
    btn.BackgroundColor3 = Color3.fromRGB(0,0,0)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 18
    btn.Parent = buttonContainer

    table.insert(buttons, btn)
    yPosition = yPosition + 50

    btn.MouseButton1Click:Connect(function()
        toggleButtonColor(btn)
        if name == "Activar pegar muerto" then
            -- Tu lógica aquí para activar pegar muerto
        elseif name == "Auto Kill a todos" then
            -- Tu lógica aquí para Auto Kill a todos
        elseif name == "Auto Kill a jugadores" then
            -- Tu lógica aquí para Auto Kill a jugadores
        elseif name == "Auto Reset" then
            -- Tu lógica aquí para Auto Reset
        elseif name == "Espectear" then
            -- Tu lógica aquí para Espectear
        end
    end)
end

-- Drag & minimize (simple)
local dragging, dragInput, dragStart, startPos
titleButton.MouseButton1Down:Connect(function(input)
    dragging = true
    dragStart = input.Position
    startPos = mainFrame.Position
end)

titleButton.MouseButton1Up:Connect(function()
    dragging = false
end)

titleButton.MouseMoved:Connect(function(input)
    if dragging then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- Minimize toggle
local minimized = false
titleButton.MouseButton2Click:Connect(function()
    minimized = not minimized
    buttonContainer.Visible = not minimized
    mainFrame.Size = minimized and UDim2.new(0,650,0,30) or UDim2.new(0,650,0,400)
end)
