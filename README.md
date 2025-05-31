# Fedora-GPU-Passthrough
This is my setup for getting GPU passthough to a VM with QEMU/KVM in user mode
## Detach the GPU from the host (based on https://gist.github.com/paul-vd/)
### /etc/default/grub
```
GRUB_CMDLINE_LINUX="rhgb quiet"
```
too
```
GRUB_CMDLINE_LINUX="rhgb quiet rd.driver.blacklist=nouveau modprobe.blacklist=nouveau video=efifb:off intel_iommu=on rd.driver.pre=vfio-pci kvm.ignore_msrs=1 vfio-pci.ids=10de:2204,10de:1aef pcie_aspm=off"
```
and update
```
sudo grub2-mkconfig -o /etc/grub2-efi.cfg
```
### /etc/dracut.conf.d/local.conf
```
add_drivers+=" vfio vfio_iommu_type1 vfio_pci "
```
and update
```
sudo dracut -f --kver $(uname -r)
```
## PERMISSIONS
### /etc/security/limits.conf
```
buggex  -  memlock  60000000
```
to avoid reboot
```
ulimit -l 60000000
```

### /etc/udev/rules.d/10-qemu-hw-users.rules
```
SUBSYSTEM=="vfio", OWNER="buggex"
```
to avoid reboot
```
sudo udevadm control --reload-rules
sudo udevadm trigger
```

### /etc/libvirt/qemu.conf
```
user = "buggex"
group = "qemu"
```
