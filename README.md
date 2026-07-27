-- ============================================================
-- MIKASA - All Features in 1 Tab (Black & Light Gray Edition)
-- WITH AVATAR & USERNAME DISPLAY AT THE TOP
-- REMOVED INTRO SPLASH SCREEN (fixed saved positions & auto-save)
-- MUTUAL EXCLUSION: Left / Right / Aimbot cannot be on together.
-- SPEED MODES: Carry, Lagger, LaggerCarry are mutually exclusive.
-- ADDED: SKY THEME SYSTEM (24 presets)
-- ADDED: INSTANT RESET BUTTON (with 2s cooldown)
-- ADDED: INTRO "MIKASA BETTER" - 3s then slide out
-- FLOATING BUTTON NOW RECTANGLE ROUNDED, RENAMED TO "Menu", MADE BIGGER
-- CHANGED ALL PURPLE TO BLUE (active color: 0,150,255)
-- FIXED AUTO-SAVE for Steal Duration (default 1.3)
-- ============================================================

local Players         = game:GetService("Players")
local RunService      = game:GetService("RunService")
local UIS             = game:GetService("UserInputService")
local TweenService    = game:GetService("TweenService")
local HttpService      = game:GetService("HttpService")
local ContentProvider = game:GetService("ContentProvider")
local Stats           = game:GetService("Stats")
local CoreGui         = game:GetService("CoreGui")
local Lighting        = game:GetService("Lighting")
local LP              = Players.LocalPlayer

-- ====== BACKGROUND IMAGE ID ======
local BG_IMAGE_ID = "rbxassetid://99908969530938"
task.spawn(function() pcall(function() ContentProvider:PreloadAsync({BG_IMAGE_ID}) end) end)

-- ====== LOGO ID ======
local LOGO_ID = "rbxassetid://90052457672604"
task.spawn(function() pcall(function() ContentProvider:PreloadAsync({LOGO_ID}) end) end)

local _isfile   = isfile   or (syn and syn.isfile)   or (getgenv and getgenv().isfile)   or function() return false end
local _readfile = readfile  or (syn and syn.readfile)  or (syn and syn.getfile) or (getgenv and getgenv().readfile)  or function() return nil  end
local _writefile= writefile or (syn and syn.writefile) or (getgenv and getgenv().writefile) or function() end
local getconnections = getconnections or get_signal_cons or getconnects or (syn and syn.get_signal_cons)

-- ============================================================
-- SKY THEME SYSTEM (stolen from Movee Duels)
-- ============================================================
local CANDY_SKY_TAG = "MIKASAHubSkyTheme"
local currentSkyTheme = "Night"  -- default

local CANDY_SKY_PRESETS = {
    ["Off"]={kind="off"},
    ["Night"]={clock=22,brightness=2,ambient={110,100,130},outAmb={120,110,140},sky={stars=4000,moon=18,sun=0,moonTex=true},atm={dens=0.45,color={120,60,180},decay={60,20,100},glare=0.5,haze=1.2}},
    ["Aurora"]={clock=14,brightness=3,ambient={150,120,150},outAmb={160,130,150},atm={dens=0.55,color={255,80,200},decay={255,20,150},glare=2.5,haze=3},clouds={cover=0.7,dens=0.7,color={255,240,250}}},
    ["Sunset"]={clock=17.2,brightness=2.5,ambient={170,120,100},outAmb={180,130,110},sky={stars=0,sun=25,moon=0},atm={dens=0.5,color={255,130,60},decay={255,80,30},glare=2,haze=2.5},clouds={cover=0.55,dens=0.55,color={255,200,140}}},
    ["Galaxy"]={clock=0,brightness=1.5,ambient={70,60,100},outAmb={80,70,110},sky={stars=10000,moon=30,sun=0},atm={dens=0.15,color={40,20,80},decay={20,10,50},glare=0.3,haze=0.5}},
    ["MIKASA"]={clock=21,brightness=2.2,ambient={90,130,170},outAmb={100,140,180},sky={stars=2000,moon=12},atm={dens=0.4,color={0,200,255},decay={150,0,255},glare=2,haze=2},clouds={cover=0.4,dens=0.6,color={100,200,255}}},
    ["Sakura"]={clock=11,brightness=3.5,ambient={170,150,160},outAmb={180,160,170},sky={sun=8},atm={dens=0.3,color={255,200,220},decay={255,170,200},glare=1,haze=1.5},clouds={cover=0.6,dens=0.4,color={255,250,252}}},
    ["Pink Night"]={clock=23,brightness=2.2,ambient={120,60,110},outAmb={140,70,120},sky={stars=5000,moon=22,sun=0,moonTex=true},atm={dens=0.5,color={255,80,180},decay={140,30,100},glare=0.7,haze=1.4},clouds={cover=0.3,dens=0.5,color={180,90,150}}},
    ["Blood Moon"]={clock=22.5,brightness=1.6,ambient={130,40,40},outAmb={150,50,50},sky={stars=1500,moon=28,sun=0,moonTex=true},atm={dens=0.6,color={220,30,30},decay={120,10,10},glare=1.4,haze=2},clouds={cover=0.5,dens=0.7,color={120,30,30}}},
    ["Emerald Dawn"]={clock=6.5,brightness=2.8,ambient={130,170,140},outAmb={140,180,150},sky={sun=18,moon=0,stars=0},atm={dens=0.4,color={80,200,140},decay={40,150,90},glare=1.8,haze=2.2},clouds={cover=0.5,dens=0.5,color={200,255,220}}},
    ["Volcanic"]={clock=19,brightness=2,ambient={180,80,40},outAmb={200,90,50},sky={stars=200,sun=12,moon=0},atm={dens=0.75,color={255,60,0},decay={180,20,0},glare=3,haze=3.5},clouds={cover=0.8,dens=0.9,color={120,40,20}}},
    ["Arctic"]={clock=9,brightness=3.2,ambient={200,220,235},outAmb={210,230,245},sky={sun=10,stars=0,moon=0},atm={dens=0.3,color={180,220,255},decay={140,200,240},glare=1.5,haze=1.8},clouds={cover=0.7,dens=0.6,color={250,253,255}}},
    ["Midnight Ocean"]={clock=1.5,brightness=1.7,ambient={60,90,130},outAmb={70,100,140},sky={stars=6000,moon=24,sun=0,moonTex=true},atm={dens=0.5,color={20,60,140},decay={10,30,90},glare=0.6,haze=1.5}},
    ["Vaporwave"]={clock=19.5,brightness=2.4,ambient={180,120,200},outAmb={190,130,210},sky={stars=1000,moon=14},atm={dens=0.45,color={255,100,220},decay={120,60,255},glare=2.2,haze=2.4},clouds={cover=0.5,dens=0.55,color={200,150,255}}},
    ["Toxic"]={clock=13,brightness=2.5,ambient={140,180,80},outAmb={150,190,90},atm={dens=0.55,color={100,220,40},decay={60,150,20},glare=1.8,haze=2.6},clouds={cover=0.65,dens=0.7,color={180,255,120}}},
    ["Solar Eclipse"]={clock=12,brightness=0.9,ambient={50,40,60},outAmb={60,50,70},sky={stars=3500,sun=22,moon=0},atm={dens=0.5,color={255,140,40},decay={30,20,40},glare=2.8,haze=1.8}},
    ["Hellscape"]={clock=18,brightness=1.8,ambient={200,60,30},outAmb={220,70,40},sky={stars=100,sun=30,moon=0},atm={dens=0.85,color={255,30,0},decay={120,0,0},glare=3.5,haze=4},clouds={cover=0.95,dens=0.95,color={80,20,10}}},
    ["Heaven"]={clock=12,brightness=4,ambient={240,235,210},outAmb={250,245,220},sky={sun=16,moon=0,stars=0},atm={dens=0.25,color={255,250,220},decay={255,240,200},glare=3,haze=1.5},clouds={cover=0.85,dens=0.5,color={255,255,255}}},
    ["Storm"]={clock=15,brightness=1.4,ambient={90,90,110},outAmb={100,100,120},sky={stars=0,sun=6,moon=0},atm={dens=0.65,color={80,90,120},decay={40,50,80},glare=0.5,haze=3},clouds={cover=0.95,dens=0.95,color={60,65,80}}},
    ["Sunrise"]={clock=6.2,brightness=2.8,ambient={220,180,130},outAmb={230,190,140},sky={sun=22,stars=0,moon=0},atm={dens=0.45,color={255,180,100},decay={255,140,80},glare=2.4,haze=2.2},clouds={cover=0.4,dens=0.4,color={255,220,180}}},
    ["Deep Space"]={clock=0,brightness=1,ambient={30,25,50},outAmb={40,35,60},sky={stars=15000,moon=0,sun=0},atm={dens=0.08,color={15,5,40},decay={5,0,20},glare=0.2,haze=0.3}},
    ["Lavender Dream"]={clock=18.5,brightness=2.6,ambient={180,160,220},outAmb={190,170,230},sky={stars=800,moon=16,sun=0},atm={dens=0.4,color={200,160,255},decay={160,120,220},glare=1.4,haze=1.8},clouds={cover=0.55,dens=0.5,color={220,200,255}}},
    ["Inferno"]={clock=17.5,brightness=2.2,ambient={220,100,40},outAmb={235,110,50},sky={sun=26,moon=0,stars=0},atm={dens=0.6,color={255,90,20},decay={200,40,0},glare=3,haze=3.2},clouds={cover=0.7,dens=0.7,color={200,80,40}}},
    ["Mint Sky"]={clock=10,brightness=3.2,ambient={180,230,210},outAmb={190,240,220},sky={sun=10},atm={dens=0.32,color={150,255,210},decay={100,220,180},glare=1.6,haze=1.6},clouds={cover=0.55,dens=0.45,color={240,255,250}}},
}
local SkyOrder = {"Off","Night","Aurora","Sunset","Galaxy","MIKASA","Sakura","Pink Night","Blood Moon","Emerald Dawn","Volcanic","Arctic","Midnight Ocean","Vaporwave","Toxic","Solar Eclipse","Hellscape","Heaven","Storm","Sunrise","Deep Space","Lavender Dream","Inferno","Mint Sky"}

local function candyColor(rgb) return Color3.fromRGB(rgb[1],rgb[2],rgb[3]) end

local function CandyApplyCustomSky(mode)
    for _,child in ipairs(Lighting:GetChildren()) do
        if child:GetAttribute(CANDY_SKY_TAG) then pcall(function() child:Destroy() end) end
    end
    local terrain = workspace:FindFirstChildOfClass("Terrain")
    if terrain then
        for _,child in ipairs(terrain:GetChildren()) do
            if child:GetAttribute(CANDY_SKY_TAG) then pcall(function() child:Destroy() end) end
        end
    end
    local preset = CANDY_SKY_PRESETS[mode]
    if not preset or preset.kind=="off" then
        Lighting.ClockTime = 14; Lighting.Brightness = 2; Lighting.OutdoorAmbient = Color3.fromRGB(127,127,127)
        Lighting.Ambient = Color3.fromRGB(127,127,127); Lighting.FogEnd = 100000; Lighting.GlobalShadows = true
        return
    end
    Lighting.FogStart = 0; Lighting.FogEnd = 100000; Lighting.FogColor = Color3.fromRGB(200,200,200)
    Lighting.ColorShift_Top = Color3.fromRGB(0,0,0); Lighting.ColorShift_Bottom = Color3.fromRGB(0,0,0); Lighting.GlobalShadows = true
    Lighting.ClockTime = preset.clock or 14; Lighting.Brightness = preset.brightness or 2
    if preset.outAmb then Lighting.OutdoorAmbient = candyColor(preset.outAmb) end
    if preset.ambient then Lighting.Ambient = candyColor(preset.ambient) end
    if preset.sky then
        local skyInst = Instance.new("Sky"); skyInst:SetAttribute(CANDY_SKY_TAG,true)
        if preset.sky.stars then skyInst.StarCount = preset.sky.stars end
        if preset.sky.moon then skyInst.MoonAngularSize = preset.sky.moon end
        if preset.sky.sun then skyInst.SunAngularSize = preset.sky.sun end
        if preset.sky.moonTex then skyInst.MoonTextureId = "rbxasset://sky/moon.jpg" end
        skyInst.Parent = Lighting
    end
    if preset.atm then
        local atm = Instance.new("Atmosphere"); atm:SetAttribute(CANDY_SKY_TAG,true)
        atm.Density = preset.atm.dens or 0.3; atm.Color = candyColor(preset.atm.color); atm.Decay = candyColor(preset.atm.decay)
        atm.Glare = preset.atm.glare or 1; atm.Haze = preset.atm.haze or 1; atm.Parent = Lighting
    end
    if preset.clouds and terrain then
        local clouds = Instance.new("Clouds"); clouds:SetAttribute(CANDY_SKY_TAG,true)
        clouds.Cover = preset.clouds.cover or 0.5; clouds.Density = preset.clouds.dens or 0.5
        clouds.Color = candyColor(preset.clouds.color); clouds.Parent = terrain
    end
end

-- ============================================================
-- STATE
-- ============================================================
local State = {
    normalSpeed=60, carrySpeed=30, laggerSpeed=11.1,
    speedToggled=false,          -- CARRY SPEED mode
    laggerEnabled=false,         -- LAGGER MODE
    laggerCarryEnabled=false,    -- LAGGER CARRY (mutually exclusive with laggerEnabled)
    laggerCarrySpeed=20,         -- speed used when LAGGER CARRY is ON

    infJumpEnabled=false, antiRagdollEnabled=false, fpsBoostEnabled=false,
    guiVisible=true, uiLocked=false,
    isStealing=false, stealStartTime=nil, lastStealTick=0,
    autoLeftEnabled=false, autoRightEnabled=false,
    autoLeftPhase=1, autoRightPhase=1,
    medusaLastUsed=0, medusaDebounce=false, medusaCounterEnabled=false,
    autoBatToggled=false,
    hittingCooldown=false,
    batCounterEnabled=false, batCounterDebounce=false,
    dropEnabled=false, _tpInProgress=false,
    lastMoveDir=Vector3.new(0,0,0),
    unwalkEnabled=false, stackButtonsHidden=false,
    _prevSpeed=false,
    antiBatEnabled=false,
    uiScale=1.0,
    infJumpMode = "manual",
    jumpPower = 55,
    skyTheme = "Night",          -- added for sky theme
}

local Keys = {
    speed=Enum.KeyCode.Q, guiHide=Enum.KeyCode.LeftControl,
    autoLeft=Enum.KeyCode.L, autoRight=Enum.KeyCode.R,
    lagger=Enum.KeyCode.Unknown, tpDown=Enum.KeyCode.Unknown,
    drop=Enum.KeyCode.H, aimbot=Enum.KeyCode.Unknown,
    antiBat=Enum.KeyCode.Unknown,
}

