 
Steps to configure ELK stack on a freash CentOS 6.5 64-bit host

# Download and install the Fedora EPEL Repo to gain access to necessary packages
```sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm```

# Recommend disabling IPV6 for testing and lab purposes:
```sudo vi /etc/sysctl```
# Add the following keys:
```sysctl -w net.ipv6.conf.all.disable_ipv6=1
sysctl -w net.ipv6.conf.default.disable_ipv6=1```


# Activate the sysctl IPV6 changes:
```sysctl -p```


# Configure 
# Allow access to Kibana GUI
```iptables -A INPUT -p tcp --dport 80 -j ACCEPT```
# Optional if you choose to access Kibana via SSL
```iptables -A INPUT -p tcp --dport 443 -j ACCEPT```
# Allow client-side access to Elasticsearch
```iptables -A INPUT -p tcp --dport 9200 -j ACCEPT```
# Allow client-side access to Elasticsearch API
```iptables -A INPUT -p tcp --dport 9300 -j ACCEPT```


# OPTIONAL WITH A WARNING: If you don't use iptables or want to temporarily disable it:
```sudo /sbin/service iptables stop```
```sudo /sbinchkconfig iptables off```


# Set SELinux to 'permissive' to allow correct operation of ELK stack; 
# Collect data from SEL logs and tune, then return to 'enforcing'
```sudo vi etc/selinux/config```
# Set mode to 'permissive'


# Install GPG key for the Elasticsearch repo
```sudo rpm --import http://packages.elasticsearch.org/GPG-KEY-elasticsearch```


# Create the Elasticsearch repo:
```sudo vi /etc/yum.repos.d/elasticsearch.repo```

# Add the following and save the file:
```
[elasticsearch-1.2]
name=Elasticsearch repository for 1.2.x packages
baseurl=http://packages.elasticsearch.org/elasticsearch/1.2/centos
gpgcheck=1
gpgkey=http://packages.elasticsearch.org/GPG-KEY-elasticsearch
enabled=1
```


# Install Elasticsearch:
```sudo yum install elasticsearch -y```


# Enable the Elasticsearch service 
```sudo chkconfig --add elasticsearch```


# Force Elasticsearch to start automatically:
```sudo chkconfig elasticsearch on```


# Install the Logstash repo for yum to use:
```sudo vi /etc/yum.repos.d/logstash.repo```


# Add the following and save the file:
```[logstash-1.4]
name=logstash repository for 1.4.x packages
baseurl=http://packages.elasticsearch.org/logstash/1.4/centos
gpgcheck=1
gpgkey=http://packages.elasticsearch.org/GPG-KEY-elasticsearch
enabled=1```


# Install Logstash
```sudo yum install logstash -y```


# Install the Logstash plugins:
# WARNING: There may still be a bug with some of the plugin install scripts, if so
# install them manually to /opt/logstash, link here: 
```cd /opt/logstash```
```sudo bin/plugin install contrib```


# Enable Logstash to start automatically:
```sudo /sbin/chkconfig logstash on```


# Install a web server for brevity Nginx can be installed.
# Apache and Nginx are interchangeable here
```sudo yum install nginx -y```


# Force nginx to start automatically:
``sudo /sbin/chkconfig nginx on```

# Start Nginx:
```sudo service nginx start```


# Download and install Kibana
```wget https://download.elasticsearch.org/kibana/kibana/kibana-3.1.0.tar.gz
tar zxf kibana-3.1.0.tar.gz
sudo mv kibana-3.1.0 /usr/share/nginx/html/kibana
rm -f kibana-3.1.0.tar.gz```


# Grab the Elasticsearch example config from the ELK Demo repo, change IP addresses to match your host config
# Copy your personalized configuration to: /etc/elasticsearch/elasticsearch.yml


# Start Elasticsearch
```sudo service elasticsearch start```


# Grab the Logstash example config from the ELK Demo repo
# Copy your personalized configuration to: /etc/logstash/conf.d/logstash.conf


# Update the builtin grok patterns grab the firewall grok patterns from the ELK Demo repo
# Copy your personalized configuration to: /usr/share/grok/patterns/cisco-firewalls


# Start Elasticsearch
```sudo service logstash start```


# Configure your firewall(s) to send syslog data to your new ES host
# Be sure to match destination ports and protocols with those set within the Logstash config

# Now browse to http://<ES_HOST_IP>:9200/kibana
