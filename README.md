# RNCTech Kafka setup on Kubernetes Cluster
========================================

1, create and deploy zookeeper
   deploy zookeeper service and pvc
   deploy zookeeper deployment

   make sure zookeeper pod is running


2, create and deploy kafka cluster
 2.1, deploy kafka-service, kafka-nodeport, kafka-data-persistentvolumeclaim and kafka-conf
   kubectl get svc | grep kafka
   to get ClusterIP

 2.2,  update kafka-deployment-external-column.yml with above ip address of EXTERNAL_PLAINTEXT ${ip} / localhost
   value: "INTERNAL_PLAINTEXT://kafka-np:30092,EXTERNAL_PLAINTEXT://${ip}:9092"

 2.3, create kafka deploy as below
   kubectl create -f kafka-deployment-external-volume.yml


3, start RNCtech application pods
 3.1, deploy kafkacat for testing
    kubectl create -f kafkacat.yml

    kubectl exec -ti ${kafkacat} bash

    -- list topics
      kafkacat -b kafka:9092 -L       (kafka:9092 external access)
      kafkacat -b kafka-np:30092 -L   (kafka-np:30092 in-cluster access)

      kafkacat -b kafka-np:30092 -P -t testTopic  (producer)
      kafkacat -b kafka-np:30092 -C -t testTopic  (consumer)

 3.2, deploy application consumers such as
        Webhook consumer
        ETL consumer

4, followups
  4.1 PVC config ReadWriteMany on Prod (aws storage/NFS)
  4.2 consumer group with comment sync/async
