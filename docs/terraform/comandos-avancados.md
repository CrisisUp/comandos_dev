# 📘 Comandos Avançados do Terraform

> Guia para usuários que já dominam o básico e querem workspaces, backend remoto, módulos reutilizáveis, funções, sensibilidade e imports

---

## 📦 Backend Remoto (estado compartilhado)

```hcl
terraform {
  backend "s3" {
    bucket         = "meu-tf-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"   # lock otimista
  }
}
```

```bash
terraform init        # migra o estado para o backend
terraform init -reconfigure
```

## 🗂️ Workspaces (ambientes)

```bash
# Criar/usar workspace
terraform workspace new dev
terraform workspace new prod
terraform workspace select prod
terraform workspace list
terraform workspace show
```

```hcl
# Diferenciar por workspace
resource "aws_instance" "app" {
  instance_type = terraform.workspace == "prod" ? "t3.large" : "t3.micro"
}
```

## 🔄 Dependências e Meta-argumentos

```hcl
resource "aws_instance" "app" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  depends_on = [aws_s3_bucket.meu_bucket]
}

# count - múltiplos recursos
resource "aws_iam_user" "equipe" {
  count = 3
  name  = "dev-${count.index}"
}

# for_each - a partir de map
resource "aws_iam_user" "us" {
  for_each = toset(["ana", "beto"])
  name     = each.value
}
```

## 🔍 Data Sources e Locals

```hcl
# Data source - consultar infra existente
data "aws_ami" "ubuntu" {
  most_recent = true
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-*-amd64-server-*"]
  }
  owners = ["099720109477"]
}

resource "aws_instance" "app" {
  ami = data.aws_ami.ubuntu.id
}

# Locals
locals {
  app_name = "meu-app"
  tags     = { Env = "prod", App = local.app_name }
}
```

## 📦 Modules com Outputs e Vars

```hcl
module "vpc" {
  source = "./modules/vpc"
  cidr   = "10.0.0.0/16"
  name   = "prod-vpc"
}

# Usar output do módulo
resource "aws_subnet" "publica" {
  vpc_id     = module.vpc.vpc_id
  cidr_block = "10.0.1.0/24"
}
```

```bash
# Ver outputs (globais e de módulos)
terraform output
terraform output vpc_id
```

## 🔢 Recriação Forçada

```hcl
# force_destroy: permite destruir bucket mesmo com conteúdo
resource "aws_s3_bucket" "meu_bucket" {
  bucket       = "x"
  force_destroy = true
}

# taint (deprecado em favor de replace) - forçar recriação
terraform taint aws_instance.app
terraform untaint aws_instance.app
```

```bash
# Terraform 1.5+: substituir um recurso pelo plano
terraform apply -replace="aws_instance.app"
```

## 🔎 Import e Movimento

```bash
# Importar infra existente (adotar para o terraform)
terraform import aws_s3_bucket.meu_bucket "nome-existente"

# Mover de um endereço para outro
terraform state mv old.address new.address

# Salvar plano em arquivo (para aplicar depois)
terraform plan -out plano.tfplan
terraform apply plano.tfplan
```

## ⚙️ Data e dinâmico

```hcl
# dynamic blocks - loops dentro de configuração
dynamic "ingress" {
  for_each = var.portas
  content {
    from_port   = ingress.value
    to_port     = ingress.value
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

## 🔁 Apply com Approve e Targets

```bash
# Aplicar apenas um recurso (dev/troubleshoot)
terraform apply -target=aws_instance.app

# Destroy apenas um
terraform destroy -target=aws_s3_bucket.old

# Refresh (atualizar estado)
terraform refresh
terraform apply -refresh-only
```

## 🧱 Segurança e Segredos

```bash
# Variáveis sensíveis
terraform apply -var="db_password=segredo"

# Sensitive output (mascara)
# output "db_pass" { value = var.db_pass; sensitive = true }
```

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

## ✅ Checklist Terraform Avançado

| Tema | Comando/Recurso |
| ---- | --------------- |
| Backend remoto | `backend "s3"`, `init` |
| Ambientes | `workspace select` |
| Dependências | `depends_on`, `count`, `for_each` |
| Data | `data "aws_ami"` |
| Modules | `module` + outputs |
| Force | `force_destroy`, `taint` |
| Import | `terraform import` |
| Dynamic | `dynamic` blocks |
| Target | `apply --target` |

## 📚 Referências

* Terraform CLI Docs
* Terraform Language (functions, meta-args)
* HashiCorp Learn - Modules & Backends

Pronto para dominar Terraform em produção! 🏗️🚀