# Variáveis Especiais do Bash

## O que são?

Além das variáveis criadas pelo usuário, o Bash possui diversas **variáveis especiais**.

Essas variáveis são criadas automaticamente pelo shell e fornecem informações sobre:

- O sistema.
- O usuário.
- O ambiente de execução.
- O próprio script.

Algumas delas também possuem comportamentos especiais, como gerar valores automaticamente.

---

# Resumo

| Variável | Descrição |
|----------|-----------|
| `RANDOM` | Gera números pseudoaleatórios |
| `SECONDS` | Tempo em segundos desde o início do script |
| `UID` | ID do usuário atual |
| `PWD` | Diretório atual |
| `OLDPWD` | Diretório anterior |
| `HOME` | Diretório pessoal do usuário |
| `HOSTNAME` | Nome da máquina |
| `BASH_VERSION` | Versão do Bash |

---

# RANDOM

## O que é?

`RANDOM` é uma variável especial que gera um número pseudoaleatório sempre que é acessada.

Ela já existe no Bash e não precisa ser declarada.

## Exemplo

```bash
echo "$RANDOM"
```

Saída:

```text
21834
```

Executando novamente:

```bash
echo "$RANDOM"
```

Saída:

```text
971
```

Cada leitura gera um novo número.

---

## Intervalo

```text
0 até 32767
```

---

## Guardando um valor

```bash
numero=$RANDOM

echo "$numero"
echo "$numero"
```

Saída:

```text
15428
15428
```

Agora o valor permanece o mesmo.

---

## Gerando números em um intervalo

### Entre 0 e 9

```bash
echo $(( RANDOM % 10 ))
```

---

### Entre 1 e 10

```bash
echo $(( RANDOM % 10 + 1 ))
```

---

### Entre 50 e 100

```bash
echo $(( RANDOM % 51 + 50 ))
```

---

## Simulando um dado

```bash
echo $(( RANDOM % 6 + 1 ))
```

Resultado possível

```text
4
```

---

## Cara ou coroa

```bash
if (( RANDOM % 2 == 0 )); then
    echo "Cara"
else
    echo "Coroa"
fi
```

---

## Escolhendo um elemento de um vetor

```bash
nomes=("Ana" "Carlos" "Maria" "João")

indice=$(( RANDOM % ${#nomes[@]} ))

echo "${nomes[$indice]}"
```

---

## Gerando uma senha simples

```bash
senha=""

for (( i=0; i<8; i++ ))
do
    senha+=$(( RANDOM % 10 ))
done

echo "$senha"
```

---

## O operador `%`

O operador `%` representa o **resto da divisão**.

Exemplo

```bash
10 % 3
```

Resultado

```text
1
```

Porque

```text
10 ÷ 3 = 3

Resto = 1
```

Outro exemplo

```bash
17 % 5
```

Resultado

```text
2
```

Esse operador é utilizado para limitar os valores produzidos pelo `RANDOM`.

---

## Observação

O `RANDOM` gera números **pseudoaleatórios**.

Ele não deve ser utilizado para fins criptográficos, como geração de senhas seguras ou chaves de criptografia.

Para isso, utilize ferramentas como:

```bash
openssl rand
```

ou

```bash
/dev/urandom
```

---

# SECONDS

## O que é?

`SECONDS` armazena o número de segundos desde que o shell ou o script foi iniciado.

## Exemplo

```bash
echo "$SECONDS"

sleep 5

echo "$SECONDS"
```

Saída

```text
0
5
```

---

## Medindo tempo de execução

```bash
inicio=$SECONDS

sleep 3

echo "Tempo: $((SECONDS - inicio)) segundos"
```

Saída

```text
Tempo: 3 segundos
```

---

# UID

## O que é?

`UID` contém o identificador numérico do usuário atual.

## Exemplo

```bash
echo "$UID"
```

Saída

```text
1000
```

O usuário **root** possui:

```text
0
```

---

## Verificando se o script está sendo executado como root

```bash
if (( UID == 0 )); then
    echo "Executando como root."
else
    echo "Usuário comum."
fi
```

---

# PWD

## O que é?

`PWD` contém o diretório atual.

## Exemplo

```bash
echo "$PWD"
```

Saída

```text
/home/allan/Documentos
```

É equivalente a:

```bash
pwd
```

---

# OLDPWD

## O que é?

Armazena o diretório anterior.

É atualizado sempre que usamos o comando:

```bash
cd
```

## Exemplo

```bash
cd /etc

cd /home

echo "$OLDPWD"
```

Saída

```text
/etc
```

---

# HOME

## O que é?

`HOME` aponta para o diretório pessoal do usuário.

## Exemplo

```bash
echo "$HOME"
```

Saída

```text
/home/allan
```

Muito utilizado para acessar arquivos do usuário.

Exemplo

```bash
cd "$HOME"
```

---

# HOSTNAME

## O que é?

Contém o nome da máquina.

## Exemplo

```bash
echo "$HOSTNAME"
```

Saída

```text
ubuntu
```

Pode ser útil para identificar em qual computador um script está sendo executado.

---

# BASH_VERSION

## O que é?

Mostra a versão do Bash instalada.

## Exemplo

```bash
echo "$BASH_VERSION"
```

Saída

```text
5.2.21(1)-release
```

Muito útil para verificar compatibilidade de scripts.

---

# Comparação rápida

| Variável | Tipo | Exemplo |
|----------|------|----------|
| `RANDOM` | Gera valores | `18342` |
| `SECONDS` | Tempo | `15` |
| `UID` | Identificação | `1000` |
| `PWD` | Diretório atual | `/home/allan` |
| `OLDPWD` | Diretório anterior | `/etc` |
| `HOME` | Diretório do usuário | `/home/allan` |
| `HOSTNAME` | Nome da máquina | `ubuntu` |
| `BASH_VERSION` | Versão do Bash | `5.2.21` |

---

# Quando utilizar?

| Variável | Utilização |
|----------|------------|
| `RANDOM` | Jogos, sorteios, testes |
| `SECONDS` | Medir tempo de execução |
| `UID` | Verificar permissões de usuário |
| `PWD` | Obter o diretório atual |
| `OLDPWD` | Retornar ao diretório anterior |
| `HOME` | Acessar o diretório pessoal do usuário |
| `HOSTNAME` | Identificar a máquina |
| `BASH_VERSION` | Verificar compatibilidade de scripts |

---

# Resumo

## Gerar um número aleatório

```bash
echo "$RANDOM"
```

## Tempo desde o início do script

```bash
echo "$SECONDS"
```

## Ver o ID do usuário

```bash
echo "$UID"
```

## Ver o diretório atual

```bash
echo "$PWD"
```

## Ver o diretório anterior

```bash
echo "$OLDPWD"
```

## Ver o diretório pessoal

```bash
echo "$HOME"
```

## Ver o nome da máquina

```bash
echo "$HOSTNAME"
```

## Ver a versão do Bash

```bash
echo "$BASH_VERSION"
```