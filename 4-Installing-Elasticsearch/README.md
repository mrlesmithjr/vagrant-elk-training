4 - Installing [Elasticsearch]
============================

In this section we will be installing [Elasticsearch] to use for our output
from [Logstash] (To be setup in the next section).

Assumption(s)
-------------
* The [Vagrant] VM is still up and running from the [1st section](1-Installing-Logstash/README.md).

Pre-Reqs
--------
Because we are installing [Elasticsearch] on the same node as we installed
[Logstash] our pre-reqs should already be met. However for the list of pre-reqs
to install see below:

```
sudo apt-get update && sudo apt-get install -y default-jre-headless
```

* Add the [Elasticsearch] Apt-Key
```
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

How-To
------
Add the [Elasticsearch] Apt-Repository
```
echo "deb https://packages.elastic.co/elasticsearch/2.x/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-2.x.list
```
Update the Apt-Cache and install [Elasticsearch]
```
sudo apt-get update && sudo apt-get install elasticsearch
```

One final note is to ensure that [Elasticsearch] is enabled and starts on boot.

`Ubuntu 14.04`
```
sudo update-rc.d elasticsearch defaults 96 9
```
`Ubuntu 16.04`
```
sudo systemctl enable elasticsearch
```
If you are using this [Vagrant] VM to learn with then you will use the
`Ubuntu 16.04` method above.

Now you are ready to start the [Elasticsearch] service:
```
sudo service elasticsearch start
```

And now verify that [Elasticsearch] is running:
```
curl http://localhost:9200
...
{
  "name" : "Steel Serpent",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "D70gaoc2SjmSIbPGzkPBAQ",
  "version" : {
    "number" : "2.4.1",
    "build_hash" : "c67dc32e24162035d18d6fe1e952c4cbcbe79d16",
    "build_timestamp" : "2016-09-27T18:57:55Z",
    "build_snapshot" : false,
    "lucene_version" : "5.5.2"
  },
  "tagline" : "You Know, for Search"
}
```

[Home](../README.md)

[Elasticsearch]: <https://www.elastic.co/products/elasticsearch>
[Logstash]: <https://www.elastic.co/products/logstash>
