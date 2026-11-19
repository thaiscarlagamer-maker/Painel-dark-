--=============================================================
-- 🌑 DARK PANEL v6 — MM2 ADMIN (single script, completo)
-- Keys: DARK (VIP) | Dev (Ultimate) | Legend (LEGEND)
-- K = toggle painel | − = minimiza
--=============================================================
if not game:IsLoaded() then task.wait(2) end

local Players      = game:GetService("Players")
local RunService   = game:GetService("RunService")
local UIS          = game:GetService("UserInputService")
local Workspace    = game:GetService("Workspace")
local TweenService = game:GetService("TweenService")

local LP = Players.LocalPlayer
local playerGui = LP:WaitForChild("PlayerGui")

-- Cleanup
for _, n in ipairs({"DarkPanel","DarkESPv2","DarkFakePurchase","DarkDiscordPopup","DarkPurchaseSuccess"}) do
    local o = playerGui:FindFirstChild(n)
    if o then o:Destroy() end
end
pcall(function() RunService:UnbindFromRenderStep("DarkAimbot") end)

local Camera = Workspace.CurrentCamera
Workspace:GetPropertyChangedSignal("CurrentCamera"):Connect(function()
    Camera = Workspace.CurrentCamera
end)

-- Keys e estado
local KEY_VIP, KEY_ULT, KEY_LEG = "DARK", "Dev", "Legend"
local isVIP, isUltimate, isLegend = false, false, false

local S = {
    Aimbot=false, Spinbot=false, SpinSpeed=20, NoClip=false,
    SpinbotV2=false, SpinbotV2Speed=200, WalkSpeed=16,
    Wallbang=false, ESP=false, ESPRange=500,
    Fly=false, FlySpeed=100, NoVoid=false,
    KillAura=false, KillAuraDamage=1000000, KillAuraRange=500,
    ESPv2=false, HitKill=false, GhostMode=false,
    BringPlayer=false, JumpPower=50, NoRecharge=false,
    BotPuxada=false, BotPuxadaPart="Head",
}

local bringTarget = nil
local bringMaxDist = 2000
local aimAssistActive = false

--=============================================================
-- GUI ROOT
--=============================================================
local gui = Instance.new("ScreenGui")
gui.Name = "DarkPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.DisplayOrder = 999
gui.Parent = playerGui

local main = Instance.new("Frame")
main.Size = UDim2.new(0, 340, 0, 560)
main.Position = UDim2.new(0, 20, 0.5, -280)
main.BackgroundColor3 = Color3.fromRGB(12, 12, 18)
main.BorderSizePixel = 0
main.Active = true
main.Parent = gui
Instance.new("UICorner", main).CornerRadius = UDim.new(0, 10)
local stroke = Instance.new("UIStroke", main)
stroke.Color = Color3.fromRGB(130, 0, 220)
stroke.Thickness = 2

local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 42)
titleBar.BackgroundColor3 = Color3.fromRGB(25, 20, 35)
titleBar.BorderSizePixel = 0
titleBar.Active = true
titleBar.Parent = main
Instance.new("UICorner", titleBar).CornerRadius = UDim.new(0, 10)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -100, 1, 0)
title.Position = UDim2.new(0, 15, 0, 0)
title.BackgroundTransparency = 1
title.Text = "🌑  DARK PANEL v6"
title.TextColor3 = Color3.fromRGB(200, 100, 255)
title.Font = Enum.Font.GothamBlack
title.TextSize = 17
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = titleBar

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 28, 0, 28)
closeBtn.Position = UDim2.new(1, -34, 0, 7)
closeBtn.BackgroundColor3 = Color3.fromRGB(180, 40, 40)
closeBtn.Text = "×"
closeBtn.TextColor3 = Color3.new(1,1,1)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 18
closeBtn.AutoButtonColor = false
closeBtn.Parent = titleBar
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 6)

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 28, 0, 28)
minBtn.Position = UDim2.new(1, -66, 0, 7)
minBtn.BackgroundColor3 = Color3.fromRGB(60, 50, 90)
minBtn.Text = "−"
minBtn.TextColor3 = Color3.new(1,1,1)
minBtn.Font = Enum.Font.GothamBold
minBtn.TextSize = 22
minBtn.AutoButtonColor = false
minBtn.Parent = titleBar
Instance.new("UICorner", minBtn).CornerRadius = UDim.new(0, 6)

local openBtn = Instance.new("TextButton")
openBtn.Size = UDim2.new(0, 44, 0, 44)
openBtn.Position = UDim2.new(0, 20, 0, 20)
openBtn.BackgroundColor3 = Color3.fromRGB(30, 20, 45)
openBtn.Text = "🌑"
openBtn.TextSize = 22
openBtn.TextColor3 = Color3.new(1,1,1)
openBtn.Visible = false
openBtn.Parent = gui
Instance.new("UICorner", openBtn).CornerRadius = UDim.new(1, 0)
local openStroke = Instance.new("UIStroke", openBtn)
openStroke.Color = Color3.fromRGB(130, 0, 220)
openStroke.Thickness = 2

-- Drag
local dragging, dragStart, startPos
titleBar.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1
    or i.UserInputType == Enum.UserInputType.Touch then
        dragging, dragStart, startPos = true, i.Position, main.Position
    end
end)
UIS.InputChanged:Connect(function(i)
    if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement
    or i.UserInputType == Enum.UserInputType.Touch) then
        local d = i.Position - dragStart
        main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X,
                                  startPos.Y.Scale, startPos.Y.Offset + d.Y)
    end
end)
UIS.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1
    or i.UserInputType == Enum.UserInputType.Touch then dragging = false end
end)

local function minimizePanel() main.Visible = false; openBtn.Visible = true end
local function restorePanel()  main.Visible = true;  openBtn.Visible = false end
minBtn.MouseButton1Click:Connect(minimizePanel)
closeBtn.MouseButton1Click:Connect(minimizePanel)
openBtn.MouseButton1Click:Connect(restorePanel)
UIS.InputBegan:Connect(function(i, gpe)
    if gpe then return end
    if i.KeyCode == Enum.KeyCode.K then
        if main.Visible then minimizePanel() else restorePanel() end
    end
end)

-- Scroll
local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, -20, 1, -55)
scroll.Position = UDim2.new(0, 10, 0, 48)
scroll.BackgroundTransparency = 1
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 5
scroll.ScrollBarImageColor3 = Color3.fromRGB(130, 0, 220)
scroll.ClipsDescendants = true
scroll.CanvasSize = UDim2.new(0, 0, 0, 1500)
scroll.Parent = main

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0, 6)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = scroll
local pad = Instance.new("UIPadding")
pad.PaddingTop = UDim.new(0, 5)
pad.PaddingBottom = UDim.new(0, 10)
pad.Parent = scroll

local function refreshCanvas()
    pcall(function()
        scroll.CanvasSize = UDim2.new(0, 0, 0, math.max(layout.AbsoluteContentSize.Y + 30, 200))
    end)
end
layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(refreshCanvas)
scroll.ChildAdded:Connect(function() task.defer(refreshCanvas) end)
scroll.ChildRemoved:Connect(refreshCanvas)
task.spawn(function()
    for _ = 1, 10 do task.wait(0.1); refreshCanvas() end
end)

--=============================================================
-- HELPERS UI
--=============================================================
local order = 0
local function nextOrder() order = order + 1; return order end

local function makeSection(text, color)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, 0, 0, 24)
    l.BackgroundTransparency = 1
    l.Text = text
    l.TextColor3 = color or Color3.fromRGB(170, 100, 240)
    l.Font = Enum.Font.GothamBold
    l.TextSize = 12
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.LayoutOrder = nextOrder()
    l.Parent = scroll
    return l
