## 1. Por que estruturar ferramentas Python?

Quando começamos a programar, é comum escrever toda a lógica em um único arquivo:

```
import requests

url = "https://jsonplaceholder.typicode.com/posts"
response = requests.get(url, timeout=5)

if response.status_code == 200:
    posts = response.json()

    for post in posts:
        print(post["title"])
```

Esse código é adequado para demonstrar uma requisição HTTP simples. Entretanto, quando uma ferramenta começa a crescer, podem surgir novas responsabilidades:

- Receber argumentos do terminal.
    
- Validar os dados fornecidos pelo usuário.
    
- Realizar requisições HTTP.
    
- Processar respostas.
    
- Salvar arquivos.
    
- Registrar erros e eventos.
    
- Executar testes.
    
- Compartilhar funções entre diferentes módulos.
    

Colocar tudo em um arquivo pode dificultar a manutenção, a reutilização e os testes.

**Estruturar um projeto significa separar responsabilidades em módulos que trabalham em conjunto.** Não é necessário criar dezenas de arquivos: a organização deve acompanhar a complexidade real da ferramenta.

## 2. O que são `tools` e `utils`?

Os termos `tools` e `utils` são convenções de nomenclatura, não palavras reservadas nem recursos especiais do Python.

### Tools

Uma _tool_ é uma ferramenta executável que realiza uma tarefa identificável.

Exemplos:

- Um verificador de disponibilidade de serviços.
    
- Um coletor de informações de uma API.
    
- Um analisador de arquivos de log.
    
- Um enumerador de recursos de uma aplicação autorizada.
    
- Um utilitário de linha de comando para automatizar uma rotina.
    

Uma ferramenta geralmente combina entrada, lógica de execução e saída.

### Utils

`utils`, abreviação de _utilities_, costuma representar funções auxiliares reutilizáveis.

Exemplos:

- Validar um endereço URL.
    
- Formatar uma data.
    
- Normalizar uma string.
    
- Calcular um hash de arquivo.
    
- Converter bytes para uma unidade legível.
    
- Criar diretórios quando necessário.
    

Uma função auxiliar não precisa ser um programa completo. Ela pode ser chamada por várias ferramentas.

### Diferença prática

Imagine uma ferramenta que consulta uma API e salva os resultados.

- A **tool** coordena o processo.
    
- O módulo de **API** executa a comunicação HTTP.
    
- O módulo de **utils** contém funções genéricas que ajudam no processamento.
    
- O módulo de **configuração** centraliza valores e opções.
    
- O módulo de **testes** verifica se as funções funcionam corretamente.
    

Essa separação é uma convenção de projeto. Em programas pequenos, algumas dessas responsabilidades podem permanecer juntas.

## 3. Entendendo módulos e pacotes

Antes de organizar um projeto, é importante distinguir dois conceitos.

### 3.1. Módulo

Um módulo Python é normalmente um arquivo `.py` que pode ser importado.

Considere esta estrutura:

```
meu_projeto/
├── main.py
└── utils.py
```

No arquivo `utils.py`:

```
def normalizar_texto(texto):
    return texto.strip().lower()
```

A função `normalizar_texto()` remove espaços no início e no final da string e converte o texto para letras minúsculas.

No arquivo `main.py`:

```
from utils import normalizar_texto

nome = "  PYTHON  "
resultado = normalizar_texto(nome)

print(resultado)
```

Saída:

```
python
```

A instrução:

```
from utils import normalizar_texto
```

importa a função definida no módulo `utils.py`.

Depois, `main.py` pode chamá-la como qualquer outra função disponível no escopo.

**Importante:** o nome `utils` não tem significado especial para o interpretador. O Python encontra o módulo conforme as regras de importação e os caminhos de busca disponíveis.

### 3.2. Pacote

Um pacote é uma forma de organizar módulos em uma estrutura hierárquica.

Por exemplo:

```
meu_projeto/
├── main.py
└── utils/
    ├── __init__.py
    ├── arquivos.py
    └── validacao.py
```

Nesse exemplo, `utils` é um pacote Python tradicional porque contém `__init__.py`.

No arquivo `utils/validacao.py`:

```
def validar_nome(nome):
    return isinstance(nome, str) and bool(nome.strip())
```

No arquivo `main.py`:

```
from utils.validacao import validar_nome

print(validar_nome("Python"))
print(validar_nome("   "))
```

Saída:

```
True
False
```

A expressão `utils.validacao` identifica o módulo `validacao` dentro do pacote `utils`.

O arquivo `__init__.py` pode inicializar o pacote ou definir sua interface pública. Em pacotes simples, ele pode ficar vazio. Versões modernas do Python também permitem pacotes sem esse arquivo, chamados pacotes de namespace, mas não precisamos deles neste exemplo.

## 4. Uma estrutura inicial para ferramentas

Uma ferramenta pequena ou intermediária pode começar com esta organização:

```
minha_tool/
├── main.py
├── config.py
├── api.py
├── utils.py
├── requirements.txt
├── README.md
└── tests/
    └── test_utils.py
```

Responsabilidade dos arquivos:

|Arquivo|Responsabilidade|
|---|---|
|`main.py`|Ponto de entrada e coordenação da execução.|
|`config.py`|Configurações compartilhadas e valores padrão.|
|`api.py`|Funções relacionadas à comunicação com a API.|
|`utils.py`|Funções auxiliares reutilizáveis.|
|`requirements.txt`|Dependências externas do projeto.|
|`README.md`|Instruções de instalação e utilização.|
|`tests/`|Testes automatizados.|

Essa estrutura é um ponto de partida, não uma regra obrigatória. Se a ferramenta não utiliza API, por exemplo, não há necessidade de criar `api.py`.

Da mesma forma, se `utils.py` começar a concentrar muitas funções sem relação entre si, pode ser melhor dividi-lo em módulos com responsabilidades mais específicas.

## 5. Separando a lógica do ponto de entrada

Um erro comum é colocar toda a execução diretamente no corpo do arquivo:

```
import requests

response = requests.get(
    "https://jsonplaceholder.typicode.com/posts",
    timeout=5,
)

print(response.status_code)
```

Quando esse arquivo é importado por outro módulo, a requisição é executada imediatamente.

Isso pode ser inconveniente para testes, reutilização e importações.

Uma alternativa é colocar a execução em uma função:

```
import requests


def main():
    response = requests.get(
        "https://jsonplaceholder.typicode.com/posts",
        timeout=5,
    )

    response.raise_for_status()
    print(response.status_code)


if __name__ == "__main__":
    main()
```

### Entendendo o padrão

```
def main():
```

Define a função responsável por coordenar a execução principal do programa.

O nome `main` é uma convenção comum, não uma palavra reservada.

```
if __name__ == "__main__":
```

Verifica se o arquivo está sendo executado como programa principal.

Quando o interpretador executa diretamente `python main.py`, o valor de `__name__` nesse arquivo é `"__main__"`.

Quando outro módulo importa esse arquivo, `__name__` recebe o nome do módulo, e o bloco não é executado automaticamente.

```
main()
```

Chama a função somente quando a condição anterior é verdadeira.

Esse padrão ajuda a separar **definições reutilizáveis** de **execução do programa**.

## 6. Criando um módulo de utilidades

Vamos criar um `utils.py` com funções pequenas e reutilizáveis.

```
from pathlib import Path


def normalizar_texto(texto):
    return texto.strip().lower()


def garantir_diretorio(caminho):
    diretorio = Path(caminho)
    diretorio.mkdir(parents=True, exist_ok=True)

    return diretorio


def bytes_para_kb(tamanho):
    return tamanho / 1024
```

### 6.1. Importando `Path`

```
from pathlib import Path
```

Importa a classe `Path`, da biblioteca padrão `pathlib`, para trabalhar com caminhos do sistema de arquivos de maneira orientada a objetos.

### 6.2. Normalizando texto

```
def normalizar_texto(texto):
    return texto.strip().lower()
```

A função recebe uma string, remove espaços externos com `strip()` e transforma as letras em minúsculas com `lower()`.

Exemplo:

```
normalizar_texto("  Python  ")
```

Resultado:

```
python
```

Essa função é genérica e pode ser útil em diferentes partes de um programa.

### 6.3. Garantindo a existência de um diretório

```
def garantir_diretorio(caminho):
```

Recebe um caminho de diretório.

```
diretorio = Path(caminho)
```

Converte o caminho em um objeto `Path`.

```
diretorio.mkdir(parents=True, exist_ok=True)
```

Cria o diretório, se necessário.

- `parents=True`: cria os diretórios pais que ainda não existem.
    
- `exist_ok=True`: não gera erro simplesmente porque o diretório já existe.
    

```
return diretorio
```

Retorna o objeto `Path`, permitindo que o código chamador reutilize o caminho.

Exemplo:

```
saida = garantir_diretorio("resultados/relatorios")
arquivo = saida / "resultado.txt"

print(arquivo)
```

O operador `/`, nesse contexto, combina caminhos `Path`. Ele não realiza uma divisão matemática.

### 6.4. Convertendo bytes para KB

