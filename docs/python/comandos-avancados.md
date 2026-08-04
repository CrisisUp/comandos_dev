# 📘 Comandos Avançados do Python

> Guia para usuários que já dominam o básico e querem geradores, decoradores, tipagem avançada, async, profiling e tooling

---

## 🧬 Estruturas de Dados Avançadas

* `Modulo collections`

```python
from collections import Counter, defaultdict, deque, namedtuple

# Contagem
Counter("abracadabra")              # {'a': 5, 'b': 2, ...}

# Dict com valor padrão
dd = defaultdict(list)
dd["chave"].append(1)               # não levanta KeyError

# Fila dupla
dq = deque([1, 2, 3])
dq.appendleft(0)
dq.popleft()

# Tupla nomeada
Ponto = namedtuple("Ponto", ["x", "y"])
p = Ponto(1, 2)
p.x
```

* `Comprehensions e geradores`

```python
# List comprehension
quadrados = [x**2 for x in range(10) if x % 2 == 0]

# Dict comprehension
m = {x: x**2 for x in range(4)}

# Set comprehension
unis = {c for c in "aabbcc"}

# Gerador (lazy, economia de memória)
gen = (x**2 for x in range(1_000_000))
next(gen)
```

## 🧠 Geradores e Iteradores

```python
# Função geradora (yield)
def contador(n):
    for i in range(n):
        yield i

for v in contador(3):
    print(v)

# Consumo sob demanda
# vs list() que materializa tudo em memória
```

## 🎨 Decoradores

* `Decorador simples`

```python
import time

def cronometra(func):
    def wrapper(*args, **kwargs):
        t0 = time.perf_counter()
        r = func(*args, **kwargs)
        print(f"{func.__name__} levou {time.perf_counter()-t0:.3f}s")
        return r
    return wrapper

@cronometra
def trabalho():
    time.sleep(0.1)

trabalho()
```

* `Decorador com argumentos`

```python
def repetir(vezes):
    def decorador(func):
        def wrapper(*args, **kwargs):
            for _ in range(vezes):
                func(*args, **kwargs)
        return wrapper
    return decorador

@repetir(3)
def oi():
    print("olá")
```

## 🔤 Tipagem Avançada

```python
from typing import Optional, Union, Callable, Any, Literal
from typing import TypeVar, Generic, Protocol
```

* `Generics`

```python
T = TypeVar("T")

def primeiro(lista: list[T]) -> T:
    return lista[0]
```

* `Callable e Literal`

```python
from collections.abc import Callable

# Função que recebe uma função
def aplicar(fn: Callable[[int], int], x: int) -> int:
    return fn(x)

# Valores permitidos
def modo_motor(m: Literal["eco", "sport", "normal"]) -> None:
    pass
```

* `Python 3.10+: Union | None`

```python
def f(x: int | None = None) -> int:
    return x if x is not None else 0
```

## ⚡ Assíncronia (async/await)

```python
import asyncio

async def baixar(url):
    await asyncio.sleep(0.1)   # simula I/O
    return f"dados de {url}"

async def main():
    resultados = await asyncio.gather(
        baixar("a.com"),
        baixar("b.com"),
        baixar("c.com"),
    )
    print(resultados)

asyncio.run(main())
```

```bash
# httpx / aiohttp para HTTP assíncrono
pip install httpx
```

## 🧵 Concorrência e Parallelismo

* `Threads (déficit para I/O)`

```python
from concurrent.futures import ThreadPoolExecutor

# Use uma função síncrona (async não roda em ThreadPool sem await)
def baixar_sync(url):
    return f"dados de {url}"

with ThreadPoolExecutor(max_workers=4) as ex:
    futuros = [ex.submit(baixar_sync, url) for url in urls]
    for f in futuros:
        print(f.result())
```

* `Processos (CPU-bound)`

```python
from concurrent.futures import ProcessPoolExecutor

def calcular(x):
    return x * x  # trabalho pesado de CPU

with ProcessPoolExecutor() as ex:
    resultados = list(ex.map(calcular, range(10)))
```

## 📊 Profiling e Performance

```bash
# CPU profile
python3 -m cProfile -s cumulative script.py

# Tempo de execução
python3 -m timeit "sum(range(100))"

# Memória
pip install memory-profiler
python3 -m memory_profiler script.py
```

* `cProfile + pstats`

```bash
python3 -m cProfile -o perfil.prof script.py
python3 -c "import pstats; pstats.Stats('perfil.prof').sort_stats('cumulative').print_stats(10)"
```

## 🔄 Ferramentas de Projeto

* `Empacotamento (build)`

```bash
# build da distribuição
python3 -m build
# gera dist/*.whl e *.tar.gz

# Publicar no PyPI (twine)
pip install twine
twine upload dist/*
```

* `pip-tools (dependências reproduzíveis)`

```bash
pip install pip-tools
pip-compile requirements.in -o requirements.txt
pip-sync
```

* `Linting no CI robusto`

```bash
ruff check . --fix
ruff format --check .
mypy src/
```

## 🧪 Testes Avançados

* `Fixture e monkeypatch`

```python
import pytest

@pytest.fixture
def cliente():
    return criar_cliente_teste()

def test_usando_fixture(cliente):
    assert cliente.status == "ok"

def test_monkeypatch(monkeypatch):
    monkeypatch.setattr("os.getcwd", lambda: "/tmp")
    assert os.getcwd() == "/tmp"
```

* `Parametrize`

```python
@pytest.mark.parametrize("a,b,esp", [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
])
def test_soma(a, b, esp):
    assert a + b == esp
```

* `Cobertura`

```bash
pytest --cov=meu_modulo --cov-report=html
```

## 🧠 Módulos Especiais

* `Dataclasses`

```python
from dataclasses import dataclass

@dataclass
class Usuario:
    nome: str
    idade: int = 0
```

* `Enum`

```python
from enum import Enum

class Cor(Enum):
    VERMELHO = 1
    VERDE = 2
```

* `Context managers`

```python
from contextlib import contextmanager

@contextmanager
def abre():
    print("abrir")
    yield
    print("fechar")

with abre():
    print("corpo")
```

## 📋 Checklist de Python Avançado

| Tema | Ferramenta |
| ---- | ---------- |
| Perf | `cProfile`, `timeit`, `memory-profiler` |
| Async | `asyncio`, `httpx` |
| Concorrência | `ThreadPool`, `ProcessPool` |
| Tipagem | `mypy`, `typing` |
| Testes | `pytest`, fixtures, parametrize |
| Empacotamento | `wheel`, `twine` |
| Lint | `ruff` |
| Estruturas | `collections`, `dataclasses` |

## 📚 Referências

* Python Docs - asyncio
* Python Docs - typing
* Python Docs - cProfile
* Real Python

Pronto para dominar Python em produção! 🐍🚀