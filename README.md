# docker-rest-api-example

This config is usefull for anyone that is looking for a solution to orchestrate docker containers using a REST API, if you don't want to spend a bunch of time configuring kuber or any big tool, if you are looking for a straigth solution to just pull, create containers and deploy it thru a CI solution, it might be the perfect solution.

This tutorial was wrote using docker:

`
$ docker --version
Docker version 1.13.1, build 092cba3
`

### edit file /lib/systemd/system/docker.service and replace the ExecStart with the following line:

`
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375  $DOCKER_OPTS
`

### restart docker daemon
`
systemctl daemon-reload
service docker restart
`
### in order to list images, execute the following command:
`
curl http://localhost:2375/images/json
`

result:
```
[  
   {  
      "Containers":-1,
      "Created":1525477287,
      "Id":"sha256:5f5ccdc8aedc4183c2f79dde76d7412913762bf0f0070cd503488b8989e1ed23",
      "Labels":null,
      "ParentId":"",
      "RepoDigests":[  
         "mysql@sha256:7e3575b7949a4f8712a47b0fc875022dcea5d84f4672b1532587b6933502f757"
      ],
      "RepoTags":[  
         "mysql:5.6"
      ],
      "SharedSize":-1,
      "Size":256072166,
      "VirtualSize":256072166
   }
]
```

### let's pull the lastest mysql image

`
sudo curl -v -XPOST http://localhost:2375/images/create?fromImage=mysql:5.6
`

### to create a new container

`
http://localhost:2375/containers/create?name=mysql-test
`
with the following post json body:

```
{
       "Hostname": "",
       "Domainname": "",
       "User": "",
       "AttachStdin": false,
       "AttachStdout": true,
       "AttachStderr": true,
       "Tty": false,
       "OpenStdin": false,
       "StdinOnce": false,
       "Env": [
               "MYSQL_ALLOW_EMPTY_PASSWORD=yes",
               "MYSQL_ROOT_PASSWORD=123123"
       ],
       "Image": "mysql:5.6"
  }
```


### try list containers

`
curl http://localhost:2375/containers/json
`

### The result is a json output with all containers information, as below 

```
{  
   "Id":"bbfa194e2c60125f65fa6a105dc8436ebc8acdc10faddf1dfbf61b20d1ef9147",
   "Names":[  
      "/mysql"
   ],
   "Image":"mysql:5.6",
   "ImageID":"sha256:5f5ccdc8aedc4183c2f79dde76d7412913762bf0f0070cd503488b8989e1ed23",
   "Command":"docker-entrypoint.sh mysqld",
   "Created":1528723159,
   "Ports":[  
      {  
         "IP":"0.0.0.0",
         "PrivatePort":3306,
         "PublicPort":3309,
         "Type":"tcp"
      }
   ],
   "Labels":{  

   },
   "State":"running",
   "Status":"Up About a minute",
   "HostConfig":{  
      "NetworkMode":"default"
   },
   "NetworkSettings":{  
      "Networks":{  
         "bridge":{  
            "IPAMConfig":null,
            "Links":null,
            "Aliases":null,
            "NetworkID":"59488601c6f86c57f817bf2627f49b0aed004141cb5fdb5a6c2734185476ec0b",
            "EndpointID":"cc3718b38e319cc4e4c62578be69fd503c9dcf3ff2357dc2de652fefb5dfc42d",
            "Gateway":"172.17.0.1",
            "IPAddress":"172.17.0.2",
            "IPPrefixLen":16,
            "IPv6Gateway":"",
            "GlobalIPv6Address":"",
            "GlobalIPv6PrefixLen":0,
            "MacAddress":"02:42:ac:11:00:02"
         }
      }
   },
   "Mounts":[  
      {  
         "Type":"bind",
         "Source":"/home/thiago/mysqldata",
         "Destination":"/var/lib/mysql",
         "Mode":"Z",
         "RW":true,
         "Propagation":""
      }
   ]
}

```

### To create a conainer use the following POST

`
POST /containers/create
`

the full API spec and post examples could be find in: 
[Docker REST](https://docs.docker.com/engine/api/v1.24/#31-containers)   https://docs.docker.com/engine/api/v1.24/#31-containers



