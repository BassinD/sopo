# # Отчет по разворачиванию Docker-контейнера

**Цель: ** развернуть Docker-контейнер веб-сервиса

## Ход работы

### Описание контенера
Для работы был выбран контейнер, подготовленный для учебных целей. В качестве запускаего приложени выступает Node.js приложение, разработанное в ходе выполнения курсового проекта на курсе "Веб-приложения", код которого расположен в следующем репозитории: https://github.com/BassinD/NIR-utils/tree/master/nodejs_game.

Dockerfile для формирования контейнера представлен в репозитории: https://github.com/BassinD/sopo/blob/main/docker/Dockerfile

### Среда выполнения
Работа выполнена на рабочей машине под управлением Linux Ubuntu 18.04.
Версия докера 18.06.3-ce.

### Разворачивание контейнера

Работа выполнена в терминале рабочей машины.
Выполненные команды и их результаты представлены ниже:

```
bassid1@bassid1-VirtualBox:~/etu/sopo/docker$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
bassid1@bassid1-VirtualBox:~/etu/sopo/docker$ docker pull bassind/nir-test-app
Using default tag: latest
latest: Pulling from bassind/nir-test-app
cbdbe7a5bc2a: Pull complete 
61a85185085d: Pull complete 
5b39b6139c1d: Pull complete 
90931dacd6cd: Pull complete 
50809824b43c: Pull complete 
6a1ef87bf522: Pull complete 
Digest: sha256:b7c87e210ec1a1103caf0b3e7a2652400f7f160fc386159597cfe56cc2544353
Status: Downloaded newer image for bassind/nir-test-app:latest
bassid1@bassid1-VirtualBox:~/etu/sopo/docker$ docker images | grep bassin
bassind/nir-test-app                            latest                    651e91951abd        4 months ago        173MB
bassid1@bassid1-VirtualBox:~/etu/sopo/docker$ docker run -p 7654:8080 651e91951abd

> plan_b@1.0.0 start /usr/src/app
> node server.js

(node:25) Warning: Accessing non-existent property 'padLevels' of module exports inside circular dependency
(Use `node --trace-warnings ...` to show where the warning was created)
Server run on port: 8080
^Cbassid1@bassid1-VirtualBox:~/etu/sopo/docker$ docker run -p -d 7654:8080 651e91951abd
docker: Invalid containerPort: -d.
See 'docker run --help'.
bassid1@bassid1-VirtualBox:~/etu/sopo/docker$ docker run -d -p 7654:8080 651e91951abd
8f27b02c9bbfc7544914ac5107e67a9f0c1158ab00bd73e1db2e97df1c9adbc9
bassid1@bassid1-VirtualBox:~/etu/sopo/docker$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
8f27b02c9bbf        651e91951abd        "npm run start"     5 seconds ago       Up 3 seconds        0.0.0.0:7654->8080/tcp   loving_galileo
bassid1@bassid1-VirtualBox:~/etu/sopo/docker$ netstat -tulnp | grep 7654
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp6       0      0 :::7654                 :::*                    LISTEN      -                   
bassid1@bassid1-VirtualBox:~/etu/sopo/docker$ wget http://localhost:7654
--2020-10-11 16:48:32--  http://localhost:7654/
Resolving localhost (localhost)... 127.0.0.1
Connecting to localhost (localhost)|127.0.0.1|:7654... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2601 (2,5K) [text/html]
Saving to: ‘index.html’

index.html                100%[==================================>]   2,54K  --.-KB/s    in 0s      

2020-10-11 16:48:32 (304 MB/s) - ‘index.html’ saved [2601/2601]

```
