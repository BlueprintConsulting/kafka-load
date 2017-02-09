Build the project
sbt clean stage universal:packageBin
or, if you want to scp or ftp the created jar to some other maching, do 'dist' instead of 'stage'
sbt clean dist universal:packageBin

cd to target/universal
either unip the jar or cd into stage, assuming there is a stage directory
run with 
  bash bin/kafka-load
you can change the conf/application.conf file so that you are a producer or a consumer
if read=true, you're a consumer
otherwise you're a producer
if you're a producer, you can change the speed of production by decreasing restBetweenWrites and/or increasing numWriters
numRecordsToWrite is the number that each thread (each writer) will write
   unless your max.runtime expires before all the records have been written
   I often just shut down the process manually. currently it won't stop on its own, it will just stop writing
window.duration.in.millis tells an Akka Actor how long to wait between reporting stats
  that same property tells the actor about which time period it's reporting
  for example, window.duration.in.millis = 2000 means the actor will attempt to report every 2 seconds
    and the actor will calculate events/second for that same time period
    the actor also calculates events/second for the full proces time

most of the code is somewhat general. Driver is an example of how to use the code.

the rest of the notes below will probably be removed soon. They are just dashed off notes, unlike the beautiful prose above


bin/zookeeper-server-start.sh config/zookeeper.properties &
bin/kafka-server-start.sh config/server1.properties &
bin/kafka-server-start.sh config/server2.properties &

bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 2 --topic orders
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 2 --topic metrics
And to verify that your topics have been created:
bin/kafka-topics.sh --zookeeper localhost:2181 --describe

bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic <theTopic>

/Users/charlestassoni/scala/blueprint/dev/SC-SCTEL-Plat-Common/src/test/resources/com/microsoft/shm/bpm.testevents/JemLoopsjson.txt bpm_in 10.0.0.247:9093,10.0.0.247:9094
dummFile bpm_in 10.0.0.39:9093,10.0.0.39:9094
dummFile bpm_in 10.1.10.52:9093,10.1.10.52:9094

gogo air
172.19.131.166
host.name=172.19.131.166

10.1.10.52

export BOOTSTRAP_SERVERS=10.0.0.247:9093,10.0.0.247:9094


You can use the Kafka command line perf test instead of the consumer in this project. I don't like it as much, but you might.
./kafka-consumer-perf-test.sh --messages 100000 --topic bpm_in --zookeeper 127.0.0.1:2181 --from-latest --num-fetch-threads 2

Could do something like this if you want to do the above many times:

for i in `seq 1 1000`; do kafka-consumer-perf-test.sh --messages 100000 --topic bpm_in --zookeeper 127.0.0.1:2181 --from-latest --num-fetch-threads 2; done