-- ============================================================
-- STACK BUTTONS (including LAGGER CARRY and INSTANT RESET)
-- ============================================================
-- ðŸ”¥ SQUARE ROUNDED BUTTONS: width = height = 64
local BTN_W = 64
local BTN_H = 64
local BTN_GAP = 7
local COLS = 2
local stackDefs = {
    {key="autoLeft",   label="AUTO\nLEFT"},
    {key="autoRight",  label="AUTO\nRIGHT"},
    {key="aimbot",     label="AIMBOT"},
    {key="lagger",     label="LAGGER\nMODE"},
    {key="laggerCarry",label="LAGGER\nCARRY"},
    {key="drop",       label="DROP\nBR"},
    {key="tpDown",     label="TP\nDOWN"},
    {key="carrySpeed", label="CARRY\nSPEED"},
    {key="instantReset", label="INSTANT\nRESET"},   -- NEW
}
local GRID_W = COLS * (BTN_W + BTN_GAP) - BTN_GAP
local GRID_H = math.ceil(#stackDefs / COLS) * (BTN_H + BTN_GAP) - BTN_GAP

local function getDefaultStackPos(i)
    local col = (i - 1) % COLS
    local row2 = math.floor((i - 1) / COLS)
    return UDim2.new(1, -(GRID_W + 14) + col * (BTN_W + BTN_GAP),
                     0.5, -(GRID_H / 2) + row2 * (BTN_H + BTN_GAP))
end

local Steal = {
    AutoStealEnabled=false, StealRadius=20, StealDuration=1.3,  -- default 1.3
    Data={}, plotCache={}, plotCacheTime={}, cachedPrompts={}, promptCacheTime=0,
}

-- ============================================================
-- CONFIG FILE
-- ============================================================
local CONFIG_FILE = "MIKASAHubConfig.json"
local BUTTON_POS_FILE = "MIKASAHubButtonPos.json"

local MOVE_KEYS={[Enum.KeyCode.W]=true,[Enum.KeyCode.A]=true,[Enum.KeyCode.S]=true,[Enum.KeyCode.D]=true,
    [Enum.KeyCode.Up]=true,[Enum.KeyCode.Down]=true,[Enum.KeyCode.Left]=true,[Enum.KeyCode.Right]=true}

local PLOT_CACHE_DURATION=2; local PROMPT_CACHE_REFRESH=0.15
local STEAL_COOLDOWN=0.1; local MEDUSA_COOLDOWN=25
local DROP_AUTO_OFF_DELAY=0.15
local DROP_ASCEND_DURATION = 0.2
local DROP_ASCEND_SPEED = 150

local POS={
    L1=Vector3.new(-476.48,-6.28,92.73), L2=Vector3.new(-483.12,-4.95,96.80),
    R1=Vector3.new(-476.16,-6.52,25.62), R2=Vector3.new(-483.04,-5.11,23.14),
}

local Conns={autoSteal=nil,antiRag=nil,autoLeft=nil,autoRight=nil,aimbot=nil,anchor={},progress=nil,batCounter=nil,unwalk=nil,antiBat=nil,holdJump=nil}

local h,hrp
local setAutoLeft,setAutoRight,setInfJump,setAntiRag,setFps
local setMedusaCounter,setUnwalkToggle,setAimbot,setAutoSwing
local setLagger,setDropBrainrot,setInstaGrab
local setupMedusaCounter,stopMedusaCounter,startAntiRagdoll,stopAntiRagdoll
local applyFPSBoost,startAutoSteal,stopAutoSteal
local startAutoLeft,stopAutoLeft,startAutoRight,stopAutoRight
local saveConfig,loadConfig,runDropBrainrot,stopDropBrainrot,doTpDown
local startBatCounter,stopBatCounter
local stackBtnRefs={}; local stackWrappers={}; local keybindBtnRefs={}
local normalBox,carryBox,laggerBox,stealRadBox,lockBtn
local stealDurationBox = nil  -- declared here
local setHideButtonsToggle
local radTB
local unwalkAnimateRef = nil

local startAntiBat, stopAntiBat, toggleAntiBat
local updateJumpModeUI
local jumpPowerBox, jumpManualBtn, jumpHoldBtn

local modeStatusLbl = nil
local modeBtns = {}
local laggerCarrySpeedBox = nil

-- Sky theme UI reference
local skyThemeLabel = nil
local skyIndex = 1

-- Instant reset cooldown
local lastResetTime = 0

-- ============================================================
-- CURSED INSTA RESET (from Movee Duels - faster reset method)
-- ============================================================
local cursedResetRemote = nil
local CURSED_RESET_GUID = "f888ee6e-c86d-46e1-93d7-0639d6635d42"

-- Hook FireServer to auto-detect the reset RemoteEvent
task.spawn(function()
    if hookfunction and newcclosure then
        local oldFire
        oldFire = hookfunction(Instance.new("RemoteEvent").FireServer, newcclosure(function(self, ...)
            if not cursedResetRemote and typeof(self) == "Instance" and self:IsA("RemoteEvent") and self.Name:sub(1,3) == "RE/" then
                cursedResetRemote = self
            end
            return oldFire(self, ...)
        end))
    end
end)

-- Fallback: scan descendants after 2s
task.spawn(function()
    task.wait(2)
    if cursedResetRemote then return end
    for _, desc in ipairs(game:GetDescendants()) do
        if desc:IsA("RemoteEvent") and desc.Name:sub(1,3) == "RE/" then
            cursedResetRemote = desc; break
        end
    end
end)

local function cursedInstaReset()
    -- Scan if not found yet
    if not cursedResetRemote then
        for _, desc in ipairs(game:GetDescendants()) do
            if desc:IsA("RemoteEvent") and desc.Name:sub(1,3) == "RE/" then
                cursedResetRemote = desc; break
            end
        end
    end
    local character = LP.Character
    local humanoid = character and character:FindFirstChildOfClass("Humanoid")
    -- If already dead, just fire once
    if humanoid and humanoid.Health <= 0 then
        if cursedResetRemote then
            pcall(function() cursedResetRemote:FireServer(CURSED_RESET_GUID, LP, "balloon") end)
        end
        return
    end
    -- Fire rapidly until reset is detected, then fallback to Health=0
    local resetDetected = false
    local conns = {}
    if humanoid then
        table.insert(conns, humanoid.Died:Connect(function() resetDetected = true end))
    end
    if character then
        table.insert(conns, character.AncestryChanged:Connect(function(_, parent)
            if not parent then resetDetected = true end
        end))
    end
    task.spawn(function()
        if cursedResetRemote then
            for _ = 1, 50 do
                if resetDetected then break end
                pcall(function() cursedResetRemote:FireServer(CURSED_RESET_GUID, LP, "balloon") end)
                task.wait()
            end
        else
            -- Fallback to Health=0 if remote not found
            if humanoid then pcall(function() humanoid.Health = 0 end) end
        end
        for _, conn in ipairs(conns) do pcall(function() conn:Disconnect() end) end
    end)
end

-- ============================================================
-- GRADIENT OUTLINE HELPERS (NOW BLUE)
-- ============================================================
local ColorSequence = ColorSequence
local ColorSequenceKeypoint = ColorSequenceKeypoint

local BLUE_KEYS = {
    ColorSequenceKeypoint.new(0,    Color3.fromRGB(255, 192, 203)),
    ColorSequenceKeypoint.new(0.25, Color3.fromRGB(255, 255, 255)),
    ColorSequenceKeypoint.new(0.45, Color3.fromRGB(255, 192, 203)),
    ColorSequenceKeypoint.new(0.65, Color3.fromRGB(255, 255, 255)),
    ColorSequenceKeypoint.new(0.9,  Color3.fromRGB(255, 192, 203)),
    ColorSequenceKeypoint.new(1,    Color3.fromRGB(255, 255, 255)),
}

local function addGradientStroke(parent, thickness)
    for _, child in ipairs(parent:GetChildren()) do
        if child:IsA("UIStroke") then child:Destroy() end
    end
    local stroke = Instance.new("UIStroke", parent)
    stroke.Thickness = thickness or 1.5
    stroke.Color = Color3.fromRGB(255, 255, 255)
    stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    stroke.Transparency = 0.2

    local grad = Instance.new("UIGradient", stroke)
    grad.Color = ColorSequence.new(BLUE_KEYS)
    grad.Rotation = 0

    task.spawn(function()
        local rot = 0
        while stroke and stroke.Parent do
            rot = (rot + 80 * task.wait()) % 360
            pcall(function() grad.Rotation = rot end)
        end
    end)
    return stroke
end

-- ============================================================
-- STYLE HELPERS
-- ============================================================
local function styleMenuItem(frame)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    for _, child in ipairs(frame:GetChildren()) do
        if child:IsA("UIStroke") then child:Destroy() end
    end
    local corner = Instance.new("UICorner", frame)
    corner.CornerRadius = UDim.new(0, 12)
    addGradientStroke(frame, 1.2)
end

local function styleStackButton(frame)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    for _, child in ipairs(frame:GetChildren()) do
        if child:IsA("UIStroke") then child:Destroy() end
    end
    local corner = Instance.new("UICorner", frame)
    corner.CornerRadius = UDim.new(0, 8)   -- rounded rectangle corners
    addGradientStroke(frame, 2.8)
end

-- ============================================================
-- CLEANUP
-- ============================================================
for _,name in pairs({"VyseSlottedGUI","VyseAsireGUI","VyseAsireHubV4","VyseAsireHubV5","VyseAsireHubV5_1","AsireHubV5_1","AsireHubV5_2","OpiumGGV5_2","MIKASAHub","MIKASAProfileGui","ChaosHub","ENVY_Aimbot","MIKASAHubHUD"}) do
    pcall(function() local o=game:GetService("CoreGui"):FindFirstChild(name); if o then o:Destroy() end end)
    pcall(function() local o=LP:WaitForChild("PlayerGui"):FindFirstChild(name); if o then o:Destroy() end end)
end

-- ============================================================
-- ROOT GUI
-- ============================================================
local gui=Instance.new("ScreenGui")
gui.Name="MIKASAHub"
gui.ResetOnSpawn=false; gui.DisplayOrder=10
gui.IgnoreGuiInset=true; gui.ZIndexBehavior=Enum.ZIndexBehavior.Sibling
gui.Parent=LP:WaitForChild("PlayerGui")

local uiScaleObj=Instance.new("UIScale",gui); uiScaleObj.Scale=State.uiScale or 1.0

-- ============================================================
-- HELPERS
-- ============================================================
local function mkCorner(p,r) local c=Instance.new("UICorner",p); c.CornerRadius=UDim.new(0,r or 6); return c end

local function makeDraggable(frame,handle)
    local src=handle or frame
    local dragging,dragInput,dragStart,startPos=false,nil,nil,nil
    src.InputBegan:Connect(function(inp)
        if State.uiLocked then return end
        if inp.UserInputType==Enum.UserInputType.MouseButton1 or inp.UserInputType==Enum.UserInputType.Touch then
            dragging=true; dragStart=inp.Position; startPos=frame.Position
            inp.Changed:Connect(function() if inp.UserInputState==Enum.UserInputState.End then dragging=false end end)
        end
    end)
    src.InputChanged:Connect(function(inp)
        if inp.UserInputType==Enum.UserInputType.MouseMovement or inp.UserInputType==Enum.UserInputType.Touch then dragInput=inp end
    end)
    UIS.InputChanged:Connect(function(inp)
        if inp==dragInput and dragging and not State.uiLocked then
            local dx=inp.Position.X-dragStart.X; local dy=inp.Position.Y-dragStart.Y
            frame.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+dx,startPos.Y.Scale,startPos.Y.Offset+dy)
        end
    end)
end

local function makeStackDraggable(frame,onTap,buttonKey)
    local dragging,dragInput,dragStart,startPos=false,nil,nil,nil; local moved=false
    frame.InputBegan:Connect(function(inp)
        if inp.UserInputType~=Enum.UserInputType.MouseButton1 and inp.UserInputType~=Enum.UserInputType.Touch then return end
        dragging=true; moved=false; dragStart=inp.Position; startPos=frame.Position
    end)
    frame.InputEnded:Connect(function(inp)
        if inp.UserInputType~=Enum.UserInputType.MouseButton1 and inp.UserInputType~=Enum.UserInputType.Touch then return end
        if dragging then
            if not moved and onTap then onTap() end
            if moved and buttonKey then
                State.savedButtonPositions = State.savedButtonPositions or {}
                State.savedButtonPositions[buttonKey] = {X=frame.Position.X.Offset, Y=frame.Position.Y.Offset}
                pcall(saveButtonPositions)
            end
        end
        dragging=false; moved=false
    end)
    frame.InputChanged:Connect(function(inp)
        if inp.UserInputType==Enum.UserInputType.MouseMovement or inp.UserInputType==Enum.UserInputType.Touch then
            dragInput=inp
        end
    end)
    UIS.InputChanged:Connect(function(inp)
        if inp~=dragInput or not dragging then return end
        local dx=inp.Position.X-dragStart.X; local dy=inp.Position.Y-dragStart.Y
        if math.abs(dx)>4 or math.abs(dy)>4 then moved=true end
        if moved and not State.uiLocked then
            frame.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+dx,startPos.Y.Scale,startPos.Y.Offset+dy)
        end
    end)
end

-- ============================================================
-- SAVE / LOAD BUTTON POSITIONS
-- ============================================================
local function saveButtonPositions()
    local data = State.savedButtonPositions or {}
    -- Save main menu position
    if mainOuter then
        data["__menu__"] = {
            XS = mainOuter.Position.X.Scale, XO = mainOuter.Position.X.Offset,
            YS = mainOuter.Position.Y.Scale, YO = mainOuter.Position.Y.Offset,
        }
    end
    -- Save steal bar position
    if pTrack then
        data["__stealbar__"] = {
            XS = pTrack.Position.X.Scale, XO = pTrack.Position.X.Offset,
            YS = pTrack.Position.Y.Scale, YO = pTrack.Position.Y.Offset,
        }
    end
    State.savedButtonPositions = data
    local ok, encoded = pcall(function() return HttpService:JSONEncode(data) end)
    if ok then pcall(function() _writefile(BUTTON_POS_FILE, encoded) end) end
end

local function loadButtonPositions()
    local hasFile = false; pcall(function() hasFile = _isfile(BUTTON_POS_FILE) end)
    if not hasFile then return end
    local raw; pcall(function() raw = _readfile(BUTTON_POS_FILE) end)
    if not raw then return end
    local ok, decoded = pcall(function() return HttpService:JSONDecode(raw) end)
    if ok and decoded then
        State.savedButtonPositions = decoded
        -- Restore stack buttons
        for i, def in ipairs(stackDefs) do
            local wrapper = stackWrappers[def.key]
            if wrapper and decoded[def.key] then
                local pos = decoded[def.key]
                wrapper.Position = UDim2.new(1, pos.X, 0.5, pos.Y)
            end
        end
        -- Restore main menu position
        if decoded["__menu__"] and mainOuter then
            local m = decoded["__menu__"]
            mainOuter.Position = UDim2.new(m.XS or 0.5, m.XO or -130, m.YS or 0.5, m.YO or -170)
        end
        -- Restore steal bar position
        if decoded["__stealbar__"] and pTrack then
            local s = decoded["__stealbar__"]
            pTrack.Position = UDim2.new(s.XS or 0.5, s.XO or -125, s.YS or 0, s.YO or 8)
        end
    end
end

-- ============================================================
-- MAIN WINDOW
-- ============================================================
local WIN_W = 260; local WIN_H = 340; local TITLE_H = 65

local mainOuter = Instance.new("Frame", gui)
mainOuter.Name = "MainOuter"
mainOuter.Size = UDim2.new(0, WIN_W, 0, WIN_H)
mainOuter.Position = UDim2.new(0.5, -WIN_W/2, 0.5, -WIN_H/2)
mainOuter.BackgroundTransparency = 1; mainOuter.BorderSizePixel = 0; mainOuter.ClipsDescendants = true
mainOuter.Visible = true
mkCorner(mainOuter, 14)
makeDraggable(mainOuter)

local bgImg = Instance.new("ImageLabel", mainOuter)
bgImg.Size = UDim2.new(1,0,1,0); bgImg.BackgroundColor3 = Color3.fromRGB(0,0,0)
bgImg.Image = BG_IMAGE_ID
bgImg.ScaleType = Enum.ScaleType.Crop
bgImg.ImageTransparency = 0.5
bgImg.ZIndex = 0; mkCorner(bgImg, 14)

local bgOverlay = Instance.new("Frame", mainOuter)
bgOverlay.Size = UDim2.new(1,0,1,0); bgOverlay.BackgroundColor3 = Color3.fromRGB(0,0,0)
bgOverlay.BackgroundTransparency = 0.5
bgOverlay.BorderSizePixel = 0; bgOverlay.ZIndex = 1; mkCorner(bgOverlay, 14)

-- Profile Section
local ProfileFrame = Instance.new("Frame", mainOuter)
ProfileFrame.Size = UDim2.new(1,0,0,TITLE_H); ProfileFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
ProfileFrame.BackgroundTransparency = 0.5
ProfileFrame.ZIndex = 5; mkCorner(ProfileFrame, 14)

local AvatarImage = Instance.new("ImageLabel", ProfileFrame)
AvatarImage.Size = UDim2.new(0,48,0,48); AvatarImage.Position = UDim2.new(0,10,0.5,-24)
AvatarImage.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
AvatarImage.Image = "rbxthumb://type=AvatarHeadShot&id="..LP.UserId.."&w=150&h=150"
AvatarImage.ZIndex = 6; mkCorner(AvatarImage, 999); addGradientStroke(AvatarImage, 2)

