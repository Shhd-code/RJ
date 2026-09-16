local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- حذف الواجهة القديمة إن وجدت لتفادي التكرار
if PlayerGui:FindFirstChild("LogEntryExitUI") then
    PlayerGui.LogEntryExitUI:Destroy()
end

-- ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LogEntryExitUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

-- Main Frame (الواجهة الرئيسية بحدود خضراء حيّة)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 470, 0, 360)
MainFrame.Position = UDim2.new(0.5, -235, 0.5, -180)
MainFrame.BackgroundColor3 = Color3.fromRGB(8, 20, 12)
MainFrame.BackgroundTransparency = 0.1
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainUICorner = Instance.new("UICorner")
MainUICorner.CornerRadius = UDim.new(0, 16)
MainUICorner.Parent = MainFrame

local MainUIStroke = Instance.new("UIStroke")
MainUIStroke.Color = Color3.fromRGB(0, 255, 127) -- أخضر زمردي حي
MainUIStroke.Thickness = 2
MainUIStroke.Parent = MainFrame

-- Header Area
local HeaderFrame = Instance.new("Frame")
HeaderFrame.Size = UDim2.new(1, 0, 0, 45)
HeaderFrame.BackgroundTransparency = 1
HeaderFrame.Parent = MainFrame

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Name = "TitleLabel"
TitleLabel.Size = UDim2.new(1, -70, 1, 0)
TitleLabel.Position = UDim2.new(0, 15, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "لوق خروج ودخول"
TitleLabel.TextColor3 = Color3.fromRGB(0, 255, 180)
TitleLabel.TextSize = 18
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = HeaderFrame

-- Refresh Button (زر التحديث)
local RefreshBtn = Instance.new("TextButton")
RefreshBtn.Name = "RefreshBtn"
RefreshBtn.Size = UDim2.new(0, 55, 0, 28)
RefreshBtn.Position = UDim2.new(1, -65, 0.5, -14)
RefreshBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 100)
RefreshBtn.Text = "تحديث"
RefreshBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
RefreshBtn.Font = Enum.Font.GothamBold
RefreshBtn.TextSize = 12
RefreshBtn.Parent = HeaderFrame

local RefreshCorner = Instance.new("UICorner")
RefreshCorner.CornerRadius = UDim.new(0, 8)
RefreshCorner.Parent = RefreshBtn

local RefreshStroke = Instance.new("UIStroke")
RefreshStroke.Color = Color3.fromRGB(50, 255, 150)
RefreshStroke.Thickness = 1.5
RefreshStroke.Parent = RefreshBtn

-- Line Separator
local Line = Instance.new("Frame")
Line.Size = UDim2.new(1, -30, 0, 1)
Line.Position = UDim2.new(0, 15, 0, 45)
Line.BackgroundColor3 = Color3.fromRGB(0, 255, 127)
Line.BackgroundTransparency = 0.5
Line.BorderSizePixel = 0
Line.Parent = MainFrame

-- Scroll Frame (قائمة اللاعبين)
local ScrollFrame = Instance.new("ScrollingFrame")
ScrollFrame.Name = "LogScroll"
ScrollFrame.Size = UDim2.new(1, -20, 1, -60)
ScrollFrame.Position = UDim2.new(0, 10, 0, 52)
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.BorderSizePixel = 0
ScrollFrame.ScrollBarThickness = 5
ScrollFrame.ScrollBarImageColor3 = Color3.fromRGB(0, 255, 127)
ScrollFrame.Parent = MainFrame

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = ScrollFrame
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 8)

UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
end)

