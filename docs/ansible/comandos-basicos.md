# 📘 Comandos Básicos do Ansible

> Guia essencial para começar a usar Ansible para automação de configuração e gerenciamento de servidores

---

## 🔧 Instalação e Configuração

* `Instalar`

```bash
# macOS
brew install ansible

# Ubuntu/Debian
sudo apt install ansible

# via pip (qualquer SO)
pip install ansible
pip install ansible-core

# Windows (WSL recomendado)
```

* `Verificar`

```bash
ansible --version
ansible-config list
```

* `Inventário básico`

```ini
# /etc/ansible/hosts (ou arquivo próprio)
[web]
web1.example.com
web2.example.com

[db]
db1.example.com ansible_host=192.168.1.50

[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa
```

## 📦 Módulos Essenciais

* `Ad-hoc`

```bash
# Ping (conectividade)
ansible web -m ping

# Executar comando
ansible web -m command -a "uptime"
ansible web -m shell -a "hostname; date"

# Instalar pacote
ansible db -m apt -a "name=postgresql state=present" --become
```

* `Módulos por SO`

```bash
# apt (Debian/Ubuntu)
ansible web -m apt -a "name=nginx state=latest" --become

# yum/dnf (RHEL)
ansible web -m dnf -a "name=httpd state=present" --become

# Serviço
ansible web -m service -a "name=nginx state=started enabled=yes" --become

# Copiar arquivo
ansible web -m copy -a "src=index.html dest=/var/www/index.html" --become
```

## 📝 Playbooks

* `Estrutura básica`

```yaml
---
- name: Configurar servidor web
  hosts: web
  become: yes
  tasks:
    - name: Instalar nginx
      apt:
        name: nginx
        state: present

    - name: Copiar index
      copy:
        src: index.html
        dest: /var/www/html/index.html

    - name: Garantir serviço rodando
      service:
        name: nginx
        state: started
        enabled: yes
```

* `Rodar playbook`

```bash
ansible-playbook playbook.yml
ansible-playbook -i inventario playbook.yml
ansible-playbook playbook.yml --check   # dry-run
ansible-playbook playbook.yml --syntax-check
```

## 🔍 Fatos (Facts)

```bash
# Coletar fatos do host
ansible web -m setup
ansible web -m setup -a "filter=ansible_distribution"

# Desativar coleta (performance)
# ansible-playbook playbook.yml  # gather_facts: false no play
```

## 🧩 Variáveis e Templates

* `Variáveis`

```yaml
vars:
  app_port: 8080
  domain: exemplo.com
```

* `Template (Jinja2)`

```yaml
- name: Configurar app
  template:
    src: app.conf.j2
    dest: /etc/app.conf
```

```jinja2
server {
    listen {{ app_port }};
    server_name {{ domain }};
}
```

* `Usar em tasks`

```yaml
- name: Mostrar variável
  debug:
    msg: "Porta do app: {{ app_port }}"
```

## ♻️ Handlers

```yaml
tasks:
  - name: Editar config
    template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    notify: reiniciar nginx

handlers:
  - name: reiniciar nginx
    service:
      name: nginx
      state: restarted
```

## 🗂️ Roles (estrutura)

```
roles/
└── nginx/
    ├── tasks/main.yml
    ├── handlers/main.yml
    ├── templates/
    ├── files/
    ├── vars/main.yml
    └── defaults/main.yml
```

```yaml
# playbook usando role
- name: Aplicar role
  hosts: web
  roles:
    - nginx
```

## 🧾 Tags e Condições

```yaml
- name: Com tags
  apt:
    name: curl
  tags: [base, tools]

- name: Condicional
  service:
    name: httpd
    state: started
  when: ansible_os_family == "RedHat"
```

```bash
# Rodar só uma tag
ansible-playbook playbook.yml --tags base
ansible-playbook playbook.yml --skip-tags tools
```

## 🆘 Ajuda

```bash
ansible-doc -l                     # lista módulos
ansible-doc apt                    # docs do módulo
ansible-doc -s service             # resumo de opções
ansible-playbook --help
```

## 📋 Checklist Diário

| Comando | O que faz |
| ------- | --------- |
| `ansible web -m ping` | Testar conectividade |
| `ansible web -m setup` | Ver fatos |
| `ansible-playbook play.yml` | Rodar playbook |
| `--check` | Dry-run |
| `ansible-doc apt` | Ver módulo |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Ad-hoc** | `ansible <hosts> -m <modulo>` |
| **Playbooks** | `ansible-playbook` |
| **Módulos** | `apt`, `dnf`, `service`, `copy`, `template` |
| **Debug** | `--check`, `--syntax-check` |
| **Fatos** | `-m setup` |
| **Roles** | `roles/<nome>/tasks` |
| **Doc** | `ansible-doc` |

## 📚 Referências

* Ansible Documentation
* Ansible Galaxy (roles prontas)
* Ansible Best Practices

Pronto para usar Ansible com confiança! 🤖🚀