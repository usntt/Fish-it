local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

local FISHING_DELAY = 0.01
local AUTO_REEL = true
local MAX_SPEED = true

local function getRod()
    local rod = player.Character:FindFirstChildOfClass("Tool")
    if rod and (rod:FindFirstChild("RodScript") or rod.Name:lower():find("rod")) then
        return rod
    end
    return nil
end

spawn(function()
    while wait() do
        local rod = getRod()
        if not rod then continue end
        
        if MAX_SPEED then
            if ReplicatedStorage:FindFirstChild("Remotes") then
                local remotes = ReplicatedStorage.Remotes
                if remotes:FindFirstChild("Fishing") then
                    remotes.Fishing:FireServer("Cast")
                elseif remotes:FindFirstChild("ThrowRod") then
                    remotes.ThrowRod:FireServer()
                end
            end
        else
            wait(FISHING_DELAY)
            if ReplicatedStorage:FindFirstChild("Remotes") then
                local remotes = ReplicatedStorage.Remotes
                if remotes:FindFirstChild("Fishing") then
                    remotes.Fishing:FireServer("Cast")
                end
            end
        end
        
        if AUTO_REEL then
            local hasBite = false
            local playerGui = player:WaitForChild("PlayerGui")
            for _, gui in pairs(playerGui:GetChildren()) do
                if gui.Name:lower():find("fishing") then
                    for _, indicator in pairs(gui:GetChildren()) do
                        if (indicator.Name:lower():find("bite") or 
                            indicator.Name:lower():find("catch") or
                            indicator.Name:lower():find("fish")) and
                           indicator.Visible then
                            hasBite = true
                            break
                        end
                    end
                end
            end
            
            if hasBite then
                if MAX_SPEED then
                    if ReplicatedStorage:FindFirstChild("Remotes") then
                        local remotes = ReplicatedStorage.Remotes
                        if remotes:FindFirstChild("Fishing") then
                            remotes.Fishing:FireServer("Reel")
                        elseif remotes:Find
