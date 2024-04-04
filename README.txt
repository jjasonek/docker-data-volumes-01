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


PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker build -t feedback-node:volumes .

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker images
REPOSITORY                  TAG                  IMAGE ID       CREATED          SIZE
feedback-node               volumes              f9e22d3b261b   51 seconds ago   186MB
feedback-node               latest               0373476d4879   4 hours ago      186MB

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker run -p 3000:80 -d --rm --name feedback-app feedback-node:volumes
63b06e1068ef676aa485bcce773085bc7187c7dd5f2c502cd66f7c7007c7d1cd

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED         STATUS         PORTS                  NAMES
63b06e1068ef   feedback-node:volumes   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   0.0.0.0:3000->80/tcp   feedback-app

## we can't save the feedback file. So we look into the container logs.

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker logs feedback-app
(node:1) UnhandledPromiseRejectionWarning: Error: EXDEV: cross-device link not permitted, rename '/app/temp/aw.txt' -> '/app/feedback/aw.txt'
(Use `node --trace-warnings ...` to show where the warning was created)
(node:1) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). To terminate the node process on unhandled promise rejection, use the CLI flag `--unhandled-rejections=strict` (see https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode). (rejection id: 1)
(node:1) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.
(node:1) UnhandledPromiseRejectionWarning: Error: EXDEV: cross-device link not permitted, rename '/app/temp/awesome.txt' -> '/app/feedback/awesome.txt'
(node:1) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). To terminate the node process on unhandled promise rejection, use the CLI flag `--unhandled-rejections=strict` (see https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode). (rejection id: 2)
(node:1) UnhandledPromiseRejectionWarning: Error: EXDEV: cross-device link not permitted, rename '/app/temp/awesome.txt' -> '/app/feedback/awesome.txt'
(node:1) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). To terminate the node process on unhandled promise rejection, use the CLI flag `--unhandled-rejections=strict` (see https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode). (rejection id: 3)
(node:1) UnhandledPromiseRejectionWarning: Error: EXDEV: cross-device link not permitted, rename '/app/temp/awesome.txt' -> '/app/feedback/awesome.txt'
(node:1) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). To terminate the node process on unhandled promise rejection, use the CLI flag `--unhandled-rejections=strict` (see https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode). (rejection id: 4)

## The error says: Error: EXDEV: cross-device link not permitted, rename '/app/temp/awesome.txt' -> '/app/feedback/awesome.txt'
