# RSA - Remote Shadow Administrator

**Первостепенная цель программы - подключение к текущим RDP-сессия по средствам Shadow-подключения.** Можно использовать как альтернативное средство удаленного подключения (например, Radmin или VNC, которые требуют установки ПО и имеют некоторые дыры в безопасности). Используется 100% кода на powershell и Windows Forms (без использования Visual Studio).

При выборе сервера и нажатии на кнопку "Проверить" отображается список текущих пользователей в виде таблицы. **При выборе ID пользователя можно произвести три действия: Shadow-подключение с возможностью запроса на подключение и без (последнее настраивается через GPO), отключение пользователя (выход из системы) и отправки набранного сообщения всем пользователям на сервере или выбранному в таблице.** Создание таблицы происходит в 3 этапа, вначале проверяется доступность сервера, о чем сообщается в статус-баре и дополнительно проверяется uptime (если не доступен WinRM, программа об этом сообщит), если сервер не доступен, во избежании долгой задержки проверка пользователей не производится. На втором этапе парсится вывод команды query по средствам Regex, на последнем происходит создание Custom Object с выводом в GridView. Для поиска пользователей в сети можно воспользоваться [этим скриптом](https://github.com/Lifailon/Find-Users).

![Image alt](https://github.com/Lifailon/Remote-Shadow-Administrator/blob/rsa/User.jpg)

Для подключение к серверу через rdp используется mstsc с ключем /admin, что позволяет подключаться к RDSH-серверу минуя Broker. **Для аутентификации используется cmdkey**, после прохождения единоразовой аутентификации (в меню Файл - Аутентификация), в последствии происходит предварительная аутентификация на все сервера в списке и действует до закрытия программы, **что позволяет не хранить пароль администратора в коде а так же хранилище ключей ОС (которые можно скомпрометировать)**. Список серверов хранится в файле %USERPROFILE%\Documents\RSA.conf.txt, который можно вызвать из программы нажатием правой кнопки мыши в списке серверов или комбинацией клавиш Ctrl+S.

**Вторая цель программы - автоматизация удаленного администрирования**. Программ имеет более 20 скриптов по удаленному взаимодействию с серверами и рабочими станциями. Присутствует вывод всех команд в статус-бар.

**Типовые:** перезагрузка и выключение (shutdown) с задержкий 30 секунд и возможностью отмены. Управление компьютером (Computer Management), gpupdate на удаленной машине, gpresult с выводом в XML-файл и указанием пользователя. Проверка служб с возможностью удобной фильтрации поиска и повторной проверки статуса после остановки/перезапуска. Список запущенных процессов пользователей с возможностью их завершения. Список открытых SMB-сессий с возможностью их закрытия для освобождения файла. Просмотр всех сетевых ресурсов с возможностью открытия (в т.ч. c$). Просмотр и фильтрация логов (используется 3 журнала).

**Заимствованные:** TCP Viewer (источник: [winitpro](http://winitpro.ru)) - производит resolve FQDN для всех удаленных адресов и через Get-Process по ID определяет path исполняемого процесса. Подключение к Connection Broker (требуется установленный модуль RemoteDesktop) с возможностью Shadow-подключения к пользователю. Wake on Lan (источник: [coolcode](http://coolcode.ru)) - формирование Magic Packet c отправкой broadcast (MAC-адрес берется из формы ввода сообщения). Просмотр свободного места на разделах дисков (источник: [fixmypc](http://fixmypc.ru)) и по аналогии ОЗУ.

![Image alt](https://github.com/Lifailon/Remote-Shadow-Administrator/blob/rsa/Disk.jpg)

## **Собственные реализации:**

### **Поиск MAC-адреса компьютера**.
Используется в случае, если нужно узнать MAC-адрес компьютера, который уже не доступен. Производится по средствам просмотра ARP-таблиц на других серверах в кач-ве proxy. 2-й вариант, просмотр всех клиентов на сервере с ролью DHCP (установка модуля не требуется) в виде таблицы с сортировкой вывода. Используется для последующей отправки Magic Packet.

### **Скрипты по синхронизации компьютерных часов (w32tm):**
* Отображает текущее время на сервере и разницу с сервером источника (localhost). 
* Узнать источник времени, а так же частоту и время последней синхронизации (последнее отображается в зависимости от языкового пакета на удаленной машине). 
* Проверка сервера как источника времени. 
* Незамедлительно синхронизировать время на удаленном сервере с источником. 
* Изменить на удаленном сервере источник времени на ближайший DC в подсети.

![Image alt](https://github.com/Lifailon/Remote-Shadow-Administrator/blob/rsa/Times.jpg)

### **WMI:** 
* Просмотри списка обновлений с возможностью скопировать номер в буфер обмена. В связи с тем, что более не поддерживается удаление обновлений через WUSA в тихом режиме, используется в связке с DISM online (специально оставил отдельной вкладкой, можно автоматизировать сразу процесс удаления и/или отпарсить вывод dism).
* Удаленная проверка а так же включение/отключение rdp и nla. 
* Список установленных программ, используется два метода: get-packet и gwmi с возможностью удаления. Пример:

![Image alt](https://github.com/Lifailon/Remote-Shadow-Administrator/blob/rsa/Programs.jpg)

* **Инвентаризация комплектующих** - модель процессора, мат. платы, видеокарты, оперативной памяти, модели дисков, так же присутствует вывод всех сетевых адаптеро, очень часто в сканерах используется для инициализации хоста ВМ (раскомментировать строки 505-506) с ковертацией в HTML-файл:

![Image alt](https://github.com/Lifailon/Remote-Shadow-Administrator/blob/rsa/Report.jpg)

> На текущий момент есть одна нерешенная проблема с установкой программ, function wmi-installer (452-475 строки). Использовал install-package (сейчас используется этот вариант в меню: WMI - Установка) и 2 метода gwmi. В первом случае установка происходит не на всех серверах (не зависимо от использования версии TLS), в случае с wmi установка происходит из unc-пути только на тот же сервер, где лежит msi-пакет (в т.ч. через icm session и предварительной аудентификацией -credential, директория через icm доступна по пути). Самый простой вариант решения, это добавить в скрипт копирование пакета на удаленный сервер и его удаление после установки в случае с WMI.

По вопросам и предложениям **Telegram: @kup57**
