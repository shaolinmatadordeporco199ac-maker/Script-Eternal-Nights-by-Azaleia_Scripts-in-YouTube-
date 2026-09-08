--Script By @Azaleia_Scripts in YouTube

local Rayfield = loadstring(game:HttpGet("https://sirius.menu/gen2"))()

local window = Rayfield:CreateWindow({
    name = "Eternal Nights Script",
    subtitle = "Script by Azaleia_Scripts",
    sidebarLayout = true,
})

local tab = window:CreateTab({ name = "ESP"})

tab:CreateToggle({
    name = "Animatronics ESP",
    callback = function(value)
    local Workspace = game:GetService("Workspace")
    local Env = getgenv()

    local Colors = {
        Freddy = Color3.fromRGB(101, 67, 33),
        Foxy = Color3.fromRGB(255, 0, 0),
        Bonnie = Color3.fromRGB(0, 100, 255),
        Chica = Color3.fromRGB(255, 255, 0),
        ["Golden Freddy"] = Color3.fromRGB(255, 215, 0),
        GoldenFreddy = Color3.fromRGB(255, 215, 0)
    }

    Env.AnimatronicESPEnabled = value

    local function UpdateModel(Model)
        if not Model:IsA("Model") then return end

        local Color = Colors[Model.Name]
        if not Color then return end

        local Humanoid = Model:FindFirstChild("Humanoid", true)
        if not Humanoid or not Humanoid:IsA("Humanoid") then return end

        local Highlight = Model:FindFirstChild("AnimatronicESP")
        local Billboard = Model:FindFirstChild("AnimatronicName")

        if Highlight then
            Highlight.Enabled = Env.AnimatronicESPEnabled
        end

        if Billboard then
            Billboard.Enabled = Env.AnimatronicESPEnabled
        end
    end

    local function ApplyESP(Model)
        if not Env.AnimatronicESPEnabled then return end
        if not Model:IsA("Model") then return end

        local Color = Colors[Model.Name]
        if not Color then return end

        local Humanoid = Model:FindFirstChild("Humanoid", true)
        if not Humanoid or not Humanoid:IsA("Humanoid") then return end

        local Root = Model:FindFirstChild("HumanoidRootPart", true)
            or Model:FindFirstChild("Head", true)
            or Model.PrimaryPart

        if not Root then return end

        local Highlight = Model:FindFirstChild("AnimatronicESP")

        if not Highlight then
            Highlight = Instance.new("Highlight")
            Highlight.Name = "AnimatronicESP"
            Highlight.Adornee = Model
            Highlight.FillColor = Color
            Highlight.OutlineColor = Color
            Highlight.FillTransparency = 1
            Highlight.OutlineTransparency = 0
            Highlight.Parent = Model
        end

        local Billboard = Model:FindFirstChild("AnimatronicName")

        if not Billboard then
            Billboard = Instance.new("BillboardGui")
            Billboard.Name = "AnimatronicName"
            Billboard.Adornee = Root
            Billboard.Size = UDim2.fromOffset(60, 14)
            Billboard.StudsOffset = Vector3.new(0, 2.5, 0)
            Billboard.AlwaysOnTop = true
            Billboard.Parent = Model

            local Text = Instance.new("TextLabel")
            Text.Size = UDim2.fromScale(1, 1)
            Text.BackgroundTransparency = 1
            Text.Text = Model.Name
            Text.TextColor3 = Color
            Text.TextStrokeTransparency = 0
            Text.Font = Enum.Font.GothamBold
            Text.TextScaled = true
            Text.Parent = Billboard
        end

        Highlight.Enabled = true
        Billboard.Enabled = true
    end

    -- Atualiza os que já existem
    for _, Object in ipairs(Workspace:GetDescendants()) do
        if Object:IsA("Model") then
            if value then
                ApplyESP(Object)
            else
                UpdateModel(Object)
            end
        end
    end

    -- Cria apenas uma conexão
    if not Env.AnimatronicESPConnection then
        Env.AnimatronicESPConnection = Workspace.DescendantAdded:Connect(function(Object)
            task.wait()

            if not Env.AnimatronicESPEnabled then
                return
            end

            local Model

            if Object:IsA("Model") then
                Model = Object
            else
                Model = Object:FindFirstAncestorOfClass("Model")
            end

            if Model then
                ApplyESP(Model)
            end
        end)
    end
end
})

