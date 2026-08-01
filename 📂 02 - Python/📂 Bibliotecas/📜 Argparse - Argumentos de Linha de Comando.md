
---

# Argparse - Argumentos de Linha de Comando

## O que é?

O **argparse** é uma biblioteca da biblioteca padrão do Python utilizada para criar interfaces de linha de comando (CLI - Command Line Interface).

Ela permite que um programa receba argumentos informados pelo usuário através do terminal de forma organizada, validando valores, exibindo mensagens de ajuda e convertendo automaticamente tipos de dados.

O `argparse` substitui o uso direto de `sys.argv`, tornando o código mais limpo, legível e fácil de manter.

---

## Quando utilizar?

Sempre que um programa precisar receber parâmetros pela linha de comando.

Exemplos:

- Scanner de portas
- Banner Grabbing
- Ping Sweep
- Enumeradores
- Ferramentas de Pentest
- Scripts de automação
- Ferramentas de administração
- Utilitários em geral

Praticamente todas as ferramentas conhecidas do Linux utilizam esse conceito.

Exemplos:

```bash
nmap -sV 10.10.10.10

ffuf -u http://site/FUZZ -w wordlist.txt

gobuster dir -u http://site -w lista.txt

hydra -l admin -P rockyou.txt ssh://10.10.10.10
```

Ao utilizar o `argparse`, podemos criar programas com uma interface semelhante a essas ferramentas.

---

# sys.argv x argparse

Antes do `argparse`, era comum utilizar a biblioteca `sys`.

Exemplo:

```python
import sys

print(sys.argv)
```

Executando:

```bash
python exemplo.py Allan 20
```

Saída:

```text
['exemplo.py', 'Allan', '20']
```

## O que é sys.argv?

`sys.argv` é uma lista contendo todos os argumentos passados ao programa.

Sempre funciona assim:

```text
argv[0]
```

Nome do programa.

```text
argv[1]
```

Primeiro argumento.

```text
argv[2]
```

Segundo argumento.

Exemplo:

```bash
python exemplo.py Allan 20
```

Resultado:

| Índice | Valor |
|---------|--------|
| argv[0] | exemplo.py |
| argv[1] | Allan |
| argv[2] | 20 |

---

## Problemas do sys.argv

Imagine um scanner simples.

```python
import sys

host = sys.argv[1]
porta = int(sys.argv[2])

print(host)
print(porta)
```

Executando:

```bash
python scanner.py 10.10.10.10 80
```

Funciona.

Mas...

Se esquecer algum argumento:

```bash
python scanner.py
```

Erro:

```text
IndexError:
list index out of range
```

Outro problema.

Todos os valores chegam como **string**.

```python
print(type(sys.argv[2]))
```

Resultado:

```text
<class 'str'>
```

Mesmo digitando:

```bash
80
```

O Python recebe:

```python
"80"
```

Sendo necessário converter manualmente.

```python
porta = int(sys.argv[2])
```

Além disso, o `sys.argv`:

- não possui ajuda (`-h`);
- não valida argumentos;
- não verifica tipos;
- não gera mensagens amigáveis.

---

# Por que utilizar argparse?

O `argparse` resolve todos esses problemas automaticamente.

Com poucas linhas de código conseguimos:

- validar argumentos;
- converter tipos automaticamente;
- gerar ajuda (`-h`);
- definir valores padrão;
- limitar valores permitidos;
- criar parâmetros opcionais;
- criar parâmetros obrigatórios.

Tudo isso sem escrever código extra.

---

# Fluxo de funcionamento

O funcionamento do `argparse` pode ser resumido no fluxo abaixo.

```text
Usuário executa o programa
            │
            ▼
python scanner.py -t 10.10.10.10 -p 80
            │
            ▼
ArgumentParser()
            │
            ▼
add_argument()
            │
            ▼
parse_args()
            │
            ▼
Namespace
            │
            ▼
Programa utiliza os valores
```

Cada etapa possui uma função específica.

---

# Estrutura básica

Todo programa utilizando `argparse` segue praticamente esta estrutura.

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument("nome")

args = parser.parse_args()

print(args.nome)
```

Executando:

```bash
python exemplo.py Allan
```

Saída:

```text
Allan
```

Mesmo sendo um exemplo simples, praticamente toda ferramenta feita em Python segue esse padrão.

---

# Importando a biblioteca

Antes de utilizar qualquer recurso do `argparse`, é necessário importar a biblioteca.

```python
import argparse
```

O `argparse` faz parte da biblioteca padrão do Python.

Não é necessário instalar nada.

---

# ArgumentParser()

## O que é?

É a classe responsável por criar um analisador de argumentos.

Sem ela, o programa não consegue interpretar os parâmetros digitados pelo usuário.

Sintaxe:

```python
parser = argparse.ArgumentParser()
```

Visualmente:

```text
                Usuário
                   │
                   ▼
