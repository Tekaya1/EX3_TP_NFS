
# 🖧 Ex3 TP LINUX
---



---

## 🖥️ On `serverb` (NFS Server)

### 1. Install NFS server packages
```bash
 dnf install -y nfs-utils
 systemctl enable --now nfs-server
```

### 2. Create and prepare the shared directory
```bash
 mkdir -p /shares/public
 groupadd admin  # if not already existing
 chgrp admin /shares/public
 chmod 2775 /shares/public
```

### 3. Create some test files
```bash
 touch /shares/public/test1.txt /shares/public/test2.txt
```

### 4. Create and configure users
```bash
 useradd admin1
 useradd sysmanager1
 usermod -aG admin admin1
 usermod -aG admin sysmanager1
```

### 5. Configure `/etc/exports`
```bash
echo "/shares/public servera(rw,sync,no_root_squash)" |  tee -a /etc/exports
```

---

## 💻 On `servera` (NFS Client)

### 1. Install NFS client utilities
```bash
 dnf install -y nfs-utils
```

### 2. Create the mount point
```bash
 mkdir -p /public
```

### 3. Create and configure user
```bash
 useradd admin1
 passwd  admin1
 groupadd admin 
 usermod -aG admin admin1
```

### 4. Temporarily mount the share
```bash
 mount -t IP:/shares/public /public
```

### 5. Fix permissions if access is denied
If `admin1` gets “Permission denied”:
```bash
 chown root:admin /public
 chmod 2775 /public
```

Ensure `admin1` is in `admin` group:
```bash
groups admin1
```

If not:
```bash
 usermod -aG admin admin1
su - admin1  # refresh session
```

### 6. Test the share
```bash
su - admin1
cd /public
touch test_admin1.txt
ls -l
```

---

## 📌 Permanent Mount Configuration

### Edit `/etc/fstab` on `servera`:
```bash
 nano /etc/fstab
```

Add this line:
```
serverb:/shares/public   /public   nfs   defaults  0 0
```

Then run:
```bash
 umount /public
 mount -a
```

---

## ✅ Final Test

As user `admin1`:
```bash
su - admin1
cd /public
touch final_test.txt
echo "test OK" > final_test.txt
cat final_test.txt
```

You should be able to read/write without error.

---
