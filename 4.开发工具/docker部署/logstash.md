<!--
 * @Descripttion: 
 * @Author: guox
 * @Date: 2020-09-21 17:15:07
 * @LastEditors: guox
-->
# logstash

+ 拉取镜像
`docker pull logstash:6.6.0`

+ 配置文件pipelines.yml、mongo.conf、es.conf
`docker run -it -d -p 5044:5044 -p 5045:5045 --name logstash -v /home/bdf/logstash/pipelines.yml:/usr/share/logstash/config/pipelines.yml -v /home/bdf/logstash/pipeline/:/usr/share/logstash/pipeline/ logstash:6.6.0`