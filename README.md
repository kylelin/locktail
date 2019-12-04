Locktail
=========

Tailing on a file which is rotated and its name is changed daily like `test-'date +"%Y%m%d".log`.

  - locktail.sh: Tailing on rotated files with static file names
  - locktail_rotate.sh: Tailing on rotated files with rotated file names

Version
----
1.0

Excemple
----
```
# Origin log stream configuration for flume 
# 1. Load balancing
# 2. Event header modifying in-flight

originAgent.sources = originSource1 originSource2
originAgent.channels = originChannel
originAgent.sinks = originSink1 originSink2 originSink3

#-------------------------------------------
#               SOURCE
#-------------------------------------------
originAgent.sources.originSource1.type = exec
originAgent.sources.originSource1.command = /path/to/locktail.sh /path/to/your.log
originAgent.sources.originSource1.channels = originChannel

# data interceptors
originAgent.sources.originSource1.interceptors = addTopic addTimestamp addOriginLabel
originAgent.sources.originSource1.interceptors.addTopic.type = static
originAgent.sources.originSource1.interceptors.addTopic.key = topic
originAgent.sources.originSource1.interceptors.addTopic.value = log
originAgent.sources.originSource1.interceptors.addTimestamp.type = timestamp
originAgent.sources.originSource1.interceptors.addOriginLabel.type = static
originAgent.sources.originSource1.interceptors.addOriginLabel.key = origin
originAgent.sources.originSource1.interceptors.addOriginLabel.value = static_log


originAgent.sources.originSource2.type = exec
originAgent.sources.originSource2.command = /path/to/locktail_rotate.sh /path/to/your.DATE_ROTATE.log 'date +"%Y%m%d"'
originAgent.sources.originSource2.channels = originChannel

# data interceptors
originAgent.sources.originSource2.interceptors = addTopic addTimestamp addOriginLabel
originAgent.sources.originSource2.interceptors.addTopic.type = static
originAgent.sources.originSource2.interceptors.addTopic.key = topic
originAgent.sources.originSource2.interceptors.addTopic.value = log
originAgent.sources.originSource2.interceptors.addTimestamp.type = timestamp
originAgent.sources.originSource2.interceptors.addOriginLabel.type = static
originAgent.sources.originSource2.interceptors.addOriginLabel.key = origin
originAgent.sources.originSource2.interceptors.addOriginLabel.value = rotate_log

#-------------------------------------------
#               CHANNEL
#-------------------------------------------
# Each channel's type is defined.
originAgent.channels.originChannel.type = memory
originAgent.channels.originChannel.capacity = 20000
originAgent.channels.originChannel.transactionCapacity = 1000

#-------------------------------------------
#               SINK
#-------------------------------------------
#Specify the channel the sink should use
originAgent.sinks.originSink1.channel = originChannel
originAgent.sinks.originSink1.type = avro
originAgent.sinks.originSink1.hostname = host_1
originAgent.sinks.originSink1.port = 32120

originAgent.sinks.originSink2.channel = originChannel
originAgent.sinks.originSink2.type = avro
originAgent.sinks.originSink2.hostname = host_2
originAgent.sinks.originSink2.port = 32120

originAgent.sinks.originSink3.channel = originChannel
originAgent.sinks.originSink3.type = avro
originAgent.sinks.originSink3.hostname = host_3
originAgent.sinks.originSink3.port = 32120

originAgent.sinkgroups = originSinkGroup
originAgent.sinkgroups.originSinkGroup.sinks = originSink1 originSink2 originSink3
originAgent.sinkgroups.originSinkGroup.processor.type = load_balance
originAgent.sinkgroups.originSinkGroup.backoff = true
originAgent.sinkgroups.originSinkGroup.selector = round_robin
```


