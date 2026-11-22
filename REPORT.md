# отчет
## на 4 балла

#### сборка и запуск

docker build -t counter-app .
docker images #проверка что он есть
docker run -d -p 8080:8000 -e GREETING="Привет из Docker" --name counter counter-app


#### проверка выполнения


stepan@Stepans-MacBook-Pro hw_3_template % curl localhost:8080/

{"greeting": "Привет из Docker", "count": 0, "hostname": "35d8be0590f6", "storage": "sqlite"%       
stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment

{"greeting": "Привет из Docker", "count": 1, "hostname": "35d8be0590f6", "storage": "sqlite"}%                                                                          stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment

{"greeting": "Привет из Docker", "count": 2, "hostname": "35d8be0590f6", "storage": "sqlite"}%                                                                          stepan@Stepans-MacBook-Pro hw_3_template % curl localhost:8080/

{"greeting": "Привет из Docker", "count": 2, "hostname": "35d8be0590f6", "storage": "sqlite"}%                                                                   

#### Логи

см logs/


## на 6 баллов

Проверка этой задачи выполнена с SQLite. В (см compose-конфиг коммита 095b701 на гитхабе) 
volume "counter-data" подключён к /data приложения. После "docker compose down" и "docker compose up -d" контейнер получил новый hostname, а значение счетчика сохранилось

#### контейнер пересоздан, счетчик сохранен

stepan@Stepans-MacBook-Pro hw_3_template % docker compose up -d

[+] up 1/1
 ✔ Container hw_3_template-app-1 Started                                        0.1s
stepan@Stepans-MacBook-Pro hw_3_template % docker compose ps                    
NAME                  IMAGE         COMMAND           SERVICE   CREATED          STATUS          PORTS
hw_3_template-app-1   counter-app   "python app.py"   app       12 seconds ago   Up 12 seconds   0.0.0.0:8080->8000/tcp, [::]:8080->8000/tcp
stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment

{"greeting": "Hello, Compose", "count": 1, "hostname": "19a80553ed3a", "storage": "sqlite"}%                                                                            stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment

{"greeting": "Hello, Compose", "count": 2, "hostname": "19a80553ed3a", "storage": "sqlite"}%                                                                            stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment

{"greeting": "Hello, Compose", "count": 3, "hostname": "19a80553ed3a", "storage": "sqlite"}%                                                                            stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment

{"greeting": "Hello, Compose", "count": 4, "hostname": "19a80553ed3a", "storage": "sqlite"}%                                                                            stepan@Stepans-MacBook-Pro hw_3_template % curl localhost:8080/

{"greeting": "Hello, Compose", "count": 4, "hostname": "19a80553ed3a", "storage": "sqlite"}%                                                                            stepan@Stepans-MacBook-Pro hw_3_template % docker compose down

[+] down 2/2
 ✔ Container hw_3_template-app-1 Removed                                        0.1s
 ✔ Network hw_3_template_default Removed                                        0.1s
stepan@Stepans-MacBook-Pro hw_3_template % docker volume ls

DRIVER    VOLUME NAME
local     hw_3_template_counter-data
stepan@Stepans-MacBook-Pro hw_3_template % docker compose up -d

[+] up 2/2
 ✔ Network hw_3_template_default Created                                        0.0s
 ✔ Container hw_3_template-app-1 Started                                        0.1s
stepan@Stepans-MacBook-Pro hw_3_template % curl localhost:8080/

{"greeting": "Hello, Compose", "count": 4, "hostname": "95cc8ad2d6bd", "storage": "sqlite"}%                                                                            


## отчет на 8 баллов

чуть-чуть поменял compose.yaml (пред версию можно посмотреть в гитхабе на коммите на 4-6 баллов), а именно:

1) сделал в компоузе 2 сервиса - app, redis
2) redis доступен по локальному адресу redis://redis:6379/0
3) redis собран из образа redis:7.4-alpine, запись на диск происходит через AOF, данные хранятся в volume подключенном к /data у redis

В публичном доступе только порт самого приложения (8080), redis доступен только внутри сети compose

### некоторые технические детали

#### healthcheck

redis: redis-cli ping | grep -q PONG

app: запрос к /healthz через python (urllib.request), тк в образе нет curl Проверяется /healthz чтобы приложение оставалось healthy, когда redis недоступен


app запускается только после того, как redis стал healthy (depends_on: condition: service_healthy)


### запуск

hw_3_template % docker compose up -d --build
stepan@Stepans-MacBook-Pro hw_3_template % docker compose up -d --build
 ✔ Image counter-app               Built                                            1.9s
 ✔ Container hw_3_template-redis-1 Healthy                                          0.0s
 ✔ Container hw_3_template-app-1   Running     

docker compose ps

NAME                    IMAGE              SERVICE   STATUS                    PORTS
hw_3_template-app-1     counter-app        app       Up 9 seconds (healthy)    0.0.0.0:8080->8000/tcp, [::]:8080->8000/tcp
hw_3_template-redis-1   redis:7.4-alpine   redis     Up 15 seconds (healthy)   6379/tcp

