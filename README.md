local StarterGui = game:GetService("StarterGui")
local Players = game:GetService("Players")

-- ************************ 移除白名單驗證，直接進入功能 ************************
local LocalPlayer = Players.LocalPlayer
while not LocalPlayer do
    Players.PlayerAdded:Wait()
    LocalPlayer = Players.LocalPlayer
end

-- 發送歡迎通知
pcall(function()
    StarterGui:SetCore("SendNotification", {
        Title = "ezzzz腳本",
        Text = string.format("歡迎 %s，正在加載腳本...", LocalPlayer.Name),
        Duration = 5,
    })
end)

warn("ezzzz已啟動，開始加載功能")

-- ************************ 以下為原腳本本地核心功能（完整保留） ************************

-- 加載 WindUI
local success, WindUI = pcall(function()
    return loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
end)

if not success or not WindUI then
    game:GetService("StarterGui"):SetCore("SendNotification",{
        Title = "錯誤",
        Text = "UI 加載失敗",
        Duration = 5;
    })
    return
end

-- 設置主題
WindUI:SetTheme("Midnight")

-- 基本通知
WindUI:Notify({
    Title = "ezzzz",
    Content = "加載中",
    Duration = 5,
})

task.wait(1)

-- 服務初始化
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local Camera = Workspace.CurrentCamera

-- 全局變量存儲
_G.ToggleStates = _G.ToggleStates or {}
local translateSpeed = 50
local translateConnection = nil
local translateAccelEnabled = false
local _G = _G or getfenv(0)._G
_G.FastAttack = _G.FastAttack ~= nil and _G.FastAttack or true

-- 創建主窗口
local Window = WindUI:CreateWindow({
    Title = "ezzzz",
    Icon = "rbxassetid://1067026253",
    Author = "Priestess",
    Folder = "WindUI",
    Size = UDim2.fromOffset(580, 460),
    Transparent = false,
    Theme = "Light",
    ScrollBarEnabled = true,
})

-- G 快捷鍵開關 UI
Window:SetToggleKey(Enum.KeyCode.G)

-- 創建所有標籤頁
local Tabs = {}

-- 創建分區
local noticeSection = Window:Section({ Title = "公告區", Opened = true })
local functionSection = Window:Section({ Title = "主要功能", Opened = true })
local configSection = Window:Section({ Title = "主題配置", Opened = true })

Tabs.NoticeTab = noticeSection:Tab({ Title = "公告", Icon = "info" })
Tabs.GeneralTab = functionSection:Tab({ Title = "功能1", Icon = "settings" })
Tabs.AttackTab = functionSection:Tab({ Title = "範圍攻擊", Icon = "sword" })
Tabs.ESPTab = functionSection:Tab({ Title = "透視", Icon = "eye" })
Tabs.TeleportTab = functionSection:Tab({ Title = "傳送", Icon = "map-pin" })
Tabs.ActivityTab = functionSection:Tab({ Title = "功能2", Icon = "zap" })
Tabs.texiaoTab = configSection:Tab({ Title = "更改特效", Icon = "palette" })
Tabs.ThemeTab = configSection:Tab({ Title = "主題顏色", Icon = "palette" })

-- ===================== 公告標籤頁 =====================
Tabs.NoticeTab:Paragraph({
    Title = "ezzzz",
    Desc = "ezzzz",
    Image = "info",
    Color = "Red"
})

Tabs.NoticeTab:Divider()

Tabs.NoticeTab:Paragraph({
    Title = "快捷鍵說明",
    Desc = "G: 開關界面",
    Image = "keyboard",
    Color = "Green"
})

-- ===================== 通用標籤頁 =====================
local currentZoom = 128
Tabs.GeneralTab:Slider({
    Title = "視角縮放距離",
    Value = { Min = 128, Max = 1000000, Default = 128 },
    Callback = function(value)
        currentZoom = value
        LocalPlayer.CameraMaxZoomDistance = value
        WindUI:Notify({ Title = "視角縮放", Content = "已設置為: " .. value, Duration = 2 })
    end
})

Tabs.GeneralTab:Divider()

