local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Services
local RunService = game:GetService("RunService")
local VirtualUser = game:GetService("VirtualUser")

-- Settings
local AUTO_PUNCH = true
local PUNCH_RANGE = 50  -- Adjust based on hitbox
local FARM_DELAY = 0.1  -- Lower = faster (riskier)

print("Ability Wars Auto Farm Loaded!")

-- Anti-AFK
player.Idled:Connect(function()
    VirtualUser:Button2Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
    wait(1)
    VirtualUser:Button2Up(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
end)

-- Main Auto Farm Loop
spawn(function()
    while true do
        if AUTO_PUNCH and character and humanoid.Health > 0 then
            -- Find nearby targets (Players or Dummies/NPCs)
            for _, target in ipairs(workspace:GetDescendants()) do
                if target:IsA("Model") and target:FindFirstChild("Humanoid") and target \~= character then
                    local targetHum = target.Humanoid
                    local targetRoot = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChild("Torso")
                    local myRoot = character:FindFirstChild("HumanoidRootPart")
                    
                    if targetHum and targetHum.Health > 0 and myRoot and targetRoot then
                        local distance = (myRoot.Position - targetRoot.Position).Magnitude
                        if distance <= PUNCH_RANGE then
                            -- Simulate punch / ability use (customize to game's remotes)
                            -- Example: FireServer on game's attack remote (you'll need to find the actual RemoteEvent)
                            -- game.ReplicatedStorage.Remotes.Attack:FireServer(...)
                            
                            -- Basic simulation (may need adjustment)
                            humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                            wait(0.05)
                        end
                    end
                end
            end
        end
        wait(FARM_DELAY)
    end
end)

-- Toggle with key (Press F to toggle)
game:GetService("UserInputService").InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.F then
        AUTO_PUNCH = not AUTO_PUNCH
        print("Auto Farm:", AUTO_PUNCH and "ENABLED" or "DISABLED")
    end
end)

-- Add more features like:
-- Auto Quest (check player.leaderstats or quests folder)
-- Auto Collect Badges/Punches
-- Kill Aura (loop FireServer on damage remote)
-- Auto Ability (random or specific ability activation)
