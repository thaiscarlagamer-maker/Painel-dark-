--=============================================================
-- 🌑 DARK PANEL - MM2 ADMIN  |  single LocalScript / executor
-- FIX: CanvasSize manual (compatível com qualquer executor)
--=============================================================
if not game:IsLoaded() then game.Loaded:Wait() end

local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace        = game:GetService("Workspace")

local LP     = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
local Mouse  = LP:GetMouse()

local VIP_KEY = "DARK"
local isVIP   = false

local S = {
    Aimbot=false, Spinbot=false, SpinSpeed=20, NoClip=false,
    SpinbotV2=false, SpinbotV2Speed=200, WalkSpeed=16,
    Wallbang=false, ESP=false, ESPRange=500,
}

--=============================================================
-- GUI ROOT
--=============================================================
local gui = Instance.new("ScreenGui")
gui.Name = "DarkPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.DisplayOrder = 999
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = LP:WaitForChild("PlayerGui")

local main = Instance.new("Frame")
main.Size            = UDim2.new(0, 340, 0, 520)
main.Position        = UDim2.new(0, 20, 0.5, -260)
main.BackgroundColor3= Color3.fromRGB(12, 12, 18)
main.BorderSizePixel = 0
main.Active          = true
main.Visible         = true
main.Parent          = gui
Instance.new("UICorner", main).CornerRadius = UDim.new(0, 10)

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(130, 0, 220)
stroke.Thickness = 2
stroke.Parent = main

-- title bar
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 42)
titleBar.BackgroundColor3 = Color3.fromRGB(25, 20, 35)
titleBar.BorderSizePixel = 0
titleBar.Active = true
titleBar.Parent = main
Instance.new("UICorner", titleBar).CornerRadius = UDim.new(0, 10)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -70, 1, 0)
title.Position = UDim2.new(0, 15, 0, 0)
title.BackgroundTransparency = 1
title.Text = "🌑  DARK PANEL"
title.TextColor3 = Color3.fromRGB(200, 100, 255)
title.Font = Enum.Font.GothamBlack
title.TextSize = 18
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

local openBtn = Instance.new("TextButton")
openBtn.Size = UDim2.new(0, 44, 0, 44)
openBtn.Position = UDim2.new(0, 20, 0, 20)
openBtn.BackgroundColor3 = Color3.fromRGB(30, 20, 45)
openBtn.Text = "🌑"
openBtn.TextSize = 22
openBtn.Visible = false
openBtn.Parent = gui
Instance.new("UICorner", openBtn).CornerRadius = UDim.new(1, 0)

-- drag
local dragging, dragStart, startPos
titleBar.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging, dragStart, startPos = true, i.Position, main.Position
    end
end)
UserInputService.InputChanged:Connect(function(i)
    if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
        local d = i.Position - dragStart
        main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X,
                                  startPos.Y.Scale, startPos.Y.Offset + d.Y)
    end
end)
UserInputService.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
end)
closeBtn.MouseButton1Click:Connect(function() main.Visible = false; openBtn.Visible = true end)
openBtn.MouseButton1Click:Connect(function()  main.Visible = true;  openBtn.Visible = false end)

--=============================================================
-- SCROLLING FRAME  (CanvasSize manual → sem bugs)
--=============================================================
local scroll = Instance.new("ScrollingFrame")
scroll.Size                  = UDim2.new(1, -20, 1, -55)
scroll.Position              = UDim2.new(0, 10, 0, 48)
scroll.BackgroundTransparency= 1
scroll.BorderSizePixel       = 0
scroll.ScrollBarThickness    = 5
scroll.ScrollBarImageColor3  = Color3.fromRGB(130, 0, 220)
scroll.ClipsDescendants      = true
scroll.CanvasSize            = UDim2.new(0, 0, 0, 1000)  -- fallback inicial
scroll.Parent                = main

local layout = Instance.new("UIListLayout")
layout.Padding    = UDim.new(0, 6)
layout.SortOrder  = Enum.SortOrder.LayoutOrder
layout.Parent     = scroll

local pad = Instance.new("UIPadding")
pad.PaddingTop    = UDim.new(0, 5)
pad.PaddingBottom = UDim.new(0, 10)
pad.Parent        = scroll

-- 🔑 FIX PRINCIPAL — recalcula CanvasSize sempre
local function refreshCanvas()
    local ok, sz = pcall(function() return layout.AbsoluteContentSize end)
    if not ok or not sz then return end
    local h = math.max(sz.Y + 20, 200)
    scroll.CanvasSize = UDim2.new(0, 0, 0, h)
end

layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(refreshCanvas)
scroll.ChildAdded:Connect(function(c)
    if c:IsA("GuiObject") then
        task.defer(refreshCanvas)
    end
end)
scroll.ChildRemoved:Connect(refreshCanvas)