end

local function makeToggle(text, cb)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, 0, 0, 34)
    b.BackgroundColor3 = Color3.fromRGB(22, 20, 30)
    b.BorderSizePixel = 0
    b.Text = "  " .. text
    b.TextColor3 = Color3.fromRGB(230, 230, 230)
    b.Font = Enum.Font.Gotham
    b.TextSize = 14
    b.TextXAlignment = Enum.TextXAlignment.Left
    b.AutoButtonColor = false
    b.LayoutOrder = nextOrder()
    b.Parent = scroll
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
    local ind = Instance.new("Frame")
    ind.Size = UDim2.new(0, 12, 0, 12)
    ind.Position = UDim2.new(1, -24, 0.5, -6)
    ind.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    ind.Parent = b
    Instance.new("UICorner", ind).CornerRadius = UDim.new(1, 0)
    local on = false
    b.MouseButton1Click:Connect(function()
        on = not on
        ind.BackgroundColor3 = on and Color3.fromRGB(0,255,120) or Color3.fromRGB(80,80,80)
        b.BackgroundColor3 = on and Color3.fromRGB(40,30,55) or Color3.fromRGB(22,20,30)
        pcall(cb, on)
    end)
    return b
end

local function makeSlider(text, min, max, def, cb)
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, 0, 0, 54)
    f.BackgroundColor3 = Color3.fromRGB(22, 20, 30)
    f.BorderSizePixel = 0
    f.LayoutOrder = nextOrder()
    f.Parent = scroll
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 6)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -20, 0, 22)
    lbl.Position = UDim2.new(0, 10, 0, 3)
    lbl.BackgroundTransparency = 1
    lbl.Text = text .. ": " .. def
    lbl.TextColor3 = Color3.fromRGB(230,230,230)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = f
    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(1, -20, 0, 8)
    bar.Position = UDim2.new(0, 10, 0, 34)
    bar.BackgroundColor3 = Color3.fromRGB(50, 45, 65)
    bar.BorderSizePixel = 0
    bar.Parent = f
    Instance.new("UICorner", bar).CornerRadius = UDim.new(1, 0)
    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((def - min)/(max - min), 0, 1, 0)
    fill.BackgroundColor3 = Color3.fromRGB(140, 50, 230)
    fill.BorderSizePixel = 0
    fill.Parent = bar
    Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)
    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 16, 0, 16)
    knob.Position = UDim2.new((def - min)/(max - min), -8, 0.5, -8)
    knob.BackgroundColor3 = Color3.new(1,1,1)
    knob.Parent = bar
    Instance.new("UICorner", knob).CornerRadius = UDim.new(1, 0)
    local sDrag = false
    local function update(input)
        local rel = math.clamp((input.Position.X - bar.AbsolutePosition.X) / math.max(bar.AbsoluteSize.X, 1), 0, 1)
        fill.Size = UDim2.new(rel, 0, 1, 0)
        knob.Position = UDim2.new(rel, -8, 0.5, -8)
        local v = math.floor(min + (max - min) * rel + 0.5)
        lbl.Text = text .. ": " .. v
        pcall(cb, v)
    end
    bar.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1
        or i.UserInputType == Enum.UserInputType.Touch then
            sDrag = true; update(i)
        end
    end)
    UIS.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1
        or i.UserInputType == Enum.UserInputType.Touch then sDrag = false end
    end)
    UIS.InputChanged:Connect(function(i)
        if sDrag and (i.UserInputType == Enum.UserInputType.MouseMovement
        or i.UserInputType == Enum.UserInputType.Touch) then update(i) end
    end)
    return f
end

local function makeButton(text, cb, color)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, 0, 0, 36)
    b.BackgroundColor3 = color or Color3.fromRGB(130, 0, 220)
    b.BorderSizePixel = 0
    b.Text = text
    b.TextColor3 = Color3.new(1,1,1)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 13
    b.AutoButtonColor = false
    b.LayoutOrder = nextOrder()
    b.Parent = scroll
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
    if cb then b.MouseButton1Click:Connect(function() pcall(cb) end) end
    return b
end

--=============================================================
-- UTILS
--=============================================================
function getAlivePlayers()
    local list = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LP and p.Character then
            local hum = p.Character:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then table.insert(list, p) end
        end
    end
    return list
end

function sameWorld(p)
    if p == LP then return false end
    if not p.Character or not LP.Character then return false end
    if LP.Team and p.Team and LP.Team ~= p.Team then return false end
    local h1 = LP.Character:FindFirstChild("HumanoidRootPart")
    local h2 = p.Character:FindFirstChild("HumanoidRootPart")
    if not h1 or not h2 then return false end
    return (h1.Position - h2.Position).Magnitude <= bringMaxDist
end

function closestSameWorld(exclude)
    local hrp = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end
    local best, bd = nil, math.huge
    for _, p in ipairs(getAlivePlayers()) do
        if p ~= exclude and sameWorld(p) then
            local t = p.Character:FindFirstChild("HumanoidRootPart")
            if t then
                local d = (t.Position - hrp.Position).Magnitude
                if d < bd then best, bd = p, d end
            end
        end
    end
    return best
end

local function raycastPlayer(maxDist)
    if not Camera then return nil end
    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = LP.Character and {LP.Character} or {}
    local result = Workspace:Raycast(Camera.CFrame.Position, Camera.CFrame.LookVector * (maxDist or 500), params)
    if result and result.Instance then
        local model = result.Instance:FindFirstAncestorOfClass("Model")
        if model then return Players:GetPlayerFromCharacter(model) end
    end
    return nil
end