local chaosTitle = Instance.new("TextLabel", ProfileFrame)
chaosTitle.Size = UDim2.new(1,0,1,0); chaosTitle.BackgroundTransparency = 1
chaosTitle.Text = "MIKASA"
chaosTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
chaosTitle.Font = Enum.Font.GothamBlack; chaosTitle.TextSize = 20
chaosTitle.TextXAlignment = Enum.TextXAlignment.Center; chaosTitle.TextYAlignment = Enum.TextYAlignment.Center
chaosTitle.ZIndex = 6; chaosTitle.TextStrokeTransparency = 0.5
chaosTitle.TextStrokeColor3 = Color3.fromRGB(150, 150, 150)

local titleDiv = Instance.new("Frame", mainOuter)
titleDiv.Size = UDim2.new(1,0,0,1); titleDiv.Position = UDim2.new(0,0,0,TITLE_H)
titleDiv.BackgroundColor3 = Color3.fromRGB(80, 80, 80); titleDiv.BorderSizePixel = 0; titleDiv.ZIndex = 5

-- Content Area
local CONTENT_Y = TITLE_H + 1
local contentBg = Instance.new("Frame", mainOuter)
contentBg.Size = UDim2.new(1,0,1,-CONTENT_Y); contentBg.Position = UDim2.new(0,0,0,CONTENT_Y)
contentBg.BackgroundTransparency = 1; contentBg.BorderSizePixel = 0
contentBg.ClipsDescendants = true; contentBg.ZIndex = 2

local scrollFrame = Instance.new("ScrollingFrame", contentBg)
scrollFrame.Size = UDim2.new(1,0,1,0); scrollFrame.BackgroundTransparency = 1
scrollFrame.BorderSizePixel = 0; scrollFrame.ScrollBarThickness = 3
scrollFrame.ScrollBarImageColor3 = Color3.fromRGB(180, 180, 180); scrollFrame.ScrollBarImageTransparency = 0.4
scrollFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y; scrollFrame.CanvasSize = UDim2.new(0,0,0,0)

local pageLayout = Instance.new("UIListLayout", scrollFrame)
pageLayout.SortOrder = Enum.SortOrder.LayoutOrder; pageLayout.Padding = UDim.new(0,0)

local currentPage = scrollFrame; local lo = 0
local function LO() lo = lo + 1; return lo end

local function makeGap(px)
    local f = Instance.new("Frame", currentPage)
    f.Size = UDim2.new(1,0,0,px or 6); f.BackgroundTransparency = 1
    f.BorderSizePixel = 0; f.LayoutOrder = LO()
end

local function makeSectionHeader(label)
    local wrap = Instance.new("Frame", currentPage)
    wrap.Size = UDim2.new(1,0,0,28); wrap.BackgroundTransparency = 1
    wrap.BorderSizePixel = 0; wrap.LayoutOrder = LO()
    local lbl = Instance.new("TextLabel", wrap)
    lbl.Size = UDim2.new(1,-28,1,0); lbl.Position = UDim2.new(0,14,0,0)
    lbl.BackgroundTransparency = 1; lbl.Text = label and label:upper() or ""
    lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    lbl.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    lbl.TextSize = 10; lbl.TextXAlignment = Enum.TextXAlignment.Left
end

local function makeInputRow(label, default, onChange)
    local row = Instance.new("Frame", currentPage)
    row.Size = UDim2.new(1,0,0,44); row.BackgroundTransparency = 1
    row.BorderSizePixel = 0; row.LayoutOrder = LO()
    local div = Instance.new("Frame", row)
    div.Size = UDim2.new(1,-28,0,1); div.Position = UDim2.new(0,14,1,-1)
    div.BackgroundColor3 = Color3.fromRGB(80, 80, 80); div.BorderSizePixel = 0
    local lbl = Instance.new("TextLabel", row)
    lbl.Size = UDim2.new(1,-100,1,0); lbl.Position = UDim2.new(0,14,0,0)
    lbl.BackgroundTransparency = 1; lbl.Text = label; lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    lbl.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    lbl.TextSize = 13; lbl.TextXAlignment = Enum.TextXAlignment.Left
    local boxWrap = Instance.new("Frame", row)
    boxWrap.Size = UDim2.new(0,70,0,28); boxWrap.Position = UDim2.new(1,-84,0.5,-14)
    styleMenuItem(boxWrap)
    local box = Instance.new("TextBox", boxWrap)
    box.Size = UDim2.new(1,-8,1,0); box.Position = UDim2.new(0,4,0,0)
    box.BackgroundTransparency = 1; box.Text = tostring(default); box.TextColor3 = Color3.fromRGB(255, 255, 255)
    box.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    box.TextSize = 13; box.ClearTextOnFocus = false; box.ZIndex = 8
    box.TextXAlignment = Enum.TextXAlignment.Center
    box.FocusLost:Connect(function()
        if onChange then 
            local n = tonumber(box.Text)
            if n then 
                onChange(n) 
            else 
                box.Text = tostring(default) 
            end
        end
    end)
    return box
end

local function makeToggleRow(label, defaultOn, onToggle)
    local row = Instance.new("Frame", currentPage)
    row.Size = UDim2.new(1,0,0,44); row.BackgroundTransparency = 1
    row.BorderSizePixel = 0; row.LayoutOrder = LO()
    local div = Instance.new("Frame", row)
    div.Size = UDim2.new(1,-28,0,1); div.Position = UDim2.new(0,14,1,-1)
    div.BackgroundColor3 = Color3.fromRGB(80, 80, 80); div.BorderSizePixel = 0
    local lbl = Instance.new("TextLabel", row)
    lbl.Size = UDim2.new(1,-70,1,0); lbl.Position = UDim2.new(0,14,0,0)
    lbl.BackgroundTransparency = 1; lbl.Text = label; lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    lbl.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    lbl.TextSize = 13; lbl.TextXAlignment = Enum.TextXAlignment.Left
    local pillBg = Instance.new("Frame", row)
    pillBg.Size = UDim2.new(0,52,0,26); pillBg.Position = UDim2.new(1,-62,0.5,-13)
    styleMenuItem(pillBg)
    pillBg.BackgroundColor3 = defaultOn and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(20, 20, 20)
    pillBg.ZIndex = 7
    local offLbl = Instance.new("TextLabel", pillBg)
    offLbl.Size = UDim2.new(0.5,0,1,0); offLbl.BackgroundTransparency = 1; offLbl.Text = "OFF"
    offLbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    offLbl.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    offLbl.TextSize = 9; offLbl.TextXAlignment = Enum.TextXAlignment.Center; offLbl.ZIndex = 8
    local onLbl = Instance.new("TextLabel", pillBg)
    onLbl.Size = UDim2.new(0.5,0,1,0); onLbl.Position = UDim2.new(0.5,0,0,0)
    onLbl.BackgroundTransparency = 1; onLbl.Text = "ON"
    onLbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    onLbl.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    onLbl.TextSize = 9; onLbl.TextXAlignment = Enum.TextXAlignment.Center; onLbl.ZIndex = 8
    local dot = Instance.new("Frame", pillBg)
    dot.Size = UDim2.new(0.5,-3,1,-4)
    dot.Position = defaultOn and UDim2.new(0.5,1,0,2) or UDim2.new(0,2,0,2)
    dot.BackgroundColor3 = defaultOn and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(60, 60, 60)
    dot.BorderSizePixel = 0; dot.ZIndex = 7; mkCorner(dot, 5)
    addGradientStroke(dot, 1)
    local isOn = defaultOn or false
    local function setV(on)
        isOn = on
        TweenService:Create(pillBg,TweenInfo.new(0.2,Enum.EasingStyle.Quad),{BackgroundColor3=on and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(20, 20, 20)}):Play()
        TweenService:Create(dot,TweenInfo.new(0.2,Enum.EasingStyle.Back),{Position=on and UDim2.new(0.5,1,0,2) or UDim2.new(0,2,0,2),BackgroundColor3=on and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(60, 60, 60)}):Play()
    end
    local function toggle() isOn=not isOn; setV(isOn); if onToggle then pcall(onToggle,isOn) end
        task.spawn(function() if saveConfig then pcall(saveConfig) end end)
    end
    local clk = Instance.new("TextButton", row)
    clk.Size = UDim2.new(1,-58,1,0); clk.BackgroundTransparency = 1; clk.Text = ""
    clk.ZIndex = 5; clk.BorderSizePixel = 0; clk.MouseButton1Click:Connect(toggle)
    local pClk = Instance.new("TextButton", pillBg)
    pClk.Size = UDim2.new(1,0,1,0); pClk.BackgroundTransparency = 1; pClk.Text = ""
    pClk.ZIndex = 9; pClk.BorderSizePixel = 0; pClk.MouseButton1Click:Connect(toggle)
    return setV
end

-- ============================================================
-- KEYBIND ROW
-- ============================================================
local function getKeyDisplayName(kc)
    local n = kc.Name
    local gpNames = {ButtonA="A",ButtonB="B",ButtonX="X",ButtonY="Y",ButtonL1="LB",ButtonL2="LT",ButtonL3="LS",ButtonR1="RB",ButtonR2="RT",ButtonR3="RS",ButtonSelect="SEL",ButtonStart="STA",DPadUp="DÃƒÆ’Ã‚Â¢ÃƒÂ¢Ã¢â€šÂ¬ ÃƒÂ¢Ã¢â€šÂ¬Ã‹Å“",DPadDown="DÃƒÆ’Ã‚Â¢ÃƒÂ¢Ã¢â€šÂ¬ ÃƒÂ¢Ã¢â€šÂ¬Ã…â€œ",DPadLeft="DÃƒÆ’Ã‚Â¢ÃƒÂ¢Ã¢â€šÂ¬ Ãƒâ€šÃ‚Â",DPadRight="DÃƒÆ’Ã‚Â¢ÃƒÂ¢Ã¢â€šÂ¬ ÃƒÂ¢Ã¢â€šÂ¬Ã¢â€žÂ¢",Thumbstick1="LS",Thumbstick2="RS"}
    if gpNames[n] then return gpNames[n] end
    return n:sub(1,5)
end

local function makeKeybindRow(label, currentKey, onChanged, keyName)
    local row = Instance.new("Frame", currentPage)
    row.Size = UDim2.new(1,0,0,44); row.BackgroundTransparency = 1
    row.BorderSizePixel = 0; row.LayoutOrder = LO()
    local div = Instance.new("Frame", row)
    div.Size = UDim2.new(1,-28,0,1); div.Position = UDim2.new(0,14,1,-1)
    div.BackgroundColor3 = Color3.fromRGB(80, 80, 80); div.BorderSizePixel = 0
    local lbl = Instance.new("TextLabel", row)
    lbl.Size = UDim2.new(1,-80,1,0); lbl.Position = UDim2.new(0,14,0,0)
    lbl.BackgroundTransparency = 1; lbl.Text = label; lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    lbl.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    lbl.TextSize = 13; lbl.TextXAlignment = Enum.TextXAlignment.Left
    local kbtnWrap = Instance.new("Frame", row)
    kbtnWrap.Size = UDim2.new(0,70,0,28); kbtnWrap.Position = UDim2.new(1,-84,0.5,-14)
    styleMenuItem(kbtnWrap)
    local kbtn = Instance.new("TextButton", kbtnWrap)
    kbtn.Size = UDim2.new(1,0,1,0); kbtn.BackgroundTransparency = 1; kbtn.BorderSizePixel = 0
    kbtn.Text = getKeyDisplayName(currentKey); kbtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    kbtn.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    kbtn.TextSize = 11; kbtn.ZIndex = 8; mkCorner(kbtn, 5)
    local listening = false; local lconnKeyboard = nil; local lconnGamepad = nil
    local function stopL(key)
        listening = false
        if lconnKeyboard then lconnKeyboard:Disconnect(); lconnKeyboard = nil end
        if lconnGamepad then lconnGamepad:Disconnect(); lconnGamepad = nil end
        kbtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        if key then
            kbtn.Text = getKeyDisplayName(key)
            if onChanged then onChanged(key) end
            task.spawn(function() if saveConfig then pcall(saveConfig) end end)
        end
    end
    kbtn.MouseButton1Click:Connect(function()
        if listening then stopL(nil); return end
        listening = true; kbtn.Text = "ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â·ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â·ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â·"; kbtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        lconnKeyboard = UIS.InputBegan:Connect(function(inp)
            if not listening then return end
            if inp.UserInputType ~= Enum.UserInputType.Keyboard then return end
            if inp.KeyCode == Enum.KeyCode.Escape then stopL(nil); return end
            stopL(inp.KeyCode)
        end)
        lconnGamepad = UIS.InputBegan:Connect(function(inp)
            if not listening then return end
            if inp.UserInputType ~= Enum.UserInputType.Gamepad1 and inp.UserInputType ~= Enum.UserInputType.Gamepad2 and inp.UserInputType ~= Enum.UserInputType.Gamepad3 and inp.UserInputType ~= Enum.UserInputType.Gamepad4 then return end
            local kc = inp.KeyCode; if kc == Enum.KeyCode.Unknown then return end
            stopL(kc)
        end)
    end)
    if keyName then keybindBtnRefs[keyName] = kbtn end
    return kbtn
end

-- ============================================================
-- UNWALK FUNCTIONS
-- ============================================================
local function doStartUnwalk()
    local c = LP.Character; if not c then return end
    local hum2 = c:FindFirstChildOfClass("Humanoid")
    if hum2 then
        pcall(function()
            for _, track in ipairs(hum2:GetPlayingAnimationTracks()) do
                track:Stop(0)
            end
        end)
    end
    local animCtrl = c:FindFirstChildOfClass("AnimationController")
    if animCtrl then
        pcall(function()
            for _, track in ipairs(animCtrl:GetPlayingAnimationTracks()) do
                track:Stop(0)
            end
        end)
    end
    local anim = c:FindFirstChild("Animate")
    if anim and anim:IsA("LocalScript") then
        anim.Disabled = true
        unwalkAnimateRef = anim
    end
    if Conns.unwalk then Conns.unwalk:Disconnect(); Conns.unwalk = nil end
    Conns.unwalk = RunService.Heartbeat:Connect(function()
        if not State.unwalkEnabled then return end
        local c2 = LP.Character; if not c2 then return end
        local hum3 = c2:FindFirstChildOfClass("Humanoid")
        if hum3 then
            pcall(function()
                for _, track in ipairs(hum3:GetPlayingAnimationTracks()) do
                    track:Stop(0)
                end
            end)
        end
        local animCtrl2 = c2:FindFirstChildOfClass("AnimationController")
        if animCtrl2 then
            pcall(function()
                for _, track in ipairs(animCtrl2:GetPlayingAnimationTracks()) do
                    track:Stop(0)
                end
            end)
        end
    end)
end

local function doStopUnwalk()
    if Conns.unwalk then Conns.unwalk:Disconnect(); Conns.unwalk = nil end
    local c = LP.Character
    if c and unwalkAnimateRef and unwalkAnimateRef.Parent == c then
        unwalkAnimateRef.Disabled = false
    end
    unwalkAnimateRef = nil
end

-- ============================================================
-- ANTI BAT
-- ============================================================
startAntiBat = function()
    local char = LP.Character
    if not char then return end
    local rootPart = char:FindFirstChild("HumanoidRootPart")
    if not rootPart then return end
    if Conns.antiBat then Conns.antiBat:Disconnect() end
    Conns.antiBat = RunService.Heartbeat:Connect(function()
        if not State.antiBatEnabled then return end
        local c = LP.Character
        if not c then return end
        local root = c:FindFirstChild("HumanoidRootPart")
        if not root or not root.Parent then return end
        local originalVelocity = root.Velocity
        root.Velocity = Vector3.new(1000, root.Velocity.Y, 1000)
        RunService.RenderStepped:Wait()
        root.Velocity = originalVelocity
    end)
end

stopAntiBat = function()
    if Conns.antiBat then
        Conns.antiBat:Disconnect()
        Conns.antiBat = nil
    end
end

toggleAntiBat = function()
    State.antiBatEnabled = not State.antiBatEnabled
    if State.antiBatEnabled then startAntiBat() else stopAntiBat() end
    pcall(saveConfig)
end

-- ============================================================
-- CORE FUNCTIONS
-- ============================================================
local function resetProgressBar()
    if progressFill then progressFill.Size = UDim2.new(0, 0, 1, 0) end
    if stealPctLbl then stealPctLbl.Text = "" end
end

