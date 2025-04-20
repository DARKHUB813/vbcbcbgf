local Window = Library.CreateLib("TITLE", "DarkTheme")
local Tab = Window:NewTab("inicio")
Section:NewButton("auto farm level", "farme seu level", function(-- Auto Farm Blox Fruits completo
-- Script para farmar automaticamente até o nível máximo (2650)

-- Tabela com dados das áreas e inimigos para cada faixa de nível
local FarmData = {
    {min = 1, max = 9, enemy = "Bandit", pos = Vector3.new(1038, 16, 1547)},
    {min = 10, max = 29, enemy = "Monkey", pos = Vector3.new(-1599, 37, 154)},
    {min = 30, max = 59, enemy = "Gorilla", pos = Vector3.new(-1321, 38, -533)},
    {min = 60, max = 89, enemy = "Pirate", pos = Vector3.new(2200, 15, 3000)},
    {min = 90, max = 149, enemy = "Shark", pos = Vector3.new(1350, 20, -1560)},
    {min = 150, max = 299, enemy = "Giant", pos = Vector3.new(-2500, 100, -1800)},
    {min = 300, max = 499, enemy = "Cyborg", pos = Vector3.new(3000, 45, 2000)},
    {min = 500, max = 999, enemy = "Dragon", pos = Vector3.new(-5000, 100, -1500)},
    {min = 1000, max = 1499, enemy = "Magma", pos = Vector3.new(7000, 50, -4000)},
    {min = 1500, max = 1999, enemy = "MagmaBoss", pos = Vector3.new(10000, 150, -5000)},
    {min = 2000, max = 2499, enemy = "Ice", pos = Vector3.new(12000, 200, -6000)},
    {min = 2500, max = 2650, enemy = "Boss", pos = Vector3.new(13000, 300, -7000)},
}

-- Função para pegar o nível atual do jogador
function getPlayerLevel()
    local levelText = game.Players.LocalPlayer.PlayerGui:WaitForChild("Main"):WaitForChild("Level").Text
    return tonumber(string.match(levelText, "%d+"))
end

-- Função para detectar qual inimigo deve ser farmado de acordo com o nível
function getFarmTarget(level)
    for _, v in pairs(FarmData) do
        if level >= v.min and level <= v.max then
            return v
        end
    end
    return nil
end

-- Função para encontrar o inimigo mais próximo
function getClosestEnemy()
    local closestEnemy = nil
    local shortestDistance = math.huge
    local player = game.Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

    for _, enemy in pairs(workspace.Enemies:GetChildren()) do
        if enemy:FindFirstChild("HumanoidRootPart") and enemy:FindFirstChild("Humanoid") then
            local distance = (enemy.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
            if distance < shortestDistance then
                shortestDistance = distance
                closestEnemy = enemy
            end
        end
    end
    return closestEnemy
end

-- Função para atacar o inimigo
function attackEnemy(enemy)
    local vim = game:GetService("VirtualInputManager")
    repeat
        task.wait()
        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0)
        vim:SendKeyEvent(true, "Z", false, game)
        task.wait(0.2)
        vim:SendKeyEvent(false, "Z", false, game)
    until enemy.Humanoid.Health <= 0 or not enemy.Parent
end

-- Loop principal de Auto Farm
while true do
    task.wait(1) -- Espera entre as verificações
    local level = getPlayerLevel() -- Obtém o nível atual do jogador
    local farmData = getFarmTarget(level) -- Obtém a área correta para o nível

    if farmData == nil then continue end -- Caso não haja área correspondente, continua

    -- Teleporta o jogador para a posição do inimigo
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(farmData.pos)

    -- Encontra e ataca o inimigo mais próximo
    local enemy = getClosestEnemy()
    if enemy and enemy.Name == farmData.enemy then
        attackEnemy(enemy)
    end
end
)
    print("Clicked")
end)
