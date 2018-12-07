# CryptoKube Proof-of-concept

## Prerequisites
- DigitalOcean account w/Kubernetes service
- Kuberenetes cluster created, env var KUBECONFIG=<path/to/kubeconfig.yaml>
- kubectl installed on management host (laptop or otherwise)

## Setup
### Bitcoin
```bash
./init.sh -c mycluster-kubeconfig.yaml -u myrpcuser -p myrpcpass # set KUBECONFIG, rpcuser+rpcpass secrets
source env.sh
kubectl cluster-info
kubectl create -f bitcoin-secrets.yaml
kubectl create configmap bitcoin-config --from-file=bitcoin.conf
kubectl create -f bitcoin-deploy.yaml
kubectl logs deploy/bitcoin --tail=5 -f
```
### Ethereum
```bash
#kubectl create -f parity-pvc.yaml
#kubectl create configmap parity-config --from-file=config.toml
kubectl create -f namespaces.yaml
kubectl create configmap -n ethereum-kovan parity-config --from-file=config.toml=parity/config.toml_kovan
kubectl create configmap -n ethereum-ropsten parity-config --from-file=config.toml=parity/config.toml_ropsten
kubectl create configmap -n ethereum-mainnet parity-config --from-file=config.toml=parity/config.toml_mainnet 
kubectl create -f parity/parity-statefulset.yaml -n ethereum-kovan
kubectl create -f parity/parity-statefulset.yaml -n ethereum-ropsten
kubectl create -f parity/parity-statefulset.yaml -n ethereum-mainnet
kubectl logs statefulset/parity --tail=5 -f
```

## Monitoring
For monitoring, alerting, and visualization I have been using [kube-prometheus](https://github.com/coreos/prometheus-operator/tree/master/contrib/kube-prometheus). It seems to cause issues with namespaces (preventing termination at least). As a workaround, I delete kube-prometheus, modify namespaces, and re-create it.

## External resources
### Bitcoin
- https://bitcoin.org/en/download
- https://jlopp.github.io/bitcoin-core-config-generator
- https://en.bitcoin.it/wiki/Running_Bitcoin#Command-line_arguments
- https://bitcoin.org/en/developer-reference
#### Docker images
- https://github.com/SatoshiPortal/dockers
- https://github.com/kylemanna/docker-bitcoind
- https://github.com/jamesob/docker-bitcoind
- https://github.com/zquestz/docker-bitcoin
- https://github.com/amacneil/docker-bitcoin (archived)
#### Kubernetes configs
- https://github.com/btc1/bitcoin/issues/128
- https://github.com/bonovoxly/gke-bitcoin-node
### Ethereum
- https://wiki.parity.io/Configuring-Parity-Ethereum
- https://paritytech.github.io/parity-config-generator
- https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options
#### Docker
- https://wiki.parity.io/Docker
#### Kubernetes/Helm
- https://github.com/carlolm/kube-parity
- https://github.com/jpoon/kubernetes-ethereum-chart
- https://github.com/helm/charts/tree/master/stable/ethereum
- https://github.com/ethersphere/swarm-kubernetes
- https://github.com/ethersphere/helm-charts
 
