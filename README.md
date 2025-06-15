local Players = game:GetService("Players")
local LP = Players.LocalPlayer
local Mouse = LP:GetMouse()

-- Criar arma
local tool = Instance.new("Tool")
tool.Name = "AK-47"
tool.RequiresHandle = true
tool.CanBeDropped = false
tool.Parent = LP.Backpack

-- Parte da arma
local handle = Instance.new("Part")
handle.Name = "Handle"
handle.Size = Vector3.new(1, 1, 4)
handle.BrickColor = BrickColor.new("Black")
handle.Material = Enum.Material.Metal
handle.CanCollide = false
handle.Parent = tool

-- Som
local shootSound = Instance.new("Sound", handle)
shootSound.SoundId = "rbxassetid://292744948"
shootSound.Volume = 2

-- Tiro
local cooldown = false
local shootRate = 0.1

tool.Activated:Connect(function()
	if cooldown then return end
	cooldown = true
	shootSound:Play()

	local origin = LP.Character.Head.Position
	local direction = (Mouse.Hit.p - origin).Unit * 500
	local ray = Ray.new(origin, direction)

	local part, pos = workspace:FindPartOnRay(ray, LP.Character, false, true)
	if part then
		local model = part:FindFirstAncestorOfClass("Model")
		if model and model:FindFirstChild("Humanoid") then
			local humanoid = model:FindFirstChild("Humanoid")
			-- Tenta matar
			pcall(function()
				humanoid.Health = 0
			end)

			-- Se não morrer, joga longe o player
			pcall(function()
				if model:FindFirstChild("HumanoidRootPart") then
					model.HumanoidRootPart.Velocity = Vector3.new(0, 100, 0)
				end
			end)
		end
	end

	-- Traço visual do tiro
	local beam = Instance.new("Part", workspace)
	beam.Anchored = true
	beam.CanCollide = false
	beam.Material = Enum.Material.Neon
	beam.BrickColor = BrickColor.new("Bright red")
	beam.Size = Vector3.new(0.1, 0.1, (pos - origin).Magnitude)
	beam.CFrame = CFrame.new(origin, pos) * CFrame.new(0, 0, -beam.Size.Z/2)
	game.Debris:AddItem(beam, 0.1)

	task.delay(shootRate, function()
		cooldown = false
	end)
end)# Ak47