python scanner.py -t 10.10.10.10
                   │
                   ▼
        ArgumentParser()
                   │
        interpreta argumentos
```

O objeto retornado normalmente recebe o nome:

```python
parser
```

Esse nome não é obrigatório.

Poderia ser:

```python
meu_parser = argparse.ArgumentParser()
```

Mas praticamente todos os projetos utilizam:

```python
parser
```

por convenção.

---

# O objeto parser

Após criar o parser:

```python
parser = argparse.ArgumentParser()
```

ele passa a possuir vários métodos.

Os mais utilizados são:

| Método | Função |
|---------|--------|
| add_argument() | Adiciona argumentos |
| parse_args() | Lê os argumentos |
| print_help() | Mostra a ajuda |
| error() | Exibe mensagens de erro |

Durante praticamente todo o uso do `argparse`, os métodos mais utilizados serão:

```python
parser.add_argument()

parser.parse_args()
```

---

# parse_args()

## O que é?

É o método responsável por ler todos os argumentos informados pelo usuário.

Até esse momento, o programa apenas definiu quais argumentos existem.

Quem realmente faz a leitura é:

```python
args = parser.parse_args()
```

Visualmente:

```text
parser.add_argument()
        │
Define os argumentos
        │
        ▼
parse_args()
        │
Lê o que foi digitado
        │
        ▼
Namespace
```

---

## Exemplo

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument("nome")

args = parser.parse_args()

print(args.nome)
```

Executando:

```bash
python exemplo.py Allan
```

Saída:

```text
Allan
```

---

# Namespace

Após executar:

```python
args = parser.parse_args()
```

o `argparse` cria um objeto chamado **Namespace**.

Exemplo.

```python
print(args)
```

Saída:

```text
Namespace(nome='Allan')
```

O Namespace é um objeto que armazena todos os argumentos informados pelo usuário.

É semelhante a um objeto comum do Python.

Por isso podemos acessar seus atributos utilizando o operador ponto (`.`).

```python
args.nome
```

Resultado:

```text
Allan
```

Outro exemplo.

Código:

```python
parser.add_argument("host")
parser.add_argument("porta", type=int)
```

Executando:

```bash
python scanner.py 10.10.10.10 80
```

Saída:

```python
print(args)
```

```text
Namespace(
    host='10.10.10.10',
    porta=80
)
```

Agora podemos acessar:

```python
args.host

args.porta
```

Assim como qualquer outro objeto do Python.

---

# Resumo da Parte 1

Nesta primeira parte aprendemos:

- O que é o `argparse`;
- Diferenças entre `sys.argv` e `argparse`;
- Fluxo de funcionamento;
- Importação da biblioteca;
- `ArgumentParser()`;
- `parse_args()`;
- O que é um `Namespace`.

Na próxima parte estudaremos o método mais importante da biblioteca:

```python
parser.add_argument()
```

e todos os parâmetros aceitos por ele.

---

# add_argument()

## O que é?

O método:

```python
parser.add_argument()
```

é responsável por informar ao `argparse` quais argumentos o programa aceita.

Sempre que desejamos que o usuário possa informar algum valor pelo terminal, devemos utilizar o `add_argument()`.

Visualmente:

```text
Usuário
   │
   ▼
python scanner.py -t 10.10.10.10 -p 80
          │
          ▼
   add_argument()
          │
Define quais argumentos existem
          │
          ▼
parse_args()
          │
          ▼
Namespace
```

Sem utilizar o `add_argument()`, o `argparse` não saberá reconhecer nenhum argumento.

---

# Sintaxe

A forma mais simples é:

```python
parser.add_argument("nome")
```

ou

```python
parser.add_argument("-t")
```

ou

```python
parser.add_argument(
    "-t",
    "--target"
)
```

Cada argumento criado será armazenado dentro do objeto retornado pelo `parse_args()`.

---

# Argumentos posicionais

São argumentos obrigatórios informados **sem utilizar "-" ou "--"**.

Exemplo:

```python
parser.add_argument("host")
```

Execução:

```bash
python scanner.py 10.10.10.10
```

Resultado:

```python
args.host
```

```text
10.10.10.10
```

Observe que não utilizamos:

```text
-host

--host
```

Apenas:

```text
10.10.10.10
```