--=============================================================
-- COMPRA FALSA (estilo Roblox)
--=============================================================
function showRobloxPurchasePrompt(itemName, price, creatorName, balance)
    balance = balance or 1000000
    local old = playerGui:FindFirstChild("DarkFakePurchase")
    if old then old:Destroy() end

    local sg = Instance.new("ScreenGui")
    sg.Name = "DarkFakePurchase"
    sg.ResetOnSpawn = false
    sg.DisplayOrder = 10000
    sg.IgnoreGuiInset = true
    sg.Parent = playerGui

    local overlay = Instance.new("Frame")
    overlay.Size = UDim2.new(1, 0, 1, 0)
    overlay.BackgroundColor3 = Color3.new(0, 0, 0)
    overlay.BackgroundTransparency = 0.4
    overlay.Parent = sg

    local dialog = Instance.new("Frame")
    dialog.Size = UDim2.new(0, 380, 0, 560)
    dialog.Position = UDim2.new(0.5, -190, 0.5, -280)
    dialog.BackgroundColor3 = Color3.fromRGB(35, 37, 39)
    dialog.BorderSizePixel = 0
    dialog.Parent = sg
    Instance.new("UICorner", dialog).CornerRadius = UDim.new(0, 12)

    local closeX = Instance.new("TextButton")
    closeX.Size = UDim2.new(0, 36, 0, 36)
    closeX.Position = UDim2.new(1, -46, 0, 10)
    closeX.BackgroundTransparency = 1
    closeX.Text = "×"
    closeX.TextColor3 = Color3.fromRGB(180, 180, 180)
    closeX.Font = Enum.Font.GothamBold
    closeX.TextSize = 24
    closeX.Parent = dialog
    closeX.MouseButton1Click:Connect(function() sg:Destroy() end)

    local header = Instance.new("TextLabel")
    header.Size = UDim2.new(1, 0, 0, 46)
    header.BackgroundTransparency = 1
    header.Text = "Roblox"
    header.TextColor3 = Color3.new(1,1,1)
    header.Font = Enum.Font.GothamBlack
    header.TextSize = 22
    header.Parent = dialog

    local card = Instance.new("Frame")
    card.Size = UDim2.new(1, -40, 0, 350)
    card.Position = UDim2.new(0, 20, 0, 56)
    card.BackgroundColor3 = Color3.new(1, 1, 1)
    card.BorderSizePixel = 0
    card.Parent = dialog
    Instance.new("UICorner", card).CornerRadius = UDim.new(0, 8)

    local thumb = Instance.new("Frame")
    thumb.Size = UDim2.new(1, -30, 0, 160)
    thumb.Position = UDim2.new(0, 15, 0, 15)
    thumb.BackgroundColor3 = Color3.fromRGB(230, 230, 230)
    thumb.BorderSizePixel = 0
    thumb.Parent = card
    Instance.new("UICorner", thumb).CornerRadius = UDim.new(0, 6)

    local thumbIcon = Instance.new("TextLabel")
    thumbIcon.Size = UDim2.new(1, 0, 1, 0)
    thumbIcon.BackgroundTransparency = 1
    thumbIcon.Text = "🎮"
    thumbIcon.TextSize = 80
    thumbIcon.Parent = thumb

    local nameLbl = Instance.new("TextLabel")
    nameLbl.Size = UDim2.new(1, -30, 0, 28)
    nameLbl.Position = UDim2.new(0, 15, 0, 185)
    nameLbl.BackgroundTransparency = 1
    nameLbl.Text = itemName
    nameLbl.TextColor3 = Color3.fromRGB(20, 20, 20)
    nameLbl.Font = Enum.Font.GothamBold
    nameLbl.TextSize = 17
    nameLbl.TextXAlignment = Enum.TextXAlignment.Left
    nameLbl.TextTruncate = Enum.TextTruncate.AtEnd
    nameLbl.Parent = card

    local creatorLbl = Instance.new("TextLabel")
    creatorLbl.Size = UDim2.new(1, -30, 0, 18)
    creatorLbl.Position = UDim2.new(0, 15, 0, 213)
    creatorLbl.BackgroundTransparency = 1
    creatorLbl.Text = "Por " .. (creatorName or "Roblox")
    creatorLbl.TextColor3 = Color3.fromRGB(120, 120, 120)
    creatorLbl.Font = Enum.Font.Gotham
    creatorLbl.TextSize = 12
    creatorLbl.TextXAlignment = Enum.TextXAlignment.Left
    creatorLbl.Parent = card

    local priceRow = Instance.new("Frame")
    priceRow.Size = UDim2.new(1, -30, 0, 44)
    priceRow.Position = UDim2.new(0, 15, 0, 240)
    priceRow.BackgroundColor3 = Color3.fromRGB(240, 240, 240)
    priceRow.BorderSizePixel = 0
    priceRow.Parent = card
    Instance.new("UICorner", priceRow).CornerRadius = UDim.new(0, 6)

    local priceLbl = Instance.new("TextLabel")
    priceLbl.Size = UDim2.new(1, -20, 1, 0)
    priceLbl.Position = UDim2.new(0, 10, 0, 0)
    priceLbl.BackgroundTransparency = 1
    priceLbl.Text = "💎 " .. tostring(price) .. " Robux"
    priceLbl.TextColor3 = Color3.fromRGB(0, 140, 60)
    priceLbl.Font = Enum.Font.GothamBold
    priceLbl.TextSize = 17
    priceLbl.TextXAlignment = Enum.TextXAlignment.Left
    priceLbl.Parent = priceRow

    local balanceLbl = Instance.new("TextLabel")
    balanceLbl.Size = UDim2.new(1, -30, 0, 18)
    balanceLbl.Position = UDim2.new(0, 15, 0, 292)
    balanceLbl.BackgroundTransparency = 1
    balanceLbl.Text = "Saldo: 💎 " .. tostring(balance) .. " Robux"
    balanceLbl.TextColor3 = Color3.fromRGB(100, 100, 100)
    balanceLbl.Font = Enum.Font.Gotham
    balanceLbl.TextSize = 12
    balanceLbl.TextXAlignment = Enum.TextXAlignment.Left
    balanceLbl.Parent = card

    local buyBtn = Instance.new("TextButton")
    buyBtn.Size = UDim2.new(1, -40, 0, 52)
    buyBtn.Position = UDim2.new(0, 20, 0, 420)
    buyBtn.BackgroundColor3 = Color3.fromRGB(0, 176, 111)
    buyBtn.Text = "Comprar por 💎 " .. tostring(price)
    buyBtn.TextColor3 = Color3.new(1,1,1)
    buyBtn.Font = Enum.Font.GothamBold
    buyBtn.TextSize = 16
    buyBtn.BorderSizePixel = 0
    buyBtn.Parent = dialog
    Instance.new("UICorner", buyBtn).CornerRadius = UDim.new(0, 8)

    local cancelBtn = Instance.new("TextButton")
    cancelBtn.Size = UDim2.new(1, -40, 0, 44)
    cancelBtn.Position = UDim2.new(0, 20, 0, 484)
    cancelBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
    cancelBtn.Text = "Cancelar"
    cancelBtn.TextColor3 = Color3.new(1,1,1)
    cancelBtn.Font = Enum.Font.GothamBold
    cancelBtn.TextSize = 15
    cancelBtn.BorderSizePixel = 0
    cancelBtn.Parent = dialog
    Instance.new("UICorner", cancelBtn).CornerRadius = UDim.new(0, 8)

    buyBtn.MouseButton1Click:Connect(function()
        sg:Destroy()
        local sg2 = Instance.new("ScreenGui")
        sg2.DisplayOrder = 10001
        sg2.IgnoreGuiInset = true
        sg2.Parent = playerGui
        local notif = Instance.new("Frame")
        notif.Size = UDim2.new(0, 340, 0, 70)
        notif.Position = UDim2.new(0.5, -170, 0, -100)
        notif.BackgroundColor3 = Color3.fromRGB(0, 176, 111)
        notif.BorderSizePixel = 0
        notif.Parent = sg2
        Instance.new("UICorner", notif).CornerRadius = UDim.new(0, 8)
        local t = Instance.new("TextLabel")
        t.Size = UDim2.new(1, -20, 1, 0)
        t.Position = UDim2.new(0, 10, 0, 0)
        t.BackgroundTransparency = 1
        t.Text = "✅ Compra realizada!\n" .. itemName .. " — 💎 " .. tostring(price)
        t.TextColor3 = Color3.new(1,1,1)
        t.Font = Enum.Font.GothamBold
        t.TextSize = 14
        t.TextWrapped = true
        t.Parent = notif
        TweenService:Create(notif, TweenInfo.new(0.4, Enum.EasingStyle.Quad),
            {Position = UDim2.new(0.5, -170, 0, 30)}):Play()
        task.wait(3)
        TweenService:Create(notif, TweenInfo.new(0.4, Enum.EasingStyle.Quad),
            {Position = UDim2.new(0.5, -170, 0, -100)}):Play()
        task.wait(0.5)
        sg2:Destroy()
    end)
    cancelBtn.MouseButton1Click:Connect(function() sg:Destroy() end)
end