doTpDown = function()
    pcall(function()
        local c=LP.Character; if not c then return end
        local root=c:FindFirstChild("HumanoidRootPart"); if not root then return end
        local rp=RaycastParams.new(); rp.FilterDescendantsInstances={c}; rp.FilterType=Enum.RaycastFilterType.Exclude
        local res=workspace:Raycast(root.Position,Vector3.new(0,-1000,0),rp)
        if res then root.CFrame=CFrame.new(res.Position+Vector3.new(0,root.Size.Y/2+0.5,0)); root.AssemblyLinearVelocity=Vector3.zero end
    end)
end

-- ============================================================
-- DROP BRAINROT
-- ============================================================
local dropBrainrotActive = false

local function dropBrainrotNow()
    if dropBrainrotActive then return end
    local char = LP.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    dropBrainrotActive = true
    local t0 = tick()
    local dc
    dc = RunService.Heartbeat:Connect(function()
        local r = char and char:FindFirstChild("HumanoidRootPart")
        if not r then
            dc:Disconnect()
            dropBrainrotActive = false
            return
        end
        if tick() - t0 >= DROP_ASCEND_DURATION then
            dc:Disconnect()
            local rp = RaycastParams.new()
            rp.FilterDescendantsInstances = { char }
            rp.FilterType = Enum.RaycastFilterType.Exclude
            local rr = workspace:Raycast(r.Position, Vector3.new(0, -2000, 0), rp)
            if rr then
                local hum = char:FindFirstChildOfClass("Humanoid")
                local off = (hum and hum.HipHeight or 2) + (r.Size.Y / 2)
                r.CFrame = CFrame.new(r.Position.X, rr.Position.Y + off, r.Position.Z)
                r.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            end
            dropBrainrotActive = false
            return
        end
        r.AssemblyLinearVelocity = Vector3.new(r.AssemblyLinearVelocity.X, DROP_ASCEND_SPEED, r.AssemblyLinearVelocity.Z)
    end)
end

local dropBRConn = nil
local function startDropBR()
    if dropBRConn then return end
    dropBRConn = RunService.Heartbeat:Connect(function()
        if State.dropEnabled then dropBrainrotNow() end
    end)
end

local function stopDropBR()
    if dropBRConn then dropBRConn:Disconnect(); dropBRConn = nil end
    dropBrainrotActive = false
end

runDropBrainrot = function()
    if State.dropEnabled then return end
    State.dropEnabled = true
    if stackBtnRefs.drop then stackBtnRefs.drop.setOn(true) end
    startDropBR()
    task.delay(DROP_AUTO_OFF_DELAY, function()
        State.dropEnabled = false
        stopDropBR()
        if stackBtnRefs.drop then stackBtnRefs.drop.setOn(false) end
    end)
end

stopDropBrainrot = function()
    State.dropEnabled = false
    stopDropBR()
    if stackBtnRefs.drop then stackBtnRefs.drop.setOn(false) end
end

loadstring(game:HttpGet("https://raw.githubusercontent.com/Argian-dotcom/Jdkffkfo/refs/heads/main/Coding"))()

-- ============================================================
-- AUTO BAT AIMBOT
-- ============================================================
local autoBatConnection = nil
local autoBatEquipped = false
local _autoBatTarget = nil
local _autoBatLastScan = 0
local batTool = nil
local unwalkSavedAnimate = nil

local AUTO_BAT_SPEED = 58
local AUTO_BAT_VERT_SPEED = 52
local AUTO_BAT_DIST = -2.8
local AUTO_BAT_HEIGHT = 4.75
local AUTO_BAT_V_OFF = 1
local AUTO_BAT_TURN_SPEED = 285
local AUTO_BAT_MAX_TURN_RATE = 40
local AUTO_SWING_ENABLED = true

local function getCharacter() return LP.Character end
local function getHumanoid()
    local char = getCharacter()
    return char and char:FindFirstChildOfClass("Humanoid")
end
local function getRootPart()
    local char = getCharacter()
    return char and char:FindFirstChild("HumanoidRootPart")
end

local function getAutoBatTarget()
    local root = getRootPart()
    if not root then return nil end
    local now = tick()
    if now - _autoBatLastScan <= 0.1 and _autoBatTarget and _autoBatTarget.Parent then
        local hum = _autoBatTarget.Parent:FindFirstChildOfClass("Humanoid")
        if hum and hum.Health > 0 then
            return _autoBatTarget
        end
    end
    _autoBatLastScan = now
    _autoBatTarget = nil
    local closest, minDist = nil, math.huge
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LP and plr.Character then
            local tRoot = plr.Character:FindFirstChild("HumanoidRootPart")
            local hum = plr.Character:FindFirstChildOfClass("Humanoid")
            if tRoot and hum and hum.Health > 0 then
                local dist = (tRoot.Position - root.Position).Magnitude
                if dist < minDist then
                    minDist = dist
                    closest = tRoot
                end
            end
        end
    end
    _autoBatTarget = closest
    return _autoBatTarget
end

local function findBat()
    local char = getCharacter()
    if not char then return nil end
    for _, tool in ipairs(char:GetChildren()) do
        if tool:IsA("Tool") then
            local name = tool.Name:lower()
            if name:find("bat") or name:find("slap") then
                return tool
            end
        end
    end
    local bp = LP:FindFirstChildOfClass("Backpack") or LP:FindFirstChild("Backpack")
    if bp then
        for _, tool in ipairs(bp:GetChildren()) do
            if tool:IsA("Tool") then
                local name = tool.Name:lower()
                if name:find("bat") or name:find("slap") then
                    return tool
                end
            end
        end
    end
    return nil
end

local function ensureBatEquipped()
    local char = getCharacter()
    local hum = getHumanoid()
    if not char or not hum then return end
    if not char:FindFirstChildOfClass("Tool") then
        local bat = findBat()
        if bat then
            pcall(function() hum:EquipTool(bat) end)
            batTool = bat
        end
    else
        batTool = char:FindFirstChildOfClass("Tool")
    end
end

local function resetAutoBatMotion()
    local root = getRootPart()
    local hum = getHumanoid()
    if root then
        root.AssemblyLinearVelocity = root.AssemblyLinearVelocity * 0.3
        root.AssemblyAngularVelocity = Vector3.zero
    end
    if hum then hum.AutoRotate = true end
end

local function startUnwalkAimbot()
    local char = getCharacter()
    if not char then return end
    local hum = getHumanoid()
    if hum then
        for _, track in pairs(hum:GetPlayingAnimationTracks()) do
            track:Stop()
        end
    end
    local animate = char:FindFirstChild("Animate")
    if animate then
        unwalkSavedAnimate = animate:Clone()
        animate:Destroy()
    end
end

local function stopUnwalkAimbot()
    local char = getCharacter()
    if char and unwalkSavedAnimate then
        unwalkSavedAnimate.Parent = char
        unwalkSavedAnimate = nil
    end
end

local function startAutoBat()
    if autoBatConnection then return end
    startUnwalkAimbot()
    autoBatConnection = RunService.Heartbeat:Connect(function()
        if not State.autoBatToggled then return end
        local char = getCharacter()
        local hum = getHumanoid()
        local root = getRootPart()
        if not char or not hum or not root then return end
        if not autoBatEquipped then
            autoBatEquipped = true
            ensureBatEquipped()
        end
        local target = getAutoBatTarget()
        if target then
            local targetVel = target.AssemblyLinearVelocity
            local aimTargetPos = target.Position + (targetVel * math.clamp(targetVel.Magnitude / 130, 0.05, 0.15)) + Vector3.new(0, AUTO_BAT_V_OFF, 0)
            hum.AutoRotate = false
            local look = aimTargetPos - root.Position
            local flatLook = Vector3.new(look.X, 0, look.Z)
            if look.Magnitude > 0.01 and flatLook.Magnitude > 0.01 then
                local targetYaw = math.deg(math.atan2(-flatLook.X, -flatLook.Z))
                local yawDelta = (targetYaw - root.Orientation.Y + 180) % 360 - 180
                local targetPitch = math.deg(math.atan2(look.Y, flatLook.Magnitude))
                local pitchDelta = (targetPitch - root.Orientation.X + 180) % 360 - 180
                local yawRate = math.clamp(math.rad(yawDelta) * AUTO_BAT_TURN_SPEED, -AUTO_BAT_MAX_TURN_RATE, AUTO_BAT_MAX_TURN_RATE)
                local pitchRate = math.clamp(math.rad(pitchDelta) * AUTO_BAT_TURN_SPEED, -AUTO_BAT_MAX_TURN_RATE, AUTO_BAT_MAX_TURN_RATE)
                local yawRad = math.rad(root.Orientation.Y)
                local rightAxis = Vector3.new(math.cos(yawRad), 0, -math.sin(yawRad))
                root.AssemblyAngularVelocity = Vector3.new(0, yawRate, 0) + (rightAxis * pitchRate)
            else
                root.AssemblyAngularVelocity = Vector3.zero
            end
            local dir = look.Magnitude > 0.01 and look.Unit or Vector3.zero
            local standPos = aimTargetPos - (dir * AUTO_BAT_DIST) + Vector3.new(0, AUTO_BAT_HEIGHT, 0)
            local moveDir = standPos - root.Position
            local hDir = Vector3.new(moveDir.X, 0, moveDir.Z)
            local hVel = hDir.Magnitude > 0.1 and hDir.Unit * AUTO_BAT_SPEED or Vector3.zero
            local vVel = math.abs(moveDir.Y) > 0.1 and Vector3.new(0, math.sign(moveDir.Y) * AUTO_BAT_VERT_SPEED, 0) or Vector3.new(0, -2, 0)
            root.AssemblyLinearVelocity = hVel + vVel
            if hDir.Magnitude > 0.5 then
                hum:Move(hDir.Unit, false)
            end
            if AUTO_SWING_ENABLED and (root.Position - target.Position).Magnitude < 6 then
                local bat = findBat() or batTool
                if bat and bat:IsA("Tool") then
                    pcall(function() bat:Activate() end)
                end
            end
        else
            hum.AutoRotate = true
            root.AssemblyAngularVelocity = Vector3.zero
            root.AssemblyLinearVelocity = Vector3.zero
        end
    end)
end

local function stopAutoBat()
    if autoBatConnection then
        autoBatConnection:Disconnect()
        autoBatConnection = nil
    end
    resetAutoBatMotion()
    autoBatEquipped = false
    stopUnwalkAimbot()
end

-- ============================================================
-- MEDUSA COUNTER
-- ============================================================
local function findMedusa()
    local c=LP.Character; if not c then return nil end
    for _,t in ipairs(c:GetChildren()) do if t:IsA("Tool") then local n=t.Name:lower(); if n:find("medusa") or n:find("head") or n:find("stone") then return t end end end
    local bp=LP:FindFirstChild("Backpack")
    if bp then for _,t in ipairs(bp:GetChildren()) do if t:IsA("Tool") then local n=t.Name:lower(); if n:find("medusa") or n:find("head") or n:find("stone") then return t end end end end
    return nil
end
local function useMedusaCounter()
    if State.medusaDebounce then return end; if tick()-State.medusaLastUsed<MEDUSA_COOLDOWN then return end
    local c=LP.Character; if not c then return end; State.medusaDebounce=true
    local med=findMedusa(); if not med then State.medusaDebounce=false; return end
    if med.Parent~=c then local hum2=c:FindFirstChildOfClass("Humanoid"); if hum2 then hum2:EquipTool(med) end end
    pcall(function() med:Activate() end); State.medusaLastUsed=tick(); State.medusaDebounce=false
end
local function onAnchorChanged(part) return part:GetPropertyChangedSignal("Anchored"):Connect(function() if part.Anchored and part.Transparency==1 then useMedusaCounter() end end) end
setupMedusaCounter=function(char)
    stopMedusaCounter(); if not char then return end
    for _,part in ipairs(char:GetDescendants()) do if part:IsA("BasePart") then table.insert(Conns.anchor,onAnchorChanged(part)) end end
    table.insert(Conns.anchor,char.DescendantAdded:Connect(function(part) if part:IsA("BasePart") then table.insert(Conns.anchor,onAnchorChanged(part)) end end))
end
stopMedusaCounter=function() for _,c2 in pairs(Conns.anchor) do pcall(function() c2:Disconnect() end) end; Conns.anchor={} end

-- AUTO LEFT / RIGHT
local function faceSouth() pcall(function() local c=LP.Character; if not c then return end; local root=c:FindFirstChild("HumanoidRootPart"); if root then root.CFrame=CFrame.new(root.Position)*CFrame.Angles(0,0,0) end end) end
local function faceNorth() pcall(function() local c=LP.Character; if not c then return end; local root=c:FindFirstChild("HumanoidRootPart"); if root then root.CFrame=CFrame.new(root.Position)*CFrame.Angles(0,math.rad(180),0) end end) end

startAutoLeft=function()
    if Conns.autoLeft then Conns.autoLeft:Disconnect() end; State.autoLeftPhase=1
    Conns.autoLeft=RunService.Heartbeat:Connect(function()
        if not State.autoLeftEnabled then return end; local c=LP.Character; if not c then return end
        local root=c:FindFirstChild("HumanoidRootPart"); local hum2=c:FindFirstChildOfClass("Humanoid"); if not root or not hum2 then return end
        local spd=State.normalSpeed
        if State.autoLeftPhase==1 then
            local tgt=Vector3.new(POS.L1.X,root.Position.Y,POS.L1.Z); if (tgt-root.Position).Magnitude<1 then State.autoLeftPhase=2; local d=(POS.L2-root.Position); local mv=Vector3.new(d.X,0,d.Z).Unit; hum2:Move(mv,false); root.AssemblyLinearVelocity=Vector3.new(mv.X*spd,root.AssemblyLinearVelocity.Y,mv.Z*spd); return end
            local d=(POS.L1-root.Position); local mv=Vector3.new(d.X,0,d.Z).Unit; hum2:Move(mv,false); root.AssemblyLinearVelocity=Vector3.new(mv.X*spd,root.AssemblyLinearVelocity.Y,mv.Z*spd)
        elseif State.autoLeftPhase==2 then
            local tgt=Vector3.new(POS.L2.X,root.Position.Y,POS.L2.Z); if (tgt-root.Position).Magnitude<1 then hum2:Move(Vector3.zero,false); root.AssemblyLinearVelocity=Vector3.zero; State.autoLeftEnabled=false; if Conns.autoLeft then Conns.autoLeft:Disconnect(); Conns.autoLeft=nil end; State.autoLeftPhase=1; if stackBtnRefs.autoLeft then stackBtnRefs.autoLeft.setOn(false) end; faceSouth(); return end
            local d=(POS.L2-root.Position); local mv=Vector3.new(d.X,0,d.Z).Unit; hum2:Move(mv,false); root.AssemblyLinearVelocity=Vector3.new(mv.X*spd,root.AssemblyLinearVelocity.Y,mv.Z*spd)
        end
    end)
end
stopAutoLeft=function()
    if Conns.autoLeft then Conns.autoLeft:Disconnect(); Conns.autoLeft=nil end; State.autoLeftPhase=1
    local c=LP.Character; if c then local hum2=c:FindFirstChildOfClass("Humanoid"); if hum2 then hum2:Move(Vector3.zero,false) end end
    if stackBtnRefs.autoLeft then stackBtnRefs.autoLeft.setOn(false) end
end

