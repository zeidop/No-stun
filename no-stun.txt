local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- ==================== VALORES ====================
local enabled = false
local jumping = true
local jumpPower = 50          -- Valor por defecto de Roblox
local dashForce = 60          -- Valor inicial del dash
local minimized = false

local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:FindFirstChildOfClass("Humanoid")
local root = character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("Torso")

player.CharacterAdded:Connect(function(c)
	character = c
	humanoid = c:WaitForChild("Humanoid")
	root = c:WaitForChild("HumanoidRootPart", 3) or c:FindFirstChild("Torso")
	jumping = true
end)

-- ==================== FUNCIÓN DE SALTO (Anti-Stun) ====================
local function jump()
	if not humanoid or not root or not jumping then return end
	jumping = false
	humanoid.PlatformStand = true
	root.Velocity = Vector3.zero
	task.wait()
	root.Velocity = Vector3.new(0, jumpPower, 0)
	humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
	task.wait(0.05)
	humanoid.PlatformStand = false
	task.wait(1.2)
	jumping = true
end

-- ==================== FUNCIÓN DE DASH ====================
local function dash()
	if not humanoid or not root then return end
	local lookVector = root.CFrame.LookVector
	root.Velocity = Vector3.new(lookVector.X * dashForce, root.Velocity.Y, lookVector.Z * dashForce)
end

-- ==================== GUI ====================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AntiStunMobile"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = playerGui

-- Frame principal (expandido)
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 160, 0, 195)
mainFrame.Position = UDim2.new(0.03, 0, 0.35, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 12)
mainCorner.Parent = mainFrame

local mainStroke = Instance.new("UIStroke")
mainStroke.Color = Color3.fromRGB(60, 60, 70)
mainStroke.Thickness = 1.2
mainStroke.Parent = mainFrame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -30, 0, 28)
title.Position = UDim2.new(0, 8, 0, 4)
title.BackgroundTransparency = 1
title.Text = "Anti-Stun"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextSize = 15
title.Font = Enum.Font.GothamBold
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = mainFrame

-- Botón Minimizar
local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 24, 0, 24)
minBtn.Position = UDim2.new(1, -28, 0, 4)
minBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
minBtn.Text = "–"
minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minBtn.TextSize = 18
minBtn.Font = Enum.Font.GothamBold
minBtn.BorderSizePixel = 0
minBtn.Parent = mainFrame

local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0, 6)
minCorner.Parent = minBtn

-- Botón Toggle
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(1, -16, 0, 34)
toggleBtn.Position = UDim2.new(0, 8, 0, 36)
toggleBtn.BackgroundColor3 = Color3.fromRGB(200, 55, 55)
toggleBtn.Text = "OFF"
toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleBtn.TextSize = 14
toggleBtn.Font = Enum.Font.GothamBold
toggleBtn.BorderSizePixel = 0
toggleBtn.Parent = mainFrame

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 8)
toggleCorner.Parent = toggleBtn

-- ===== Jump Power =====
local jumpLabel = Instance.new("TextLabel")
jumpLabel.Size = UDim2.new(1, -16, 0, 18)
jumpLabel.Position = UDim2.new(0, 8, 0, 78)
jumpLabel.BackgroundTransparency = 1
jumpLabel.Text = "Jump Power: 50"
jumpLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
jumpLabel.TextSize = 12
jumpLabel.Font = Enum.Font.Gotham
jumpLabel.TextXAlignment = Enum.TextXAlignment.Left
jumpLabel.Parent = mainFrame

local jumpMinus = Instance.new("TextButton")
jumpMinus.Size = UDim2.new(0, 32, 0, 26)
jumpMinus.Position = UDim2.new(0, 8, 0, 98)
jumpMinus.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
jumpMinus.Text = "-"
jumpMinus.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpMinus.TextSize = 16
jumpMinus.Font = Enum.Font.GothamBold
jumpMinus.BorderSizePixel = 0
jumpMinus.Parent = mainFrame

local jumpPlus = Instance.new("TextButton")
jumpPlus.Size = UDim2.new(0, 32, 0, 26)
jumpPlus.Position = UDim2.new(0, 48, 0, 98)
jumpPlus.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
jumpPlus.Text = "+"
jumpPlus.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpPlus.TextSize = 16
jumpPlus.Font = Enum.Font.GothamBold
jumpPlus.BorderSizePixel = 0
jumpPlus.Parent = mainFrame

local jumpCorner1 = Instance.new("UICorner")
jumpCorner1.CornerRadius = UDim.new(0, 6)
jumpCorner1.Parent = jumpMinus
local jumpCorner2 = Instance.new("UICorner")
jumpCorner2.CornerRadius = UDim.new(0, 6)
jumpCorner2.Parent = jumpPlus

-- ===== Dash Force =====
local dashLabel = Instance.new("TextLabel")
dashLabel.Size = UDim2.new(1, -16, 0, 18)
dashLabel.Position = UDim2.new(0, 8, 0, 132)
dashLabel.BackgroundTransparency = 1
dashLabel.Text = "Dash Force: 60"
dashLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
dashLabel.TextSize = 12
dashLabel.Font = Enum.Font.Gotham
dashLabel.TextXAlignment = Enum.TextXAlignment.Left
dashLabel.Parent = mainFrame

local dashMinus = Instance.new("TextButton")
dashMinus.Size = UDim2.new(0, 32, 0, 26)
dashMinus.Position = UDim2.new(0, 8, 0, 152)
dashMinus.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
dashMinus.Text = "-"
dashMinus.TextColor3 = Color3.fromRGB(255, 255, 255)
dashMinus.TextSize = 16
dashMinus.Font = Enum.Font.GothamBold
dashMinus.BorderSizePixel = 0
dashMinus.Parent = mainFrame

