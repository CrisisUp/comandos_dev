# 📘 Comandos Básicos do Python

> Guia essencial para começar a usar Python, o gerenciador de pacotes pip e os ambientes virtuais no dia a dia

---

## 🔧 Instalação e Configuração

* `Instalar Python`

```bash
# macOS (Homebrew)
brew install python

# Ubuntu/Debian
sudo apt install python3 python3-pip python3-venv

# Windows (choco)
choco install python3
```

* `Verificar instalação`

```bash
python3 --version
pip3 --version
python3 -m pip --version
```

* `Observação sobre o comando`

```bash
# Em muitos sistemas, python3 e pip3; em outros, python e pip
python --version
pip --version    # pode ser necessário/padrão em alguns ambientes
```

## 🚀 Ambientes Virtuais

* **Criar e ativar**

```bash
# Criar ambiente virtual
python3 -m venv .venv

# Ativar no Linux/macOS
source .venv/bin/activate

# Ativar no Windows
.venv\Scripts\activate

# Desativar
deactivate

# Ver pacotes do env
pip list
```

* `Gerenciar ambientes`

```bash
# Usar uma versão específica: chame o python desejado para criar o venv
python3.11 -m venv .venv

# (gerenciadores avançados: pyenv, uv, poetry, conda)
```

## 📦 Gerenciamento de Pacotes (pip)

* `Instalar pacotes`

```bash
# Instalar um pacote
pip install requests

# Instalar versão específica
pip install requests==2.31.0

# Instalar de requirements.txt
pip install -r requirements.txt

# Instalar pacote local (editável, desenvolvimento)
pip install -e .
```

* `Gerenciar pacotes`

```bash
# Listar instalados
pip list

# Ver detalhes
pip show requests

# Atualizar um pacote
pip install --upgrade requests

# Remover pacote
pip uninstall requests
```

* `Gerar requirements`

```bash
# Exportar dependências
pip freeze > requirements.txt
pip freeze --local > requirements.txt

# Listar dependências de topo (não exigidas por outras)
pip list --not-required
```

## 🏃 Execução de Scripts

* `Rodar código Python`

```bash
# Executar um arquivo
python3 script.py
python3 caminho/para/script.py

# Executar com argumentos
python3 script.py arg1 arg2

# Modo interativo (REPL)
python3
python3 -i script.py   # abre REPL após rodar
```

* `Modo interativo`

```bash
# Ajuda dentro do REPL
help(requests)

# Histórico / sair
exit()
# ou Ctrl+D
```

* `Flags úteis`

```bash
python3 -c "print('direto')"          # executar código inline
python3 -m http.server 8000            # servidor HTTP simples
python3 -m json.tool arquivo.json      # formatar/validar JSON
python3 -m unittest                    # rodar testes
```

## 🧪 Testes

* `Rodar testes com unittest`

```bash
python3 -m unittest discover
python3 -m unittest tests.test_modulo
python3 -m unittest -v
```

* `Rodar testes com pytest`

```bash
# Instalar
pip install pytest

# Rodar
pytest
pytest tests/
pytest tests/test_modulo.py -v
pytest -k "nome_teste"                # filtrar por nome
pytest --pdb                           # debug no primeiro erro
```

## 🔍 Lint e Formatação

* `Lint`

```bash
# ruff (rápido, moderno)
ruff check .
ruff check . --fix

# flake8 (clássico)
flake8 .
```

* `Formatação`

```bash
# black (padrão popular)
black .
black --check .

# ruff format
ruff format .
```

* `Tipagem`

```bash
# mypy (checador de tipos estáticos)
mypy meu_modulo.py
mypy src/ --ignore-missing-imports
```

## 🧠 Módulos da Biblioteca Padrão

```python
import os, sys, json, re
import datetime as dt
from pathlib import Path
```

```bash
# Ver módulos disponíveis
help('modules')
```

## 🆘 Ajuda

```bash
python3 --help
python3 -m pip --help
pytest --help
```

## 📋 Checklist Diário

| Comando | Descrição |
| ------- | --------- |
| `python3 -m venv .venv` | Criar ambiente |
| `source .venv/bin/activate` | Ativar ambiente |
| `pip install -r requirements.txt` | Instalar deps |
| `python3 script.py` | Rodar script |
| `pytest` | Rodar testes |
| `ruff check .` | Lint |
| `ruff format .` | Formatar |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Ambiente** | `python3 -m venv`, `source activate` |
| **Pacotes** | `pip install`, `pip freeze` |
| **Executar** | `python3 script.py`, REPL |
| **Testes** | `pytest`, `unittest` |
| **Lint** | `ruff`, `flake8`, `mypy` |
| **Formatação** | `ruff format`, `black` |
| **Ajuda** | `--help`, REPL `help()` |

## 📚 Referências

* Python Documentation (docs oficial)
* PyPI (índice de pacotes)
* The Python Standard Library
* docs.python.org/tutorial

Pronto para programar Python com confiança! 🐍🚀