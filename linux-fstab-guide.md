# 📌 Linux /etc/fstab Explained

## 🔹 What is /etc/fstab?

/etc/fstab is a system configuration file in Linux that defines how disk partitions, block devices, and remote filesystems are mounted automatically during system boot.

---

## Basic Command

```bash
vim /etc/fstab
```

---

## 🧾 Syntax Format

```bash
<device>   <mount_point>   <filesystem_type>   <options>   <dump>   <pass>
```

---

## ✅ Example Entry

```fstab
/dev/sdb1   /data   xfs   defaults   0 0
```

---

## 🔍 Field Explanation

```
/dev/sdb1   --> Partition (device name)

/data       --> Mount point (directory where filesystem is mounted)

xfs / ext4  --> Filesystem type

defaults    --> Standard mount options

0 0         --> dump and fsck values (backup and filesystem check order)
```

---

# `defaults` Option Explanation

The `defaults` option in `/etc/fstab` represents:

```
rw,suid,dev,exec,auto,nouser,async
```

## Meaning of Each Option

| Option | Meaning                     |
| ------ | --------------------------- |
| rw     | Read-write mount            |
| suid   | Allow SUID programs         |
| dev    | Allow device files          |
| exec   | Allow execution of binaries |
| auto   | Mount automatically at boot |
| nouser | Only root can mount         |
| async  | Asynchronous I/O            |

---

# Last Two Numbers — `0 0`

Controls **dump (backup)** and **fsck (filesystem check)**

## First Number — dump

```
0 = Disabled (commonly used)
1 = Enabled
```

---

## Second Number — fsck Order

```
0 = Skip check
1 = Root filesystem ( first root filesystem)
2 = Other filesystems (after root /)
```

---

# 🚀 Recommended (Production) — Use UUID

```fstab
UUID=xxxx-xxxx   /data   xfs   defaults   0 2
```

### Get UUID:

```bash
blkid
```

👉 Prevents issues caused by changing device names.

---

# 🌐 NFS Mount Example

```fstab
192.168.1.10:/nfsdata   /mnt/nfs   nfs   rw,sync,root_squash   0 0
```

## Common NFS Options

| Option         | Meaning                            |
| -------------- | ---------------------------------- |
| rw             | Read-write access                  |
| sync           | Immediate write                    |
| root_squash    | Root mapped to anonymous (secure)  |
| no_root_squash | Root has full access (less secure) |

---

# ⚠️ Important Boot Safety Option

## 🔹 nofail

```fstab
/dev/sdb1   /data   xfs   defaults,nofail   0 2
```

👉 System will boot even if disk is missing.

---

# 🔒 Security Hardening Example

```fstab
/dev/sdb1   /secure   xfs   defaults,nosuid,nodev,noexec   0 2
```

---

# 💡 Performance Option

```fstab
noatime
```

👉 Improves performance by disabling access time updates.

---

# 📦 Automount on Access (Advanced)

```fstab
/dev/sdb1   /data   xfs   defaults,x-systemd.automount   0 2
```

👉 Mount happens only when accessed (useful for large or slow disks)

---

# Example — Standard System Configuration

```fstab
/dev/pve/root   /       xfs   defaults   0 1
/dev/sdb1       /data   xfs   defaults,noatime   0 2
/dev/pve/swap   none    swap  sw         0 0
```

---

# ⚙️ Useful Commands

```bash
mount -a
df -h
lsblk
blkid
```

---

# ❗ Common Mistakes

* Mount point not created
* Wrong filesystem type
* Typo in fstab
* Missing nofail for external disks

---

# ✅ Best Practice

```bash
mount -a
```

👉 Always test before reboot

---

# Common Alternate Mount Options

```
noexec   → Disable execution
nosuid   → Disable SUID
nodev    → Disable device files
nofail   → Ignore missing disk at boot
noatime  → Improve performance
ro       → Read-only mount
```

---

# 🎯 Quick Interview Summary

```
The defaults option includes rw, suid, dev, exec, auto, nouser, and async.

The last two fields control dump and fsck order.

In production, UUID is preferred, and options like nofail and noatime improve reliability and performance.
```
