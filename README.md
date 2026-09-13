-- ILHA BELA -- By: Theus -- Version: Beta

-- ═══════════════════════════════════════════════════════
-- 🛡️ BYPASS ATIVÁVEL - ILHA BELA / KLZ v3.5
-- ═══════════════════════════════════════════════════════
local BypassEnabled = true
local Player = game.Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")
local Camera = workspace.CurrentCamera
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

-- LISTA GIGANTE DE NOMES DE ANTI-CHEAT
local AntiCheatNames = {
	"KLZ", "klz", "Klz", "K.L.Z", "K_L_Z",
	"Anti Cheat", "AntiCheat", "Anti-Cheat", "Anti_Cheat",
	"AntiCheatKLZ", "AntiCheatKlz", "AntiCheat klz", "AntiCheat KLZ",
	"AntiCheatKLZv3", "AntiCheatKLZv35",
	"Anti Cheat initated", "AntiCheatInitated", "initated",
	"AntiCheatInitiated", "AntiCheatStarted",
	"v3.5", "v3", "v4", "v5", "v6", "v7",
	"AntiFly", "antifly", "Antifly", "ANTIFLY",
	"Anti Fly", "Anti_Fly", "Anti-Fly", "AntiFlySystem",
	"AntiFlyScript", "AntiFlyHandler", "AntiFlyDetect",
	"AntiFlyDetection", "AntiFlyProtection",
	"flyDetector", "FlyDetector", "Fly-Detector", "fly_detector",
	"FlyDetection", "FlyCheck", "FlyCheckSystem",
	"AntiFlying", "AntiFlyingSystem", "AntiFlyingDetect",
	"NoFly", "NoFlyZone", "NoFlySystem",
	"antiflyligado", "antifly_ligado",
	"AntiHitbox", "antiHitbox", "AntiHitBox", "antihitbox", "ANTIHITBOX",
	"Anti Hitbox", "Anti_Hitbox", "Anti-Hitbox",
	"AntiHitboxSystem", "AntiHitboxDetect", "AntiHitboxDetection",
	"HitboxDetector", "hitboxDetector", "Hitbox-Detector", "hitbox_detector",
	"HitboxDetection", "HitboxCheck", "HitboxCheckSystem",
	"AntiHitBoxSystem", "AntiHitBoxDetect",
	"HitboxBlocker", "HitboxProtection",
	"AntiCheatBox", "AntiCheatBoxSystem", "AntiCheatBoxDetect",
	"Anti Cheat Box", "Anti_Cheat_Box", "Anti-Cheat-Box",
	"CheatBox", "CheatBoxSystem", "CheatBoxDetect",
	"ACBox", "AC-Box", "AC_Box", "antiCheatBox", "anticheatbox",
	"Orpheus", "OrpheusShared", "OrpheusAC", "OrpheusAntiCheat",
	"OrpheusSystem", "OrpheusDetect",
	"AntiExploit", "AntiExploits", "Anti-Exploit",
	"AntiHack", "AntiHacks", "Anti-Hack",
	"AntiCheatSystem", "ACS", "AC",
	"CheatDetector", "HackDetector", "ExploitDetector",
	"BypassDetector", "InjectionDetector",
	"SpeedDetector", "SpeedCheck", "SpeedHackDetect",
	"NoclipDetector", "NoclipCheck", "NoClipDetect",
	"GodModeDetector", "GodModeCheck",
	"TeleportDetector", "TeleportCheck",
	"RemoteMonitor", "RemoteDetector", "RemoteChecker",
	"PlayerMonitor", "PlayerChecker", "ClientChecker",
	"ServerGuard", "ServerSecurity", "GameGuard",
	"PlayerGuard", "GameProtection",
	"IlhaBelaAntiCheat", "IlhaBelaAC", "IlhaBelaAntiFly", "IlhaBelaAntiHitbox",
	"TesteAnti", "NovoCmd",
	"AntiBan", "AntiKick", "AntiBypass",
	"Detector", "Verify", "Monitor",
	"Flag", "Report", "Suspect", "Suspicious",
	"Guardian", "Sentinel", "Watcher", "Observer",
}

-- SILENCIA O CONSOLE
local originalPrint = print
local originalWarn = warn

print = function(...)
	local args = {...}
	local msg = tostring(args[1] or "")
	for _, name in pairs(AntiCheatNames) do
		if msg:lower():find(name:lower()) then return end
	end
	local lowerMsg = msg:lower()
	if lowerMsg:find("anti") or lowerMsg:find("kick") or 
	   lowerMsg:find("detect") or lowerMsg:find("cheat") or
	   lowerMsg:find("fly") or lowerMsg:find("hitbox") or
	   lowerMsg:find("ac ") or lowerMsg:find("blocked") or
	   lowerMsg:find("flagged") or lowerMsg:find("suspicious") then
		return
	end
	originalPrint(...)
end

warn = function(...)
	local args = {...}
	local msg = tostring(args[1] or "")
	for _, name in pairs(AntiCheatNames) do
		if msg:lower():find(name:lower()) then return end
	end
	originalWarn(...)
end

-- DESATIVA SCRIPTS ANTI-CHEAT
local function DisableAntiCheatScripts(container)
	if not container then return end
	pcall(function()
		for _, obj in pairs(container:GetDescendants()) do
			if obj:IsA("Script") or obj:IsA("LocalScript") or obj:IsA("ModuleScript") then
				for _, name in pairs(AntiCheatNames) do
					if obj.Name:lower():find(name:lower()) then
						pcall(function() obj.Disabled = true end)
						break
					end
				end
			end
		end
	end)
end

DisableAntiCheatScripts(ReplicatedStorage)
pcall(function() DisableAntiCheatScripts(Player:WaitForChild("PlayerScripts")) end)
pcall(function() DisableAntiCheatScripts(game:GetService("StarterPlayer")) end)
pcall(function() DisableAntiCheatScripts(game:GetService("ServerScriptService")) end)

-- BLOQUEIA REMOTES DE ANTI-CHEAT
local function BlockRemote(remote)
	if not remote then return end
	pcall(function()
		if remote:IsA("RemoteEvent") then
			remote.OnClientEvent:Connect(function() end)
			local oldFire = remote.FireServer
			remote.FireServer = newcclosure(function(self, ...) return end)
			pcall(function()
				local oldFireClient = remote.FireClient
				remote.FireClient = newcclosure(function(self, ...) return end)
			end)
		elseif remote:IsA("RemoteFunction") then
			local oldInvoke = remote.InvokeServer
			remote.InvokeServer = newcclosure(function(self, ...) return nil end)
		end
	end)
end

local function BlockAntiCheatRemotes(container)
	if not container then return end
	pcall(function()
		for _, child in pairs(container:GetDescendants()) do
			if child:IsA("RemoteEvent") or child:IsA("RemoteFunction") then
				for _, name in pairs(AntiCheatNames) do
					if child.Name:lower():find(name:lower()) then
						BlockRemote(child)
						break
					end
				end
			end
		end
	end)
end

BlockAntiCheatRemotes(ReplicatedStorage)
pcall(function() BlockAntiCheatRemotes(Player:WaitForChild("PlayerGui")) end)
BlockAntiCheatRemotes(workspace)

-- BLOQUEIO DE ATRIBUTOS
local BlockedAttributes = {
	"EmFly", "BlockCam", "Fly", "Flying", "IsFlying", "UsingFly",
	"FlyDetected", "HitboxDetected", "Suspicious", "Flagged",
	"Cheat", "Hack", "AC", "Banned", "Kick", "AntiCheat", "ACFlag",
	"NoClip", "Noclip", "SpeedHack", "GodMode", "Hitbox",
}

spawn(function()
	while BypassEnabled do
		task.wait(0.5)
		pcall(function()
			for _, attr in pairs(BlockedAttributes) do
				if Player:GetAttribute(attr) ~= false then
					Player:SetAttribute(attr, false)
				end
			end
		end)
	end
end)

-- MONITORAMENTO CONTÍNUO
spawn(function()
	while BypassEnabled do
		task.wait(3)
		pcall(function()
			DisableAntiCheatScripts(ReplicatedStorage)
			DisableAntiCheatScripts(Player:WaitForChild("PlayerScripts"))
			DisableAntiCheatScripts(game:GetService("StarterPlayer"))
			BlockAntiCheatRemotes(ReplicatedStorage)
		end)
	end
end)

