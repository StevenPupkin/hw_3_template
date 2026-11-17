## Шаблон для третьего ДЗ по ИПР

Условие дз доступно по [ссылке](https://github.com/Nikfilk2030/missing_semester_hse_cs/blob/main/2026/amcs/year_1/hw_3/README.md)

## запуск

```
cp .env.example .env   #эту штуку можно менять
docker compose up -d --build
curl localhost:8080/
```

## настройки .env

APP_PORT: по умолчанию - 8080 
GREETING: по умолчанию - Hello, Docker

Данные SQLite хранятся в докер volume counter-data (папка /data)
и все данные сохраняются после docker "compose down"
