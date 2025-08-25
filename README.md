local Players = game:GetService("Players")

local bannedTools = {
    ["Barrier"] = true,
    ["Grab"] = true
}

local function kickIfBanned(player, tool)
    if tool:IsA("Tool") and bannedTools[tool.Name] then
        player:Kick("Жёлтый Блодмэн кикнул тебя за " .. tool.Name)
    end
end

local function checkPlayer(player)
    local function onCharacterAdded(char)
        -- Проверка уже находящихся инструментов в рюкзаке
        for _, tool in ipairs(player.Backpack:GetChildren()) do
            kickIfBanned(player, tool)
        end
        -- Проверка уже находящихся инструментов в руках
        for _, tool in ipairs(char:GetChildren()) do
            kickIfBanned(player, tool)
        end

        -- Отслеживаем появление новых инструментов в рюкзаке
        player.Backpack.ChildAdded:Connect(function(tool)
            kickIfBanned(player, tool)
        end)

        -- Отслеживаем появление новых инструментов в руках
        char.ChildAdded:Connect(function(tool)
            kickIfBanned(player, tool)
        end)
    end

    -- На случай, если персонаж уже создан
    if player.Character then
        onCharacterAdded(player.Character)
    end

    player.CharacterAdded:Connect(onCharacterAdded)
end

Players.PlayerAdded:Connect(checkPlayer)
