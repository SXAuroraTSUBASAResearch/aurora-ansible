# Ansible for Aurora

This repository includes ansible roles and playbooks to setup Aurora using yum
repository on sx-aurora.com.

## Prerequisites

- CentOS 7.5 is installed into the Aurora you are setting up.
- The Aurora can access the internet. 

## Instructions

### 1. Write inventory file

`hosts.yaml` is the sample of inventory file.  List IP addresses of your Aurora
under `hosts:`.

If you have to use a proxy, write it as below in `hosts.yaml`.

```
all:
        hosts:
		192.168.0.1:
        vars:
                proxy_env:
                        http_proxy: http://address:port/
                        https_proxy: http://address:port/
```

### 2. Run playbooks

Run the sample playbooks you need.

```
% ansible-playbook -i hosts.yaml -u root -k playbooks/aurora.yaml
```

Or run your own playbooks using the provided roles.

## Playbook samples

The sample playbooks are located in `playbooks` directory.

- aurora.yaml: Setup repositories and install VEOS.
- aurora-sdk.yaml: Install tuning tool, nlc and SDK.
- aurora-ib.yaml: Setup Infiniband .
- aurora-scatefs.yaml: Setup scatefs. It reuiqres IB.
- aurora-musl.yaml: Setup musl environment.

### aurora-sdk.yaml

Before using this playbook, you have to download RPM files for SDK. Please
refere [the installation
guide](https://www.hpc.nec/documents/guide/#idm139944214657512) and download
required files and list them under `vars:`.  You also have to have lines for
license server.

```
	vars:
		aur_license:
                        License_server_host: 192.168.0.2
                        License_server_port: 7300
                sdk:
                        - files/sdk/nec-nc++-1.7.21-1.7.21-1.x86_64.rpm
                        - files/sdk/nec-nc++-2.1.22-2.1.22-1.x86_64.rpm
                        - files/sdk/nec-nc++-inst-2.1.22-1.noarch.rpm
                        - files/sdk/nec-nc++-musl-inst-1.7.21-1.noarch.rpm
                        - files/sdk/nec-nfort-1.7.21-1.7.21-1.x86_64.rpm
                        - files/sdk/nec-nfort-2.1.22-2.1.22-1.x86_64.rpm
                        - files/sdk/nec-nfort-inst-2.1.22-1.noarch.rpm
                        - files/sdk/nec-nfort-musl-inst-1.7.21-1.noarch.rpm
```

### aurora-ib.yaml

This playbook installs Mellanox OFED, installs packages in ve-infiniband and
setup IB network as described in the installation guide.

Before using this, please download
`MLNX_OFED_LINUX-4.3-3.0.2.1-rhel7.5-x86_64.tgz` from
http://www.mellanox.com/page/products_dyn?product_family=26 and save it in
`files` directory.

NOTE: Be careful that this playbook is not idempotent.

### aurora-scatefs.yaml

Setup Aurora as scatefs client. See the installation guide for details.

```
	vars:
                scatefs:
                        ibdevice:
                                -
                                        label: <label>
                                        pci_id: "0000:18:00.0"
                        mount:
                                -
                                        src: <address>:<path>
                                        path: <mount point>
                                        opts: "ibdev=HOME,rsize=4194304,wsize=4194304,auto,bg,_netdev"
```


### aurora-musl.yaml

Installs packages to support musl.
