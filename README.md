local ADMIN_USERIDS = {
    [7255074977] = true, -- Seu UserId
}

-- armas--
local armas = {
    "Parafal",
    "Uzi",
    "IA2",
    "G2",
    "AR15",
}
-- carros--
local carros = {
    "F1 Ferrari",
    "F1 Mercedes",
    "Ferrari Purosangue",
    "Cybertruck",
    "Lamborghini Urus",
    "Dodge Charger",
}

local player = game.Players.LocalPlayer
if not ADMIN_USERIDS[player.UserId] then return end

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RemoteEvent = ReplicatedStorage:FindFirstChild("SpawnItem")
if not RemoteEvent then
    RemoteEvent = Instance.new("RemoteEvent")
    RemoteEvent.Name = "SpawnItem"
    RemoteEvent.Parent = ReplicatedStorage
end

-- Sistema anti-cheat simulado (função local, para testes)
RemoteEvent.OnClientEvent:Connect(function(itemName, tipo)
    if not ADMIN_USERIDS[player.UserId] then
        player:Kick("Tentativa de trapaça detectada.")
        return
    end

    local modelo = ReplicatedStorage:FindFirstChild(itemName)
    if not modelo then return end

    if tipo == "Arma" then
        modelo:Clone().Parent = player.Backpack
    elseif tipo == "Carro" then
        local novoCarro = modelo:Clone()
        novoCarro.Parent = workspace
        if player.Character and player.Character.PrimaryPart then
            novoCarro:SetPrimaryPartCFrame(player.Character.PrimaryPart.CFrame * CFrame.new(5,0,0))
        end
    end
end)

-- Simulação do servidor (para testes locais)
function servidorFake(itemName, tipo)
    if not ADMIN_USERIDS[player.UserId] then
        player:Kick("Tentativa de trapaça detectada.")
        return
    end
    RemoteEvent:FireClient(player, itemName, tipo)
end

local function criarBotao(nome, parent, onClick, ordem)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 32)
    btn.Position = UDim2.new(0, 5, 0, (ordem - 1) * 36)
    btn.Text = nome
    btn.Parent = parent
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.SourceSans
    btn.TextSize = 18
    btn.AutoButtonColor = true
    btn.MouseButton1Click:Connect(function() onClick(nome) end)
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AdminPanel"
screenGui.Parent = player:WaitForChild("PlayerGui")

local painel = Instance.new("Frame", screenGui)
painel.Size = UDim2.new(0, 320, 0, 420)
painel.Position = UDim2.new(0, 30, 0.5, -210)
painel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
painel.BorderSizePixel = 0

local titulo = Instance.new("TextLabel", painel)
titulo.Size = UDim2.new(1, 0, 0, 40)
titulo.Text = "Painel Admin"
titulo.BackgroundTransparency = 1
titulo.TextColor3 = Color3.fromRGB(255, 255, 127)
titulo.Font = Enum.Font.SourceSansBold
titulo.TextSize = 24

local abaFrame = Instance.new("Frame", painel)
abaFrame.Position = UDim2.new(0, 0, 0, 40)
abaFrame.Size = UDim2.new(1, 0, 0, 40)
abaFrame.BackgroundTransparency = 1

local abaArmas = Instance.new("TextButton", abaFrame)
abaArmas.Size = UDim2.new(0.5, 0, 1, 0)
abaArmas.Position = UDim2.new(0, 0, 0, 0)
abaArmas.Text = "Armas"
abaArmas.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
abaArmas.TextColor3 = Color3.fromRGB(255, 255, 255)
abaArmas.Font = Enum.Font.SourceSansBold
abaArmas.TextSize = 20

local abaCarros = Instance.new("TextButton", abaFrame)
abaCarros.Size = UDim2.new(0.5, 0, 1, 0)
abaCarros.Position = UDim2.new(0.5, 0, 0, 0)
abaCarros.Text = "Carros"
abaCarros.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
abaCarros.TextColor3 = Color3.fromRGB(255, 255, 255)
abaCarros.Font = Enum.Font.SourceSansBold
abaCarros.TextSize = 20

local armasScrolling = Instance.new("ScrollingFrame", painel)
armasScrolling.Position = UDim2.new(0, 10, 0, 90)
armasScrolling.Size = UDim2.new(1, -20, 1, -120)
armasScrolling.BackgroundTransparency = 1
armasScrolling.ScrollBarThickness = 8
armasScrolling.CanvasSize = UDim2.new(0, 0, 0, math.max(36 * #armas, armasScrolling.AbsoluteSize.Y))

local carrosScrolling = Instance.new("ScrollingFrame", painel)
carrosScrolling.Position = UDim2.new(0, 10, 0, 90)
carrosScrolling.Size = UDim2.new(1, -20, 1, -120)
carrosScrolling.BackgroundTransparency = 1
carrosScrolling.ScrollBarThickness = 8
carrosScrolling.CanvasSize = UDim2.new(0, 0, 0, math.max(36 * #carros, carrosScrolling.AbsoluteSize.Y))
carrosScrolling.Visible = false

for i, arma in ipairs(armas) do
    criarBotao(arma, armasScrolling, function(nome)
        servidorFake(nome, "Arma")
    end, i)
end

for i, carro in ipairs(carros) do
    criarBotao(carro, carrosScrolling, function(nome)
        servidorFake(nome, "Carro")
    end, i)
end

local function mostrarAba(nome)
    armasScrolling.Visible = nome == "Armas"
    carrosScrolling.Visible = nome == "Carros"
    abaArmas.BackgroundColor3 = nome == "Armas" and Color3.fromRGB(78, 78, 78) or Color3.fromRGB(45, 45, 45)
    abaCarros.BackgroundColor3 = nome == "Carros" and Color3.fromRGB(78, 78, 78) or Color3.fromRGB(45, 45, 45)
end

abaArmas.MouseButton1Click:Connect(function() mostrarAba("Armas") end)
abaCarros.MouseButton1Click:Connect(function() mostrarAba("Carros") end)
mostrarAba("Armas")

local fecharBtn = Instance.new("TextButton", painel)
fecharBtn.Size = UDim2.new(0, 60, 0, 30)
fecharBtn.Position = UDim2.new(1, -70, 0, 10)
fecharBtn.Text = "Fechar"
fecharBtn.BackgroundColor3 = Color3.fromRGB(120, 30, 30)
fecharBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
fecharBtn.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)
