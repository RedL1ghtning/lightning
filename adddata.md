1. Create pv, vg and lv
```
pvcreate /dev/sdb
vgcreate data /dev/sdb
lvcreate -n lv_data -l +100%FREE /dev/data
```
2. Make FS for volume
```
mkfs.xfs -n ftype=1 /dev/data/lv_data
```
3. Add line to /etc/fstab
```
echo "/dev/mapper/data-lv_data /data                       xfs     defaults        0 0" | sudo tee -a /etc/fstab >/dev/null
```
4. Make directory and mount them
```
mkdir /data
mount /data
```
