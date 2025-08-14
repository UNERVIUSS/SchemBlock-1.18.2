-- Сохраните как 'google_browser.lua'
-- Требуется монитор и интернет-подключение

-- Настройки
local monitorSide = "top"     -- Сторона подключения монитора
local googleButtonText = "Google"
local defaultSite = "https://www.google.com"

-- Инициализация
local monitor = peripheral.wrap(monitorSide)
if not monitor then error("Монитор не подключен") end
term.redirect(monitor)
monitor.setTextScale(0.5)
local w, h = monitor.getSize()

-- Отрисовка интерфейса
local function drawUI()
    monitor.setBackgroundColor(colors.black)
    monitor.clear()
    
    -- Кнопка Google
    monitor.setBackgroundColor(colors.blue)
    local btnWidth = #googleButtonText + 4
    local btnX = math.floor((w - btnWidth) / 2)
    local btnY = math.floor(h / 3)
    
    monitor.setCursorPos(btnX, btnY)
    monitor.write(string.rep(" ", btnWidth))
    monitor.setCursorPos(btnX, btnY + 1)
    monitor.write("  " .. googleButtonText .. "  ")
    monitor.setCursorPos(btnX, btnY + 2)
    monitor.write(string.rep(" ", btnWidth))
    
    -- URL-бар
    monitor.setBackgroundColor(colors.gray)
    monitor.setCursorPos(1, h)
    monitor.write(string.rep(" ", w))
    monitor.setCursorPos(2, h)
    monitor.setTextColor(colors.white)
    monitor.write("URL: ")
    
    return btnX, btnY, btnWidth, 3
end

-- Загрузка сайта
local function browse(url)
    monitor.clear()
    monitor.setCursorPos(1, 1)
    monitor.setTextColor(colors.white)
    monitor.setBackgroundColor(colors.black)
    monitor.write("Загрузка: " .. url)
    
    local response = http.get(url)
    if not response then
        monitor.setCursorPos(1, 3)
        monitor.setTextColor(colors.red)
        monitor.write("Ошибка загрузки")
        return
    end
    
    local html = response.readAll()
    response.close()
    
    -- Простое текстовое отображение
    monitor.clear()
    monitor.setCursorPos(1, 1)
    
    local lines = {}
    for line in html:gmatch("[^\r\n]+") do
        local clean = line:gsub("<[^>]+>", ""):gsub("%s+", " ")
        if #clean > 0 then
            table.insert(lines, clean)
        end
    end
    
    for i = 1, math.min(#lines, h * 2) do
        if i % 2 == 0 then
            monitor.setTextColor(colors.lightGray)
        else
            monitor.setTextColor(colors.white)
        end
        monitor.setCursorPos(1, math.ceil(i / 2) + 1)
        monitor.write(lines[i]:sub(1, w))
    end
end

-- Основной цикл
while true do
    local btnX, btnY, btnW, btnH = drawUI()
    monitor.setBackgroundColor(colors.black)
    monitor.setCursorPos(1, 1)
    monitor.write("Браузер CC: Tweaked")
    
    while true do
        local event, side, x, y = os.pullEvent("monitor_touch")
        if side == monitorSide then
            -- Клик по кнопке Google
            if x >= btnX and x < btnX + btnW and y >= btnY and y < btnY + btnH then
                browse(defaultSite)
                break
            -- Клик по URL-бару
            elseif y == h then
                monitor.setCursorPos(7, h)
                monitor.setBackgroundColor(colors.black)
                monitor.setTextColor(colors.white)
                monitor.write(string.rep(" ", w - 7))
                monitor.setCursorPos(7, h)
                
                local url = read()
                if url and url ~= "" then
                    if not url:find("://") then
                        url = "http://" .. url
                    end
                    browse(url)
                    break
                end
            end
        end
    end
end
