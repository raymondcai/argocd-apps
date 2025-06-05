In app-of-apps.yaml, which is root app, it has source.path=apps, which shows child apps are in ./apps directory. 
In ./apps directory, which is child apps, each yaml defines its own resource path under source.path=manifests/nginx
In ./manifests/nginx, it has the actual kubernetes manifests the deploy the actual application. 

The "App of Apps" pattern in Argo CD is a powerful approach to managing multiple applications (or environments) from a single root application.

What Is "App of Apps"?
In Argo CD:

You define a parent ArgoCD Application (aka "root app") that points to a Git repository directory.

That Git repo directory contains YAMLs for other ArgoCD Applications (the "child apps").

When the parent app is synced, ArgoCD discovers and manages all those child apps.

This gives you modular, scalable GitOps: organize apps by team, env, etc., and manage them all from a single entry point.

simple manifest based directory layout:

```
argocd-apps/
├── README.md
├── app-of-apps.yaml
└── apps/
    ├── nginx-dev.yaml
    └── nginx-stage.yaml
```

Helm based layout for app-of-apps.

```
argocd-apps/
├── app-of-apps.yaml
├── apps/
│   ├── nginx-dev.yaml
│   └── nginx-stage.yaml
└── charts/
    └── nginx/
        ├── Chart.yaml
        ├── values.yaml
        └── templates/
            └── deployment.yaml
```


Common Built-in .Release Fields
Variable	Description
.Release.Name	Name of the release (Application name in Argo CD)
.Release.Namespace	Namespace Helm is installing into (not always the same as ArgoCD destination.namespace)
.Release.Service	Usually Helm, but irrelevant in ArgoCD
.Release.Revision	Incrementing number of the release version

.Release.Name is a built-in Helm template variable that represents the name of the Helm release — in the context of Argo CD, this is automatically set to the name of the Application resource.

To add ingress and nodeport, here is updated directory layout

```
charts/nginx/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    └── ingress.yaml     <-- new
```

If I want to separate dev and stage values.yaml into different directory. here is the layout.

```
app-of-apps/
├── apps/
│   ├── nginx-dev.yaml
│   └── nginx-stage.yaml
├── envs/
│   ├── dev/
│   │   └── values.yaml
│   └── stage/
│       └── values.yaml
├── charts/
│   └── nginx/       <-- Helm chart lives here
│       ├── Chart.yaml
│       ├── values.yaml        (default/base)
│       └── templates/
│           ├── deployment.yaml
│           ├── service.yaml
│           └── ingress.yaml
└── root-app.yaml
```
