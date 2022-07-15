# Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера" - dev-17_virt3-yakovlev_vs

## Задача 1

Сценарий выполения задачи:

- создайте свой репозиторий на https://hub.docker.com;
- выберете любой образ, который содержит веб-сервер Nginx;
- создайте свой fork образа;
- реализуйте функциональность:
запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.

Решение

https://hub.docker.com/r/valdem88/netology-virt3

## Задача 2

Посмотрите на сценарий ниже и ответьте на вопрос:
"Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

- Высоконагруженное монолитное java веб-приложение;
- Nodejs веб-приложение;
- Мобильное приложение c версиями для Android и iOS;
- Шина данных на базе Apache Kafka;
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
- Мониторинг-стек на базе Prometheus и Grafana;
- MongoDB, как основное хранилище данных для java-приложения;
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.

Решение

1) Высоконагруженное монолитное java веб-приложение;

Думаю физический сервер. Монолитное, следовательно в микросерверах не реализуемо без изменения кода,
   и так как высоконагруженное - то необходим физический доступ к ресурсами, без использования гипервизора виртуалки. 
2) Nodejs веб-приложение

Докер хорошо подойдёт. В рамках микропроцессрной архитектуры должно быть хорошим решением.

3) Мобильное приложение c версиями для Android и iOS

Ни разу не сталкивался по своему опыту с разработкой мобильных приложений, но
из того что пишут в глобальной сети Докер вполне применим для подобных сценариев.

4) Шина данных на базе Apache Kafka

Думаю это зависит от передаваемых данных или контура (тест/прод). Для прода и критичности данных лучше использовать вируалку. Для теста можно использовать Докер (контейнеры). 

5) Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;

Для создания кластера elasticsearch можно использовать docker-compose и пользоваться преимуществами работы с контейнерами.

6) Мониторинг-стек на базе Prometheus и Grafana

Для Prometheus и Grafana тоже самое(Докер подходит). Плюсы - скорость развертывания, возможность масштабирования для различных задач.

7) MongoDB, как основное хранилище данных для java-приложения

Пишут, что Докер подходит, но я бы использовал виртуальную машину (хранить данные надежнее в ВМ). 

8) Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry

Виртуальная машина. Серверу GitLab не требуется масштабирование или деплой новой версии несколько раз в день, а виртуальная машина позволит очень удобно делать бекапы и при необходимости мигрировать её в кластере.

## Задача 3

