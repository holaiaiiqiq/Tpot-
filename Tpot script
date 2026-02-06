-- TPOT21 Box Order Assistant (FIX CAJAS)
-- Detecta cajas como MODELS

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local workspace = game:GetService("Workspace")

-- Colores
local COLOR_PENDING = Color3.fromRGB(255, 255, 0)
local COLOR_OPENED  = Color3.fromRGB(0, 255, 0)
local COLOR_LAST    = Color3.fromRGB(255, 0, 0)

local boxes = {}   -- [model] = {highlight, opened}
local lastBox = nil
local openedCount = 0

-- Crear Highlight
local function makeHighlight(adornee, color)
    local h = Instance.new("Highlight")
    h.FillColor = color
    h.OutlineColor = Color3.new(1,1,1)
    h.FillTransparency = 0.35
    h.OutlineTransparency = 0
    h.Adornee = adornee
    h.Parent = adornee
    return h
end

-- Heurística de caja (MODEL)
local function isBoxModel(model)
    if not model:IsA("Model") then return false end
    if not model.PrimaryPart then return false end

    -- tamaño mínimo
    local size = model:GetExtentsSize()
    if size.Magnitude < 6 then return false end

    -- muchas cajas tienen SurfaceGui o ClickDetector
    for _,v in pairs(model:GetDescendants()) do
        if v:IsA("SurfaceGui") or v:IsA("ClickDetector") then
            return true
        end
    end

    return false
end

-- Escanear cajas
local function scanBoxes()
    for _,v in pairs(workspace:GetDescendants()) do
        if isBoxModel(v) and not boxes[v] then
            boxes[v] = {
                model = v,
                opened = false,
                highlight = makeHighlight(v, COLOR_PENDING)
            }

            -- detectar apertura: algo del modelo cambia
            v.DescendantAdded:Connect(function(desc)
                if not boxes[v].opened then
                    boxes[v].opened = true
                    openedCount += 1
                    lastBox = v
                    print("📦 Caja abierta #" .. openedCount)
                end
            end)
        end
    end
end

-- Actualizar colores
local function updateESP()
    for model,data in pairs(boxes) do
        if data.highlight then
            if data.opened then
                data.highlight.FillColor = COLOR_OPENED
            else
                data.highlight.FillColor = COLOR_PENDING
            end
        end
    end

    if lastBox and boxes[lastBox] then
        boxes[lastBox].highlight.FillColor = COLOR_LAST
    end
end

-- Loop principal
task.spawn(function()
    while true do
        scanBoxes()
        updateESP()
        task.wait(0.5)
    end
end)

print("✅ TPOT21 Box Order Assistant (cajas) cargado")
print("🟡 Amarillo = sin abrir | 🟢 Verde = abiertas | 🔴 Roja = última")
