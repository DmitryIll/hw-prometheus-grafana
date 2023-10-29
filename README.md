Задание 1*

Установите Prometheus.
Процесс выполнения

    Выполняя задание, сверяйтесь с процессом, отражённым в записи лекции
    Создайте пользователя prometheus
    Скачайте prometheus и в соответствии с лекцией разместите файлы в целевые директории
    Создайте сервис как показано на уроке
    Проверьте что prometheus запускается, останавливается, перезапускается и отображает статус с помощью systemctl

Требования к результату

    Прикрепите к файлу README.md скриншот systemctl status prometheus, где будет написано: prometheus.service — Prometheus Service Netology Lesson 9.4 — [Ваши ФИО]

    



Задание 2*

Установите Node Exporter.
Процесс выполнения

    Выполняя ДЗ сверяйтесь с процессом отражённым в записи лекции.
    Скачайте node exporter приведённый в презентации и в соответствии с лекцией разместите файлы в целевые директории
    Создайте сервис для как показано на уроке
    Проверьте что node exporter запускается, останавливается, перезапускается и отображает статус с помощью systemctl

Требования к результату

    Прикрепите к файлу README.md скриншот systemctl status node-exporter, где будет написано: node-exporter.service — Node Exporter Netology Lesson 9.4 — [Ваши ФИО]

Задание 3*

Подключите Node Exporter к серверу Prometheus.
Процесс выполнения

    Выполняя ДЗ сверяйтесь с процессом отражённым в записи лекции.
    Отредактируйте prometheus.yaml, добавив в массив таргетов установленный в задании 2 node exporter
    Перезапустите prometheus
    Проверьте что он запустился

Требования к результату

    Прикрепите к файлу README.md скриншот конфигурации из интерфейса Prometheus вкладки Status > Configuration
    Прикрепите к файлу README.md скриншот из интерфейса Prometheus вкладки Status > Targets, чтобы было видно минимум два эндпоинта

Дополнительные задания со звёздочкой*

Эти задания дополнительные. Их можно не выполнять. Это не повлияет на зачёт. Вы можете их выполнить, если хотите глубже разобраться в материале.
Задание 4*

Установите Grafana.
Требования к результату

    Прикрепите к файлу README.md скриншот левого нижнего угла интерфейса, чтобы при наведении на иконку пользователя были видны ваши ФИО

Задание 5*

Интегрируйте Grafana и Prometheus.
Требования к результату

    Прикрепите к файлу README.md скриншот дашборда (ID:11074) с поступающими туда данными из Node Exporter

Критерии оценки

    Выполнено минимум 3 обязательных задания
    Прикреплены требуемые скриншоты
    Задание оформлено в шаблоне с решением и опубликовано на GitHub


---

# Результаты

Установка prometheus

Создайте пользователя Prometheus:
Найдите последнюю версию Prometheus на GitHub: https://github.com/prometheus/prometheus/releases
а конкретно для AMD64 на линуксе:
https://github.com/prometheus/prometheus/releases/download/v2.47.2/prometheus-2.47.2.linux-amd64.tar.gz

Извлеките архив и скопируйте файлы в необходимые директории:

```
useradd --no-create-home --shell /bin/false prometheus 
wget https://github.com/prometheus/prometheus/releases/download/v2.47.2/prometheus-2.47.2.linux-amd64.tar.gz
tar -xvf prometheus-2.47.2.linux-amd64.tar.gz
mkdir /etc/prometheus
mkdir /var/lib/prometheus
cp ./prometheus promtool /usr/local/bin
cp -R ./console_libraries ./consoles/ ./prometheus.yml /etc/prometheus
chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus 
chown prometheus:prometheus /usr/local/bin/promtool /usr/local/bin/prometheus
```


или

```
chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool
```

Редактировать эту секцию
Запуск prometheus

и проверьте результат:

```
/usr/local/bin/prometheus --config.file /etc/prometheus/prometheus.yml --storage.tsdb.path /var/lib/prometheus/ --web.console.templates=/etc/prometheus/consoles --web.console.libraries=/etc/prometheus/console_libraries
```
зайти по адресу:
http://ip-addres:9090
Редактировать эту секцию
Создание сервиса для работы с Prometheus

