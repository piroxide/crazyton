
# Установить Linux Oracle на VirtualBox:


Установить образ Linux

![image](https://github.com/piroxide/crazyton/blob/main/1.png)

Выделить 4 ядра

Выделить 4096 МБ оперативной памяти

![image](https://github.com/user-attachments/assets/6dcc2669-aefc-470c-a133-163215089fbf)


# Установка docker

Устанавливает утилиту wget на систему

     sudo yum install wget

![image](https://github.com/piroxide/crazyton/blob/main/2.png)


Скачиваем файл репозитория

     sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo

![image](https://github.com/piroxide/crazyton/blob/main/3.png)


Устанавливаем docker

     sudo yum install docker-ce docker-ce-cli containerd.io

![image](https://github.com/piroxide/crazyton/blob/main/4.png)

Запускаем его и разрешаем автозапуск

     sudo systemctl enable docker --now

![image](https://github.com/piroxide/crazyton/blob/main/5.png)



# Установка compose

Для начала нужно убедиться в наличии пакета curl

     sudo yum install curl

![image](https://github.com/piroxide/crazyton/blob/main/6.png)



Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

     COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

     sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose

![image](https://github.com/piroxide/crazyton/blob/main/7.png)



Предоставление прав на выполнение файла docker-compose

     sudo chmod +x /usr/bin/docker-compose

Проверка установленной версии Docker Compose

     sudo docker-compose --version

![image](https://github.com/piroxide/crazyton/blob/main/8.png)


# Делаем grafana

Установка git

     sudo yum install git

![image](https://github.com/piroxide/crazyton/blob/main/9.png)



Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

     sudo git clone https://github.com/skl256/grafana_stack_for_docker.git

![image](https://github.com/user-attachments/assets/0a6b8008-4b54-4b27-a3ef-754b24e57959)



Заходит в папку - cd

     cd grafana_stack_for_docker

cd .. - возвращает в папку выше


(После этого можно вставлять готовый docker-compose)

Cоздаем папки двумя разными способами

     sudo mkdir -p /mnt/common_volume/swarm/grafana/config

     sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}

Выдаем права

     sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}

Создаем файл

     sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini

Копирование файлов

     sudo cp config/* /mnt/common_volume/swarm/grafana/config/

Переименовывание файла

     sudo mv grafana.yaml docker-compose.yaml

![image](https://github.com/user-attachments/assets/894d0467-5354-48b7-ad3b-553326f4815f)


Собрать докер (нужно запускать из папки где docker-compose.yaml)

     sudo docker compose up -d

Опустить докер - sudo docker compose stop

![image](https://github.com/user-attachments/assets/bec1cf3c-484b-44b9-8da0-6bc8131aeac9)




# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi docker-compose.yaml

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/14df36d8-22eb-42ea-b544-b1269c0a0393)

выйти не сохраняясь из vim - esc -> :q!

выйти сохраняясь из vim - esc -> :wq!

Заходим в другую папку 

     sudo cd /mnt/common_volume/swarm/grafana/config

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi prometheus.yaml

![image](https://github.com/user-attachments/assets/df43bb29-972e-48ae-9224-6f8d222ebe0d)



Далее нужно исправить targets: на exporter:9100

![image](https://github.com/user-attachments/assets/d6dc7b39-175e-44e1-8fe3-e244cd85c825)


# Делаем grafana на сайте

   •	переходим на сайт localhost:3000

                User & Password GRAFANA: admin

                Код графаны: 3000

                Код прометеуса: http://prometheus:9090

   •	в меню выбираем вкладку Dashboards и создаем Dashboard

                ждем кнопку +Add visualization, а после "Configure a new data source"

                выбираем Prometheus

                Connection

                http://prometheus:9090

   •	Authentication

                Basic authentication

                User: admin

                Password: admin

                Нажимаем на Save & test и должно показывать зелёную галочку

   •	в меню выбираем вкладку Dashboards и создаем Dashboard

                ждем кнопку "Import dashboard"

                Find and import dashboards for common applications at grafana.com/dashboards: 1860 //ждем кнопку Load

                Select Prometheus ждем кнопку "Import"



![image](https://github.com/user-attachments/assets/0c2c1e9e-a2e9-484b-909c-03f10e7282f6)





# Делаем VictoriaMetrics

Для начала зайдем в нужную папку

     cd grafana_stack_for_docker

Открываем docker-compose

     sudo vi docker-compose.yaml

После prometheus вставляем vmagent (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/ad84d1c3-5449-4dc3-a8fd-e8ce10a098b2)



Захом в connection там где мы писали http://prometheus:9090 пишем http://victoriametrics:8428 И заменяем имя из "Prometheus-2" в "Vika" нажимаем на dashboards add visualition выбираем "Vika" снизу меняем на "code" Переходим в терминал и пишем

     echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus

• команда отправляет бинарные данные (например, метрики в формате Prometheus) на локальный сервер, который слушает на порту 8428.

     curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'

• команда делает запрос к API для получения данных по метрике OILCOINT_metric1

• команда выводит текст, который может быть использован для определения метрики в формате, совместимом с Prometheus

• команда выводит информацию о типе и значении этой метрики в формате, который может быть использован системой мониторинга Prometheus.

![image](https://github.com/user-attachments/assets/557aae50-2fcf-4334-be28-2c823d5bf964)

Значение 0 меняем на любое другое

Копируем переменную OILCOINT_metric1 и вставляем в query

Нажимаем run

![image](https://github.com/user-attachments/assets/03954f3d-1782-4658-9475-8d089be587c4)


Копируем переменную OILCOINT_metric1 и вставляем в code

![image](https://github.com/user-attachments/assets/8dff93c2-2a70-4a9a-8302-4670f4bd6f44)
