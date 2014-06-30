Was expreimenting with getting the elk stack set up at home with ansible and docker.  Attempted to do this so that the containers would be managed by upstart.

# Local Requirements
- `pip install ansible>=1.6.5`

# Real ELK playbook requirements
- `pip` should be installed and runnable on root
- `docker` should be installed

# Testing ELK playbook
- make sure `vagrant>= 1.4` is installed
- `vagrant up` - this will fail the first time due to what seems to be [a bug in the docker module which supposedly was fixed](https://github.com/ansible/ansible/issues/7367) but seems to still be broken
- `vagrant provision` - you'll have to do this 2 times (the first time will fail) due to the aforementioned bug
- `vagrant ssh` in to verify the state

### Check the ELK stack containers are ok
First, make sure all 3 containers are running:

    $ docker ps
    CONTAINER ID        IMAGE                             COMMAND                CREATED              STATUS              PORTS                                            NAMES
    2106537da66d        myself/kibana:latest              nginx                  About a minute ago   Up About a minute   443/tcp, 0.0.0.0:8080->80/tcp                    kibana                                                      
    c66ed19f6ec9        myself/logstash:latest            /bin/sh -c '/logstas   2 minutes ago        Up 2 minutes        0.0.0.0:5000->5000/tcp                           logstash                                                    
    6d8a605074bc        dockerfile/elasticsearch:latest   /elasticsearch/bin/e   4 minutes ago        Up 4 minutes        0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp   elasticsearch,kibana/elasticsearch,logstash/elasticsearch   

Now check to make sure they were set up correctly:

- make sure elasticsearch, logstash, and kibana all 
  have listening ports mapped correctly:

        $ netstat -l --numeric-ports
        ...
        tcp6     0      0 [::]:9200         [::]:*             LISTEN
        tcp6     0      0 [::]:9300         [::]:*             LISTEN
        tcp6     0      0 [::]:5000         [::]:*             LISTEN
        tcp6     0      0 [::]:8123         [::]:*             LISTEN
        ...
   
- make sure elasticsearch is actually running 

        $ curl localhost:9200
        {
          "status" : 200,
          "name" : "Grim Hunter",
          "version" : {
            "number" : "1.2.1",
            "build_hash" : "6c95b759f9e7ef0f8e17f77d850da43ce8a4b364",
            "build_timestamp" : "2014-06-03T15:02:52Z",
            "build_snapshot" : false,
            "lucene_version" : "4.8"
          },
          "tagline" : "You Know, for Search"
        }

- make sure logstash can accept connections
        
        $ telnet localhost 5000
        Trying 127.0.0.1...
        Connected to localhost.
        Escape character is '^]'.
        ^]

        telnet> Connection closed.

- make sure that the kibana nginx is proxying elasticsearch

        $ curl localhost:8080/_nodes | python -m json.tool
        {
            "cluster_name": "elasticsearch", 
            "nodes": {
                "bHWFXra-QiigQitRs4N1YA": {
                    "build": "6c95b75", 
                    "host": "6d8a605074bc", 
                    ...
                ...
            ...
        }

- make sure that the kibana nginx is serving the kibana3 app

        $ curl localhost:8123
        <!DOCTYPE html>...<title>Kibana 3{{dashboard.current.title ? " - "+dashboard.current.title : ""}}</title>...</html>


### Check that syslogs are being forwarded to logstash

Run `curl localhost:8123/logstash-2014.06.29/_search?q=type:*` and make sure that some syslog messages are returned

