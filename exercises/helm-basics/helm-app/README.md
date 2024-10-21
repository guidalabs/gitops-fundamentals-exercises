# Deploy and upgrade an app via Helm

## First deployment of podinfo

Update `podinfo.yaml` with your name and domain (replace \<YOUR_NAME\> on line 5 & 28).

Then install the app using this command:

```bash
helm install <releasename> --values <values-file> <helm repository>
```

For the app we are installing (podinfo) this command will become the following, you can change the releasename if you want
just remember to update the commands below with your choses releasename:
```bash
helm install podinfo --values podinfo.yaml oci://ghcr.io/stefanprodan/charts/podinfo
```

Check the install status of the release:
```bash
helm status <releasename>
helm status podinfo
```

See podinfo resources now installed on the cluster:

```bash
kubectl get ingress,all
```

Now when you go in the browser to the choses URL you will probably get a default ingress-nginx page.
First check if all pods are running and the helm deployment shows `deployed`

You can list the installed helm charts with the `helm list` command and check their status:
```bash
helm list
```

If that is the case then I can give you a hint to what is in the way, it is a missing networkpolicy. If you list the
networkpolicies in the namespace:

```bash
kubectl get networkpolicies
kubectl get ciliumnetworkpolicies
```

You will see a `guida-default-deny` networkpolicy which, as the name suggest by default denies all the ingress and egress
traffic from that namespace (except DNS to kube-dns).
You can inspect the policy with:

```bash
kubectl get ciliumnetworkpolicies guida-default-deny -o yaml
```

To make the podinfo pod work we need to allow the ingress-nginx pods to access the port of the podinfo app (9898)
to be able to serve it to the outside world.
There is a almost complete networkpolicy in the `netpol-app.yaml` file, see if you can get it working.

You might still get a certificate warning in the browser, although we configured a Lets Encrypt (LE) cluster-issuer. This also has to
do with networkpolicies. If you check the pods:

```bash
kubectl get pods
```

You will see a `cm-acme-http-solver-xxxxx` pod, that is the pod that serves the token for the LE verification and should be accessible
on the outside for LE to verify that we are indeed the owners of this domain.
You can get the path of the token by looking at the ingress:

```bash
kubectl get ingress --selector acme.cert-manager.io/http01-solver=true -oyaml
```

You can visit this in the browser to test:
```
http://yourdomain.tdl/.well-known/acme-challenge/<token>
```

You will see that it doesn't work, for the same reason that the app isn't working. There is a `netpol-cm.yaml` file which has a kickstart
for you the challenge to get a HTTPS verified website to show!

## Upgrade the podinfo deployment

If you now have connection to the pod in the browser, update replicas to 2 in `podinfo.yaml` and see that different instances are serving HTTP requests and run the upgrade
command:

```bash
helm upgrade podinfo --values podinfo.yaml oci://ghcr.io/stefanprodan/charts/podinfo
```

Now we did the install and upgrade separately but you can use this idempotent command which installs when the release
isn't installed yet and upgrades when the release is found:

```bash
helm upgrade --install podinfo --values podinfo.yaml oci://ghcr.io/stefanprodan/charts/podinfo
```

## Setup a second install of podinfo

The releasename is 'instance' of the chart active on the cluster. You can install the same helmchart multiple times just
by differing the releasename.
So change line 6 & 28 once more and install this app as a separate instance, we also set a variable using the imperative
`--set` argument

```bash
helm upgrade --install podinfo2 --values podinfo.yaml --set ui.color="#347C59" oci://ghcr.io/stefanprodan/charts/podinfo
```

It is advised to reference the version you are installing in the command, especially for documentation. By default it
chooses the latest version, but your values file might have been created with a different version and does not work with latest.
You can set the chart version with the `--version` flag.
The `helm list` command also shows the installed version.

```bash
helm upgrade --install podinfo2 --values podinfo.yaml --set ui.color="#347C59" --version 6.7.0 oci://ghcr.io/stefanprodan/charts/podinfo
```

When you now run the helm list command:

```bash
helm list
```

Clean up by removing the helmreleases:

```bash
helm delete podinfo
helm delete podinfo2
```
