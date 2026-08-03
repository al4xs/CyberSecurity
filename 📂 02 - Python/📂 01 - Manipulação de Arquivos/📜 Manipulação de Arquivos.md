A biblioteca nativa do Python permite criar, ler, modificar e apagar arquivos através da função `open()`.

---

# Sintaxe

```python
arquivo = open(caminho, modo)
```

Exemplo:

```python
arquivo = open("nomes.txt", "r")
```

---

# Parâmetros da função

```python
open(file, mode="r", encoding=None)
```

| Parâmetro | Obrigatório | Descrição |
|-----------|------------|-----------|
| file | ✅ | Caminho do arquivo. |
| mode | ❌ | Modo de abertura do arquivo. O padrão é `"r"`. |
| encoding | ❌ | Codificação do arquivo (UTF-8, ASCII...). |

Exemplo

```python
arquivo = open("usuarios.txt", "r", encoding="utf-8")
```

---

# Parâmetro file

Representa o caminho do arquivo.

Pode ser:

Arquivo na mesma pasta.

```python
open("dados.txt")
```

---

Caminho absoluto.

```python
open("/home/mafiaboy/Documentos/dados.txt")
```

---

Caminho relativo.

```python
open("../dados.txt")
```

---

# Parâmetro mode

Define como o arquivo será aberto.

---

## r

Read (Leitura)

Abre um arquivo apenas para leitura.

O arquivo precisa existir.

```python
arquivo = open("dados.txt", "r")
```

Permite

- Ler

Não permite

- Escrever

- Criar

---

## w

Write (Escrita)

Cria um arquivo novo.

Se já existir, APAGA todo o conteúdo.

```python
arquivo = open("dados.txt", "w")
```

Permite

- Escrever

- Criar

Não permite

- Ler

⚠️ Muito cuidado.

Esse modo sobrescreve todo o arquivo.

---

## a

Append

Acrescenta informações no final do arquivo.

Se não existir, cria.

```python
arquivo = open("dados.txt", "a")
```

Exemplo.

Antes.

```text
João
Maria
```

Código.

```python
arquivo = open("dados.txt", "a")

arquivo.write("Pedro\n")

arquivo.close()
```

Depois.

```text
João
Maria
Pedro
```

---

## x

Create

Cria um arquivo.

Se já existir, gera erro.

```python
arquivo = open("dados.txt", "x")
```

Erro.

```text
FileExistsError
```

Muito usado quando queremos garantir que o arquivo ainda não exista.

---

## r+

Leitura e escrita.

O arquivo precisa existir.

```python
arquivo = open("dados.txt", "r+")
```

Permite

- Ler

- Escrever

Não cria arquivo.

---

## w+

Leitura e escrita.

Apaga todo o conteúdo.

Cria caso não exista.

```python
arquivo = open("dados.txt", "w+")
```

---

## a+

Leitura e escrita.

Sempre escreve no final.

```python
arquivo = open("dados.txt", "a+")
```

---

# Encoding

Define como os caracteres serão interpretados.

Sem encoding.

```python
open("dados.txt")
```

Com encoding.

```python
open("dados.txt", encoding="utf-8")
```

Recomendado utilizar UTF-8.

---

# Lendo um arquivo

## read()

Lê todo o conteúdo.

```python
arquivo = open("dados.txt", "r")

conteudo = arquivo.read()

print(conteudo)

arquivo.close()
```

Arquivo.

```text
João
Maria
Pedro
```

Saída.

```text
João
Maria
Pedro
```

---

## readline()

Lê apenas uma linha.

Arquivo.

```text
João
Maria
Pedro
```

Código.

```python
arquivo = open("dados.txt")

print(arquivo.readline())

arquivo.close()
```

Saída.

```text
João
```

---

## readlines()

Retorna uma lista.

```python
arquivo = open("dados.txt")

linhas = arquivo.readlines()

print(linhas)

arquivo.close()
```

Saída.

```python
['João\n', 'Maria\n', 'Pedro\n']
```

---

# Escrevendo

## write()

Escreve texto.

```python
arquivo = open("dados.txt", "w")

arquivo.write("Olá Mundo")

arquivo.close()
```

Arquivo.

```text
Olá Mundo
```

---

## writelines()

Escreve várias linhas.

```python
arquivo = open("dados.txt", "w")

arquivo.writelines([
    "João\n",
    "Maria\n",
    "Pedro\n"
])

arquivo.close()
```

---

# Fechando

Sempre feche o arquivo.

```python
arquivo.close()
```

Caso contrário o sistema pode manter o arquivo aberto.

---

# Melhor forma

Utilize o contexto `with`.

```python
with open("dados.txt", "r") as arquivo:

    print(arquivo.read())
```

Ao sair do bloco, o Python fecha o arquivo automaticamente.

Não é necessário utilizar:

```python
arquivo.close()
```

---

# Exemplo completo

```python
with open("usuarios.txt", "r", encoding="utf-8") as arquivo:

    for linha in arquivo:

        print(linha.strip())
```

Arquivo.

```text
João
Maria
Pedro
```

Saída.

```text
João
Maria
Pedro
```

---

# Resumo

| Modo | Lê | Escreve | Cria | Apaga conteúdo |
|------|:--:|:--------:|:----:|:--------------:|
| r | ✅ | ❌ | ❌ | ❌ |
| w | ❌ | ✅ | ✅ | ✅ |
| a | ❌ | ✅ | ✅ | ❌ |
| x | ❌ | ✅ | ✅ | ❌ |
| r+ | ✅ | ✅ | ❌ | ❌ |
| w+ | ✅ | ✅ | ✅ | ✅ |
| a+ | ✅ | ✅ | ✅ | ❌ |

---

# Boas práticas

- Utilize `with` sempre que possível.
- Utilize `encoding="utf-8"` em arquivos de texto.
- Evite utilizar `"w"` quando não quiser apagar o conteúdo existente.
- Utilize `"a"` para adicionar informações.
- Utilize `"x"` quando quiser garantir que o arquivo ainda não exista.