local dashPlus = Instance.new("TextButton")
dashPlus.Size = UDim2.new(0, 32, 0, 26)
dashPlus.Position = UDim2.new(0, 48, 0, 152)
dashPlus.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
dashPlus.Text = "+"
dashPlus.TextColor3 = Color3.fromRGB(255, 255, 255)
dashPlus.TextSize = 16
dashPlus.Font = Enum.Font.GothamBold
dashPlus.BorderSizePixel = 0
dashPlus.Parent = mainFrame

local dashCorner1 = Instance.new("UICorner")
dashCorner1.CornerRadius = UDim.new(0, 6)
dashCorner1.Parent = dashMinus
local dashCorner2 = Instance.new("UICorner")
dashCorner2.CornerRadius = UDim.new(0, 6)
dashCorner2.Parent = dashPlus

-- Botón Dash manual
local dashBtn = Instance.new("TextButton")
dashBtn.Size = UDim2.new(0, 64, 0, 26)
dashBtn.Position = UDim2.new(0, 88, 0, 152)
dashBtn.BackgroundColor3 = Color3.fromRGB(70, 130, 220)
dashBtn.Text = "DASH"
dashBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
dashBtn.TextSize = 12
dashBtn.Font = Enum.Font.GothamBold
dashBtn.BorderSizePixel = 0
dashBtn.Parent = mainFrame

local dashBtnCorner = Instance.new("UICorner")
dashBtnCorner.CornerRadius = UDim.new(0, 6)
dashBtnCorner.Parent = dashBtn

-- ==================== BOTÓN MINIMIZADO ====================
local miniBtn = Instance.new("TextButton")
miniBtn.Name = "MiniButton"
miniBtn.Size = UDim2.new(0, 48, 0, 48)
miniBtn.Position = UDim2.new(0.03, 0, 0.35, 0)
miniBtn.BackgroundColor3 = Color3.fromRGB(200, 55, 55)
miniBtn.Text = "AS"
miniBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
miniBtn.TextSize = 16
miniBtn.Font = Enum.Font.GothamBold
miniBtn.BorderSizePixel = 0
miniBtn.Visible = false
miniBtn.Parent = screenGui

local miniCorner = Instance.new("UICorner")
miniCorner.CornerRadius = UDim.new(1, 0) -- Circular
miniCorner.Parent = miniBtn

local miniStroke = Instance.new("UIStroke")
miniStroke.Color = Color3.fromRGB(0, 0, 0)
miniStroke.Thickness = 1.5
miniStroke.Transparency = 0.3
miniStroke.Parent = miniBtn

-- ==================== ARRASTRE ====================
local function makeDraggable(guiObject)
	local dragging = false
	local dragStart, startPos

	guiObject.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = guiObject.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)

	UserInputService.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			local delta = input.Position - dragStart
			guiObject.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
end

makeDraggable(mainFrame)
makeDraggable(miniBtn)

-- ==================== LÓGICA ====================
local function updateToggleVisual()
	if enabled then
		toggleBtn.BackgroundColor3 = Color3.fromRGB(50, 180, 80)
		toggleBtn.Text = "ON"
		miniBtn.BackgroundColor3 = Color3.fromRGB(50, 180, 80)
	else
		toggleBtn.BackgroundColor3 = Color3.fromRGB(200, 55, 55)
		toggleBtn.Text = "OFF"
		miniBtn.BackgroundColor3 = Color3.fromRGB(200, 55, 55)
	end
end

local function setEnabled(state)
	enabled = state
	jumping = true
	updateToggleVisual()
end

toggleBtn.MouseButton1Click:Connect(function()
	setEnabled(not enabled)
end)

minBtn.MouseButton1Click:Connect(function()
	minimized = true
	mainFrame.Visible = false
	miniBtn.Visible = true
	miniBtn.Position = mainFrame.Position
end)

miniBtn.MouseButton1Click:Connect(function()
	minimized = false
	miniBtn.Visible = false
	mainFrame.Visible = true
	mainFrame.Position = miniBtn.Position
end)

-- Controles de potencia
jumpMinus.MouseButton1Click:Connect(function()
	jumpPower = math.max(10, jumpPower - 5)
	jumpLabel.Text = "Jump Power: " .. jumpPower
end)

jumpPlus.MouseButton1Click:Connect(function()
	jumpPower = math.min(150, jumpPower + 5)
	jumpLabel.Text = "Jump Power: " .. jumpPower
end)

dashMinus.MouseButton1Click:Connect(function()
	dashForce = math.max(10, dashForce - 5)
	dashLabel.Text = "Dash Force: " .. dashForce
end)

dashPlus.MouseButton1Click:Connect(function()
	dashForce = math.min(150, dashForce + 5)
	dashLabel.Text = "Dash Force: " .. dashForce
end)

dashBtn.MouseButton1Click:Connect(function()
	dash()
end)

-- Controles de teclado
UserInputService.InputBegan:Connect(function(input, processed)
	if processed then return end
	if input.KeyCode == Enum.KeyCode.X then
		setEnabled(not enabled)
	elseif input.KeyCode == Enum.KeyCode.Z then
		setEnabled(false)
	elseif input.KeyCode == Enum.KeyCode.Space and enabled then
		jump()
	elseif input.KeyCode == Enum.KeyCode.Q then
		dash()
	end
end)

-- Detección de stun
RunService.Heartbeat:Connect(function()
	if not enabled or not humanoid or not jumping then return end
	local state = humanoid:GetState()
	if state == Enum.HumanoidStateType.FallingDown
		or state == Enum.HumanoidStateType.GettingUp
		or state == Enum.HumanoidStateType.Stunned then
		jump()
	end
end)

print("Anti-Stun Mobile + Dash cargado")
