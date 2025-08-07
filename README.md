--[[
99 Días Hub - GUI Completa (Delta Mobile V3)
- Botón flotante para abrir/cerrar menú
- Pestañas: Combate, Movimiento, Visual, Farm
- Kill Aura funcional (para cualquier NPC dentro de cierto rango)
- Speed Hack (preset hasta 300)
- Salto infinito
- ESP universal para todos los modelos con HumanoidRootPart
- AutoFarm para árboles con ProximityPrompt
--]]

local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "DiasHub"
gui.ResetOnSpawn = false

-- Botón flotante
local openButton = Instance.new("TextButton", gui)
openButton.Size = UDim2.new(0, 100, 0, 40)
openButton.Position = UDim2.new(0, 20, 1, -60)
openButton.Text = "Menu"
openButton.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
openButton.TextColor3 = Color3.new(1, 1, 1)
openButton.ZIndex = 10

-- Menú principal
local mainFrame = Instance.new("Frame", gui)
mainFrame.Size = UDim2.new(0, 320, 0, 240)
mainFrame.Position = UDim2.new(0.5, -160, 0.5, -120)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
mainFrame.Visible = false
mainFrame.ZIndex = 9

local tabs = {"Combate", "Movimiento", "Visual", "Farm"}
local buttons = {}

for i, tab in ipairs(tabs) do
    local tabBtn = Instance.new("TextButton", mainFrame)
    tabBtn.Size = UDim2.new(0, 75, 0, 30)
    tabBtn.Position = UDim2.new(0, (i-1)*80 + 5, 0, 5)
    tabBtn.Text = tab
    tabBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    tabBtn.TextColor3 = Color3.new(1,1,1)
    tabBtn.ZIndex = 10
    buttons[tab] = tabBtn
end

local contentFrame = Instance.new("Frame", mainFrame)
contentFrame.Size = UDim2.new(1, -20, 1, -50)
contentFrame.Position = UDim2.new(0, 10, 0, 40)
contentFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
contentFrame.ZIndex = 9
contentFrame.ClipsDescendants = true

local function createButton(text, callback)
    local btn = Instance.new("TextButton", contentFrame)
    btn.Size = UDim2.new(1, -20, 0, 30)
    btn.Position = UDim2.new(0, 10, 0, (#contentFrame:GetChildren()-1)*35)
    btn.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Text = text
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 18
    btn.ZIndex = 10
    btn.MouseButton1Click:Connect(callback)
    return btn
end

local function showTab(name)
    for _, c in pairs(contentFrame:GetChildren()) do
        if c:IsA("GuiObject") then c:Destroy() end
    end

    if name == "Combate" then
        createButton("Activar Kill Aura", function()
            spawn(function()
                while true do
                    task.wait(0.2)
                    if player.Character then
                        local hrp = player.Character:FindFirstChild("HumanoidRootPart")
                        for _, npc in ipairs(workspace:GetDescendants()) do
                            if npc:IsA("Model") and npc ~= player.Character and npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then
                                local dist = (npc.HumanoidRootPart.Position - hrp.Position).Magnitude
                                if dist <= 300 and npc.Humanoid.Health > 0 then
                                    npc.Humanoid.Health = 0
                                end
                            end
                        end
                    end
                end
            end)
        end)
    elseif name == "Movimiento" then
        createButton("Speed 300", function()
            player.Character.Humanoid.WalkSpeed = 300
        end)
        createButton("Salto Infinito", function()
            game:GetService("UserInputService").JumpRequest:Connect(function()
                if player.Character then
                    player.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
                end
            end)
        end)
    elseif name == "Visual" then
        createButton("Activar ESP", function()
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("Model") and obj:FindFirstChild("HumanoidRootPart") then
                    if not obj:FindFirstChild("ESP") then
                        local esp = Instance.new("BillboardGui", obj)
                        esp.Name = "ESP"
                        esp.Size = UDim2.new(0, 100, 0, 30)
                        esp.Adornee = obj.HumanoidRootPart
                        esp.AlwaysOnTop = true
                        local label = Instance.new("TextLabel", esp)
                        label.Size = UDim2.new(1, 0, 1, 0)
                        label.BackgroundTransparency = 1
                        label.Text = obj.Name
                        label.TextColor3 = Color3.new(1, 1, 0)
                        label.TextScaled = true
                    end
                end
            end
        end)
    elseif name == "Farm" then
        createButton("AutoFarm Árboles", function()
            spawn(function()
                while true do
                    task.wait(1)
                    for _, v in ipairs(workspace:GetDescendants()) do
                        if v:IsA("ProximityPrompt") and v.Parent and v.Parent.Name:lower():find("tree") then
                            fireproximityprompt(v)
                        end
                    end
                end
            end)
        end)
    end
end

openButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = not mainFrame.Visible
    if mainFrame.Visible then
        showTab("Combate")
    end
end)

for name, btn in pairs(buttons) do
    btn.MouseButton1Click:Connect(function()
        showTab(name)
    end)
end # 99-dias-hub
