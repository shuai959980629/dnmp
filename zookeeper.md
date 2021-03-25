
docker ps -a

docker-compose stop zk1 zk2 zk3

docker-compose rm zk1 zk2 zk3
docker rm zk1 zk2 zk3


docker images

docker rmi 28ffb774bc32


docker-compose config

docker-compose up -d zk1 zk2 zk3

docker exec -it 17e08451f82d bash


docker-compose -f docker-compose-zookeeper.yml ps
docker-compose -f docker-compose-zookeeper.yml stop zk1 zk2 zk3
docker-compose -f docker-compose-zookeeper.yml rm  zk1 zk2 zk3
docker-compose -f docker-compose-zookeeper.yml config
docker-compose -f docker-compose-zookeeper.yml up -d 

