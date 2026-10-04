A biblioteca `socket` permite criar comunicação entre processos através da rede. Em Python, ela é usada para desenvolver clientes, servidores, aplicações de rede, chats, ferramentas de automação de rede e diversos projetos relacionados a redes e cibersegurança.

---

## O que é um Socket?

Um **socket** é um ponto de comunicação entre dois processos.

Em uma comunicação de rede, podemos ter:

```text
┌──────────────┐                    ┌──────────────┐
│    CLIENTE   │                    │   SERVIDOR   │
│              │                    │              │
│   socket     │◄────── TCP ───────►│   socket     │
│              │                    │              │
└──────────────┘                    └──────────────┘
```

O socket permite que os programas enviem e recebam dados.

Em uma comunicação TCP tradicional:

```text
Servidor                         Cliente

socket()
   │
bind()
   │
listen()
   │
accept() ◄──────────────────── connect()
   │
   │
recv() ◄────────────────────── sendall()
   │
sendall() ────────────────────► recv()
```

---

# Importando a biblioteca

```python
import socket
```

A biblioteca `socket` faz parte da biblioteca padrão do Python, portanto não precisa ser instalada com `pip`.

---

# Criando um Socket

A criação normalmente é feita com:

```python
socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

Exemplo:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

## `socket.socket()`

Cria um novo socket.

### Sintaxe

```python
socket.socket(
    family=AF_INET,
    type=SOCK_STREAM,
    proto=0,
    fileno=None
)
```

|Parâmetro|Tipo|Obrigatório|Padrão|Função|
|---|---|--:|---|---|
|`family`|constante|Não|`AF_INET`|Define a família de endereços|
|`type`|constante|Não|`SOCK_STREAM`|Define o tipo de comunicação|
|`proto`|inteiro|Não|`0`|Define o protocolo específico|
|`fileno`|inteiro|Não|`None`|Usa um descritor de arquivo existente|

Na prática, para começar com TCP/IPv4, normalmente utilizamos:

```python
socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

---

# `AF_INET`

```python
socket.AF_INET
```

Define a família de endereços **IPv4**.

Exemplo:

```text
127.0.0.1
192.168.0.10
10.0.0.5
```

Para IPv6:

```python
socket.AF_INET6
```

Exemplo de endereço IPv6:

```text
::1
```

---

# `SOCK_STREAM`

```python
socket.SOCK_STREAM
```

Define um socket orientado a fluxo.

É normalmente utilizado com **TCP**.

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Representação:

```text
SOCK_STREAM
     │
     ▼
    TCP
     │
     ▼
Conexão orientada
a conexão
```

O TCP fornece mecanismos para comunicação confiável e ordenada.

---

# TCP x UDP

A escolha do tipo de socket depende do protocolo desejado.

### TCP

```python
socket.SOCK_STREAM
```

Normalmente utilizado para TCP.

Características:

- orientado à conexão;
    
- entrega confiável;
    
- mantém a ordem dos bytes;
    
- possui controle de transmissão;
    
- utiliza `connect()` / `accept()` em uma comunicação tradicional.
    

### UDP

```python
socket.SOCK_DGRAM
```

Normalmente utilizado para UDP.

Características:

- não estabelece uma conexão TCP;
    
- possui menor overhead;
    
- não garante entrega;
    
- não garante a ordem dos datagramas.
    

---

# Endereço de Rede

Em IPv4, um endereço normalmente é representado por:

```python
(host, port)
```

Exemplo:

```python
('127.0.0.1', 4444)
```

Onde:

```text
127.0.0.1 → endereço IP
4444      → porta
```

---

# `bind()`

O método `bind()` associa o socket a um endereço local.

```python
server.bind(('127.0.0.1', 4444))
```

### Sintaxe

```python
socket.bind(address)
```

|Parâmetro|Tipo|Obrigatório|Função|
|---|---|--:|---|
|`address`|tupla|Sim|Endereço local que será associado ao socket|

Exemplo:

```python
server.bind(('127.0.0.1', 4444))
```

Nesse caso, o servidor será associado ao:

```text
IP:    127.0.0.1
Porta: 4444
```

---

## `127.0.0.1`

```text
127.0.0.1
```

É o endereço de **loopback**.

Ele representa a própria máquina.

Quando usamos:

```python
server.bind(('127.0.0.1', 4444))
```

o servidor fica acessível somente localmente.

```text
┌────────────────────────────┐
│         COMPUTADOR         │
│                            │
│  Cliente ──► 127.0.0.1    │
│             :4444          │
│                 │          │
│                 ▼          │
│              Servidor      │
└────────────────────────────┘
```

---

## `0.0.0.0`

Também podemos utilizar:

