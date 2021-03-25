
docker-compose config

docker-compose up -d kafka

docker-compose stop kafka 

docker rm  kafka 

docker ps -a 

docker-compose stop kafka nginx php

docker-compose rm  kafka nginx php

docker-compose up -d --scale kafka=4

docker-compose ps 


docker ps -a

docker-compose stop kafka1 kafka2 kafka3

docker-compose rm kafka1 kafka2 kafka3
docker rm kafka1 kafka2 kafka3


docker images

docker rmi 28ffb774bc32


docker-compose config

docker-compose up -d zk1 zk2 zk3
docker-compose up -d kafka1 kafka2 kafka3


docker-compose -f docker-compose-zookeeper.yml ps
docker-compose -f docker-compose-zookeeper.yml stop zk1 zk2 zk3
docker-compose -f docker-compose-zookeeper.yml rm  zk1 zk2 zk3
docker-compose -f docker-compose-zookeeper.yml config
docker-compose -f docker-compose-zookeeper.yml up -d

docker-compose -f docker-compose-kafka.yml ps
docker-compose -f docker-compose-kafka.yml stop kafka1 kafka2 kafka3
docker-compose -f docker-compose-kafka.yml rm  kafka1 kafka2 kafka3
docker-compose -f docker-compose-kafka.yml config
docker-compose -f docker-compose-kafka.yml up -d


#test测试
docker exec kafka1 kafka-topics.sh --create --topic topic001 --partitions 3 --zookeeper zk1:2181 --replication-factor 2

docker exec kafka1 kafka-topics.sh --list --zookeeper zk1:2181

docker exec kafka3 kafka-topics.sh --describe --topic zhou --zookeeper zk2:2181

#消费消息
docker exec kafka1 kafka-console-consumer.sh --topic topic001 --bootstrap-server kafka1:9092,kafka2:9093,kafka3:9094
docker exec kafka1 kafka-console-consumer.sh --topic zhou --bootstrap-server 192.168.31.244:9092,192.168.31.244:9093,192.168.31.244:9094


#生产消息
docker exec -it kafka1 kafka-console-producer.sh --topic topic001 --broker-list kafka1:9092,kafka2:9093,kafka3:9094
docker exec -it kafka1 kafka-console-producer.sh --topic topic001 --broker-list 182.150.164.173:9092,182.150.164.173:9092,182.150.164.173:9092
docker exec -it kafka1 kafka-console-producer.sh --topic zhou --broker-list 192.168.31.244:9092,192.168.31.244:9093,192.168.31.244:9094

###### 现在已经进入了生产消息的命令行模式，输入一些字符串然后回车，再去消费消息的控制台窗口看看，已经有消息打印出来，说明消息的生产和消费都成功了