function EnableBypass()
	BypassEnabled = true
	DisableAntiCheatScripts(ReplicatedStorage)
	DisableAntiCheatScripts(Player:WaitForChild("PlayerScripts"))
	DisableAntiCheatScripts(game:GetService("StarterPlayer"))
	BlockAntiCheatRemotes(ReplicatedStorage)
	for _, attr in pairs(BlockedAttributes) do
		Player:SetAttribute(attr, false)
	end
	return true
end

function DisableBypass()
	BypassEnabled = false
	return true
end

-- CARREGA LIBRARY
local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
local ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
local SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()

local Options = Library.Options
local Toggles = Library.Toggles

Library.ForceCheckbox = false
Library.ShowToggleFrameInKeybinds = true

local Window = Library:CreateWindow({
	Title = "🏝️ Ilha Bela",
	Footer = "By: Theus | Beta",
	Icon = 95816097006870,
	NotifySide = "Right",
	ShowCustomCursor = true,
})

local Tabs = {
	Main = Window:AddTab("Main", "user"),
	Vehicles = Window:AddTab("Vehicles", "car-front"),
	AutoFarm = Window:AddTab("Auto Farm", "target"),
	Weapons = Window:AddTab("Weapons", "swords"),
	Visual = Window:AddTab("Visual", "eye"),
}

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.KeypadPlus or input.KeyCode == Enum.KeyCode.Equals then
		Window:Toggle()
	end
end)

-- VARIÁVEIS DE ESTADO
local FlyingModeEnabled = false
local FlyingModeSpeed = 50
local FlyingModeBodyGyro = nil
local FlyingModeBodyVelocity = nil
local FlyingModePreviousStates = {}

local AutoFarmRotasEnabled = false
local AutoFarmRotasLock = false
local AutoFarmRotasCurrentTarget = nil
local AutoFarmRotasCurrentIndex = 1
local AutoFarmRotasState = "idle"
local AutoFarmRotasTimer = 0
local AutoFarmRotasLastPrompt = 0
local AutoFarmRotasPromptInterval = 0.5

local FarmEmpilhadorEnabled = false
local FarmEmpilhadorLock = false
local FarmEmpilhadorStep = 1
local FarmEmpilhadorPointA = CFrame.new(2990, 7, 3142)
local FarmEmpilhadorPointB = CFrame.new(2950, 22, 3256)

-- ═══════════════════════════════════════════════════════
-- AUTO VARRER (AGORA COM TP DIRETO - SEM TWEEN)
-- ═══════════════════════════════════════════════════════
local AutoVarrerEnabled = false
local AutoVarrerLock = false
local AutoVarrerWaitTime = 7
local AutoVarrerNoclipEnabled = false

local AutoFarmCaixaEnabled = false
local AutoFarmCaixaLock = false
local CaixaPointA = Vector3.new(3894, 65, 4065)
local CaixaPointB = Vector3.new(3801, 65, 4122)
local CaixaTimeA = 2
local CaixaTimeB = 3

local AntiAFKEnabled = false
local AntiAFKConnection = nil

local AntiStaffEnabled = false
local AntiStaffLastCheck = 0
local AntiStaffCheckInterval = 60
local AutoCLEnabled = false

local VehiclesSavedPosition = nil
local VehiclesSavedCFrame = nil
local VisualSavedPosition = nil
local VisualSavedCFrame = nil

local AimbotEnabled = false
local AimbotFOV = 200
local AimbotSmoothing = 0
local AimbotTarget = "Head"
local IgnoreFriends = true
local IgnoreProtected = true

local ESPEnabled = false
local ESPTeams = false
local ESPTools = true
local ESPFriends = true
local ESPColor = Color3.fromRGB(255, 255, 255)

local HitboxEnabled = false
local HitboxSize = 5
local HitboxColor = Color3.fromRGB(255, 255, 0)
local HitboxTransparency = 0.6

local FOVCircle = nil
local FOVCircleGui = nil

-- ANTI AFK
local function StartAntiAFK()
	if AntiAFKConnection then AntiAFKConnection:Disconnect() AntiAFKConnection = nil end
	AntiAFKConnection = Player.Idled:Connect(function()
		local VirtualUser = game:GetService("VirtualUser")
		VirtualUser:CaptureController()
		VirtualUser:ClickButton2(Vector2.new())
	end)
	pcall(function()
		local StarterGui = game:GetService("StarterGui")
		StarterGui:SetCore("ResetButtonCallback", false)
	end)
end

local function StopAntiAFK()
	if AntiAFKConnection then AntiAFKConnection:Disconnect() AntiAFKConnection = nil end
end

-- AUTO FARM CAIXA
local function ExecuteAutoFarmCaixa()
	if not AutoFarmCaixaEnabled then return end
	if AutoFarmCaixaLock then return end
	AutoFarmCaixaLock = true
	
	pcall(function()
		if not Character or not Character:FindFirstChild("HumanoidRootPart") then
			AutoFarmCaixaLock = false
			return
		end
		
		Library:Notify({Title = "📦 Auto Farm Caixa", Description = "TP para Ponto A", Time = 1})
		Character.HumanoidRootPart.CFrame = CFrame.new(CaixaPointA)
		task.wait(0.1)
		
		local function ActivatePromptsNearPosition(position, radius)
			local count = 0
			for _, obj in pairs(workspace:GetDescendants()) do
				if obj:IsA("ProximityPrompt") then
					local parent = obj.Parent
					if parent and parent:IsA("BasePart") then
						if (parent.Position - position).Magnitude <= radius and obj.Enabled then
							pcall(function() fireproximityprompt(obj) count = count + 1 end)
						end
					end
				end
			end
			return count
		end
		
		local StartTime = tick()
		local TotalPrompts = 0
		while AutoFarmCaixaEnabled and (tick() - StartTime) < CaixaTimeA do
			TotalPrompts = TotalPrompts + ActivatePromptsNearPosition(CaixaPointA, 50)
			task.wait(0.05)
		end
		Library:Notify({Title = "📦 Auto Farm Caixa", Description = "Ponto A: " .. TotalPrompts .. " prompts", Time = 1})
		
		if not AutoFarmCaixaEnabled then AutoFarmCaixaLock = false return end
		
		Library:Notify({Title = "📦 Auto Farm Caixa", Description = "TP para Ponto B", Time = 1})
		if Character and Character:FindFirstChild("HumanoidRootPart") then
			Character.HumanoidRootPart.CFrame = CFrame.new(CaixaPointB)
			task.wait(0.1)
		end
		
		local StartTimeB = tick()
		local TotalPromptsB = 0
		while AutoFarmCaixaEnabled and (tick() - StartTimeB) < CaixaTimeB do
			TotalPromptsB = TotalPromptsB + ActivatePromptsNearPosition(CaixaPointB, 50)
			task.wait(0.05)
		end
		Library:Notify({Title = "📦 Auto Farm Caixa", Description = "Ponto B: " .. TotalPromptsB .. " prompts", Time = 1})
		task.wait(0.5)
	end)
	
	AutoFarmCaixaLock = false
end