Tabs.GeneralTab:Input({
    Title = "速度",
    Value = "50",
    Placeholder = "輸入速度值",
    Callback = function(value)
        local speed = tonumber(value)
        if speed then
            translateSpeed = speed
            WindUI:Notify({ Title = "速度設置", Content = "速度已設置為: " .. speed, Duration = 2 })
        end
    end
})

Tabs.GeneralTab:Toggle({
    Title = "加速開關",
    Value = false,
    Callback = function(state)
        translateAccelEnabled = state
        if translateConnection then
            translateConnection:Disconnect()
            translateConnection = nil
        end
        if state then
            translateConnection = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                if not char then return end
                local humanoid = char:FindFirstChildOfClass("Humanoid")
                if not humanoid or humanoid.Health <= 0 then return end
                if humanoid.MoveDirection.Magnitude > 0 then
                    local moveDirection = humanoid.MoveDirection
                    local acceleration = moveDirection * translateSpeed / 30
                    char:TranslateBy(acceleration)
                end
            end)
            WindUI:Notify({ Title = "加速", Content = "加速已開啟", Duration = 2 })
        else
            WindUI:Notify({ Title = "加速", Content = "加速已關閉", Duration = 2 })
        end
    end
})

Tabs.GeneralTab:Divider()

Tabs.GeneralTab:Button({
    Title = "飛行",
    Icon = "wind",
    Callback = function()
        WindUI:Notify({ Title = "飛行", Content = "正在加載飛行腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()
        end)
    end
})

Tabs.GeneralTab:Button({
    Title = "飛行（穿墙模式）",
    Icon = "wind",
    Callback = function()
        WindUI:Notify({ Title = "飛行", Content = "正在加載飛行腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/s1050031-debug/jsaj/refs/heads/main/F"))()
        end)
    end
})

Tabs.GeneralTab:Button({
    Title = "減畫質",
    Icon = "monitor",
    Callback = function()
        WindUI:Notify({ Title = "減畫質", Content = "正在加載畫質優化腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/kismile36/g1/refs/heads/main/fflags"))()
        end)
    end
})

Tabs.GeneralTab:Button({
    Title = "鎖人",
    Icon = "flame",
    Callback = function()
        WindUI:Notify({ Title = "鎖人", Content = "正在加載鎖人腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/56123asda/546546/refs/heads/main/追"))()
        end)
    end
})

Tabs.GeneralTab:Button({
    Title = "移除霧",
    Icon = "cloud-off",
    Callback = function()
        pcall(function()
            local layers = Lighting:FindFirstChild("LightingLayers")
            if layers then layers:Destroy() end
            
            local sky = Lighting:FindFirstChildOfClass("Sky")
            if sky then sky:Destroy() end
            
            for _, v in ipairs(Lighting:GetChildren()) do
                if v:IsA("Atmosphere") or v:IsA("PostEffect") then
                    v:Destroy()
                end
            end
            
            Lighting.FogStart = 0
            Lighting.FogEnd = 9e9
        end)
        WindUI:Notify({ Title = "移除霧", Content = "霧氣與大氣特效已成功移除！", Duration = 3 })
    end
})

-- 新增：移除岩漿 (直接帶入你給的第二個腳本)
Tabs.GeneralTab:Button({
    Title = "移除岩漿",
    Icon = "flame",
    Callback = function()
        local function removeLava()
            for i, v in pairs(game.Workspace:GetDescendants()) do
                if v.Name == "Lava" then
                    v:Destroy()
                end
            end
            for i, v in pairs(game.ReplicatedStorage:GetDescendants()) do
                if v.Name == "Lava" then
                    v:Destroy()
                end
            end
        end

        removeLava()

        local function removeLava()
            for i, v in pairs(game.Workspace:GetDescendants()) do
                if v.Name == "LavaParts" and v.Parent and v.Parent.Name == "CircleIsland" 
                    and v.Parent.Parent and v.Parent.Parent.Name == "Map" then
                    v:Destroy()
                end
            end

            for i, v in pairs(game.ReplicatedStorage:GetDescendants()) do
                if v.Name == "LavaParts" and v.Parent and v.Parent.Name == "CircleIsland" 
                    and v.Parent.Parent and v.Parent.Parent.Name == "Map" then
                    v:Destroy()
                end
            end
        end

        removeLava()
        WindUI:Notify({ Title = "移除岩漿", Content = "岩漿判定已移除！", Duration = 3 })
    end
})

