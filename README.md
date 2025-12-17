--==============================
-- ESP MOBILE - SEM ADM
--==============================

local Players = game:GetService("Players")
local player = Players.LocalPlayer

--==============================
-- GUI BASE
--==============================
local gui = Instance.new("ScreenGui")
gui.Name = "ESP_MOBILE"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- Botão flutuante
local openBtn = Instance.new("TextButton", gui)
openBtn.Size = UDim2.fromScale(0.12,0.12)
openBtn.Position = UDim2.fromScale(0.85,0.7)
openBtn.Text = "☰"
openBtn.TextScaled = true
openBtn.Font = Enum.Font.GothamBlack
openBtn.BackgroundColor3 = Color3.fromRGB(30,30,30)
openBtn.TextColor3 = Color3.new(1,1,1)
openBtn.Active = true
openBtn.Draggable = true
Instance.new("UICorner", openBtn).CornerRadius = UDim.new(1,0)

-- Painel
local panel = Instance.new("Frame", gui)
panel.Size = UDim2.fromScale(0.8,0.6)
panel.Position = UDim2.fromScale(0.1,0.2)
panel.BackgroundColor3 = Color3.fromRGB(20,20,20)
panel.Visible = false
panel.Active = true
panel.Draggable = true
Instance.new("UICorner", panel).CornerRadius = UDim.new(0,20)

local title = Instance.new("TextLabel", panel)
title.Size = UDim2.fromScale(1,0.15)
title.BackgroundTransparency = 1
title.Text = "ESP MOBILE"
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.TextColor3 = Color3.new(1,1,1)

--==============================
-- BOTÕES
--==============================
local function makeButton(text,y)
    local b = Instance.new("TextButton", panel)
    b.Size = UDim2.fromScale(0.9,0.14)
    b.Position = UDim2.fromScale(0.05,y)
    b.Text = text .. ": OFF"
    b.TextScaled = true
    b.Font = Enum.Font.GothamBold
    b.BackgroundColor3 = Color3.fromRGB(170,50,50)
    b.TextColor3 = Color3.new(1,1,1)
    Instance.new("UICorner", b).CornerRadius = UDim.new(0,16)
    return b
end

local btnPlayers = makeButton("ESP PLAYERS",0.18)
local btnNPCs    = makeButton("ESP NPCs",0.35)
local btnColor   = makeButton("TROCAR COR",0.52)
local btnClose   = makeButton("FECHAR",0.70)

--==============================
-- ESP SYSTEM
--==============================
local espPlayers = false
local espNPCs = false

local colors = {
    Color3.fromRGB(0,255,0),
    Color3.fromRGB(255,0,0),
    Color3.fromRGB(0,170,255),
    Color3.fromRGB(255,255,0)
}
local colorIndex = 1
local espObjects = {}

local function addESP(model)
    if espObjects[model] then return end
    if not model:FindFirstChild("HumanoidRootPart") then return end

    local h = Instance.new("Highlight")
    h.FillTransparency = 1
    h.OutlineTransparency = 0
    h.OutlineColor = colors[colorIndex]
    h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    h.Adornee = model
    h.Parent = model
    espObjects[model] = h
end

local function clearESP()
    for _,h in pairs(espObjects) do
        if h then h:Destroy() end
    end
    espObjects = {}
end

local function updateESP()
    clearESP()

    if espPlayers then
        for _,plr in pairs(Players:GetPlayers()) do
            if plr ~= player and plr.Character then
                addESP(plr.Character)
            end
        end
    end

    if espNPCs then
        for _,m in pairs(workspace:GetDescendants()) do
            if m:IsA("Model") and m:FindFirstChild("Humanoid")
            and not Players:GetPlayerFromCharacter(m) then
                addESP(m)
            end
        end
    end
end

--==============================
-- BOTÕES AÇÃO
--==============================
openBtn.MouseButton1Click:Connect(function()
    panel.Visible = not panel.Visible
end)

btnPlayers.MouseButton1Click:Connect(function()
    espPlayers = not espPlayers
    btnPlayers.Text = "ESP PLAYERS: " .. (espPlayers and "ON" or "OFF")
    btnPlayers.BackgroundColor3 = espPlayers and Color3.fromRGB(50,170,50) or Color3.fromRGB(170,50,50)
    updateESP()
end)

btnNPCs.MouseButton1Click:Connect(function()
    espNPCs = not espNPCs
    btnNPCs.Text = "ESP NPCs: " .. (espNPCs and "ON" or "OFF")
    btnNPCs.BackgroundColor3 = espNPCs and Color3.fromRGB(50,170,50) or Color3.fromRGB(170,50,50)
    updateESP()
end)

btnColor.MouseButton1Click:Connect(function()
    colorIndex = colorIndex % #colors + 1
    for _,h in pairs(espObjects) do
        h.OutlineColor = colors[colorIndex]
    end
end)

btnClose.MouseButton1Click:Connect(function()
    panel.Visible = false
end)

print("ESP MOBILE SEM ADM CARREGADO")
