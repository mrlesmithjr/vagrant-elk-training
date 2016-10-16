5 - Configuring Logstash Elasticsearch Output
=============================================

In this section we will be quickly configuring our [Logstash] configuration to
send it's output to [Elasticsearch] instead of just `stdout`. As this is ultimately
where you will want your output to be sent.

Assumption(s)
-------------
* The [Vagrant] VM is still up and running from the [1st section](../1-Installing-Logstash/README.md).

How-To
------
We will need to edit our `/etc/logstash/conf.d/logstash.conf` file and remove
the following:
```
output {
  stdout {}
}
```
And then add the following:
```
output {
  elasticsearch {
    hosts => ["localhost:9200"]
  }
}
```
Now save and exit your editor and restart [Logstash]:
```
sudo service logstash restart
```

And there you have it. A quick and simple configuration change to have [Logstash]
now send it's output to [Elasticsearch].

[Home](../README.md)

[Elasticsearch]: <https://www.elastic.co/products/elasticsearch>
[Logstash]: <https://www.elastic.co/products/logstash>
[Vagrant]: <https://www.vagrantup.com/>
