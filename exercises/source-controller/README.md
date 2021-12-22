# Source controller
The main component of Flux is the source controller. It's main role is to download artifacts from external sources like Git repositories, Helm repositories or S3 buckets. It will handle authentication, detect source changes and fetch sources on-demand or on-a-schedule. The artifacts will be packaged into a well-known format and can be used by other Flux components like the helm-controller.

## Exercises
1. Create a Flux Gitrepository source with SSH by using the following command.
```
flux create source git flux-fundamentals \
    --url=https://<REPO_URL> \
    --username=<PERSONAL_ACCESS_TOKEN_NAME> \
    --password=<PERSONAL_ACCESS_TOKEN> \
    --branch=main \
    --interval=10m \
    -n <NAMESPACE>
```

> REPO_URL example > --url=https://github.com/guidalabs/gitops-fundamentals-exercises.git

2. Your source should now be added. Verify that the git source is added correctly by running:
```
flux get source git fundamentals -n <NAMESPACE>
```
3. Export this Git Source as a yaml file.
```
flux export source git flux-fundamentals -n <NAMESPACE> > cluster/flux-source.yaml
```
4. Push these changes to Git.
5. Your Git repository will be synced every 10 minutes, but you can also manually reconcile your repository by running:
```
flux reconcile source git flux-fundamentals -n <NAMESPACE>
```
6. Confirm that the fetched revision matches your latest git commit.
7. (BONUS) Create a flux git source from a public git repository.