```
def bytes_para_kb(tamanho):
    return tamanho / 1024
```

Recebe um valor numérico em bytes e divide por 1024.

Essa função utiliza a convenção binária de 1024 bytes por KiB, embora o nome `kb` seja frequentemente utilizado de maneira informal. Para maior precisão terminológica, uma versão mais clara seria `bytes_para_kib()`.

## 7. Configurações: o que colocar em `config.py`?

Um módulo de configuração pode reunir valores padrão utilizados por diferentes partes do programa.

Exemplo:

```
DEFAULT_TIMEOUT = 5
DEFAULT_OUTPUT_DIR = "resultados"
USER_AGENT = "MinhaTool/1.0"
```

Por convenção, constantes são frequentemente escritas em letras maiúsculas.

Em `api.py`, os valores podem ser importados:

```
from config import DEFAULT_TIMEOUT, USER_AGENT
```

E utilizados em uma função:

```
import requests

from config import DEFAULT_TIMEOUT, USER_AGENT


def consultar_api(url):
    headers = {"User-Agent": USER_AGENT}

    response = requests.get(
        url,
        headers=headers,
        timeout=DEFAULT_TIMEOUT,
    )

    response.raise_for_status()
    return response.json()
```

A função centraliza a lógica da requisição, enquanto a configuração mantém os valores padrão fora dela.

**Não coloque senhas, tokens ou chaves de API diretamente em arquivos de configuração versionados.** Segredos devem ser fornecidos por mecanismos apropriados, como variáveis de ambiente ou um gerenciador de segredos.

Também não é necessário criar um `config.py` apenas para armazenar um único valor usado uma vez. A abstração deve ser útil.

## 8. Responsabilidade de cada camada

Uma forma prática de pensar em um projeto é separar as responsabilidades em três camadas:

<text color="secondary" size="sm">Fluxo conceitual</text>

```
Entrada do usuário
       |
       v
main.py
Coordena a execução
       |
       v
api.py
Executa a comunicação HTTP
       |
       v
Servidor remoto
```

O módulo `utils.py` pode fornecer funções auxiliares utilizadas por `main.py`, `api.py` ou outros módulos.

A separação não precisa ser rígida. O objetivo é evitar que um único arquivo faça tudo, não criar uma arquitetura complexa para um script de poucas linhas.

Por exemplo:

- `main.py` decide qual operação executar.
    
- `api.py` sabe como realizar a requisição.
    
- `utils.py` oferece operações genéricas.
    
- `tests/` verifica o comportamento esperado.
    

Se for necessário trocar a API, boa parte da lógica de argumentos e apresentação dos resultados pode permanecer inalterada.

## 9. Dependências e ambiente virtual

Cada projeto deve declarar as dependências externas das quais precisa.

Exemplo de `requirements.txt`:

```
requests
pytest
```

Para instalar as dependências:

```
python3 -m pip install -r requirements.txt
```

Um ambiente virtual ajuda a isolar as bibliotecas de um projeto das bibliotecas de outros projetos.

Criar e ativar o ambiente:

```
python3 -m venv .venv
source .venv/bin/activate
```

No Ubuntu e em outros sistemas Linux, o segundo comando ativa o ambiente virtual no shell atual.

O arquivo `requirements.txt` acima declara os pacotes necessários, mas não fixa suas versões. Em projetos que precisam de instalações reproduzíveis, considere registrar versões compatíveis e manter uma estratégia de atualização das dependências.

Não inclua a pasta `.venv` no controle de versão. Ela pode ser recriada a partir das dependências do projeto.

## 10. Testando funções utilitárias

Funções pequenas são boas candidatas a testes unitários.

Crie `tests/test_utils.py`:

```
from utils import normalizar_texto, bytes_para_kb


def test_normalizar_texto():
    assert normalizar_texto("  PYTHON  ") == "python"


def test_bytes_para_kb():
    assert bytes_para_kb(2048) == 2
```

O pytest identifica funções cujos nomes começam com `test_`.

A instrução `assert` verifica uma condição. Se o resultado for diferente do esperado, o teste falha.

Execute:

```
python -m pytest -v
```

Os testes acima verificam o comportamento de funções puras ou praticamente isoladas, sem depender de rede ou de um serviço externo.

Para testar `garantir_diretorio()`, utilize um diretório temporário fornecido pelo pytest com o parâmetro `tmp_path`, evitando criar arquivos de teste em locais arbitrários do sistema.

Exemplo:

```
def test_garantir_diretorio(tmp_path):
    caminho = tmp_path / "saida" / "relatorios"

    resultado = garantir_diretorio(caminho)

    assert resultado.exists()
    assert resultado.is_dir()
```

Esse exemplo pressupõe que `garantir_diretorio` foi importada no arquivo de teste. `tmp_path` é uma funcionalidade do pytest que fornece um diretório temporário exclusivo para o teste.

## 11. Quando dividir um módulo?

Não existe um número universal de linhas a partir do qual um arquivo deva ser dividido.

Considere criar um módulo separado quando:

- O arquivo contém funções com responsabilidades diferentes.
    
- A mesma lógica é repetida em vários lugares.
    
- O módulo pode ser testado de forma independente.
    
- As importações e dependências começam a ficar difíceis de entender.
    
- Uma mudança simples exige editar partes sem relação entre si.
    
- O nome do arquivo deixa de representar claramente o que ele contém.
    

Por outro lado, evite criar arquivos separados para cada função trivial. Uma ferramenta pode ter poucos módulos bem definidos e ainda assim ser organizada.

## 12. Erros comuns na organização de projetos

### Importações circulares

Acontecem quando dois módulos dependem um do outro de forma circular.

Por exemplo, `api.py` importa algo de `utils.py`, enquanto `utils.py` também importa algo de `api.py`.

Esse arranjo pode causar erros de importação ou comportamentos difíceis de compreender. Em geral, prefira que funções genéricas não dependam dos módulos de nível mais alto da aplicação.

### Código executado durante a importação

Iniciar requisições, apagar arquivos ou executar tarefas automaticamente ao importar um módulo pode produzir efeitos inesperados.

Defina funções e classes nos módulos e mantenha a execução principal protegida por `if __name__ == "__main__":` quando for apropriado.

### Um `utils.py` para tudo

Um módulo de utilidades pode acabar misturando operações de arquivos, HTTP, autenticação, parsing e formatação.

Quando isso acontecer, agrupe as funções por responsabilidade, usando nomes claros, como `file_utils.py`, `http_utils.py` ou `validators.py`, conforme a necessidade real.

### Abstrações desnecessárias

Uma função que apenas repete uma chamada simples e é usada uma única vez pode não precisar de um módulo próprio.

Abstrações são úteis quando melhoram a reutilização, a clareza, os testes ou a manutenção.

### Caminhos dependentes do diretório atual

Um caminho relativo como `"resultados/arquivo.txt"` é interpretado em relação ao diretório de trabalho atual do processo, que nem sempre é a pasta onde está o script.

Quando a localização do arquivo precisar ser relativa ao próprio módulo, considere utilizar `Path(__file__).resolve().parent` como ponto de referência.

## 13. Exemplo de uso integrado

Com os arquivos `main.py`, `utils.py` e `config.py` criados, uma pequena ferramenta pode combinar as responsabilidades:

```
from config import DEFAULT_OUTPUT_DIR
from utils import garantir_diretorio, normalizar_texto


def main():
    nome = input("Nome do relatório: ")
    nome = normalizar_texto(nome)

    diretorio = garantir_diretorio(DEFAULT_OUTPUT_DIR)
    arquivo = diretorio / f"{nome}.txt"

    arquivo.write_text(
        f"Relatório: {nome}\n",
        encoding="utf-8",
    )

    print(f"Arquivo criado: {arquivo}")


if __name__ == "__main__":
    main()
```

Nesse exemplo:

- `main()` coordena a execução.
    
- `normalizar_texto()` prepara a entrada.
    
- `garantir_diretorio()` assegura a existência do diretório.
    
- `Path.write_text()` grava o conteúdo no arquivo.
    
- `encoding="utf-8"` define a codificação utilizada na escrita.
    

A ferramenta é simples, mas cada função tem uma responsabilidade identificável e pode ser reutilizada ou testada separadamente.

---
# 📜 Python — Pacotes, Imports e Estrutura Profissional de Projetos

## 1. Por que aprofundar a estrutura de um projeto?

Na anotação anterior, vimos como separar a interface de linha de comando, a lógica HTTP e as funções auxiliares em arquivos diferentes.

Essa organização funciona bem para ferramentas pequenas. Porém, conforme um projeto cresce, surgem novas necessidades:

- Separar funcionalidades em módulos e pacotes.
    
- Evitar imports confusos e dependências circulares.
    
- Executar o projeto de maneira consistente.
    
- Manter o código de produção separado dos testes.
    
- Declarar as dependências e os metadados do projeto.
    
- Facilitar a instalação e a reutilização do próprio código.
    

Para isso, vamos estudar uma estrutura que utiliza pacotes Python, o diretório `src/` e o arquivo `pyproject.toml`.

