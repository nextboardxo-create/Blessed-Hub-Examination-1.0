--// SERVICES
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

--// CONFIG
local CONFIG = {
    HitboxSize = Vector3.new(5, 5, 5),
    HitboxTransparency = 0.5,

    FOV = 120,

    ESP = true,
    ESPDistance = 350,

    HighlightColor = Color3.fromRGB(255, 0, 0),
    TextColor = Color3.fromRGB(255, 0, 0),
}

--// CACHE
local processedHeads = {}
local processedNPCs = {}

--------------------------------------------------
--// UTIL
--------------------------------------------------

local function isHumanoidModel(model)
    return model
        and model:IsA("Model")
        and model:FindFirstChildOfClass("Humanoid") ~= nil
end

local function isPlayerCharacter(model)
    return model
        and model:IsA("Model")
        and Players:GetPlayerFromCharacter(model) ~= nil
end

local function isNPC(model)
    if not isHumanoidModel(model) then
        return false
    end

    if isPlayerCharacter(model) then
        return false
    end

    return true
end

--------------------------------------------------
--// HITBOX
--------------------------------------------------

local function applyHitbox(head)
    if not head or not head:IsA("BasePart") then
        return
    end

    if head.Name ~= "Head" then
        return
    end

    local model = head.Parent

    if not isNPC(model) then
        return
    end

    if processedHeads[head] then
        return
    end

    processedHeads[head] = true

    head.Size = CONFIG.HitboxSize
    head.CanCollide = false
    head.CanTouch = true
    head.Transparency = CONFIG.HitboxTransparency
end

--------------------------------------------------
--// ESP
--------------------------------------------------

local function removeESP(model)
    if not model then
        return
    end

    local highlight = model:FindFirstChild("OptimizedNPC_Highlight")
    if highlight then
        highlight:Destroy()
    end

    local billboard = model:FindFirstChild("OptimizedNPC_UI")
    if billboard then
        billboard:Destroy()
    end
