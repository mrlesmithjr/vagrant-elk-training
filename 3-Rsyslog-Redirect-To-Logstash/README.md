3 - [Rsyslog] Redirect To [Logstash]
================================

Explanation
-----------
In this section we will go over how to actually configure [Rsyslog] to listen
on `514 TCP/UDP` and redirect to our [Logstash] instance listening on `10514 TCP/UDP`.
You may be wondering why we are doing this right? Well if you remember in section
2 we mentioned that in order for [Logstash] to use ports < 1024 would require
[Logstash] to run as root and we do not want that. So this is where we will use
[Rsyslog] to actually collect our events and then redirect them to [Logstash] for
processing. This is a useful trick when devices do not have the ability to define
a non standard port of `514` especially.

Assumption(s)
-------------
* The [Vagrant] VM is still up and running from the [1st section](../1-Installing-Logstash/README.md).

How-to
------
In order to configure [Rsyslog] to receive messages from remote devices we must
reconfigure the default `/etc/rsyslog.conf`.

Edit `/etc/rsyslog.conf`:
```
vi /etc/rsyslog.conf
```
Find the following lines in the configuration:
```
# provides UDP syslog reception
#module(load="imudp")
#input(type="imudp" port="514")

# provides TCP syslog reception
#module(load="imtcp")
#input(type="imtcp" port="514")
```
Change the above configuration to look like below:
```
# provides UDP syslog reception
module(load="imudp")
input(type="imudp" port="514")

# provides TCP syslog reception
module(load="imtcp")
input(type="imtcp" port="514")
```
Now save and exit the editor.

Now we need to configure our [Rsyslog] redirect. And we do that by editing
`/etc/rsyslog.d/50-default.conf`
```
vi /etc/rsyslog.d/50-default.conf
```
Scroll down to just below the following:
```
#  Default rules for rsyslog.
#
#                       For more information see rsyslog.conf(5) and /etc/rsyslog.conf
```
And add the following line and then save and exit:
```
*.* @127.0.0.1:10514
```
This is all that is required in order to do our redirect. Now a couple of things
around this. One thing is that the above will do a redirect to `10514 UDP` due to
the `@127.0.0.1` being defined. But if we wanted to use `10514 TCP` instead we
would change that line to look like the following:
```
*.* @@127.0.0.1:10514
```
If you notice we added and additional `@` before our loopback address. This tells
[Rsyslog] to use `TCP` instead of `UDP`. The other thing to note is the placement
of this redirect. I have seen many times where certain events never arrive to the
[Logstash] processing when the redirect is placed near the bottom of the configuration
file therefore if we place it at the top of the configuration it will always be
redirected.

Now we are ready to restart our [Rsyslog] service in order to apply our new
configurations.
```
sudo service rsyslog restart
```

And now at this point we could start configuring remote devices to begin sending
their events to our [Logstash] server and [Rsyslog] will begin redirecting to
[Logstash] for processing. But again, we are only configured for `stdout` as the
[Logstash] output. So it won't be very useful at this point.

[Home](../README.md)

[Logstash]: <https://www.elastic.co/products/logstash>
[Rsyslog]: <https://en.wikipedia.org/wiki/Rsyslog>
[Vagrant]: <https://www.vagrantup.com/>
