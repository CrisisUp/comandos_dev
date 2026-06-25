# 📘 Boas Práticas no Git

> Guia de boas práticas para usar Git de forma eficiente e profissional

---

## 📝 Mensagens de Commit

### Estrutura padrão

```markdown
<tipo>(<escopo>): <assunto>

<corpo>

<rodapé>
```

Tipos de commit

| Tipo | Descrição | Exemplo |
| ---- | --------- | ------- |
| feat | Nova funcionalidade | feat(auth): adiciona login com Google |
| fix | Correção de bug | fix(api): corrige erro 500 no endpoint /users |
| docs | Documentação | docs(readme): atualiza instruções de instalação |
| style | Formatação (sem mudança de código) | style: ajusta indentação do arquivo main.js |
| refactor | Refatoração (sem mudança de funcionalidade) | refactor(user): simplifica lógica de validação |
| test | Testes | test(auth): adiciona testes para login |
| chore | Tarefas de manutenção | chore(deps): atualiza dependências |
| perf | Melhoria de performance | perf(query): otimiza consulta ao banco |
| ci | Configuração de CI/CD | ci: adiciona pipeline de deploy |
| build | Build/dependências | build: configura webpack para produção |

* `Exemplos de boas mensagens`

```bash
# ✅ Bom
git commit -m "feat(auth): adiciona autenticação com JWT"
git commit -m "fix(api): corrige validação de email no cadastro"
git commit -m "docs(readme): adiciona seção de configuração"

# ❌ Ruim
git commit -m "update"
git commit -m "fix"
git commit -m "mudanças"
git commit -m "coisas"
```

* `Corpo do commit (para commits complexos)`

```bash
git commit -m "feat(payment): implementa integração com Stripe

- Adiciona webhook para confirmação de pagamento
- Implementa validação de cartão de crédito
- Adiciona logs para rastreamento de transações

Closes #123"
```

🌿 Estratégias de Branch

* `Git Flow (Recomendado para projetos grandes)`

```markdown
- main      → Código em produção (sempre estável)
- develop   → Integração de novas features
- feature/* → Novas funcionalidades
- release/* → Preparação para lançamento
- hotfix/*  → Correções urgentes em produção
```

* `GitHub Flow (Recomendado para projetos pequenos)`

```markdown
- main         → Código em produção
- feature/*    → Novas funcionalidades
- bugfix/*     → Correções de bugs
- hotfix/*     → Correções urgentes
```

* `Nomenclatura de branches`

```bash
# ✅ Bom
- feature/autenticacao-jwt
- feature/pagamento-stripe
- bugfix/validacao-email
- hotfix/corrige-login
- release/v1.2.0

# ❌ Ruim
- feature
- nova-branch
- teste
- minha-branch
```

## 🔀 Boas Práticas de Merge

* `Prefira merge com --no-ff (no-ff)`

```bash
# Mantém histórico de merges
git merge --no-ff feature/nova-funcionalidade

# Em vez de:
git merge feature/nova-funcionalidade  # fast-forward
```

* `Mantenha histórico limpo com squash`

```bash
# Squash antes de merge (quando muitos commits pequenos)
git merge --squash feature/nova-funcionalidade
git commit -m "feat: implementa nova funcionalidade"
```

* `Use pull com rebase`

```bash
# Mantém histórico linear
git pull --rebase

# Configurar como padrão
git config --global pull.rebase true
```

## 🛡️ Antes de Commitar

* `Checklist de pré-commit`

```bash
# 1. Verificar o que foi modificado
git status

# 2. Verificar o diff
git diff

# 3. Verificar o que está no staging
git diff --staged

# 4. Rodar testes (se aplicável)
npm test
pytest
./run-tests.sh

# 5. Verificar lint
npm run lint
```

* `Commits atômicos`

```markdown
✅ **Faça commits atômicos:**
- Um commit por funcionalidade
- Um commit por correção de bug
- Commits pequenos e focados

❌ **Evite:**
- Commits com múltiplas funcionalidades
- Commits gigantes com muitas alterações
- Commits com mudanças não relacionadas
```

## 📂 .gitignore

* `Sempre use .gitignore`

```bash
# Criar .gitignore
touch .gitignore
```

Exemplos de .gitignore

```bash
# Dependências
node_modules/
vendor/
venv/
.venv/
env/
.env

# Arquivos de build
dist/
build/
*.log
*.exe
*.dll
*.so
*.dylib

# Arquivos do sistema
.DS_Store
Thumbs.db
desktop.ini

# Arquivos de IDE
.vscode/
.idea/
*.swp
*.swo
*~
*.iml

# Arquivos sensíveis
*.pem
*.key
*.crt
*.p12
.secrets
.env.local
.env.production

# Logs
logs/
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
```

* `.gitignore global`

```bash
## Criar .gitignore global`
git config --global core.excludesfile ~/.gitignore_global

