# 📘 Comandos Básicos do Kubernetes

> Guia essencial para começar a usar kubectl e gerenciar pods, deployments e serviços

---

## 🔧 Instalação e Contexto

* `Instalar kubectl`

```bash
# macOS
brew install kubectl

# Linux
curl -LO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Windows (choco)
choco install kubernetes-cli
```

* `Contextos e clusters`

```bash
# Ver contexto atual
kubectl config current-context

# Listar contextos
kubectl config get-contexts

# Trocar de contexto
kubectl config use-context meu-cluster

# Ver versão
kubectl version
kubectl cluster-info
```

## 📦 Pods

```bash
# Listar pods
kubectl get pods
kubectl get pods -n meu-namespace
kubectl get pods -o wide

# Detalhes
kubectl describe pod meu-pod
kubectl get pod meu-pod -o yaml

# Criar/executar
kubectl run meu-pod --image=nginx
kubectl run meu-pod --image=nginx --port=80 --restart=Never

# Logs
kubectl logs meu-pod
kubectl logs meu-pod --follow
kubectl logs meu-pod -c meu-container

# Executar comando no pod
kubectl exec -it meu-pod -- /bin/sh

# Remover
kubectl delete pod meu-pod
```

## 🚀 Deployments

```bash
# Listar
kubectl get deployments
kubectl get deploy

# Criar a partir de YAML/arquivo
kubectl apply -f deployment.yaml
kubectl create deployment meu-app --image=nginx

# Escalar
kubectl scale deployment meu-app --replicas=3

# Atualizar imagem
kubectl set image deployment/meu-app nginx=nginx:1.25

# Status e rollback
kubectl rollout status deployment/meu-app
kubectl rollout undo deployment/meu-app

# Expor serviço
kubectl expose deployment meu-app --port=80 --target-port=80
```

## 🧩 Services

```bash
# Listar services
kubectl get services
kubectl get svc

# Descrever
kubectl describe svc meu-svc

# Port-forward
kubectl port-forward svc/meu-svc 8080:80

# Criar serviço
kubectl expose pod meu-pod --port=8080 --target-port=80
```

## 🗂️ Namespaces

```bash
# Listar/criar
kubectl get namespaces
kubectl create namespace dev

# Trabalhar dentro de um namespace
kubectl get pods -n dev
kubectl config set-context --current --namespace=dev
```

## 📊 Nodes

```bash
# Listar nodes
kubectl get nodes
kubectl get nodes -o wide

# Drenar/cordon
kubectl cordon node01          # impede novos pods
kubectl uncordon node01
```

## 🧹 Recursos e limpeza

```bash
# Ver todos os recursos de um tipo
kubectl get all -n dev

# ConfigMaps e Secrets
kubectl get configmaps
kubectl get secrets
kubectl create configmap meu-cm --from-literal=key=value

# Limpar (cuidado)
kubectl delete all --all -n dev
```

## 📦 HPA e Ingress (básico)

```bash
# Autoscaling horizontal
kubectl autoscale deployment meu-app --cpu-percent=50 --min=1 --max=5

# Ingress
kubectl get ingress
kubectl get ingress -n dev
```

## 🆘 Ajuda e Debug

```bash
# Ajuda
kubectl help
kubectl get --help

# Debug
kubectl describe pod meu-pod      # eventos
kubectl logs meu-pod --previous   # reinícios anteriores
kubectl get events --sort-by=.lastTimestamp
```

## 📋 Checklist Diário

| Comando | O que faz |
| ------- | --------- |
| `kubectl get pods` | Listar pods |
| `kubectl get deploy` | Ver deployments |
| `kubectl logs -f pod` | Logs em tempo real |
| `kubectl exec -it pod -- sh` | Shell no pod |
| `kubectl apply -f yaml` | Aplicar manifest |
| `kubectl get svc` | Ver serviços |
| `kubectl port-forward` | Acessar serviço |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Contexto** | `kubectl config`, `get contexts` |
| **Pods** | `get`, `logs`, `exec`, `delete` |
| **Deploy** | `create`, `scale`, `rollout` |
| **Services** | `expose`, `port-forward` |
| **Namespace** | `create`, `-n` |
| **ConfigMaps** | `get/create configmap` |
| **HPA** | `autoscale` |

## 📚 Referências

* kubectl Cheat Sheet (documentação oficial)
* Kubernetes Basics
* Katacoda

Pronto para usar Kubernetes com confiança! ☸️🚀