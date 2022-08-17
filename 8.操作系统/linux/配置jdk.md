export JAVA_HOME=/usr/local/java/jdk1.8.0_261
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH


javac 出错  -bash: /home/jdk1.8.0_202/bin/javac: /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory
yum install glibc.i686