- Запустите первый контейнер из образа ***centos*** c любым тэгом в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```;
- Добавьте еще один файл в папку ```/data``` на хостовой машине;
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.

Решение 

```bash
root@server1:~# docker run -it -d --name centos_test -v ~/date_test/:/date centos
a4550528647f1c9d76bfba17817a6693e73ca146a2638b9f4dc0cb73581b80f2
root@server1:~# docker run -it -d --name debian_test -v ~/date_test/:/date debian
85b3e3b2b798f8e3248f757e44f1a3f00e143028af394e6a2c33f9f5045b2e95
root@server1:~# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
85b3e3b2b798   debian    "bash"        4 seconds ago   Up 3 seconds             debian_test
a4550528647f   centos    "/bin/bash"   5 minutes ago   Up 5 minutes             centos_test
```
```bash
root@server1:~# docker exec -it centos_test bash
[root@a4550528647f /]# cd date/
[root@a4550528647f date]# touch test.txt
```
```bash
root@server1:~/date_test# nano hello_netology.txt
root@server1:~/date_test# cat hello_netology.txt 
Hello Netology!
root@server1:~/date_test# nano hallo_netology2.txt
```
Листинг из терминала локального хоста

```bash
root@server1:~/date_test# ls
hallo_netology2.txt  hello_netology.txt  test.txt
```
Листинг из терминала centos

```bash
root@server1:~# docker exec -it centos_test bash
[root@a4550528647f /]# ls /date/
hello_netology.txt  hello_netology2.txt  test.txt
```

Листинг из терминала debian

```bash
root@server1:~# docker exec -it debian_test bash
root@85b3e3b2b798:/# ls /date/
hello_netology.txt  hello_netology2.txt  test.txt
```

## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

Соберите Docker образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.

Dockerfile
```dockerfile
# Манифест Docker образа.
FROM alpine:3.14
RUN  CARGO_NET_GIT_FETCH_WITH_CLI=1 && \
     apk --no-cache add \
      sudo python3 py3-pip openssl ca-certificates sshpass openssh-client rsync git && \
     apk --no-cache add \
      --virtual build-dependencies python3-dev libffi-dev musl-dev gcc cargo openssl-dev \
        libressl-dev \
        build-base && \
      pip install --upgrade pip wheel && \
      pip install --upgrade cryptography cffi && \
      pip install ansible==2.9.24 && \
      pip install mitogen ansible-lint jmespath && \ 
      pip install --upgrade pywinrm && \ 
      apk del build-dependencies && \ 
      rm -rf /var/cache/apk/* && \ 
      rm -rf /root/.cache/pip && \
      rm -rf /root/.cargo
      RUN  mkdir /ansible && \
           mkdir -p /etc/ansible && \
           echo 'localhost' > /etc/ansible/hosts
      
      WORKDIR /ansible
      
      CMD  [ "ansible-playbook", "--version" ]
```

```bash
root@server1:~# docker build -t valdem88/ansible:1.1.0 .
Sending build context to Docker daemon  28.16kB
Step 1/5 : FROM alpine:3.14
3.14: Pulling from library/alpine
8663204ce13b: Pull complete
Digest: sha256:06b5d462c92fc39303e6363c65e074559f8d6b1363250027ed5053557e3398c5
Status: Downloaded newer image for alpine:3.14
 ---> e04c818066af
Step 2/5 : RUN  CARGO_NET_GIT_FETCH_WITH_CLI=1 &&      apk --no-cache add       sudo python3 py3-pip openssl ca-certificates sshpass openssh-client rsync git &&      apk --no-cache add       --virtual build-dependencies python3-dev libffi-dev musl-dev gcc cargo openssl-dev         libressl-dev         build-base &&       pip install --upgrade pip wheel &&       pip install --upgrade cryptography cffi &&       pip install ansible==2.9.24 &&       pip install mitogen ansible-lint jmespath &&       pip install --upgrade pywinrm &&       apk del build-dependencies &&       rm -rf /var/cache/apk/* &&       rm -rf /root/.cache/pip &&       rm -rf /root/.cargo
 ---> Running in 3b5883d801fc
fetch https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz
(1/55) Installing ca-certificates (20211220-r0)
(2/55) Installing brotli-libs (1.0.9-r5)
(3/55) Installing nghttp2-libs (1.43.0-r0)
(4/55) Installing libcurl (7.79.1-r2)
(5/55) Installing expat (2.4.7-r0)
(6/55) Installing pcre2 (10.36-r1)
(7/55) Installing git (2.32.3-r0)
(8/55) Installing openssh-keygen (8.6_p1-r3)
(9/55) Installing ncurses-terminfo-base (6.2_p20210612-r0)
(10/55) Installing ncurses-libs (6.2_p20210612-r0)
(11/55) Installing libedit (20210216.3.1-r0)
(12/55) Installing openssh-client-common (8.6_p1-r3)
(13/55) Installing openssh-client-default (8.6_p1-r3)
(14/55) Installing openssl (1.1.1q-r0)
(15/55) Installing libbz2 (1.0.8-r1)
(16/55) Installing libffi (3.3-r2)
(17/55) Installing gdbm (1.19-r0)
(18/55) Installing xz-libs (5.2.5-r1)
(19/55) Installing libgcc (10.3.1_git20210424-r2)
(20/55) Installing libstdc++ (10.3.1_git20210424-r2)
(21/55) Installing mpdecimal (2.5.1-r1)
(22/55) Installing readline (8.1.0-r0)
(23/55) Installing sqlite-libs (3.35.5-r0)
(24/55) Installing python3 (3.9.5-r2)
(25/55) Installing py3-appdirs (1.4.4-r2)
(26/55) Installing py3-chardet (4.0.0-r2)
(27/55) Installing py3-idna (3.2-r0)
(28/55) Installing py3-urllib3 (1.26.5-r0)
(29/55) Installing py3-certifi (2020.12.5-r1)
(30/55) Installing py3-requests (2.25.1-r4)
(31/55) Installing py3-msgpack (1.0.2-r1)
(32/55) Installing py3-lockfile (0.12.2-r4)
(33/55) Installing py3-cachecontrol (0.12.6-r1)
(34/55) Installing py3-colorama (0.4.4-r1)
(35/55) Installing py3-contextlib2 (0.6.0-r1)
(36/55) Installing py3-distlib (0.3.1-r3)
(37/55) Installing py3-distro (1.5.0-r3)
(38/55) Installing py3-six (1.15.0-r1)
(39/55) Installing py3-webencodings (0.5.1-r4)
(40/55) Installing py3-html5lib (1.1-r1)
(41/55) Installing py3-parsing (2.4.7-r2)
(42/55) Installing py3-packaging (20.9-r1)
(43/55) Installing py3-toml (0.10.2-r2)
(44/55) Installing py3-pep517 (0.10.0-r2)
(45/55) Installing py3-progress (1.5-r2)
(46/55) Installing py3-retrying (1.3.3-r1)
(47/55) Installing py3-ordered-set (4.0.2-r1)
(48/55) Installing py3-setuptools (52.0.0-r3)
(49/55) Installing py3-pip (20.3.4-r1)
(50/55) Installing libacl (2.2.53-r0)
(51/55) Installing popt (1.18-r0)
(52/55) Installing zstd-libs (1.4.9-r1)
(53/55) Installing rsync (3.2.3-r4)
(54/55) Installing sshpass (1.09-r0)
(55/55) Installing sudo (1.9.7_p1-r1)
Executing busybox-1.33.1-r7.trigger
Executing ca-certificates-20211220-r0.trigger
OK: 98 MiB in 69 packages
fetch https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz
(1/39) Upgrading libcrypto1.1 (1.1.1n-r0 -> 1.1.1q-r0)
(2/39) Upgrading libssl1.1 (1.1.1n-r0 -> 1.1.1q-r0)
(3/39) Installing pkgconf (1.7.4-r0)
(4/39) Installing python3-dev (3.9.5-r2)
(5/39) Installing linux-headers (5.10.41-r0)
..........
Successfully built 9e557893cb7d
Successfully tagged valdem88/ansible:1.1.0
```
```bash
root@server1:~# docker image ls
REPOSITORY                TAG              IMAGE ID       CREATED          SIZE
valdem88/ansible          1.1.0            9e557893cb7d   42 seconds ago   246MB
valdem88/netology-virt3   1.1.0-netology   95032f43a958   16 hours ago     142MB
nginx                     latest           41b0e86104ba   3 days ago       142MB
debian                    latest           123c2f3835fd   3 days ago       124MB
ubuntu                    latest           27941809078c   5 weeks ago      77.8MB
alpine                    3.14             e04c818066af   3 months ago     5.59MB
hello-world               latest           feb5d9fea6a5   9 months ago     13.3kB
centos                    latest           5d0da3dc9764   10 months ago    231MB
```

https://hub.docker.com/r/valdem88/ansible