## Шаблон для третьего ДЗ по ИПР

Условие дз доступно по [ссылке](https://github.com/Nikfilk2030/missing_semester_hse_cs/blob/main/2026/amcs/year_1/hw_3/README.md)

# на 4-6 баллов


## запуск


cp .env.example .env   #эту штуку можно менять
docker compose up -d --build
curl localhost:8080/


## настройки .env

APP_PORT: по умолчанию - 8080 
GREETING: по умолчанию - Hello, Docker

Данные SQLite хранятся в докер volume counter-data (папка /data)
и все данные сохраняются после docker "compose down"


# на 8

## Запуск

cp .env.example .env
docker compose up -d --build
curl http://localhost:8080/

## Хранение данных

Приложение хранит счётчик в Redis у которого volume "redis-data"
подключён к /data сервиса redis

Redis сохраняет данные на диск через AOF

После "docker compose down" и повторного запуска значение
счётчика сохраняется. Команда "docker compose down -v" удаляет volume
вместе с данными

Порт Redis доступен внутри сети Compose. На хосте опубликован только
порт приложения

Команды и результаты проверок см в REPORT.md