function simulatePurchase()
    if not isUltimate then return end
    local old = playerGui:FindFirstChild("DarkFakePurchase")
    if old then old:Destroy() end

    local sg = Instance.new("ScreenGui")
    sg.Name = "DarkFakePurchase"
    sg.ResetOnSpawn = false
    sg.DisplayOrder = 10000
    sg.IgnoreGuiInset = true
    sg.Parent = playerGui

    local overlay = Instance.new("Frame")
    overlay.Size = UDim2.new(1,0,1,0)
    overlay.BackgroundColor3 = Color3.new(0,0,0)
    overlay.BackgroundTransparency = 0.5
    overlay.Parent = sg

    local dialog = Instance.new("Frame")
    dialog.Size = UDim2.new(0, 340, 0, 280)
    dialog.Position = UDim2.new(0.5, -170, 0.5, -140)
    dialog.BackgroundColor3 = Color3.fromRGB(35, 37, 39)
    dialog.BorderSizePixel = 0
    dialog.Parent = sg
    Instance.new("UICorner", dialog).CornerRadius = UDim.new(0, 12)

    local h = Instance.new("TextLabel")
    h.Size = UDim2.new(1, 0, 0, 40)
    h.BackgroundTransparency = 1
    h.Text = "🛒 Configurar compra"
    h.TextColor3 = Color3.new(1,1,1)
    h.Font = Enum.Font.GothamBold
    h.TextSize = 16
    h.Parent = dialog

    local nLbl = Instance.new("TextLabel")
    nLbl.Size = UDim2.new(1, -30, 0, 18)
    nLbl.Position = UDim2.new(0, 15, 0, 45)
    nLbl.BackgroundTransparency = 1
    nLbl.Text = "Nome do item:"
    nLbl.TextColor3 = Color3.fromRGB(200, 200, 200)
    nLbl.Font = Enum.Font.Gotham
    nLbl.TextSize = 12
    nLbl.TextXAlignment = Enum.TextXAlignment.Left
    nLbl.Parent = dialog

    local nBox = Instance.new("TextBox")
    nBox.Size = UDim2.new(1, -30, 0, 34)
    nBox.Position = UDim2.new(0, 15, 0, 65)
    nBox.BackgroundColor3 = Color3.fromRGB(240, 240, 240)
    nBox.BorderSizePixel = 0
    nBox.Text = "Item Raro"
    nBox.TextColor3 = Color3.fromRGB(30, 30, 30)
    nBox.Font = Enum.Font.Gotham
    nBox.TextSize = 13
    nBox.ClearTextOnFocus = false
    nBox.Parent = dialog
    Instance.new("UICorner", nBox).CornerRadius = UDim.new(0, 5)

    local pLbl = Instance.new("TextLabel")
    pLbl.Size = UDim2.new(1, -30, 0, 18)
    pLbl.Position = UDim2.new(0, 15, 0, 107)
    pLbl.BackgroundTransparency = 1
    pLbl.Text = "Preço (Robux):"
    pLbl.TextColor3 = Color3.fromRGB(200, 200, 200)
    pLbl.Font = Enum.Font.Gotham
    pLbl.TextSize = 12
    pLbl.TextXAlignment = Enum.TextXAlignment.Left
    pLbl.Parent = dialog

    local pBox = Instance.new("TextBox")
    pBox.Size = UDim2.new(1, -30, 0, 34)
    pBox.Position = UDim2.new(0, 15, 0, 127)
    pBox.BackgroundColor3 = Color3.fromRGB(240, 240, 240)
    pBox.BorderSizePixel = 0
    pBox.Text = "500"
    pBox.TextColor3 = Color3.fromRGB(30, 30, 30)
    pBox.Font = Enum.Font.Gotham
    pBox.TextSize = 13
    pBox.ClearTextOnFocus = false
    pBox.Parent = dialog
    Instance.new("UICorner", pBox).CornerRadius = UDim.new(0, 5)

    local cont = Instance.new("TextButton")
    cont.Size = UDim2.new(1, -30, 0, 44)
    cont.Position = UDim2.new(0, 15, 1, -58)
    cont.BackgroundColor3 = Color3.fromRGB(0, 176, 111)
    cont.Text = "Continuar"
    cont.TextColor3 = Color3.new(1,1,1)
    cont.Font = Enum.Font.GothamBold
    cont.TextSize = 15
    cont.BorderSizePixel = 0
    cont.Parent = dialog
    Instance.new("UICorner", cont).CornerRadius = UDim.new(0, 8)

    cont.MouseButton1Click:Connect(function()
        local itemName = nBox.Text ~= "" and nBox.Text or "Item"
        local price = tonumber(pBox.Text) or 500
        sg:Destroy()
        showRobloxPurchasePrompt(itemName, price, "Roblox", 1000000)
    end)
end

function openDiscordPopup()
    local old = playerGui:FindFirstChild("DarkDiscordPopup")
    if old then old:Destroy() end
    pcall(function() if setclipboard then setclipboard("https://discord.gg/hUnjpZDKrG") end end)
    local sg = Instance.new("ScreenGui")
    sg.Name = "DarkDiscordPopup"
    sg.ResetOnSpawn = false
    sg.DisplayOrder = 10000
    sg.IgnoreGuiInset = true
    sg.Parent = playerGui
    local overlay = Instance.new("Frame")
    overlay.Size = UDim2.new(1,0,1,0)
    overlay.BackgroundColor3 = Color3.new(0,0,0)
    overlay.BackgroundTransparency = 0.5
    overlay.Parent = sg
    local dialog = Instance.new("Frame")
    dialog.Size = UDim2.new(0, 340, 0, 210)
    dialog.Position = UDim2.new(0.5, -170, 0.5, -105)
    dialog.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    dialog.BorderSizePixel = 0
    dialog.Parent = sg
    Instance.new("UICorner", dialog).CornerRadius = UDim.new(0, 10)
    local st = Instance.new("UIStroke", dialog)
    st.Color = Color3.fromRGB(88, 101, 242)
    st.Thickness = 2
    local t = Instance.new("TextLabel")
    t.Size = UDim2.new(1, 0, 0, 44)
    t.BackgroundTransparency = 1
    t.Text = "💬 ENTRE NO NOSSO DISCORD"
    t.TextColor3 = Color3.fromRGB(88, 101, 242)
    t.Font = Enum.Font.GothamBlack
    t.TextSize = 16
    t.Parent = dialog
    local link = Instance.new("TextBox")
    link.Size = UDim2.new(1, -30, 0, 44)
    link.Position = UDim2.new(0, 15, 0, 55)
    link.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    link.BorderSizePixel = 0
    link.Text = "https://discord.gg/hUnjpZDKrG"
    link.TextColor3 = Color3.fromRGB(88, 101, 242)
    link.Font = Enum.Font.GothamBold
    link.TextSize = 13
    link.TextEditable = false
    link.ClearTextOnFocus = false
    link.Parent = dialog
    Instance.new("UICorner", link).CornerRadius = UDim.new(0, 6)
    local info = Instance.new("TextLabel")
    info.Size = UDim2.new(1, -30, 0, 30)
    info.Position = UDim2.new(0, 15, 0, 105)
    info.BackgroundTransparency = 1
    info.Text = "✅ Link copiado! Cole no navegador."
    info.TextColor3 = Color3.fromRGB(0, 220, 100)
    info.Font = Enum.Font.Gotham
    info.TextSize = 12
    info.Parent = dialog
    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0, 120, 0, 38)
    close.Position = UDim2.new(0.5, -60, 1, -50)
    close.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
    close.Text = "Fechar"
    close.TextColor3 = Color3.new(1,1,1)
    close.Font = Enum.Font.GothamBold
    close.TextSize = 14
    close.Parent = dialog
    Instance.new("UICorner", close).CornerRadius = UDim.new(0, 6)
    close.MouseButton1Click:Connect(function() sg:Destroy() end)
