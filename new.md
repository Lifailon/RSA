# <img src="https://github.com/Lifailon/RSA/blob/rsa/Image/ico/RSA-Logo.ico" width="25" /> RSA (Remote Shadow Administrator)

[![PowerShell](https://github.com/Lifailon/RSA/blob/rsa/Image/Logo/PowerShell-Button.png)](https://github.com/PowerShell/PowerShell) [![Telegram @kup57](https://github.com/Lifailon/RSA/blob/rsa/Image/Logo/Telegram-Button.png)](https://t.me/kup57)

- [💡 Описание](#-Описание)
- [📦 Модули](#-Модули)
- [📘 Функционал](#-Функционал)

**[🚀 Скачать (RSA.exe)](https://github.com/Lifailon/RSA/releases)**

## 💡 Описание

Программа для подключения к текущим RDP-сессиям по средствам **Shadow-подключения**. Так же содержит набор модулей, направленного на автоматизацию удаленного администрирования и взаимодействия с ОС Windows.

Можно использовать как альтернативное средство для удаленного подключения, таким как Radmin или VNC, которые требуют установки программного обеспечения по модели клиент-сервер. Используется 100% кода на PowerShell и Windows Forms (без использования Toolbox), не содержит зависимостей в виде модулей. Протестировано на Windows Server 2016-2019 DC и Windows 10 Pro, не зависит от локализации ОС.

![Image alt]()

## 📦 Модули

* **[Get-Query](https://github.com/Lifailon/Get-Query)** - используется для парсинга программы **query.exe с выводом в PSObject**, с целью отображения текущих сессий и запущенных пользовательских процессов (версия 1.2) на компьютере.

* **[Get-Invent](https://github.com/Lifailon/Get-Invent-SQLite)** - используется для сбора данных об оборудовании на удаленном компьютере с выводом в HTML-файл (версия 1.1).

* **[RSA-Modules](https://github.com/Lifailon/RSA-Modules)** - сборник некоторых функций, которые я выделил в отдельные модули.

## 📘 Функционал

