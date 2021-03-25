#官方文档
https://www.docker.elastic.co/r/logstash/logstash
https://www.elastic.co/guide/en/elasticsearch/reference/7.11/elasticsearch-intro.html
https://www.elastic.co/guide/en/kibana/7.11/introduction.html
https://www.elastic.co/guide/en/logstash/7.11/introduction.html

https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html
https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html

docker-compose -f docker-compose-elk.yml ps
docker-compose -f docker-compose-elk.yml stop es1 es2 kibana logstash
docker-compose -f docker-compose-elk.yml rm  es1 es2 kibana logstash
docker-compose -f docker-compose-elk.yml config
docker-compose -f docker-compose-elk.yml up -d 

docker-compose -f docker-compose-elk.yml ps



docker-compose up -d elasticsearch kibana logstash

docker-compose stop elasticsearch kibana logstash 
docker-compose rm elasticsearch kibana logstash 

docker-compose ps -a 