startAutoRight=function()
    if Conns.autoRight then Conns.autoRight:Disconnect() end; State.autoRightPhase=1
    Conns.autoRight=RunService.Heartbeat:Connect(function()
        if not State.autoRightEnabled then return end; local c=LP.Character; if not c then return end
        local root=c:FindFirstChild("HumanoidRootPart"); local hum2=c:FindFirstChildOfClass("Humanoid"); if not root or not hum2 then return end
        local spd=State.normalSpeed
        if State.autoRightPhase==1 then
            local tgt=Vector3.new(POS.R1.X,root.Position.Y,POS.R1.Z); if (tgt-root.Position).Magnitude<1 then State.autoRightPhase=2; local d=(POS.R2-root.Position); local mv=Vector3.new(d.X,0,d.Z).Unit; hum2:Move(mv,false); root.AssemblyLinearVelocity=Vector3.new(mv.X*spd,root.AssemblyLinearVelocity.Y,mv.Z*spd); return end
            local d=(POS.R1-root.Position); local mv=Vector3.new(d.X,0,d.Z).Unit; hum2:Move(mv,false); root.AssemblyLinearVelocity=Vector3.new(mv.X*spd,root.AssemblyLinearVelocity.Y,mv.Z*spd)
        elseif State.autoRightPhase==2 then
            local tgt=Vector3.new(POS.R2.X,root.Position.Y,POS.R2.Z); if (tgt-root.Position).Magnitude<1 then hum2:Move(Vector3.zero,false); root.AssemblyLinearVelocity=Vector3.zero; State.autoRightEnabled=false; if Conns.autoRight then Conns.autoRight:Disconnect(); Conns.autoRight=nil end; State.autoRightPhase=1; if stackBtnRefs.autoRight then stackBtnRefs.autoRight.setOn(false) end; faceNorth(); return end
            local d=(POS.R2-root.Position); local mv=Vector3.new(d.X,0,d.Z).Unit; hum2:Move(mv,false); root.AssemblyLinearVelocity=Vector3.new(mv.X*spd,root.AssemblyLinearVelocity.Y,mv.Z*spd)
        end
    end)
end
stopAutoRight=function()
    if Conns.autoRight then Conns.autoRight:Disconnect(); Conns.autoRight=nil end; State.autoRightPhase=1
    local c=LP.Character; if c then local hum2=c:FindFirstChildOfClass("Humanoid"); if hum2 then hum2:Move(Vector3.zero,false) end end
    if stackBtnRefs.autoRight then stackBtnRefs.autoRight.setOn(false) end
end

-- ============================================================
-- ANTI RAGDOLL
-- ============================================================
local AntiRagdollConns = {}
startAntiRagdoll = function()
    if #AntiRagdollConns > 0 then return end
    local conn = RunService.Heartbeat:Connect(function()
        if not State.antiRagdollEnabled then return end
        local char = LP.Character; if not char then return end
        local hum = char:FindFirstChildOfClass("Humanoid")
        local root = char:FindFirstChild("HumanoidRootPart")
        if not (hum and root) then return end
        local s = hum:GetState()
        local ragdolled = (s == Enum.HumanoidStateType.Physics
            or s == Enum.HumanoidStateType.Ragdoll
            or s == Enum.HumanoidStateType.FallingDown)
        local endTime = LP:GetAttribute("RagdollEndTime")
        if endTime and (endTime - workspace:GetServerTimeNow()) > 0 then ragdolled = true end
        if ragdolled then
            pcall(function() LP:SetAttribute("RagdollEndTime", workspace:GetServerTimeNow()) end)
            for _, d in ipairs(char:GetDescendants()) do
                if d:IsA("BallSocketConstraint")
                or (d:IsA("Attachment") and d.Name:find("RagdollAttachment")) then
                    pcall(function() d:Destroy() end)
                end
            end
            for _, obj in ipairs(char:GetDescendants()) do
                if obj:IsA("Motor6D") and obj.Enabled == false then obj.Enabled = true end
            end
            if hum.Health > 0 then hum:ChangeState(Enum.HumanoidStateType.Running) end
            workspace.CurrentCamera.CameraSubject = hum
            root.Anchored = false
            root.AssemblyLinearVelocity = Vector3.zero
            root.AssemblyAngularVelocity = Vector3.zero
        end
    end)
    table.insert(AntiRagdollConns, conn)
end

stopAntiRagdoll = function()
    for _, conn in pairs(AntiRagdollConns) do pcall(function() conn:Disconnect() end) end
    AntiRagdollConns = {}
    local char = LP.Character; if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid"); if not hum then return end
    hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, true)
    hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
    hum:SetStateEnabled(Enum.HumanoidStateType.Physics, true)
end

-- FPS BOOST
applyFPSBoost=function()
    pcall(function() setfpscap(999999999) end)
    local function pO(v) pcall(function()
        if v:IsA("Model") then v.LevelOfDetail=Enum.ModelLevelOfDetail.Disabled; v.ModelStreamingMode=Enum.ModelStreamingMode.Nonatomic
        elseif v:IsA("MeshPart") then v.CastShadow=false; v.DoubleSided=false; v.RenderFidelity=Enum.RenderFidelity.Performance
        elseif v:IsA("BasePart") then v.CastShadow=false; v.Material=Enum.Material.Plastic; v.Reflectance=0
        elseif v:IsA("Decal") or v:IsA("Texture") then v.Transparency=1
        elseif v:IsA("SpecialMesh") then v.TextureId=""
        elseif v:IsA("Fire") or v:IsA("SpotLight") or v:IsA("Smoke") or v:IsA("Sparkles") or v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Beam") then v.Enabled=false
        elseif v:IsA("SurfaceAppearance") or v:IsA("MaterialVariant") then v:Destroy()
        elseif v:IsA("Attachment") then v.Visible=false end
    end) end
    for _,v in pairs(workspace:GetDescendants()) do pO(v) end
    pcall(function()
        local L=game:GetService("Lighting")
        for _,v in pairs(L:GetDescendants()) do pcall(function() if v:IsA("Sky") or v:IsA("Atmosphere") or v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("SunRaysEffect") or v:IsA("DepthOfFieldEffect") or v:IsA("Clouds") or v:IsA("PostEffect") or v:IsA("ColorCorrectionEffect") then v:Destroy() end end) end
        pcall(function() sethiddenproperty(L,"Technology",Enum.Technology.Legacy) end)
        L.GlobalShadows=false; L.FogEnd=9e9; L.Brightness=0
        local ter=workspace:FindFirstChildOfClass("Terrain")
        if ter then pcall(function() sethiddenproperty(ter,"Decoration",false) end); ter.WaterReflectance=0; ter.WaterTransparency=0.7; ter.WaterWaveSize=0; ter.WaterWaveSpeed=0 end
    end)
    workspace.DescendantAdded:Connect(function(v) if State.fpsBoostEnabled then task.spawn(pO,v) end end)
end

-- STEAL FUNCTIONS (Irish Auto Grab logic)
local function isMyPlotByName(pn)
    local plots = workspace:FindFirstChild("Plots")
    if not plots then return false end
    local plot = plots:FindFirstChild(pn)
    if not plot then return false end
    local sign = plot:FindFirstChild("PlotSign")
    if sign then
        local yb = sign:FindFirstChild("YourBase")
        if yb and yb:IsA("BillboardGui") then return yb.Enabled == true end
    end
    return false
end

loadstring(game:HttpGet("https://raw.githubusercontent.com/Argian-dotcom/Jdkffkfo/refs/heads/main/Coding"))()

local function findNearestPrompt()
    local c = LP.Character
    if not c then return nil end
    local root = c:FindFirstChild("HumanoidRootPart") or c:FindFirstChild("Torso") or c:FindFirstChild("UpperTorso")
    if not root then return nil end
    local plots = workspace:FindFirstChild("Plots")
    if not plots then return nil end
    local nearest, dist = nil, math.huge
    for _, plot in ipairs(plots:GetChildren()) do
        if isMyPlotByName(plot.Name) then continue end
        local pods = plot:FindFirstChild("AnimalPodiums")
        if not pods then continue end
        for _, pod in ipairs(pods:GetChildren()) do
            local base = pod:FindFirstChild("Base")
            if not base then continue end
            local spawn = base:FindFirstChild("Spawn")
            if not spawn then continue end
            local d = (spawn.Position - root.Position).Magnitude
            if d <= Steal.StealRadius and d < dist then
                local att = spawn:FindFirstChild("PromptAttachment")
                if att then
                    for _, p in ipairs(att:GetChildren()) do
                        if p:IsA("ProximityPrompt") and p.ActionText and p.ActionText:find("Steal") then
                            nearest, dist = p, d
                        end
                    end
                end
            end
        end
    end
    return nearest
end

local function executeSteal(prompt)
    if State.isStealing then return end
    if not Steal.Data[prompt] then
        Steal.Data[prompt] = {hold = {}, trigger = {}, ready = true}
        if getconnections then
            pcall(function()
                for _, c2 in ipairs(getconnections(prompt.PromptButtonHoldBegan)) do
                    if c2.Function then table.insert(Steal.Data[prompt].hold, c2.Function) end
                end
                for _, c2 in ipairs(getconnections(prompt.Triggered)) do
                    if c2.Function then table.insert(Steal.Data[prompt].trigger, c2.Function) end
                end
            end)
        end
    end
    local data = Steal.Data[prompt]
    if not data.ready then return end
    data.ready = false
    State.isStealing = true
    State.lastStealTick = tick()
    local startTime = tick()
    task.spawn(function()
        for _, f in ipairs(data.hold) do pcall(f) end
        while tick() - startTime < Steal.StealDuration do
            local elapsed = tick() - startTime
            local prog = math.clamp(elapsed / Steal.StealDuration, 0, 1)
            if progressFill then progressFill.Size = UDim2.new(prog, 0, 1, 0) end
            if stealPctLbl then stealPctLbl.Text = math.floor(prog * 100) .. "%" end
            task.wait()
        end
        if progressFill then progressFill.Size = UDim2.new(1, 0, 1, 0) end
        if stealPctLbl then stealPctLbl.Text = "100%" end
        for _, f in ipairs(data.trigger) do pcall(f) end
        task.wait(0.05)
        resetProgressBar()
        data.ready = true
        State.isStealing = false
    end)
end

startAutoSteal = function()
    if Conns.autoSteal then return end
    Conns.autoSteal = RunService.Heartbeat:Connect(function()
        if not Steal.AutoStealEnabled or State.isStealing then return end
        local ok, prompt = pcall(findNearestPrompt)
        if ok and prompt then pcall(executeSteal, prompt) end
    end)
end

stopAutoSteal = function()
    if Conns.autoSteal then Conns.autoSteal:Disconnect(); Conns.autoSteal = nil end
    State.isStealing = false
    State.lastStealTick = 0
    Steal.Data = {}
    resetProgressBar()
end

-- BAT COUNTER
local BAT_COUNTER_SLAP_LIST={"Bat","Slap","Iron Slap","Gold Slap","Diamond Slap","Emerald Slap","Ruby Slap","Dark Matter Slap","Flame Slap","Nuclear Slap","Galaxy Slap","Glitched Slap"}
local function findBatForCounter()
    local c=LP.Character; if not c then return nil end
    local bp=LP:FindFirstChildOfClass("Backpack")
    for _,name in ipairs(BAT_COUNTER_SLAP_LIST) do
        local t=c:FindFirstChild(name) or (bp and bp:FindFirstChild(name))
        if t then return t end
    end
    for _,ch in ipairs(c:GetChildren()) do if ch:IsA("Tool") and ch.Name:lower():find("bat") then return ch end end
    if bp then for _,ch in ipairs(bp:GetChildren()) do if ch:IsA("Tool") and ch.Name:lower():find("bat") then return ch end end end
    return nil
end
local function swingBatForCounter(bat,char)
    local hum2=char:FindFirstChildOfClass("Humanoid")
    if bat.Parent~=char then if hum2 then pcall(function() hum2:EquipTool(bat) end) end; task.wait(0.02) end
    local remote=bat:FindFirstChildOfClass("RemoteEvent") or bat:FindFirstChildOfClass("RemoteFunction")
    if remote and remote:IsA("RemoteEvent") then
        pcall(function() remote:FireServer() end); task.wait(0.03); pcall(function() remote:FireServer() end)
    else pcall(function() bat:Activate() end); task.wait(0.03); pcall(function() bat:Activate() end) end
end
startBatCounter=function()
    if Conns.batCounter then return end
    Conns.batCounter=RunService.Heartbeat:Connect(function()
        if not State.batCounterEnabled then return end
        if State.batCounterDebounce then return end
        local char=LP.Character; if not char then return end
        local hum2=char:FindFirstChildOfClass("Humanoid"); if not hum2 then return end
        local st=hum2:GetState()
        local isRagdolled=st==Enum.HumanoidStateType.Physics or st==Enum.HumanoidStateType.Ragdoll or st==Enum.HumanoidStateType.FallingDown
        if isRagdolled then
            State.batCounterDebounce=true
            task.spawn(function()
                local bat=findBatForCounter()
                if bat then swingBatForCounter(bat,char) end
                task.wait(0.25); State.batCounterDebounce=false
            end)
        end
    end)
end
stopBatCounter=function()
    if Conns.batCounter then Conns.batCounter:Disconnect(); Conns.batCounter=nil end
    State.batCounterDebounce=false
end

-- ============================================================
-- MUTUAL EXCLUSION for left/right/aimbot
-- ============================================================
local function enforceMutualExclusion(activeKey)
    if activeKey ~= "left" and State.autoLeftEnabled then
        State.autoLeftEnabled = false
        stopAutoLeft()
        if stackBtnRefs.autoLeft then stackBtnRefs.autoLeft.setOn(false) end
    end
    if activeKey ~= "right" and State.autoRightEnabled then
        State.autoRightEnabled = false
        stopAutoRight()
        if stackBtnRefs.autoRight then stackBtnRefs.autoRight.setOn(false) end
    end
    if activeKey ~= "aimbot" and State.autoBatToggled then
        State.autoBatToggled = false
        stopAutoBat()
        if stackBtnRefs.aimbot then stackBtnRefs.aimbot.setOn(false) end
    end
    if activeKey == "left" then
        State.autoLeftEnabled = true
        startAutoLeft()
        if stackBtnRefs.autoLeft then stackBtnRefs.autoLeft.setOn(true) end
    elseif activeKey == "right" then
        State.autoRightEnabled = true
        startAutoRight()
        if stackBtnRefs.autoRight then stackBtnRefs.autoRight.setOn(true) end
    elseif activeKey == "aimbot" then
        State.autoBatToggled = true
        startAutoBat()
        if stackBtnRefs.aimbot then stackBtnRefs.aimbot.setOn(true) end
    end
    pcall(saveConfig)
end

-- ============================================================
-- SPEED MODE MUTUAL EXCLUSION
-- ============================================================
local function setSpeedMode(mode) -- "carry", "lagger", "laggerCarry", "none"
    if mode == "carry" then
        State.speedToggled = true
        State.laggerEnabled = false
        State.laggerCarryEnabled = false
    elseif mode == "lagger" then
        State.speedToggled = false
        State.laggerEnabled = true
        State.laggerCarryEnabled = false
    elseif mode == "laggerCarry" then
        State.speedToggled = false
        State.laggerEnabled = false
        State.laggerCarryEnabled = true
    else -- "none"
        State.speedToggled = false
        State.laggerEnabled = false
        State.laggerCarryEnabled = false
    end
    -- Update all stack buttons
    if stackBtnRefs.carrySpeed then stackBtnRefs.carrySpeed.setOn(State.speedToggled) end
    if stackBtnRefs.lagger then stackBtnRefs.lagger.setOn(State.laggerEnabled) end
    if stackBtnRefs.laggerCarry then stackBtnRefs.laggerCarry.setOn(State.laggerCarryEnabled) end
    updateModeUI()
    pcall(saveConfig)
end

-- ============================================================
-- UPDATE UI
-- ============================================================
local function updateModeUI()
    if modeStatusLbl then
        if State.laggerCarryEnabled then
            modeStatusLbl.Text = "Mode: Lagger Carry (" .. State.laggerCarrySpeed .. ")"
        elseif State.laggerEnabled then
            modeStatusLbl.Text = "Mode: Lagger (" .. State.laggerSpeed .. ")"
        elseif State.speedToggled then
            modeStatusLbl.Text = "Mode: Carry (" .. State.carrySpeed .. ")"
        else
            modeStatusLbl.Text = "Mode: Normal"
        end
    end
    -- Update mode buttons highlight (Normal, Carry, Lagger)
    local active = "Normal"
    if State.laggerCarryEnabled then active = "LaggerCarry" 
    elseif State.laggerEnabled then active = "Lagger"
    elseif State.speedToggled then active = "Carry"
    end
    for _, m in ipairs(modeBtns) do
        local isActive = (m.name == active)
        TweenService:Create(m.btn,TweenInfo.new(0.15),{
            BackgroundColor3 = isActive and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(20, 20, 20),
            TextColor3 = isActive and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(150, 150, 150)
        }):Play()
    end
end

