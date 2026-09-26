if getgenv().Alemanha then
    getgenv().Alemanha:Unload()
end

local players    = cloneref(game:GetService("Players"))
local run        = cloneref(game:GetService("RunService"))
local http       = cloneref(game:GetService("HttpService"))
local tws        = cloneref(game:GetService("TweenService"))
local uis        = cloneref(game:GetService("UserInputService"))
local core       = cloneref(game:GetService("CoreGui"))

local lp         = players.LocalPlayer
local mouse      = lp:GetMouse()

local new        = Instance.new
local rgb        = Color3.fromRGB
local hsv        = Color3.fromHSV
local udim2      = UDim2.new
local udim       = UDim.new
local vec2       = Vector2.new
local rgbseq     = ColorSequence.new
local rgbkey     = ColorSequenceKeypoint.new
local fmt        = string.format
local find       = string.find
local lower      = string.lower
local ins        = table.insert
local rem        = table.remove
local tfind      = table.find
local concat     = table.concat
local clone      = table.clone
local clamp      = math.clamp
local round      = math.round
local floor      = math.floor
local abs        = math.abs
local sin        = math.sin

-- ─── LIBRARY ────────────────────────────────────────────────────────────────

local lib = {
    Flags       = {},
    SetFlags    = {},
    ThemeItems  = {},
    ThemeMap    = {},
    Connections = {},
    Threads     = {},
    SearchItems = {},
    OpenFrames  = {},
    Pages       = {},
    Sections    = {},
    CurrentPage = nil,
    MenuKeybind = "Enum.KeyCode.RightShift",
    UnnamedFlags = 0,
    UnnamedConnections = 0,
    Font        = Font.fromEnum(Enum.Font.GothamSemibold),

    Tween = {
        Time      = 0.3,
        Style     = Enum.EasingStyle.Cubic,
        Direction = Enum.EasingDirection.Out,
    },

    Theme = {
        Background      = rgb(16, 18, 21),
        Inline          = rgb(22, 25, 29),
        Element         = rgb(34, 39, 45),
        Border          = rgb(32, 36, 42),
        Accent          = rgb(183, 213, 172),
        Text            = rgb(255, 255, 255),
        ["Inactive Text"] = rgb(185, 185, 185),
        ["Dark Gradient"] = rgb(211, 211, 211),
        Image           = rgb(255, 255, 255),
        Shadow          = rgb(0, 0, 0),
    },

    Folders = {
        Directory = "Alemanha",
        Assets    = "Alemanha/Assets",
        Configs   = "Alemanha/Configs",
        Themes    = "Alemanha/Themes",
    },

    Images = {
        Saturation = { "Saturation.png", "https://github.com/sametexe001/images/blob/main/saturation.png?raw=true" },
        Value      = { "Value.png",      "https://github.com/sametexe001/images/blob/main/value.png?raw=true"      },
        Hue        = { "Hue.png",        "https://github.com/sametexe001/images/blob/main/horizontalhue.png?raw=true" },
        Checkers   = { "Checkers.png",   "https://github.com/sametexe001/images/blob/main/checkers.png?raw=true"   },
    },

    -- rbxassetids usados (exatos da kiwisense)
    Icons = {
        Check    = "rbxassetid://116339777575852",  -- checkmark dropdown
        Grid     = "rbxassetid://114252321536924",   -- grid/arrow dropdown
        Close    = "rbxassetid://76001605964586",    -- X fechar janela
        Minimize = "rbxassetid://94817928404736",    -- minimizar
        UnMin    = "rbxassetid://77419631183448",    -- restaurar
        Search   = "rbxassetid://71924825350727",    -- lupa search
        Resize   = "rbxassetid://7368471234",        -- resize corner
        Mouse    = "rbxassetid://136489814131946",   -- cursor customizado
        -- bottombar
        Save     = "rbxassetid://116339777575852",   -- save configs (check icon)
        Palette  = "rbxassetid://9126796571",        -- theme/palette
        Settings = "rbxassetid://111178525804834",     -- settings gear
    },
}

lib.__index    = lib
lib.Pages.__index    = lib.Pages
lib.Sections.__index = lib.Sections

-- pastas
for _, v in lib.Folders do
    if not isfolder(v) then makefolder(v) end
end

-- imagens do colorpicker
for _, img in lib.Images do
    local path = lib.Folders.Assets .. "/" .. img[1]
    if not isfile(path) then
        writefile(path, game:HttpGet(img[2]))
    end
end

-- font inter
do
    local fontName = "Inter"
    local fontUrl  = "https://github.com/sametexe001/luas/raw/refs/heads/main/fonts/InterSemibold.ttf"
    local ttfPath  = lib.Folders.Assets .. "/" .. fontName .. ".ttf"
    local jsonPath = lib.Folders.Assets .. "/" .. fontName .. ".json"

    if isfile(jsonPath) then
        lib.Font = Font.new(getcustomasset(jsonPath))
    else
        if not isfile(ttfPath) then
            writefile(ttfPath, game:HttpGet(fontUrl))
        end
        local data = { name = fontName, faces = {{ name = "Regular", weight = 200, style = "Regular", assetId = getcustomasset(ttfPath) }} }
        writefile(jsonPath, http:JSONEncode(data))
        lib.Font = Font.new(getcustomasset(jsonPath))
    end
end

-- ─── HELPERS ────────────────────────────────────────────────────────────────

lib.GetImage = function(self, name)
    local img = self.Images[name]
    if not img then return "" end
    return getcustomasset(self.Folders.Assets .. "/" .. img[1])
end

lib.Round = function(n, dec)
    dec = dec or 1
    local m = 10 ^ dec
    return floor(n * m + 0.5) / m
end

lib.NextFlag = function(self)
    self.UnnamedFlags += 1
    return fmt("flag_%d_%s", self.UnnamedFlags, http:GenerateGUID(false))
end

lib.SafeCall = function(self, fn, ...)
    local args = {...}
    local ok, err = pcall(fn, table.unpack(args))
    if not ok then warn("[Alemanha] " .. tostring(err)) end
    return ok
end

lib.Thread = function(self, fn)
    local t = coroutine.create(fn)
    coroutine.wrap(function() coroutine.resume(t) end)()
    ins(self.Threads, t)
    return t
end

lib.Connect = function(self, evt, cb, name)
    name = name or fmt("conn_%d", self.UnnamedConnections + 1)
    self.UnnamedConnections += 1
    local conn = { Event = evt, Callback = cb, Name = name, Connection = nil }
    conn.Connection = evt:Connect(cb)
    ins(self.Connections, conn)
    return conn
end

lib.Disconnect = function(self, name)
    for _, c in self.Connections do
        if c.Name == name and c.Connection then
            c.Connection:Disconnect()
            break
        end
    end
end

lib.IsMouseOver = function(self, frame, ox, oy)
    frame = frame.Instance or frame
    ox, oy = ox or 0, oy or 0
    local mp = vec2(mouse.X + ox, mouse.Y + oy)
    local p, s = frame.AbsolutePosition, frame.AbsoluteSize
    return mp.X >= p.X and mp.X <= p.X + s.X and mp.Y >= p.Y and mp.Y <= p.Y + s.Y
end

lib.GetLighterColor = function(self, color, factor)
    local h, s, v = color:ToHSV()
    return hsv(h, s, clamp(v * factor, 0, 1))
end

lib.GetDarkerColor = function(self, color)
    local h, s, v = color:ToHSV()
    return hsv(h, s, v / 1.35)
end

-- ─── THEME ──────────────────────────────────────────────────────────────────

lib.AddToTheme = function(self, item, props)
    local inst = item.Instance or item
    local data = { Item = inst, Properties = props }
    for prop, val in props do
        if type(val) == "string" then
            inst[prop] = self.Theme[val]
        else
            inst[prop] = val()
        end
    end
    ins(self.ThemeItems, data)
    self.ThemeMap[inst] = data
end

lib.ChangeItemTheme = function(self, item, props)
    local inst = item.Instance or item
    if not self.ThemeMap[inst] then return end
    self.ThemeMap[inst].Properties = props
end

lib.ChangeTheme = function(self, key, color)
    self.Theme[key] = color
    for _, data in self.ThemeItems do
        for prop, val in data.Properties do
            if type(val) == "string" and val == key then
                data.Item[prop] = color
            elseif type(val) == "function" then
                data.Item[prop] = val()
            end
        end
    end
end

lib.GetConfig = function(self)
    local cfg = {}
    for k, v in self.Flags do
        if v == nil then continue end
        if type(v) == "table" and v.Key then
            cfg[k] = { Key = tostring(v.Key), Mode = v.Mode }
        elseif type(v) == "table" and v.Color then
            cfg[k] = { Color = "#" .. v.Color, Alpha = v.Alpha }
        elseif type(v) == "boolean" or type(v) == "number" or type(v) == "string" then
            cfg[k] = v
        end
    end
    local ok, encoded = pcall(http.JSONEncode, http, cfg)
    if not ok then return "{}" end
    return encoded
end

lib.LoadConfig = function(self, raw)
    local ok, decoded = pcall(http.JSONDecode, http, raw)
    if not ok then return false, decoded end
    for k, v in decoded do
        local setter = self.SetFlags[k]
        if not setter then continue end
        if type(v) == "table" and v.Key then
            setter(v)
        elseif type(v) == "table" and v.Color then
            setter(v.Color, v.Alpha)
        else
            setter(v)
        end
    end
    return true
end

lib.SaveConfig = function(self, name)
    local path = self.Folders.Configs .. "/" .. name .. ".json"
    local ok, err = pcall(writefile, path, self:GetConfig())
    if ok then
        self:Notification({ Name = "Success", Description = "Config salvo: " .. name, Duration = 4, IconColor = rgb(52, 255, 164) })
    else
        self:Notification({ Name = "Erro", Description = "Falha ao salvar: " .. tostring(err), Duration = 5, IconColor = rgb(255, 100, 100) })
    end
end

lib.DeleteConfig = function(self, name)
    local path = self.Folders.Configs .. "/" .. name .. ".json"
    if isfile(path) then
        local ok, err = pcall(delfile, path)
        if ok then
            self:Notification({ Name = "Success", Description = "Config deletado: " .. name, Duration = 4, IconColor = rgb(52, 255, 164) })
        else
            self:Notification({ Name = "Erro", Description = "Falha ao deletar: " .. tostring(err), Duration = 5, IconColor = rgb(255, 100, 100) })
        end
    else
        self:Notification({ Name = "Erro", Description = "Config não encontrado: " .. name, Duration = 4, IconColor = rgb(255, 100, 100) })
    end
end

lib.RefreshConfigsList = function(self, element)
    local list = {}
    for _, v in listfiles(self.Folders.Configs) do
        local name = v:match("[^\\/]+$") or v
        ins(list, name)
    end
    if element and element.Refresh then
        element:Refresh(list)
    end
    return list
end

-- ─── THEME SYSTEM ────────────────────────────────────────────────────────────

lib.GetTheme = function(self)
    local cfg = {}
    for k, v in self.Flags do
        if type(v) == "table" and v.Color and find(k, "Theme") then
            cfg[k] = { Color = "#" .. v.Color, Alpha = v.Alpha }
        end
    end
    return http:JSONEncode(cfg)
end

lib.LoadTheme = function(self, raw)
    local ok, decoded = pcall(http.JSONDecode, http, raw)
    if not ok then return false, decoded end
    for k, v in decoded do
        local setter = self.SetFlags[k]
        if not setter then continue end
        if type(v) == "table" and v.Color and find(k, "Theme") then
            setter(v.Color, v.Alpha)
        end
    end
    return true
end

lib.SaveTheme = function(self, name)
    local path = self.Folders.Themes .. "/" .. name .. ".json"
    writefile(path, self:GetTheme())
    self:Notification({ Name = "Success", Description = "Tema salvo: " .. name, Duration = 4, IconColor = rgb(52, 255, 164) })
end

lib.DeleteTheme = function(self, name)
    local path = self.Folders.Themes .. "/" .. name .. ".json"
    if isfile(path) then
        delfile(path)
        self:Notification({ Name = "Success", Description = "Tema deletado: " .. name, Duration = 4, IconColor = rgb(52, 255, 164) })
    end
end

lib.RefreshThemesList = function(self, element)
    local list = {}
    for _, v in listfiles(self.Folders.Themes) do
        local name = v:match("[^\\/]+$") or v
        ins(list, name)
    end
    if element and element.Refresh then
        element:Refresh(list)
    end
    return list
end

-- ─── TWEEN SYSTEM ───────────────────────────────────────────────────────────

local tw = {}
tw.__index = tw

tw.Create = function(self, item, info, goal, raw)
    local inst = raw and item or (item.Instance or item)
    info = info or TweenInfo.new(lib.Tween.Time, lib.Tween.Style, lib.Tween.Direction)
    local t = { Tween = tws:Create(inst, info, goal), Info = info, Goal = goal, Item = inst }
    t.Tween:Play()
    setmetatable(t, self)
    return t
end

tw.GetProperty = function(self, inst)
    if inst:IsA("Frame") then return { "BackgroundTransparency" }
    elseif inst:IsA("TextLabel") or inst:IsA("TextButton") then return { "TextTransparency", "BackgroundTransparency" }
    elseif inst:IsA("ImageLabel") or inst:IsA("ImageButton") then return { "BackgroundTransparency", "ImageTransparency" }
    elseif inst:IsA("ScrollingFrame") then return { "BackgroundTransparency", "ScrollBarImageTransparency" }
    elseif inst:IsA("TextBox") then return { "TextTransparency", "BackgroundTransparency" }
    elseif inst:IsA("UIStroke") then return { "Transparency" }
    end
end

tw.Get = function(self)
    if not self.Tween then return end
    return self.Tween, self.Info, self.Goal
end

tw.Pause = function(self)
    if not self.Tween then return end
    self.Tween:Pause()
end

tw.Play = function(self)
    if not self.Tween then return end
    self.Tween:Play()
end

tw.Clean = function(self)
    if not self.Tween then return end
    self:Pause()
    self = nil
end

tw.FadeItem = function(self, inst, prop, show, speed)
    local old = inst[prop]
    -- fade in: seta invisível antes de tweenear pra valor original
    -- fade out: tweenea do valor atual pra 1 (invisível)
    if show then inst[prop] = 1 end
    local goal = show and old or 1
    local t = tw:Create(inst, TweenInfo.new(speed or lib.Tween.Time, lib.Tween.Style, lib.Tween.Direction), { [prop] = goal }, true)
    lib:Connect(t.Tween.Completed, function()
        if not show then
            task.wait()
            inst[prop] = old
        end
    end)
    return t
end

-- ─── INSTANCES WRAPPER ──────────────────────────────────────────────────────

local inst = {}
inst.__index = inst

inst.Create = function(self, class, props)
    local obj = { Instance = new(class), Properties = props, Class = class }
    setmetatable(obj, self)
    for k, v in props do
        obj.Instance[k] = v
    end
    return obj
end

inst.Tween = function(self, info, goal)
    if not self.Instance then return end
    return tw:Create(self, info, goal)
end

inst.AddToTheme = function(self, props)
    if not self.Instance then return end
    lib:AddToTheme(self, props)
end

inst.ChangeItemTheme = function(self, props)
    if not self.Instance then return end
    lib:ChangeItemTheme(self, props)
end

inst.Connect = function(self, evt, cb, name)
    if not self.Instance then return end
    local evtName = evt
    if evtName == "MouseButton1Down" or evtName == "MouseButton1Click" then
        -- mobile compat pode ser adicionado depois
    end
    if not self.Instance[evtName] then return end
    return lib:Connect(self.Instance[evtName], cb, name)
end

inst.Disconnect = function(self, name)
    lib:Disconnect(name)
end

inst.OnHover = function(self, fn)
    if not self.Instance then return end
    return lib:Connect(self.Instance.MouseEnter, fn)
