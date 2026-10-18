Вот готовое профессиональное описание для вашего GitHub-репозитория на двух языках (**English** и **Russian**). Вы можете просто скопировать этот текст и вставить его в файл `README.md`.

---

# PS5 Overlay (KUS Edition) 🎮⚡

[English](https://www.google.com/search?q=%23english) | [Русский](https://www.google.com/search?q=%23%D1%80%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9)

---

## 🇬🇧 English

**PS5 In-Game Telemetry Overlay** is a standalone, lightweight real-time performance and system telemetry in-game overlay for **PlayStation 5 (Jailbreak)**.

The overlay renders on top of any game and the `SceShellUI` system menu using native PlayStation PUI (Mono) UI components. Management and fine-tuning are handled via a built-in **web interface** directly from your phone or PC.

---

### ✨ Key Features

* 📊 **12 Real-Time Telemetry Metrics**:
1. **FPS** — Hardware frame rate via display controller flip counter (`/dev/dce`) plus a software fallback using `SceVshAppSystem` hooks.
2. **CPU** — AMD Zen 2 core temperature (`sceKernelGetCpuTemperature`).
3. **LOAD** — Total CPU utilization percentage calculated based on thread execution time.
4. **CCLK** — Current CPU core frequency in MHz (`sceKernelGetCpuCoreClock` / `sceKernelGetCpuFrequency`).
5. **GPU** — RDNA 2 SoC graphics chip temperature (`sceKernelGetSocSensorTemperature`, channel 0).
6. **GCLK** — Actual GFXCLK graphics core frequency in MHz (`sceKernelGetSocClock`).
7. **RAM** — System RAM usage (`get_page_table_stats`).
8. **VRAM** — Video memory usage of the graphics accelerator (`get_page_table_stats`).
9. **SSD** — M.2 NVMe storage temperature via SoC controller sensor (`sceKernelGetSocSensorTemperature`, channel 2). Displays `N/A` if the slot is empty.
10. **FAN** — Current cooling fan duty cycle percentage (`sceKernelGetCurrentFanDuty`).
11. **PWR** — SoC APU power consumption in Watts (`sceKernelGetSocPowerConsumption`).
12. **IP** — Local console IPv4 network address.


* 🌐 **Built-in Web Control Panel (Port 8585)**:
* Accessible from any device on your local network: `http://<PS5_IP>:8585`.
* **Live Preview** of the HUD right in your browser window.
* **Sorting & Filtering**: Enable/disable any metric and change display order using ▲ / ▼ buttons.
* **Color Palette**: Custom HEX color selection for each telemetry parameter.
* **Positioning**: Choose layout placement (Top / Bottom, Left / Center / Right) with fine X/Y offset tuning.
* **Appearance**: Customize font size, bar height, background color, and transparency.
* 🛑 **Hot Unload**: Safe shutdown button to unload the overlay and free memory without rebooting the console.
* **Auto-Save**: All changes are saved to `/data/overlay/config.ini` and automatically loaded on startup.


* 🚀 **Standalone Loader (All-In-One ELF)**:
* Single executable file `ps5-overlay-kus_vX.X.X.elf`.
* Automatically finds the `SceShellUI` process, escalates privileges (`escalate_process_caps`), allocates remote memory via `ptrace`, maps the payload, and starts a working thread with proper Mono domain attachment (`mono_thread_attach`).
* Safe pre-resolution of system kernel symbols prior to injection prevents crashes and freezes.



---

### 🛠 Quick Start & Usage

#### 1. Sending to the Console

Send the compiled ELF to your console via Netcat or Socat to the payload loader port (e.g., 9021 or 9020):

```bash
# Using netcat
nc -q0 192.168.1.100 9021 < bin/ps5-overlay-kus_v1.0.19.elf

# Or using socat
socat - TCP:192.168.1.100:9021 < bin/ps5-overlay-kus_v1.0.19.elf

```

Upon successful injection, a system notification will appear on your screen:

> **"PS5 Overlay Loaded successfully!"**

#### 2. Browser Configuration

Open the following address in your smartphone or PC browser:

```
http://<YOUR_PS5_IP>:8585

```

*(If port 8585 is busy, the server will automatically fall back to an alternative port: 8282, 8989, or 9876).*

---

### 🏗 Project Architecture

```
PS5_Overlay/
├── Makefile                # Build rules for payload and loader
├── common/                 # Shared headers, structures, and NIDs
│   ├── bootstrap.h         # Pointer passing arguments from Loader to Payload
│   ├── log.h               # System logging macros
│   └── nid.c               # NID resolution for libkernel_sys
├── loader/                 # Loader / Injector (ps5-overlay-kus.elf)
│   ├── main.c              # Entry point, SceShellUI search
│   ├── injector.c          # Shellcode injector and symbol pre-resolving
│   ├── proc.c              # Process PID search via sysctl
│   ├── ucred.c             # Privilege escalation (jailbreak_process)
│   ├── pt.c                # ptrace wrappers
│   ├── elfldr.c            # Remote ELF loader
│   ├── notify.c            # System toast notification output
│   └── embedded_payload.S  # overlay_payload.elf embedding section
└── payload/                # Payload (injected into SceShellUI)
    ├── main.cpp            # Mono hooks, HUD building, telemetry polling
    ├── config.cpp / .hpp   # INI configuration parser & generator (/data/overlay/config.ini)
    ├── web_server.cpp      # Built-in HTTP web server and REST API
    ├── mono_runtime.cpp    # Mono runtime interaction
    └── payload_log.cpp     # Payload logging

```

---

### ⚙️ Building from Source

A Linux environment with **[ps5-payload-sdk](https://github.com/ps5-payload-dev/ps5-payload-sdk)** installed is required.

```bash
# Clone the repository
git clone https://github.com/smoxa/ps5-new-overlay.git
cd ps5-new-overlay

# Build the project
make clean && make

# The compiled binary will be available in the bin/ directory:
# bin/ps5-overlay-kus_v1.0.19.elf

```

---

### 📝 Configuration File

All settings are stored on the console at `/data/overlay/config.ini`:

```ini
[overlay]
enabled = true
pos_v = top
pos_h = left
offset_x = 24
offset_y = 0
font_size = 18
bar_height = 34
bg_visible = true
bg_color = #000000
bg_opacity = 0.70
port = 8585

[metrics]
order = fps,cpu,load,cclk,gpu,gclk,ram,vram,ssd,fan,pwr,ip

[metric_fps]
enabled = true
color = #FFEB3B

[metric_ssd]
enabled = true
color = #00E5FF
...

```

---

### 🤝 Acknowledgments

* The **ps5-payload-sdk** developers
* **ProsperoMgr** and **ps5-hwinfo** projects for reverse engineering and researching PlayStation 5 SoC APU sensors
* The **OnionHEN** team for baseline work on Mono PUI integration

---

---

## 🇷🇺 Русский

**PS5 In-Game Telemetry Overlay** — автономный, легковесный внутриигровой оверлей мониторинга производительности и системной телеметрии в реальном времени для **PlayStation 5 (Jailbreak)**.

Оверлей выводится поверх любых игр и системного меню `SceShellUI` через нативные UI-компоненты PlayStation PUI (Mono). Управление и тонкая кастомизация осуществляются через встроенный **веб-интерфейс** прямо с телефона или ПК.

---

### ✨ Основные возможности

* 📊 **12 параметров телеметрии в реальном времени**:
1. **FPS** — аппаратная частота кадров через счётчик флипов дисплейного контроллера (`/dev/dce`) плюс программный fallback через хуки `SceVshAppSystem`.
2. **CPU** — температура ядер процессора AMD Zen 2 (`sceKernelGetCpuTemperature`).
3. **LOAD** — общая загрузка процессора в процентах, рассчитываемая на основе времени выполнения тредов.
4. **CCLK** — текущая частота ядер CPU в МГц (`sceKernelGetCpuCoreClock` / `sceKernelGetCpuFrequency`).
5. **GPU** — температура графического чипа SoC RDNA 2 (`sceKernelGetSocSensorTemperature`, channel 0).
6. **GCLK** — фактическая частота видеоядра GFXCLK в МГц (`sceKernelGetSocClock`).
7. **RAM** — использование системной оперативной памяти (`get_page_table_stats`).
8. **VRAM** — объем используемой видеопамяти графического ускорителя (`get_page_table_stats`).
9. **SSD** — температура накопителя в слоте M.2 NVMe через датчик контроллера SoC (`sceKernelGetSocSensorTemperature`, channel 2). Если слот пуст — отображается `N/A`.
10. **FAN** — текущая скорость вращения турбины охлаждения в процентах (`sceKernelGetCurrentFanDuty`).
11. **PWR** — энергопотребление кристалла SoC APU в Ваттах (`sceKernelGetSocPowerConsumption`).
12. **IP** — локальный IPv4-адрес консоли в сети.


* 🌐 **Встроенная Web-панель управления (порт 8585)**:
* Доступна с любого устройства в локальной сети: `http://<IP_PS5>:8585`.
* **Живой предпросмотр (Live Preview)** HUD в окне браузера.
* **Сортировка и фильтрация**: включение/выключение любой метрики и изменение порядка отображения кнопками ▲ / ▼.
* **Цветовая палитра**: индивидуальный выбор HEX-цвета для каждого параметра телеметрии.
* **Позиционирование**: выбор расположения (Сверху / Снизу, Слева / По центру / Справа) и тонкая подстройка отступов по X и Y.
* **Внешний вид**: настройка размера шрифта, высоты полосы, цвета и прозрачности полупрозрачной подложки (фона).
* 🛑 **Горячая выгрузка (Unload)**: кнопка безопасного завершения оверлея и очистки памяти без необходимости перезагружать консоль.
* **Автосохранение**: все изменения сохраняются в конфигурационный файл `/data/overlay/config.ini` и автоматически подгружаются при старте.


* 🚀 **Автономный Loader (All-In-One ELF)**:
* Единый исполняемый файл `ps5-overlay-kus_vX.X.X.elf`.
* Сам находит процесс `SceShellUI`, повышает привилегии (`escalate_process_caps`), выделяет удалённую память через `ptrace`, маппит пейлоад и запускает рабочий тред с корректным подключением к домену Mono (`mono_thread_attach`).
* Безопасное предварительное разрешение системных символов ядра перед инжектом исключает падения и зависания.



---

### 🛠 Быстрый старт и использование

#### 1. Отправка на консоль

Отправьте собранный ELF на консоль через Netcat / Socat на порт загрузчика пейлоадов (например, 9021 или 9020):

```bash
# Через netcat
nc -q0 192.168.1.100 9021 < bin/ps5-overlay-kus_v1.0.19.elf

# Или через socat
socat - TCP:192.168.1.100:9021 < bin/ps5-overlay-kus_v1.0.19.elf

```

После успешного внедрения на экране консоли появится уведомление:

> **"PS5 Overlay Loaded successfully!"**

#### 2. Настройка через браузер

Откройте в браузере смартфона или компьютера адрес:

```
http://<IP_ВАШЕЙ_PS5>:8585

```

*(Если порт 8585 занят, сервер автоматически выберет альтернативный порт: 8282, 8989 или 9876).*

---

### 🏗 Архитектура проекта

```
PS5_Overlay/
├── Makefile                # Правила сборки payload и loader
├── common/                 # Общие заголовки, структуры и NID
│   ├── bootstrap.h         # Аргументы передачи указателей от Loader к Payload
│   ├── log.h               # Макросы системного логирования
│   └── nid.c               # Разрешение NID для libkernel_sys
├── loader/                 # Загрузчик / Инжектор (ps5-overlay-kus.elf)
│   ├── main.c              # Точка входа, поиск SceShellUI
│   ├── injector.c          # Инжектор шеллкода и pre-resolving символов
│   ├── proc.c              # Поиск PID процессов через sysctl
│   ├── ucred.c             # Эскалация прав и привилегий (jailbreak_process)
│   ├── pt.c                # Обертки ptrace
│   ├── elfldr.c            # Удаленный ELF-загрузчик
│   ├── notify.c            # Вывод системных всплывающих уведомлений (toast)
│   └── embedded_payload.S  # Секция встраивания overlay_payload.elf
└── payload/                # Полезная нагрузка (внедряется в SceShellUI)
    ├── main.cpp            # Mono хуки, построение HUD, опрос телеметрии
    ├── config.cpp / .hpp   # Парсер и генератор конфигурации INI (/data/overlay/config.ini)
    ├── web_server.cpp      # Встроенный HTTP веб-сервер и REST API
    ├── mono_runtime.cpp    # Взаимодействие с рантаймом Mono
    └── payload_log.cpp     # Логирование пейлоада

```

---

### ⚙️ Сборка из исходников

Для сборки требуется окружение Linux с установленным **[ps5-payload-sdk](https://github.com/ps5-payload-dev/ps5-payload-sdk)**.

```bash
# Клонирование репозитория
git clone https://github.com/smoxa/ps5-new-overlay.git
cd ps5-new-overlay

# Сборка проекта
make clean && make

# Готовый файл появится в папке bin/:
# bin/ps5-overlay-kus_v1.0.19.elf

```

---

### 📝 Конфигурационный файл

Все настройки сохраняются на консоли по пути `/data/overlay/config.ini`:

```ini
[overlay]
enabled = true
pos_v = top
pos_h = left
offset_x = 24
offset_y = 0
font_size = 18
bar_height = 34
bg_visible = true
bg_color = #000000
bg_opacity = 0.70
port = 8585

[metrics]
order = fps,cpu,load,cclk,gpu,gclk,ram,vram,ssd,fan,pwr,ip

[metric_fps]
enabled = true
color = #FFEB3B

[metric_ssd]
enabled = true
color = #00E5FF
...

```

---

### 🤝 Благодарности

* Разработчикам **ps5-payload-sdk**
* Проектам **ProsperoMgr** и **ps5-hwinfo** за реверс-инжиниринг и исследования сенсоров PlayStation 5 SoC APU
* Команде **OnionHEN** за базовые наработки интеграции с Mono PUI
