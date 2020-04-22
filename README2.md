# Semux-tools

## Структура 
1. ```full-node``` - сервис узла Semux
2. ```db``` - сервис базы данных
3. ```pool-manager``` - сервис менеджера пулов

## Предварительная конфигурация

###  ```full-node```
Файл конфигурации
```
./full-node/config/semux.properties
```
Минимальные настройки включают изменния двух строк разделе ```API``` (Возможно, эти поля можно генерировать автоматически, без обращения участия пользователя):
```
api.username = user_name
api.password = user_pwd
```

**Если у Вас нет кошелька**, то предварительно его необходимо создать. Для этого:

Создать пустой файл и ограничить доступ к файлу
```
touch ./full-node/wallet.data
chmod 600 wallet.data
``` 

Запустить сервис ```fullnode``` для создания пароля:
```
sudo docker-compose run --rm fullnode
```
Появится запрос на задание повторение пароля. Задайте его и сохраните, он еще пригодится (можно воспользоваться командой ```pwgen -Bs 25 3``` и выбрать понравившийся пароль).
```
Please enter a new password:
Please repeat the new password:
```

В первой строке вывода, который появится после повторения пароля появится адрес  Вашего кошелька. Запишите его. Для него мы тоже найдем применение.
```
INFO     SemuxCli         A new account has been created for you: address = e49a5bddf7235e34da48a608dc2ee24bd8e4a4af
```

Дождитесь строки вида
```
INFO     SemuxSync        Block [number = 200, view = 0, hash = 32da907fbec0da4c6c50c5b22690ebd81f0b494f6a88a0fd95626c0705391aef, # txs = 0, # votes = 4]
```
чтоб убедиться, что все работает. 

Остановите сервис ```Ctrl-C```.

**Если у Вас был кошелек или Вы его только что создали**

Откройте файл ```docker-compose.yml```. Найдите блок ```fullnode```. Поставьте пароль от Вашего кошелька в поле 
```
SEMUX_WALLET_PASSWORD: wallet_pwd
```
Файл ```wallet.data```, если он у Вас уже существовал, поместите в папку ```full-node/```.

**Все, на этом настройка узла закончена.**

###  ```db```
Следующий этап, настоить базу данных.

В файле ```docker-compose.yml``` задайте поля:
```
#Настройки базы данных
POSTGRES_PASSWORD:  пороль пользователя базы данных
POSTGRES_USER:      имя пользователя базы данных
POSTGRES_DB:        название базы данных

#Настройки узла
FULLNODE_API_USER_NAME:     api.username из ./full-node/config/semux.properties
FULLNODE_API_USER_PASSWORD: api.password из ./full-node/config/semux.properties

#Настройки менеджера
PM_VALIDATOR_NAME:      название Вашего валидатора
PM_VALIDATOR_COMISSION: размер комиссии в процентах (0-100)
PM_POOLS_ADDR:          адреса валидаторов, с которыми менеджер будет работать, через запятую. Каждый адрес начинать с 0x
PM_PAYOUT_ADDR:         адрес, с которого совершаются выплаты
PM_START_BLOCK_ID:      номер блока, с которого следует начинать расчет выплат (можно найти на сайте https://semux.top)
PM_PAYMENT_PERIOD:      период выплат, выражается в количестве блоков
PM_TX_FEE:              комиссия за транзакцию (минимум 0.05 SEM)
PM_MIN_PAYOUT:          минимальный размер выплаты (нано)
PM_VOTES_MIN_AGE:       минимальный время существования голосов за Ваш пул, измеряется в блоках
```
Если адрес, с которого совершаются выплаты отличается от адреса валидатора, то ...

Установите права доступа к файлу ```docker-compose.yml```
```
chmod 600 docker-compose.yml
```

###  ```pool-manager```
Осталось настроить менеджер пула

В файле ```/pool-manager/config.json``` в блоке ```default``` блока  ```db_connections``` поправить содержимое поля ```connection_str```:
```
"connection_str":"dbname=db_name hostaddr=172.20.128.1 port=5432 connect_timeout=5 user=db_user password=db_user_pwd"
```
1. ```dbname``` - имя базы данных, которое установлено в ```POSTGRES_DB```
2. ```user``` -  имя пользователя базы данных, которое установлено в ```POSTGRES_USER```
3. ```password``` - пароль пользователя базы данных, который установлен в ```POSTGRES_PASSWORD```

#### Обновление параметров работы менеджера
Если появилась необходимость изменить настройки менеджера то необходимо изменить запись в таблице ```poolmanager.config```
Для этого нужно получить консоль базы
```
sudo docker exec -it <название контейнера> psql -U <имя пользовател базы> -d <название базы>
```

В ответ Вы получите приглашение:
```
<название базы>=#
```

Выполните команду ``` select * from pool_manager.config ```, скопируйте содержимое поля ``` data ```.

Для обновления записи следует выполнить запрос к базе данных
```
update pool_manager.config set data='{"id": 1, "name": "PM_VALIDATOR_NAME", "tx_fee": PM_TX_FEE, "comission": PM_VALIDATOR_COMISSION*100, "pools_addr": [PM_POOLS_ADDR], "payout_addr": "PM_PAYOUT_ADDR", "votes_min_age": PM_VOTES_MIN_AGE, "minimal_payout": PM_MIN_PAYOUT, "payment_period": PM_PAYMENT_PERIOD, "start_block_id": PM_START_BLOCK_ID}'::jsonb;
```

## Запуск всех сервисов
```
sudo docker-compose up -d
sudo docker container restart semux-tools_poolmanager_1
```