-- ===================== 範圍攻擊標籤頁 =====================
local _ENV = (getgenv or getrenv or getfenv)()
local Settings = {AutoClick = true, ClickDelay = 0.3}
local AutoHakiEnabled = false
local autoHakiTask = nil

-- Auto Haki函數
local function startAutoHakiLoop()
    if autoHakiTask then
        task.cancel(autoHakiTask)
        autoHakiTask = nil
    end
    autoHakiTask = task.spawn(function()
        while AutoHakiEnabled do
            task.wait(0.2)
            if AutoHakiEnabled and LocalPlayer.Character and not LocalPlayer.Character:FindFirstChild("HasBuso") then
                pcall(function()
                    ReplicatedStorage.Remotes.CommF_:InvokeServer("Buso")
                end)
            end
        end
        autoHakiTask = nil
    end)
end

LocalPlayer.CharacterAdded:Connect(function(newCharacter)
    if AutoHakiEnabled then
        task.wait(2)
        startAutoHakiLoop()
    end
end)

-- 快速攻擊模塊
local FastAttackModule = (function()
    if _ENV.rz_FastAttack then return _ENV.rz_FastAttack end
    
    local FastAttack = {
        Distance = 3000,
        attackMobs = true,
        attackPlayers = true,
        Equipped = nil,
        IsRunning = _G.FastAttack,
        consecutiveFailures = 0,
        maxConsecutiveFailures = 5
    }

    local function ProcessEnemies(OthersEnemies, Folder)
        if not Folder or not FastAttack.attackMobs then return nil end
        local BasePart = nil
        for _, Enemy in Folder:GetChildren() do
            if Enemy == LocalPlayer.Character then continue end
            local humanoid = Enemy:FindFirstChildOfClass("Humanoid")
            if not humanoid or humanoid.Health <= 0 then continue end
            local foundPart = Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildWhichIsA("BasePart")
            if foundPart and LocalPlayer:DistanceFromCharacter(foundPart.Position) < FastAttack.Distance then
                table.insert(OthersEnemies, {Enemy, foundPart})
                BasePart = foundPart
            end
        end
        return BasePart
    end

    local function ProcessRealPlayers(OthersEnemies)
        if not FastAttack.attackPlayers then return nil end
        local BasePart = nil
        for _, OtherPlayer in Players:GetPlayers() do
            if OtherPlayer == LocalPlayer then continue end
            local OtherChar = OtherPlayer.Character
            if not OtherChar then continue end
            local humanoid = OtherChar:FindFirstChildOfClass("Humanoid")
            if not humanoid or humanoid.Health <= 0 then continue end
            local foundPart = OtherChar:FindFirstChild("HumanoidRootPart") or OtherChar:FindFirstChildWhichIsA("BasePart")
            if foundPart and LocalPlayer:DistanceFromCharacter(foundPart.Position) < FastAttack.Distance then
                table.insert(OthersEnemies, {OtherChar, foundPart})
                BasePart = foundPart
            end
        end
        return BasePart
    end

    function FastAttack:AttackNearest()
        if not self.IsRunning then return end
        local OthersEnemies = {}
        local Enemies = Workspace:FindFirstChild("Enemies")
        local Part1 = Enemies and ProcessEnemies(OthersEnemies, Enemies)
        local Part2 = ProcessRealPlayers(OthersEnemies)
        
        if #OthersEnemies > 0 then
            local targetPart = Part1 or Part2
            if targetPart then
                local Remotes = ReplicatedStorage:FindFirstChild("Remotes")
                if Remotes then
                    local Modules = ReplicatedStorage:FindFirstChild("Modules")
                    local Net = Modules and Modules:FindFirstChild("Net")
                    local RegisterAttack = Net and Net:FindFirstChild("RE/RegisterAttack")
                    local RegisterHit = Net and Net:FindFirstChild("RE/RegisterHit")
                    if RegisterAttack and RegisterHit then
                        RegisterAttack:FireServer(Settings.ClickDelay or 0)
                        RegisterHit:FireServer(targetPart, OthersEnemies)
                    end
                end
            end
        end
    end

    function FastAttack:BladeHits()
        if not self.IsRunning then return end
        local Equipped = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Tool")
        if Equipped then
            self:AttackNearest()
        end
    end

    task.spawn(function()
        while true do
            task.wait(Settings.ClickDelay)
            if Settings.AutoClick and FastAttack.IsRunning then
                FastAttack:BladeHits()
            else
                task.wait(0.1)
            end
        end
    end)

    _ENV.rz_FastAttack = FastAttack
    return FastAttack
end)()

