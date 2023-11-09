## 1- Go to /etc/prometheus/prometheus.yml to enable Rules
```
sudo vi /etc/prometheus/prometheus.yml
```
## 2- Create rule file under /etc/prometheus/ directory:
```
sudo vi /etc/prometheus/rules.yml
```
## 3- restart prometheus service:
```
sudo systemctl restart prometheus
```


```yaml
groups:
  - name: OdooServiceDown
    rules:
      - alert: SystemdUnitStateAlert
        expr: node_systemd_unit_state{instance="3.87.188.129:9100", job="odoo-node", name="project-one-dev.service", state="active"} == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Systemd unit project-one-dev.service is not active"
          description: "The systemd unit project-one-dev.service is not in the active state for 1 minute."
  - name: odoo.rules
    rules:
      - alert: OdooNodeDown
        expr: up{job="odoo-node"} == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Odoo node is down"
          description: "Odoo node has been down for more than 1 minutes."
```
