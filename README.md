# vault-dr-k8s
Testing out running K8s with DR and PR in K8s for testing

## Setup
create a new file called `vault.hclic` in the root directory containing your Vault licence, then create a secret for this:
```bash
    kubectl create secret generic vault-license --from-file=./vault.hclic
```