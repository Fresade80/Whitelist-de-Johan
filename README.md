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
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Fondo negro
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Parent = screenGui


local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0,10)
corner.Parent = mainFrame

-- Té“†tulo (botè´¸n para minimizar y arrastrar)
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1,0,0,30)
titleBar.BackgroundColor3 = Color3.fromRGB(255,0,0)  -- Botè´¸n de minimizar y abrir el menç…¤ en rojo
titleBar.Parent = mainFrame

local titleButton = Instance.new("TextButton")
titleButton.Size = UDim2.new(1,0,1,0)
titleButton.BackgroundTransparency = 1
titleButton.Text = "Johan's script"  -- Aqué“† cambiamos el texto por "Johan's script"
titleButton.TextColor3 = Color3.fromRGB(255,255,255)  -- Letras blancas
titleButton.Font = Enum.Font.GothamBold
titleButton.TextSize = 20
titleButton.TextXAlignment = Enum.TextXAlignment.Center
titleButton.Parent = titleBar


local buttonContainer = Instance.new("Frame")
buttonContainer.Size = UDim2.new(0,400,1,-30)
buttonContainer.Position = UDim2.new(0,0,0,30)
buttonContainer.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Fondo negro
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

-- Funciè´¸n para cambiar el color del botè´¸n (verde y original)
local function toggleButtonColor(button)
    if button.BackgroundColor3 == Color3.fromRGB(0, 255, 0) then
        button.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Color negro
    else
        button.BackgroundColor3 = Color3.fromRGB(0, 255, 0)  -- Cambiar a verde
    end
end

for _, name in ipairs(buttonNames) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0,360,0,40)
    btn.Position = UDim2.new(0,20,0,yPosition)
    btn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Fondo negro
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)  -- Letras blancas
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 18
    btn.Parent = buttonContainer

    buttons[name] = btn

    -- Cambiar color al presionar el botè´¸n
    btn.MouseButton1Click:Connect(function()
        toggleButtonColor(btn)
    end)

    yPosition = yPosition + 50
end


local autoKillPanel = Instance.new("Frame")
autoKillPanel.Size = UDim2.new(0,250,0,360)
autoKillPanel.Position = UDim2.new(0,400,0,30)
autoKillPanel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  
autoKillPanel.BorderSizePixel = 0
autoKillPanel.Visible = false
autoKillPanel.Active = true
autoKillPanel.Draggable = false
autoKillPanel.Parent = mainFrame

local titleAK = Instance.new("TextLabel")
titleAK.Size = UDim2.new(1,0,0,30)
titleAK.BackgroundColor3 = Color3.fromRGB(50,50,50)
titleAK.Text = "Selecionar Enemigo"
titleAK.TextColor3 = Color3.fromRGB(255,255,255)  
titleAK.Font = Enum.Font.SourceSansBold
titleAK.TextSize = 20
titleAK.Parent = autoKillPanel

local scrollingFrame = Instance.new("ScrollingFrame")
scrollingFrame.Size = UDim2.new(1,0,1,-30)
scrollingFrame.Position = UDim2.new(0,0,0,30)
scrollingFrame.CanvasSize = UDim2.new(0,0,0,0)
scrollingFrame.BackgroundColor3 = Color3.fromRGB(35,35,35)
scrollingFrame.ScrollBarThickness = 6
scrollingFrame.Parent = autoKillPanel

local function updatePlayerButtons()
    scrollingFrame:ClearAllChildren()
    local ySize = 0
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= Players.LocalPlayer then
            local button = Instance.new("TextButton")
            button.Size = UDim2.new(1,-10,0,30)
            button.Position = UDim2.new(0,5,0,ySize)
            button.BackgroundColor3 = getgenv().AutoKillTargets[plr.Name] and Color3.fromRGB(150,0,0) or Color3.fromRGB(70,70,70)
            button.Text = plr.Name
            button.TextColor3 = Color3.fromRGB(255,255,255)  -- Letras blancas
            button.Font = Enum.Font.SourceSans
            button.TextSize = 18
            button.Parent = scrollingFrame

            button.MouseButton1Click:Connect(function()
                if getgenv().AutoKillTargets[plr.Name] then
                    getgenv().AutoKillTargets[plr.Name] = nil
                else
                    getgenv().AutoKillTargets[plr.Name] = true
                end
                updatePlayerButtons()
            end)

            ySize = ySize + 35
        end
    end
    scrollingFrame.CanvasSize = UDim2.new(0,0,0,ySize)