---

## Mais de um argumento posicional

```python
parser.add_argument("host")

parser.add_argument("porta")
```

Execução:

```bash
python scanner.py 10.10.10.10 80
```

Resultado:

```python
print(args)
```

```text
Namespace(
    host='10.10.10.10',
    porta='80'
)
```

---

## Erro ao esquecer um argumento

Código:

```python
parser.add_argument("host")
```

Executando:

```bash
python scanner.py
```

Resultado:

```text
usage:

scanner.py [-h] host

scanner.py: error:
the following arguments are required:

host
```

Observe que o próprio `argparse` informa o erro.

---

# Argumentos opcionais

São argumentos iniciados por:

```text
-
```

ou

```text
--
```

Exemplo:

```python
parser.add_argument("-t")
```

Execução:

```bash
python scanner.py -t 10.10.10.10
```

Resultado:

```python
args.t
```

```text
10.10.10.10
```

---

# Nome curto

Normalmente possui apenas uma letra.

```python
parser.add_argument("-p")
```

Execução:

```bash
python scanner.py -p 80
```

Resultado:

```python
args.p
```

```text
80
```

É muito utilizado para parâmetros digitados frequentemente.

Exemplos conhecidos:

```text
-v

-h

-p

-t

-u

-w

-o
```

---

# Nome longo

Também podemos criar um nome mais descritivo.

```python
parser.add_argument("--porta")
```

Execução:

```bash
python scanner.py --porta 80
```

Resultado:

```python
args.porta
```

```text
80
```

O nome longo facilita a leitura.

---

# Nome curto + Nome longo

Essa é a forma mais utilizada.

```python
parser.add_argument(
    "-t",
    "--target"
)
```

Agora ambos funcionam.

```bash
python scanner.py -t 10.10.10.10
```

ou

```bash
python scanner.py --target 10.10.10.10
```

Resultado:

```python
args.target
```

```text
10.10.10.10
```

Observe que o atributo recebe o nome longo.

---

# Como o argparse escolhe o nome do atributo?

Exemplo:

```python
parser.add_argument(
    "-t",
    "--target"
)
```

Depois do:

```python
args = parser.parse_args()
```

podemos acessar:

```python
args.target
```

e não:

```python
args.t
```

Isso acontece porque o `argparse` utiliza automaticamente o nome longo como atributo.

---

# type=

## O que é?

O parâmetro:

```python
type=
```

define qual tipo de dado o `argparse` deve converter automaticamente.

Sem ele, todos os argumentos chegam como:

```python
str
```

---

## Exemplo sem type

Código:

```python
parser.add_argument("-p")
```

Execução:

```bash
python scanner.py -p 80
```

Código:

```python
print(type(args.p))
```

Resultado:

```text
<class 'str'>
```

Mesmo digitando:

```text
80
```

o Python recebe:

```python
"80"
```

---

## type=int

Podemos converter automaticamente.

```python
parser.add_argument(
    "-p",
    type=int
)
```

Execução:

```bash
python scanner.py -p 80
```

Código:

```python
print(type(args.p))
```

Resultado:

```text
<class 'int'>
```

Agora:

```python
args.p
```

já pode ser utilizado em operações matemáticas.

---

## type=float

Também podemos converter para números reais.

```python
parser.add_argument(
    "--tempo",
    type=float
)
```

Execução:

```bash
python scanner.py --tempo 0.5
```

Resultado:

```python
args.tempo
```

```text
0.5
```

Tipo:

```text
float
```

---

## type=str

É o padrão.

Mesmo assim pode ser utilizado para deixar o código mais explícito.

```python
parser.add_argument(
    "--host",
    type=str
)
```

---

## type=bool

Apesar de existir, raramente é utilizado.

Normalmente utiliza-se:

```python
action="store_true"
```

que será explicado posteriormente.

---

## Outros tipos

Qualquer função que receba um valor e retorne outro pode ser utilizada.

Exemplo:

```python
type=pathlib.Path
```

```python
type=ipaddress.ip_address
```

```python
type=pathlib.Path
```

Esses casos são muito comuns em ferramentas profissionais.

---

## O que acontece se a conversão falhar?

Código:

```python
parser.add_argument(
    "-p",
    type=int
)
```

Execução:

```bash
python scanner.py -p teste
```

Resultado:

```text
error:

invalid int value:

teste
```

O programa é encerrado automaticamente.

Não é necessário fazer nenhuma validação.

---

# default=

## O que é?

Define um valor padrão.

Caso o usuário não informe aquele argumento, o `argparse` utilizará o valor definido.

