

# Manage machines with qemu CLI (qemu monitor console)


Most of the below sections about installation and booting up was learned from [this tutorial at linuxhint](https://linuxhint.com/install_qemu_debian/).

## Check support and install

Check your virtualization support (e.g. `VT-x`)
```
lscpu | grep Virt
```
If your output has `VT-x` ya should be good to go.


Install (debian)
```
sudo apt update
sudo apt install qemu qemu-kvm

```

## Create a machine from ISO

### Image file

```
qemu-img create -f qcow2 <my-image-name>.img 40G
```
Example allocates 40 GB to your image. Replace `<my-image-name>` with a name according to your preference.
`qcow2` is the image file system, there are other options that may be more efficient in some cases but this choice "works for me" while it enables to save execution state snapshots.

### Installing the machine with KVM

Prepare a shell script (e.g. `install.sh`) for the installation. Point to the ISO you want to use in your script.\
Example:
```
kvm 	-hda <my-image-name>.img \
	-cdrom ../../ISOs/linuxmint-19.2-cinnamon-64bit.iso \
	-m 3072 \
	-net nic \
	-net user \
	-soundhw all
```
In this case we allocate 3 GB RAM to the machine (`-m 3072`). Run the script to start installation from ISO.


## Starting up the machine
Prepare a shell script (e.g. `start.sh`) for booting up the machine.
Example:
```
kvm 	-hda <my-image-name>.img \
	-m 3072 \
	-net nic \
	-net user \
	-vga virtio \
	-soundhw all
```
The `-vga virtio` parameter will enable different screen resolutions (at least this conf "works for me" I have not researched the alternatives very well though).


## Enter/exit monitor console
While working in the machine (or while booting up in case you want a load a snapshot according to below) you can switch between the machine interface and the *qemu monitor console* with:\ 

`Ctrl + Alt + 2` to enter monitor console\

`Ctrl + Alt + 1` to enter machine interface



## Saving/restoring execution state snapshot

Save the current state:
```
savevm <snapshot-name>
```

Load a particular (`<snapshot-name>`) snapshot:
```
loadvm <snapshot-name>
```

List all snapshots
```
info snapshots
```

Remove a snaphsot
```
delvm <snapshot-name>
```


## Misc monitor console commands


Attempt a clean (ACPI) shutdown
```
system_powerdown
```


Quit QEMU immediately\

`quit` or `q`


A good [reference on monitor commands on wikibooks](https://en.wikibooks.org/wiki/QEMU/Monitor).

Another [good reference at opensuse.org](https://doc.opensuse.org/documentation/leap/virtualization/html/book.virt/cha.qemu.monitor.html).


# Manage machines with virt-manager

...cos all the above is great but we do want to use virt manager don't we. 

## Install

(Debian)
```
apt-get install virt-manager
```

## Share folder between host and guest


Decide on the folder to share at your host. Example:

```
mkdir /vm-share
chmod 777 /vm-share
```


Go to the hardware view in virt-manager while the machine is not running (same window as the virtual machine console but choose "show virtual hardware details").\
Click `Add hardware`

Example values:

```
Mode: Mapped (enables writing in both directions)
Source path: /vm-share
Target path: /share
```
The *Target path* is a common name for host and guest that the guest will use for mounting the device.

Now in the machine when you boot it up you can mount the drive as follows (Example):


```
root@my-machine# mkdir /home/<my-user>/Desktop/host-share
root@my-machine# chmod 777 /home/<my-user>/Desktop/host-share
root@my-machine# mount -t 9p -o trans=virtio /share /home/<my-user>/Desktop/host-share
```

or permanently in fstab:

```
sudo vim /etc/fstab
```
```
...
/home/<my-user>/Desktop/host-share /share 9p  trans=virtio,version=9p2000.L,rw    0   0
...
```


This technique was learned from [this excellent tutorial](http://nts.strzibny.name/how-to-set-up-shared-folders-in-virt-manager/)


## virt-manager troubleshoot

# Hanging,...problem starting up virt-manager? 
Try to restart the libvirt. Depending on your distro, something like:

```
sudo systemctl stop libvitd
sudo systemctl status libvirtd
sudo systemctl start libvirtd
``` 

Another remedy if libvirt or some of the machines are in a bad state and cant recover, can be to startit up "low level" with a qemu script according to above. Just start up and shut down the machine. Worked for me.


