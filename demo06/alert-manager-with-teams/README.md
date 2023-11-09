# Prometheus-Alertmanager integration with MS-teams

There are a couple of tools, which we can use as a proxy, but I preferred to use prometheus-msteams, for a couple of reasons.

- Well-structured documentation.
- Easy to configure.
- We have more control in hand, can customize alert notifications, and can also configure to send notifications to multiple channels on MS teams. Besides well-described documentation.
I still faced some challenges and took half of the day of mine.


1. Download the binary:

```sh
cd /tmp/

sudo curl -Lo prometheus-msteams  https://github.com/bzon/prometheus-msteams/releases/download/v1.4.1/prometheus-msteams-linux-amd64 && chmod +x prometheus-msteams && sudo mv prometheus-msteams /usr/local/bin/
```
```sh
sudo mkdir -p /opt/promethues-msteams/
```

2. To have more options in the future you can use config.yml to provide webhook. So that you can give multiple webhooks to send alerts to multiple channels in MS-teams in future if you need it.

```sh
sudo vim /opt/promethues-msteams/config.yml
```

Add webhooks as shown below. if you want to add another webhook, you can add right after first webhook.
```yml
connectors: 
- alert_channel: "WEBHOOK URL"
```

3. The next step is to add a template for custom notification.

```sh
sudo vim /opt/promethues-msteams/card.tmpl
```
Copy the following content in your file, or you can modify the following template as per your requirements. This template can be customized and uses the Go [Templating Engine](https://golang.org/pkg/text/template/).

```go

{{ define "teams.card" }}
{
"@type": "MessageCard",
"@context": "http://schema.org/extensions",
"themeColor": "{{- if eq .Status "resolved" -}}2DC72D
{{- else if eq .Status "firing" -}}
{{- if eq .CommonLabels.severity "critical" -}}8C1A1A
{{- else if eq .CommonLabels.severity "warning" -}}FFA500
{{- else -}}808080{{- end -}}
{{- else -}}808080{{- end -}}",
"summary": "Prometheus Alerts",
"title": "Prometheus Alert ({{ .Status }})",
"sections": [ {{$externalUrl := .ExternalURL}}
{{- range $index, $alert := .Alerts }}{{- if $index }},{{- end }}
{
"facts": [ {{- range $key, $value := $alert.Annotations }}
{
"name": "{{ reReplaceAll "_" "\\\\_" $key }}",
"value": "{{ reReplaceAll "_" "\\\\_" $value }}"
},
{{- end -}}
{{$c := counter}}{{ range $key, $value := $alert.Labels }}{{if call $c}},{{ end }}
{
"name": "{{ reReplaceAll "_" "\\\\_" $key }}",
"value": "{{ reReplaceAll "_" "\\\\_" $value }}"
}
{{- end }}
],
"markdown": true
}
{{- end }}
]
}
{{ end }}

```

4. Create prometheus-msteams user, and use --no-create-home and --shell /bin/false to restrict this user log into the server.

```sh
sudo useradd --no-create-home --shell /bin/false prometheus-msteams
```

5. Change Owner of files and directorys:
```sh
sudo chown prometheus-msteams:prometheus-msteams  /opt/promethues-msteams/
sudo chown prometheus-msteams:prometheus-msteams  /opt/promethues-msteams/*
sudo chown prometheus-msteams:prometheus-msteams /usr/local/bin/prometheus-msteams 
```


6. Create a service file to run prometheus-msteams as service with the following command.

```sh
sudo vim /etc/systemd/system/prometheus-msteams.service
```
The service file tells systemd to run prometheus-msteams as the prometheus-msteams user, with the configuration file located /opt/promethues-msteams/config.yml, and template file located in the same directory.

Copy the following content into prometheus-msteams.service file.
```yaml
[Unit] 
Description=Prometheus-msteams 
Wants=network-online.target 
After=network-online.target 
[Service] 
User=prometheus-msteams 
Group=prometheus-msteams 
Type=simple 
ExecStart=/usr/local/bin/prometheus-msteams -config-file /opt/prometheus-msteams/config.yml -template-file /opt/prometheus-msteams/card.tmpl 
[Install] 
WantedBy=multi-user.target
```

promethues-msteams listen on localhost on `2000 port`, and you have to provide configuration file and template also.


7. Start Service
```sh
sudo systemctl daemon-reload
sudo systemctl start prometheus-msteams.service
sudo systemctl status prometheus-msteams
sudo systemctl enable prometheus-msteams
```
