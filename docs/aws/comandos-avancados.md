# 📘 Comandos Avançados do AWS CLI

> Guia para usuários que já dominam o básico e querem VPC, CloudFormation, filtros avançados, SSM, automação e multi-contas

---

## 📦 Organização e Multi-Conta

* `Perfis e SSO`

```bash
# Logar com AWS SSO
aws sso login --profile dev
# Depois, assumir a conta/role desejada pelo perfil configurado
aws sts get-caller-identity --profile dev

# Usar MFA
aws sts get-session-token --serial-number arn:aws:iam::xxx:mfa/dev --token-code 123456

# Assumir role
aws sts assume-role --role-arn arn:aws:iam::xxx:role/prod --role-session-name sessao
```

* `Organizations`

```bash
aws organizations list-accounts
aws organizations list-roots
```

## 📦 S3 Avançado

* `Versionamento e lifecycle`

```bash
# Versionar bucket
aws s3api put-bucket-versioning --bucket meu-bucket --versioning-configuration Status=Enabled

# Política de lifecycle
aws s3api put-bucket-lifecycle-configuration \
  --bucket meu-bucket \
  --lifecycle-configuration '{"Rules":[{"ID":"old","Status":"Enabled","Expiration":{"Days":90}}]}'

# Listar versões
aws s3api list-object-versions --bucket meu-bucket
```

* `Transferências robustas`

```bash
# Sync com exclusão (align local/remoto)
aws s3 sync ./local s3://bucket/ --delete

# Multipart
aws s3 cp grande.bin s3://bucket/ --expected-size 100000000

# ACLs e tags
aws s3api put-object-tagging --bucket meu-bucket --key x --tagging 'TagSet=[{Key=k,Value=v}]'
```

## 🌐 VPC e Rede

```bash
# VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16
aws ec2 describe-vpcs

# Subnets e gateways
aws ec2 create-subnet --vpc-id vpc-xxx --cidr-block 10.0.1.0/24
aws ec2 create-internet-gateway
aws ec2 attach-internet-gateway --internet-gateway-id igw-xxx --vpc-id vpc-xxx
aws ec2 create-route-table --vpc-id vpc-xxx
aws ec2 create-route --route-table-id rtb-xxx --destination-cidr-block 0.0.0.0/0 --gateway-id igw-xxx

# Segurança
aws ec2 describe-security-groups
aws ec2 authorize-security-group-egress ...
aws ec2 describe-nacl
```

## 🔍 Queries e Filtros

* `--query (JMESPath)`

```bash
# Selecionar campos
aws ec2 describe-instances --query 'Reservations[].Instances[].InstanceId'

# Filtros
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running"
aws s3 ls --filter

# Paginação (+ controlar output)
aws ec2 describe-instances --max-items 10 --next-token ...
aws ec2 describe-instances --output table
```

## 📜 CloudFormation

```bash
# Validar template
aws cloudformation validate-template --template-body file://template.yaml

# Criar stack
aws cloudformation create-stack \
  --stack-name meu-stack \
  --template-body file://template.yaml \
  --parameters ParameterKey=VpcCidr,ParameterValue=10.0.0.0/16

# Atualizar
aws cloudformation update-stack --stack-name meu-stack --template-body file://template.yaml

# Status e remover
aws cloudformation describe-stacks --stack-name meu-stack
aws cloudformation delete-stack --stack-name meu-stack
```

## 🤖 Lambda e Eventos Avançados

```bash
# Deploy de função
aws lambda update-function-code \
  --function-name minha-func \
  --zip-file fileb://function.zip

# Listar/descrição
aws lambda list-functions --max-items 0
aws lambda get-function --function-name minha-func

# Invocar assíncrono
aws lambda invoke --function-name f --invocation-type Event out.json
```

## ⚙️ SSM e Automação

```bash
# Parameters Store
aws ssm put-parameter --name "/app/db/pass" --value "secret" --type SecureString
aws ssm get-parameter --name "/app/db/pass" --with-decryption

# Run Command (executar em instâncias)
aws ssm send-command --document-name "AWS-RunShellScript" --instance-ids i-xxx --parameters 'commands=["uptime"]'

# Session Manager
aws ssm start-session --target i-xxx
```

## 🗄️ RDS e Banco Avançado

```bash
# Ver clusters Aurora
aws rds describe-db-clusters

# Promover réplica de leitura a master
aws rds promote-read-replica --db-instance-identifier meu-db

# Reiniciar (failover manual)
aws rds reboot-db-instance --db-instance-identifier meu-db
```

## 🛒 Cost e Tagging

```bash
# Custos (requer organizações/CUR)
aws ce get-cost-and-usage \
  --time-period Start=2024-01-01,End=2024-01-31 \
  --granularity MONTHLY --metrics "BlendedCost"

# Tagging
aws resourcegroupstaggingapi get-resources
aws resourcegroupstaggingapi tag-resources \
  --resource-arn-list arn:aws:s3:::meu-bucket \
  --tags Env=prod
```

## 🔐 IAM Avançado

* `Roles e políticas`

```bash
# Criar role
aws iam create-role --role-name app-role --assume-role-policy-document file://assume.json

# Inline policy
aws iam put-role-policy --role-name app-role --policy-name p --policy-document file://p.json

# Simulate
aws iam simulate --action elastic-beanstalk:CreateApplication
```

## ⚠️ Segurança é Multi-Factor

```bash
# Ver privilégios da identidade atual
aws iam get-account-authorization-details
aws sts get-access-key-info --access-key-id AKIA...
```

## 📋 Checklist AWS Avançado

| Tema | Comando |
| ---- | ------- |
| Query | `--query 'Reservations[].Instances[].InstanceId'` |
| SSO/MFA | `aws configure sso`, `sts get-session-token` |
| VPC | `ec2 create-vpc/subnet/gw` |
| CloudFormation | `aws cloudformation create-stack` |
| SSM | `ssm send-command` |

## 📚 Referências

* AWS CLI Advanced (queries)
* CloudFormation CLI
* AWS SSM Run Command
* AWS Cost Explorer

Pronto para dominar a AWS CLI em produção! ☁️🚀