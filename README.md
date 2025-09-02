# Roblox-AI-Script-README.md-AI.lua
Universal Roblox AI Chatbot 
!learn pizza:I love pizza too!

---

#### **AI.lua**
```lua
-- Roblox AI Assistant Script (Educational Purposes Only)
local AI_ENABLED = false
local memory = {}
local customResponses = {}

local defaultResponses = {
    greetings = {"Hello!", "Hi there!", "Hey! How can I help you today?"},
    farewell = {"Goodbye!", "See you later!", "Take care!"},
    whoami = {"I am an AI assistant created for educational purposes."},
    unknown = {"Hmm, I’m not sure about that.", "Interesting question! Can you ask me something else?"}
}

local function getRandomResponse(list)
    return list[math.random(1, #list)]
end

game.Players.PlayerAdded:Connect(function(player)
    player.Chatted:Connect(function(msg)
        if string.sub(msg, 1, 1) == "!" then
            local args = string.split(string.sub(msg, 2), " ")
            local command = string.lower(args[1])

            if command == "ai" and args[2] == "on" then
                AI_ENABLED = true
                game:GetService("StarterGui"):SetCore("SendNotification", {Title="AI", Text="AI Enabled"})
            elseif command == "ai" and args[2] == "off" then
                AI_ENABLED = false
                game:GetService("StarterGui"):SetCore("SendNotification", {Title="AI", Text="AI Disabled"})
            elseif command == "clear" then
                memory = {}
                game:GetService("StarterGui"):SetCore("SendNotification", {Title="AI", Text="Memory Cleared"})
            elseif command == "learn" and args[2] then
                local learnData = string.split(table.concat(args, " ", 2), ":")
                if #learnData == 2 then
                    local keyword = string.lower(learnData[1])
                    local response = learnData[2]
                    customResponses[keyword] = response
                    game:GetService("StarterGui"):SetCore("SendNotification", {Title="AI", Text="Learned new word: " .. keyword})
                end
            end
        elseif AI_ENABLED then
            local lowerMsg = string.lower(msg)
            local reply = nil

            for keyword, response in pairs(customResponses) do
                if string.find(lowerMsg, keyword) then
                    reply = response
                    break
                end
            end

            if not reply then
                if string.find(lowerMsg, "hello") or string.find(lowerMsg, "hi") then
                    reply = getRandomResponse(defaultResponses.greetings)
                elseif string.find(lowerMsg, "bye") then
                    reply = getRandomResponse(defaultResponses.farewell)
                elseif string.find(lowerMsg, "who") then
                    reply = getRandomResponse(defaultResponses.whoami)
                else
                    reply = getRandomResponse(defaultResponses.unknown)
                end
            end

            table.insert(memory, msg)
            game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest:FireServer(reply, "All")
        end
    end)
end)