**Importante:** nem todo script precisa dessa estrutura. Para um arquivo pequeno que resolve uma tarefa simples, uma organização mais enxuta pode ser suficiente.

## 2. Módulos e pacotes

### 2.1. Módulo

Um módulo Python normalmente é um arquivo `.py` que contém código reutilizável.

Exemplo:

```
projeto/
├── main.py
└── normalizacao.py
```

No arquivo `normalizacao.py`:

```
def normalizar_texto(texto):
    return texto.strip().lower()
```

A função `normalizar_texto()` pode ser importada por outro arquivo:

```
from normalizacao import normalizar_texto

nome = normalizar_texto("  ADMIN  ")
print(nome)
```

Resultado:

```
admin
```

O comando `from normalizacao import normalizar_texto` significa:

- `from normalizacao`: procura o módulo chamado `normalizacao`.
    
- `import normalizar_texto`: importa o nome definido nesse módulo.
    

O Python não exige que o nome do módulo seja escrito com a extensão `.py`.

### 2.2. Pacote

Um pacote permite agrupar módulos relacionados em uma estrutura de diretórios.

Exemplo:

```
projeto/
└── ferramentas/
    ├── __init__.py
    ├── arquivos.py
    └── rede.py
```

Nesse exemplo:

- `ferramentas/` é o diretório do pacote.
    
- `__init__.py` pode ser utilizado para inicializar o pacote e definir sua interface pública.
    
- `arquivos.py` contém funcionalidades relacionadas a arquivos.
    
- `rede.py` contém funcionalidades relacionadas a operações de rede.
    

O arquivo `__init__.py` pode ficar vazio. Em pacotes Python tradicionais, ele indica explicitamente que o diretório é um pacote. O Python também aceita _namespace packages_ sem esse arquivo, mas não precisamos começar por esse caso.

Para importar uma função de `rede.py`:

```
from ferramentas.rede import verificar_conexao
```

A leitura é:

1. Encontrar o pacote `ferramentas`.
    
2. Encontrar o módulo `rede` dentro dele.
    
3. Importar o nome `verificar_conexao`.
    

## 3. Entendendo a notação dos imports

Considere esta estrutura:

```
projeto/
└── ferramentas/
    ├── __init__.py
    ├── arquivos.py
    └── rede.py
```

Os imports podem ser escritos de diferentes maneiras.

### Importar o módulo inteiro

```
import ferramentas.rede

ferramentas.rede.verificar_conexao()
```

O nome completo do módulo é utilizado para acessar a função.

### Importar uma função específica

```
from ferramentas.rede import verificar_conexao

verificar_conexao()
```

A função fica disponível diretamente pelo nome importado.

### Utilizar um alias

```
import ferramentas.rede as rede

rede.verificar_conexao()
```

O `as rede` define um nome alternativo para o módulo nesse arquivo.

Aliases são úteis quando melhoram a legibilidade ou seguem convenções conhecidas. Evite criar abreviações obscuras que dificultem a leitura do código.

## 4. O que é o diretório `src/`?

O diretório `src/` é uma convenção utilizada para separar o código do pacote da raiz do projeto.

Uma estrutura possível:

```
minha-ferramenta/
├── pyproject.toml
├── README.md
├── src/
│   └── minha_ferramenta/
│       ├── __init__.py
│       ├── __main__.py
│       ├── cli.py
│       ├── config.py
│       └── utilidades.py
└── tests/
    └── test_utilidades.py
```

Observe que `minha-ferramenta` e `minha_ferramenta` têm grafias diferentes:

- `minha-ferramenta` é o nome do diretório do projeto.
    
- `minha_ferramenta` é o nome do pacote Python.
    

O hífen é comum em nomes de projetos de distribuição, mas não pode ser utilizado normalmente como parte de um identificador Python. Por isso, o pacote usa um sublinhado.

### Responsabilidade de cada elemento

|Elemento|Responsabilidade|
|---|---|
|`pyproject.toml`|Define metadados e configurações do projeto.|
|`README.md`|Explica o propósito, a instalação e o uso da ferramenta.|
|`src/`|Guarda o código-fonte do pacote.|
|`__init__.py`|Marca o pacote tradicional e pode definir sua interface pública.|
|`__main__.py`|Permite executar o pacote com `python -m`.|
|`cli.py`|Implementa a interface de linha de comando.|
|`config.py`|Centraliza configurações não secretas.|
|`utilidades.py`|Reúne funções auxiliares relacionadas ao projeto.|
|`tests/`|Contém testes automatizados.|

O benefício de `src/` é ajudar a evitar que os testes importem acidentalmente o código diretamente da raiz do repositório, mascarando problemas de instalação ou de configuração de imports.

## 5. Criando um pacote mínimo

Vamos montar um exemplo executável, sem depender de bibliotecas externas.

### 5.1. Criando a estrutura

No terminal:

```
mkdir -p minha-ferramenta/src/minha_ferramenta
mkdir -p minha-ferramenta/tests
cd minha-ferramenta
```

Explicação:

- `mkdir` cria diretórios.
    
- `-p` cria também os diretórios intermediários necessários e não falha simplesmente porque eles já existem.
    
- `cd` entra no diretório do projeto.
    

Crie os arquivos:

```
minha-ferramenta/
├── pyproject.toml
├── src/
│   └── minha_ferramenta/
│       ├── __init__.py
│       ├── __main__.py
│       ├── cli.py
│       └── utilidades.py
└── tests/
    └── test_utilidades.py
```

### 5.2. Criando uma função reutilizável

Arquivo `src/minha_ferramenta/utilidades.py`:

```
def normalizar_texto(texto):
    """Remove espaços externos e converte o texto para minúsculas."""
    return texto.strip().lower()
```

O parâmetro `texto` representa a string recebida pela função.

O método `.strip()` remove espaços em branco do início e do final. O método `.lower()` converte letras para minúsculas.

A docstring entre aspas triplas descreve o comportamento esperado da função.

Essa função é independente da interface de terminal: pode ser utilizada por uma CLI, por testes ou por outro módulo.

### 5.3. Criando a interface de linha de comando

Arquivo `src/minha_ferramenta/cli.py`:

```
import argparse

from minha_ferramenta.utilidades import normalizar_texto


def main():
    parser = argparse.ArgumentParser(
        description="Normaliza um texto."
    )

    parser.add_argument(
        "texto",
        help="Texto que será normalizado.",
    )

    args = parser.parse_args()

    resultado = normalizar_texto(args.texto)
    print(resultado)
```

O módulo importa `argparse` para processar argumentos do terminal e importa a função que implementa a operação.

O argumento `"texto"` é posicional: quem executar o programa deve fornecer um valor para ele.

A chamada `parser.parse_args()` interpreta os argumentos e os disponibiliza em `args.texto`.

Por fim, a CLI chama a função de normalização e imprime o resultado. Ela não precisa reimplementar a lógica de tratamento do texto.

### 5.4. Criando o ponto de entrada do pacote

Arquivo `src/minha_ferramenta/__main__.py`:

```
from minha_ferramenta.cli import main


if __name__ == "__main__":
    main()
```

O arquivo `__main__.py` é o ponto de entrada executado quando o pacote é iniciado com `python -m minha_ferramenta`.

A condição `if __name__ == "__main__":` evita que `main()` seja executada automaticamente caso o arquivo seja importado em outro contexto.

## 6. Configurando o `pyproject.toml`

O `pyproject.toml` é um arquivo de configuração padronizado para projetos Python. Ele pode conter metadados, dependências e configurações de ferramentas de desenvolvimento.

Crie o arquivo `pyproject.toml` na raiz:

```
[build-system]
requires = ["setuptools>=68"]
build-backend = "setuptools.build_meta"

[project]
name = "minha-ferramenta"
version = "0.1.0"
description = "Uma ferramenta simples de normalização de texto"
requires-python = ">=3.10"

[project.scripts]
normalizar-texto = "minha_ferramenta.cli:main"

[tool.setuptools]
package-dir = {"" = "src"}

[tool.setuptools.packages.find]
where = ["src"]
```

### 6.1. Seção `[build-system]`

```
[build-system]
requires = ["setuptools>=68"]
build-backend = "setuptools.build_meta"
```

- `requires`: declara a ferramenta de construção necessária para preparar o pacote.
    
- `setuptools>=68`: solicita uma versão compatível do Setuptools.
    
- `build-backend`: identifica o componente responsável pela construção do pacote.
    

Essas configurações permitem que ferramentas de empacotamento saibam como construir a distribuição.

### 6.2. Seção `[project]`

```
[project]
name = "minha-ferramenta"
version = "0.1.0"
description = "Uma ferramenta simples de normalização de texto"
requires-python = ">=3.10"
```

- `name`: nome da distribuição.
    
- `version`: versão inicial do projeto.
    
- `description`: descrição curta.
    
- `requires-python`: versões de Python aceitas pelo projeto.
    

A versão `0.1.0` é apenas um exemplo. Ela não representa uma versão oficial de uma ferramenta publicada.

### 6.3. Seção `[project.scripts]`

```
[project.scripts]
normalizar-texto = "minha_ferramenta.cli:main"
```

