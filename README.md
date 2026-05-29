# Install RKE2

On All Nodes:

```bash
cat >> /etc/NetworkManager/conf.d/rke2-canal.conf << EOF
[keyfile]
unmanaged-devices=interface-name:cali*;interface-name:flannel*
EOF

systemctl reload NetworkManager
```

On First Node:

```bash
systemctl disable --now firewalld
curl -sfL https://get.rke2.io | sh -
systemctl enable rke2-server.service 
systemctl start rke2-server.service
```

On First Node:

```bash
cat >> ~/.bashrc <<EOF
export KUBECONFIG=/etc/rancher/rke2/rke2.yaml
export PATH=$PATH:/var/lib/rancher/rke2/bin:/usr/local/bin
export CRI_CONFIG_FILE=/var/lib/rancher/rke2/agent/etc/crictl.yaml
#export KU_NS=default
#alias ku="kubectl -n \\\$KU_NS"
alias ku="kubectl"
alias k="kubectl"
EOF
source ~/.bashrc

```

On First Node:
```
dnf install -y https://github.com/derailed/k9s/releases/download/v0.50.18/k9s_linux_amd64.rpm
```

On First Node:

```
cat /var/lib/rancher/rke2/server/node-token
```

On Second and Third Node:
```bash

mkdir -p /etc/rancher/rke2/

cat >> /etc/rancher/rke2/config.yaml << EOF
server: https://<your-first-node-ip>:9345
token: K104d27666dcebe13f6994516a9fee04731669182a95a44862afa11a8bd8021208e::server:f40634780a58ac1477c972806abec087
EOF
```


On Second and Third Node:
```
systemctl disable --now firewalld
curl -sfL https://get.rke2.io | sh -
systemctl enable rke2-server.service 
systemctl start rke2-server.service
```

To enable Cilium Encryption via Wireguard, kubectl apply:

```
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: rke2-cilium
  namespace: kube-system
spec:
  valuesContent: |-
    cluster:
      name: <name (cluster-matt)>
      id: <# (use 2)>
    encryption:
      enabled: true
      type: wireguard
      nodeEncryption: true
```


```
# paste the above into cilium-config.yaml  
kubectl apply -f cilium-config.yaml  
```