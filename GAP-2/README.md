# ДЗ №2

## На VM с установленным Prometheus установите Grafana последней версии доступной на момент выполнения дз

```
sudo apt-get install -y adduser libfontconfig1
wget https://dl.grafana.com/oss/release/grafana_9.3.2_amd64.deb
dpkg -i grafana_9.3.2_amd64.deb
ufw allow 3000/tcp
systemctl restart ufw
systemctl enable grafana-server
systemctl restart grafana-server
```

## Создайте внутри Grafana папки с названиями infra и app. Внутри директории infra создайте дашборд который будет отображать сводную информацию по инфраструктуре (CPU, RAM, Network, etc.). Внутри директории app создайте дашборд который будет отображать сводную информацию о CMS (доступность компонентов, время ответа, etc.)

Выполнено. Скриншоты в GAP-2

## Задание со звездочкой 1 - при помощи Grafana создайте alert о недоступности одного из компонентов CMS и инфраструктуры

Выполнено. Скриншоты в GAP-2
