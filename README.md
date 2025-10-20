local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer

-- Konfigurasi
local FISHING_DELAY = 0.10 -- jeda antar percobaan cast saat MAX_SPEED=false
local AUTO_REEL = true      -- otomatis menarik saat ada "bite"
local MAX_SPEED = true      -- true: tanpa jeda; false: gunakan FISHING_DELAY

local function getRod()
    local character = player.Character or player.CharacterAdded:Wait()
    local tool = character:FindFirstChildOfClass("Tool")
    if not tool then
        return nil
    end
    local nameLower = string.lower(tool.Name)
    if tool:FindFirstChild("RodScript") or string.find(nameLower, "rod", 1, true) then
        return tool
    end
    return nil
end

local function getRemotes()
    local remotes = ReplicatedStorage:FindFirstChild("Remotes")
    if remotes and remotes:IsA("Folder") then
        return remotes
    end
    return nil
end

local function castLine()
    local remotes = getRemotes()
    if not remotes then
        return
    end
    if remotes:FindFirstChild("Fishing") then
        remotes.Fishing:FireServer("Cast")
        return
    end
    if remotes:FindFirstChild("ThrowRod") then
        remotes.ThrowRod:FireServer()
        return
    end
    if remotes:FindFirstChild("Cast") then
        remotes.Cast:FireServer()
        return
    end
end

local function reelIn()
    local remotes = getRemotes()
    if not remotes then
        return
    end
    if remotes:FindFirstChild("Fishing") then
        remotes.Fishing:FireServer("Reel")
        return
    end
    if remotes:FindFirstChild("Reel") then
        remotes.Reel:FireServer()
        return
    end
    if remotes:FindFirstChild("Pull") then
        remotes.Pull:FireServer()
        return
    end
end

local function detectBite()
    local playerGui = player:WaitForChild("PlayerGui")
    for _, gui in ipairs(playerGui:GetChildren()) do
        local guiNameLower = string.lower(gui.Name)
        if string.find(guiNameLower, "fishing", 1, true) then
            for _, indicator in ipairs(gui:GetChildren()) do
                local indicatorNameLower = string.lower(indicator.Name)
                if (string.find(indicatorNameLower, "bite", 1, true)
                    or string.find(indicatorNameLower, "catch", 1, true)
                    or string.find(indicatorNameLower, "fish", 1, true))
                    and indicator:IsA("GuiObject")
                    and indicator.Visible then
                    return true
                end
            end
        end
    end
    return false
end

-- Jalankan loop auto-fishing di client
task.spawn(function()
    while task.wait(MAX_SPEED and 0 or FISHING_DELAY) do
        local rod = getRod()
        if not rod then
            continue
        end

        castLine()

        if AUTO_REEL and detectBite() then
            reelIn()
            task.wait(0.1)
        end
    end
end)
