# Deploy AKS Ingress Controller with Azure Key Vault integration

Previously you have configured [workload prerequisites](./12-workload-prerequisites.md). These steps configure Traefik, the AKS ingress solution used in this reference implementation, so that it can securely expose the web app to your Application Gateway.

## Expected results

* Your cluster will have an ingress controller deployed to handle ingress requests in namespace `a0005`.
* Azure Key Vault provider for Secret Store mount your ingress controller's certificate, which will be retrieved from Azure Key Vault use your ingress controller's assigned Azure AD Pod Identity.

## Steps

1. _From your Azure Bastion connection_, ensure Flux has created the following namespace.

   ```bash
   kubectl get namespace a0005
   ```

1. _From your Azure Bastion connection_, deploy your ingress controller.

   ```bash
   cd ../workload/ingress-controller
   kubectl apply -k .
   ```

1. _From your Azure Bastion connection_, wait for your ingress controller to be ready.

   > During Traefik's pod creation process, Azure AD Pod Identity will need to retrieve token for Azure Key Vault. This process can take time to complete and it's possible for the pod volume mount to fail during this time but the volume mount will eventually succeed. For more information, please refer to the [Pod Identity documentation](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/docs/pod-identity-mode.md).

   ```bash
   kubectl wait --namespace a0005 --for=condition=ready pod --selector=app.kubernetes.io/name=traefik-ingress-ilb --timeout=90s
   ```

### Next step

:arrow_forward: [Deploy the Workload](./14-workload.md)
