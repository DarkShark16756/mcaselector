local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

local vfxSettings = {
    EffectColor = Color3.fromRGB(50, 150, 255), -- Cor do efeito
    Size = Vector3.new(3, 3, 3), -- Tamanho inicial do efeito
    Material = Enum.Material.Neon, -- Material do efeito
    Lifetime = 1, 
}

local function createVFX(position)
    local effect = Instance.new("Part")
    effect.Anchored = true
    effect.CanCollide = false
    effect.Size = vfxSettings.Size
    effect.Position = position
    effect.Material = vfxSettings.Material
    effect.Color = vfxSettings.EffectColor
    effect.Transparency = 0.5
    effect.Parent = Workspace
    
    local tweenService = game:GetService("TweenService")
    local tweenInfo = TweenInfo.new(
        vfxSettings.Lifetime,
        Enum.EasingStyle.Quad,
        Enum.EasingDirection.Out
    )
    local goal = {
        Position = position + Vector3.new(0, 10, 0), -- Move para cima
        Transparency = 1 -- Desaparece
    }
    local tween = tweenService:Create(effect, tweenInfo, goal)
    tween:Play()
    
    game:GetService("Debris"):AddItem(effect, vfxSettings.Lifetime)
end

local function launchEnemy(target)
    if target and target:FindFirstChild("HumanoidRootPart") then
        local root = target.HumanoidRootPart
        
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 50, 0) -- Para cima
        bodyVelocity.MaxForce = Vector3.new(1e6, 1e6, 1e6)
        bodyVelocity.Parent = root
        
        task.wait(0.5)
        bodyVelocity.Velocity = rootPart.CFrame.LookVector * 50 -- Para frente
        task.wait(0.5)
        bodyVelocity:Destroy()
    end
end

local function launchPlayer()
    local bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Velocity = Vector3.new(0, 50, 0) -- Para cima
    bodyVelocity.MaxForce = Vector3.new(1e6, 1e6, 1e6)
    bodyVelocity.Parent = rootPart
    
    task.wait(1)
    bodyVelocity:Destroy()
end

local function onFlowingWaterAttack(target)
    if target then
        local position = target.HumanoidRootPart.Position
        
        createVFX(position)
        
        launchPlayer()
        launchEnemy(target)
    end
end

local function setAttackName(skillBar, attackIndex, newName)
    if skillBar and skillBar:FindFirstChild("Attack" .. attackIndex) then
        local attackButton = skillBar["Attack" .. attackIndex]
        attackButton.Text = newName
    end
end

local skillBar = player.PlayerGui:WaitForChild("SkillBar") -- Substitua pelo nome correto da barra de habilidades
setAttackName(skillBar, 1, "Múltiplo Attack") -- Renomeia o primeiro ataque para "Múltiplo Attack"

local FlowingWaterEvent = Instance.new("BindableEvent") -- Exemplo fictício
FlowingWaterEvent.Event:Connect(onFlowingWaterAttack)
