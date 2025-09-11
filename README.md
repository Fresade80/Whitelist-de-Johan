--[[
    Scrips JX7 Public
    A comprehensive utility script with various functionalities.
    Credits: By Johan
]]

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local HttpService = game:GetService("HttpService")
local StarterPack = game:GetService("StarterPack")
local Workspace = game:GetService("Workspace")

-- Player
local LocalPlayer = Players.LocalPlayer

-- Global variables to manage state
getgenv().autoWinBrawl = false
getgenv().autoJoinBrawl = false
getgenv().workingGym = false
getgenv().posLock = nil

-- UI elements
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "JX7_Public_GUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 450, 0, 550)
mainFrame.Position = UDim2.new(0.5, -225, 0.5, -275)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.BackgroundColor3 = Color3.fromRGB(75, 0, 130)
titleBar.Parent = mainFrame

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, 0, 1, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "Scrips JX7 Public"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextSize = 20
titleLabel.Parent = titleBar

-- Minimizer button
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 30, 1, 0)
minimizeButton.Position = UDim2.new(1, -30, 0, 0)
minimizeButton.BackgroundColor3 = Color3.fromRGB(75, 0, 130)
minimizeButton.Text = "_"
minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.Font = Enum.Font.SourceSansBold
minimizeButton.TextSize = 20
minimizeButton.Parent = titleBar

-- Tab management
local tabContainer = Instance.new("Frame")
tabContainer.Size = UDim2.new(0, 100, 1, -30)
tabContainer.Position = UDim2.new(0, 0, 0, 30)
tabContainer.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
tabContainer.Parent = mainFrame

local contentContainer = Instance.new("Frame")
contentContainer.Size = UDim2.new(1, -100, 1, -30)
contentContainer.Position = UDim2.new(0, 100, 0, 30)
contentContainer.BackgroundTransparency = 1
contentContainer.Parent = mainFrame

local isMinimized = false
minimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        mainFrame.Size = UDim2.new(0, 450, 0, 30)
        tabContainer.Visible = false
        contentContainer.Visible = false
        minimizeButton.Text = "+"
    else
        mainFrame.Size = UDim2.new(0, 450, 0, 550)
        tabContainer.Visible = true
        contentContainer.Visible = true
        minimizeButton.Text = "_"
    end
end)

local tabs = {}
local currentTab = "Main"

local function showTab(tabName)
    for name, content in pairs(tabs) do
        content.Visible = (name == tabName)
    end
end

local function createTabButton(name, yPos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 30)
    btn.Position = UDim2.new(0, 0, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 14
    btn.Parent = tabContainer
    btn.MouseButton1Click:Connect(function()
        currentTab = name
        showTab(name)
    end)
end

local function createContentFrame(name)
    local contentFrame = Instance.new("ScrollingFrame")
    contentFrame.Name = name .. "Content"
    contentFrame.Size = UDim2.new(1, 0, 1, 0)
    contentFrame.BackgroundTransparency = 1
    contentFrame.CanvasSize = UDim2.new(0, 0, 2, 0) -- Extend canvas to fit content
    contentFrame.ScrollBarThickness = 6
    contentFrame.Visible = false
    contentFrame.Parent = contentContainer
    
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 10)
    listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    listLayout.VerticalAlignment = Enum.VerticalAlignment.Top
    listLayout.Parent = contentFrame
    
    return contentFrame
end

local function createFolder(parentFrame, name)
    local folderFrame = Instance.new("Frame")
    folderFrame.Size = UDim2.new(1, -20, 0, 20)
    folderFrame.Position = UDim2.new(0, 10, 0, 0)
    folderFrame.BackgroundTransparency = 1
    folderFrame.Parent = parentFrame
    
    local folderLabel = Instance.new("TextLabel")
    folderLabel.Size = UDim2.new(1, 0, 1, 0)
    folderLabel.BackgroundTransparency = 1
    folderLabel.Text = "  " .. name
    folderLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
    folderLabel.Font = Enum.Font.SourceSansBold
    folderLabel.TextSize = 16
    folderLabel.TextXAlignment = Enum.TextXAlignment.Left
    folderLabel.Parent = folderFrame
    
    local divider = Instance.new("Frame")
    divider.Size = UDim2.new(1, 0, 0, 1)
    divider.Position = UDim2.new(0, 0, 1, 0)
    divider.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    divider.Parent = folderFrame

    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, 0, 0, 0)
    contentFrame.Position = UDim2.new(0, 0, 1, 15)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = parentFrame
    
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 10)
    listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
    listLayout.VerticalAlignment = Enum.VerticalAlignment.Top
    listLayout.Parent = contentFrame
    
    return contentFrame
