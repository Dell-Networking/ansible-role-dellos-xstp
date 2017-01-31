xSTP Role for Dell EMC Networking OS
====================================

This role facilitates the configuration of xSTP attributes. It supports multiple version of Spanning Tree Protocol (STP): Rapid Spanning Tree (RSTP), Multiple Spanning Tree (MST), and Per-VLAN Spanning Tree (PVST).
It supports the configuration of bridge priority, enabling and disabling spanning tree, creating and deleting instances, and mapping virtual LAN (VLAN) to instances. This role is abstracted for OS9, OS6 and OS10 devices.


Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-xstp
```

Requirements
------------

This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in dellos connection variables, or the ``provider``
dictionary.

Role Variables
--------------

``dellos_xstp``(dictionary) contains the hostname (dictionary). 
The hostname is the value of the variable ``hostname`` that corresponds to the name of the OS device. This role is abstracted using the variable ``ansible_net_os_name`` that can take the following values: dellos9, dellos6 and dellos10.

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. 
For variables with no state variable, setting an empty value to the variable negates the corresponding configuration.
The variables and its values are case-sensitive.

``hostname`` contains the following keys:

|       Key | Type                      | Notes                                                                                                                                                                                     |
|------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| type       | string, choices: stp, rstp, pvst, mstp | Configures the type of spanning tree mode.  |
| enable  | boolean, choices: true, false             | Enables/Disables the spanning tree protocol specified in type variable. |
| stp  | dictionary             | Configures simple spanning tree protocol. See the following stp.* keys for each list item. |
| stp.bridge_priority | integer | Configures bridge-priority for the spanning tree. You can specify a value in the range 0-61440 in multiples of 4096. |
| stp.state | string, choices: absent, present* | If set to absent, deletes the configured STP. |
| rstp  | dictionary             | Configures rapid spanning tree protocol. See the following rstp.* keys for each list item. |
| rstp.bridge_priority | integer | Configures bridge-priority for the spanning tree. You can specify a value in the range 0-61440 in multiples of 4096. |
| rstp.state | string, choices: absent, present* | If set to absent, deletes the configured RSTP. |
| pvst  | dictionary     | Configures per-vlan spanning tree protocol. See the following pvst.* keys for each list item. | 
| pvst.vlan | list | Configures vlan for pvst. See the following vlan.* keys for each list item. |
| vlan.range_or_id  | string             | Configures VLAN/range of VLANs for the per-vlan spanning tree protocol. |
| vlan.bridge_priority | integer | Configures bridge-priority for the per-vlan spanning tree. You can specify a value in the range 0-61440 in multiples of 4096. |
| pvst.state | string, choices: absent, present* | If set to absent, deletes the configured PVST. |
| mstp  | dictionary     | Configures multiple spanning tree protocol. See the following mstp.* keys for each list item. |  
| mstp.mstp_instances | list | Configures MSTP instance. See the following mstp_instances.* keys for each list item. |
| mstp_instances.number     | integer                   | Configures the multiple spanning tree instance number. |
| mstp_instances.vlans      | string     | Configures VLAN/range of VLANs by mapping it to the instance number. |
| mstp_instances.bridge_priority | integer | Configures bridge-priority for the spanning tree. You can specify any value in the range 0-61440 in multiples of 4096.|
| mstp_instances.vlans_state | string, choices: absent, present* | If set to absent, deletes the set of VLANs mapped to the spanning tree instance. | 
| mstp.state | string, choices: absent, present* | If set to absent, deletes the configured MSTP. |                     
| intf | list | Configures multiple spanning tree in interface. See the following intf.* keys for each list item. |
| intf.name | string (required)        | Configures spanning tree in the interface with this interface name. |
| stp_type | list | Configures the list of spanning tree in interface. This key is supported only in OS9. |
| intf.edgeport | string, choices: present,bpduguard   | Configures the edgeport at the interface spanning tree. If the value is "bpduguard", enables bpduguard at edgeport.                                                        |

```
Note: Asterisk (*) denotes the default value if none is specified.
```

Connection Variables
--------------------

Ansible Dell EMC Networking roles require the following connection information to establish communication with the nodes in your inventory. This information can exist in
the Ansible group_vars or host_vars directories, or in the playbook itself.

|         Key | Required | Choices    | Description                              |
| ----------- | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | Hostname or address for connecting to the remote device over the specified ``transport``. The value of this key is used as the destination address for the transport. |
|        port | no       |            | Port to use when building the connection to the remote device. This key  applies to either acceptable value of ``transport``. This key defaults to the appropriate transport common port if the key provides none (CLI=22, HTTP=80, HTTPS=443). |
|    username | no       |            | Configures the username to use to authenticate the connection to the remote device. The value of this key authenticates the CLI login. If this key does not specify the value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password to use to authenticate the connection to the remote device. This is a common argument used for the acceptable value of ``transport``. If this key does not specify the value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If the task does not specify the value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. If not specified, the device attempts to execute all commands in non-privileged mode. |
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If ``authorize`` is set to no, then this key is not applicable. If this key does not specify the value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. This key supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all the above connection arguments as a dictionary object. All constraints (such as required or choices) must be met either by individual arguments or values in this dictionary. |

```
Note: Asterisk (*) denotes the default value if none is specified.
```

Dependencies
------------

The dellos-xstp role is built on modules included in the core Ansible code.
These modules were added in Ansible version 2.2.0.


Example Playbook
----------------
The following example uses the dellos.dellos-xstp role to configure different variants of spanning tree. 
Based on the type of STP and defined objects, the VLANs are associated and bridge priorities are assigned.
This example creates a ``hosts`` file with the switch details, a ``host_vars`` file with connection variables. The corresponding 
role variables are defined in the ``vars/main.yaml`` file at the role path.
This example writes a simple playbook that only references the dellos-xstp role. 

Sample hosts file:

    spine1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9/dellos6/dellos10)>


Sample ``host_vars/spine1``:
    
    hostname: spine1
    provider:
      host: "{{ hostname }}"
      username: xxxxx
      password: xxxxx
      authorize: yes
      auth_pass: xxxxx
      transport: cli

Sample ``vars/main.yaml``:

    dellos_xstp:
      spine1:
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
          - name: fortyGigE 1/25
            stp_type:
              - stp
              - mstp
            edgeport: present

Simple playbook to setup system, ``spine.yaml``:

    - hosts: spine1
      roles:
         - Dell-Networking.dellos-xstp
 
Then run with:

    ansible-playbook -i hosts spine.yaml


License
--------

Copyright (c) 2017, Dell Inc. All rights reserved.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
    http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