```python
server.bind(('0.0.0.0', 4444))
```

Nesse caso, o servidor fica associado a todas as interfaces IPv4 disponíveis.

Isso permite que outros dispositivos da rede possam acessar o serviço, desde que firewall, roteamento e demais configurações permitam.

```text
             ┌───────────────┐
             │    SERVIDOR   │
             │               │
             │ 0.0.0.0:4444  │
             └───────┬───────┘
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
     Ethernet       Wi-Fi      Loopback
```

**Importante:** `0.0.0.0` é um endereço usado para indicar a escuta em todas as interfaces; não é o endereço que normalmente usamos como destino de um cliente.

---

# `listen()`

Depois de associar o socket a um endereço, um servidor TCP precisa colocá-lo em estado de escuta.

```python
server.listen()
```

### Sintaxe

```python
socket.listen(backlog=None)
```

Exemplo:

```python
server.bind(('127.0.0.1', 4444))
server.listen()
```

O fluxo fica:

```text
socket()
   │
   ▼
bind()
   │
   ▼
listen()
   │
   ▼
Aguardando conexões
```

O `listen()` transforma o socket em um socket de escuta para conexões TCP.

---

# `accept()`

Depois de executar `listen()`, o servidor pode aceitar uma conexão:

```python
client, address = server.accept()
```

### Sintaxe

```python
socket.accept()
```

Retorna:

```python
(connection, address)
```

Exemplo:

```python
client, address = server.accept()
```

Podemos interpretar:

```text
client
   │
   └── socket específico daquele cliente

address
   │
   └── endereço do cliente
```

Para IPv4, `address` normalmente será:

```python
('127.0.0.1', 53218)
```

Onde:

```text
127.0.0.1 → IP do cliente
53218     → porta utilizada pelo cliente
```

---

## Socket do servidor x Socket do cliente conectado

Esse conceito é importante.

Depois de:

```python
client, address = server.accept()
```

existem dois sockets:

```text
server
  │
  └── continua responsável por aceitar conexões

client
  │
  └── comunicação com aquele cliente específico
```

Exemplo:

```text
              SERVER SOCKET
                   │
              server.accept()
                   │
          ┌────────┴────────┐
          ▼                 ▼
      Cliente 1          Cliente 2
       socket              socket
```

O `accept()` não transforma o `server` no socket do cliente.

Ele cria/retorna um novo socket para aquela conexão.

---

# `connect()`

O cliente utiliza `connect()` para iniciar uma conexão TCP com o servidor.

```python
client.connect(('127.0.0.1', 4444))
```

### Sintaxe

```python
socket.connect(address)
```

|Parâmetro|Tipo|Obrigatório|Função|
|---|---|--:|---|
|`address`|tupla|Sim|Endereço do servidor|

Exemplo:

```python
client.connect(('127.0.0.1', 4444))
```

O cliente está tentando conectar em:

```text
IP:    127.0.0.1
Porta: 4444
```

---

# `recv()`

O método `recv()` recebe dados através do socket.

```python
data = client.recv(1024)
```

### Sintaxe

```python
socket.recv(bufsize)
```

|Parâmetro|Tipo|Obrigatório|Função|
|---|---|--:|---|
|`bufsize`|inteiro|Sim|Quantidade máxima de bytes solicitada|

Exemplo:

```python
data = client.recv(1024)
```

Isso significa:

> Receba até 1024 bytes.

**Não significa que exatamente 1024 bytes serão recebidos.**

Pode retornar menos.

O resultado é do tipo:

```python
bytes
```

Exemplo:

```python
data = client.recv(1024)

print(type(data))
```

Resultado:

```text
<class 'bytes'>
```

---

# `decode()`

Como `recv()` retorna `bytes`, podemos converter os bytes para texto:

```python
data = client.recv(1024)

message = data.decode()
```

Exemplo:

```python
print(data)
```

Pode resultar em:

```text
b'Olá'
```

Enquanto:

```python
print(data.decode())
```

resulta em:

```text
Olá
```

Fluxo:

```text
Texto
  │
  │ encode()
  ▼
bytes
  │
  │ transmissão
  ▼
bytes
  │
  │ decode()
  ▼
Texto
```

---

# `encode()`

Converte uma string em bytes.

```python
message = 'Olá'
data = message.encode()
```

Resultado:

```python
b'Ol\xc3\xa1'
```

Na comunicação de rede:

```python
client.sendall(message.encode())
```

é uma forma comum de enviar texto.

---

# `sendall()`

Envia todos os bytes fornecidos através do socket.

```python
client.sendall(message.encode())
```

### Sintaxe

```python
socket.sendall(data)
```

