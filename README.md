# Ansible for Couchbase cluster

The ansible plaboks here are for configuration of a Couchbase cluster.
The instructions here are fpr 3 data nodes, 2 index nodes and another node for backups and data loading via the cli.


# Test your environment
 ```
   ansible -i inventory/hosts.conf  all  -m ping
```
we should get 
```
tf-cb01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

# Initialize the nodes
[https://docs.couchbase.com/server/current/cli/cbcli/couchbase-cli-node-init.html](https://docs.couchbase.com/server/current/cli/cbcli/couchbase-cli-node-init.html)
```
ansible-playbook -i inventory/hosts.conf  playbook-cb-init-nodes.yml 
```


Then we would go to one of the data nodes web console and Initialize a new cluster.
[https://cb01.example.com:18091](https://cb01.example.com:18091)

The rest of the nodes will be added via the REST API as described in the official documentation
[https://docs.couchbase.com/server/current/rest-api/rest-cluster-joinnode.html](https://docs.couchbase.com/server/current/rest-api/rest-cluster-joinnode.html)

We can add them all at once and then from the console from the first node we would accept


### The rest of the data nodes
```
curl -kv -X POST -u Administrator:passwordString \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb02.example.com' \
-d 'user=Administrator' \
-d 'password=passwordString' \
-d 'services=kv,n1ql'


curl -kv -X POST -u Administrator:passwordString \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb03.example.com' \
-d 'user=Administrator' \
-d 'password=passwordString' \
-d 'services=kv,n1ql'
```
#### INDEX NODES
```
curl -kv -X POST -u Administrator:passwordString \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-index01.example.com' \
-d 'user=Administrator' \
-d 'password=passwordString' \
-d 'services=index'


curl -kv -X POST -u Administrator:passwordString \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-index02.example.com' \
-d 'user=Administrator' \
-d 'password=passwordString' \
-d 'services=index'
```
#### Backup Node
```
curl -kv -X POST -u Administrator:passwordString \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-backup.example.com' \
-d 'user=Administrator' \
-d 'password=passwordString' \
-d 'services=backup'

```


# Remove Couchbase
```
ansible-playbook -i inventory/hosts.conf  playbook-remove-couchbase-server.yml 
```
