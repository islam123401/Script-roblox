-- Load Rayfield UI
local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Key yang kamu tentukan langsung di code
local validKey = 'free_va0svsvwb10872555@>%:×,))][-<^^^^^^^^^"×""::@*(><<;:×::×,098865321/1,'

local unlocked = false

-- Window utama
local Window = Rayfield:CreateWindow({
    Name = "ESP System",
    LoadingTitle = "Key System Loader",
    LoadingSubtitle = "by Ibram",
    ConfigurationSaving = { Enabled = false },
    KeySystem = false
})

-- Tab Key
local KeyTab = Window:CreateTab("🔑 Key", 4483362458)

KeyTab:CreateInput({
    Name = "Enter Key",
    PlaceholderText = "Paste your key here...",
    RemoveTextAfterFocusLost = false,
    Callback = function(input)
        if input == validKey then
            unlocked = true
            Rayfield:Notify({
                Title = "Key Accepted",
                Content = "✅ ESP unlocked!",
                Duration = 4
            })
        else
            Rayfield:Notify({
                Title = "Invalid Key",
                Content = "❌ Wrong key, try again.",
                Duration = 4
            })
        end
    end,
})

-- Tab ESP
local ESPTab = Window:CreateTab("👀 ESP", 4483362458)

local NameTagESP = false
local BoxESP = false

-- Setup ESP untuk karakter
local function setupESP(char, plr)
    char:WaitForChild("Head")
    if char:FindFirstChild("ESPFolder") then return end

    local folder = Instance.new("Folder")
    folder.Name = "ESPFolder"
    folder.Parent = char

    -- Billboard (Nametag + Distance)
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "NameTag"
    billboard.Adornee = char.Head
    billboard.Size = UDim2.new(0, 150, 0, 40)
    billboard.StudsOffset = Vector3.new(0, 2, 0)
    billboard.AlwaysOnTop = true

    local nameLabel = Instance.new("TextLabel")
    nameLabel.Size = UDim2.new(1, 0, 0.5, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    nameLabel.Font = Enum.Font.SourceSansBold
    nameLabel.TextSize = 12
    nameLabel.Text = plr.Name
    nameLabel.Parent = billboard

    local distLabel = Instance.new("TextLabel")
    distLabel.Position = UDim2.new(0, 0, 0.5, 0)
    distLabel.Size = UDim2.new(1, 0, 0.5, 0)
    distLabel.BackgroundTransparency = 1
    distLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
    distLabel.Font = Enum.Font.SourceSansBold
    distLabel.TextSize = 11
    distLabel.Text = "0 studs"
    distLabel.Parent = billboard

    billboard.Parent = folder

    -- Box Highlight
    local box = Instance.new("Highlight")
    box.Name = "Box"
    box.Adornee = char
    box.FillTransparency = 1
    box.OutlineColor = Color3.fromRGB(255, 0, 0)
    box.OutlineTransparency = 0
    box.Enabled = false
    box.Parent = folder

    -- Update loop
    RunService.RenderStepped:Connect(function()
        if not unlocked then
            billboard.Enabled = false
            box.Enabled = false
            return
        end

        if char and char:FindFirstChild("HumanoidRootPart") then
            local dist = (LocalPlayer.Character.HumanoidRootPart.Position - char.HumanoidRootPart.Position).Magnitude
            distLabel.Text = tostring(math.floor(dist)) .. " studs"
        end
        billboard.Enabled = NameTagESP
        box.Enabled = BoxESP
    end)
end

-- Pasang ESP untuk semua player
local function createESP(plr)
    if plr == LocalPlayer then return end
    if plr.Character then
        setupESP(plr.Character, plr)
    end
    plr.CharacterAdded:Connect(function(char)
        setupESP(char, plr)
    end)
end

for _, plr in pairs(Players:GetPlayers()) do
    createESP(plr)
end
Players.PlayerAdded:Connect(createESP)

-- Toggle di UI
ESPTab:CreateToggle({
    Name = "Nametag ESP",
    CurrentValue = false,
    Flag = "NameTagToggle",
    Callback = function(state)
        if unlocked then
            NameTagESP = state
        else
            Rayfield:Notify({
                Title = "Locked",
                Content = "❌ Enter valid key first!",
                Duration = 3
            })
        end
    end
})

ESPTab:CreateToggle({
    Name = "Box ESP",
    CurrentValue = false,
    Flag = "BoxToggle",
    Callback = function(state)
        if unlocked then
            BoxESP = state
        else
            Rayfield:Notify({
                Title = "Locked",
                Content = "❌ Enter valid key first!",
                Duration = 3
            })
        end
    end
})