end

--=============================================================
-- SEÇÕES DA UI
--=============================================================
makeSection("▸ BÁSICO")
makeToggle("Aimbot",            function(v) S.Aimbot  = v end)
makeToggle("Spinbot",           function(v) S.Spinbot = v end)
makeSlider("Spin Speed", 1, 100, 20, function(v) S.SpinSpeed = v end)
makeToggle("No Walks (Noclip)", function(v) S.NoClip  = v end)

makeSection("▸ ACESSO")
local keySection = Instance.new("Frame")
keySection.Size = UDim2.new(1, 0, 0, 54)
keySection.BackgroundColor3 = Color3.fromRGB(22, 20, 30)
keySection.BorderSizePixel = 0
keySection.LayoutOrder = nextOrder()
keySection.Parent = scroll
Instance.new("UICorner", keySection).CornerRadius = UDim.new(0, 6)

local keyBox = Instance.new("TextBox")
keyBox.Size = UDim2.new(1, -80, 0, 36)
keyBox.Position = UDim2.new(0, 10, 0, 9)
keyBox.BackgroundColor3 = Color3.fromRGB(35, 30, 45)
keyBox.BorderSizePixel = 0
keyBox.Text = ""
keyBox.PlaceholderText = "Digite aqui..."
keyBox.TextColor3 = Color3.new(1,1,1)
keyBox.PlaceholderColor3 = Color3.fromRGB(120, 120, 120)
keyBox.Font = Enum.Font.Gotham
keyBox.TextSize = 13
keyBox.ClearTextOnFocus = false
keyBox.Parent = keySection
Instance.new("UICorner", keyBox).CornerRadius = UDim.new(0, 5)

local submit = Instance.new("TextButton")
submit.Size = UDim2.new(0, 60, 0, 36)
submit.Position = UDim2.new(1, -70, 0, 9)
submit.BackgroundColor3 = Color3.fromRGB(130, 0, 220)
submit.Text = "OK"
submit.TextColor3 = Color3.new(1,1,1)
submit.Font = Enum.Font.GothamBold
submit.TextSize = 14
submit.Parent = keySection
Instance.new("UICorner", submit).CornerRadius = UDim.new(0, 5)

local statusLbl = Instance.new("TextLabel")
statusLbl.Size = UDim2.new(1, -20, 0, 14)
statusLbl.Position = UDim2.new(0, 10, 0, 48)
statusLbl.BackgroundTransparency = 1
statusLbl.Text = ""
statusLbl.TextColor3 = Color3.fromRGB(255, 80, 80)
statusLbl.Font = Enum.Font.Gotham
statusLbl.TextSize = 11
statusLbl.TextXAlignment = Enum.TextXAlignment.Left
statusLbl.Parent = keySection

-- VIP
local vipElements = {}
local function regV(el) table.insert(vipElements, el); el.Visible = false; return el end
regV(makeSection("▸ VIP FUNCTIONS"))
regV(makeToggle("Spinbot V2",               function(v) S.SpinbotV2 = v end))
regV(makeSlider("Spin V2 Speed", 1, 1000, 200, function(v) S.SpinbotV2Speed = v end))
regV(makeSlider("WalkSpeed", 1, 1000, 16, function(v) S.WalkSpeed = v end))
regV(makeToggle("Tiro no Walk (Wallbang)",  function(v) S.Wallbang = v end))
regV(makeToggle("ESP",                      function(v) S.ESP = v end))
regV(makeSlider("ESP Range (m)", 1, 10000, 500, function(v) S.ESPRange = v end))
regV(makeToggle("Fly",                      function(v) S.Fly = v end))
regV(makeSlider("Fly Speed", 1, 1000, 100, function(v) S.FlySpeed = v end))
regV(makeToggle("No Void (chão invisível)",  function(v) S.NoVoid = v end))

-- ULTIMATE
local ultElements = {}
local function regU(el) table.insert(ultElements, el); el.Visible = false; return el end
regU(makeSection("▸ ULTIMATE VIP (Dev)", Color3.fromRGB(255, 60, 60)))
regU(makeToggle("Kill Aura (click/arma)",   function(v) S.KillAura = v end))
regU(makeSlider("KA Dano", 1, 1000000, 1000000, function(v) S.KillAuraDamage = v end))
regU(makeSlider("KA Range (m)", 1, 10000, 500, function(v) S.KillAuraRange = v end))
regU(makeToggle("ESP V2 (HUD completo)",    function(v) S.ESPv2 = v end))
regU(makeToggle("Hit Kill (1 clique)",      function(v) S.HitKill = v end))
regU(makeToggle("Ghost Mode (invisível)",   function(v) S.GhostMode = v end))
regU(makeToggle("Bring Player (Q)",         function(v)
    S.BringPlayer = v
    if not v then bringTarget = nil end
end))
regU(makeButton("🔄 Pular Bring (trocar alvo)", function()
    if not isUltimate or not S.BringPlayer then return end
    bringTarget = closestSameWorld(bringTarget)
end))
regU(makeSlider("Jump Power", 1, 100, 50, function(v) S.JumpPower = v end))
regU(makeToggle("No Recharge (sem cooldown)", function(v) S.NoRecharge = v end))
regU(makeButton("💎 Simular Compra (Robux)", function() simulatePurchase() end, Color3.fromRGB(0, 150, 80)))

-- LEGEND
local legElements = {}
local function regL(el) table.insert(legElements, el); el.Visible = false; return el end
regL(makeSection("▸ LEGEND VIP 🌟", Color3.fromRGB(255, 200, 0)))
regL(makeToggle("Bot de Puxada (aim assist)", function(v) S.BotPuxada = v end))

local partFrame = Instance.new("Frame")
partFrame.Size = UDim2.new(1, 0, 0, 34)
partFrame.BackgroundColor3 = Color3.fromRGB(22, 20, 30)
partFrame.BorderSizePixel = 0
partFrame.LayoutOrder = nextOrder()
partFrame.Parent = scroll
Instance.new("UICorner", partFrame).CornerRadius = UDim.new(0, 6)
regL(partFrame)

local partLabels = {Head="🎯 Cabeça", Chest="🎯 Peito", Legs="🎯 Pernas"}
local partKeys = {"Head", "Chest", "Legs"}
local partBtns = {}
for i, pk in ipairs(partKeys) do
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1/3, -6, 1, -8)
    b.Position = UDim2.new((i-1)/3 + 0.01, 0, 0, 4)
    b.BackgroundColor3 = (i == 1) and Color3.fromRGB(200, 160, 0) or Color3.fromRGB(40, 30, 55)
    b.Text = partLabels[pk]
    b.TextColor3 = Color3.new(1,1,1)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 11
    b.AutoButtonColor = false
    b.Parent = partFrame
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 4)
    b.MouseButton1Click:Connect(function()
        S.BotPuxadaPart = pk
        for j, other in ipairs(partBtns) do
            other.BackgroundColor3 = (j == i) and Color3.fromRGB(200, 160, 0) or Color3.fromRGB(40, 30, 55)
        end
    end)
    partBtns[i] = b
end

makeSection("▸ COMUNIDADE")
makeButton("💬 ENTRAR NO DISCORD", function() openDiscordPopup() end, Color3.fromRGB(88, 101, 242))

-- Submit key
local function unlockVIP()
    keySection.Visible = false
    for _, el in ipairs(vipElements) do el.Visible = true end
    task.defer(refreshCanvas)
