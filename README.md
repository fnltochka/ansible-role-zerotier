[![GitHub issues](https://img.shields.io/github/issues/fnltochka/ansible-role-zerotier.svg)](https://github.com/fnltochka/ansible-role-zerotier/issues)

# ZeroTier

This Ansible role adds the ZeroTier repository and installs the `zerotier-one` package using your system's package manager. Depending on the provided variables this role can also add and authorize new members to (existing) ZeroTier networks, and tell the new member to join the network.

## Requirements

Technically this role has no requirements. If it's ran without any variables set it will only run the installation tasks. The following variables impact the role's behavior:

[**zerotier_network_id**](#zerotier_network_id): when set hosts are told to join this network.  
[**zerotier_api_accesstoken**](#zerotier_api_accesstoken): when set the role can handle member authentication and configuration using the ZeroTier API.

## Role Variables

### zerotier_network_id

_Type_: string  
_Default value_:  
_Description_: The 16 character network ID of the network the new members should join. The node will not join any network if omitted.

### zerotier_member_register_short_hostname

_Type_: boolean  
_Default value_: `false`  
_Description_: By default `inventory_hostname` will be used to name a member in a network. If set to `true`, `inventory_hostname_short` will be used instead.

### zerotier_member_ip_assignments

_Type_: list  
_Default value_: `""`  
_Description_: A list of IP addresses to assign this member. For example, “[192.168.0.1, 192.168.0.2]". If you write “[]”, the system will select the addresses for you. If you don't write anything, the IP addresses will remain the same.

### zerotier_member_description

_Type_: string  
_Default value_: `""`  
_Description_: Optional description for a member.

### zerotier_allow_managed

_Type_: string  
_Default value_: `"1"`  
_Description_: Allow ZeroTier to set IP Addresses and Routes ( local/private ranges only)

### zerotier_allow_global

_Type_: string  
_Default value_: `"0"`  
_Description_: Allow ZeroTier to set Global/Public/Not-Private range IPs and Routes.

### zerotier_allow_default

_Type_: string  
_Default value_: `"0"`  
_Description_: Allow ZeroTier to set the Default Route on the system.

### zerotier_allow_dns

_Type_: string  
_Default value_: `"0"`  
_Description_: Allow ZeroTier to set DNS servers.

### zerotier_api_accesstoken

_Type_: string  
_Default value_: `""`  
_Description_: The access token needed to authorize with the ZeroTier API. You can generate one in your account settings at https://my.zerotier.com/. If this is left out then the newly joined member will not be automatically authorized.

### zerotier_api_url

_Type_: string  
_Default value_: `https://my.zerotier.com`  
_Description_: The url where the Zerotier API lives. Must use HTTPS protocol.

### zerotier_api_delegate

_Type_: string  
_Default value_: `localhost`  
_Description_: Option to delegate tasks for Zerotier API calls. This is useful in a situation where API calls can only be made from a white-listed management server, for example.

## Example Playbook

```yaml
- hosts: servers
  vars:
    zerotier_network_id: 1234567890qwerty
    zerotier_api_accesstoken: "{{ vault_zerotier_accesstoken }}"
    zerotier_register_short_hostname: true
    zerotier_allow_dns: "1"

  roles:
    - { role: fnltochka.zerotier, become: true }
```

## Example Inventory

```INI
    [servers]
    web1.example.com zerotier_member_ip_assignments='["192.168.195.1", "192.168.195.2"]'
    web2.example.com zerotier_member_ip_assignments='["192.168.195.3", "192.168.195.4"'
    db1.example.com zerotier_member_ip_assignments='["192.168.195.10"]'
    db2.example.com zerotier_member_ip_assignments='["192.168.195.11"]'
    db3.example.com zerotier_member_ip_assignments='["192.168.195.12"]'

    [webservers]
    web1.example.com
    web2.example.com

    [dbservers]
    db1.example.com
    db2.example.com
    db3.example.com

    [webservers:vars]
    zerotier_member_description='<AppName> webserver'

    [dbservers:vars]
    zerotier_member_description='<AppName> db cluster node'
```