---

## Exemplo

```python
parser.add_argument(
    "--threads",
    type=int,
    default=5
)
```

Execução:

```bash
python scanner.py
```

Resultado:

```python
args.threads
```

```text
5
```

Mesmo sem informar:

```text
--threads
```

---

## Quando utilizar?

É muito utilizado quando existe um valor considerado adequado para a maioria dos casos.

Exemplo:

```python
default=80
```

```python
default=5
```

```python
default=False
```

---

## Fluxo

```text
Usuário informou valor?
            │
      Sim ──┴──── Não
       │           │
       ▼           ▼
valor digitado   default
```

---

# Resumo da Parte 2

Nesta parte estudamos:

- `add_argument()`;
- argumentos posicionais;
- argumentos opcionais;
- nome curto (`-t`);
- nome longo (`--target`);
- como o `argparse` cria os atributos;
- `type=`;
- `default=`.

Na próxima parte estudaremos os parâmetros mais utilizados em ferramentas profissionais:

- `help=`
- `required=`
- `choices=`
- `action=`
- `nargs=`
- `metavar=`
- `dest=`
- `const=`

---

 # add_argument()

## O que é?

O método:

```python
parser.add_argument()
```

é responsável por informar ao `argparse` quais argumentos o programa aceita.

Sempre que desejamos que o usuário possa informar algum valor pelo terminal, devemos utilizar o `add_argument()`.

Visualmente:

```text
Usuário
   │
   ▼
python scanner.py -t 10.10.10.10 -p 80
          │
          ▼
   add_argument()
          │
Define quais argumentos existem
          │
          ▼
parse_args()
          │
          ▼
Namespace
```

Sem utilizar o `add_argument()`, o `argparse` não saberá reconhecer nenhum argumento.

---

# Sintaxe

A forma mais simples é:

```python
parser.add_argument("nome")
```

ou

```python
parser.add_argument("-t")
```

ou

```python
parser.add_argument(
    "-t",
    "--target"
)
```

Cada argumento criado será armazenado dentro do objeto retornado pelo `parse_args()`.

---

# Argumentos posicionais

São argumentos obrigatórios informados **sem utilizar "-" ou "--"**.

Exemplo:

```python
parser.add_argument("host")
```

Execução:

```bash
python scanner.py 10.10.10.10
```

Resultado:

```python
args.host
```

```text
10.10.10.10
```

Observe que não utilizamos:

```text
-host

--host
```

Apenas:

```text
10.10.10.10
```

---

## Mais de um argumento posicional

```python
parser.add_argument("host")

parser.add_argument("porta")
```

Execução:

```bash
python scanner.py 10.10.10.10 80
```

Resultado:

```python
print(args)
```

```text
Namespace(
    host='10.10.10.10',
    porta='80'
)
```

---

## Erro ao esquecer um argumento

Código:

```python
parser.add_argument("host")
```

Executando:

```bash
python scanner.py
```

Resultado:

```text
usage:

scanner.py [-h] host

scanner.py: error:
the following arguments are required:

host
```

Observe que o próprio `argparse` informa o erro.

---

# Argumentos opcionais

São argumentos iniciados por:

```text
-
```

ou

```text
--
```

Exemplo:

```python
parser.add_argument("-t")
```

Execução:

```bash
python scanner.py -t 10.10.10.10
```

Resultado:

```python
args.t
```

```text
10.10.10.10
```

---

# Nome curto

Normalmente possui apenas uma letra.

```python
parser.add_argument("-p")
```

Execução:

```bash
python scanner.py -p 80
```

Resultado:

```python
args.p
```

```text
80
```

É muito utilizado para parâmetros digitados frequentemente.

Exemplos conhecidos:

```text
-v

-h

-p

-t

-u

-w

-o
```

---

# Nome longo

Também podemos criar um nome mais descritivo.

```python
parser.add_argument("--porta")
```

Execução:

```bash
python scanner.py --porta 80
```

Resultado:

```python
args.porta
```

```text
80
```

O nome longo facilita a leitura.

---

# Nome curto + Nome longo

Essa é a forma mais utilizada.

```python
parser.add_argument(
    "-t",
    "--target"
)
```

Agora ambos funcionam.

```bash
python scanner.py -t 10.10.10.10
```

ou

```bash
python scanner.py --target 10.10.10.10
```

Resultado:

```python
args.target
```

```text
10.10.10.10
```

Observe que o atributo recebe o nome longo.

---

# Como o argparse escolhe o nome do atributo?

Exemplo:

```python
parser.add_argument(
    "-t",
    "--target"
)
```