-- ═══════════════════════════════════════════════════════
-- AUTO VARRER (COM TP DIRETO - SEM TWEEN)
-- ═══════════════════════════════════════════════════════
local function ExecuteAutoVarrer()
	if not AutoVarrerEnabled then return end
	if AutoVarrerLock then return end
	AutoVarrerLock = true
	
	pcall(function()
		-- Verifica a pasta
		local LocaisVarrer = workspace:FindFirstChild("LocaisVarrer")
		if not LocaisVarrer then
			Library:Notify({Title = "🧹 Auto Varrer", Description = "Pasta não encontrada!", Time = 3})
			AutoVarrerLock = false
			return
		end
		
		-- Coleta as parts
		local Parts = {}
		for _, obj in pairs(LocaisVarrer:GetChildren()) do
			if obj:IsA("BasePart") then 
				table.insert(Parts, obj) 
			end
		end
		
		if #Parts == 0 then
			Library:Notify({Title = "🧹 Auto Varrer", Description = "Nenhuma part!", Time = 3})
			AutoVarrerLock = false
			return
		end
		
		-- Verifica personagem
		if not Character or not Character:FindFirstChild("HumanoidRootPart") then
			AutoVarrerLock = false
			return
		end
		
		-- Escolhe uma part aleatória
		local RandomPart = Parts[math.random(1, #Parts)]
		
		Library:Notify({
			Title = "🧹 Auto Varrer",
			Description = "TP para: " .. RandomPart.Name,
			Time = 2,
		})
		
		-- ═══════════════════════════════════════════════
		-- TELEPORTE DIRETO (SEM TWEEN)
		-- ═══════════════════════════════════════════════
		local TargetPosition = RandomPart.Position + Vector3.new(0, 5, 0)
		Character.HumanoidRootPart.CFrame = CFrame.new(TargetPosition)
		
		-- Espera 0.1s para garantir o teleporte
		task.wait(0.1)
		
		-- Verifica se ainda está ativo
		if not AutoVarrerEnabled then
			AutoVarrerLock = false
			return
		end
		
		-- ═══════════════════════════════════════════════
		-- SIMULA O CLICK NA PART
		-- ═══════════════════════════════════════════════
		local Clicked = false
		
		pcall(function()
			-- 1. ClickDetector
			local ClickDetector = RandomPart:FindFirstChildOfClass("ClickDetector")
			if not ClickDetector then
				for _, child in pairs(RandomPart:GetDescendants()) do
					if child:IsA("ClickDetector") then
						ClickDetector = child
						break
					end
				end
			end
			
			if ClickDetector then
				fireclickdetector(ClickDetector)
				Clicked = true
				print("🖱️ ClickDetector: " .. RandomPart.Name)
			end
			
			-- 2. ProximityPrompt
			local ProximityPrompt = RandomPart:FindFirstChildOfClass("ProximityPrompt")
			if not ProximityPrompt then
				for _, child in pairs(RandomPart:GetDescendants()) do
					if child:IsA("ProximityPrompt") then
						ProximityPrompt = child
						break
					end
				end
			end
			
			if ProximityPrompt then
				fireproximityprompt(ProximityPrompt)
				Clicked = true
				print("🎯 ProximityPrompt: " .. RandomPart.Name)
			end
			
			-- 3. Tool (se tiver)
			local Tool = Character:FindFirstChildOfClass("Tool")
			if Tool then
				Tool:Activate()
				Clicked = true
				print("🔧 Tool: " .. Tool.Name)
			end
		end)
		
		if Clicked then
			Library:Notify({
				Title = "🧹 Auto Varrer",
				Description = "Clicou em: " .. RandomPart.Name .. " | Esperando " .. AutoVarrerWaitTime .. "s",
				Time = 2,
			})
		else
			Library:Notify({
				Title = "🧹 Auto Varrer",
				Description = "TP para: " .. RandomPart.Name .. " (sem click)",
				Time = 2,
			})
		end
		
		-- ═══════════════════════════════════════════════
		-- ESPERA 7 SEGUNDOS E REPETE
		-- ═══════════════════════════════════════════════
		task.wait(AutoVarrerWaitTime)
	end)
	
	AutoVarrerLock = false
end

-- NOCLIP FORTE
local function ApplyStrongNoclip()
	if not AutoVarrerNoclipEnabled then return end
	if not Character then return end
	pcall(function()
		for _, part in pairs(Character:GetDescendants()) do
			if part:IsA("BasePart") then part.CanCollide = false end
		end
	end)
end

-- FOV CIRCLE
local function CreateFOVCircle()
	if FOVCircleGui then FOVCircleGui:Destroy() FOVCircleGui = nil FOVCircle = nil end
	FOVCircleGui = Instance.new("ScreenGui")
	FOVCircleGui.Name = "FOVCircleGui"
	FOVCircleGui.Parent = game:GetService("CoreGui")
	FOVCircleGui.ResetOnSpawn = false
	FOVCircleGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
	FOVCircle = Instance.new("Frame")
	FOVCircle.Name = "FOVCircle"
	FOVCircle.Parent = FOVCircleGui
	FOVCircle.AnchorPoint = Vector2.new(0.5, 0.5)
	FOVCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
	FOVCircle.Size = UDim2.new(0, AimbotFOV * 2, 0, AimbotFOV * 2)
	FOVCircle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	FOVCircle.BackgroundTransparency = 1
	FOVCircle.BorderSizePixel = 0
	FOVCircle.Visible = AimbotEnabled
	local Stroke = Instance.new("UIStroke")
	Stroke.Parent = FOVCircle
	Stroke.Color = Color3.fromRGB(255, 0, 0)
	Stroke.Thickness = 2
	Stroke.Transparency = 0.5
	local Corner = Instance.new("UICorner")
	Corner.Parent = FOVCircle
	Corner.CornerRadius = UDim.new(1, 0)
end

local function UpdateFOVCircle()
	if FOVCircle then FOVCircle.Size = UDim2.new(0, AimbotFOV * 2, 0, AimbotFOV * 2) end
end

-- FUNÇÕES DE CARRO
local function IsCarLocked(Car)
	if Car:FindFirstChild("Locked") and Car.Locked.Value == true then return true end
	local Body = Car:FindFirstChild("Body")
	if Body then
		local PortaLocked = Body:FindFirstChild("PortaLocked")
		if PortaLocked and PortaLocked.Value == true then return true end
	end
	return false
end

local function IsCarOwner(Car)
	if Car:FindFirstChild("Owner") and Car.Owner:IsA("ObjectValue") and Car.Owner.Value == Player then return true end
	return false
end

local function GetVehiclesFolder()
	local World = workspace:FindFirstChild("World")
	if not World then return workspace:FindFirstChild("CarrosSpawnados") end
	return World:FindFirstChild("Veiculos") or World:FindFirstChild("CarrosSpawnados")
end

-- FLYING MODE
local function CreateFlyComponents()
	if FlyingModeBodyGyro then FlyingModeBodyGyro:Destroy() end
	if FlyingModeBodyVelocity then FlyingModeBodyVelocity:Destroy() end
	FlyingModeBodyGyro = Instance.new("BodyGyro")
	FlyingModeBodyGyro.P = 9e4
	FlyingModeBodyGyro.maxTorque = Vector3.new(9e9, 9e9, 9e9)
	FlyingModeBodyGyro.Parent = RootPart
	FlyingModeBodyVelocity = Instance.new("BodyVelocity")
	FlyingModeBodyVelocity.velocity = Vector3.new(0, 0.1, 0)
	FlyingModeBodyVelocity.maxForce = Vector3.new(9e9, 9e9, 9e9)
	FlyingModeBodyVelocity.Parent = RootPart
end

local function DestroyFlyComponents()
	if FlyingModeBodyGyro then FlyingModeBodyGyro:Destroy() FlyingModeBodyGyro = nil end
	if FlyingModeBodyVelocity then FlyingModeBodyVelocity:Destroy() FlyingModeBodyVelocity = nil end
end

local function ToggleFly()
	FlyingModeEnabled = not FlyingModeEnabled
	if FlyingModeEnabled then
		for _, State in pairs(Enum.HumanoidStateType:GetEnumItems()) do
			FlyingModePreviousStates[State] = Humanoid:GetStateEnabled(State)
			Humanoid:SetStateEnabled(State, false)
		end
		Humanoid:ChangeState(Enum.HumanoidStateType.Swimming)
		Humanoid.PlatformStand = true
		if Character:FindFirstChild("Animate") then Character.Animate.Disabled = true end
		CreateFlyComponents()
		Library:Notify({Title = "✈️ Fly", Description = "Flying mode enabled!", Time = 2})
	else
		for State, Enabled in pairs(FlyingModePreviousStates) do
			Humanoid:SetStateEnabled(State, Enabled)
		end
		table.clear(FlyingModePreviousStates)
		Humanoid.PlatformStand = false
		Humanoid:ChangeState(Enum.HumanoidStateType.Landed)
		if Character:FindFirstChild("Animate") then Character.Animate.Disabled = false end
		DestroyFlyComponents()
		Library:Notify({Title = "✈️ Fly", Description = "Flying mode disabled!", Time = 2})
	end
end

local function UpdateFly()
	if not FlyingModeEnabled then return end
	if not Character or not Character.Parent then FlyingModeEnabled = false DestroyFlyComponents() return end
	if not RootPart or not Humanoid then return end
	local CameraLook = Camera.CFrame.LookVector
	local CameraRight = Camera.CFrame.RightVector
	local CameraUp = Camera.CFrame.UpVector
	local MoveDirection = Humanoid.MoveDirection
	FlyingModeBodyGyro.cframe = Camera.CFrame
	if MoveDirection.Magnitude == 0 then FlyingModeBodyVelocity.velocity = Vector3.new(0, 0.1, 0) return end
	local Forward = Vector3.new(CameraLook.X, 0, CameraLook.Z).Unit
	local Right = Vector3.new(CameraRight.X, 0, CameraRight.Z).Unit
	local HorizontalMove = (Forward * -MoveDirection.Z) + (Right * MoveDirection.X)
	local VerticalMove = CameraUp * MoveDirection.Y
	local MoveVector = HorizontalMove + VerticalMove
	if MoveVector.Magnitude > 0 then
		FlyingModeBodyVelocity.velocity = MoveVector.Unit * FlyingModeSpeed
	else
		FlyingModeBodyVelocity.velocity = Vector3.new(0, 0.1, 0)
	end
end

-- PULL CAR
local function PullCarAndEnter(Car)
	if not Car then return false end
	local VehicleSeat = Car:FindFirstChildOfClass("VehicleSeat")
	if not VehicleSeat then return false end
	if not Character or not Character:FindFirstChild("HumanoidRootPart") then return false end
	local Welds = {}
	for _, Part in pairs(Car:GetDescendants()) do
		if Part:IsA("BasePart") and Part ~= VehicleSeat then
			local Weld = Instance.new("Weld")
			Weld.Part0 = VehicleSeat
			Weld.Part1 = Part
			Weld.C0 = VehicleSeat.CFrame:Inverse() * Part.CFrame
			Weld.Parent = VehicleSeat
			table.insert(Welds, Weld)
		end
	end
	local TargetPos = Character.HumanoidRootPart.Position + Vector3.new(0, 5, 0)
	local Tween = TweenService:Create(VehicleSeat, TweenInfo.new(1.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
		CFrame = CFrame.new(TargetPos) * CFrame.Angles(0, math.rad(VehicleSeat.Orientation.Y), 0)
	})
	Tween:Play()
	Tween.Completed:Wait()
	for _, Weld in pairs(Welds) do Weld:Destroy() end
	VehicleSeat:Sit(Humanoid)
	task.wait(0.5)
	return true
end

local function GetAvailableCarForFarm()
	local Veiculos = GetVehiclesFolder()
	if not Veiculos then return nil end
	for _, Car in pairs(Veiculos:GetChildren()) do
		if Car:IsA("Model") then
			local VehicleSeat = Car:FindFirstChildOfClass("VehicleSeat")
			local IsLocked = IsCarLocked(Car)
			local IsOwner = IsCarOwner(Car)
			if VehicleSeat and not VehicleSeat.Occupant then
				if IsOwner or not IsLocked then return Car end
			end
		end
	end
	return nil
end

local function TeleportCarToDestination(Car, DestinationCFrame)
	if not Car or not DestinationCFrame then return false end
	local VehicleSeat = Car:FindFirstChildOfClass("VehicleSeat")
	if not VehicleSeat then return false end
	local Welds = {}
	for _, Part in pairs(Car:GetDescendants()) do
		if Part:IsA("BasePart") and Part ~= VehicleSeat then
			local Weld = Instance.new("Weld")
			Weld.Part0 = VehicleSeat
			Weld.Part1 = Part
			Weld.C0 = VehicleSeat.CFrame:Inverse() * Part.CFrame
			Weld.Parent = VehicleSeat
			table.insert(Welds, Weld)
		end
	end
	VehicleSeat.CFrame = DestinationCFrame * CFrame.Angles(0, math.rad(VehicleSeat.Orientation.Y), 0)
	for _, Weld in pairs(Welds) do Weld:Destroy() end
	return true
end

local function TeleportAndInteract(Car, DestinationCFrame, PromptPart)
	if not Car or not DestinationCFrame then return false end
	local VehicleSeat = Car:FindFirstChildOfClass("VehicleSeat")
	if not VehicleSeat then return false end
	local Welds = {}
	for _, Part in pairs(Car:GetDescendants()) do
		if Part:IsA("BasePart") and Part ~= VehicleSeat then
			local Weld = Instance.new("Weld")
			Weld.Part0 = VehicleSeat
			Weld.Part1 = Part
			Weld.C0 = VehicleSeat.CFrame:Inverse() * Part.CFrame
			Weld.Parent = VehicleSeat
			table.insert(Welds, Weld)
		end
	end
	VehicleSeat.CFrame = DestinationCFrame * CFrame.Angles(0, math.rad(VehicleSeat.Orientation.Y), 0)
	for _, Weld in pairs(Welds) do Weld:Destroy() end
	if PromptPart then
		task.wait(0.05)
		fireproximityprompt(PromptPart)
	end
	return true
end

-- AUTO FARM ROTAS
local function ExecuteAutoFarmRotas()
	if not AutoFarmRotasEnabled then
		AutoFarmRotasCurrentTarget = nil
		AutoFarmRotasCurrentIndex = 1
		AutoFarmRotasState = "idle"
		AutoFarmRotasTimer = 0
		AutoFarmRotasLastPrompt = 0
		return
	end
	if AutoFarmRotasLock then return end
	AutoFarmRotasLock = true
	pcall(function()
		if not Humanoid.SeatPart or not Humanoid.SeatPart:IsA("VehicleSeat") then
			local Car = GetAvailableCarForFarm()
			if Car then PullCarAndEnter(Car) else AutoFarmRotasLock = false return end
		end
		if not Humanoid.SeatPart or not Humanoid.SeatPart:IsA("VehicleSeat") then AutoFarmRotasLock = false return end
		local CurrentVehicleSeat = Humanoid.SeatPart
		local CurrentCar = CurrentVehicleSeat:FindFirstAncestorOfClass("Model")
		if not CurrentCar then AutoFarmRotasLock = false return end
		local Construcoes = workspace:FindFirstChild("Construcoes")
		if not Construcoes then AutoFarmRotasLock = false return end
		local LocaisRotas = Construcoes:FindFirstChild("LocaisRotas")
		if not LocaisRotas then AutoFarmRotasLock = false return end
		local TargetLocation, TargetCFrame, TargetPrompt = nil, nil, nil
		for i = AutoFarmRotasCurrentIndex, 15 do
			local Location = LocaisRotas:FindFirstChild("Local" .. i)
			if Location then
				local LocalMarcado = Location:FindFirstChild("LocalMarcado")
				if LocalMarcado and LocalMarcado:IsA("BasePart") then
					TargetLocation = Location
					TargetCFrame = LocalMarcado.CFrame
					local Prompt = LocalMarcado:FindFirstChildOfClass("ProximityPrompt")
					if not Prompt then Prompt = Location:FindFirstChildOfClass("ProximityPrompt") end
					if not Prompt then
						for _, Desc in pairs(Location:GetDescendants()) do
							if Desc:IsA("ProximityPrompt") then Prompt = Desc break end
						end
					end
					TargetPrompt = Prompt
					AutoFarmRotasCurrentIndex = i
					break
				end
			end
		end
		if not TargetLocation or not TargetCFrame then
			AutoFarmRotasCurrentIndex = 1
			AutoFarmRotasLock = false
			return
		end
		if AutoFarmRotasState == "idle" then
			AutoFarmRotasState = "waiting_tp"
			AutoFarmRotasTimer = os.time() + 7
		elseif AutoFarmRotasState == "waiting_tp" then
			if os.time() >= AutoFarmRotasTimer then
				TeleportAndInteract(CurrentCar, TargetCFrame, TargetPrompt)
				CurrentVehicleSeat.CFrame = TargetCFrame
				AutoFarmRotasState = "waiting_prompt"
				AutoFarmRotasTimer = os.time() + 14
				AutoFarmRotasLastPrompt = os.time()
				Library:Notify({Title = "🔄 Auto Farm Rotas", Description = "TP: " .. TargetLocation.Name, Time = 2})
			end
		elseif AutoFarmRotasState == "waiting_prompt" then
			if TargetPrompt and os.time() - AutoFarmRotasLastPrompt >= AutoFarmRotasPromptInterval then
				fireproximityprompt(TargetPrompt)
				AutoFarmRotasLastPrompt = os.time()
			end
			if os.time() >= AutoFarmRotasTimer then
				AutoFarmRotasCurrentIndex = AutoFarmRotasCurrentIndex + 1
				if AutoFarmRotasCurrentIndex > 15 then AutoFarmRotasCurrentIndex = 1 end
				AutoFarmRotasState = "idle"
				AutoFarmRotasLastPrompt = 0
			end
		end
	end)
	AutoFarmRotasLock = false
end

-- FARM EMPILHADOR
local function ExecuteFarmEmpilhador()
	if not FarmEmpilhadorEnabled then FarmEmpilhadorStep = 1 return end
	if FarmEmpilhadorLock then return end
	FarmEmpilhadorLock = true
	pcall(function()
		if not Humanoid.SeatPart or not Humanoid.SeatPart:IsA("VehicleSeat") then
			local Car = GetAvailableCarForFarm()
			if Car then PullCarAndEnter(Car) else FarmEmpilhadorLock = false return end
		end
		if not Humanoid.SeatPart or not Humanoid.SeatPart:IsA("VehicleSeat") then FarmEmpilhadorLock = false return end
		local CurrentVehicleSeat = Humanoid.SeatPart
		local CurrentCar = CurrentVehicleSeat:FindFirstAncestorOfClass("Model")
		if not CurrentCar then FarmEmpilhadorLock = false return end
		local CurrentPos = CurrentVehicleSeat.Position
		if FarmEmpilhadorStep == 1 then
			local Distance = (CurrentPos - FarmEmpilhadorPointA.Position).Magnitude
			if Distance < 10 then
				task.wait(2)
				FarmEmpilhadorStep = 2
			else
				TeleportCarToDestination(CurrentCar, FarmEmpilhadorPointA)
				CurrentVehicleSeat.CFrame = FarmEmpilhadorPointA
			end
		elseif FarmEmpilhadorStep == 2 then
			local Distance = (CurrentPos - FarmEmpilhadorPointB.Position).Magnitude
			if Distance < 10 then
				task.wait(14)
				FarmEmpilhadorStep = 1
			else
				TeleportCarToDestination(CurrentCar, FarmEmpilhadorPointB)
				CurrentVehicleSeat.CFrame = FarmEmpilhadorPointB
			end
		end
	end)
	FarmEmpilhadorLock = false
end

-- ANTI-STAFF
local function ExecuteAntiStaff()
	if not AntiStaffEnabled then return end
	local CurrentTime = os.time()
	if CurrentTime - AntiStaffLastCheck < AntiStaffCheckInterval then return end
	AntiStaffLastCheck = CurrentTime
	pcall(function()
		local StaffTeam = game:GetService("Teams"):FindFirstChild("BIB | STAFF")
		if not StaffTeam then return end
		for _, StaffPlayer in pairs(StaffTeam:GetPlayers()) do
			if StaffPlayer ~= Player and StaffPlayer.Character and StaffPlayer.Character:FindFirstChild("HumanoidRootPart") then
				local Distance = (RootPart.Position - StaffPlayer.Character.HumanoidRootPart.Position).Magnitude
				if Distance <= 80 then
					Library:Notify({Title = "🛡️ Anti-Staff", Description = "Staff detectado!", Time = 3, Icon = "shield"})
					task.wait(1)
					game:Shutdown()
					return
				end
			end
		end
	end)
end

-- AUTO CL
local function ExecuteAutoCL()
	if not AutoCLEnabled then return end
	pcall(function()
		if Player.PlayerGui:FindFirstChild("TelaMorte") and Player.PlayerGui.TelaMorte.Enabled then
			game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, Player)
		end
	end)
end

-- SALVAR POSIÇÃO
local function SavePositionVehicles()
	pcall(function()
		if not Character or not Character:FindFirstChild("HumanoidRootPart") then
			Library:Notify({Title = "📌 Salvar", Description = "Personagem não encontrado!", Time = 2})
			return
		end
		VehiclesSavedPosition = RootPart.Position
		VehiclesSavedCFrame = RootPart.CFrame
		Library:Notify({Title = "📌 Salvar", Description = "Salvo!", Time = 3})
	end)
end

local function TeleportToSavedPositionVehicles()
	pcall(function()
		if not VehiclesSavedPosition or not VehiclesSavedCFrame then
			Library:Notify({Title = "📌 TP", Description = "Nenhuma posição salva!", Time = 3})
			return
		end
		if not Humanoid.SeatPart or not Humanoid.SeatPart:IsA("VehicleSeat") then
			Library:Notify({Title = "📌 TP", Description = "Precisa estar em um carro!", Time = 2})
			return
		end
		local CurrentVehicleSeat = Humanoid.SeatPart
		local CurrentCar = CurrentVehicleSeat:FindFirstAncestorOfClass("Model")
		if not CurrentCar then return end
		TeleportCarToDestination(CurrentCar, VehiclesSavedCFrame)
		CurrentVehicleSeat.CFrame = VehiclesSavedCFrame
		Library:Notify({Title = "📌 TP", Description = "Teleportado!", Time = 2})
	end)
end

local function SavePositionVisual()
	pcall(function()
		if not Character or not Character:FindFirstChild("HumanoidRootPart") then
			Library:Notify({Title = "📌 Salvar", Description = "Personagem não encontrado!", Time = 2})
			return
		end
		VisualSavedPosition = RootPart.Position
		VisualSavedCFrame = RootPart.CFrame
		Library:Notify({Title = "📌 Salvar", Description = "Salvo!", Time = 3})
	end)
end

local function TeleportToSavedPositionVisual()
	pcall(function()
		if not VisualSavedPosition or not VisualSavedCFrame then
			Library:Notify({Title = "📌 TP", Description = "Nenhuma posição salva!", Time = 3})
			return
		end
		if not Humanoid.SeatPart or not Humanoid.SeatPart:IsA("VehicleSeat") then
			Library:Notify({Title = "📌 TP", Description = "Precisa estar em um carro!", Time = 2})
			return
		end
		local CurrentVehicleSeat = Humanoid.SeatPart
		local CurrentCar = CurrentVehicleSeat:FindFirstAncestorOfClass("Model")
		if not CurrentCar then return end
		TeleportCarToDestination(CurrentCar, VisualSavedCFrame)
		CurrentVehicleSeat.CFrame = VisualSavedCFrame
		Library:Notify({Title = "📌 TP", Description = "Teleportado!", Time = 2})
	end)
end

-- AIMBOT
local AimbotTargetCharacter = nil
local AimbotTargetPart = nil

local function ExecuteAimbot()
	if not AimbotEnabled then AimbotTargetCharacter = nil AimbotTargetPart = nil return end
	if not Character or not Character:FindFirstChild("HumanoidRootPart") then return end
	local ClosestPlayer, ClosestDistance, ClosestPart = nil, math.huge, nil
	for _, TargetPlayer in pairs(game.Players:GetPlayers()) do
		if TargetPlayer == Player then continue end
		if IgnoreFriends and Player:IsFriendsWith(TargetPlayer.UserId) then continue end
		local TargetChar = TargetPlayer.Character
		if not TargetChar or not TargetChar:FindFirstChild("HumanoidRootPart") then continue end
		if IgnoreProtected and TargetChar:FindFirstChild("AreaSafe", true) then continue end
		local TargetHumanoid = TargetChar:FindFirstChild("Humanoid")
		if not TargetHumanoid or TargetHumanoid.Health <= 0 then continue end
		local TargetPart
		if AimbotTarget == "Head" then TargetPart = TargetChar:FindFirstChild("Head")
		elseif AimbotTarget == "HumanoidRootPart" then TargetPart = TargetChar:FindFirstChild("HumanoidRootPart")
		elseif AimbotTarget == "Torso" then TargetPart = TargetChar:FindFirstChild("Torso") or TargetChar:FindFirstChild("UpperTorso") end
		if not TargetPart then TargetPart = TargetChar:FindFirstChild("HumanoidRootPart") end
		if not TargetPart then continue end
		local ScreenPos, OnScreen = Camera:WorldToViewportPoint(TargetPart.Position)
		if not OnScreen then continue end
		local ScreenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
		local DistanceFromCenter = (Vector2.new(ScreenPos.X, ScreenPos.Y) - ScreenCenter).Magnitude
		if DistanceFromCenter > AimbotFOV then continue end
		if DistanceFromCenter < ClosestDistance then
			ClosestDistance = DistanceFromCenter
			ClosestPlayer = TargetPlayer
			ClosestPart = TargetPart
		end
	end
	if ClosestPlayer and ClosestPart then
		AimbotTargetCharacter = ClosestPlayer
		AimbotTargetPart = ClosestPart
		local CameraCFrame = CFrame.new(Camera.CFrame.Position, ClosestPart.Position)
		if AimbotSmoothing > 0 then
			Camera.CFrame = Camera.CFrame:Lerp(CameraCFrame, AimbotSmoothing)
		else
			Camera.CFrame = CameraCFrame
		end
	else
		AimbotTargetCharacter = nil
		AimbotTargetPart = nil
	end
end

-- HITBOX
local function ApplyChestHitbox(targetPlayer)
	if targetPlayer == Player then return end
	local function UpdateHitbox()
		local char = targetPlayer.Character
		if char then
			local torso = char:FindFirstChild("UpperTorso") or char:FindFirstChild("Torso")
			if torso then
				local hitbox = char:FindFirstChild("RealChestHitbox")
				if not hitbox then
					hitbox = Instance.new("Part")
					hitbox.Name = "RealChestHitbox"
					hitbox.Transparency = HitboxTransparency
					hitbox.Color = HitboxColor
					hitbox.Material = Enum.Material.Neon
					hitbox.CanCollide = false
					hitbox.Anchored = true
					hitbox.Massless = true
					hitbox.Parent = char
				end
				if HitboxEnabled then
					hitbox.Size = Vector3.new(HitboxSize, HitboxSize, HitboxSize)
					hitbox.CFrame = torso.CFrame
					hitbox.Transparency = HitboxTransparency
					hitbox.Color = HitboxColor
					hitbox.CanCollide = false
					hitbox.Anchored = true
				else
					if hitbox then hitbox:Destroy() end
				end
			end
		end
	end
	game:GetService("RunService").Heartbeat:Connect(UpdateHitbox)
end

for _, p in ipairs(game.Players:GetPlayers()) do ApplyChestHitbox(p) end
game.Players.PlayerAdded:Connect(ApplyChestHitbox)

-- ESP
local ESPObjects = {}

local function CreateESP(TargetPlayer)
	if not TargetPlayer or not TargetPlayer.Character then return end
	local TargetChar = TargetPlayer.Character
	local Head = TargetChar:FindFirstChild("Head")
	if not Head then return end
	if ESPObjects[TargetPlayer.UserId] then
		if ESPObjects[TargetPlayer.UserId].Billboard then ESPObjects[TargetPlayer.UserId].Billboard:Destroy() end
		if ESPObjects[TargetPlayer.UserId].Highlight then ESPObjects[TargetPlayer.UserId].Highlight:Destroy() end
		ESPObjects[TargetPlayer.UserId] = nil
	end
	if not ESPEnabled then return end
	local Billboard = Instance.new("BillboardGui")
	Billboard.Name, Billboard.Parent, Billboard.Adornee = "ESP", Head, Head
	Billboard.Size, Billboard.StudsOffset = UDim2.new(0, 200, 0, 50), Vector3.new(0, 3, 0)
	Billboard.AlwaysOnTop, Billboard.ResetOnSpawn = true, false
	Billboard.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
	local NameLabel = Instance.new("TextLabel")
	NameLabel.Name, NameLabel.Parent = "Name", Billboard
	NameLabel.Size, NameLabel.Position = UDim2.new(1, 0, 0.33, 0), UDim2.new(0, 0, 0, 0)
	NameLabel.BackgroundTransparency, NameLabel.Text = 1, TargetPlayer.Name
	NameLabel.TextColor3, NameLabel.TextSize = ESPColor, 14
	NameLabel.Font = Enum.Font.GothamBold
	NameLabel.TextStrokeTransparency, NameLabel.TextStrokeColor3 = 0.3, Color3.fromRGB(0, 0, 0)
	local InfoLabel = Instance.new("TextLabel")
	InfoLabel.Name, InfoLabel.Parent = "Info", Billboard
	InfoLabel.Size, InfoLabel.Position = UDim2.new(1, 0, 0.33, 0), UDim2.new(0, 0, 0.33, 0)
	InfoLabel.BackgroundTransparency, InfoLabel.Text = 1, ""
	InfoLabel.TextColor3, InfoLabel.TextSize = Color3.fromRGB(255, 255, 255), 12
	InfoLabel.Font = Enum.Font.Gotham
	InfoLabel.TextStrokeTransparency, InfoLabel.TextStrokeColor3 = 0.3, Color3.fromRGB(0, 0, 0)
	local FriendLabel = Instance.new("TextLabel")
	FriendLabel.Name, FriendLabel.Parent = "Friend", Billboard
	FriendLabel.Size, FriendLabel.Position = UDim2.new(1, 0, 0.33, 0), UDim2.new(0, 0, 0.66, 0)
	FriendLabel.BackgroundTransparency, FriendLabel.Text = 1, ""
	FriendLabel.TextColor3, FriendLabel.TextSize = Color3.fromRGB(0, 150, 255), 12
	FriendLabel.Font = Enum.Font.Gotham
	FriendLabel.TextStrokeTransparency, FriendLabel.TextStrokeColor3 = 0.3, Color3.fromRGB(0, 0, 0)
	local Highlight = Instance.new("Highlight")
	Highlight.Name, Highlight.Parent = "ESP_Highlight", TargetChar
	Highlight.FillColor, Highlight.FillTransparency = ESPColor, 0.5
	Highlight.OutlineColor, Highlight.OutlineTransparency = ESPColor, 0
	Highlight.Adornee = TargetChar
	ESPObjects[TargetPlayer.UserId] = {Billboard = Billboard, Highlight = Highlight, NameLabel = NameLabel, InfoLabel = InfoLabel, FriendLabel = FriendLabel}
end

local function UpdateESP()
	if not ESPEnabled then
		for UserId, Data in pairs(ESPObjects) do
			if Data.Billboard then Data.Billboard:Destroy() end
			if Data.Highlight then Data.Highlight:Destroy() end
		end
		table.clear(ESPObjects)
		return
	end
	for _, TargetPlayer in pairs(game.Players:GetPlayers()) do
		if TargetPlayer == Player then continue end
		if not TargetPlayer.Character or not TargetPlayer.Character:FindFirstChild("Head") then
			if ESPObjects[TargetPlayer.UserId] then
				if ESPObjects[TargetPlayer.UserId].Billboard then ESPObjects[TargetPlayer.UserId].Billboard:Destroy() end
				if ESPObjects[TargetPlayer.UserId].Highlight then ESPObjects[TargetPlayer.UserId].Highlight:Destroy() end
				ESPObjects[TargetPlayer.UserId] = nil
			end
			continue
		end
		if not ESPObjects[TargetPlayer.UserId] then CreateESP(TargetPlayer) end
		local Data = ESPObjects[TargetPlayer.UserId]
		if Data and Data.InfoLabel and Data.FriendLabel then
			local TargetChar = TargetPlayer.Character
			local Distance = math.floor((RootPart.Position - TargetChar.HumanoidRootPart.Position).Magnitude)
			local TeamName = TargetPlayer.Team and TargetPlayer.Team.Name or "None"
			local TeamColor = TargetPlayer.Team and TargetPlayer.Team.TeamColor and TargetPlayer.Team.TeamColor.Color or Color3.fromRGB(255, 255, 255)
			local Tool = TargetChar:FindFirstChildOfClass("Tool")
			local ToolName = Tool and Tool.Name or "None"
			local InfoText = "[" .. Distance .. "M]"
			if ESPTeams and TargetPlayer.Team then
				InfoText = "[" .. TeamName .. "] " .. InfoText
				Data.InfoLabel.TextColor3 = TeamColor
			end
			if ESPTools then InfoText = "[" .. ToolName .. "] " .. InfoText end
			Data.InfoLabel.Text = InfoText
			if ESPFriends and Player:IsFriendsWith(TargetPlayer.UserId) then
				Data.FriendLabel.Text = "[Roblox Friend]"
			else
				Data.FriendLabel.Text = ""
			end
			if Data.Highlight then
				Data.Highlight.FillColor = ESPColor
				Data.Highlight.OutlineColor = ESPColor
			end
		end
	end
end

-- EVENTOS
Player.CharacterAdded:Connect(function(newChar)
	Character = newChar
	Humanoid = Character:WaitForChild("Humanoid")
	RootPart = Character:WaitForChild("HumanoidRootPart")
	if FlyingModeEnabled then
		DestroyFlyComponents()
		FlyingModeEnabled = false
	end
end)

game:GetService("RunService").Heartbeat:Connect(function()
	pcall(ExecuteAutoFarmRotas)
	pcall(ExecuteFarmEmpilhador)
	pcall(ExecuteAntiStaff)
	pcall(ExecuteAutoCL)
	pcall(ExecuteAimbot)
	pcall(UpdateESP)
	pcall(ExecuteAutoVarrer)
	pcall(ExecuteAutoFarmCaixa)
	pcall(ApplyStrongNoclip)
end)

game:GetService("RunService").RenderStepped:Connect(function()
	pcall(UpdateFly)
end)

-- ═══════════════════════════════════════════════════════
-- UI - ABA MAIN
-- ═══════════════════════════════════════════════════════
local MainGroup = Tabs.Main:AddGroupbox({Side = "Left", Name = "Movement", Description = "Movement hacks", IconName = "move"})

MainGroup:AddToggle("FlyingMode", {Text = "✈️ Fly (risco de kick ou ban)", Default = false, Callback = function(Value) ToggleFly() end})
MainGroup:AddSlider("FlyingSpeed", {Text = "Flying Speed", Default = 50, Min = 5, Max = 300, Rounding = 0, Suffix = " studs/s", Callback = function(Value) FlyingModeSpeed = Value end})
MainGroup:AddLabel("Flying Keybind"):AddKeyPicker("FlyKeybind", {Default = "F", Mode = "Toggle", Text = "Flying Mode Keybind", Callback = function(Value) if Value then ToggleFly() Toggles.FlyingMode:SetValue(FlyingModeEnabled) end end})

MainGroup:AddDivider()
MainGroup:AddLabel("🚀 Speed Hack")
MainGroup:AddToggle("SpeedHack", {Text = "Speed Hack", Default = false, Callback = function(Value)
	local c = Player.Character
	if c and c:FindFirstChild("Humanoid") then c.Humanoid.WalkSpeed = Value and 50 or 16 end
end})
MainGroup:AddSlider("SpeedAmount", {Text = "Speed Amount", Default = 50, Min = 20, Max = 250, Rounding = 0, Suffix = " studs/s", Callback = function(Value)
	if Toggles.SpeedHack.Value then
		local c = Player.Character
		if c and c:FindFirstChild("Humanoid") then c.Humanoid.WalkSpeed = Value end
	end
end})

MainGroup:AddDivider()
MainGroup:AddLabel("🦘 Jump Hack")
MainGroup:AddToggle("JumpHack", {Text = "Jump Hack", Default = false, Callback = function(Value)
	local c = Player.Character
	if c and c:FindFirstChild("Humanoid") then c.Humanoid.JumpPower = Value and 100 or 50 end
end})

MainGroup:AddDivider()
MainGroup:AddLabel("🧱 No Clip")
MainGroup:AddToggle("NoClip", {Text = "No Clip", Default = false, Callback = function(Value)
	local c = Player.Character
	if c then
		for _, Part in pairs(c:GetDescendants()) do
			if Part:IsA("BasePart") then Part.CanCollide = not Value end
		end
	end
end})

-- ═══════════════════════════════════════════════════════
-- UI - ABA VEHICLES
-- ═══════════════════════════════════════════════════════
local VehiclesGroup = Tabs.Vehicles:AddGroupbox({Side = "Left", Name = "Vehicles", Description = "Vehicle controls", IconName = "car-front"})

VehiclesGroup:AddLabel("📌 Salvar Posição (Vehicles)")
VehiclesGroup:AddButton({Text = "📌 Salvar Posição Atual", Func = function() SavePositionVehicles() end})
VehiclesGroup:AddButton({Text = "🚗 TP para Posição Salva", Func = function() TeleportToSavedPositionVehicles() end})

VehiclesGroup:AddDivider()
VehiclesGroup:AddToggle("AntiStaff", {Text = "🛡️ Anti-Staff (KICK)", Default = false, Callback = function(Value)
	AntiStaffEnabled = Value
	if Value then AntiStaffLastCheck = 0 end
	Library:Notify({Title = "🛡️ Anti-Staff", Description = Value and "Enabled!" or "Disabled!", Time = 2})
end})

VehiclesGroup:AddToggle("AutoCL", {Text = "🔄 Auto CL", Default = false, Callback = function(Value)
	AutoCLEnabled = Value
	Library:Notify({Title = "🔄 Auto CL", Description = Value and "Enabled!" or "Disabled!", Time = 2})
end})

-- ═══════════════════════════════════════════════════════
-- UI - ABA AUTO FARM
-- ═══════════════════════════════════════════════════════
local AutoFarmGroup = Tabs.AutoFarm:AddGroupbox({Side = "Left", Name = "Auto Farm", Description = "Auto farm functions", IconName = "target"})

AutoFarmGroup:AddToggle("AntiAFK", {
	Text = "🛡️ Anti AFK",
	Tooltip = "Impede kick por inatividade",
	Default = false,
	Callback = function(Value)
		AntiAFKEnabled = Value
		if Value then
			StartAntiAFK()
			Library:Notify({Title = "🛡️ Anti AFK", Description = "Ativado!", Time = 2})
		else
			StopAntiAFK()
			Library:Notify({Title = "🛡️ Anti AFK", Description = "Desativado!", Time = 2})
		end
	end,
})

AutoFarmGroup:AddDivider()

AutoFarmGroup:AddToggle("AutoFarmCaixa", {
	Text = "📦 Auto Farm Caixa",
	Tooltip = "Loop:\n1. TP (3894, 65, 4065)\n2. Prompts por 2s\n3. TP (3801, 65, 4122)\n4. Prompts por 3s",
	Default = false,
	Callback = function(Value)
		AutoFarmCaixaEnabled = Value
		if Value then
			Library:Notify({Title = "📦 Auto Farm Caixa", Description = "Ativado!", Time = 2})
		else
			AutoFarmCaixaLock = false
			Library:Notify({Title = "📦 Auto Farm Caixa", Description = "Desativado!", Time = 2})
		end
	end,
})

AutoFarmGroup:AddDivider()

-- AUTO VARRER (AGORA COM TP DIRETO)
AutoFarmGroup:AddToggle("AutoVarrer", {
	Text = "🧹 Auto Varrer (TP Direto)",
	Tooltip = "Varre parts de workspace.LocaisVarrer\nTP direto → clica → espera 7s → repete\nAtiva Noclip Forte!",
	Default = false,
	Callback = function(Value)
		AutoVarrerEnabled = Value
		AutoVarrerNoclipEnabled = Value
		if Value then
			Library:Notify({Title = "🧹 Auto Varrer", Description = "Ativado! (TP Direto + Noclip Forte)", Time = 2})
		else
			AutoVarrerLock = false
			Library:Notify({Title = "🧹 Auto Varrer", Description = "Desativado!", Time = 2})
		end
	end,
})

AutoFarmGroup:AddSlider("AutoVarrerWait", {Text = "Tempo de Espera", Default = 7, Min = 1, Max = 30, Rounding = 0, Suffix = " s", Callback = function(Value) AutoVarrerWaitTime = Value end})

AutoFarmGroup:AddButton({
	Text = "🔍 Verificar LocaisVarrer",
	Func = function()
		local LocaisVarrer = workspace:FindFirstChild("LocaisVarrer")
		if LocaisVarrer then
			local count = 0
			for _, obj in pairs(LocaisVarrer:GetChildren()) do
				if obj:IsA("BasePart") then count = count + 1 end
			end
			Library:Notify({Title = "🔍 LocaisVarrer", Description = "Encontradas " .. count .. " parts!", Time = 3})
		else
			Library:Notify({Title = "🔍 LocaisVarrer", Description = "Pasta não encontrada!", Time = 3})
		end
	end,
})

AutoFarmGroup:AddDivider()

AutoFarmGroup:AddToggle("AutoFarmRotas", {
	Text = "🔄 Auto Farm Rotas",
	Default = false,
	Callback = function(Value)
		AutoFarmRotasEnabled = Value
		AutoFarmRotasCurrentIndex = 1
		AutoFarmRotasState = "idle"
		AutoFarmRotasTimer = 0
		AutoFarmRotasLastPrompt = 0
		Library:Notify({Title = "🔄 Auto Farm Rotas", Description = Value and "Enabled!" or "Disabled!", Time = 2})
	end,
})

AutoFarmGroup:AddToggle("FarmEmpilhador", {
	Text = "🏗️ Farm Empilhador",
	Default = false,
	Callback = function(Value)
		FarmEmpilhadorEnabled = Value
		FarmEmpilhadorStep = 1
		Library:Notify({Title = "🏗️ Farm Empilhador", Description = Value and "Enabled!" or "Disabled!", Time = 2})
	end,
})

-- ═══════════════════════════════════════════════════════
-- UI - ABA WEAPONS
-- ═══════════════════════════════════════════════════════
local WeaponsGroup = Tabs.Weapons:AddGroupbox({Side = "Left", Name = "Aimbot", Description = "Aimbot settings", IconName = "crosshair"})

WeaponsGroup:AddToggle("Aimbot", {Text = "🎯 Enable Aimbot", Default = false, Callback = function(Value)
	AimbotEnabled = Value
	if FOVCircle then FOVCircle.Visible = Value end
	Library:Notify({Title = "🎯 Aimbot", Description = Value and "Enabled!" or "Disabled!", Time = 2})
end})

WeaponsGroup:AddDropdown("AimbotTarget", {Values = {"Head", "HumanoidRootPart", "Torso"}, Default = 1, Multi = false, Text = "🎯 Target", Callback = function(Value) AimbotTarget = Value end})
WeaponsGroup:AddSlider("AimbotFOV", {Text = "FOV Radius", Default = 200, Min = 50, Max = 500, Rounding = 0, Suffix = "px", Callback = function(Value) AimbotFOV = Value UpdateFOVCircle() end})
WeaponsGroup:AddSlider("AimbotSmoothing", {Text = "Smoothing", Default = 0, Min = 0, Max = 100, Rounding = 0, Suffix = "%", Callback = function(Value) AimbotSmoothing = Value / 100 end})
WeaponsGroup:AddToggle("IgnoreFriends", {Text = "👥 Ignore Friends", Default = true, Callback = function(Value) IgnoreFriends = Value end})
WeaponsGroup:AddToggle("IgnoreProtected", {Text = "🛡️ Ignore Protecteds", Default = true, Callback = function(Value) IgnoreProtected = Value end})

-- ═══════════════════════════════════════════════════════
-- UI - ABA VISUAL (LEFT)
-- ═══════════════════════════════════════════════════════
local VisualGroup = Tabs.Visual:AddGroupbox({Side = "Left", Name = "Visual", Description = "Visual hacks", IconName = "eye"})

VisualGroup:AddToggle("BypassToggle", {
	Text = "🛡️ Ativar Bypass Anti-Cheat",
	Tooltip = "Bloqueia: KLZ v3.5, AntiFly, AntiHitbox,\nAntiCheatBox, Orpheus, etc.",
	Default = true,
	Callback = function(Value)
		if Value then
			EnableBypass()
			Library:Notify({Title = "🛡️ Bypass", Description = "Ativado!", Time = 2})
		else
			DisableBypass()
			Library:Notify({Title = "🛡️ Bypass", Description = "Desativado!", Time = 2})
		end
	end,
})

VisualGroup:AddDivider()

VisualGroup:AddToggle("Hitbox", {Text = "🎯 Chest Hitbox Expander", Default = false, Callback = function(Value)
	HitboxEnabled = Value
	if Value then
		Library:Notify({Title = "🎯 Chest Hitbox", Description = "Enabled! Size: " .. HitboxSize, Time = 2})
	else
		for _, p in pairs(game.Players:GetPlayers()) do
			if p ~= Player and p.Character then
				local hb = p.Character:FindFirstChild("RealChestHitbox")
				if hb then hb:Destroy() end
			end
		end
		Library:Notify({Title = "🎯 Chest Hitbox", Description = "Disabled!", Time = 2})
	end
end})

VisualGroup:AddSlider("HitboxSize", {Text = "Hitbox Size", Default = 5, Min = 3, Max = 15, Rounding = 0, Suffix = " studs", Callback = function(Value) HitboxSize = Value end})

VisualGroup:AddDivider()
VisualGroup:AddToggle("ESP", {Text = "👤 Enable ESP", Default = false, Callback = function(Value)
	ESPEnabled = Value
	if not Value then
		for _, Data in pairs(ESPObjects) do
			if Data.Billboard then Data.Billboard:Destroy() end
			if Data.Highlight then Data.Highlight:Destroy() end
		end
		table.clear(ESPObjects)
	end
	Library:Notify({Title = "👁️ ESP", Description = Value and "Enabled!" or "Disabled!", Time = 2})
end})

VisualGroup:AddToggle("ESPTeams", {Text = "🏳️ ESP Teams", Default = false, Callback = function(Value) ESPTeams = Value end})
VisualGroup:AddToggle("ESPTools", {Text = "🔧 ESP Tools", Default = true, Callback = function(Value) ESPTools = Value end})
VisualGroup:AddToggle("ESPFriends", {Text = "👥 ESP Friends", Default = true, Callback = function(Value) ESPFriends = Value end})
VisualGroup:AddLabel("ESP Color"):AddColorPicker("ESPColor", {Default = Color3.new(1, 1, 1), Title = "ESP Color", Transparency = 0, Callback = function(Value) ESPColor = Value end})

-- ═══════════════════════════════════════════════════════
-- UI - ABA VISUAL (RIGHT)
-- ═══════════════════════════════════════════════════════
local VisualRight = Tabs.Visual:AddGroupbox({Side = "Right", Name = "Posição (Visual)", Description = "Posição exclusiva da aba Visual", IconName = "map-pin"})

VisualRight:AddLabel("📌 Salvar Posição (Visual)")

VisualRight:AddButton({Text = "📌 Salvar Posição Atual", Func = function() SavePositionVisual() end})
VisualRight:AddButton({Text = "🚗 TP para Posição Salva", Func = function() TeleportToSavedPositionVisual() end})

VisualRight:AddDivider()

local VisualSavedPosLabel = VisualRight:AddLabel("Posição Visual: Nenhuma")

VisualRight:AddButton({Text = "🔄 Atualizar Info", Func = function()
	if VisualSavedPosition then
		VisualSavedPosLabel:SetText(string.format("Posição Visual:\nX: %d  Y: %d  Z: %d", 
			math.round(VisualSavedPosition.X), 
			math.round(VisualSavedPosition.Y), 
			math.round(VisualSavedPosition.Z)))
	else
		VisualSavedPosLabel:SetText("Posição Visual: Nenhuma")
	end
end})

-- ═══════════════════════════════════════════════════════
-- INICIALIZAÇÃO
-- ═══════════════════════════════════════════════════════
task.wait(0.5)
CreateFOVCircle()

print("✅ Ilha Bela carregado com sucesso!")
print("🛡️ Bypass Anti-Cheat ATIVO")
print("🧹 Auto Varrer agora usa TP DIRETO (sem tween)")
