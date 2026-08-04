
Quando ocorre um erro durante a execução de um programa, o Python gera uma **Exception (Exceção)**.

Sem tratamento, o programa é encerrado imediatamente.

Com `try` e `except`, é possível capturar o erro e decidir o que fazer.

---

# Estrutura básica

```python
try:
    # Código que pode gerar erro

except:
    # Executado caso ocorra um erro
```

Exemplo.

```python
try:

    numero = int(input("Número: "))

except:

    print("Valor inválido.")
```

Saída.

```text
Número: abc

Valor inválido.
```

Sem o `try`, o programa encerraria.

---

# Como funciona

Fluxo.

```text
Programa

↓

try

↓

Ocorreu erro?

↓

Sim ─────────► except

↓

Não

↓

Continua normalmente
```

---

# try

Representa o bloco onde pode ocorrer uma exceção.

```python
try:

    idade = int(input("Idade: "))
```

Qualquer erro ocorrido aqui será enviado ao `except`.

---

# except

Captura a exceção.

```python
try:

    numero = int(input())

except:

    print("Erro")
```

Esse exemplo captura **qualquer erro**.

---

# Capturando erros específicos

É a forma recomendada.

```python
try:

    numero = int(input())

except ValueError:

    print("Digite apenas números.")
```

Agora somente `ValueError` será tratado.

---

# Exception

Captura praticamente qualquer exceção.

```python
try:

    ...

except Exception:

    print("Erro")
```

Muito usada.

Mas não deve ser utilizada em excesso.

O ideal é capturar erros específicos.

---

# Capturando o erro

Pode guardar a exceção em uma variável.

Sintaxe.

```python
except Exception as erro:
```

Exemplo.

```python
try:

    numero = int(input())

except Exception as erro:

    print(erro)
```

Entrada.

```text
abc
```

Saída.

```text
invalid literal for int() with base 10: 'abc'
```

Muito útil para Debug.

---

# else

Executado apenas se NÃO ocorrer erro.

```python
try:

    numero = int(input())

except ValueError:

    print("Valor inválido.")

else:

    print("Número válido.")
```

Entrada.

```text
10
```

Saída.

```text
Número válido.
```

---

# finally

Executado SEMPRE.

Mesmo ocorrendo erro.

Muito utilizado para:

- fechar arquivos;
- fechar conexões;
- liberar recursos.

```python
try:

    arquivo = open("dados.txt")

except FileNotFoundError:

    print("Arquivo não encontrado.")

finally:

    print("Fim do programa.")
```

Mesmo com erro.

```text
Arquivo não encontrado.

Fim do programa.
```

---

# Estrutura completa

```python
try:

    ...

except ValueError:

    ...

except FileNotFoundError:

    ...

else:

    ...

finally:

    ...
```

Nem todos os blocos são obrigatórios.

---

# Exceções mais utilizadas

## ValueError

Valor inválido.

```python
int("abc")
```

Erro.

```text
ValueError
```

---

## TypeError

Tipo incompatível.

```python
"10" + 10
```

Erro.

```text
TypeError
```

---

## ZeroDivisionError

Divisão por zero.

```python
10 / 0
```

Erro.

```text
ZeroDivisionError
```

---

## FileNotFoundError

Arquivo inexistente.

```python
open("arquivo.txt")
```

Erro.

```text
FileNotFoundError
```

---

## IndexError

Índice inexistente.

```python
lista = [1,2,3]

print(lista[10])
```

Erro.

```text
IndexError
```

---

## KeyError

Chave inexistente.

```python
usuario = {

    "nome": "João"

}

print(usuario["idade"])
```

Erro.

```text
KeyError
```

---

## NameError

Variável inexistente.

```python
print(nome)
```

Erro.

```text
NameError
```

---

## PermissionError

Sem permissão.

```python
open("/etc/shadow")
```

Sem privilégios.

```text
PermissionError
```

---

## OSError

Erro relacionado ao sistema operacional.

Muito usado para:

- Arquivos
- Diretórios
- Permissões

---

## KeyboardInterrupt

Quando o usuário pressiona:

```text
CTRL + C
```

Pode ser tratado.

```python
try:

    while True:

        pass

except KeyboardInterrupt:

    print("\nPrograma encerrado.")
```

---

# Capturando vários erros

```python
try:

    ...

except (ValueError, TypeError):

    print("Erro")
```

---

# Criando sua própria exceção

```python
idade = -10

if idade < 0:

    raise ValueError("Idade inválida.")
```

Saída.

```text
ValueError: Idade inválida.
```

Muito utilizado para validar dados.

---

# Exemplo completo

```python
try:

    numero = int(input("Número: "))

    resultado = 100 / numero

except ValueError:

    print("Digite apenas números.")

except ZeroDivisionError:

    print("Não é possível dividir por zero.")

else:

    print(resultado)

finally:

    print("Programa finalizado.")
```

---

# Resumo

| Palavra | Função |
|----------|--------|
| try | Executa o código que pode gerar erro. |
| except | Captura a exceção. |
| else | Executa apenas se não houver erro. |
| finally | Executa sempre. |
| raise | Gera uma exceção manualmente. |
| as | Guarda o erro em uma variável. |

---

# Boas práticas

✅ Capture erros específicos.

```python
except ValueError:
```

---

❌ Evite.

```python
except:
```

Porque captura qualquer erro e dificulta identificar o problema.

---

Sempre utilize:

```python
except Exception as erro:

    print(erro)
```

Durante o desenvolvimento.

---

Utilize `finally` para:

- fechar arquivos;
- fechar conexões;
- liberar recursos.

---

Utilize `raise` para validar dados quando necessário.

---

# Quando utilizar?

- Ler arquivos.
- Fazer requisições HTTP.
- Trabalhar com banco de dados.
- Receber entrada do usuário.
- Criar ferramentas de Pentest.
- Scripts que manipulam arquivos.
- Automações.