end

inst.OnHoverLeave = function(self, fn)
    if not self.Instance then return end
    return lib:Connect(self.Instance.MouseLeave, fn)
end

inst.Clean = function(self)
    if not self.Instance then return end
    self.Instance:Destroy()
    self = nil
end

inst.Border = function(self)
    if not self.Instance then return end
    local stroke = inst:Create("UIStroke", {
        Parent = self.Instance,
        Name = "\0",
        Color = lib.Theme.Border,
        Thickness = 1,
        LineJoinMode = Enum.LineJoinMode.Miter,
    })
    stroke:AddToTheme({ Color = "Border" })
    return stroke
end

inst.FadeItem = function(self, show, speed)
    local item = self.Instance
    if show then item.Visible = true end

    local kids = item:GetDescendants()
    ins(kids, item)

    local last
    for _, v in kids do
        local props = tw:GetProperty(v)
        if not props then continue end
        for _, prop in props do
            last = tw:FadeItem(v, prop, not show, speed)
        end
    end
    return last
end

inst.Tooltip = function(self, text)
    if type(text) ~= "string" then return end
    if not self.Instance then return end
    local gui = self.Instance
    local ml = uis:GetMouseLocation()
    local rs

    local tip = inst:Create("Frame", {
        Parent = lib.Holder.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        AutomaticSize = Enum.AutomaticSize.XY,
        Position = udim2(0, ml.X, 0, ml.Y - 38),
        BorderSizePixel = 0,
        ZIndex = 9999,
        BackgroundColor3 = lib.Theme.Background,
    })
    tip:AddToTheme({ BackgroundColor3 = "Background" })

    local stroke = inst:Create("UIStroke", {
        Parent = tip.Instance,
        Name = "\0",
        Color = lib.Theme.Border,
        Transparency = 1,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    })
    stroke:AddToTheme({ Color = "Border" })

    inst:Create("UICorner", { Parent = tip.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIPadding", { Parent = tip.Instance, Name = "\0", PaddingTop = udim(0,5), PaddingBottom = udim(0,5), PaddingLeft = udim(0,5), PaddingRight = udim(0,5) })

    local label = inst:Create("TextLabel", {
        Parent = tip.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = text,
        TextSize = 14,
        TextColor3 = lib.Theme.Text,
        TextTransparency = 1,
        AutomaticSize = Enum.AutomaticSize.XY,
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 9999,
        BackgroundColor3 = rgb(255,255,255),
    })

    lib:Connect(gui.MouseEnter, function()
        tip:Tween(nil, { BackgroundTransparency = 0.15 })
        label:Tween(nil, { TextTransparency = 0 })
        stroke:Tween(nil, { Transparency = 0.4 })
        rs = run.RenderStepped:Connect(function()
            ml = uis:GetMouseLocation()
            tip:Tween(nil, { Position = udim2(0, ml.X, 0, ml.Y - 38) })
        end)
    end)

    lib:Connect(gui.MouseLeave, function()
        tip:Tween(nil, { BackgroundTransparency = 1 })
        label:Tween(nil, { TextTransparency = 1 })
        stroke:Tween(nil, { Transparency = 1 })
        if rs then rs:Disconnect(); rs = nil end
    end)
end

inst.MakeDraggable = function(self)
    if not self.Instance then return end
    local gui = self.Instance
    local dragging, start, startPos = false, nil, nil
    local ic

    self:Connect("InputBegan", function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch then return end
        dragging = true
        start = input.Position
        startPos = gui.Position

        if ic then return end
        ic = input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
                ic:Disconnect(); ic = nil
            end
        end)
    end)

    lib:Connect(uis.InputChanged, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseMovement and input.UserInputType ~= Enum.UserInputType.Touch then return end
        if not dragging then return end
        local d = input.Position - start
        self:Tween(TweenInfo.new(0.16, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Position = udim2(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
        })
    end)
end

inst.MakeResizeable = function(self, minS, maxS)
    if not self.Instance then return end
    local gui = self.Instance
    local resizing, start = false, udim2()
    local ic

    local btn = inst:Create("ImageButton", {
        Parent = gui,
        Name = "\0",
        Image = lib.Icons.Resize,
        AnchorPoint = vec2(1, 1),
        Size = udim2(0, 9, 0, 9),
        Position = udim2(1, -4, 1, -4),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 5,
        AutoButtonColor = false,
        BorderColor3 = rgb(0,0,0),
    })
    btn:AddToTheme({ ImageColor3 = "Accent" })

    btn:Connect("InputBegan", function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch then return end
        resizing = true
        start = gui.Size - udim2(0, input.Position.X, 0, input.Position.Y)
        if ic then return end
        ic = input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                resizing = false; ic:Disconnect(); ic = nil
            end
        end)
    end)

    lib:Connect(uis.InputChanged, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseMovement and input.UserInputType ~= Enum.UserInputType.Touch then return end
        if not resizing then return end
        local rmax = maxS or gui.Parent.AbsoluteSize
        local d = start + udim2(0, input.Position.X, 0, input.Position.Y)
        d = udim2(0, clamp(d.X.Offset, minS.X, rmax.X), 0, clamp(d.Y.Offset, minS.Y, rmax.Y))
        tws:Create(gui, TweenInfo.new(0.17, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), { Size = d }):Play()
    end)
end

-- ─── HOLDER + NOTIF HOLDER ──────────────────────────────────────────────────

lib.Holder = inst:Create("ScreenGui", {
    Parent = core,
    Name = "\0",
    ZIndexBehavior = Enum.ZIndexBehavior.Global,
    DisplayOrder = 2,
    ResetOnSpawn = false,
})

lib.UnusedHolder = inst:Create("ScreenGui", {
    Parent = core,
    Name = "\0",
    ZIndexBehavior = Enum.ZIndexBehavior.Global,
    Enabled = false,
    ResetOnSpawn = false,
})

lib.NotifHolder = inst:Create("Frame", {
    Parent = lib.Holder.Instance,
    Name = "\0",
    AnchorPoint = vec2(1, 0),
    BackgroundTransparency = 1,
    Position = udim2(1, 0, 0, 0),
    Size = udim2(0, 0, 1, 0),
    BorderSizePixel = 0,
    AutomaticSize = Enum.AutomaticSize.X,
    BackgroundColor3 = rgb(255, 255, 255),
})

inst:Create("UIListLayout", {
    Parent = lib.NotifHolder.Instance,
    Name = "\0",
    Padding = udim(0, 5),
    SortOrder = Enum.SortOrder.LayoutOrder,
    VerticalAlignment = Enum.VerticalAlignment.Top,
})

inst:Create("UIPadding", {
    Parent = lib.NotifHolder.Instance,
    Name = "\0",
    PaddingTop    = udim(0, 15),
    PaddingBottom = udim(0, 15),
    PaddingRight  = udim(0, 15),
})

-- ─── UNLOAD ─────────────────────────────────────────────────────────────────

lib.Unload = function(self)
    for _, c in self.Connections do
        if c.Connection then pcall(c.Connection.Disconnect, c.Connection) end
    end
    for _, t in self.Threads do
        pcall(coroutine.close, t)
    end
    if self.Holder then pcall(self.Holder.Instance.Destroy, self.Holder.Instance) end
    if self.UnusedHolder then pcall(self.UnusedHolder.Instance.Destroy, self.UnusedHolder.Instance) end
    getgenv().Alemanha = nil
end

-- ─── NOTIFICATION ───────────────────────────────────────────────────────────

lib.Notification = function(self, data)
    data = data or {}
    local name  = data.Name or data.name or "Alemanha"
    local desc  = data.Description or data.description or ""
    local dur   = data.Duration or data.duration or 5
    local icon  = data.Icon or data.icon
    local icolor= data.IconColor or data.iconcolor or rgb(255, 255, 255)

    local items = {}

    items.Notif = inst:Create("Frame", {
        Parent = lib.NotifHolder.Instance,
        Name = "\0",
        AutomaticSize = Enum.AutomaticSize.XY,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Background,
    })
    items.Notif:AddToTheme({ BackgroundColor3 = "Background" })

    inst:Create("UIStroke", {
        Parent = items.Notif.Instance,
        Name = "\0",
        Color = lib.Theme.Border,
        Transparency = 0.4,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    }):AddToTheme({ Color = "Border" })

    inst:Create("UICorner", { Parent = items.Notif.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIPadding", { Parent = items.Notif.Instance, Name = "\0",
        PaddingTop = udim(0,8), PaddingBottom = udim(0,8),
        PaddingLeft = udim(0,8), PaddingRight = udim(0,8),
    })

    if icon then
        items.Icon = inst:Create("ImageLabel", {
            Parent = items.Notif.Instance,
            Name = "\0",
            ImageColor3 = icolor,
            AnchorPoint = vec2(1, 0),
            Image = "rbxassetid://" .. icon,
            BackgroundTransparency = 1,
            Position = udim2(1, 5, 0, 0),
            Size = udim2(0, 22, 0, 22),
            BorderSizePixel = 0,
            BackgroundColor3 = rgb(255,255,255),
        })
    end

    items.Title = inst:Create("TextLabel", {
        Parent = items.Notif.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AutomaticSize = Enum.AutomaticSize.X,
        Size = udim2(0,0,0,15),
        Position = udim2(0,0,0,2),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Title:AddToTheme({ TextColor3 = "Text" })

    items.Desc = inst:Create("TextLabel", {
        Parent = items.Notif.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = desc,
        TextSize = 14,
        TextTransparency = 0.5,
        TextColor3 = rgb(185,185,185),
        AutomaticSize = Enum.AutomaticSize.X,
        Size = udim2(0,0,0,15),
        Position = udim2(0,0,0,24),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Desc:AddToTheme({ TextColor3 = "Inactive Text" })

    -- animate in — espera 1 frame pro Roblox calcular AbsoluteSize antes de esconder
    lib:Thread(function()
        task.wait()
        local oldSize = items.Notif.Instance.AbsoluteSize

        items.Notif.Instance.BackgroundTransparency = 1
        items.Notif.Instance.AutomaticSize = Enum.AutomaticSize.None
        items.Notif.Instance.Size = udim2(0, oldSize.X, 0, 0)

        for _, v in items.Notif.Instance:GetDescendants() do
            if v:IsA("UIStroke") then v.Transparency = 1
            elseif v:IsA("TextLabel") then v.TextTransparency = 1
            elseif v:IsA("ImageLabel") then v.ImageTransparency = 1
            elseif v:IsA("Frame") then v.BackgroundTransparency = 1 end
        end

        items.Notif:Tween(nil, { BackgroundTransparency = 0, Size = udim2(0, oldSize.X, 0, oldSize.Y) })
        task.wait(0.06)
        for _, v in items.Notif.Instance:GetDescendants() do
            if v:IsA("UIStroke") then tw:Create(v, nil, { Transparency = 0 }, true)
            elseif v:IsA("TextLabel") then tw:Create(v, nil, { TextTransparency = 0 }, true)
            elseif v:IsA("ImageLabel") then tw:Create(v, nil, { ImageTransparency = 0 }, true)
            elseif v:IsA("Frame") then tw:Create(v, nil, { BackgroundTransparency = 0 }, true) end
        end

        task.delay(dur, function()
            for _, v in items.Notif.Instance:GetDescendants() do
                if v:IsA("UIStroke") then tw:Create(v, nil, { Transparency = 1 }, true)
                elseif v:IsA("TextLabel") then tw:Create(v, nil, { TextTransparency = 1 }, true)
                elseif v:IsA("ImageLabel") then tw:Create(v, nil, { ImageTransparency = 1 }, true)
                elseif v:IsA("Frame") then tw:Create(v, nil, { BackgroundTransparency = 1 }, true) end
            end
            task.wait(0.06)
            items.Notif:Tween(nil, { BackgroundTransparency = 1, Size = udim2(0, oldSize.X, 0, 0) })
            task.wait(0.5)
            items.Notif:Clean()
        end)
    end)
end

-- ─── WATERMARK ──────────────────────────────────────────────────────────────

lib.Watermark = function(self, text)
    local wm = inst:Create("Frame", {
        Parent = lib.Holder.Instance,
        Name = "\0",
        AnchorPoint = vec2(0, 0),
        BackgroundTransparency = 0,
        Position = udim2(0, 6, 0, 6),
        Size = udim2(0, 0, 0, 28),
        AutomaticSize = Enum.AutomaticSize.X,
        BorderSizePixel = 0,
        ZIndex = 5,
        BackgroundColor3 = lib.Theme.Background,
    })
    wm:AddToTheme({ BackgroundColor3 = "Background" })

    inst:Create("UIStroke", {
        Parent = wm.Instance, Name = "\0",
        Color = lib.Theme.Border, Transparency = 0.4,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    }):AddToTheme({ Color = "Border" })

    inst:Create("UICorner", { Parent = wm.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIPadding", { Parent = wm.Instance, Name = "\0",
        PaddingLeft = udim(0,8), PaddingRight = udim(0,8) })

    local label = inst:Create("TextLabel", {
        Parent = wm.Instance, Name = "\0",
        FontFace = lib.Font,
        Text = text,
        TextSize = 14,
        TextColor3 = lib.Theme.Text,
        AnchorPoint = vec2(0, 0.5),
        Position = udim2(0, 0, 0.5, 0),
        Size = udim2(0, 0, 0, 15),
        AutomaticSize = Enum.AutomaticSize.X,
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 5,
        BackgroundColor3 = rgb(255,255,255),
    })
    label:AddToTheme({ TextColor3 = "Text" })

    local wmObj = { Label = label, Frame = wm }
    function wmObj:SetText(t) label.Instance.Text = t end
    function wmObj:Remove() wm:Clean() end
    return wmObj
end

-- ─── KEYBINDS LIST ──────────────────────────────────────────────────────────

lib.KeybindsList = function(self)
    local kl = inst:Create("Frame", {
        Parent = lib.Holder.Instance,
        Name = "\0",
        AnchorPoint = vec2(0, 1),
        BackgroundTransparency = 0,
        Position = udim2(0, 6, 1, -6),
        Size = udim2(0, 0, 0, 0),
        AutomaticSize = Enum.AutomaticSize.XY,
        BorderSizePixel = 0,
        ZIndex = 5,
        BackgroundColor3 = lib.Theme.Background,
    })
    kl:AddToTheme({ BackgroundColor3 = "Background" })

    inst:Create("UIStroke", {
        Parent = kl.Instance, Name = "\0",
        Color = lib.Theme.Border, Transparency = 0.4,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    }):AddToTheme({ Color = "Border" })

    inst:Create("UICorner", { Parent = kl.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIPadding", { Parent = kl.Instance, Name = "\0",
        PaddingTop = udim(0,6), PaddingBottom = udim(0,6),
        PaddingLeft = udim(0,8), PaddingRight = udim(0,8),
    })
    inst:Create("UIListLayout", {
        Parent = kl.Instance, Name = "\0",
        Padding = udim(0, 4),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })

    local entries = {}

    local klObj = { Frame = kl, Entries = entries }

    function klObj:Add(name, key)
        local row = inst:Create("Frame", {
            Parent = kl.Instance, Name = "\0",
            BackgroundTransparency = 1,
            Size = udim2(1, 0, 0, 18),
            AutomaticSize = Enum.AutomaticSize.X,
            BorderSizePixel = 0,
            BackgroundColor3 = rgb(255,255,255),
        })

        local lbl = inst:Create("TextLabel", {
            Parent = row.Instance, Name = "\0",
            FontFace = lib.Font,
            Text = name .. "  [" .. key .. "]",
            TextSize = 13,
            TextColor3 = lib.Theme["Inactive Text"],
            AnchorPoint = vec2(0, 0.5),
            Position = udim2(0,0,0.5,0),
            Size = udim2(0,0,0,15),
            AutomaticSize = Enum.AutomaticSize.X,
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            ZIndex = 5,
            BackgroundColor3 = rgb(255,255,255),
        })
        lbl:AddToTheme({ TextColor3 = "Inactive Text" })

        entries[name] = { Row = row, Label = lbl }
    end

    function klObj:Remove(name)
        if entries[name] then
            entries[name].Row:Clean()
            entries[name] = nil
        end
    end

    return klObj
end

-- ─── KEY NAMES ──────────────────────────────────────────────────────────────

local keyNames = {
    ["Space"] = "Space", ["Return"] = "Enter", ["LeftShift"] = "LShift", ["RightShift"] = "RShift",
    ["LeftControl"] = "LCtrl", ["RightControl"] = "RCtrl", ["LeftAlt"] = "LAlt", ["RightAlt"] = "RAlt",
    ["Tab"] = "Tab", ["Backspace"] = "Backspace", ["Delete"] = "Delete", ["Insert"] = "Insert",
    ["Home"] = "Home", ["End"] = "End", ["PageUp"] = "PgUp", ["PageDown"] = "PgDn",
    ["F1"]="F1",["F2"]="F2",["F3"]="F3",["F4"]="F4",["F5"]="F5",["F6"]="F6",
    ["F7"]="F7",["F8"]="F8",["F9"]="F9",["F10"]="F10",["F11"]="F11",["F12"]="F12",
    ["MouseButton1"] = "MB1", ["MouseButton2"] = "MB2",
}

local function getKeyName(key)
    local s = tostring(key)
    s = s:gsub("Enum%.KeyCode%.", ""):gsub("Enum%.UserInputType%.", "")
    return keyNames[s] or s
end

-- ─── COMPONENTS ─────────────────────────────────────────────────────────────

local comp = {}

-- TOGGLE
comp.Toggle = function(data)
    local name     = data.Name
    local parent   = data.Parent
    local flag     = data.Flag
    local default  = data.Default or false
    local page     = data.Page
    local cb       = data.Callback or function() end

    local items = {}
    local value = default

    items.Toggle = inst:Create("Frame", {
        Parent = parent.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        Size = udim2(1, 0, 0, 20),
        BorderColor3 = rgb(0,0,0),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = rgb(255,255,255),
    })

    items.Label = inst:Create("TextLabel", {
        Parent = items.Toggle.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AnchorPoint = vec2(0, 0.5),
        Position = udim2(0, 0, 0.5, 0),
        Size = udim2(1, -80, 0, 15),
        BackgroundTransparency = 1,
        TextXAlignment = Enum.TextXAlignment.Left,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Label:AddToTheme({ TextColor3 = "Text" })

    -- sub-elements holder (keybind, colorpicker ficam aqui)
    items.SubElements = inst:Create("Frame", {
        Parent = items.Toggle.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        AnchorPoint = vec2(1, 0.5),
        Position = udim2(1, 0, 0.5, 0),
        Size = udim2(0, 0, 0, 24),
        AutomaticSize = Enum.AutomaticSize.X,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })

    inst:Create("UIListLayout", {
        Parent = items.SubElements.Instance,
        Name = "\0",
        FillDirection = Enum.FillDirection.Horizontal,
        HorizontalAlignment = Enum.HorizontalAlignment.Right,
        VerticalAlignment = Enum.VerticalAlignment.Center,
        Padding = udim(0, 5),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })

    -- indicator (outer box)
    items.Indicator = inst:Create("Frame", {
        Parent = items.SubElements.Instance,
        Name = "\0",
        AnchorPoint = vec2(1, 0.5),
        Size = udim2(0, 20, 0, 20),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Element,
    })
    items.Indicator:AddToTheme({ BackgroundColor3 = "Element" })
    inst:Create("UICorner", { Parent = items.Indicator.Instance, Name = "\0", CornerRadius = udim(0, 4) })

    -- inline (inner fill)
    items.Check = inst:Create("TextButton", {
        Parent = items.Indicator.Instance,
        Name = "\0",
        Text = "",
        AutoButtonColor = false,
        Size = udim2(1, -4, 1, -4),
        Position = udim2(0, 2, 0, 2),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Element,
    })
    items.Check:AddToTheme({ BackgroundColor3 = "Element" })
    inst:Create("UICorner", { Parent = items.Check.Instance, Name = "\0", CornerRadius = udim(0, 4) })
    inst:Create("UIGradient", {
        Parent = items.Check.Instance, Name = "\0",
        Rotation = 84,
        Color = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, rgb(211,211,211)) },
    }):AddToTheme({ Color = function()
        return rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) }
    end })

    items.CheckIcon = inst:Create("ImageLabel", {
        Parent = items.Check.Instance,
        Name = "\0",
        Image = lib.Icons.Check,
        ImageColor3 = rgb(16,18,21),
        ImageTransparency = value and 0 or 1,
        AnchorPoint = vec2(0.5, 0.5),
        Position = udim2(0.5, 0, 0.5, 0),
        Size = udim2(1, -2, 1, -2),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })

    local obj = { Value = value, Flag = flag, Page = page, Callback = cb, Count = 0 }

    local function refresh()
        if obj.Value then
            items.Indicator:ChangeItemTheme({ BackgroundColor3 = "Accent" })
            items.Check:ChangeItemTheme({ BackgroundColor3 = "Accent" })
            items.Indicator:Tween(nil, { BackgroundColor3 = lib.Theme.Accent })
            items.Check:Tween(nil, { BackgroundColor3 = lib.Theme.Accent })
            items.CheckIcon:Tween(nil, { ImageTransparency = 0 })
            items.Label:Tween(nil, { TextTransparency = 0 })
        else
            items.Indicator:ChangeItemTheme({ BackgroundColor3 = "Element" })
            items.Check:ChangeItemTheme({ BackgroundColor3 = "Element" })
            items.Indicator:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
            items.Check:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
            items.CheckIcon:Tween(nil, { ImageTransparency = 1 })
            items.Label:Tween(nil, { TextTransparency = 0.4 })
        end
    end

    function obj:Set(bool)
        self.Value = bool
        lib.Flags[self.Flag] = bool
        refresh()
        lib:SafeCall(self.Callback, bool)
    end

    function obj:Get() return self.Value end

    function obj:SetVisibility(bool) items.Toggle.Instance.Visible = bool end

    items.Check:Connect("MouseButton1Down", function()
        obj:Set(not obj.Value)
    end)

    -- click no row inteiro também ativa, mas ignora cliques nos sub-elements
    lib:Connect(items.Toggle.Instance.InputBegan, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 then return end
        if lib:IsMouseOver(items.SubElements) then return end
        obj:Set(not obj.Value)
    end)

    -- hover no frame inteiro (igual kiwisense)
    items.Toggle:OnHover(function()
        if obj.Value then return end
        items.Indicator:Tween(nil, { BackgroundColor3 = lib:GetLighterColor(lib.Theme.Element, 1.45) })
        items.Check:Tween(nil, { BackgroundColor3 = lib:GetLighterColor(lib.Theme.Element, 1.45) })
    end)
    items.Toggle:OnHoverLeave(function()
        if obj.Value then return end
        items.Indicator:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
        items.Check:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
    end)

    lib.Flags[flag] = default
    lib.SetFlags[flag] = function(v) obj:Set(v) end

    if default then refresh() end

    return obj, items
end

-- KEYBIND (standalone e inline)
comp.Keybind = function(data)
    local name      = data.Name
    local parent    = data.Parent  -- pode ser SubElements de toggle ou Content de section
    local flag      = data.Flag
    local default   = data.Default or Enum.KeyCode.RightShift
    local mode      = data.Mode or "Toggle"
    local isToggle  = data.IsToggle or false
    local cb        = data.Callback or function() end

    local items = {}
    local value = { Key = default, Mode = mode }
    local listening = false

    -- label do keybind (texto da tecla)
    if isToggle then
        items.Label = inst:Create("TextButton", {
            Parent = parent.Instance,
            Name = "\0",
            FontFace = lib.Font,
            AutoButtonColor = false,
            Text = getKeyName(default),
            TextSize = 14,
            ZIndex = 2,
            Size = udim2(0, 0, 0, 15),
            AutomaticSize = Enum.AutomaticSize.X,
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            BackgroundColor3 = rgb(255,255,255),
        })
        items.Label:AddToTheme({ TextColor3 = "Inactive Text" })
    else
        -- standalone: row com label esquerda + keybind direita
        items.Row = inst:Create("Frame", {
            Parent = parent.Instance,
            Name = "\0",
            BackgroundTransparency = 1,
            Size = udim2(1, 0, 0, 24),
            BorderSizePixel = 0,
            ZIndex = 2,
            BackgroundColor3 = rgb(255,255,255),
        })

        local nameLabel = inst:Create("TextLabel", {
            Parent = items.Row.Instance,
            Name = "\0",
            FontFace = lib.Font,
            Text = name,
            TextSize = 14,
            TextColor3 = rgb(255,255,255),
            AnchorPoint = vec2(0, 0.5),
            Position = udim2(0, 0, 0.5, 0),
            Size = udim2(1, -80, 0, 15),
            BackgroundTransparency = 1,
            TextXAlignment = Enum.TextXAlignment.Left,
            BorderSizePixel = 0,
            ZIndex = 2,
            BackgroundColor3 = rgb(255,255,255),
        })
        nameLabel:AddToTheme({ TextColor3 = "Text" })

        items.Label = inst:Create("TextButton", {
            Parent = items.Row.Instance,
            Name = "\0",
            FontFace = lib.Font,
            AutoButtonColor = false,
            Text = getKeyName(default),
            TextSize = 14,
            ZIndex = 2,
            Size = udim2(0, 0, 0, 15),
            AutomaticSize = Enum.AutomaticSize.X,
            AnchorPoint = vec2(1, 0.5),
            Position = udim2(1, 0, 0.5, 0),
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            BackgroundColor3 = rgb(255,255,255),
        })
        items.Label:AddToTheme({ TextColor3 = "Inactive Text" })
        items.Label.Instance.Parent = items.Row.Instance
    end

    local obj = { Value = value, Flag = flag, Mode = mode, Callback = cb }

    -- atualiza visual e flag, sem chamar o callback
    local function setKey(key)
        obj.Value = { Key = key, Mode = mode }
        lib.Flags[flag] = obj.Value
        items.Label.Instance.Text = getKeyName(key)
    end

    -- executa a ação do keybind (chama o callback)
    local function pressKey(key)
        lib:SafeCall(cb, key)
    end

    function obj:Set(data2)
        if type(data2) == "table" then
            mode = data2.Mode or mode
            obj.Mode = mode
            setKey(data2.Key or default)
        else
            setKey(data2)
        end
    end

    function obj:Get() return obj.Value end

    -- listener global: quando a tecla é pressionada, executa a ação
    lib:Connect(uis.InputBegan, function(input, gpe)
        if listening then return end
        local pressed = input.KeyCode ~= Enum.KeyCode.Unknown and input.KeyCode or input.UserInputType
        if pressed == obj.Value.Key then
            pressKey(pressed)
        end
    end)

    items.Label:Connect("MouseButton1Down", function()
        if listening then return end
        listening = true
        items.Label.Instance.Text = "..."
        items.Label:Tween(nil, { TextTransparency = 0 })

        local conn
        conn = lib:Connect(uis.InputBegan, function(input, gpe)
            if gpe then return end
            listening = false
            if input.KeyCode == Enum.KeyCode.Escape then
                setKey(obj.Value.Key)
                if conn then conn.Connection:Disconnect() end
                return
            end
            local key = input.KeyCode ~= Enum.KeyCode.Unknown and input.KeyCode or input.UserInputType
            setKey(key)
            if conn then conn.Connection:Disconnect() end
        end)
    end)

    lib.Flags[flag] = value
    lib.SetFlags[flag] = function(v) obj:Set(v) end
    setKey(default)

    return obj, items
end

-- COLORPICKER
comp.Colorpicker = function(data)
    local numseq = NumberSequence.new
    local numkey = NumberSequenceKeypoint.new

    local cp = {
        IsOpen     = false,
        Color      = data.Default or rgb(255, 255, 255),
        HexValue   = "",
        Alpha      = data.Alpha or 0,
        Hue        = 0,
        Saturation = 0,
        Value      = 0,
        Name       = data.Name,
        Type       = "Colorpicker",
    }

    local flag     = data.Flag
    local parent   = data.Parent
    local cb       = data.Callback or function() end
    local isToggle = data.IsToggle or false

    lib.Flags[flag] = {}

    local items = {}

    -- ── Dot (botão colorido) ──────────────────────────────────────────────
    items.Dot = inst:Create("TextButton", {
        Parent          = parent.Instance,
        Name            = "\0",
        Text            = "",
        AutoButtonColor = false,
        BorderSizePixel = 0,
        Size            = udim2(0, 20, 0, 20),
        ZIndex          = 5,
        BackgroundColor3 = cp.Color,
    })
    inst:Create("UICorner", { Parent = items.Dot.Instance, Name = "\0", CornerRadius = udim(0, 4) })

    items.Inline = inst:Create("Frame", {
        Parent          = items.Dot.Instance,
        Name            = "\0",
        Size            = udim2(1, -4, 1, -4),
        Position        = udim2(0, 2, 0, 2),
        BorderSizePixel = 0,
        ZIndex          = 6,
        BackgroundColor3 = cp.Color,
    })
    inst:Create("UICorner", { Parent = items.Inline.Instance, Name = "\0", CornerRadius = udim(0, 4) })
    inst:Create("UIGradient", {
        Parent   = items.Inline.Instance,
        Name     = "\0",
        Rotation = 84,
        Color    = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) },
    }):AddToTheme({ Color = function()
        return rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) }
    end })

    -- ── Janela flutuante ──────────────────────────────────────────────────
    items.Window = inst:Create("TextButton", {
        Parent          = lib.UnusedHolder.Instance,
        Text            = "",
        AutoButtonColor = false,
        Name            = "\0",
        Active          = false,
        Selectable      = false,
        Size            = udim2(0, 219, 0, 245),
        Position        = udim2(0, 0, 0, 0),
        BorderSizePixel = 0,
        ZIndex          = 2,
        Visible         = false,
        BackgroundColor3 = lib.Theme.Background,
    })
    items.Window:AddToTheme({ BackgroundColor3 = "Background" })
    items.Window:MakeDraggable()

    inst:Create("UIStroke", {
        Parent          = items.Window.Instance,
        Name            = "\0",
        Color           = lib.Theme.Border,
        Transparency    = 0.4,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    }):AddToTheme({ Color = "Border" })
    inst:Create("UICorner", { Parent = items.Window.Instance, Name = "\0", CornerRadius = udim(0, 5) })

    -- shadow
    inst:Create("ImageLabel", {
        Parent              = items.Window.Instance,
        Name                = "\0",
        ImageColor3         = rgb(0,0,0),
        ScaleType           = Enum.ScaleType.Slice,
        ImageTransparency   = 0.9,
        BorderSizePixel     = 0,
        Size                = udim2(1, 25, 1, 25),
        AnchorPoint         = vec2(0.5, 0.5),
        Image               = "rbxassetid://18245826428",
        BackgroundTransparency = 1,
        Position            = udim2(0.5, 0, 0.5, 0),
        BackgroundColor3    = rgb(255,255,255),
        SliceCenter         = Rect.new(vec2(21,21), vec2(79,79)),
    }):AddToTheme({ ImageColor3 = "Shadow" })

    -- ── Palette (S/V) ─────────────────────────────────────────────────────
    items.Palette = inst:Create("TextButton", {
        Parent          = items.Window.Instance,
        Name            = "\0",
        Text            = "",
        AutoButtonColor = false,
        BorderSizePixel = 0,
        Position        = udim2(0, 8, 0, 8),
        Size            = udim2(1, -16, 1, -125),
        ZIndex          = 2,
        BackgroundColor3 = rgb(255, 125, 32),
    })
    inst:Create("UICorner", { Parent = items.Palette.Instance, Name = "\0", CornerRadius = udim(0, 5) })

    -- saturation overlay
    inst:Create("ImageLabel", {
        Parent              = items.Palette.Instance,
        Name                = "\0",
        Image               = lib:GetImage("Saturation"),
        BackgroundTransparency = 1,
        Size                = udim2(1, 0, 1, 0),
        ZIndex              = 2,
        BorderSizePixel     = 0,
        BackgroundColor3    = rgb(255,255,255),
    })
    -- value overlay
    inst:Create("ImageLabel", {
        Parent              = items.Palette.Instance,
        Name                = "\0",
        Image               = lib:GetImage("Value"),
        BackgroundTransparency = 1,
        Size                = udim2(1, 2, 1, 0),
        Position            = udim2(0, -1, 0, 0),
        ZIndex              = 3,
        BorderSizePixel     = 0,
        BackgroundColor3    = rgb(255,255,255),
    })

    -- palette dragger
    items.PaletteDragger = inst:Create("Frame", {
        Parent          = items.Palette.Instance,
        Name            = "\0",
        Size            = udim2(0, 8, 0, 8),
        Position        = udim2(0, 0, 0, 0),
        BorderSizePixel = 0,
        ZIndex          = 4,
        BackgroundColor3 = rgb(255,255,255),
    })
    inst:Create("UICorner",  { Parent = items.PaletteDragger.Instance, Name = "\0", CornerRadius = udim(1, 0) })
    inst:Create("UIStroke",  { Parent = items.PaletteDragger.Instance, Name = "\0", Thickness = 1.2, ApplyStrokeMode = Enum.ApplyStrokeMode.Border })

    -- ── Hue slider ────────────────────────────────────────────────────────
    items.Hue = inst:Create("TextButton", {
        Parent          = items.Window.Instance,
        Name            = "\0",
        Text            = "",
        AutoButtonColor = false,
        BorderSizePixel = 0,
        AnchorPoint     = vec2(0, 1),
        Position        = udim2(0, 8, 1, -90),
        Size            = udim2(1, -16, 0, 18),
        ZIndex          = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    inst:Create("UICorner", { Parent = items.Hue.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIGradient", {
        Parent = items.Hue.Instance,
        Color  = rgbseq{
            rgbkey(0,    rgb(255,0,0)),
            rgbkey(0.17, rgb(255,255,0)),
            rgbkey(0.33, rgb(0,255,0)),
            rgbkey(0.50, rgb(0,255,255)),
            rgbkey(0.67, rgb(0,0,255)),
            rgbkey(0.83, rgb(255,0,255)),
            rgbkey(1,    rgb(255,0,0)),
        },
    })

    items.HueDragger = inst:Create("Frame", {
        Parent          = items.Hue.Instance,
        Name            = "\0",
        BorderSizePixel = 0,
        AnchorPoint     = vec2(0, 0.5),
        Position        = udim2(0, 0, 0.5, 0),
        Size            = udim2(0, 4, 1, -6),
        ZIndex          = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    inst:Create("UICorner", { Parent = items.HueDragger.Instance, Name = "\0", CornerRadius = udim(1, 0) })
    inst:Create("UIStroke", { Parent = items.HueDragger.Instance, Name = "\0", Thickness = 1.2, ApplyStrokeMode = Enum.ApplyStrokeMode.Border })

    -- ── Alpha slider ──────────────────────────────────────────────────────
    items.Alpha = inst:Create("TextButton", {
        Parent          = items.Window.Instance,
        Name            = "\0",
        Text            = "",
        AutoButtonColor = false,
        BorderSizePixel = 0,
        AnchorPoint     = vec2(0, 1),
        Position        = udim2(0, 8, 1, -63),
        Size            = udim2(1, -16, 0, 18),
        ZIndex          = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    inst:Create("UICorner", { Parent = items.Alpha.Instance, Name = "\0", CornerRadius = udim(0, 5) })

    -- checkerboard
    inst:Create("ImageLabel", {
        Parent              = items.Alpha.Instance,
        Name                = "\0",
        ScaleType           = Enum.ScaleType.Tile,
        TileSize            = udim2(0, 6, 0, 6),
        Image               = "rbxassetid://1911661212",
        BackgroundTransparency = 1,
        Size                = udim2(1, 0, 1, 0),
        ZIndex              = 2,
        BorderSizePixel     = 0,
        BackgroundColor3    = rgb(255,255,255),
    })

    -- gradient transparência
    inst:Create("UIGradient", {
        Parent       = items.Alpha.Instance,
        Name         = "\0",
        Transparency = numseq{ numkey(0, 1), numkey(0.37, 0.5), numkey(1, 0) },
    })

    items.AlphaDragger = inst:Create("Frame", {
        Parent          = items.Alpha.Instance,
        Name            = "\0",
        BorderSizePixel = 0,
        AnchorPoint     = vec2(0, 0.5),
        Position        = udim2(0, 0, 0.5, 0),
        Size            = udim2(0, 4, 1, -6),
        ZIndex          = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    inst:Create("UICorner", { Parent = items.AlphaDragger.Instance, Name = "\0", CornerRadius = udim(1, 0) })
    inst:Create("UIStroke", { Parent = items.AlphaDragger.Instance, Name = "\0", Thickness = 1.2, ApplyStrokeMode = Enum.ApplyStrokeMode.Border })

    -- ── Hex input ─────────────────────────────────────────────────────────
    local hexBg = inst:Create("Frame", {
        Parent          = items.Window.Instance,
        Name            = "\0",
        AnchorPoint     = vec2(0, 1),
        Position        = udim2(0, 8, 1, -8),
        Size            = udim2(1, -16, 0, 22),
        BorderSizePixel = 0,
        ZIndex          = 2,
        BackgroundColor3 = lib.Theme.Element,
    })
    hexBg:AddToTheme({ BackgroundColor3 = "Element" })
    inst:Create("UICorner", { Parent = hexBg.Instance, Name = "\0", CornerRadius = udim(0, 4) })

    local hexInput = inst:Create("TextBox", {
        Parent              = hexBg.Instance,
        Name                = "\0",
        FontFace            = lib.Font,
        Text                = "#" .. cp.Color:ToHex(),
        TextSize            = 13,
        TextColor3          = lib.Theme.Text,
        PlaceholderText     = "#ffffff",
        PlaceholderColor3   = lib.Theme["Inactive Text"],
        Size                = udim2(1, 0, 1, 0),
        BackgroundTransparency = 1,
        BorderSizePixel     = 0,
        ZIndex              = 3,
        ClearTextOnFocus    = false,
        TextXAlignment      = Enum.TextXAlignment.Center,
        BackgroundColor3    = rgb(255,255,255),
    })
    hexInput:AddToTheme({ TextColor3 = "Text", PlaceholderColor3 = "Inactive Text" })

    -- ── Estado de drag ────────────────────────────────────────────────────
    local slidingPalette = false
    local slidingHue     = false
    local slidingAlpha   = false

    -- ── Update ────────────────────────────────────────────────────────────
    local function update(fromAlpha)
        local c = hsv(cp.Hue, cp.Saturation, cp.Value)
        cp.Color    = c
        cp.HexValue = c:ToHex()

        lib.Flags[flag] = { Color = cp.HexValue, Alpha = cp.Alpha }

        items.Dot:Tween(nil, { BackgroundColor3 = c })
        items.Inline:Tween(nil, { BackgroundColor3 = c })
        items.Palette:Tween(nil, { BackgroundColor3 = hsv(cp.Hue, 1, 1) })

        if not fromAlpha then
            items.Alpha:Tween(nil, { BackgroundColor3 = c })
        end

        hexInput.Instance.Text = "#" .. cp.HexValue

        lib:SafeCall(cb, c, cp.Alpha)
    end

    local tweenInfo = TweenInfo.new(0.08, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)

    local function slidePalette(input)
        if not slidingPalette or not input then return end
        local px = items.Palette.Instance.AbsolutePosition.X
        local py = items.Palette.Instance.AbsolutePosition.Y
        local sx = items.Palette.Instance.AbsoluteSize.X
        local sy = items.Palette.Instance.AbsoluteSize.Y

        cp.Saturation = clamp(1 - (input.Position.X - px) / sx, 0, 1)
        cp.Value      = clamp(1 - (input.Position.Y - py) / sy, 0, 1)

        local dx = clamp((input.Position.X - px) / sx, 0, 0.97)
        local dy = clamp((input.Position.Y - py) / sy, 0, 0.96)
        items.PaletteDragger:Tween(tweenInfo, { Position = udim2(dx, -4, dy, -4) })
        update()
    end

    local function slideHue(input)
        if not slidingHue or not input then return end
        local px = items.Hue.Instance.AbsolutePosition.X
        local sx = items.Hue.Instance.AbsoluteSize.X

        cp.Hue = clamp((input.Position.X - px) / sx, 0, 1)

        local dx = clamp((input.Position.X - px) / sx, 0, 0.98)
        items.HueDragger:Tween(tweenInfo, { Position = udim2(dx, 0, 0.5, 0) })
        update()
    end

    local function slideAlpha(input)
        if not slidingAlpha or not input then return end
        local px = items.Alpha.Instance.AbsolutePosition.X
        local sx = items.Alpha.Instance.AbsoluteSize.X

        cp.Alpha = clamp((input.Position.X - px) / sx, 0, 1)

        local dx = clamp((input.Position.X - px) / sx, 0, 0.98)
        items.AlphaDragger:Tween(tweenInfo, { Position = udim2(dx, 0, 0.5, 0) })
        update(true)
    end

    -- ── Inputs palette ────────────────────────────────────────────────────
    local ic1
    lib:Connect(items.Palette.Instance.InputBegan, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 and
           input.UserInputType ~= Enum.UserInputType.Touch then return end
        slidingPalette = true
        slidePalette(input)
        if ic1 then return end
        ic1 = input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                slidingPalette = false
                ic1:Disconnect(); ic1 = nil
            end
        end)
    end)

    -- ── Inputs hue ───────────────────────────────────────────────────────
    local ic2
    lib:Connect(items.Hue.Instance.InputBegan, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 and
           input.UserInputType ~= Enum.UserInputType.Touch then return end
        slidingHue = true
        slideHue(input)
        if ic2 then return end
        ic2 = input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                slidingHue = false
                ic2:Disconnect(); ic2 = nil
            end
        end)
    end)

    -- ── Inputs alpha ──────────────────────────────────────────────────────
    local ic3
    lib:Connect(items.Alpha.Instance.InputBegan, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 and
           input.UserInputType ~= Enum.UserInputType.Touch then return end
        slidingAlpha = true
        slideAlpha(input)
        if ic3 then return end
        ic3 = input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                slidingAlpha = false
                ic3:Disconnect(); ic3 = nil
            end
        end)
    end)

    -- ── Mouse move global ─────────────────────────────────────────────────
    lib:Connect(uis.InputChanged, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseMovement and
           input.UserInputType ~= Enum.UserInputType.Touch then return end
        if slidingPalette then slidePalette(input) end
        if slidingHue     then slideHue(input)     end
        if slidingAlpha   then slideAlpha(input)   end
    end)

    -- ── Fechar ao clicar fora ─────────────────────────────────────────────
    lib:Connect(uis.InputEnded, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 then return end
        if not cp.IsOpen then return end
        if lib:IsMouseOver(items.Window) then return end
        if lib:IsMouseOver(items.Dot) then return end
        cp:SetOpen(false)
    end)

    -- ── Hex input ─────────────────────────────────────────────────────────
    hexInput:Connect("FocusLost", function()
        local hex = hexInput.Instance.Text:gsub("#", "")
        local ok, c = pcall(Color3.fromHex, hex)
        if ok then cp:Set(c, cp.Alpha) end
    end)

    -- ── SetOpen ───────────────────────────────────────────────────────────
    function cp:SetOpen(bool)
        cp.IsOpen = bool

        if bool then
            for _, f in lib.OpenFrames do
                if f ~= cp and f.Type == "Colorpicker" then
                    f:SetOpen(false)
                end
            end
            lib.OpenFrames[flag] = cp

            items.Window.Instance.Parent  = lib.Holder.Instance
            items.Window.Instance.Visible = true

            task.defer(function()
                local p = items.Dot.Instance.AbsolutePosition
                local s = items.Dot.Instance.AbsoluteSize
                items.Window.Instance.Position = udim2(0, p.X - 200, 0, p.Y + s.Y + 5)
            end)
        else
            lib.OpenFrames[flag] = nil
            items.Window.Instance.Visible = false
            items.Window.Instance.Parent  = lib.UnusedHolder.Instance
        end
    end

    items.Dot:Connect("MouseButton1Down", function()
        cp:SetOpen(not cp.IsOpen)
    end)

    -- ── Get / Set ─────────────────────────────────────────────────────────
    function cp:Get()
        return cp.Color, cp.Alpha
    end

    function cp:Set(color, alpha)
        if type(color) == "string" then
            color = Color3.fromHex(color:gsub("#",""))
        end

        cp.Hue, cp.Saturation, cp.Value = color:ToHSV()
        cp.Alpha = alpha ~= nil and alpha or cp.Alpha

        local dx = clamp(1 - cp.Saturation, 0, 0.97)
        local dy = clamp(1 - cp.Value,      0, 0.96)
        local hx = clamp(cp.Hue,            0, 0.98)
        local ax = clamp(cp.Alpha,          0, 0.98)

        items.PaletteDragger:Tween(tweenInfo, { Position = udim2(dx, -4, dy, -4) })
        items.HueDragger:Tween(tweenInfo,     { Position = udim2(hx, 0, 0.5, 0) })
        items.AlphaDragger:Tween(tweenInfo,   { Position = udim2(ax, 0, 0.5, 0) })
        update()
    end

    -- ── Init com default ──────────────────────────────────────────────────
    if data.Default then
        cp:Set(data.Default, data.Alpha or 0)
    end

    lib.Flags[flag]    = { Color = cp.Color:ToHex(), Alpha = cp.Alpha }
    lib.SetFlags[flag] = function(hex, a)
        local ok, c = pcall(Color3.fromHex, (hex or ""):gsub("#",""))
        if ok then cp:Set(c, a) end
    end

    getgenv().Options[flag] = cp

    return cp, items
end

-- SLIDER
comp.Slider = function(data)
    local name     = data.Name
    local parent   = data.Parent
    local flag     = data.Flag
    local min      = data.Min or 0
    local max      = data.Max or 100
    local default  = data.Default or min
    local decimals = data.Decimals or 1
    local suffix   = data.Suffix or ""
    local cb       = data.Callback or function() end

    local items = {}
    local sliding = false
    local value = default

    items.Slider = inst:Create("Frame", {
        Parent = parent.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        Size = udim2(1, 0, 0, 38),
        BorderColor3 = rgb(0,0,0),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = rgb(255,255,255),
    })

    items.Text = inst:Create("TextLabel", {
        Parent = items.Slider.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AutomaticSize = Enum.AutomaticSize.X,
        Size = udim2(0,0,0,15),
        BackgroundTransparency = 1,
        TextXAlignment = Enum.TextXAlignment.Left,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Text:AddToTheme({ TextColor3 = "Text" })

    items.Value = inst:Create("TextLabel", {
        Parent = items.Slider.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = "",
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AutomaticSize = Enum.AutomaticSize.X,
        AnchorPoint = vec2(1, 0),
        Size = udim2(0,0,0,15),
        Position = udim2(1,0,0,0),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Value:AddToTheme({ TextColor3 = "Text" })

    items.Track = inst:Create("TextButton", {
        Parent = items.Slider.Instance,
        Name = "\0",
        Text = "",
        AutoButtonColor = false,
        AnchorPoint = vec2(0, 1),
        Position = udim2(0,0,1,0),
        Size = udim2(1,0,0,15),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Element,
    })
    items.Track:AddToTheme({ BackgroundColor3 = "Element" })
    inst:Create("UICorner", { Parent = items.Track.Instance, Name = "\0", CornerRadius = udim(0, 4) })
    inst:Create("UIGradient", {
        Parent = items.Track.Instance, Name = "\0",
        Rotation = 84,
        Color = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, rgb(211,211,211)) },
    }):AddToTheme({ Color = function()
        return rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) }
    end })

    items.Fill = inst:Create("Frame", {
        Parent = items.Track.Instance,
        Name = "\0",
        Size = udim2(0, 0, 1, 0),
        ZIndex = 3,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Accent,
    })
    items.Fill:AddToTheme({ BackgroundColor3 = "Accent" })
    inst:Create("UICorner", { Parent = items.Fill.Instance, Name = "\0", CornerRadius = udim(0, 4) })
    inst:Create("UIGradient", {
        Parent = items.Fill.Instance, Name = "\0",
        Rotation = 84,
        Color = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, rgb(211,211,211)) },
    }):AddToTheme({ Color = function()
        return rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) }
    end })

    items.Track:OnHover(function()
        items.Track:Tween(nil, { BackgroundColor3 = lib:GetLighterColor(lib.Theme.Element, 1.45) })
    end)
    items.Track:OnHoverLeave(function()
        items.Track:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
    end)

    local obj = { Value = value, Flag = flag, Min = min, Max = max, Decimals = decimals, Suffix = suffix, Callback = cb }

    function obj:Set(v)
        self.Value = lib.Round(clamp(v, min, max), decimals)
        lib.Flags[flag] = self.Value
        items.Fill:Tween(TweenInfo.new(0.21, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Size = udim2((self.Value - min) / (max - min), 0, 1, 0)
        })
        items.Value.Instance.Text = fmt("%s%s", tostring(self.Value), suffix)
        lib:SafeCall(cb, self.Value)
    end

    function obj:Get() return self.Value end
    function obj:SetVisibility(bool) items.Slider.Instance.Visible = bool end

    local ic
    items.Track:Connect("InputBegan", function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch then return end
        sliding = true
        local sx = (input.Position.X - items.Track.Instance.AbsolutePosition.X) / items.Track.Instance.AbsoluteSize.X
        obj:Set(((max - min) * sx) + min)

        if ic then return end
        ic = input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                sliding = false; ic:Disconnect(); ic = nil
            end
        end)
    end)

    lib:Connect(uis.InputChanged, function(input)
        if not sliding then return end
        if input.UserInputType ~= Enum.UserInputType.MouseMovement and input.UserInputType ~= Enum.UserInputType.Touch then return end
        local sx = (input.Position.X - items.Track.Instance.AbsolutePosition.X) / items.Track.Instance.AbsoluteSize.X
        obj:Set(((max - min) * sx) + min)
    end)

    lib.Flags[flag] = default
    lib.SetFlags[flag] = function(v) obj:Set(v) end
    obj:Set(default)

    return obj, items
