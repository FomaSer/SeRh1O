local Players = game:GetService("Players")
local CollectionService = game:GetService("CollectionService")

-- Функция создает выделение и показывает имя игрока над персонажем
local function createHighlightForPlayer(player)
    -- Проверяем, существует ли персонаж у игрока
    if not player.Character then
        return
    end

    -- Ищем существующий HighlightTag
    local existingHighlight = player.Character:FindFirstChild("HighlightTag")
    if existingHighlight then
        return
    end

    -- Создаем выделение (highlight)
    local highlight = Instance.new("Highlight")
    highlight.Name = "HighlightTag"
    highlight.Adornee = player.Character
    highlight.FillColor = Color3.fromRGB(255, 0, 0) -- Красный цвет заполнения
    highlight.OutlineColor = Color3.new(1, 1, 1)   -- Белый контур
    highlight.Parent = player.Character

    -- Создаем BillBoardGui для показа имени игрока
    local billBoardGui = Instance.new("BillboardGui")
    billBoardGui.Name = "PlayerNameGui"
    billBoardGui.AlwaysOnTop = true -- <--- ДОБАВИЛИ ЭТУ СТРОКУ!!!
    billBoardGui.Adornee = player.Character:WaitForChild("Head") -- Крепят на голову
    billBoardGui.Size = UDim2.new(0, 200, 0, 50)                 -- Размер панели
    billBoardGui.StudsOffset = Vector3.new(0, 2, 0)              -- Позиция относительно головы
    billBoardGui.Parent = player.Character

    -- Текстовое поле с именем игрока
    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = player.Name                               -- Имя игрока
    textLabel.TextColor3 = Color3.new(1, 1, 1)                  -- Цвет текста белый
    textLabel.TextStrokeColor3 = Color3.new(0, 0, 0)            -- Черная обводка текста
    textLabel.TextStrokeTransparency = 0                       -- Видимая обводка
    textLabel.TextScaled = true                                 -- Автоматическое масштабирование шрифта
    textLabel.Parent = billBoardGui
end

-- Обработчик события добавления персонажа игроку
local function handleCharacterAdded(player, character)
    wait() -- Подождём завершение процесса появления персонажа
    createHighlightForPlayer(player)
end

-- Настройка обработчиков для нового игрока
local function setupPlayer(player)
    player.CharacterAdded:Connect(function(character)
        handleCharacterAdded(player, character)
    end)
    
    -- Если игрок уже имеет персонажа, сразу создадим элементы
    if player.Character then
        handleCharacterAdded(player, player.Character)
    end
end

-- Присоединяем обработку для каждого текущего игрока
for _, player in pairs(Players:GetPlayers()) do
    setupPlayer(player)
end

-- Добавляем обработку для будущих подключаемых игроков
Players.PlayerAdded:Connect(setupPlayer)
