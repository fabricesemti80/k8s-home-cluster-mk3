# How to configure Ceph-Rook

## References

https://computingforgeeks.com/how-to-deploy-rook-ceph-storage-on-kubernetes-cluster/

Note: ensure to remove ceph datadir before install (check what is the path in the cluster.yaml  file at dataDirHostPath:  value)

```sh
sudo rm -r /var/lib/rook -f
```
