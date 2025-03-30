-- Tạo GUI nhập key
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local TextBox = Instance.new("TextBox")
local Button = Instance.new("TextButton")

-- Cấu hình GUI
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
Frame.Size = UDim2.new(0, 300, 0, 150)
Frame.Position = UDim2.new(0.5, -150, 0.5, -75)
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Màu đen
Frame.Parent = ScreenGui

TextBox.Size = UDim2.new(0, 200, 0, 50)
TextBox.Position = UDim2.new(0.5, -100, 0.2, 0)
TextBox.PlaceholderText = "Nhập key vào đây"
TextBox.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Màu đen
TextBox.TextColor3 = Color3.fromRGB(255, 255, 255) -- Chữ trắng
TextBox.Parent = Frame

Button.Size = UDim2.new(0, 100, 0, 40)
Button.Position = UDim2.new(0.5, -50, 0.6, 0)
Button.Text = "Xác nhận"
Button.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Màu đen
Button.TextColor3 = Color3.fromRGB(255, 255, 255) -- Chữ trắng
Button.Parent = Frame

-- Key cần nhập đúng
local CorrectKey = "trungnhandeptrai"

-- Hàm cho phép kéo hộp thoại
local function makeDraggable(frame)
    local dragging, dragInput, startPos, startMousePos
    
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            startMousePos = input.Position
            startPos = frame.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    
    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - startMousePos
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- Hàm kích hoạt chống ban
local function activateAntiBan()
    local mt = getrawmetatable(game)
    local old = mt.__namecall
    setreadonly(mt, false)
    mt.__namecall = newcclosure(function(...)
        local args = {...}
        if tostring(args[1]) == "Kick" then
            return nil -- Ngăn chặn lệnh Kick
        end
        return old(...)
    end)
    setreadonly(mt, true)
    print("Chống ban đã kích hoạt!")
end

-- Hàm mở hub tricks.hub
local function openTricksHub()
    local Hub = Instance.new("ScreenGui")
    Hub.Name = "tricks.hub"
    Hub.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

    local HubFrame = Instance.new("Frame")
    HubFrame.Size = UDim2.new(0, 400, 0, 300)
    HubFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
    HubFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Màu đen
    HubFrame.Parent = Hub
    
    makeDraggable(HubFrame) -- Cho phép kéo hộp thoại

    local HubLabel = Instance.new("TextLabel")
    HubLabel.Size = UDim2.new(1, 0, 0, 50)
    HubLabel.Text = "Tricks Hub"
    HubLabel.TextSize = 20
    HubLabel.TextColor3 = Color3.fromRGB(255, 255, 255) -- Chữ trắng
    HubLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Màu đen
    HubLabel.Parent = HubFrame

    -- Nút bật/tắt noclip
    local NoclipLabel = Instance.new("TextLabel")
    NoclipLabel.Size = UDim2.new(0, 100, 0, 30)
    NoclipLabel.Position = UDim2.new(0, 10, 0, 60)
    NoclipLabel.Text = "Noclip"
    NoclipLabel.TextSize = 16
    NoclipLabel.TextColor3 = Color3.fromRGB(255, 255, 255) -- Chữ trắng
    NoclipLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Màu đen
    NoclipLabel.Parent = HubFrame

    local NoclipButton = Instance.new("TextButton")
    NoclipButton.Size = UDim2.new(0, 60, 0, 30)
    NoclipButton.Position = UDim2.new(0, 120, 0, 60)
    NoclipButton.Text = "OFF"
    NoclipButton.TextSize = 16
    NoclipButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    NoclipButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    NoclipButton.Parent = HubFrame
    
    local noclipEnabled = false
    local player = game.Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    
    local function toggleNoclip()
        noclipEnabled = not noclipEnabled
        NoclipButton.Text = noclipEnabled and "ON" or "OFF"
        
        if noclipEnabled then
            game:GetService("RunService").Stepped:Connect(function()
                if noclipEnabled then
                    for _, part in pairs(character:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = false
                        end
                    end
                end
            end)
        else
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
    
    NoclipButton.MouseButton1Click:Connect(toggleNoclip)

    -- Kích hoạt chống ban ngay khi mở hub
    activateAntiBan()

    -- Nút X để tắt hub
    local CloseButton = Instance.new("TextButton")
    CloseButton.Size = UDim2.new(0, 30, 0, 30)
    CloseButton.Position = UDim2.new(1, -35, 0, 5)
    CloseButton.Text = "X"
    CloseButton.TextSize = 18
    CloseButton.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Màu đen
    CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255) -- Chữ trắng
    CloseButton.Parent = HubFrame

    CloseButton.MouseButton1Click:Connect(function()
        Hub:Destroy()
    end)
end

Button.MouseButton1Click:Connect(function()
    if TextBox.Text == CorrectKey then
        Frame:Destroy()
        openTricksHub()
    end
end)

makeDraggable(Frame)

