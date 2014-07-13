# vagrant-nova-client

This is a simple vagrant boy with a nova lient insatllation on ubuntu

The novaclient a python tool to use rackspace

## rackspace glossar

* API key := used for api access, can be found under Account Settings
* tenantID := can be found under account
* flavor := A resource configuration for a server. Each flavor is a unique combination of disk, memory, vCPUs, and network bandwidth, see http://docs.rackspace.com/servers/api/v2/cs-devguide/content/Flavors-d1e4180.html  
* network := The virtual space where your servers live. Rackspace has two default networks: PublicNet, which is the Internet; ServiceNet, which is rackspaces internal network.

## rackspace API

Of course, rackspace has an webfrontend, but normally i would prefer a command line interface.
For automation this is the only valid option.

Rackspace support a rest api (curl), a java api, ... and the nova client
See http://docs.rackspace.com/servers/api/v2/cs-devguide/content/ch_preface.html

### rest API (e.g. use curl)

I will not explain the rest api, but all commands and action can be executed by rest calls e.g. for use by a shell script
This is an example:

    curl -s https://identity.api.rackspacecloud.com/v2.0/tokens -X 'POST' -d '{"auth":{"RAX-KSKEY:apiKeyCredentials":{"username":"YOUR-NAME", "apiKey":"YOUR-API-KEY"}}}' -H "Content-Type: application/json" | python -m json.tool

### nova client

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
 
