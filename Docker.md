Remove all containers
```
docker rm -vf $(docker ps -aq)
```

Remove all images
```
docker rmi -f $(docker images -aq)
```