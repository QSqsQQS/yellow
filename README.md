--[[ 
Painel para ativar teleporte para base ao equipar QUALQUER Tool ou Accessory

- Adiciona um painel com botão para ativar/desativar.
- Ao ativar, se você equipar qualquer Tool/Accessory, teleporta para base de "SUA BASE".
- Coloque como LocalScript em StarterPlayerScripts!
]]--

local player = game.Players.LocalPlayer

-- Função para encontrar base "SUA BASE"
local function getBasePosition()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("TextLabel") and obj.Text:find("SUA BASE") and obj.Parent and obj.Parent:IsA("Part") then
            return obj.Parent.Position
        end
    end
    return nil
end

local function teleportToBase()
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        local pos = getBasePosition()
        if pos then
            char.HumanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0,4,0))
        else
            warn("Não achou a base 'SUA BASE'")
        end
    end
end

-- Painel de ativação/desativação
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TeleportPanel"
screenGui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame", screenGui)
frame.BackgroundColor3 = Color3.fromRGB(30, 144, 255)
frame.Position = UDim2.new(0.7, 0, 0.05, 0)
frame.Size = UDim2.new(0, 220, 0, 80)
frame.Active = true

local button = Instance.new("TextButton", frame)
button.Text = "Teleportar para base? [DESLIGADO]"
button.Size = UDim2.new(1, -10, 0, 40)
button.Position = UDim2.new(0, 5, 0, 20)
button.BackgroundColor3 = Color3.fromRGB(60, 200, 80)
button.Font = Enum.Font.SourceSansBold
button.TextSize = 18

local ativo = false
button.MouseButton1Click:Connect(function()
    ativo = not ativo
    button.Text = ativo and "Teleportar para base? [LIGADO]" or "Teleportar para base? [DESLIGADO]"
end)

-- Detecta equipar Tool ou Accessory
local equipped = false
local function detectEquipped()
    local char = player.Character
    if not char then return end

    -- Tool equip detect (Hand tools)
    for _, tool in ipairs(char:GetChildren()) do
        if tool:IsA("Tool") then
            if ativo and not equipped then
                equipped = true
                teleportToBase()
            end
        end
    end

    char.ChildAdded:Connect(function(child)
        if (child:IsA("Tool") or child:IsA("Accessory")) and ativo and not equipped then
            equipped = true
            teleportToBase()
        end
    end)
    char.ChildRemoved:Connect(function(child)
        if child:IsA("Tool") or child:IsA("Accessory") then
            equipped = false
        end
    end)
end

player.CharacterAdded:Connect(detectEquipped)
if player.Character then detectEquipped() end
