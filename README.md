local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")
local VirtualUser = game:GetService("VirtualUser")
local StarterGui = game:GetService("StarterGui")

local LP = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local Config = {
    Nome = "🥚 EGG HUNTER HUB",
    Versao = "2.0 MOBILE",
    Cor1 = Color3.fromRGB(140, 90, 255),
    Cor2 = Color3.fromRGB(0, 180, 255),
    Cor3 = Color3.fromRGB(15, 15, 25),
    Cor4 = Color3.fromRGB(30, 30, 48),
    Texto = Color3.fromRGB(240, 240, 255),
    Sucesso = Color3.fromRGB(0, 230, 140),
    Erro = Color3.fromRGB(255, 90, 90),
    Alerta = Color3.fromRGB(255, 200, 0),
    Tamanho = UDim2.new(0, 340, 0, 480),
}

local Estado = {
    Aberto = true,
    Minimizado = false,
    AbaAtual = "Main",
    FuncoesAtivas = 0,
    Waypoints = {},
    AntiAfkAtivo = false,
    PuloInfinito = false,
    EspAtivo = false,
    Favoritos = {},
    SpeedPadrao = 16,
    JumpPadrao = 50,
    FovPadrao = 70,
}

local function GetChar()
    local char = LP.Character
    if not char then return nil, nil, nil end
    local hum = char:FindFirstChildOfClass("Humanoid")
    local root = char:FindFirstChild("HumanoidRootPart")
    return char, hum, root
end

pcall(function()
    local old = CoreGui:FindFirstChild("EggHunterHubMobile")
    if old then old:Destroy() end
end)

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "EggHunterHubMobile"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.IgnoreGuiInset = true
ScreenGui.DisplayOrder = 999
ScreenGui.Parent = CoreGui

local BotaoFlutuante = Instance.new("TextButton")
BotaoFlutuante.Name = "BotaoFlutuante"
BotaoFlutuante.Size = UDim2.new(0, 55, 0, 55)
BotaoFlutuante.Position = UDim2.new(0, 15, 0.5, -27)
BotaoFlutuante.BackgroundColor3 = Config.Cor1
BotaoFlutuante.Text = "🥚"
BotaoFlutuante.TextSize = 26
BotaoFlutuante.Font = Enum.Font.GothamBold
BotaoFlutuante.TextColor3 = Color3.new(1, 1, 1)
BotaoFlutuante.BorderSizePixel = 0
BotaoFlutuante.Visible = false
BotaoFlutuante.Parent = ScreenGui

Instance.new("UICorner", BotaoFlutuante).CornerRadius = UDim.new(1, 0)
local strokeBotao = Instance.new("UIStroke", BotaoFlutuante)
strokeBotao.Color = Config.Cor2
strokeBotao.Thickness = 2

local Janela = Instance.new("Frame")
Janela.Name = "Janela"
Janela.Size = Config.Tamanho
Janela.Position = UDim2.new(0.5, -170, 0.5, -240)
Janela.BackgroundColor3 = Config.Cor3
Janela.BackgroundTransparency = 0.05
Janela.BorderSizePixel = 0
Janela.Active = true
Janela.ClipsDescendants = true
Janela.Parent = ScreenGui

Instance.new("UICorner", Janela).CornerRadius = UDim.new(0, 14)
local strokeJanela = Instance.new("UIStroke", Janela)
strokeJanela.Color = Config.Cor1
strokeJanela.Thickness = 1.5
strokeJanela.Transparency = 0.3

local Header = Instance.new("Frame")
Header.Name = "Header"
Header.Size = UDim2.new(1, 0, 0, 52)
Header.BackgroundColor3 = Config.Cor1
Header.BorderSizePixel = 0
Header.Parent = Janela

Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 14)

local HeaderGrad = Instance.new("UIGradient")
HeaderGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Config.Cor1),
    ColorSequenceKeypoint.new(0.5, Config.Cor2),
    ColorSequenceKeypoint.new(1, Config.Cor1),
})
HeaderGrad.Parent = Header

task.spawn(function()
    while HeaderGrad.Parent do
        HeaderGrad.Rotation = (HeaderGrad.Rotation + 1) % 360
        RunService.RenderStepped:Wait()
    end
end)

