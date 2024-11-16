#  Установка Linux Oracle на VirtualBox:

Следует установить образ Linux. Выделить 4 ядра, а также выделить 4096 мб оперативной памяти.



# Установка docker

устанавливает утилиту wget на систему

`•sudo yum install wget`

![image](https://github.com/user-attachments/assets/cd281b7a-df70-49cc-8450-1598a9ca02d7)

Надо будет скачать файл репозитория

`•sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo`

![image](https://github.com/user-attachments/assets/e4b08797-b243-457a-915c-8e8be1e0cc97)

Следует установить docker

`•sudo yum install docker-ce docker-ce-cli containerd.io`

![image](https://github.com/user-attachments/assets/7f1d6bcc-db85-41df-aa7a-f930aba0d26a)

![image](https://github.com/user-attachments/assets/a4a10eb9-bb04-4ea5-a021-86f819230187)

![image](https://github.com/user-attachments/assets/826ec2e0-cdfe-4a4c-851d-8d6dffc205be)

Запускаем его и разрешаем автозапуск

`•sudo systemctl enable docker --now`

![image](https://github.com/user-attachments/assets/51cdf1b6-db46-4f57-bc03-40afd3eb4403)



# Установка compose

Сначала нужно убедиться в наличии пакета curl

`•sudo yum install curl`

Объявить переменной COMVER, полученной в результате curl запроса. 

Она хранит в себе номер последней версии Docker Compose

`•COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

`•sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![image](https://github.com/user-attachments/assets/82a056db-98d6-4f0a-9b7b-11b5d623bf3f)

Потебуется предоставление прав на выполнение файла docker-compose

`•sudo chmod +x /usr/bin/docker-compose`

А также проверка установленной версии Docker Compose

`•sudo docker-compose --version`

![image](https://github.com/user-attachments/assets/31b099bc-4d98-4bda-bde0-a3d2a9c8eced)



# Занимаемся grafana

Проводим установку git

`•sudo yum install git`

![image](https://github.com/user-attachments/assets/a4aa5bce-12a1-41c2-8738-38d202bf0f43)

![image](https://github.com/user-attachments/assets/1c7b3dc4-ffed-417c-aa50-6ed9656a579d)

![image](https://github.com/user-attachments/assets/babda015-3890-430d-a121-fcaf63c698a3)

Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

`•sudo git clone https://github.com/skl256/grafana_stack_for_docker.git`

![image](https://github.com/user-attachments/assets/808ad99d-d53d-4bd1-89e0-16393131a605)

Заходит в папку - cd

`•cd grafana_stack_for_docker`

cd .. - возвращает в папку выше

Cоздаем папки двумя разными способами

`•sudo mkdir -p /mnt/common_volume/swarm/grafana/config`

`•sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}`

![image](https://github.com/user-attachments/assets/47500c09-9a28-46e3-8200-dfe384617460)

Выдаем права

`•sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}`

Создаем файл

`•sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini`

Копирование файлов

`•sudo cp config/* /mnt/common_volume/swarm/grafana/config/`

Переименовывание файла

`•sudo mv grafana.yaml docker-compose.yaml`

Собрать докер (нужно запускать из папки где docker-compose.yaml)

![image](https://github.com/user-attachments/assets/57c2ec71-5ec7-4854-b701-c50bd9a8db49)

`•sudo docker compose up -d`

Опустить докер - sudo docker compose stop

![image](https://github.com/user-attachments/assets/b0620a74-78a4-4545-bdcc-dac19be81ae4)



# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

`•sudo vi docker-compose.yaml`

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре. Потом следует в docker-compose нужно вставить node-exporter и удалить ненужные файлы (можно вставить готовый докер)

![image](https://github.com/user-attachments/assets/f41938b0-8934-42d9-a990-5de113e8bb13)

выйти не сохраняясь из vim - `esc -> :q!`, выйти сохраняясь из vim - `esc -> :wq!`

Заходим в другую папку 

`•cd /mnt/common_volume/swarm/grafana/config/`

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

`•sudo vi prometheus.yaml`

Далее нужно исправить targets: на exporter:9100

![image](https://github.com/user-attachments/assets/ef9a23e5-85d1-47ee-8697-a1c81cd4f05b)



#  Grafana

1. Переходим на сайт `localhost:3000`
    * User & Password GRAFANA: `admin`
    * Код графаны: `3000`
    * Код прометеуса: `http://prometheus:9090`
2. В меню выбираем вкладку Dashboards и создаем Dashboard
    * ждем кнопку +Add visualization, а после "Configure a new data source"
    * выбираем Prometheus
    * Connection
    * `http://prometheus:9090`
3. Authentication
    * Basic authentication
    * User: `admin`
    * Password: `admin`
    * Нажимаем на Save & test
4. Ищем вкладку меню  и выбираем Dashboards и создаем Dashboard
    * ждем кнопку "Import dashboard"
    * Find and import dashboards for common applications at grafana.com/dashboards: 1860 //ждем кнопку Load
    * Select Prometheus ждем кнопку "Import"

![image](https://github.com/user-attachments/assets/71fc841c-4c92-4fcc-afba-483d0f8a26a1)



#  VictoriaMetrics

Для начала изменим docker-compose.yaml. Для начала зайдем в нужную папку

`•cd grafana_stack_for_docker`

•`sudo vi docker-compose.yaml` - команда sudo открывает файл docker-compose.yaml в редакторе.

В самом текстовом редакторе после prometheus вставляем. Захом в connection
там где мы писали http//:prometheus:9090 пишем http:victoriametrics:9090 И заменяем имя из "Prometheus-2" в "yani"
нажимаем на dashboards add visualition выбираем "yani"
снизу меняем на "code"
Переходим в терминал и пишем

3. `echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus  ` - команда отправляет бинарные данные (например, метрики в формате Prometheus) на локальный сервер, который слушает на порту 8428.

•`curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'` - Команда делает запрос к API для получения данных по метрике OILCOINT_metric1. А также команда выводит информацию о типе и значении этой метрики в формате, который может быть использован системой мониторинга Prometheus.

Значение 0 меняем на любое другое

![image](https://github.com/user-attachments/assets/348cbd7a-8824-4b95-8600-f109b00de3e6)

Копируем переменную OILCOINT_metric1 и вставляем в query

Нажимаем run

![image](https://github.com/user-attachments/assets/11ffe25e-a7ed-4daf-9799-619c4d482dc8)

![image](https://github.com/user-attachments/assets/cef63faa-849e-4457-9307-fed668b8aa80)

Копируем переменную OILCOINT_metric1 и вставляем в code

![image](https://github.com/user-attachments/assets/739a9d3f-163b-40df-bc74-f5b76613a5f5)
