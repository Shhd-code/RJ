local TweenService = game:GetService("TweenService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- حذف الواجهة القديمة إن وجدت
if PlayerGui:FindFirstChild("GreenServerBrowserUI") then
    PlayerGui.GreenServerBrowserUI:Destroy()
end

-- ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "GreenServerBrowserUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

-- Main Frame (الواجهة الرئيسية الخضراء الشفافة)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 440, 0, 340)
MainFrame.Position = UDim2.new(0.5, -220, 0.5, -170)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 25, 15)
MainFrame.BackgroundTransparency = 0.2
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainUICorner = Instance.new("UICorner")
MainUICorner.CornerRadius = UDim.new(0, 16)
MainUICorner.Parent = MainFrame

local MainUIStroke = Instance.new("UIStroke")
MainUIStroke.Color = Color3.fromRGB(46, 204, 113)
MainUIStroke.Thickness = 2
MainUIStroke.Parent = MainFrame

-- Title
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Name = "TitleLabel"
TitleLabel.Size = UDim2.new(1, -70, 0, 40)
TitleLabel.Position = UDim2.new(0, 15, 0, 5)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "🌐 قائمة السيرفرات"
TitleLabel.TextColor3 = Color3.fromRGB(240, 255, 245)
TitleLabel.TextSize = 16
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = MainFrame

-- Refresh Button (زر التحديث اليدوي)
local RefreshBtn = Instance.new("TextButton")
RefreshBtn.Name = "RefreshBtn"
RefreshBtn.Size = UDim2.new(0, 35, 0, 30)
RefreshBtn.Position = UDim2.new(1, -45, 0, 10)
RefreshBtn.BackgroundColor3 = Color3.fromRGB(20, 55, 30)
RefreshBtn.Text = "🔄"
RefreshBtn.TextSize = 15
RefreshBtn.Parent = MainFrame

local RefreshCorner = Instance.new("UICorner")
RefreshCorner.CornerRadius = UDim.new(0, 8)
RefreshCorner.Parent = RefreshBtn

local RefreshStroke = Instance.new("UIStroke")
RefreshStroke.Color = Color3.fromRGB(46, 204, 113)
RefreshStroke.Thickness = 1
RefreshStroke.Parent = RefreshBtn

-- Scroll Frame
local ScrollFrame = Instance.new("ScrollingFrame")
ScrollFrame.Name = "ServerScroll"
ScrollFrame.Size = UDim2.new(1, -20, 1, -55)
ScrollFrame.Position = UDim2.new(0, 10, 0, 45)
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.BorderSizePixel = 0
ScrollFrame.ScrollBarThickness = 4
ScrollFrame.ScrollBarImageColor3 = Color3.fromRGB(46, 204, 113)
ScrollFrame.Parent = MainFrame

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = ScrollFrame
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 8)

UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
end)

