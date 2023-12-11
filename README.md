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

### 4. Destroy
```
terraform destroy --auto-approve
```

