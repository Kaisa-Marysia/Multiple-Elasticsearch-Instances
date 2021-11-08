# Multiple-Elasticsearch-Instances
How to run multiple elasticsearch instances on different, managed by systemd.
Tested on Ubuntu 20.04 LTS.

## prepare elasticsearch
Create a backup or move  the default elasticsearch environment file<br>
```$ mv /etc/default/elasticsearch /etc/default/elasticsearch.orig```<br>
and create a empty file or use the empty file of this repository.<br>
```$ > /etc/default/elasticsearch```<br>
This must be done to avoid that elasticsearch want to use the default environments. This is hardcoded in `/usr/share/elasticsearch/bin/elasticsearch-env` on line 81.<br>
```
[...]
80                                  
81 source /etc/default/elasticsearch
82                                                            
[...]
```

Change the owner from root to elasticsearch of /etc/elasticsearch<br>
```$ chown -R elasticsearch: /etc/elasticsearch```

## systemd service file
Download the [systemd service](https://github.com/Kaisa-Marysia/Multiple-Elasticsearch-Instances/blob/main/etc/systemd/system/elasticsearch-instance%40.service) file as `/etc/systemd/system/elasticsearch-instance@.service`

## setup elasticsearch instance
For any instance you want to use, create as elasticsearch user a dir named as the port number you want to use.<br>
```$ sudo -u elasticsearch mkdir /etc/elasticsearch/9200```<br>
and copy the [configurations](https://github.com/Kaisa-Marysia/Multiple-Elasticsearch-Instances/tree/main/etc/elasticsearch/92XX) of this repository. Edit the [elasticsearch.yml](https://github.com/Kaisa-Marysia/Multiple-Elasticsearch-Instances/blob/main/etc/elasticsearch/92XX/elasticsearch.yml) and change `92XX` into the port number you want to use. 

```
bootstrap.system_call_filter: false

path.data: /var/lib/elasticsearch/9200
path.logs: /var/log/elasticsearch/9200

network.host: 127.0.0.1

http.port: 9200

cluster.routing.allocation.disk.threshold_enabled: false
```

Drop the other [jvm.options and log4j2.properties](https://github.com/Kaisa-Marysia/Multiple-Elasticsearch-Instances/tree/main/etc/elasticsearch/92XX) into `/etc/elasticsearch/9200` as the elasticsearch user<br>

## Run 
After you prepare all you just need to reload the systemd daemon and start the instance:<br>
```
$ systemctl daemon-reload
$ systemctl start elasticsearch-instance@9200
```
Check if elasticsearch is running on this port with curl:<br>
```
$ curl 127.0.0.1:9200
```
If all works as expected, you can enable the service<br>
```
$ systemctl enable elasticsearch-instance@9200
```

## Add more instances

To create a new instance just make a new dir `/etc/elasticsearch/9201` and copy the config files from this repository or of a other instance. Edit the `elasticsearch.yml` and replace the portnumber with 9201 (or that one u want to use) and start the instance `$ systemctl start elasticsearch-instance@9201`.
