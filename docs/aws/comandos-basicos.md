# 📘 Comandos Básicos do AWS CLI

> Guia essencial para começar a usar a AWS CLI no dia a dia

---

## 🔧 Instalação e Configuração

* `Instalar`

```bash
# macOS
brew install awscli

# Linux (script oficial)
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
unzip awscliv2.zip
sudo ./aws/install

# Windows (choco)
choco install awscli
```

* `Configurar credenciais`

```bash
# Configuração interativa (access key, secret, região)
aws configure

# Configurar por arquivo
aws configure --profile producao

# Ver configuração atual
aws configure list
aws sts get-caller-identity
```

* `Perfis e variáveis`

```bash
# Usar um perfil
aws s3 ls --profile producao

# Variáveis de ambiente
export AWS_ACCESS_KEY_ID=AKIA...
export AWS_SECRET_ACCESS_KEY=...
export AWS_DEFAULT_REGION=us-east-1
export AWS_PROFILE=producao
```

## 📦 Serviços Principais — S3

* `Buckets`

```bash
# Listar buckets
aws s3 ls

# Criar bucket
aws s3 mb s3://meu-bucket

# Remover bucket (vazio)
aws s3 rb s3://meu-bucket
```

* `Arquivos`

```bash
# Upload
aws s3 cp arquivo.txt s3://meu-bucket/
aws s3 cp diretorio/ s3://meu-bucket/ --recursive

# Download
aws s3 cp s3://meu-bucket/arquivo.txt ./
aws s3 sync s3://meu-bucket/ ./local/

# Listar objetos
aws s3 ls s3://meu-bucket/
aws s3 ls s3://meu-bucket/ --recursive

# Remover
aws s3 rm s3://meu-bucket/arquivo.txt
aws s3 rm s3://meu-bucket/ --recursive
```

## 🖥️ EC2 — Instâncias

```bash
# Listar instâncias
aws ec2 describe-instances

# Listar com filtro (resumo)
aws ec2 describe-instances --query 'Reservations[].Instances[].{Id:InstanceId,Estado:State.Name}' --output table

# Iniciar/parar/encerrar
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Criar par de chaves
aws ec2 create-key-pair --key-name minha-chave --query 'KeyMaterial' --output text > minha-chave.pem
```

* `Security Groups`

```bash
# Listar
aws ec2 describe-security-groups

# Criar grupo e autorizar porta
aws ec2 create-security-group --group-name web --description "Web"
aws ec2 authorize-security-group-ingress \
  --group-name web --protocol tcp --port 22 --cidr 0.0.0.0/0
```

## 📋 IAM — Usuários e Políticas

```bash
# Usuários
aws iam list-users
aws iam create-user --user-name app-bot
aws iam delete-user --user-name app-bot

# Acess keys
aws iam create-access-key --user-name app-bot

# Políticas
aws iam list-policies --scope AWS --only-attached
aws iam attach-user-policy \
  --user-name app-bot \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
```

## 🗄️ RDS — Banco de Dados

```bash
# Listar instâncias
aws rds describe-db-instances

# Criar instância
aws rds create-db-instance \
  --db-instance-identifier meu-db \
  --engine postgres \
  --db-instance-class db.t3.micro \
  --master-username admin \
  --master-user-password senha-forte

# Snapshot
aws rds create-db-snapshot --db-instance-identifier meu-db --db-snapshot-identifier meu-db-snap
```

## 🐳 ECR e Lambda

```bash
# ECR - login e push
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin <account>.dkr.ecr.us-east-1.amazonaws.com

# Lambda
aws lambda list-functions
aws lambda invoke --function-name minha-funcao saida.json
```

## 🔍 CloudWatch e Logs

```bash
# Logs
aws logs describe-log-groups
aws logs tail /aws/lambda/minha-funcao --follow

# Métricas
aws cloudwatch list-metrics --namespace AWS/EC2
```

## 🆘 Ajuda

```bash
# Ajuda
aws help
aws s3 help
aws ec2 describe-instances help

# Autocompletar
complete -C aws_completer aws
```

## 📋 Checklist Diário

| Comando | O que faz |
| ------- | --------- |
| `aws configure` | Configurar credenciais |
| `aws sts get-caller-identity` | Ver identidade atual |
| `aws s3 ls` | Listar buckets |
| `aws s3 cp` | Copiar objetos |
| `aws ec2 describe-instances` | Ver instâncias |
| `aws logs tail` | Ver logs |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Config** | `aws configure`, `--profile` |
| **S3** | `aws s3 ls/cp/sync/mb/rb` |
| **EC2** | `describe/start/stop/terminate` |
| **IAM** | `iam list-users/create-user` |
| **RDS** | `rds describe/create` |
| **Lambda** | `lambda list/invoke` |
| **Logs** | `logs tail` |

## 📚 Referências

* AWS CLI Reference
* AWS CLI Documentation
* AWS CloudShell

Pronto para usar AWS com confiança! ☁️🚀