# Kustomize controller
Kustomize is a k8s configuration management solution that lets you customize and apply YAML files. It can be seen as an overlay engine. With Kustomize you can create a base configuration with the most common configuration of an application and use patches to apply settings that are specific for an environment. The example structure below illustrates an overlay structure. Suppose the only configuration difference between staging and production is the amount of replicas. All common configuration will be in the the base folder and the patch yaml files will only contain the correct amount of replicas.

```
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
└── overlays
    ├── staging
    │   ├── patch.yaml
    │   ├── kustomization.yaml
    └── production
        ├── patch.yaml
        ├── kustomization.yaml
```
![Kustomize overview](./kustomize.png)

With the Flux Kustomize controller you are able to watch for Kustomization objects in a Git repository. Based on the Kustomization file it will build Kubernetes manifest files, validates the build output and applies them on the cluster. Whenever objects are removed from source it will remove them from the cluster as well keeping your cluster in sync with the repository.

# Exercises
1. Create a service account and the required RBAC so Flux is able to deploy resources in your namespace.
```
kubectl create serviceaccount flux-reconciler -n {NAMESPACE}
```
```
kubectl create rolebinding flux-reconciler-admin-binding \
--clusterrole=admin \
--serviceaccount={NAMESPACE}:flux-reconciler \
-n {NAMESPACE}
```
2. Patch {NAMESPACE} in the file exercises/kustomize-controller/staging/podinfo/kustomization.yaml with your own namespace.
3. Push these changes to git and reconcile your source so these changes will be available for Flux.
```
flux reconcile source git flux-fundamentals -n {NAMESPACE}
```
4. Create the Kustomization resource.
```
flux create kustomization staging \
--service-account=flux-reconciler \
--source=flux-fundamentals \
--path="./exercises/kustomize-controller/staging" \
--prune=true \
--interval=10m \
-n {NAMESPACE}
```
> With this Kustomization Flux will look for kustomization.yaml files in the given directory and will apply the constructed Kubernetes manifests. Every 10 minutes it will sync.

5. Export this Kustomization as a yaml file.
```
flux export kustomization staging -n {NAMESPACE} > cluster/flux-staging-kustomization.yaml
```

6. Validate if there is a podinfo deployment in your namespace
```
kubectl get deployments -n {NAMESPACE}
```
7. Create a port-forward and validate in your browser (http://localhost:9898) if the application is running
```
kubectl port-forward svc/podinfo 9898
```
8. If you look at the base/podinfo/deployment.yaml you see that PODINFO_UI_COLOR has a different color code than the one that is applied at staging/podinfo/patch.yaml. Validate if the overlay is working correctly by comparing color #326ce5 with the one that is displayed in your browser.

9. In the base/podinfo/deployment.yaml file you can see that this application is configured with 1 replica. Figure out a way to apply a patch in the staging/podinfo/patch.yaml file so the amount of replicas will be 3. Push these changes to git and reconcile your Kustomization --with-source
> Be aware that you apply the setting with the same indentation as in the base.
```
flux reconcile kustomization staging --with-source -n {NAMESPACE}
```

10. Confirm you now have 3 replicas of the podinfo deployment.
```
kubectl get pods -n {NAMESPACE}
```
11. Now remove the staging/podinfo directory from your repo and push these changes to git.
12. Reconcile your Kustomization and confirm that Flux has removed your deployment because it has been removed from git.
```
flux reconcile kustomization staging --with-source -n {NAMESPACE}
```
```
kubectl get deployment -n {NAMESPACE}
```