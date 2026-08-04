# 📘 Comandos Básicos do Git

> Guia essencial para começar a usar Git no dia a dia

---

## 🔧 Configuração Inicial

* `Definir nome e email`

    ```bash
    git config --global user.name "Seu Nome"
    git config --global user.email "seu@email.com"
    ```

* `Verificar configurações`

    ```bash
    git config --list
    git config --global --list
    ```

* `Definir editor padrão`

    ```bash
    git config --global core.editor "code --wait"  # VS Code
    git config --global core.editor "nano"         # Nano
    git config --global core.editor "vim"          # Vim
    ```

## 🚀 Iniciando um Repositório

* `Criar novo repositório na pasta atual`

    ```bash
    git init
    ```

* `Clonar repositório existente`

    ```bash
    git clone https://github.com/usuario/repositorio.git
    ```

* `Clonar em uma pasta específica`

    ```bash
    git clone https://github.com/usuario/repositorio.git nome-da-pasta
    ```

## 📋 Verificando o Status

* `Status do repositório`

    ```bash

    ### Ver arquivos modificados, adicionados e não rastreados

    git status

    ### Status resumido (menos verboso)

    git status -s

    ### Status com mais detalhes

    git status --verbose
    ```

* `Diferenças`

    ```bash
    ### Ver o que foi modificado (não adicionado)

    git diff

    # Ver diferença entre staging e último commit
    git diff --staged

    # Ver diferença entre dois commits
    git diff commit1 commit2
    ```

## ➕ Adicionando Arquivos

* `Adicionar ao staging`

    ```bash
    # Adicionar um arquivo específico
    git add arquivo.txt

    # Adicionar todos os arquivos da pasta atual
    git add .

    # Adicionar todos os arquivos (incluindo subpastas)
    git add -A

    # Adicionar apenas arquivos modificados
    git add -u

    # Adicionar interativamente
    git add -i

    # Adicionar patch (partes de um arquivo)
    git add -p
    ```

* `Remover do staging`

    ```bash
    # Remover um arquivo do staging (mas manter alterações)
    git reset HEAD arquivo.txt

    # Remover todos os arquivos do staging
    git reset HEAD
    ```

## 💾 Commits

* `Criar commits`

```bash
# Commit com mensagem
git commit -m "Mensagem descritiva"

# Commit com mensagem multilinha
git commit -m "Título" -m "Descrição detalhada..."

# Commit com tudo (add + commit)
git commit -a -m "Mensagem"

# Commitar arquivos específicos
git commit arquivo1.txt arquivo2.txt -m "Mensagem"
```

* `Modificar commits`

```bash
# Modificar último commit (mensagem)
git commit --amend -m "Nova mensagem"

# Adicionar arquivos ao último commit

git add arquivo-esquecido.txt
git commit --amend

# Modificar último commit sem mudar mensagem

git commit --amend --no-edit
```

* `Boas práticas de mensagens`

```markdown
✅ **Boas mensagens:**
- feat: adiciona funcionalidade X
- fix: corrige bug Y
- docs: atualiza documentação
- style: ajusta formatação
- refactor: refatora código
- test: adiciona testes
- chore: atualiza dependências

❌ **Mensagens ruins:**
- "update" (muito vago)
- "fix" (sem contexto)
- "mudanças" (genérico)
```

## 🌿 Branches

* `Gerenciar branches`

```bash
# Listar branches locais
git branch

# Listar todas (incluindo remotas)
git branch -a

# Criar nova branch
git branch nova-branch

# Criar e mudar para nova branch
git checkout -b nova-branch
git switch -c nova-branch  # (Git 2.23+)

# Mudar de branch
git checkout outra-branch
git switch outra-branch    # (Git 2.23+)

# Renomear branch atual
git branch -m novo-nome

# Deletar branch
git branch -d branch-remover  # Só se já foi mesclada
git branch -D branch-remover  # Forçar (mesmo sem mesclar)
```

## 🔀 Merge e Pull

* `Mesclar branches`

