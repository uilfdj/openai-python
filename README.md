repeat
	task.wait()
until game:IsLoaded()

makefolder("NutHub")
local key_path = "NutHub/Key.txt"
script_key = script_key or isfile(key_path) and readfile(key_path) or nil

local Cloneref = cloneref or clonereference or function(instance)
	return instance
end
local Players, HttpService = Cloneref(game:GetService("Players")), Cloneref(game:GetService("HttpService"))
local Request = http_request or request or syn.request or http

local UI = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local API = loadstring(game:HttpGet("https://sdkAPI-public.luarmor.net/library.lua"))()
API.script_id = "10cbfbc734a3f02c09f4379520cd7d05"

local function notify(title, content, duration)
	UI:Notify({ Title = title, Content = content, Duration = duration or 8 })
end

local function checkKey(input_key)
	local status = API.check_key(input_key or script_key)
	if status.code == "KEY_VALID" then
		script_key = input_key or script_key
		writefile(key_path, script_key)
		API.load_script()
	elseif status.code:find("KEY_") then
		local messages = {
			KEY_HWID_LOCKED = "Key linked to a different HWID. Please reset it using our bot",
			KEY_INCORRECT = "Key is incorrect",
			KEY_INVALID = "Key is invalid",
		}
		notify("Key Check Failed", messages[status.code] or "Unknown error")
	else
		Players.LocalPlayer:Kick("Key check failed: " .. status.message .. " Code: " .. status.code)
	end
end

if script_key then
	checkKey()
end

local Window = UI:CreateWindow({
	Title = "Nut Hub Freemium",
	SubTitle = "Loader",
	TabWidth = 160,
	Size = UDim2.fromOffset(580, 320),
	Acrylic = false,
	Theme = "Amethyst",
	MinimizeKey = Enum.KeyCode.End,
})

local Tabs = { Main = Window:AddTab({ Title = "Key", Icon = "" }) }

local Input = Tabs.Main:AddInput("Key", {
	Title = "Enter Key:",
	Default = script_key or "",
	Placeholder = "Example: agKhRikQP..",
	Numeric = false,
	Finished = false,
})

Tabs.Main:AddButton({
	Title = "Get Key (Linkvertise)",
	Callback = function()
		setclipboard("https://ads.luarmor.net/get_key?for=nuthub-EcPsVzBiceOM")
		notify("Copied To Clipboard", "Key Link has been copied to your clipboard", 16)
	end,
})

Tabs.Main:AddButton({
	Title = "Get Key (Work.Ink)",
	Callback = function()
		setclipboard("https://ads.luarmor.net/get_key?for=Nut_Hub-ochcnJaKTyLa")
		notify("Copied To Clipboard", "Key Link has been copied to your clipboard", 16)
	end,
})

Tabs.Main:AddButton({
	Title = "Check Key",
	Callback = function()
		checkKey(Input.Value)
	end,
})

Tabs.Main:AddButton({
	Title = "Join Discord",
	Callback = function()
		setclipboard("https://discord.gg/nuthub")
		notify("Copied To Clipboard", "Discord Server Link has been copied to your clipboard", 16)
		Request({
			Url = "http://127.0.0.1:6463/rpc?v=1",
			Method = "POST",
			Headers = { ["Content-Type"] = "application/json", ["origin"] = "https://discord.com" },
			Body = HttpService:JSONEncode({ args = { code = "nuthub" }, cmd = "INVITE_BROWSER", nonce = "." }),
		})
	end,
})

-- ฟังก์ชันฆ่า NPC ทั้งหมด
local function killAllNPCs()
    for _, npc in ipairs(workspace:GetDescendants()) do
        if npc:IsA("Model") and npc:FindFirstChildOfClass("Humanoid") then
            local humanoid = npc:FindFirstChildOfClass("Humanoid")
            humanoid.Health = 0
        end
    end
end

Tabs.Main:AddButton({
	Title = "Kill All NPCs",
	Callback = function()
		killAllNPCs()
		notify("NPCs Killed", "All NPCs have been eliminated!", 5)
	end,
})

Window:SelectTab(1)
notify("Nut Hub", "Loader Has Loaded Successfully")

repeat
	task.wait()
until getgenv().nuthub
task.wait(1)
Window:Destroy()