|Parâmetro|Tipo|Obrigatório|Função|
|---|---|--:|---|
|`data`|bytes-like|Sim|Dados que serão enviados|

Exemplo:

```python
message = 'Olá servidor'

client.sendall(message.encode())
```

`sendall()` **não significa enviar para todos os clientes**.

Ele envia os dados pelo socket específico.

```text
CLIENTE
   │
   │ sendall()
   ▼
 SOCKET
   │
   ▼
 SERVIDOR
```

Se o socket representa uma conexão com determinado cliente, os dados serão enviados para aquele peer.

---

# `close()`

Fecha o socket.

```python
client.close()
```

Exemplo:

```python
client.close()
server.close()
```

Normalmente:

```text
client.close()
     │
     ▼
encerra conexão daquele cliente

server.close()
     │
     ▼
fecha o socket do servidor
```

---

# `accept()` é bloqueante

Por padrão, chamadas de rede como `accept()` e `recv()` podem ser **bloqueantes**.

Por exemplo:

```python
client, address = server.accept()
```

O programa fica aguardando até uma conexão chegar.

```text
Servidor
   │
   ▼
accept()
   │
   │ aguardando...
   │
   │ aguardando...
   │
   ▼
Cliente conecta
   │
   ▼
accept() retorna
```

Da mesma forma:

```python
data = client.recv(1024)
```

pode ficar aguardando até dados chegarem.

---

# Problema da comunicação sequencial

Considere:

```python
while True:
    data = client.recv(1024)

    message = input('ADMIN -> ')

    client.sendall(message.encode())
```

Existe um problema:

```text
recv()
  │
  ▼
espera cliente
  │
  ▼
input()
  │
  ▼
espera administrador
  │
  ▼
sendall()
```

Enquanto `input()` estiver esperando o administrador digitar, o programa não estará executando outro código daquela thread.

Isso dificulta uma comunicação realmente bidirecional.

---

# `threading` + `socket`

Uma solução comum é separar envio e recebimento em threads.

Exemplo:

```python
import socket
import threading

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(('127.0.0.1', 4444))
server.listen()

print('Servidor esperando conexão...')

client, address = server.accept()

print(f'Cliente conectado: {address}')


def receive_messages():
    while True:
        data = client.recv(1024)

        if not data:
            print('Cliente desconectou.')
            break

        print(f'\nCliente: {data.decode()}')


thread = threading.Thread(
    target=receive_messages
)

thread.start()


while True:
    message = input('ADMIN -> ')

    if message.lower() == 'exit':
        break

    client.sendall(message.encode())


client.close()
server.close()
```

Agora temos:

```text
                 SERVIDOR
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
    Thread principal      Thread de
                          recebimento
          │                   │
          ▼                   ▼
       input()              recv()
          │                   │
          ▼                   ▼
      sendall()             print()
```

Assim, o servidor pode:

- receber mensagens;
    
- enviar mensagens;
    
- fazer as duas coisas de maneira independente.
    

---

# Identificando o Cliente pelo Nome

Podemos pedir ao cliente que informe um nome ao conectar.

### Cliente

```python
name = input('Digite seu nome: ')

client.sendall(name.encode())
```

O servidor recebe:

```python
name = client.recv(1024).decode()
```

Agora o servidor sabe quem está conectado.

```python
print(f'{name} entrou no chat!')
```

Depois pode identificar as mensagens:

```python
print(f'{name}: {data.decode()}')
```

Exemplo:

```text
Digite seu nome: Marcos
```

Servidor:

```text
Marcos entrou no chat!
Marcos: Olá!
Marcos: Tudo bem?
```

---

# Exemplo completo — Chat TCP simples

## Servidor

```python
import socket
import threading

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(('127.0.0.1', 4444))
server.listen()

print('Servidor esperando conexão...')

client, address = server.accept()

name = client.recv(1024).decode()

print(f'{name} entrou no chat!')
print(f'IP: {address[0]}')
print(f'Porta: {address[1]}')


def receive_messages():
    while True:
        data = client.recv(1024)

        if not data:
            print(f'{name} desconectou.')
            break

        print(f'\n{name}: {data.decode()}')


thread = threading.Thread(
    target=receive_messages
)

thread.start()


while True:
    message = input('ADMIN: ')

    if message.lower() == 'exit':
        break

    client.sendall(message.encode())


client.close()
server.close()
```

---

## Cliente

```python
import socket
import threading

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

client.connect(('127.0.0.1', 4444))

name = input('Digite seu nome: ')

client.sendall(name.encode())

print(f'Conectado como {name}')


def receive_messages():
    while True:
        data = client.recv(1024)

        if not data:
            break

        print(f'\nServidor: {data.decode()}')


thread = threading.Thread(
    target=receive_messages
)

thread.start()


while True:
    message = input(f'{name}: ')

    if message.lower() == 'exit':
        break

    client.sendall(message.encode())


client.close()
```

