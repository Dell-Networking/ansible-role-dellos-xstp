# xSTP role

This role facilitates the configuration of xSTP attributes. It supports multiple version of spanning-tree protocol (STP), rapid spanning-tree (RSTP), rapid per-VLAN spanning-tree (Rapid PVST+), multiple spanning-tree (MST), and per-VLAN spanning-tree (PVST). It supports the configuration of bridge priority, enabling and disabling spanning-tree, creating and deleting instances, and mapping virtual LAN (VLAN) to instances. This role is abstracted for OS6, OS9, and OS10.

The xstp role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in dellos connection variables, or the *provider* dictionary.

Installation
------------

    ansible-galaxy install Dell-Networking.dellos-xstp

Role variables
--------------

- *dellos_xstp*(dictionary) contains the hostname (dictionary)
- Hostname is the value of the *hostname* variable that corresponds to the name of the OS device
- Role is abstracted using the *ansible_net_os_name* variable that can take dellos6, dellos9, and dellos10 values
- Any role variable with a corresponding state variable set to absent negates the configuration of that variable
- Setting an empty value to any variable negates the corresponding configuration
- Variables and values are case-sensitive

**hostname keys**

| Key        | Type                      | Description                                             | Support              |
|------------|---------------------------|---------------------------------------------------------|----------------------|
| ``type``       | string(required) | Configures type of spanning-tree mode specified that can vary according the OS device; OS9 devices: stp, rstp, pvst, mstp; OS6 and OS10 devices: rstp, rapid-pvst, mst | dellos6, dellos9, dellos10 |
| ``enable``  | boolean, choices: true, false             | Enables/Disables the spanning tree protocol specified in type variable. | dellos6, dellos9, dellos10 |
| ``path_cost`` | boolean, choices: true, false | Configures or unconfigure the dynamic type of pathcost in OS10 devices.| dellos10 | dellos10 |
| ``mac_flush_timer`` | integer | Configures the mac_flush_timer value in dellos10 devices in the range 0-500. | dellos10 |
| ``stp``  | dictionary             | Configures simple spanning tree protocol. See the following stp.* keys for each list item.| dellos6. dellos9 |
| ``stp.bridge_priority`` | integer | Configures bridge-priority for the spanning tree. You can specify a value in the range 0-61440 in multiples of 4096. | dellos6, dellos9 |
| ``stp.state`` | string, choices: absent, present* | If set to absent, deletes the configured STP. | dellos9 |
| ``rstp``  | dictionary             | Configures rapid spanning tree protocol. See the following rstp.* keys for each list item. | dellos6, dellos9, dellos10 |
| ``rstp.bridge_priority`` | integer | Configures bridge-priority for the spanning tree. You can specify a value in the range 0-61440 in multiples of 4096. | dellos6, dellos9, dellos10 |
| ``rstp.max_age`` | integer  | Configures max_age timer for rstp in the range 6-40 in OS10 devices.| dellos10 |
| ``rstp.hello_time`` | integer | Configures hello time for rstp in the range 1-10 in OS10 devices. | dellos10 |
| ``rstp.forward_time`` | integer | Configures forward time for rstp in the range 4-30 in OS10 devices. | dellos10 |
| ``rstp.force_version`` | string, choices: stp | Configures force version for the BPDUs transmitted by rstp in OS10 devices. | dellos10 |
| ``rstp.mac_flush_threshold`` | integer | Configures mac flush threshold for rstp in the range 1-65535 in OS10 devices.| dellos10 |
| ``rstp.state ``| string, choices: absent, present* | If set to absent, deletes the configured RSTP in OS9 devices. | dellos9 |
| ``pvst``  | dictionary     | Configures per-vlan spanning tree protocol. See the following pvst.* keys for each list item. | dellos6, dellos9, dellos10 |
| ``pvst.vlan`` | list | Configures vlan for pvst. See the following vlan.* keys for each list item. | dellos6, dellos9, dellos10 |
| ``vlan.range_or_id``  | string             | Configures VLAN/range of VLANs for the per-vlan spanning tree protocol. | dellos6, dellos9, dellos10 |
| ``vlan.max_age`` | integer  | Configures max_age timer for vlan in the range 6-40 in OS10 devices.| dellos10 |
| ``vlan.hello_time`` | integer | Configures hello time for vlan in the range 1-10 in OS10 devices. | dellos10 |
| ``vlan.forward_time`` | integer | Configures forward time for vlan in the range 4-30 in OS10 devices. | dellos10 |
| ``vlan.enable`` | boolean, choices: true,false | Enable or disable spanning tree for the associated vlan range_or_id in OS10 devices. | dellos10 |
| ``vlan.mac_flush_threshold`` | integer | Configures mac flush threshold for vlan in the range 1-65535 in OS10 devices.| dellos10 |
| ``vlan.root`` | string, choices: primary, secondary | Designates the primary or secondary root for the associated vlan range_or_id in OS10 devices.This key is mutually exclusive with vlan.bridge_priority | dellos10 |
| ``vlan.bridge_priority`` | integer | Configures bridge-priority for the per-vlan spanning tree. You can specify a value in the range 0-61440 in multiples of 4096. This key is mutually exclusive with vlan.root. | dellos6, dellos9, dellos10 |
| ``pvst.state`` | string, choices: absent, present* | If set to absent, deletes the configured PVST. | dellos9 |
| ``mstp``  | dictionary     | Configures multiple spanning tree protocol. See the following mstp.* keys for each list item. | dellos6, dellos9, dellos10 |
| ``mstp.max_age`` | integer  | Configures max_age timer for mstp in the range 6-40 in OS10 devices.| dellos10 |
| ``mstp.max_hops`` | integer | Configures max-hops for mstp in the range 6-40 in OS10 devices. | dellos10 |
| ``mstp.hello_time`` | integer | Configures hello time for mstp in the range 1-10 in OS10 devices. | dellos10 |
| ``mstp.forward_time`` | integer | Configures forward time for mstp in the range 4-30 in OS10 devices. | dellos10 |
| ``mstp.force_version`` | string, choices: stp, rstp | Configures force version for the BPDUs transmitted by mstp in OS10 devices. | dellos10 |
| ``mstp.mstp_instances`` | list | Configures MSTP instance. See the following mstp_instances.* keys for each list item. | dellos6, dellos9, dellos10 |
| ``mstp_instances.number``     | integer                   | Configures the multiple spanning tree instance number. | dellos6, dellos9 |
| ``mstp_instances.number_or_range``     | integer                   | Configures the multiple spanning tree instance number in OS10 devices.| dellos10 |
| ``mstp_instances.vlans``      | string     | Configures VLAN/range of VLANs by mapping it to the instance number in OS9 and OS6 devices. | dellos6, dellos9 |
| ``mstp_instances.bridge_priority`` | integer | Configures bridge-priority for the spanning tree. You can specify any value in the range 0-61440 in multiples of 4096.This key is mutually exclusive with mstp_instances.root| dellos6, dellos9, dellos10 |
| ``mstp_instances.enable`` | boolean, choices: true,false | Enable or disable spanning tree for the associated mstp instance in OS10 devices. | dellos10 |
| ``mstp_instances.mac_flush_threshold`` | integer | Configures mac flush threshold for mstp instance in the range 1-65535 in OS10 devices.| dellos10 |
| ``mstp_instances.root`` | string, choices: primary, secondary | Designates the primary or secondary root for the associated mstp instance in OS10 devices.This key is mutually exclusive with mstp_instances.bridge_priority. | dellos10 |
| ``mstp_instances.vlans_state`` | string, choices: absent, present* | If set to absent, deletes the set of VLANs mapped to the spanning tree instance. | dellos6, dellos9 |
| ``mstp.state`` | string, choices: absent, present* | If set to absent, deletes the configured MSTP. | dellos9 |
| ``mstp.mst_config`` | dictionary | See the following mstp.mst_config.* for each item.This key is supported in OS10 devices| dellos10 |
| ``mst_config.name`` | string | Configures the name which is specified for the mstp. | dellos10 |
| ``mst_config.revision`` | integer | Configures the revision number for mstp. | dellos10 |
| ``mst_config.cfg_list`` | list | See the following mst_config.cfg_list.* for each list item. | dellos10 |
| ``cfg_list.number`` | integer | Specifies the mstp instance number. | dellos10 |
| ``cfg_list.vlans``      | string     | Configures VLAN/range of VLANs by mapping it to the instance number in OS10 devices. | dellos10 |
| ``cfg_list.vlans_state`` | string, choices: absent, present* | If set to absent, deletes the set of VLANs mapped to the spanning tree instance. | dellos10 |
| ``intf`` | list | Configures multiple spanning tree in interface. See the following intf.* keys for each list item. | dellos6, dellos9, dellos10 |
| ``intf < interface name >``| dictionary | See the following intf.&lt;interface name&gt;.* for each item. | dellos6, dellos9, dellos10 |
| ``intf.< interface name >.stp_type`` | list | Configures the list of spanning tree in interface.| dellos9 |
| ``intf.< interface name >.edge_port`` | boolean, choices: true, false |Configures edgeport as dynamic if set to true in OS10 devices.In OS9 devices according to the stp_type edge port is configured. In OS6, enables port fast at the interface level if the key is set to true.| dellos6, dellos9, dellos10 |
| ``intf.< interface name >.bpdu_filter``| boolean, choices: true, false | Enables or disables bpdufilter at interface. | dellos10 |
| ``intf.< interface name >.bpdu_guard``| boolean, choices: true,false | Enables or disables bpduguard at interface. | dellos10 |
| ``intf.< interface name >.guard``| string, choices: loop, root, none | Configures guard on the interface.| dellos10 |
| ``intf.< interface name >.enable`` | boolean, choices: true, false | Enables or disables spanning-tree at interface level. | dellos10 |
| ``intf.< interface name >.link_type``| string, choices: auto, point-to-point,shared. |Configures link type at the interface.| dellos10 |
| ``intf.< interface name >.rstp`` | dictionary | see the following intf.&lt;interface name&gt;.rstp.* for each item.| dellos10 |
| ``rstp.priority``| integer | Configures the rstp priority value at interface. | dellos10 |
| ``rstp.cost`` | integer | Configures the rstp cost value at interface. | dellos10 |
| ``intf.< interface name >.msti`` | list | See the following intf.&lt;interface name&gt;.msti for each list item. | dellos10 |
| ``msti.instance_number`` | integer or range | Specifies the mstp instance number or range. | dellos10 |
| ``msti.priority`` | integer | Specifies the priority value to be configured at the interface. | dellos10 |
| ``msti.cost`` | integer | Specifies the cost value to be configured at the interface. | dellos10 |
| ``intf.< interface name >.vlan`` | list | See the following intf.&lt;interface name&gt;.vlan for each list item. | dellos10 |
| ``vlan.instance_number`` | integer or range | Specifies the vlan id or range. | dellos10 |
| ``vlan.priority`` | integer | Specifies the priority value to be configured at the interface. | dellos10 |
| ``vlan.cost`` | integer | Specifies the cost value to be configured at the interface. | dellos10 |