local Titulo = Instance.new("TextLabel")
Titulo.Size = UDim2.new(1, -100, 1, 0)
Titulo.Position = UDim2.new(0, 12, 0, 0)
Titulo.BackgroundTransparency = 1
Titulo.Text = Config.Nome
Titulo.TextColor3 = Color3.new(1, 1, 1)
Titulo.TextSize = 18
Titulo.Font = Enum.Font.GothamBold
Titulo.TextXAlignment = Enum.TextXAlignment.Left
Titulo.Parent = Header

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(0, 60, 0, 14)
StatusLabel.Position = UDim2.new(0, 12, 1, -18)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "● 0 ativas"
StatusLabel.TextColor3 = Config.Sucesso
StatusLabel.TextSize = 11
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.Parent = Header

local BtnMin = Instance.new("TextButton")
BtnMin.Size = UDim2.new(0, 38, 0, 38)
BtnMin.Position = UDim2.new(1, -88, 0.5, -19)
BtnMin.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
BtnMin.BackgroundTransparency = 0.6
BtnMin.Text = "—"
BtnMin.TextColor3 = Color3.new(1, 1, 1)
BtnMin.TextSize = 20
BtnMin.Font = Enum.Font.GothamBold
BtnMin.BorderSizePixel = 0
BtnMin.Parent = Header
Instance.new("UICorner", BtnMin).CornerRadius = UDim.new(0, 8)

local BtnFechar = Instance.new("TextButton")
BtnFechar.Size = UDim2.new(0, 38, 0, 38)
BtnFechar.Position = UDim2.new(1, -44, 0.5, -19)
BtnFechar.BackgroundColor3 = Config.Erro
BtnFechar.BackgroundTransparency = 0.6
BtnFechar.Text = "✕"
BtnFechar.TextColor3 = Color3.new(1, 1, 1)
BtnFechar.TextSize = 16
BtnFechar.Font = Enum.Font.GothamBold
BtnFechar.BorderSizePixel = 0
BtnFechar.Parent = Header
Instance.new("UICorner", BtnFechar).CornerRadius = UDim.new(0, 8)

local NavBar = Instance.new("ScrollingFrame")
NavBar.Name = "NavBar"
NavBar.Size = UDim2.new(1, -10, 0, 40)
NavBar.Position = UDim2.new(0, 5, 0, 56)
NavBar.BackgroundColor3 = Config.Cor4
NavBar.BackgroundTransparency = 0.4
NavBar.BorderSizePixel = 0
NavBar.ScrollBarThickness = 0
NavBar.ScrollingDirection = Enum.ScrollingDirection.X
NavBar.CanvasSize = UDim2.new(0, 0, 0, 0)
NavBar.AutomaticCanvasSize = Enum.AutomaticSize.X
NavBar.Parent = Janela
Instance.new("UICorner", NavBar).CornerRadius = UDim.new(0, 10)

local NavLayout = Instance.new("UIListLayout")
NavLayout.FillDirection = Enum.FillDirection.Horizontal
NavLayout.Padding = UDim.new(0, 4)
NavLayout.SortOrder = Enum.SortOrder.LayoutOrder
NavLayout.VerticalAlignment = Enum.VerticalAlignment.Center
NavLayout.Parent = NavBar

local NavPadding = Instance.new("UIPadding")
NavPadding.PaddingLeft = UDim.new(0, 4)
NavPadding.PaddingRight = UDim.new(0, 4)
NavPadding.Parent = NavBar

local Content = Instance.new("ScrollingFrame")
Content.Name = "Content"
Content.Size = UDim2.new(1, -10, 1, -105)
Content.Position = UDim2.new(0, 5, 0, 100)
Content.BackgroundTransparency = 1
Content.BorderSizePixel = 0
Content.ScrollBarThickness = 3
Content.ScrollBarImageColor3 = Config.Cor1
Content.CanvasSize = UDim2.new(0, 0, 0, 0)
Content.AutomaticCanvasSize = Enum.AutomaticSize.Y
Content.Parent = Janela

local ContentLayout = Instance.new("UIListLayout")
ContentLayout.Padding = UDim.new(0, 6)
ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
ContentLayout.Parent = Content

local ContentPad = Instance.new("UIPadding")
ContentPad.PaddingLeft = UDim.new(0, 6)
ContentPad.PaddingRight = UDim.new(0, 6)
ContentPad.PaddingTop = UDim.new(0, 4)
ContentPad.PaddingBottom = UDim.new(0, 8)
ContentPad.Parent = Content

