local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RainbowUI"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = playerGui

local function createRainbowBorder(parent, size, position, anchorPoint, cornerRadius, innerColor, innerTransparency, borderThickness)
	local outer = Instance.new("Frame")
	outer.Size = size
	outer.Position = position
	outer.AnchorPoint = anchorPoint
	outer.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	outer.BackgroundTransparency = 0
	outer.BorderSizePixel = 0
	outer.Parent = parent

	local outerCorner = Instance.new("UICorner")
	outerCorner.CornerRadius = UDim.new(0, cornerRadius)
	outerCorner.Parent = outer

	local rainbowGradient = Instance.new("UIGradient")
	rainbowGradient.Color = ColorSequence.new({
		ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 0, 0)),
		ColorSequenceKeypoint.new(0.15, Color3.fromRGB(255, 165, 0)),
		ColorSequenceKeypoint.new(0.3, Color3.fromRGB(255, 255, 0)),
		ColorSequenceKeypoint.new(0.45, Color3.fromRGB(0, 255, 0)),
		ColorSequenceKeypoint.new(0.6, Color3.fromRGB(0, 255, 255)),
		ColorSequenceKeypoint.new(0.75, Color3.fromRGB(0, 0, 255)),
		ColorSequenceKeypoint.new(0.9, Color3.fromRGB(139, 0, 255)),
		ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 0))
	})
	rainbowGradient.Rotation = 0
	rainbowGradient.Parent = outer

	local inner = Instance.new("Frame")
	inner.Size = UDim2.new(1, -borderThickness * 2, 1, -borderThickness * 2)
	inner.Position = UDim2.new(0.5, 0, 0.5, 0)
	inner.AnchorPoint = Vector2.new(0.5, 0.5)
	inner.BackgroundColor3 = innerColor
	inner.BackgroundTransparency = innerTransparency
	inner.BorderSizePixel = 0
	inner.Parent = outer

	local innerCorner = Instance.new("UICorner")
	innerCorner.CornerRadius = UDim.new(0, cornerRadius - borderThickness)
	innerCorner.Parent = inner

	spawn(function()
		while rainbowGradient and rainbowGradient.Parent do
			TweenService:Create(rainbowGradient, TweenInfo.new(1.5, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, -1), {Rotation = 360}):Play()
			wait(1.5)
		end
	end)

	return inner, outer, rainbowGradient
end

local function spawnSnowflakes(parent, count)
	local snowContainer = Instance.new("Frame")
	snowContainer.Size = UDim2.fromScale(1, 1)
	snowContainer.Position = UDim2.fromScale(0, 0)
	snowContainer.BackgroundTransparency = 1
	snowContainer.BorderSizePixel = 0
	snowContainer.ZIndex = 0
	snowContainer.Active = false
	snowContainer.Parent = parent

	for i = 1, count do
		local flake = Instance.new("Frame")
		local size = math.random(4, 10)
		flake.Size = UDim2.fromOffset(size, size)
		flake.Position = UDim2.fromScale(math.random(), math.random())
		flake.AnchorPoint = Vector2.new(0.5, 0.5)
		flake.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
		flake.BackgroundTransparency = math.random(20, 60) / 100
		flake.BorderSizePixel = 0
		flake.ZIndex = 0
		flake.Active = false
		local corner = Instance.new("UICorner")
		corner.CornerRadius = UDim.new(1, 0)
		corner.Parent = flake
		flake.Parent = snowContainer

		spawn(function()
			local speed = math.random(30, 80)
			local startX = flake.Position.X.Scale
			local timeOffset = math.random() * math.pi * 2
			while flake and flake.Parent do
				local dt = 0.03
				wait(dt)
				local currentY = flake.Position.Y.Scale
				local newY = currentY + (speed * dt) / parent.AbsoluteSize.Y
				if newY > 1.1 then
					newY = -0.1
					startX = math.random()
				end
				local waveX = startX + math.sin(tick() * 2 + timeOffset) * 0.03
				waveX = math.clamp(waveX, 0.02, 0.98)
				flake.Position = UDim2.fromScale(waveX, newY)
			end
		end)
	end