task.spawn(function()
    for i = 1, 6 do
        task.wait(0.15)
        refreshCanvas()
    end
end)

--=============================================================
-- HELPERS DE UI
--=============================================================
local order = 0
local function nextOrder() order = order + 1; return order end

local function makeSection(text)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, 0, 0, 24)
    l.BackgroundTransparency = 1
    l.Text = text
    l.TextColor3 = Color3.fromRGB(170, 100, 240)
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
        b.BackgroundColor3   = on and Color3.fromRGB(40,30,55)   or Color3.fromRGB(22,20,30)
        cb(on)
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

    local dragging = false
    local function update(input)
        local rel = math.clamp((input.Position.X - bar.AbsolutePosition.X) / math.max(bar.AbsoluteSize.X, 1), 0, 1)
        fill.Size = UDim2.new(rel, 0, 1, 0)
        knob.Position = UDim2.new(rel, -8, 0.5, -8)
        local v = math.floor(min + (max - min) * rel + 0.5)
        lbl.Text = text .. ": " .. v
        cb(v)
    end
    bar.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            update(i)
        end
    end)
    UserInputService.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
    UserInputService.InputChanged:Connect(function(i)
        if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then update(i) end
    end)
    return f
end

--=============================================================
-- SEÇÃO BÁSICA
--=============================================================
makeSection("▸ BÁSICO")
makeToggle("Aimbot",             function(v) S.Aimbot  = v end)
makeToggle("Spinbot",            function(v) S.Spinbot = v end)
makeSlider("Spin Speed", 1, 100, 20, function(v) S.SpinSpeed = v end)
makeToggle("No Walks (Noclip)",  function(v) S.NoClip  = v end)

--=============================================================
-- SEÇÃO KEY VIP
--=============================================================
makeSection("▸ ACESSO VIP")

local keySection = Instance.new("Frame")
keySection.Size = UDim2.new(1, 0, 0, 82)
keySection.BackgroundColor3 = Color3.fromRGB(22, 20, 30)
keySection.BorderSizePixel = 0
keySection.LayoutOrder = nextOrder()
keySection.Parent = scroll
Instance.new("UICorner", keySection).CornerRadius = UDim.new(0, 6)

local keyLbl = Instance.new("TextLabel")
keyLbl.Size = UDim2.new(1, -20, 0, 20)
keyLbl.Position = UDim2.new(0, 10, 0, 5)
keyLbl.BackgroundTransparency = 1
keyLbl.Text = "Insira sua key VIP:"
keyLbl.TextColor3 = Color3.fromRGB(200, 200, 200)
keyLbl.Font = Enum.Font.Gotham
keyLbl.TextSize = 12
keyLbl.TextXAlignment = Enum.TextXAlignment.Left
keyLbl.Parent = keySection

local keyBox = Instance.new("TextBox")
keyBox.Size = UDim2.new(1, -80, 0, 32)
keyBox.Position = UDim2.new(0, 10, 0, 30)
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
submit.Size = UDim2.new(0, 60, 0, 32)
submit.Position = UDim2.new(1, -70, 0, 30)
submit.BackgroundColor3 = Color3.fromRGB(130, 0, 220)
submit.Text = "OK"
submit.TextColor3 = Color3.new(1,1,1)
submit.Font = Enum.Font.GothamBold
submit.TextSize = 14
submit.Parent = keySection
Instance.new("UICorner", submit).CornerRadius = UDim.new(0, 5)

local statusLbl = Instance.new("TextLabel")
statusLbl.Size = UDim2.new(1, -20, 0, 16)
statusLbl.Position = UDim2.new(0, 10, 0, 64)
statusLbl.BackgroundTransparency = 1
statusLbl.Text = ""
statusLbl.TextColor3 = Color3.fromRGB(255, 80, 80)
statusLbl.Font = Enum.Font.Gotham
statusLbl.TextSize = 11
statusLbl.TextXAlignment = Enum.TextXAlignment.Left
statusLbl.Parent = keySection

--=============================================================
-- SEÇÃO VIP (oculta até inserir key)
--=============================================================
local vipElements = {}
local function reg(el)
    table.insert(vipElements, el)
    el.Visible = false
    return el
end

reg(makeSection("▸ VIP FUNCTIONS"))
reg(makeToggle("Spinbot V2",                function(v) S.SpinbotV2 = v end))
reg(makeSlider("Spin V2 Speed", 1, 1000, 200, function(v) S.SpinbotV2Speed = v end))
reg(makeSlider("WalkSpeed",     1, 1000, 16,  function(v) S.WalkSpeed = v end))
reg(makeToggle("Tiro no Walk (Wallbang)",   function(v) S.Wallbang  = v end))
reg(makeToggle("ESP",                       function(v) S.ESP       = v end))
reg(makeSlider("ESP Range (m)", 1, 10000, 500, function(v) S.ESPRange = v end))