local Abas = {}
local BotoesNav = {}
local AbaAtualParaElementos = nil

local function TargetContent()
    if AbaAtualParaElementos and Abas[AbaAtualParaElementos] then
        return Abas[AbaAtualParaElementos].Frame
    end
    return Content
end

local function Secao(texto)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, 0, 0, 26)
    lbl.BackgroundTransparency = 1
    lbl.Text = "▸ " .. texto
    lbl.TextColor3 = Config.Cor2
    lbl.TextSize = 13
    lbl.Font = Enum.Font.GothamBold
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = TargetContent()
    return lbl
end

local function Toggle(nome, callback, padrao)
    padrao = padrao or false
    local ativo = padrao

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 44)
    frame.BackgroundColor3 = Config.Cor4
    frame.BackgroundTransparency = 0.3
    frame.BorderSizePixel = 0
    frame.Parent = TargetContent()
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -70, 1, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = nome
    lbl.TextColor3 = Config.Texto
    lbl.TextSize = 13
    lbl.Font = Enum.Font.GothamMedium
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = frame

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 54, 0, 28)
    btn.Position = UDim2.new(1, -62, 0.5, -14)
    btn.BackgroundColor3 = ativo and Config.Sucesso or Color3.fromRGB(60, 60, 80)
    btn.Text = ativo and "ON" or "OFF"
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextSize = 12
    btn.Font = Enum.Font.GothamBold
    btn.BorderSizePixel = 0
    btn.Parent = frame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 14)

    btn.MouseButton1Click:Connect(function()
        ativo = not ativo
        TweenService:Create(btn, TweenInfo.new(0.15), {
            BackgroundColor3 = ativo and Config.Sucesso or Color3.fromRGB(60, 60, 80)
        }):Play()
        btn.Text = ativo and "ON" or "OFF"
        if ativo then
            Estado.FuncoesAtivas = Estado.FuncoesAtivas + 1
        else
            Estado.FuncoesAtivas = math.max(0, Estado.FuncoesAtivas - 1)
        end
        StatusLabel.Text = "● " .. Estado.FuncoesAtivas .. " ativas"
        pcall(callback, ativo)
    end)

    if padrao and callback then pcall(callback, true) end
    return frame, btn
end

local function Botao(texto, callback, cor)
    cor = cor or Config.Cor1

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 42)
    btn.BackgroundColor3 = cor
    btn.BackgroundTransparency = 0.2
    btn.Text = texto
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextSize = 13
    btn.Font = Enum.Font.GothamBold
    btn.BorderSizePixel = 0
    btn.Parent = TargetContent()
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)

    btn.MouseButton1Click:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.1), { BackgroundTransparency = 0.5 }):Play()
        task.wait(0.1)
        TweenService:Create(btn, TweenInfo.new(0.1), { BackgroundTransparency = 0.2 }):Play()
        pcall(callback)
    end)
    return btn
end

