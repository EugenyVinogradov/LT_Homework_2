# Домашнее задание к лекции 2 «‎Подготовка стенда нагрузочного тестирования»

### Задание

1. Развернуть систему мониторинга на базе `telegraf` + `influxDB`.
2. Развернуть систему мониторинга на базе  `node-exporter` + `prometheus`.
3. Для отображения использовать дашборды `grafana`.
4. Настроить мониторинг на отображение всех аппаратных метрик вашего сервера (компьютера).
5. **Для telegraf**: сделать частоту отправки метрик каждые 60 секунд, подключить метрики оперативной памяти и swap, жёсткого диска и сети.
6. **Для prometheus**: сделать частоту отправки метрик каждые 36 секунд.
7. Сделать скриншоты состояния метрик системы в покое длительностью минимум 15 минут.
---
## 1. `telegraf` + `influxDB`
![image](https://user-images.githubusercontent.com/98056528/216752804-8fa09b0d-8be5-46a7-8503-eee5587d227a.png)
### Файл конфигурации telegraf.conf:
```
# Configuration for telegraf agent
[agent]
  interval = "60s"
  round_interval = true
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_interval = "5s"
  flush_jitter = "0s"
  precision = ""
  hostname = ""
  omit_hostname = false
[[outputs.influxdb_v2]]
  urls = ["http://192.168.3.113:8086"]
  token = "c6lwijLnHS-mtlqrTZMCtC_-K-z1ZJf_5qddF5X29CfVyqHZPpAONv7jmketHSt5bYZN3WmbtJZtyMuWqNl7mA=="
  organization = "test"
  bucket = "test"
# Read metrics about cpu usage
[[inputs.cpu]]
  ## Whether to report per-cpu stats or not
  percpu = true
  ## Whether to report total system cpu stats or not
  totalcpu = true
  ## If true, collect raw CPU time metrics
  collect_cpu_time = false
  ## If true, compute and report the sum of all non-idle CPU states
  report_active = false
  ## If true and the info is available then add core_id and physical_id tags
  core_tags = false
# Read metrics about disk usage by mount point
[[inputs.disk]]
  ## Ignore mount points by filesystem type.
  ignore_fs = ["tmpfs", "devtmpfs", "devfs", "iso9660", "overlay", "aufs", "squashfs"]
# Read metrics about disk IO by device
[[inputs.diskio]]
# Read metrics about memory usage
[[inputs.mem]]
  # no configuration
# Get the number of processes and group them by status
[[inputs.processes]]
  # no configuration
# Read metrics about swap memory usage
[[inputs.swap]]
  # no configuration
# Read metrics about system load & uptime
[[inputs.system]]
  # no configuration
# # Read metrics about network interface usage 
[[inputs.net]]
```
---

## 2. `node-exporter` + `prometheus`
![image](https://user-images.githubusercontent.com/98056528/216752754-41495b7b-5b97-4389-9cbf-2792bd5423e9.png)
### Файл конфигурации prometheus.yml:
```
# my global config
global:
  scrape_interval: 36s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).
# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093
# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"
# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
      - targets: ["localhost:9090","localhost:9100"]
```