submit.MouseButton1Click:Connect(function()
    if keyBox.Text == VIP_KEY then
        isVIP = true
        keySection.Visible = false
        for _, el in ipairs(vipElements) do el.Visible = true end
        task.defer(refreshCanvas)
    else
        statusLbl.Text = "❌ Key inválida!"
        task.wait(1.5)
        statusLbl.Text = ""
    end
end)

--=============================================================
-- AIMBOT
--=============================================================
local function getAlivePlayers()
    local list = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LP and p.Character then
            local hum = p.Character:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then table.insert(list, p) end
        end
    end
    return list
end

local function getAimbotTarget()
    local best, bestDist = nil, math.huge
    local camPos = Camera.CFrame.Position
    for _, p in ipairs(getAlivePlayers()) do
        local hrp = p.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            local _, onScreen = Camera:WorldToViewportPoint(hrp.Position)
            if onScreen then
                local d = (hrp.Position - camPos).Magnitude
                if d < bestDist then best, bestDist = p, d end
            end
        end
    end
    return best
end

local oldCamType
RunService:BindToRenderStep("DarkAimbot", Enum.RenderPriority.Camera.Value + 1, function()
    if S.Aimbot then
        if Camera.CameraType ~= Enum.CameraType.Scriptable then
            oldCamType = Camera.CameraType
            Camera.CameraType = Enum.CameraType.Scriptable
        end
        local t = getAimbotTarget()
        if t then
            local hrp = t.Character:FindFirstChild("HumanoidRootPart")
            if hrp then Camera.CFrame = CFrame.lookAt(Camera.CFrame.Position, hrp.Position) end
        end
    elseif oldCamType then
        Camera.CameraType = oldCamType
        oldCamType = nil
    end
end)

--=============================================================
-- SPIN / NOCLIP / WALKSPEED
--=============================================================
RunService.Heartbeat:Connect(function(dt)
    local char = LP.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")

    if hrp and (S.Spinbot or S.SpinbotV2) then
        local speed = S.SpinbotV2 and S.SpinbotV2Speed or S.SpinSpeed
        hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(speed) * (dt * 60), 0)
    end

    if S.NoClip then
        for _, v in ipairs(char:GetDescendants()) do
            if v:IsA("BasePart") and v.CanCollide then v.CanCollide = false end
        end
    end

    if isVIP and hum and hum.WalkSpeed ~= S.WalkSpeed then
        hum.WalkSpeed = S.WalkSpeed
    end
end)

--=============================================================
-- ESP
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
    hl.OutlineTransparency = 0
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    hl.Adornee = p.Character
    hl.Parent  = p.Character

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
    if not S.ESP then
        if next(espData) then
            for p in pairs(espData) do clearESP(p) end
        end
        return
    end
    local camPos = Camera.CFrame.Position
    for _, p in ipairs(Players:GetPlayers()) do
        if p == LP then continue end
        if not p.Character then clearESP(p) continue end
        local hrp = p.Character:FindFirstChild("HumanoidRootPart")
        if not hrp then clearESP(p) continue end
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
end)

--=============================================================
-- WALLBANG (Tiro no Walk)
--=============================================================
local function setupWallbang()
    local grm, sr, ncc, cc =
        getrawmetatable, setreadonly, newcclosure, checkcaller
    if not (grm and sr and ncc and cc) then return false end
    local ok, mt = pcall(grm, Mouse)
    if not ok or not mt or not mt.__index then return false end
    sr(mt, false)
    local oldIdx = mt.__index
    mt.__index = ncc(function(self, key)
        if cc() and S.Wallbang and rawequal(self, Mouse) then
            if key == "Target" or key == "Hit" or key == "UnitRay" then
                local camPos  = Camera.CFrame.Position
                local camLook = Camera.CFrame.LookVector
                local best, bestDist = nil, math.huge
                for _, p in ipairs(getAlivePlayers()) do
                    local head = p.Character:FindFirstChild("Head")
                    if head then
                        local dir  = head.Position - camPos
                        local dist = dir.Magnitude
                        if camLook:Dot(dir.Unit) > 0.95 and dist < bestDist then
                            best, bestDist = head, dist
                        end
                    end
                end
                if best then
                    if key == "Target"  then return best end
                    if key == "Hit"     then return CFrame.new(best.Position) end
                    if key == "UnitRay" then return Ray.new(camPos, (best.Position - camPos).Unit) end
                end
            end
        end
        return oldIdx(self, key)
    end)
    sr(mt, true)
    return true
end
pcall(setupWallbang)

--=============================================================
-- REFRESH FINAL (garante que aparece tudo após criação)
--=============================================================
task.defer(function()
    task.wait(0.3)
    refreshCanvas()
end)
