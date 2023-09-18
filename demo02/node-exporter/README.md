# Demo 2 - Install Node Exporter as a Service

we need to do some steps to to install `Node_Exporter`.

[Node Exporter github](https://github.com/prometheus/node_exporter)

## 1.1 Installing prometheus as a service steps

> 1. add `Node_Exporter` user with no shell:

```
sudo useradd --no-create-home --shell /bin/false node_exporter
```
> 2. download `Node_Exporter` instalation directory:

```
cd /tmp/

wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
```
> 3. extraxt files:
```
tar -xvf node_exporter-1.3.1.linux-amd64.tar.gz

cd node_exporter-1.3.1.linux-amd64

ls
```
> 4. copy binary files like node_exporter and give it permission:
```
sudo mv node_exporter /usr/local/bin/

sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```
> 5. finally create the service file to be able to restart the `Node_Exporter` service:
```
sudo vi /etc/systemd/system/node_exporter.service
```
- go to this file [Node_Exporter Service File](node_exporter.service)

> 6. finally start and enable `Node_Exporter` service:
```
sudo systemctl daemon-reload

sudo systemctl start node_exporter

sudo systemctl status node_exporter

sudo systemctl enable node_exporter
```
The `Node_Exporter` listens on HTTP port `9100` by default.

go to `/etc/prometheus/prometheus.yml` to add another item in the scrabers section.


# You can enable additional collectors in Node Exporter after following the installation steps you provided:

1. Edit the Node Exporter Service Unit File:
- Use a text editor to open the Node Exporter service unit file you created:
```
sudo vim /etc/systemd/system/node_exporter.service
```

2. Modify the ExecStart Line:
- Inside the service unit file, locate the ExecStart line. It should look something like this:
```
ExecStart=/usr/local/bin/node_exporter
```

To enable additional collectors, you can add the --collector flags to this line. For example, if you want to enable the cpu and memory collectors, modify the line as follows:
```
ExecStart=/usr/local/bin/node_exporter --collector.cpu --collector.memory
```
Add any other collectors you want to enable by including their respective --collector flags.

3. Save the Service Unit File: After making the necessary changes, save the file and exit the text editor.

4. Reload systemd and Restart Node Exporter:
- Reload the systemd configuration to apply the changes:
```
sudo systemctl daemon-reload
```
Then, restart the Node Exporter service to apply the new configuration:
```
sudo systemctl restart node_exporter
```

5. Check Node Exporter Status:
- Verify that Node Exporter is running without errors:
```
sudo systemctl status node_exporter
```
Ensure that there are no errors in the status output.

By modifying the ExecStart line in the systemd service unit file, you can enable additional collectors for Node Exporter. Remember to monitor the metrics mentioned in your original question (scrape_duration_seconds and scrape_samples_post_metric_relabeling) to ensure the collectors are working as expected and not causing any performance issues.