end
local function unlockUlt()
    keySection.Visible = false
    for _, el in ipairs(vipElements) do el.Visible = true end
    for _, el in ipairs(ultElements) do el.Visible = true end
    task.defer(refreshCanvas)
end
local function unlockLeg()
    keySection.Visible = false
    for _, el in ipairs(vipElements) do el.Visible = true end
    for _, el in ipairs(ultElements) do el.Visible = true end
    for _, el in ipairs(legElements) do el.Visible = true end
    task.defer(refreshCanvas)
end

local function submitKey()
    local txt = keyBox.Text
    if txt == KEY_LEG then
        isVIP, isUltimate, isLegend = true, true, true
        unlockLeg()
    elseif txt == KEY_ULT then
        isVIP, isUltimate = true, true
        unlockUlt()
    elseif txt == KEY_VIP then
        isVIP = true
        unlockVIP()
    else
        statusLbl.Text = "❌ Key inválida!"
        task.wait(1.5)
        statusLbl.Text = ""
    end
end
submit.MouseButton1Click:Connect(submitKey)
keyBox.FocusLost:Connect(function(e) if e then submitKey() end end)

--=============================================================
-- RENDER STEPPED (WalkSpeed + Fly) — ANTES da física
--=============================================================
RunService.RenderStepped:Connect(function()
    local char = LP.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hum then return end

    -- WalkSpeed
    if isVIP and hum.WalkSpeed ~= S.WalkSpeed then
        pcall(function() hum.WalkSpeed = S.WalkSpeed end)
    end

    -- Fly
    if isVIP and S.Fly and hrp then
        if not hum.PlatformStand then
            pcall(function() hum.PlatformStand = true end)
        end
        if Camera then
            local cam = Camera.CFrame
            local dir = Vector3.new(0, 0, 0)
            if UIS:IsKeyDown(Enum.KeyCode.W) then dir = dir + cam.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.S) then dir = dir - cam.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.A) then dir = dir - cam.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.D) then dir = dir + cam.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then dir = dir + Vector3.new(0,1,0) end
            if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then dir = dir - Vector3.new(0,1,0) end
            if dir.Magnitude > 0 then dir = dir.Unit end
            local vel = dir * S.FlySpeed
            pcall(function() hrp.AssemblyLinearVelocity = vel end)
            pcall(function() hrp.Velocity = vel end)
        end
    elseif hum.PlatformStand and not S.Fly then
        pcall(function() hum.PlatformStand = false end)
    end
end)

--=============================================================
-- NOCLIP (Stepped)
--=============================================================
RunService.Stepped:Connect(function()
    if not S.NoClip then return end
    local char = LP.Character
    if not char then return end
    for _, v in ipairs(char:GetDescendants()) do
        if v:IsA("BasePart") and v.CanCollide then
            v.CanCollide = false
        end
    end
end)

--=============================================================
-- BRING (Stepped — antes da física)
--=============================================================
RunService.Stepped:Connect(function()
    if not (isUltimate and S.BringPlayer and bringTarget) then return end
    local char = LP.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    if not sameWorld(bringTarget) then
        bringTarget = nil
        return
    end
    local tChar = bringTarget.Character
    local tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")
    local tHrp = tChar and tChar:FindFirstChild("HumanoidRootPart")
    if not (tHum and tHum.Health > 0 and tHrp) then
        bringTarget = nil
        return
    end
    hrp.CFrame = tHrp.CFrame * CFrame.new(0, 0, 2.5)
    pcall(function()
        hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        hrp.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
    end)
end)

--=============================================================
-- AIMBOT + BOT PUXADA
--=============================================================
local cameraLocked, prevCamType = false, nil
local function lockCamera()
    if not cameraLocked and Camera then
        prevCamType = Camera.CameraType
        Camera.CameraType = Enum.CameraType.Scriptable
        cameraLocked = true
    end
end
local function unlockCamera()
    if cameraLocked and Camera then
        Camera.CameraType = prevCamType or Enum.CameraType.Custom
        cameraLocked = false
    end
end

local function getAimbotTarget()
    local best, bd = nil, math.huge
    local camPos = Camera.CFrame.Position
    for _, p in ipairs(getAlivePlayers()) do
        local hrp = p.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            local _, on = Camera:WorldToViewportPoint(hrp.Position)
            if on then
                local d = (hrp.Position - camPos).Magnitude
                if d < bd then best, bd = p, d end
            end
        end
    end
    return best
end

local function getPartByMode(mode)
    local best, bd = nil, math.huge
    local camPos = Camera.CFrame.Position
    for _, p in ipairs(getAlivePlayers()) do
        local part
        if mode == "Head" then
            part = p.Character:FindFirstChild("Head")
        elseif mode == "Chest" then
            part = p.Character:FindFirstChild("UpperTorso") or p.Character:FindFirstChild("Torso")
        elseif mode == "Legs" then
            part = p.Character:FindFirstChild("LeftUpperLeg") or p.Character:FindFirstChild("Left Leg")
        end
        if part then
            local _, on = Camera:WorldToViewportPoint(part.Position)
            if on then
                local d = (part.Position - camPos).Magnitude
                if d < bd then best, bd = part, d end
            end
        end
    end
    return best
end

pcall(function()
    RunService:BindToRenderStep("DarkAimbot", Enum.RenderPriority.Camera.Value + 1, function()
        if not Camera then return end
        local target = nil
        local shouldLock = false
        if S.Aimbot then
            shouldLock = true
            local p = getAimbotTarget()
            if p then target = p.Character:FindFirstChild("HumanoidRootPart") end
        end
        if aimAssistActive and isLegend and S.BotPuxada then
            shouldLock = true
            target = getPartByMode(S.BotPuxadaPart)
        end
        if shouldLock then
            lockCamera()
            if target then
                Camera.CFrame = CFrame.lookAt(Camera.CFrame.Position, target.Position)
            end
        else
            unlockCamera()
        end
    end)
end)

--=============================================================
-- GHOST / NO VOID / NO RECHARGE
--=============================================================
local function applyGhost(state)
    local char = LP.Character
    if not char then return end
    for _, v in ipairs(char:GetDescendants()) do
        if v:IsA("BasePart") then
            v.LocalTransparencyModifier = state and 1 or 0
        elseif v:IsA("Decal") or v:IsA("Texture") then
            v.Transparency = state and 1 or 0
        end
    end
end

local safePos = nil
local function updateNoVoid(hrp)
    if not hrp then return end
    local ray = Ray.new(hrp.Position, Vector3.new(0, -15, 0))
    local hit = Workspace:FindPartOnRay(ray, LP.Character)
    if hit then safePos = hrp.Position + Vector3.new(0, 3, 0) end
    if hrp.Position.Y < -200 then
        hrp.CFrame = CFrame.new(safePos or Vector3.new(0, 50, 0))
    end
end

local function applyNoRecharge()
    if not isUltimate or not S.NoRecharge then return end
    local char = LP.Character
    if not char then return end
    for _, v in ipairs(char:GetChildren()) do
        if v:IsA("Tool") then
            pcall(function()
                for _, prop in ipairs({"Cooldown","CooldownTime","ReloadTime","FireRate",
                                       "AttackCooldown","LastActivation","Reloading","CanReload",
                                       "NextFire","LastShot"}) do
                    if v[prop] ~= nil then
                        if type(v[prop]) == "number" then v[prop] = 0
                        elseif type(v[prop]) == "boolean" then v[prop] = false end
                    end
                end
            end)
        end
    end
end

