# 🧾 LVM + Partitioning Notes (Quick Reference)

## 🔹 Backup fstab (Important Safety Step)

cp /etc/fstab /home/fstab_bkp

## 🔹 1. Check Disks

```bash
lsblk
# OR
fdisk -l
```

### 👉 Check Partition Table / Disk Label

```bash
parted /dev/sdb print
```

Look for:

* `Partition Table: gpt`
* OR `Partition Table: msdos`

---

## 🔹 2. Create GPT Partition (Disk: /dev/sdb)

```bash
parted /dev/sdb
```

### Inside parted prompt:

```bash
(parted) mklabel gpt        # Create/Change partition table to GPT
(parted) mkpart primary 0% 100%   # Create partition using full disk
(parted) set 1 lvm on       # Enable LVM flag on partition 1
(parted) print              # Verify partition details
(parted) quit               # Exit
```

---

## 🔹 3. Create LVM (PV → VG → LV)

### 👉 Create Physical Volume (PV)

```bash
pvcreate /dev/sdb1
pvs
```

### 👉 Create Volume Group (VG)

```bash
vgcreate vgdata /dev/sdb1
vgs
```

### 👉 Create Logical Volume (LV)

```bash
lvcreate -l 100%FREE -n lvdata vgdata
lvs
```

---

## 🔹 4. Create Mount Point & Mount

```bash
mkdir -p /data
mount /dev/vgdata/lvdata /data
```

### 👉 Get UUID (for fstab entry)

```bash
blkid /dev/vgdata/lvdata
```

---

## 🔹 5. Permanent Mount (fstab)

```bash
vim /etc/fstab
```

### 👉 Add entry like:

```
UUID=<your-uuid>   /data   xfs   defaults   0 0
```

---

## 🔥 Final Notes

* Always take **fstab backup** before changes
* Use `lsblk` to verify structure
* GPT = modern partitioning (no primary/extended concept)
* `-p` in mkdir = creates parent directories if not present

---