-- 攻擊開關
Tabs.AttackTab:Toggle({
    Title = "攻擊開關",
    Value = _G.FastAttack,
    Callback = function(state)
        if _ENV.rz_FastAttack then
            _ENV.rz_FastAttack.IsRunning = state
            _G.FastAttack = state
            WindUI:Notify({ Title = "快速攻擊", Content = state and "已開啟" or "已關閉", Duration = 2 })
        end
    end
})

Tabs.AttackTab:Input({
    Title = "攻擊範圍",
    Value = "3000",
    Placeholder = "輸入攻擊範圍",
    Callback = function(text)
        local num = tonumber(text) or 3000
        num = math.floor(math.clamp(num, 1, 3000))
        if _ENV.rz_FastAttack then
            _ENV.rz_FastAttack.Distance = num
            WindUI:Notify({ Title = "攻擊範圍", Content = "已設置為: " .. num, Duration = 2 })
        end
    end
})

Tabs.AttackTab:Input({
    Title = "攻擊速度",
    Value = "0.3",
    Placeholder = "輸入攻擊速度",
    Callback = function(text)
        local num = tonumber(text) or 0.3
        num = math.round(math.clamp(num, 0.05, 2) * 100) / 100
        Settings.ClickDelay = num
        WindUI:Notify({ Title = "攻擊速度", Content = "已設置為: " .. num, Duration = 2 })
    end
})

Tabs.AttackTab:Toggle({
    Title = "攻擊怪物",
    Value = true,
    Callback = function(state)
        if _ENV.rz_FastAttack then
            _ENV.rz_FastAttack.attackMobs = state
            WindUI:Notify({ Title = "攻擊目標", Content = state and "攻擊怪物: 開啟" or "攻擊怪物: 關閉", Duration = 2 })
        end
    end
})

Tabs.AttackTab:Toggle({
    Title = "攻擊玩家",
    Value = true,
    Callback = function(state)
        if _ENV.rz_FastAttack then
            _ENV.rz_FastAttack.attackPlayers = state
            WindUI:Notify({ Title = "攻擊目標", Content = state and "攻擊玩家: 開啟" or "攻擊玩家: 關閉", Duration = 2 })
        end
    end
})

Tabs.AttackTab:Divider()

-- 自動V4
local autoV4Task = nil
local function callAwakeningRemote()
    local character = LocalPlayer.Character
    if not character then return end
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    if not humanoid or humanoid.Health <= 0 then return end
    local Backpack = LocalPlayer:FindFirstChild("Backpack")
    if not Backpack then return end
    local Awakening = Backpack:FindFirstChild("Awakening")
    if not Awakening then return end
    local RemoteFunc = Awakening:FindFirstChild("RemoteFunction")
    if not RemoteFunc then return end
    pcall(function()
        RemoteFunc:InvokeServer(true)
    end)
end

Tabs.AttackTab:Toggle({
    Title = "自動V4",
    Value = false,
    Callback = function(state)
        _G.ToggleStates["自動V4"] = state
        if autoV4Task then
            task.cancel(autoV4Task)
            autoV4Task = nil
        end
        if state then
            autoV4Task = task.spawn(function()
                while _G.ToggleStates["自動V4"] do
                    callAwakeningRemote()
                    task.wait(1)
                end
                autoV4Task = nil
            end)
            WindUI:Notify({ Title = "自動V4", Content = "已開啟", Duration = 2 })
        else
            WindUI:Notify({ Title = "自動V4", Content = "已關閉", Duration = 2 })
        end
    end
})

