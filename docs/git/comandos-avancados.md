# 📘 Comandos Avançados do Git

> Guia para usuários que já dominam o básico e querem mais controle sobre o histórico e o workflow

---

## 🔍 Reescrita de Histórico

* `Rebase interativo`

```bash
# Reorganizar/editar os últimos 3 commits
git rebase -i HEAD~3

# No editor:
# - pick: manter o commit
# - reword: alterar a mensagem
# - edit: parar para corrigir conteúdo
# - squash: combinar com o commit anterior
# - fixup: combinar descartando a mensagem
# - drop: remover o commit
```

* `Amend`

```bash
# Alterar a mensagem do último commit
git commit --amend -m "nova mensagem"

# Adicionar arquivos ao último commit sem alterar a mensagem
git add arquivo-esquecido.txt
git commit --amend --no-edit

# Alterar o autor do último commit
git commit --amend --author="Nome <email>"
```

## 🚨 Resolução de Problemas

* `Recuperar commits perdidos`

```bash
# Ver todas as operações do reflog (últimos ~90 dias)
git reflog

# Voltar para um commit perdido
git checkout <hash-do-commit>
git branch nova-branch <hash-do-commit>

# Resets perigosos podem ser desfeitos via reflog
git reflog
git reset --hard HEAD@{1}
```

* `Stash (guardar trabalho pendente)`

```bash
# Guardar alterações sem commit
git stash
git stash push -m "mensagem descritiva"

# Guardar apenas arquivos rastreados
git stash push -u

# Listar stashes
git stash list

# Aplicar o último stash (mantém na lista)
git stash apply

# Aplicar e remover da lista
git stash pop

# Remover um stash específico
git stash drop stash@{0}
```

## 🧲 Cherry-pick e Revert

* `Cherry-pick (aplicar commit específico)`

```bash
# Aplicar um commit em outra branch
git cherry-pick <hash>

# Aplicar vários commits
git cherry-pick <hash1> <hash2> <hash3>

# Cherry-pick sem criar commit automaticamente
git cherry-pick -n <hash>

# Continuar após resolver conflitos
git cherry-pick --continue
```

* `Revert (desfazer com novo commit)`

```bash
# Criar commit que reverte outro
git revert <hash>

# Reverter sem abrir o editor
git revert --no-edit <hash>

# Reverter o último commit
git revert HEAD

# Reverter sem criar commit (aplicar as mudanças no working tree)
git revert -n <hash>
```

## 🌿 Trabalhando com Remotos

* `Submodules`

```bash
# Adicionar submodule
git submodule add https://github.com/usuario/repositorio.git caminho/submodulo

# Inicializar submodules após clonar
git submodule update --init --recursive

# Atualizar submodules
git submodule update --remote
```

* `Tracking branches`

```bash
# Ver branches e seu upstream
git branch -vv

# Definir upstream
git branch -u origin/minha-branch
git push -u origin minha-branch

# Ver diferenças com o remoto
git fetch
git diff origin/main..main
```

* `Tagging`

```bash
# Criar tag anotada em commit específico
git tag -a v1.0.0 <hash> -m "Versão 1.0.0"

# Enviar tags para o remoto
git push origin --tags

# Deletar tag local e remota
git tag -d v1.0.0
git push origin :refs/tags/v1.0.0
```

## 🔍 Busca Avançada

* `git log avançado`

```bash
# Buscar commits por mensagem
git log --grep="palavra-chave"

# Buscar commits que tocaram um arquivo
git log --follow -- arquivo.txt

# Buscar commits por conteúdo (diff)
git log -S "trecho-de-código" -- arquivo.txt

# Buscar commits por autor e período
git log --author="Nome" --since="2024-01-01"

# Ver resumo de alterações por autor
git shortlog -sn --all
```

* `Bisect (encontrar commit que causou bug)`

```bash
# Iniciar busca
git bisect start

# Marcar commit ruim e bom
git bisect bad
git bisect good <hash-ultimo-bom>

# Testar e marcar a cada iteração
git bisect good  # ou git bisect bad

# Terminar a busca
git bisect reset
```

* `Grep`

```bash
# Buscar texto em todos os commits
git grep "termo-buscado" <hash>

# Buscar texto no branch atual
git grep -n "termo-buscado"
```

## 🧹 Limpeza e Manutenção

* `Limpar histórico`

```bash
# Remover arquivos não rastreados (cuidado!)
git clean -fd

# Limpar branches locais já mescladas
git branch --merged | xargs git branch -d

# Compactar o repositório
git gc --prune=now --aggressive
```

* `Remover dados sensíveis do histórico`

```bash
# Remover arquivo de todos os commits
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch .env" \
  --prune-empty --tag-name-filter cat -- --all

# Ou usar BFG Repo-Cleaner (mais rápido)
java -jar bfg.jar --delete-files .env
```

## 🛠️ Configurações Úteis

```bash
# Aliases para o dia a dia
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.unstage "reset HEAD --"

# Autocorreção de digitação
git config --global help.autocorrect 1

# Coloração
git config --global color.ui auto

# Rebase como padrão no pull
git config --global pull.rebase true
```

## 📚 Referências

* Documentação Oficial do Git
* Pro Git Book