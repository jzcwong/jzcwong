# Simple example of using KEDA to scale out pods every 5 minutes
This is a simple example of using KEDA to scale out pods every 5 minutes, without having to write CloudWatch queries against metrics. KEDA actually creates HPA behind the scenes for auto-scaling pods. 

KEDA changes the number of pods of an application in response to external triggers, for example, number of messages in SQS queue etc while HPA only looks at pod resource usage, for example CPU to do scaling.


## Step 1 - install KEDA
```bash
# Add KEDA helm repo
helm repo add kedacore https://kedacore.github.io/charts
helm repo update

# Create a namespace and install KEDA
kubectl create namespace keda
helm install keda kedacore/keda --namespace keda

kubectl get pods -n keda
```
---

## Step 2 - install your target application for scaling
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
      metadata:
        labels:
          app: nginx
      spec:
        containers:
        - name: nginx
          image: nginx:latest
          resources:
            requests:
              cpu: 50m
              memory: 50Mi
```
---

## Step 3 - create a ScaledObject that scales the target application every 5 minutes
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: nginx-scaledobject
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicaCount: 1
  maxReplicaCount: 3
  triggers:
  - type: cron
    metadata:
      timezone: Etc/UTC
      # Set the cron expression to trigger active scaling right now (adjust times as needed)
      start: '* * * * *'
      end: '*/5 * * * *'
      desiredReplicas: '3'
```
---

## Step 4 - verification
```bash
kubectl get hpa
NAME                          REFERENCE                     TARGETS     MINPODS   MAXPODS   REPLICAS   AGE
keda-hpa-nginx-scaledobject   Deployment/nginx-deployment   0/1 (avg)   1         3         3          2m52s


kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-669f9577b-2ltcg   1/1     Running   0          2m44s
nginx-deployment-669f9577b-bmkjx   1/1     Running   0          2m44s
nginx-deployment-669f9577b-jql8g   1/1     Running   0          7m2s


kubectl get ScaledObject
NAME                 SCALETARGETKIND      SCALETARGETNAME    MIN   MAX   READY   ACTIVE   FALLBACK   PAUSED   TRIGGERS   AUTHENTICATIONS   AGE
nginx-scaledobject   apps/v1.Deployment   nginx-deployment   1     3     True    False    False      False    cron                         3m23s
```