-- 自動V3
local autoV3Task = nil
local function callRaceV3Remote()
    local Remotes = ReplicatedStorage:FindFirstChild("Remotes")
    if not Remotes then return end
    local CommE = Remotes:FindFirstChild("CommE")
    if not CommE then return end
    pcall(function()
        CommE:FireServer("ActivateAbility")
    end)
end

Tabs.AttackTab:Toggle({
    Title = "自動V3",
    Value = false,
    Callback = function(state)
        _G.ToggleStates["自動V3"] = state
        if autoV3Task then
            task.cancel(autoV3Task)
            autoV3Task = nil
        end
        if state then
            autoV3Task = task.spawn(function()
                while _G.ToggleStates["自動V3"] do
                    callRaceV3Remote()
                    task.wait(1)
                end
                autoV3Task = nil
            end)
            WindUI:Notify({ Title = "自動V3", Content = "已開啟", Duration = 2 })
        else
            WindUI:Notify({ Title = "自動V3", Content = "已關閉", Duration = 2 })
        end
    end
})

Tabs.AttackTab:Divider()

-- 自動武裝色
Tabs.AttackTab:Toggle({
    Title = "自動武裝色",
    Value = true,
    Callback = function(state)
        AutoHakiEnabled = state
        if state then
            startAutoHakiLoop()
            WindUI:Notify({ Title = "自動武裝色", Content = "已開啟", Duration = 2 })
        else
            if autoHakiTask then
                task.cancel(autoHakiTask)
                autoHakiTask = nil
            end
            WindUI:Notify({ Title = "自動武裝色", Content = "已關閉", Duration = 2 })
        end
    end
})

-- ===================== ESP透視標籤頁 =====================
local espEnabled = false
local espObjects = {}
local RandomID = math.random(1, 1000000)

local function Round(num)
    return math.floor(tonumber(num) + 0.5)
end

local function getTeamColor(player)
    if player.Team ~= LocalPlayer.Team then
        return Color3.fromRGB(255, 0, 0)
    else
        return Color3.fromRGB(0, 0, 255)
    end
end

local function removeESP(player)
    if espObjects[player] then
        for _, v in pairs(espObjects[player]) do
            if typeof(v) == "Instance" and v.Parent then
                v:Destroy()
            end
        end
        espObjects[player] = nil
    end
end

local function createESP(player)
    if player == LocalPlayer then return end
    if not player.Character then return end
    
    removeESP(player)
    
    local head = player.Character:FindFirstChild("Head")
    if not head then return end

    local highlight = Instance.new("Highlight")
    highlight.Adornee = player.Character
    highlight.FillTransparency = 0.7
    highlight.OutlineColor = getTeamColor(player)
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    highlight.Parent = player.Character

    local billboard = Instance.new("BillboardGui")
    billboard.Name = "NameESP_" .. RandomID
    billboard.Adornee = head
    billboard.Size = UDim2.new(0, 200, 0, 50)
    billboard.AlwaysOnTop = true
    billboard.StudsOffset = Vector3.new(0, 2.5, 0)
    billboard.Parent = head

    local avatar = Instance.new("ImageLabel")
    avatar.Size = UDim2.new(0, 45, 0, 45)
    avatar.Position = UDim2.new(0, 0, 0, 0)
    avatar.BackgroundTransparency = 1
    avatar.BorderSizePixel = 0
    avatar.Image = "https://www.roblox.com/headshot-thumbnail/image?userId="..player.UserId.."&width=150&height=150&format=png"
    avatar.Parent = billboard

    local text = Instance.new("TextLabel")
    text.Size = UDim2.new(1, -50, 1, 0)
    text.Position = UDim2.new(0, 50, 0, 0)
    text.BackgroundTransparency = 1
    text.TextStrokeTransparency = 0.5
    text.TextScaled = true
    text.Font = Enum.Font.Code
    text.TextYAlignment = Enum.TextYAlignment.Top
    text.TextXAlignment = Enum.TextXAlignment.Left
    text.TextColor3 = getTeamColor(player)
    text.Parent = billboard

    espObjects[player] = { highlight = highlight, billboard = billboard, text = text, avatar = avatar }

    task.spawn(function()
        while espEnabled and player and player.Parent and espObjects[player] do
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local root = player.Character.HumanoidRootPart
                local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if root and localRoot then
                    local distance = Round((root.Position - localRoot.Position).Magnitude)
                    local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                    local healthPercent = humanoid and Round((humanoid.Health / humanoid.MaxHealth) * 100) or 0
                    if espObjects[player] and espObjects[player].text then
                        espObjects[player].text.Text = string.format("%s | %d M\nHealth: %d%%", player.Name, distance, healthPercent)
                    end
                end
            else
                break
            end
            task.wait(0.2)
        end
    end)
