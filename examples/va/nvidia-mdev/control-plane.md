# Configuring networking and deploy the OpenStack control plane

## Assumptions

- A storage class called `local-storage` should already exist.

## Initialize

Switch to the "openstack" namespace
```
oc project openstack
```
Change to the nvidia-mdev directory
```
cd architecture/examples/va/nvidia-mdev
```
Edit the [control-plance/nncp/values.yaml](control-plane/nncp/values.yaml) and
[control-plane/service-values.yaml](control-plane/service-values.yaml) files to suit 
your environment.
```
vi nncp/values.yaml
vi service-values.yaml
```

## Apply node network configuration

Generate the node network configuration
```
kustomize build control-plane/nncp > nncp.yaml
```
Apply the NNCP CRs
```
oc apply -f nncp.yaml
```
Wait for NNCPs to be available
```
oc wait nncp -l osp/nncm-config-type=standard --for jsonpath='{.status.conditions[0].reason}'=SuccessfullyConfigured --timeout=300s
```

## Apply networking and control-plane configuration

Generate the control-plane and networking CRs.
```
kustomize build control-plane > control-plane.yaml
```
Apply the CRs
```
oc apply -f control-plane.yaml
```

Wait for control plane to be available
```
oc wait osctlplane controlplane --for condition=Ready --timeout=600s
```