end

local function createButton(parent, text, position, size, zIndex)
	local btn = Instance.new("TextButton")
	btn.Size = size or UDim2.fromOffset(80, 40)
	btn.Position = position
	btn.AnchorPoint = Vector2.new(0.5, 0.5)
	btn.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
	btn.BackgroundTransparency = 0.2
	btn.BorderSizePixel = 0
	btn.Text = text or ""
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.TextSize = 16
	btn.Font = Enum.Font.GothamBold
	btn.AutoButtonColor = false
	btn.ZIndex = zIndex or 10
	btn.Parent = parent

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(1, 0)
	corner.Parent = btn

	local stroke = Instance.new("UIStroke")
	stroke.Color = Color3.fromRGB(255, 255, 255)
	stroke.Transparency = 0.2
	stroke.Thickness = 2
	stroke.Parent = btn

	return btn
end

-- 悬浮球（真正的 TextButton）
local ballButton = Instance.new("TextButton")
ballButton.Size = UDim2.fromOffset(150, 40)
ballButton.Position = UDim2.new(0.5, 0, 0, 8)
ballButton.AnchorPoint = Vector2.new(0.5, 0)
ballButton.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
ballButton.BackgroundTransparency = 0.05
ballButton.BorderSizePixel = 0
ballButton.Text = ""
ballButton.AutoButtonColor = false
ballButton.ZIndex = 150
ballButton.Parent = screenGui

local ballCorner = Instance.new("UICorner")
ballCorner.CornerRadius = UDim.new(1, 0)
ballCorner.Parent = ballButton

local ballGradient = Instance.new("UIGradient")
ballGradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 0, 0)),
	ColorSequenceKeypoint.new(0.15, Color3.fromRGB(255, 165, 0)),
	ColorSequenceKeypoint.new(0.3, Color3.fromRGB(255, 255, 0)),
	ColorSequenceKeypoint.new(0.45, Color3.fromRGB(0, 255, 0)),
	ColorSequenceKeypoint.new(0.6, Color3.fromRGB(0, 255, 255)),
	ColorSequenceKeypoint.new(0.75, Color3.fromRGB(0, 0, 255)),
	ColorSequenceKeypoint.new(0.9, Color3.fromRGB(139, 0, 255)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 0))
})
ballGradient.Rotation = 0
ballGradient.Parent = ballButton

spawn(function()
	while ballGradient and ballGradient.Parent do
		TweenService:Create(ballGradient, TweenInfo.new(1.5, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, -1), {Rotation = 360}):Play()
		wait(1.5)
	end
end)

local ballLine = Instance.new("Frame")
ballLine.Size = UDim2.fromOffset(2, 18)
ballLine.Position = UDim2.fromScale(0.08, 0.5)
ballLine.AnchorPoint = Vector2.new(0.5, 0.5)
ballLine.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ballLine.BorderSizePixel = 0
ballLine.Parent = ballButton

local ballArrows = Instance.new("TextLabel")
ballArrows.Size = UDim2.fromScale(0.2, 1)
ballArrows.Position = UDim2.fromScale(0.12, 0)
ballArrows.BackgroundTransparency = 1
ballArrows.Text = "◈"
ballArrows.TextColor3 = Color3.fromRGB(255, 255, 255)
ballArrows.Font = Enum.Font.GothamBold
ballArrows.TextSize = 16
ballArrows.TextXAlignment = Enum.TextXAlignment.Center
ballArrows.Parent = ballButton

