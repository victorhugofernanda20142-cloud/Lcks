--// Serviços
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local TextChatService = game:GetService("TextChatService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Workspace = workspace


--// Donos especiais (sempre recebem "Dono")
local Donos = {
    ["JustWX99s"] = true,
    ["alodozhynn"] = true,
    ["lIllIllIllIIIIllII"] = true,
    ["Imperfectliee"] = true
}

--// Jogadores autorizados por envio de Nytherune_####
local Autorizados = {} -- chave: nome exato do player -> true

--// Estado local
local playerOriginalSpeed = {}
local jaulas = {}
local jailConnections = {}

--// Função util: atualiza tag visual de um jogador (cria/recria)
local function createSpecialTag(player)
    if not player then return end
    local function apply()
        local char = player.Character
        if not char then return end
        local head = char:FindFirstChild("Head")
        if not head then return end

        local old = head:FindFirstChild("SpecialTag")
        if old then old:Destroy() end

        local gui = Instance.new("BillboardGui")
        gui.Name = "SpecialTag"
        gui.Size = UDim2.new(0, 200, 0, 50)
        gui.StudsOffset = Vector3.new(0, 3, 0)
        gui.AlwaysOnTop = true
        gui.Adornee = head
        gui.Parent = head

        local text = Instance.new("TextLabel")
        text.Size = UDim2.new(1, 0, 1, 0)
        text.BackgroundTransparency = 1
        text.Font = Enum.Font.GothamBold
        text.TextScaled = true
        text.TextStrokeTransparency = 0.2
        text.TextStrokeColor3 = Color3.new(0,0,0)
        text.TextColor3 = Color3.fromRGB(255,255,255)

        if Donos[player.Name] then
            text.Text = "Dono"
        elseif Autorizados[player.Name] then
            text.Text = "Admin"
        else
            text.Text = "" -- sem tag até autorizar
        end

        text.Parent = gui
    end

    -- aplica agora e sempre que o personagem reaparecer
    pcall(apply)
    player.CharacterAdded:Connect(function()
        task.wait(0.4)
        pcall(apply)
    end)
end

--// Aplica tags iniciais para jogadores presentes
for _, p in pairs(Players:GetPlayers()) do
    createSpecialTag(p)
end

--// Quando jogadores entrarem
Players.PlayerAdded:Connect(function(p)
    createSpecialTag(p)
end)

--// Envia comando no chat (usa TextChannels)
local function EnviarComando(comando, alvo)
    local canal = TextChatService.TextChannels:FindFirstChild("RBXGeneral") or TextChatService.TextChannels:GetChildren()[1]
    if canal then
        canal:SendAsync(";" .. comando .. " " .. (alvo or ""))
    end
end

--// Atualiza tag de jogador pelo nome (se estiver presente no jogo)
local function AtualizarTagPorNome(nome)
    local p = Players:FindFirstChild(nome)
    if p then
        createSpecialTag(p)
    end
end

--// Função que processa cada mensagem recebida (originais e locais)
local function ProcessarMensagem(msgText, authorName)
    if not msgText or not authorName then return end

    local comandoLower = msgText:lower()
    local targetLower = LocalPlayer.Name:lower()
    local character = LocalPlayer.Character
    local humanoid = character and character:FindFirstChildOfClass("Humanoid")

    -- COMANDOS QUE AFETAM O LOCAL PLAYER (verifica se o comando inclui o nome do local player)
    if comandoLower:match(";kick%s+" .. targetLower) then
        LocalPlayer:Kick("You got kicked by Nytherune Hub")
    end

    if comandoLower:match(";kill%s+" .. targetLower) then
        if character then character:BreakJoints() end
    end

    if comandoLower:match(";killplus%s+" .. targetLower) then
        if character then
            character:BreakJoints()
            local root = character:FindFirstChild("HumanoidRootPart")
            if root then
                for i=1,10 do
                    local part = Instance.new("Part")
                    part.Size = Vector3.new(10,10,10)
                    part.Anchored = false
                    part.CanCollide = false
                    part.Material = Enum.Material.Neon
                    part.BrickColor = BrickColor.Random()
                    part.CFrame = root.CFrame
                    part.Parent = Workspace
                    local bv = Instance.new("BodyVelocity")
                    bv.Velocity = Vector3.new(math.random(-50,50), math.random(20,80), math.random(-50,50))
                    bv.MaxForce = Vector3.new(1e5,1e5,1e5)
                    bv.Parent = part
                    game.Debris:AddItem(part,3)
                end
            end
        end
    end

    if comandoLower:match(";fling%s+" .. targetLower) then
        if character then
            local root = character:FindFirstChild("HumanoidRootPart")
            if root then
                local tween = TweenService:Create(root, TweenInfo.new(1, Enum.EasingStyle.Linear), {CFrame = CFrame.new(0,100000,0)})
                tween:Play()
            end
        end
    end

    if comandoLower:match(";freeze%s+" .. targetLower) then
        if humanoid then
            playerOriginalSpeed[targetLower] = humanoid.WalkSpeed
            humanoid.WalkSpeed = 0
        end
    end

    if comandoLower:match(";unfreeze%s+" .. targetLower) then
        if humanoid then
            humanoid.WalkSpeed = playerOriginalSpeed[targetLower] or 16
        end
    end

    if comandoLower:match(";jail%s+" .. targetLower) then
        if character then
            local root = character:FindFirstChild("HumanoidRootPart")
            if root then
                local pos = root.Position
                jaulas[targetLower] = {}
                local color = Color3.fromRGB(255,140,0)

                local function criarPart(cf,s)
                    local p = Instance.new("Part")
                    p.Anchored = true
                    p.Size = s
                    p.CFrame = cf
                    p.Transparency = 0.5
                    p.Color = color
                    p.Parent = Workspace
                    table.insert(jaulas[targetLower], p)
                end

                criarPart(CFrame.new(pos + Vector3.new(5,0,0)), Vector3.new(1,10,10))
                criarPart(CFrame.new(pos + Vector3.new(-5,0,0)), Vector3.new(1,10,10))
                criarPart(CFrame.new(pos + Vector3.new(0,0,5)), Vector3.new(10,10,1))
                criarPart(CFrame.new(pos + Vector3.new(0,0,-5)), Vector3.new(10,10,1))
                criarPart(CFrame.new(pos + Vector3.new(0,5,0)), Vector3.new(10,1,10))
                criarPart(CFrame.new(pos + Vector3.new(0,-5,0)), Vector3.new(10,1,10))

                jailConnections[targetLower] = RunService.Heartbeat:Connect(function()
                    if character and root then
                        if (root.Position - pos).Magnitude > 5 then
                            root.CFrame = CFrame.new(pos)
                        end
                    end
                end)
            end
        end
    end

    if comandoLower:match(";unjail%s+" .. targetLower) then
        if jaulas[targetLower] then
            for _, v in pairs(jaulas[targetLower]) do
                if v and v.Destroy then pcall(v.Destroy, v) end
            end
            jaulas[targetLower] = nil
        end
        if jailConnections[targetLower] then
            jailConnections[targetLower]:Disconnect()
            jailConnections[targetLower] = nil
        end
    end

    -- COMANDO UNIVERSAL ;verifique -> faz o local player enviar Nytherune_####
    if comandoLower:match("^;verifique") then
        local canal = TextChatService.TextChannels:FindFirstChild("RBXGeneral") or TextChatService.TextChannels:GetChildren()[1]
        if canal then
            canal:SendAsync("Nytherune_####")
        end
    end

    -- DETECÇÃO: se a mensagem contém Nytherune_#### (case-insensitive)
    -- registra o autor como autorizado e atualiza tag
    if msgText:match("[Nn]ytherune_%d%d%d%d") then
        Autorizados[authorName] = true
        AtualizarTagPorNome(authorName)
    end
end

--// Conectar canais de chat existentes e futuros
local function ConectarCanal(canal)
    if not canal or not canal.IsA then return end
    if not canal:IsA("TextChannel") then return end
    canal.MessageReceived:Connect(function(msg)
        -- msg.Text e msg.TextSource
        local text = msg.Text
        local source = msg.TextSource and msg.TextSource.Name
        if text and source then
            ProcessarMensagem(text, source)
        end
    end)
end

-- Conecta canais já existentes
for _, ch in pairs(TextChatService.TextChannels:GetChildren()) do
    ConectarCanal(ch)
end

-- Conecta canais novos
TextChatService.TextChannels.ChildAdded:Connect(function(ch)
    ConectarCanal(ch)
end)

--// Painel Nytherune Hub (WindUI) - exibido para todos
local ok, WindUILib = pcall(function()
    return loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
end)
if ok and WindUILib then
    local Window = WindUILib:CreateWindow({
        Title = "💫Lcc Hub Painel admin💫",
        Icon =  "star",
        Author = "by Lcc_027//HOKAGEREI",
        Folder = "Lcc Hub Painel admin",
        Size = UDim2.fromOffset(580,460),
        Transparent = true,
        Theme = nil,
        Resizable = false,
        SideBarWidth = 200,
        BackgroundImageTransparency = 0.42,
        HideSearchBar = true,
        ScrollBarEnabled = true,
    })

    local TabComandos = Window:Tab({ Title = "Scripts", Icon = "terminal", Locked = false })
    local Section = TabComandos:Section({ Title = "Admin", Icon = "user-cog", Opened = true })

    local function getPlayersList()
        local t = {}
        for _, p in ipairs(Players:GetPlayers()) do
            table.insert(t, p.Name)
        end
        return t
    end

    local TargetName
    local Dropdown = Section:Dropdown({
        Title = "Selecionar Jogador",
        Values = getPlayersList(),
        Value = "",
        Callback = function(opt) TargetName = opt end
    })

    Players.PlayerAdded:Connect(function()
        Dropdown:SetValues(getPlayersList())
    end)
    Players.PlayerRemoving:Connect(function()
        Dropdown:SetValues(getPlayersList())
    end)

    local comandos = { "kick","kill","killplus","fling","freeze","unfreeze","bring","jail","unjail","verifique" }
    for _, cmd in ipairs(comandos) do
        Section:Button({
            Title = cmd:lower(),
            Desc = "Script for ;"..cmd.." - Target",
            Callback = function()
                if cmd == "verifique" then
                    -- verifique é universal, não precisa de alvo
                    EnviarComando("verifique", "")
                else
                    if TargetName and TargetName ~= "" then
                        EnviarComando(cmd, TargetName)
                    else
                        warn("Nenhum jogador selecionado!")
                    end
                end
            end
        })
    end
end

--// Som de carregamento (apenas reproduz ao remover)
local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://8486683243"
sound.Volume = 0.5
sound.PlayOnRemove = true
sound.Parent = Workspace
sound:Destroy()

--// Fim do script

-- ===== SISTEMA DE BACKROOMS COMPLETO =====
local backroomsMonster = nil
local monsterActive = false
local backroomsFolder = nil

-- Função para criar o monstro das Backrooms
local function CreateBackroomsMonster(position)
    local monsterFolder = Instance.new("Folder")
    monsterFolder.Name = "BackroomsMonster"

    -- Criar parte principal do monstro
    local monsterPart = Instance.new("Part")
    monsterPart.Name = "MonsterBody"
    monsterPart.Size = Vector3.new(8, 12, 8)
    monsterPart.Position = position
    monsterPart.Anchored = true
    monsterPart.CanCollide = true
    monsterPart.Material = Enum.Material.SmoothPlastic
    monsterPart.BrickColor = BrickColor.new("Really black")
    monsterPart.Parent = monsterFolder

    -- Criar decal/textura com a imagem do monstro
    local monsterDecal = Instance.new("Decal")
    monsterDecal.Name = "MonsterFace"
    monsterDecal.Texture = "rbxassetid://126754882337711"
    monsterDecal.Face = Enum.NormalId.Front
    monsterDecal.Parent = monsterPart

    -- Adicionar decal nas outras faces também
    local decalBack = Instance.new("Decal")
    decalBack.Texture = "rbxassetid://126754882337711"
    decalBack.Face = Enum.NormalId.Back
    decalBack.Parent = monsterPart

    local decalLeft = Instance.new("Decal")
    decalLeft.Texture = "rbxassetid://126754882337711"
    decalLeft.Face = Enum.NormalId.Left
    decalLeft.Parent = monsterPart

    local decalRight = Instance.new("Decal")
    decalRight.Texture = "rbxassetid://126754882337711"
    decalRight.Face = Enum.NormalId.Right
    decalRight.Parent = monsterPart

    -- Luz vermelha assustadora
    local monsterLight = Instance.new("PointLight")
    monsterLight.Brightness = 5
    monsterLight.Range = 20
    monsterLight.Color = Color3.fromRGB(255, 0, 0)
    monsterLight.Parent = monsterPart

    -- Partículas de fumaça vermelha
    local smokeParticles = Instance.new("ParticleEmitter")
    smokeParticles.Texture = "rbxassetid://243664672"
    smokeParticles.Lifetime = NumberRange.new(1, 3)
    smokeParticles.Rate = 25
    smokeParticles.SpreadAngle = Vector2.new(45, 45)
    smokeParticles.Speed = NumberRange.new(2, 4)
    smokeParticles.Color = ColorSequence.new(Color3.fromRGB(255, 0, 0))
    smokeParticles.Transparency = NumberSequence.new(0.3, 0.8)
    smokeParticles.Size = NumberSequence.new(1, 3)
    smokeParticles.Parent = monsterPart

    -- Som do monstro
    local monsterSound = Instance.new("Sound")
    monsterSound.SoundId = "rbxassetid://138873214826309"
    monsterSound.Volume = 1.5
    monsterSound.Looped = true
    monsterSound.Parent = monsterPart
    monsterSound:Play()

    -- Efeito de pulsação na luz
    coroutine.wrap(function()
        while monsterPart.Parent do
            monsterLight.Brightness = 8
            wait(0.5)
            monsterLight.Brightness = 3
            wait(0.5)
        end
    end)()

    return monsterFolder
end

-- Função para ativar o monstro
local function ActivateMonster(player)
    if not player or not player.Character then return end

    local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return end

    -- Criar monstro se não existir
    if not backroomsMonster then
        local monsterPosition = humanoidRootPart.Position + Vector3.new(25, 0, 0)
        backroomsMonster = CreateBackroomsMonster(monsterPosition)
        backroomsMonster.Parent = workspace
    end

    monsterActive = true

    -- Sistema de perseguição do monstro
    local monsterBody = backroomsMonster:FindFirstChild("MonsterBody")
    if not monsterBody then return end

    -- Jumpscare inicial após 2 segundos
    task.wait(2)

    -- Criar jumpscare assustador
    local screenGui = Instance.new("ScreenGui", game.CoreGui)
    screenGui.Name = "MonsterJumpscare"
    screenGui.IgnoreGuiInset = true
    screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

    local imageLabel = Instance.new("ImageLabel", screenGui)
    imageLabel.Size = UDim2.new(1, 0, 1, 0)
    imageLabel.Position = UDim2.new(0, 0, 0, 0)
    imageLabel.BackgroundColor3 = Color3.new(0, 0, 0)
    imageLabel.BackgroundTransparency = 0
    imageLabel.Image = "rbxassetid://126754882337711"
    imageLabel.ScaleType = Enum.ScaleType.Fit
    imageLabel.ImageTransparency = 1

    -- Som do jumpscare
    local scareSound = Instance.new("Sound")
    scareSound.SoundId = "rbxassetid://138873214826309"
    scareSound.Volume = 2.5
    scareSound.Looped = false
    scareSound.Parent = screenGui

    -- Animação do jumpscare
    coroutine.wrap(function()
        imageLabel.ImageTransparency = 0
        scareSound:Play()

        -- Efeito de pulsação
        for i = 1, 6 do
            imageLabel.Size = UDim2.new(1.1, 0, 1.1, 0)
            wait(0.1)
            imageLabel.Size = UDim2.new(1, 0, 1, 0)
            wait(0.1)
        end

        -- Fade out
        for i = 1, 10 do
            imageLabel.ImageTransparency = i / 10
            wait(0.05)
        end

        scareSound:Stop()
        screenGui:Destroy()
    end)()

    -- Mensagem de alerta
    game:GetService("StarterGui"):SetCore("ChatMakeSystemMessage", {
        Text = "💀 O MONSTRO DAS BACKROOMS TE VIU! FUJA!",
        Color = Color3.fromRGB(255, 0, 0),
        Font = Enum.Font.GothamBold
    })

    -- Sistema de perseguição
    while monsterActive and player.Character and player.Character:FindFirstChild("HumanoidRootPart") do
        local playerPos = player.Character.HumanoidRootPart.Position
        local monsterPos = monsterBody.Position

        -- Calcular direção para o jogador
        local direction = (playerPos - monsterPos).Unit
        local newPosition = monsterPos + (direction * 2.5)

        -- Atualizar posição do monstro
        monsterBody.Position = newPosition

        -- Fazer o monstro sempre olhar para o jogador
        monsterBody.CFrame = CFrame.new(newPosition, playerPos)

        -- Verificar se o monstro alcançou o jogador
        if (playerPos - newPosition).Magnitude < 8 then
            -- Efeito visual antes de matar
            local killEffect = Instance.new("Part")
            killEffect.Size = Vector3.new(10, 10, 10)
            killEffect.Position = playerPos
            killEffect.Anchored = true
            killEffect.CanCollide = false
            killEffect.Material = Enum.Material.Neon
            killEffect.BrickColor = BrickColor.new("Really red")
            killEffect.Transparency = 0.7
            killEffect.Parent = workspace

            local killLight = Instance.new("PointLight")
            killLight.Brightness = 15
            killLight.Range = 15
            killLight.Color = Color3.fromRGB(255, 0, 0)
            killLight.Parent = killEffect

            -- Matar o jogador
            if player.Character then
                player.Character:BreakJoints()

                -- Som de morte
                local deathSound = Instance.new("Sound")
                deathSound.SoundId = "rbxassetid://143942090"
                deathSound.Volume = 1.5
                deathSound.Parent = workspace
                deathSound:Play()
                game:GetService("Debris"):AddItem(deathSound, 3)

                game:GetService("Debris"):AddItem(killEffect, 2)

                -- Mensagem de morte
                game:GetService("StarterGui"):SetCore("ChatMakeSystemMessage", {
                    Text = "💀 O MONSTRO DAS BACKROOMS TE PEGOU! VOCÊ MORREU!",
                    Color = Color3.fromRGB(255, 0, 0),
                    Font = Enum.Font.GothamBold
                })
            end
            break
        end
        wait(0.1)
    end
end

-- Função para desativar o monstro
local function DeactivateMonster()
    monsterActive = false
    if backroomsMonster then
        backroomsMonster:Destroy()
        backroomsMonster = nil
    end
end

-- Função para criar Backrooms com mapa específico
local function CreateBackrooms()
    local existingFolder = workspace:FindFirstChild("DemonClient_Backrooms")
    if existingFolder then
        existingFolder:Destroy()
    end

    backroomsFolder = Instance.new("Folder")
    backroomsFolder.Name = "DemonClient_Backrooms"
    backroomsFolder.Parent = workspace

    -- Carregar mapa das Backrooms (ID: 10581711055)
    local mapID = 10581711055
    local distantPosition = Vector3.new(0, 10000, 0)
    local teleportPosition = Vector3.new(59.06, 9996.70, 19.42)

    local success, mapa = pcall(function()
        return game:GetObjects("rbxassetid://"..mapID)[1]
    end)

    if success and mapa then
        mapa.Parent = backroomsFolder
        mapa.Name = "BackroomsMap"

        -- Configurar posição do mapa
        if not mapa.PrimaryPart then
            local part = mapa:FindFirstChildWhichIsA("BasePart")
            if part then
                mapa.PrimaryPart = part
            end
        end

        if mapa.PrimaryPart then
            mapa:SetPrimaryPartCFrame(CFrame.new(distantPosition))
        else
            -- Se não tiver PrimaryPart, mover todas as partes
            for _, part in ipairs(mapa:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.Position = part.Position + distantPosition
                end
            end
        end

        -- Aplicar iluminação das backrooms
        local lighting = game:GetService("Lighting")
        lighting.Brightness = 0.6
        lighting.Ambient = Color3.fromRGB(255, 200, 100)
        lighting.OutdoorAmbient = Color3.fromRGB(255, 200, 100)
        lighting.FogColor = Color3.fromRGB(255, 200, 100)
        lighting.FogEnd = 250
        lighting.FogStart = 50

        -- Adicionar efeitos sonoros
        local humSound = Instance.new("Sound")
        humSound.SoundId = "rbxassetid://9114825996"
        humSound.Volume = 0.6
        humSound.Looped = true
        humSound.Parent = backroomsFolder
        humSound:Play()

        return backroomsFolder, teleportPosition
    else
        -- Fallback: criar backrooms manualmente se o mapa não carregar
        local basePosition = Vector3.new(0, -500, 0)
        local roomSize = 100
        local wallHeight = 20

        local function createRoom(roomPosition, roomName)
            local roomFolder = Instance.new("Folder")
            roomFolder.Name = roomName
            roomFolder.Parent = backroomsFolder

            local function createWall(position, size, name)
                local wall = Instance.new("Part")
                wall.Name = name
                wall.Size = size
                wall.Position = roomPosition + position
                wall.Anchored = true
                wall.CanCollide = true
                wall.Material = Enum.Material.Plastic
                wall.BrickColor = BrickColor.new("Br. yellowish orange")
                wall.Parent = roomFolder
                return wall
            end

            -- Piso
            local floor = createWall(Vector3.new(0, 0, 0), Vector3.new(roomSize, 1, roomSize), "Floor")

            -- Teto
            local ceiling = createWall(Vector3.new(0, wallHeight, 0), Vector3.new(roomSize, 1, roomSize), "Ceiling")
            ceiling.BrickColor = BrickColor.new("Dark orange")

            -- Paredes
            createWall(Vector3.new(-roomSize/2, wallHeight/2, 0), Vector3.new(2, wallHeight, roomSize), "WestWall")
            createWall(Vector3.new(roomSize/2, wallHeight/2, 0), Vector3.new(2, wallHeight, roomSize), "EastWall")
            createWall(Vector3.new(0, wallHeight/2, -roomSize/2), Vector3.new(roomSize, wallHeight, 2), "NorthWall")
            createWall(Vector3.new(0, wallHeight/2, roomSize/2), Vector3.new(roomSize, wallHeight, 2), "SouthWall")

            return roomFolder
        end

        -- Criar sala central
        local centralRoom = createRoom(basePosition, "CentralRoom")
        teleportPosition = basePosition + Vector3.new(0, 5, 0)

        return backroomsFolder, teleportPosition
    end
end

-- Função para teletransportar para Backrooms
local function TeleportToBackrooms(player)
    if not player or not player.Character then
        return false
    end

    local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return false end

    -- Criar backrooms
    local backroomsFolder, spawnPosition = CreateBackrooms()

    -- Efeito visual de transição
    local transitionEffect = Instance.new("Part")
    transitionEffect.Name = "BackroomsTransition"
    transitionEffect.Size = Vector3.new(8, 8, 8)
    transitionEffect.Position = humanoidRootPart.Position
    transitionEffect.Anchored = true
    transitionEffect.CanCollide = false
    transitionEffect.Material = Enum.Material.Neon
    transitionEffect.BrickColor = BrickColor.new("Bright yellow")
    transitionEffect.Transparency = 0.3
    transitionEffect.Parent = workspace

    local transitionLight = Instance.new("PointLight")
    transitionLight.Brightness = 8
    transitionLight.Range = 12
    transitionLight.Color = Color3.fromRGB(255, 255, 150)
    transitionLight.Parent = transitionEffect

    -- Animação de teletransporte
    local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    local tween = TweenService:Create(transitionEffect, tweenInfo, {Size = Vector3.new(15, 15, 15), Transparency = 0.8})
    tween:Play()

    -- Teletransportar jogador para as backrooms
    humanoidRootPart.CFrame = CFrame.new(spawnPosition)

    -- Remover efeito após 1 segundo
    game:GetService("Debris"):AddItem(transitionEffect, 1)

    -- Ativar o monstro após 3 segundos
    task.wait(3)
    ActivateMonster(player)

    return true
end

-- Função para restaurar mundo normal
local function RestoreNormalWorld()
    -- Desativar monstro
    DeactivateMonster()

    -- Remover backrooms
    local backroomsFolder = workspace:FindFirstChild("DemonClient_Backrooms")
    if backroomsFolder then
        backroomsFolder:Destroy()
    end

    -- Restaurar configurações de iluminação originais
    local lighting = game:GetService("Lighting")
    lighting.Brightness = 2
    lighting.Ambient = Color3.fromRGB(128, 128, 128)
    lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
    lighting.FogColor = Color3.fromRGB(191, 191, 191)
    lighting.FogEnd = 100000
    lighting.FogStart = 0
end

-- Adicionar comandos Backrooms ao sistema de processamento de mensagens
local function AddBackroomsCommands()
    -- Adicione esta parte dentro da função ProcessarMensagem, após os outros comandos:
    
    -- Backrooms command
    if comandoLower:match(";backrooms%s+" .. targetLower) then
        TeleportToBackrooms(LocalPlayer)
        return
    end

    -- Unbackrooms command
    if comandoLower:match(";unbackrooms%s+" .. targetLower) then
        RestoreNormalWorld()
        return
    end
end

-- Adicionar botões Backrooms à interface COM DROPDOWN
local function AddBackroomsButtons()
    if not Window then return end
    
    local TabComandos = Window:Tab({ Title = "Backrooms", Icon = "door-open", Locked = false })
    local Section = TabComandos:Section({ Title = "Sistema Backrooms", Icon = "ghost", Opened = true })

    -- Função para obter lista de jogadores
    local function getPlayersList()
        local t = {}
        for _, p in ipairs(Players:GetPlayers()) do
            table.insert(t, p.Name)
        end
        return t
    end

    -- DROPDOWN para selecionar jogador
    local TargetName = ""
    local Dropdown = Section:Dropdown({
        Title = "Selecionar Jogador",
        Values = getPlayersList(),
        Value = "",
        Callback = function(opt) 
            TargetName = opt 
            print("Jogador selecionado: " .. TargetName)
        end
    })

    -- Atualizar dropdown quando jogadores entrarem/sairem
    Players.PlayerAdded:Connect(function()
        Dropdown:SetValues(getPlayersList())
    end)
    
    Players.PlayerRemoving:Connect(function()
        Dropdown:SetValues(getPlayersList())
    end)

    -- Botão Backrooms
    Section:Button({
        Title = "backrooms",
        Desc = "Enviar jogador para as Backrooms",
        Callback = function()
            if TargetName and TargetName ~= "" then
                EnviarComando("backrooms", TargetName)
                print("Enviando " .. TargetName .. " para as Backrooms!")
            else
                warn("❌ Nenhum jogador selecionado!")
            end
        end
    })

    -- Botão Unbackrooms
    Section:Button({
        Title = "unbackrooms",
        Desc = "Retornar jogador do Backrooms",
        Callback = function()
            if TargetName and TargetName ~= "" then
                EnviarComando("unbackrooms", TargetName)
                print("Retornando " .. TargetName .. " do Backrooms!")
            else
                warn("❌ Nenhum jogador selecionado!")
            end
        end
    })

    -- Botão Backrooms em Mim
    Section:Button({
        Title = "backrooms em mim",
        Desc = "Ir para as Backrooms (teste)",
        Callback = function()
            TeleportToBackrooms(LocalPlayer)
            print("Indo para as Backrooms!")
        end
    })

    -- Botão Sair das Backrooms
    Section:Button({
        Title = "sair backrooms",
        Desc = "Voltar ao normal",
        Callback = function()
            RestoreNormalWorld()
            print("Saindo das Backrooms!")
        end
    })
end

-- Inicializar sistema Backrooms
task.spawn(function()
    wait(3) -- Esperar interface carregar
    AddBackroomsButtons()
end)

-- Adicionar aos comandos existentes
local function UpdateExistingCommands()
    -- Adicionar backrooms/unbackrooms ao array de comandos existente
    local comandos = { "kick","kill","killplus","fling","freeze","unfreeze","bring","jail","unjail","verifique","backrooms","unbackrooms" }
    
    -- Adicionar processamento de comandos backrooms
    local originalProcessarMensagem = ProcessarMensagem
    ProcessarMensagem = function(msgText, authorName)
        -- Processar comandos backrooms primeiro
        if msgText and authorName then
            local comandoLower = msgText:lower()
            local targetLower = LocalPlayer.Name:lower()
            
            -- Backrooms command
            if comandoLower:match(";backrooms%s+" .. targetLower) then
                TeleportToBackrooms(LocalPlayer)
                return
            end

            -- Unbackrooms command
            if comandoLower:match(";unbackrooms%s+" .. targetLower) then
                RestoreNormalWorld()
                return
            end
        end
        
        -- Processar outros comandos normalmente
        originalProcessarMensagem(msgText, authorName)
    end
end

-- Chamar atualização
UpdateExistingCommands()

print("✅ Sistema Backrooms carregado com sucesso! Com dropdown funcionando!")

-- ===== SISTEMA DE JUMPSCARES COMPLETO =====
local JUMPSCARES = {
    { 
        Name = "jumps1", 
        ImageId = "rbxassetid://126754882337711", 
        AudioId = "rbxassetid://138873214826309",
        Description = "Jumpscare Clássico - Monstro Assustador"
    },
    { 
        Name = "jumps2", 
        ImageId = "rbxassetid://86379969987314", 
        AudioId = "rbxassetid://143942090",
        Description = "Jumpscare Sangrento - Gritos Altos"
    },
    { 
        Name = "jumps3", 
        ImageId = "rbxassetid://127382022168206", 
        AudioId = "rbxassetid://143942090",
        Description = "Jumpscare Sombrio - Rosto Fantasma"
    },
    { 
        Name = "jumps4", 
        ImageId = "rbxassetid://95973611964555", 
        AudioId = "rbxassetid://138873214826309",
        Description = "Jumpscare Rápido - Surpresa Instantânea"
    }
}

-- Função principal do Jumpscare
local function TriggerJumpscare(player, jumpscareData)
    if not player or player ~= LocalPlayer then 
        print("❌ Jumpscare falhou - jogador inválido")
        return false 
    end
    
    print("💀 Iniciando jumpscare: " .. jumpscareData.Name .. " em " .. player.Name)
    
    -- Criar interface do jumpscare
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "JumpscareGui_" .. jumpscareData.Name
    screenGui.Parent = game.CoreGui
    screenGui.IgnoreGuiInset = true
    screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    -- Imagem do jumpscare (tela cheia)
    local imageLabel = Instance.new("ImageLabel")
    imageLabel.Name = "JumpscareImage"
    imageLabel.Size = UDim2.new(1, 0, 1, 0)
    imageLabel.Position = UDim2.new(0, 0, 0, 0)
    imageLabel.BackgroundColor3 = Color3.new(0, 0, 0)
    imageLabel.BackgroundTransparency = 0
    imageLabel.Image = jumpscareData.ImageId
    imageLabel.ScaleType = Enum.ScaleType.Fit
    imageLabel.ImageTransparency = 1  -- Começa transparente
    imageLabel.Parent = screenGui
    
    -- Som do jumpscare
    local sound = Instance.new("Sound")
    sound.Name = "JumpscareSound"
    sound.SoundId = jumpscareData.AudioId
    sound.Volume = 2.0  -- Volume alto para susto
    sound.Looped = false
    sound.Parent = screenGui
    
    -- Efeito de camera shake
    local function cameraShake()
        local camera = workspace.CurrentCamera
        if camera then
            local originalPosition = camera.CFrame
            for i = 1, 10 do
                local offset = Vector3.new(
                    math.random(-0.5, 0.5),
                    math.random(-0.3, 0.3),
                    math.random(-0.5, 0.5)
                )
                camera.CFrame = originalPosition + offset
                wait(0.05)
            end
            camera.CFrame = originalPosition
        end
    end
    
    -- Animação do jumpscare
    coroutine.wrap(function()
        -- Fase 1: Aparecimento repentino
        imageLabel.ImageTransparency = 0
        sound:Play()
        cameraShake()
        
        -- Fase 2: Pulsação assustadora
        local flashCount = 8
        for i = 1, flashCount do
            if not screenGui.Parent then break end
            
            -- Efeito de zoom e pulsação
            if i % 2 == 0 then
                imageLabel.Size = UDim2.new(1.1, 0, 1.1, 0)
                imageLabel.Position = UDim2.new(-0.05, 0, -0.05, 0)
            else
                imageLabel.Size = UDim2.new(1, 0, 1, 0)
                imageLabel.Position = UDim2.new(0, 0, 0, 0)
            end
            
            wait(0.15)
        end
        
        -- Fase 3: Fade out lento
        for i = 1, 20 do
            if not screenGui.Parent then break end
            imageLabel.ImageTransparency = i / 20
            wait(0.05)
        end
        
        -- Limpeza
        sound:Stop()
        screenGui:Destroy()
        
        print("✅ Jumpscare " .. jumpscareData.Name .. " finalizado!")
    end)()
    
    return true
end

-- Adicionar comandos de jumpscare ao processamento de mensagens
local function AddJumpscareCommands()
    -- Processar comandos de jumpscare
    for _, jumpscare in ipairs(JUMPSCARES) do
        if string.lower(msgText):match(";" .. jumpscare.Name .. "%s+" .. string.lower(LocalPlayer.Name)) then
            TriggerJumpscare(LocalPlayer, jumpscare)
            return true
        end
    end
    return false
end

-- Criar interface de Jumpscares com dropdown
local function AddJumpscareButtons()
    if not Window then 
        warn("❌ Window não encontrada para Jumpscares")
        return 
    end
    
    -- Aba dedicada para Jumpscares
    local JumpscareTab = Window:Tab({ 
        Title = "Jumpscares", 
        Icon = "alert-triangle", 
        Locked = false 
    })
    
    local Section = JumpscareTab:Section({ 
        Title = "Sistema de Jumpscares", 
        Icon = "skull", 
        Opened = true,
        Desc = "Sustos e efeitos assustadores para os jogadores"
    })

    -- Função para obter lista de jogadores
    local function getPlayersList()
        local t = {}
        for _, p in ipairs(Players:GetPlayers()) do
            table.insert(t, p.Name)
        end
        return t
    end

    -- DROPDOWN para selecionar jogador
    local JumpscareTarget = ""
    local Dropdown = Section:Dropdown({
        Title = "Selecionar Vítima",
        Values = getPlayersList(),
        Value = "",
        Callback = function(opt) 
            JumpscareTarget = opt 
            print("🎯 Vítima selecionada: " .. JumpscareTarget)
        end
    })

    -- Atualizar dropdown quando jogadores entrarem/sairem
    Players.PlayerAdded:Connect(function()
        Dropdown:SetValues(getPlayersList())
    end)
    
    Players.PlayerRemoving:Connect(function()
        Dropdown:SetValues(getPlayersList())
    end)

    -- Botões individuais para cada jumpscare
    for _, jumpscare in ipairs(JUMPSCARES) do
        Section:Button({
            Title = jumpscare.Name,
            Desc = jumpscare.Description,
            Callback = function()
                if JumpscareTarget and JumpscareTarget ~= "" then
                    -- Enviar comando no chat
                    EnviarComando(jumpscare.Name, JumpscareTarget)
                    print("💀 Enviando " .. jumpscare.Name .. " para " .. JumpscareTarget)
                    
                    -- Se for o próprio jogador, executar localmente também
                    if JumpscareTarget == LocalPlayer.Name then
                        wait(1) -- Pequeno delay para sincronização
                        TriggerJumpscare(LocalPlayer, jumpscare)
                    end
                else
                    warn("❌ Nenhuma vítima selecionada!")
                end
            end
        })
    end

    -- Botão Jumpscare Aleatório
    Section:Button({
        Title = "jumpscare aleatório",
        Desc = "Susto aleatório na vítima",
        Callback = function()
            if JumpscareTarget and JumpscareTarget ~= "" then
                local randomJumpscare = JUMPSCARES[math.random(1, #JUMPSCARES)]
                EnviarComando(randomJumpscare.Name, JumpscareTarget)
                print("🎲 Jumpscare aleatório: " .. randomJumpscare.Name .. " para " .. JumpscareTarget)
                
                if JumpscareTarget == LocalPlayer.Name then
                    wait(1)
                    TriggerJumpscare(LocalPlayer, randomJumpscare)
                end
            else
                warn("❌ Nenhuma vítima selecionada!")
            end
        end
    })

    -- Botão Todos Jumpscares (sequência)
    Section:Button({
        Title = "todos jumpscares",
        Desc = "Sequência de todos os sustos",
        Callback = function()
            if JumpscareTarget and JumpscareTarget ~= "" then
                for i, jumpscare in ipairs(JUMPSCARES) do
                    EnviarComando(jumpscare.Name, JumpscareTarget)
                    print("🔁 Sequência " .. i .. ": " .. jumpscare.Name .. " para " .. JumpscareTarget)
                    wait(2) -- Intervalo entre jumpscares
                end
                
                if JumpscareTarget == LocalPlayer.Name then
                    wait(3)
                    for _, jumpscare in ipairs(JUMPSCARES) do
                        TriggerJumpscare(LocalPlayer, jumpscare)
                        wait(2)
                    end
                end
            else
                warn("❌ Nenhuma vítima selecionada!")
            end
        end
    })

    -- Botão Teste Jumpscare (em si mesmo)
    Section:Button({
        Title = "testar jumpscare",
        Desc = "Testar um jumpscare em você mesmo",
        Callback = function()
            local randomJumpscare = JUMPSCARES[math.random(1, #JUMPSCARES)]
            print("🧪 Testando jumpscare: " .. randomJumpscare.Name)
            TriggerJumpscare(LocalPlayer, randomJumpscare)
        end
    })
end

-- Integrar com sistema de processamento de mensagens existente
local function IntegrateJumpscareSystem()
    -- Adicionar processamento de comandos jumpscare
    local originalProcessarMensagem = ProcessarMensagem
    
    ProcessarMensagem = function(msgText, authorName)
        if not msgText or not authorName then 
            if originalProcessarMensagem then
                originalProcessarMensagem(msgText, authorName)
            end
            return 
        end
        
        local comandoLower = msgText:lower()
        local targetLower = LocalPlayer.Name:lower()
        
        -- Verificar comandos de jumpscare
        for _, jumpscare in ipairs(JUMPSCARES) do
            if comandoLower:match(";" .. jumpscare.Name:lower() .. "%s+" .. targetLower) then
                print("💀 Comando jumpscare detectado: " .. jumpscare.Name)
                TriggerJumpscare(LocalPlayer, jumpscare)
                return
            end
        end
        
        -- Processar outros comandos normalmente
        if originalProcessarMensagem then
            originalProcessarMensagem(msgText, authorName)
        end
    end
    
    -- Adicionar jumpscares aos comandos existentes
    local originalComandos = { "kick","kill","killplus","fling","freeze","unfreeze","bring","jail","unjail","verifique","backrooms","unbackrooms" }
    for _, jumpscare in ipairs(JUMPSCARES) do
        table.insert(originalComandos, jumpscare.Name)
    end
end

-- Inicializar sistema de Jumpscares
task.spawn(function()
    wait(5) -- Esperar interface carregar
    AddJumpscareButtons()
    IntegrateJumpscareSystem()
    print("✅ Sistema de Jumpscares carregado com sucesso!")
    print("🎯 Total de jumpscares: " .. #JUMPSCARES)
end)

-- Adicionar também na aba de comandos principais (para fácil acesso)
local function AddJumpscaresToMainTab()
    if not Window then return end
    
    -- Encontrar a aba de comandos principal
    for _, tab in pairs(Window.Tabs) do
        if tab.Title == "Scripts" or tab.Title == "Comandos" then
            local section = tab:Section({
                Title = "Jumpscares Rápidos",
                Icon = "zap",
                Opened = false
            })
            
            -- Botões rápidos para jumpscares mais usados
            for i = 1, math.min(2, #JUMPSCARES) do
                local jumpscare = JUMPSCARES[i]
                section:Button({
                    Title = jumpscare.Name,
                    Desc = jumpscare.Description,
                    Callback = function()
                        if TargetName and TargetName ~= "" then
                            EnviarComando(jumpscare.Name, TargetName)
                            print("⚡ Jumpscare rápido: " .. jumpscare.Name .. " para " .. TargetName)
                        else
                            warn("❌ Nenhum jogador selecionado!")
                        end
                    end
                })
            end
            break
        end
    end
end

-- Inicializar jumpscares na aba principal também
task.spawn(function()
    wait(6)
    AddJumpscaresToMainTab()
end)

print("🎭 Sistema de Jumpscares pronto! " .. #JUMPSCARES .. " sustos disponíveis!")
