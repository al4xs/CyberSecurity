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