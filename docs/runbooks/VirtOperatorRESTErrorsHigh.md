# VirtOperatorRESTErrorsHigh 

## Meaning

More than 5% of the rest calls in virt-operator pods failed in the last hour.

## Impact

This alert suggests that the virt-operator is potentially lost the connection to the apiserver. Cluster-level actions such as upgrading and controller reconciliation can be delayed. Customer workloads, i.e. VM and VMI, should not be affected.

## Diagnosis

Two common types of errors may lead to this alert:
- The apiserver is overloaded and we run into timeouts. Issues like this can be identified by checking the apiserver metrics and looking at its response times, overall calls.  For users without cluster privileges, logs of KubeVirt apiserver pods can be fetched with `kubectl logs` command.
- The virt-operator pod cannot reach the apiserver. Common issues are network connectivity issues such as DNS issues on the node. Check virt-operator logs to verify whether it can connect to the apiserver at all.
    - `export NAMESPACE="$(kubectl get kubevirt -A -o custom-columns="":.metadata.namespace)"`
    - `kubectl -n $NAMESPACE get pods -l kubevirt.io=virt-operator`
    - `kubectl -n $NAMESPACE logs <pod-name>`.
    - `kubectl -n $NAMESPACE describe pod <pod-name>`.

## Mitigation

If there is indication that the virt-operator cannot connect to the apiserver, delete the pod to force a restart. In this case the issue is normally related to DNS or CNI issues outside of the scope of kubevirt.

