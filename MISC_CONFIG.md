
ulimit -n 250000
 
```

https://docs.couchbase.com/server/current/install/install-swap-space.html
https://docs.couchbase.com/server/current/install/thp-disable.html
### Original Mount Point for Data Dir 
/opt/couchbase/var/lib/couchbase/data


### API Add node 
1. Initialize a node
```shell
curl -X POST \
  http://127.0.0.1:8091/nodes/self/controller/settings \
  -d 'data_path=%2Fopt%2Fdata&' \
  -d 'index_path=%2Fopt%2Fdata&' \
  -d 'cbas_path=%2Fopt%2Fdata&' \
  -d 'eventing_path=%2Fopt%2Fdata&'
  
  %2Fopt%2Fcouchbase%2Fvar%2Flib%2Fcouchbase%2Fdata&
 ``` 
  1. Init node with the CLI
```shell
/opt/couchbase/bin/couchbase-cli node-init -c 127.0.0.1 --node-init-hostname cb-index02.example.com \
--ec2-user Administrator --password passwordString \
   --node-init-data-path /opt/data \
   --node-init-index-path /opt/data \
   --node-init-analytics-path /opt/data 

``` 
2. Establish Credentials
```shell
curl -X POST http://127.0.0.1:8091/settings/web \
-d 'password=passwordString&ec2-user=Administrator&port=SAME'
```
3. Name the node
```shell
curl -X POST -u Administrator:passwordString \
 http://10.0.1.57:8091/node/controller/rename \
-d hostname=cb-backup.example.com
```
4. Add node to cluster
```shell
curl -kv -X POST -u Administrator:passwordString \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-index01.example.com' \
-d 'user=Administrator' \
-d 'password=passwordString' \
-d 'services=index'
```
curl -kv -X POST -u Administrator:passwordString \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-index02.example.com' \
-d 'user=Administrator' \
-d 'password=passwordString' \
-d 'services=index'


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

-d 'services=kv,n1ql,index'
backup
[https://docs.couchbase.com/server/current/rest-api/rest-cluster-joinnode.html](https://docs.couchbase.com/server/current/rest-api/rest-cluster-joinnode.html)
```shell
curl -u Administrator:passwordString -d 'clusterMemberHostIp=192.168.0.1' \
  -d 'clusterMemberPort=8091' \
  -d 'user=admin' -d 'password=password' \
  http://10.0.1.57:8091/node/controller/doJoinCluster
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

# INDEX NODES
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

# Backup Node

curl -kv -X POST -u Administrator:passwordString \
https://cb01.example.com:18091/controller/addNode \
-d 'hostname=cb-backup.example.com' \
-d 'user=Administrator' \
-d 'password=passwordString' \
-d 'services=backup'

# Restore
```

# /opt/couchbase/bin/cbbackupmgr info  -a s3://mrf-cb-backup/snapshots/ --all --obj-staging-dir /mnt/data/ --obj-region us-west-2 --obj-auth-by-instance-metadata
Name      | UUID                                 | Size     | # Repos |
snapshots | 6ccc2bb2-fc2a-495d-a715-c906e18ab507 | 19.87GiB | 2       |

*  Name                                 | Size | # Backups | Encrypted |
*  afeef5ef-73b4-46e9-97c4-a64a37b2e7c1 | 0B   | 0         | false     |

*  Name                                 | Size     | # Backups | Encrypted |
*  d016bc73-bff7-4d87-bf27-0754f5247def | 19.87GiB | 1         | false     |

+    Backup                         | Size     | Type | Source                          | Cluster UUID                     | Range | Events | Aliases | Complete |
+    2023-01-07T21_56_44.817519988Z | 19.87GiB | FULL | http://cb-backup.example.com:8091 | 3c0354ab217b0d53e30ef5f3c6b48f19 | N/A   | 0      | 0       | true     |

-      Bucket    | Size     | Items   | Mutations | Tombstones | Views | FTS | Indexes | CBAS |
-      pt_bucket | 19.87GiB | 7792801 | 7792801   | 0          | 0     | 0   | 12      | 0    |

~        Scope    | Scope ID | Mutations | Tombstones |
~        _default | 0        | 0         | 0          |

#          Collection | Collection ID | Mutations | Tombstones |
#          _default   | 0             | 0         | 0          |

~        Scope | Scope ID | Mutations | Tombstones |
~        provider    | 8        | 199958    | 0          |

#          Collection          | Collection ID | Mutations | Tombstones |
#          in_network          | 10            | 89447     | 0          |
#          provider_references | 11            | 110511    | 0          |

~        Scope    | Scope ID | Mutations | Tombstones |
~        provider | 9        | 7592843   | 0          |

#          Collection | Collection ID | Mutations | Tombstones |
#          nppes      | 12            | 7592843   | 0          |
 
```
Restore
```shell
/opt/couchbase/bin/cbbackupmgr restore -c  $cb_connection_string -u $cb_user -p $cb_password --no-ssl-verify   -a s3://mrf-cb-backup/snapshots/ --obj-staging-dir /mnt/data/ --obj-region us-west-2 --obj-auth-by-instance-metadata  -r d016bc73-bff7-4d87-bf27-0754f5247def --purge

```

# Restore 
Create the bucket. Do not create the scopes and collections.
```shell
export cb_connection_string="https://cb03.example.com:18091,cb01.example.com:18091,cb02.example.com:18091"
export cb_user=Administrator
export cb_password=<passwordString>
export bucket=pt_bucket
```
2. Get the snapshot id that we need to restore
```shell 
opt/couchbase/bin/cbbackupmgr info  -a s3://mrf-cb-backup/snapshots/ --all --obj-staging-dir /mnt/data/ --obj-region us-west-2 --obj-auth-by-instance-metadata
``` 
Set the restore ID environment variable
```shell
export cb_restore_name=1dd222ee-8f90-4266-9612-815d52bb2760
```

3. Restore
```shell
/opt/couchbase/bin/cbbackupmgr restore -c  $cb_connection_string -u $cb_user -p $cb_password --no-ssl-verify   -a s3://mrf-cb-backup/snapshots/ --obj-staging-dir /mnt/data/ --obj-region us-west-2 --obj-auth-by-instance-metadata  -r  $cb_restore_name --purge
```


/mnt/data/pt-snapshots-repo-1dd222ee-8f90-4266-9612-815d52bb2760/  
