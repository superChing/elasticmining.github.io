---
layout: post
title: Using Fluentd to Collect Logs
categories: programming
description: "A simple way to collect many kinds of data by using fluentd."
comments: true
tags: ["fluentd", "data collection"]
image:
  feature:
  credit: 
  creditlink: 
date: 2016-01-09T05:00:00+00:00
author: larrylo
---
# Using Fluentd to Collect Logs

In data analysis areas, collecting data is the most important job overall analysis procedure. After a data engineer collects data from several source like apache logs, application logs, or streaming logs, he maybe wants to transform or filter the natvie logs to the type he needs and then saving into a storage. To achieve this aim, he needs to use many kinds of tools to transform data from all facets types to **analysable** format. It's not an easy job and may take too much time on it when developing a data analysis system. 

**Fluentd** provides a simple high level design to make developers and data analysts unify the data collection and consumption for a better use and understanding of data. The following two figures show the comparison between before and after using Fluentd.

![before-using-fluentd](/img/blog/larry/20160110/fluentd-before.png "Figure 1: Before using fluentd")
Figure 1: http://www.fluentd.org/images/fluentd-before.png "Before using fluentd"

![after-using-fluentd](/img/blog/larry/20160110/fluentd-architecture.png "Figure 2: After using fluentd")
Figure 2: http://docs.fluentd.org/images/fluentd-architecture.png "After using fluentd"

As the previous figures show, you don't need to write lots of codes to aggregate many types data source and direct the data flow to different destinations. Instead, Fluentd uses configurations to set plugins to define the input or output purpose. It helps developers easily build a data collection to manage data flows. Besides, all Fluentd plugins can be developed up to your demand.

In this article, I will reorganize the concept of Fluentd including installation, plugins and a simple example of designing a data collection. If you want to know more details about Fluentd, please visit its formal site, http://www.fluentd.org.

