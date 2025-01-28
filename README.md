# Docker Minecraft Server
Repository to deploy a **fabric minecraft 1.21.1 server** using [Docker Desktop](https://docs.docker.com/desktop/) or [Docker Engine](https://docs.docker.com/engine/install/) with observability tools 


## Documentation
It is **highly recommended** to [Read Iztg's Documentation](https://docker-minecraft-server.readthedocs.io/en/latest/) to fully customize your docker-compose.yaml file to fit your desired configs.

## Tools
The base observability tools included are:

- [Grafana](https://grafana.com/docs/grafana/latest/introduction/)
- [Promtail](https://grafana.com/docs/loki/latest/send-data/promtail/#:~:text=Promtail%20is%20an%20agent%20which,will%20occur%20in%20Grafana%20Alloy.)
- [Loki](https://grafana.com/oss/loki/)
- [Prometheus](https://prometheus.io/docs/introduction/overview/) `v-1.21.1-fabric` included in the mods file

## Content
This repo contains the following files:
- ***docker-compose.yaml***: Contains all the services to initialize the server and the observability tools, it also creates its own network to communicate each service 
  
- ***.env***: An example .env file or **environment variables** to customize your **local machine ports**, variables are set with the default values aswell as the **ops** for the server.

- ***plugins***: This folder contains the .jar files for specific **mods** to be copied into the container, in this case it contains the **Prometheus Exporter v1.21.1** to be able to obtain the metrics, the **fabric-api** to be able to launch the server and **lithium** to improve performance,  it is recommended to [Read This Part To Differenciate Mods From Plugins](https://docker-minecraft-server.readthedocs.io/en/latest/mods-and-plugins/), you can also create your own **plugins** folder and modify the `volumes` in the `docker-compose` to map specific plugins in case you need it. If you want to **add a modpack** the documentation explains how to include them easily.

- ***prometheus***: This folder contains the `prometheus.yml` with the config to scrape metrics of the different services, in this case minecraft and prometheus itself.
  
## How to Run
0- Install [Docker](https://docs.docker.com/desktop/) in your computer and check if your bios has [virtualization](https://support.microsoft.com/en-us/windows/enable-virtualization-on-windows-c5578302-6e43-4b4b-a449-8ced115f58e1) on, this change will require to reboot in order to set up. 

0.5- If you are using a linux distro, you **should** add the following line `user: "root:root"` to the `grafana` service inside `docker-compose.yaml` in since it may run into permission issues. 

1- Use bash/powershell terminal and head to the repository folder.

2- Execute `docker compose up -d` and wait for the containers to be up and running.

3- Head into multiplayer and add a new server with the ip `localhost:25565` or `127.0.0.1:25565` if you didnt change the variable.

4- Server should be up and running for LAN.

## Port Fowarding

### Windows

In order for your friends to be able to connect to your server there are a serious of steps to do in order for your server to be accessible to the public:

1- Head into your router config, you can access by typing `ipconfig` in windows and searching for `ethernet default gateway`. After logging into your router head into the `port forwarding`, fill in a **new rule** with the `external port` being the one you set in your **.env** file as `SERVER_PORT`, and your `ethernet IPv4` as `internal ip adress` or if you are Host you can easily log in with the `localhost:ServerPort`.

2- In Windows head into **entry rules** in the `Windows Defender Firewall Advanced Security` and create a **new rule**.Choose the option you selected in the previous step(TCP or UDP) and select the same port chosen previously, after that just hit next until the last step where you can fill in with the name you desire.

After this two steps your friends should be able to connect with `Your Public IP:ServerPort`. If you don't know your public ip or you want to check if the port is open [Click Here](https://www.portchecktool.com/). 

### Linux
1- Head into your router config, you can access by typing `ipconfig` in windows and searching for `ethernet default gateway`. After logging into your router head into the `port forwarding`, fill in a **new rule** with the `external port` being the one you set in your **.env** file as `SERVER_PORT`, and your `ethernet IPv4` as `internal ip adress` or if you are Host you can easily log in with the `localhost:ServerPort`.

After this step your friends should be able to connect with `Your Public IP:ServerPort`. If you don't know your public ip or you want to check if the port is open [Click Here](https://www.portchecktool.com/).

## Common Issues

- In case the **Host or anyone connected to the host router** wants to join by using the public ip may be incapable of connecting due to not having [NAT LOOPBACK](https://kb.netgear.com/000049578/What-is-NAT-loopback-and-which-NETGEAR-routers-support-NAT-loopback) support, for this case it is recommended to login with the private local ip address known as `Ethernet IPv4:ServerPort`.

- Another issue that may happen is if you have the whitelist activated where you need to manually add the different users into the whitelist.json to let them join your server with the following syntax:
`[{"name":"USERNAME","uuid":"USER UUID"}]` in case you dont know your user uuid you can [Check It Here](https://mcuuid.net/). An alternative to add users into the whitelist can be achieved inside the game by using the following command `/whitelist USERNAME` however you need an OP inside the server to trigger this command in the chat.

- I want to play in other version how can i do it? You can actually change the environment variable.
- My grafana container did not start and when i check the logs i get permission errors, what do i do? You are probably trying to start the server in a linux machine **Read step 0.5 on How to Run** 
