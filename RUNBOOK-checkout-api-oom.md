# Runbook: checkout-api OOMKilled Incident

## Diagnosis

1. List the checkout-api pods and identify the pod with a climbing restart count:

   ```bash
   kubectl get pods -n checkout-api
   ```

2. Describe the affected pod, replacing the placeholder with its exact name:

   ```bash
   kubectl describe pod <pod-name> -n checkout-api
   ```

3. Confirm the container's Last State shows `Terminated`, `Reason: OOMKilled`,
   and `Exit Code: 137`.

4. Review the previous container logs. Empty output or an unavailable-log error
   is possible because the kernel terminates the process directly.

   ```bash
   kubectl logs <pod-name> -n checkout-api --previous
   ```

5. Check the deployment's configured resource values:

   ```bash
   kubectl get deployment checkout-api -n checkout-api -o jsonpath='{.spec.template.spec.containers[0].resources}'; echo
   ```

## Resolution

```bash
sed -i 's/"8Mi"/"128Mi"/g' checkout-api-deployment.yaml
kubectl apply -f checkout-api-deployment.yaml
```