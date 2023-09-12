![Alt text](assets/logo.jpg?raw=true "BitrixDock")

# BitrixDock
BitrixDock позволяет легко и просто запускать **Bitrix CMS** на **Docker**.

:warning: **Этот проект, для того чтобы посмотреть демо Битрикса, а не запустить продакшн сайт. Примеры реальных продакшн сайтов ищите внизу страницы.**


## Введение
BitrixDock запускает демо Битрикса предоставляя готовые сервисы PHP, NGINX, MySQL и многие другие.

### Преимущества данной сборки
- Сервис PHP запакован в отдельный образ, чтобы избавить разработчиков от долгого компилирования.
- Остальные сервисы так же "причёсаны" и разворачиваются моментально.
- Ничего лишнего.

## Порядок разработки в Windows
Если вы работаете в Windows, то все заводится на штатном WSL2 + Docker Desktop

Как альтернативный вариант - можно поднять виртуальную машину (через Vagrant, VirtualBox, VMware и тп), тестировалось на Ubuntu 18.04.
Ваш рабочий проект должен хранится в двух местах, первое — локальная папка с проектами на хосте (открывается в IDE), второе — виртуальная машина
(например ```/var/www/bitrix```). Проект на хосте мапится в IDE к гостевой OC.

## Автоматическая установка для WSL (Рекомендуем)
```
curl -fsSL https://raw.githubusercontent.com/jackkiwema/bitrixdock/master/install.wsl.sh?$(date +%s) -o install.wsl.sh && chmod +x install.wsl.sh && sh install.wsl.sh
```

## Автоматическая установка для Виртуальной машины
```
curl -fsSL https://raw.githubusercontent.com/jackkiwema/bitrixdock/master/install.sh -o install.sh && chmod +x install.sh && sh install.sh
```

## Ручная установка
<details><summary>Читать</summary>
<p>

## Ручная установка
#### Зависимости
- Git
```
apt-get update && apt-get install -y git
```
- Docker & Docker-Compose
```
cd /usr/local/src && wget -qO- https://get.docker.com/ | sh && \
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
chmod +x /usr/local/bin/docker-compose && \
echo "alias dc='docker-compose'" >> ~/.bash_aliases && \
source ~/.bashrc
```

### Папки и файл Битрикс
```
mkdir -p /var/www/bitrix && \
cd /var/www/bitrix && \
wget https://www.1c-bitrix.ru/download/scripts/bitrixsetup.php && \
cd /var/www/ && \
git clone https://github.com/bitrixdock/bitrixdock.git && \
cd /var/ && chmod -R 775 www/ && chown -R root:www-data www/ && \
cd /var/www/bitrixdock
```

### Выполните настройку окружения

Скопируйте файл `.env_template` в `.env`

```
cp -f .env_template .env
```
⚠ Если у вас мак, удалите строчку `/etc/localtime:/etc/localtime/:ro` из docker-compose.yml

По умолчанию используется nginx, php 7.4, mysql. Настройки можно изменить в файле ```.env```. Также можно задать путь к каталогу с сайтом и параметры базы данных MySQL.


```
COMPOSE_PROJECT_NAME=bitrixdock  # Имя проекта. Используется для наименования контейнеров
PHP_VERSION=php74                # Версия php
WEB_SERVER_TYPE=nginx            # Веб-сервер nginx/apache
DB_SERVER_TYPE=mysql             # Сервер базы данных mysql/percona
MYSQL_DATABASE=bitrix            # Имя базы данных
MYSQL_USER=bitrix                # Пользователь базы данных
MYSQL_PASSWORD=123               # Пароль для доступа к базе данных
MYSQL_ROOT_PASSWORD=123          # Пароль для пользователя root от базы данных
INTERFACE=0.0.0.0                # На данный интерфейс будут проксироваться порты
SITE_PATH=/var/www/bitrix        # Путь к директории Вашего сайта

```
</p>
</details>

