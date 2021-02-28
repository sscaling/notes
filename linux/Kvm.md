KVM
===

Virtualisation module built into the Linux kernel. Uses virsh (CLI) / virt-manager (Python UI) to manage VMs.

Libvirt
-------

An open-source API, daemon and management tool for platform virtualization [https://en.wikipedia.org/wiki/Libvirt](https://en.wikipedia.org/wiki/Libvirt). virsh / virt-manager interact with this.

`virt-host-validate` - sanity check that system is configured correctly

Qemu
----

Hardware emulator

Virsh (Virtualisation Shell)
----------------------------

-	`nodeinfo` - See the host info (memory, cpu etc)
-	`domcapabilities` - describe the capabilities of qemu-kvm and libvirt

---

Virtualbox
----------

Can use nested virtualization for CPUs with VT-x/AMD-V (Intel/AMD) capabilities

```
VBoxManage modifyvm <vm name> --nested-hw-virt on
```

For some reason, this is always grayed out in the UI

-	[Install KVM on Ubuntu 20 Server](https://computingforgeeks.com/install-kvm-hypervisor-on-ubuntu-focal-fossa/)
	-	Seems to be incorrect network bridge config
-	[Install KVM on Ubuntu 18](https://www.linuxtechi.com/install-configure-kvm-ubuntu-18-04-server/)
