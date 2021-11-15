# VPN gateway setup

## Preparation

- In order to have a VPN gateway, we have to have a VPN account. My choosen one is NordVPN, as it supports [openVPN](https://nl.wikipedia.org/wiki/OpenVPN).

- we will also need the app user and app password (can be different for other VPN types)

- we also need a connection file; for me this comes from [here](https://nordvpn.com/ovpn/)

## Create a secret

The values from the previous step should be saved into a secret.

![secret](images/2021-11-15%2016_28_02-WSL_%20Ubuntu%20_%20vpn_secret.png)

- the VPN_AUH: should contain the "appusername;apppassword" values

- the vpnConfigfile: should contain the contents of the config file (following the |- sign)

- this file then should be encrypted

```sh
sops --encrypt --in-place "/home/fabrice/k8s-home-cluster-mk3/cluster/apps/vpn-gateway/secret.sops.yaml"
```

## Add settings

The following, not secret values should also be stored, for example in the clsuter-settings.yaml:

```yaml
#
  K8S_CLUSTER_CIDR: "10.42.0.0/16"
  K8S_SERVICE_CIDR: "10.43.0.0/16"
  #
  NETWORK_MANAGEMENT_CIDR: "192.168.0.0/16" # LAN
  #
  VPN_GATEWAY_PORT: "1194"
  VPN_PORT_QB: "56059"
```

## See the helm-release for further details.

TBC

## Verify

If the setup was succesfull, pods that connect to this VPN gateway should should other country IP-s than the one we are in.

![check](images/2021-11-15%2017_18_10-Lens_vpn_check.png)

(my geolocation is in fact UK, but as seen, the VPN breakes out in Ireland)

## Ref

<https://github.com/aka-raccoon/raccoons-burrow/tree/96cb74f38171bcd21bc532509b415f6d77c34cc9/cluster/apps/vpn-gateway>