Оба сервиса healthy, у redis порт не виден на компе


### проверки

#### проверка что данные сохраняются при down/up 


stepan@Stepans-MacBook-Pro hw_3_template % curl localhost:8080/

{"greeting": "Hello, Compose", "count": 4, "hostname": "2dd06ffab516", "storage": "redis"}%                                                                                     stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment
{"greeting": "Hello, Compose", "count": 5, "hostname": "2dd06ffab516", "storage": "redis"}%                                                                                     stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment
{"greeting": "Hello, Compose", "count": 6, "hostname": "2dd06ffab516", "storage": "redis"}%                                                                                     stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment
{"greeting": "Hello, Compose", "count": 7, "hostname": "2dd06ffab516", "storage": "redis"}%                                                                                     stepan@Stepans-MacBook-Pro hw_3_template % curl localhost:8080/

{"greeting": "Hello, Compose", "count": 7, "hostname": "2dd06ffab516", "storage": "redis"}%                                                                                     stepan@Stepans-MacBook-Pro hw_3_template % docker compose down
[+] down 3/3
 ✔ Container hw_3_template-app-1   Removed                                          0.2s
 ✔ Container hw_3_template-redis-1 Removed                                          0.1s
 ✔ Network hw_3_template_default   Removed                                          0.1s
stepan@Stepans-MacBook-Pro hw_3_template % docker compose up -d

[+] up 3/3
 ✔ Network hw_3_template_default   Created                                          0.0s
 ✔ Container hw_3_template-redis-1 Healthy                                          5.6s
 ✔ Container hw_3_template-app-1   Started                                          5.7s
stepan@Stepans-MacBook-Pro hw_3_template % curl localhost:8080/

{"greeting": "Hello, Compose", "count": 7, "hostname": "5c805262588f", "storage": "redis"}%                                                                                     stepan@Stepans-MacBook-Pro hw_3_template % 

Оба контейнера удалены и созданы заново (hostname изменился), счетчик сохранился в volume (redis)

#### stop redis

stepan@Stepans-MacBook-Pro hw_3_template % docker compose stop redis
[+] stop 1/1
 ✔ Container hw_3_template-redis-1 Stopped                                          0.2s
stepan@Stepans-MacBook-Pro hw_3_template % docker compose ps
NAME                  IMAGE         COMMAND           SERVICE   CREATED              STATUS                        PORTS
hw_3_template-app-1   counter-app   "python app.py"   app       About a minute ago   Up About a minute (healthy)   0.0.0.0:8080->8000/tcp, [::]:8080->8000/tcp

stepan@Stepans-MacBook-Pro hw_3_template % curl -s -o /dev/null -w '%{http_code}\n' localhost:8080/healthz

200
stepan@Stepans-MacBook-Pro hw_3_template % curl -s -o /dev/null -w '%{http_code}\n' localhost:8080/readyz

503
stepan@Stepans-MacBook-Pro hw_3_template % curl -s -o /dev/null -w '%{http_code}\n' -X POST localhost:8080/increment

503


Приложение продолжает работать (healthy, /healthz = 200), хранилище недоступно: /readyz и POST /increment возвращают 503



#### start redis

stepan@Stepans-MacBook-Pro hw_3_template % docker compose start redis
[+] start 1/1
 ✔ Container hw_3_template-redis-1 Started                                          0.1s
stepan@Stepans-MacBook-Pro hw_3_template % docker compose ps
NAME                    IMAGE              COMMAND                  SERVICE   CREATED         STATUS                            PORTS
hw_3_template-app-1     counter-app        "python app.py"          app       4 minutes ago   Up 4 minutes (healthy)            0.0.0.0:8080->8000/tcp, [::]:8080->8000/tcp
hw_3_template-redis-1   redis:7.4-alpine   "docker-entrypoint.s…"   redis     4 minutes ago   Up 4 seconds (health: starting)   6379/tcp
stepan@Stepans-MacBook-Pro hw_3_template % curl localhost:8080/
{"greeting": "Hello, Compose", "count": 7, "hostname": "5c805262588f", "storage": "redis"}%                                                                                     stepan@Stepans-MacBook-Pro hw_3_template % curl -X POST localhost:8080/increment
{"greeting": "Hello, Compose", "count": 8, "hostname": "5c805262588f", "storage": "redis"}%                                                                                     stepan@Stepans-MacBook-Pro hw_3_template % 


После того как redis стал healthy, приложение без перезапуска (тот же hostname 2dd06ffab516) снова возвращает правильный счетчик

(еще один комментарий)
После еще одного запуска Redis запрос GET вернул сохраненное
значение 7, а следующий "POST /increment" вернул 8. В обоих
ответах hostname приложения был 5c805262588f (такой же, как до
остановки Redis). Приложение восстановило доступ к счетчику без
перезапуска.

stepan@Stepans-MacBook-Pro hw_3_template % curl -fsS --retry 10 --retry-delay 1 --retry-connrefused --max-time 2 \
  -w '\nHTTP %{http_code}\n' http://localhost:8080/readyz
{"status": "ready", "storage": "redis"}
HTTP 200