end

-- DROPDOWN
comp.Dropdown = function(data)
    local name     = data.Name
    local parent   = data.Parent
    local flag     = data.Flag
    local optList  = data.Items or {}
    local default  = data.Default
    local maxsize  = data.MaxSize or 165
    local multi    = data.Multi or false
    local cb       = data.Callback or function() end
    local winRef   = data.Window

    local items = {}
    local isOpen  = false
    local debounce = false
    local value   = multi and {} or nil
    local options = {}
    local rs

    -- wrapper
    items.Dropdown = inst:Create("Frame", {
        Parent = parent.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        Size = udim2(1, 0, 0, 47),
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })

    items.Text = inst:Create("TextLabel", {
        Parent = items.Dropdown.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AutomaticSize = Enum.AutomaticSize.X,
        Size = udim2(0,0,0,15),
        BackgroundTransparency = 1,
        TextXAlignment = Enum.TextXAlignment.Left,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Text:AddToTheme({ TextColor3 = "Text" })

    items.RealDropdown = inst:Create("TextButton", {
        Parent = items.Dropdown.Instance,
        Name = "\0",
        Text = "",
        AutoButtonColor = false,
        AnchorPoint = vec2(0, 1),
        Position = udim2(0,0,1,0),
        Size = udim2(1,0,0,25),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Element,
    })
    items.RealDropdown:AddToTheme({ BackgroundColor3 = "Element" })
    inst:Create("UICorner", { Parent = items.RealDropdown.Instance, Name = "\0", CornerRadius = udim(0, 4) })
    inst:Create("UIGradient", {
        Parent = items.RealDropdown.Instance, Name = "\0",
        Rotation = 84,
        Color = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, rgb(211,211,211)) },
    }):AddToTheme({ Color = function()
        return rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) }
    end })

    items.Value = inst:Create("TextLabel", {
        Parent = items.RealDropdown.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = "--",
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AutomaticSize = Enum.AutomaticSize.X,
        AnchorPoint = vec2(0, 0.5),
        Size = udim2(0,0,0,15),
        Position = udim2(0,8,0.5,0),
        BackgroundTransparency = 1,
        TextTruncate = Enum.TextTruncate.AtEnd,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Value:AddToTheme({ TextColor3 = "Text" })

    items.OpenIcon = inst:Create("ImageLabel", {
        Parent = items.RealDropdown.Instance,
        Name = "\0",
        ImageColor3 = lib.Theme.Accent,
        ScaleType = Enum.ScaleType.Fit,
        Image = lib.Icons.Grid,
        AnchorPoint = vec2(1, 0.5),
        Position = udim2(1,-3,0.5,0),
        Size = udim2(0,20,0,20),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.OpenIcon:AddToTheme({ ImageColor3 = "Accent" })

    -- option holder (flutuante)
    items.OptionHolder = inst:Create("TextButton", {
        Parent = lib.UnusedHolder.Instance,
        Name = "\0",
        Text = "",
        AutoButtonColor = false,
        Visible = false,
        Size = udim2(1,0,0,50),
        Position = udim2(0,0,1,5),
        BorderSizePixel = 0,
        ZIndex = 10,
        BackgroundColor3 = lib.Theme.Inline,
    })
    items.OptionHolder:AddToTheme({ BackgroundColor3 = "Inline" })
    inst:Create("UICorner", { Parent = items.OptionHolder.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIStroke", {
        Parent = items.OptionHolder.Instance, Name = "\0",
        Color = lib.Theme.Border, Transparency = 0.4,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    }):AddToTheme({ Color = "Border" })
    inst:Create("UIGradient", {
        Parent = items.OptionHolder.Instance, Name = "\0",
        Rotation = 84,
        Color = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, rgb(211,211,211)) },
    }):AddToTheme({ Color = function()
        return rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) }
    end })

    -- search
    items.Input = inst:Create("TextBox", {
        Parent = items.OptionHolder.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = "",
        PlaceholderText = "search",
        PlaceholderColor3 = lib.Theme["Inactive Text"],
        TextSize = 14,
        TextColor3 = lib.Theme.Text,
        Size = udim2(1,0,0,25),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ClearTextOnFocus = false,
        ZIndex = 11,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Input:AddToTheme({ TextColor3 = "Text", PlaceholderColor3 = "Inactive Text" })
    inst:Create("UIPadding", { Parent = items.Input.Instance, Name = "\0", PaddingLeft = udim(0,8) })

    inst:Create("Frame", {
        Parent = items.OptionHolder.Instance,
        Name = "\0",
        AnchorPoint = vec2(0,0),
        Position = udim2(0,0,0,25),
        Size = udim2(1,0,0,1),
        BorderSizePixel = 0,
        BackgroundTransparency = 0.4,
        ZIndex = 11,
        BackgroundColor3 = lib.Theme.Border,
    }):AddToTheme({ BackgroundColor3 = "Border" })

    items.OptionsFrame = inst:Create("ScrollingFrame", {
        Parent = items.OptionHolder.Instance,
        Name = "\0",
        Position = udim2(0,0,0,26),
        Size = udim2(1,0,1,-26),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ScrollBarThickness = 2,
        ScrollBarImageColor3 = lib.Theme.Accent,
        AutomaticCanvasSize = Enum.AutomaticSize.Y,
        CanvasSize = udim2(0,0,0,0),
        ZIndex = 11,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.OptionsFrame:AddToTheme({ ScrollBarImageColor3 = "Accent" })
    inst:Create("UIListLayout", {
        Parent = items.OptionsFrame.Instance,
        Name = "\0",
        Padding = udim(0,2),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })
    inst:Create("UIPadding", {
        Parent = items.OptionsFrame.Instance,
        Name = "\0",
        PaddingTop = udim(0,3), PaddingBottom = udim(0,3),
        PaddingLeft = udim(0,3), PaddingRight = udim(0,3),
    })

    items.RealDropdown:OnHover(function()
        items.RealDropdown:Tween(nil, { BackgroundColor3 = lib:GetLighterColor(lib.Theme.Element, 1.45) })
    end)
    items.RealDropdown:OnHoverLeave(function()
        items.RealDropdown:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
    end)

    local obj = { IsOpen = false, Value = multi and {} or nil, Options = options, Multi = multi, Flag = flag, Callback = cb, Name = name }

    function obj:SetOpen(bool)
        if debounce then return end
        isOpen = bool
        obj.IsOpen = bool

        items.OptionHolder.Instance.Parent = bool and lib.Holder.Instance or lib.UnusedHolder.Instance
        debounce = true

        if bool then
            items.OptionHolder.Instance.Visible = true
            items.OptionHolder.Instance.ZIndex = 11

            rs = run.RenderStepped:Connect(function()
                items.OptionHolder.Instance.Position = udim2(0, items.RealDropdown.Instance.AbsolutePosition.X, 0, items.RealDropdown.Instance.AbsolutePosition.Y + 30)
                items.OptionHolder.Instance.Size = udim2(0, items.RealDropdown.Instance.AbsoluteSize.X, 0, maxsize)
            end)

            for k, f in lib.OpenFrames do
                if k ~= name and f.SetOpen then
                    f:SetOpen(false)
                end
            end
            lib.OpenFrames[name] = obj
        else
            if lib.OpenFrames[name] then lib.OpenFrames[name] = nil end
            if rs then rs:Disconnect(); rs = nil end
        end

        local kids = items.OptionHolder.Instance:GetDescendants()
        ins(kids, items.OptionHolder.Instance)

        local last
        for _, v in kids do
            local props = tw:GetProperty(v)
            if not props then continue end
            if find(v.ClassName, "UI") then continue end
            v.ZIndex = bool and 10 or 0
            for _, prop in props do
                last = tw:FadeItem(v, prop, bool, winRef and winRef.FadeSpeed or lib.Tween.Time)
            end
        end

        if last then
            lib:Connect(last.Tween.Completed, function()
                debounce = false
                items.OptionHolder.Instance.Visible = bool
            end)
        else
            debounce = false
        end
    end

    function obj:AddOption(optName)
        local btn = inst:Create("TextButton", {
            Parent = items.OptionsFrame.Instance,
            Name = "\0",
            Text = "",
            AutoButtonColor = false,
            BackgroundTransparency = 1,
            Size = udim2(1,0,0,25),
            ZIndex = 12,
            BorderSizePixel = 0,
            BackgroundColor3 = lib.Theme.Background,
        })
        btn:AddToTheme({ BackgroundColor3 = "Background" })
        inst:Create("UICorner", { Parent = btn.Instance, Name = "\0", CornerRadius = udim(0, 5) })

        local checkImg = inst:Create("ImageLabel", {
            Parent = btn.Instance,
            Name = "\0",
            ImageColor3 = lib.Theme.Accent,
            Image = lib.Icons.Check,
            AnchorPoint = vec2(0, 0.5),
            Position = udim2(0, 3, 0.5, 0),
            Size = udim2(0, 18, 0, 18),
            ImageTransparency = 1,
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            ZIndex = 13,
            BackgroundColor3 = rgb(255,255,255),
        })
        checkImg:AddToTheme({ ImageColor3 = "Accent" })

        local label = inst:Create("TextLabel", {
            Parent = btn.Instance,
            Name = "\0",
            FontFace = lib.Font,
            Text = optName,
            TextSize = 14,
            TextTransparency = 0.5,
            TextColor3 = rgb(255,255,255),
            AnchorPoint = vec2(0, 0.5),
            Position = udim2(0, 7, 0.5, 0),
            AutomaticSize = Enum.AutomaticSize.X,
            Size = udim2(0,0,0,15),
            BackgroundTransparency = 1,
            TextXAlignment = Enum.TextXAlignment.Left,
            BorderSizePixel = 0,
            ZIndex = 13,
            BackgroundColor3 = rgb(255,255,255),
        })
        label:AddToTheme({ TextColor3 = "Text" })

        local od = { Selected = false, Name = optName, RealName = optName, Button = btn, Text = label, Check = checkImg }

        function od:Toggle(status)
            if status == "Active" then
                od.Button:Tween(nil, { BackgroundTransparency = 0 })
                od.Text:Tween(nil, { TextTransparency = 0, Position = udim2(0, 27, 0.5, 0) })
                od.Check:Tween(nil, { ImageTransparency = 0 })
            elseif status == "Inactive" then
                od.Button:Tween(nil, { BackgroundTransparency = 1 })
                od.Text:Tween(nil, { TextTransparency = 0.5, Position = udim2(0, 7, 0.5, 0) })
                od.Check:Tween(nil, { ImageTransparency = 1 })
            end
        end

        function od:Set()
            od.Selected = not od.Selected
            if multi then
                local idx = tfind(obj.Value, od.RealName)
                if idx then rem(obj.Value, idx) else ins(obj.Value, od.RealName) end
                lib.Flags[flag] = obj.Value
                od:Toggle(idx and "Inactive" or "Active")
                items.Value.Instance.Text = #obj.Value > 0 and concat(obj.Value, ", ") or "--"
            else
                if od.Selected then
                    obj.Value = od.RealName
                    lib.Flags[flag] = od.RealName
                    od:Toggle("Active")
                    for _, other in options do
                        if other ~= od then other.Selected = false; other:Toggle("Inactive") end
                    end
                    items.Value.Instance.Text = od.RealName
                else
                    obj.Value = nil
                    lib.Flags[flag] = nil
                    od:Toggle("Inactive")
                    items.Value.Instance.Text = "--"
                end
            end
            lib:SafeCall(cb, obj.Value)
        end

        btn:Connect("MouseButton1Down", function() od:Set() end)
        options[optName] = od
        return od
    end

    function obj:RemoveOption(n)
        if options[n] then options[n].Button:Clean(); options[n] = nil end
    end

    function obj:Refresh(list)
        for k in options do obj:RemoveOption(k) end
        for _, v in list do obj:AddOption(v) end
    end

    function obj:Set(opt)
        if multi then
            if type(opt) ~= "table" then return end
            obj.Value = opt
            lib.Flags[flag] = opt
            for _, v in opt do
                if options[v] then options[v].Selected = true; options[v]:Toggle("Active") end
            end
            items.Value.Instance.Text = concat(opt, ", ")
        else
            if not options[opt] then return end
            local od = options[opt]
            obj.Value = od.Name
            lib.Flags[flag] = od.Name
            for _, other in options do
                if other ~= od then other.Selected = false; other:Toggle("Inactive")
                else other.Selected = true; other:Toggle("Active") end
            end
            items.Value.Instance.Text = od.Name
        end
        lib:SafeCall(cb, obj.Value)
    end

    function obj:SetVisibility(bool) items.Dropdown.Instance.Visible = bool end

    -- search
    local searchStepped
    items.Input:Connect("Focused", function()
        if searchStepped then return end
        searchStepped = run.RenderStepped:Connect(function()
            for _, od in options do
                od.Button.Instance.Visible = find(lower(od.Name), lower(items.Input.Instance.Text)) ~= nil
            end
        end)
    end)
    items.Input:Connect("FocusLost", function()
        if searchStepped then searchStepped:Disconnect(); searchStepped = nil end
    end)

    items.RealDropdown:Connect("MouseButton1Down", function()
        obj:SetOpen(not isOpen)
    end)

    lib:Connect(uis.InputBegan, function(input)
        if input.UserInputType ~= Enum.UserInputType.MouseButton1 then return end
        if lib:IsMouseOver(items.OptionHolder) then return end
        if debounce then return end
        if not isOpen then return end
        obj:SetOpen(false)
    end)

    for _, v in optList do obj:AddOption(v) end

    lib.Flags[flag] = value
    lib.SetFlags[flag] = function(v) obj:Set(v) end

    if default then obj:Set(default) end

    getgenv().Options = getgenv().Options or {}
    getgenv().Options[flag] = obj

    return obj, items
end

-- BUTTON
comp.Button = function(data)
    local name   = data.Name
    local parent = data.Parent
    local cb     = data.Callback or function() end
    local tooltip= data.Tooltip

    local items = {}

    items.Button = inst:Create("TextButton", {
        Parent = parent.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = "",
        AutoButtonColor = false,
        Size = udim2(1,0,0,30),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Element,
    })
    items.Button:AddToTheme({ BackgroundColor3 = "Element" })
    inst:Create("UICorner", { Parent = items.Button.Instance, Name = "\0", CornerRadius = udim(0, 4) })
    inst:Create("UIGradient", {
        Parent = items.Button.Instance, Name = "\0",
        Rotation = 84,
        Color = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, rgb(211,211,211)) },
    }):AddToTheme({ Color = function()
        return rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) }
    end })

    items.Text = inst:Create("TextLabel", {
        Parent = items.Button.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        Size = udim2(1,0,1,0),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Text:AddToTheme({ TextColor3 = "Text" })

    items.Button:Tooltip(tooltip)

    items.Button:OnHover(function()
        items.Button:Tween(nil, { BackgroundColor3 = lib:GetLighterColor(lib.Theme.Element, 1.45) })
    end)
    items.Button:OnHoverLeave(function()
        items.Button:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
    end)

    local obj = { Callback = cb }

    function obj:Press()
        task.spawn(function()
            items.Button:ChangeItemTheme({ BackgroundColor3 = "Accent" })
            items.Button:Tween(nil, { BackgroundColor3 = lib.Theme.Accent })
            task.wait(0.1)
            lib:SafeCall(cb)
            items.Button:ChangeItemTheme({ BackgroundColor3 = "Element" })
            items.Button:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
        end)
    end

    function obj:SetVisibility(bool) items.Button.Instance.Visible = bool end

    items.Button:Connect("MouseButton1Down", function() obj:Press() end)

    return obj, items
end

-- TEXTBOX
comp.Textbox = function(data)
    local name       = data.Name
    local parent     = data.Parent
    local flag       = data.Flag
    local default    = data.Default
    local placeholder= data.Placeholder or ""
    local cb         = data.Callback or function() end
    local tooltip    = data.Tooltip

    local items = {}
    local value = default or ""

    items.Textbox = inst:Create("Frame", {
        Parent = parent.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        Size = udim2(1,0,0,47),
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })

    items.Textbox:Tooltip(tooltip)

    items.Text = inst:Create("TextLabel", {
        Parent = items.Textbox.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AutomaticSize = Enum.AutomaticSize.X,
        Size = udim2(0,0,0,15),
        BackgroundTransparency = 1,
        TextXAlignment = Enum.TextXAlignment.Left,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Text:AddToTheme({ TextColor3 = "Text" })

    items.Background = inst:Create("Frame", {
        Parent = items.Textbox.Instance,
        Name = "\0",
        AnchorPoint = vec2(0,1),
        Position = udim2(0,0,1,0),
        Size = udim2(1,0,0,25),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Element,
    })
    items.Background:AddToTheme({ BackgroundColor3 = "Element" })
    inst:Create("UICorner", { Parent = items.Background.Instance, Name = "\0", CornerRadius = udim(0, 4) })
    inst:Create("UIGradient", {
        Parent = items.Background.Instance, Name = "\0",
        Rotation = 84,
        Color = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, rgb(211,211,211)) },
    })

    items.Background:OnHover(function()
        items.Background:Tween(nil, { BackgroundColor3 = lib:GetLighterColor(lib.Theme.Element, 1.45) })
    end)
    items.Background:OnHoverLeave(function()
        items.Background:Tween(nil, { BackgroundColor3 = lib.Theme.Element })
    end)

    items.Input = inst:Create("TextBox", {
        Parent = items.Background.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = "",
        PlaceholderText = placeholder,
        PlaceholderColor3 = lib.Theme["Inactive Text"],
        TextColor3 = lib.Theme.Text,
        TextSize = 14,
        Size = udim2(1,0,1,0),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ClipsDescendants = true,
        ClearTextOnFocus = false,
        TextXAlignment = Enum.TextXAlignment.Left,
        CursorPosition = -1,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Input:AddToTheme({ TextColor3 = "Text", PlaceholderColor3 = "Inactive Text" })
    inst:Create("UIPadding", { Parent = items.Input.Instance, Name = "\0", PaddingLeft = udim(0,8) })

    local obj = { Value = value, Flag = flag, Callback = cb }

    function obj:Set(v)
        self.Value = tostring(v)
        items.Input.Instance.Text = self.Value
        lib.Flags[flag] = self.Value
        items.Input:ChangeItemTheme({ TextColor3 = "Text" })
        items.Input:Tween(nil, { TextColor3 = lib.Theme.Text })
        lib:SafeCall(cb, self.Value)
    end

    function obj:Get() return self.Value end
    function obj:SetVisibility(bool) items.Textbox.Instance.Visible = bool end

    items.Input:Connect("Focused", function()
        items.Input:ChangeItemTheme({ TextColor3 = "Accent" })
        items.Input:Tween(nil, { TextColor3 = lib.Theme.Accent })
    end)

    items.Input:Connect("FocusLost", function()
        obj:Set(items.Input.Instance.Text)
    end)

    lib.Flags[flag] = value
    lib.SetFlags[flag] = function(v) obj:Set(v) end
    if default then obj:Set(default) end

    getgenv().Options = getgenv().Options or {}
    getgenv().Options[flag] = obj

    return obj, items
end

-- LABEL
comp.Label = function(data)
    local name      = data.Name
    local parent    = data.Parent
    local alignment = data.Alignment or "Left"

    local items = {}

    local xalign = alignment == "Center" and Enum.TextXAlignment.Center
        or alignment == "Right" and Enum.TextXAlignment.Right
        or Enum.TextXAlignment.Left

    items.Label = inst:Create("Frame", {
        Parent = parent.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        Size = udim2(1,0,0,24),
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })

    items.Text = inst:Create("TextLabel", {
        Parent = items.Label.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AnchorPoint = vec2(0, 0.5),
        Position = udim2(0,0,0.5,0),
        Size = udim2(1,0,0,15),
        BackgroundTransparency = 1,
        TextXAlignment = xalign,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Text:AddToTheme({ TextColor3 = "Text" })

    -- sub-elements holder (keybind, colorpicker)
    items.SubElements = inst:Create("Frame", {
        Parent = items.Label.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        AnchorPoint = vec2(1, 0.5),
        Position = udim2(1,0,0.5,0),
        Size = udim2(0,0,0,24),
        AutomaticSize = Enum.AutomaticSize.X,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })

    inst:Create("UIListLayout", {
        Parent = items.SubElements.Instance,
        Name = "\0",
        FillDirection = Enum.FillDirection.Horizontal,
        HorizontalAlignment = Enum.HorizontalAlignment.Right,
        VerticalAlignment = Enum.VerticalAlignment.Center,
        Padding = udim(0, 5),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })

    local obj = { Items = items, Count = 0 }
    function obj:SetText(t) items.Text.Instance.Text = t end
    function obj:SetVisibility(bool) items.Label.Instance.Visible = bool end

    return obj, items
end

-- ─── SECTIONS ───────────────────────────────────────────────────────────────

lib.Sections.Section = function(self, data)
    data = data or {}

    local sec = {
        Window  = self.Window,
        Page    = self,
        Name    = data.Name or data.name or "Section",
        Side    = data.Side or data.side or 1,
        Icon    = data.Icon or data.icon or "111178525804834",
        Items   = {},
    }

    local items = {}

    items.Section = inst:Create("Frame", {
        Parent = sec.Page.ColumnsData[sec.Side].Instance,
        Name = "\0",
        BorderSizePixel = 0,
        Size = udim2(1,0,0,55),
        AutomaticSize = Enum.AutomaticSize.Y,
        ZIndex = 2,
        BackgroundColor3 = lib.Theme.Inline,
    })
    items.Section:AddToTheme({ BackgroundColor3 = "Inline" })
    inst:Create("UICorner", { Parent = items.Section.Instance, Name = "\0", CornerRadius = udim(0, 5) })

    items.Topbar = inst:Create("Frame", {
        Parent = items.Section.Instance,
        Name = "\0",
        Size = udim2(1,0,0,35),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Inline,
    })
    items.Topbar:AddToTheme({ BackgroundColor3 = "Inline" })
    inst:Create("UICorner", { Parent = items.Topbar.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIGradient", {
        Parent = items.Topbar.Instance, Name = "\0",
        Rotation = 84,
        Color = rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, rgb(211,211,211)) },
    }):AddToTheme({ Color = function()
        return rgbseq{ rgbkey(0, rgb(255,255,255)), rgbkey(1, lib.Theme["Dark Gradient"]) }
    end })

    -- tampa o arredondamento inferior do topbar
    inst:Create("Frame", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        AnchorPoint = vec2(0,1),
        Position = udim2(0,0,1,0),
        Size = udim2(1,0,0,3),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Inline,
    }):AddToTheme({ BackgroundColor3 = "Inline" })

    -- border bottom do topbar
    inst:Create("Frame", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        AnchorPoint = vec2(0,1),
        BackgroundTransparency = 0.4,
        Position = udim2(0,0,1,0),
        Size = udim2(1,0,0,1),
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Border,
    }):AddToTheme({ BackgroundColor3 = "Border" })

    items.Title = inst:Create("TextLabel", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = sec.Name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AnchorPoint = vec2(0, 0.5),
        Position = udim2(0,8,0.5,0),
        Size = udim2(1,-30,0,15),
        BackgroundTransparency = 1,
        TextXAlignment = Enum.TextXAlignment.Left,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Title:AddToTheme({ TextColor3 = "Text" })

    items.Icon = inst:Create("ImageLabel", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        ImageColor3 = lib.Theme.Accent,
        ScaleType = Enum.ScaleType.Fit,
        Image = "rbxassetid://" .. sec.Icon,
        AnchorPoint = vec2(1, 0.5),
        Position = udim2(1,-7,0.5,-1),
        Size = udim2(0,18,0,18),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Icon:AddToTheme({ ImageColor3 = "Accent" })

    inst:Create("UIPadding", { Parent = items.Section.Instance, Name = "\0", PaddingBottom = udim(0,8) })

    items.Content = inst:Create("Frame", {
        Parent = items.Section.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        Position = udim2(0,8,0,45),
        Size = udim2(1,-16,0,0),
        AutomaticSize = Enum.AutomaticSize.Y,
        ZIndex = 2,
        BorderSizePixel = 0,
        BackgroundColor3 = rgb(255,255,255),
    })

    inst:Create("UIListLayout", {
        Parent = items.Content.Instance,
        Name = "\0",
        Padding = udim(0,8),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })

    sec.Items = items

    local secMeta = setmetatable(sec, lib.Sections)

    -- wrapper de cada elemento
    function secMeta:Toggle(d)
        d = d or {}
        d.Parent = items.Content
        d.Page   = sec.Page
        d.Flag   = d.Flag or d.flag or lib:NextFlag()
        local obj, i = comp.Toggle(d)

        -- search
        do local _sd = lib.SearchItems[sec.Page]; if _sd then ins(_sd, { Name = d.Name or d.name or "Toggle", Item = i.Toggle }) end end

        local tog = { _obj = obj, _items = i, Window = sec.Window, Page = sec.Page, Section = sec, Count = 0 }
        setmetatable(tog, { __index = lib.Sections })

        function tog:Set(v) obj:Set(v) end
        function tog:Get() return obj:Get() end
        function tog:SetVisibility(v) obj:SetVisibility(v) end

        function tog:Keybind(kd)
            kd = kd or {}
            kd.Parent    = i.SubElements
            kd.Window    = sec.Window
            kd.Flag      = kd.Flag or kd.flag or lib:NextFlag()
            kd.IsToggle  = true
            local userCb = kd.Callback or kd.callback
            kd.Callback  = function(k)
                obj:Set(not obj.Value)
                if userCb then lib:SafeCall(userCb, k) end
            end
            local kobj = comp.Keybind(kd)
            return kobj
        end

        function tog:Colorpicker(cd)
            cd = cd or {}
            cd.Parent   = i.SubElements
            cd.Window   = sec.Window
            cd.Flag     = cd.Flag or cd.flag or lib:NextFlag()
            cd.IsToggle = true
            local cobj = comp.Colorpicker(cd)
            return cobj
        end

        return tog
    end

    function secMeta:Slider(d)
        d = d or {}
        d.Parent = items.Content
        d.Page   = sec.Page
        d.Flag   = d.Flag or d.flag or lib:NextFlag()
        local obj, i = comp.Slider(d)
        do local _sd = lib.SearchItems[sec.Page]; if _sd then ins(_sd, { Name = d.Name or d.name or "Slider", Item = i.Slider }) end end
        return obj
    end

    function secMeta:Dropdown(d)
        d = d or {}
        d.Parent = items.Content
        d.Page   = sec.Page
        d.Window = sec.Window
        d.Flag   = d.Flag or d.flag or lib:NextFlag()
        local obj, i = comp.Dropdown(d)
        do local _sd = lib.SearchItems[sec.Page]; if _sd then ins(_sd, { Name = d.Name or d.name or "Dropdown", Item = i.Dropdown }) end end
        return obj
    end

    function secMeta:Button(d)
        d = d or {}
        d.Parent = items.Content
        local obj, i = comp.Button(d)
        do local _sd = lib.SearchItems[sec.Page]; if _sd then ins(_sd, { Name = d.Name or d.name or "Button", Item = i.Button }) end end
        return obj
    end

    function secMeta:Textbox(d)
        d = d or {}
        d.Parent = items.Content
        d.Page   = sec.Page
        d.Flag   = d.Flag or d.flag or lib:NextFlag()
        local obj, i = comp.Textbox(d)
        do local _sd = lib.SearchItems[sec.Page]; if _sd then ins(_sd, { Name = d.Name or d.name or "Textbox", Item = i.Textbox }) end end
        return obj
    end

    function secMeta:Label(d)
        d = d or {}
        d.Parent = items.Content
        local obj, i = comp.Label(d)

        local lbl = {}
        setmetatable(lbl, { __index = lib.Sections })

        function lbl:SetText(t) obj:SetText(t) end
        function lbl:SetVisibility(v) obj:SetVisibility(v) end

        function lbl:Keybind(kd)
            kd = kd or {}
            kd.Parent   = i.SubElements
            kd.Window   = sec.Window
            kd.Flag     = kd.Flag or kd.flag or lib:NextFlag()
            kd.IsToggle = true
            return comp.Keybind(kd)
        end

        function lbl:Colorpicker(cd)
            cd = cd or {}
            cd.Parent   = i.SubElements
            cd.Window   = sec.Window
            cd.Flag     = cd.Flag or cd.flag or lib:NextFlag()
            cd.IsToggle = true
            return comp.Colorpicker(cd)
        end

        return lbl
    end

    return secMeta
end

-- ─── PAGES ──────────────────────────────────────────────────────────────────

lib.Pages.Section = lib.Sections.Section

lib.Pages.Page = function(self, data)
    data = data or {}

    local page = {
        Window      = self.Window,
        Name        = data.Name or data.name or "Page",
        Icon        = data.Icon or data.icon,
        Columns     = data.Columns or data.columns or 2,
        ColumnsData = {},
        Items       = {},
    }

    lib.SearchItems[page] = {}

    local items = {}

    items.Page = inst:Create("Frame", {
        Parent = lib.UnusedHolder.Instance,
        Name = "\0",
        BackgroundTransparency = 1,
        Size = udim2(1,0,1,0),
        BorderSizePixel = 0,
        Visible = false,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })

    inst:Create("UIListLayout", {
        Parent = items.Page.Instance,
        Name = "\0",
        FillDirection = Enum.FillDirection.Horizontal,
        Padding = udim(0, 14),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })

    for i = 1, page.Columns do
        local col = inst:Create("ScrollingFrame", {
            Parent = items.Page.Instance,
            Name = "\0",
            BackgroundTransparency = 1,
            Size = udim2(1/page.Columns, i < page.Columns and -7 or 0, 1, 0),
            BorderSizePixel = 0,
            ScrollBarThickness = 2,
            ScrollBarImageColor3 = lib.Theme.Accent,
            AutomaticCanvasSize = Enum.AutomaticSize.Y,
            CanvasSize = udim2(0,0,0,0),
            ZIndex = 2,
            BackgroundColor3 = rgb(255,255,255),
        })
        col:AddToTheme({ ScrollBarImageColor3 = "Accent" })
        inst:Create("UIListLayout", {
            Parent = col.Instance, Name = "\0",
            Padding = udim(0,14),
            SortOrder = Enum.SortOrder.LayoutOrder,
        })
        page.ColumnsData[i] = col
    end

    items.Page.Instance.Visible = false
    page.Items = items

    local pageMeta = setmetatable(page, lib.Pages)
    pageMeta.Section = lib.Sections.Section

    -- adiciona ao bottom bar do window
    self.Window:_addPageButton(pageMeta, items.Page)

    return pageMeta
end

-- ─── WINDOW ─────────────────────────────────────────────────────────────────

lib.Window = function(self, data)
    data = data or {}

    local win = {
        Name       = data.Name or data.name or "Alemanha",
        Version    = data.Version or data.version or "v1.0",
        Logo       = data.Logo or data.logo or "111178525804834",
        Size       = data.Size or data.size or udim2(0, 659, 0, 511),
        FadeSpeed  = data.FadeSpeed or data.fadespeed or 0.24,
        IsOpen     = true,
        Pages      = {},
        Items      = {},
    }

    local items = {}
    local pageFrames = {}  -- { page, frame }
    local currentPage = nil
    local pageBtns = {}

    -- main frame
    items.MainFrame = inst:Create("Frame", {
        Parent = lib.Holder.Instance,
        Name = "\0",
        Size = win.Size,
        AnchorPoint = vec2(0.5, 0.5),
        Position = udim2(0.5,0,0.5,0),
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = lib.Theme.Background,
    })
    items.MainFrame:AddToTheme({ BackgroundColor3 = "Background" })
    inst:Create("UICorner", { Parent = items.MainFrame.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIStroke", {
        Parent = items.MainFrame.Instance, Name = "\0",
        Color = lib.Theme.Border, Transparency = 0.4,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    }):AddToTheme({ Color = "Border" })

    inst:Create("ImageLabel", {
        Parent = items.MainFrame.Instance,
        Name = "\0",
        ImageColor3 = lib.Theme.Shadow,
        ScaleType = Enum.ScaleType.Slice,
        ImageTransparency = 0.9,
        BorderColor3 = rgb(0, 0, 0),
        Size = udim2(1, 25, 1, 25),
        AnchorPoint = vec2(0.5, 0.5),
        Image = "http://www.roblox.com/asset/?id=18245826428",
        BackgroundTransparency = 1,
        Position = udim2(0.5, 0, 0.5, 0),
        BackgroundColor3 = rgb(255, 255, 255),
        BorderSizePixel = 0,
        SliceCenter = Rect.new(Vector2.new(21, 21), Vector2.new(79, 79)),
        ZIndex = 1,
    }):AddToTheme({ ImageColor3 = "Shadow" })

    items.MainFrame:MakeDraggable()
    items.MainFrame:MakeResizeable(vec2(400, 300), nil)

    -- topbar
    items.Topbar = inst:Create("Frame", {
        Parent = items.MainFrame.Instance,
        Name = "\0",
        Size = udim2(1,0,0,35),
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = lib.Theme.Background,
    })
    items.Topbar:AddToTheme({ BackgroundColor3 = "Background" })
    inst:Create("UICorner", { Parent = items.Topbar.Instance, Name = "\0", CornerRadius = udim(0, 5) })

    inst:Create("Frame", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        AnchorPoint = vec2(0,1),
        Position = udim2(0,0,1,0),
        Size = udim2(1,0,0,3),
        ZIndex = 3,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Background,
    }):AddToTheme({ BackgroundColor3 = "Background" })

    inst:Create("Frame", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        AnchorPoint = vec2(0,1),
        BackgroundTransparency = 0.7,
        Position = udim2(0,0,1,0),
        Size = udim2(1,0,0,1),
        ZIndex = 3,
        BorderSizePixel = 0,
        BackgroundColor3 = lib.Theme.Border,
    }):AddToTheme({ BackgroundColor3 = "Border" })

    -- logo
    items.Logo = inst:Create("ImageLabel", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        ImageColor3 = lib.Theme.Accent,
        Image = "rbxassetid://" .. win.Logo,
        AnchorPoint = vec2(0, 0.5),
        Position = udim2(0,8,0.5,0),
        Size = udim2(0,20,0,20),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Logo:AddToTheme({ ImageColor3 = "Accent" })

    items.Title = inst:Create("TextLabel", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = win.Name,
        TextSize = 14,
        TextColor3 = rgb(255,255,255),
        AnchorPoint = vec2(0, 0.5),
        Position = udim2(0,34,0.5,0),
        Size = udim2(0,0,0,15),
        AutomaticSize = Enum.AutomaticSize.X,
        BackgroundTransparency = 1,
        TextXAlignment = Enum.TextXAlignment.Left,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Title:AddToTheme({ TextColor3 = "Text" })

    -- version badge (pill estilo kiwisense, filho do Title)
    items.VersionBadge = inst:Create("Frame", {
        Parent = items.Title.Instance,
        Name = "\0",
        Size = udim2(0, 0, 0, 15),
        Position = udim2(1, 5, 0, 1),
        AutomaticSize = Enum.AutomaticSize.X,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = lib.Theme.Background,
    })
    items.VersionBadge:AddToTheme({ BackgroundColor3 = "Background" })
    inst:Create("UICorner", { Parent = items.VersionBadge.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIStroke", {
        Parent = items.VersionBadge.Instance, Name = "\0",
        Color = lib.Theme.Border, Transparency = 0.4,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    }):AddToTheme({ Color = "Border" })
    inst:Create("UIPadding", {
        Parent = items.VersionBadge.Instance, Name = "\0",
        PaddingLeft = udim(0, 6), PaddingRight = udim(0, 5),
    })

    items.Version = inst:Create("TextLabel", {
        Parent = items.VersionBadge.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = win.Version,
        TextSize = 12,
        TextTransparency = 0.7,
        TextColor3 = rgb(255,255,255),
        AutomaticSize = Enum.AutomaticSize.X,
        Size = udim2(0, 0, 0, 15),
        Position = udim2(0, -2, 0, 0),
        BackgroundTransparency = 1,
        TextXAlignment = Enum.TextXAlignment.Left,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Version:AddToTheme({ TextColor3 = "Text" })

    -- close / minimize
    items.CloseButton = inst:Create("ImageButton", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        Image = lib.Icons.Close,
        ImageColor3 = lib.Theme.Image,
        ScaleType = Enum.ScaleType.Fit,
        AnchorPoint = vec2(1, 0.5),
        Position = udim2(1,-7,0.5,0),
        Size = udim2(0,17,0,17),
        AutoButtonColor = false,
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.CloseButton:AddToTheme({ ImageColor3 = "Image" })

    items.MinimizeButton = inst:Create("ImageButton", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        Image = lib.Icons.Minimize,
        ImageColor3 = lib.Theme.Image,
        ScaleType = Enum.ScaleType.Fit,
        AnchorPoint = vec2(1, 0.5),
        Position = udim2(1,-28,0.5,-5),
        Size = udim2(0,17,0,17),
        AutoButtonColor = false,
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.MinimizeButton:AddToTheme({ ImageColor3 = "Image" })

    items.UnMinimizeButton = inst:Create("ImageButton", {
        Parent = items.Topbar.Instance,
        Name = "\0",
        Image = lib.Icons.UnMin,
        ImageColor3 = lib.Theme.Image,
        ScaleType = Enum.ScaleType.Fit,
        AnchorPoint = vec2(1, 0.5),
        Position = udim2(1,-28,0.5,0),
        Size = udim2(0,17,0,17),
        AutoButtonColor = false,
        BackgroundTransparency = 1,
        ImageTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.UnMinimizeButton:AddToTheme({ ImageColor3 = "Image" })

    -- search
    items.SearchFrame = inst:Create("Frame", {
        Parent = items.MainFrame.Instance,
        Name = "\0",
        Position = udim2(0,8,0,43),
        Size = udim2(0,250,0,32),
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = lib.Theme.Inline,
    })
    items.SearchFrame:AddToTheme({ BackgroundColor3 = "Inline" })
    inst:Create("UICorner", { Parent = items.SearchFrame.Instance, Name = "\0", CornerRadius = udim(0, 5) })
    inst:Create("UIStroke", {
        Parent = items.SearchFrame.Instance, Name = "\0",
        Color = lib.Theme.Border, Transparency = 0.4,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
    }):AddToTheme({ Color = "Border" })

    local searchIcon = inst:Create("ImageLabel", {
        Parent = items.SearchFrame.Instance,
        Name = "\0",
        ImageColor3 = lib.Theme["Inactive Text"],
        ImageTransparency = 0.5,
        Image = lib.Icons.Search,
        AnchorPoint = vec2(0, 0.5),
        Position = udim2(0,8,0.5,0),
        Size = udim2(0,20,0,20),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    searchIcon:AddToTheme({ ImageColor3 = "Image" })

    items.Input = inst:Create("TextBox", {
        Parent = items.SearchFrame.Instance,
        Name = "\0",
        FontFace = lib.Font,
        Text = "",
        PlaceholderText = "search",
        PlaceholderColor3 = lib.Theme["Inactive Text"],
        TextColor3 = lib.Theme.Text,
        TextSize = 14,
        AnchorPoint = vec2(0, 0.5),
        Position = udim2(0,30,0.5,0),
        Size = udim2(1,-34,0,15),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ClearTextOnFocus = false,
        ZIndex = 3,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.Input:AddToTheme({ TextColor3 = "Text", PlaceholderColor3 = "Inactive Text" })

    -- content
    items.Content = inst:Create("Frame", {
        Parent = items.MainFrame.Instance,
        Name = "\0",
        Position = udim2(0,8,0,83),
        Size = udim2(1,-16,1,-130),
        BackgroundTransparency = 1,
        ClipsDescendants = true,
        BorderSizePixel = 0,
        ZIndex = 2,
        BackgroundColor3 = rgb(255,255,255),
    })

    -- bottombar
    items.Bottombar = inst:Create("Frame", {
        Parent = items.MainFrame.Instance,
        Name = "\0",
        Position = udim2(0,0,1,-46),
        Size = udim2(1,0,0,46),
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = lib.Theme.Background,
    })
    items.Bottombar:AddToTheme({ BackgroundColor3 = "Background" })

    inst:Create("Frame", {
        Parent = items.MainFrame.Instance,
        Name = "\0",
        BackgroundTransparency = 0.4,
        Position = udim2(0,0,1,-46),
        Size = udim2(1,0,0,1),
        BorderSizePixel = 0,
        ZIndex = 3,
        BackgroundColor3 = lib.Theme.Border,
    }):AddToTheme({ BackgroundColor3 = "Border" })

    inst:Create("UICorner", { Parent = items.Bottombar.Instance, Name = "\0", CornerRadius = udim(0, 5) })

    local bbLayout = inst:Create("UIListLayout", {
        Parent = items.Bottombar.Instance,
        Name = "\0",
        FillDirection = Enum.FillDirection.Horizontal,
        HorizontalAlignment = Enum.HorizontalAlignment.Center,
        VerticalAlignment = Enum.VerticalAlignment.Center,
        Padding = udim(0, 20),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })

    -- mouse customizado
    uis.MouseIconEnabled = false
    items.MouseImage = inst:Create("ImageLabel", {
        Parent = lib.Holder.Instance,
        Name = "\0",
        Image = lib.Icons.Mouse,
        ImageColor3 = lib.Theme.Accent,
        ScaleType = Enum.ScaleType.Fit,
        Size = udim2(0,20,0,20),
        Position = udim2(0,0,0,0),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 99999,
        BackgroundColor3 = rgb(255,255,255),
    })
    items.MouseImage:AddToTheme({ ImageColor3 = "Accent" })

    lib:Connect(run.RenderStepped, function()
        local ml = uis:GetMouseLocation()
        items.MouseImage.Instance.Position = udim2(0, ml.X - 1, 0, ml.Y - 56)
    end)

    win.Items = items

    -- ── SetOpen ──
    local debounce = false
    local oldSizes = {}

    function win:AddToOldSizes(item, size)
        if not oldSizes[item] then oldSizes[item] = size end
    end

    function win:GetOldSize(item)
        return oldSizes[item]
    end

    function win:SetOpen(bool)
        if debounce then return end
        self.IsOpen = bool
        debounce = true

        if bool then items.MainFrame.Instance.Visible = true end

        local kids = items.MainFrame.Instance:GetDescendants()
        ins(kids, items.MainFrame.Instance)

        local last
        for _, v in kids do
            local props = tw:GetProperty(v)
            if not props then continue end
            for _, prop in props do
                last = tw:FadeItem(v, prop, bool, self.FadeSpeed)
            end
        end

        if last then
            lib:Connect(last.Tween.Completed, function()
                debounce = false
                items.MainFrame.Instance.Visible = bool
                if bool then
                    items.MouseImage.Instance.Visible = true
                    uis.MouseIconEnabled = false
                else
                    items.MouseImage.Instance.Visible = false
                    uis.MouseIconEnabled = true
                end
            end)
        else
            debounce = false
        end
    end

    -- ── minimize ──
    local isMin = false
    local minOldSize

    items.MinimizeButton:Connect("MouseButton1Down", function()
        isMin = not isMin
        if isMin then
            minOldSize = items.MainFrame.Instance.AbsoluteSize
            items.MainFrame:Tween(nil, { Size = udim2(0, items.MainFrame.Instance.Size.X.Offset, 0, 35) })
            items.MinimizeButton:Tween(nil, { ImageTransparency = 1 })
            items.UnMinimizeButton:Tween(nil, { ImageTransparency = 0 })
        else
            items.MainFrame:Tween(nil, { Size = udim2(0, items.MainFrame.Instance.Size.X.Offset, 0, minOldSize.Y) })
            items.MinimizeButton:Tween(nil, { ImageTransparency = 0 })
            items.UnMinimizeButton:Tween(nil, { ImageTransparency = 1 })
        end
    end)

    items.CloseButton:Connect("MouseButton1Down", function()
        win:SetOpen(false)
        task.wait(0.1)
        lib:Unload()
    end)

    -- menu keybind
    lib:Connect(uis.InputBegan, function(input, gpe)
        if gpe then return end
        if tostring(input.KeyCode) == lib.MenuKeybind or tostring(input.UserInputType) == lib.MenuKeybind then
            win:SetOpen(not win.IsOpen)
        end
    end)

    -- search logic
    local searchRS
    items.Input:Connect("Focused", function()
        local pageData = lib.SearchItems[currentPage]
        if not pageData then return end

        searchRS = run.RenderStepped:Connect(function()
            for _, v in pageData do
                local name2 = v.Name
                local el = v.Item
                if find(lower(name2), lower(items.Input.Instance.Text)) then
                    el.Instance.Visible = true
                    el:Tween(nil, { Size = win:GetOldSize(el) })
                else
                    win:AddToOldSizes(el, el.Instance.Size)
                    el:Tween(nil, { Size = udim2(win:GetOldSize(el).X.Scale, win:GetOldSize(el).X.Offset, 0, 0) })
                    task.wait(0.1)
                    el.Instance.Visible = false
                end
            end
        end)
    end)

    items.Input:Connect("FocusLost", function()
        if searchRS then searchRS:Disconnect(); searchRS = nil end
    end)

    -- ── page button helper ──
    function win:_addPageButton(page, frame)
        local iconId = page.Icon or "111178525804834"
        local btn = inst:Create("TextButton", {
            Parent = items.Bottombar.Instance,
            Name = "\0",
            Text = "",
            AutoButtonColor = false,
            Size = udim2(0,50,0,46),
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            ZIndex = 4,
            BackgroundColor3 = lib.Theme.Background,
        })

        local icon = inst:Create("ImageLabel", {
            Parent = btn.Instance,
            Name = "\0",
            Image = "rbxassetid://" .. iconId,
            ImageColor3 = lib.Theme["Inactive Text"],
            ImageTransparency = 0.5,
            ScaleType = Enum.ScaleType.Fit,
            AnchorPoint = vec2(0.5, 0.4),
            Position = udim2(0.5,0,0.4,0),
            Size = udim2(0,22,0,22),
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            ZIndex = 4,
            BackgroundColor3 = rgb(255,255,255),
        })
        icon:AddToTheme({ ImageColor3 = "Inactive Text" })

        local label = inst:Create("TextLabel", {
            Parent = btn.Instance,
            Name = "\0",
            FontFace = lib.Font,
            Text = page.Name,
            TextSize = 11,
            TextColor3 = lib.Theme["Inactive Text"],
            AnchorPoint = vec2(0.5, 1),
            Position = udim2(0.5,0,1,-2),
            Size = udim2(1,0,0,11),
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            ZIndex = 4,
            BackgroundColor3 = rgb(255,255,255),
        })
        label:AddToTheme({ TextColor3 = "Inactive Text" })

        -- indicador ativo (barra no topo do botão)
        local indicator = inst:Create("Frame", {
            Parent = btn.Instance,
            Name = "\0",
            AnchorPoint = vec2(0.5, 0),
            Position = udim2(0.5, 0, 0, 0),
            Size = udim2(0, 16, 0, 3),
            BackgroundTransparency = 1,
            BorderSizePixel = 0,
            ZIndex = 5,
            BackgroundColor3 = lib.Theme.Accent,
        })
        indicator:AddToTheme({ BackgroundColor3 = "Accent" })
        inst:Create("UICorner", { Parent = indicator.Instance, Name = "\0", CornerRadius = udim(1, 0) })

        ins(pageBtns, { Btn = btn, Icon = icon, Label = label, Indicator = indicator, Page = page, Frame = frame })

        local debounce = false

        local function activate(p)
            if currentPage == p then return end
            if debounce then return end
            debounce = true

            -- desativa anterior com fade out
            if currentPage then
                for _, b in pageBtns do
                    if b.Page == currentPage then
                        local kids = b.Frame.Instance:GetDescendants()
                        ins(kids, b.Frame.Instance)
                        local last
                        for _, v in kids do
                            local props = tw:GetProperty(v)
                            if not props then continue end
                            for _, prop in props do
                                last = tw:FadeItem(v, prop, false, win.FadeSpeed)
                            end
                        end
                        if last then
                            lib:Connect(last.Tween.Completed, function()
                                b.Frame.Instance.Visible = false
                                b.Frame.Instance.Parent = lib.UnusedHolder.Instance
                            end)
                        else
                            b.Frame.Instance.Visible = false
                            b.Frame.Instance.Parent = lib.UnusedHolder.Instance
                        end
                        b.Icon:ChangeItemTheme({ ImageColor3 = "Inactive Text" })
                        b.Icon:Tween(nil, { ImageColor3 = lib.Theme["Inactive Text"], ImageTransparency = 0.5 })
                        b.Label:Tween(nil, { TextColor3 = lib.Theme["Inactive Text"] })
                        b.Btn:Tween(nil, { BackgroundTransparency = 1 })
                        b.Indicator:Tween(nil, { BackgroundTransparency = 1 })
                        break
                    end
                end
            end

            currentPage = p
            lib.CurrentPage = p

            -- move para Content e faz fade in
            frame.Instance.Parent = items.Content.Instance
            frame.Instance.Visible = true

            local kids = frame.Instance:GetDescendants()
            ins(kids, frame.Instance)
            local last
            for _, v in kids do
                local props = tw:GetProperty(v)
                if not props then continue end
                for _, prop in props do
                    last = tw:FadeItem(v, prop, true, win.FadeSpeed)
                end
            end

            if last then
                lib:Connect(last.Tween.Completed, function()
                    debounce = false
                end)
            else
                debounce = false
            end

            icon:ChangeItemTheme({ ImageColor3 = "Accent" })
            icon:Tween(nil, { ImageColor3 = lib.Theme.Accent, ImageTransparency = 0 })
            label:Tween(nil, { TextColor3 = lib.Theme.Accent })
            btn:Tween(nil, { BackgroundTransparency = 0 })
            indicator:Tween(nil, { BackgroundTransparency = 0 })
        end

        btn:Connect("MouseButton1Down", function()
            activate(page)
        end)

        btn:OnHover(function()
            if currentPage ~= page then
                icon:Tween(nil, { ImageTransparency = 0 })
            end
        end)
        btn:OnHoverLeave(function()
            if currentPage ~= page then
                icon:Tween(nil, { ImageTransparency = 0.5 })
            end
        end)

        -- ativa primeira page automaticamente
        if #pageBtns == 1 then
            activate(page)
        end

        ins(win.Pages, page)
    end


    -- wrapper de page
    local winMeta = setmetatable(win, lib.Pages)
    winMeta.Window = win

    function winMeta:Page(d)
        d = d or {}
        d.Window = win
        return lib.Pages.Page(winMeta, d)
    end

    function winMeta:SetText(t) items.Title.Instance.Text = t end

    return winMeta
end

-- ─── SETTINGS PAGE ──────────────────────────────────────────────────────────
-- Cria a aba de settings padrão numa window existente.
-- Uso: lib:Settings(window)
-- Retorna o objeto da page para quem quiser estender.

lib.Settings = function(self, win)
    -- Page de settings — ícone de gear
    local settingsPage = win:Page({
        Name = "Settings",
        Icon = "111178525804834",
    })

    -- ── Col 1: Menu ─────────────────────────────────────────────────────────

    local menuSec = settingsPage:Section({
        Name = "menu",
        Icon = "111178525804834",
        Side = 1,
    })

    -- Menu keybind
    local kbTog = menuSec:Label({ Name = "menu keybind" })
    local menuKb = kbTog:Keybind({
        Name  = "menu keybind",
        Flag  = "MenuKeybind",
        Default = Enum.KeyCode.RightShift,
        Mode  = "Toggle",
        Callback = function(key)
            lib.MenuKeybind = tostring(key)
        end,
    })

    -- Toggles de overlay
    menuSec:Toggle({
        Name    = "keybind list",
        Flag    = "ShowKeybindList",
        Default = false,
        Callback = function(v)
            if lib._keybindsListFrame then
                lib._keybindsListFrame.Frame.Instance.Visible = v
            end
        end,
    })

    menuSec:Toggle({
        Name    = "watermark",
        Flag    = "ShowWatermark",
        Default = false,
        Callback = function(v)
            if lib._watermarkFrame then
                lib._watermarkFrame.Frame.Instance.Visible = v
            end
        end,
    })

    menuSec:Button({
        Name = "unload",
        Callback = function()
            win:SetOpen(false)
            task.wait(0.15)
            lib:Unload()
        end,
    })

    -- ── Col 1: Tweening ─────────────────────────────────────────────────────

    local tweenSec = settingsPage:Section({
        Name = "tweening",
        Icon = "111178525804834",
        Side = 1,
    })

    tweenSec:Slider({
        Name     = "time",
        Min      = 0,
        Max      = 1,
        Default  = lib.Tween.Time,
        Decimals = 2,
        Flag     = "TweenTime",
        Callback = function(v)
            lib.Tween.Time = v
        end,
    })

    tweenSec:Dropdown({
        Name    = "style",
        Items   = { "Linear", "Sine", "Bounce", "Elastic", "Exponential", "Circular", "Cubic", "Quart", "Quint", "Back" },
        Default = "Exponential",
        Flag    = "TweenStyle",
        Callback = function(v)
            lib.Tween.Style = Enum.EasingStyle[v]
        end,
    })

    tweenSec:Dropdown({
        Name    = "direction",
        Items   = { "In", "Out", "InOut" },
        Default = "Out",
        Flag    = "TweenDirection",
        Callback = function(v)
            lib.Tween.Direction = Enum.EasingDirection[v]
        end,
    })

    -- ── Col 2: Profiles (Configs) ────────────────────────────────────────────

    local cfgSec = settingsPage:Section({
        Name = "profiles",
        Icon = "116339777575852",
        Side = 2,
    })

    -- dropdown de configs existentes
    local cfgDropdown = cfgSec:Dropdown({
        Name  = "configs",
        Items = lib:RefreshConfigsList(),
        Flag  = "SelectedConfig",
        Callback = function() end,
    })

    cfgSec:Textbox({
        Name        = "config name",
        Placeholder = "enter text",
        Flag        = "ConfigName",
        Callback    = function() end,
    })

    cfgSec:Button({
        Name = "create",
        Callback = function()
            local name = lib.Flags["ConfigName"]
            if not name or name == "" then
                lib:Notification({ Name = "Erro", Description = "Nome vazio", Duration = 3, IconColor = rgb(255,100,100) })
                return
            end
            writefile(lib.Folders.Configs .. "/" .. name .. ".json", lib:GetConfig())
            lib:Notification({ Name = "Success", Description = "Config criado: " .. name, Duration = 4, IconColor = rgb(52,255,164) })
            lib:RefreshConfigsList(cfgDropdown)
        end,
    })

    cfgSec:Button({
        Name = "delete",
        Callback = function()
            local sel = lib.Flags["SelectedConfig"]
            if not sel then
                lib:Notification({ Name = "Erro", Description = "Nenhum config selecionado", Duration = 3, IconColor = rgb(255,100,100) })
                return
            end
            lib:DeleteConfig(sel:gsub("%.json$", ""))
            lib:RefreshConfigsList(cfgDropdown)
        end,
    })

    cfgSec:Button({
        Name = "load",
        Callback = function()
            local sel = lib.Flags["SelectedConfig"]
            if not sel then
                lib:Notification({ Name = "Erro", Description = "Nenhum config selecionado", Duration = 3, IconColor = rgb(255,100,100) })
                return
            end
            local path = lib.Folders.Configs .. "/" .. sel
            if not isfile(path) then
                lib:Notification({ Name = "Erro", Description = "Arquivo não encontrado", Duration = 3, IconColor = rgb(255,100,100) })
                return
            end
            local ok, err = lib:LoadConfig(readfile(path))
            if ok then
                lib:Notification({ Name = "Success", Description = "Config carregado", Duration = 4, IconColor = rgb(52,255,164) })
            else
                lib:Notification({ Name = "Erro", Description = tostring(err), Duration = 5, IconColor = rgb(255,100,100) })
            end
        end,
    })

    cfgSec:Button({
        Name = "save",
        Callback = function()
            local sel = lib.Flags["SelectedConfig"]
            if not sel then
                lib:Notification({ Name = "Erro", Description = "Nenhum config selecionado", Duration = 3, IconColor = rgb(255,100,100) })
                return
            end
            writefile(lib.Folders.Configs .. "/" .. sel, lib:GetConfig())
            lib:Notification({ Name = "Success", Description = "Config salvo: " .. sel, Duration = 4, IconColor = rgb(52,255,164) })
        end,
    })

    cfgSec:Button({
        Name = "refresh list",
        Callback = function()
            lib:RefreshConfigsList(cfgDropdown)
        end,
    })

    -- ── Col 2: Autoload ──────────────────────────────────────────────────────

    local autoSec = settingsPage:Section({
        Name = "autoload",
        Icon = "111178525804834",
        Side = 2,
    })

    autoSec:Button({
        Name = "set selected config as autoload",
        Callback = function()
            local sel = lib.Flags["SelectedConfig"]
            if not sel then
                lib:Notification({ Name = "Erro", Description = "Nenhum config selecionado", Duration = 3, IconColor = rgb(255,100,100) })
                return
            end
            local path = lib.Folders.Configs .. "/" .. sel
            if not isfile(path) then
                lib:Notification({ Name = "Erro", Description = "Config não encontrado", Duration = 3, IconColor = rgb(255,100,100) })
                return
            end
            writefile(lib.Folders.Directory .. "/AutoLoadConfig (do not modify this).json", sel)
            lib:Notification({ Name = "Success", Description = "Autoload definido: " .. sel, Duration = 4, IconColor = rgb(52,255,164) })
        end,
    })

    autoSec:Button({
        Name = "set current config as autoload",
        Callback = function()
            writefile(lib.Folders.Directory .. "/AutoLoadConfig (do not modify this).json", lib:GetConfig())
            lib:Notification({ Name = "Success", Description = "Autoload definido com config atual", Duration = 4, IconColor = rgb(52,255,164) })
        end,
    })

    autoSec:Button({
        Name = "remove autoload config",
        Callback = function()
            writefile(lib.Folders.Directory .. "/AutoLoadConfig (do not modify this).json", "")
            lib:Notification({ Name = "Success", Description = "Autoload removido", Duration = 4, IconColor = rgb(52,255,164) })
        end,
    })

    return settingsPage
end

-- ─── INIT ───────────────────────────────────────────────────────────────────

lib.Init = function(self)
    local cfgPath   = self.Folders.Directory .. "/AutoLoadConfig (do not modify this).json"
    local themePath = self.Folders.Directory .. "/AutoLoadTheme (do not modify this).json"

    if not isfile(cfgPath) then writefile(cfgPath, "") end
    if not isfile(themePath) then writefile(themePath, "") end

    local rawCfg = readfile(cfgPath)
    if rawCfg ~= "" then
        local ok, err = self:LoadConfig(rawCfg)
        if ok then
            self:Notification({ Name = "Success", Description = "Config carregado com sucesso", Duration = 5, Icon = "116339777575852", IconColor = rgb(52,255,164) })
        else
            self:Notification({ Name = "Erro!", Description = "Falha ao carregar config:\n" .. tostring(err), Duration = 5, Icon = "97118059177470", IconColor = rgb(255,120,120) })
        end
    end

    local rawTheme = readfile(themePath)
    if rawTheme ~= "" then
        local ok, err = self:LoadTheme(rawTheme)
        if ok then
            self:Notification({ Name = "Success", Description = "Tema carregado com sucesso", Duration = 5, Icon = "116339777575852", IconColor = rgb(52,255,164) })
        else
            self:Notification({ Name = "Erro!", Description = "Falha ao carregar tema:\n" .. tostring(err), Duration = 5, Icon = "97118059177470", IconColor = rgb(255,120,120) })
        end
    end
end

getgenv().Alemanha = lib
getgenv().Options  = getgenv().Options or {}
return lib