Depois do:

```python
args = parser.parse_args()
```

podemos acessar:

```python
args.target
```

e não:

```python
args.t
```

Isso acontece porque o `argparse` utiliza automaticamente o nome longo como atributo.

---

# type=

## O que é?

O parâmetro:

```python
type=
```

define qual tipo de dado o `argparse` deve converter automaticamente.

Sem ele, todos os argumentos chegam como:

```python
str
```

---

## Exemplo sem type

Código:

```python
parser.add_argument("-p")
```

Execução:

```bash
python scanner.py -p 80
```

Código:

```python
print(type(args.p))
```

Resultado:

```text
<class 'str'>
```

Mesmo digitando:

```text
80
```

o Python recebe:

```python
"80"
```

---

## type=int

Podemos converter automaticamente.

```python
parser.add_argument(
    "-p",
    type=int
)
```

Execução:

```bash
python scanner.py -p 80
```

Código:

```python
print(type(args.p))
```

Resultado:

```text
<class 'int'>
```

Agora:

```python
args.p
```

já pode ser utilizado em operações matemáticas.

---

## type=float

Também podemos converter para números reais.

```python
parser.add_argument(
    "--tempo",
    type=float
)
```

Execução:

```bash
python scanner.py --tempo 0.5
```

Resultado:

```python
args.tempo
```

```text
0.5
```

Tipo:

```text
float
```

---

## type=str

É o padrão.

Mesmo assim pode ser utilizado para deixar o código mais explícito.

```python
parser.add_argument(
    "--host",
    type=str
)
```

---

## type=bool

Apesar de existir, raramente é utilizado.

Normalmente utiliza-se:

```python
action="store_true"
```

que será explicado posteriormente.

---

## Outros tipos

Qualquer função que receba um valor e retorne outro pode ser utilizada.

Exemplo:

```python
type=pathlib.Path
```

```python
type=ipaddress.ip_address
```

```python
type=pathlib.Path
```

Esses casos são muito comuns em ferramentas profissionais.

---

## O que acontece se a conversão falhar?

Código:

```python
parser.add_argument(
    "-p",
    type=int
)
```

Execução:

```bash
python scanner.py -p teste
```

Resultado:

```text
error:

invalid int value:

teste
```

O programa é encerrado automaticamente.

Não é necessário fazer nenhuma validação.

---

# default=

## O que é?

Define um valor padrão.

Caso o usuário não informe aquele argumento, o `argparse` utilizará o valor definido.

---

## Exemplo

```python
parser.add_argument(
    "--threads",
    type=int,
    default=5
)
```

Execução:

```bash
python scanner.py
```

Resultado:

```python
args.threads
```

```text
5
```

Mesmo sem informar:

```text
--threads
```

---

## Quando utilizar?

É muito utilizado quando existe um valor considerado adequado para a maioria dos casos.

Exemplo:

```python
default=80
```

```python
default=5
```

```python
default=False
```

---

## Fluxo

```text
Usuário informou valor?
            │
      Sim ──┴──── Não
       │           │
       ▼           ▼
valor digitado   default
```

---

# Resumo da Parte 2

Nesta parte estudamos:

- `add_argument()`;
- argumentos posicionais;
- argumentos opcionais;
- nome curto (`-t`);
- nome longo (`--target`);
- como o `argparse` cria os atributos;
- `type=`;
- `default=`.

Na próxima parte estudaremos os parâmetros mais utilizados em ferramentas profissionais:

- `help=`
- `required=`
- `choices=`
- `action=`
- `nargs=`
- `metavar=`
- `dest=`
- `const=`

---

# nargs=

## O que é?

O parâmetro:

```python
nargs=
```

define **quantos valores um argumento pode receber**.

Por padrão, todo argumento recebe apenas **um único valor**.

Exemplo:

```python
parser.add_argument("--host")
```

Execução:

```bash
python scanner.py --host 10.10.10.10
```

Resultado:

```python
args.host
```

```text
10.10.10.10
```

Mas existem situações onde desejamos receber:

- vários IPs;
- várias portas;
- vários arquivos;
- vários usuários.

Para isso existe o `nargs`.

---

# Funcionamento

Visualmente:

```text
Sem nargs

--host 10.10.10.10
         │
         ▼
     Um valor
```

Com:

```python
nargs="+"
```

```text
--host 10.10.10.10 10.10.10.20 10.10.10.30
          │
          ▼
      Vários valores
```

---

# nargs=1

Recebe exatamente um valor.

```python
parser.add_argument(
    "--host",
    nargs=1
)
```