-- Toggle Mini Button (الدائرة الميني بايموجي العين 👁️)
local ToggleButton = Instance.new("TextButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 52, 0, 52)
ToggleButton.Position = UDim2.new(0.02, 0, 0.45, 0)
ToggleButton.BackgroundColor3 = Color3.fromRGB(10, 35, 20)
ToggleButton.BackgroundTransparency = 0.1
ToggleButton.Text = "👁️"
ToggleButton.TextSize = 22
ToggleButton.Active = true
ToggleButton.Draggable = true
ToggleButton.Parent = ScreenGui

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(1, 0)
ToggleCorner.Parent = ToggleButton

local ToggleStroke = Instance.new("UIStroke")
ToggleStroke.Color = Color3.fromRGB(0, 255, 127)
ToggleStroke.Thickness = 2.5
ToggleStroke.Parent = ToggleButton

local isVisible = true
ToggleButton.MouseButton1Click:Connect(function()
    isVisible = not isVisible
    MainFrame.Visible = isVisible
end)

---------------------------------------------------------
-- نظام التتبع وسجل اللاعبين
---------------------------------------------------------
local playerLogs = {} 

local function recordJoin(player)
    local uId = player.UserId
    if not playerLogs[uId] then
        playerLogs[uId] = {
            UserId = uId,
            Name = player.Name,
            DisplayName = player.DisplayName,
            Joins = 1,
            Leaves = 0,
            LastSeen = tick()
        }
    else
        playerLogs[uId].Joins = playerLogs[uId].Joins + 1
        playerLogs[uId].LastSeen = tick()
    end
end

local function recordLeave(player)
    local uId = player.UserId
    if playerLogs[uId] then
        playerLogs[uId].Leaves = playerLogs[uId].Leaves + 1
        playerLogs[uId].LastSeen = tick()
    end
end

for _, p in ipairs(Players:GetPlayers()) do
    recordJoin(p)
end

Players.PlayerAdded:Connect(recordJoin)
Players.PlayerRemoving:Connect(recordLeave)

-- تحديث القائمة
local function updateLogUI()
    for _, item in ipairs(ScrollFrame:GetChildren()) do
        if item:IsA("Frame") then
            item:Destroy()
        end
    end

    local currentTime = tick()
    local timeoutLimit = 15 * 60 -- 15 دقيقة

    for userId, data in pairs(playerLogs) do
        if (currentTime - data.LastSeen) <= timeoutLimit then
            -- البطاقة الرئيسية
            local Card = Instance.new("Frame")
            Card.Name = "PlayerCard"
            Card.Size = UDim2.new(1, -8, 0, 58)
            Card.BackgroundColor3 = Color3.fromRGB(15, 38, 24)
            Card.BackgroundTransparency = 0.2
            Card.BorderSizePixel = 0
            Card.Parent = ScrollFrame

            local CardCorner = Instance.new("UICorner")
            CardCorner.CornerRadius = UDim.new(0, 10)
            CardCorner.Parent = Card

            local CardStroke = Instance.new("UIStroke")
            CardStroke.Color = Color3.fromRGB(0, 230, 115)
            CardStroke.Thickness = 1.2
            CardStroke.Transparency = 0.4
            CardStroke.Parent = Card

            -- صورة الأفتار
            local AvatarImg = Instance.new("ImageLabel")
            AvatarImg.Name = "Avatar"
            AvatarImg.Size = UDim2.new(0, 42, 0, 42)
            AvatarImg.Position = UDim2.new(0, 8, 0.5, -21)
            AvatarImg.BackgroundColor3 = Color3.fromRGB(5, 18, 10)
            AvatarImg.BackgroundTransparency = 0.1
            AvatarImg.Image = "rbxthumb://type=AvatarHeadShot&id=" .. tostring(userId) .. "&w=150&h=150"
            AvatarImg.Parent = Card

            local AvatarCorner = Instance.new("UICorner")
            AvatarCorner.CornerRadius = UDim.new(1, 0)
            AvatarCorner.Parent = AvatarImg

            -- اسم اللاعب
            local NameLabel = Instance.new("TextLabel")
            NameLabel.Size = UDim2.new(0.35, 0, 1, 0)
            NameLabel.Position = UDim2.new(0, 56, 0, 0)
            NameLabel.BackgroundTransparency = 1
            NameLabel.Text = data.DisplayName .. "\n<font color=\"rgb(120,255,180)\">@" .. data.Name .. "</font>"
            NameLabel.RichText = true
            NameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            NameLabel.TextSize = 12
            NameLabel.Font = Enum.Font.GothamBold
            NameLabel.TextXAlignment = Enum.TextXAlignment.Left
            NameLabel.TextYAlignment = Enum.TextYAlignment.Center
            NameLabel.TextTruncate = Enum.TextTruncate.AtEnd
            NameLabel.Parent = Card

            ---------------------------------------------------------
            -- الأزرار الحية (دخول / خروج / متواجد)
            ---------------------------------------------------------

            -- 1. زر "دخول"
            local JoinBadge = Instance.new("TextLabel")
            JoinBadge.Size = UDim2.new(0, 70, 0, 28)
            JoinBadge.Position = UDim2.new(1, -235, 0.5, -14)
            JoinBadge.BackgroundColor3 = Color3.fromRGB(180, 40, 20)
            JoinBadge.Text = "دخول: " .. tostring(data.Joins)
            JoinBadge.TextColor3 = Color3.fromRGB(255, 230, 230)
            JoinBadge.TextSize = 11
            JoinBadge.Font = Enum.Font.GothamBold
            JoinBadge.Parent = Card

            local JoinCorner = Instance.new("UICorner")
            JoinCorner.CornerRadius = UDim.new(0, 8)
            JoinCorner.Parent = JoinBadge

            local JoinStroke = Instance.new("UIStroke")
            JoinStroke.Color = Color3.fromRGB(255, 80, 50)
            JoinStroke.Thickness = 1.5
            JoinStroke.Parent = JoinBadge

            -- 2. زر "خروج"
            local LeaveBadge = Instance.new("TextLabel")
            LeaveBadge.Size = UDim2.new(0, 70, 0, 28)
            LeaveBadge.Position = UDim2.new(1, -160, 0.5, -14)
            LeaveBadge.BackgroundColor3 = Color3.fromRGB(200, 20, 40)
            LeaveBadge.Text = "خروج: " .. tostring(data.Leaves)
            LeaveBadge.TextColor3 = Color3.fromRGB(255, 230, 230)
            LeaveBadge.TextSize = 11
            LeaveBadge.Font = Enum.Font.GothamBold
            LeaveBadge.Parent = Card

            local LeaveCorner = Instance.new("UICorner")
            LeaveCorner.CornerRadius = UDim.new(0, 8)
            LeaveCorner.Parent = LeaveBadge

            local LeaveStroke = Instance.new("UIStroke")
            LeaveStroke.Color = Color3.fromRGB(255, 60, 80)
            LeaveStroke.Thickness = 1.5
            LeaveStroke.Parent = LeaveBadge

            -- 3. زر حالة الحضور
            local StatusBadge = Instance.new("TextLabel")
            StatusBadge.Size = UDim2.new(0, 75, 0, 28)
            StatusBadge.Position = UDim2.new(1, -85, 0.5, -14)
            
            local isCurrentlyInServer = Players:FindFirstChild(data.Name) ~= nil
            if isCurrentlyInServer then
                StatusBadge.BackgroundColor3 = Color3.fromRGB(0, 160, 75)
                StatusBadge.Text = "متواجد"
                StatusBadge.TextColor3 = Color3.fromRGB(220, 255, 230)
            else
                StatusBadge.BackgroundColor3 = Color3.fromRGB(150, 25, 25)
                StatusBadge.Text = "غادر"
                StatusBadge.TextColor3 = Color3.fromRGB(255, 210, 210)
            end

            StatusBadge.TextSize = 11
            StatusBadge.Font = Enum.Font.GothamBold
            StatusBadge.Parent = Card

            local StatusCorner = Instance.new("UICorner")
            StatusCorner.CornerRadius = UDim.new(0, 8)
            StatusCorner.Parent = StatusBadge

            local StatusStroke = Instance.new("UIStroke")
            StatusStroke.Color = isCurrentlyInServer and Color3.fromRGB(0, 255, 127) or Color3.fromRGB(255, 50, 50)
            StatusStroke.Thickness = 1.5
            StatusStroke.Parent = StatusBadge
        end
    end
end

-- زر التحديث اليدوي
RefreshBtn.MouseButton1Click:Connect(updateLogUI)

-- تحديث تلقائي مستمر كل 3 ثوانٍ
task.spawn(function()
    while true do
        updateLogUI()
        task.wait(3)
    end
end)
