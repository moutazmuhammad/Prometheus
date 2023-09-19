```yaml
global:

templates:
  - '/etc/alertmanager/*.tmpl'

receivers:
  - name: alert_channel
    webhook_configs:
      - url: 'http://localhost:2000/alert_channel'
        send_resolved: false

route:
  group_by: ['alertname']
  group_interval: 15s
  group_wait: 10s
  receiver: alert_channel
  # repeat_interval: 3h
```