# 第三方参考文档
https://blog.csdn.net/moshowgame/article/details/80304198

# 官方文档
https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter
https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98

问题
	配置完成启动出现错误：Reason: org.apache.log4j.Logger
		缺少日志依赖
		<dependency>
		<groupId>org.slf4j</groupId>
		<artifactId>slf4j-log4j12</artifactId>
		</dependency>
	
启动支持出错：java.lang.ClassNotFoundException: org.springframework.jdbc.datasource.embedd