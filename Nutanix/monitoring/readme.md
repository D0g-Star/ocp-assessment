
## 1. Red Hat OpenShift Monitoring
To retain your metrics data, you must add persistent storage for Prometheus and AlertManager. Add a Volume Claim Template in the cluster-monitoring-config for prometheusK8s and AlertmanagerMain components.

### 1.1 Edit cluster-monitoring-config yaml to fit your size requirements
```
example:
data:
  config.yaml: |
    <component>:
      volumeClaimTemplate:
       spec:
         storageClassName: <storageclass-name>
         resources:
           requests:
             storage: <ammount of storage>

```

## References
- https://docs.openshift.com/container-platform/4.12/monitoring/configuring-the-monitoring-stack.html
- https://docs.openshift.com/container-platform/4.12/scalability_and_performance/recommended-performance-scale-practices/recommended-infrastructure-practices.html