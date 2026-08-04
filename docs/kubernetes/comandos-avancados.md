# 📘 Comandos Avançados do Kubernetes

> Guia para usuários que já dominam o básico e querem RBAC, Network Policies, Pod Autoscaling, Helm, segurança e observabilidade

---

## 🎛️ Manifestos e Kubectl Avançado

* `Filtros e custom columns`

```bash
# Custom columns (saída limpa)
kubectl get pods -o custom-columns=NAME:.metadata.name,IP:.status.podIP

# JSON/YAML
kubectl get pod meu-pod -o json | jq '.status.conditions[] | select(.type=="Ready")'

# Prometheus-style
kubectl get --raw /api/v1/nodes
kubectl get --raw /apis/metrics.k8s.io/v1beta1
```

## 🔐 RBAC — Acesso e Controle

* `ServiceAccount, Role, RoleBinding`

```yaml
# serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
  namespace: dev
```

```yaml
# role.yaml — permissões no namespace
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata: { name: app-role, namespace: dev }
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch", "create", "delete"]
```

```yaml
# rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata: { name: app-bind, namespace: dev }
subjects:
  - kind: ServiceAccount
    name: app-sa
    namespace: dev
roleRef:
  kind: Role
  name: app-role
  apiGroup: rbac.authorization.k8s.io
```

```bash
# Aplicar e verificar
kubectl apply -f role.yaml
kubectl auth can-i get pods --as=system:serviceaccount:dev:app-sa
```

## 🛡️ Network Policy

```yaml
# netpol.yaml — isolar tráfego de um app
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: web-allow
  namespace: dev
spec:
  podSelector:
    matchLabels: { app: web }
  policyTypes: ["Ingress"]
  ingress:
    - from:
        - podSelector: { app: api }
      ports:
        - protocol: TCP
          port: 8080
```

```bash
kubectl apply -f netpol.yaml
# requer CNI que suporte (Calico, Cilium)
```

## 📈 HPA e VPA

* `HorizontalPodAutoscaler`

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata: { name: meu-app, namespace: dev }
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: meu-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 60
```

```bash
kubectl get hpa
kubectl describe hpa meu-hpa
```

## 🗃️ Secrets e Config

* `Secrets criptografados e env`

```bash
# Criar a partir de arquivo/literal
kubectl create secret generic db-pass --from-literal=pass=abc123
kubectl create secret generic tls --from-file=tls.crt=./cert.crt

# Referenciar via env em deployment
# envFrom:
#   - secretRef: { name: db-pass }
```

* `Helm (gerenciador de charts)`

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm search repo bitnami/nginx
helm install meu-app bitnami/nginx
helm upgrade meu-app bitnami/nginx
helm rollback meu-app 1
helm list --all-namespaces
helm uninstall meu-app
helm template meu-app bitnami/nginx   # renderizar sem instalar
```

## 🔁 StatefulSets e Volumes

```bash
# Volumes
kubectl get pvc
kubectl get pv
kubectl describe pvc meu-pvc

# Persistir
kubectl run app --image=nginx \
  --dry-run=client -o yaml > pod.yaml   # gerar manifest
```

## 🔍 Observabilidade

* `Metrics`

```bash
# Métrica de CPU/memória (requer metrics-server)
kubectl top nodes
kubectl top pods -n dev

# Descrever com eventos
kubectl describe pod meu-pod
kubectl get events --field-selector involvedObject.name=meu-pod
```

* *Troubleshoot*

```bash
# Ver logs com prefixo de tempo
kubectl logs meu-pod --timestamps
kubectl logs meu-pod -f --prefix

# Executar debug container
kubectl debug node/node01 -it --image=ubuntu
```

## 🔀 Rollouts e Canary

```bash
# Deploy escalonado (não reiniciar tudo)
kubectl rollout restart deployment/meu-app --namespace=dev

# Status e histórico
kubectl rollout history deployment/meu-app
kubectl rollout status deployment/meu-app
kubectl rollout undo deployment/meu-app --to-revision=2
```

## 🗂️ Multi-cluster (kubeconfig)

```bash
# Mesclar múltiplos kubeconfigs
export KUBECONFIG=/etc/kubernetes/admin.conf:./kubeconfig-prod
kubectl config view
kubectl config rename-context nome-antigo nome-novo
```

## 📦 Helm Values e Parametrizar

```bash
# Customizar install com values
helm install meu-app bitnami/nginx -f values.yaml
helm upgrade meu-app bitnami/nginx --set replicaCount=3
helm show values bitnami/nginx
```

## 🧹 Limpeza e Manutenção

```bash
# Remover recursos por label
kubectl delete pods -l app=web
# Forçar remoção de namespace (cuidado: pode deixar recursos órfãos)
kubectl delete ns dev --force --grace-period=0

# Ver orçamento de pods do deployment
kubectl get po --field-selector=status.phase=Failed -A
```

## ✅ Checklist K8s Avançado

| Tema | Comando |
| ---- | ------- |
| RBAC | `kubectl auth can-i` |
| NetworkPolicy | `apply -f` |
| HPA | `kubectl get hpa` |
| Metrics | `kubectl top` |
| Helm | `helm install/upgrade` |
| Rollout | `kubectl rollout undo` |
| Segurança | `kubectl auth` + policies |

## 📚 Referências

* Kubernetes RBAC Docs
* Network Policies
* Helm Docs
* `kubectl` cheat sheet oficial

Pronto para dominar Kubernetes em produção! ☸️🚀