```bash
# Mesclar outra branch na atual
git merge outra-branch

# Mesclar com mensagem
git merge outra-branch -m "Mesclando feature X"

# Merge sem avanço rápido
git merge --no-ff outra-branch
```

* `Atualizar repositório`

```bash
# Baixar alterações do remoto
git fetch

# Baixar e mesclar (equivalente a fetch + merge)
git pull

# Pull com rebase
git pull --rebase

# Pull de uma branch específica
git pull origin branch-especifica
```

## 📤 Enviar para Remoto

* `Push`

```bash
# Enviar branch atual para remoto
git push

# Enviar para remoto específico
git push origin minha-branch

# Enviar e definir upstream
git push -u origin minha-branch

# Enviar todas as tags
git push --tags

# Forçar push (cuidado!)
git push --force
git push -f

# Push seguro (evita sobrescrever)
git push --force-with-lease
```

## 📥 Trabalhando com Remotos

* `Gerenciar remotos`

    ```bash
    # Ver remotos
    git remote -v

    # Adicionar remoto
    git remote add origin https://github.com/usuario/repositorio.git

    # Remover remoto
    git remote remove origin

    # Renomear remoto
    git remote rename origin novo-origin

    # Ver informações do remoto
    git remote show origin
    ```

## 📜 Histórico

* `Log de commits`

    ```bash
    # Mostrar histórico completo
    git log

    # Log resumido (uma linha por commit)
    git log --oneline

    # Log com gráfico
    git log --graph --oneline --all

    # Últimos N commits
    git log -5

    # Log com alterações
    git log -p

    # Log de um autor específico
    git log --author="Nome"

    # Log com data
    git log --since="2024-01-01" --until="2024-12-31"

    # Log de commits de um arquivo
    git log --follow arquivo.txt
    ```

* `Visualizar commit`

    ```bash
    # Ver detalhes de um commit específico
    git show commit-hash

    # Ver alterações do último commit
    git show HEAD

    # Ver arquivos modificados em um commit
    git show --stat commit-hash
    ```

## 🗑️ Desfazendo Coisas

* `Descartar alterações`

    ```bash
    # Descartar alterações em um arquivo
    git checkout -- arquivo.txt
    git restore arquivo.txt  # (Git 2.23+)

    # Descartar todas as alterações
    git restore .

    # Desfazer último commit mantendo alterações no staging
    git reset --soft HEAD~1

    # Desfazer último commit (remove alterações)
    git reset --hard HEAD~1

    # Desfazer commit mantendo alterações fora do staging (padrão)
    git reset --mixed HEAD~1
    ```

* `Reverter commit`

    ```bash
    ## Criar commit que reverte outro
    git revert commit-hash

    ## Reverter sem abrir editor
    git revert --no-edit commit-hash
    ```

## 🏷️ Tags

* `Criar tags`

    ```bash

    ## Tag leve
    git tag v1.0.0

    ## Tag anotada (com mensagem)
    git tag -a v1.0.0 -m "Versão 1.0.0"

    ## Tag de um commit específico
    git tag -a v0.9.0 commit-hash

    ## Enviar uma tag
    git push origin v1.0.0

    ## Enviar todas as tags
    git push --tags

    ## Ver todas as tags
    git tag

    ## Buscar tags no remoto
    git fetch --tags
    ```

## 🆘 Ajuda

* `Ajuda geral`

    ```bash
    git --help
    ```

* `Ajuda de um comando específico`

    ```bash
    git help commit
    git commit --help
    ```

## 📝 Dicas rápidas

| Comando | O que faz |
| ------- | --------- |
| git stash | Guarda alterações temporariamente |
| git stash pop | Recupera alterações guardadas |
| git cherry-pick commit | Aplica commit específico |
| git bisect start | Encontra commit que causou bug |
| git blame arquivo | Quem modificou cada linha |
| git clean -fd | Remove arquivos não rastreados |

## ✅ Check-list diário

```bash
git status - Veja o que foi modificado
git add . - Adicione suas alterações
git commit -m "mensagem" - Crie um commit
git pull - Atualize sua branch
git push - Envie para o remoto
```
