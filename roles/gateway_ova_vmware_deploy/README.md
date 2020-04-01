Deploy
======================

This role will take the vmware template of your choosing and start it up in the APIM vCenter.

Requirements
------------

The only requirement for this role is that you specify one of the pre-existing VMware templates or one that you have created.

Role Variables
--------------

govc_version_url - URL to the latest version of govc deployed in an agent.
vcenter_ip - The IP address for APIM vCenter.
vcenter_username - The default username used to authenticate to vCenter.
vcenter_password - The authentication password.
newvm.instance.ipv4 - This is the IP address discovered via either DHCP or when a static is specified. It is then kept as an in memory variable to be passed along to the next role.

Dependencies
------------

There are no depencies upon other roles.