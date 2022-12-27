# ДЗ №3

Для успешного выполнения ДЗ вам необходимо установить ELK (elasticsearch, logstash, kibana).
Базовая операционная система - по вашему выбору.
После успешной установки ELK-стека вам необходимо настроить отправку логов sshd в elasticsearch через logstash.
Для этого вам придется изменить настройку rsyslog.
Проверьте создался ли index в elasticsearch.
После настройки отправки логов в ELK попробуйте настроить визуализацию логов от sshd в kibana.
В качестве результата ДЗ принимается: конфиг rsyslog, конфиг logstash и результат проверки index в elasticsearch, а также скриншот из kibana, если получилось настроить визуализацию.

## Установка ELK (elasticsearch, logstash, kibana)
### Установка elasticsearch
```
apt-get install default-jre
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
cat /etc/apt/sources.list.d/elastic-8.x.list
deb https://artifacts.elastic.co/packages/8.x/apt stable main
apt-get update
apt-get install elasticsearch
systemctl enable elasticsearch
systemctl start elasticsearch
```
Файл конфигурации находится в GAP-3.

### Установка Logstash
```
apt-get install logstash
systemctl enable logstash
systemctl start logstash
```
Файл конфигурации находится в GAP-3.

### Установка Logstash
```
apt-get install kibana
systemctl enable kibana
systemctl start kibana
```
Файл конфигурации находится в GAP-3.

Скриншоты и прочие конфигурационные файлы находятся в GAP-3.