end

local function setupPlayerConnections(player)
    player.CharacterAdded:Connect(function(character)
        if espEnabled then
            task.wait(0.5)
            createESP(player)
        end
    end)
    player.CharacterRemoving:Connect(function()
        removeESP(player)
    end)
end

local function enableESPPro()
    espEnabled = true
    for _, p in pairs(Players:GetPlayers()) do
        setupPlayerConnections(p)
        if p.Character then
            task.wait(0.1)
            createESP(p)
        end
    end
    WindUI:Notify({ Title = "ESP Pro", Content = "已啟用ESP", Duration = 2 })
end

local function disableESPPro()
    espEnabled = false
    for player, _ in pairs(espObjects) do
        removeESP(player)
    end
    espObjects = {}
    WindUI:Notify({ Title = "ESP Pro", Content = "已禁用ESP", Duration = 2 })
end

Tabs.ESPTab:Toggle({
    Title = "ESP Pro",
    Value = false,
    Callback = function(state)
        if state then
            enableESPPro()
        else
            disableESPPro()
        end
    end
})

Players.PlayerAdded:Connect(function(player)
    setupPlayerConnections(player)
    if espEnabled and player.Character then
        task.wait(1)
        createESP(player)
    end
end)

Players.PlayerRemoving:Connect(function(player)
    removeESP(player)
end)

for _, p in pairs(Players:GetPlayers()) do
    setupPlayerConnections(p)
end

-- ===================== 傳送標籤頁 =====================
Tabs.TeleportTab:Button({
    Title = "傳送至一海",
    Callback = function()
        pcall(function()
            ReplicatedStorage.Remotes.CommF_:InvokeServer("TravelMain")
            WindUI:Notify({ Title = "傳送", Content = "正在傳送至一海", Duration = 2 })
        end)
    end
})

Tabs.TeleportTab:Button({
    Title = "傳送至二海",
    Callback = function()
        pcall(function()
            ReplicatedStorage.Remotes.CommF_:InvokeServer("TravelDressrosa")
            WindUI:Notify({ Title = "傳送", Content = "正在傳送至二海", Duration = 2 })
        end)
    end
})

Tabs.TeleportTab:Button({
    Title = "傳送至三海",
    Callback = function()
        pcall(function()
            ReplicatedStorage.Remotes.CommF_:InvokeServer("TravelZou")
            WindUI:Notify({ Title = "傳送", Content = "正在傳送至三海", Duration = 2 })
        end)
    end
})

Tabs.TeleportTab:Divider()

-- 二海傳送點
local sea2Locations = {
    ["天鵝的房間"] = CFrame.new(-287.37, 305.81, 592.98),
    ["豪宅"] = CFrame.new(2286.93, 15.06, 910.51),
    ["鬼船裡"] = CFrame.new(-6501.06, 83.11, -123.52),
    ["鬼船外"] = CFrame.new(922.78, 123.96, 32842.40)
}

for name, cf in pairs(sea2Locations) do
    Tabs.TeleportTab:Button({
        Title = "傳送至" .. name,
        Callback = function()
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                char.HumanoidRootPart.CFrame = cf
                WindUI:Notify({ Title = "傳送", Content = "已傳送至" .. name, Duration = 2 })
            end
        end
    })
end

Tabs.TeleportTab:Divider()

-- 三海傳送點
local sea3Locations = {
    ["海洋城堡"] = CFrame.new(-12463.60, 376.26, -7566.08),
    ["海龜豪宅"] = CFrame.new(-5060.41, 316.43, -3192.30),
    ["司法"] = CFrame.new(-5096.48, 316.43, -3177.91),
    ["九頭蛇"] = CFrame.new(-5027.03, 316.43, -3206.07)
}

