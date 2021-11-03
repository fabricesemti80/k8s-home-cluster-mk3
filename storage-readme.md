# How to configure Ceph-Rook

## :bricks: Manual tasks

  **Ensure to remove ceph datadir before install (check what is the path in the cluster.yaml  file at dataDirHostPath:  value) if you ever had rook on the worker nodes!**

```sh
sudo rm -r /var/lib/rook -f
```

* The storage disks allso need to be "zapped"

```sh
#!/usr/bin/env bash
DISK="/dev/sdb"
# Zap the disk to a fresh, usable state (zap-all is important, b/c MBR has to be clean)
# You will have to run this step for all disks.
sgdisk --zap-all $DISK
```

## 🧑‍💼 iSCSI reference (manual setup)

* Set target connection automatic

* Manage OSD-s (from toolbox pod)

### 🤖 iSCSI reference (Ansible)

* make sure the module is installed

* get the iscsi target names (prepare them on your iscsi server, for me this is a Qnap NAS)

* update the iSCSI-related settings in the Ansible task file (./.taskfiles/ansible.yml)

```yaml
  # these are the iSCSI targets for each node
  WORKER1: "k8s-1"
  WORKER2: "k8s-2"
  ISCSI1: "iqn.2004-04.com.qnap:ts-809:iscsi.k8s1.c50b93"
  ISCSI2: "iqn.2004-04.com.qnap:ts-809:iscsi.k8s2.c50b93"
  # this should be your NAS or iSCSI server
  PORTAL: "192.168.1.3"
  ```

Then run the task

```sh
task ansible:iscsi
```

Once this done, reboot the workers and check if the iSCI disk is still present

```sh
ubuntu@k8s-2:~$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0    7:0    0  55.4M  1 loop /snap/core18/2128
loop1    7:1    0  70.3M  1 loop /snap/lxd/21029
loop2    7:2    0  32.3M  1 loop /snap/snapd/12704
sda      8:0    0 111.8G  0 disk
├─sda1   8:1    0     1M  0 part
└─sda2   8:2    0 111.8G  0 part /
sdb      8:16   0   500G  0 disk
sr0     11:0    1   128M  0 rom
ubuntu@k8s-2:~$
```

(in my case sdb is an 500 GB iSCSI disk)

*This should be done **before** the cluster install!*

## 📋 Access dashboard

Once Rook is installed, retrieve pw

```sh
kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo
```

Then access it using 'admin' + this password

## References

<https://github.com/rook/rook/blob/master/Documentation/ceph-cluster-crd.md>

<https://computingforgeeks.com/how-to-deploy-rook-ceph-storage-on-kubernetes-cluster/>

<https://github.com/rook/rook/issues/3116>

<https://linuxhint.com/iscsi_storage_server_ubuntu/>

<https://askubuntu.com/questions/499246/iscsi-auto-startup-at-boot-14-04>

<https://docs.ceph.com/en/latest/rados/operations/add-or-rm-osds/>

<https://docs.ansible.com/ansible/latest/collections/community/general/open_iscsi_module.html>
