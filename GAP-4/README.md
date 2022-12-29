# ДЗ №4

Для успешного выполнения дз вам нужно сконфигурировать hearthbeat, filebeat и metricbeat.
Heartbeat должен проверять доступность следующих ресурсов: otus.ru, google.com.
Metricbeat должен формировать метрики на основе показателей загрузки процессора и оперативной памяти.
Filebeat должен собирать логи ssh сервера. По собственному усмотрению вы можете собирать логи других сервисов которые присутствуют в системе ^_^
В качестве результата приложите конфиги hearthbeat, filebeat и metricbeat. Скриншот полученных данных отображенных в Kibana.

## Установка Beats
### Установка filebeat
```
apt install filebeat
filebeat setup
systemctl start filebeat
systemctl enable filebeat
```
Файл конфигурации находится в GAP-4.

### Установка metricbeat
```
apt-get install metricbeat
metricbeat setup
systemctl enable metricbeat
systemctl start metricbeat
```
Файл конфигурации находится в GAP-4.

### Установка heartbeat
```
apt-get install heartbeat
heartbeat setup
systemctl enable heartbeat-elastic
systemctl start heartbeat-elastic
```
Файл конфигурации находится в GAP-4.

Скриншоты находятся в GAP-4.