Essa configuração declara um comando de terminal chamado `normalizar-texto`.

O formato do valor é:

```
nome_do_pacote.modulo:funcao
```

Nesse caso:

- `minha_ferramenta.cli` identifica o módulo.
    
- `main` identifica a função chamada quando o comando é executado.
    

Depois de instalar o projeto no ambiente, o instalador cria um comando que encaminha a execução para essa função.

### 6.4. Configuração do código em `src/`

```
[tool.setuptools]
package-dir = {"" = "src"}
```

Informa ao Setuptools que os pacotes ficam sob o diretório `src/`.

```
[tool.setuptools.packages.find]
where = ["src"]
```

Instrui o mecanismo de descoberta de pacotes a procurar pacotes dentro de `src/`.

Essas seções pertencem à configuração do Setuptools. Elas não são instruções executadas diretamente pelo interpretador Python.

## 7. Criando um ambiente virtual e instalando o projeto

Na raiz do projeto, execute:

```
python3 -m venv .venv
source .venv/bin/activate
```

O primeiro comando cria um ambiente virtual. O segundo o ativa no shell.

Instale o projeto em modo editável:

```
python -m pip install -e .
```

O `-e` significa _editable install_. Durante o desenvolvimento, o ambiente fica configurado para utilizar o código-fonte do projeto sem exigir uma nova instalação após cada alteração nos arquivos Python.

O ponto `.` indica o diretório atual, onde está o `pyproject.toml`.

Confira a instalação:

```
python -m pip show minha-ferramenta
```

Esse comando exibe os metadados do pacote instalado, quando ele está disponível nesse ambiente.

## 8. Executando o pacote

Com a instalação editável concluída, execute:

```
python -m minha_ferramenta "  ADMIN  "
```

Resultado esperado:

```
admin
```

O Python procura o pacote `minha_ferramenta` no ambiente de execução e executa seu `__main__.py`.

Também é possível utilizar o comando declarado em `[project.scripts]`:

```
normalizar-texto "  ADMIN  "
```

O comando `normalizar-texto` estará disponível no ambiente virtual depois da instalação. Se o ambiente não estiver ativo, o shell pode não encontrá-lo.

## 9. Entendendo imports absolutos e relativos

Dentro de um pacote, os imports podem utilizar caminhos absolutos ou relativos.

### 9.1. Import absoluto

```
from minha_ferramenta.utilidades import normalizar_texto
```

O import identifica o pacote desde sua raiz lógica.

Vantagens:

- Deixa explícita a origem do módulo.
    
- Facilita a leitura em projetos com vários pacotes.
    
- Reduz ambiguidades quando existem módulos com nomes iguais.
    

### 9.2. Import relativo

Imagine um módulo chamado `validacao.py` dentro do pacote `minha_ferramenta`.

Um import relativo poderia ser:

```
from .utilidades import normalizar_texto
```

O ponto inicial indica o pacote atual. O Python procura `utilidades` dentro desse mesmo pacote.

Dois pontos, como em `from ..outro_pacote import funcao`, indicam subir um nível na hierarquia de pacotes, quando a estrutura permite.

Imports relativos dependem do contexto de pacote. Executar um arquivo interno diretamente, por exemplo `python src/minha_ferramenta/cli.py`, pode fazer com que os imports não funcionem como esperado.

Para esse projeto, prefira executar o pacote com `python -m minha_ferramenta` ou utilizar o comando instalado.

## 10. Criando testes para o pacote

Arquivo `tests/test_utilidades.py`:

```
from minha_ferramenta.utilidades import normalizar_texto


def test_normalizar_texto_remove_espacos():
    assert normalizar_texto("  ADMIN  ") == "admin"


def test_normalizar_texto_converte_maiusculas():
    assert normalizar_texto("PyThOn") == "python"


def test_normalizar_texto_string_vazia():
    assert normalizar_texto("") == ""
```

Os testes verificam três comportamentos:

1. A remoção de espaços externos.
    
2. A conversão para minúsculas.
    
3. O tratamento de uma string vazia.
    

A instrução `assert` compara o resultado real com o esperado. Se a condição for falsa, o teste falha.

Instale o pytest no ambiente de desenvolvimento:

```
python -m pip install pytest
```

Execute os testes:

```
python -m pytest -v
```

O modo `-v` exibe os nomes dos testes durante a execução.

Como o projeto foi instalado em modo editável, o teste importa o pacote instalado no ambiente e utiliza o código-fonte correspondente.

## 11. Por que evitar executar módulos internos diretamente?

Considere:

```
python src/minha_ferramenta/cli.py
```

Essa forma executa `cli.py` como um arquivo isolado. Dependendo do contexto de execução, o diretório de importação e a identidade do módulo podem ser diferentes daqueles esperados pelo pacote.

O problema não significa que executar arquivos diretamente seja sempre incorreto. É uma questão de contexto: um arquivo interno que depende de imports de pacote foi projetado para ser executado nesse contexto.

Para o projeto deste exemplo, use:

```
python -m minha_ferramenta "  ADMIN  "
```

Ou, depois da instalação editável:

```
normalizar-texto "  ADMIN  "
```

Essas formas deixam explícito o ponto de entrada utilizado.

## 12. Imports circulares

Um import circular ocorre quando dois módulos dependem um do outro durante a inicialização.

Exemplo conceitual:

```
modulo_a.py → importa modulo_b.py
modulo_b.py → importa modulo_a.py
```

Esse tipo de dependência pode causar erros relacionados a módulos parcialmente inicializados e tornar a ordem de importação difícil de compreender.

Uma solução frequente é reorganizar as responsabilidades:

- Colocar a lógica compartilhada em um terceiro módulo.
    
- Fazer a dependência apontar em uma única direção.
    
- Evitar que módulos de baixo nível importem a interface de linha de comando.
    
- Importar apenas o necessário em cada módulo.
    

Por exemplo, uma organização mais saudável pode ser:

```
cli.py ───────→ servicos.py
                   │
                   ▼
               utilidades.py
```

Nesse desenho, a CLI utiliza os serviços, e os serviços utilizam as funções auxiliares. As utilidades não precisam importar a CLI.

O diagrama representa uma direção de dependência possível, não uma regra universal para todos os projetos.

## 13. Estrutura mínima versus estrutura com `src/`

|Situação|Estrutura recomendada|
|---|---|
|Script pequeno e descartável|Um arquivo Python pode ser suficiente.|
|Ferramenta com alguns módulos|Separar a lógica em módulos e manter um ponto de entrada claro.|
|Projeto com várias funcionalidades e testes|Considerar pacotes, testes separados e configuração de projeto.|
|Biblioteca reutilizável ou ferramenta instalável|Considerar `pyproject.toml`, instalação e uma estrutura de pacote consistente.|
|Aplicação com muitas integrações|Separar responsabilidades por domínio e controlar as dependências entre módulos.|

Não adicione arquivos apenas para deixar o projeto aparentemente profissional. Cada módulo deve ter uma responsabilidade compreensível.

## 14. Erros comuns

### `ModuleNotFoundError`

O Python não encontrou o módulo ou pacote solicitado.

Verifique:

- Se o nome do pacote está correto.
    
- Se o ambiente virtual está ativo.
    
- Se o projeto foi instalado quando necessário.
    
- Se o comando está sendo executado no contexto esperado.
    

### Comando de terminal não encontrado

Se `normalizar-texto` não for reconhecido, confira se a instalação editável foi realizada no ambiente ativo e se esse ambiente está ativado.

### Import relativo sem pacote pai

Imports como `from .utilidades import ...` dependem do contexto de pacote. Executar um arquivo diretamente pode remover esse contexto.

Utilize o ponto de entrada previsto pelo projeto.

### Testes passam, mas o pacote não funciona após instalação

Isso pode indicar que os testes estavam encontrando módulos pela raiz do repositório, em vez de utilizar a instalação esperada.

A estrutura `src/` ajuda a revelar esse tipo de problema, mas a configuração e o ambiente de execução ainda precisam estar corretos.

### Dependências instaladas no ambiente errado

Utilize:

```
python -m pip install pytest
```

em vez de depender de um comando `pip` que talvez pertença a outra instalação de Python.

O formato `python -m pip` associa a instalação de pacotes ao interpretador invocado.

## 15. Relação com ferramentas de automação e segurança

A estrutura de pacotes é útil quando uma ferramenta deixa de ser um script isolado e passa a ter diferentes componentes.

Por exemplo, uma ferramenta de inventário de ativos autorizados poderia ser organizada assim:

```
inventario/
├── pyproject.toml
├── README.md
├── src/
│   └── inventario/
│       ├── __init__.py
│       ├── __main__.py
│       ├── cli.py
│       ├── configuracao.py
│       ├── coleta.py
│       ├── relatorios.py
│       └── utilidades.py
└── tests/
    ├── test_coleta.py
    └── test_relatorios.py
```

As responsabilidades seriam:

- `cli.py`: recebe argumentos e apresenta opções.
    
- `configuracao.py`: organiza configurações não secretas.
    
- `coleta.py`: implementa a coleta de informações permitidas.
    
- `relatorios.py`: transforma os dados em relatórios.
    