local ballLabel = Instance.new("TextLabel")
ballLabel.Size = UDim2.fromScale(0.6, 1)
ballLabel.Position = UDim2.fromScale(0.35, 0)
ballLabel.BackgroundTransparency = 1
ballLabel.Text = "通告脚本"
ballLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
ballLabel.Font = Enum.Font.GothamBold
ballLabel.TextSize = 14
ballLabel.TextXAlignment = Enum.TextXAlignment.Center
ballLabel.Parent = ballButton

-- 主界面（初始隐藏）
local mainInner, mainOuter, mainGradient = createRainbowBorder(screenGui, UDim2.fromScale(0.85, 0.6), UDim2.fromScale(0.5, 0.55), Vector2.new(0.5, 0.5), 24, Color3.fromRGB(30, 30, 35), 0.1, 4)
mainOuter.Visible = false
mainOuter.ZIndex = 100

spawnSnowflakes(mainInner, 40)

local shirokoImage = Instance.new("ImageLabel")
shirokoImage.Size = UDim2.fromOffset(100, 100)
shirokoImage.Position = UDim2.fromScale(0.12, 0.15)
shirokoImage.AnchorPoint = Vector2.new(0.5, 0.5)
shirokoImage.BackgroundTransparency = 1
shirokoImage.BorderSizePixel = 0
shirokoImage.Image = "rbxassetid://0"
shirokoImage.ZIndex = 1
shirokoImage.Parent = mainInner

local imageCorner = Instance.new("UICorner")
imageCorner.CornerRadius = UDim.new(0, 12)
imageCorner.Parent = shirokoImage

local imageStroke = Instance.new("UIStroke")
imageStroke.Color = Color3.fromRGB(255, 255, 255)
imageStroke.Transparency = 0.3
imageStroke.Thickness = 2
imageStroke.Parent = shirokoImage

local btn1 = createButton(mainInner, "", UDim2.fromScale(0.3, 0.8), UDim2.fromScale(0.25, 0.18), 5)
local btn2 = createButton(mainInner, "", UDim2.fromScale(0.7, 0.8), UDim2.fromScale(0.25, 0.18), 5)

local closeButton = createButton(mainInner, "✕", UDim2.new(1, -25, 0, 25), UDim2.fromOffset(40, 40), 101)

-- 确认框
local confirmFrame, confirmOuter, confirmGradient = createRainbowBorder(screenGui, UDim2.fromOffset(240, 120), UDim2.fromScale(0.5, 0.5), Vector2.new(0.5, 0.5), 16, Color3.fromRGB(35, 35, 40), 0.05, 3)
confirmOuter.Visible = false
confirmOuter.ZIndex = 200

local confirmText = Instance.new("TextLabel")
confirmText.Size = UDim2.fromScale(1, 0.5)
confirmText.Position = UDim2.fromScale(0, 0.1)
confirmText.BackgroundTransparency = 1
confirmText.Text = "是否关闭脚本？"
confirmText.TextColor3 = Color3.fromRGB(255, 255, 255)
confirmText.Font = Enum.Font.GothamBold
confirmText.TextSize = 16
confirmText.Parent = confirmFrame

local confirmButtonClose = createButton(confirmFrame, "关闭", UDim2.fromScale(0.25, 0.65), UDim2.fromOffset(80, 34), 210)
local confirmButtonCancel = createButton(confirmFrame, "取消", UDim2.fromScale(0.75, 0.65), UDim2.fromOffset(80, 34), 210)

-- 悬浮球点击切换主界面
ballButton.Activated:Connect(function()
	mainOuter.Visible = not mainOuter.Visible
end)

-- 按钮事件
btn1.Activated:Connect(function()
	print("Button 1 clicked")
end)
btn2.Activated:Connect(function()
	print("Button 2 clicked")
end)

closeButton.Activated:Connect(function()
	confirmOuter.Visible = true
end)

confirmButtonCancel.Activated:Connect(function()
	confirmOuter.Visible = false
end)

confirmButtonClose.Activated:Connect(function()
	screenGui:Destroy()
end)