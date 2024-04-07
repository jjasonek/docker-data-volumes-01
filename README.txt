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


PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker stop feedback-app
feedback-app

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker rmi feedback-node:volumes
Untagged: feedback-node:volumes
Deleted: sha256:f9e22d3b261b740b027f475cb9ca1a095557d3cf87907757ff65854af871f670

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker build -t feedback-node:volumes .

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker run -p 3000:80 -d --rm --name feedback-app feedback-node:volumes
bffe22bc66594cafeaebded9e8726f19dbba8f758079462191ea1765e11d71a0


## try to restart
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker stop feedback-app
feedback-app
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker run -p 3000:80 -d --rm --name feedback-app feedback-node:volumes
fc3bbe2097686539f741a44f17094c73ca0c2f6bbca6953598c01f86a1a5543f

## but the files are still not there


PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker volume --help

Usage:  docker volume COMMAND

Manage volumes

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove unused local volumes
  rm          Remove one or more volumes

Run 'docker volume COMMAND --help' for more information on a command.
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker volume ls
DRIVER    VOLUME NAME
local     c9c9cc08c12e06e652ff929b6dc80e72d5cf7ad64325e7bf952dcc8cd723b14a

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker volume ls
DRIVER    VOLUME NAME

## We can see here that our ANONYMOUS VOLUME is gone.
## So we need the NAMES VOLUME, which can't be created in Dockerfile

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker stop feedback-app
Error response from daemon: No such container: feedback-app
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker rmi feedback-node:volumes
Untagged: feedback-node:volumes
Deleted: sha256:4452f6e2c41486cdd0aecfbcdb2c2f5743b819ed44584e72ded938ee5928377d
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker build -t feedback-node:volumes .
[+] Building 2.2s (10/10) FINISHED
...
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker run -p 3000:80 -d --rm --name feedback-app -v feedback:/app/feedback feedback-node:volumes
c9450abc91bba11bb108e5f9959b6a46b45eff0bb00811c48712456a29be20e4

## now the volume exists even after the container is removed!

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker stop feedback-app

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker volume ls
DRIVER    VOLUME NAME
local     feedback


## ADDING BIND MOUNT (NOT SUCCESSFUL YET)

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker run -p 3000:80 -d --rm --name feedback-app -v feedback:/app/feedback -v "C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup:/app" feedback-node:volumes
1ac9e608e220081ae26e58c7c591d727869f5100078b8c9bb83be765a825de16
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

## Start without --rm to be able to see the logs
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker run -p 3000:80 -d --name feedback-app -v feedback:/app/feedback -v "C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup:/app" feedback-node:volumes
7314f5d85d84fc5d27f4bb0b88c734b83fb2dc221d560432cc37d210b88da051
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker ps -a
CONTAINER ID   IMAGE                   COMMAND                  CREATED         STATUS                     PORTS     NAMES
7314f5d85d84   feedback-node:volumes   "docker-entrypoint.s…"   6 seconds ago   Exited (1) 4 seconds ago             feedback-app
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker logs feedback-app
internal/modules/cjs/loader.js:934
  throw err;
  ^

Error: Cannot find module 'express'
Require stack:
- /app/server.js
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:931:15)
    at Function.Module._load (internal/modules/cjs/loader.js:774:27)
    at Module.require (internal/modules/cjs/loader.js:1003:19)
    at require (internal/modules/cjs/helpers.js:107:18)
    at Object.<anonymous> (/app/server.js:5:17)
    at Module._compile (internal/modules/cjs/loader.js:1114:14)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1143:10)
    at Module.load (internal/modules/cjs/loader.js:979:32)
    at Function.Module._load (internal/modules/cjs/loader.js:819:12)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:75:12) {
  code: 'MODULE_NOT_FOUND',
  requireStack: [ '/app/server.js' ]
}

## the problem here is that Docker overwrites container /app with content from local host's /app
## including generated /node_volumes
## To solve the problem we add another ANONYMOUS volume: -v /app/node_modules
## Or we can do it also in the Dockerfile: VOLUME [ "/app/node_modules" ]
## In this case LONGER internal path wins so this approach works as kind of exclusion

PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker rm feedback-app
feedback-app
PS C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup> docker run -p 3000:80 -d --name feedback-app -v feedback:/app/feedback -v "C:\Training\Docker\data-volumes-01-starting-setup\data-volumes-01-starting-setup:/app" -v /app/node_modules feedback-node:volumes
2e0d5d7222f4b096fc0b9e8a92fcf61faa6f20b2c48f5e98df240eebf79b98e9
