-- Создаем интерфейс
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TeleportGUI"
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Основной фрейм
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0.15, 0, 0.1, 0)
frame.Position = UDim2.new(0.83, 0, 0.03, 0) -- Правый верхний угол
frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
frame.BorderSizePixel = 0
frame.Parent = screenGui

-- Заголовок
local title = Instance.new("TextLabel")
title.Text = "Телепорт"
title.Size = UDim2.new(1, 0, 0.3, 0)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 18
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundTransparency = 1
title.Parent = frame

-- Кнопка телепортации
local teleportButton = Instance.new("TextButton")
teleportButton.Text = "На высоту 50"
teleportButton.Size = UDim2.new(0.9, 0, 0.3, 0)
teleportButton.Position = UDim2.new(0.05, 0, 0.35, 0)
teleportButton.Font = Enum.Font.SourceSans
teleportButton.TextSize = 14
teleportButton.TextColor3 = Color3.fromRGB(255, 255, 255)
teleportButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
teleportButton.Parent = frame

-- Переключатель
local toggleButton = Instance.new("TextButton")
toggleButton.Text = "ВКЛ"
toggleButton.Size = UDim2.new(0.9, 0, 0.3, 0)
toggleButton.Position = UDim2.new(0.05, 0, 0.7, 0)
toggleButton.Font = Enum.Font.SourceSans
toggleButton.TextSize = 14
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.BackgroundColor3 = Color3.fromRGB(0, 120, 0)
toggleButton.Parent = frame

-- Состояние скрипта
local enabled = true

-- Создаем постоянную платформу
local platform = nil
local platformWalls = {}
local platformSize = Vector3.new(30, 1, 30) -- Увеличили размер платформы

-- Функция создания/обновления платформы и стен
local function createPlatformSystem(position)
	-- Удаляем старые стены если есть
	for _, wall in pairs(platformWalls) do
		wall:Destroy()
	end
	platformWalls = {}

	-- Создаем/обновляем основную платформу
	if not platform or not platform.Parent then
		platform = Instance.new("Part")
		platform.Name = "TeleportPlatform"
		platform.Size = platformSize
		platform.Anchored = true
		platform.CanCollide = true
		platform.Material = Enum.Material.Concrete
		platform.Color = Color3.fromRGB(120, 120, 120)
		platform.Parent = workspace
	end

	platform.Position = position + Vector3.new(0, -3, 0)

	-- Создаем стены по краям платформы
	local wallHeight = 10
	local wallThickness = 1

	-- Северная стена
	local northWall = Instance.new("Part")
	northWall.Size = Vector3.new(platformSize.X + 2*wallThickness, wallHeight, wallThickness)
	northWall.Position = platform.Position + Vector3.new(0, wallHeight/2, platformSize.Z/2)
	northWall.Anchored = true
	northWall.CanCollide = true
	northWall.Transparency = 0.5
	northWall.Color = Color3.fromRGB(200, 200, 200)
	northWall.Parent = workspace
	table.insert(platformWalls, northWall)

	-- Южная стена
	local southWall = Instance.new("Part")
	southWall.Size = Vector3.new(platformSize.X + 2*wallThickness, wallHeight, wallThickness)
	southWall.Position = platform.Position + Vector3.new(0, wallHeight/2, -platformSize.Z/2)
	southWall.Anchored = true
	southWall.CanCollide = true
	southWall.Transparency = 0.5
	southWall.Color = Color3.fromRGB(200, 200, 200)
	southWall.Parent = workspace
	table.insert(platformWalls, southWall)

	-- Восточная стена
	local eastWall = Instance.new("Part")
	eastWall.Size = Vector3.new(wallThickness, wallHeight, platformSize.Z)
	eastWall.Position = platform.Position + Vector3.new(platformSize.X/2, wallHeight/2, 0)
	eastWall.Anchored = true
	eastWall.CanCollide = true
	eastWall.Transparency = 0.5
	eastWall.Color = Color3.fromRGB(200, 200, 200)
	eastWall.Parent = workspace
	table.insert(platformWalls, eastWall)

	-- Западная стена
	local westWall = Instance.new("Part")
	westWall.Size = Vector3.new(wallThickness, wallHeight, platformSize.Z)
	westWall.Position = platform.Position + Vector3.new(-platformSize.X/2, wallHeight/2, 0)
	westWall.Anchored = true
	westWall.CanCollide = true
	westWall.Transparency = 0.5
	westWall.Color = Color3.fromRGB(200, 200, 200)
	westWall.Parent = workspace
	table.insert(platformWalls, westWall)
end

-- Функция телепортации
local function teleportPlayer()
	if not enabled then return end

	local player = game.Players.LocalPlayer
	local character = player.Character or player.CharacterAdded:Wait()
	local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

	-- Новая позиция (те же X и Z, Y = 50)
	local newPosition = Vector3.new(
		humanoidRootPart.Position.X,
		50,
		humanoidRootPart.Position.Z
	)

	-- Телепортация
	humanoidRootPart.CFrame = CFrame.new(newPosition)

	-- Создаем/обновляем платформу и стены
	createPlatformSystem(newPosition)
end

-- Функция для обновления позиции платформы вслед за игроком
local function updatePlatformPosition()
	while enabled and platform and platform.Parent do
		local player = game.Players.LocalPlayer
		local character = player.Character
		if character then
			local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
			if humanoidRootPart then
				-- Плавное перемещение платформы к игроку
				local targetPosition = Vector3.new(
					humanoidRootPart.Position.X,
					47, -- 50 (высота игрока) - 3 (смещение платформы)
					humanoidRootPart.Position.Z
				)

				platform.Position = platform.Position:Lerp(targetPosition, 0.1)

				-- Обновляем позиции стен
				for _, wall in pairs(platformWalls) do
					if wall and wall.Parent then
						local offset = wall.Position - platform.Position
						wall.Position = platform.Position + offset
					end
				end
			end
		end
		wait(0.1)
	end
end

-- Обработчики кнопок
teleportButton.MouseButton1Click:Connect(function()
	teleportPlayer()
	if enabled then
		coroutine.wrap(updatePlatformPosition)()
	end
end)

toggleButton.MouseButton1Click:Connect(function()
	enabled = not enabled
	if enabled then
		toggleButton.Text = "ВКЛ"
		toggleButton.BackgroundColor3 = Color3.fromRGB(0, 120, 0)
		teleportPlayer()
		coroutine.wrap(updatePlatformPosition)()
	else
		toggleButton.Text = "ВЫКЛ"
		toggleButton.BackgroundColor3 = Color3.fromRGB(120, 0, 0)
		if platform then platform:Destroy() end
		for _, wall in pairs(platformWalls) do
			wall:Destroy()
		end
		platformWalls = {}
	end
end)

-- Автотелепортация при спавне
game.Players.LocalPlayer.CharacterAdded:Connect(function(character)
	if enabled then
		wait(1)
		teleportPlayer()
		coroutine.wrap(updatePlatformPosition)()
	end
end)
