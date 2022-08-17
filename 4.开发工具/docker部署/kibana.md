
docker run -itd  -p 5601:5601 --name kibana -e  ELASTICSEARCH_HOSTS=http://172.28.21.8:9200  kibana:6.6.0