local function Slider(nome, min, max, padrao, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 62)
    frame.BackgroundColor3 = Config.Cor4
    frame.BackgroundTransparency = 0.3
    frame.BorderSizePixel = 0
    frame.Parent = TargetContent()
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0.7, 0, 0, 22)
    lbl.Position = UDim2.new(0, 12, 0, 4)
    lbl.BackgroundTransparency = 1
    lbl.Text = nome
    lbl.TextColor3 = Config.Texto
    lbl.TextSize = 12
    lbl.Font = Enum.Font.GothamMedium
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = frame

    local valorLbl = Instance.new("TextLabel")
    valorLbl.Size = UDim2.new(0.25, 0, 0, 22)
    valorLbl.Position = UDim2.new(0.7, 0, 0, 4)
    valorLbl.BackgroundTransparency = 1
    valorLbl.Text = tostring(padrao)
    valorLbl.TextColor3 = Config.Cor2
    valorLbl.TextSize = 12
    valorLbl.Font = Enum.Font.GothamBold
    valorLbl.TextXAlignment = Enum.TextXAlignment.Right
    valorLbl.Parent = frame

    local barra = Instance.new("Frame")
    barra.Size = UDim2.new(1, -24, 0, 6)
    barra.Position = UDim2.new(0, 12, 1, -16)
    barra.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    barra.BorderSizePixel = 0
    barra.Parent = frame
    Instance.new("UICorner", barra).CornerRadius = UDim.new(1, 0)

    local preench = Instance.new("Frame")
    preench.Size = UDim2.new((padrao - min) / (max - min), 0, 1, 0)
    preench.BackgroundColor3 = Config.Cor1
    preench.BorderSizePixel = 0
    preench.Parent = barra
    Instance.new("UICorner", preench).CornerRadius = UDim.new(1, 0)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 16, 0, 16)
    knob.Position = UDim2.new(1, -8, 0.5, -8)
    knob.BackgroundColor3 = Config.Cor2
    knob.BorderSizePixel = 0
    knob.Parent = preench
    Instance.new("UICorner", knob).CornerRadius = UDim.new(1, 0)

    local arrastando = false
    local function atualizar(x)
        local absX = x - barra.AbsolutePosition.X
        local pct = math.clamp(absX / barra.AbsoluteSize.X, 0, 1)
        local val = math.floor(min + (max - min) * pct + 0.5)
        preench.Size = UDim2.new(pct, 0, 1, 0)
        valorLbl.Text = tostring(val)
        pcall(callback, val)
    end

    barra.InputBegan:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.Touch or
           inp.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = true
            atualizar(inp.Position.X)
        end
    end)

    UserInputService.InputChanged:Connect(function(inp)
        if arrastando and (inp.UserInputType == Enum.UserInputType.Touch or
           inp.UserInputType == Enum.UserInputType.MouseMovement) then
            atualizar(inp.Position.X)
        end
    end)

    UserInputService.InputEnded:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.Touch or
           inp.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = false
        end
    end)

    if padrao and callback then pcall(callback, padrao) end
    return frame
end

local function Info(texto, valor)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 34)
    frame.BackgroundColor3 = Config.Cor4
    frame.BackgroundTransparency = 0.4
    frame.BorderSizePixel = 0
    frame.Parent = TargetContent()
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)

    local l1 = Instance.new("TextLabel")
    l1.Size = UDim2.new(0.6, 0, 1, 0)
    l1.Position = UDim2.new(0, 12, 0, 0)
    l1.BackgroundTransparency = 1
    l1.Text = texto
    l1.TextColor3 = Config.Texto
    l1.TextSize = 12
    l1.Font = Enum.Font.Gotham
    l1.TextXAlignment = Enum.TextXAlignment.Left
    l1.Parent = frame

    local l2 = Instance.new("TextLabel")
    l2.Size = UDim2.new(0.4, -12, 1, 0)
    l2.Position = UDim2.new(0.6, 0, 0, 0)
    l2.BackgroundTransparency = 1
    l2.Text = valor
    l2.TextColor3 = Config.Cor2
    l2.TextSize = 12
    l2.Font = Enum.Font.GothamBold
    l2.TextXAlignment = Enum.TextXAlignment.Right
    l2.Parent = frame

    return frame, l2
end

local function Input(nome, placeholder, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 68)
    frame.BackgroundColor3 = Config.Cor4
    frame.BackgroundTransparency = 0.3
    frame.BorderSizePixel = 0
    frame.Parent = TargetContent()
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -24, 0, 20)
    lbl.Position = UDim2.new(0, 12, 0, 4)
    lbl.BackgroundTransparency = 1
    lbl.Text = nome
    lbl.TextColor3 = Config.Texto
    lbl.TextSize = 12
    lbl.Font = Enum.Font.GothamMedium
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = frame

    local box = Instance.new("TextBox")
    box.Size = UDim2.new(1, -24, 0, 30)
    box.Position = UDim2.new(0, 12, 0, 28)
    box.BackgroundColor3 = Color3.fromRGB(45, 45, 65)
    box.BorderSizePixel = 0
    box.PlaceholderText = placeholder
    box.Text = ""
    box.TextColor3 = Config.Texto
    box.PlaceholderColor3 = Color3.fromRGB(120, 120, 140)
    box.TextSize = 12
    box.Font = Enum.Font.Gotham
    box.ClearTextOnFocus = false
    box.Parent = frame
    Instance.new("UICorner", box).CornerRadius = UDim.new(0, 6)
    return frame, box
end

local NotifContainer = Instance.new("Frame")
NotifContainer.Name = "NotifContainer"
NotifContainer.Size = UDim2.new(0, 260, 1, -20)
NotifContainer.Position = UDim2.new(1, -270, 0, 10)
NotifContainer.BackgroundTransparency = 1
NotifContainer.Parent = ScreenGui