## Installation 
[Treasure Data, Inc](http://www.treasuredata.com/) provides two kinds of distributions for users: 
* Fluentd gem
* td-agent

If you would like to control Fluentd features yourself, it's recommended to use **Fluentd gem**. Otherwise, you can just use the stable version, **td-agent**. This link, [Fluentd Distribution Comparison](http://www.fluentd.org/faqs), described the differences between Fluentd gem and td-agent. 

Before installation, there are still some steps needed to do. You can follow this link, [
Before Installation](http://docs.fluentd.org/articles/before-install), to tune OS settings to prevent unnecessary problems.

I use **CentOS 6.7** and **td-agent 2.3.0** rpm as my experiment environment. At the first step of installation, download and install td-agent follow this command.

```
$ curl -L https://toolbelt.treasuredata.com/sh/install-redhat-td-agent2.sh | sh
```

After that, td-agent will be set as a system daemon and you can control it by service commands.

```
$ service td-agent start
$ service td-agent stop
$ service td-agent restart
$ service td-agent status
```

All installation steps are finished! Is it very easy?

## Plugins
Fluentd define the data source and output destination by using specific plugins. It has 6 types of plugins for different purpose: **Input**, **Output**, **Buffer**, **Filter**, **Parser** and **Formatter**. You can set plugins definition in ```/etc/td-agent/td-agent.conf``` directly or use ```@include``` to load external configurations.  

#### Input
The input plugin define the data source at the beginning of data collection procedure. I use the standard input plugin ```http``` to explain how to make it work.

```
<source>
  @type http
  port 22222
</source>
```

Fluentd define ```<source>``` tag as input plugin. In this case, there are two parameters required.

1. ```@type```: The input plugin you want to use
2. ```port```: The port number listened to http request

#### Output
After setting a input plugin, you still need an output plugin to define the destination of this data flow. There are 3 types of output plugins: **Non-Buffered**, **Buffered** and **Time Sliced**.

1. Non-Buffered: Immediately write out result after capturing events from input plugins. 
2. Buffered: This kind of output plugin consists of a queue with chunks. When the memory size of all events exceeds the buffered limitation, output plugins will insert a blank chunk at the top of queue and flush the chunk at the bottom of queue.
    * Queue: The lengths of chunks. User can define it with ```buffer_queue_limit```.
    * Chunk: The memory size consisting of events. User can define it with ```buffer_chunk_limit```.
3. Time Sliced: A kind of buffered output plugin. User can set ```flush_interval``` to flush chunks periodically.

I use a simple ```file``` plugin as my example. 

```
<match my.app>
  @type file
  path /path/to/file
  flush_interval 10m
</match>
```

Fluentd define ```<match custom.tag.pattern>``` tag as output plugin. The ```custom.tag.pattern``` is an identification help Fluentd to notify which output plugin to use. For example, if user sends a http request with the ```my.app``` as the previous output plugin like 

```
$ curl -X POST -d 'json={"action":"test","user":larrylo}' http://localhost:22222/my.app
```

, the output plugin will store this event log into a chunk and flush it into a file after 10 minutes period arrived. In this case, there are two parameters required.

1. ```@type```: The output plugin you want to use.
2. ```path```: The file path you want to store.

#### Buffer
Buffer plugins are used by buffered output plugin. As the explain in output plugin section, there are two kinds of buffered plugin, chunk-size-based and time-slice-based. I use ```memory``` plugin as the example. 

```
<match my.app>
  buffer_type memory
  buffer_chunk_limit 10m
  flush_at_shutdown true
</match>
```

In this case, there is one parameter required.

1. ```buffer_type```: There are two kinds of type, memory and file.

The ```flush_at_shutdown``` define whether flush all events in chunks or not.

#### Filter
Filter plugins are designed to modify events. There are 3 kinds of use cases of filter plugin: **Grep**, **Delete-Field** and **Add-Field**.  

1. Grep: This kind of filter plugin will capture the events matched the defined ```regexp```.  
2. Delete-Field: Fluentd can delete redundant fields by this kind of field plugin.
3. Add-Field: Fluentd can add additional fields as needed.

```
<filter my.app>
  @type grep
  regexp1 message ^hello.*$
</filter>
```

In this case, there is one parameter required.

1. ```type```: The type of filter plugin.

The ```regexp1``` is a condition to filter out events if the "message" field does not match ```^hello.*$```. For example, the event {"message":"hello, world."} will be kept but the event {"message":"say hello."} will not.

#### Parser
In some special case, the ```format``` parameter cannot parse the custom format of **input plugin**. Fluentd provides developers define their own input events format to fit on demand. There are several built-in parser supplied for users.
1. regexp
2. apache2
3. apache_error
4. nginx
5. syslog
6. tsv or csv

#### Formatter
In some special case, the **output plugin** format cannot meet one's needs. Fluentd supplies developers extend and re-users their output format. There are several built-in formatter supplied for users.
1. out_file
2. json
3. hash
4. ltsv
5. single value
6. tsv or csv 
7. stdout

## Example 
After understanding the basic concept of Fluentd and the function of its plugins, we give an one example to help people know **how** to design a data collection to gather logs by using Fluentd.

A user has many sensor devices produce logs to a MQTT cluster, and he wants to see the data visualization with Kibana to monitor the real-time data status. How to build a consumer role by using Fluend to subscribe the topic of MQTT and save data into Elasticsearch? 


```
<source>
  @type mqtt
  bind 10.1.2.3
  port 1883
  topic Test/#
  tag mqtt.es.app
</source>

<match mqtt.es.app>
  @type copy
  <store>
    # for debug (see /var/log/td-agent/ta-agent.log)
    @type stdout
  </store>
  <store>
    @type elasticsearch
    host localhost
    port 9200
    index_name fluentd
    type_name test
    logstash_format true
    flush_interval 10s 
  </store>
</match>
```

At the beginning, we have to use an input plugin to subscribe the topic data produced from MQTT broker. On this demand, we install the [input_mqtt](https://github.com/yuuna/fluent-plugin-mqtt) plugin to gather data from MQTT and deliver the data to the specific tag named ```mqtt.es.app```. Once the data arrived the Fluentd, we use ```output_copy``` plugin to copy events to two outputs. The first one is the simple ```output_stdout``` plugin which helps developers debug. The second one is a kind of ```output_elasticsearch``` plugin to ```PUT``` a restful request like ```http://localhost:9200/fluentd/test``` every 10 seconds. After that, user can monitor the real-time data status on Kibana. All these steps are arranged into the following figure.

![mqtt-fluentd-elasticsearch](/img/blog/larry/20160110/mqtt-fluentd-elasticsearch.png "Figure 3: The data flow from MQTT to Elasticsearch via Fluentd")
Figure 3: "The data flow from MQTT to Elasticsearch via Fluentd"

## Summary
In this article, we roughly introduce the basic concept of Fluentd and explain the function of its plugins. After that, we give an example to help make a deeper impression. Fluentd has many advantages like simple to use and high performance. However, if you want to gather data and then pipeline to analyze it on distributed system, it's better to chose another kinds of mechanism, like spark streaming, to meet this demand. 

## Reference
[1] http://www.fluentd.org