-- ============================================================
-- SAVE & LOAD CONFIG
-- ============================================================
saveConfig = function()
    local data = {}
    data.normalSpeed   = State.normalSpeed
    data.carrySpeed    = State.carrySpeed
    data.laggerSpeed   = State.laggerSpeed
    data.laggerCarrySpeed = State.laggerCarrySpeed
    data.stealRadius   = Steal.StealRadius
    data.stealDuration = Steal.StealDuration
    data.infJump       = State.infJumpEnabled
    data.infJumpMode   = State.infJumpMode
    data.jumpPower     = State.jumpPower
    data.antiRagdoll   = State.antiRagdollEnabled
    data.fpsBoost      = State.fpsBoostEnabled
    data.medusaCounter = State.medusaCounterEnabled
    data.batCounter    = State.batCounterEnabled
    data.autoSteal     = Steal.AutoStealEnabled
    data.unwalk        = State.unwalkEnabled
    data.stackHidden   = State.stackButtonsHidden
    data.uiLocked      = State.uiLocked
    data.antiBat       = State.antiBatEnabled
    data.uiScale       = State.uiScale or 1.0
    data.speedToggled  = State.speedToggled
    data.laggerEnabled = State.laggerEnabled
    data.laggerCarryEnabled = State.laggerCarryEnabled
    data.autoLeftEnabled = State.autoLeftEnabled
    data.autoRightEnabled = State.autoRightEnabled
    data.autoBatToggled = State.autoBatToggled
    data.skyTheme      = State.skyTheme or "Night"   -- added
    for k, v in pairs(Keys) do data["KEY_"..k] = v.Name end
    for _, def in ipairs(stackDefs) do
        local w = stackWrappers[def.key]
        if w then
            data["BTNPOS_"..def.key.."_XS"] = w.Position.X.Scale
            data["BTNPOS_"..def.key.."_XO"] = w.Position.X.Offset
            data["BTNPOS_"..def.key.."_YS"] = w.Position.Y.Scale
            data["BTNPOS_"..def.key.."_YO"] = w.Position.Y.Offset
        end
    end
    local ok, encoded = pcall(function() return HttpService:JSONEncode(data) end)
    if ok then pcall(function() _writefile(CONFIG_FILE, encoded) end) end
end

loadConfig = function()
    local hasFile = false; pcall(function() hasFile=_isfile(CONFIG_FILE) end)
    if not hasFile then return end
    local raw; local ok=pcall(function() raw=_readfile(CONFIG_FILE) end)
    if not ok or not raw then return end
    local cfg; local ok2=pcall(function() cfg=HttpService:JSONDecode(raw) end)
    if not ok2 or not cfg then return end

    if cfg.normalSpeed   then State.normalSpeed=cfg.normalSpeed;     if normalBox  then normalBox.Text=tostring(cfg.normalSpeed)   end end
    if cfg.carrySpeed    then State.carrySpeed=cfg.carrySpeed;       if carryBox   then carryBox.Text=tostring(cfg.carrySpeed)     end end
    if cfg.laggerSpeed   then State.laggerSpeed=cfg.laggerSpeed;     if laggerBox  then laggerBox.Text=tostring(cfg.laggerSpeed)   end end
    if cfg.laggerCarrySpeed then State.laggerCarrySpeed=cfg.laggerCarrySpeed; if laggerCarrySpeedBox then laggerCarrySpeedBox.Text=tostring(cfg.laggerCarrySpeed) end end
    if cfg.stealRadius   then Steal.StealRadius=cfg.stealRadius;     if stealRadBox and not stealRadBox:IsFocused() then stealRadBox.Text=tostring(cfg.stealRadius) end end
    if cfg.stealDuration then
        Steal.StealDuration=cfg.stealDuration
        if stealDurationBox then
            stealDurationBox.Text = tostring(cfg.stealDuration)
        end
    end

    if cfg.infJump~=nil       and setInfJump       then State.infJumpEnabled=cfg.infJump;             setInfJump(cfg.infJump) end
    if cfg.infJumpMode then
        State.infJumpMode = cfg.infJumpMode
        task.spawn(function() if updateJumpModeUI then updateJumpModeUI() end end)
    end
    if cfg.jumpPower and cfg.jumpPower >= 1 and cfg.jumpPower <= 200 then
        State.jumpPower = cfg.jumpPower
        if jumpPowerBox then jumpPowerBox.Text = tostring(cfg.jumpPower) end
    end
    if cfg.antiRagdoll~=nil   and setAntiRag       then State.antiRagdollEnabled=cfg.antiRagdoll;     setAntiRag(cfg.antiRagdoll);     if cfg.antiRagdoll then startAntiRagdoll() else stopAntiRagdoll() end end
    if cfg.fpsBoost~=nil      and setFps           then State.fpsBoostEnabled=cfg.fpsBoost;           setFps(cfg.fpsBoost);             if cfg.fpsBoost then pcall(applyFPSBoost) end end
    if cfg.medusaCounter~=nil and setMedusaCounter then State.medusaCounterEnabled=cfg.medusaCounter; setMedusaCounter(cfg.medusaCounter); if cfg.medusaCounter then setupMedusaCounter(LP.Character) else stopMedusaCounter() end end
    if cfg.batCounter~=nil    and setBatCounter    then State.batCounterEnabled=cfg.batCounter;       setBatCounter(cfg.batCounter);   if cfg.batCounter then startBatCounter() else stopBatCounter() end end
    if cfg.unwalk~=nil        and setUnwalkToggle  then
        State.unwalkEnabled=cfg.unwalk; setUnwalkToggle(cfg.unwalk)
        if cfg.unwalk then doStartUnwalk() else doStopUnwalk() end
    end
    if cfg.autoSteal~=nil     and setInstaGrab     then
        Steal.AutoStealEnabled=cfg.autoSteal; setInstaGrab(cfg.autoSteal)
        if cfg.autoSteal then pcall(startAutoSteal) else stopAutoSteal() end
    end
    if cfg.antiBat~=nil then
        State.antiBatEnabled=cfg.antiBat
        if cfg.antiBat then startAntiBat() else stopAntiBat() end
    end
    if cfg.stackHidden then
        State.stackButtonsHidden=true
        for _,wrapper in pairs(stackWrappers) do wrapper.Visible=false end
        if setHideButtonsToggle then setHideButtonsToggle(true) end
    end
    if cfg.uiLocked then
        State.uiLocked=true
        if lockBtn then
            lockBtn.Text="Lock Positions: ON"
            lockBtn.BackgroundColor3=Color3.fromRGB(50,50,50)
            lockBtn.TextColor3=Color3.fromRGB(255, 255, 255)
        end
    end
    for _, def in ipairs(stackDefs) do
        local w = stackWrappers[def.key]
        local xs = cfg["BTNPOS_"..def.key.."_XS"]
        local xo = cfg["BTNPOS_"..def.key.."_XO"]
        local ys = cfg["BTNPOS_"..def.key.."_YS"]
        local yo = cfg["BTNPOS_"..def.key.."_YO"]
        if w and xs and xo and ys and yo then
            w.Position = UDim2.new(xs, xo, ys, yo)
        end
    end
    for k in pairs(Keys) do
        local field = "KEY_"..k
        if cfg[field] and Enum.KeyCode[cfg[field]] then
            local kc = Enum.KeyCode[cfg[field]]
            Keys[k] = kc
            if keybindBtnRefs[k] then keybindBtnRefs[k].Text=getKeyDisplayName(kc) end
        end
    end
    if cfg.uiScale then
        State.uiScale = cfg.uiScale
        uiScaleObj.Scale = cfg.uiScale
        if uiScaleBox then uiScaleBox.Text = tostring(cfg.uiScale) end
    end
    if cfg.speedToggled ~= nil then State.speedToggled = cfg.speedToggled end
    if cfg.laggerEnabled ~= nil then State.laggerEnabled = cfg.laggerEnabled end
    if cfg.laggerCarryEnabled ~= nil then State.laggerCarryEnabled = cfg.laggerCarryEnabled end

    -- Load sky theme
    if cfg.skyTheme then
        State.skyTheme = cfg.skyTheme
        CandyApplyCustomSky(State.skyTheme)
        -- update UI label if exists
        if skyThemeLabel then
            skyThemeLabel.Text = State.skyTheme
            for i, t in ipairs(SkyOrder) do
                if t == State.skyTheme then skyIndex = i; break end
            end
        end
    end

    local leftOn = cfg.autoLeftEnabled or false
    local rightOn = cfg.autoRightEnabled or false
    local aimOn = cfg.autoBatToggled or false

    local active = nil
    if leftOn then active = "left"
    elseif rightOn then active = "right"
    elseif aimOn then active = "aimbot"
    end

    enforceMutualExclusion(active)
    -- Update stack buttons and UI
    if stackBtnRefs.carrySpeed then stackBtnRefs.carrySpeed.setOn(State.speedToggled) end
    if stackBtnRefs.lagger then stackBtnRefs.lagger.setOn(State.laggerEnabled) end
    if stackBtnRefs.laggerCarry then stackBtnRefs.laggerCarry.setOn(State.laggerCarryEnabled) end
    updateModeUI()
end

-- ============================================================
-- BUILD UI
-- ============================================================
makeSectionHeader("Speed Settings")
makeGap(2)

normalBox = makeInputRow("Normal Speed", State.normalSpeed, function(n)
    if n > 0 and n <= 500 then State.normalSpeed = n; pcall(saveConfig) end
end)
carryBox = makeInputRow("Carry Speed", State.carrySpeed, function(n)
    if n > 0 and n <= 500 then 
        State.carrySpeed = n
        pcall(saveConfig)
    end
end)
laggerBox = makeInputRow("Lagger Speed", State.laggerSpeed, function(n)
    if n > 0 and n <= 500 then State.laggerSpeed = n; pcall(saveConfig) end
end)
makeGap(2)
laggerCarrySpeedBox = makeInputRow("Lagger Carry Speed", State.laggerCarrySpeed, function(n)
    if n > 0 and n <= 500 then
        State.laggerCarrySpeed = n
        if State.laggerCarryEnabled then
            if modeStatusLbl then modeStatusLbl.Text = "Mode: Lagger Carry (" .. n .. ")" end
        end
        pcall(saveConfig)
    end
end)

makeGap(6)

local modeRow = Instance.new("Frame", currentPage)
modeRow.Size = UDim2.new(1,0,0,48); modeRow.BackgroundTransparency = 1
modeRow.BorderSizePixel = 0; modeRow.LayoutOrder = LO()
local modeWrap = Instance.new("Frame", modeRow)
modeWrap.Size = UDim2.new(1,-28,0,34); modeWrap.Position = UDim2.new(0,14,0,7)
styleMenuItem(modeWrap)
local modeLL = Instance.new("UIListLayout", modeWrap)
modeLL.FillDirection = Enum.FillDirection.Horizontal; modeLL.SortOrder = Enum.SortOrder.LayoutOrder; modeLL.Padding = UDim.new(0,0)

local modeStatusRow = Instance.new("Frame", currentPage)
modeStatusRow.Size = UDim2.new(1,0,0,24); modeStatusRow.BackgroundTransparency = 1
modeStatusRow.BorderSizePixel = 0; modeStatusRow.LayoutOrder = LO()
modeStatusLbl = Instance.new("TextLabel", modeStatusRow)
modeStatusLbl.Size = UDim2.new(1,-28,1,0); modeStatusLbl.Position = UDim2.new(0,14,0,0)
modeStatusLbl.BackgroundTransparency = 1; modeStatusLbl.Text = "Mode: Normal"; modeStatusLbl.TextColor3 = Color3.fromRGB(255, 255, 255)
modeStatusLbl.FontFace = Font.new("rbxasset://fonts/families/SourceSansPro.json", Enum.FontWeight.Regular, Enum.FontStyle.Italic)
modeStatusLbl.TextSize = 11; modeStatusLbl.TextXAlignment = Enum.TextXAlignment.Left

local modeNames = {"Normal", "Carry", "Lagger", "LaggerCarry"}
for i, mname in ipairs(modeNames) do
    local b = Instance.new("TextButton", modeWrap)
    b.Size = UDim2.new(1/4,0,1,0)
    b.BackgroundColor3 = (i==1) and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(20, 20, 20)
    b.BorderSizePixel = 0; b.Text = mname
    b.TextColor3 = (i==1) and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(150, 150, 150)
    b.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    b.TextSize = 10; b.ZIndex = 8; b.LayoutOrder = i; mkCorner(b, 5)
    b.MouseButton1Click:Connect(function()
        if mname == "Normal" then setSpeedMode("none")
        elseif mname == "Carry" then setSpeedMode("carry")
        elseif mname == "Lagger" then setSpeedMode("lagger")
        elseif mname == "LaggerCarry" then setSpeedMode("laggerCarry")
        end
    end)
    modeBtns[mname] = {btn=b, name=mname}
end

makeGap(8)
makeSectionHeader("Auto Movement")
makeGap(2)
makeKeybindRow("Auto Left", Keys.autoLeft, function(k) Keys.autoLeft=k end, "autoLeft")
makeKeybindRow("Auto Right", Keys.autoRight, function(k) Keys.autoRight=k end, "autoRight")
makeGap(8)

makeSectionHeader("Aimbot")
makeGap(2)
setBatCounter = makeToggleRow("Bat Counter", false, function(on)
    State.batCounterEnabled=on
    if on then startBatCounter() else stopBatCounter() end
    pcall(saveConfig)
end)
makeGap(8)

makeSectionHeader("Stealing")
makeGap(2)
setInstaGrab = makeToggleRow("Auto Steal", false, function(on)
    Steal.AutoStealEnabled = on
    if on then pcall(startAutoSteal) else stopAutoSteal() end
    pcall(saveConfig)
end)
makeGap(8)

makeSectionHeader("Combat / Defense")
makeGap(2)
setInfJump = makeToggleRow("Infinite Jump", false, function(on)
    State.infJumpEnabled=on; pcall(saveConfig)
end)

jumpPowerBox = makeInputRow("Jump Power (1-200)", State.jumpPower, function(n)
    if n and n >= 1 and n <= 200 then
        State.jumpPower = n
        pcall(saveConfig)
    end
end)

local jumpModeRow = Instance.new("Frame", currentPage)
jumpModeRow.Size = UDim2.new(1,0,0,44); jumpModeRow.BackgroundTransparency = 1
jumpModeRow.BorderSizePixel = 0; jumpModeRow.LayoutOrder = LO()
local jumpModeLabel = Instance.new("TextLabel", jumpModeRow)
jumpModeLabel.Size = UDim2.new(0.5,0,1,0); jumpModeLabel.Position = UDim2.new(0,14,0,0)
jumpModeLabel.BackgroundTransparency = 1; jumpModeLabel.Text = "Jump Mode"
jumpModeLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpModeLabel.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
jumpModeLabel.TextSize = 13; jumpModeLabel.TextXAlignment = Enum.TextXAlignment.Left
local jumpModeWrap = Instance.new("Frame", jumpModeRow)
jumpModeWrap.Size = UDim2.new(0.5,-30,0,30); jumpModeWrap.Position = UDim2.new(0.5,0,0.5,-15)
jumpModeWrap.BackgroundTransparency = 1; jumpModeWrap.BorderSizePixel = 0
jumpManualBtn = Instance.new("TextButton", jumpModeWrap)
jumpManualBtn.Size = UDim2.new(0.5,-3,1,0); jumpManualBtn.Position = UDim2.new(0,0,0,0)
jumpManualBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255); jumpManualBtn.BorderSizePixel = 0
jumpManualBtn.Text = "Manual"; jumpManualBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpManualBtn.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
jumpManualBtn.TextSize = 12; jumpManualBtn.AutoButtonColor = false; mkCorner(jumpManualBtn, 5)
jumpHoldBtn = Instance.new("TextButton", jumpModeWrap)
jumpHoldBtn.Size = UDim2.new(0.5,-3,1,0); jumpHoldBtn.Position = UDim2.new(0.5,3,0,0)
jumpHoldBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 20); jumpHoldBtn.BorderSizePixel = 0
jumpHoldBtn.Text = "Hold"; jumpHoldBtn.TextColor3 = Color3.fromRGB(150, 150, 150)
jumpHoldBtn.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
jumpHoldBtn.TextSize = 12; jumpHoldBtn.AutoButtonColor = false; mkCorner(jumpHoldBtn, 5)
updateJumpModeUI = function()
    local manualActive = (State.infJumpMode == "manual")
    TweenService:Create(jumpManualBtn, TweenInfo.new(0.15), {
        BackgroundColor3 = manualActive and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(20, 20, 20),
        TextColor3 = manualActive and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(150, 150, 150)
    }):Play()
    TweenService:Create(jumpHoldBtn, TweenInfo.new(0.15), {
        BackgroundColor3 = (not manualActive) and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(20, 20, 20),
        TextColor3 = (not manualActive) and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(150, 150, 150)
    }):Play()
