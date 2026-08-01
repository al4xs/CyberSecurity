
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

