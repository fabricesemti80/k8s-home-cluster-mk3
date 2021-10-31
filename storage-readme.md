# How to configure Ceph-Rook

## References

https://computingforgeeks.com/how-to-deploy-rook-ceph-storage-on-kubernetes-cluster/

* Ensure to remove ceph datadir before install (check what is the path in the cluster.yaml  file at dataDirHostPath:  value)

```sh
sudo rm -r /var/lib/rook -f
```

* The storage disks allso need to be "zapped" - https://github.com/rook/rook/issues/3116

```sh
#!/usr/bin/env bash
DISK="/dev/sdb"
# Zap the disk to a fresh, usable state (zap-all is important, b/c MBR has to be clean)
# You will have to run this step for all disks.
sgdisk --zap-all $DISK
```

## iSCSI

https://linuxhint.com/iscsi_storage_server_ubuntu/

Set target connection automatic

https://askubuntu.com/questions/499246/iscsi-auto-startup-at-boot-14-04