## Установка Oracle

**1. Загрузка репозитория и установка binary файлов:**

# Клонирование репозитория:

``cd $HOME``

``rm -rf slinky``

``git clone https://github.com/skip-mev/slinky.git``

``cd slinky``

``git checkout v0.4.3``

# Установка бинарников:

``make install``

``mv build/slinky /usr/local/bin/
rm -rf build``

# 2. Запуск Oracle:
# Создание системного файла:

```
sudo tee /etc/systemd/system/slinky.service > /dev/null <<EOF

[Unit]

Description=Initia Slinky Oracle

After=network-online.target

[Service]

User=$USER

ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 127.0.0.1:17990

Restart=on-failure

RestartSec=30

LimitNOFILE=65535


[Install]

WantedBy=multi-user.target

EOF
```

# Запуск сервиса:

``sudo systemctl daemon-reload
sudo systemctl enable slinky.service
sudo systemctl start slinky.service``

# 3. Валидация цен:

``make run-oracle-client``

# 4: Подключение Oracle Vote Extension:

```
cd
```

```
cd .initia/config
```

```
sudo nano app.toml
```

**Меняете значения таким же образом, как на скрине ниже:**

![sc](https://github.com/freshe4qa/initia/assets/85982863/d15f4de9-efc9-4903-bf3e-b7e4e8331ced)

# 5. Проверка системных логов:

``journalctl -fu slinky --no-hostname``
