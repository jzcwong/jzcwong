## What is External Secrets Operator?
The purpose of the External Secrets Operator (ESO) is to synchronize secrets from external secret management systems (like AWS Secrets Manager, HashiCorp Vault, Azure Key Vault, and Google Secret Manager) into native Kubernetes Secret objects.

### Core features and benefits:
- Single Source of Truth: Keep your sensitive data (passwords, API keys, certificates) stored and rotated centrally in an external vault
- Automatic Synchronization: Periodically fetch updates from the external API and automatically reconcile or update the corresponding Kubernetes Secrets without manual intervention

## Demo example
In this example, a JSON array of secret key-value pairs is created as a secret in AWS Secrets Manager. External Secrets Operator is installed on an EKS cluster with IRSA permissions to allow reading of secrets from Secrets Manager. ESO is configured to pull this secret from AWS Secrets Manager and configure it as a native K8S generic opaque secrets object. 

Note that at the end of the day, ESO will still help to create a K8S secrets object which is the base64 encoded value of the secret.

### Step 1 - create the secret in AWS Secrets Manager
Navigate to AWS Secrets Manager in your chosen region, and create a plaintext secret with the following JSON contents as an example. Here the name of the secret is called ***gharc-defined-secret***

```json
{
  "github_app_id": "111222",
  "github_app_installation_id": "222333445",
  "github_app_private_key": "-----BEGIN RSA PRIVATE KEY-----MIIEpAI-----END RSA PRIVATE KEY-----"
}
```

### Step 2 - create IRSA for the EKS Cluster to allow ESO to access Secrets Manager to retrieve the secret

Here we create OIDC identity provider for the EKS cluster inside IAM, then we create a role with the following trust policy:

```json
{
  "Version":"2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<YOUR_AWS_ACCOUNT_ID>:oidc-provider/oidc.eks.<YOUR_REGION>.amazonaws.com/id/<YOUR_OIDC_ID>"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "oidc.eks.<YOUR_REGION>.amazonaws.com/id/<YOUR_OIDC_ID>:aud": "sts.amazonaws.com",
          "oidc.eks.<YOUR_REGION>.amazonaws.com/id/<YOUR_OIDC_ID>:sub": "system:serviceaccount:<SERVICE_ACCOUNT_NAMESPACE>:<SERVICE_ACCOUNT_NAME>"
        }
      }
    }
  ]
}
```

and the following built-in AWS permissions policy ***AWSSecretsManagerClientReadOnlyAccess***


### Step 3 - create the service account in the right namespace
The service account should be created in the namespace where you want to install ESO

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::<AWS_ACCOUNT_ID>:role/<IAM_ROLE_CREATED_IN_STEP2>
  name: <SERVICE_ACCOUNT_NAME>
  namespace: <NAMESPACE>
```

### Step 4 - install ESO
Here we install ESO into the default namespace. The rest of the steps assume that the default namespace is used

```bash
helm repo add external-secrets https://charts.external-secrets.io

helm install external-secrets \
   external-secrets/external-secrets \
    -n default
```

### Step 5 - create the secretStore and externalSecret
The ***secretStore**** is used to specify the name of the secret in Secrets Manager, as well as the service account to used for authenticating to gain access to Secrets Manager. The ***externalSecret*** is a template for the actual secret retrieved from Secrets Manager. 

```yaml
apiVersion: external-secrets.io/v1
kind: SecretStore
metadata:
  name: gharc-defined-secret #name of the secret in secrets manager
  namespace: default
spec:
  provider:
    aws:
      service: SecretsManager
      region: us-east-1
      auth:
        jwt:
          serviceAccountRef:
            name: gh-arc #name of the service-account used for IRSA			
---
apiVersion: external-secrets.io/v1
kind: ExternalSecret
metadata:
  name: gh-arc-eso #name of this externalSecret template
  namespace: default
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: gharc-defined-secret #name of secretStore defined in earlier section
    kind: SecretStore
  target:
    name: gh-arc-eso #name of k8s secret created
    creationPolicy: Owner
  dataFrom:
      - extract:
          key: gharc-defined-secret
```

Then ***kubectl apply -f*** for the above yaml manifiest

### Step 5 - verify

```bash
kubectl get secretstores.external-secrets.io            
NAME                   AGE   STATUS   CAPABILITIES   READY
gharc-defined-secret   16m   Valid    ReadWrite      True
```

```bash
kubectl get externalsecrets.external-secrets.io 
NAME         STORETYPE     STORE                  REFRESH INTERVAL   STATUS         READY   LAST SYNC
gh-arc-eso   SecretStore   gharc-defined-secret   1h                 SecretSynced   True    2m35s
```

```bash
It also generated the k8s secret for you

kubectl get secret
NAME                                     TYPE                 DATA   AGE
external-secrets-webhook                 Opaque               4      39m
gh-arc-eso                               Opaque               3      8m23s
sh.helm.release.v1.external-secrets.v1   helm.sh/release.v1   1      39m
```

Below are base64 encoded values of the secret
```bash
kubectl get secret gh-arc-eso -o yaml
apiVersion: v1
data:
  github_app_id: NTI11D10aNA==
  github_app_installation_id: MTY5Nzk2NDQ1a
  github_app_private_key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLU1JSUVwQUktLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQ==
kind: Secret
```