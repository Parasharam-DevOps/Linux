# LVM + Partitioning Notes (Quick Reference)

## Scenario

Client added a 9 TB disk on a server running RHEL 8.10 and requested to mount it permanently.
To complete this task, knowledge of MBR, GPT, and LVM is required.

---

## MBR vs GPT (Short Overview)

MBR and GPT are two types of partition tables.

MBR (Master Boot Record) is the traditional partitioning scheme

* Supports up to 2 TB disk size
* Maximum 4 primary partitions

GPT (GUID Partition Table) is the modern partitioning scheme

* Supports large disks (>2 TB)
* Allows multiple partitions

fdisk is commonly used with MBR, while GPT is typically managed using parted (modern fdisk also supports GPT).

---

## What is LVM?

LVM (Logical Volume Manager) is a Linux storage management system used to create flexible logical volumes.

Advantages:

* Easy to extend storage without downtime
* Better disk management

---

## Why LVM in this Case?

Since the disk size is 9 TB, future expansion may be required.
So instead of using a normal partition, LVM is used for flexibility.

---

# Method 1: Direct Partition (Without LVM)

## 🔹 1. Check Disks

lsblk

---

## 🔹 2. Create GPT Partition

parted /dev/sdb
mklabel gpt
mkpart primary xfs 0% 100%
quit

---

## 🔹 3. Create Filesystem

mkfs.xfs /dev/sdb1

---

## 🔹 4. Create Mount Point & Mount

mkdir /data
mount /dev/sdb1 /data

---

## 🔹 5. Permanent Mount

blkid /dev/sdb1

vim /etc/fstab
UUID=xxxx   /data   xfs   defaults   0 0

mount -a

---

# Method 2: Using LVM (Recommended)

## 🔹 Backup fstab (Important Safety Step)

cp /etc/fstab /home/fstab_bkp

---

## 🔹 1. Check Disks

lsblk
fdisk -l

---

## 🔹 2. Create GPT Partition

parted /dev/sdb

(parted) mklabel gpt
(parted) mkpart primary 0% 100%
(parted) set 1 lvm on
(parted) print
(parted) quit

---

## 🔹 3. Create LVM

### 👉 Physical Volume

pvcreate /dev/sdb1
pvs

### 👉 Volume Group

vgcreate vgdata /dev/sdb1
vgs

### 👉 Logical Volume

lvcreate -l 100%FREE -n lvdata vgdata
lvs

---

## 🔹 4. Format & Mount

mkfs.xfs /dev/vgdata/lvdata

mkdir -p /data
mount /dev/vgdata/lvdata /data

---

## 🔹 5. Permanent Mount

blkid /dev/vgdata/lvdata

vim /etc/fstab
UUID=<your-uuid>   /data   xfs   defaults   0 0

mount -a

---

## Final Notes

* Always take fstab backup
* Use lsblk to verify changes
* GPT is required for disks larger than 2 TB
* LVM provides flexibility for future expansion

---
