# Configuring default Ingress SSL Certificate

## 1. Import CA Bundle
Follow the instruction to import/trust a custom PKI(CA-Bundle)
* Wait for Nodes to update with new CA Bundle

## 2. Create the Secret
```
oc create secret tls default-ingress-cert --cert=certs/wildcard.crt --key=certs/cert.key -n openshift-ingress
```

## 3. Update the Ingress Controller
```
oc patch ingresscontroller.operator default --type=merge -p '{"spec":{"defaultCertificate": {"name": "default-ingress-cert"}}}' -n openshift-ingress-operator
```

## 4.  Scaling the Ingress Router pods

Run the following to scale the ingress routers to the desired number. Usually the number of workers.
```
oc patch -n openshift-ingress-operator ingresscontroller/default --patch '{"spec":{"replicas": 3}}' --type=merge
ingresscontroller.operator.openshift.io/default patched
```

## 5. Check the status
```
oc get -o yaml deployment/router-default -n openshift-ingress | grep replicas
```

## References
  * https://access.redhat.com/solutions/5393521
    
  * https://docs.openshift.com/container-platform/4.12/security/certificates/replacing-default-ingress-certificate.html