local NotifLayout = Instance.new("UIListLayout")
NotifLayout.Padding = UDim.new(0, 6)
NotifLayout.SortOrder = Enum.SortOrder.LayoutOrder
NotifLayout.VerticalAlignment = Enum.VerticalAlignment.Bottom
NotifLayout.Parent = NotifContainer

local function Notificar(titulo, texto, cor)
    cor = cor or Config.Sucesso
    local n = Instance.new("Frame")
    n.Size = UDim2.new(1, 0, 0, 52)
    n.BackgroundColor3 = Config.Cor4
    n.BackgroundTransparency = 0.1
    n.BorderSizePixel = 0
    n.Parent = NotifContainer
    Instance.new("UICorner", n).CornerRadius = UDim.new(0, 10)

    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(0, 4, 1, 0)
    bar.BackgroundColor3 = cor
    bar.BorderSizePixel = 0
    bar.Parent = n
    Instance.new("UICorner", bar).CornerRadius = UDim.new(0, 10)

    local t = Instance.new("TextLabel")
    t.Size = UDim2.new(1, -20, 0, 20)
    t.Position = UDim2.new(0, 12, 0, 4)
    t.BackgroundTransparency = 1
    t.Text = titulo
    t.TextColor3 = cor
    t.TextSize = 12
    t.Font = Enum.Font.GothamBold
    t.TextXAlignment = Enum.TextXAlignment.Left
    t.Parent = n

    local s = Instance.new("TextLabel")
    s.Size = UDim2.new(1, -20, 0, 20)
    s.Position = UDim2.new(0, 12, 0, 24)
    s.BackgroundTransparency = 1
    s.Text = texto
    s.TextColor3 = Config.Texto
    s.TextSize = 11
    s.Font = Enum.Font.Gotham
    s.TextXAlignment = Enum.TextXAlignment.Left
    s.TextTruncate = Enum.TextTruncate.AtEnd
    s.Parent = n

    task.spawn(function()
        task.wait(3.5)
        TweenService:Create(n, TweenInfo.new(0.3), { BackgroundTransparency = 1 }):Play()
        task.wait(0.3)
        n:Destroy()
    end)
end

pcall(function()
    StarterGui:SetCore("SendNotification", {
        Title = "🥚 EGG HUNTER HUB",
        Text = "Mobile carregado com sucesso!",
        Duration = 4,
    })
end)

local function SetSpeed(v)
    local _, hum = GetChar()
    if hum then hum.WalkSpeed = v end
end

local function SetJump(v)
    local _, hum = GetChar()
    if hum then
        hum.UseJumpPower = true
        hum.JumpPower = v
    end
end

local function SetFov(v)
    if Camera then Camera.FieldOfView = v end
end

local puloInfConexao = nil
local function SetPuloInfinito(ativo)
    if ativo then
        if not puloInfConexao then
            puloInfConexao = UserInputService.JumpRequest:Connect(function()
                local _, hum = GetChar()
                if hum then
                    hum:ChangeState(Enum.HumanoidStateType.Jumping)
                end
            end)
        end
    else
        if puloInfConexao then
            puloInfConexao:Disconnect()
            puloInfConexao = nil
        end
    end
end

local antiAfkCon = nil
local function SetAntiAfk(ativo)
    if ativo then
        if not antiAfkCon then
            antiAfkCon = LP.Idled:Connect(function()
                VirtualUser:CaptureController()
                VirtualUser:ClickButton2(Vector2.new())
            end)
        end
    else
        if antiAfkCon then
            antiAfkCon:Disconnect()
            antiAfkCon = nil
        end
    end
end

local function Resetar()
    local char = LP.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then hum.Health = 0 end
    end
end

local espCache = {}
local function SetPlayerESP(ativo, cor)
    for _, h in pairs(espCache) do pcall(function() h:Destroy() end) end
    espCache = {}

    if not ativo then return end

    local function addESP(plr)
        if plr == LP or not plr.Character then return end
        if espCache[plr] then return end
        local hl = Instance.new("Highlight")
        hl.Name = "EggH_ESP"
        hl.FillColor = cor or Config.Cor1
        hl.FillTransparency = 0.55
        hl.OutlineColor = Color3.new(1, 1, 1)
        hl.OutlineTransparency = 0.2
        hl.Parent = plr.Character
        espCache[plr] = hl
    end

    for _, p in ipairs(Players:GetPlayers()) do addESP(p) 
