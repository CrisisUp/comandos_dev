# 📘 Boas Práticas no Python

> Guia de boas práticas para escrever Python limpo, idiomático (PEP 8), testável e seguro

---

## 📝 Estilo e Conformidade (PEP 8)

* `Convenções básicas`

```python
# ✅ snake_case para funções/variáveis, PascalCase para classes
def calcular_total():
    pass

class ConfigUsuario:
    pass

# ✅ Espaço em volta de operadores
total = preco * quantidade

# ❌ Não misture tabs e espaços; 4 espaços
# ❌ Linhas muito longas (> 88/100)
```

* `Escape manual é desnecessário`

```python
# ✅ Use f-strings (Python 3.6+)
nome = "Ana"
print(f"Olá, {nome}!")

# ❌ Concatenação + ou % antigos quando f-string serve
```

* `Nomes claros`

```python
# ❌ data, tmp, x são vagos
# ✅ data_de_nascimento, arquivo_temporario, quantidade
```

## 🧠 Idiotismo (Pythonic)

* `Use compreensão de listas`

```python
# ✅
pares = [x for x in numeros if x % 2 == 0]

# ❌ loop manual verboso
```

* `Itere diretamente`

```python
# ✅
for item in colecao:
    processar(item)

# ❌ for i in range(len(colecao)): colecao[i]
```

* `Use with para recursos`

```python
# ✅ Fecha automaticamente
with open("dados.txt") as f:
    dados = f.read()

# ❌ f = open(...) sem fechar
```

* `Desempacotamento`

```python
# ✅
a, b = b, a          # troca
nome, *resto = itens
```

## 🧪 Testabilidade

* `Funções puras sempre que possível`

```python
# ✅ Sem efeitos colaterais, fácil de testar
def calcular_imposto(valor, aliquota):
    return valor * aliquota

# ❌ Função que lê arquivo, imprime e mexe no banco
```

* `Testes nomeados e organizados`

```python
# tests/test_calculo.py
def test_calcula_imposto_correto():
    assert calcular_imposto(100, 0.1) == 10
```

## 🔒 Segurança

* `Nunca use eval()/exec() com entrada do usuário`

```python
# ❌ Perigoso
eval(entrada_usuario)

# ✅ Use json, yaml (parser) ou busca em dict
```

* `Ambiente e segredos`

```python
# ✅ Use variáveis de ambiente para segredos
import os
DB_PASS = os.environ.get("DB_PASS")

# ❌ Senha hardcoded no código
```

* `Cuidado com `pickle` de fontes não confiáveis`

## 📦 Ambiente e Dependências

* `Ambiente isolado por projeto`

```bash
# ✅ Sempre um venv por projeto
python3 -m venv .venv
source .venv/bin/activate

# ❌ Instalar no Python global do sistema
```

* `Dependências declaradas`

```bash
# ✅ requirements.txt / pyproject.toml versionado
pip freeze > requirements.txt

# ✅ Prefira pyproject.toml (pip, uv, poetry)
```

## ⚡ Performance

* `Ferramenta certa para dados grandes`

```python
# ✅ numpy/pandas para arrays numéricos
import numpy as np
arr = np.array([1, 2, 3])

# ❌ loops Python para math pesada
```

* `Evite re-inventar`

```python
# ✅ Use a stdlib
from collections import deque       # fila
from itertools import groupby       # agrupamento
from functools import lru_cache     # cache
```

* `Métodos certos para string`

```python
# ✅
" ".join(lista)          # concatenação de muitos itens
texto.startswith("x")    # em vez de texto[:1] == "x"
```

## 🔤 Tipagem

* `Type hints para código crítico`

```python
# ✅ Ajuda IDEs e mypy
def soma(a: int, b: int) -> int:
    return a + b
```

* `Rodar mypy em CI`

```bash
mypy src/
```

## 📋 Checklist de Boas Práticas Python

* ✅ PEP 8: 4 espaços, snake_case, f-strings
* ✅ `with` para arquivos/recursos
* ✅ Compreensões e iteradores diretos
* ✅ Testes com funções puras
* ✅ Sem `eval` com entrada não confiável
* ✅ Segredos via variáveis de ambiente
* ✅ Venv por projeto
* ✅ Type hints + mypy
* ✅ `ruff`/`black` no CI

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Estilo | PEP 8, `ruff`/`black` |
| Strings | f-strings |
| Recursos | `with` |
| Testes | Funções puras, `pytest` |
| Segurança | Sem `eval`, segredos em env |
| Ambientes | Venv por projeto |
| Tipagem | Type hints + mypy |
| Perf | stdlib, numpy |

## 📚 Referências

* PEP 8 (Guia de Estilo)
* PEP 20 (Zen of Python)
* The Python Tutorial
* Real Python - Best Practices

Pronto para escrever Python com boas práticas! 🐍🚀