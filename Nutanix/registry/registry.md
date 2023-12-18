oc patch configs.imageregistry.operator.openshift.io cluster --type merge --patch '{"spec":{"managementState":"Managed"}}'

oc patch configs.imageregistry.operator.openshift.io cluster --patch '{"spec":{"storage":{"pvc":"image-registry-storage"{"claim":}}}}'