---

# Fluxo completo do servidor

```text
socket()
   │
   │ cria o socket
   ▼
bind()
   │
   │ associa IP + porta
   ▼
listen()
   │
   │ começa a escutar
   ▼
accept()
   │
   │ aceita conexão
   ▼
recv()
   │
   │ recebe dados
   ▼
decode()
   │
   │ bytes → texto
   ▼
processamento
   │
   ▼
sendall()
   │
   │ envia resposta
   ▼
close()
```

---

# Fluxo completo do cliente

```text
socket()
   │
   │ cria o socket
   ▼
connect()
   │
   │ conecta ao servidor
   ▼
encode()
   │
   │ texto → bytes
   ▼
sendall()
   │
   │ envia dados
   ▼
recv()
   │
   │ recebe dados
   ▼
decode()
   │
   │ bytes → texto
   ▼
close()
```

---

# Servidor x Cliente

|Operação|Servidor|Cliente|
|---|--:|--:|
|`socket()`|✅|✅|
|`bind()`|✅|Normalmente não|
|`listen()`|✅|❌|
|`accept()`|✅|❌|
|`connect()`|❌|✅|
|`recv()`|✅|✅|
|`sendall()`|✅|✅|
|`close()`|✅|✅|

A diferença principal é:

```text
SERVIDOR

socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
   ↓
comunicação


CLIENTE

socket()
   ↓
connect()
   ↓
comunicação
```

---

# Erros comuns

## `OSError: [Errno 98] Address already in use`

Exemplo:

```text
OSError: [Errno 98] Address already in use
```

Significa que a porta escolhida já está sendo utilizada por outro processo ou por um socket que ainda está em estado relacionado à conexão anterior.

Exemplo:

```python
server.bind(('127.0.0.1', 4444))
```

Se outro processo já estiver usando a porta `4444`, o `bind()` poderá falhar.

Uma forma de investigar:

```bash
ss -ltnp | grep 4444
```

Ou:

```bash
lsof -i :4444
```

---

# `recv()` retornando `b''`

Quando:

```python
data = client.recv(1024)
```

retorna:

```python
b''
```

em uma conexão TCP, isso normalmente indica que o outro lado encerrou a conexão de forma ordenada.

Por isso é comum encontrar:

```python
if not data:
    break
```

---

# TCP é um fluxo de bytes

Um ponto importante:

**TCP não trabalha com o conceito de "mensagens" da aplicação.**

Ele fornece um **fluxo de bytes**.

Por exemplo, se o cliente fizer:

```python
client.sendall(b'Olá')
client.sendall(b'Mundo')
```

não devemos assumir que o servidor necessariamente receberá:

```text
recv() → b'Olá'
recv() → b'Mundo'
```

Poderia receber os dados agrupados ou divididos de outra maneira.

Por isso, aplicações reais precisam definir algum mecanismo de **framing/protocolo de mensagens**, como:

- tamanho da mensagem;
    
- delimitador;
    
- JSON com protocolo próprio;
    
- newline (`\n`);
    
- cabeçalho contendo tamanho do payload.
    

Esse é um dos próximos conceitos importantes depois de entender o básico de sockets.

---

# Resumo da Parte

### Criação

```python
socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

Cria um socket IPv4 utilizando comunicação orientada a fluxo, normalmente TCP.

### Servidor

```python
socket()
↓
bind()
↓
listen()
↓
accept()
↓
recv() / sendall()
↓
close()
```

### Cliente

```python
socket()
↓
connect()
↓
recv() / sendall()
↓
close()
```

### Principais métodos

|Método|Função|
|---|---|
|`socket()`|Cria o socket|
|`bind()`|Associa o socket a um endereço local|
|`listen()`|Coloca o socket TCP em estado de escuta|
|`accept()`|Aceita uma conexão recebida|
|`connect()`|Conecta o cliente ao servidor|
|`recv()`|Recebe bytes|
|`sendall()`|Envia todos os bytes fornecidos|
|`close()`|Fecha o socket|
|`encode()`|Converte texto para bytes|
|`decode()`|Converte bytes para texto|

### Conceito central

```text
SOCKET
  │
  ├── Endereço → IP + Porta
  │
  ├── TCP
  │    ├── Servidor → bind → listen → accept
  │    └── Cliente  → connect
  │
  └── Comunicação
       ├── sendall()
       └── recv()
```

## **Próximos conceitos:** múltiplos clientes, `threading` por conexão, broadcast, gerenciamento de clientes, encerramento seguro, tratamento de exceções e framing de mensagens TCP.