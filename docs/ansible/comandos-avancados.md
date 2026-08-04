# 📘 Comandos Avançados do Ansible

> Guia para usuários que já dominam o básico e querem roles robustas, vault, loops, blocos, AWX, performance e troubleshooting

---

## 🧠 Estrutura de Roles Avançada

```
roles/app/
├── defaults/main.yml     # vars com valor padrão (baixa prioridade)
├── vars/main.yml         # vars internas (alta prioridade)
├── tasks/main.yml        # passos principais
├── handlers/main.yml     # ações disparadas por notify
├── templates/            # arquivos Jinja2
├── files/                # arquivos estáticos
└── meta/main.yml         # dependências de roles
```

```yaml
# meta/main.yml — dependências
dependencies:
  - role: common
    vars:
      ntp_enabled: true
```

## 🔒 Ansible Vault (segredos)

```bash
# Criar arquivo criptografado
ansible-vault create segredos.yml
ansible-vault encrypt group_vars/prod.yml
ansible-vault decrypt group_vars/prod.yml

# Editar/ver
ansible-vault edit segredos.yml
ansible-vault view segredos.yml

# Rodar playbook com vault
ansible-playbook playbook.yml --ask-vault-pass
ansible-playbook playbook.yml --vault-password-file vault.pass

# Multi-senhas
ansible-vault encrypt --vault-id prod@prompt group_vars/prod.yml
```

## 🔁 Loops e Controle de Fluxo

* `Loops`

```yaml
- name: Instalar vários pacotes
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - curl
    - vim

# loop com dict
- name: Criar usuários
  user:
    name: "{{ item.name }}"
    state: present
  loop:
    - { name: ana, group: dev }
    - { name: beto, group: ops }
```

* `Blocos e resgate`

```yaml
- block:
    - name: Passo que pode falhar
      command: /bin/false
  rescue:
    - name: Recuperar
      debug: { msg: "falhou, recuperando" }
  always:
    - name: Sempre roda
      debug: { msg: "final" }
```

## 🎯 Delegation e Run_once

```yaml
# Rodar em um host, mas agir em outro
- name: Atualizar DNS no host central
  command: dnsupdate
  delegate_to: dns-server

# Rodar uma vez no grupo (não em cada host)
- name: Criar rede
  command: docker network create app
  run_once: true
  delegate_to: localhost
```

## 🔧 Async e Polling

```yaml
- name: Tarefa longa assíncrona
  command: long-running-job
  async: 3600       # timeout em segundos
  poll: 30          # checar a cada 30s
```

## 🔍 Factos, Cache e Inventory

```bash
# Cache de factos (performance)
ansible-playbook playbook.yml --flush-cache
```

* `Inventory dinâmico (AWS)`

```bash
# Usar plugin aws_ec2
ansible-inventory -i aws_ec2.yml --graph
ansible-inventory -i aws_ec2.yml --list

# Testar inventory
ansible all -i aws_ec2.yml -m ping
```

* `add_host/group_by em runtime`

```yaml
- name: Registrar e adicionar ao grupo
  add_host:
    name: "{{ item }}"
    groups: novos_hosts
  loop: "{{ resultado.hosts }}"
```

## 📦 AWX / Tower e Templates

```bash
# AWX: UI web + API
# Templates de job, inventários, credenciais
# Playbooks via git
```

## ⚡ Performance

```bash
# Paraleelo (forks)
ansible-playbook playbook.yml -f 20

# Mitogen (acelerador) - alternativa ao SSH
# ansible-playbook com strategy plugin

# Sem gather_facts quando não precisa
ansible-playbook playbook.yml   # no play: gather_facts: false
```

## 🧪 Troubleshooting

```bash
# Verbosity
ansible-playbook playbook.yml -vvv
ansible-playbook playbook.yml -vvvv

# Testar módulo específico
ansible web -m shell -a "sudo tail -n 50 /var/log/syslog"

# Ver diff (para módulos com suporte)
ansible-playbook playbook.yml --diff

# Limitar execução a host específico
ansible-playbook playbook.yml --limit web1
```

## 🔁 Factos Customizados e Filtros

```yaml
- name: Usar filtro
  debug:
    msg: "{{ 'hello' | upper }}"

- name: Filtro padrão
  debug:
    msg: "{{ variavel | default('sem valor') }}"

- name: Combinar listas
  debug:
    msg: "{{ lista_a | union(lista_b) }}"
```

## 📋 Checklist Ansible Avançado

| Tema | Comando/Recurso |
| ---- | --------------- |
| Segredos | `ansible-vault encrypt` |
| Loop | `loop`, `with_items` |
| Blocos | `block/rescue/always` |
| Delegation | `delegate_to`, `run_once` |
| Async | `async` + `poll` |
| Inventory dinâmico | plugin `aws_ec2` |
| Performance | `-f`, `gather_facts: false` |
| Debug | `-vvv`, `--diff`, `--limit` |

## 📚 Referências

* Ansible Docs - Roles, Vault, Loop
* Ansible Best Practices
* AWX Docs

Pronto para dominar Ansible em produção! 🤖🚀