for name, cf in pairs(sea3Locations) do
    Tabs.TeleportTab:Button({
        Title = "傳送至" .. name,
        Callback = function()
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                char.HumanoidRootPart.CFrame = cf
                WindUI:Notify({ Title = "傳送", Content = "已傳送至" .. name, Duration = 2 })
            end
        end
    })
end

-- ===================== 功能2標籤頁 =====================
local AUTO_BUDDHA_ENABLED = false
local CHECK_INTERVAL = 5
local BUDDHA_SLOT = 2
local SWORD_SLOT = 3
local BUDDHA_MIN_SIZE = 20.0

local ALL_SWORDS = {
    "True Triple Katana", "Hallow Scythe", "Dark Blade", "Cursed Dual Katana",
    "Rengoku", "Saber", "Saishi", "Shark Anchor", "Spikey Trident",
    "Tushita", "Yama", "Dragonheart"
}

local SWORDS_TABLE = {}
for _, sword in ipairs(ALL_SWORDS) do
    SWORDS_TABLE[sword] = true
end

local function isBuddhaForm()
    local character = LocalPlayer.Character
    if not character then return false end
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return false end
    local sizeMagnitude = humanoidRootPart.Size.Magnitude
    return sizeMagnitude > BUDDHA_MIN_SIZE
end

local function pressNumberKey(number)
    local virtualInput = game:GetService("VirtualInputManager")
    local keyCode
    if number == 1 then keyCode = Enum.KeyCode.One
    elseif number == 2 then keyCode = Enum.KeyCode.Two
    elseif number == 3 then keyCode = Enum.KeyCode.Three
    elseif number == 4 then keyCode = Enum.KeyCode.Four
    elseif number == 5 then keyCode = Enum.KeyCode.Five
    elseif number == 6 then keyCode = Enum.KeyCode.Six
    elseif number == 7 then keyCode = Enum.KeyCode.Seven
    elseif number == 8 then keyCode = Enum.KeyCode.Eight
    else keyCode = Enum.KeyCode.Nine end
    virtualInput:SendKeyEvent(true, keyCode, false, nil)
    task.wait(0.05)
    virtualInput:SendKeyEvent(false, keyCode, false, nil)
end

local function pressZKey()
    local virtualInput = game:GetService("VirtualInputManager")
    virtualInput:SendKeyEvent(true, Enum.KeyCode.Z, false, nil)
    task.wait(0.1)
    virtualInput:SendKeyEvent(false, Enum.KeyCode.Z, false, nil)
end

local function hasAnySwordEquipped()
    local character = LocalPlayer.Character
    if not character then return false, nil end
    for _, child in ipairs(character:GetChildren()) do
        if child:IsA("Tool") and SWORDS_TABLE[child.Name] then
            return true, child.Name
        end
    end
    return false, nil
end

local function equipSword()
    pressNumberKey(SWORD_SLOT)
    task.wait(0.3)
    local hasSword, swordName = hasAnySwordEquipped()
    if hasSword then
        return true, swordName
    end
    task.wait(0.5)
    return hasAnySwordEquipped()
end

local function performBuddhaTransformation()
    local character = LocalPlayer.Character
    if not character then return false end
    pressNumberKey(BUDDHA_SLOT)
    task.wait(0.5)
    pressZKey()
    local waitTime = 0
    local maxWaitTime = 3
    while waitTime < maxWaitTime do
        task.wait(0.1)
        waitTime = waitTime + 0.1
        if isBuddhaForm() then
            return true
        end
    end
    return false
end

local buddhaTask = nil
local function startBuddhaLoop()
    if buddhaTask then
        task.cancel(buddhaTask)
        buddhaTask = nil
    end
    buddhaTask = task.spawn(function()
        local lastCheckTime = 0
        while AUTO_BUDDHA_ENABLED do
            task.wait(1)
            local currentTime = tick()
            if currentTime - lastCheckTime >= CHECK_INTERVAL then
                lastCheckTime = currentTime
                if not LocalPlayer.Character then
                    -- 等待角色
                else
                    if isBuddhaForm() then
                        local hasSword, swordName = hasAnySwordEquipped()
                        if not hasSword then
                            equipSword()
                        end
                    else
                        local success = performBuddhaTransformation()
                        if success then
                            task.wait(0.5)
                            local hasSword, swordName = hasAnySwordEquipped()
                            if not hasSword then
                                equipSword()
                            end
                        end
                    end
                end
            end
        end
        buddhaTask = nil
    end)
