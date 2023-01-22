## Amazon Linux 2 (CentOS)

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

### Running Elastic Search with systemd

```
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
```

### Check that Elastic Search is running

```
curl --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic https://localhost:9200
```