nano /etc/systemd/system/prometheus.service
```
[Unit]
Description=Prometheus Service Netology Lesson 9.4
After=network.target
[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
--config.file /etc/prometheus/prometheus.yml \
--storage.tsdb.path /var/lib/prometheus/ \
--web.console.templates=/etc/prometheus/consoles \
--web.console.libraries=/etc/prometheus/console_libraries
ExecReload=/bin/kill -HUP $MAINPID Restart=on-failure
[Install]
WantedBy=multi-user.target
```
- проверить что бы в конце строк не было бы пробелов. они портят и не работает.

Проверяем:

systemctl enable prometheus
systemctl start prometheus
systemctl status prometheus

Но, сразу не запускается:

Смотрим ошибки:
```
tail -n 100 /var/log/syslog
```
Исправляем:
```
chown -R prometheus:prometheus /var/lib/prometheus
```
После этого запускается.
Редактировать эту секцию
Установка Node Exporter

Скачайте архив с Node Exporter и извлеките его:
https://github.com/prometheus/node_exporter/releases/
https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz

Перейдите в появившуюся папку:
````
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar xvfz node_exporter-*.*-amd64.tar.gz
cd node_exporter-*.*-amd64
./node_exporter

````
Node Exporter помогает извлекать данные с хоста, на котором установлен.
Редактировать эту секцию
Проверить работу экспортера

Можно проверить, что всё работает, перейдя по адресу:

    http://z.dmil.ru:9100/
    http://ip_сервера:9100/metrics

Редактировать эту секцию
Создать службу экспортера

Cкопируйте Node Exporter в папку Prometheus:
Передайте права на папку пользователю Prometheus:
``````
mkdir /etc/prometheus/node-exporter
cp ./* /etc/prometheus/node-exporter

```
Передайте права на папку пользователю Prometheus:

chown -R prometheus:prometheus /etc/prometheus/node-exporter/

Создайте сервис для работы с Node Exporter
Вставьте в файл сервиса следующее содержимое:
```
nano /etc/systemd/system/node-exporter.service
```
```
[Unit]
Description=Node Exporter Lesson 9.4
After=network.target
[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/etc/prometheus/node-exporter/node_exporter
[Install]
WantedBy=multi-user.target
```
Пропишите автозапуск:
Запустите сервис:
Проверьте статус сервиса:

systemctl enable node-exporter
systemctl start node-exporter
systemctl status node-exporter

Редактировать эту секцию
Добавление Node Exporter в Prometheus

Отредактируйте конфигурацию Prometheus:
Добавьте в scrape_config адрес экспортёра:
Перезапустите Prometheus:

nano /etc/prometheus/prometheus.yml

scrape_configs:
— job_name: 'prometheus'
scrape_interval: 5s
static_configs:
— targets: ['localhost:9090', 'localhost:9100']

- тут отступы съехали, но, нужно только добавить реально:

, 'localhost:9100'


Рестартруем:

systemctl restart prometheus
systemctl status prometheus

Редактировать эту секцию
Установка Grafana

Что скачивать можно тут см.:
https://grafana.com/grafana/download?pg=get&plcmt=selfmanaged-box1-cta1
Выбрать oss а не enterprise.

sudo apt-get install -y adduser libfontconfig1 musl
wget https://dl.grafana.com/oss/release/grafana_10.2.0_amd64.deb
sudo dpkg -i grafana_10.2.0_amd64.deb

Или старая версия:
Скачайте и установите deb-пакет:

wget https://dl.grafana.com/oss/release/grafana_9.2.4_amd64.deb
dpkg -i grafana_9.2.4_amd64.deb


Включите автозапуск и запустите сервер Grafana:

systemctl enable grafana-server
systemctl start grafana-server
systemctl status grafana-server


Проверьте статус, перейдя по адресу: https://<наш сервер>:3000

Стандартный логин и пароль: admin/admin

grafanadm*
Редактировать эту секцию
Импорт дашборда в графане

Например взять дашборт тут https://grafana.com/grafana/dashboards/1860-node-exporter-full/

можно взять id.

И этот id скопировать в гафану и сказать импорт.
Этот дашбборд только для нашего экспортера.
Для разных экспортеров разные шаблоны - дашборды.
в графане настроить импорт в разделе дашбордов.
