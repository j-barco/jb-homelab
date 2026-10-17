# 2026-10-17 - Hardware virtualization disabled in BIOS

**Area:** proxmox

## Symptom

On the Proxmox host (ThinkCentre M910Q, Intel Core i5-7600T), this command returned no `Virtualization` line:

```bash
lscpu | grep -E "Model name|^CPU\(s\)|Virtualization"
```

Further checks confirmed KVM was unavailable:

```bash
grep -c vmx /proc/cpuinfo   # 0
ls -l /dev/kvm              # No such file or directory
```

Without KVM, Proxmox can only run LXC containers; VMs (OPNsense, the K3s master) would be extremely slow or fail to start.

## Cause

Intel VT-x was disabled in the BIOS. The i5-7600T supports it, so this was a firmware setting, common on second-hand machines.

## Solution

In the BIOS: **Advanced -> CPU Setup**

- Intel (R) Virtualization Technology: **Enabled**
- VT-d: **Enabled**
- TxT (Trusted Execution Technology): left **Disabled** (not needed)

Verification after reboot:

```bash
grep -c vmx /proc/cpuinfo   # 8
ls -l /dev/kvm              # crw-rw---- 1 root kvm 10, 232 ...
```

## What I learned

- Always check `/dev/kvm` before planning VMs on a new host.
- `grep -c vmx /proc/cpuinfo` counts lines, not cores: the `flags` line and the `vmx flags` line both match, so 4 cores report 8.
- BIOS menu locations vary by model: on this ThinkCentre the option lives under *Advanced -> CPU Setup*, not under *Security*.
