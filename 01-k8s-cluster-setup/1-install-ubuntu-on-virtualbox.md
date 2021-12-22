# Install Ubuntu on VirtualBox

This guide demonstrates how to install a Linux distro (Ubuntu) on a Virtual Machine (virtualbox).

> The Lab environment is on MacOS, but it applies to Windows with some minor changes.
>
> VirtualBox Version: 6.1.30
>
> Ubuntu: 20.04.3

[TOC]

## 1. Install virtualization tools

### 1.1 Install VirtualBox

You can download binaries from [Download VirtualBox](https://www.virtualbox.org/wiki/Downloads)

Alternatively, if you are using a package manager, just type `brew install --cask virtualbox`.

> :point_right: VirtualBox requires a kernel extension to work. The kernel extension module is blocked due to the macOS security restrictions. You have to enable it first, otherwise you may run into failure when configure the **Host Network Interface**.
>
> ![failed-create-host-network-interface](../images/failed-create-host-network-interface.png)
>
> **How to enable the extension**: System Preferences → Security & Privacy → General →  **Allow** *System software from developer "Oracle America, Inc" has been updated.*
>
> ![allow-vbox-extension](../images/allow-vbox-extension.png)

### 1.2 Configurate VirtualBox Network

In File → Host Network Manager → Properties

* Make sure you configure `vboxnet0` and set it to `192.168.56.1/24`.
* If the subnet is different, update IPv4 Address to `192.168.56.1` and IPv4 Network Mask `255.255.255.0`
* Disable DHCP

Now you have added `vboxnet0` 

![host-network-setting](../images/host-network-setting.png)

## 2. Install Ubuntu Image

This section shows how to install and prepare **a Ubuntu base VM**. In the future, we can clone new VMs quickly from the base VM.

### 2.1 Download Ubuntu ISO

Download Ubuntu 20.04 LTS (Focal Fossa) from [Ubuntu 20.04](https://releases.ubuntu.com/20.04/).

Choose the **Server install image** to download.

### 2.2 Create and setup a VM

* Click **New** button option, Name the virtual machine `basenode`, select Linux Ubuntu (64-bit)

![vm-create-new-ubuntu-64](../images/vm-create-new-ubuntu-64.png)

* Set **Memory size to 6G** 

![vm-set-memory-size](../images/vm-set-memory-size.png)

* Create a virtual hard disk

![vm-create-new-virtual-hard-disk](../images/vm-create-new-virtual-hard-disk.png)

* Select **VDI** as the hard disk file type

![vm-hard-disk-file-type](../images/vm-hard-disk-file-type.png)

* Configure storage on physical hard disk as **Dinamically allocated**

![vm-dynamically-allocated](../images/vm-dynamically-allocated.png)

* Set  the hard disk file size to **40G** and then create the VM.

![vm-file-disk-size](../images/vm-file-disk-size.png)

* Now the VM is successfully created, select the VM and click the `settings` property on the top menu bar.

![vm-basenode-settings](/Users/min/Work/dive-into-k8s/assets/vm-basenode-settings.png)

* Go to **System → Processor**, set the number of **CPUs to 2**. Save the changes.

![vm-basenode-set-cpu-core](/Users/min/Work/dive-into-k8s/assets/vm-basenode-set-cpu-core.png)

* Go to **Storage → Controller: IDE**, now it should be empty. Then click the **disk icon** on the right of the **Optical Drive**. Select **choose a disk file**. Locate the **Ubuntu ISO image** you have downloaded before and select it.

![vm-basenode-add-ubuntu-to-optical-drive](../images/vm-basenode-add-ubuntu-to-optical-drive.png)

### 2.3 Install Ubuntu

* Click **Start** to boot the `basenode` VM, and choose the system language, for example **English**.

![install-ubuntu-language](../images/install-ubuntu-language.png)

* Choose **Continue without updating**.

![install-ubuntu-no-updating](../images/install-ubuntu-no-updating.png)

* Select **default** setting for **Keyboard configuration**.

![install-ubuntu-keyboard-config](../images/install-ubuntu-keyboard-config.png)

* Select **default** setting for **Network connections**.

![install-ubuntu-network-connections](../images/install-ubuntu-network-connections.png)

* Select **default** setting for **Configure proxy**.

![install-ubuntu-configure-proxy](../images/install-ubuntu-configure-proxy.png)

* Use **default** setting for **Ubuntu archive mirror**.

![install-ubuntu-default-archive-mirror](../images/install-ubuntu-default-archive-mirror.png)

* Use **default** setting for **storage configuration**.

![install-ubuntu-storage-configuration](../images/install-ubuntu-storage-configuration.png)

![install-ubuntu-storage-configuration-summary](../images/install-ubuntu-storage-configuration-summary.png)

![install-ubuntu-storage-configuration-continue](../images/install-ubuntu-storage-configuration-continue.png)

* Setup profile. For example:
  * Your name: `sadmin`
  * Your server's name: `basenode`
  * Pick a username: `sadmin`
  * Choose a password: `sadmin`

![install-ubuntu-setup-profile](../images/install-ubuntu-setup-profile.png)

* Enable **Install OpenSSH server**

![install-ubuntu-ssh-setup](../images/install-ubuntu-ssh-setup.png)

* Leave the **Featured Server Snaps** empty.

![install-ubuntu-featured-snaps](../images/install-ubuntu-featured-snaps.png)

* Wait for the installation to complete. **Remember do not restart the VM at the moment**, **shutdown** the VM directly.

![install-ubuntu-complete](/Users/min/Work/dive-into-k8s/assets/install-ubuntu-complete.png)

![install-ubuntu-shutdown-vm](../images/install-ubuntu-shutdown-vm.png)

* In VM settings, go to **Storage → Controller: IDE** → Select `Ubuntu-20.04.3-live-server-amd64.iso` and then choose **remove disk from virtual drive** from the Optical Drive. This step is to prevent VM reinstall ubuntu on reboot.

![install-ubuntu-remove-storage](../images/install-ubuntu-remove-storage.png)

### 2.4 Add Host-only Network Adapter to VM

> With **NAT** Network Adapter:
>
> * **If host can access internet, then VM can access internet**.
> * VM cannot ping another VM
> * VM can ping the host
> * **Host cannot ping VM**.
> * IP is `10.0.2.15`
> * Gateway is `10.0.2.2`
> * Requests from the VM is passed to NAT Engine, the NAT engine can leverage the host to access internet, the processed packet is then come back to VM via the NAT Engine.
>
> With **Host-only** Network Adapter：
>
> * The VM cannot access internet
> * VM can ping another VM
> * VM can ping host
> * Host can ping VM
> * IP is in range of the Host-only network interface, default is `192.168.56.*`
> * Gateway is the IP of Host-only network interface, default is `192.168.56.1`
>
> **With the help of these two network interfaces:**
>
> * **Host can ping VM**
> * **VM can access internet**
> * **VM can ping another VM**
> * **VM can ping host**

Go to **Settings → Network → Adapter 2**

Enable Network Adpater and Attach to `Host-only Adapter`, and select `vboxnet0` as the network.

![vm-hostonly-network-adapter](../images/vm-hostonly-network-adapter.png)

### 2.5 Set IP of Adapter 2 Network

**Boot** the VM and **login** to the system.

Make sure you have the root priviledge to make below changes.

```sh
# switch to root user
sudo -i

# Update netplan config
vi /etc/netplan/00-installer-config.yaml
```

Update config with below content.

```yaml
network:
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: no
      addresses:
        - 192.168.56.2/24
  version: 2
```

Then apply the changes.

```sh
netplan apply
```

After this change, the VM has two network adapters:

* One is NAT which will get an IP automatically, for example, 10.0.2.15, allowing external access from your VM
* One is host adapter, whose static IP is configured as `192.168.56.2`

### 2.6 Turn off swap

```sh
swapoff -a

vi /etc/fstab
# Comment or remove the line with `swap` keyword.
```

![disable-swap](../images/disable-swap.png)

### 2.7 Update source

```sh
# 1. backup sources.list
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

vi /etc/apt/sources.list
# Then press ESC + gg + dG to delete all lines
# Then Set paste

# 2. copy aliyun source to /etc/apt/sources.list
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

# 3. Update source list
sudo apt update
sudo apt upgrade
```

**Poweroff** current machine with `shutdown -h now`. The **basenode** VM is now successfully setup.


