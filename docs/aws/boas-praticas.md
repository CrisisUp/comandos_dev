# 📘 Boas Práticas na AWS

> Guia de boas práticas para operar AWS de forma segura, econômica, auditável e de fácil manutenção

---

## 🔐 Segurança (IAM)

### 1. Privilégio mínimo

```json
// ✅ Permitir só o necessário
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::meu-bucket/logs/*"
    }
  ]
}

// ❌ NUNCA: "Action": "*" com "Resource": "*" desnecessariamente
```

### 2. Não usar a conta root no dia a dia

```bash
# ✅ Crie usuários/roles com o mínimo
# ✅ Use AWS SSO ou assume roles
aws sts get-caller-identity
```

### 3. Credenciais nunca no código

```bash
# ❌ Access key hardcoded
# ✅ Use AWS CLI default chain: env, ~/.aws/credentials, roles, SSO
# ✅ Use AWS Secrets Manager para segredos
```

## 🏷️ Tagging e Ornamento

### 1. Sempre etiquetar recursos

```bash
# Tag em criação/uso
aws ec2 create-tags --resources i-xxx --tags Key=Env,Value=prod Key=Cost,Value=team-x
```

### 2. Convenção de tags
- `Env` (dev/stage/prod)
- `Team` / `Owner`
- `CostCenter`
- `Application` / `Component`

## 💸 Custo e Otimização

- ✅ Prefira **serverless/spot** para cargas não críticas
- ✅ **Right Sizing**: use instâncias adequadas, não superdimensionadas
- Configure **Budgets** e **Alerts** de custo
- Use **Compute Optimizer** para recomendações
- **Dormam/desligue** recursos de dev fora do horário

```bash
# Alertas de custo
aws budgets create-budget --budget '{"BudgetLimit":{"Amount":100,"Unit":"USD"},"BudgetName":"mensal","BudgetType":"COST"}'
```

## 📦 IaC e Infraestrutura

### 1. Use Infra as Code (CloudFormation/Terraform)

```bash
# ✅ Toda infra versionada e revisável
aws cloudformation create-stack...
```

- ✅ Não altere recursos de produção manualmente pelo console
- ✅ Mudanças via CloudFormation/Terraform em pipelines

## 🔁 Alta Disponibilidade

- Use **múltiplas AZs** (zonas) para produção
- Implemente **autoscaling** (EC2 e RDS)
- Use **load balancers** para distribuir carga
- **Multi-AZ RDS** para failover transparente

## 🔍 Observabilidade

- **CloudWatch**: logs, métricas, alarmes
- **X-Ray**: tracing de chamadas
- **Audit**: CloudTrail (quem fez o quê)

```bash
# Ativar CloudTrail
aws cloudtrail create-trail --name audit --s3-bucket-name meu-bucket-audit
```

## 📋 Checklist de Boas Práticas AWS

- 🔐 Privilégio mínimo no IAM
- 🤖 SSO/IAM, sem credenciais no código
- 🏷️ Todos os recursos etiquetados (Env, Team, Cost)
- 💸 Budgets/alertas de custo configurados
- 🧱 Infra em código (CloudFormation/Terraform)
- 🔁 Multi-AZ + autoscaling para produção
- 🔍 CloudTrail + CloudWatch ativos
- ⚠️ Rotação de credenciais e audit

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Acesso | Mínimo privilégio, SSO |
| Segredos | Secrets Manager, env vars |
| Tags | Sempre (Env/Team/Cost) |
| Custo | Serverless, budgets |
| Infra | IaC versionado |
| HA | Multi-AZ + scaling |
| Observabilidade | CloudTrail/CloudWatch |

## 📚 Referências

* AWS Well-Architected Framework
* IAM Best Practices
* Cost Optimization AWS
* AWS Security Blog

Pronto para operar AWS com boas práticas! ☁️🚀