- `utilidades.py`: reúne funções auxiliares realmente compartilhadas.
    
- `tests/`: verifica os comportamentos sem depender desnecessariamente de serviços externos.
    

Tokens e senhas não devem ser armazenados diretamente no código-fonte ou no `pyproject.toml`. Para segredos, utilize mecanismos apropriados, como variáveis de ambiente ou um gerenciador de segredos.

A estrutura deve refletir as responsabilidades reais do projeto, não uma lista obrigatória de arquivos.

---
# 📜 Python — Configuração, Variáveis de Ambiente e Logging

## 1. Por que separar a configuração do código?

Imagine uma ferramenta Python que consulta uma API, salva relatórios e registra erros.

No início, é comum encontrar valores diretamente no código:

```
API_URL = "https://api.exemplo.com"
TIMEOUT = 10
MAX_RETRIES = 3
```

Essa abordagem pode funcionar em um projeto pequeno. Porém, conforme o programa cresce, as configurações podem ficar espalhadas por vários módulos.

Isso dificulta:

- Alterar valores sem procurar em vários arquivos.
    
- Utilizar configurações diferentes em ambientes distintos.
    
- Proteger credenciais e tokens.
    
- Testar o programa com configurações controladas.
    
- Entender por que uma execução falhou.
    

Uma solução é separar três responsabilidades:

|Recurso|Responsabilidade|
|---|---|
|Arquivo de configuração|Organizar valores e opções do programa.|
|Variáveis de ambiente|Permitir configurar o processo sem alterar o código.|
|Logging|Registrar eventos, avisos e erros durante a execução.|

Esses recursos são complementares. Uma variável de ambiente não é automaticamente um segredo protegido, e um log não deve conter indiscriminadamente tudo o que o programa recebe.

## 2. Configurações fixas no código

Valores constantes podem ficar em um módulo como `config.py`.

```
API_URL = "https://api.exemplo.com"
TIMEOUT = 10
MAX_RETRIES = 3
```

### Entendendo cada linha

- `API_URL`: endereço-base utilizado pelo cliente HTTP.
    
- `TIMEOUT`: tempo de espera configurado para as requisições, normalmente em segundos quando passado ao Requests.
    
- `MAX_RETRIES`: quantidade de tentativas adicionais ou limite de repetição definido pela implementação que utilizar essa configuração.
    

A última constante exige atenção: o significado exato de `MAX_RETRIES` depende de como o código implementa as tentativas. É importante documentar se o número representa tentativas totais ou tentativas adicionais.

Em outro módulo, as constantes podem ser importadas:

```
from config import API_URL, TIMEOUT

print(API_URL)
print(TIMEOUT)
```

Essa organização centraliza os valores, mas não é suficiente para configurações que precisam variar entre máquinas ou ambientes.

## 3. Variáveis de ambiente

Uma variável de ambiente é um valor disponibilizado pelo sistema operacional ao processo.

Ela pode representar, por exemplo:

- O endereço de uma API.
    
- Um diretório de saída.
    
- O nível de logging.
    
- Um token de autenticação.
    
- O modo de execução, como desenvolvimento ou produção.
    

No Python, o módulo `os` permite acessar essas variáveis.

```
import os

api_url = os.environ.get("API_URL")
```

### O que significa cada parte?

- `import os`: importa o módulo da biblioteca padrão responsável por funcionalidades relacionadas ao sistema operacional.
    
- `os.environ`: disponibiliza o mapeamento das variáveis de ambiente do processo.
    
- `.get("API_URL")`: consulta a variável `API_URL` e retorna seu valor, ou `None` se ela não existir.
    

Exemplo no terminal Linux:

```
export API_URL="https://api.exemplo.com"
python main.py
```

O comando `export` disponibiliza a variável para os processos iniciados a partir daquele shell.

O valor não é gravado automaticamente no código Python. Ele é fornecido pelo ambiente de execução.

### 3.1. Utilizando um valor padrão

É possível definir um valor padrão quando a variável não estiver configurada:

```
import os

api_url = os.environ.get(
    "API_URL",
    "https://api.exemplo.com",
)
```

O segundo argumento de `.get()` é o valor retornado quando a variável não existe.

Isso é útil para configurações opcionais, como um endpoint público de demonstração. Para credenciais obrigatórias, usar um valor padrão fictício pode mascarar erros de configuração.

### 3.2. Diferença entre `os.environ.get()` e `os.environ[]`

Com `.get()`:

```
import os

token = os.environ.get("API_TOKEN")
```

Se `API_TOKEN` não existir, `token` receberá `None`.

Com acesso por chave:

```
import os

token = os.environ["API_TOKEN"]
```

Se a variável não existir, o Python lançará `KeyError`.

A escolha depende do comportamento desejado:

- Use `.get()` quando a ausência for aceitável ou será verificada explicitamente.
    
- Use `os.environ["NOME"]` quando a variável for obrigatória e sua ausência deva provocar uma falha imediata.
    

## 4. Convertendo variáveis de ambiente para os tipos corretos

Variáveis de ambiente são recebidas como texto. Mesmo quando representam números ou valores booleanos, o Python não converte automaticamente o conteúdo para o tipo desejado.

Considere:

```
export REQUEST_TIMEOUT="15"
```

Ao executar:

```
import os

timeout = os.environ.get("REQUEST_TIMEOUT")
print(type(timeout))
```

O resultado será:

```
<class 'str'>
```

Para converter o valor para um número:

```
import os

timeout = float(os.environ.get("REQUEST_TIMEOUT", "5"))
```

Agora, `timeout` será um `float`.

A conversão pode falhar se a variável contiver um valor inválido, como `"abc"`. Por isso, configurações externas devem ser validadas.

### 4.1. Validando uma configuração numérica

```
import os


def obter_timeout():
    valor = os.environ.get("REQUEST_TIMEOUT", "5")

    try:
        timeout = float(valor)
    except ValueError as exc:
        raise ValueError(
            "REQUEST_TIMEOUT deve ser um número."
        ) from exc

    if timeout <= 0:
        raise ValueError(
            "REQUEST_TIMEOUT deve ser maior que zero."
        )

    return timeout
```

A função executa quatro etapas:

1. Lê a variável de ambiente.
    
2. Utiliza `"5"` como padrão quando ela não existe.
    
3. Converte o texto para `float`.
    
4. Rejeita valores menores ou iguais a zero.
    

O `except ValueError` trata a falha de conversão. O `raise ... from exc` apresenta uma mensagem mais clara e preserva a exceção original como causa.

A função retorna o número validado, em vez de devolver diretamente o texto recebido do ambiente.

## 5. Criando um módulo central de configuração

Em um projeto com vários módulos, é útil concentrar a leitura e a validação das configurações.

Estrutura:

```
minha_ferramenta/
├── main.py
├── config.py
├── api_client.py
└── utilidades.py
```

Arquivo `config.py`:

```
import os


def carregar_configuracao():
    api_url = os.environ.get(
        "API_URL",
        "https://api.exemplo.com",
    )

    timeout_texto = os.environ.get(
        "REQUEST_TIMEOUT",
        "5",
    )

    try:
        timeout = float(timeout_texto)
    except ValueError as exc:
        raise ValueError(
            "REQUEST_TIMEOUT deve ser numérico."
        ) from exc

    if timeout <= 0:
        raise ValueError(
            "REQUEST_TIMEOUT deve ser maior que zero."
        )

    return {
        "api_url": api_url.rstrip("/"),
        "timeout": timeout,
    }
```

### Por que utilizar uma função?

A função `carregar_configuracao()` agrupa a leitura e a validação das opções em um único ponto.

O dicionário retornado contém as configurações já processadas:

```
{
    "api_url": "https://api.exemplo.com",
    "timeout": 5.0,
}
```

O resultado exato depende das variáveis de ambiente presentes durante a execução.

Em projetos maiores, esse dicionário pode ser substituído por uma classe ou por uma `dataclass`, permitindo tipagem e organização mais explícitas.

### Utilizando a configuração

Em `main.py`:

```
from config import carregar_configuracao


def main():
    config = carregar_configuracao()

    print(config["api_url"])
    print(config["timeout"])


if __name__ == "__main__":
    main()
```

O módulo principal não precisa conhecer os detalhes de leitura das variáveis de ambiente. Ele solicita a configuração pronta e utiliza os valores retornados.

## 6. Segredos e credenciais

Tokens, senhas e chaves de API devem ser tratados de maneira diferente de configurações comuns.

Exemplo de leitura de um token:

```
import os

token = os.environ.get("API_TOKEN")

if not token:
    raise RuntimeError(
        "A variável de ambiente API_TOKEN não foi configurada."
    )
```

A condição `if not token` verifica se o valor está ausente ou vazio.

Para configurar o token temporariamente em um shell Linux:

```
export API_TOKEN="seu_token_de_teste"
python main.py
```

Use apenas credenciais autorizadas. O texto mostrado é ilustrativo, não um token funcional.

### Boas práticas para segredos

- Não escreva tokens diretamente no código-fonte.
    
- Não os envie para repositórios Git.
    
