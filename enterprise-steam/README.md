# Enterprise Steam

![Version: 1.9.0](https://img.shields.io/badge/Version-1.9.0-informational?style=flat-square)

Enterprise Steam is deployer and manager of H2O.ai products on Kubernetes.

**Homepage:** <https://www.h2o.ai>

## Prerequisites
- Kubernetes 1.10+
- Helm 2.11+
- PV provisioner support in the underlying infrastructure

## Downloading the chart
Latest version of the chart is always available on the Enterprise Steam [download page](https://s3.amazonaws.com/steam-release/enterprise-steam/latest-stable.html).

## Installing the Chart
To install the chart with the release name `my-release`:

```console
helm install my-release ./enterprise-steam-1.9.0.tgz
```
Alternatively, a YAML file that specifies the values can be provided while installing the chart.

```console
helm install my-release -f values.yaml ./enterprise-steam-1.9.0.tgz
```

The command deploys Enterprise Steam on the Kubernetes cluster in the default configuration.
The Values section lists the values that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Common configuration

Here is a list of common configurations.
Feel free to combine them and see the full list of values below.

Install Enterprise Steam into h2o namespace:

```console
helm install my-release ./enterprise-steam-1.9.0.tgz \
  --namespace h2o
```

Set custom Enterprise Steam docker image name and tag:

```console
helm install my-release ./enterprise-steam-1.9.0.tgz \
  --set image.repository=myrepo/enterprise-steam \
  --set image.tag=1.7.3
```

Set custom Enterprise Steam storage:

```console
helm install my-release ./enterprise-steam-1.9.0.tgz \
  --set persistentVolume.size=256Gi
```

Set custom Enterprise Steam resources:

```console
helm install my-release ./enterprise-steam-1.9.0.tgz \
  --set resources.limits.cpu=2 \
  --set resources.limits.memory=32Gi \
  --set resources.requests.cpu=1 \
  --set resources.requests.memory=8Gi
```

Set strict launch mode for Steam. In this mode, Steam pod fails to launch if OIDC or Kubernetes services won't start:

```console
helm install my-release ./enterprise-steam-1.9.0.tgz \
  --set strictLaunch=true
```

Spawn a Load Balancer for Enterprise Steam:

```console
helm install my-release ./enterprise-steam-1.9.0.tgz \
  --set service.type=LoadBalancer
```

## Ingress example

This advanced example shows Enterprise Steam exposed via a TLS secured Kubernetes Ingress.

- `my-storage-class` is the StorageClass name that Steam will use to provision it's storage
- `steam.mycluster.mycompany.com` is the hostname where Enterprise Steam will be exposed
- `ingress-wildcard-cert` is the name of Kubernetes Secret that contains TLS certificate valid for `steam.mycluster.mycompany.com` domain

```yaml
persistentVolume:
  storageClassName: "my-storage-class"

ingress:
  enabled: true
    hosts:
      - host: steam.mycluster.mycompany.com
        paths: ["/"]

  tls:
    - secretName: ingress-wildcard-cert
  hosts:
    - steam.mycluster.mycompany.com
```

Save the file as `steam-config.yaml` and run:

```console
helm install -f steam-config.yaml my-release ./enterprise-steam-1.9.0.tgz
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Deployment affinity.  |
| caCertificates | string | `""` | Optional raw CA certificate PEM file to be added to the container.  |
| containerSecurityContext | object | `{"allowPrivilegeEscalation":false}` | Container security context.  |
| extraEnv | list | `[]` | Extra 'env' passed to the container(s).  |
| fullnameOverride | string | `""` | If you need override the fully qualified app name.  |
| image.pullPolicy | string | `"Always"` | Docker image pull policy.  |
| image.pullSecrets | list | `[]` | Optional list of references to secrets in the same namespace to use for pulling the image  |
| image.repository | string | `"h2oai/enterprise-steam"` | Application Docker repository.  |
| image.tag | string | `""` | Application Docker tag / version. Defaults to the chart appVersion.  |
| ingress.annotations | object | `{}` | Ingress annotations. |
| ingress.className | string | `""` | IngressClass name |
| ingress.enabled | bool | `false` | Ingress enabled.  |
| ingress.hosts | list | `[{"host":"enterprise-steam.cluster.local","paths":["/"]}]` | Set Ingress host and paths. |
| ingress.tls | list | `[]` | Ingress TLS setting. Optionally enable TLS for Ingress. |
| nameOverride | string | `""` | If you need to override the name of the chart from 'enterprise-steam' to something else.  |
| nodeSelector | object | `{}` | Deployment node selector.  |
| persistentVolume.accessModes | list | `["ReadWriteOnce"]` | PersistentVolume access modes. Must match those of existing PV or dynamic provisioner. |
| persistentVolume.annotations | object | `{}` | PersistentVolumeClaim annotations.  |
| persistentVolume.existingClaim | string | `""` | Set to use an existing PersistentVolumeClaim. If left empty, a new PersistentVolumeClaim will be created.  |
| persistentVolume.resourcePolicy | string | `""` | Setting it to "keep" to avoid removing PVCs during a helm delete operation. Leaving it empty will delete PVCs after the chart is deleted. |
| persistentVolume.size | string | `"64Gi"` | PersistentVolume Size.  |
| persistentVolume.storageClassName | string | `""` | StorageClass name. If left empty, no storageClassName spec is set, choosing the default provisioner (gp2 on AWS, standard on GKE, etc..).  |
| podAnnotations | object | `{}` | Deployment Pod annotations.  |
| podLabels | object | `{}` | Deployment Pod labels.  |
| podSecurityContext | object | `{"fsGroup":955,"runAsGroup":955,"runAsUser":955}` | Deployment/Pod security context.  |
| resources | object | `{"limits":{"cpu":2,"memory":"8Gi"},"requests":{"cpu":2,"memory":"8Gi"}}` | Resources requested for Enterprise Steam Pod. Please adjust them as you like. Listed is the minimum spec. |
| service.annotations | object | `{}` | Service annotations. Includes example for use with LoadBalancer service type. |
| service.loadBalancerIP | string | `""` | LoadBalancer IP. Ignored if the type is not LoadBalancer or if the IP is empty string.  |
| service.name | string | `""` | Service name is user-configurable for maximum service discovery flexibility. Leave empty for default Service name.  |
| service.port | int | `9555` | Service port.  |
| service.type | string | `"ClusterIP"` | Service type.  |
| serviceAccount.allowClusterRolePrivileges | bool | `false` | Set to grant Enterprise Steam read access to some cluster-wide resources. See rbac.tpl for detail on what RBAC privileges are granted.  |
| serviceAccount.annotations | object | `{}` | Annotations to add to the created ServiceAccount.  |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created. If you choose to provide an existing ServiceAccount make sure it has all necessary roles.  |
| serviceAccount.name | string | `""` | The name of the service account to use. If not set and create is true, a name is generated.  |
| serviceAccount.openshift | bool | `false` | Creates special apiGroups required in case of an OpenShift deployment.  |
| serviceAccount.openshiftResourceNames | list | `["privileged"]` | Configurable resourceNames for OpenShift apiGroups.  |
| strictLaunch | bool | `false` | Optional flag to set strict launch for Steam. If set to true, Steam pod launch will fail if OIDC or kubernetes services fail to initialize  |
| tolerations | list | `[]` | Deployment tolerations.  |
| volumeMounts | list | `[]` | Deployment Pod volume mounts.  |
| volumes | list | `[]` | Additional deployment Pod volumes.  |

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| Ondrej Bilek | <ondrej@h2o.ai> |  |