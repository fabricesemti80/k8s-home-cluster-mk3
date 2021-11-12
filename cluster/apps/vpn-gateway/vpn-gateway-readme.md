# Setup instructions

As I am using NordVPN, I need to use [this addon](https://github.com/dperson/openvpn-client)
with the [pod-gateway](https://github.com/k8s-at-home/charts/tree/master/charts/stable/pod-gateway)
chart. The inspiration for this was
[this repo](https://github.com/aka-raccoon/raccoons-burrow/tree/96cb74f38171bcd21bc532509b415f6d77c34cc9/cluster/apps/vpn-gateway).

The following values should be created in the cluster secrets:

- K8S_CLUSTER_CIDR: "10.42.0.0/16" *- these are kubernetes defaults*

- K8S_SERVICE_CIDR: "10.43.0.0/16" *- these are kubernetes defaults*

- NETWORK_MANAGEMENT_CIDR: "192.168.0.0/16" *- this should be the local network*

- VPN_GATEWAY_PORT: "1194"
- VPN_PORT_QB: "56059"

We will also need a secret:

```yaml
apiVersion: v1
kind: Secret
metadata:
    name: vpn-gateway-vpnconfig
    namespace: vpn-gateway
stringData:
    VPN_AUTH: xxxxx;yyyyy
    vpnConfigfile: |-
    blahblahblah
    blahblahblah
```

- xxxxx is your (NordVPN) app username
- yyyyy is your (NordVPN) app username
- blahblahblah is your openvpn config (pick one from
[here](https://nordvpn.com/ovpn/) )

Once all is set up, check from the pod
connected to the vpn gateway, if it seen (by public IP)
as a different country, for example doing a:

```sh
curl ipinfo.io
```

(the country should match to the downloaded config file)
