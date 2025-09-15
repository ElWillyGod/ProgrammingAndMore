Docker

docker swarm init --advertise-addr eth0

docker swarm join --token

docker node ls

docker service create --detach=true --name nginx1 --publish 80:80  --mount source=/etc/hostname,target=/usr/share/nginx/html/index.html,type=bind,ro nginx:1.12

docker service ls

docker service update --replicas=5 --detach=true nginx1
nginx1