tab:CreateToggle({
    name = "Fuse ESP",
    callback = function(value)
    local Workspace = game:GetService("Workspace")
    local White = Color3.fromRGB(255, 255, 255)

    local function RemoveESP(Fuse)
        local Highlight = Fuse:FindFirstChild("FuseESP")
        if Highlight then
            Highlight:Destroy()
        end

        local Billboard = Fuse:FindFirstChild("FuseName")
        if Billboard then
            Billboard:Destroy()
        end
    end

    local function ApplyFuseESP(Fuse)
        if Fuse.Name ~= "Fuse" or Fuse.Parent ~= Workspace then
            return
        end

        local Part

        if Fuse:IsA("BasePart") then
            Part = Fuse
        elseif Fuse:IsA("Model") then
            Part = Fuse:FindFirstChildWhichIsA("BasePart", true)
        end

        if not Part then return end

        local Highlight = Fuse:FindFirstChild("FuseESP")

        if not Highlight then
            Highlight = Instance.new("Highlight")
            Highlight.Name = "FuseESP"
            Highlight.Adornee = Fuse
            Highlight.FillColor = White
            Highlight.OutlineColor = White
            Highlight.FillTransparency = 1
            Highlight.OutlineTransparency = 0
            Highlight.Parent = Fuse
        end

        local Billboard = Fuse:FindFirstChild("FuseName")

        if not Billboard then
            Billboard = Instance.new("BillboardGui")
            Billboard.Name = "FuseName"
            Billboard.Adornee = Part
            Billboard.Size = UDim2.fromOffset(40, 9)
            Billboard.StudsOffset = Vector3.new(0, 1.5, 0)
            Billboard.AlwaysOnTop = true
            Billboard.Parent = Fuse

            local Text = Instance.new("TextLabel")
            Text.Size = UDim2.fromScale(1, 1)
            Text.BackgroundTransparency = 1
            Text.Text = "Fuse"
            Text.TextColor3 = White
            Text.TextStrokeTransparency = 0
            Text.Font = Enum.Font.GothamBold
            Text.TextScaled = true
            Text.Parent = Billboard
        end

        Highlight.Enabled = value
        Billboard.Enabled = value
    end

    -- Fuses que já estão no Workspace
    for _, Object in ipairs(Workspace:GetChildren()) do
        if Object.Name == "Fuse" then
            ApplyFuseESP(Object)
        end
    end

    -- Quando entrar/sair do Workspace
    Workspace.ChildAdded:Connect(function(Object)
        if Object.Name == "Fuse" then
            task.wait()
            ApplyFuseESP(Object)
        end
    end)

    Workspace.ChildRemoved:Connect(function(Object)
        if Object.Name == "Fuse" then
            RemoveESP(Object)
        end
    end)
end
})

tab:CreateToggle({
    name = "Itens ESP",
    callback = function(value)
    local Workspace = game:GetService("Workspace")
    local Players = game:GetService("Players")

    local DarkBlue = Color3.fromRGB(0, 0, 80)

    local function IsPlayerCharacter(Object)
        for _, Player in ipairs(Players:GetPlayers()) do
            if Player.Character == Object then
                return true
            end
        end
        return false
    end

    local function ApplyESP(Item)
        -- Ignora Fuse
        if Item.Name == "Fuse" then
            return
        end

        -- Somente objetos diretamente no Workspace
        if Item.Parent ~= Workspace then
            return
        end

        -- Ignora personagens
        if IsPlayerCharacter(Item) then
            return
        end

        -- Precisa ter Handle
        local Handle = Item:FindFirstChild("Handle", true)

        if not Handle or not Handle:IsA("BasePart") then
            return
        end

        -- ESP
        local Highlight = Item:FindFirstChild("ItemESP")

        if not Highlight then
            Highlight = Instance.new("Highlight")
            Highlight.Name = "ItemESP"
            Highlight.Adornee = Item
            Highlight.FillColor = DarkBlue
            Highlight.OutlineColor = DarkBlue
            Highlight.FillTransparency = 1
            Highlight.OutlineTransparency = 0
            Highlight.Parent = Item
        end

        -- Nome
        local Billboard = Item:FindFirstChild("ItemNameESP")

        if not Billboard then
            Billboard = Instance.new("BillboardGui")
            Billboard.Name = "ItemNameESP"
            Billboard.Adornee = Handle
            Billboard.Size = UDim2.fromOffset(50, 10)
            Billboard.StudsOffset = Vector3.new(0, 1.5, 0)
            Billboard.AlwaysOnTop = true
            Billboard.Parent = Item

            local Text = Instance.new("TextLabel")
            Text.Size = UDim2.fromScale(1, 1)
            Text.BackgroundTransparency = 1
            Text.Text = Item.Name
            Text.TextColor3 = DarkBlue
            Text.TextStrokeTransparency = 0
            Text.Font = Enum.Font.GothamBold
            Text.TextScaled = true
            Text.Parent = Billboard
        end

        Highlight.Enabled = value
        Billboard.Enabled = value
    end

    -- Todos os itens que JÁ estão diretamente no Workspace
    for _, Item in ipairs(Workspace:GetChildren()) do
        ApplyESP(Item)
    end

    -- Novos itens que entrarem diretamente no Workspace
    Workspace.ChildAdded:Connect(function(Item)
        task.wait(0.1)
        ApplyESP(Item)
    end)

    -- Remove o ESP quando o item sair do Workspace
    Workspace.ChildRemoved:Connect(function(Item)
        local Highlight = Item:FindFirstChild("ItemESP")
        if Highlight then
            Highlight:Destroy()
        end

        local Billboard = Item:FindFirstChild("ItemNameESP")
        if Billboard then
            Billboard:Destroy()
        end
    end)
end
})