Execução:

```bash
python scanner.py --host 10.10.10.10
```

Resultado:

```python
args.host
```

```python
['10.10.10.10']
```

Observe.

Mesmo sendo apenas um valor.

O resultado é uma lista.

---

# nargs=2

Recebe exatamente dois valores.

Código.

```python
parser.add_argument(
    "--hosts",
    nargs=2
)
```

Execução.

```bash
python scanner.py \
--hosts 10.10.10.10 10.10.10.20
```

Resultado.

```python
args.hosts
```

```python
[
    '10.10.10.10',
    '10.10.10.20'
]
```

---

Se informar apenas um.

```bash
python scanner.py \
--hosts 10.10.10.10
```

Resultado.

```text
error

expected 2 arguments
```

---

# nargs="+"

Um dos mais utilizados.

Significa:

> Receba um ou mais valores.

Código.

```python
parser.add_argument(
    "--hosts",
    nargs="+"
)
```

Execução.

```bash
python scanner.py \
--hosts \
10.10.10.10 \
10.10.10.20 \
10.10.10.30
```

Resultado.

```python
args.hosts
```

```python
[
    '10.10.10.10',
    '10.10.10.20',
    '10.10.10.30'
]
```

---

## O que acontece se não informar nenhum?

```bash
python scanner.py --hosts
```

Resultado.

```text
error

expected at least one argument
```

---

## Quando utilizar?

Muito comum em:

Scanner de portas

```bash
python scanner.py \
--hosts \
10.10.10.10 \
10.10.10.20 \
10.10.10.30
```

---

Ferramentas de enumeração.

```bash
python enum.py \
host1 \
host2 \
host3
```

---

# nargs="*"

Recebe zero ou mais valores.

Código.

```python
parser.add_argument(
    "--hosts",
    nargs="*"
)
```

Sem informar nada.

```bash
python scanner.py
```

Resultado.

```python
[]
```

Uma lista vazia.

---

Informando valores.

```bash
python scanner.py \
--hosts \
10.10.10.10 \
10.10.10.20
```

Resultado.

```python
[
    '10.10.10.10',
    '10.10.10.20'
]
```

---

# Diferença entre * e +

```text
+
```

Obrigatoriamente.

Pelo menos um valor.

```text
*
```

Pode não receber nenhum.

---

Visualmente.

```text
nargs="+"

1
2
3
4
...
```

Já.

```text
nargs="*"

0
1
2
3
...
```

---

# nargs="?"

Recebe zero ou um valor.

Código.

```python
parser.add_argument(
    "--host",
    nargs="?"
)
```

Executando.

```bash
python scanner.py
```

Resultado.

```python
None
```

Agora.

```bash
python scanner.py \
--host 10.10.10.10
```

Resultado.

```python
'10.10.10.10'
```

---

Muito utilizado junto com.

```python
const=
```

que veremos posteriormente.

---

# Exemplo prático

Imagine um scanner.

Queremos aceitar várias portas.

Código.

```python
parser.add_argument(
    "-p",
    "--ports",
    nargs="+",
    type=int
)
```

Execução.

```bash
python scanner.py \
-p \
22 \
80 \
443 \
3306
```

Resultado.

```python
args.ports
```

```python
[
    22,
    80,
    443,
    3306
]
```

Observe.

Como utilizamos.

```python
type=int
```

Todos os valores foram convertidos automaticamente.

---

# Combinando nargs e type

Essa combinação é extremamente comum.

Código.

```python
parser.add_argument(
    "--threads",
    nargs="+",
    type=int
)
```

Execução.

```bash
python exemplo.py \
--threads \
1 \
2 \
4 \
8
```

Resultado.

```python
[
    1,
    2,
    4,
    8
]
```

---

# metavar=

## O que é?

O parâmetro:

```python
metavar=
```

não altera o funcionamento do programa.

Ele altera apenas a forma como o argumento aparece na ajuda (`-h`).

---

Sem:

```python
parser.add_argument("--target")
```

Saída.

```text
--target TARGET
```

---

Com:

```python
parser.add_argument(
    "--target",
    metavar="IP"
)
```

Agora.

```bash
python scanner.py -h
```

Resultado.

```text
--target IP
```

Observe.

Mudou apenas a aparência.

---

Outro exemplo.

```python
parser.add_argument(
    "--porta",
    metavar="PORTA"
)
```

Resultado.

```text
--porta PORTA
```

---

# Quando utilizar?

Quando desejar deixar a ajuda mais clara.

Em vez de:

```text
TARGET
```

Pode escrever.

