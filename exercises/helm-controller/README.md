# Helm controller
Helm is the package manager for Kubernetes. A Helm Chart is a package that contains all the resource definitions necesarry to run an application or tool inside of a Kubernetes cluster. You can store a Helm Chart inside of a Helm Repository which can be accessed both publicly or privately. With a Helm Chart you can create a Helm Release which is an instance of a chart running in a Kubernetes cluster. When a new version of a chart is released, or when you want to change the configuration of your release, you can use Helm to upgrade your release.

The Flux Helm controller allows you to declaratively manage Helm chart releases through a Kubernetes Custom Resource named HelmRelease. The controller watches for HelmReleases, fetches artifacts produced by the source controller, watches for revision changes (including semver ranges) and performs Helm install/upgrade actions. It is also capable of performing rollbacks and retries on failed Helm install/upgrade actions. The Kustomize controller and Helm controller complement each other since you can also use overlays and deploy HelmRelease manifests with the Kustomize controller.

# Exercises
In the following exercise we are going to extend the podinfo application we deployed in the previous exercise. The podinfo Helm Chart allows you to configure Ingress so the application will be publicly available. After the deployment you are going to upgrade the application.

3. Patch \<NAMESPACE>, \<YOUR_NAME> and \<COMPANY> in the file exercises/helm-controller/development/podinfo/release.yaml with your own namespace/name/company.

4. Push these changes to git and reconcile your Git source so these changes will be available for Flux.

5. Create a Kustomization resource for the folder which holds the HelmRelease manifest
```
flux create kustomization helm-apps \
--service-account=flux-reconciler \
--source=flux-fundamentals \
--path="./exercises/helm-controller/staging" \
--prune=true \
--interval=10m
```
6. Export this Kustomization as a yaml file.
```
flux export kustomization helm-apps > cluster/flux-helm-apps-kustomization.yaml
```
7. Validate if your helmrelease is deployed correctly
```
flux get helmreleases
```
8. Your helmrelease has probably failed. Identify what has happened by looking at the events.
```
kubectl get events --sort-by=.metadata.creationTimestamp -n <NAMESPACE>
```
9. Patch the file exercises/helm-controller/development/podinfo/release.yaml to correct the issue.
10. Push these changes to git and reconcile kustomization.
```
flux reconcile kustomization helm-apps --with-source
```
11. Validate if your helmrelease is deployed correctly
```
flux get helmreleases
```
```
kubectl get pods -n <NAMESPACE>
```
12. Go to \<YOUR_NAME>.\<COMPANY>.app.guida.io and see that the application is publicly available now.

With Flux it is possible to configure a semver expression for the chart version in your HelmRelease. The Helm controller will automatically upgrade once there is a new version of your chart available.

13. Configure the semantic versioning expression ">=6.0.2 <7.0.0" in the file exercises/helm-controller/development/podinfo/release.yaml. Push these changes to git and reconcile the kustomization.
```
flux reconcile kustomization helm-apps --with-source
```

14. Confirm that the latest podinfo chart version (<7.0.0) is deployed. You can check the podinfo chart releases at: https://github.com/stefanprodan/podinfo/releases
```
flux get helmreleases -n <NAMESPACE>
```
15. Cleanup all resources
```
flux delete helmrelease podinfo
```
```
flux delete kustomization kustomize-apps
```
```
flux delete kustomization helm-apps
```
```
flux delete kustomization repositories
```
```
flux delete kustomization cluster
```
```
flux delete source helm podinfo
```
```
flux delete source git flux-fundamentals
```
```
kubectl delete serviceaccount flux-reconciler -n <NAMESPACE>
```
```
kubectl delete rolebinding flux-reconciler-admin-binding -n <NAMESPACE>
```