--[[
    DUZIN07X / DUZIN.MDS - V12.1 SUPREME
    Novidade: Anti-AFK Automático Integrado
]]

-- 1. SISTEMA ANTI-AFK (Roda antes de tudo)
local VirtualUser = game:GetService("VirtualUser")
game:GetService("Players").LocalPlayer.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new())
    print("Anti-AFK: Movimento de prevenção executado!")
end)

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "Duzin07x / Duzin.mds | V12.1",
   LoadingTitle = "Protocolo Supreme Ativo...",
   LoadingSubtitle = "Anti-AFK: Ativado",
   ConfigurationSaving = { Enabled = false }
})

-- [ Restante das funções V12 mantidas ]
getgenv().Aimbot = false
getgenv().AimTeam = false
getgenv().BringAll = false
getgenv().HitboxSize = 2
getgenv().HitboxEnabled = false
getgenv().ESP_Box = false
getgenv().ClickTP = false

local TabCombate = Window:CreateTab("Combate", 4483362458)
local TabTroll = Window:CreateTab("Troll & Admin", 4483362458)
local TabTeleport = Window:CreateTab("Teleporte", 4483362458)
local TabVisual = Window:CreateTab("Visual/ESP", 4483345998)

-- [ ABA COMBATE ]
TabCombate:CreateToggle({
   Name = "Aim Lock",
   CurrentValue = false,
   Callback = function(v) getgenv().Aimbot = v end,
})

TabCombate:CreateSlider({
   Name = "Hitbox",
   Range = {2, 100},
   Increment = 1,
   CurrentValue = 2,
   Callback = function(v) 
      getgenv().HitboxSize = v 
      getgenv().HitboxEnabled = (v > 2)
   end,
})

-- [ ABA TROLL ]
TabTroll:CreateToggle({
   Name = "Bring All",
   CurrentValue = false,
   Callback = function(v) getgenv().BringAll = v end,
})

TabTroll:CreateButton({
   Name = "Void All",
   Callback = function()
      for _, p in pairs(game.Players:GetPlayers()) do
         if p ~= game.Players.LocalPlayer and p.Character then
            p.Character:MoveTo(Vector3.new(0, -500, 0))
         end
      end
   end,
})

-- [ ABA TELEPORTE ]
TabTeleport:CreateToggle({
   Name = "Click Teleport",
   CurrentValue = false,
   Callback = function(v) getgenv().ClickTP = v end,
})

local PlayerDropdown = TabTeleport:CreateDropdown({
   Name = "Teleport para Jogador",
   Options = {"Atualizar"},
   CurrentOption = "Atualizar",
   Callback = function(Option)
      local target = game.Players:FindFirstChild(Option)
      if target and target.Character then
         game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame
      end
   end,
})

-- Loop de atualização da lista
task.spawn(function()
    while task.wait(5) do
        local plys = {}
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= game.Players.LocalPlayer then table.insert(plys, p.Name) end
        end
        PlayerDropdown:Refresh(plys)
    end
end)

-- [ ABA VISUAL ]
TabVisual:CreateToggle({
   Name = "ESP Highlight",
   CurrentValue = false,
   Callback = function(v) getgenv().ESP_Box = v end,
})

-- LÓGICA DE CLICK TELEPORT
game:GetService("UserInputService").InputBegan:Connect(function(input, gpe)
    if getgenv().ClickTP and not gpe then
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            local mouse = game.Players.LocalPlayer:GetMouse()
            game.Players.LocalPlayer.Character:MoveTo(mouse.Hit.p + Vector3.new(0, 3, 0))
        end
    end
end)

-- LOOP GERAL
game:GetService("RunService").RenderStepped:Connect(function()
    local lp = game.Players.LocalPlayer
    local cam = workspace.CurrentCamera
    
    for _, p in pairs(game.Players:GetPlayers()) do
        if p ~= lp and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            if getgenv().HitboxEnabled then
                p.Character.HumanoidRootPart.Size = Vector3.new(getgenv().HitboxSize, getgenv().HitboxSize, getgenv().HitboxSize)
                p.Character.HumanoidRootPart.Transparency = 0.7
            end
            if getgenv().BringAll then
                p.Character.HumanoidRootPart.CFrame = lp.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -4)
            end
            local h = p.Character:FindFirstChild("Highlight")
            if getgenv().ESP_Box then
                if not h then Instance.new("Highlight", p.Character) end
            elseif h then h:Destroy() end
        end
    end

    if getgenv().Aimbot then
        local target = nil
        local dist = math.huge
        for _, v in pairs(game.Players:GetPlayers()) do
            if v ~= lp and v.Character and v.Character:FindFirstChild("Head") then
                local pos, vis = cam:WorldToViewportPoint(v.Character.Head.Position)
                if vis then
                    local m = (Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2) - Vector2.new(pos.X, pos.Y)).Magnitude
                    if m < dist then dist = m; target = v.Character.Head end
                end
            end
        end
        if target then
            cam.CFrame = CFrame.new(cam.CFrame.Position, target.Position)
        end
    end
end)

Rayfield:Notify({Title = "V12.1 Supreme", Content = "Anti-AFK Ativado automaticamente!", Duration = 5})
