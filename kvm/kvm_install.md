* `egrep -c '(vmx|svm)' /proc/cpuinfo` output should be greater than 0

* check kvm device

```
 sudo apt install cpu-checker
 sudo kvm-ok 
 	INFO: /dev/kvm exists
	KVM acceleration can be used
```



* install packages

```
 sudo apt update

 sudo apt-get install qemu qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

 sudo systemctl start libvirtd

 sudo systemctl enable libvirtd
 
```

* add bridge network

```
 nmcli con show --active
 nmcli con add ifname br0 type bridge con-name br0
 nmcli con add type bridge-slave ifname enp0s25 master br0
 nmcli con modify br0 bridge.stp no
 nmcli con down wired-direct
 nmcli con up br0
 sudo systemctl restart NetworkManager.service

 virsh net-list --all

 cat > bridge.xml <<EOF
<network>
    <name>host-bridge</name>
    <forward mode="bridge"/>
    <bridge name="br0"/>
</network>
EOF

virsh net-define bridge.xml
virsh net-list --all
virsh net-start host-bridge
virsh net-autostart host-bridge
```

* define custom path for storage pool(optional)

```
virsh pool-list 
virsh pool-edit default
```
