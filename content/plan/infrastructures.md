+++
date = "2017-04-18T13:36:57+02:00"
title = "Infrastructures"
toc = true
weight = 14

+++

The infrastructures hash holds information about cloud providers. Each entry in
an infrastructures hash describes a certain infrastructure or cloud if you want.
It is of hash type. Following is a list of keys:

 1. __*type*__ - is the type of the infrastructure provider. It is a reuired
	property. The infrastructure provider type can be specified by following values:
    - *baremetal*,
    - *ovirt* or *rhev*,
    - *openstack*,
    - *vsphere* or *vmware*.

    Please note that *rhev* and *ovirt* are synonyms and so are *vsphere* and *vmware*.

 2. __*endpoint*__ - is an URL that is an entry point for API calls.

    __IMPORTANT__: This property is required unless the provider type is *baremetal*.

 3. __*credentials*__ - A pointer to an entry in credentials hash. Please refer
    to credentials section above for further for further information.

    __IMPORTANT__: Currently, only one credentials provider is required and
    supported for infrastructure. The credentials must be of
    __*username_password*__ type.

    __IMPORTANT__: This property is required unless the provider type is *baremetal*.

 4. __*networks*__ - provides networks definition hashes. Each network
    definition is hashed by its name that can be an arbitrary string or symbol.
	Please refer to network subsection for further details.

	__IMPORTANT__: This property is required unless the provider type is *baremetal*.

 5. __*affinity_groups*__ - provides affinity groups definition hashes. Each
affinity group definition itself is a hash. Affinity groups may be provider
specific. For instance, OVirt/RHEVm infrastructure must define __*name*__,
__*cluster*__, __*positive*__ and __*enforced*__ properties. Please note
that some providers may not have affinities implemented, hence this feature
is optional in deployment plan.

The following snippet is an example infrastructures configuration:

    infrastructures:
      management:
        type: rhev
        endpoint: https://rhev.example.com/api/
        credentials: rhev_api_login
        networks:
          management:
            ip_pool:
              from: 192.168.254.11
              to: 192.168.254.245
            ip_netmask: 255.255.255.0
            ip_defgw: 192.168.254.254
        production:
            ip_pool:
              from: 192.168.1.11
              to: 192.168.1.245
            ip_netmask: 255.255.255.0
            ip_defgw: 192.168.1.254
        affinity_groups:
          clu-lab1ch-ag_1:
            positive: true
            enforce: true
            cluster: clu-lab1ch
          clu-lab1ch-ag_2:
            positive: true
            enforce: false
            cluster: clu-lab1ch
          clu-lab1ch-ag_3:
            positive: false
            enforce: true
            cluster: clu-lab1ch
      lamp:
        type: openstack
        endpoint: https://openstack.example.com/api/
        credentials: openstack_api_login
        networks:
          management-subnet:
            ip_pool:
              from: 192.168.253.11
              to: 192.168.253.245
            ip_netmask: 255.255.255.0
            ip_defgw: 192.168.253.254
          production-subnet:
            ip_pool:
              from: 192.168.2.11
              to: 192.168.2.245
            ip_netmask: 255.255.255.0
            ip_defgw: 192.168.2.254
      db:
        type: vsphere
        endpoint: https://vsphere.example.com/api/
        credentials: vsphere_api_login
        networks:
          management:
            ip_pool:
              from: 192.168.252.11
              to: 192.168.252.245
            ip_netmask: 255.255.255.0
            ip_defgw: 192.168.252.254
          production:
            ip_pool:
              from: 192.168.3.11
              to: 192.168.3.245
            ip_netmask: 255.255.255.0
            ip_defgw: 192.168.3.254

## Network

The network hash describes a particular network within a given infrastructure
provider (cloud). Following are the properties of a network hash:
 1. __*ip_pool*__ - a hash of IP addresses which can be assigned to guest VMs.
    The hash must contain __*ip_from*__ and __*ip_to*__ keywords that specify
    the lower and upper bounds of IP addresses that can be assigned statically.
 2. __*ip_netmask*__ - a network mask in octet format.
 3. __*ip_defgw*__ - an IP address of the default gateway of the network.

__NOTE__: One might also specify an empty hash `{}` as netwrok properties in
case of pure DHCP or NONE-based networks.

__IMPORTANT__: Please note that network names must refer to sub-network names
or their identifiers in case of [OpenStack](http://www.openstack.org/) based
infrastructures.


