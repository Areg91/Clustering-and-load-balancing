Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки» 
Цель задания
В результате выполнения этого задания вы научитесь:

Настраивать балансировку с помощью HAProxy
Настраивать связку HAProxy + Nginx
Чеклист готовности к домашнему заданию
Установлена операционная система Ubuntu на виртуальную машину и имеется доступ к терминалу
Просмотрены конфигурационные файлы, рассматриваемые на лекции, которые находятся по ссылке
Инструкция по выполнению домашнего задания
Сделайте fork репозитория c шаблоном решения к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
Выполните клонирование этого репозитория к себе на ПК с помощью команды git clone.
Выполните домашнее задание и заполните у себя локально этот файл README.md:
впишите вверху название занятия и ваши фамилию и имя;
в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
для корректного добавления скриншотов воспользуйтесь инструкцией «Как вставить скриншот в шаблон с решением»;
при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в инструкции по MarkDown.
После завершения работы над домашним заданием сделайте коммит (git commit -m "comment") и отправьте его на Github (git push origin).
Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.

Задание 1
Запустите два simple python сервера на своей виртуальной машине на разных портах
Установите и настройте HAProxy, воспользуйтесь материалами к лекции по ссылке
Настройте балансировку Round-robin на 4 уровне.
На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy.
Решение 1

1. Запуск двух простых Python-серверов:
   - Запустите первый простой Python-сервер на порту 8000:
          python3 -m http.server 8000
     
   - Затем запустите второй простой Python-сервер на другом порту, например, 8001:
          python3 -m http.server 8001
     

2. Установка и настройка HAProxy:
   - Установите HAProxy командой:
          sudo apt install haproxy
     
  3. - Создайте конфигурационный файл HAProxy, например /etc/haproxy/haproxy.cfg:
          global
       log 127.0.0.1 local0 notice
       chroot /var/lib/haproxy
       pidfile /var/run/haproxy.pid
       maxconn 4096
       user haproxy
       group haproxy
       daemon
       stats socket /tmp/stats.sock mode 660 level admin expose-fd listeners
       tune.ssl.default-dh-param 2048
     defaults
       log global
       mode tcp
       option tcplog
       timeout connect 5s
       timeout client 50s
       timeout server 50s
     frontend http-in
       bind *:80
       default_backend servers
     backend servers
       balance roundrobin
       server srv1 localhost:8000 check inter 2000 rise 2 fall 3
       server srv2 localhost:8001 check inter 2000 rise 2 fall 3
     

4. Проверка конфигурации и настройка балансировки:
   - Откройте конфигурационный файл /etc/haproxy/haproxy.cfg в текстовом редакторе, например vi или nano.
   - Добавьте правила балансировки нагрузки с использованием алгоритма Round Robin (roundrobin) для обоих серверов.
   - Сохраните изменения и закройте файл.
   - Запустите HAProxy:
          sudo systemctl start haproxy
     

5. Проверка работы HAProxy и перенаправления запросов:
   - Используйте любой веб-браузер или утилиту для тестирования HTTP, например curl, чтобы отправить запрос на порт 80, который обрабатывается HAProxy.
   - Проверьте, что запросы равномерно распределены между двумя простыми Python-серверами.

### Скриншоты для отправки:

1. Конфигурационный файл haproxy.cfg.
2. Скриншот терминала с запуском простых Python-серверов.
3. Скриншот терминала с запуском HAProxy.
4. Скриншот результата запроса через HAProxy, показывающего перенаправление запросов на разные серверы.
![1](https://github.com/user-attachments/assets/0911655f-ac83-4127-810b-be6100988ed2)
![2](https://github.com/user-attachments/assets/ef114132-4f77-42e3-bd81-5ecbb950de5e)
![3](https://github.com/user-attachments/assets/487fa15d-46a2-4491-8bdc-986671a8f3a8)


Задание 2
Запустите три simple python сервера на своей виртуальной машине на разных портах
Настройте балансировку Weighted Round Robin на 7 уровне, чтобы первый сервер имел вес 2, второй - 3, а третий - 4
HAproxy должен балансировать только тот http-трафик, который адресован домену example.local
На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy c использованием домена example.local и без него.
Решение 2

1. Запуск трех простых Python-серверов:
   - Запустите первый простой Python-сервер на порту 8000:
     
Bash

     python3 -m http.server 8000
     
   - Затем запустите второй простой Python-сервер на другом порту, например, 8001:
     
Bash

     python3 -m http.server 8001
     
   - И наконец, запустите третий простой Python-сервер на еще одном порту, скажем, 8002:
     
Bash

     python3 -m http.server 8002
     

2. Установка и настройка HAProxy:
   - Установите HAProxy командой:
     
Bash

     sudo apt install haproxy
     
  3. - Создайте конфигурационный файл HAProxy, например /etc/haproxy/haproxy.cfg:
     
ini

     global
       log 127.0.0.1 local0 notice
       chroot /var/lib/haproxy
       pidfile /var/run/haproxy.pid
       maxconn 4096
       user haproxy
       group haproxy
       daemon
       stats socket /tmp/stats.sock mode 660 level admin expose-fd listeners
       tune.ssl.default-dh-param 2048
     defaults
       log global
       mode tcp
       option tcplog
       timeout connect 5s
       timeout client 50s
       timeout server 50s
     frontend http-in
       bind *:80
       acl host_example hdr(host) -i example.local
       use_backend servers if host_example
     backend servers
       balance weighted-roundrobin
       option httpchk GET /
       server srv1 localhost:8000 weight 2 check inter 2000 rise 2 fall 3
       server srv2 localhost:8001 weight 3 check inter 2000 rise 2 fall 3
       server srv3 localhost:8002 weight 4 check inter 2000 rise 2 fall 3
     

4. Проверка конфигурации и настройка балансировки:
   - Откройте конфигурационный файл /etc/haproxy/haproxy.cfg в текстовом редакторе, например vi или nano.
   - Добавьте правила балансировки нагрузки с использованием алгоритма Weighted Round Robin и весами для каждого сервера.
   - Добавьте условие для фильтрации трафика по домену example.local с помощью ACL.
   - Сохраните изменения и закройте файл.
   - Запустите HAProxy:
     
Bash

     sudo systemctl start haproxy
     

5. Проверка работы HAProxy и перенаправления запросов:
   - Используйте любой веб-браузер или утилиту для тестирования HTTP, например curl, чтобы отправить запрос на порт 80, который обрабатывается HAProxy.
   - Проверьте, что запросы равномерно распределены между тремя простыми Python-серверами, используя веса, определенные в конфигурационном файле.
   - Убедитесь, что запросы балансируются только для домена example.local.

### Скриншоты для отправки:

1. Конфигурационный файл haproxy.cfg.
2. Скриншот терминала с запуском простых Python-серверов.
3. Скриншот терминала с запуском HAProxy.
4. Скриншот результатов запроса через HAProxy, показывающего перенаправление запросов на разные серверы с использованием домена example.local.
   ![4](https://github.com/user-attachments/assets/750394dc-763d-4741-a7ed-cb6fb9babad5)
   ![5](https://github.com/user-attachments/assets/e225caa6-28c6-4089-acfb-5f4f68b6c2ed)
   ![6](https://github.com/user-attachments/assets/f6f75054-bfc7-449e-93d1-c63486ef2c11)
   ![7](https://github.com/user-attachments/assets/7eb05d45-e3dc-44e0-9e31-3762da7aeac8)
   $ curl http://example.local:80
   Таким образом, каждый раз запрос будет перенаправляться на соответствующий сервер с учетом его веса, обеспечивая равномерную нагрузку.



