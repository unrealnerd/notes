# Notes On Docker
* Set up an external network switch [Steps](https://dev.to/bitsmonkey/setting-up-docker-with-hyper-v-4936)

- Creating a docker machine

  `docker-machine create -d hyperv --hyperv-virtual-switch "Primary Virtual Switch" default`
- When we get errror *"Verify that the Virtual Machine Management service on the computer is running"* run this comand

  `MOFCOMP %SYSTEMROOT%\System32\WindowsVirtualization.V2.mof`
- List docker machines

  `docker-machine ls`
- Set docker machines environment variables

  powershell-`docker-machine env default | Invoke-Expression`

  just show-`docker-machine env default`

  gitbash-`eval $("C:\Users<username>\bin\docker-machine.exe" env default --shell bash)`
  
  *Setting enviroment like this is per session of the shell only so if running multiple shells, we need to run everytime we open and shell and deal with docker*

- Creating a docker image

   `docker build -t <projname> .`
   - list docker images `docker image ls`

- Saving and laoding container image

  `docker pull <image>`

  `docker image ls`

  `docker save -o c:/image.tar <imagename>`

  `docker load -i c:/image.tar`
- To check or execute something in the container we can use `docker exec -it bash`
- Remove All containers `docker rm $(docker ps -aq)`
- Run Mongo on docker with volumes mapped so that DB is persistent
  `docker run -d -v /data:/data/db -p 27017:27017 mongo`
- Run Mongo express UI to connect mongo container `docker run -e ME_CONFIG_MONGODB_SERVER="192.168.0.125" -p 8081:8081 mongo-express`
- Volumes and sharing files in Docker- 
  * creating a volume- `docker create volume mongodata`
  * running a container with volume attached- ` docker run --it --name mongo -v mongodata/mymongo mongo`
  * copying file from host to contatiner or vice-versa- `docker cp c:\\sample mongo:mongodata/mymongo`
- In case we get error when doing `docker-compose up` saying image not found then run this command `docker-compose rm`. Its cause of the old cache images
- copying the files from localhost to host vm docker machine 
  * ```bash
      docker create volume mongodata
      docker run -it -v mongodata:/root busybox
      docker start -ai inspiring_leavitt
      docker cp . inspiring_leavitt:/root
      docker run --rm -i -v=mongodata:/tmp/mydata busybox ls ./tmp/mydata/data
    ```
  * docker run -it -v mongodata:/root busybox 
- when disk full space issues run `docker system prune -a`
- export the collecction as json data `.\mongoexport.exe -d phrasesdb --collection phrases -o C:\backup\phrases.json`
- [Why alpine](https://nickjanetakis.com/blog/the-3-biggest-wins-when-using-alpine-as-a-base-docker-image)
- " $'\r': command not found" make sure to change CRLF to LF on windows any IDE and then build docker image 
- "Waiting for SSH to be available" when stuck at this line I had to goto network adapters and uncheck wifi from the bridge then goto hyperv and set the virtual switch to external
- network_mode: "host" using this yml will let us use "localhost" address as the docker container instance instead of host machine 
- prune cache image