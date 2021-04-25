https://www.cnblogs.com/yanwanglol/p/9860202.html

docker search fastdfs
docker pull delron/fastdfs
docker run -d --network=host --name tracker -v /var/fdfs/tracker:/var/fdfs fastdfs tracker
docker run -d --network=host --name storage -e TRACKER_SERVER=10.128.35.28:22122 -v /var/fdfs/storage:/var/fdfs -e GROUP_NAME=group1 fastdfs storage
docker exec -it storage bash
vi /home/test.txt
/usr/bin/fdfs_upload_file /etc/fdfs/client.conf /home/test.txt
http://10.128.35.28:8888/group1/M00/00/00/CoAjHGCFTNeAOUhBAAAABD1ngkg800.txt