Если у вас всё получилось, будем благодарны за звёздочку :)
Ошибки ждём в [issue](https://github.com/bitrixdock/bitrixdock/issues)
Приятной работы!

## Запуск и остановка bitrixdock
### Запуск
```
docker compose -p bitrixdock up -d
```
Чтобы проверить, что все сервисы запустились посмотрите список процессов ```docker ps```.
Посмотрите все прослушиваемые порты, должны быть 80, 11211, 9000 ```netstat -plnt```.
Откройте IP машины в браузере.

### Остановка
```
docker compose -p bitrixdock stop
```

### Полное удаление
```
docker compose -p bitrixdock down
```
## Как заполнять подключение к БД
![db](https://raw.githubusercontent.com/bitrixdock/bitrixdock/master/assets/db.png)

## Примечание
- По умолчанию стоит папка ```/var/www/bitrix/```
- В настройках подключения требуется указывать имя сервиса, например для подключения к базе нужно указывать "db", а не "localhost". Пример [конфига](configs/.settings.php) с подключением к mysql и memcached.
- Для загрузки резервной копии в контейнер используйте команду: ```cat /var/www/bitrix/backup.sql | docker exec -i mysql /usr/bin/mysql -u root -p123 bitrix```
- При использовании php74 в production удалите строку с `php7.4-xdebug` из файла `php74/Dockerfile`, сам факт его установки снижает производительность Битрикса и он должен использоваться только для разработки

## Отличие от виртуальной машины Битрикс
Виртуальная машина от разработчиков Битрикс решает ту же задачу, что и BitrixDock - предоставляет готовое окружение. Разница лишь в том, что Docker намного удобнее, проще и легче в поддержке.

Как только вы запускаете виртуалку, Docker сервисы автоматически стартуют, т.е. вы запускаете свой минихостинг для проекта и он сразу доступен.

Если у вас появится новый проект и поменяется окружение, достаточно скопировать чистую виртуалку (если вы на винде), скопировать папку BitrixDock, добавить или заменить сервисы и запустить.

P.S.
Виртуальная машина от разработчиков Битрикс на Apache, а у нас на Nginx, а он работает намного быстрее и кушает меньше памяти.

## Использование xdebug.

- Настройки xdebug задаются в `phpXX/php.ini`.
- Для php73, php74 дефолтовые настройки xdebug - коннект на порт `9003` хоста, с которого пришел запрос. В случае невозможности коннекта - фаллбек на `host.docker.internal`.
- При изменении `php.ini` в проекте не забудьте добавить флаг `--build` при запуске `docker-compose`, чтобы форсировать пересборку образа.


# Ищем контрибьюторов
Помогите развитию проекта! Требуется закрывать задачи в [issue](https://github.com/bitrixdock/bitrixdock/issues)

# Пример
Пример реального Docker проекта для Bitrix - Single Node
https://github.com/bitrixdock/production-single-node

Ещё один проект с php7 и отправкой почты, взят с боевого проекта, вырезаны пароли, сертификаты и тп
https://github.com/bitrixdock/bitrixdock-production

Ещё один production проект с memcached композитом, php8.2, почтой и кроном в контейнере и развёрнутым Readme (англ.):
https://github.com/paskal/bitrix.infra

Реальные проекты на основе этих проектов работают годами без проблем если их не трогать )
![Alt text](assets/Clip2net_200727170318.png?raw=true "BitrixDock")

# Для контрибьюторов
1. Форкаем оригинальный проект https://github.com/bitrixdock/bitrixdock кнопкой Fork
2. Клонируем форк себе на компьютер
```
git clone https://github.com/my_account/bitrixdock
cd bitrixdock
```
3. Создаем новую ветку
```
git checkout -b myfix
```
4. Создаем upstream на оригинальный проект
```
git remote add upstream https://github.com/bitrixdock/bitrixdock
```
5. Меняем файлы
6. Делаем коммит и отправляем правки
```
git add .
git commit -am "My fixes"
git push -u origin new_branch
```
7. Переходим в свой проект ```https://github.com/my_account/bitrixdock``` и жмем кнопку Compare & pull request
8. Описываем какую проблему решает Пул Реквест с кратким описанием, зачем сделано изменение
9. Вы прекрасны! ;)


# Install BitrixDock on Ubuntu 20.04
```bash
# Docker and Docker Compose Install
sudo su -
curl -fsSL https://get.docker.com -o docker.sh
bash ./docker.sh
curl -L "https://github.com/docker/compose/releases/download/v2.20.3/docker-compose-linux-x86_64" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
echo "alias dc='docker-compose'" >> ~/.bash_aliases
source ~/.bashrc

# Bitrix Files Download
mkdir -p /var/www/bitrix
cd /var/www/bitrix
wget https://www.1c-bitrix.ru/download/scripts/bitrixsetup.php
cd /var/www/
git clone https://github.com/bitrixdock/bitrixdock.git
cd /var/ && chmod -R 775 www/ && chown -R root:www-data www/
cd /var/www/bitrixdock

# Environment Setup
cp -f .env_template .env

# Starting and Stopping Bitrixdock
docker compose -p bitrixdock up -d 

# Install Bitrix

# DB Configuration
db name: db
User: bitrix
password: 123
db: bitrix
```