--=============================================================
-- LOOP PRINCIPAL
--=============================================================
local ghostApplied = false
RunService.Heartbeat:Connect(function(dt)
    local char = LP.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")

    if hrp and (S.Spinbot or S.SpinbotV2) then
        local sp = S.SpinbotV2 and S.SpinbotV2Speed or S.SpinSpeed
        hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(sp) * (dt * 60), 0)
    end

    if isUltimate and hum then
        if not hum.UseJumpPower then hum.UseJumpPower = true end
        if hum.JumpPower ~= S.JumpPower then hum.JumpPower = S.JumpPower end
    end

    if isVIP and S.NoVoid then updateNoVoid(hrp) end
    if isUltimate and S.NoRecharge then applyNoRecharge() end

    if isUltimate then
        if S.GhostMode ~= ghostApplied then
            applyGhost(S.GhostMode)
            ghostApplied = S.GhostMode
        elseif S.GhostMode then
            applyGhost(true)
        end
    end
end)

LP.CharacterAdded:Connect(function()
    task.wait(0.5)
    if isUltimate and S.GhostMode then applyGhost(true) end
end)

--=============================================================
-- INPUT Q + CLICKS
--=============================================================
UIS.InputBegan:Connect(function(i, gpe)
    if gpe then return end
    if i.KeyCode == Enum.KeyCode.Q and S.BringPlayer and isUltimate then
        bringTarget = closestSameWorld(bringTarget)
    end
end)

--=============================================================
-- HIT KILL + KILL AURA
--=============================================================
local function doHitKill()
    if not isUltimate or not S.HitKill then return end
    local target = raycastPlayer(500)
    if not target or not target.Character then return end
    local tChar = target.Character
    local tHum = tChar:FindFirstChildOfClass("Humanoid")
    if tHum then
        pcall(function() tHum.Health = 0 end)
        pcall(function() tHum:TakeDamage(1e7) end)
        pcall(function() tHum:ChangeState(Enum.HumanoidStateType.Dead) end)
    end
    for _, joint in ipairs(tChar:GetDescendants()) do
        if joint:IsA("Motor6D") or joint:IsA("Weld") then
            pcall(function() joint:Destroy() end)
        end
    end
end

local function doKillAura()
    if not isUltimate or not S.KillAura then return end
    local hrp = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _, p in ipairs(getAlivePlayers()) do
        local tHrp = p.Character:FindFirstChild("HumanoidRootPart")
        local tHum = p.Character:FindFirstChildOfClass("Humanoid")
        if tHrp and tHum and (tHrp.Position - hrp.Position).Magnitude <= S.KillAuraRange then
            pcall(function() tHum.Health = 0 end)
            pcall(function() tHum:TakeDamage(S.KillAuraDamage) end)
        end
    end
end

UIS.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.UserInputType == Enum.UserInputType.MouseButton1
    or input.UserInputType == Enum.UserInputType.Touch then
        if isLegend and S.BotPuxada then aimAssistActive = true end
        doKillAura()
        doHitKill()
    end
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
    or input.UserInputType == Enum.UserInputType.Touch then
        aimAssistActive = false
    end
end)

local function hookTool(tool)
    tool.Activated:Connect(function()
        if not isUltimate then return end
        doKillAura()
        doHitKill()
        if isLegend and S.BotPuxada then
            aimAssistActive = true
            task.delay(0.35, function() aimAssistActive = false end)
        end
        if S.NoRecharge then applyNoRecharge() end
    end)
end

local function watchChar(char)
    for _, v in ipairs(char:GetChildren()) do
        if v:IsA("Tool") then hookTool(v) end
    end
    char.ChildAdded:Connect(function(c)
        if c:IsA("Tool") then hookTool(c) end
    end)
end
if LP.Character then watchChar(LP.Character) end
LP.CharacterAdded:Connect(watchChar)

--=============================================================
-- ESP BÁSICO
--=============================================================
local espData = {}
local function clearESP(p)
    if espData[p] then
        for _, obj in pairs(espData[p]) do
            if obj and obj.Destroy then obj:Destroy() end
        end
        espData[p] = nil
    end
end
local function createESP(p)
    if espData[p] or not p.Character then return end
    local hl = Instance.new("Highlight")
    hl.FillColor = Color3.fromRGB(200, 0, 0)
    hl.FillTransparency = 0.55
    hl.OutlineColor = Color3.new(1,1,1)
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    hl.Adornee = p.Character
    hl.Parent = p.Character
    local bb = Instance.new("BillboardGui")
    bb.Size = UDim2.new(0, 140, 0, 24)
    bb.StudsOffset = Vector3.new(0, 3, 0)
    bb.AlwaysOnTop = true
    bb.Adornee = p.Character:FindFirstChild("Head") or p.Character:FindFirstChild("HumanoidRootPart")
    bb.Parent = p.Character
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, 0, 1, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = p.Name
    lbl.TextColor3 = Color3.fromRGB(255, 60, 60)
    lbl.TextStrokeTransparency = 0
    lbl.TextStrokeColor3 = Color3.new(0,0,0)
    lbl.Font = Enum.Font.GothamBold
    lbl.TextSize = 13
    lbl.Parent = bb
    espData[p] = {hl, bb, lbl}
end

RunService.Heartbeat:Connect(function()
    if not Camera then return end
    if S.ESPv2 or not S.ESP then
        if next(espData) then for p in pairs(espData) do clearESP(p) end end
        return
    end
    local camPos = Camera.CFrame.Position
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LP then
            if not p.Character then
                clearESP(p)
            else
                local hrp = p.Character:FindFirstChild("HumanoidRootPart")
                if not hrp then
                    clearESP(p)
                else
                    local d = (hrp.Position - camPos).Magnitude
                    if d <= S.ESPRange then
                        if not espData[p] then createESP(p) end
                        if espData[p] and espData[p][3] then
                            espData[p][3].Text = p.Name .. " [" .. math.floor(d) .. "m]"
                            if espData[p][2] then
                                espData[p][2].Adornee = p.Character:FindFirstChild("Head") or hrp
                            end
                        end
                    else
                        clearESP(p)
                    end
                end
            end
        end
    end
end)

--=============================================================
-- ESP V2
--=============================================================
local espv2Gui = Instance.new("ScreenGui")
espv2Gui.Name = "DarkESPv2"
espv2Gui.ResetOnSpawn = false
espv2Gui.IgnoreGuiInset = true
espv2Gui.DisplayOrder = 100
espv2Gui.Parent = playerGui

local R15_SKEL = {
    {"Head","UpperTorso"}, {"UpperTorso","LowerTorso"},
    {"UpperTorso","LeftUpperArm"}, {"LeftUpperArm","LeftLowerArm"}, {"LeftLowerArm","LeftHand"},
    {"UpperTorso","RightUpperArm"}, {"RightUpperArm","RightLowerArm"}, {"RightLowerArm","RightHand"},
    {"LowerTorso","LeftUpperLeg"}, {"LeftUpperLeg","LeftLowerLeg"}, {"LeftLowerLeg","LeftFoot"},
    {"LowerTorso","RightUpperLeg"}, {"RightUpperLeg","RightLowerLeg"}, {"RightLowerLeg","RightFoot"},
}
local R6_SKEL = {
    {"Head","Torso"}, {"Torso","Left Arm"}, {"Torso","Right Arm"},
    {"Torso","Left Leg"}, {"Torso","Right Leg"},
}
local espv2Data = {}

local function destroyESPv2(p)
    if espv2Data[p] then
        local d = espv2Data[p]
        if d.container then d.container:Destroy() end
        if d.bb then d.bb:Destroy() end
        if d.tracer then d.tracer:Destroy() end
        if d.hl then d.hl:Destroy() end
        espv2Data[p] = nil
    end
