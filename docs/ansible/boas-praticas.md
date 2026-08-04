# 📘 Boas Práticas no Ansible

> Guia de boas práticas para escrever playbooks idempotentes, seguros, organizados e de fácil manutenção

---

## 🧱 Idempotência

> O objetivo é que rodar o playbook **N** vezes dê o **mesmo resultado**. Módulos declarativos (`apt`, `service`, `copy`) são idempotentes; comandos `shell`/`command` não.

* `Prefira módulos declarativos`

```yaml
# ✅ Idempotente: só instala se não existir
- name: Instalar nginx
  apt:
    name: nginx
    state: present

# ❌ Sempre roda, mesmo sem mudanças
- name: Instalar
  shell: apt-get install -y nginx
```

* `Use changed_when/creates quando precisar de command`

```yaml
- name: Baixar binário
  command: wget -O /usr/local/bin/app https://exemplo.com/app
  args:
    creates: /usr/local/bin/app   # não reexecuta se já existe
  changed_when: false
```

## 🗂️ Organização do Projeto

```
ansible/
├── inventory/
│   ├── production/
│   └── staging/
├── group_vars/
│   ├── all.yml
│   └── web.yml
├── host_vars/
│   └── web1.yml
├── roles/
│   └── nginx/
└── playbooks/
    └── deploy.yml
```

- ✅ Segregue `vars` por grupo/host (não hardcode no playbook)
- ✅ Roles para reuso; playbooks finos (só orquestração)
- ✅ Versionar tudo com git

## 🔒 Segurança

### 1. Nunca exponha segredos

```yaml
# ❌ Senha em plaintext no playbook
# ✅ Use ansible-vault:
#   ansible-vault encrypt group_vars/prod.yml
```

### 2. Privilegiamento mínimo

```yaml
# ✅ become apenas quando necessário (não em tudo)
- name: Task com sudo
  become: yes
  service: { name: nginx, state: started }
```

### 3. Verifique antes de aplicar em prod

```bash
ansible-playbook deploy.yml --check
ansible-playbook deploy.yml --diff
```

## 📐 Convenções

- **Nomes descritivos** em tasks (`Instalar nginx`, não `tarefa 1`)
- **Tags** para segmentar execução (`--tags web`)
- **Handlers** para ações de resposta (restart) — não repetir em cada task
- **Template Jinja2** para arquivos dinâmicos (não `copy` com conteúdo fixo quando há vars)
- `when` condições com fatos (`ansible_os_family`)

## 🧪 Testes

```bash
# Sintaxe
ansible-playbook playbook.yml --syntax-check

# Dry-run
ansible-playbook playbook.yml --check

# CI: ansible-lint
ansible-lint playbook.yml
```

## 📋 Checklist de Boas Práticas Ansible

- ✅ Idempotente (módulos declarativos)
- ✅ `creates`/`changed_when` para commands
- ✅ Roles + group_vars organizados
- ✅ Segredos no Vault
- ✅ `become` mínimo
- ✅ `--check` antes de prod
- ✅ Tags e handlers
- ✅ `ansible-lint` no CI
- ✅ Fatos quando preciso; gather_facts false se não

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Idempotência | Módulos declarativos |
| Segredos | Vault |
| Organização | Roles + vars por grupo |
| Privilégio | become mínimo |
| Segurança | --check antes |
| Convenções | Nomes claros, tags |
| Testes | ansible-lint, --syntax-check |

## 📚 Referências

* Ansible Best Practices (oficial)
* Ansible Galaxy - roles exemplo
* Ansible-Lint Docs

Pronto para escrever Ansible com boas práticas! 🤖🚀