end

local function createSwitch(parentFrame, name, callback)
    local switchFrame = Instance.new("Frame")
    switchFrame.Size = UDim2.new(1, -20, 0, 30)
    switchFrame.Position = UDim2.new(0, 10, 0, 0)
    switchFrame.BackgroundTransparency = 1
    switchFrame.Parent = parentFrame
    
    local switchLabel = Instance.new("TextLabel")
    switchLabel.Size = UDim2.new(0, 120, 1, 0)
    switchLabel.BackgroundTransparency = 1
    switchLabel.Text = name
    switchLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    switchLabel.Font = Enum.Font.Gotham
    switchLabel.TextSize = 14
    switchLabel.TextXAlignment = Enum.TextXAlignment.Left
    switchLabel.Parent = switchFrame
    
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 50, 0, 20)
    button.Position = UDim2.new(1, -50, 0, 5)
    button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    button.Text = "OFF"
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Font = Enum.Font.GothamBold
    button.TextSize = 12
    button.Parent = switchFrame

    local active = false
    button.MouseButton1Click:Connect(function()
        active = not active
        button.BackgroundColor3 = active and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(50, 50, 50)
        button.Text = active and "ON" or "OFF"
        callback(active)
    end)
end

local function createButton(parentFrame, name, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 30)
    btn.Position = UDim2.new(0, 10, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 14
    btn.Parent = parentFrame
    btn.MouseButton1Click:Connect(callback)
end

-- Create tabs and their content frames
local tabNames = {"Main", "Farm", "Misc", "Pets", "Stats", "Calculator", "Killer", "Teleport", "Credits"}
local yOffset = 0
for _, name in ipairs(tabNames) do
    createTabButton(name, yOffset)
    tabs[name] = createContentFrame(name)
    yOffset = yOffset + 30
end

-- ============================================================================
-- MAIN TAB CONTENT
-- ============================================================================

local mainTabContent = tabs["Main"]

-- Auto Brawls Folder
local brawlFolder = createFolder(mainTabContent, "Auto Brawls")

local godModeToggle = false
createSwitch(brawlFolder, "God Mode (Brawl)", function(state)
    godModeToggle = state
    if state then
        task.spawn(function()
            while godModeToggle do
                if ReplicatedStorage.rEvents.brawlEvent then
                    ReplicatedStorage.rEvents.brawlEvent:FireServer("joinBrawl")
                end
                task.wait(0.1)
            end
        end)
    end
end)

local function equipPunch()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    if not character:FindFirstChild("Punch") then
        local punchTool = LocalPlayer.Backpack:FindFirstChild("Punch")
        if punchTool then
            punchTool.Parent = character
        end
    end
end

createSwitch(brawlFolder, "Auto Win Brawls", function(state)
    getgenv().autoWinBrawl = state
    if state then
        task.spawn(function()
            while getgenv().autoWinBrawl do
                pcall(function()
                    if LocalPlayer.PlayerGui.gameGui.brawlJoinLabel.Visible then
                        ReplicatedStorage.rEvents.brawlEvent:FireServer("joinBrawl")
                        LocalPlayer.PlayerGui.gameGui.brawlJoinLabel.Visible = false
                    end
                    equipPunch()
                    if ReplicatedStorage.brawlInProgress.Value then
                        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
                            LocalPlayer.Character.Humanoid.Jump = true
                        end
                        ReplicatedStorage.rEvents.muscleEvent:FireServer("punch", "rightHand")
                        ReplicatedStorage.rEvents.muscleEvent:FireServer("punch", "leftHand")
                    end
                end)
                task.wait(0.2)
            end
        end)
    end
end)

createSwitch(brawlFolder, "Auto Join Brawls", function(state)
    getgenv().autoJoinBrawl = state
    if state then
        task.spawn(function()
            while getgenv().autoJoinBrawl and task.wait(0.5) do
                pcall(function()
                    if LocalPlayer.PlayerGui.gameGui.brawlJoinLabel.Visible then
                        ReplicatedStorage.rEvents.brawlEvent:FireServer("joinBrawl")
                        LocalPlayer.PlayerGui.gameGui.brawlJoinLabel.Visible = false
                    end
                end)
            end
        end)
    end
end)

-- Jungle Gym Folder
local jungleGymFolder = createFolder(mainTabContent, "Jungle Gym")

local function pressE()
    UserInputService:SimulateKeyPress(Enum.KeyCode.E)
end

local function autoLift()
    while getgenv().workingGym do
        if ReplicatedStorage.rEvents.muscleEvent then
            ReplicatedStorage.rEvents.muscleEvent:FireServer("rep")
        end
        task.wait(0.1)
    end
end

local function teleportAndStart(machineName, position)
    local character = LocalPlayer.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        character.HumanoidRootPart.CFrame = position
        task.wait(0.5)
        pressE()
        task.wait(1)
        task.spawn(autoLift)
    end
end

createSwitch(jungleGymFolder, "Jungle Bench Press", function(bool)
    getgenv().workingGym = bool
    if bool then
        teleportAndStart("Bench Press", CFrame.new(-8173, 64, 1898))
    end
end)

createSwitch(jungleGymFolder, "Jungle Squat", function(bool)
    getgenv().workingGym = bool
    if bool then
        teleportAndStart("Squat", CFrame.new(-8352, 34, 2878))
    end
end)

createSwitch(jungleGymFolder, "Jungle Pull Ups", function(bool)
    getgenv().workingGym = bool
    if bool then
        teleportAndStart("Pull Up", CFrame.new(-8666, 34, 2070))
    end
end)

createSwitch(jungleGymFolder, "Jungle Boulder", function(bool)
    getgenv().workingGym = bool
    if bool then
        teleportAndStart("Boulder", CFrame.new(-8621, 34, 2684))
    end
end)

-- All Gyms Folder
local farmGymsFolder = createFolder(mainTabContent, "All Gyms")

local workoutPositions = {
    ["Bench Press"] = {
        ["Eternal Gym"] = CFrame.new(-7176.19141, 45.394104, -1106.31421),
        ["Legend Gym"] = CFrame.new(4111.91748, 1020.46674, -3799.97217),
        ["Muscle King Gym"] = CFrame.new(-8590.06152, 46.0167427, -6043.34717)
    },
    ["Squat"] = {
        ["Eternal Gym"] = CFrame.new(-7176.19141, 45.394104, -1106.31421),
        ["Legend Gym"] = CFrame.new(4304.99023, 987.829956, -4124.2334),
        ["Muscle King Gym"] = CFrame.new(-8940.12402, 13.1642084, -5699.13477)
    },
    ["Deadlift"] = {
        ["Eternal Gym"] = CFrame.new(-7176.19141, 45.394104, -1106.31421),
        ["Legend Gym"] = CFrame.new(4304.99023, 987.829956, -4124.2334),
        ["Muscle King Gym"] = CFrame.new(-8940.12402, 13.1642084, -5699.13477)
    },
    ["Pull Up"] = {
        ["Eternal Gym"] = CFrame.new(-7176.19141, 45.394104, -1106.31421),
        ["Legend Gym"] = CFrame.new(4304.99023, 987.829956, -4124.2334),
        ["Muscle King Gym"] = CFrame.new(-8940.12402, 13.1642084, -5699.13477)
    }
}
local workoutTypes = {"Bench Press", "Squat", "Deadlift", "Pull Up"}
local gymLocations = {"Eternal Gym", "Legend Gym", "Muscle King Gym"}

for _, workoutType in ipairs(workoutTypes) do
    local dropdown = createSwitch(farmGymsFolder, workoutType .. " - Gym", function(state)
        -- Placeholder for dropdown logic
        print("Dropdown for " .. workoutType .. " clicked, but functionality is not implemented.")
    end)
end

-- Auto Snack Folder
local autoSnackFolder = createFolder(mainTabContent, "Auto Snacks")

local snackConnections = {}
local function equipAndUse(itemName)
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local backpack = LocalPlayer.Backpack

    local tool = backpack:FindFirstChild(itemName)
    if tool then
        tool.Parent = character
        task.wait(0.1)
        local equippedTool = character:FindFirstChild(itemName)
        if equippedTool and equippedTool:IsA("Tool") then
            equippedTool:Activate()
        end
    end
end

local function startSnackLoop(itemName, waitTime)
    if snackConnections[itemName] then task.cancel(snackConnections[itemName]) end
    snackConnections[itemName] = task.spawn(function()
        while true do
            equipAndUse(itemName)
            task.wait(waitTime)
        end
    end)
end

createSwitch(autoSnackFolder, "Protein Shake", function(state)
    if state then startSnackLoop("Protein Shake", 0.1) else task.cancel(snackConnections["Protein Shake"]) end
end)
createSwitch(autoSnackFolder, "Energy Shake", function(state)
    if state then startSnackLoop("Energy Shake", 0.1) else task.cancel(snackConnections["Energy Shake"]) end
end)
createSwitch(autoSnackFolder, "Tough Bar", function(state)
    if state then startSnackLoop("Tough Bar", 0.1) else task.cancel(snackConnections["Tough Bar"]) end
end)
createSwitch(autoSnackFolder, "Ultra Shake", function(state)
    if state then startSnackLoop("Ultra Shake", 0.1) else task.cancel(snackConnections["Ultra Shake"]) end
end)
createSwitch(autoSnackFolder, "Energy Bar", function(state)
    if state then startSnackLoop("Energy Bar", 0.1) else task.cancel(snackConnections["Energy Bar"]) end
end)
createSwitch(autoSnackFolder, "Protein Egg 30 Minuts", function(state)
    if state then startSnackLoop("Protein Egg", 1800) else task.cancel(snackConnections["Protein Egg"]) end
end)
createSwitch(autoSnackFolder, "Tropical Shake 15 Minuts", function(state)
    if state then startSnackLoop("Tropical Shake", 900) else task.cancel(snackConnections["Tropical Shake"]) end
end)

-- Extras Folder
local extrasFolder = createFolder(mainTabContent, "Extras")

createSwitch(extrasFolder, "Lock Position", function(state)
    if state then
        local currentPos = LocalPlayer.Character.HumanoidRootPart.CFrame
        getgenv().posLock = RunService.Heartbeat:Connect(function()
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                LocalPlayer.Character.HumanoidRootPart.CFrame = currentPos
            end
        end)
    else
        if getgenv().posLock then
            getgenv().posLock:Disconnect()
            getgenv().posLock = nil
        end
    end
end)

createSwitch(extrasFolder, "Anti Knockback", function(state)
    if state then
        local rootPart = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if rootPart then
            local bv = Instance.new("BodyVelocity")
            bv.MaxForce = Vector3.new(1, 0, 1) * math.huge
            bv.Velocity = Vector3.new(0, 0, 0)
            bv.Parent = rootPart
        end
    else
        local rootPart = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if rootPart then
            local bv = rootPart:FindFirstChildOfClass("BodyVelocity")
            if bv then bv:Destroy() end
        end
    end
end)

createButton(extrasFolder, "Anti AFK", function()
    local success, result = pcall(function()
        for i, v in pairs(getconnections(LocalPlayer.Idled)) do
            v:Disable()
        end
    end)
    
    if not success then
        local lastInput = tick()
        LocalPlayer.Idled:Connect(function()
            if tick() - lastInput > 20 then
                VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.W, false, game)
                VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.W, false, game)
                lastInput = tick()
            end
        end)
    end
