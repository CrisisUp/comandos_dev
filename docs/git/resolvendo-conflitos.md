# 📘 Resolvendo Conflitos no Git

> Guia prático para entender, identificar e resolver conflitos de merge no Git

---

## 🤔 O que é um conflito?

Um conflito acontece quando o Git não consegue mesclar automaticamente duas versões de um **mesmo arquivo** porque ambas alteraram **as mesmas linhas**. Não é um erro do Git — é um alerta para você decidir qual versão manter.

## 🔍 Como identificar um conflito

### Ao mesclar (merge)

```bash
# O merge falha e o Git marca os arquivos conflitantes
git merge outra-branch
# CONFLICT (content): Merge conflict in arquivo.txt
# Automatic merge failed; fix conflicts and then commit the result.
```

### Ao puxar alterações (pull)

```bash
git pull   # ou git pull --rebase
```

### Ao aplicar commit (cherry-pick) ou rebase

```bash
git cherry-pick <hash>
git rebase -i HEAD~3
```

## 📄 Anatomia de um conflito

Quando há conflito, o Git insere marcadores no arquivo:

```
<<<<<<< HEAD
alteração feita na branch atual
=======
alteração vinda da outra branch
>>>>>>> nome-da-outra-branch
```

| Marcador | Significado |
| -------- | ----------- |
| `<<<<<<< HEAD` | Início da versão **sua** (branch atual) |
| `=======` | Separação entre as duas versões |
| `>>>>>>>` | Fim da versão da **outra** branch |

---

## 🛠️ Como resolver passo a passo

### 1. Abrir o editor e escolher a versão

Edite o arquivo diretamente: **delete os marcadores** `<<<<<<<`, `=======`, `>>>>>>>` e deixe apenas o conteúdo desejado — pode ser a sua versão, a outra, ou uma combinação das duas.

### 2. Marcar como resolvido

```bash
# Depois de editar, adicionar o arquivo ao staging
git add arquivo.txt
```

### 3. Finalizar a operação

* Depois de um **merge**:
  ```bash
  git commit
  ```
* Depois de um **rebase** / **cherry-pick**:
  ```bash
  git rebase --continue
  ```

---

## 🧰 Ferramenta de resolução de conflitos

```bash
# Abrir a ferramenta de merge configurada (ex: vimdiff, kdiff3, meld)
git mergetool

# Configurar/trocar a ferramenta
git config --global merge.tool meld
```

> Dica: em muitos editores (VS Code, JetBrains), o arquivo conflitante aparece destacado e você resolve clicando em "Accept Incoming / Current".

---

## 📍 Como escolher a versão (atalhos)

Você pode aceitar rapidamente uma das versões sem abrir editor:

```bash
# Aceitar a versão da branch atual (HEAD)
git checkout --ours arquivo.txt

# Aceitar a versão da outra branch
git checkout --theirs arquivo.txt

# Depois adicionar ao staging
git add arquivo.txt
```

> ⚠️ `--ours` / `--theirs` têm sentido **invertido durante um rebase** (ours = a branch que está sendo rebaseada). Confirme com `git status` antes de usar.

---

## 🛡️ Prevenindo conflitos

* **Sempre puxe antes de trabalhar**: `git pull` (ou `git pull --rebase`) no início do dia.
* **Branches de curta duração**: menos tempo afastado = menos chance de divergência.
* **Merges pequenos e frequentes** em vez de um rebase gigante no final.
* **Squash de commits** antes de merge mantém o histórico linear.
* **Branches específicas por feature** reduzem edições simultâneas no mesmo arquivo.

---

## 🆘 Desfazer em caso de erro

```bash
# Abortar merge e voltar ao estado anterior
git merge --abort

# Abortar rebase
git rebase --abort

# Abortar cherry-pick
git cherry-pick --abort

# Abortar um merge em andamento de forma durável
git reset --hard HEAD  # cuidado: descarta alterações
```

---

## ✅ Checklist de resolução de conflito

1. `git status` para listar os arquivos conflitantes
2. Abrir cada arquivo e remover os marcadores, escolhendo a versão final
3. `git add arquivo` para cada arquivo resolvido
4. `git commit` (merge) ou `git rebase --continue` / `git cherry-pick --continue`
5. Rodar testes para confirmar que nada quebrou

---

## 📚 Referências

* Documentação Oficial do Git - Resolving a merge conflict
* Pro Git Book - Capítulo sobre merge