## Adicionar arquivos comuns
echo ".DS_Store" >> ~/.gitignore_global
echo "Thumbs.db" >> ~/.gitignore_global
echo "*.log" >> ~/.gitignore_global
```

## 🔒 Segurança

* `Nunca commite dados sensíveis`

```bash
# ❌ NUNCA FAÇA ISSO
- git add .env
- git add secrets.json
- git add *.pem
- git add config/credentials.yml

# ✅ Faça:
# 1. Adicione ao .gitignore
- echo ".env" >> .gitignore
- echo "*.pem" >> .gitignore

# 2. Use variáveis de ambiente
# 3. Use ferramentas como Vault ou AWS Secrets Manager
```

* `Como remover dados sensíveis do histórico`

```bash
# Usar BFG Repo-Cleaner
java -jar bfg.jar --delete-files .env

# Ou git filter-branch
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch .env" \
  --prune-empty --tag-name-filter cat -- --all
```

## 📊 Manter Histórico Limpo

* `Rebase interativo (squash)`

```bash
# Juntar últimos 3 commits
git rebase -i HEAD~3

# No editor:
# pick -> squash (manter mensagem)
# pick -> fixup (descartar mensagem)
```

* `Regras de ouro do rebase`

```markdown
✅ **Quando usar rebase:**
- Antes de fazer push
- Em branches locais (não compartilhadas)
- Para manter histórico linear

❌ **Quando NÃO usar rebase:**
- Em branches públicas/compartilhadas
- Em branches que outras pessoas estão usando
- Quando não tem certeza do que está fazendo
🔍 Revisão de Código (Pull Requests)
```

* `Boas práticas de PR`

```markdown
✅ Tamanho ideal:
- Máximo de 400-500 linhas alteradas
- Máximo de 10-15 arquivos alterados
- PRs grandes: quebrar em partes menores

✅ Título descritivo:
- "feat(auth): adiciona login com biometria"

✅ Descrição do PR:
- O que foi feito
- Por que foi feito
- Como testar
- Screenshots (se aplicável)
- Issues relacionadas: Closes #123

✅ Checklist:
- [ ] Testes passam
- [ ] Código revisado
- [ ] Documentação atualizada
- [ ] Sem dados sensíveis
```

* `Template de PR`

```markdown
## Descrição
[Descrição das mudanças]

## Tipo de mudança
- [ ] Bug fix
- [ ] Nova funcionalidade
- [ ] Documentação
- [ ] Refatoração

## Como testar
1. [Passo 1]
2. [Passo 2]
3. [Passo 3]

## Screenshots
[Adicionar screenshots se aplicável]

## Issues relacionadas
Closes #123
```

## 🔄 Workflows Recomendados

* `Para projetos individuais`

```bash
# Workflow simples
git checkout -b feature/nova-funcionalidade
git add .
git commit -m "feat: implementa nova funcionalidade"
git push -u origin feature/nova-funcionalidade

# Criar PR no GitHub para times
# Git Flow simplificado
git checkout develop
git pull
git checkout -b feature/nova-funcionalidade

# Desenvolvimento...
git add .
git commit -m "feat: implementa nova funcionalidade"
git push -u origin feature/nova-funcionalidade

# Criar PR para develop
Hotfix em produção
bash
# Correção urgente
git checkout main
git pull
git checkout -b hotfix/corrige-bug

# Corrigir bug...
git add .
git commit -m "fix: corrige bug crítico"
git push -u origin hotfix/corrige-bug

# Criar PR para main e depois merge para develop

## 🛠️ Ferramentas Úteis
* `Git Aliases úteis`

```bash
# Configurar aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"
git config --global alias.tree "log --oneline --graph --all"

# Alias para ver commits recentes
git config --global alias.recent "log --oneline -10"

# Alias para stats
git config --global alias.stats "shortlog -sn --all --no-merges"
```

* `Hooks úteis`

```bash
# .git/hooks/pre-commit
#!/bin/bash
echo "🔍 Rodando verificações..."

# Rodar linter
npm run lint || exit 1

# Rodar testes
npm test || exit 1

echo "✅ Verificações passaram!"
```

## 📋 Checklist de Boas Práticas

### Diário

* `git status` antes de começar
* `git pull` para atualizar
* Commits atômicos com mensagens descritivas
* `git push` apenas após testes

### Antes de merge

* Branch atualizada com `main/develop`
* Todos os testes passam
* Sem conflitos
* Mensagens de commit claras
* Código revisado

### Segurança

* Sem dados sensíveis no commit
* .gitignore configurado
* Variáveis de ambiente em arquivo separado
* Permissões corretas para arquivos

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Mensagens de commit | `<tipo>(<escopo>): <assunto>` |
| Tamanho do commit | Atômico, focado em uma coisa |
| Branches | `feature/*, bugfix/*, hotfix/*` |
| Merge | Preferir `--no-ff` |
| Pull | Preferir `--rebase` |
| .gitignore | Sempre configurar |
| Dados sensíveis | Nunca commitar |
| PRs | Pequenos, descritivos, revisados |

## 📚 Referências

* `Conventional Commits`
* `Git Flow`
* `GitHub Flow`
* `Pro Git Book`
