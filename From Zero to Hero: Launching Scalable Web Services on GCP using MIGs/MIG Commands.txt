gcloud auth login 
gcloud auth application-default login
gcloud config set project nishanta-banik
gcloud config set compute/zone europe-central2-b
gcloud compute instances create myinstance
gcloud compute firewall-rules create allow-80 --allow tcp:80
gcloud compute ssh myinstance

Log into myinstance:
# sudo su -  
apt-get update 
apt-get install -y nginx 
service nginx start 
apt-get install -y wget 

wget -q -O - localhost:80 | grep nginx

gcloud compute instances list


$ vi startup.sh 

#! /bin/bash 
apt-get update 
apt-get install -y nginx 
service nginx start 
sed -i -- 's/nginx/Google Cloud Platform - '"$HOSTNAME"'/' /var/www/html/index.nginx-debian.html --wq 


gcloud compute instances create nginx --metadata-from-file startup-script=startup.sh

gcloud compute instance-templates create nginx-template  --metadata-from-file startup-script=startup.sh 

gcloud compute target-pools create nginx-pool

gcloud compute instance-groups managed create nginx-group --base-instance-name nginx --template nginx-template --size 2 --target-pool nginx-pool 
gcloud compute instance-groups managed resize nginx-group --zone=europe-central2-b --size 1 

gcloud compute instance-groups managed set-autoscaling nginx-group --zone=europe-central2-b --min-num-replicas=3 --max-num-replicas=7 

gcloud compute instances list

gcloud compute forwarding-rules create nginx-lb --ports 80 --target-pool nginx-pool 

gcloud compute forwarding-rules list


gcloud compute forwarding-rules delete nginx-lb --region europe-central2 --quiet 
gcloud compute instance-groups managed delete nginx-group --quiet 
gcloud compute target-pools delete nginx-pool --region europe-central2 --quiet 
gcloud compute instance-templates delete nginx-template --quiet 
gcloud compute instances delete nginx --quiet 
gcloud compute instances delete myinstance --quiet 
gcloud compute firewall-rules delete allow-80 --quiet 





| Command | Description |
|---------|-------------|
| `gcloud auth login` | Authenticates the current user via the web-based Google account login. |
| `gcloud auth application-default login` | Provides authentication credentials to your application running on Google Cloud. |
| `gcloud config set project nishanta-banik` | Sets the Google Cloud project to `nishanta-banik`, ensuring subsequent commands affect only this project. |
| `gcloud config set compute/zone europe-central2-b` | Sets the compute zone to `europe-central2-b` for deploying resources in a specific geographical location. |
| `gcloud compute instances create myinstance` | Creates a new compute instance named `myinstance`, which serves as a virtual server. |
| `gcloud compute firewall-rules create allow-80 --allow tcp:80` | Creates a firewall rule named `allow-80` to allow incoming traffic on TCP port 80, necessary for HTTP web traffic. |
| `gcloud compute ssh myinstance` | Initiates an SSH connection to `myinstance`, allowing command-line access to the server. |
| **Log into myinstance:** |
| `# sudo su -` | Switches to the root user for administrative tasks. |
| `apt-get update` | Updates the package lists for upgrades or new package installations. |
| `apt-get install -y nginx` | Installs Nginx, a high-performance web server. |
| `service nginx start` | Starts the Nginx service, making the web server active. |
| `apt-get install -y wget` | Installs `wget`, a utility for non-interactive download of files from the web. |
| `wget -q -O - localhost:80 \| grep nginx` | Verifies Nginx is running by making a request to localhost on port 80 and checking for the Nginx signature. |
| `gcloud compute instances list` | Lists all compute instances in the current project, useful for verification and management. |
| **Create startup script:** |
| `$ vi startup.sh` | Opens a new or existing file named `startup.sh` in the vi editor for editing. |
| **Contents of `startup.sh`:** |
| `#! /bin/bash` | Specifies the script is to be run with bash, the Bourne-Again SHell. |
| **Script commands:** | Updates package lists, installs Nginx, starts Nginx, and modifies the default Nginx landing page to include the instance hostname. |
| `gcloud compute instances create nginx --metadata-from-file startup-script=startup.sh` | Creates a compute instance with Nginx installed and configured using the `startup.sh` script. |
| `gcloud compute instance-templates create nginx-template --metadata-from-file startup-script=startup.sh` | Creates an instance template for launching scalable instances with Nginx pre-configured. |
| `gcloud compute target-pools create nginx-pool` | Creates a target pool named `nginx-pool` for grouping instances that receive incoming traffic. |
| `gcloud compute instance-groups managed create nginx-group --base-instance-name nginx --template nginx-template --size 2 --target-pool nginx-pool` | Creates a managed instance group with 2 instances based on `nginx-template`, allowing for easy scaling and management. |
| `gcloud compute instance-groups managed resize nginx-group --zone=europe-central2-b --size 1` | Resizes the instance group to 1, demonstrating scalability. |
| `gcloud compute instance-groups managed set-autoscaling nginx-group --zone=europe-central2-b --min-num-replicas=3 --max-num-replicas=7` | Enables autoscaling for the instance group, with a minimum of 3 and a maximum of 7 instances, ensuring scalability based on load. |
| `gcloud compute instances list` | Lists instances again to verify autoscaling and management changes. |
| `gcloud compute forwarding-rules create nginx-lb --ports 80 --target-pool nginx-pool` | Creates a forwarding rule for the load balancer to direct traffic on port 80 to instances in `nginx-pool`. |
| `gcloud compute forwarding-rules list` | Lists all forwarding rules, helpful for verifying load balancer setup. |
| **Clean up resources:** |
| `gcloud compute forwarding-rules delete nginx-lb --region europe-central2 --quiet` | Deletes the forwarding rule for the load balancer, preventing further traffic routing. |
| `gcloud compute instance-groups managed delete nginx-group --quiet` | Deletes the managed instance group, removing all associated instances. |
| `gcloud compute target-pools delete nginx-pool --region europe-central2 --quiet` | Deletes the target pool, removing the grouping for load-balanced instances. |
| `gcloud compute instance-templates delete nginx-template --quiet` | Deletes the instance template, removing the blueprint for creating new instances. |
| `gcloud compute instances delete nginx --quiet` | Deletes the `nginx` instance created separately from the instance group. |
| `gcloud compute instances delete myinstance --quiet` | Deletes the initially created `myinstance`. |
| `gcloud compute firewall-rules delete allow-80 --quiet` | Deletes the firewall rule allowing traffic on port 80, securing the network. |
