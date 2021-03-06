# OpenFaaS - Serverless Functions Made Simple

![OpenFaaS Logo](https://blog.alexellis.io/content/images/2017/08/faas_side.png)

[OpenFaaS](https://github.com/openfaas/faas) (Functions as a Service) is a framework for building serverless functions with Docker and Kubernetes which has first class support for metrics. Any process can be packaged as a function enabling you to consume a range of web events without repetitive boiler-plate coding.

**Highlights**

* Ease of use through UI portal and *one-click* install
* Write functions in any language for Linux or Windows and package in Docker/OCI image format
* Portable - runs on existing hardware or public/private cloud - [Kubernetes](https://github.com/openfaas/faas-netes) and Docker Swarm native
* [CLI](http://github.com/openfaas/faas-cli) available with YAML format for templating and defining functions
* Auto-scales as demand increases

## Deploy OpenFaaS

**Note:** You must also pass `--set rbac=false` if your cluster is not configured with role-based access control. For further information, see [here](https://kubernetes.io/docs/admin/authorization/rbac/).

---

You can create a separate namespace for OpenFaaS core services and functions (recommended):

```
$ kubectl create ns openfaas
$ kubectl create ns openfaas-fn

$ helm upgrade --install openfaas openfaas/ \
   --namespace openfaas \
   --set functionNamespace=openfaas-fn
```

Or to use the default namespace (not recommended):

```
$ helm upgrade --install openfaas openfaas/ \
   --namespace default \
   --set functionNamespace=default
```

By default you will have NodePorts available for each service such as the API gateway and Prometheus

### Deploy with an IngressController

In order to make use of automatic ingress settings you will need an IngressController in your cluster such as Traefik or Nginx.

Add `--set ingress.enabled` to enable ingress:

```
$ helm upgrade --install openfaas openfaas/ \
    --set ingress.enabled=true
```

By default services will be exposed with following hostnames (can be changed, see values.yaml for details):
* `faas-netesd.openfaas.local`
* `gateway.openfaas.local`
* `prometheus.openfaas.local`
* `alertmanager.openfaas.local`

## Configuration

Additional OpenFaaS options.

| Parameter               | Description                           | Default                                                    |
| ----------------------- | ----------------------------------    | ---------------------------------------------------------- |
| `functionNamespace` | Functions namespace | `default` |
| `async` | Deploys NATS | `true` |
| `armhf` | Use arm images |  `false` |
| `exposeServices` | Expose `NodePorts/LoadBalancer`  | `true` |
| `serviceType` | Type of external service to use `NodePort/LoadBalancer` | `NodePort` |
| `ingress.enabled` | Create ingress resources | `false` |
| `rbac` | Enable RBAC | `false` |
| `faasnetesd.readTimeout` | Queue worker read timeout | `20` |
| `faasnetesd.writeTimeout` | Queue worker write timeout | `20` |
| `gateway.readTimeout` | Queue worker read timeout | `20` |
| `gateway.writeTimeout` | Queue worker write timeout | `20` |
| `queueWorker.ackWait` | Max duration of any async task/request | `30` |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.
See values.yaml for detailed configuration.

## Removing the OpenFaaS

All control plane components can be cleaned up with helm:

```
$ helm delete --purge openfaas
```

Individual functions will need to be either deleted before deleting the chart with `faas-cli` or manually deleted using `kubectl delete`.
