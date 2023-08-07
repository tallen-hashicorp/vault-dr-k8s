# vault-dr-k8s
Testing out running K8s with DR and PR in K8s for testing

## Setup
create a new file called `vault.hclic` in the root directory containing your Vault licence, then create a secret for this:
```bash
    kubectl create secret generic vault-license --from-file=./vault.hclic
```

## Running
To run use the following command
```bash
kubectl apply -f ./k8s-primary
```

Once started setup a port forward to the primary then init the cluster
```bash
kubectl port-forward vault-primary 8201:8201
```

```bash
export VAULT_ADDR='http://127.0.0.1:8201'
vault operator init
```

**KEEP THE DEATILS OF THE PREVIOUS OUTPUT**
Then unseal the cluster
```bash
vault operator unseal
vault operator unseal
vault operator unseal
```