> **NOTE**: Asterisk (_*_) denotes the default value if none is specified.

Connection variables
--------------------

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory
. This information can exist in the Ansible *group_vars* or *host_vars* directories or in the playbook itself.

| Key         | Required | Choices    | Description                                           |
|-------------|----------|------------|-------------------------------------------------------|
| ``host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport |
| ``port``        | no       |            | Specifies the port used to build the connection to the remote device; if unspecified, the value defaults to 22 |
| ``username`` | no       |            | Configures the username to use to authenticate the CLI login for the connection to the remote device; if value is unspecified, the ANSIBLE_NET_USERNAME environment variable value is used |
| ``password`` | no       |            | Specifies the password to use to authenticate the connection to the remote device (acceptable value of *transport*); if value is unspecified, the ANSIBLE_NET_PASSWORD environment variable value is used |
| ``authorize`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the ANSIBLE_NET_AUTH_PASS environment variable value is used and the device attempts to execute all commands in non-privileged mode . This key is supported only in dellos9 and dellos6. |
| ``auth_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if *authorize* is set to no, this key is not applicable; if value is unspecified, the ANSIBLE_NET_AUTH_PASS environment variable value is used . This key is supported only in dellos9 and dellos6. |
| ``provider`` | no       |            | Convenient method that passes all the above connection arguments as a dictionary object; all constraints (such as required or choices) must be met either by individual arguments or values in this dictionary|

> **NOTE**: Asterisk (\*) denotes the default value if none is specified.

Dependencies
------------

The *dellos-xstp* role is built on modules included in the core Ansible code. These modules were added in Ansible version 2.2.0.

Example playbook
----------------

This example uses the *dellos.dellos-xstp* role to configure different variants of spanning tree. Based on the type of STP and defined objects, the VLANs are associated and bridge priorities are assigned. It creates a *hosts* file with the switch details, and a *host_vars* file with connection variables. The corresponding role variables are defined in the *vars/main.yml* file at the role path. 
This example writes a simple playbook that only references the *dellos-xstp* role. By including the role, you automatically get access to all of the tasks to configure xSTP. 

**Sample hosts file**

    spine1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9/dellos6/dellos10)>

**Sample host_vars/spine1**
    
    hostname: spine1
    provider:
      host: "{{ hostname }}"
      username: xxxxx
      password: xxxxx
      authorize: yes
      auth_pass: xxxxx
    build_dir: ../temp/dellos9

**Sample vars/main.yml**

     dellos_xstp:
        type: rstp
        enable: true
        stp:
          bridge_priority: 4096
          state: present
        rstp:
          bridge_priority: 4096
        pvst:
          vlan:
            - range_or_id: 10
              bridge_priority: 4096
        mstp:
          mstp_instances:
            - number: 1
              vlans: 10,12
              bridge_priority: 4096
              vlans_state: present
        intf:
          fortyGigE 1/25
            stp_type:
              - stp
              - mstp
            edge_port: true

**Simple playbook to setup system - spine.yml**

    - hosts: spine
      roles:
         - Dell-Networking.dellos-xstp
 
**Run**

    ansible-playbook -i hosts spine.yml

(c) 2017 Dell EMC
