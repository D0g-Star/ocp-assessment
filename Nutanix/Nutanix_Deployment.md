
ssh-keygen -t ed25519 -N '' -f ~/.ssh/ocp_test
cp pc.crt 
/etc/pki/ca-trust/source/anchors
update-ca-trust extract
./openshift-install create install-config --dir ocp_test
oc adm release extract --credentials-requests --cloud=nutanix \// --to=ocp_test/credrequests \ quay.io/openshift-release-dev/ocp-release:4.11.38-x86_64
./ccoctl nutanix create-shared-secrets \
 --credentials-requests-dir=home/ocp_user/ocp_test/credrequests \
 --output-dir=home/ocp_user/ocp_test \
 --credentials-source-filepath=home/ocp_user/ocp_test/credentials.yaml
 
#backup install-config.yaml
cp install-config.yaml install-config.yaml-backup
openshift-install create manifests

./openshift-install create install-config

./openshift-install create cluster --log-level=info
