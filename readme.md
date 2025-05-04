# Лабораторная работа №9. Оптимизация образов контейнеров

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
Для выполнения данной лабораторной работы я создаю репозиторий `containers09`. Для начала создаю внутри папку `site/`, где будет располагаться веб-сайт (скрин обязателен к просмотру).
![image](https://github.com/user-attachments/assets/91ad69f1-16f3-4247-b8fb-ba5d645f2165)  

---

Далее в `containers09` создаю файл `Dockerfile.raw` со следующим содержимым для оптимизации:
```dockerfile
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

Собираю образ с именем `mynginx:raw`:
![Снимок экрана 2025-05-04 130924](https://github.com/user-attachments/assets/a2c43e68-bb81-4a20-ab90-b44fbe1d3ea5)

---

Создаю также `Dockerfile.clean` для удаления временных файлов и неиспользуемых зависимостей:
```dockerfile
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

Собираю образ с именем `mynginx:clean`:
![Снимок экрана 2025-05-04 131136](https://github.com/user-attachments/assets/35fe8765-79d2-4450-b512-3eded93d30c7)  

Проверяю его размер - 225мб:
![Снимок экрана 2025-05-04 131153](https://github.com/user-attachments/assets/e3e2f3c7-8f1e-49bf-9dd4-eb31d943ad39)  

---

В новом файле `Dockerfile.few` уменьшаю количество слоев:
```dockerfile
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

Собираю образ с именем `mynginx:few`:
![Снимок экрана 2025-05-04 131218](https://github.com/user-attachments/assets/b715d3e8-704e-4791-b895-c0b582dc1654)  

Проверяю размер - 143 мб:
![Снимок экрана 2025-05-04 131235](https://github.com/user-attachments/assets/2611bcc4-39a0-4f8d-8d7d-97642d88fa7a)  

---

В файле `Dockerfile.alpine` заменяю базовый образ:
```dockerfile
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

Собираю образ `mynginx:alpine`:
![Снимок экрана 2025-05-04 131254](https://github.com/user-attachments/assets/9f2e10e2-4ec4-49b3-b04b-17bbc8e956f5)  

Проверяю размер - 19.5мб:
![Снимок экрана 2025-05-04 131316](https://github.com/user-attachments/assets/953fe6f0-8ac3-4d8c-99f2-4113d04d5d45)  

---

Затем я перепаковываю образ `mynginx:raw` в `mynginx:repack`:
![Снимок экрана 2025-05-04 131604](https://github.com/user-attachments/assets/7b54d179-ab4e-46a6-b26f-07ba2c6e5814)
![Снимок экрана 2025-05-04 131648](https://github.com/user-attachments/assets/4089df2c-9971-4664-9c79-705fe5bcb0e0)  

---

В файле `Dockerfile.min` создаю образ `mynginx:min` с использованием всех методов:
![Снимок экрана 2025-05-04 131749](https://github.com/user-attachments/assets/40666560-ba64-4b20-aa6d-8ecd2042aa5a)  

Перепаковываю образ `mynginx:minx` в `mynginx:min`:
![Снимок экрана 2025-05-04 132739](https://github.com/user-attachments/assets/8d402770-8d58-44ea-a18c-49bdb9635d1c)  

---

Проверяю размеры всех созданных образов:
![Снимок экрана 2025-05-04 132859](https://github.com/user-attachments/assets/438f3cb2-0d84-4561-b027-a9968f7b9754)  

Таблица с размерами данных образов в порядке возрастания:

| Репозиторий |   Тэг   | Размер |
|-------------|---------|--------|
| mynginx     | min     | 14.3мб |
| mynginx     | minx    | 14.5мб |
| mynginx     | alpine  | 19.5мб |
| mynginx     | few     | 143мб  |
| mynginx     | repack  | 212мб  |
| mynginx     | clean   | 225мб  |
| mynginx     | raw     | 225мб  |


## Вывод
В ходе лабораторной работы были созданы и оптимизированы Docker-образы веб-сервера на базе Nginx. Благодаря пошаговому улучшению `Dockerfile`: удаления временных файлов, уменьшения количества слоёв, использования минимального образа Alpine и последующей перепаковки, удалось сократить размер итогового образа.

## Контрольные вопросы
**1. Какой метод оптимизации образов вы считаете наиболее эффективным?**  
Наиболее эффективным является использование минимального базового образа, например, `alpine`. Он изначально занимает очень мало места и содержит только необходимые компоненты, что существенно снижает общий размер итогового образа. В сочетании с очисткой кэша и уменьшением количества слоёв это даёт максимальный эффект.

**2. Почему очистка кэша пакетов в отдельном слое не уменьшает размер образа?**  
В Docker каждый `RUN` создаёт новый слой, и данные из предыдущих слоёв сохраняются. Если кэш был загружен в одном слое, а очищен в следующем, то он по-прежнему присутствует в истории образа. Только объединение команд в один слой (через &&) позволяет реально удалить кэш до завершения слоя, тем самым уменьшая размер.

**3. Что такое перепаковка образа?**  
Перепаковка — это процесс создания нового образа путём экспорта файловой системы контейнера и импорта её обратно как новый образ. Этот метод удаляет историю слоёв и метаданные, что позволяет ещё больше уменьшить размер и "упростить" структуру образа.

