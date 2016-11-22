xSTP Role for Dell EMC Networking OS
====================================

This role facilitates the configuration of xSTP attributes. It supports multiple version of Spanning Tree Protocol (STP): Rapid Spanning Tree (RSTP), Rapid Per-VLAN Spanning Tree (Rapid PVST+), Multiple Spanning Tree (MST), and Per-VLAN Spanning Tree (PVST).
It supports the configuration of bridge priority, enabling and disabling spanning tree, creating and deleting instances, and mapping virtual LAN (VLAN) to instances.
This role is abstracted for OS6, OS9, and OS10.


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
The hostname is the value of the variable ``hostname`` that corresponds to the name of the OS device.
This role is abstracted using the variable ``ansible_net_os_name`` that can take the following values: dellos6, dellos9 and dellos10.

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. 
For variables with no state variable, setting an empty value to the variable negates the corresponding configuration.
The variables and its values are case-sensitive.

hostname contains the following keys:

|       Key | Type                      | Notes                                                                                                                                                                                     |
|------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| type       | string(required) | Configures type of spanning tree mode specified that can vary according the OS device. For OS9 devices: stp, rstp, pvst, mstp. For OS6 and OS10 devices: rstp, rapid-pvst, mst. |
| pvst_vlans  | integer             | Configures VLAN/range of VLANs for the type of spanning tree protocol-- OS6: 1-4093; OS10: 1-4094. |
| bridge_priority | integer | Configures bridge-priority for the spanning tree. You can specify a value in the range 0-61440 in multiples of 4096. |
| mstp_instances | list | Contains objects to configure MSTP instance. See the following mstp_instances.* keys for each list item. |
| mstp_instances.number     | integer                   | Specifies the number of the spanning tree instance. |
| mstp_instances.vlans      | string     | Configures VLAN/range of VLANs by mapping it to the instance number. |
| mstp_instances.bridge_priority | integer | Configures bridge-priority for the spanning tree. You can specify any value in the range 0-61440 in multiples of 4096.|
| mstp_instances.vlans_state | string, choices: absent, present* | Configuring this key to absent deletes the set of VLANs mapped to the spanning tree instance. | 
| xstp_state | string, choices: absent, present* | Configuring this key to absent deletes the configured type of STP. |

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
role variables are defined in the ``vars/main.yml`` file at the role path.
This example writes a simple playbook that only references the dellos-xstp role. 
By including the role, you automatically get access to all of the tasks to configure xSTP. 

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

Sample ``vars/main.yml``:

    dellos_xstp:
      spine1:
        type: rapid-pvst
        pvst_vlans: 10
        bridge_priority: 4096
        mstp_instances:
          - number: 1
            vlans: 10,12
            bridge_priority: 4096
            vlans_state: present
        xstp_state: present


Simple playbook to setup system, ``spine.yml``:

    - hosts: spine
      roles:
         - Dell-Networking.dellos-xstp
 
Then run with:

    ansible-playbook -i hosts spine.yml


License
--------

Copyright (c) 2016, Dell Inc. All rights reserved.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
    http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