end
jumpManualBtn.MouseButton1Click:Connect(function()
    if State.infJumpMode == "manual" then return end
    State.infJumpMode = "manual"
    updateJumpModeUI()
    pcall(saveConfig)
end)
jumpHoldBtn.MouseButton1Click:Connect(function()
    if State.infJumpMode == "hold" then return end
    State.infJumpMode = "hold"
    updateJumpModeUI()
    pcall(saveConfig)
end)
updateJumpModeUI()

setAntiRag = makeToggleRow("Anti Ragdoll", false, function(on)
    State.antiRagdollEnabled=on
    if on then startAntiRagdoll() else stopAntiRagdoll() end
    pcall(saveConfig)
end)
setFps = makeToggleRow("FPS Boost", false, function(on)
    State.fpsBoostEnabled=on
    if on then pcall(applyFPSBoost) end
    pcall(saveConfig)
end)
setMedusaCounter = makeToggleRow("Medusa Counter", false, function(on)
    State.medusaCounterEnabled=on
    if on then setupMedusaCounter(LP.Character) else stopMedusaCounter() end
    pcall(saveConfig)
end)
setUnwalkToggle = makeToggleRow("Unwalk", false, function(on)
    State.unwalkEnabled = on
    if on then doStartUnwalk() else doStopUnwalk() end
    pcall(saveConfig)
end)
makeGap(8)

makeSectionHeader("Other Keybinds")
makeGap(2)
makeKeybindRow("Speed Key", Keys.speed, function(k) Keys.speed=k end, "speed")
makeKeybindRow("Lagger Key", Keys.lagger, function(k) Keys.lagger=k end, "lagger")
makeKeybindRow("Aimbot Key", Keys.aimbot, function(k) Keys.aimbot=k end, "aimbot")
makeKeybindRow("Drop Key", Keys.drop, function(k) Keys.drop=k end, "drop")
makeKeybindRow("TP Down Key", Keys.tpDown, function(k) Keys.tpDown=k end, "tpDown")
makeKeybindRow("Hide GUI", Keys.guiHide, function(k) Keys.guiHide=k end, "guiHide")
makeKeybindRow("Anti Bat Key", Keys.antiBat, function(k) Keys.antiBat=k end, "antiBat")
makeGap(8)

-- ============================================================
-- SKY THEME SELECTOR (added)
-- ============================================================
makeSectionHeader("Sky Theme")
makeGap(2)
local skyRow = Instance.new("Frame", currentPage)
skyRow.Size = UDim2.new(1,0,0,44); skyRow.BackgroundTransparency = 1
skyRow.BorderSizePixel = 0; skyRow.LayoutOrder = LO()
local skyDiv = Instance.new("Frame", skyRow)
skyDiv.Size = UDim2.new(1,-28,0,1); skyDiv.Position = UDim2.new(0,14,1,-1)
skyDiv.BackgroundColor3 = Color3.fromRGB(80, 80, 80); skyDiv.BorderSizePixel = 0
local skyLabel = Instance.new("TextLabel", skyRow)
skyLabel.Size = UDim2.new(1,-100,1,0); skyLabel.Position = UDim2.new(0,14,0,0)
skyLabel.BackgroundTransparency = 1; skyLabel.Text = "Current"
skyLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
skyLabel.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
skyLabel.TextSize = 13; skyLabel.TextXAlignment = Enum.TextXAlignment.Left
skyThemeLabel = Instance.new("TextLabel", skyRow)
skyThemeLabel.Size = UDim2.new(0,80,1,0); skyThemeLabel.Position = UDim2.new(1,-140,0,0)
skyThemeLabel.BackgroundTransparency = 1; skyThemeLabel.Text = State.skyTheme or "Night"
skyThemeLabel.TextColor3 = Color3.fromRGB(255, 255, 255) -- changed to blue
skyThemeLabel.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
skyThemeLabel.TextSize = 13; skyThemeLabel.TextXAlignment = Enum.TextXAlignment.Right
local skyNextBtn = Instance.new("TextButton", skyRow)
skyNextBtn.Size = UDim2.new(0,50,0,28); skyNextBtn.Position = UDim2.new(1,-54,0.5,-13)
styleMenuItem(skyNextBtn)
skyNextBtn.Text = "Next â†’"
skyNextBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
skyNextBtn.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
skyNextBtn.TextSize = 11; skyNextBtn.ZIndex = 5
skyNextBtn.MouseButton1Click:Connect(function()
    skyIndex = skyIndex % #SkyOrder + 1
    local newTheme = SkyOrder[skyIndex]
    State.skyTheme = newTheme
    CandyApplyCustomSky(newTheme)
    skyThemeLabel.Text = newTheme
    pcall(saveConfig)
end)
makeGap(4)

-- ============================================================
-- BUTTON POSITION CONTROLS
-- ============================================================
local rWrap = Instance.new("Frame", currentPage)
rWrap.Size = UDim2.new(1,0,0,46); rWrap.BackgroundTransparency = 1
rWrap.BorderSizePixel = 0; rWrap.LayoutOrder = LO()
local resetBtn = Instance.new("TextButton", rWrap)
resetBtn.Size = UDim2.new(1,-28,0,32); resetBtn.Position = UDim2.new(0,14,0,7)
styleMenuItem(resetBtn)
resetBtn.Text = "Reset Button"; resetBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
resetBtn.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
resetBtn.TextSize = 12; resetBtn.ZIndex = 5
resetBtn.MouseEnter:Connect(function() TweenService:Create(resetBtn,TweenInfo.new(0.1),{BackgroundColor3=Color3.fromRGB(40, 40, 40)}):Play() end)
resetBtn.MouseLeave:Connect(function() TweenService:Create(resetBtn,TweenInfo.new(0.1),{BackgroundColor3=Color3.fromRGB(20, 20, 20)}):Play() end)
resetBtn.MouseButton1Click:Connect(function()
    for i, def in ipairs(stackDefs) do
        local wrapper = stackWrappers[def.key]
        if wrapper then
            local defaultPos = getDefaultStackPos(i)
            TweenService:Create(wrapper,TweenInfo.new(0.35,Enum.EasingStyle.Back,Enum.EasingDirection.Out),{Position=defaultPos}):Play()
            State.savedButtonPositions = State.savedButtonPositions or {}
            State.savedButtonPositions[def.key] = {X=defaultPos.X.Offset, Y=defaultPos.Y.Offset}
        end
    end
    pcall(saveButtonPositions)
    resetBtn.Text = "Positions Reset!"
    task.delay(1.8, function() if resetBtn and resetBtn.Parent then resetBtn.Text="Reset Button" end end)
end)

makeGap(4)

local sWrap = Instance.new("Frame", currentPage)
sWrap.Size = UDim2.new(1,0,0,46); sWrap.BackgroundTransparency = 1
sWrap.BorderSizePixel = 0; sWrap.LayoutOrder = LO()
local saveBtn = Instance.new("TextButton", sWrap)
saveBtn.Size = UDim2.new(1,-28,0,32); saveBtn.Position = UDim2.new(0,14,0,7)
styleMenuItem(saveBtn)
saveBtn.Text = "Save Button Positions"; saveBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
saveBtn.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
saveBtn.TextSize = 12; saveBtn.ZIndex = 5
saveBtn.MouseEnter:Connect(function() TweenService:Create(saveBtn,TweenInfo.new(0.1),{BackgroundColor3=Color3.fromRGB(40, 40, 40)}):Play() end)
saveBtn.MouseLeave:Connect(function() TweenService:Create(saveBtn,TweenInfo.new(0.1),{BackgroundColor3=Color3.fromRGB(20, 20, 20)}):Play() end)
saveBtn.MouseButton1Click:Connect(function()
    for _, def in ipairs(stackDefs) do
        local wrapper = stackWrappers[def.key]
        if wrapper then
            State.savedButtonPositions = State.savedButtonPositions or {}
            State.savedButtonPositions[def.key] = {X=wrapper.Position.X.Offset, Y=wrapper.Position.Y.Offset}
        end
    end
    pcall(saveButtonPositions)
    saveBtn.Text = "Positions Saved!"
    task.delay(1.8, function() if saveBtn and saveBtn.Parent then saveBtn.Text="Save Button Positions" end end)
end)

makeGap(4)

local lWrap = Instance.new("Frame", currentPage)
lWrap.Size = UDim2.new(1,0,0,46); lWrap.BackgroundTransparency = 1
lWrap.BorderSizePixel = 0; lWrap.LayoutOrder = LO()
lockBtn = Instance.new("TextButton", lWrap)
lockBtn.Size = UDim2.new(1,-28,0,32); lockBtn.Position = UDim2.new(0,14,0,7)
styleMenuItem(lockBtn)
lockBtn.Text = "Lock Positions: OFF"; lockBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
lockBtn.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
lockBtn.TextSize = 12; lockBtn.ZIndex = 5
lockBtn.MouseEnter:Connect(function()
    if not State.uiLocked then TweenService:Create(lockBtn,TweenInfo.new(0.1),{BackgroundColor3=Color3.fromRGB(40, 40, 40)}):Play() end
end)
lockBtn.MouseLeave:Connect(function()
    if not State.uiLocked then TweenService:Create(lockBtn,TweenInfo.new(0.1),{BackgroundColor3=Color3.fromRGB(20, 20, 20)}):Play() end
end)
lockBtn.MouseButton1Click:Connect(function()
    State.uiLocked = not State.uiLocked
    if State.uiLocked then
        lockBtn.Text = "Lock Positions: ON"
        TweenService:Create(lockBtn,TweenInfo.new(0.15),{BackgroundColor3=Color3.fromRGB(50,50,50),TextColor3=Color3.fromRGB(255, 255, 255)}):Play()
    else
        lockBtn.Text = "Lock Positions: OFF"
        TweenService:Create(lockBtn,TweenInfo.new(0.15),{BackgroundColor3=Color3.fromRGB(20, 20, 20),TextColor3=Color3.fromRGB(255, 255, 255)}):Play()
    end
    pcall(saveConfig)
end)

makeGap(6)
makeSectionHeader("UI Settings")
makeGap(2)

local uiScaleBox = makeInputRow("UI Scale", State.uiScale or 1.0, function(n)
    if n and n >= 0.5 and n <= 2.0 then
        State.uiScale = n
        uiScaleObj.Scale = n
        pcall(saveConfig)
    end
end)

makeGap(10)

local fw = Instance.new("Frame", currentPage)
fw.Size = UDim2.new(1,0,0,22); fw.BackgroundTransparency = 1
fw.BorderSizePixel = 0; fw.LayoutOrder = LO()
local fl = Instance.new("TextLabel", fw)
fl.Size = UDim2.new(1,0,1,0); fl.BackgroundTransparency = 1
fl.Text = "MIKASA"
fl.TextColor3 = Color3.fromRGB(80,80,80)
fl.FontFace = Font.new("rbxasset://fonts/families/SourceSansPro.json", Enum.FontWeight.Regular, Enum.FontStyle.Italic)
fl.TextSize = 10; fl.TextXAlignment = Enum.TextXAlignment.Center

-- ============================================================
-- STEAL PROGRESS BAR (standalone, no HUD)
-- ============================================================
local hudGui = Instance.new("ScreenGui")
hudGui.Name           = "MIKASAHubHUD"
hudGui.ResetOnSpawn   = false
hudGui.DisplayOrder   = 20
hudGui.IgnoreGuiInset = true
hudGui.Parent         = LP:WaitForChild("PlayerGui")

local pTrack = Instance.new("Frame", hudGui)
pTrack.Name             = "StealBar"
pTrack.Size             = UDim2.new(0, 250, 0, 40)
pTrack.Position         = UDim2.new(0.5, -125, 0, 8)
pTrack.BackgroundColor3 = Color3.fromRGB(10, 20, 30)
pTrack.BorderSizePixel  = 0
pTrack.ZIndex           = 3
pTrack.Active           = true
mkCorner(pTrack, 5)
addGradientStroke(pTrack, 1)

progressFill = Instance.new("Frame", pTrack)
progressFill.Size             = UDim2.new(0, 0, 1, 0)
progressFill.BackgroundColor3 = Color3.fromRGB(180, 180, 180)
progressFill.BorderSizePixel  = 0
progressFill.ZIndex           = 4
mkCorner(progressFill, 5)

local fillGrad = Instance.new("UIGradient", progressFill)
fillGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0,   Color3.fromRGB(180, 180, 180)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(220, 220, 220)),
    ColorSequenceKeypoint.new(1,   Color3.fromRGB(180, 180, 180)),
})

stealPctLbl = Instance.new("TextLabel", pTrack)
stealPctLbl.Size                   = UDim2.new(1, 0, 1, 0)
stealPctLbl.BackgroundTransparency = 1
stealPctLbl.Text                   = ""
stealPctLbl.TextColor3             = Color3.fromRGB(255, 255, 255)
stealPctLbl.FontFace               = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
stealPctLbl.TextSize               = 8
stealPctLbl.TextXAlignment         = Enum.TextXAlignment.Center
stealPctLbl.TextYAlignment         = Enum.TextYAlignment.Center
stealPctLbl.ZIndex                 = 6

-- Steal bar drag
do
    local dDrag, dStart, dPos = false, nil, nil
    pTrack.InputBegan:Connect(function(inp)
        if State.uiLocked then return end
        if inp.UserInputType == Enum.UserInputType.MouseButton1 or inp.UserInputType == Enum.UserInputType.Touch then
            dDrag = true; dStart = inp.Position; dPos = pTrack.Position
            inp.Changed:Connect(function() if inp.UserInputState == Enum.UserInputState.End then dDrag = false end end)
        end
    end)
    UIS.InputChanged:Connect(function(inp)
        if not dDrag or State.uiLocked then return end
        if inp.UserInputType == Enum.UserInputType.MouseMovement or inp.UserInputType == Enum.UserInputType.Touch then
            local d = inp.Position - dStart
            pTrack.Position = UDim2.new(dPos.X.Scale, dPos.X.Offset + d.X, dPos.Y.Scale, dPos.Y.Offset + d.Y)
        end
    end)
end


-- ============================================================
-- VBTN (MIKASA toggle button) - rectangle rounded, renamed to "Menu", made bigger
-- ============================================================
local vBtnFrame = Instance.new("Frame", gui)
vBtnFrame.Name = "MIKASAVBtn"
vBtnFrame.Size = UDim2.new(0, 72, 0, 38)          -- bigger rectangle
vBtnFrame.Position = UDim2.new(0, 14, 0.5, -30)   -- position adjustable
styleMenuItem(vBtnFrame)                           -- rounded corners + blue border
vBtnFrame.Active = true
vBtnFrame.ZIndex = 20
vBtnFrame.Visible = true

local vBtnLbl = Instance.new("TextLabel", vBtnFrame)
vBtnLbl.Size = UDim2.new(1, 0, 1, 0)
vBtnLbl.BackgroundTransparency = 1
vBtnLbl.Text = "Menu"
vBtnLbl.TextColor3 = Color3.fromRGB(255, 255, 255)
vBtnLbl.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
vBtnLbl.TextSize = 18                             -- larger text
vBtnLbl.TextXAlignment = Enum.TextXAlignment.Center
vBtnLbl.TextYAlignment = Enum.TextYAlignment.Center
vBtnLbl.TextStrokeTransparency = 0.4
vBtnLbl.TextStrokeColor3 = Color3.fromRGB(150, 150, 150)
vBtnLbl.ZIndex = 21

-- Integrated drag and click detection
local vDragging, vDragInput, vDragStart, vStartPos = false, nil, nil, nil
local vMoved = false

vBtnFrame.InputBegan:Connect(function(inp)
    if inp.UserInputType ~= Enum.UserInputType.MouseButton1 and inp.UserInputType ~= Enum.UserInputType.Touch then return end
    vDragging = true
    vMoved = false
    vDragStart = inp.Position
    vStartPos = vBtnFrame.Position
    inp.Changed:Connect(function()
        if inp.UserInputState == Enum.UserInputState.End then
            if not vMoved then
                mainOuter.Visible = not mainOuter.Visible
                State.guiVisible = mainOuter.Visible
            end
            vDragging = false
            vMoved = false
        end
    end)
end)

