# Multiport

Веб-сервис для мониторинга и управления **Meshtastic** и **RNode** на одной панели.

![Интерфейс](https://github.com/user-attachments/assets/2048ca22-bff5-4341-9418-d0a9ee41378e)
![Интерфейс](https://github.com/user-attachments/assets/43883321-3a62-4489-984d-d5bfcdf37823)
![Интерфейс](https://github.com/user-attachments/assets/83893123-1513-4829-b9e8-755aba804866)
![Интерфейс](https://github.com/user-attachments/assets/5b324a1f-79d3-4bd0-a3ca-ed46ac489262)

## Требования к системе

### Аппаратные требования
- **Оперативная память:** минимум 512 МБ (рекомендуется 1 ГБ)
- **Процессор:** любой совместимый с Linux (ARM/x86_64)
- **USB-порты:** как минимум 1 свободный порт для подключения устройств
- **Сеть:** Ethernet или Wi-Fi подключение

### Программные требования
- **ОС:** Linux (Ubuntu 20.04+, Debian 11+, Raspberry Pi OS)
- **Python:** 3.10 или выше
- **Reticulum Network Stack:** для работы с RNode
- **Meshtastic CLI:** утилита командной строки Meshtastic

### Необходимые пакеты Python
```
flask
netifaces
pyserial
```

### Внешние зависимости
- `meshtastic` — CLI утилита (устанавливается через pip)
- `rnstatus` — утилита для мониторинга RNode
- Последовательные устройства: `/dev/ttyACM*`, `/dev/meshtastic`

---

## Инструкция по установке

### Шаг 1: Установка системных зависимостей

```bash
sudo apt update
sudo apt install -y python3-pip python3-venv python3-serial
```

### Шаг 2: Установка Python-пакетов

```bash
pip3 install flask netifaces pyserial meshtastic
```

### Шаг 3: Установка Reticulum и RNode

```bash
pip3 install rns
pip3 install rnode-firmware
```

### Шаг 4: Клонирование репозитория

```bash
cd ~
git clone <URL_репозитория> multiport
cd multiport
```

### Шаг 5: Настройка прав доступа к USB-устройствам

```bash
sudo usermod -aG dialout $USER
sudo udevadm control --reload-rules
```

### Шаг 6: Запуск приложения

```bash
# Установите токен для перезагрузки (опционально)
export REBOOT_TOKEN='your_secret_token'

# Запуск сервера
python3 multiport
```

Приложение будет доступно по адресу: `http://localhost:5002`

### Шаг 7: Автозапуск при загрузке (опционально)

Создайте systemd-сервис `/etc/systemd/system/multiport.service`:

```ini
[Unit]
Description=Multiport Web Service
After=network.target

[Service]
Type=simple
User=your_username
WorkingDirectory=/home/your_username/multiport
Environment="REBOOT_TOKEN=your_secret_token"
ExecStart=/usr/bin/python3 /home/your_username/multiport/multiport
Restart=always

[Install]
WantedBy=multi-user.target
```

Активируйте сервис:

```bash
sudo systemctl daemon-reload
sudo systemctl enable multiport
sudo systemctl start multiport
```

---

## Примеры использования

### Просмотр сообщений Meshtastic

После запуска откройте веб-интерфейс в браузере. Сообщения от узлов Meshtastic будут отображаться в реальном времени в разделе сообщений.

### Мониторинг статуса RNode

На главной панели отображается:
- Статус подключения RNode
- Значения метрик (частота, мощность передачи, фактор распространения)
- Список видимых узлов RNode

### Проверка состояния сети

В интерфейсе доступна информация о сетевых подключениях:
- IP-адрес Wi-Fi
- IP-адрес VPN (если подключено)

### Управление узлами

Через веб-интерфейс можно:
- Просматривать список известных узлов
- Видеть время последней активности
- Отслеживать уровень заряда батареи подключенных устройств

### Использование API

Приложение предоставляет JSON API для интеграции:

```bash
# Получить текущие данные
curl http://localhost:5002/api/data

# Перезагрузить устройство (требуется токен)
curl -X POST http://localhost:5002/reboot?token=your_secret_token
```

### Логирование

Сообщения Meshtastic логируются в файл `~/meshtastic_output.log`. Для просмотра в реальном времени:

```bash
tail -f ~/meshtastic_output.log
```

---

## Структура проекта

```
multiport/
├── multiport      # Основной исполняемый файл (Flask-приложение)
└── README.md      # Документация
```

---

## Лицензия

Проект распространяется без явной лицензии. Для коммерческого использования свяжитесь с автором.

---

## Поддержка

При возникновении проблем:
1. Убедитесь, что USB-устройства правильно подключены
2. Проверьте права доступа к последовательным портам
3. Убедитесь, что процессы `meshtastic` и `rnode` запущены
4. Проверьте логи приложения

Для вопросов и предложений создайте issue в репозитории.
