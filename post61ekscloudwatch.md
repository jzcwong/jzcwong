# Enable Cloudwatch for EKS
You can enable the following Cloudwatch features for EKS:
- send control plane logs to Cloudwatch logs. This does not require any add-on installation
- send pod logs and cluster/k8s metrics to Cloudwatch Container insights. For this you need to enable the *Amazon CloudWatch Observability* add-on which installs the following

```bash
kubectl get pods -n amazon-cloudwatch
NAME                                                              READY   STATUS    RESTARTS   AGE
amazon-cloudwatch-observability-controller-manager-77bdccdvswxv   1/1     Running   0          53m
cloudwatch-agent-cxdjq                                            1/1     Running   0          53m
fluent-bit-6rfwl                                                  1/1     Running   0          53m
```


After receiving the metrics, you can set up CloudWatch alarms which when triggered can send emails via SNS topic etc