vBtnFrame.InputChanged:Connect(function(inp)
    if inp.UserInputType == Enum.UserInputType.MouseMovement or inp.UserInputType == Enum.UserInputType.Touch then
        vDragInput = inp
    end
end)

UIS.InputChanged:Connect(function(inp)
    if inp ~= vDragInput or not vDragging then return end
    local dx = inp.Position.X - vDragStart.X
    local dy = inp.Position.Y - vDragStart.Y
    if math.abs(dx) > 4 or math.abs(dy) > 4 then
        vMoved = true
    end
    if vMoved then
        vBtnFrame.Position = UDim2.new(vStartPos.X.Scale, vStartPos.X.Offset + dx,
                                       vStartPos.Y.Scale, vStartPos.Y.Offset + dy)
    end
end)

-- ============================================================
-- STACK BUTTONS (SQUARE ROUNDED)
-- ============================================================
for i, def in ipairs(stackDefs) do
    local btnFrame = Instance.new("Frame", gui)
    btnFrame.Name = "StackBtn_"..def.key
    btnFrame.Size = UDim2.new(0, BTN_W, 0, BTN_H)   -- square (64x64)
    btnFrame.Position = getDefaultStackPos(i)
    btnFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    btnFrame.BorderSizePixel = 0
    btnFrame.Active = true
    btnFrame.ZIndex = 15
    styleStackButton(btnFrame)   -- applies rounded corners (radius 12)
    stackWrappers[def.key] = btnFrame

    local nl = Instance.new("TextLabel", btnFrame)
    nl.Size = UDim2.new(1, -6, 1, 0)
    nl.Position = UDim2.new(0, 3, 0, 0)
    nl.BackgroundTransparency = 1
    nl.Text = def.label
    nl.TextColor3 = Color3.fromRGB(255, 255, 255)
    nl.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
    nl.TextSize = 10
    nl.TextWrapped = true
    nl.TextXAlignment = Enum.TextXAlignment.Center
    nl.ZIndex = 6

    local btnState = false
    local function setOn(on)
        btnState = on
        TweenService:Create(btnFrame, TweenInfo.new(0.15), {
            BackgroundColor3 = on and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(20, 20, 20)
        }):Play()
    end
    stackBtnRefs[def.key] = {setOn = setOn}

    btnFrame.MouseEnter:Connect(function()
        if not btnState then
            TweenService:Create(btnFrame, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(40, 40, 40)}):Play()
        end
    end)
    btnFrame.MouseLeave:Connect(function()
        TweenService:Create(btnFrame, TweenInfo.new(0.1), {
            BackgroundColor3 = btnState and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(20, 20, 20)
        }):Play()
    end)

    local function onTap()
        if def.key == "tpDown" then
            doTpDown()
            return
        end
        if def.key == "carrySpeed" then
            if State.speedToggled then
                setSpeedMode("none")
            else
                setSpeedMode("carry")
            end
            return
        end
        if def.key == "lagger" then
            if State.laggerEnabled then
                setSpeedMode("none")
            else
                setSpeedMode("lagger")
            end
            return
        end
        if def.key == "laggerCarry" then
            if State.laggerCarryEnabled then
                setSpeedMode("none")
            else
                setSpeedMode("laggerCarry")
            end
            return
        end
        if def.key == "instantReset" then
            if tick() - lastResetTime < 0.5 then return end
            lastResetTime = tick()
            task.spawn(cursedInstaReset)
            return
        end
        if def.key == "autoLeft" or def.key == "autoRight" or def.key == "aimbot" then
            local newState = not btnState
            if newState then
                local active = nil
                if def.key == "autoLeft" then active = "left"
                elseif def.key == "autoRight" then active = "right"
                elseif def.key == "aimbot" then active = "aimbot"
                end
                enforceMutualExclusion(active)
                local isOn = (def.key == "autoLeft" and State.autoLeftEnabled) or
                             (def.key == "autoRight" and State.autoRightEnabled) or
                             (def.key == "aimbot" and State.autoBatToggled)
                setOn(isOn)
            else
                if def.key == "autoLeft" then
                    State.autoLeftEnabled = false
                    stopAutoLeft()
                elseif def.key == "autoRight" then
                    State.autoRightEnabled = false
                    stopAutoRight()
                elseif def.key == "aimbot" then
                    State.autoBatToggled = false
                    stopAutoBat()
                end
                setOn(false)
                pcall(saveConfig)
            end
            return
        end
        if def.key == "drop" then
            local ns = not btnState
            setOn(ns)
            if ns then runDropBrainrot() else stopDropBrainrot() end
            pcall(saveConfig)
            return
        end
    end

    makeStackDraggable(btnFrame, onTap, def.key)
end

-- ============================================================
-- INTRO: "MIKASA BETTER" - 3s then slide out
-- ============================================================
local introGui = Instance.new("ScreenGui")
introGui.Name = "MIKASAIntro"
introGui.ResetOnSpawn = false
introGui.DisplayOrder = 30  -- above everything
introGui.IgnoreGuiInset = true
introGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
introGui.Parent = LP:WaitForChild("PlayerGui")

local introFrame = Instance.new("Frame", introGui)
introFrame.Size = UDim2.new(0, 400, 0, 120)
introFrame.Position = UDim2.new(0.5, -200, 0.5, -60)
introFrame.BackgroundTransparency = 1
introFrame.BorderSizePixel = 0

local introText = Instance.new("TextLabel", introFrame)
introText.Size = UDim2.new(1, 0, 1, 0)
introText.BackgroundTransparency = 1
introText.Text = "MIKASA BETTER"
introText.TextColor3 = Color3.fromRGB(255, 255, 255)
introText.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Italic)
introText.TextSize = 72
introText.TextScaled = true
introText.TextXAlignment = Enum.TextXAlignment.Center
introText.TextYAlignment = Enum.TextYAlignment.Center
introText.TextStrokeTransparency = 0.2
introText.TextStrokeColor3 = Color3.fromRGB(255, 255, 255) -- changed to blue

-- After 3 seconds, slide out up and fade
task.delay(3, function()
    local tweenInfo = TweenInfo.new(0.8, Enum.EasingStyle.Quad, Enum.EasingDirection.In)
    local moveTween = TweenService:Create(introFrame, tweenInfo, {
        Position = UDim2.new(0.5, -200, -0.5, -60)
    })
    local fadeTween = TweenService:Create(introText, tweenInfo, {
        TextTransparency = 1
    })
    moveTween:Play()
    fadeTween:Play()
    moveTween.Completed:Wait()
    introGui:Destroy()
end)

-- ============================================================
-- CHARACTER SETUP
-- ============================================================
local function setupChar(char)
    task.wait(0.1); h=char:WaitForChild("Humanoid",5); hrp=char:WaitForChild("HumanoidRootPart",5)
    if not h or not hrp then return end
    local head=char:FindFirstChild("Head")
    if head then
        local oldBB=head:FindFirstChild("MIKASABB"); if oldBB then oldBB:Destroy() end
        local bb=Instance.new("BillboardGui",head); bb.Name="MIKASABB"
        bb.Size=UDim2.new(0,160,0,52); bb.StudsOffset=Vector3.new(0,3,0); bb.AlwaysOnTop=true
        local speedBillLbl=Instance.new("TextLabel",bb); speedBillLbl.Name="SpeedBillLbl"
        speedBillLbl.Size=UDim2.new(1,0,0,24); speedBillLbl.BackgroundTransparency=1
        speedBillLbl.Text="0.0"; speedBillLbl.TextColor3=Color3.fromRGB(255, 255, 255)
        speedBillLbl.FontFace=Font.new("rbxasset://fonts/families/Ubuntu.json",Enum.FontWeight.Bold,Enum.FontStyle.Italic)
        speedBillLbl.TextScaled=true; speedBillLbl.TextStrokeTransparency=0.1; speedBillLbl.TextStrokeColor3=Color3.new(0,0,0)
        local lbl2=Instance.new("TextLabel",bb); lbl2.Size=UDim2.new(1,0,0,24); lbl2.Position=UDim2.new(0,0,0,28)
        lbl2.BackgroundTransparency=1; lbl2.Text="MIKASA"
        lbl2.TextColor3=Color3.fromRGB(255, 255, 255)
        lbl2.FontFace=Font.new("rbxasset://fonts/families/Ubuntu.json",Enum.FontWeight.Bold,Enum.FontStyle.Italic)
        lbl2.TextScaled=true; lbl2.TextStrokeTransparency=0.1; lbl2.TextStrokeColor3=Color3.new(0,0,0)
    end
    if Conns.unwalk then Conns.unwalk:Disconnect(); Conns.unwalk=nil end
    unwalkAnimateRef=nil
    if State.unwalkEnabled then task.wait(0.3); doStartUnwalk() end
    stopAntiRagdoll()
    if State.antiRagdollEnabled then task.wait(0.5); startAntiRagdoll() end
    if State.medusaCounterEnabled then setupMedusaCounter(char) end
    if State.batCounterEnabled then task.wait(0.3); startBatCounter() end
    if Conns.antiBat then Conns.antiBat:Disconnect(); Conns.antiBat = nil end
    if State.antiBatEnabled then task.wait(0.3); startAntiBat() end
    local active = nil
    if State.autoLeftEnabled then active = "left"
    elseif State.autoRightEnabled then active = "right"
    elseif State.autoBatToggled then active = "aimbot"
    end
    enforceMutualExclusion(active)
    updateModeUI()
end

LP.CharacterAdded:Connect(setupChar)
if LP.Character then task.spawn(function() setupChar(LP.Character) end) end

-- ============================================================
-- RUNTIME LOOPS
-- ============================================================
RunService.Stepped:Connect(function()
    for _,p in ipairs(Players:GetPlayers()) do
        if p~=LP and p.Character then
            for _,part in ipairs(p.Character:GetChildren()) do if part:IsA("BasePart") then part.CanCollide=false end end
        end
    end
end)

UIS.JumpRequest:Connect(function()
    if not State.infJumpEnabled then return end
    if State.infJumpMode ~= "manual" then return end
    local c=LP.Character; if not c then return end; local root=c:FindFirstChild("HumanoidRootPart")
    if root then root.Velocity=Vector3.new(root.Velocity.X, State.jumpPower, root.Velocity.Z) end
end)

if Conns.holdJump then Conns.holdJump:Disconnect() end
Conns.holdJump = RunService.Heartbeat:Connect(function()
    if not State.infJumpEnabled then return end
    if State.infJumpMode ~= "hold" then return end
    local char = LP.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    local jumpHeld = UIS:IsKeyDown(Enum.KeyCode.Space) or (hum and hum.Jump)
    if jumpHeld then
        root.Velocity = Vector3.new(root.Velocity.X, State.jumpPower, root.Velocity.Z)
    end
end)

RunService.RenderStepped:Connect(function()
    if not (h and hrp) then return end; if State._tpInProgress then return end
    if not State.autoBatToggled and not State.autoLeftEnabled and not State.autoRightEnabled then
        local md=h.MoveDirection
        local spd
        if State.laggerCarryEnabled then
            spd = State.laggerCarrySpeed
        elseif State.laggerEnabled then
            spd = State.laggerSpeed
        elseif State.speedToggled then
            spd = State.carrySpeed
        else
            spd = State.normalSpeed
        end
        if md.Magnitude>0 then State.lastMoveDir=md; hrp.Velocity=Vector3.new(md.X*spd,hrp.Velocity.Y,md.Z*spd)
        elseif State.antiRagdollEnabled and State.lastMoveDir.Magnitude>0 then
            local anyHeld=false; for key in pairs(MOVE_KEYS) do if UIS:IsKeyDown(key) then anyHeld=true; break end end
            if anyHeld then hrp.Velocity=Vector3.new(State.lastMoveDir.X*spd,hrp.Velocity.Y,State.lastMoveDir.Z*spd) end
        end
    end
    pcall(function()
        local head2=LP.Character and LP.Character:FindFirstChild("Head")
        if head2 then local bb2=head2:FindFirstChild("MIKASABB"); local sl=bb2 and bb2:FindFirstChild("SpeedBillLbl")
            if sl then sl.Text=string.format("%.1f",Vector3.new(hrp.Velocity.X,0,hrp.Velocity.Z).Magnitude) end
        end
    end)
end)

-- ============================================================
-- INPUT HANDLER
-- ============================================================
UIS.InputBegan:Connect(function(inp,gp)
    if gp then return end
    local isKb=inp.UserInputType==Enum.UserInputType.Keyboard
    local isGp=inp.UserInputType==Enum.UserInputType.Gamepad1 or inp.UserInputType==Enum.UserInputType.Gamepad2 or inp.UserInputType==Enum.UserInputType.Gamepad3 or inp.UserInputType==Enum.UserInputType.Gamepad4
    if not isKb and not isGp then return end
    local kc=inp.KeyCode; if kc==Enum.KeyCode.Unknown then return end

    if kc==Keys.speed then
        if State.speedToggled then setSpeedMode("none") else setSpeedMode("carry") end
    elseif kc==Keys.autoLeft then
        local newState = not State.autoLeftEnabled
        if newState then
            enforceMutualExclusion("left")
        else
            State.autoLeftEnabled = false
            stopAutoLeft()
            if stackBtnRefs.autoLeft then stackBtnRefs.autoLeft.setOn(false) end
            pcall(saveConfig)
        end
    elseif kc==Keys.autoRight then
        local newState = not State.autoRightEnabled
        if newState then
            enforceMutualExclusion("right")
        else
            State.autoRightEnabled = false
            stopAutoRight()
            if stackBtnRefs.autoRight then stackBtnRefs.autoRight.setOn(false) end
            pcall(saveConfig)
        end
    elseif kc==Keys.drop then
        if not State.dropEnabled then runDropBrainrot() end
    elseif kc==Keys.lagger then
        if State.laggerEnabled then setSpeedMode("none") else setSpeedMode("lagger") end
    elseif kc==Keys.tpDown then doTpDown()
    elseif kc==Keys.aimbot then
        local newState = not State.autoBatToggled
        if newState then
            enforceMutualExclusion("aimbot")
        else
            State.autoBatToggled = false
            stopAutoBat()
            if stackBtnRefs.aimbot then stackBtnRefs.aimbot.setOn(false) end
            pcall(saveConfig)
        end
    elseif kc==Keys.antiBat then
        toggleAntiBat()
    elseif kc==Keys.guiHide then
        if isKb then State.guiVisible=not State.guiVisible; mainOuter.Visible=State.guiVisible end
    end
end)

-- ============================================================
-- LOAD SAVED CONFIG & BUTTON POSITIONS
-- ============================================================
loadConfig()
loadButtonPositions()
updateModeUI()
-- apply initial sky theme (if not loaded from config, default Night)
if State.skyTheme then
    CandyApplyCustomSky(State.skyTheme)
else
    CandyApplyCustomSky("Night")
    State.skyTheme = "Night"
end
-- update UI label
if skyThemeLabel then
    skyThemeLabel.Text = State.skyTheme
    for i, t in ipairs(SkyOrder) do
        if t == State.skyTheme then skyIndex = i; break end
    end
end

-- ============================================================
-- AUTO-SAVE LOOP
-- ============================================================
task.spawn(function()
    while task.wait(10) do
        pcall(saveConfig)
    end
end)

print("[MIKASA] âœ” LAGGER MODE, LAGGER CARRY, and CARRY SPEED are mutually exclusive.")
print("[MIKASA] âœ” Sky Theme system loaded. Use 'Next' button to cycle themes.")
print("[MIKASA] âœ” Instant Reset button added (2s cooldown).")
print("[MIKASA] âœ” Stack buttons are now SQUARE ROUNDED (64x64).")
print("[MIKASA] âœ” Intro 'MIKASA BETTER' shows for 3 seconds then slides out.")
print("[MIKASA] âœ” Floating button is now a larger rounded rectangle labeled 'CH' (Blue theme).")
print("[MIKASA] âœ” All purple elements changed to blue (0,150,255).")
print("[MIKASA] âœ” Auto-save now properly saves and loads Steal Duration (default 1.3).")
