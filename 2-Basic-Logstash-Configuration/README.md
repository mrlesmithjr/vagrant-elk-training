2 - Basic Logstash Configuration
================================

In this section we will be doing a basic configuration of [Logstash]. We will
not be doing anything advanced in this section so it will just be a simple
configuration to start laying the foundation of more advanced configurations.

Assumption(s)
-------------
* The [Vagrant] VM is still up and running from the [1st section](../1-Installing-Logstash/README.md).

Basic configuration:
--------------------
We are now ready to do a basic [Logstash] configuration with the following
configuration:
* Configure inputs for Syslog listeners on `10514 TCP/UDP`
  We are configuring these to listen on this port due to the fact that in order
  to open the standard port(s) of `514 TCP/UDP` it would require [Logstash] to
  run as root. This is not something that we want to do.
* Configure a type of `syslog` to identify which input our events are arriving on.
* Configure a filter to create a tag of `syslog` based on our type being `syslog`
* Configure an output of `stdout`

With the above being defined we can now create our `/etc/logstash/conf.d/logstash.conf`
with the following code:
```
input {
  tcp {
    type => "syslog"
    port => "10514"
  }
}
input {
  udp {
    type => "syslog"
    port => "10514"
  }
}
filter {
  if [type] == "syslog" {
    mutate {
      add_tag => [ "syslog" ]
    }
  }
}
output {
  stdout {}
}
```
With the above defined configuration you can now ssh to your [Vagrant] VM and
copy/paste the above code into `/etc/logstash/conf.d/logstash.conf`
```
vagrant ssh
vi /etc/logstash/conf.d/logstash.conf
```
Now copy/paste the code from above and save the file. Now you can restart the
[Logstash] service and verify that ports `10514 TCP/UDP` are now listening for
incoming events.

Restart [Logstash]:
```
sudo service logstash restart
```

Validate [Logstash] is listening on our defined ports:
```
sudo ss -ltnp
...
State      Recv-Q Send-Q                                   Local Address:Port                                                  Peer Address:Port
LISTEN     0      128                                                  *:22                                                               *:*
users:(("sshd",pid=778,fd=3))
LISTEN     0      50                                                  :::10514                                                           :::*
users:(("java",pid=6192,fd=24))
LISTEN     0      128                                                 :::22                                                              :::*
users:(("sshd",pid=778,fd=4))
```
```
sudo ss -lunp
...
State       Recv-Q Send-Q                                                         Local Address:Port                                                                        Peer Address:Port
UNCONN      0      0                                                                          *:68                                                                                     *:*
users:(("dhclient",pid=725,fd=7))
UNCONN      0      0                                                                  10.0.2.15:123                                                                                    *:*
users:(("ntpd",pid=800,fd=19))
UNCONN      0      0                                                                  127.0.0.1:123                                                                                    *:*
users:(("ntpd",pid=800,fd=18))
UNCONN      0      0                                                                          *:123                                                                                    *:*
users:(("ntpd",pid=800,fd=17))
UNCONN      0      0                                                                         :::10514                                                                                 :::*
users:(("java",pid=6192,fd=28))
UNCONN      0      0                                            fe80::a00:27ff:fe51:d07c%enp0s3:123                                                                                   :::*
users:(("ntpd",pid=800,fd=24))
UNCONN      0      0                                                                        ::1:123                                                                                   :::*
users:(("ntpd",pid=800,fd=20))
UNCONN      0      0                                                                         :::123                                                                                   :::*
users:(("ntpd",pid=800,fd=16))
```
And as you can see from above we are indeed listening on `10514 TCP/UDP`. Now to
validate that we are indeed receiving events on our `stdout` we can do the following:

Stop [Logstash] service:
```
sudo service logstash stop
```
Now start [Logstash] via command line:
```
sudo /opt/logstash/bin/logstash -f /etc/logstash/conf.d/logstash.conf &
...
Settings: Default pipeline workers: 1
Pipeline main started
```
Hit `enter`:

Now send a test message to [Logstash] and should show up on your console:
```
logger "test message" -n 127.0.0.1 -P 10514
...
2016-10-15T03:17:41.285Z 127.0.0.1 <13>1 2016-10-15T03:17:41.284356+00:00 node0 vagrant - - [timeQuality tzKnown="1" isSynced="1" syncAccuracy="460769"] test message
```

As you can see we successfully received an event.

Now you can kill the [Logstash] process we started and restart [Logstash] using
the service command.

Kill the PID of the running [Logstash] process:
```
PID=$(ps -ef | grep logstash/runner.rb | awk '{print $2}') && echo $PID | awk '{print $1}' && sudo kill -9 $PID
...
7234
```

Now you can start [Logstash] using the service command:
```
sudo service logstash start
```

At this point you could begin setting up remote devices to send to your [Logstash]
instance on `10514 TCP/UDP` but you wont get a whole lot at this point.

[Home](../README.md)

[Logstash]: <https://www.elastic.co/products/logstash>
[Vagrant]: <https://www.vagrantup.com/>
