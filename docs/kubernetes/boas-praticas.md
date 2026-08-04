# 📘 Boas Práticas no Kubernetes

> Guia de boas práticas para operar clusters de forma segura, eficiente, resiliente e de fácil manutenção

---

## 📦 Recursos e Limites

* `Sempre defina requests/limits`

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "1"
    memory: "512Mi"
```

> ⚠️ Sem limites, um pod pode consumir todo o nó. O Kubernetes Scheduler usa `requests` (pedido) para agendar, e `limits` são o teto de uso. **Defina ambos** para cada container.

## ✅ Liveness e Readiness Probes

```yaml
# readiness: quando está pronto para receber tráfego
readinessProbe:
  httpGet: { path: /health, port: 8080 }
  initialDelaySeconds: 5
  periodSeconds: 10

# liveness: se deve reiniciar (oom, deadlock)
livenessProbe:
  exec:
    command: ["curl", "-f", "http://localhost:8080/health"]
  failureThreshold: 3
```

> ⚠️ Probes mal configuradas (timeouts curtos) podem derrubar pods; teste em canário. Distinga liveness vs readiness.

## 🏷️ Labels e Nomes Consistentes

```yaml
metadata:
  labels:
    app: web
    tier: frontend
    env: prod
    release: v1.2.0
```

- ✅ Labels para seleção (`matchLabels`) e organização (`app`, `env`)
- ❌ Não use names como selector frágil; use labels

## 🔒 Segurança

### 1. Não rode como root

```yaml
securityContext:
  runAsUser: 1000
  runAsNonRoot: true
  capabilities:
    drop: ["ALL"]
```

### 2. Imagens confiáveis e sem tag `latest`

```yaml
image: nginx:1.25        # ✅ versão fixa
# ❌ image: nginx:latest  (não reproduzível)
```

### 3. Outras práticas
- Escaneie vulnerabilidades (Trivy) no CI
- Segredos via `Secret`, nunca em configmap/plain
- RBAC mínimo (veja role de serviceaccount)
- Templates com `requests`/`limits` acima

## 🔁 Alta Disponibilidade e rollout

- Use `Deployment`/`StatefulSet` com **replicas >= 2**
- **POD anti-affinity** para espalhar os pods
- **PDB** (Pod Disruption Budget) para evitar quedas em manutenção

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata: { name: meu-app-pdb }
spec:
  minAvailable: 2
  selector: { matchLabels: { app: meu-app } }
```

## 📦 Configuração e Secrets

- Config via **ConfigMaps** e **Secrets**, não embutida na imagem
- Versionar manifests com **git/GitOps** (ArgoCD/Flux)
- Use **Helm** para parametrizar

## 📈 Observabilidade

- **Logs em stdout** (coletados por Fluentd/Logstash, não arquivos)
- **Métricas** com Prometheus (reveja `kubectl top`)
- **Tracing** com OpenTelemetry

## 🧱 GitOps Form

```bash
# Flux / Argo CD para GitOps:
# - cluster estado desejado em git
# - CI/CD reconcílio
```

## 📋 Checklist K8s Boas Práticas

- ✅ `resources` (requests/limits) em todo pod
- ✅ Liveness + readiness probes
- ✅ Labels consistentes (app/tier/env)
- ✅ Não root, imagem não-`latest`
- ✅ RBAC mínimo por service-account
- ✅ Multi-AZ (3+) + replicas >= 2
- ✅ Secrets via Secret (não env)
- ✅ GitOps/Helm para manifests
- ✅ Observabilidade (métricas/logs)

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Resources | Sempre requests/limits |
| Health | probes de readiness |
| Imagem | Pinada (não latest) |
| Segurança | Não-root, RBAC |
| Config | ConfigMap/Secret, GitOps |
| HA | replicas + PDB |
| Logs | stdout, Prometheus |

## 📚 Referências

* Kubernetes Production Best Practices
* 12 Factor App perspective K8s
* CNCF Best Practices

Pronto para operar Kubernetes com boas práticas! ☸️🚀