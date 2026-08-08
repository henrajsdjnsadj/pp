local Players = game:GetService("Players")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

-- Espera o personagem carregar corretamente
local function esperarPersonagem()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    local root = character:WaitForChild("HumanoidRootPart")
    return character, humanoid, root
end

local character, humanoid, root = esperarPersonagem()

-- ============================
-- CONTROLE DE LOOP
-- ============================
_G.PararFarm = false

-- Interrompe automaticamente após 1 minuto
task.delay(40000, function()
    _G.PararFarm = true
    print(">> [Controle] Farm interrompido automaticamente após 1 minuto.")
end)
-- ============================
-- DESTINOS
-- ============================
local destinos = {
    Vector3.new(-301.91, 254.87, 466.99),
    Vector3.new(-308.11, 254.83, 467.22),
    Vector3.new(-315.67, 254.62, 467.61),
    Vector3.new(-321.26, 254.62, 467.77),
    Vector3.new(-326.19, 254.62, 467.96),
    Vector3.new(-332.00, 254.62, 468.20),
    Vector3.new(-337.13, 254.62, 468.39),
    Vector3.new(-343.76, 254.62, 469.27),
    Vector3.new(-350.05, 254.62, 470.54),
    Vector3.new(-355.81, 254.62, 470.35),
    Vector3.new(-308.11, 254.83, 467.22),
    Vector3.new(-223.12, 254.72, 464.61),
    Vector3.new(-221.06, 254.81, 299.43),
    Vector3.new(-72.23,  254.81, 295.66),
    Vector3.new(-65.89,  254.58, 203.77),
    Vector3.new(-117.46, 254.81, 167.35),
    Vector3.new(-135.68, 254.95, 153.35)
}

-- ============================
-- FUNÇÕES DE MOVIMENTO
-- ============================
local function PressionarShift()
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.LeftShift, false, game)
end

local function SoltarShift()
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.LeftShift, false, game)
end

local function moverComGarantia(destino, proximoDestino)
    PressionarShift()
    humanoid:MoveTo(destino)

    local tempoInicial = tick()
    repeat
        task.wait(0.2)
        if tick() - tempoInicial > 10 then
            root.CFrame = CFrame.new(destino)
            humanoid:MoveTo(destino)
            tempoInicial = tick()
        end
    until (root.Position - destino).Magnitude <= 3 or _G.PararFarm

    if proximoDestino then
        root.CFrame = CFrame.new(destino, proximoDestino)
    end

    SoltarShift()
    print("Chegou em:", destino)
end

-- ============================
-- FUNÇÕES DE CLIQUE
-- ============================
local function ExecutarCliques4()
    local screenSize = Camera.ViewportSize
    local sx, sy = screenSize.X, screenSize.Y

    VirtualInputManager:SendMouseButtonEvent(sx*0.93, sy*0.40, 0, true, game, 0)
    VirtualInputManager:SendMouseButtonEvent(sx*0.93, sy*0.40, 0, false, game, 0)
    task.wait(1)

    VirtualInputManager:SendMouseButtonEvent(sx*0.50, sy*0.25, 0, true, game, 0)
    VirtualInputManager:SendMouseButtonEvent(sx*0.50, sy*0.25, 0, false, game, 0)
    task.wait(1)

    VirtualInputManager:SendMouseButtonEvent(sx*0.48, sy*0.71, 0, true, game, 0)
    VirtualInputManager:SendMouseButtonEvent(sx*0.48, sy*0.71, 0, false, game, 0)
    task.wait(1)

    VirtualInputManager:SendMouseButtonEvent(sx*0.48, sy*0.71, 0, true, game, 0)
    task.wait(1)
    VirtualInputManager:SendMouseButtonEvent(sx*0.48, sy*0.71, 0, false, game, 0)

    print(">> 4 cliques realizados.")
end

local function ExecutarVendaPorProporcao()
    local screenSize = Camera.ViewportSize
    local cliqueX = screenSize.X * 0.35
    local cliqueY = screenSize.Y * 0.40

    VirtualInputManager:SendMouseButtonEvent(cliqueX, cliqueY, 0, true, game, 0)
    VirtualInputManager:SendMouseButtonEvent(cliqueX, cliqueY, 0, false, game, 0)
    task.wait(0.2)

    print(">> [Sucesso] Venda concluída.")
end

local function ExecutarCliquesRapidos()
    local screenSize = Camera.ViewportSize
    local cliqueX = screenSize.X * 0.60
    local cliqueY = screenSize.Y * 0.60

    print(">> [Compra] Executando cliques rápidos por 45 segundos")

    local tempoInicial = tick()
    while tick() - tempoInicial < 50 and not _G.PararFarm do
        VirtualInputManager:SendMouseButtonEvent(cliqueX, cliqueY, 0, true, game, 0)
        VirtualInputManager:SendMouseButtonEvent(cliqueX, cliqueY, 0, false, game, 0)
        task.wait(0.05)
    end
    print(">> [Compra] Finalizado.")
end

-- ============================
-- INÍCIO COM ATRASO DE 5 SEGUNDOS (GUI)
-- ============================
local ScreenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
local TextLabel = Instance.new("TextLabel", ScreenGui)
TextLabel.Size = UDim2.new(0, 200, 0, 50)
TextLabel.Position = UDim2.new(0.5, -100, 0.5, -25)
TextLabel.Text = "3 CONTA"
TextLabel.TextScaled = true
TextLabel.BackgroundTransparency = 0.5
TextLabel.BackgroundColor3 = Color3.new(0,0,0)
TextLabel.TextColor3 = Color3.new(1,1,1)

task.wait(1)
TextLabel:Destroy()

-- ============================
-- LOOP PRINCIPAL
-- ============================
print("=== FARM AUTOMÁTICO INICIADO ===")

while not _G.PararFarm do
    -- CAMINHO DE IDA
    for i, destino in ipairs(destinos) do
        if _G.PararFarm then break end
        local proximo = destinos[i+1]
        moverComGarantia(destino, proximo)

        if i >= 1 and i <= 10 then
            ExecutarCliques4()
            task.wait(2)
        end

        if i == 17 then
            task.wait(5)
            ExecutarVendaPorProporcao()
            task.wait(2)
            ExecutarCliquesRapidos()
            task.wait(100)

            local proximoVolta = destinos[i-1]
            if proximoVolta then
                moverComGarantia(destino, proximoVolta)
            else
                moverComGarantia(destino)
            end
        end
    end

    -- CAMINHO DE VOLTA
    for i = #destinos, 1, -1 do
        if _G.PararFarm then break end
        local destino = destinos[i]
        local proximo = destinos[i-1]

        if proximo then
            moverComGarantia(destino, proximo)
        else
            moverComGarantia(destino)
        end

        if i >= 3 and i <= 10 then
            ExecutarCliques4()
            task.wait(15)
        end


        if i == 16 then
            print(">> Pausa de 10 segundos na coordenada 16 (volta).")
            task.wait(15)
        end
    end

    print(">> Ciclo completo (ida e volta). Aguardando 10 minutos antes de reiniciar...")
    task.wait(335) -- pausa de 10 minutos
end
