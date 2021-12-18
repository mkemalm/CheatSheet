 **PROBLEM: qemu-kvm: could not open disk image ' ': Permission denied**
 
 **SOLUTION:** open /etc/libvirt/qemu.conf and uncomment "user=root" and "group=root", restart libvirtd. 
