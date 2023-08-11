# vault-dr-k8s
Testing out running K8s with DR and PR in K8s for testing

## Setup
create a new file called `vault.hclic` in the root directory containing your Vault licence, then create a secret for this:
```bash
kubectl create secret generic vault-license --from-file=./vault.hclic
```

## Running Primary
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

Next login to vault and activate the pr
```bash
vault login

vault policy write superuser -<<EOF
path "*" {
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}
EOF
vault auth enable userpass
vault write auth/userpass/users/tester password="changeme" policies="superuser"

vault write -f sys/replication/performance/primary/enable
```

Fetching a Secondary Token **KEEP THE DEATILS OF THIS**
```bash
vault write sys/replication/performance/primary/secondary-token id=pr
```

## Running Secondary as PR
To run use the following command
```bash
kubectl apply -f ./k8s-secondary
```

Once started setup a port forward to the primary then init the secondary cluster
```bash
kubectl port-forward vault-pr 8202:8202
```

```bash
export VAULT_ADDR='http://127.0.0.1:8202'
vault operator init
```

**KEEP THE DEATILS OF THE PREVIOUS OUTPUT**
Then unseal the cluster
```bash
vault operator unseal
vault operator unseal
vault operator unseal
```

Next login to vault and activate the dr and use the token from the previous command
```bash
vault login

vault write sys/replication/performance/secondary/enable primary_api_addr= token=<token> 
```