end

local function createESPv2(p)
    if espv2Data[p] or not p.Character then return end
    local d = { lines = {} }

    d.hl = Instance.new("Highlight")
    d.hl.FillColor = Color3.fromRGB(0, 200, 255)
    d.hl.FillTransparency = 0.6
    d.hl.OutlineColor = Color3.fromRGB(0, 255, 255)
    d.hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    d.hl.Adornee = p.Character
    d.hl.Parent = p.Character

    d.bb = Instance.new("BillboardGui")
    d.bb.Size = UDim2.new(0, 160, 0, 44)
    d.bb.StudsOffset = Vector3.new(0, 3.2, 0)
    d.bb.AlwaysOnTop = true
    d.bb.Adornee = p.Character:FindFirstChild("Head") or p.Character:FindFirstChild("HumanoidRootPart")
    d.bb.Parent = p.Character

    d.nameLabel = Instance.new("TextLabel")
    d.nameLabel.Size = UDim2.new(1, 0, 0, 20)
    d.nameLabel.BackgroundTransparency = 1
    d.nameLabel.Text = p.Name
    d.nameLabel.TextColor3 = Color3.fromRGB(0, 255, 255)
    d.nameLabel.TextStrokeTransparency = 0
    d.nameLabel.TextStrokeColor3 = Color3.new(0,0,0)
    d.nameLabel.Font = Enum.Font.GothamBold
    d.nameLabel.TextSize = 13
    d.nameLabel.Parent = d.bb

    local hbBg = Instance.new("Frame")
    hbBg.Size = UDim2.new(1, 0, 0, 8)
    hbBg.Position = UDim2.new(0, 0, 0, 24)
    hbBg.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    hbBg.BorderSizePixel = 0
    hbBg.Parent = d.bb

    d.hpFill = Instance.new("Frame")
    d.hpFill.Size = UDim2.new(1, 0, 1, 0)
    d.hpFill.BackgroundColor3 = Color3.fromRGB(0, 255, 80)
    d.hpFill.BorderSizePixel = 0
    d.hpFill.Parent = hbBg

    d.hpLbl = Instance.new("TextLabel")
    d.hpLbl.Size = UDim2.new(1, 0, 0, 12)
    d.hpLbl.Position = UDim2.new(0, 0, 0, 33)
    d.hpLbl.BackgroundTransparency = 1
    d.hpLbl.Text = "100 HP"
    d.hpLbl.TextColor3 = Color3.new(1,1,1)
    d.hpLbl.TextStrokeTransparency = 0
    d.hpLbl.TextStrokeColor3 = Color3.new(0,0,0)
    d.hpLbl.Font = Enum.Font.GothamBold
    d.hpLbl.TextSize = 11
    d.hpLbl.Parent = d.bb

    d.container = Instance.new("Frame")
    d.container.Size = UDim2.new(1, 0, 1, 0)
    d.container.BackgroundTransparency = 1
    d.container.Parent = espv2Gui

    for i = 1, 16 do
        local ln = Instance.new("Frame")
        ln.Size = UDim2.new(0, 0, 0, 1)
        ln.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
        ln.BorderSizePixel = 0
        ln.AnchorPoint = Vector2.new(0.5, 0.5)
        ln.Visible = false
        ln.Parent = d.container
        d.lines[i] = ln
    end

    d.tracer = Instance.new("Frame")
    d.tracer.Size = UDim2.new(0, 0, 0, 1)
    d.tracer.BackgroundColor3 = Color3.fromRGB(255, 0, 255)
    d.tracer.BorderSizePixel = 0
    d.tracer.AnchorPoint = Vector2.new(0.5, 0.5)
    d.tracer.Parent = espv2Gui

    espv2Data[p] = d
end

local function setLine(ln, a, b)
    if not a or not b then ln.Visible = false; return end
    local delta = b - a
    local dist = delta.Magnitude
    if dist < 1 then ln.Visible = false; return end
    local center = (a + b) * 0.5
    local ang = math.deg(math.atan2(delta.Y, delta.X))
    ln.Size = UDim2.new(0, dist, 0, 1.5)
    ln.Position = UDim2.new(0, center.X, 0, center.Y)
    ln.Rotation = ang
    ln.Visible = true
end

RunService.Heartbeat:Connect(function()
    if not Camera then return end
    if not S.ESPv2 or not isUltimate then
        if next(espv2Data) then
            for p in pairs(espv2Data) do destroyESPv2(p) end
        end
        return
    end
    local camPos = Camera.CFrame.Position
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LP then
            local char = p.Character
            if not char then
                destroyESPv2(p)
            else
                local hrp = char:FindFirstChild("HumanoidRootPart")
                local hum = char:FindFirstChildOfClass("Humanoid")
                if not hrp or not hum then
                    destroyESPv2(p)
                else
                    local dist = (hrp.Position - camPos).Magnitude
                    if dist > S.ESPRange then
                        destroyESPv2(p)
                    else
                        if not espv2Data[p] then createESPv2(p) end
                        local data = espv2Data[p]
                        if data then
                            if data.bb then
                                data.bb.Adornee = char:FindFirstChild("Head") or hrp
                            end
                            local hp = math.max(hum.Health, 0)
                            local hpMax = math.max(hum.MaxHealth, 1)
                            local pct = hp / hpMax
                            if data.hpFill then
                                data.hpFill.Size = UDim2.new(pct, 0, 1, 0)
                                data.hpFill.BackgroundColor3 = Color3.fromRGB(
                                    math.floor(255 * (1 - pct)),
                                    math.floor(255 * pct),
                                    0)
                            end
                            if data.hpLbl then
                                data.hpLbl.Text = math.floor(hp) .. " / " .. math.floor(hpMax) .. " HP"
                            end
                            if data.nameLabel then
                                data.nameLabel.Text = p.Name .. " [" .. math.floor(dist) .. "m]"
                            end

                            local skel = (hum.RigType == Enum.HumanoidRigType.R15) and R15_SKEL or R6_SKEL
                            local lineIdx = 1
                            for _, conn in ipairs(skel) do
                                local a = char:FindFirstChild(conn[1])
                                local b = char:FindFirstChild(conn[2])
                                if a and b and data.lines[lineIdx] then
                                    local pa, onA = Camera:WorldToViewportPoint(a.Position)
                                    local pb, onB = Camera:WorldToViewportPoint(b.Position)
                                    if onA and onB and pa.Z > 0 and pb.Z > 0 then
                                        setLine(data.lines[lineIdx],
                                            Vector2.new(pa.X, pa.Y),
                                            Vector2.new(pb.X, pb.Y))
                                    else
                                        data.lines[lineIdx].Visible = false
                                    end
                                elseif data.lines[lineIdx] then
                                    data.lines[lineIdx].Visible = false
                                end
                                lineIdx = lineIdx + 1
                            end
                            for i = lineIdx, #data.lines do
                                data.lines[i].Visible = false
                            end

                            if data.tracer then
                                local pos, on = Camera:WorldToViewportPoint(hrp.Position)
                                if on and pos.Z > 0 then
                                    setLine(data.tracer,
                                        Vector2.new(Camera.ViewportSize.X / 2, 0),
                                        Vector2.new(pos.X, pos.Y))
                                else
                                    data.tracer.Visible = false
                                end
                            end
                        end
                    end
                end
            end
        end
    end
end)

--=============================================================
-- FINAL
--=============================================================
task.defer(function()
    task.wait(0.3)
    refreshCanvas()
end)

print("[DarkPanel v6] ✅ SCRIPT COMPLETO CARREGADO")
