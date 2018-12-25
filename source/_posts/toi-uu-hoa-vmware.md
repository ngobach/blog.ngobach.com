---
title: Tối ưu hóa VMWare
s: toi-uu-hoa-vmware
date: 2017-03-06 23:54:30
tags: [vmware, hackintosh, vm]
---

Dưới đây là 1 số tham số có thể thêm vào file **.vmx** để tăng hiệu năng của máy ảo VMWare một cách đáng kể.
<!--more-->

```
sched.mem.pshare.enable = "FALSE"
mainMem.useNamedFile = "FALSE"
prefvmx.minVmMemPct = "100"
prefvmx.useRecommendedLockedMemSize = "TRUE"
mainMem.partialLazySave = "FALSE"
mainMem.partialLazyRestore = "FALSE"
priority.grabbed = "high"
priority.ungrabbed = "normal"
```

* sched.mem.pshare.enable = "FALSE", by disabling memory sharing your guests will not share common memory blocks. Your VMware product will also stop comparing memory blocks
* mainMem.useNamedFile = "FALSE", doesn't use named-file - use for VMs on USB-disks or other slow disks. On Windows: useNamedFile= "FALSE" causes memory to be backed by the host's swap space.
* prefvmx.minVmMemPct = "100", fit memory into RAM. Whenever possible, avoid settings lower 100%. 
* MemTrimRate = "0" , by disabling MemTrimRate, memory allocation inside the guest is faster because it doesn't take and give memory to the host os upon all requests.
* prefvmx.useRecommendedLockedMemSize = "TRUE" , this tells VMWare whether to use a fixed sized memory chunk or balloon and shrink memory as needed.
* mainMem.partialLazySave = "FALSE" , do not take snapshots in background
* mainMem.partialLazyRestore = "FALSE" , do not restore snapshots in background
* priority.grabbed = "high" , sets the priority for the VM in grabbed state
* priority.ungrabbed = "normal" , sets the priority for the VM in ungrabbed state