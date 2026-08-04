# 📘 Comandos Básicos do Terraform

> Guia essencial para começar a usar Terraform para infraestrutura como código (IaC)

---

## 🔧 Instalação e Configuração

* `Instalar`

```bash
# macOS
brew install terraform

# Linux (binário)
curl -fsSL https://releases.hashicorp.com/terraform/1.8.0/terraform_1.8.0_linux_amd64.zip -o tf.zip
unzip tf.zip
sudo mv terraform /usr/local/bin/

# Windows (choco)
choco install terraform
```

* `Verificar`

```bash
terraform version
terraform -help
```

## 📝 Arquivos HCL

* `Estrutura básica`

```hcl
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "meu_bucket" {
  bucket = "meu-bucket-exemplo"
  tags = {
    Name = "Meu bucket"
  }
}
```

* `Init`

```bash
# Baixar providers e inicializar
terraform init
terraform init -upgrade    # atualizar providers
```

## 🚀 Ciclo de Vida

* `Plan e Apply`

```bash
# Mostrar o que seria feito
terraform plan
terraform plan -out plano.tfplan

# Aplicar
terraform apply
terraform apply plano.tfplan     # aplicar plano salvo
terraform apply -auto-approve    # sem confirmação
```

* `Destroy`

```bash
# Remover recursos
terraform destroy
terraform destroy -auto-approve

# Ver o que seria destruído
terraform plan -destroy
```

## 📦 Estado (State)

```bash
# Ver estado
terraform state list
terraform state show aws_s3_bucket.meu_bucket

# Mover recurso no estado
terraform state mv aws_s3_bucket.a aws_s3_bucket.b

# Remover do estado (não destrói)
terraform state rm aws_s3_bucket.meu_bucket

# Ver por fora
terraform state pull
terraform state push
```

## 🔍 Outputs e Inputs

* `Variáveis`

```hcl
variable "region" {
  description = "Região"
  type        = string
  default     = "us-east-1"
}

variable "instancias" {
  type    = number
  default = 2
}
```

* `Outputs`

```hcl
output "bucket_arn" {
  value = aws_s3_bucket.meu_bucket.arn
}
```

```bash
# Usar variável
terraform apply -var="region=sa-east-1"
terraform apply -var-file=prod.tfvars

# Ver outputs
terraform output
terraform output bucket_arn
```

## 📚 Modules

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"

  name = "minha-vpc"
  cidr = "10.0.0.0/16"
}
```

```bash
terraform init   # baixa módulos
terraform get
```

## 🧹 Formatação e Validação

```bash
# Formata HCL
terraform fmt
terraform fmt --check

# Validar sintaxe/consistência
terraform validate
```

## 🆘 Ajuda

```bash
terraform -help
terraform providers
terraform workspace list
```

## 📋 Checklist Diário

| Comando | O que faz |
| ------- | --------- |
| `terraform init` | Inicializar |
| `terraform plan` | Ver mudanças |
| `terraform apply` | Aplicar |
| `terraform destroy` | Destruir |
| `terraform fmt` | Formatar |
| `terraform validate` | Validar |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Init** | `terraform init` |
| **Plan** | `terraform plan -out` |
| **Apply** | `terraform apply` |
| **Destroy** | `terraform destroy` |
| **State** | `state list/show/mv/rm` |
| **Vars** | `-var`, `-var-file` |
| **Modules** | `module`, `init` |
| **Fmt/Validate** | `fmt`, `validate` |

## 📚 Referências

* Terraform Documentation
* Terraform Registry (modules/providers)
* HashiCorp Learn

Pronto para usar Terraform com confiança! 🏗️🚀