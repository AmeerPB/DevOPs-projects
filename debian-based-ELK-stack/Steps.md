
# OpenJDK

apt update && apt install -y openjdk-8-jdk


# NGINX

apt install nginx -y

vim /etc/nginx/sites-available/default


server {
        listen 80;
        server_name 3.145.118.13;

        auth_basic "Restricted Access";
        auth_basic_user_file /etc/nginx/htpasswd.users;

        location / {
                proxy_pass http://localhost:5601;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
                
    }
}


systemctl restart nginx && systemctl status nginx



# Elastic

https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg

sudo apt install -y apt-transport-https

echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list

sudo apt update && sudo apt install -y elasticsearch


vim /etc/elasticsearch/elasticsearch.yml

echo "cluster.name: my-application" | sudo tee -a /etc/elasticsearch/elasticsearch.yml
echo "node.name: node-1" | sudo tee -a /etc/elasticsearch/elasticsearch.yml
echo "http.port: 9200" | sudo tee -a /etc/elasticsearch/elasticsearch.yml
echo "network.host: localhost" | sudo tee -a /etc/elasticsearch/elasticsearch.yml

sudo systemctl start elasticsearch.service && systemctl status elasticsearch.service

bin/elasticsearch-create-enrollment-token --scope kibana


# Kibana

https://www.elastic.co/guide/en/kibana/current/deb.html

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list

sudo apt update && sudo apt install kibana -y


vim /etc/kibana/kibana.yml

echo "server.port: 5601" | tee -a /etc/kibana/kibana.yml
echo "server.host: "localhost"" | tee -a /etc/kibana/kibana.yml


systemctl start kibana.service && systemctl status kibana.service






# Logstash

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/elastic-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list

sudo apt update && sudo apt install logstash -y

edited /etc/apt/sources.list.d/elastic-8.x.list and commented out 





# Adding htaccess password

apt install -y apache2-utils

htpasswd -c /etc/nginx/htpasswd.users admin



###################



# Elastic manual installation -- Issues


wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.14.1-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.14.1-amd64.deb.sha512
shasum -a 512 -c elasticsearch-8.14.1-amd64.deb.sha512 
sudo dpkg -i elasticsearch-8.14.1-amd64.deb

sudo systemctl start elasticsearch.service



root@ip-172-31-26-107:/etc/elasticsearch/jvm.options.d# curl http://localhost:9200
curl: (52) Empty reply from server



edit /etc/elasticsearch/elasticsearch.yml

and replace this setting with false
# Enable security features
xpack.security.enabled: false



bin/elasticsearch-create-enrollment-token -s kibana


# Reference

User --> elastic

https://medium.com/@cybertoolguardian/what-is-elk-and-installing-elk-stack-elasticsearch-logstash-kibana-in-ubuntu-376d60c445b3



# Installation logs


root@ip-172-31-26-107:/home/ubuntu# dpkg -i elasticsearch-8.14.1-amd64.deb
Selecting previously unselected package elasticsearch.
(Reading database ... 81673 files and directories currently installed.)
Preparing to unpack elasticsearch-8.14.1-amd64.deb ...
Creating elasticsearch group... OK
Creating elasticsearch user... OK
Unpacking elasticsearch (8.14.1) ...
Setting up elasticsearch (8.14.1) ...
--------------------------- Security autoconfiguration information ------------------------------

Authentication and authorization are enabled.
TLS for the transport and HTTP layers is enabled and configured.

The generated password for the elastic built-in superuser is : XVnzX5=ppmzzjqghXu+3

If this node should join an existing cluster, you can reconfigure this with
'/usr/share/elasticsearch/bin/elasticsearch-reconfigure-node --enrollment-token <token-here>'
after creating an enrollment token on your existing cluster.

You can complete the following actions at any time:

Reset the password of the elastic built-in superuser with 
'/usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic'.

Generate an enrollment token for Kibana instances with 
 '/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana'.

Generate an enrollment token for Elasticsearch nodes with 
'/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node'.




# Sample commands - OLD / DO NOT run 



/usr/share/elasticsearch/bin/elasticsearch-setup-passwords interactive

/usr/share/elasticsearch/bin/elasticsearch-reset-password -u kibana_system

/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token --scope kibana

elasticsearch.hosts: ["http://localhost:9200"]
elasticsearch.hosts: ["http://localhost:9200"]


/usr/share/elasticsearch/bin/elasticsearch-reset-password -i -u elastic --url https://localhost:9200
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana --url http://localhost:9200


/usr/share/elasticsearch/bin/elasticsearch-reset-password -i -u elastic --url http://localhost:9200





# ----------------------------------------------------------------------------------------------


wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic-keyring.gpg


echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
echo "deb [signed-by=/usr/share/keyrings/elastic-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list


# ----------------------------------------------------------------------------------------------













