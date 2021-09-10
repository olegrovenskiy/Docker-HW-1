# Docker-HW-1

# Домашнее задание к занятию "5.3. Контейнеризация на примере Docker"


## Задача 1

  Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование докера? 
Или лучше подойдет виртуальная машина, физическая машина? Или возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

Высоконагруженное монолитное java веб-приложение;

	-Точно не докер. Нужны по максимуму рессурсы системы. Выбор между железным сервером и ВМ. 
На мой взгляд предподчительнее ВМ, т.к. можно на лету при необходимости корректировать ресурсы.


Go-микросервис для генерации отчетов;

	- ключевое слово микросервис - здесь Докер, единая среда для всех запусков


Nodejs веб-приложение;

	- Докер. Позволит упаковать приложение вместе с окружением.

Мобильное приложение c версиями для Android и iOS;

	- Тоже докер, то же самое, упаковка приложения вместе с окружением.


База данных postgresql используемая, как кэш;

	- Используемая как кэш, значит нужна бысьрая скорость отклика на запрос - аппаратный сервер

Шина данных на базе Apache Kafka;

	- Из хабр - Сегодня Kafka – это целая платформа, обеспечивающая избыточность, достаточную для хранения 
абсурдно огромных объемов данных. Здесь предоставляется шина сообщений с колоссальной пропускной способностью, 
на которой можно в реальном времени обрабатывать абсолютно все проходящие через нее данные.

	судя по определению - аппаратный сервер
	

Очередь для Logstash на базе Redis;

	- тоже подойдёт Докер контейнер. опять же всё окружение в одном месте


Elastic stack для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, 
два logstash и две ноды kibana;

	- для данной системы на мой взгляд предподчительна виртуализвция, например ВМваре.

Мониторинг-стек на базе prometheus и grafana;

	- Докер. В последнее время данные приложения разварачивают в докере.

Mongodb, как основное хранилище данных для java-приложения;

		- Виртуальная машина, требуется хранить данные, нет условий высоконагружености.

Jenkins-сервер.

	- Jenkins — это сервер непрерывной интеграции, написанный на Java. Соответсвенно здесь тоже Докер

## Задача 2
Скачал образ с апачем

	vagrant@vagrant:~$ sudo docker pull httpd
	Using default tag: latest
	latest: Pulling from library/httpd
	a330b6cecb98: Pull complete
	14e3dd65f04d: Pull complete
	fe59ad2e7efe: Pull complete
	2cb26220caa8: Pull complete
	3138742bd847: Pull complete
	Digest: sha256:af1199cd77b018781e2610923f15e8a58ce22941b42ce63a6ae8b6e282af79f5
	Status: Downloaded newer image for httpd:latest
	docker.io/library/httpd:latest
	vagrant@vagrant:~$
	
	vagrant@vagrant:~$ sudo docker images
	REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
	httpd        latest    f34528d8e714   7 days ago   138MB
	vagrant@vagrant:~$
	
запустил и проверил курлом с виртуалки на вагрант

	vagrant@vagrant:~$ sudo docker run --name httpd-1 -p 9090:80 -d httpd
	35509185e2be2bd0d872066947629649022f56e308bffe6297aeee9e3d38befc
	vagrant@vagrant:~$
	vagrant@vagrant:~$ sudo docker ps -a
	CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS                                   NAMES
	35509185e2be   httpd     "httpd-foreground"   8 seconds ago   Up 7 seconds   0.0.0.0:9090->80/tcp, :::9090->80/tcp   httpd-1
	vagrant@vagrant:~$
	vagrant@vagrant:~$
	vagrant@vagrant:~$ curl localhost:9090
	<html><body><h1>It works!</h1></body></html>
	vagrant@vagrant:~$

после корректирвки index.html в соответствии с заданием, курл выдал соответствующий результат:

	vagrant@vagrant:~$
	vagrant@vagrant:~$ curl localhost:9090
	<html>
        	<head>
          	      Hey, Netology
    	    </head>
   	     <body>
  	 	             <h1>I'm kinda devOps now </h1>
     	   </body>
	</html>


