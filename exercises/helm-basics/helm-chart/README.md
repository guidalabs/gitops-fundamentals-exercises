# Setup your first helm chart

## Helm chart basics

Helm comes with an quite complete chart skeleton which you can generate with the `create` command:

```bash
helm create <chartname>
helm create myapp-chart
```

This will generate a nice chart to get started with in the `<chartname>/` folder.

If you look around in the folder you will see the following structure:
```
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

This structure is explained in depth in the Helm documentation:

[https://helm.sh/docs/topics/charts/](https://helm.sh/docs/topics/charts/)

But in short:

```
  Chart.yaml                # A YAML file containing information about the chart
  charts/                   # A directory containing any charts upon which this chart depends.
  values.yaml               # The default configuration values for this chart
  values.schema.json        # OPTIONAL: A JSON Schema for imposing a structure on the values.yaml file
  templates/                # A directory of templates that, when combined with values,
                            # will generate valid Kubernetes manifest files.
  templates/_helpers.tpl    # 'special' template file where you can define function like template structures to
                            # reuse logic between template files
  templates/NOTES.txt       # OPTIONAL: A plain text file containing short usage notes
```

When you look around in the `templates/` folder you can start to understand how the Helm (Go + Sprig) template language
works.
To see it in action you can run the command:

```bash
helm template myapp-chart
```

This will output the result of the templating helm has done. It can be useful developing the chart to sometimes just print
a single template:

```bash
helm template --show-only templates/deployment.yaml myapp-chart
```

As you can see it then just output the `deployment.yaml` template.

We can feed a value file to helm template as well to troubleshoot a non working app deployment for example:

```bash
helm template --show-only templates/deployment.yaml --values app1.yaml myapp-chart
```

Which now shows the podinfo image instead of nginx since that is what we passed in with the values file.

## Helm chart exercise networkpolicy

We should be able to deploy the helmchart and get the deployment up and running:

```bash
helm upgrade --install app1 --values app1.yaml myapp-chart
```

Now we face the same issue, there is no networkpolicy deployed so we can't access the application.
Instead of deploying this separately like the previous exercise, let's try to get the networkpolicy deployed from the helmchart.

```bash
touch myapp-chart/templates/netpol.yaml
```

Now for you the challenge to take the networkpolicy from the previous exercise and deploy that from the chart.
Remember the `helm template --show-only` and the `helm upgrade --install` commands. Good luck!

## Bonus: Helm chart exercise variable

In the `app1.yaml` file is a `#TODO` present. The UI color variable currently has no use, try to get this working in the chart.
You can set the environment variable `PODINFO_UI_COLOR` on the pod to the value of the variable to get this to work.
Here in the Kubernetes documentation you can lookup how to set these on a pod:

[https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/#define-an-environment-variable-for-a-container](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/#define-an-environment-variable-for-a-container)

Good luck! And don't be afraid to ask questions :)

Clean up by removing the installed app:

```bash
helm delete myapp
```