-- Toggle Mini Button (🔁 الدائرة الميني لإخفاء وإظهار الواجهة)
local ToggleButton = Instance.new("TextButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 50, 0, 50)
ToggleButton.Position = UDim2.new(0.02, 0, 0.45, 0)
ToggleButton.BackgroundColor3 = Color3.fromRGB(15, 45, 25)
ToggleButton.BackgroundTransparency = 0.15
ToggleButton.Text = "🔁"
ToggleButton.TextSize = 24
ToggleButton.Active = true
ToggleButton.Draggable = true
ToggleButton.Parent = ScreenGui

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(1, 0)
ToggleCorner.Parent = ToggleButton

local ToggleStroke = Instance.new("UIStroke")
ToggleStroke.Color = Color3.fromRGB(46, 204, 113)
ToggleStroke.Thickness = 2.5
ToggleStroke.Parent = ToggleButton

local isVisible = true
ToggleButton.MouseButton1Click:Connect(function()
    isVisible = not isVisible
    MainFrame.Visible = isVisible
end)

-- وظيفة إنشاء بطاقة السيرفر والتحقق من الامتلاء
local function createServerCard(serverId, serverName, playerCount, maxPlayers)
    local isFull = false
    
    local numCount = tonumber(playerCount)
    local numMax = tonumber(maxPlayers)
    if numCount and numMax and numCount >= numMax then
        isFull = true
    end

    local Card = Instance.new("Frame")
    Card.Name = "ServerCard"
    Card.Size = UDim2.new(1, -8, 0, 52)
    Card.BackgroundColor3 = Color3.fromRGB(18, 45, 26)
    Card.BackgroundTransparency = 0.3
    Card.BorderSizePixel = 0
    Card.Parent = ScrollFrame

    local CardCorner = Instance.new("UICorner")
    CardCorner.CornerRadius = UDim.new(0, 10)
    CardCorner.Parent = Card

    local CardStroke = Instance.new("UIStroke")
    CardStroke.Color = isFull and Color3.fromRGB(231, 76, 60) or Color3.fromRGB(46, 204, 113)
    CardStroke.Thickness = 1
    CardStroke.Transparency = 0.6
    CardStroke.Parent = Card

    -- اسم السيرفر
    local NameLabel = Instance.new("TextLabel")
    NameLabel.Size = UDim2.new(0.6, 0, 0.5, 0)
    NameLabel.Position = UDim2.new(0, 12, 0, 5)
    NameLabel.BackgroundTransparency = 1
    NameLabel.Text = serverName
    NameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    NameLabel.TextSize = 13
    NameLabel.Font = Enum.Font.GothamBold
    NameLabel.TextXAlignment = Enum.TextXAlignment.Left
    NameLabel.Parent = Card

    -- عدد اللاعبين
    local CountLabel = Instance.new("TextLabel")
    CountLabel.Size = UDim2.new(0.6, 0, 0.4, 0)
    CountLabel.Position = UDim2.new(0, 12, 0.5, 0)
    CountLabel.BackgroundTransparency = 1
    CountLabel.Text = "👥 اللاعبين: " .. tostring(playerCount) .. (maxPlayers and ("/" .. tostring(maxPlayers)) or "")
    CountLabel.TextColor3 = isFull and Color3.fromRGB(235, 120, 120) or Color3.fromRGB(160, 220, 180)
    CountLabel.TextSize = 11
    CountLabel.Font = Enum.Font.Gotham
    CountLabel.TextXAlignment = Enum.TextXAlignment.Left
    CountLabel.Parent = Card

    -- زر الدخول أو التنبيه بالامتلاء
    local JoinBtn = Instance.new("TextButton")
    JoinBtn.Size = UDim2.new(0.28, 0, 0.65, 0)
    JoinBtn.Position = UDim2.new(0.69, 0, 0.175, 0)
    
    if isFull then
        JoinBtn.BackgroundColor3 = Color3.fromRGB(120, 40, 40)
        JoinBtn.Text = "ممتلئ"
        JoinBtn.TextColor3 = Color3.fromRGB(255, 200, 200)
    else
        JoinBtn.BackgroundColor3 = Color3.fromRGB(35, 150, 85)
        JoinBtn.Text = "دخول ➔"
        JoinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    end
    
    JoinBtn.TextSize = 12
    JoinBtn.Font = Enum.Font.GothamBold
    JoinBtn.Parent = Card

    local BtnCorner = Instance.new("UICorner")
    BtnCorner.CornerRadius = UDim.new(0, 8)
    BtnCorner.Parent = JoinBtn

    local BtnStroke = Instance.new("UIStroke")
    BtnStroke.Color = isFull and Color3.fromRGB(231, 76, 60) or Color3.fromRGB(72, 230, 140)
    BtnStroke.Thickness = 1.5
    BtnStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    BtnStroke.Parent = JoinBtn

    if not isFull then
        JoinBtn.MouseEnter:Connect(function()
            JoinBtn.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
        end)
        JoinBtn.MouseLeave:Connect(function()
            JoinBtn.BackgroundColor3 = Color3.fromRGB(35, 150, 85)
        end)

        JoinBtn.MouseButton1Click:Connect(function()
            pcall(function()
                ReplicatedStorage.ServerBrowserRemotes.JoinServer:FireServer(serverId)
            end)
        end)
    end
end

-- وظيفة جلب البيانات عند الطلب
local function fetchServers()
    for _, item in ipairs(ScrollFrame:GetChildren()) do
        if item:IsA("Frame") then
            item:Destroy()
        end
    end

    local serverRemote = ReplicatedStorage:FindFirstChild("ServerBrowserRemotes") and ReplicatedStorage.ServerBrowserRemotes:FindFirstChild("GetServerList")
    if not serverRemote then return end

    local rawData = nil
    pcall(function() rawData = serverRemote:InvokeServer() end)
    if not rawData then
        pcall(function() rawData = serverRemote:InvokeServer("All") end)
    end

    local count = 0

    if rawData and type(rawData) == "table" then
        for k, v in pairs(rawData) do
            count = count + 1
            if type(v) == "table" then
                local sId = v.UUID or v.JobId or v.Id or v.ServerId or v[1] or k
                local sName = v.Name or v.Title or v.ServerName or ("سيرفر #" .. tostring(count))
                
                local pCount = v.PlayersCount or v.PlayerCount or v.Players or v.CurrentPlayers or (v.PlayersList and #v.PlayersList)
                if not pCount and type(v.Players) == "table" then
                    pCount = #v.Players
                end
                pCount = pCount or 0

                local maxP = v.MaxPlayers or v.Max or v.Capacity or 12
                createServerCard(sId, sName, pCount, maxP)
            else
                createServerCard(v, "سيرفر #" .. tostring(count), 1, 12)
            end
        end
    end

    if count == 0 then
        -- سيرفر افتراضي تجريبي
        createServerCard("d90dbb60-8a38-48cc-a6cd-e75af8aabbf5", "سيرفر خارجي (تلقائي)", 12, 12)
    end
end

-- التحديث عند ضغط الزر (🔄) فقط
RefreshBtn.MouseButton1Click:Connect(fetchServers)

-- جلب السيرفرات لأول مرة عند فتح السكربت
fetchServers()
