–// 👾 MONSTER SCRIPT | FIXED UI (NO LAYOUT BUGS) –// Steal a Brainrot |
MOBILE SAFE

local Players = game:GetService(“Players”) local RunService =
game:GetService(“RunService”)

local player = Players.LocalPlayer local camera =
workspace.CurrentCamera

– ================= STATES ================= local AimEnabled = false
local AutoBat = false local SuperJumpEnabled = false local AutoJump =
false local Minimized = false

local AIM_RANGE = 16 local GROUND_SMOOTH = 8 local AIR_SMOOTH = 3 local
PREDICTION = 0.12

local jumpCooldown = 0.25 local lastJump = 0

– ================= CHARACTER ================= local char =
player.Character or player.CharacterAdded:Wait() local humanoid =
char:WaitForChild(“Humanoid”) local hrp =
char:WaitForChild(“HumanoidRootPart”)

player.CharacterAdded:Connect(function(c) char = c humanoid =
c:WaitForChild(“Humanoid”) hrp = c:WaitForChild(“HumanoidRootPart”) end)

– ================= GUI ================= local gui =
Instance.new(“ScreenGui”) gui.Name = “MonsterScriptGUI” gui.ResetOnSpawn
= false gui.Parent = player.PlayerGui

local frame = Instance.new(“Frame”, gui) frame.Size = UDim2.new(0, 260,
0, 340) frame.Position = UDim2.new(0.5, -130, 0.5, -170)
frame.BackgroundColor3 = Color3.fromRGB(10,10,10) frame.Active = true
frame.Draggable = true Instance.new(“UICorner”, frame).CornerRadius =
UDim.new(0,18)

local stroke = Instance.new(“UIStroke”, frame) stroke.Color =
Color3.fromRGB(255,255,255) stroke.Transparency = 0.3

– ================= TITLE ================= local title =
Instance.new(“TextLabel”, frame) title.Size = UDim2.new(1, -50, 0, 40)
title.Position = UDim2.new(0, 10, 0, 0) title.BackgroundTransparency = 1
title.Text = “👾 MONSTER SCRIPT” title.Font = Enum.Font.GothamBold
title.TextSize = 17 title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextXAlignment = Enum.TextXAlignment.Left

– ================= MINIMIZE ================= local minBtn =
Instance.new(“TextButton”, frame) minBtn.Size = UDim2.new(0, 32, 0, 32)
minBtn.Position = UDim2.new(1, -38, 0, 4) minBtn.Text = “—” minBtn.Font
= Enum.Font.GothamBold minBtn.TextSize = 22 minBtn.TextColor3 =
Color3.fromRGB(255,255,255) minBtn.BackgroundColor3 =
Color3.fromRGB(25,25,25) Instance.new(“UICorner”, minBtn)

– ================= BUTTON MAKER ================= local function
makeButton(text, y) local btn = Instance.new(“TextButton”, frame)
btn.Size = UDim2.new(0.9, 0, 0, 40) btn.Position = UDim2.new(0.05, 0, 0,
y) btn.Text = text btn.Font = Enum.Font.GothamBold btn.TextSize = 14
btn.TextColor3 = Color3.fromRGB(255,255,255) btn.BackgroundColor3 =
Color3.fromRGB(25,25,25) Instance.new(“UICorner”, btn).CornerRadius =
UDim.new(0,12)

    local st = Instance.new("UIStroke", btn)
    st.Color = Color3.fromRGB(255,255,255)
    st.Transparency = 0.4
    return btn

end

– ================= BUTTONS ================= local aimBtn =
makeButton(“AIM SHIFT LOCK : OFF”, 50) local batBtn = makeButton(“AUTO
BAT : OFF”, 100) local jumpBtn = makeButton(“SUPER JUMP : OFF”, 150)
local autoJB = makeButton(“AUTO JUMP : OFF”, 200)

– ================= BUTTON LOGIC =================
aimBtn.MouseButton1Click:Connect(function() AimEnabled = not AimEnabled
aimBtn.Text = AimEnabled and “AIM SHIFT LOCK : ON” or “AIM SHIFT LOCK :
OFF” end)

batBtn.MouseButton1Click:Connect(function() AutoBat = not AutoBat
batBtn.Text = AutoBat and “AUTO BAT : ON” or “AUTO BAT : OFF” end)

– SUPER JUMP jumpBtn.MouseButton1Click:Connect(function()
SuperJumpEnabled = not SuperJumpEnabled jumpBtn.Text = SuperJumpEnabled
and “SUPER JUMP : ON” or “SUPER JUMP : OFF”

    if SuperJumpEnabled then
        pcall(function()
            loadstring(game:HttpGet(
                "https://rawscripts.net/raw/Universal-Script-Super-Jump-Script-58482"
            ))()
        end)
    end

end)

– AUTO JUMP (SAFE) autoJB.MouseButton1Click:Connect(function() AutoJump
= not AutoJump autoJB.Text = AutoJump and “AUTO JUMP : ON” or “AUTO JUMP
: OFF” end)

– ================= MINIMIZE =================
minBtn.MouseButton1Click:Connect(function() Minimized = not Minimized
aimBtn.Visible = not Minimized batBtn.Visible = not Minimized
jumpBtn.Visible = not Minimized autoJB.Visible = not Minimized
frame.Size = Minimized and UDim2.new(0,260,0,40) or
UDim2.new(0,260,0,340) minBtn.Text = Minimized and “+” or “—” end)

– ================= AIM + BAT ================= local function
getClosestEnemy() local closest, dist = nil, AIM_RANGE for _,plr in
pairs(Players:GetPlayers()) do if plr ~= player and plr.Character and
plr.Character:FindFirstChild(“HumanoidRootPart”) then local h =
plr.Character:FindFirstChildOfClass(“Humanoid”) if h and h.Health > 0
then local d = (plr.Character.HumanoidRootPart.Position -
hrp.Position).Magnitude if d < dist then dist = d closest =
plr.Character end end end end return closest end

RunService.RenderStepped:Connect(function(dt) if AimEnabled then local t
= getClosestEnemy() if t then local pos = t.HumanoidRootPart.Position +
t.HumanoidRootPart.Velocity * PREDICTION local state =
humanoid:GetState() local smooth = (state ==
Enum.HumanoidStateType.Freefall or state ==
Enum.HumanoidStateType.Jumping) and AIR_SMOOTH or GROUND_SMOOTH

            camera.CFrame = camera.CFrame:Lerp(
                CFrame.new(camera.CFrame.Position, pos),
                math.clamp(smooth * dt, 0, 1)
            )
        end
    end

    if AutoBat then
        local tool = char:FindFirstChildOfClass("Tool")
        if tool then pcall(function() tool:Activate() end) end
    end

end)

– ================= AUTO JUMP LOOP =================
RunService.Heartbeat:Connect(function() if AutoJump and humanoid and
humanoid.Health > 0 then if humanoid.FloorMaterial ~= Enum.Material.Air
then if tick() - lastJump >= jumpCooldown then
humanoid:ChangeState(Enum.HumanoidStateType.Jumping) lastJump = tick()
end end end end)
