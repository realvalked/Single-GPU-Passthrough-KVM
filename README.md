# Single-GPU-Passthrough-KVM
LAST UPDATED: 19th December, 2024

Settings that helped me fix the dreaded reset bug on an RX580 <br>
GPU: XFX Radeon RX 580 GTS XXX, 8GB with vbios dump attached to virt-manager<br>
CPU: Ryzen 5 4500 <br>
Motherboard: MSI A520M-A PRO <br>
RAM: 16GB <br>
SSD: WD Blue SN570 500GB <br>
Post that ultimately helped me fix everything: https://www.reddit.com/r/VFIO/comments/17jtexm/single_gpu_passthrough_no_longer_working/ <br>
As a rule of thumb, start off by not using hooks at all. Otherwise, try to keep them minimal, not overcomplicated. QEMU nowadays should automatically handle reinitializing the GPU, all you have to do is start/stop your display manager. At least that's how it's like for my setup.

Good guide of setting up virt-manager (except the hooks part): https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/home <br>
also video version: https://www.youtube.com/watch?v=jc3PjDX-CGs <br>
Also make sure to configure all the necessary BIOS settings before proceeding!!!

# The thick of it

**My XML:**

> uploaded separately since it's too long

**/etc/default/grub:**

`GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet amdgpu.ppfeaturemask=0xffffffff amd_iommu=on iommu=pt video=efifb:off disable_idle_d3=1"`

**HOOK HELPER: https://passthroughpo.st/simple-per-vm-libvirt-hooks-with-the-vfio-tools-hook-helper/**

**/etc/libvirt/hooks/qemu.d/win11/prepare/begin/start.sh:**

`set -x` <br>
`pkill wlroots (shouldn't be necessary, I still have to do some testing though)` <br>
`systemctl stop display-manager.service`

**/etc/libvirt/hooks/qemu.d/win11/release/end/release.sh:**

`set -x` <br>
`sleep 5` <br>
`systemctl start display-manager.service`

**vendor-reset**

https://github.com/gnif/vendor-reset
Check if you properly installed it by doing `dkms status` <br>
output should be something like: `vendor-reset/0.1.1, (your kernel here), x86_64: installed` along with other dkms modules if you have any installed <br>
Note: I did try using the AUR vendor-reset packages, however the one that worked best for me was the one directly from the repository, cloned and built. <br>
Note 2: if you have any issues building on kernel 6.12, you have to change a line in `src/amd/amdgpu/atom.c`, according to this github issue: https://github.com/gnif/vendor-reset/issues/87 (personally I had to do this)

# The End

Other sources: <br>
https://www.reddit.com/r/VFIO/comments/rp0vbi/single_gpu_guides_need_to_stop_putting_forbidden/ <br>
https://github.com/cosminmocan/vfio-single-amdgpu-passthrough <br>
https://www.reddit.com/r/VFIO/comments/1g3ptx7/7900_xtx_issue/ <br>
https://www.reddit.com/r/VFIO/comments/rqyas3/rx_6700xt_appearing_to_not_reset_correctly_after/?context=3 <br>
https://github.com/joeknock90/Single-GPU-Passthrough <br>
https://gitlab.com/Karuri/vfio <br>
https://github.com/cosminmocan/vfio-single-amdgpu-passthrough <br>
