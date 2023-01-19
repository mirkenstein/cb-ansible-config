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
test on a subset of the inventory [https://docs.ansible.com/ansible/latest/inventory_guide/intro_patterns.html](https://docs.ansible.com/ansible/latest/inventory_guide/intro_patterns.html)
```
ansible  -i inventory/hosts.conf tf-cb-index* -m ping
```
outputs 
```
tf-cb-index01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
...
```


# Initialize the nodes
[https://docs.couchbase.com/server/current/cli/cbcli/couchbase-cli-node-init.html](https://docs.couchbase.com/server/current/cli/cbcli/couchbase-cli-node-init.html)
```
ansible-playbook -i inventory/hosts.conf  playbook-cb-init-nodes.yml 
```
To execute that on a subset of the inventory add the `--limit` flag
```
ansible-playbook -i inventory/hosts.conf  playbook-cb-init-nodes.yml  --limit cb-tf-index*
```

Then we would go to one of the data nodes web console and Initialize a new cluster.
[https://cb01.example.com:18091](https://cb01.example.com:18091)

The rest of the nodes will be added via the REST API as described in the official documentation
[https://docs.couchbase.com/server/current/rest-api/rest-cluster-joinnode.html](https://docs.couchbase.com/server/current/rest-api/rest-cluster-joinnode.html)

We can add them all at once and then from the console from the first node we would accept

### Set environment varialbes
```
export cb_user=<Administrator user string>
export cb_password=<password String>
```

### The rest of the data nodes
```
curl -kv -X POST -u $cb_user:$cb_password \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb02.example.com' \
-d user=${cb_user} \
-d password=${cb_password} \
-d 'services=kv,n1ql'


curl -kv -X POST -u $cb_user:$cb_password \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb03.example.com' \
-d user=${cb_user} \
-d password=${cb_password} \
-d 'services=kv,n1ql'
```
#### INDEX NODES
```
curl -kv -X POST -u $cb_user:$cb_password \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-index01.example.com' \
-d user=${cb_user} \
-d password=${cb_password} \
-d 'services=index'


curl -kv -X POST -u $cb_user:$cb_password \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-index02.example.com' \
-d user=${cb_user} \
-d password=${cb_password} \
-d 'services=index'
```
#### Backup Node
```
curl -kv -X POST -u $cb_user:$cb_password \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-backup.example.com' \
-d user=${cb_user} \
-d password=${cb_password} \
-d 'services=backup'

```
#### Analytics Nodes
```
curl -kv -X POST -u $cb_user:$cb_password \
 https://cb-as01.example.com:18091/controller/addNode \
-d 'hostname=cb-analytics01.example.com' \
-d user=${cb_user} \
-d password=${cb_password} \
-d 'services=cbas'
```

```
curl -kv -X POST -u $cb_user:$cb_password \
 https://cb-index01.example.com:18091/controller/addNode \
-d 'hostname=cb-as02.example.com' \
-d user=${cb_user} \
-d password=${cb_password} \
-d 'services=cbas'
```

# Remove Couchbase
```
ansible-playbook -i inventory/hosts.conf  playbook-remove-couchbase-server.yml 
```
To remove only from selected nodes
```
ansible-playbook -i inventory/hosts.conf  playbook-remove-couchbase-server.yml --limit tf-cb-as*
```
