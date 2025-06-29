# HiddenDevs-ScriptExamples
My scripting examples


1.(My nuke bridge for my squid game glass bridge script)


local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")
local ServerStorage = game:GetService("ServerStorage")
local Workspace = game:GetService("Workspace")

local devProductIds = {3301124429}
local freeUserIds = {0, 0}

local function manageBridgeFolders()
	local bridge = Workspace:FindFirstChild("Bridge")
	if not bridge then return end

	local correct = bridge:FindFirstChild("Correct")
	local wrong = bridge:FindFirstChild("Wrong")

	if correct then correct.Parent = ServerStorage end
	if wrong then wrong.Parent = ServerStorage end

	task.delay(15, function()
		local bridge = Workspace:FindFirstChild("Bridge")
		if not bridge then
			warn("Bridge folder not found in Workspace after 15 seconds!")
			return
		end
		if correct then correct.Parent = bridge end
		if wrong then wrong.Parent = bridge end
	end)
end

local function playPurchaseSound()
	local soundsFolder = Workspace:FindFirstChild("Sounds")
	local purchaseSound = soundsFolder and soundsFolder:FindFirstChild("Sound")
	if purchaseSound then
		local soundClone = purchaseSound:Clone()
		soundClone.Parent = Workspace
		soundClone:Play()
		task.delay(soundClone.TimeLength + 1, function()
			soundClone:Destroy()
		end)
	else
		warn("Purchase sound not found in Workspace.Sounds!")
	end
end

local function onPurchaseSuccess()
	manageBridgeFolders()
	playPurchaseSound()
end

local function manageWrongFolder()
	manageBridgeFolders()
end

MarketplaceService.PromptProductPurchaseFinished:Connect(function(player, productId, purchased)
	if purchased and table.find(devProductIds, productId) then
		onPurchaseSuccess()
	end
end)

Players.PlayerAdded:Connect(function(player)
	if table.find(freeUserIds, player.UserId) then
		manageWrongFolder()
		return
	end
	for _, gamePassId in ipairs({}) do
		if MarketplaceService:UserOwnsGamePassAsync(player.UserId, gamePassId) then
			manageWrongFolder()



			break
		end
	end
end)


2. (my see path for my squid game glass bridge)

local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")
local ServerStorage = game:GetService("ServerStorage")

local devProductIds = {3289440289} -- Changed to developer product ID
local freeUserIds = {7904201663, 0}

local function manageWrongFolder()
	local bridge = workspace:FindFirstChild("Bridge")
	if not bridge then return end

	local wrong = bridge:FindFirstChild("Wrong")
	if wrong then
		-- Move to ServerStorage immediately
		wrong.Parent = ServerStorage

		-- Set up automatic return after 15 seconds
		delay(15, function()
			if wrong and wrong.Parent == ServerStorage then
				wrong.Parent = bridge
			end
		end)
	end
end

-- Developer product purchase handler
MarketplaceService.PromptProductPurchaseFinished:Connect(function(player, productId, purchased)
	if purchased and table.find(devProductIds, productId) then
		manageWrongFolder()
	end
end)

-- Player join handler (this section is unchanged, but note it still checks UserOwnsGamePassAsync)
Players.PlayerAdded:Connect(function(player)
	if table.find(freeUserIds, player.UserId) then
		manageWrongFolder()
		return
	end

	-- NOTE: This part still checks for game pass ownership; if you want to check for product ownership,
	-- that is not possible directly as UserOwnsGamePassAsync is for game passes only.
	-- If you want to check if a player owns a product, you must implement your own tracking system.
	-- For now, this part is left unchanged as per your request.
	for _, gamePassId in ipairs({}) do -- Empty, since we only have a product ID now, but left as example
		if MarketplaceService:UserOwnsGamePassAsync(player.UserId, gamePassId) then
			manageWrongFolder()
			break
		end
	end
end)

3. (My kill all button that kills everyone but you)


local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")

local DEV_PRODUCT_ID = 32894411633

local function killAllExcept(player)
	for _, otherPlayer in ipairs(Players:GetPlayers()) do
		if otherPlayer ~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild("Humanoid") then
			otherPlayer.Character.Humanoid.Health = 0
		end
	end
end

MarketplaceService.PromptProductPurchaseFinished:Connect(function(userId, productId, isPurchased)
	if productId ~= DEV_PRODUCT_ID or not isPurchased then return end

	local player = Players:GetPlayerByUserId(userId)
	if player then
		killAllExcept(player)
	end
end)