local tab = window:CreateTab({ name = "Teleport"})

tab:CreateButton({
    name = "Office Tp",
    callback = function()
    local Player = game:GetService("Players").LocalPlayer
    local Character = Player.Character or Player.CharacterAdded:Wait()
    local Root = Character:WaitForChild("HumanoidRootPart")

    Root.CFrame = CFrame.new(
        -208.330734,
        3.872358,
        148.240845
    )
end
})

tab:CreateButton({
    name = "Puppet Tp",
    callback = function()
    local Player = game:GetService("Players").LocalPlayer
    local Character = Player.Character or Player.CharacterAdded:Wait()
    local Root = Character:WaitForChild("HumanoidRootPart")

    Root.CFrame = CFrame.new(
        -114.758614,
        3.871756,
        -8.630657
    )
end
})

tab:CreateButton({
    name = "Fuses Tp",
    callback = function()
    local Player = game:GetService("Players").LocalPlayer
    local Character = Player.Character or Player.CharacterAdded:Wait()
    local Root = Character:WaitForChild("HumanoidRootPart")

    Root.CFrame = CFrame.new(
        -79.132332,
        3.404323,
        148.619400
    )
end
})

local tab = window:CreateTab({ name = "Misc"})


tab:CreateToggle({
    name = "Fulllight",
    callback = function()
    local Lighting = game:GetService("Lighting")

    Lighting.Brightness = 2
    Lighting.ClockTime = 14
    Lighting.FogEnd = 100000
    Lighting.GlobalShadows = false

    local Atmosphere = Lighting:FindFirstChildOfClass("Atmosphere")
    if Atmosphere then
        Atmosphere.Density = 0
        Atmosphere.Haze = 0
        Atmosphere.Glare = 0
    end
end
})

tab:CreateToggle({
    name = "Show Time",
    callback = function(value)
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local CoreGui = game:GetService("CoreGui")
    local RunService = game:GetService("RunService")

    local ClockTime = ReplicatedStorage.GameSettings.Coisas.ClockTime
    local Minutes = ClockTime.Minutes
    local Seconds = ClockTime.Seconds

    if not getgenv().ClockSystemStarted then
        getgenv().ClockSystemStarted = true

        getgenv().ClockUpdateConnection = RunService.Heartbeat:Connect(function()
            local gui = CoreGui:FindFirstChild("GameClock")

            if gui then
                local clockLabel = gui:FindFirstChild("Clock")

                if clockLabel then
                    clockLabel.Text = string.format(
                        "%d:%02d",
                        Minutes.Value,
                        Seconds.Value
                    )
                end
            end
        end)
    end

    if value then
        local oldGui = CoreGui:FindFirstChild("GameClock")

        if oldGui then
            oldGui:Destroy()
        end

        local gui = Instance.new("ScreenGui")
        gui.Name = "GameClock"
        gui.ResetOnSpawn = false
        gui.Parent = CoreGui

        local clockLabel = Instance.new("TextLabel")
        clockLabel.Name = "Clock"
        clockLabel.Size = UDim2.new(0, 75, 0, 25)
        clockLabel.Position = UDim2.new(0.5, -37.5, 0, 20)
        clockLabel.BackgroundTransparency = 1
        clockLabel.TextColor3 = Color3.new(1, 1, 1)
        clockLabel.TextStrokeTransparency = 0
        clockLabel.TextScaled = true
        clockLabel.Font = Enum.Font.GothamBold
        clockLabel.Text = string.format(
            "%d:%02d",
            Minutes.Value,
            Seconds.Value
        )
        clockLabel.Parent = gui

    else
        local gui = CoreGui:FindFirstChild("GameClock")

        if gui then
            gui:Destroy()
        end
    end
end
})

