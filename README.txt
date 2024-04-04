PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker build -t feedback-node .
[+] Building 16.2s (10/10) FINISHED                                                                                                                                                                                  docker:default
 ...

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker images
REPOSITORY                  TAG                  IMAGE ID       CREATED          SIZE
feedback-node               latest               0373476d4879   55 seconds ago   186MB
 ...

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker run -p 3000:80 -d --name feedback-app --rm feedback-node
02c268cad2d5d6eef4dd0960fb95b08845977a8ce07f9b9ff240ac6439d3670f

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                  NAMES
02c268cad2d5   feedback-node   "docker-entrypoint.s…"   26 seconds ago   Up 26 seconds   0.0.0.0:3000->80/tcp   feedback-app


## Then we can see http://localhost:3000
## And after posting some input we have a file for example: http://localhost:3000/feedback/awesome.txt

## If the container is not deleted, files are still in it.
## Because the file system is inside the container. And data is not deleted until container is removed
## (or re-built because of code change).


