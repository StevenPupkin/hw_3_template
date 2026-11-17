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



