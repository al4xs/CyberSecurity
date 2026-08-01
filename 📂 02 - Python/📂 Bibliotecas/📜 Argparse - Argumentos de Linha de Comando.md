
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

 