```text
IP
```

Ou.

```text
HOST
```

Ou.

```text
ARQUIVO
```

Ou.

```text
WORDLIST
```

Melhorando bastante a legibilidade.

---

# Resumo da Parte 4

Nesta parte aprendemos:

- `nargs=1`
- `nargs=2`
- `nargs="+"`
- `nargs="*"`
- `nargs="?"`
- combinação entre `nargs` e `type`
- `metavar=`

Na próxima parte veremos os últimos parâmetros importantes (`dest` e `const`), aprenderemos a personalizar a ajuda (`description`, `epilog`, grupos de argumentos) e finalizaremos com exemplos completos de ferramentas reais de Pentest utilizando `argparse`.

---

# dest=

## O que é?

O parâmetro:

```python
dest=
```

permite definir manualmente o nome do atributo criado pelo `argparse`.

Por padrão, o nome do atributo é obtido automaticamente a partir do nome longo do argumento.

---

## Exemplo sem dest

Código.

```python
parser.add_argument(
    "-t",
    "--target"
)
```

Execução.

```bash
python scanner.py --target 10.10.10.10
```

Resultado.

```python
print(args.target)
```

```text
10.10.10.10
```

O atributo criado foi:

```python
args.target
```

---

## Exemplo com dest

Código.

```python
parser.add_argument(
    "-t",
    "--target",
    dest="host"
)
```

Execução.

```bash
python scanner.py --target 10.10.10.10
```

Agora.

```python
print(args.host)
```

Resultado.

```text
10.10.10.10
```

Observe.

O usuário continua digitando:

```bash
--target
```

Mas o programa utiliza:

```python
args.host
```

---

## Quando utilizar?

Quando desejar utilizar nomes menores ou mais claros dentro do código.

Exemplo.

Usuário:

```bash
--target
```

Código.

```python
args.ip
```

---

# const=

## O que é?

O parâmetro:

```python
const=
```

define um valor constante que será utilizado em determinadas situações.

Normalmente ele é utilizado junto com:

```python
nargs="?"
```

---

## Exemplo

Código.

```python
parser.add_argument(
    "--host",
    nargs="?",
    const="127.0.0.1"
)
```

Executando.

```bash
python scanner.py --host
```

Resultado.

```python
args.host
```

```text
127.0.0.1
```

Agora.

```bash
python scanner.py --host 10.10.10.10
```

Resultado.

```text
10.10.10.10
```

---

# description=

## O que é?

Permite adicionar uma descrição ao programa.

Essa descrição aparece no topo da ajuda.

---

Exemplo.

```python
parser = argparse.ArgumentParser(
    description="Scanner de Portas em Python"
)
```

Executando.

```bash
python scanner.py -h
```

Resultado.

```text
Scanner de Portas em Python
```

---

# epilog=

## O que é?

Adiciona um texto ao final da ajuda.

Muito utilizado para colocar exemplos de uso.

---

Exemplo.

```python
parser = argparse.ArgumentParser(
    epilog="Exemplo: python scanner.py -t 10.10.10.10"
)
```

Resultado.

```text
Exemplo:

python scanner.py -t 10.10.10.10
```

---

# Grupos de Argumentos

Quando uma ferramenta possui muitos parâmetros.

Exemplo.

```text
Scanner

Autenticação

Proxy

Saída

Performance
```

Podemos agrupá-los.

Código.

```python
scanner = parser.add_argument_group(
    "Scanner"
)

scanner.add_argument("--target")
scanner.add_argument("--port")
```

Resultado.

```text
Scanner

--target

--port
```

A ajuda fica muito mais organizada.

---

# Mutually Exclusive Groups

## O que é?

Permite criar argumentos que não podem ser utilizados ao mesmo tempo.

Exemplo.

```text
--tcp

--udp
```

O usuário deve escolher apenas um.

---

Código.

```python
grupo = parser.add_mutually_exclusive_group()

grupo.add_argument(
    "--tcp",
    action="store_true"
)

grupo.add_argument(
    "--udp",
    action="store_true"
)
```

Agora.

```bash
python scanner.py --tcp
```

Funciona.

Também.

```bash
python scanner.py --udp
```

Funciona.

Mas.

```bash
python scanner.py --tcp --udp
```

Resultado.

```text
error

not allowed with argument
```

---

# Exemplo completo

Scanner simples.

