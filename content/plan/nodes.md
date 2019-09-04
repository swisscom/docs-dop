+++
date = "2017-04-18T13:37:04+02:00"
title = "Nodes"
toc = true
weight = 16

+++

The nodes hash holds the basic information about all nodes you want to create
and use. Each entry in the nodes hash is itself a hash. A single entry of this
is called a node hash (singular). Each node hash starts with the node name as a
key:

    nodes:
        mysql01.example.com:
          ...
          ...
          ...

__IMPORTANT__: The node name must be unique for each deployment. Please keep
this in mind when combining several deployments into a single deployment file.

## Node Properties
Each node configuration is described by a so-called *"node hash"*. The list
bellow provides an overview of various node properties. Please note that
a property name is actually a keyword of a node hash.

 1. __*fqdn*__ - an optional fully qualified domain name that is used to
    generate the hostname of the guest. If not defined, the hostname is implicitly
    derived from the node name itself (for instance, in case of
    `mgt01.example.com`, the hostname definition would match the node name, i.e.
    `mgt01.example.com`).

 2. __*infrastructure*__ - a name of the  infrastructure this node is a part of.
    This is a required property and its value must point to a valid entry of
    infrastructures hash.

 3. __*infrastructure_properties*__ - a hash that specifies various properties
    of a given node should be deployed with in an infrastructure.

    1. __*affinity_groups*__ - an optional OVirt/RHEVm-specific property that
       designates what affinity groups should be a node associated with.

    2.  __*keep_ha*__ - an optional OVirt/RHEVm-specific boolean property that
        indicates whether the VM should be highly available or not. By default,
        instances are set as highly available. If the provider also supports a
        migration priorities they are set to low by default.

    3. __*datacenter*__ and __*cluster*__ - Specify which datacenter and cluster
       should be the node deployed into.

       __IMPORTANT__: These properties are required for provider OVirt/RHEVm and
       VSphere providers. Other providers silently disregard it.

    4. __*default_pool*__ - property specifies the default data storage which is
       used when deploying a guest from the template. It is also used for persistent
       disks that do not specify an explicit __*pool*__. This attribute is optional.

    5. __*dest_folder*__ property defines a destination folder into which the
       guest shall be deployed. This folder must exist before the deployment
       of the guest. This is propery is optional and VSphere-specific.

    6. __*tenant*__ property specifies the name of the tenant for OpenStack
       infrastructures. It is required for OpenStack infrastructures.

	7. __*use_config_drive*__ an optional OpenStack-specific boolean property
       that specifies whether a config drive should be used for VM
       provisioning. If set to `false` or indefined, metadata service is
       used. If set to `true` config drive is used.

	8. __*domain_id*__ an optional property specifies the name of the domain ID
       for OpenStack infrastructures. It defaults to `default`.

	9. __*endpoint_type*__ an optional property specifies the endpoint type
       for OpenStack infrastructures. Accepted values are `publicURL`,
       `internalURL` and `adminURL`. It defaults to `publicURL`.

    __IMPORTANT__: Infrastructure properties may differ across different
    provider types.

    __IMPORTANT__: In general, some of infrastructure properties have to be
    defined for each provider other than *baremetal*.

 4. __*image*__ - an image to deploy the node from (a.k.a template). An image
    must be registered within the provider.

	__IMPORTANT__: This property is required unless the provider type is
	*baremetal*.

 5. __*full_clone*__ - an optional boolean property that instructs OVirt/RHEVm
	providers:
    1. To provision a node from a template as a full independent clone if set to
       `true` or unset.

    2. To provision a node from a template as thin (dependent) clone if set to
       `false`.

    The default is to provision a fully independent clone.

    __IMPORTANT__: Do not use this property for other cloud provider types than
    OVirt/RHEVm.

 6. __*interfaces*__ - network interface hash cards specification. Each NIC is
    hashed by its name (for instance, *eth0*, *eth1*, etc).

    __IMPORTANT__: For Linux guests, the NIC name defined in a plan should
	correspond to its logical name in the guest OS.

	__NOTE__: NICs are ordered and configured within the the guest OS in the
	order they were defined in the plan.

	__IMPORTANT__: This property is required unless the provider type is
	*baremetal*.

    Following is a list of properties that descirbe a network interface card:
    1. __*network*__ - name of the network the NIC belongs to. The network
       must exist in infrastructures' networks hash.

       __IMPORTANT:__ In case of OpenStack providers, the network name must
       point to valid subnet rather than a network name.

    2. __*ip*__ - a property that defines an IP address. Following values are
       permitted:
       - a properly formatted string witn an IP in case of static,
       - a *dhcp* literal in case the IP should by assigned by DHCP,
       - *none* literal in case no IP adress should be set for a given interface.

    3. __*set_gateway*__ - an optional boolean property that defines, whether a
       gateway should be defined for a given interface during guest customization.
       It is `true` by default.

    4. __*virtual_switch*__ - an optional (currently VSphere-specific) property
       that specifies which distributed virtual switch should be used.

    5. __*floating_network*__ - an optional OpenStack specific property that
       specifies the network from which is the __floating__ IP provisioned and
       associated with the interface.

 7. __*disks*__ - an optional property to define additional disks that should
    persist accross deployments. It is of hash type. The key represents a disk
	name. A persistant disk itself is described by a so-called *"disk hash"*
	with following keywords:
    1. __*pool*__ - the name of the storage pool that should be used as a backing
       store for a disk. It is required for OVirt/RHEVm and VSphere providers,
       unless the __*default_pool*__ is specified in
       __*infrastructure_properties*__.
    2. __*size*__ - the size of the disk in megabytes (when the value has a
       suffix *M*) or gigabytes (when the value has a suffix *G*).
    3. __*thin*__ - an optional boolean flag that indicates whether the disk will be
       thin provisioned. Its default  value is *true*, meaning the disks are
       thin-provisioned by default. Please use *false* as the value if you need to
       thick provision a disk.

       __IMPORTANT:__ Currently, the selection of provisioning type is honored
       only by OVirt/RHEVm provider. A thick-provisioned disk in OVirt/RHEVm
       provider is of __raw__ rather than __cow__ type. As a consequence,
       it is not possible to create a snapshot of such a disk.*

 8. __*credentials*__ - an optional property to define credentials for
    administrator user (root, Administrator). This information is passed to
	the customization tool (cloud-init, VSphere-based windows-guests customization, etc.). Following
	data can be specified:
    * __*root_password*__ - super user password that is set for cloud init phase (IMPORTANT: not supported in VSphere).
    * __*root_ssh_keys*__ - an array of OpenSSH public keys that are recorded
      into `/root/.ssh/authorized_keys` by cloud init.
    * __*administrator_password*__ - an optional property that specifies the
      password of the administrator user for VSphere-based windows-guests
      customization. it defaults to an empty password which in turn leads to an
      automatic logon upon windows guest startup.

 9. __*flavor*__ - an optional property that specifies how to set the amount of
     CPU cores, memory and the size of the *root* disk. Please consult
     [OpenStack
     flavors](http://docs.openstack.org/openstack-ops/content/flavors.html) for
     their definition. In case the infrastructure does not support flavors feature,
     it is emulated.

     __IMPORTANT:__ The __flavor__ property is mutually exclusive with any of
     __cores__, __memory__ or __storage__ properties. Having said that, an
     exception will logged if __flavor__ is specified along with one or more
     of __cores__, __memory__ and/or __storage__ properties.

 10. __*cores*__ - an optional positive integer that sets the number of cores
    for a given node. It is `2` by default.

 11. __*memory*__ - an optional value that is used to set the amount of
     provisioned memory for a given node. The default is `4G`.

     The format of an input value is a positive number followed by one of:
     * `K` for kibibytes,
     * `M` for mebibytes,
     * `G` for gibibytes,
     * `KB` for kilobytes,
     * `MB` for megabytes,
     * `GB` for gigabytes.

 12. __*storage*__ - an that specifies the size of the root volume. The
     default value is `10G`.

     Please note that some infrastructure providers disregard this value,
     especially when the node is provisioned from a template.

     Please have a look to __*memory*__ for formatting of input value.


 13. __*timezone*__ - a property that specifies the timezone of the guest
     operating system. Please make sure that:
      * [following values are
        used](https://www.vmware.com/support/developer/windowstoolkit/wintk40u1/html/New-OSCustomizationSpec.html)
        for VSphere-based windows guests customization,
      * for Linux guests, values specified in a __`TZ`__ column of the [list of
        tz databaze time zones](http://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
        are used.

     __IMPORTANT:__ This property is required when customizing guest on *VSPhere* provider.

 14. __*product_id*__ - an optional, VSphere-specific windows-only guest customization
     property that specifies a serial number. Its default value is
     undefined which leaves the guest OS in an evaluation/trial mode.

 15. __*organization_name*__ - a required, VSphere-specific windows-only guest
     customization property that specifies the organization name of the
     administrator user.

 16. __*dns*__ - an optional property that specifies name servers and search
     domains for further node customization. If specified, it has to be a hash
     with any of the following items:
     1. __*name_servers*__ - a list of valid IP addresses.
     2. __*search_domains*__ - a list of valid domains.

 17. __*thin_clone*__ - an optional, VSphere-specific boolean property that instructs the VSphere provider:
     * `true`  converts every disk, that is inherited from template, to thin provision (sparse).
     * `false` converts every disk, that is inherited from template, to thick provision (flat).

     __IMPORTANT__: The default (unset) converts every disk, that is inherited from template, to thin provision (sparse).

     __NOTE__: Do not use this property for other cloud provider types than VSphere.

 18. __*tags*__ - a optional, VSphere-specific, property that instructs the VSphere provider
     to associate the given node with the listed tags

     __IMPORTANT__: Do not use this property for other cloud provider types than VSphere. Requires minimum version 6.0.0 of VSphere. Raises an warning if the tag does not exist.

 19. __*force_stop*__ - an optional boolean property to force shutdown e.g. during undeploying.
     It is `false` by default (graceful shutdown).


The example bellow shows a specification for a database backend and a web node:

    nodes:
      mgt01.example.com:
        infrastructure: management
        infrastructure_properties:
          affinity_groups:
            - clu-lab1ch-ag_1
            - clu-lab1ch-ag_3
          keep_ha: false
          datacenter: lab1ch
          cluster: clu-lab1ch
          default_pool: ssd_pool1
          full_clone: false
        image: rhel6cloudinit
        interfaces:
          eth0:
            network: management
              ip: dhcp
        credentials:
          - root_password
          - root_ssh_pubkey

      mssql01_mgt01:
        fqdn: mssql01.example.com
        infrastructure: management
        infrastructure_properties:
          datacenter: lab1ch
          cluster: clu-lab1ch
          keep_ha: true
        image: win12r1_64
        cores: 6
        memory: 64G
        storage: 128G
        interfaces:
          eth0:
            network: management
            ip: 192.168.254.13
        disks:
          db1:
            pool: storage_pool3
            size: 256G
        thin: false
        credentials:
          - admin_password

      mysql01.example.com:
        infrastructure: lamp
        infrastructure_properties:
          tenant: lamp01
        image: rhel6cloudinit
        flavor: medium
        interfaces:
          eth0:
            network: management-subnet
            ip: 192.168.253.25
          eth1:
            network: production-subnet
            ip: 192.168.2.102
        floating_network: ext-net0
        disks:
          rdo:
            pool: storage_pool1
            size: 4000M
        thin: false
          db1:
            size: 20G

      mssql01.example.com:
        infrastructure: db
        infrastructure_properties:
          dest_folder: sql
          datacenter: dc01
          cluster: cl01
          default_pool: sql_pool
        image: w12r2
        flavor: medium
        interfaces:
          eth0:
            network: management
            ip: 192.168.252.33
        set_gateway: false
          eth1:
            network: db
            ip: 192.168.3.109
        disks:
          rdo:
            pool: storage_pool3
            size: 4000M
        thin: false
          db1:
            size: 20G
        credentials:
          - admin_password
      timezone: 100
        organization_name: Acme

      web01.example.com:
        infrastructure: lamp
        infrastructure_properties:
          tenant: lamp01
        datacenter: lab1ch
        cluster: clu-lab1ch
        image: rhel6
        flavor: small
        interfaces:
          eth0:
            network: management-subnet
            ip: 192.168.253.26
          eth1:
            network: production-subnet
            ip: dhcp
        floating_network: ext-net0