- Não os imprima em mensagens de diagnóstico.
    
- Não os inclua em capturas de tela ou relatórios compartilhados.
    
- Limite as permissões das credenciais ao necessário.
    
- Revogue e substitua uma credencial caso ela seja exposta.
    

**Atenção:** variáveis de ambiente ajudam a separar segredos do código, mas não são um cofre de credenciais. Processos com permissões suficientes podem conseguir acessar seus valores. Em sistemas mais sensíveis, considere um gerenciador de segredos apropriado.

## 7. Arquivos `.env`

Durante o desenvolvimento, algumas pessoas utilizam um arquivo `.env` para manter configurações locais.

Exemplo de conteúdo:

```
API_URL=https://api.exemplo.com
REQUEST_TIMEOUT=5
API_TOKEN=token_local_de_teste
```

Esse arquivo é texto simples. O Python não carrega arquivos `.env` automaticamente.

Uma biblioteca popular para esse propósito é `python-dotenv`.

Instalação:

```
python -m pip install python-dotenv
```

Utilização:

```
import os

from dotenv import load_dotenv

load_dotenv()

api_url = os.environ.get("API_URL")
```

A chamada `load_dotenv()` procura um arquivo `.env` de acordo com as regras da biblioteca e carrega as variáveis encontradas no ambiente do processo, sem sobrescrever variáveis já existentes por padrão.

Se o projeto utilizar `.env`, adicione-o ao `.gitignore`:

```
.env
.venv/
__pycache__/
.pytest_cache/
```

O `.gitignore` instrui o Git a ignorar esses caminhos quando aplicável. Ele não remove arquivos que já foram adicionados ao controle de versão; se um segredo já tiver sido enviado a um repositório, será necessário tratar a exposição e substituir a credencial.

Para projetos pequenos, variáveis de ambiente configuradas no shell podem ser suficientes. Não é necessário adicionar `python-dotenv` se o projeto não precisa desse recurso.

## 8. O que é logging?

Logging é o registro de eventos que acontecem durante a execução de um programa.

Por exemplo, uma ferramenta pode precisar registrar:

- Quando uma execução começa.
    
- Qual operação está sendo realizada.
    
- Quando uma resposta HTTP é recebida.
    
- Quando ocorre um timeout.
    
- Quando uma configuração está inválida.
    
- Quando uma operação termina com sucesso.
    

A biblioteca `logging` faz parte da biblioteca padrão do Python.

Diferentemente de `print()`, ela oferece níveis de severidade, formatos configuráveis, timestamps e diferentes destinos para as mensagens.

## 9. Os níveis de logging

O módulo `logging` possui cinco níveis de uso comum:

|Nível|Valor padrão|Quando utilizar|
|---|---|---|
|`DEBUG`|10|Informações detalhadas para investigação de problemas.|
|`INFO`|20|Eventos normais da execução.|
|`WARNING`|30|Situações inesperadas que não interromperam necessariamente o programa.|
|`ERROR`|40|Falhas que impediram uma operação.|
|`CRITICAL`|50|Falhas graves que podem comprometer a continuidade da aplicação.|

Os números representam os valores de severidade usados pelo sistema de logging. Em geral, quanto maior o valor, maior a severidade.

Exemplo:

```
import logging

logging.basicConfig(level=logging.DEBUG)

logging.debug("Detalhe técnico da execução.")
logging.info("Programa iniciado.")
logging.warning("A resposta demorou mais que o esperado.")
logging.error("Não foi possível concluir a operação.")
logging.critical("Falha grave na aplicação.")
```

O `basicConfig()` configura o sistema básico de logging. O parâmetro `level=logging.DEBUG` permite que todos os níveis listados sejam considerados para exibição, desde que não haja outra configuração ou filtro que os descarte.

Se nenhum formato for definido, o logging utiliza um formato padrão.

## 10. Configurando um formato de log

Uma configuração mais informativa:

```
import logging


logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(name)s | %(message)s",
)
```

O parâmetro `format` define a composição de cada registro.

|Campo|Significado|
|---|---|
|`%(asctime)s`|Data e hora do registro.|
|`%(levelname)s`|Nome do nível, como `INFO` ou `ERROR`.|
|`%(name)s`|Nome do logger que produziu a mensagem.|
|`%(message)s`|Mensagem registrada.|

Para registrar um evento:

```
logging.info("Consulta à API iniciada.")
```

A saída terá um formato semelhante a:

```
2026-10-16 10:00:00,000 | INFO | root | Consulta à API iniciada.
```

O horário é ilustrativo. A saída real depende do relógio do sistema e da configuração do logging.

### Por que o nível é `INFO`?

Com `level=logging.INFO`, os registros `INFO`, `WARNING`, `ERROR` e `CRITICAL` normalmente são aceitos. Os registros `DEBUG` são descartados pelo filtro de nível.

Isso permite reduzir o volume de mensagens em execuções comuns e ativar mais detalhes quando necessário.

## 11. Utilizando um logger por módulo

Em projetos maiores, é comum criar um logger com o nome do módulo:

```
import logging

logger = logging.getLogger(__name__)
```

A variável especial `__name__` contém o nome do módulo no contexto em que ele está sendo utilizado.

Assim, um módulo chamado `api_client.py` pode produzir registros identificados por `api_client`, enquanto outro módulo pode utilizar seu próprio nome.

Exemplo:

```
import logging

logger = logging.getLogger(__name__)


def consultar_api():
    logger.info("Iniciando consulta.")
```

A função registra um evento sem precisar conhecer o formato global do log.

**Importante:** `getLogger(__name__)` cria ou recupera um logger identificado por aquele nome. A configuração dos handlers, do formato e dos níveis pode ser feita centralmente pela aplicação principal.

Isso evita que cada módulo configure o logging de maneira diferente.

## 12. Registrando exceções corretamente

Considere uma operação que pode falhar:

```
import logging
import requests

logger = logging.getLogger(__name__)


def consultar(url):
    try:
        response = requests.get(
            url,
            timeout=5,
        )
        response.raise_for_status()

    except requests.exceptions.RequestException:
        logger.exception("Falha ao consultar a API.")
        raise

    return response
```

### Entendendo o tratamento

```
except requests.exceptions.RequestException:
```

Captura exceções da família `RequestException`, que inclui diversas falhas de requisição da biblioteca Requests.

```
logger.exception("Falha ao consultar a API.")
```

Registra uma mensagem de erro e inclui informações do traceback da exceção atual. Por isso, deve ser chamado dentro do bloco `except`.

```
raise
```

Lança novamente a mesma exceção. Isso permite que a camada superior decida se deve apresentar uma mensagem ao usuário, encerrar a execução ou tentar outra estratégia.

Esse padrão registra o erro sem escondê-lo.

Evite capturar todas as exceções com `except Exception` quando não houver uma razão concreta. Capturas genéricas podem ocultar erros de programação que deveriam ser corrigidos.

### Não registre dados sensíveis

A mensagem acima não inclui o token, cookies ou cabeçalhos da requisição.

Ao registrar erros HTTP, tenha cuidado com URLs que possam conter dados privados, parâmetros sensíveis ou informações pessoais. O traceback deve ajudar no diagnóstico sem expor segredos.

## 13. Salvando logs em arquivo

Para gravar mensagens em um arquivo:

```
import logging


logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(name)s | %(message)s",
    filename="app.log",
    encoding="utf-8",
)
```

O argumento `filename` define o arquivo de destino. O argumento `encoding="utf-8"` especifica a codificação utilizada para gravar os registros.

Quando o logging é configurado para usar `filename`, as mensagens são enviadas ao arquivo em vez de serem exibidas pelo handler padrão do console.

A opção `encoding` está disponível nas versões modernas do Python, incluindo Python 3.10.

Para enviar mensagens tanto ao terminal quanto a um arquivo, é possível configurar handlers separados, por exemplo `StreamHandler` e `FileHandler`.

Em ferramentas que executam muitas operações, considere também rotação de logs, retenção e permissões adequadas para evitar que o arquivo cresça indefinidamente ou exponha informações a usuários não autorizados.

## 14. Exemplo integrado: configuração e logging

Considere esta estrutura:

```
minha_ferramenta/
├── main.py
├── config.py
└── api_client.py
```

Arquivo `config.py`:

```
import os


def carregar_configuracao():
    api_url = os.environ.get("API_URL")

    if not api_url:
        raise ValueError(
            "Configure a variável de ambiente API_URL."
        )

    timeout_texto = os.environ.get(
        "REQUEST_TIMEOUT",
        "5",
    )

    try:
        timeout = float(timeout_texto)
    except ValueError as exc:
        raise ValueError(
            "REQUEST_TIMEOUT deve ser numérico."
        ) from exc

    if timeout <= 0:
        raise ValueError(
            "REQUEST_TIMEOUT deve ser maior que zero."
        )

    return {
        "api_url": api_url.rstrip("/"),
        "timeout": timeout,
    }
```

Arquivo `api_client.py`:

