## Updating nervenet tsg sqlite docker container to image 1.6.2

#### Remove current nervenet-tsg-sqlite 

1. Stop the existing running nervenet-tsg-sqlite image
```
docker stop nervenet-tsg-sqlite 
```

2. Remove the running container 
```
docker rm nervenet-tsg-sqlite 
```

3. Remove the image from containers
```
docker rmi [current nervenet-tsg-sqlite.version] 
```

4. Load the latest image
```
docker load < nervenet-tsg-sqlite-1.6.2.docker.tar.gz 
```

5. Run the latest image with the following arguments
```
docker run --name=nervenet-tsg-sqlite -d --network=host --privileged --mount type=bind,src=/writable/personal,dst=/writable/personal --mount type=bind,src=/writable/refconf,dst=/writable/refconf --mount type=bind,src=/var/log,dst=/var/log --mount type=bind,src=/var/tmp,dst=/var/tmp --restart always nervenet-tsg-sqlite:1.6.2
```
