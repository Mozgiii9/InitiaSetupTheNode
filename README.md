![image](https://github.com/Mozgiii9/InitiaSetupTheNode/assets/74683169/3b587196-5497-48fb-ac59-42c5d0fd1a81)

## Дата создания гайда: 15.05.2024

## Дата обновления гайда: 21.05.2024

## Обзор проекта:

**Initia — это совместимая сеть, в которой используется MoveVM, первая интеграция языка интеллектуальных контрактов Move.**

**- Инвестировали: $7 500 000;**

**- Инвесторы: Binance Labs, Delphi Digital и другие**

Initia анонсировали запуск вознаграждаемого тестнета, который продлится 8 недель и позволит нам изучить экосистему Initia, а также получить вознаграждения. У проекта интересная кампания, но нас интересует именно установка ноды, поскольку за нее нам тоже обещают вознаграждения. Помимо установки ноды, нодраннерам также необходимо выполнять задания для фарма XP. Более подробно можете ознакомиться [здесь](https://initia-xyz.notion.site/The-Initiation-Validator-Tasks-6d88ab0034644473907435662f9285b3?pvs=4).

**Информационные ресурсы проекта:**

**- Twitter: [клик](https://twitter.com/initiaFDN);**

**- Discord: [клик](https://discord.gg/drK2NVxy);**

**- Кран: [клик](https://faucet.testnet.initia.xyz/?address=);**

**- Эксплорер: [клик](https://scan.testnet.initia.xyz/initiation-1/validators)**

**Требования к серверу:**

**- CPU: 4 ядра;**

**- RAM: 16GB;**

**- Storage: 1 TB SSD;**
 
**- Скорость соединения: 100 Mbps**

## Инструкция по установке ноды Initia:

**1. Обновление пакетов сервера:**

```
sudo apt update && sudo apt upgrade -y
```

**2. Установка необходимого ПО для работы ноды:**

```
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**3. Скачаем скрипт для установки ПО ноды Initia. Задайте имя кошелька, имя ноды(moniker). Порт оставьте дефолтным(26):**

```
source <(curl -s https://itrocket.net/api/testnet/initia/autoinstall/)
```

**В конце скрипта начнется установка Snapshot'а, это довольно долгий процесс, который может занять до нескольких часов. Если пойдут логи(Height'ы) это значит, что нода успешно установлена**

**4. Проверим синхронизацию ноды:**

```
initiad status 2>&1 | jq
```

**Если статус "catching_up" равен "true", то это значит, что нода еще не синхронизирована. Ждем, когда "catching_up" станет "false" и только потом переходим к следущему шагу.**

**Синхронизация в Initia так же занимает продолжительное время. Вы можете отследить время окончания синхронизации при помощи команды ниже:**

```
. <(wget -qO- https://raw.githubusercontent.com/SecorD0/Initia/main/node_info.sh) 2>/dev/null
```

**5. Создадим кошелек:**

```
initiad keys add $WALLET
```

**Создаем пароль для кошелька, сервер попросит ввести его 2 раза. После чего сохраняем данные от кошелька в надежное место.**

```
WALLET_ADDRESS=$(initiad keys show $WALLET -a)
```

```
VALOPER_ADDRESS=$(initiad keys show $WALLET --bech val -a)
```

```
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
```

```
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
```

```
source $HOME/.bash_profile
```

**Еще раз проверим статус синхронизации ноды:**

```
initiad status 2>&1 | jq
```

**6. Переходим в [кран](https://faucet.testnet.initia.xyz/?address=), предварительно импортировав seed фразу от созданного кошелька в Metamask или Keplr. Запрашиваем тестовые токены в кране, сделать это можно 1 раз в 24 часа!**

**7. Проверим баланс кошелька командой:**

```
initiad q bank balances $WALLET_ADDRESS
```

**8. Создадим валидатора:**

```
initiad tx mstaking create-validator \
--amount 1000000uinit \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(initiad tendermint show-validator) \
--moniker "ИМЯ_ВАШЕЙ_НОДЫ" \
--identity "" \
--website "ССЫЛКА_НА_ВАШ_ТВИТТЕР_ИЛИ_ГИТХАБ" \
--details "" \
--chain-id initiation-1 \
--gas auto --fees 80000uinit \
```

**9. Теперь переходим к заполнению формы. Форма актуальна до 24 мая, после чего она будет закрыта. [Ссылка](https://forms.gle/HqLFePaka2NLmzY98) на форму.**

![изображение](https://github.com/Mozgiii9/InitiaSetupTheNode/assets/74683169/d0b29fcd-ccab-43cc-b0dc-785c735bb748)

**Узнать адрес валидатора(initvaloper...) можно через команду:**

```
initiad keys show $YOUR_WALLET_NAME --bech val -a
```

**Замените "YOUR_WALLET_NAME" на имя Вашего кошелька**

**Узнать RPC можно следующим образом:**

```
RPC="http://$(wget -qO- eth0.me)$(grep -A 3 "\[rpc\]" $HOME/.initia/config/config.toml | egrep -o ":[0-9]+")" && echo $RPC
```

```
curl $RPC/status
```

Если в ответ Вы получили "Connection refused", то необходимо сделать следующее:

```
sed -i '/\[rpc\]/,/\[/{s/^laddr = "tcp:\/\/127\.0\.0\.1:/laddr = "tcp:\/\/0.0.0.0:/}' $HOME/.initia/config/config.toml
```

```
sudo systemctl restart initiad
```

```
echo $RPC
```

**Вы можете вставить это в качестве URL адреса в браузере и убедиться, что все работает. Занесите полученный результат в значение RPC в форме.**

**Список полезных команд:**

**Информация о Вашей ноде:**

```
initiad status 2>&1 | jq
```

**Проверка логов:**

```
sudo journalctl -u initiad -f
```

**Просмотр всех кошельков на сервере:**

```
initiad keys list
```

**Импортировать кошелек:**

```
initiad keys add $WALLET --recover
```

**Проверка баланса:**

```
initiad q bank balances $WALLET_ADDRESS
```

**Делегировать токены самому себе:**

```
initiad tx staking delegate $(initiad keys show $WALLET --bech val -a) 1000000uinit --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit -y
```

**Делегировать токены другому валидатору:**

```
initiad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uinit --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit -y 
```

**Информация о тюрьме валидатора:**

```
initiad q slashing signing-info $(initiad tendermint show-validator)
```

**Освободить валидатора:**

```
initiad tx slashing unjail --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit -y
```

**Удалить ноду:**

```
sudo systemctl stop initiad
```

```
sudo systemctl disable initiad
```

```
sudo rm -rf /etc/systemd/system/initiad.service
```

```
sudo rm $(which initiad)
```

```
sudo rm -rf $HOME/.initia
```

```
sed -i "/INITIA_/d" $HOME/.bash_profile
```

## Обязательно проведите собственный ресерч проектов перед тем как ставить ноду. Сообщество NodeRunner не несет ответственность за Ваши действия и средства. Помните, проводя свой ресёрч, Вы учитесь и развиваетесь.

## Связь со мной: [Telegram(@M0zgiii)](https://t.me/m0zgiii)
## Мои соц. сети: [Twitter](https://twitter.com/m0zgiii)



