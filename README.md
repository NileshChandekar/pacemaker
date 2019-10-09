
# Pacemaker pcs  command reference

### pcs is a command line tool to manage pacemaker/cman based High availability cluster, here are some of mostly being used commands for cluster management
	


#### Display the current status

	# pcs status

		[root@overcloud-controller-0 ~]# pcs status
		Cluster name: tripleo_cluster
		Stack: corosync
		Current DC: overcloud-controller-2 (version 1.1.15-11.el7_3.2-e174ec8) - partition with quorum
		Last updated: Thu Feb  8 07:06:29 2018		Last change: Tue Feb  6 09:18:52 2018 by root via cibadmin on overcloud-controller-0

		3 nodes and 19 resources configured

		Online: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]

		Full list of resources:

		 ip-192.168.125.21	(ocf::heartbeat:IPaddr2):	Started overcloud-controller-0
		 Clone Set: haproxy-clone [haproxy]
		     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]
		 ip-192.0.2.8	(ocf::heartbeat:IPaddr2):	Started overcloud-controller-1
		 Master/Slave Set: galera-master [galera]
		     Masters: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]
		 ip-192.168.124.20	(ocf::heartbeat:IPaddr2):	Started overcloud-controller-2
		 Clone Set: rabbitmq-clone [rabbitmq]
		     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]
		 Master/Slave Set: redis-master [redis]
		     Masters: [ overcloud-controller-2 ]
		     Slaves: [ overcloud-controller-0 overcloud-controller-1 ]
		 ip-10.11.48.182	(ocf::heartbeat:IPaddr2):	Started overcloud-controller-0
		 ip-192.168.128.30	(ocf::heartbeat:IPaddr2):	Started overcloud-controller-1
		 ip-192.168.124.24	(ocf::heartbeat:IPaddr2):	Started overcloud-controller-2
		 openstack-cinder-volume	(systemd:openstack-cinder-volume):	Started overcloud-controller-0

		Daemon Status:
		  corosync: active/enabled
		  pacemaker: active/enabled
		  pcsd: active/enabled
		[root@overcloud-controller-0 ~]# 



#### Display the current cluster status

	# pcs cluster status

		[root@overcloud-controller-0 ~]# pcs cluster status
		Cluster Status:
		 Stack: corosync
		 Current DC: overcloud-controller-2 (version 1.1.15-11.el7_3.2-e174ec8) - partition with quorum
		 Last updated: Thu Feb  8 07:06:53 2018		Last change: Tue Feb  6 09:18:52 2018 by root via cibadmin on overcloud-controller-0
		 3 nodes and 19 resources configured

		PCSD Status:
		  overcloud-controller-0: Online
		  overcloud-controller-2: Online
		  overcloud-controller-1: Online
		[root@overcloud-controller-0 ~]# 


#### Create a cluster

	# pcs cluster setup [--start] [--local] --name cluster_ name node1 [node2] [...]

#### Start the cluster

	# pcs cluster start [--all] [node] [...]

#### Stop the cluster

	# pcs cluster stop [--all] [node] [...]

#### Forcebly stop cluster service on a node

	# pcs cluster kill

#### Enable cluster service on node[s]

	# pcs cluster enable [--all] [node] [...]

#### Disable cluster service on node[s]

	# pcs cluster disable [--all] [node] [...]

#### Put node in standby

	# pcs cluster standby <node-name>

#### Remove node from standby

	# pcs cluster unstandby <node-name>

#### Set cluster property

	# pcs property set stonith-enabled=false

#### Destroy/remove cluster configuration on a node

	# pcs cluster destroy [--all] 

#### Cluster node authentication

	# pcs cluster auth [node] [...]

#### Add a node to cluster

	# pcs cluster node add [node]

#### Remove a node to cluster

	# pcs cluster node remove [node]

#### Resource manipulation 

##### List Resource Agent (RA) classes

	# pcs resource standards

##### List available RAs

	# pcs resource agents ocf
	# pcs resource agents lsb
	# pcs resource agents service
	# pcs resource agents stonith
	# pcs resource agents

##### Filter by provider

	# pcs resource agents ocf:pacemaker

##### List RA info

	# pcs resource describe RA
	# pcs resource describe ocf:heartbeat:RA

#### Create a resource // params ip=192.168.122.120 cidr_netmask=32 op monitor interval=30s

	# pcs resource create ClusterIP IPaddr2 ip=192.168.0.120 cidr_netmask=32

