--[[
Script de painel para ativar/desativar teleporte para sua base ao pegar pet

-> Adiciona um painel na tela com botão "Teleportar para base?".
-> Quando ativado, toda vez que você pegar um pet, será teleportado para a base "SUA BASE".
-> Coloque este LocalScript em StarterPlayerScripts.

DICA: Se quiser mudar a aparência, edite o Frame e o Button!

]]--

local player = game.Players.LocalPlayer

-- Função para encontrar posição da base com "SUA BASE"
local function getBasePosition()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("TextLabel") and obj.Text:find("SUA BASE") and obj.Parent and obj.Parent:IsA("Part") then
            return obj.Parent.Position
        end
    end
    return nil
end

-- Função para teleportar para a base
local function teleportToBase()
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        local pos = getBasePosition()
        if pos then
            char.HumanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0,4,0))
        else
            warn("Não achou a base com 'SUA BASE'!")
        end
    end
end

-- Cria o painel de ativação
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

-- Detecta quando pega qualquer PET
local function detectPetPickup()
    player.Character.ChildAdded:Connect(function(child)
        if ativo and (child:IsA("Model") or child:IsA("Part")) then
            if child.Name ~= player.Name and not child:IsDescendantOf(workspace) then
                teleportToBase()
            end
        end
    end)
end

player.CharacterAdded:Connect(detectPetPickup)
if player.Character then detectPetPickup() end
