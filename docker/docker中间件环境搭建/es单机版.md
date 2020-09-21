<!--
 * @Descripttion: 
 * @Author: guox
 * @Date: 2020-09-21 16:57:50
 * @LastEditors: guox
-->
# es单机版

+ 拉取镜像
`docker pull elasticsearch:6.6.0`
+ 运行镜像
`docker run -d --name es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:6.6.0`