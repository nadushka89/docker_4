# Контейнеризация семинары №4


![](https://github.com/nadushka89/docker_3/blob/main/source/docker.jpg?raw=true)

## Урок 4. Dockerfile и слои

Задание:
1. Сбилдить свой имейдж на основе имейджа mariaDB используя Dockerfile. Добавить какой-нибудь файл в имейдж. Можете еще в Dockerfile добавить функционал по уменьшению веса образа к примеру. - Оценка Хорошо
2. Сделать свой конфиг для mariaDB и заменить конфиг базового имейджа используя Dockerfile. Допустим поменять порт, максимальное использованеи памяти и т.д. - Оцена Отлично
3. Запустить phpmyadmin (в контейнере) и через веб проверить, что все введенные данные доступны. - Оцена Отлично

**Задание:**

Копируем файл конфигурации my.cnf, редактируем порт на 3307

![](https://github.com/nadushka89/docker_4/blob/main/source/2.png)

Запускаем временный контейнер на основе образа mariadb:10.10.2.
Внутри контейнера выполняем команду cat /etc/mysql/my.cnf, которая выводит содержимое файла my.cnf. редактируем  Dockerfile

```
docker run --rm mariadb:10.10.2 cat /etc/mysql/my.cnf > my.cnf
nano Dockerfile
```

![](https://github.com/nadushka89/docker_4/blob/main/source/1.png)

![](https://github.com/nadushka89/docker_4/blob/main/source/4.png)

```
FROM mariadb:10.10.2

COPY my.cnf /etc/mysql/my.cnf

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        curl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

CMD ["mysqld"]

```
Этот Dockerfile будет собирать новый образ на основе mariadb:10.10.2, копировать файл my.cnf с настройками конфигурации, установит пакет curl и запустит MariaDB, выполняя команду mysqld.

![](https://github.com/nadushka89/docker_4/blob/main/source/3.png)

Соберем Docker-образ. Команда docker run --name test-mariadb-mydocker -e MARIADB_ROOT_PASSWORD=test123 -d mymaria используется для запуска контейнера на основе Docker-образа "mymaria".

Запустим контейнер с инструментом phpMyAdmin на порт порт 8081
```
docker build -t mymaria .

docker run --name test-mariadb-mydocker -e MARIADB_ROOT_PASSWORD=test123 -d mymaria


docker run --name my-phpmyadmin-mydockerfile -d --link test-mariadb-mydocker:db -p 8081:80 phpmyadmin/phpmyadmin

```
![](https://github.com/nadushka89/docker_4/blob/main/source/5.png)

