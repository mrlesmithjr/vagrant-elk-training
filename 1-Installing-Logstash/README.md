1 - Installing Logstash
=======================

In this section we will be doing our initial [Logstash] installation from
scratch on an [Ubuntu] 16.04 server. To make this easy we will be spinning up
a [Vagrant] VM to do this manually.

* Spin up the [Vagrant] VM
```
vagrant up
```
* SSH to VM
```
vagrant ssh
```

Now we will install the pre-reqs for [Logstash]:

```
sudo apt-get update && sudo apt-get install -y default-jre-headless
```

With that out of the way we are now ready to install [Logstash]:

* Add the [Elasticsearch] Apt-Key
```
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
* Add [Logstash] Apt-Repository
```
echo "deb https://packages.elastic.co/logstash/2.4/debian stable main" | sudo tee -a /etc/apt/sources.list
```
* Install [Logstash]
```
sudo apt-get update && sudo apt-get install -y logstash
```

Once that completes successfully you have now completed the [Logstash]
installation.

One final not is to ensure that [Logstash] is enabled and starts on boot.

`Ubuntu 14.04`
```
sudo update-rc.d logstash defaults 96 9
```
`Ubuntu 16.04`
```
sudo systemctl enable logstash
```
If you are using this [Vagrant] VM to learn with then you will use the
`Ubuntu 16.04` method above.

[Home](../README.md)

[Elasticsearch]: <https://www.elastic.co/products/elasticsearch>
[Logstash]: <https://www.elastic.co/products/logstash>
[Ubuntu]: <https://www.ubuntu.com/>
[Vagrant]: <https://www.vagrantup.com/>