```
import logging

import requests

logger = logging.getLogger(__name__)


def consultar_status(api_url, timeout):
    url = f"{api_url}/status"

    logger.info("Consultando endpoint de status.")

    response = requests.get(
        url,
        timeout=timeout,
    )
    response.raise_for_status()

    logger.info(
        "Consulta concluída com status HTTP %s.",
        response.status_code,
    )

    return response
```

O endpoint `/status` é ilustrativo: o servidor utilizado precisa implementá-lo.

Observe esta linha:

```
logger.info(
    "Consulta concluída com status HTTP %s.",
    response.status_code,
)
```

O logging aceita argumentos separados para interpolação da mensagem. Isso evita precisar construir manualmente a string antes de saber se o registro será emitido.

Arquivo `main.py`:

```
import logging

from config import carregar_configuracao
from api_client import consultar_status


logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(name)s | %(message)s",
)


def main():
    try:
        config = carregar_configuracao()

        consultar_status(
            api_url=config["api_url"],
            timeout=config["timeout"],
        )

    except ValueError as exc:
        logging.error("Configuração inválida: %s", exc)

    except Exception:
        logging.exception("Falha inesperada na execução.")
        raise


if __name__ == "__main__":
    main()
```

Nesse exemplo:

- `config.py` lê e valida as configurações.
    
- `api_client.py` realiza a operação HTTP.
    
- `main.py` configura o logging e coordena a execução.
    

A captura genérica do último bloco serve para registrar uma falha inesperada e relançá-la, sem fingir que a operação terminou com sucesso. Em uma aplicação real, é possível refinar as exceções tratadas conforme os requisitos.

## 15. Executando no Linux

Defina as variáveis:

```
export API_URL="https://api.exemplo.com"
export REQUEST_TIMEOUT="5"
```

Execute:

```
python main.py
```

Para alterar o nível de detalhe do logging, uma próxima melhoria seria permitir configurar o nível por variável de ambiente ou argumento de linha de comando.

Se o endpoint não existir no servidor informado, a requisição poderá falhar. Isso não significa necessariamente que a configuração ou o logging estejam incorretos; o exemplo exige um endpoint compatível.

## 16. Erros comuns

### Configuração inválida tratada como texto

Uma variável como `REQUEST_TIMEOUT="abc"` não pode ser convertida para número. Converta e valide valores antes de utilizá-los.

### Variável de ambiente ausente

`os.environ.get()` retorna `None` quando não encontra a variável e nenhum valor padrão foi definido. Verifique a ausência de configurações obrigatórias antes de iniciar operações externas.

### Segredos expostos em logs

Evite registrar tokens, senhas, cookies ou cabeçalhos de autenticação. Também revise URLs e mensagens de erro que possam conter informações privadas.

### `logging.basicConfig()` não altera a configuração esperada

Em geral, `basicConfig()` não substitui handlers existentes se o sistema de logging já estiver configurado. Em aplicações maiores, centralize a configuração e evite chamar `basicConfig()` repetidamente em diferentes módulos.

### Usar `print()` para todos os eventos

`print()` continua útil para a saída que o usuário deve receber. Logging é mais adequado para eventos operacionais, níveis de severidade e diagnóstico.

### Capturar erros e ignorá-los

Um bloco `except` vazio ou que apenas imprime uma mensagem genérica pode esconder falhas importantes. Registre informações úteis e decida explicitamente se a exceção será relançada ou tratada.

## 17. Quando utilizar cada recurso?

- **Constantes em** `**config.py**`**:** valores simples e estáveis que fazem parte da configuração do programa.
    
- **Variáveis de ambiente:** valores que precisam mudar entre execuções ou ambientes sem alteração do código.
    
- **Arquivo** `**.env**`**:** conveniência de desenvolvimento local quando a equipe precisa carregar configurações de um arquivo.
    
- `**logging**`**:** registro estruturado de eventos, avisos e falhas.
    
- `**print()**`**:** apresentação direta de resultados ao usuário da ferramenta.
    

Uma aplicação bem organizada mantém a configuração separada da lógica principal, valida os valores externos antes de utilizá-los e registra informações suficientes para investigar falhas sem expor dados sensíveis.

---
# 📜 Python — CLI Profissional com Argparse

## 1. O que é uma CLI?

CLI significa _Command-Line Interface_, ou interface de linha de comando. É uma forma de interagir com um programa por meio de comandos e argumentos no terminal.

Uma ferramenta simples pode ser executada assim:

```
python main.py
```

Já uma ferramenta com opções pode receber parâmetros:

```
python main.py --url https://exemplo.com --timeout 10
```

Uma ferramenta com subcomandos pode ter operações distintas:

```
python main.py verificar --url https://exemplo.com
python main.py analisar --arquivo resultado.json
```

Uma CLI bem estruturada facilita a automação por scripts, o uso em terminais e a integração com outras ferramentas.

O módulo `argparse`, incluído na biblioteca padrão do Python, permite definir argumentos, opções, mensagens de ajuda, validações e subcomandos.

**Observação:** esta anotação aprofunda a organização de CLIs. Para os fundamentos de `argparse`, consulte também `[[Argparse - Argumentos de Linha de Comando]]`.

## 2. Anatomia de um comando

Considere:

```
python main.py verificar --url https://exemplo.com --timeout 10
```

|Elemento|Função|
|---|---|
|`python`|Interpretador que executa o programa.|
|`main.py`|Arquivo de entrada.|
|`verificar`|Subcomando que seleciona uma operação.|
|`--url`|Opção que identifica o argumento seguinte como endereço.|
|`https://exemplo.com`|Valor da opção `--url`.|
|`--timeout`|Opção que configura o tempo limite.|
|`10`|Valor fornecido para o timeout.|

Essa separação permite que uma mesma aplicação ofereça diversas operações sem precisar criar um arquivo executável para cada uma.

## 3. Separando a interface da lógica

Uma CLI não deve concentrar toda a implementação da ferramenta em uma única função.

Considere esta estrutura:

```
minha_ferramenta/
├── main.py
├── cli.py
├── config.py
├── servicos.py
└── utilidades.py
```

Responsabilidades:

- `main.py`: ponto de entrada do programa.
    
- `cli.py`: definição dos argumentos e encaminhamento das operações.
    
- `config.py`: leitura e validação das configurações.
    
- `servicos.py`: implementação das operações principais.
    
- `utilidades.py`: funções auxiliares reutilizáveis.
    

A CLI recebe a entrada, verifica os argumentos e chama a lógica correspondente. O módulo de serviços não precisa conhecer os detalhes de como o usuário digitou o comando.

Essa separação também facilita os testes: a lógica pode ser testada sem simular toda a interação com o terminal.

## 4. Criando uma CLI com subcomandos

Os subcomandos permitem organizar operações diferentes sob o mesmo programa.

Exemplo de estrutura:

```
ferramenta/
├── main.py
├── cli.py
└── servicos.py
```

Arquivo `servicos.py`:

```
def verificar_endereco(url):
    print(f"Endereço recebido: {url}")


def analisar_arquivo(caminho):
    print(f"Arquivo recebido: {caminho}")
```

As funções são propositalmente simples. Elas demonstram a separação da lógica e não realizam uma verificação real de rede nem a análise do conteúdo de um arquivo.

### Criando o parser

Arquivo `cli.py`:

```
import argparse

from servicos import analisar_arquivo, verificar_endereco


def criar_parser():
    parser = argparse.ArgumentParser(
        prog="ferramenta",
        description="Ferramenta de demonstração com subcomandos.",
    )

    subparsers = parser.add_subparsers(
        dest="comando",
        required=True,
    )

    verificar = subparsers.add_parser(
        "verificar",
        help="Recebe um endereço para verificação.",
    )

    verificar.add_argument(
        "--url",
        required=True,
        help="Endereço que será informado à operação.",
    )

    analisar = subparsers.add_parser(
        "analisar",
        help="Recebe um arquivo para análise.",
    )

    analisar.add_argument(
        "--arquivo",
        required=True,
        help="Caminho do arquivo que será analisado.",
    )

    return parser
```

### Entendendo as partes

#### `argparse.ArgumentParser()`

Cria o parser principal:

```
parser = argparse.ArgumentParser(
    prog="ferramenta",
    description="Ferramenta de demonstração com subcomandos.",
)
```

- `prog`: nome exibido nas mensagens de uso.
    
- `description`: descrição apresentada na ajuda.
    
- `parser`: objeto responsável por definir e interpretar os argumentos.
    

#### `add_subparsers()`

```
subparsers = parser.add_subparsers(
    dest="comando",
    required=True,
)
```

Cria um grupo de subparsers.

- `dest="comando"` determina o nome do atributo em que o subcomando selecionado será armazenado.
    
- `required=True` exige que o usuário escolha um subcomando.
    

Assim, executar apenas `python main.py` sem selecionar uma operação resulta em uma mensagem de uso e em um erro de argumentos.

#### `add_parser()`

```
verificar = subparsers.add_parser("verificar")
```

Cria um parser para o subcomando `verificar`.

Cada subcomando pode ter argumentos próprios, sem misturar opções que só fazem sentido para outra operação.

#### `required=True` em uma opção

```
verificar.add_argument(
    "--url",
    required=True,
)
```

