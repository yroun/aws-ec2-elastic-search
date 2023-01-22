## Elastic Search in Amazon Linux 2 (CentOS)

### Install OpenJDK

```
yum update
yum list java*jdk-devel
yum install java-1.8.0-openjdk-devel.x86_64
```

### Elastic Search ([Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html))

- Set RPM Repository

/etc/yum.repos.d/elasticsearch.repo

```
[elasticsearch]
name=Elasticsearch repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md
```

- Install ElasticSearch

```
sudo yum install --enablerepo=elasticsearch elasticsearch
```

### Change owner

```
chown -R elasticsearch:elasticsearch /usr/share/elasticsearch
chown -R elasticsearch:elasticsearch /var/lib/elasticsearch
chown -R elasticsearch:elasticsearch /var/log/elasticsearch
chown -R elasticsearch:elasticsearch /etc/elasticsearch
```

### Set up bootstrap password

printf "password" | sudo /usr/share/elasticsearch/bin/elasticsearch-keystore add "bootstrap.password" -x

### Edit /etc/elasticsearch/elasticsearch.yml

```
sudo su elasticsearch -s /bin/bash
vim /etc/elasticsearch/elasticsearch.yml
```

### Running Elastic Search with systemd

```
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
```

### Check that Elastic Search is running

```
curl --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic https://localhost:9200
```

### (Optional) Install EC2 discovery Plugin

(IAM of DescribeInstances policy needed)

```
/usr/share/elasticsearch/bin/elasticsearch-plugin install --batch discovery-ec2
```

elasticsearch/yml

```
...
network.bind_host: 0.0.0.0
network.publish_host: _ec2_
...
discovery.seed_providers: ec2
discovery.ec2.groups: es
discovery.ec2.any_group: true
...
```


## Kibana

/etc/yum.repos.d/kibana.repo

```
[kibana]
name=Kibana repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

### Install Kibana

```
yum install -y kibana
```

### Change ownership of Kibana

```
chown -R kibana:kibana /usr/share/kibana
chown -R kibana:kibana /var/lib/kibana
chown -R kibana:kibana /var/log/kibana
chown -R kibana:kibana /etc/kibana
```

### Edit /etc/kibana/kibana.yml

```
sudo su kibana -s /bin/bash
vim /etc/kibana/kibana.yml
```

### Elastic Search changes the password for Kibana

```
curl -X POST -H 'Content-Type: application/json' http://localhost:9200/_security/user/kibana_system/_password -d '{"password": ""}' -u "elastic":""
```
