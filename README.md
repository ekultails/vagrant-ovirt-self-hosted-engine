# vagrant-ovirt-self-hosted-engine

This Vagrantfile and Ansible Playbook provides a way to easily setup an all-in-one lab environment for oVirt.

## Requirements

* KVM+QEMU with [nested virtualization enabled](https://github.com/ekultails/rootpages/blob/master/src/virtualization.rst#nested-virtualization)
* [vagrant-libvirt](https://github.com/vagrant-libvirt/vagrant-libvirt)

## Usage

Create the virtual machine.

```
$ vagrant up
```

After the Playbook is completed setting up the environment, log into the virtual machine to finish the installation.

```
$ vagrant ssh
$ sudo hosted-engine --deploy
```

Use the default values for almost everything. Set a lower maximum RAM allocation, static IP addressing, and specify the NFS export mount via the Vagrant virtual machine's IP address.

oVirt 4.2:


```
          Please specify the memory size of the VM in MB (Defaults to maximum available): []: <LOWER_AMOUNT_OF_RAM_THAN_MAX>
```
```
          How should the engine VM network be configured (DHCP, Static)[DHCP]? Static
          Please enter the IP address to be used for the engine VM [192.168.121.2]: 192.168.121.201
[ INFO  ] The engine VM will be configured to use 192.168.121.201/24
          Please provide a comma-separated list (max 3) of IP addresses of domain name servers for the engine VM
          Engine VM DNS (leave it empty to skip) [127.0.0.1]: <VAGRANT_VM_IP>
          Add lines for the appliance itself and for this host to /etc/hosts on the engine VM?
          Note: ensuring that this host could resolve the engine VM hostname is still up to you
          (Yes, No)[No] Yes
```
```
          Please provide the FQDN for the engine you would like to use.
          This needs to match the FQDN that you will use for the engine installation within the VM.
          Note: This will be the FQDN of the VM you are now going to create,
          it should not point to the base host or to any other existing machine.
          Engine FQDN:  []: engine.example.com
```
```
          Please specify the storage you would like to use (glusterfs, iscsi, fc, nfs)[nfs]: nfs
          Please specify the nfs version you would like to use (auto, v3, v4, v4_1)[auto]: auto
          Please specify the full shared storage connection path to use (example: host:/path): <VAGRANT_VM_IP>:/exports/data
```

### Variables

* ovirt_version_major = The major version of oVirt. This should be left at 4.
* ovirt_version_minor = The minor version of oVirt to install.
* enable_branch_development = Install the latest development/snapshot packages corresponding to the version defined above.
* enable_branch_master = Install the latest packages for the next upcoming minor version of oVirt. This will ignore the previous `ovirt_version_*` settings.
* nfs_export_dir = The directory to create a network share for and to mount an additional volume to.
* hostname_hypervisor = The hostname for the Vagrant virtual machine.
* hostname_ovirt_engine = The hostname to use for the oVirt Engine that will be nested virtualized inside the Vagrant virtual machine. This fully qualified domain name (FQDN) should not already be in use by any other nodes.
* storage_file_system = The file system to use for the volume that will be created and mounted to the `nfs_export_dir`.

## Known Issues

In oVirt 4.2, the installation might fail due to what looks to be an ASCII encoding issue. The real problem here is that there was an issue with the specified NFS mount. The oVirt >= 4.3 release should fix the parsing of the error.

```
[ INFO  ] TASK [Copy configuration files to the right location on host]                                                                   
[ INFO  ] TASK [Copy configuration archive to storage]
[ ERROR ]  [WARNING]: Failure using method (v2_runner_on_failed) in callback plugin                                                       

[ ERROR ] (<ansible.plugins.callback.1_otopi_json.CallbackModule object at 0x190d650>):                                                   

[ ERROR ] 'ascii' codec can't encode character u'\u2018' in position 494: ordinal not in                                                  

[ ERROR ] range(128)

[ ERROR ] Failed to execute stage 'Closing up': Failed executing ansible-playbook
```

References:

* http://lists.ovirt.org/pipermail/users/2018-January/086631.html
* https://bugzilla.redhat.com/show_bug.cgi?id=1533500

## License

Apache 2.0
