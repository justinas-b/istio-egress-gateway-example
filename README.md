# istio-egress-gateway-example
This repo contains examples on how to deploy and configure istio egress gateway.

There are two ways of deploying egress gateway:
 - egress gateway will reside in istio-system namespace
 - egress gateway will reside in dedicated istio-egress namespace

# Deploy istio and egress gateway into same namespace

To deploy istio and it's egress gateway into same namespace, run:

```bash
kubectl create namespace istio-system
helm install istio-base istio/base -n istio-system --set defaultRevision=default
helm install istiod istio/istiod -n istio-system --set meshConfig.outboundTrafficPolicy.mode=REGISTRY_ONLY
helm install istio-egress-gateway istio/gateway -n istio-system --create-namespace --wait --set service.type=ClusterIP
```

# Deploy istio and egress gateway into separate namespace

To deploy istio and it's egress gateway into same namespace, run:

```bash
kubectl create namespace istio-system
helm install istio-base istio/base -n istio-system --set defaultRevision=default
helm install istiod istio/istiod -n istio-system --set meshConfig.outboundTrafficPolicy.mode=REGISTRY_ONLY
helm install istio-egress-gateway istio/gateway -n istio-egress --create-namespace --wait --set service.type=ClusterIP
```

# Deploy sleep application

This application could be used to test access to external resources through egress gateway

```bash
kubectl create namespace sleep
kubectl label namespaces sleep istio-injection=enabled --overwrite
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/sleep/sleep.yaml -n sleep
export SOURCE_POD=$(kubectl get pod -l app=sleep -o jsonpath={.items..metadata.name} -n sleep)
```

# Deploy local httpbin app

```bash
kubectl create namespace httpbin
kubectl label namespaces httpbin istio-injection=enabled --overwrite
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/httpbin/httpbin.yaml -n httpbin
```
