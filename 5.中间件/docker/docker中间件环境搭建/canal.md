docker run --name canal -p 11111:11111 -idt -e canal.instance.master.address=172.23.3.63:33060 -e canal.instance.dbUsername=canal -e canal.instance.dbPassword=canal canal/cana-server:v1.1.4
