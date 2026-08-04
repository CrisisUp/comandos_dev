# 📘 Boas Práticas no Terraform

> Guia de boas práticas para escrever Terraform seguro, modular, reprodutível e de fácil manutenção

---

## 🏗️ Estrutura de Projeto

```
terraform/
├── main.tf
├── variables.tf
├── outputs.tf
├── backend.tf          # (estado remoto)
├── modules/
│   ├── vpc/
│   └── app/
├── environments/
│   ├── dev.tfvars
│   └── prod.tfvars
└── terraform.tfvars    # valores padrão
```

- ✅ Separe `main.tf` / `variables.tf` / `outputs.tf` / `backend.tf`
- ✅ Use **modules** para reuso
- ✅ Varíaveis com tipo e descrição

## 🌐 Estado (State)

### 1. Use backend remoto com locking

```hcl
terraform {
  backend "s3" {
    bucket         = "meu-tfstate"
    key            = "projeto/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "tf-locks"
  }
}
```

> ⚠️ Nunca armazene estado no `terraform.tfstate` local em produção (perde-se e conflita). Sempre há lock para evitar escrever simultâneas.

### 2. Versionar o estado? Não commite o estado

```bash
# .gitignore
*.tfstate
*.tfstate.backup
```

## 🔐 Segurança

- **Nunca** coloque segredos no código/estado
- Use `sensitive = true` nos outputs
- Varie `db_pass` via variável/secret store (vars/backend)
- Não aplique `-auto-approve` em produção sem review

```bash
# Exemplo seguro
terraform apply -var="db_password=$SECRET_DB_PASS"
```

## 🧹 Reproducibilidade

- **Pine providers** (`version = "~> 5.0"`)
- Commit `terraform.lock.hcl` (provider checksums)
- Use `-var-file` por ambiente, não edite código por ambiente
- Sempre `terraform fmt` + `validate` antes de commit

## 🧪 Testes e PRs

```bash
terraform fmt -check
terraform validate
terraform plan -out plano   # review em PR
```

- CI: gera plan, revisa em PR, aplica após aprovação
- Use `tflint`/`checkov` para análise estática

## 📊 Organização de Modules

- Module pequeno, com propósito único
- Outputs necessários expostos (`vpc_id`, `subnet_ids`)
- Docs via `terraform-docs`

```bash
terraform-docs markdown . > README.md
```

## 📋 Checklist de Boas Práticas Terraform

- ✅ Backend remoto + lock
- ✅ Estado não versionado
- ✅ Providers pinados + lock file
- ✅ Módulos reutilizáveis
- ✅ `fmt`/`validate`/`plan` no CI
- ✅ Segredos em variáveis/env, `sensitive`
- ✅ Ambientes por `-var-file`
- ✅ `terraform plan` review antes de apply
- ✅ Docs geradas

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Estado | Remoto com lock |
| Providers | Versão pinada |
| Módulos | Reutilizáveis |
| Segredos | Sensitive/env |
| Ambientes | -var-file |
| CI | fmt + validate + plan |
| Docs | terraform-docs |

## 📚 Referências

* HashiCorp Terraform Best Practices
* Terraform Style Guide
* TF Lint / Checkov

Pronto para escrever Terraform com boas práticas! 🏗️🚀