end)


-- ============================================================================
-- FARM TAB
-- ============================================================================
local farmTabContent = tabs["Farm"]
local farmFolder = createFolder(farmTabContent, "Auto Farming")

local autoFarmActive = false
createSwitch(farmFolder, "Auto Farm", function(state)
    autoFarmActive = state
    if state then
        task.spawn(function()
            while autoFarmActive and task.wait(0.5) do
                local closestPart = nil
                local closestDistance = math.huge
                for _, part in pairs(Workspace:GetChildren()) do
                    if part.Name:lower():match("boulder") or part.Name:lower():match("crystal") then
                        local distance = (LocalPlayer.Character.HumanoidRootPart.Position - part.Position).Magnitude
                        if distance < closestDistance then
                            closestDistance = distance
                            closestPart = part
                        end
                    end
                end
                if closestPart then
                    LocalPlayer.Character.HumanoidRootPart.CFrame = closestPart.CFrame
                    pressE()
                end
            end
        end)
    end
end)

createButton(farmFolder, "Auto Sell", function()
    local sellPoint = Workspace:FindFirstChild("SellPart") or Workspace:FindFirstChild("Shop")
    if sellPoint and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = sellPoint.CFrame
        task.wait(1)
        pressE()
    end
end)


-- ============================================================================
-- MISC TAB
-- ============================================================================
local miscTabContent = tabs["Misc"]
local miscFolder = createFolder(miscTabContent, "Miscellaneous Functions")

local walkspeedSlider = Instance.new("Frame")
walkspeedSlider.Size = UDim2.new(1, -20, 0, 30)
walkspeedSlider.Position = UDim2.new(0, 1