end

Players.PlayerAdded:Connect(updatePlayerButtons)
Players.PlayerRemoving:Connect(updatePlayerButtons)
updatePlayerButtons()


local spectatePanel = Instance.new("Frame")
spectatePanel.Size = UDim2.new(0,250,0,360)
spectatePanel.Position = UDim2.new(0,400,0,30)
spectatePanel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Fondo negro
spectatePanel.BorderSizePixel = 0
spectatePanel.Visible = false
spectatePanel.Active = true
spectatePanel.Draggable = false
spectatePanel.Parent = mainFrame

local titleSpectate = Instance.new("TextLabel")
titleSpectate.Size = UDim2.new(1,0,0,30)
titleSpectate.BackgroundColor3 = Color3.fromRGB(50,50,70)
titleSpectate.Text = "é¦ƒæ†—é””ï¿½ Espectear Jugadores"
titleSpectate.TextColor3 = Color3.fromRGB(255,255,255)  -- Letras blancas
titleSpectate.Font = Enum.Font.SourceSansBold
titleSpectate.TextSize = 20
titleSpectate.Parent = spectatePanel

local scrollingSpectate = Instance.new("ScrollingFrame")
scrollingSpectate.Size = UDim2.new(1,0,1,-30)
scrollingSpectate.Position = UDim2.new(0,0,0,30)
scrollingSpectate.CanvasSize = UDim2.new(0,0,0,0)
scrollingSpectate.BackgroundColor3 = Color3.fromRGB(35,35,50)
scrollingSpectate.ScrollBarThickness = 6
scrollingSpectate.Parent = spectatePanel

local spectateListLayout = Instance.new("UIListLayout", scrollingSpectate)
spectateListLayout.Padding = UDim.new(0,6)
spectateListLayout.SortOrder = Enum.SortOrder.Name

spectateListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    scrollingSpectate.CanvasSize = UDim2.new(0,0,0,spectateListLayout.AbsoluteContentSize.Y)
end)

local spectateButtons = {}
local currentlySpectating = nil

local function createSpectateButton(player)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1,0,0,30)
    button.BackgroundColor3 = Color3.fromRGB(40,40,70)
    button.TextColor3 = Color3.fromRGB(255,255,255)  -- Letras blancas
    button.Font = Enum.Font.Gotham
    button.TextSize = 14
    button.Text = player.DisplayName
    button.BorderSizePixel = 0
    button.Parent = scrollingSpectate

    button.MouseButton1Click:Connect(function()
        currentlySpectating = player
    end)

    spectateButtons[player] = button
end

local function removeSpectateButton(player)
    if spectateButtons[player] then
        spectateButtons[player]:Destroy()
        spectateButtons[player] = nil
    end
end

local function createReturnSpectateButton()
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1,0,0,30)
    button.BackgroundColor3 = Color3.fromRGB(70,50,50)
    button.TextColor3 = Color3.fromRGB(255,255,255)  
    button.Font = Enum.Font.GothamBold
    button.TextSize = 14
    button.Text = "Dejar De espectear"
    button.BorderSizePixel = 0
    button.Parent = scrollingSpectate

    button.MouseButton1Click:Connect(function()
        currentlySpectating = nil
        if LocalPlayer.Character then
            local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
            if hum then
                Camera.CameraSubject = hum
            end
        end
    end)
end

for _,player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        createSpectateButton(player)
    end
end
createReturnSpectateButton()

Players.PlayerAdded:Connect(function(player)
    if player ~= LocalPlayer then
        createSpectateButton(player)
    end
end)
Players.PlayerRemoving:Connect(removeSpectateButton)

RunService.RenderStepped:Connect(function()
    if currentlySpectating and currentlySpectating.Character then
        local hum = currentlySpectating.Character:FindFirstChild("Humanoid")
        if hum then
            Camera.CameraSubject = hum
        end
    end
end)