tab:CreateToggle({
    name = "Show Battery of Music Box",
    callback = function(value)
    local Workspace = game:GetService("Workspace")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")

    local ClockSound = Workspace.Game.Clock.Part.Clock
    local MusicBox = ReplicatedStorage.GameSettings.Coisas.Puppet.MusicBox

    -- Inicia o contador do relógio assim que o script é executado
    if not getgenv().GameClockStarted then
        getgenv().GameClockStarted = true
        getgenv().GameClockMinutes = getgenv().GameClockMinutes or 0

        getgenv().ClockConnection = ClockSound.Played:Connect(function()
            if getgenv().GameClockMinutes < 360 then
                getgenv().GameClockMinutes += 1
            end
        end)
    end

    -- Cria a GUI
    if value then
        local oldGui = game:GetService("CoreGui"):FindFirstChild("GameClock")
        if oldGui then
            oldGui:Destroy()
        end

        local gui = Instance.new("ScreenGui")
        gui.Name = "GameClock"
        gui.ResetOnSpawn = false
        gui.Parent = game:GetService("CoreGui")

        -- HORÁRIO
        local clockLabel = Instance.new("TextLabel")
        clockLabel.Name = "Clock"
        clockLabel.Size = UDim2.new(0, 75, 0, 25)
        clockLabel.Position = UDim2.new(0.5, -37.5, 0, 20)
        clockLabel.BackgroundTransparency = 1
        clockLabel.TextColor3 = Color3.new(1, 1, 1)
        clockLabel.TextStrokeTransparency = 0
        clockLabel.TextScaled = true
        clockLabel.Font = Enum.Font.GothamBold
        clockLabel.Parent = gui

        -- PORCENTAGEM DA MUSIC BOX
        local musicLabel = Instance.new("TextLabel")
        musicLabel.Name = "MusicBox"
        musicLabel.Size = UDim2.new(0, 60, 0, 18)
        musicLabel.Position = UDim2.new(0.5, -30, 0, 45)
        musicLabel.BackgroundTransparency = 1
        musicLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
        musicLabel.TextStrokeTransparency = 0
        musicLabel.TextScaled = true
        musicLabel.Font = Enum.Font.GothamBold
        musicLabel.Parent = gui

        -- Atualiza o horário
        local function updateClock()
            local total = getgenv().GameClockMinutes

            local hour = math.floor(total / 60)
            local minute = total % 60

            if hour == 0 then
                hour = 12
            end

            clockLabel.Text = string.format("%d:%02d", hour, minute)
        end

        -- Atualiza a porcentagem
        local function updateMusicBox()
            local value = MusicBox.Value
            musicLabel.Text = tostring(value) .. "%"
        end

        updateClock()
        updateMusicBox()

        -- Atualiza a porcentagem a cada segundo
        getgenv().MusicBoxConnection = task.spawn(function()
            while gui.Parent do
                updateMusicBox()
                task.wait(1)
            end
        end)

    else
        -- Apenas esconde a GUI.
        -- O relógio e a MusicBox continuam sendo monitorados.
        local gui = game:GetService("CoreGui"):FindFirstChild("GameClock")

        if gui then
            gui:Destroy()
        end
    end
end
})

tab:CreateToggle({
    name = "Show Energy",
    callback = function(value)
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local CoreGui = game:GetService("CoreGui")
    local RunService = game:GetService("RunService")

    local Fusivel = ReplicatedStorage.GameSettings.Coisas.Fusivel

    local Energia1 = Fusivel.EnergiaLugar1
    local Energia2 = Fusivel.EnergiaLugar2
    local Energia3 = Fusivel.EnergiaLugar3

    if not getgenv().EnergySystemStarted then
        getgenv().EnergySystemStarted = true

        getgenv().EnergyUpdateConnection = RunService.Heartbeat:Connect(function()
            local gui = CoreGui:FindFirstChild("EnergyDisplay")

            if gui then
                gui.Energia1.Text = tostring(Energia1.Value) .. "%"
                gui.Energia2.Text = tostring(Energia2.Value) .. "%"
                gui.Energia3.Text = tostring(Energia3.Value) .. "%"
            end
        end)
    end

    if value then
        local oldGui = CoreGui:FindFirstChild("EnergyDisplay")
        if oldGui then
            oldGui:Destroy()
        end

        local gui = Instance.new("ScreenGui")
        gui.Name = "EnergyDisplay"
        gui.ResetOnSpawn = false
        gui.Parent = CoreGui

        local function criarLabel(nome, posY, valor)
            local label = Instance.new("TextLabel")
            label.Name = nome
            label.Size = UDim2.new(0, 40, 0, 13)
            label.Position = UDim2.new(0.5, 42, 0, posY)
            label.BackgroundTransparency = 1
            label.TextColor3 = Color3.fromRGB(255, 255, 0)
            label.TextStrokeTransparency = 0
            label.TextScaled = true
            label.Font = Enum.Font.GothamBold
            label.Text = tostring(valor.Value) .. "%"
            label.Parent = gui
        end

        criarLabel("Energia1", 20, Energia1)
        criarLabel("Energia2", 34, Energia2)
        criarLabel("Energia3", 48, Energia3)

    else
        local gui = CoreGui:FindFirstChild("EnergyDisplay")

        if gui then
            gui:Destroy()
        end
    end
end
})
