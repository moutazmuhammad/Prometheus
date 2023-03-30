# Demo 5 - Configure Prometheus Rule

* we need to do some steps to configure prometheus Rules

### 1- Go to `/etc/prometheus/prometheus.yml` to enable `Rules`

```
sudo vi /etc/prometheus/prometheus.yml
```

### 2- Create [rule file](rules.yml) under `/etc/prometheus/` directory:

```
sudo vi /etc/prometheus/rules.yml
```

### 3- restart prometheus service:

```
sudo systemctl restart prometheus
```