### The standard and provider (ocf:heartbeat) are determined automatically since IPaddr2 is unique. The monitor operation is automatically created based on the agent's metadata.

#### Delete a resource

	# pcs resource delete resourceid

#### Display a resource

	# pcs resource show
	# pcs resource show ClusterIP

#### Display fencing resources

	# pcs stonith show

### pcs treats STONITH devices separately.

#### Display Stonith RA info

	# pcs stonith describe fence_ipmilan

#### Start a resource

	# pcs resource enable ClusterIP

#### Stop a resource

	# pcs resource disable ClusterIP

#### Remove a resource

	# pcs resource delete ClusterIP

#### Modify a resource

	# pcs resource update ClusterIP clusterip_hash=sourceip

#### Delete parameters for a given resource

	# pcs resource update ClusterIP ip=192.168.0.98

#### List the current resource defaults

	# pcs resource rsc default

#### Set resource defaults

	# pcs resource rsc defaults resource-stickiness=100

#### List the current operation defaults

	# pcs resource op defaults

#### Set operation defaults

	# pcs resource op defaults timeout=240s

#### Set Colocation

	# pcs constraint colocation add ClusterIP with WebSite INFINITY

### With roles

	# pcs constraint colocation add Started AnotherIP with Master WebSite INFINITY

#### Set ordering

	# pcs constraint order ClusterIP then WebSite

### With roles:

	# pcs constraint order promote WebSite then start AnotherIP

#### Set preferred location

	# pcs constraint location WebSite prefers pcmk-1=50

### With roles:

	# pcs constraint location WebSite rule role=master 50 \#uname eq pcmk-1

#### Move resources

	# pcs resource move WebSite pcmk-1
	# pcs resource clear WebSite

#### A resource can also be moved away from a given node:

	# pcs resource ban Website pcmk-2

### Moving a resource sets a stickyness to -INF to a given node until unmoved Also, pcs deals with constraints differently. These can be manipulated by the command above as well as the following and others

	# pcs constraint list --full
	# pcs constraint remove cli-ban-Website-on-pcmk-1

#### Set a resource failure threshold

	# pcs resource meta RA migration-threshold=3

#### Move default resource failure threshold

	# pcs resource meta default migration-threshold=3

#### Show a resource failure count

	# pcs resource failcount show RA

#### Reset a resource failure count

	# pcs resource failcount reset RA

#### Create a clone

	# pcs resource clone ClusterIP globally-unique=true clone-max=2 clone-node-max=2

#### Create a master/slave clone // meta master-max=1 master-node-max=1 clone-max=2 clone-node-max=1 notify=true

	# resource master WebDataClone WebData master-max=1 master-node-max=1 clone-max=2 clone-node-max=1 notify=true

#### To manage a resource

	# pcs resource manage RA

#### To UNmanage a resource

	# pcs resource unmanage RA

### STONITH

#### List available resource agents

	# pcs stonith list

#### Add a filter to List available resource agents

	# pcs stonith list <string>

#### Setup properties for STONITH

	# pcs property set no-quorum-policy=ignore
	# pcs property set stonith-action=poweroff     # default is reboot

#### Create a fencing device

	# pcs stonith create stonith-rsa-nodeA fence_rsa action=off ipaddr="nodeA_rsa" login=<user> passwd=<pass> pcmk_host_list=nodeA secure=true

#### Display fencing devices

	# pcs stonith show

#### Fence a node off

	# pcs stonith fence <node>

#### Modify a fencing device

	# pcs stonith update stonithid [options]

#### Display a fencing device options

	# pcs stonith describe <stonith_ra>

#### Deleting a fencing device

	# pcs stonith delete stonithid

#### Config a fencing device level

	# pcs stonith level add level node devices

### Other operations

#### Batch changes

	# pcs cluster cib drbd_cfg
	# pcs -f drbd_cfg resource create WebData ocf:linbit:drbd drbd_resource=wwwdata \
	        op monitor interval=60s
	# pcs -f drbd_cfg resource master WebDataClone WebData master-max=1 master-node-max=1 \
	        clone-max=2 clone-node-max=1 notify=true
	# pcs cluster push cib drbd_cfg
	
#### Backup cluster configuration

	# pcs config backup <tar archive>
	
#### Restore cluster using the backup

	# pcs cluster stop [--all]
	# pcs config restore <tar archive>
	# pcs cluster start [--all]

### Check if a node is still fenced

    # attrd_updater --query --all --name=evacuate

### Unset fence flag for a compute node

    # attrd_updater --name=evacuate --update=no --node=<FAILED_HOST>
