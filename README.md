# Single-GPU-Passthrough-KVM
My settings that helped me fix the dreaded reset bug on an RX580
Guides that helped me: https://github.com/cosminmocan/vfio-single-amdgpu-passthrough <br>
Though as a rule of thumb, start off by not using hooks at all. Otherwise, try to keep them minimal, not overcomplicated. QEMU nowadays should automatically handle reinitializing the GPU, all you have to do is start/stop your display manager.

**My XML:**

> uploaded separately since it's too long

**/etc/default/grub:**

`GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet amdgpu.ppfeaturemask=0xffffffff amd_iommu=on iommu=pt video=efifb:off disable_idle_d3=1"`

*/etc/libvirt/hooks/qemu.d/win11/prepare/begin/start.sh:*

`set -x` <br>
`pkill wlroots (shouldn't be necessary, I still have to do some testing though)` <br>
`systemctl stop display-manager.service`

**/etc/libvirt/hooks/qemu.d/win11/release/end/release.sh:**

`set -x` <br>
`sleep 5` <br>
`systemctl start display-manager.service`
