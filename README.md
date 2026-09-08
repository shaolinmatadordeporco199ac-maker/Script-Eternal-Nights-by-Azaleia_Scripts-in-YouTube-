--Script By @Azaleia_Scripts In YouTube

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
        Goldenfreddy = Color3.fromRGB(255, 215, 0)
    }

    Env.AnimatronicESPEnabled = value

    local function ApplyESP(Model)
        if not Model:IsA("Model") then return end

        local Color = Colors[Model.Name]
        if not Color then return end

        -- Goldenfreddy pode não ter Humanoid
        local Humanoid = Model:FindFirstChild("Humanoid", true)

        if Model.Name ~= "Goldenfreddy" then
            if not Humanoid or not Humanoid:IsA("Humanoid") then
                return
            end
        end

        local Root =
            Model:FindFirstChild("HumanoidRootPart", true)
            or Model:FindFirstChild("Head", true)
            or Model:FindFirstChildWhichIsA("BasePart", true)
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

        Highlight.Enabled = Env.AnimatronicESPEnabled
        Billboard.Enabled = Env.AnimatronicESPEnabled
    end

    -- Procura todos os animatrônicos existentes
    for _, Object in ipairs(Workspace:GetDescendants()) do
        if Object:IsA("Model") then
            ApplyESP(Object)
        end
    end

    -- Detecta novos animatrônicos
    if not Env.AnimatronicESPConnection then
        Env.AnimatronicESPConnection =
            Workspace.DescendantAdded:Connect(function(Object)
                task.wait()

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