Torna `--url` obrigatória quando o usuário seleciona `verificar`.

Isso não significa que todas as operações precisam de uma URL. A obrigatoriedade pertence ao parser específico do subcomando.

## 5. Encaminhando os argumentos para a operação correta

Ainda em `cli.py`, acrescente a função:

```
def executar(args):
    if args.comando == "verificar":
        verificar_endereco(args.url)

    elif args.comando == "analisar":
        analisar_arquivo(args.arquivo)
```

A variável `args` é o objeto retornado pelo parser. Seus atributos dependem dos argumentos definidos e do subcomando escolhido.

Quando o usuário escolhe `verificar`, a CLI acessa `args.url`. Quando escolhe `analisar`, acessa `args.arquivo`.

Complete o arquivo com:

```
def main():
    parser = criar_parser()
    args = parser.parse_args()
    executar(args)
```

O fluxo fica assim:

1. Criar a definição dos comandos.
    
2. Interpretar os argumentos fornecidos.
    
3. Identificar o subcomando selecionado.
    
4. Encaminhar os dados para a função correspondente.
    

## 6. Criando o ponto de entrada

Arquivo `main.py`:

```
from cli import main


if __name__ == "__main__":
    main()
```

A condição impede que `main()` seja executada automaticamente quando o arquivo é importado por outro módulo.

Execute a ferramenta:

```
python main.py verificar --url https://exemplo.com
```

Saída esperada:

```
Endereço recebido: https://exemplo.com
```

Para o segundo subcomando:

```
python main.py analisar --arquivo resultado.json
```

Saída esperada:

```
Arquivo recebido: resultado.json
```

O segundo exemplo apenas recebe e exibe o caminho; ele não verifica se o arquivo existe nem lê seu conteúdo.

## 7. Adicionando validação aos argumentos

O `argparse` pode validar alguns tipos e restrições diretamente.

Por exemplo, um timeout positivo:

```
import argparse


def timeout_positivo(valor):
    try:
        numero = float(valor)
    except ValueError as exc:
        raise argparse.ArgumentTypeError(
            "O timeout deve ser numérico."
        ) from exc

    if numero <= 0:
        raise argparse.ArgumentTypeError(
            "O timeout deve ser maior que zero."
        )

    return numero
```

A função `timeout_positivo()` recebe o texto informado no terminal, converte-o para `float` e verifica se o número é maior que zero.

Se o valor for inválido, ela lança `ArgumentTypeError`. O `argparse` utiliza essa exceção para apresentar uma mensagem de erro de argumentos ao usuário.

Utilização:

```
parser.add_argument(
    "--timeout",
    type=timeout_positivo,
    default=5.0,
    help="Tempo limite em segundos (padrão: 5).",
)
```

- `type=timeout_positivo`: encaminha o valor recebido à função de validação.
    
- `default=5.0`: utiliza esse valor se a opção não for informada.
    
- `help`: descreve a finalidade da opção.
    

A função retorna o valor convertido. Portanto, após o parsing, `args.timeout` será um número `float`, não uma string.

## 8. Escolhendo entre `type`, `choices` e validação posterior

Esses recursos atendem a necessidades diferentes.

### `type`

Converte ou valida um valor:

```
parser.add_argument("--porta", type=int)
```

O valor recebido precisa ser convertível para inteiro.

### `choices`

Limita o valor a opções conhecidas:

```
parser.add_argument(
    "--formato",
    choices=["json", "txt", "csv"],
    default="json",
)
```

O usuário só pode escolher um dos formatos permitidos.

Exemplo:

```
python main.py --formato json
```

Se o usuário fornecer um formato não permitido, o `argparse` rejeita o argumento.

### Validação posterior

Algumas verificações dependem do estado do sistema ou de outros argumentos.

Por exemplo, para verificar se um arquivo existe:

```
from pathlib import Path


def validar_arquivo_existente(caminho):
    arquivo = Path(caminho)

    if not arquivo.is_file():
        raise FileNotFoundError(
            f"Arquivo não encontrado: {arquivo}"
        )

    return arquivo
```

Essa função utiliza `Path.is_file()` para verificar se o caminho corresponde a um arquivo existente.

A validação pode ser realizada após o parsing, antes de iniciar a operação principal. Isso permite separar erros de sintaxe do comando de problemas encontrados durante a execução.

## 9. Códigos de saída

Programas de terminal comunicam o resultado da execução por meio de um código de saída.

Em geral:

- `0`: execução concluída com sucesso.
    
- Diferente de `0`: houve uma falha ou uma condição que não representa sucesso.
    

Esses códigos são úteis para scripts Bash, pipelines e sistemas de automação.

No Python, o módulo `sys` permite definir o código de saída:

```
import sys

sys.exit(0)
```

Ou:

```
import sys

sys.exit(1)
```

O `sys.exit()` encerra a execução por meio de `SystemExit`. Quando recebe um inteiro, esse valor é utilizado como código de saída do processo.

Uma organização comum é fazer a função principal retornar um código:

```
def main():
    # Executar a operação.
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

O comentário indica o local em que a operação real seria implementada. A função `main()` retorna `0`, e `SystemExit` faz com que esse valor seja utilizado como código de saída.

Para uma ferramenta maior, podem ser definidos códigos específicos para erros de argumentos, falhas de rede ou problemas de arquivo. Documente esses códigos para que outros programas saibam interpretá-los.

## 10. Integrando logging à CLI

O logging pode ser configurado no ponto de entrada da aplicação.

Arquivo `main.py`:

```
import logging

from cli import main


def configurar_logging():
    logging.basicConfig(
        level=logging.INFO,
        format="%(asctime)s | %(levelname)s | %(name)s | %(message)s",
    )


if __name__ == "__main__":
    configurar_logging()
    main()
```

A função `configurar_logging()` centraliza a configuração.

Os módulos chamados pela CLI podem utilizar:

```
import logging

logger = logging.getLogger(__name__)
```

E registrar eventos:

```
logger.info("Operação iniciada.")
logger.warning("Uma opção opcional não foi configurada.")
logger.error("A operação não pôde ser concluída.")
```

Evite configurar handlers independentes em todos os módulos. Centralizar a configuração facilita controlar o nível de detalhe e o destino dos logs.

A saída destinada ao usuário e os logs operacionais são coisas diferentes. Uma ferramenta pode usar `print()` para apresentar o resultado final e `logging` para registrar detalhes de execução.

## 11. Como testar a interface de linha de comando

Uma CLI pode ser testada sem precisar executar operações reais de rede ou modificar arquivos importantes.

O `pytest` permite testar funções diretamente. Também é possível utilizar `subprocess` para executar o programa como um processo separado.

Exemplo simples para testar o código de saída de um comando:

```
import subprocess
import sys


def test_comando_sem_subcomando_falha():
    resultado = subprocess.run(
        [sys.executable, "main.py"],
        capture_output=True,
        text=True,
        check=False,
    )

    assert resultado.returncode != 0
```

Explicação:

- `subprocess.run()` executa um processo externo e aguarda sua conclusão.
    
- `sys.executable` aponta para o interpretador Python em uso.
    
- `capture_output=True` captura a saída padrão e a saída de erro.
    
- `text=True` devolve as saídas como strings.
    
- `check=False` evita lançar `CalledProcessError` apenas porque o processo terminou com código diferente de zero.
    
- `returncode` contém o código de saída.
    

Esse teste pressupõe que `main.py` esteja no diretório de trabalho utilizado pelo teste. Em um projeto instalado como pacote, é possível organizar os testes para executar o ponto de entrada do pacote ou chamar suas funções diretamente.

Testes de CLI devem verificar não apenas o sucesso, mas também argumentos ausentes, valores inválidos e códigos de saída consistentes.

## 12. Boas práticas para CLIs profissionais

- Use nomes de comandos e opções que descrevam claramente a ação.
    
- Forneça mensagens de ajuda úteis.
    
- Valide os argumentos antes de iniciar operações demoradas.
    
- Mantenha a lógica de negócio fora do parser.
    
- Evite duplicar a mesma lógica em vários subcomandos.
    
- Retorne códigos de saída consistentes.
    
- Registre falhas relevantes sem expor tokens ou outros dados sensíveis.
    
- Teste os caminhos de sucesso e de erro.
    
- Documente exemplos reais de utilização no `README.md`.
    

Uma CLI deve ser previsível: o usuário precisa entender quais argumentos são aceitos, o que cada comando faz e como identificar se a execução foi bem-sucedida.

## 13. Relação com ferramentas de automação e pentest

A estrutura com subcomandos é útil quando uma ferramenta autorizada possui diferentes operações, por exemplo:

```
inventario listar
inventario consultar
inventario exportar
```

Cada subcomando pode ter argumentos próprios, mas todos compartilham a configuração, o logging e as funções reutilizáveis do projeto.

Essa arquitetura também permite que operações de análise estática, leitura de arquivos e exportação de relatórios sejam testadas separadamente, sem que a interface de terminal precise conhecer todos os detalhes internos.

O objetivo não é criar a maior quantidade possível de módulos, e sim manter cada responsabilidade clara, testável e fácil de modificar.

