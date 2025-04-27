# Лабораторная работа №9: Оптимизация образов контейнеров

## Студент

**Славов Константин, группа I2302**  
**Дата выполнения: _27.04.2025_**

## Цель работы

Целью данной лабораторной работы является знакомство с методами оптимизации образов.

## Задание

Сравнить различные методы оптимизации образов:

- Удаление неиспользуемых зависимостей и временных файлов
- Уменьшение количества слоев
- Минимальный базовый образ
- Перепаковка образа
- Использование всех методов

## Ход работы

**1. Создал репозиторий `containers09` и склонировал его к себе на компьютер. В папке `containers09` создал папку `site` и поместил туда файлы сайта, который я использовал для предыдущих лабораторных работ.**

![image]()

**2. Для оптимизации использовал образ определенный следующим `Dockerfile.raw`**

```sh
# create from ubuntu image
FROM ubuntu:latest

# update system
RUN apt-get update && apt-get upgrade -y

# install nginx
RUN apt-get install -y nginx

# copy site
COPY site /var/www/html

# expose port 80
EXPOSE 80

# run nginx
CMD ["nginx", "-g", "daemon off;"]
```

Все это я создал в папке `containers09`, затем собрал в образ `mynginx:raw`

```sh
docker image build -t mynginx:raw -f Dockerfile.raw .
```

![image]()

## Удаление неиспользуемых зависимостей и временных файлов

**1. Удалил временные файлы и неиспользуемые зависимости в `Dockerfile.clean`**

```sh
# create from ubuntu image
FROM ubuntu:latest

# update system
RUN apt-get update && apt-get upgrade -y

# install nginx
RUN apt-get install -y nginx

# remove apt cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# copy site
COPY site /var/www/html

# expose port 80
EXPOSE 80

# run nginx
CMD ["nginx", "-g", "daemon off;"]
```

**2. Собрал образ с именем `mynginx:clean` и проверил его размер:**

```sh
docker image build -t mynginx:clean -f Dockerfile.clean .
docker image list
```

Размер образа `mynginx:clean` составляет Х МБ.

![image]()

## Уменьшение количества слоев

**1. Уменьшил количество слоев в `Dockerfile.few`**

```sh
# create from ubuntu image
FROM ubuntu:latest

# update system
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y nginx && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# copy site
COPY site /var/www/html

# expose port 80
EXPOSE 80

# run nginx
CMD ["nginx", "-g", "daemon off;"]
```

**2. Собрал образ с именем `mynginx:few` и проверил его размер:**

```sh
docker image build -t mynginx:few -f Dockerfile.few .
docker image list
```

Размер образа `mynginx:few` составляет Х МБ.

![image]()

## Минимальный базовый образ

**1. Заменил базовый образ на `alpine` и пересобрал образ:**

```sh
# create from alpine image
FROM alpine:latest

# update system
RUN apk update && apk upgrade

# install nginx
RUN apk add nginx

# copy site
COPY site /var/www/html

# expose port 80
EXPOSE 80

# run nginx
CMD ["nginx", "-g", "daemon off;"]
```

**2. Собрал образ с именем `mynginx:alpine` и проверил его размер:**

```sh
docker image build -t mynginx:alpine -f Dockerfile.alpine .
docker image list
```

Размер образа `mynginx:alpine` составляет Х МБ.

![image]()

## Перепаковка образа

**1. Перепаковал образ `mynginx:raw` в `mynginx:repack`:**

```sh
docker container create --name mynginx mynginx:raw
docker container export mynginx | docker image import - mynginx:repack
docker container rm mynginx
docker image list
```

![image]()

## Использование всех методов

**1. Создал образ `mynginx:min` с использованием всех методов:**

```sh
# create from alpine image
FROM alpine:latest

# update system, install nginx and clean
RUN apk update && apk upgrade && \
    apk add nginx && \
    rm -rf /var/cache/apk/*

# copy site
COPY site /var/www/html

# expose port 80
EXPOSE 80

# run nginx
CMD ["nginx", "-g", "daemon off;"]
```

**2. Собрал образ с именем `mynginx:minx` и проверил его размер. Перепаковал образ `mynginx:minx` в `mynginx:min`:**

```sh
docker image build -t mynginx:minx -f Dockerfile.min .
docker container create --name mynginx mynginx:minx
docker container export mynginx | docker image import - myngin:min
docker container rm mynginx
docker image list
```

Размер образа `mynginx:min` составляет Х МБ.

![image]()

## Запуск и тестирование

**1. Проверил размеры образов:**

```sh
docker image list
```

![image]()

## Ответы на вопросы

## Библиография