end

Tabs.ActivityTab:Toggle({
    Title = "自動變身大佛＋拿刀",
    Value = false,
    Callback = function(state)
        AUTO_BUDDHA_ENABLED = state
        if state then
            startBuddhaLoop()
            WindUI:Notify({ Title = "自動變佛＋拿刀", Content = "功能已開啟", Duration = 2 })
        else
            if buddhaTask then
                task.cancel(buddhaTask)
                buddhaTask = nil
            end
            WindUI:Notify({ Title = "自動變佛＋拿刀", Content = "功能已關閉", Duration = 2 })
        end
    end
})

LocalPlayer.CharacterAdded:Connect(function()
    if AUTO_BUDDHA_ENABLED then
        task.wait(2)
        startBuddhaLoop()
    end
end)

Tabs.ActivityTab:Divider()

Tabs.ActivityTab:Button({
    Title = "aimbot",
    Callback = function()
        WindUI:Notify({ Title = "Aim Skill", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://gist.githubusercontent.com/kismile36/e6ada131f67fcc3a5793eb81974511c8/raw/ac2e04753b0c4e954b05bc554f7fe42a14b13615/AIMBot"))()
        end)
    end
})

Tabs.ActivityTab:Button({
    Title = "果實m1",
    Callback = function()
        WindUI:Notify({ Title = "New Attack", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://gist.github.com/kismile36/e6ada131f67fcc3a5793eb81974511c8/raw/49fd45ac4b1164135fb81de2fda6231af55cb436/flash%2520attack"))()
        end)
    end
})

-- 特效顏色更換 
Tabs.texiaoTab:Paragraph({
    Title = "特效更換",
    Desc = "只有你自己能看到",
    Image = "info",
    Color = "Red"
})

Tabs.texiaoTab:Button({
    Title = "白色",
    Callback = function()
        WindUI:Notify({ Title = "白色", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/bai"))()
        end)
    end
})

Tabs.texiaoTab:Button({
    Title = "藍色",
    Callback = function()
        WindUI:Notify({ Title = "藍色", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/%E8%93%9D"))()
        end)
    end
})

Tabs.texiaoTab:Button({
    Title = "紅色",
    Callback = function()
        WindUI:Notify({ Title = "紅色", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/%E7%BA%A2"))()
        end)
    end
})

Tabs.texiaoTab:Button({
    Title = "綠色",
    Callback = function()
        WindUI:Notify({ Title = "綠色", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/%E7%BB%BF%E8%89%B2"))()
        end)
    end
})

Tabs.texiaoTab:Button({
    Title = "七彩炫光",
    Callback = function()
        WindUI:Notify({ Title = "七彩炫光", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/%E4%B8%83%E5%BD%A9%E7%82%AB%E5%85%89"))()
        end)
    end
})

Tabs.texiaoTab:Button({
    Title = "地獄火",
    Callback = function()
        WindUI:Notify({ Title = "地獄火", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/%E5%9C%B0%E7%8B%B1%E7%81%AB"))()
        end)
    end
})

Tabs.texiaoTab:Button({
    Title = "未來科技（稍微卡頓）",
    Callback = function()
        WindUI:Notify({ Title = "未來科技", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/%E6%9C%AA%E6%9D%A5%E7%A7%91%E6%8A%80"))()
        end)
    end
})

Tabs.texiaoTab:Button({
    Title = "生化危機",
    Callback = function()
        WindUI:Notify({ Title = "生化危機", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/%E7%94%9F%E5%8C%96%E5%8D%B1%E6%9C%BA"))()
        end)
    end
})

Tabs.texiaoTab:Button({
    Title = "電磁脈衝",
    Callback = function()
        WindUI:Notify({ Title = "電磁脈衝", Content = "正在加載腳本...", Duration = 3 })
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/ttker951-dot/col/refs/heads/main/%E9%9B%BB%E7%A3%81%E8%84%88%E8%A1%9D"))()
        end)
    end
})

Window:SelectTab(1)
