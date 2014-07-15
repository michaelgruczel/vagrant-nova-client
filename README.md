# vagrant-nova-client

This is a simple vagrant boy with a nova lient insatllation on ubuntu

The novaclient a python tool to use rackspace

## rackspace glossar

* API key := used for api access, can be found under Account Settings
* tenantID := can be found under account
* flavor := A resource configuration for a server. Each flavor is a unique combination of disk, memory, vCPUs, and network bandwidth, see http://docs.rackspace.com/servers/api/v2/cs-devguide/content/Flavors-d1e4180.html  
* network := The virtual space where your servers live. Rackspace has two default networks: PublicNet, which is the Internet; ServiceNet, which is rackspaces internal network.

## rackspace APIs

Of course, rackspace has an webfrontend, but normally i would prefer a command line interface.
For automation this is the only valid option.

Rackspace has a rest api (curl) for everything, several SDKs (java, python,...) and some client (e.g. nova client) for some services
See https://developer.rackspace.com/sdks/
See http://docs.rackspace.com/servers/api/v2/cs-devguide/content/ch_preface.html

I will not explain the rest api in all cases, but all commands and actions can be executed by rest calls e.g. for use by a shell script
This is an example:

    curl -s https://identity.api.rackspacecloud.com/v2.0/tokens -X 'POST' -d '{"auth":{"RAX-KSKEY:apiKeyCredentials":{"username":"YOUR-NAME", "apiKey":"YOUR-API-KEY"}}}' -H "Content-Type: application/json" | python -m json.tool

### create server by nova client

This is also possible by curl

http://docs.rackspace.com/servers/api/v2/cs-gettingstarted/content/section_gs_install_nova.html

For an easy usage if prepared a vagrant box see. Log in by vagrant ssh and then:

#### setup

    # setup of environment
    export OS_AUTH_URL=https://lon.identity.api.rackspacecloud.com/v2.0/
    export OS_AUTH_SYSTEM=rackspace_uk
    export OS_REGION_NAME=LON
    export OS_USERNAME=<username>
    export OS_TENANT_NAME=<tenant_id>
    export NOVA_RAX_AUTH=1
    export OS_PASSWORD=<apikey>
    export OS_PROJECT_ID=<tenant_id>
    export OS_NO_CACHE=1

#### ceate and delete a server

    # show images
    nova image-list

    # show flavors
    nova flavor-list
    
    #start a server (take image hash from image-list call)
    nova boot myUbuntuServer --image "5cc098a5-7286-4b96-b3a2-49f4c4f82537" --flavor 2

    # check server with the id (see boot result) e.g. 
    nova show ce427fe7-5c3c-4283-9d9b-e8a5fbb4234f

    # show running servers
    nova list
    
    #login e.g. passwort is DRrsn5oqeScS (see boot result)
    
    # change the server size at run time see http://docs.rackspace.com/servers/api/v2/cs-gettingstarted/content/manage_server.html
    
    # delete server again
    nova delete ce427fe7-5c3c-4283-9d9b-e8a5fbb4234f
 
### create Loadbalancer

I will assume you have already at least 2 servers, in this example I will create a LB with one node and add a second node

    curl -s -d \
    '{
        "loadBalancer": {
            "name": "a-new-loadbalancer",
            "port": 80,
            "protocol": "HTTP",
            "virtualIps": [
                {
                    "type": "PUBLIC"
                }
            ],
            "nodes": [
                {
                    "address": "<IP address of FIRST cloud server>",
                    "port": 80,
                    "condition": "ENABLED"
                }
            ] 
        }
    }' \
    -H 'X-Auth-Token: your_auth_token' \
    -H 'Content-Type: application/json' \
    'https://dfw.loadbalancers.api.rackspacecloud.com/v1.0/your_acct_id/loadbalancers' | python -m json.tool

    curl -s -d \
    '{
        "nodes": [
            {
                 "address": "<;IP address of SECOND cloud server>",
                 "port": 80,
                 "condition": "ENABLED"
            }
        ]
    }' \
    -H 'X-Auth-Token: your_auth_token' \
    -H 'Content-Type: application/json' \
    'https://dfw.loadbalancers.api.rackspacecloud.com/v1.0/your_acct_id/loadbalancers/load_balancer_id/nodes' | python -m json.tool

    curl -s  \
    -H 'X-Auth-Token: your_auth_token'  \
    -H 'Accept: application/json'  \
    'https://dfw.loadbalancers.api.rackspacecloud.com/v1.0/your_acct_id/loadbalancers/load_balancer_id' | python -m json.tool

The default config for the LB is random, (WEIGHTED-) LEAST_CONNECTIONS, (WEIGHTED-) ROUND_ROBIN, is also possible.
Monoring checks forthe nodes, ssl termination, ... is also possible.
check http://docs.rackspace.com/loadbalancers/api/v1.0/clb-getting-started/content/DB_Overview.html and http://docs.rackspace.com/loadbalancers/api/v1.0/clb-devguide/content/Algorithms-d1e4367.html for more details
I've found not way to do this with the nova-client.

### database, cdn, ...

For all services rest calls are possible, for some things clients are available.
I will not explain the other functionalities of Rackspace, only some buzzwords:

* Rackspace support mysql db.
* cloud storage
* creation volumes, adding them to servers, make snapshots,... means Blockstorage
* Autoscaling, means you can add servers at a given time or based on alerts
* monitoring: more or less ping services which can trigger mails
* Backups
* Networks
* DNS
* ...

