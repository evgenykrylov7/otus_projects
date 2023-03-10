# ДЗ №1

## На виртуальной машине установите любую open source CMS которая включает в себя следующие компоненты: nginx, php-fpm, database (MySQL or Postgresql)

Для развертывания виртуальной машины была выбрана среда VitualBox. OC – Debian 11.

### Обновление системы

```
apt update
apt upgrade
```
### Установка Nginx

```
apt-get install nginx
systemctl start nginx
systemctl enable nginx
systemctl status nginx
```

### Установка PHP и MariaDB Server

```
apt-get install php php-mysql php-fpm php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip mariadb-server mariadb-client
systemctl start mariadb
systemctl enable mariadb
systemctl status mariadb
systemctl start php8.1-fpm
systemctl enable php8.1-fpm
systemctl status php8.1-fpm
mysql_secure_installation
```

### Создание БД

```
CREATE DATABASE wordpress_db;
CREATE USER 'wordpress_user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress_db.* to wordpress_user@'localhost';
FLUSH PRIVILEGES;
exit 
```

### Установка CMS (WordPress)

```
wget -O /tmp/wordpress.tar.gz https://wordpress.org/latest.tar.gz
tar -xzvf /tmp/wordpress.tar.gz -C /var/www/html
chown -R www-data.www-data /var/www/html/wordpress
chmod -R 755 /var/www/html/wordpress
```

### Создание Nginx Virtual Host

```
nano /etc/nginx/conf.d/wordpress.conf
server {
        listen 80;
        listen [::]:80;
        root /var/www/html/wordpress;
        index index.php index.html index.htm;
        error_log /var/log/nginx/wordpress_error.log;
        access_log /var/log/nginx/wordpres_access.log;
        client_max_body_size 100M;
        location / {
                try_files $uri $uri/ /index.php?$args;
        }
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php8.1-fpm.sock;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
}
rm /etc/nginx/sites-enabled/default
 
rm /etc/nginx/sites-available/default
nginx -t
systemctl reload nginx
```
Завершение установки через Web интерфейс

## На этой же виртуальной машине установите Prometheus exporters для сбора метрик со всех компонентов системы (начиная с VM и заканчивая DB, не забудьте про blackbox exporter который будет проверять доступность вашей CMS)

### Установка node_exporter

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz
tar zxvf node_exporter-*.linux-amd64.tar.gz
cd node_exporter-*.linux-amd64
cp node_exporter /usr/local/bin/
useradd --no-create-home --shell /bin/false nodeusr
chown -R nodeusr:nodeusr /usr/local/bin/node_exporter
vi /etc/systemd/system/node_exporter.service

[Unit]
Description=Node Exporter Service
After=network.target
[Service]
User=nodeusr
Group=nodeusr
Type=simple
ExecStart=/usr/local/bin/node_exporter
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
[Install]
WantedBy=multi-user.target
systemctl daemon-reload
systemctl enable node_exporter
systemctl start node_exporter
```
Для сбора метрик со всех компонентов системы (начиная с VM и заканчивая DB, не забудьте про blackbox exporter который будет проверять доступность вашей CMS) 
(см. prometheus.yml и node_expoter.yml)

## На этой же или дополнительной виртуальной машине установите Prometheus задачей которого будет раз в 5 секунд собирать метрики с экспортеров

### Установка Prometheus

```
wget https://github.com/prometheus/prometheus/releases/download/v2.20.1/prometheus-2.20.1.linux-amd64.tar.gz
mkdir /etc/prometheus
mkdir /var/lib/Prometheus
tar zxvf prometheus-*.linux-amd64.tar.gz
cd prometheus-*.linux-amd64
cp prometheus promtool /usr/local/bin/
cp -r console_libraries consoles prometheus.yml /etc/Prometheus
useradd --no-create-home --shell /bin/false Prometheus
chown -R prometheus:prometheus /etc/prometheus /var/lib/Prometheus
chown prometheus:prometheus /usr/local/bin/{prometheus,promtool}
vi /etc/systemd/system/prometheus.service

[Unit]
Description=Prometheus Service
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
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
[Install]
WantedBy=multi-user.target
systemctl daemon-reload
systemctl enable Prometheus
chown -R prometheus:prometheus /var/lib/Prometheus
systemctl start Prometheus
systemctl status Prometheus
```

см. prometheus.yml
## На этой же или дополнительной виртуальной машине установите Alertmanager и сконфигурируйте его таким образом чтобы в случае недоступности какого либо компонента был отправлен alert с важность Critical в один из канал оповещений (канал оповещений на выбор: slack or telegram)

### Установка Alertmanager

```
wget https://github.com/prometheus/alertmanager/releases/download/v0.21.0/alertmanager-0.21.0.linux-amd64.tar.gz
mkdir /etc/alertmanager /var/lib/prometheus/alertmanager
tar zxvf alertmanager-*.linux-amd64.tar.gz
cd alertmanager-*.linux-amd64
cp alertmanager amtool /usr/local/bin/
cp alertmanager.yml /etc/alertmanager
useradd --no-create-home --shell /bin/false alertmanager
chown -R alertmanager:alertmanager /etc/alertmanager /var/lib/prometheus/alertmanager
chown alertmanager:alertmanager /usr/local/bin/{alertmanager,amtool}
vi /etc/systemd/system/alertmanager.service

[Unit]
Description=Alertmanager Service
After=network.target
[Service]
EnvironmentFile=-/etc/default/alertmanager
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/local/bin/alertmanager \
          --config.file=/etc/alertmanager/alertmanager.yml \
          --storage.path=/var/lib/prometheus/alertmanager \
          $ALERTMANAGER_OPTS
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
[Install]
WantedBy=multi-user.target
systemctl daemon-reload
systemctl enable alertmanager
systemctl start alertmanager
```

В случае недоступности какого либо компонента отправляется alert с важность Critical в Telergram. (см. alertmanager.yml и prometheus.yml)