```python
import argparse

parser = argparse.ArgumentParser(
    description="Scanner de Portas"
)

parser.add_argument(
    "-t",
    "--target",
    required=True,
    help="IP do alvo"
)

parser.add_argument(
    "-p",
    "--ports",
    nargs="+",
    type=int,
    default=[80],
    help="Portas a serem verificadas"
)

parser.add_argument(
    "-v",
    "--verbose",
    action="store_true",
    help="Modo detalhado"
)

args = parser.parse_args()

print(args)
```

Execução.

```bash
python scanner.py \
-t 10.10.10.10 \
-p 22 80 443 \
-v
```

Resultado.

```text
Namespace(
    target='10.10.10.10',
    ports=[22, 80, 443],
    verbose=True
)
```

---

# Exemplo de Ping Sweep

```bash
python pingsweep.py \
--network 192.168.0.0/24 \
--threads 100 \
--timeout 2 \
-v
```

Todos esses parâmetros podem ser criados utilizando apenas:

```python
add_argument()
```

---

# Erros comuns

## Esquecer parse_args()

```python
parser.add_argument("--host")

print(args.host)
```

Resultado.

```text
NameError
```

Sempre execute:

```python
args = parser.parse_args()
```

antes de acessar qualquer argumento.

---

## Esquecer type=int

Código.

```python
parser.add_argument("--port")
```

Resultado.

```python
args.port
```

Será:

```python
"80"
```

e não.

```python
80
```

---

## Esquecer help

Sem:

```python
help=
```

A ajuda do programa fica pouco informativa.

Sempre documente seus argumentos.

---

## Usar required=True em argumentos posicionais

Argumentos posicionais já são obrigatórios.

Exemplo.

```python
parser.add_argument("host")
```

Não há necessidade de utilizar.

```python
required=True
```

---

# Boas práticas

✔ Sempre utilize nomes longos.

```text
--target

--threads

--timeout
```

✔ Utilize nomes curtos apenas quando fizer sentido.

```text
-t

-p

-v
```

✔ Sempre escreva uma mensagem em:

```python
help=
```

✔ Utilize:

```python
type=
```

para evitar conversões manuais.

✔ Utilize:

```python
choices=
```

sempre que existir uma quantidade limitada de valores.

✔ Utilize:

```python
action="store_true"
```

para argumentos booleanos.

✔ Utilize:

```python
default=
```

para definir valores seguros.

---

# Resumo dos parâmetros

| Parâmetro | Função | Exemplo |
|-----------|--------|----------|
| `type` | Converte automaticamente o tipo do argumento | `type=int` |
| `default` | Define um valor padrão | `default=80` |
| `help` | Exibe uma descrição na ajuda (`-h`) | `help="IP do alvo"` |
| `required` | Torna um argumento opcional obrigatório | `required=True` |
| `choices` | Limita os valores aceitos | `choices=["tcp","udp"]` |
| `action` | Executa uma ação especial | `action="store_true"` |
| `nargs` | Define quantos valores serão recebidos | `nargs="+"` |
| `metavar` | Altera o nome exibido na ajuda | `metavar="IP"` |
| `dest` | Define o nome do atributo criado | `dest="host"` |
| `const` | Define um valor constante | `const="127.0.0.1"` |

---

# Comparação entre sys.argv e argparse

| sys.argv | argparse |
|----------|----------|
| Simples | Completo |
| Sem validação | Validação automática |
| Todos os argumentos são strings | Conversão automática de tipos |
| Não possui ajuda | `-h` automático |
| Tratamento manual de erros | Erros amigáveis |
| Código maior | Código organizado |
| Pouco escalável | Ideal para ferramentas profissionais |

---

# Quando utilizar argparse?

Sempre que estiver desenvolvendo ferramentas executadas pelo terminal.

Exemplos.

- Scanner de Portas
- Ping Sweep
- Banner Grabbing
- Enumeradores
- Ferramentas de OSINT
- Brute Force (laboratório)
- Ferramentas para Hack The Box
- Scripts de automação
- Utilitários para Linux

Praticamente qualquer ferramenta profissional escrita em Python utiliza algum tipo de parser de argumentos, e o `argparse` é a escolha mais comum por fazer parte da biblioteca padrão da linguagem.

---

# Conclusão

O `argparse` transforma programas simples em ferramentas profissionais de linha de comando.

Além de facilitar a leitura do código, ele fornece validação automática, mensagens de ajuda, conversão de tipos e organização dos argumentos, eliminando grande parte do código que seria necessário utilizando apenas `sys.argv`.

Aprender `argparse` é um dos primeiros passos para desenvolver ferramentas de Pentest, automação e administração de sistemas em Python com uma interface semelhante à de utilitários conhecidos como `nmap`, `ffuf`, `gobuster` e `hydra`.