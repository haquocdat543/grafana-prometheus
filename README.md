# Grafana prometheus
This is a demonstration of using grafana-prometheus

## Prerequisites
* [git](https://git-scm.com/downloads)
* [terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
* [awscli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
* [config-profile](https://docs.aws.amazon.com/cli/latest/reference/configure/)
## Start
### 1. Clone project
```
git clone https://github.com/haquocdat543/grafana-prometheus.git
cd grafana-prometheus
```
### 2. Initialize
```
cd backend
terraform init
terraform apply --auto-approve
```
```
Initializing the backend...
Initializing provider plugins...
- Reusing previous version of hashicorp/aws from the dependency lock file
- Using previously-installed hashicorp/aws v5.30.0
Terraform has been successfully initialized!
You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.
If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
var.key_pair
  Enter a value:
```
Enter your `key pair` name. If you dont have create one. [keypair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html)

Outputs:
```
Monitor-Server = "ssh -i ~/Window2.pem ubuntu@35.73.249.175"
```
### 3. Config grafana-prometheus server
Copy `<your-server-public-ip>:9090/targets` to check health

Copy `<your-server-public-ip>:3000` to access grafana

Login with default `username` and `password` is `admin`

Then update to new password

Navigate to `Home` > `Connection` > `Prometheus` > `Add new data soures` 

Prometheus server url : <your-server-url> in my case it is `localhost:9090` > `Save and Test`

Navigate to `Home` > `Dashboard` > `Plug sign ( top right corner )` > `Imoort dashboard`

Find and import... : 1860 > `Load` > Select `Prometheus` > `Import`

### 4. Monitor k8s nodes ( Read further )
ssh to that nodes and execute these commands :
```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo mv \
  node_exporter-1.6.1.linux-amd64/node_exporter \
  /usr/local/bin/
rm -rf node_exporter*

cat << EOF | sudo tee -a /etc/systemd/system/node_exporter.service
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter \
    --collector.logind

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable node_exporter
    
sudo systemctl start node_exporter
```
ssh to prometheus server and execute these commands :

```
cat << EOF | sudo tee -a /etc/prometheus/prometheus.yml
  - job_name: node_export_masterk8s
    static_configs:
      - targets: ["<master-ip>:9100"]

  - job_name: node_export_workerk8s
    static_configs:
      - targets: ["<worker-ip>:9100"]

EOF
promtool check config /etc/prometheus/prometheus.yml
curl -X POST http://localhost:9090/-/reload
```
Navigate to `http://<your-prometheus-server-public-ip>:9090/targets` to check nodes health

### 4. Destroy
```
terraform destroy --auto-approve
```

