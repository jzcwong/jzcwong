## Investigating AWS load balancer controller CrashLoopBackOff

Follow the guide below for installing and using AWS load balancer controller
https://docs.aws.amazon.com/eks/latest/userguide/lbc-helm.html


However hit into this issue:

```bash
kubectl get pods -A
NAMESPACE      NAME                                                     READY   STATUS             RESTARTS        AGE
kube-system    aws-load-balancer-controller-7f8c9975ff-d5xxq            0/1     CrashLoopBackOff   3 (25s ago)   82s
kube-system    aws-load-balancer-controller-7f8c9975ff-gqspt            0/1     CrashLoopBackOff   3 (23s ago)   82s
```

```bash
kubectl logs -n kube-system deployment/aws-load-balancer-controller --tail=50
  
Found 2 pods, using pod/aws-load-balancer-controller-7f8c9975ff-d5xxq
{"level":"info","ts":"2026-07-26T07:23:09Z","msg":"version","GitVersion":"v2.14.0","GitCommit":"d847890e67b4c3a78f230bd7f7caf2bfcab01df1","BuildDate":"2025-10-02T19:02:22+0000"}
{"level":"error","ts":"2026-07-26T07:23:14Z","logger":"setup","msg":"unable to initialize AWS cloud","error":"failed to get VPC ID: failed to fetch VPC ID from instance metadata: error in fetching vpc id through ec2 metadata: get mac metadata: operation error ec2imds: GetMetadata, canceled, context deadline exceeded"}
```

The reason is because the IMDS hop-count limit is set to 1 which does not allow the AWS load balancer controller pods to get information about the VPC ID from the instance meta-data. One solution is to change the hop count limit

```bash
aws ec2 modify-instance-metadata-options \
  --instance-id i-079065e855dad1986 \
  --http-put-response-hop-limit 2 
```