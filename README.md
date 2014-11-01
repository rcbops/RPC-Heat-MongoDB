Description
===========

This is an OpenStack HEAT template to deploy [MongoDB](http://www.mongodb.org/) 
to multiple servers in an OpenStack cloud. 

This template uses [MongoDB salt-formulas](https://github.com/eglute/mongodb-formula) 
to configure the servers. It deploys a salt-master, and a number of salt-minions. 
Three minions will be Mongo configuration servers, at least one mongo routing (mongos) 
server, and we recommend at least 3 mongodb shards.

For access to nodes in the MongoDB cluster, a floating ip will be assigned to the 
salt-master. Or a new server can be created in the same network as the MongoDB network.
Additionally, mongos server will also be on a separete network so that applications could 
access your database cluster without being on the same network.

Any changes to the MongoDB configuration can be done using Salt pillars on the Salt 
master. Adding additional mongos nodes can be done via Salt as well.

Requirements
============
* A Heat provider that supports the following:
  * OS::Neutron::Net
  * OS::Neutron::Subnet
  * OS::Neutron::Router
  * OS::Neutron::RouterInterface
  * OS::Neutron::FloatingIP
  * OS::Neutron::FloatingIPAssociation
  * OS::Neutron::Port
  * OS::Heat::SoftwareConfig
  * OS::Heat::SoftwareDeployment
  * OS::Heat::RandomString
  * OS::Heat::ResourceGroup
  * OS::Nova::Server
  * OS::Nova::KeyPair

* A separete application network where mongos node will be connected.
* Injected files limit in nova should be set to 10 or more:
  nova quota-class-update --injected_files 10 default
* Port 27017 should be accesible 
* An Ubuntu image (12.04 or newer) preconfigured with heat-cfntools and heat confog-script. 
Instructions for creating a heat-cfntools enabled image for use with Heat can be 
found [here] (http://docs.openstack.org/developer/heat/getting_started/jeos_building.html).

* An OpenStack username, password, and tenant id.
* [python-heatclient](https://github.com/openstack/python-heatclient)
`>= v0.2.12`:

```bash
pip install python-heatclient
```
Using Heat-client 
=============
Here is an example of how to deploy this template using the
[python-heatclient](https://github.com/openstack/python-heatclient):

```
heat stack-create hadoop-stack -f mongodb_stack.yaml \
  -e env.yaml -P flavor=m1.large;floating-network-id=<NET_ID>; \
   < ... > ;image=<IMAGE_ID>
```
All the required parameters are as follows:
```

    "minion-count-mongos": How many mongos servers will be required
    "apps-network": Network UUID of application network
    "image": Image name
    "floating-network-id": UUID of the external network. The private network created by this stack will route to this network. Also, any floating ip's needed by this stack will come this network.
    "keyname": Key name for loggin in to instances
    "minion-size-mongodb": Flavor (image size) of mongod servers to be created.
    "minion-count-mongodb": How many mongod servers will be in a cluster.

```

Using Salt
=============
If you wish to make changes to your environment after all the nodes were built, 
you may re-run salt states as follows:

```
salt -G 'roles:mongoconfigsrv' state.highstate
salt -G 'roles:mongodb' state.highstate
salt -G 'roles:mongos' state.highstate
salt -G 'roles:mongodb' state.sls mongodb.replication
```
The roles work as follows: 
"mongoconfigsrv" is a role for mongo configuration servers.
"mongodb" is a role for mongodb (shards) servers.
"mongos" is a role for mongos (router) servers.

Using Horizon
=============
You can also go to your Horizon Dashboard in your browser and create 
the MongoDB stack from under the Orchestration tab.

License
=======
```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
