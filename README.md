-- [[ STORM HUB UNIFIED - RAYFIELD EDITION ]] --

-- Carrega a Rayfield Library
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- Janela Principal
local Window = Rayfield:CreateWindow({
   Name = "Storm Hub | Unified V2.2",
   LoadingTitle = "Storm Interface Suite",
   LoadingSubtitle = "by Sirius & Storm Team",
   ConfigurationSaving = {
      Enabled = true,
      FolderName = "StormHubConfigs",
      FileName = "StormMain"
   },
   Discord = {
      Enabled = false,
      Invite = "noinvitelink",
      RememberJoins = true
   },
   KeySystem = false,
})

-- Variáveis de Controle (Mantendo as globais do seu script original)
_G.TiltEnabled = false
_G.TiltPower = 20
_G.Athletic = false
_G.NeverTired = false
_G.ESP_Enabled = false
local curX, curZ = 0, 0

-- Serviços
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local Player = Players.LocalPlayer

--- [[ ABAS ]] ---

local MainTab = Window:CreateTab("Main Features", "zap") -- Aba de Funções Internas
local ScriptTab = Window:CreateTab("External Scripts", "code") -- Aba de Scripts Externos

--- [[ ABA PRINCIPAL: INTERNAL FEATURES ]] ---

MainTab:CreateSection("Movement & Buffs")

MainTab:CreateToggle({
   Name = "Athletic Buff (Speed)",
   CurrentValue = false,
   Flag = "AthleticToggle",
   Callback = function(Value)
      _G.Athletic = Value
   end,
})

MainTab:CreateToggle({
   Name = "Never Tired (Infinite Stamina)",
   CurrentValue = false,
   Flag = "StaminaToggle",
   Callback = function(Value)
      _G.NeverTired = Value
   end,
})

MainTab:CreateSection("Tilt System")

MainTab:CreateToggle({
   Name = "Tilt System Enabled",
   CurrentValue = false,
   Flag = "TiltToggle",
   Callback = function(Value)
      _G.TiltEnabled = Value
   end,
})

MainTab:CreateSlider({
   Name = "Tilt Power",
   Range = {0, 50},
   Increment = 1,
   Suffix = "Power",
   CurrentValue = 20,
   Flag = "TiltPowerSlider",
   Callback = function(Value)
      _G.TiltPower = Value
   end,
})

--- [[ ABA DE SCRIPTS: LOADERS ]] ---

ScriptTab:CreateSection("GitHub Script Loaders")

ScriptTab:CreateButton({
   Name = "Load Cherry Hub",
   Callback = function()
      Rayfield:Notify({Title = "Loading", Content = "Fetching Cherry Hub...", Duration = 2})
      loadstring(game:HttpGet("https://raw.githubusercontent.com/cherryhub40-ops/CherryHub/main/script.lua"))()
   end,
})

ScriptTab:CreateButton({
   Name = "Load Storm Hub V1",
   Callback = function()
      Rayfield:Notify({Title = "Loading", Content = "Fetching Storm Hub V1...", Duration = 2})
      loadstring(game:HttpGet("https://raw.githubusercontent.com/Karuzenin/StormHubV1/main/script.lua"))()
   end,
})

ScriptTab:CreateButton({
   Name = "Load Jump Power Script",
   Callback = function()
      Rayfield:Notify({Title = "Loading", Content = "Fetching Jump Power...", Duration = 2})
      loadstring(game:HttpGet("https://raw.githubusercontent.com/Karuzenin/JumpPower/refs/heads/main/JumpPower"))()
   end,
})

--- [[ LÓGICA DE BACKEND (MANTIDA DO SEU ORIGINAL) ]] ---

-- Lógica do Tilt System
RunService.RenderStepped:Connect(function(dt)
    local char = Player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")

    if _G.TiltEnabled and root and hum then
        local state = hum:GetState()
        local inAir = (state == Enum.HumanoidStateType.Freefall or state == Enum.HumanoidStateType.Jumping)
        local targetX, targetZ = 0, 0
        if inAir then
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then targetX = _G.TiltPower end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then targetX = -_G.TiltPower * 0.75 end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then targetZ = -_G.TiltPower * 0.65 end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then targetZ = _G.TiltPower * 0.65 end
        end
        curX = curX + (targetX - curX) * math.clamp(dt * 8, 0, 1)
        curZ = curZ + (targetZ - curZ) * math.clamp(dt * 8, 0, 1)
        local _, yRot, _ = root.CFrame:ToEulerAnglesYXZ()
        root.CFrame = CFrame.new(root.Position) * CFrame.Angles(0, yRot, 0) * CFrame.Angles(math.rad(-curX), 0, math.rad(-curZ))
    end
end)

-- Loop de Atributos (Speed e Stamina)
task.spawn(function()
    while true do
        local char = Player.Character
        if char and char:FindFirstChild("Humanoid") then
            if _G.Athletic then char.Humanoid.WalkSpeed = 22 end
            if _G.NeverTired then
                local st = char:FindFirstChild("Stamina") or Player:FindFirstChild("Stamina")
                if st then st.Value = 100 end
            end
        end
        task.wait(0.1)
    end
end)

-- Carrega configurações salvas (se existirem)
Rayfield:LoadConfiguration()

Rayfield:Notify({
   Title = "Storm Hub Loaded",
   Content = "Welcome back to Storm Hub Unified V2.2!",
   Duration = 5,
   Image = "zap",
})
