# Sealed Secrets

One can save all K8s configurations in git repositories except the Secrets. Since Secret (which holds sensitive data) is a base64 encoded text and can be easily decrypted, you don’t want to have them in shared git repositories. One solution to this problem is using a K8s component called SealedSecret. Quoting from its authors, i.e. `bitnami.com`: 

> Sealed Secrets are a "one-way" encrypted Secret that can be created by anyone, but can only be decrypted by the controller running in the target cluster. The Sealed Secret is safe to share publicly, upload to git repositories, post to twitter, etc. Once the SealedSecret is safely uploaded to the target Kubernetes cluster, the sealed secrets controller will decrypt it and recover the original Secret.

Let’s start with an example. If you want to add a secret to your K8s, say a password for a database and you have no idea where to start:
* You first need to install kubeseal client on your local machine, e.g. macOs: `brew install kubeseal`
* Then install the server side (i.e. cluster side) controller into your kube-system or another namespace that you want, some commands like this (better to google yourself):
```
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.16.0/controller.yaml
kubectl apply -f controller.yaml
```
* After that you are ready to generate your SealedSecret from the credential you want to encrypt, quoting from its authors, i.e. `bitnami.com`:

```
# Creates an example secret and encrypt it immediately:
kubectl --namespace $(namespace) create secret generic $(name) \
  --from-literal=$(key)=@(value) --dry-run -output json |
  kubeseal -o yaml > @(yaml_file)

# Safe to upload @(yaml_file) to git, etc.

# You can add or update exisiting sealed secrets
kubectl --namespace $(namespace) create secret generic $(name) \
  --from-literal=$(key)=@(value) --dry-run -output json |
  kubeseal -o yaml --merge-into > @(yaml_file)

# Eventually upload @(yaml_file) to cluster:
kubectl create -f @(yaml_file)

# The original secret now exists in the cluster, like magic!
kubectl get secret $(name)
```

Note that the SealedSecret and Secret must have the same namespace/name when the `--scope` flag is set to strict. See all options:

> These are the possible scopes:
> strict (default): the secret must be sealed with exactly the same name and namespace. These attributes become part of the encrypted data and thus changing name and/or namespace would lead to "decryption error".
> namespace-wide: you can freely rename the sealed secret within a given namespace.
> cluster-wide: the secret can be unsealed in any namespace and can be given any name.

Once converted into a SealedSecret, not even the original user will be able to retrieve the original Secret. kubeseal can also be run offline, without access to the cluster - it just needs a copy of the public key available on disk somewhere. I quote from documentation for more info:

> kubeseal will fetch the certificate from the controller at runtime (requires secure access to the Kubernetes API server), which is convenient for interactive use, but it's known to be brittle when users have clusters with special configurations such as private GKE clusters that have firewalls between master and nodes. An alternative workflow is to store the certificate somewhere (e.g. local disk) with `kubeseal --fetch-cert > mycert.pem`, and use it offline with `kubeseal --cert mycert.pem`. The certificate is also printed to the controller log on startup. You might need to provide controller name:  `kubeseal --fetch-cert --controller-name=sealed-secrets`

NB. SealedSecret resource and the Secret it unseals to are not exactly the same. But the SealedSecret config has a template section which encodes all fields you want the controller to put in the unsealed secret.

---
**references**
[Bitnami Sealed Secret in Kubernetes || Hands-on Tutorial](https://youtu.be/rnPqKOF05Mk)