# KafkaSource-Demo
Setting up kafka source to send data to display with knative event display 

Prereqs: 
1. Knative serving operator installed (https://docs.openshift.com/container-platform/4.3/serverless/installing_serverless/installing-knative-serving.html)

2. Knative Eventing operator installed (https://docs.openshift.com/container-platform/4.3/serverless/installing_serverless/installing-knative-eventing.html#installing-knative-eventing)

Create new project

```
oc new-project kafka-source-knative
```


# Setting up Kafka
Install strimzi files 

```
oc apply -f 'https://strimzi.io/install/latest?namespace=kafka-source-knative'
```

To deploy kafka,

```
oc apply -f kafka/kafka.yaml 
```


# Setting up Knative event-display
To deploy KafkaSource defintion, 

```
oc apply -f https://storage.googleapis.com/knative-releases/eventing-contrib/latest/kafka-source.yaml
```

To deploy kafka-topic,

```
oc apply -f kafka/kafka-topic.yaml
```

Knative event-display will allow us to view messages sent from kafka source 
To deploy event-display,
```
oc apply -f knative-eventing/event-display.yaml
```

To deploy an event-source,
```
oc apply -f knative-eventing/event-source.yaml
```

Now we need to create a kafka producer pod so we can send data (messages with kafka-producer) to event-display 
```
oc run kafka-producer -ti --image=strimzi/kafka:0.14.0-kafka-2.3.0 --rm=true --restart=Never -- bin/kafka-console-producer.sh --broker-list my-cluster-kafka-bootstrap:9092 --topic knative-demo-topic
```

To view data go to event-display pod and view logs