local function getHand()
    local char = Players.LocalPlayer.Character or Players.LocalPlayer.CharacterAdded:Wait()
    return char:FindFirstChild("RightHand") or char:FindFirstChild("Right Arm")
end

local function attackSelectedPlayers()
    local hand = getHand()
    if not hand or not hand:IsDescendantOf(workspace) then return end

    for _, target in pairs(Players:GetPlayers()) do
        if getgenv().AutoKillTargets[target.Name] and target.Character then
            local root = target.Character:FindFirstChild("HumanoidRootPart")
            if root and root:IsDescendantOf(workspace) then
                for _=1,2 do
                    firetouchinterest(hand,root,0)
                    task.wait(0.01)
                    firetouchinterest(hand,root,1)
                end
            end
        end
    end
end

task.spawn(function()
    while getgenv().AutoKillEnabled do
        pcall(attackSelectedPlayers)
        task.wait(0.08)
    end
end)

-- Variables para minimizar y drag
local minimized = false
local autoKillPanelWasVisible = false
local spectatePanelWasVisible = false

local dragging = false
local dragStart = Vector2.new()
local startPos = UDim2.new()
local dragThreshold = 5

local function isDraggingEnough(delta)
    return math.abs(delta.X) > dragThreshold or math.abs(delta.Y) > dragThreshold
end

titleButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

titleButton.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        if isDraggingEnough(delta) then
            mainFrame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end
end)

titleButton.MouseButton1Click:Connect(function()
	if not dragging then
		minimized = not minimized
		if minimized then
			autoKillPanelWasVisible = autoKillPanel.Visible
			spectatePanelWasVisible = spectatePanel.Visible

			TweenService:Create(mainFrame, TweenInfo.new(0.25), {Size = UDim2.new(0,150,0,30)}):Play()  -- Reducir el tamaÃ±o del menÃº
			buttonContainer.Visible = false
			autoKillPanel.Visible = false
			spectatePanel.Visible = false
		else
			TweenService:Create(mainFrame, TweenInfo.new(0.25), {Size = UDim2.new(0,650,0,400)}):Play()
			buttonContainer.Visible = true
			autoKillPanel.Visible = autoKillPanelWasVisible
			spectatePanel.Visible = spectatePanelWasVisible
		end
	end
end)


buttons["Auto Kill a jugadores"].MouseButton1Click:Connect(function()
    autoKillPanel.Visible = not autoKillPanel.Visible
    if autoKillPanel.Visible then
        spectatePanel.Visible = false
    end
end)

buttons["Espectear"].MouseButton1Click:Connect(function()
    spectatePanel.Visible = not spectatePanel.Visible
    if spectatePanel.Visible then
        autoKillPanel.Visible = false
    end
end)

buttons["Auto Reset"].MouseButton1Click:Connect(function()
    local url = "https://raw.githubusercontent.com/MainScriptMl/Auto-reinciar-personaje/refs/heads/main/Auto%20reinicio"
    local success, response = pcall(function()
        return game:HttpGet(url)
    end)
    if success and response then
        loadstring(response)()
    else
        warn("Error cargando script 'Activar pegar muerto'")
    end
end)

buttons["Activar pegar muerto"].MouseButton1Click:Connect(function()
    local url = "https://raw.githubusercontent.com/PheonixHub/Pegar-Muerto-Johan-/refs/heads/main/Johan"
    local success, response = pcall(function()
        return game:HttpGet(url)
    end)
    if success and response then
        loadstring(response)()
    else
        warn("Error cargando script 'Activar pegar muerto'")
    end
end)


buttons["Auto Kill a todos"].MouseButton1Click:Connect(function()
    local url = "https://raw.githubusercontent.com/PheonixHub/Auto-kill-all-/refs/heads/main/Auto%20kill%20para%20todos"
    local success, response = pcall(function()
        return game:HttpGet(url)
    end)
    if success and response then
        loadstring(response)()
    else
        warn("Error cargando script 'Auto Kill a todos'")
    end
end)
