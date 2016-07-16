waagent
=======

Ansible role which helps to install and configure Microsoft Azure Linux Guest
Agent.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
---

- name: Example of how to install WALinuxAgent with default settings
  hosts: all
  roles:
    - waagent

- name: Example of how to change the default settings
  hosts: all
  vars:
    # Install it from a local YUM repo
    waagent_epel_install: yes
    waagent_epel_yumrepo_url: https://myserver.com/path/to/the/repo/waagent
    waagent_epel_yumrepo_params:
      description: Windows Azure Linux Agent YUM repo
      name: waagent
    # Enable logging
    waagent_logs_verbose: y
    # Disable resource disk formatting
    waagent_resourcedisk_format: n
    # Add option not directly supported by this role
    waagent_config__custom:
      My.Settings: myVal
  roles:
    - waagent
```

This role requires the WALinuxAgent to be available in the form of RPM package.
It's expected that this package will be in EPEL in the near future. Until then
you have to build it [from the source](https://github.com/Azure/WALinuxAgent/releases):

```
$ curl -O -J -L https://github.com/Azure/WALinuxAgent/archive/v2.1.5.tar.gz
$ tar -xzf WALinuxAgent-2.1.5.tar.gz
$ cd WALinuxAgent-2.1.5
$ yum install -y python-setuptools.noarch
$ python setup.py bdist_rpm --post-inst rpm/post-inst
$ rpm -Uhv dist/WALinuxAgent-2.1.5-1.noarch.rpm
```


Role variables
--------------

```
# Package to be installed (explicit version can be specified here)
waagent_pkg: WALinuxAgent

# Service name
waagent_service: waagent

# Whether to install EPEL YUM repo
waagent_epel_install: no

# EPEL YUM repo URL
waagent_epel_yumrepo_url: "{{ yumrepo_epel_url | default('https://dl.fedoraproject.org/pub/epel/$releasever/$basearch/') }}"

# Extra EPEL YUM repo params
waagent_epel_yumrepo_params: "{{ yumrepo_epel_params | default({}) }}"

# Path to the WALinuxAgent config files
waagent_config_file: /etc/waagent.conf


#Values for the default config
waagent_role_stateconsumer: None
waagent_role_configurationconsumer: None
waagent_role_topologyconsumer: None
waagent_provisioning_enabled: y
waagent_provisioning_deleterootpassword: y
waagent_provisioning_regeneratesshhostkeypair: y
waagent_provisioning_sshhostkeypairtype: rsa
waagent_provisioning_monitorhostname: y
waagent_provisioning_decodecustomdata: n
waagent_provisioning_executecustomdata: n
waagent_provisioning_allowresetsysuser: n
waagent_resourcedisk_format: y
waagent_resourcedisk_filesystem: ext4
waagent_resourcedisk_mountpoint: /mnt/resource
waagent_resourcedisk_enableswap: n
waagent_resourcedisk_swapsizemb: 0
waagent_logs_verbose: n
waagent_os_rootdevicescsitimeout: 300
waagent_os_opensslpath: None
waagent_autoupdate_enabled: y
waagent_autoupdate_gafamily: Prod

# Default config
waagent_config__default:
  Role.StateConsumer: "{{ waagent_role_stateconsumer }}"
  Role.ConfigurationConsumer: "{{ waagent_role_configurationconsumer }}"
  Role.TopologyConsumer: "{{ waagent_role_topologyconsumer }}"
  Provisioning.Enabled: "{{ waagent_provisioning_enabled }}"
  Provisioning.DeleteRootPassword: "{{ waagent_provisioning_deleterootpassword }}"
  Provisioning.RegenerateSshHostKeyPair: "{{ waagent_provisioning_regeneratesshhostkeypair }}"
  Provisioning.SshHostKeyPairType: "{{ waagent_provisioning_sshhostkeypairtype }}"
  Provisioning.MonitorHostName: "{{ waagent_provisioning_monitorhostname }}"
  Provisioning.DecodeCustomData: "{{ waagent_provisioning_decodecustomdata }}"
  Provisioning.ExecuteCustomData: "{{ waagent_provisioning_executecustomdata }}"
  Provisioning.AllowResetSysUser: "{{ waagent_provisioning_allowresetsysuser }}"
  ResourceDisk.Format: "{{ waagent_resourcedisk_format }}"
  ResourceDisk.Filesystem: "{{ waagent_resourcedisk_filesystem }}"
  ResourceDisk.MountPoint: "{{ waagent_resourcedisk_mountpoint }}"
  ResourceDisk.EnableSwap: "{{ waagent_resourcedisk_enableswap }}"
  ResourceDisk.SwapSizeMB: "{{ waagent_resourcedisk_swapsizemb }}"
  Logs.Verbose: "{{ waagent_logs_verbose }}"
  OS.RootDeviceScsiTimeout: "{{ waagent_os_rootdevicescsitimeout }}"
  OS.OpensslPath: "{{ waagent_os_opensslpath }}"
  AutoUpdate.Enabled: "{{ waagent_autoupdate_enabled }}"
  AutoUpdate.GAFamily: "{{ waagent_autoupdate_gafamily }}"

# Custom config
waagent_config__custom: {}

# Final config
waagent_config: "{{
  waagent_config__default.update(waagent_config__custom) }}{{
  waagent_config__default }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