После чего был собран новый имедж


	vagrant@vagrant:~$
	vagrant@vagrant:~$ sudo docker ps -a
	CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS                                   NAMES
	35509185e2be   httpd     "httpd-foreground"   54 minutes ago   Up 54 minutes   0.0.0.0:9090->80/tcp, :::9090->80/tcp   httpd-1
	vagrant@vagrant:~$ sudo docker commit -m "New index.html" -a "Oleg rovenskiy" \35509185e2be or/newhttpd-hw
	sha256:cb16f36e6ba420398ff482b875f86087164ea761f29f248e60701e8e7bb85d33
	vagrant@vagrant:~$
	vagrant@vagrant:~$
	vagrant@vagrant:~$ sudo docker images
	REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
	or/newhttpd-hw   latest    cb16f36e6ba4   18 seconds ago   189MB
	nginx            latest    ad4c705f24d3   15 hours ago     133MB
	httpd            latest    f34528d8e714   7 days ago       138MB
	vagrant@vagrant:~$

сделан тег и запушен в репозиторий
  
	vagrant@vagrant:~$
	vagrant@vagrant:~$ sudo docker image push olegrovenskiy/newhttpd-hw1:1.0
	The push refers to repository [docker.io/olegrovenskiy/newhttpd-hw1]
	1cda1b87a58c: Layer already exists
	a5762756330a: Layer already exists
	d76ec8837f01: Layer already exists
	3453c54913b8: Layer already exists
	2136d1b3a4af: Layer already exists
	d000633a5681: Layer already exists
	1.0: digest: sha256:527994655f982f5e77b21ba06e81b58476251bc1e30f80349c321178cb6bb933 size: 1578
	vagrant@vagrant:~$

ссылка на репозиторий

https://hub.docker.com/layers/166657902/olegrovenskiy/newhttpd-hw1/1.0/images/sha256-527994655f982f5e77b21ba06e81b58476251bc1e30f80349c321178cb6bb933?context=repo


Задача 3

   Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку 
info из текущей рабочей директории на хостовой машине в /share/info контейнера;
Запустите второй контейнер из образа debian:latest в фоновом режиме, подключив папку 
info из текущей рабочей директории на хостовой машине в /info контейнера;
Подключитесь к первому контейнеру с помощью exec и создайте текстовый файл 
любого содержания в /share/info ;
Добавьте еще один файл в папку info на хостовой машине;
Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /info контейнера.

Шаги выполнения следующие:

	vagrant@vagrant:~$ sudo docker exec -ti first bash
	[root@56d9cb1a24f2 /]#
	[root@56d9cb1a24f2 /]#
	[root@56d9cb1a24f2 /]# cd /share/info
	[root@56d9cb1a24f2 info]#
	[root@56d9cb1a24f2 info]#
	[root@56d9cb1a24f2 info]# dir
	[root@56d9cb1a24f2 info]#
	[root@56d9cb1a24f2 info]#
	[root@56d9cb1a24f2 info]# nano 1.txt
	bash: nano: command not found
	[root@56d9cb1a24f2 info]# vi 1.txt
	[root@56d9cb1a24f2 info]# [root@56d9cb1a24f2 info]#
	[root@56d9cb1a24f2 info]#
	[root@56d9cb1a24f2 info]#
	[root@56d9cb1a24f2 info]#
	[root@56d9cb1a24f2 info]# cat 1.txt
	1
	2
	3
	4
	5
	6
	7

	[root@56d9cb1a24f2 info]#



	vagrant@vagrant:~/info$ vi 2
	vagrant@vagrant:~/info$ vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$ cat 2
	9
	8
	7
	6
	5
	4
	3
	2
	1
	0
	vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$


	vagrant@vagrant:~/info$
	vagrant@vagrant:~/info$ sudo docker exec -ti second bash
	root@f7cd0728ccf8:/#
	root@f7cd0728ccf8:/#
	root@f7cd0728ccf8:/#
	root@f7cd0728ccf8:/# cd /info
	root@f7cd0728ccf8:/info#
	root@f7cd0728ccf8:/info#

Итоговый листинг со второго контейнера

	root@f7cd0728ccf8:/info# ls -l
	total 8
	-rw-r--r-- 1 root root 15 Sep 10 10:33 1.txt
	-rw-rw-r-- 1 1000 1000 20 Sep 10 10:35 2
	root@f7cd0728ccf8:/info#














