# 1. Fundamentos de Sockets

## 1.1 O que é um socket?

Um **socket** é uma interface de comunicação disponibilizada pelo sistema operacional para que um programa possa **enviar e receber dados através de algum mecanismo de comunicação**.

Em Python, a biblioteca `socket` fornece uma interface para trabalhar com essa funcionalidade do sistema operacional.

Em termos práticos, podemos pensar no socket como o ponto onde a aplicação se conecta à infraestrutura de comunicação:

```text
Aplicação Python
      ↓
   socket
      ↓
Kernel / Sistema Operacional
      ↓
   TCP/IP
      ↓
Interface de rede
      ↓
     Rede
      ↓
Outro host
```

Porém, essa representação ainda é uma simplificação. O socket não é exatamente "a rede" e também não é simplesmente "um cabo virtual".

Ele é uma **abstração fornecida pelo sistema operacional** que permite à aplicação interagir com mecanismos de comunicação de rede.

Por isso, quando escrevemos:

```python
import socket

client = socket.socket()
```

não estamos criando uma conexão com outro computador.

Estamos criando um **objeto socket no processo Python**, associado a uma estrutura de socket administrada pelo sistema operacional.

A conexão pode acontecer posteriormente.

---

## 1.2 Por que sockets existem?

Uma aplicação não deveria precisar implementar diretamente toda a lógica necessária para conversar com uma interface de rede.

Imagine um programa querendo enviar:

```text
Olá, servidor!
```

Para isso, existem diversas etapas envolvidas:

```text
Aplicação
   ↓
Dados
   ↓
Socket
   ↓
Sistema operacional
   ↓
TCP
   ↓
IP
   ↓
Interface de rede
   ↓
Meio físico / Wi-Fi
   ↓
Rede
```

O programa não precisa implementar sozinho:

- montagem dos pacotes IP;
    
- controle de sequência do TCP;
    
- retransmissões;
    
- controle de fluxo;
    
- cálculo e verificação de vários campos de protocolos;
    
- gerenciamento da interface de rede;
    
- comunicação com o hardware da placa de rede.
    

Grande parte desse trabalho é responsabilidade do **sistema operacional e da pilha de protocolos de rede**.

O socket fornece uma interface para a aplicação utilizar essa infraestrutura.

---

# 1.3 O modelo mental correto

Uma maneira útil de pensar é:

> **A aplicação trabalha com sockets; o sistema operacional trabalha com a rede.**

Por exemplo:

```python
client.sendall(b"Hello")
```

A aplicação está dizendo essencialmente:

> "Quero enviar estes bytes através deste socket."

A partir daí, o sistema operacional participa do processamento necessário para encaminhar esses dados conforme o tipo de socket e os protocolos utilizados.

O caminho conceitual pode ser representado assim:

```text
┌──────────────────────────────┐
│       Aplicação Python       │
│                              │
│ client.sendall(b"Hello")     │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│       Objeto socket          │
│       em Python              │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│          Kernel              │
│                              │
│ gerenciamento do socket      │
│ buffers                       │
│ TCP/UDP/IP                    │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│      Interface de rede       │
│        Ethernet / Wi-Fi      │
└──────────────┬───────────────┘
               ↓
              Rede
```

Essa separação é fundamental para entender sockets.

---

# 1.4 Socket não é sinônimo de conexão

Um dos primeiros conceitos que precisam ficar claros é:

> **socket e conexão não são exatamente a mesma coisa.**

Podemos criar um socket:

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Nesse momento, temos um socket.

Ainda não necessariamente temos uma conexão TCP estabelecida com outro computador.

A conexão pode ser estabelecida posteriormente:

```python
client.connect(("127.0.0.1", 4444))
```

Podemos representar:

```text
socket()
   ↓
socket criado
   ↓
connect()
   ↓
tentativa de estabelecer conexão
   ↓
TCP handshake
   ↓
conexão estabelecida
```

Portanto:

```python
socket.socket()
```

e:

```python
connect()
```

representam coisas diferentes.

---

# 1.5 O que é um endpoint?

Em redes, um **endpoint** é um ponto final de comunicação.

Em uma comunicação TCP IPv4, podemos identificar um endpoint utilizando, conceitualmente:

```text
IP + porta
```

Por exemplo:

```text
127.0.0.1:4444
```

Aqui:

```text
127.0.0.1
   ↓
endereço IP

4444
   ↓
porta
```

Juntos:

```text
127.0.0.1:4444
```

representam um endpoint de rede.

Uma conexão TCP é normalmente identificada pelo conjunto de:

```text
IP de origem
porta de origem
IP de destino
porta de destino
```

Por exemplo:

```text
Cliente
192.168.1.20:53142
        │
        │ TCP
        ↓
Servidor
192.168.1.10:4444
```

Temos:

```text
Origem:
192.168.1.20:53142

Destino:
192.168.1.10:4444
```

A porta `53142` pode ter sido escolhida automaticamente pelo sistema operacional para o cliente.

Já a porta `4444` pode ter sido escolhida pelo desenvolvedor para o servidor.

---

# 1.6 IP

O **IP (Internet Protocol)** é responsável pelo endereçamento e encaminhamento de datagramas entre redes.

Em IPv4, um endereço possui 32 bits.

Normalmente ele é representado assim:

```text
192.168.1.10
```

Dividido em quatro valores:

```text
192 . 168 . 1 . 10
```

Cada parte representa 8 bits:

```text
8 + 8 + 8 + 8 = 32 bits
```

O IP permite identificar logicamente uma interface/endereço dentro de uma rede IP.

Por exemplo:

```text
192.168.1.10
```

pode identificar um endereço associado a uma máquina ou interface.

Mas o IP sozinho não identifica qual aplicação deve receber os dados.

É aí que entra a **porta**.

---

# 1.7 Porta

Uma máquina pode executar vários serviços simultaneamente.

Por exemplo:

```text
Servidor
│
├── SSH       → 22
├── HTTP      → 80
├── HTTPS     → 443
├── DNS       → 53
└── aplicação → 4444
```

Se chegasse apenas:

```text
192.168.1.10
```

o sistema operacional saberia o endereço do host, mas ainda seria necessário determinar **qual serviço/processo deve receber aquele tráfego**.

A porta ajuda nessa identificação.

Por isso podemos ter:

```text
192.168.1.10:22
192.168.1.10:80
192.168.1.10:443
192.168.1.10:4444
```

Todos pertencem ao mesmo endereço IP, mas representam portas diferentes.

---

# 1.8 IP + porta

Podemos pensar inicialmente em:

```text
IP = qual host/interface
porta = qual serviço/aplicação
```

Essa explicação é útil didaticamente, mas é uma simplificação.

Tecnicamente, uma porta é um identificador utilizado pelos protocolos de transporte, como TCP e UDP, para multiplexar diferentes fluxos/destinos dentro de um host.

Por isso:

```text
192.168.1.10:4444
```

não significa simplesmente:

> "O computador tem um arquivo chamado 4444."

Significa que o endereço IP e a porta participam da identificação do endpoint de transporte.

---

# 1.9 Cliente e servidor

Um dos modelos mais comuns de utilização de sockets é o modelo **cliente/servidor**.

```text
             conexão
Cliente ─────────────────→ Servidor
```

O servidor normalmente:

1. cria um socket;
    
2. associa o socket a um endereço local;
    
3. coloca o socket em modo de escuta;
    
4. aguarda conexões;
    
5. aceita uma conexão;
    
6. troca dados.
    

Conceitualmente:

```text
Servidor

socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
   ↓
comunicação
```

O cliente normalmente:

```text
Cliente

socket()
   ↓
connect()
   ↓
comunicação
```

Isso não significa que todo sistema de rede obrigatoriamente siga esse modelo.

Existem outros modelos, incluindo:

- peer-to-peer;
    
- multicast;
    
- broadcast;
    
- comunicação local através de Unix Domain Sockets;
    
- arquiteturas distribuídas;
    
- protocolos em que os papéis são mais simétricos.
    

O modelo cliente/servidor é apenas uma das formas mais comuns.

---

# 1.10 O socket do servidor e o socket do cliente não são a mesma coisa

Esse ponto é extremamente importante.

Considere:

```python
server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))
server.listen()

client, address = server.accept()
```

Depois de:

```python
client, address = server.accept()
```

temos dois sockets conceitualmente diferentes:

```text
server
   ↓
socket de escuta

client
   ↓
socket da conexão específica
```

Podemos representar:

```text
                    ┌─────────────────────┐
                    │ Socket de escuta    │
                    │ server              │
                    │ 127.0.0.1:4444      │
                    └──────────┬──────────┘
                               │
                            accept()
                               │
                               ↓
                    ┌─────────────────────┐
                    │ Socket conectado    │
                    │ client              │
                    │ cliente específico  │
                    └─────────────────────┘
```

O socket `server` continua existindo para aceitar novas conexões.

O socket retornado por `accept()` representa a comunicação com **aquele cliente específico**.

Isso permite posteriormente construir servidores com múltiplos clientes:

```text
                  Socket de escuta
                        │
              ┌─────────┴─────────┐
              ↓                   ↓
          Cliente A            Cliente B
              │                   │
        socket A             socket B
```

Esse conceito será essencial quando estudarmos `listen()`, `accept()`, `threading`, `selectors` e servidores concorrentes.

---

# 1.11 O socket como interface entre aplicação e kernel

Em sistemas Unix/Linux, sockets possuem uma relação muito importante com **file descriptors**.

Quando um processo cria um socket:

```python
import socket

s = socket.socket()
```

o sistema operacional cria e gerencia uma estrutura associada àquele socket.

O processo recebe um identificador chamado **file descriptor (FD)**.

Podemos imaginar:

```text
Processo Python
│
├── FD 0 → stdin
├── FD 1 → stdout
├── FD 2 → stderr
└── FD 3 → socket
```

O número exato pode variar.

Por isso, um socket em Linux possui uma relação conceitual muito forte com a ideia Unix de:

> "um recurso do sistema operacional representado por um descritor."

Podemos consultar o descritor com:

```python
fd = s.fileno()

print(fd)
```

Por exemplo:

```text
3
```

Isso significa que o processo possui um file descriptor associado ao socket.

---

# 1.12 Por que socket é relacionado a arquivo no Unix?

Em Unix/Linux, muitos recursos do sistema são acessados através de file descriptors.

Por exemplo:

```text
stdin   → FD 0
stdout  → FD 1
stderr  → FD 2
socket  → outro FD
arquivo → outro FD
pipe    → outro FD
```

Isso não significa que um socket seja literalmente um arquivo armazenado no disco.

São recursos diferentes.

O que existe é uma **interface comum baseada em descritores**.

Por isso podemos encontrar situações como:

```bash
ls -l /proc/<PID>/fd/
```

e observar descritores pertencentes a um processo.

Em Linux, um processo que possui sockets abertos terá esses recursos associados aos seus file descriptors.

---

# 1.13 Aplicação → syscall → kernel

Quando um programa Python utiliza um socket, existe uma camada abaixo da biblioteca Python.

Uma visão simplificada:

```text
Código Python
     ↓
biblioteca socket
     ↓
interface do sistema operacional
     ↓
syscall
     ↓
kernel
     ↓
subsystem de rede
     ↓
TCP / UDP / IP
     ↓
driver
     ↓
NIC
```

Por exemplo:

```python
client.sendall(b"Hello")
```

não significa que o Python está diretamente controlando a placa de rede.

O Python utiliza as interfaces fornecidas pelo sistema operacional.

No Linux, isso envolve chamadas ao kernel e mecanismos internos da pilha de rede.

A biblioteca `socket` do Python funciona como uma abstração de alto nível sobre essas operações.

---

# 1.14 O que acontece quando criamos um socket?

Considere:

```python
import socket

s = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Podemos dividir conceitualmente:

### Etapa 1 — Python

O interpretador executa:

```python
socket.socket(...)
```

### Etapa 2 — biblioteca padrão

A implementação de `socket` fornece a interface Python para a funcionalidade de sockets do sistema operacional.

### Etapa 3 — sistema operacional

O sistema operacional cria/associa uma estrutura de socket e fornece um descritor ao processo.

### Etapa 4 — socket ainda não significa conexão

Nesse momento, ainda não necessariamente temos:

```text
Cliente ←→ Servidor
```

Temos apenas um socket configurado para determinada família/tipo de comunicação.

Podemos visualizar:

```text
socket()
   ↓
Socket criado
   │
   ├── ainda não conectado
   ├── ainda pode não possuir porta local definida
   └── ainda não está necessariamente associado a um peer
```

As próximas operações determinarão como esse socket será utilizado.

---

# 1.15 Família de endereços e tipo de socket

Ao criar um socket, normalmente especificamos pelo menos:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Existem dois conceitos diferentes aqui:

```text
AF_INET
   ↓
família de endereços

SOCK_STREAM
   ↓
tipo de socket
```

Eles não significam a mesma coisa.

### `AF_INET`

Indica que estamos trabalhando com endereçamento IPv4.

### `SOCK_STREAM`

Indica um socket orientado a fluxo.

Em sua utilização mais comum:

```text
AF_INET + SOCK_STREAM
        ↓
      IPv4
        +
       TCP
```

Já:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

normalmente representa:

```text
IPv4
 +
UDP
```

Essa distinção será aprofundada nas próximas partes.

---

# 1.16 Loopback e `127.0.0.1`

Um endereço muito importante durante o aprendizado de sockets é:

```text
127.0.0.1
```

Esse endereço pertence ao espaço de endereçamento **loopback** do IPv4.

Ele representa a própria máquina através da interface lógica de loopback.

Por isso:

```python
server.bind(("127.0.0.1", 4444))
```

significa, conceitualmente:

> "Associe este socket ao endereço loopback IPv4 na porta 4444."

Um programa na própria máquina pode então tentar:

```python
client.connect(("127.0.0.1", 4444))
```

O tráfego permanece local ao host.

Podemos representar:

```text
┌───────────────────────────────────────┐
│             MESMA MÁQUINA             │
│                                       │
│  Cliente                               │
│     │                                 │
│     │ 127.0.0.1:porta                 │
│     ↓                                 │
│  Loopback                             │
│     │                                 │
│     ↓                                 │
│  Servidor                             │
│     │                                 │
│     └── 127.0.0.1:4444                │
│                                       │
└───────────────────────────────────────┘
```

Isso é extremamente útil para:

- aprendizado;
    
- desenvolvimento;
    
- testes;
    
- laboratórios;
    
- testes de aplicações;
    
- exercícios de redes;
    
- testes de segurança em ambiente local.
    

---

# 1.17 `127.0.0.1` não é a mesma coisa que `0.0.0.0`

Essa diferença será importante posteriormente.

### `127.0.0.1`

Representa o loopback IPv4.

Um servidor associado especificamente a:

```python
server.bind(("127.0.0.1", 4444))
```

normalmente aceitará conexões destinadas ao loopback daquele host, não conexões externas destinadas às interfaces de rede físicas.

### `0.0.0.0`

Em um `bind()` IPv4, representa o endereço curinga (**wildcard address**).

Por exemplo:

```python
server.bind(("0.0.0.0", 4444))
```

significa, de forma simplificada:

> "Associe a porta 4444 às interfaces IPv4 locais apropriadas."

Isso pode permitir que o serviço receba conexões através de outras interfaces, dependendo da configuração do sistema e da rede.

Importante:

```text
0.0.0.0
```

em `bind()` **não significa um host remoto chamado `0.0.0.0`**.

É um endereço especial utilizado para indicar um conjunto de endereços locais.

---

# 1.18 Socket local e socket remoto

Durante uma comunicação podemos falar em:

```text
endereço local
```

e:

```text
endereço remoto
```

Imagine:

```text
Cliente
192.168.1.20:53142
       │
       │
       ↓
Servidor
192.168.1.10:4444
```

No cliente:

```text
local  = 192.168.1.20:53142
remoto = 192.168.1.10:4444
```

No servidor:

```text
local  = 192.168.1.10:4444
remoto = 192.168.1.20:53142
```

Os mesmos quatro valores estão envolvidos, mas a perspectiva é diferente.

Posteriormente podemos consultar essas informações diretamente através de:

```python
socket.getsockname()
```

e:

```python
socket.getpeername()
```

Esses métodos serão estudados em profundidade mais adiante.

---

# 1.19 Visão completa até aqui

Podemos juntar os conceitos:

```text
                 APLICAÇÃO
                     │
                     │ Python
                     ↓
              ┌──────────────┐
              │    socket    │
              └──────┬───────┘
                     │
                     │ file descriptor
                     ↓
              ┌──────────────┐
              │    Kernel    │
              └──────┬───────┘
                     │
             ┌───────┴────────┐
             ↓                ↓
            TCP              UDP
             │                │
             └───────┬────────┘
                     ↓
                    IP
                     ↓
             Interface de rede
                     ↓
                    Rede
                     ↓
              Outro endpoint
```

E, em uma comunicação TCP típica:

```text
Cliente                                  Servidor
   │                                        │
   │ socket()                               │ socket()
   │                                        │
   │ connect()                              │ bind()
   │                                        │ listen()
   │                                        │
   │────────── conexão TCP ────────────────→│
   │                                        │ accept()
   │                                        │
   │──────────── dados ───────────────────→│
   │←─────────── dados ────────────────────│
   │                                        │
   │ close()                                │ close()
```

Essa visão será a base para compreender todas as operações que veremos posteriormente.

---

# Resumo

Até aqui, os conceitos fundamentais são:

|Conceito|Significado|
|---|---|
|**Socket**|Interface de comunicação disponibilizada pelo sistema operacional|
|**Endpoint**|Ponto final de comunicação|
|**IP**|Endereçamento na camada IP|
|**Porta**|Identificador utilizado pelo transporte para multiplexar comunicação|
|**IP:porta**|Forma comum de representar um endpoint de transporte|
|**Socket local**|Endpoint associado ao próprio lado da comunicação|
|**Socket remoto**|Endpoint do peer|
|**File descriptor**|Identificador utilizado pelo processo para referenciar recursos do SO|
|**Cliente**|Normalmente inicia a conexão|
|**Servidor**|Normalmente aguarda conexões|
|**`AF_INET`**|Família de endereços IPv4|
|**`SOCK_STREAM`**|Tipo de socket orientado a fluxo|
|**`127.0.0.1`**|Endereço loopback IPv4|
|**`0.0.0.0`**|Endereço curinga usado, entre outros contextos, para `bind()`|
|**Kernel**|Gerencia recursos e participa da comunicação de rede|

A ideia central é:

```text
Python
  ↓
socket
  ↓
file descriptor
  ↓
kernel
  ↓
pilha de rede
  ↓
TCP/UDP
  ↓
IP
  ↓
interface de rede
  ↓
rede
```

O socket é, portanto, a ponte entre o **código da aplicação** e os mecanismos de comunicação fornecidos pelo **sistema operacional**.

---
# 📜 Família de endereços

---

# 2. Família de endereços

Ao criar um socket, um dos primeiros parâmetros que precisamos definir é a **família de endereços**.

Em Python:

```python
socket.socket(
    family=...,
    type=...
)
```

O parâmetro `family` determina **como os endereços utilizados pelo socket serão representados e interpretados**.

Isso é diferente do parâmetro `type`, que veremos posteriormente.

Uma forma simples de separar os dois conceitos é:

```text
family
   ↓
"Que tipo de sistema de endereçamento estou usando?"

type
   ↓
"Que tipo de comunicação o socket representa?"
```

Por exemplo:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

significa, conceitualmente:

```text
AF_INET
   ↓
endereçamento IPv4

SOCK_STREAM
   ↓
socket orientado a fluxo
```

---

## 2.1 O que é uma família de endereços?

Uma família de endereços define a estrutura e o formato dos endereços que o socket utilizará.

Entre as famílias mais importantes para quem trabalha com Python, Linux e redes estão:

```python
socket.AF_INET
socket.AF_INET6
socket.AF_UNIX
```

Podemos visualizar:

```text
Famílias de endereços

AF_INET
   ↓
IPv4

AF_INET6
   ↓
IPv6

AF_UNIX
   ↓
Unix Domain Socket
   ↓
comunicação local entre processos
```

Essas famílias resolvem problemas diferentes.

---

# 2.2 `AF_INET` — IPv4

`AF_INET` representa a família de endereços **IPv4**.

Exemplo:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Nesse caso, o socket utilizará endereços IPv4.

Um endereço IPv4 pode ser representado, por exemplo, como:

```text
192.168.1.10
```

Quando combinado com uma porta:

```text
192.168.1.10:4444
```

temos a representação comum de um endpoint IPv4 TCP.

Para `AF_INET`, o endereço normalmente aparece em Python como uma tupla:

```python
("192.168.1.10", 4444)
```

Por exemplo:

```python
server.bind(("127.0.0.1", 4444))
```

Aqui:

```text
127.0.0.1
   ↓
endereço IPv4

4444
   ↓
porta
```

---

# 2.3 Estrutura de um endereço IPv4

IPv4 utiliza endereços de **32 bits**.

Esses 32 bits normalmente são escritos em quatro grupos de 8 bits:

```text
8 bits . 8 bits . 8 bits . 8 bits
```

Por exemplo:

```text
192.168.1.10
```

Cada número decimal representa um octeto.

```text
192 → 8 bits
168 → 8 bits
1   → 8 bits
10  → 8 bits

Total = 32 bits
```

Uma representação binária aproximada seria:

```text
192       168       1         10
↓         ↓         ↓         ↓
11000000  10101000  00000001  00001010
```

O socket Python não exige que escrevamos esses bits manualmente.

Podemos simplesmente utilizar:

```python
("192.168.1.10", 4444)
```

A conversão e manipulação necessárias são tratadas pelas camadas inferiores.

---

# 2.4 `127.0.0.1` dentro de `AF_INET`

O endereço:

```text
127.0.0.1
```

é um endereço IPv4 de **loopback**.

Quando usamos:

```python
server.bind(("127.0.0.1", 4444))
```

estamos associando o socket ao loopback IPv4.

Um cliente na mesma máquina pode fazer:

```python
client.connect(("127.0.0.1", 4444))
```

O caminho conceitual é:

```text
┌─────────────────────────────┐
│          Máquina            │
│                             │
│  Cliente                    │
│     │                       │
│     │ 127.0.0.1:4444        │
│     ↓                       │
│  Loopback                   │
│     ↓                       │
│  Servidor                   │
│                             │
└─────────────────────────────┘
```

Isso é extremamente útil para laboratórios.

Por exemplo, podemos testar:

- servidores TCP;
    
- clientes TCP;
    
- chats;
    
- protocolos próprios;
    
- scanners;
    
- testes de parsing;
    
- autenticação;
    
- TLS;
    
- tratamento de erros;
    

sem precisar expor o serviço à rede externa.

---

# 2.5 `AF_INET6` — IPv6

`AF_INET6` representa a família de endereços **IPv6**.

Exemplo:

```python
import socket

server = socket.socket(
    socket.AF_INET6,
    socket.SOCK_STREAM
)
```

IPv6 utiliza endereços de **128 bits**.

Por isso, sua representação é muito maior que IPv4.

Exemplo:

```text
2001:db8::10
```

Um endereço IPv6 pode conter vários grupos hexadecimais separados por `:`.

Por exemplo:

```text
2001:0db8:0000:0000:0000:0000:0000:0010
```

pode ser abreviado para:

```text
2001:db8::10
```

A abreviação `::` representa uma sequência de grupos consecutivos de zeros.

---

# 2.6 Loopback IPv6

Assim como IPv4 possui:

```text
127.0.0.1
```

IPv6 possui:

```text
::1
```

Portanto:

```python
("127.0.0.1", 4444)
```

é um endereço IPv4.

Enquanto:

```python
("::1", 4444)
```

é um endereço IPv6.

Podemos comparar:

|IPv4|IPv6|
|---|---|
|`AF_INET`|`AF_INET6`|
|`127.0.0.1`|`::1`|
|32 bits|128 bits|
|endereço separado por `.`|endereço separado por `:`|
|`("127.0.0.1", 4444)`|`("::1", 4444)`|

---

# 2.7 Por que `127.0.0.1` e `::1` são diferentes?

Apesar de ambos representarem o conceito de **loopback**, pertencem a famílias de endereçamento diferentes.

```text
127.0.0.1
    ↓
IPv4
    ↓
AF_INET
```

Enquanto:

```text
::1
  ↓
IPv6
  ↓
AF_INET6
```

Portanto, um socket criado como:

```python
socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

não deve ser tratado simplesmente como se fosse um socket IPv6.

E vice-versa.

A família determina como o endereço será interpretado.

---

# 2.8 `AF_UNIX` — Unix Domain Socket

Agora entramos em um conceito muito importante para Linux.

`AF_UNIX` representa **Unix Domain Sockets**.

Também pode aparecer como:

```python
socket.AF_LOCAL
```

dependendo da plataforma.

Diferentemente de `AF_INET` e `AF_INET6`, o objetivo principal aqui não é comunicação entre hosts através de IP.

O objetivo é permitir comunicação entre processos no **mesmo sistema operacional**.

Por exemplo:

```text
Processo A
    │
    │ Unix Domain Socket
    ↓
Processo B
```

Não precisamos necessariamente utilizar:

```text
IP
porta TCP
roteamento IP
Ethernet
Wi-Fi
```

A comunicação acontece através dos mecanismos locais fornecidos pelo sistema operacional.

---

# 2.9 Unix Domain Socket e arquivos

Uma característica interessante é que Unix Domain Sockets podem ser associados a um caminho no sistema de arquivos.

Por exemplo:

```text
/tmp/meu_socket
```

Um servidor pode criar:

```python
server.bind("/tmp/meu_socket")
```

e um cliente pode conectar:

```python
client.connect("/tmp/meu_socket")
```

Observe uma diferença importante:

IPv4:

```python
server.bind(("127.0.0.1", 4444))
```

Unix Domain Socket:

```python
server.bind("/tmp/meu_socket")
```

A estrutura do endereço é diferente porque a família é diferente.

---

# 2.10 Quando utilizar `AF_UNIX`?

Unix Domain Sockets são interessantes quando:

- cliente e servidor estão no mesmo host;
    
- queremos comunicação entre processos;
    
- não precisamos de comunicação IP;
    
- queremos utilizar mecanismos de controle de acesso do sistema de arquivos;
    
- queremos evitar a pilha IP quando uma comunicação local é suficiente.
    

Um exemplo comum é a comunicação entre componentes de uma aplicação.

Imagine:

```text
Aplicação Web
     │
     │ Unix Socket
     ↓
Servidor local
```

Em vez de:

```text
Aplicação Web
     │
     │ TCP/IP
     ↓
127.0.0.1:8000
```

pode existir:

```text
/tmp/app.sock
```

---

# 2.11 Unix Socket não significa "socket menos poderoso"

É importante não pensar:

```text
AF_INET
   ↓
rede

AF_UNIX
   ↓
"arquivo comum"
```

Isso seria incorreto.

Unix Domain Socket continua sendo um socket.

Ele apenas utiliza outro mecanismo de endereçamento e comunicação.

Podemos ter:

```text
AF_INET
    ↓
comunicação através de IPv4

AF_INET6
    ↓
comunicação através de IPv6

AF_UNIX
    ↓
comunicação local entre processos
```

---

# 2.12 Comparação entre as principais famílias

|Família|Comunicação|Endereço típico|Uso|
|---|---|---|---|
|`AF_INET`|IPv4|`("127.0.0.1", 4444)`|Redes IPv4|
|`AF_INET6`|IPv6|`("::1", 4444)`|Redes IPv6|
|`AF_UNIX`|Local|`"/tmp/app.sock"`|Processos no mesmo host|

Podemos pensar:

```text
                 SOCKET
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
   AF_INET      AF_INET6      AF_UNIX
       │            │            │
      IPv4         IPv6       comunicação
                              local
```

---

# 2.13 Outras famílias

Python expõe diversas constantes de famílias de endereços, e a disponibilidade exata pode variar conforme o sistema operacional.

Além das três mais importantes para nosso estudo:

```python
socket.AF_INET
socket.AF_INET6
socket.AF_UNIX
```

existem famílias relacionadas a tecnologias e mecanismos específicos.

Por exemplo, em determinados sistemas podem existir famílias relacionadas a:

- Bluetooth;
    
- Netlink;
    
- packet sockets;
    
- protocolos específicos do sistema;
    
- outras formas de comunicação local ou de rede.
    

A disponibilidade não deve ser presumida de forma universal.

Podemos consultar uma instalação Python:

```python
import socket

print(socket.AF_INET)
print(socket.AF_INET6)
print(socket.AF_UNIX)
```

E também consultar os atributos disponíveis:

```python
import socket

print(dir(socket))
```

Entretanto, `dir(socket)` mostra uma grande quantidade de constantes, classes e funções, portanto não deve ser utilizado como substituto de documentação técnica.

---

# 2.14 Família de endereço ≠ protocolo

Esse é um erro conceitual bastante comum.

Considere:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Não devemos interpretar:

```text
AF_INET = TCP
```

Isso está errado.

O correto é:

```text
AF_INET
   ↓
família de endereçamento
   ↓
IPv4
```

e:

```text
SOCK_STREAM
   ↓
tipo de socket
   ↓
fluxo de bytes
   ↓
normalmente TCP
```

Da mesma forma:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

representa normalmente:

```text
AF_INET
   ↓
IPv4

SOCK_DGRAM
   ↓
datagramas
   ↓
normalmente UDP
```

Essa separação ficará ainda mais importante quando estudarmos `SOCK_RAW`, protocolos e o parâmetro `proto`.

---

# 2.15 Endereço Python depende da família

Observe:

```python
("127.0.0.1", 4444)
```

e:

```python
("::1", 4444)
```

Eles possuem uma estrutura semelhante:

```text
(host, port)
```

mas representam famílias diferentes.

No IPv4:

```python
host = "127.0.0.1"
port = 4444
```

No IPv6:

```python
host = "::1"
port = 4444
```

O Python e o sistema operacional sabem interpretar o endereço de acordo com a família do socket.

---

# 2.16 Uma diferença importante no IPv6

IPv6 também possui informações adicionais que podem aparecer em endereços de socket, especialmente relacionadas a **escopo/interface**.

Isso é particularmente importante para endereços IPv6 _link-local_, como:

```text
fe80::...
```

Nesses casos, pode ser necessário especificar uma interface de rede.

Por isso, endereços IPv6 podem aparecer em Python com estruturas mais complexas que simplesmente:

```python
(host, port)
```

Em determinadas operações, o formato pode incluir:

```text
(host, port, flowinfo, scopeid)
```

Por exemplo, conceitualmente:

```python
("fe80::1234", 4444, 0, 2)
```

Os campos adicionais são relevantes para determinadas situações IPv6.

Não devemos assumir que todo endereço IPv6 será sempre representado apenas por dois valores.

---

# 2.17 Como escolher a família?

A escolha depende do ambiente e do objetivo.

### IPv4

Utilize:

```python
socket.AF_INET
```

quando:

- o serviço utiliza IPv4;
    
- o laboratório utiliza IPv4;
    
- você está aprendendo conceitos básicos de sockets;
    
- precisa explicitamente de um endpoint IPv4.
    

### IPv6

Utilize:

```python
socket.AF_INET6
```

quando:

- o serviço utiliza IPv6;
    
- deseja testar compatibilidade IPv6;
    
- precisa trabalhar com endereços IPv6;
    
- está desenvolvendo uma aplicação que precisa suportar IPv6 explicitamente.
    

### Unix Domain Socket

Utilize:

```python
socket.AF_UNIX
```

quando:

- os processos estão no mesmo host;
    
- não é necessário utilizar IP;
    
- comunicação local entre processos é suficiente.
    

---

# 2.18 Uma aplicação pode suportar IPv4 e IPv6?

Sim.

Existem várias estratégias para isso.

Uma aplicação pode:

```text
Socket IPv4
    +
Socket IPv6
```

ou utilizar mecanismos de dual stack dependendo do sistema operacional e da configuração do socket.

Por exemplo:

```text
                Aplicação
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
      IPv4 socket         IPv6 socket
          │                   │
       AF_INET            AF_INET6
```

Isso será aprofundado quando estudarmos IPv6 e `getaddrinfo()`.

É importante não assumir que:

```python
AF_INET6
```

automaticamente significa:

> "Esse socket sempre aceitará IPv4 e IPv6."

O comportamento de dual stack depende da configuração e do sistema operacional.

---

# 2.19 Relação com Linux

No Linux, a família escolhida influencia diretamente o tipo de socket que o kernel cria e como o endereço será tratado.

Podemos pensar:

```text
Python
   ↓
socket(AF_INET, ...)
   ↓
kernel
   ↓
socket IPv4
```

ou:

```text
Python
   ↓
socket(AF_INET6, ...)
   ↓
kernel
   ↓
socket IPv6
```

ou:

```text
Python
   ↓
socket(AF_UNIX, ...)
   ↓
kernel
   ↓
Unix Domain Socket
```

Isso mostra novamente que o objeto Python é uma interface para um recurso gerenciado pelo sistema operacional.

---

# 2.20 Observação de segurança

A família de endereços também possui implicações de segurança.

Por exemplo:

```python
server.bind(("127.0.0.1", 4444))
```

normalmente restringe o serviço ao próprio host.

Já:

```python
server.bind(("0.0.0.0", 4444))
```

pode fazer com que o serviço fique acessível através das interfaces IPv4 da máquina.

Isso muda significativamente a superfície de exposição.

Por isso, durante desenvolvimento e laboratório, frequentemente é preferível começar com:

```python
127.0.0.1
```

em vez de:

```python
0.0.0.0
```

quando não existe necessidade de acesso externo.

O mesmo princípio deve ser analisado para IPv6.

Um serviço pode estar corretamente limitado em IPv4 e, dependendo da configuração, ainda possuir exposição através de IPv6.

Essa é uma questão importante em auditorias e hardening de serviços.

---

# 2.21 Modelo mental final da família de endereços

Podemos resumir o conceito desta seção assim:

```text
                    socket()
                       │
                       ↓
              ┌─────────────────┐
              │     family      │
              └────────┬────────┘
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
      AF_INET      AF_INET6      AF_UNIX
          │            │            │
        IPv4          IPv6        local
          │            │            │
      127.0.0.1       ::1       /tmp/app.sock
          │            │            │
          └────────────┼────────────┘
                       ↓
                endereço do socket
```

O ponto fundamental é:

> **A família define o modelo de endereçamento utilizado pelo socket.**

Ela não define sozinha:

- TCP;
    
- UDP;
    
- confiabilidade;
    
- conexão;
    
- fluxo de bytes;
    
- datagramas.
    

Essas características estão relacionadas principalmente ao **tipo de socket** e ao protocolo utilizado.

Essa será a próxima etapa.

---

# Resumo da seção

- `AF_INET` representa IPv4.
    
- `AF_INET6` representa IPv6.
    
- `AF_UNIX` representa Unix Domain Sockets.
    
- `127.0.0.1` é loopback IPv4.
    
- `::1` é loopback IPv6.
    
- `IP:porta` é uma representação comum de um endpoint de transporte.
    
- `AF_INET` não significa TCP.
    
- `AF_INET6` não significa UDP ou TCP por si só.
    
- `AF_UNIX` permite comunicação local entre processos.
    
- A família de endereços influencia a estrutura do endereço passado para métodos como `bind()` e `connect()`.
    
- IPv6 pode envolver informações adicionais como `scopeid`.
    
- Dual stack não deve ser presumido automaticamente.
    
- A escolha do endereço de `bind()` influencia a exposição do serviço.
    
- `127.0.0.1` é especialmente útil para laboratórios e desenvolvimento local.


---
# 3. Tipos de sockets

Agora que entendemos as **famílias de endereços**, precisamos entender uma segunda característica fundamental de um socket: o seu **tipo**.

A família responde, de forma simplificada:

> **“Em que tipo de sistema de endereçamento esse socket vai operar?”**

Por exemplo:

```python
socket.AF_INET
```

indica IPv4.

Já o tipo responde:

> **“Qual é a semântica de comunicação que esse socket oferece?”**

Por exemplo:

```python
socket.SOCK_STREAM
```

indica uma comunicação orientada a fluxo de bytes.

Essa distinção é extremamente importante porque:

```python
socket.AF_INET
```

e:

```python
socket.SOCK_STREAM
```

não representam a mesma coisa.

Podemos pensar inicialmente assim:

```text
AF_INET
   ↓
IPv4

SOCK_STREAM
   ↓
fluxo de bytes
```

Uma combinação comum é:

```python
socket.AF_INET + socket.SOCK_STREAM
```

que normalmente resulta em um socket IPv4 usando TCP.

Outra combinação comum é:

```python
socket.AF_INET + socket.SOCK_DGRAM
```

que normalmente resulta em um socket IPv4 usando UDP.

Mas existe uma diferença importante:

> **Tipo de socket não deve ser tratado simplesmente como sinônimo do protocolo de transporte.**

O tipo define principalmente a **semântica da comunicação**. O protocolo efetivamente utilizado depende também da família, do parâmetro de protocolo e do suporte do sistema operacional.

---

## 3.1 `SOCK_STREAM`

O tipo:

```python
socket.SOCK_STREAM
```

representa uma comunicação baseada em **fluxo de bytes**.

É o tipo tradicionalmente utilizado com **TCP**.

Exemplo:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Podemos interpretar:

```text
AF_INET
   ↓
IPv4

SOCK_STREAM
   ↓
fluxo de bytes

combinação
   ↓
normalmente TCP sobre IPv4
```

---

## 3.2 O que significa "fluxo de bytes"?

Esse é um dos conceitos mais importantes de sockets.

Quando trabalhamos com:

```python
SOCK_STREAM
```

não estamos enviando necessariamente:

```text
mensagem 1
mensagem 2
mensagem 3
```

O socket trabalha com uma sequência contínua de bytes:

```text
[byte][byte][byte][byte][byte][byte]...
```

Por exemplo, imagine que uma aplicação queira enviar:

```text
"Olá mundo"
```

Isso pode ser convertido para bytes:

```python
b"Ol\xc3\xa1 mundo"
```

O receptor recebe bytes através de:

```python
recv()
```

Por exemplo:

```python
dados = sock.recv(1024)
```

O valor:

```python
1024
```

não significa:

> "receba exatamente uma mensagem de 1024 bytes."

Significa:

> "receba no máximo 1024 bytes nesta operação."

Essa diferença será extremamente importante quando estudarmos TCP em profundidade.

---

## 3.3 TCP não preserva fronteiras de mensagens

Suponha que o cliente faça:

```python
sock.sendall(b"OLA")
sock.sendall(b"MUNDO")
```

Seria um erro conceitual imaginar que o servidor obrigatoriamente receberá:

```text
OLA
MUNDO
```

em duas chamadas separadas de:

```python
recv()
```

O servidor poderia receber:

```text
OLAMUNDO
```

em uma única leitura.

Ou poderia receber:

```text
OLA
```

e depois:

```text
MUNDO
```

Ou até:

```text
OL
```

e depois:

```text
AMUNDO
```

A aplicação não pode assumir que cada chamada de `send()` ou `sendall()` corresponde a uma chamada equivalente de `recv()`.

Podemos representar:

```text
APLICAÇÃO CLIENTE

send("OLA")
send("MUNDO")
       │
       ▼
┌─────────────────────┐
│ fluxo TCP           │
│                     │
│ O L A M U N D O     │
└─────────────────────┘
       │
       ▼
APLICAÇÃO SERVIDOR

recv(...)
```

Isso acontece porque TCP fornece um **fluxo ordenado de bytes**, e não um sistema de mensagens.

Por isso, protocolos de aplicação que utilizam TCP normalmente precisam definir algum mecanismo para descobrir:

> **onde uma mensagem termina e a próxima começa?**

Algumas possibilidades são:

### Delimitador

```text
OLA\n
MUNDO\n
```

### Tamanho antes da mensagem

```text
[0005][HELLO]
[0005][WORLD]
```

### Estrutura fixa

Por exemplo:

```text
8 bytes → cabeçalho
N bytes → conteúdo
```

### Fechamento da conexão

Em determinados protocolos, o fim do fluxo pode indicar o fim dos dados.

Esse assunto será aprofundado quando estudarmos **TCP como byte stream** e construção de protocolos próprios.

---

## 3.4 `SOCK_DGRAM`

O segundo tipo importante é:

```python
socket.SOCK_DGRAM
```

`SOCK_DGRAM` representa comunicação baseada em **datagramas**.

O protocolo mais comum associado a ele é:

```text
UDP
```

Exemplo:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

Aqui temos:

```text
AF_INET
   ↓
IPv4

SOCK_DGRAM
   ↓
datagramas

combinação comum
   ↓
UDP sobre IPv4
```

A grande diferença para `SOCK_STREAM` é que datagramas possuem **fronteiras de mensagem**.

Por exemplo:

```python
sock.sendto(b"OLA", destino)
sock.sendto(b"MUNDO", destino)
```

O receptor utiliza:

```python
dados, origem = sock.recvfrom(1024)
```

e recebe datagramas individualmente.

Conceitualmente:

```text
CLIENTE

datagrama 1
┌──────────────┐
│     OLA      │
└──────────────┘

datagrama 2
┌──────────────┐
│    MUNDO     │
└──────────────┘
        │
        ▼
       UDP
        │
        ▼
SERVIDOR

recvfrom()
   ↓
"OLA"

recvfrom()
   ↓
"MUNDO"
```

Existe, portanto, uma diferença fundamental:

```text
SOCK_STREAM
    ↓
fluxo contínuo de bytes

SOCK_DGRAM
    ↓
unidades individuais de dados
```

---

## 3.5 Datagramas não significam confiabilidade

É importante não confundir:

> **preservação da fronteira da mensagem**

com:

> **entrega garantida**

UDP normalmente não oferece as garantias de entrega, ordenação e retransmissão que TCP oferece.

Imagine que uma aplicação envie:

```text
Datagrama A
Datagrama B
Datagrama C
```

O receptor pode receber:

```text
A
C
```

e não receber:

```text
B
```

Também pode ocorrer:

```text
C
A
```

dependendo do comportamento da rede.

A aplicação que utiliza UDP precisa lidar com essas características quando elas forem importantes.

Podemos resumir:

```text
TCP
 ├── confiabilidade
 ├── ordenação
 ├── retransmissão
 └── fluxo de bytes

UDP
 ├── datagramas
 ├── menor complexidade no transporte
 ├── não fornece as mesmas garantias de TCP
 └── preserva fronteiras dos datagramas
```

Isso não significa que UDP seja simplesmente "TCP pior".

São modelos diferentes.

UDP é útil justamente quando a aplicação deseja características diferentes das oferecidas pelo TCP.

---

## 3.6 `SOCK_STREAM` vs `SOCK_DGRAM`

Uma comparação simples:

|Característica|`SOCK_STREAM`|`SOCK_DGRAM`|
|---|---|---|
|Semântica|Fluxo|Datagramas|
|Protocolo comum|TCP|UDP|
|Fronteira de mensagem|Não|Sim|
|Ordenação garantida pelo transporte|Normalmente sim com TCP|Não|
|Retransmissão pelo transporte|TCP fornece|UDP não|
|Conexão TCP tradicional|Sim|Não possui conexão TCP|
|Uso típico|HTTP/1.1, SSH, chat TCP|DNS, streaming específico, jogos e aplicações que usam UDP|
|API comum|`send()` / `recv()`|`sendto()` / `recvfrom()`|

Existe uma observação importante sobre a palavra **conexão**.

Frequentemente dizemos:

```text
TCP = orientado à conexão
UDP = sem conexão
```

Isso é correto como descrição do modelo de transporte.

Porém, isso **não significa que um socket UDP jamais possa usar `connect()`**.

Um socket UDP pode fazer:

```python
sock.connect(("127.0.0.1", 9999))
```

Nesse caso, o sistema operacional associa aquele socket a um destino padrão.

Isso não transforma UDP em TCP.

Não ocorre uma conexão TCP, nem passa a existir a mesma confiabilidade do TCP.

Portanto:

```text
UDP + connect()
       ≠
TCP
```

O significado de `connect()` em UDP será estudado posteriormente.

---

## 3.7 `SOCK_RAW`

Outro tipo é:

```python
socket.SOCK_RAW
```

Raw sockets fornecem acesso muito mais baixo nível à comunicação de rede.

Em vez de trabalhar somente com a abstração tradicional de:

```text
aplicação
   ↓
TCP/UDP
```

um raw socket pode permitir que a aplicação interaja de forma mais direta com protocolos e cabeçalhos de rede, dependendo do sistema operacional e das permissões.

Conceitualmente:

```text
Aplicação
    │
    ▼
RAW SOCKET
    │
    ▼
camadas inferiores da rede
```

Isso é muito diferente de:

```python
socket.AF_INET, socket.SOCK_STREAM
```

que normalmente utilizamos para comunicação TCP convencional.

### Exemplo conceitual

Um programa utilizando raw sockets pode estar interessado em observar ou construir estruturas de protocolos de rede em um nível mais baixo.

Isso aparece em áreas como:

- análise de protocolos;
    
- ferramentas de diagnóstico;
    
- pesquisa de redes;
    
- captura e análise de pacotes;
    
- ferramentas de segurança;
    
- implementação experimental de protocolos;
    
- estudos de cabeçalhos IP/ICMP.
    

Porém, raw sockets possuem limitações importantes.

No Linux, determinadas operações exigem privilégios elevados, e o comportamento exato depende da família, protocolo e configuração do sistema.

Por isso, não devemos assumir:

```text
SOCK_RAW = posso fazer qualquer coisa na rede
```

Não é assim.

O sistema operacional continua controlando o acesso.

---

## 3.8 Raw socket e segurança

Raw sockets aparecem bastante em segurança porque permitem trabalhar próximo das camadas de rede.

Por exemplo, ferramentas de diagnóstico e pesquisa de protocolos podem precisar construir ou observar pacotes de forma mais direta.

Isso também explica por que operações com raw sockets podem exigir privilégios.

Uma abstração simplificada seria:

```text
SOCK_STREAM

Aplicação
   ↓
TCP
   ↓
IP
   ↓
Ethernet
```

Enquanto um raw socket pode permitir trabalhar em uma camada mais próxima de:

```text
Aplicação
   ↓
RAW SOCKET
   ↓
protocolo de rede
   ↓
interface de rede
```

O nível exato depende da família e do protocolo escolhido.

Quando estudarmos **raw sockets**, vamos analisar isso separadamente para não misturar o conceito com TCP e UDP.

---

## 3.9 `SOCK_SEQPACKET`

Outro tipo importante é:

```python
socket.SOCK_SEQPACKET
```

O nome pode parecer estranho inicialmente.

Podemos dividi-lo:

```text
SEQ
 ↓
sequenciado

PACKET
 ↓
pacotes/mensagens
```

A ideia é fornecer uma comunicação:

- orientada a conexão;
    
- confiável;
    
- ordenada;
    
- baseada em mensagens/records.
    

Isso é diferente de `SOCK_STREAM`.

Podemos visualizar:

```text
SOCK_STREAM

AAAAA BBBBB CCCCC
─────────────────
fluxo contínuo
```

Enquanto:

```text
SOCK_SEQPACKET

┌─────┐ ┌─────┐ ┌─────┐
│ AAA │ │ BBB │ │ CCC │
└─────┘ └─────┘ └─────┘
 mensagens preservadas
```

Portanto, `SOCK_SEQPACKET` combina características que podem ser muito interessantes:

```text
confiabilidade
      +
ordenação
      +
fronteiras de mensagem
```

Porém, existe uma consideração importante:

> **`SOCK_SEQPACKET` não deve ser tratado como simplesmente "TCP com mensagens".**

A disponibilidade e o protocolo associado dependem da família de endereços e do sistema operacional.

Em algumas famílias, como determinados usos de sockets locais, esse modelo é mais comum.

Portanto, não devemos assumir que:

```python
socket.AF_INET + socket.SOCK_SEQPACKET
```

terá necessariamente uma implementação disponível e equivalente em todos os sistemas.

---

## 3.10 Comparação geral dos tipos

Podemos montar um modelo mental:

```text
                    SOCKET TYPES
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
   SOCK_STREAM      SOCK_DGRAM       SOCK_RAW
        │                │                │
        ▼                ▼                ▼
   fluxo de bytes     datagramas      acesso baixo nível
        │                │
        ▼                ▼
      TCP*             UDP*
```

E:

```text
SOCK_SEQPACKET
       │
       ▼
mensagens ordenadas e confiáveis
```

Onde `*` significa:

> protocolo comumente associado, não uma equivalência absoluta do tipo de socket.

---

## 3.11 Tabela completa

|Tipo|Modelo|Fronteira de mensagem|Confiabilidade|Ordenação|Protocolo comum|
|---|---|--:|--:|--:|---|
|`SOCK_STREAM`|fluxo de bytes|Não|Sim, quando TCP|Sim, quando TCP|TCP|
|`SOCK_DGRAM`|datagramas|Sim|Não, quando UDP|Não, quando UDP|UDP|
|`SOCK_RAW`|acesso de baixo nível|Depende|Depende|Depende|IP/ICMP e outros usos|
|`SOCK_SEQPACKET`|mensagens sequenciadas|Sim|Sim, quando suportado|Sim|Depende da família/protocolo|

A tabela não deve ser interpretada como:

```text
SOCK_STREAM = TCP
SOCK_DGRAM = UDP
```

A maneira mais correta de pensar é:

```text
SOCK_STREAM
    ↓
semântica de fluxo

SOCK_DGRAM
    ↓
semântica de datagrama

SOCK_RAW
    ↓
acesso de baixo nível

SOCK_SEQPACKET
    ↓
semântica de mensagens sequenciadas
```

Depois entram:

```text
família
+
protocolo
+
sistema operacional
```

para determinar o comportamento concreto.

---

## 3.12 Família + tipo + protocolo

Agora podemos começar a montar uma visão mais completa da criação de sockets.

Um socket possui, conceitualmente:

```text
FAMÍLIA
   +
TIPO
   +
PROTOCOLO
```

Por exemplo:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Temos:

```text
AF_INET
   ↓
IPv4

SOCK_STREAM
   ↓
fluxo

resultado comum
   ↓
TCP/IPv4
```

Outro exemplo:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

Temos:

```text
AF_INET
   ↓
IPv4

SOCK_DGRAM
   ↓
datagrama

resultado comum
   ↓
UDP/IPv4
```

Isso já permite entender uma parte importante da assinatura:

```python
socket.socket(family, type, proto=0, fileno=None)
```

Ainda não vamos aprofundar todos esses parâmetros aqui.

O objetivo desta parte é construir o modelo mental:

```text
family
  =
onde / qual sistema de endereçamento

type
  =
como os dados são tratados

proto
  =
qual protocolo específico
```

Esse modelo será utilizado quando estudarmos `socket.socket()` em detalhes.

---

## 3.13 Um erro conceitual muito comum

Um erro frequente de quem começa com sockets é pensar:

```text
SOCK_STREAM = conexão
SOCK_DGRAM = sem conexão
```

Isso é uma simplificação excessiva.

O tipo define a **semântica do socket**.

Por exemplo, `SOCK_DGRAM` trabalha com datagramas.

Mas um socket UDP pode utilizar:

```python
connect()
```

para definir um peer padrão.

Portanto:

```text
"socket conectado"
```

e:

```text
"protocolo orientado à conexão"
```

não são necessariamente a mesma coisa.

Da mesma forma:

```text
SOCK_STREAM
```

não deve ser entendido simplesmente como:

> "um socket TCP"

O mais correto é:

> `SOCK_STREAM` fornece uma semântica de fluxo de bytes e é tradicionalmente utilizado com TCP.

Essa precisão será importante quando chegarmos a outros protocolos e famílias.

---

## 3.14 Fluxo vs mensagem

Esse é provavelmente o conceito mais importante desta parte.

### Stream

```text
AAAAAAAAAABBBBBBBBBBCCCCCCCCCC
──────────────────────────────
             fluxo
```

Não existem divisões naturais de:

```text
AAAA
BBBB
CCCC
```

A aplicação precisa criar essas divisões.

### Datagram

```text
┌─────────┐
│ AAAAAAA │
└─────────┘

┌─────────┐
│ BBBBBBB │
└─────────┘

┌─────────┐
│ CCCCCCC │
└─────────┘
```

Cada unidade possui sua própria fronteira.

### Seqpacket

```text
┌─────────┐
│ AAAAAAA │
└─────────┘
      ↓
sequenciado

┌─────────┐
│ BBBBBBB │
└─────────┘
      ↓
sequenciado

┌─────────┐
│ CCCCCCC │
└─────────┘
```

Essa diferença aparentemente pequena muda completamente a maneira como uma aplicação precisa estruturar sua comunicação.

---

## 3.15 Exemplo mínimo: TCP

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

sock.connect(("127.0.0.1", 4444))

sock.sendall(b"OLA")

sock.close()
```

Aqui temos:

```text
AF_INET
   ↓
IPv4

SOCK_STREAM
   ↓
fluxo de bytes

127.0.0.1
   ↓
localhost

4444
   ↓
porta

sendall()
   ↓
envio de bytes
```

---

## 3.16 Exemplo mínimo: UDP

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

sock.sendto(
    b"OLA",
    ("127.0.0.1", 4444)
)

sock.close()
```

Agora:

```text
AF_INET
   ↓
IPv4

SOCK_DGRAM
   ↓
datagrama

sendto()
   ↓
dados + destino
```

Observe a diferença da API:

### TCP

```python
sock.sendall(b"OLA")
```

Depois que o socket está conectado, o destino já está associado à comunicação.

### UDP

```python
sock.sendto(
    b"OLA",
    ("127.0.0.1", 4444)
)
```

O destino pode ser especificado diretamente no envio.

Essa diferença será explorada posteriormente quando estudarmos:

```text
connect()
send()
sendall()
sendto()
recv()
recvfrom()
```

---

## 3.17 O modelo mental definitivo desta parte

Ao encontrar:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

não pense simplesmente:

> "isso cria um TCP."

Pense em camadas:

```text
socket.socket()
       │
       ├── AF_INET
       │      ↓
       │    IPv4
       │
       ├── SOCK_STREAM
       │      ↓
       │    fluxo de bytes
       │
       └── protocolo
              ↓
        normalmente TCP
```

E ao encontrar:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

pense:

```text
socket.socket()
       │
       ├── AF_INET
       │      ↓
       │    IPv4
       │
       ├── SOCK_DGRAM
       │      ↓
       │    datagramas
       │
       └── protocolo
              ↓
        normalmente UDP
```

Esse modelo evita uma enorme quantidade de confusão posteriormente.

---

## Resumo da Parte

### `SOCK_STREAM`

Representa uma comunicação baseada em **fluxo de bytes**.

É normalmente utilizado com TCP.

```python
socket.AF_INET
socket.SOCK_STREAM
```

→ normalmente TCP sobre IPv4.

---

### `SOCK_DGRAM`

Representa comunicação baseada em **datagramas**.

É normalmente utilizado com UDP.

```python
socket.AF_INET
socket.SOCK_DGRAM
```

→ normalmente UDP sobre IPv4.

---

### `SOCK_RAW`

Permite acesso mais baixo nível à comunicação de rede, dependendo da família, protocolo e permissões do sistema operacional.

É importante em:

- análise de protocolos;
    
- diagnóstico;
    
- pesquisa de redes;
    
- segurança;
    
- ferramentas de baixo nível.
    

---

### `SOCK_SEQPACKET`

Representa uma semântica orientada a mensagens, sequenciada e confiável, quando suportada pela combinação de família/protocolo/sistema operacional.

Não deve ser tratado simplesmente como:

```text
TCP + mensagens
```

---

### A diferença central

```text
SOCK_STREAM
    ↓
fluxo de bytes

SOCK_DGRAM
    ↓
datagramas

SOCK_RAW
    ↓
acesso de baixo nível

SOCK_SEQPACKET
    ↓
mensagens sequenciadas
```

E a criação de um socket deve ser entendida como uma combinação:

```text
FAMÍLIA
   +
TIPO
   +
PROTOCOLO
```

Por exemplo:

```text
AF_INET + SOCK_STREAM
        ↓
      IPv4/TCP
```

ou:

```text
AF_INET + SOCK_DGRAM
        ↓
      IPv4/UDP
```

---
# 4. Criando um socket com `socket.socket()`

Depois de entender **família de endereços**, **tipo de socket** e a relação entre `SOCK_STREAM`, TCP, `SOCK_DGRAM`, UDP etc., podemos finalmente criar um socket em Python.

A criação é feita através da função:

```python
socket.socket()
```

Essa função cria um **objeto socket** que representa, dentro do programa Python, uma interface de comunicação disponibilizada pelo sistema operacional.

O modelo básico é:

```python
import socket

sock = socket.socket()
```

Nesse momento, ainda **não existe uma conexão com outro computador**.

O que foi criado foi apenas o objeto socket e os recursos correspondentes no sistema operacional.

Podemos pensar no processo desta forma:

```text
Python
   ↓
socket.socket()
   ↓
Objeto socket
   ↓
Recurso de comunicação no kernel
```

Somente depois outras operações poderão definir como esse socket será utilizado.

Por exemplo, em um servidor TCP:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
   ↓
recv() / send()
```

Enquanto em um cliente TCP:

```text
socket()
   ↓
connect()
   ↓
recv() / send()
```

Portanto, `socket.socket()` é normalmente o **primeiro passo** da criação de uma comunicação baseada em sockets.

---

## 4.1 Sintaxe de `socket.socket()`

A assinatura da função é:

```python
socket.socket(family=-1, type=SOCK_STREAM, proto=0, fileno=None)
```

Podemos dividir os parâmetros em quatro partes:

```text
socket.socket(
    family,
    type,
    proto,
    fileno
)
```

Cada parâmetro possui uma finalidade diferente.

|Parâmetro|Tipo|Obrigatório|Padrão|Função|
|---|---|---|---|---|
|`family`|constante inteira|Não|`AF_UNSPEC` / autodeterminado|Define a família de endereços|
|`type`|constante inteira|Não|`SOCK_STREAM`|Define o tipo de comunicação|
|`proto`|inteiro|Não|`0`|Define o protocolo específico|
|`fileno`|inteiro|Não|`None`|Permite criar um socket a partir de um descritor existente|

Na prática, durante o aprendizado, os parâmetros mais importantes são:

```python
family
type
```

Por exemplo:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Isso significa:

```text
AF_INET
   ↓
IPv4

SOCK_STREAM
   ↓
comunicação orientada a fluxo

Resultado
   ↓
socket TCP sobre IPv4
```

---

## 4.2 Importando o módulo `socket`

Antes de utilizar `socket.socket()`, precisamos importar o módulo:

```python
import socket
```

Depois podemos acessar a função através do módulo:

```python
socket.socket()
```

Exemplo:

```python
import socket

sock = socket.socket()
```

Aqui:

```python
socket
```

é o módulo Python.

Enquanto:

```python
socket.socket
```

é a classe utilizada para criar objetos socket.

E:

```python
socket.socket()
```

é a criação de uma instância dessa classe.

Podemos visualizar:

```text
import socket
      ↓
 módulo socket
      ↓
 socket.socket
      ↓
 classe socket
      ↓
 socket.socket()
      ↓
 objeto socket
```

---

## 4.3 Criando um socket TCP IPv4 explicitamente

Embora Python possua valores padrão, é importante aprender a escrever explicitamente a família e o tipo.

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Nesse caso:

```python
socket.AF_INET
```

define:

```text
IPv4
```

E:

```python
socket.SOCK_STREAM
```

define:

```text
fluxo de bytes
```

A combinação representa o uso tradicional de:

```text
IPv4 + TCP
```

Podemos representar:

```text
socket.socket(
    AF_INET,
    SOCK_STREAM
)

        ↓

      IPv4
        +
      TCP
```

Esse é o tipo de socket que será utilizado na maior parte dos exemplos de cliente e servidor TCP.

---

## 4.4 Criando um socket UDP IPv4

Para UDP, alteramos o tipo:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

Agora temos:

```text
AF_INET
   ↓
IPv4

SOCK_DGRAM
   ↓
Datagramas

Resultado
   ↓
UDP sobre IPv4
```

A diferença fundamental está no segundo parâmetro:

```python
socket.SOCK_STREAM
```

versus:

```python
socket.SOCK_DGRAM
```

Comparando:

```python
# TCP
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

```python
# UDP
socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

Portanto, a função `socket.socket()` não significa automaticamente TCP.

Quem define o comportamento do socket é principalmente a combinação entre:

```text
family + type + proto
```

---

## 4.5 O que realmente acontece quando `socket.socket()` é executado?

Quando fazemos:

```python
sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

não estamos simplesmente criando uma variável Python.

Existe uma interação entre:

```text
Programa Python
      ↓
Biblioteca socket
      ↓
Sistema operacional
      ↓
Kernel
```

O Python solicita ao sistema operacional a criação de um socket.

De forma simplificada:

```text
Python
  │
  │ socket.socket()
  ↓
Biblioteca socket
  │
  │ chamada ao sistema
  ↓
Kernel
  │
  │ cria recurso de socket
  ↓
Descritor de arquivo
  │
  ↓
Objeto socket Python
```

O sistema operacional passa a controlar o recurso de comunicação.

O Python recebe uma referência para esse recurso e fornece métodos para trabalhar com ele.

Por isso conseguimos fazer:

```python
sock.bind(...)
sock.listen(...)
sock.accept(...)
sock.connect(...)
sock.send(...)
sock.recv(...)
```

Esses métodos não são simplesmente funções que "fazem a rede sozinhas".

Eles são uma interface de alto nível para operações disponibilizadas pelo sistema operacional.

---

## 4.6 O objeto retornado por `socket.socket()`

Quando executamos:

```python
sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

a variável:

```python
sock
```

passa a armazenar um objeto da classe `socket.socket`.

Podemos verificar:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

print(type(sock))
```

O resultado será semelhante a:

```text
<class 'socket.socket'>
```

Portanto:

```python
sock
```

não é o IP.

Não é a porta.

Não é uma conexão.

Não é o servidor.

É o **objeto que representa o socket dentro do programa**.

Esse objeto fornece métodos para configurar e utilizar o socket.

Por exemplo:

```python
sock.bind(...)
sock.listen(...)
sock.accept(...)
sock.connect(...)
sock.send(...)
sock.recv(...)
sock.close()
```

---

## 4.7 Socket criado não significa socket conectado

Esse é um ponto extremamente importante.

Observe:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Nesse momento:

```text
socket criado
      ≠
socket conectado
```

Ainda não fizemos:

```python
sock.connect(...)
```

nem:

```python
sock.accept()
```

Também ainda não definimos necessariamente um endereço local através de:

```python
sock.bind(...)
```

Portanto, devemos separar os conceitos:

```text
socket.socket()
      ↓
cria o socket

bind()
      ↓
associa endereço local

listen()
      ↓
coloca socket TCP em modo de escuta

connect()
      ↓
solicita conexão com destino

accept()
      ↓
aceita uma conexão recebida
```

Cada operação possui uma responsabilidade diferente.

---

## 4.8 Criar, associar e conectar são coisas diferentes

Podemos representar o ciclo inicial de um socket TCP assim:

```text
1. socket()
      ↓
   cria o socket

2. bind()
      ↓
   associa IP + porta local

3. listen()
      ↓
   prepara para receber conexões

4. accept()
      ↓
   aceita uma conexão
```

Para um cliente:

```text
1. socket()
      ↓
   cria o socket

2. connect()
      ↓
   solicita conexão ao servidor
```

Essa separação é fundamental para entender sockets.

Por exemplo, este código:

```python
sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

não cria um servidor.

Ele apenas cria o socket.

Um servidor TCP começa a assumir comportamento de servidor quando realizamos operações como:

```python
sock.bind(...)
sock.listen(...)
```

e posteriormente:

```python
sock.accept()
```

---

## 4.9 O parâmetro `family`

O parâmetro `family` define a **família de endereços** utilizada pelo socket.

Exemplo:

```python
socket.AF_INET
```

representa IPv4.

Outro exemplo:

```python
socket.AF_INET6
```

representa IPv6.

Então:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

significa:

```text
Família:
IPv4

Tipo:
SOCK_STREAM
```

Enquanto:

```python
socket.socket(
    socket.AF_INET6,
    socket.SOCK_STREAM
)
```

significa:

```text
Família:
IPv6

Tipo:
SOCK_STREAM
```

A família influencia diretamente o formato dos endereços que serão utilizados posteriormente.

IPv4 normalmente trabalha com endereços como:

```text
192.168.1.10
```

IPv6 utiliza endereços como:

```text
2001:db8::1
```

Portanto:

```text
AF_INET
   ↓
estrutura de endereço IPv4

AF_INET6
   ↓
estrutura de endereço IPv6
```

---

## 4.10 O parâmetro `type`

O parâmetro `type` determina o tipo de socket.

Os mais importantes que já estudamos são:

```python
socket.SOCK_STREAM
```

e:

```python
socket.SOCK_DGRAM
```

Exemplo TCP:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Exemplo UDP:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

Portanto:

```text
family
   ↓
"qual família de endereços?"

type
   ↓
"qual modelo de comunicação?"
```

Essa distinção é importante porque `AF_INET` sozinho não significa TCP.

Por exemplo:

```python
socket.AF_INET + socket.SOCK_STREAM
```

resulta em uma combinação típica de:

```text
IPv4 + TCP
```

Enquanto:

```python
socket.AF_INET + socket.SOCK_DGRAM
```

resulta em:

```text
IPv4 + UDP
```

---

## 4.11 O parâmetro `proto`

O terceiro parâmetro é:

```python
proto
```

Ele permite especificar um protocolo específico.

Na maioria dos programas comuns, podemos utilizar:

```python
proto=0
```

ou simplesmente deixar o padrão.

Por exemplo:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM,
    0
)
```

Normalmente isso resulta na seleção automática do protocolo apropriado para a combinação utilizada.

No caso:

```text
AF_INET
+
SOCK_STREAM
+
proto=0
```

o sistema normalmente utiliza:

```text
TCP
```

Enquanto:

```text
AF_INET
+
SOCK_DGRAM
+
proto=0
```

normalmente utiliza:

```text
UDP
```

Por isso, em aplicações comuns, é muito frequente vermos:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

sem especificar `proto`.

---

## 4.12 Por que `proto=0` normalmente é suficiente?

Porque o sistema consegue determinar o protocolo adequado com base na combinação de família e tipo.

Podemos imaginar:

```text
AF_INET
   +
SOCK_STREAM
   +
proto=0
       ↓
   TCP
```

E:

```text
AF_INET
   +
SOCK_DGRAM
   +
proto=0
       ↓
   UDP
```

Isso não significa que `proto` seja inútil.

Existem situações mais específicas em que selecionar explicitamente um protocolo pode ser necessário.

Porém, para os sockets TCP e UDP tradicionais, normalmente não precisamos alterar esse parâmetro.

---

## 4.13 O parâmetro `fileno`

Existe ainda o parâmetro:

```python
fileno
```

Ele permite criar um objeto `socket` Python associado a um **descritor de arquivo já existente**.

Por exemplo, conceitualmente:

```python
socket.socket(fileno=fd)
```

onde:

```python
fd
```

é um descritor de arquivo válido.

Esse recurso é mais avançado e aparece principalmente quando estamos trabalhando diretamente com recursos do sistema operacional, integração com código de baixo nível ou manipulação de descritores existentes.

Para criar sockets normalmente, não precisamos utilizá-lo.

A forma tradicional continua sendo:

```python
sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Portanto, neste momento, podemos deixar:

```python
fileno=None
```

e trabalhar normalmente com `socket()`.

---

## 4.14 Criando e fechando um socket

Quando criamos um socket, também precisamos pensar no seu ciclo de vida.

Um exemplo mínimo:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

sock.close()
```

A primeira operação:

```python
socket.socket(...)
```

cria o socket.

A segunda:

```python
sock.close()
```

libera o recurso associado a ele.

Podemos representar:

```text
socket()
   ↓
recurso criado
   ↓
uso do socket
   ↓
close()
   ↓
recurso liberado
```

Isso é importante porque sockets são recursos do sistema operacional.

Não devemos pensar apenas na variável Python:

```python
sock
```

mas também no recurso que existe por trás dela.

---

## 4.15 Uma forma mais segura de trabalhar com sockets

Python também permite utilizar o socket com um gerenciador de contexto:

```python
import socket

with socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
) as sock:

    print("Socket criado")
```

Quando o bloco `with` termina, o socket é fechado automaticamente.

Conceitualmente:

```text
with
 ↓
cria socket
 ↓
utiliza socket
 ↓
fim do bloco
 ↓
close() automático
```

Isso ajuda a evitar situações em que o programa esquece de fechar o socket.

Para exemplos pequenos, também podemos utilizar:

```python
sock.close()
```

explicitamente.

---

## 4.16 Exemplo completo da criação de um socket TCP

Um exemplo simples:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

print("Socket criado!")

sock.close()

print("Socket fechado!")
```

Fluxo:

```text
import socket
      ↓
socket.socket()
      ↓
socket TCP/IPv4 criado
      ↓
uso do socket
      ↓
close()
      ↓
socket fechado
```

Observe que esse programa ainda **não envia dados pela rede**.

Também não cria um servidor.

Também não cria uma conexão com outro computador.

Ele apenas demonstra o ciclo básico:

```text
CRIAR
  ↓
UTILIZAR
  ↓
FECHAR
```

As próximas operações é que vão transformar esse socket em um participante real de uma comunicação de rede.

---

## 4.17 Modelo mental desta seção

Depois desta seção, podemos pensar em:

```python
sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

como:

```text
"Crie para mim um objeto socket
 capaz de trabalhar com endereços IPv4
 usando um modelo de comunicação orientado
 a fluxo."

                    ↓

              socket Python
                    ↓
             recurso no kernel
```

E ainda:

```text
socket criado
      ≠
socket conectado
      ≠
servidor
      ≠
cliente
```

O comportamento será definido pelas operações realizadas depois.

Para um servidor TCP:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
```

Para um cliente TCP:

```text
socket()
   ↓
connect()
```

Essa distinção será fundamental para entender `bind()`, `listen()`, `accept()` e `connect()`.

---

## Resumo

- `socket.socket()` cria um objeto socket em Python.
    
- Criar um socket **não significa estabelecer uma conexão**.
    
- `family` define a família de endereços.
    
- `type` define o tipo de comunicação.
    
- `proto` permite especificar o protocolo.
    
- `fileno` permite trabalhar com um descritor de arquivo existente.
    
- `AF_INET` representa IPv4.
    
- `AF_INET6` representa IPv6.
    
- `SOCK_STREAM` é utilizado normalmente com TCP.
    
- `SOCK_DGRAM` é utilizado normalmente com UDP.
    
- `proto=0` normalmente permite que o sistema escolha o protocolo adequado.
    
- `close()` libera o socket.
    
- `with socket.socket(...)` permite fechar o socket automaticamente.
    
- Um socket criado ainda não é necessariamente cliente ou servidor.
    
- Operações posteriores como `bind()`, `listen()`, `accept()` e `connect()` determinam seu papel no processo de comunicação.
    

---
# 5. Associando um socket a um endereço com `bind()`

Depois de criar um socket, precisamos entender como ele recebe um **endereço local**.

Essa etapa é especialmente importante quando estamos criando um **servidor**.

A função responsável por associar um socket a um endereço local é:

```python
bind()
```

Em Python:

```python
sock.bind(endereco)
```

Para IPv4, o endereço normalmente é representado por uma tupla:

```python
(ip, porta)
```

Por exemplo:

```python
sock.bind(("127.0.0.1", 4444))
```

Isso significa:

```text
IP:
127.0.0.1

Porta:
4444
```

Ou seja, estamos dizendo ao sistema operacional:

```text
"Este socket deve ser associado ao endereço
127.0.0.1:4444"
```

---

## 5.1 O que `bind()` realmente faz?

É importante não pensar que:

```python
sock.bind(("127.0.0.1", 4444))
```

"conecta" o programa a outro computador.

`bind()` possui outra responsabilidade.

Ele associa o socket a um **endereço local**.

Podemos representar:

```text
socket
   ↓
bind()
   ↓
IP local + porta local
```

Por exemplo:

```text
socket
   ↓
127.0.0.1:4444
```

Depois disso, o sistema operacional sabe que aquele socket está associado àquele endereço local.

---

## 5.2 `bind()` é usado principalmente em servidores

Em uma comunicação TCP tradicional, o servidor normalmente precisa informar:

```text
"Quero receber conexões neste endereço."
```

Por isso, o servidor normalmente executa:

```python
sock.bind(("127.0.0.1", 4444))
```

seguido de:

```python
sock.listen()
```

E posteriormente:

```python
sock.accept()
```

O fluxo fica:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
```

Enquanto um cliente normalmente faz:

```text
socket()
   ↓
connect()
```

Portanto:

```text
SERVIDOR

socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
```

```text
CLIENTE

socket()
   ↓
connect()
```

---

## 5.3 Sintaxe de `bind()`

A sintaxe básica é:

```python
sock.bind(endereco)
```

Para IPv4:

```python
sock.bind((ip, porta))
```

Exemplo:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

sock.bind(("127.0.0.1", 4444))
```

O segundo argumento de `bind()` é uma tupla:

```python
("127.0.0.1", 4444)
```

Essa tupla contém:

```text
        ("127.0.0.1", 4444)
               │       │
               │       └── porta
               └────────── IP
```

---

## 5.4 Por que o endereço é uma tupla?

No caso de IPv4, o endereço de um socket é representado em Python como:

```python
(host, port)
```

Por exemplo:

```python
("127.0.0.1", 4444)
```

Isso permite representar as duas informações necessárias:

```text
IP
+
PORTA
```

Portanto:

```python
sock.bind(("127.0.0.1", 4444))
```

é conceitualmente:

```text
bind(
    endereço local
)

endereço local:
    IP     = 127.0.0.1
    porta  = 4444
```

Essa representação será utilizada novamente em outras funções, como:

```python
connect()
```

---

## 5.5 O significado de `127.0.0.1`

O endereço:

```text
127.0.0.1
```

é um endereço de **loopback IPv4**.

Ele representa o próprio computador.

Quando fazemos:

```python
sock.bind(("127.0.0.1", 4444))
```

estamos dizendo que o socket deve aceitar comunicações destinadas ao loopback nessa porta.

Por exemplo:

```text
Programa A
   │
   │ TCP
   ↓
127.0.0.1:4444
   ↑
   │
Programa B
```

Os dois programas podem estar executando no mesmo computador.

Isso é muito útil para:

- testes;
    
- desenvolvimento;
    
- estudo de sockets;
    
- criação de servidores locais;
    
- comunicação entre processos;
    
- desenvolvimento de aplicações de rede.
    

---

## 5.6 `127.0.0.1` limita o acesso ao próprio computador

Considere:

```python
sock.bind(("127.0.0.1", 4444))
```

Esse servidor está associado ao loopback.

Assim, outro computador da mesma rede não poderá normalmente acessá-lo através do endereço de rede da máquina.

Por exemplo, suponha que o computador possua:

```text
192.168.1.20
```

Um dispositivo da rede tentando acessar:

```text
192.168.1.20:4444
```

não está acessando o mesmo endereço que:

```text
127.0.0.1:4444
```

São endereços diferentes.

Podemos visualizar:

```text
127.0.0.1
    ↓
próprio computador

192.168.1.20
    ↓
interface da rede local
```

---

## 5.7 `0.0.0.0` e `127.0.0.1`

Outro endereço muito importante é:

```text
0.0.0.0
```

Quando utilizado em `bind()`, ele possui um significado diferente de `127.0.0.1`.

Exemplo:

```python
sock.bind(("0.0.0.0", 4444))
```

Nesse contexto, estamos dizendo ao sistema operacional para associar o socket às interfaces IPv4 locais disponíveis, em vez de limitá-lo ao loopback.

Imagine uma máquina com:

```text
127.0.0.1
192.168.1.20
10.0.0.5
```

Um bind em:

```python
("127.0.0.1", 4444)
```

fica associado ao loopback.

Enquanto:

```python
("0.0.0.0", 4444)
```

pode aceitar conexões destinadas às interfaces IPv4 locais, conforme a configuração de rede e firewall.

Podemos pensar:

```text
127.0.0.1
    ↓
somente loopback
```

e:

```text
0.0.0.0
    ↓
todas as interfaces IPv4 locais
```

Isso não significa que `0.0.0.0` seja um IP que outro computador deve usar para conectar.

`0.0.0.0` nesse contexto é um endereço especial usado para indicar uma associação ampla das interfaces locais.

---

## 5.8 A porta utilizada por `bind()`

A porta identifica o ponto lógico onde o serviço estará disponível.

Exemplo:

```python
sock.bind(("127.0.0.1", 4444))
```

Aqui:

```text
IP:
127.0.0.1

PORTA:
4444
```

O endereço completo pode ser representado como:

```text
127.0.0.1:4444
```

Podemos pensar:

```text
IP
 ↓
qual máquina/interface?

PORTA
 ↓
qual serviço/processo?
```

A porta permite que o sistema operacional diferencie diferentes serviços utilizando a mesma máquina.

Por exemplo:

```text
127.0.0.1:80
127.0.0.1:443
127.0.0.1:22
127.0.0.1:4444
```

São endpoints diferentes.

---

## 5.9 Uma mesma porta não pode ser usada livremente por vários sockets

Um erro muito comum durante o desenvolvimento de servidores é tentar executar:

```python
sock.bind(("127.0.0.1", 4444))
```

quando outro socket já está utilizando aquele endereço e porta.

Nesse caso, podemos receber:

```text
OSError: [Errno 98] Address already in use
```

Por exemplo:

```text
Traceback (most recent call last):
  ...
OSError: [Errno 98] Address already in use
```

Isso significa que o sistema operacional não conseguiu associar o novo socket ao endereço solicitado porque aquele endereço já está sendo utilizado de forma incompatível por outro socket.

---

## 5.10 O que significa `Address already in use`?

Considere:

```text
127.0.0.1:4444
```

e um servidor já executando nesse endereço:

```text
Servidor A
    ↓
127.0.0.1:4444
```

Agora executamos outro programa:

```text
Servidor B
    ↓
127.0.0.1:4444
```

O sistema operacional precisa decidir qual socket deve receber os dados destinados àquele endpoint.

Não é possível simplesmente criar dois sockets comuns disputando exatamente o mesmo endereço local.

Por isso o segundo:

```python
bind(("127.0.0.1", 4444))
```

pode falhar.

---

## 5.11 Como verificar quem está usando a porta

No Linux, podemos utilizar:

```bash
ss -ltnp
```

Para procurar especificamente a porta `4444`:

```bash
ss -ltnp | grep :4444
```

Outra ferramenta útil é:

```bash
lsof -i :4444
```

Esses comandos podem mostrar qual processo está utilizando a porta.

Exemplo conceitual:

```text
LISTEN
127.0.0.1:4444
python3
```

Isso indica que existe um processo Python escutando nessa porta.

---

## 5.12 Encerrar o processo que está utilizando a porta

Depois de descobrir o processo, podemos encerrá-lo.

Por exemplo:

```bash
kill PID
```

onde:

```text
PID
```

é o identificador do processo.

Também podemos verificar processos Python com:

```bash
ps aux | grep python
```

Entretanto, devemos tomar cuidado ao encerrar processos.

Não é recomendado simplesmente matar processos aleatoriamente.

O correto é primeiro identificar:

```text
qual processo
        ↓
qual PID
        ↓
por que está usando a porta
```

---

## 5.13 `bind()` não inicia o servidor

Outro erro conceitual comum é pensar:

```python
sock.bind(("127.0.0.1", 4444))
```

e concluir:

```text
"Agora meu servidor está escutando."
```

Ainda não.

`bind()` apenas associa o socket ao endereço local.

Em TCP, normalmente ainda precisamos:

```python
sock.listen()
```

Portanto:

```text
socket()
   ↓
cria socket

bind()
   ↓
associa endereço

listen()
   ↓
começa a aceitar conexões pendentes
```

Essa diferença será muito importante na próxima etapa.

---

## 5.14 Exemplo mínimo com `bind()`

Podemos criar um socket TCP e associá-lo ao loopback:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

sock.bind(("127.0.0.1", 4444))

print("Socket associado a 127.0.0.1:4444")

sock.close()
```

O fluxo é:

```text
socket()
   ↓
TCP/IPv4 criado
   ↓
bind()
   ↓
127.0.0.1:4444
   ↓
close()
```

Observe que ainda não utilizamos:

```python
listen()
```

Portanto, esse código ainda não representa um servidor TCP completo.

---

## 5.15 `bind()` e o endereço local

É importante diferenciar **endereço local** de **endereço remoto**.

Quando fazemos:

```python
sock.bind(("127.0.0.1", 4444))
```

estamos definindo:

```text
ENDEREÇO LOCAL
127.0.0.1:4444
```

Ainda não estamos informando para qual computador queremos nos conectar.

Isso será responsabilidade de `connect()` em um cliente.

Podemos representar:

```text
Servidor:

LOCAL
127.0.0.1:4444
```

Enquanto um cliente poderia posteriormente fazer:

```python
sock.connect(("127.0.0.1", 4444))
```

Nesse caso:

```text
Cliente
  │
  │ connect()
  ↓
127.0.0.1:4444
  │
  ↓
Servidor
```

Portanto:

```text
bind()
   ↓
"Este socket está associado a este endereço local."

connect()
   ↓
"Quero estabelecer uma comunicação com este endereço remoto."
```

São operações diferentes.

---

## 5.16 O sistema operacional pode escolher a porta local

Nem sempre precisamos escolher manualmente a porta local de um socket.

Quando um cliente executa:

```python
sock.connect(("127.0.0.1", 4444))
```

sem realizar um `bind()` previamente, o sistema operacional normalmente pode escolher automaticamente uma porta local apropriada.

Por exemplo:

```text
Cliente
192.168.1.20:53142
       │
       │
       ↓
Servidor
192.168.1.10:4444
```

Nesse exemplo:

```text
53142
```

é uma porta local escolhida para o cliente.

Enquanto:

```text
4444
```

é a porta do servidor.

Isso nos mostra que uma conexão TCP possui mais informações do que apenas a porta do servidor.

Podemos representar:

```text
IP origem + porta origem
          ↓
192.168.1.20:53142

          │
          │ TCP
          ↓

IP destino + porta destino
          ↓
192.168.1.10:4444
```

Esse conceito será importante quando estudarmos conexões TCP e `accept()`.

---

## 5.17 Modelo mental de `bind()`

Podemos resumir `bind()` assim:

```text
socket()
   ↓
"Tenho um socket."
   ↓
bind()
   ↓
"Associe este socket a este endereço local."
   ↓
IP + porta
```

Por exemplo:

```python
sock.bind(("127.0.0.1", 4444))
```

significa:

```text
Socket
   ↓
endereço local
   ↓
127.0.0.1:4444
```

E não:

```text
bind()
   ↓
conecta ao servidor
```

Nem:

```text
bind()
   ↓
começa automaticamente a aceitar conexões
```

Nem:

```text
bind()
   ↓
envia dados
```

Cada operação possui sua própria responsabilidade.

---

## Resumo

- `bind()` associa um socket a um **endereço local**.
    
- Em IPv4, o endereço normalmente é representado por:
    
    ```python
    (ip, porta)
    ```
    
- Exemplo:
    
    ```python
    sock.bind(("127.0.0.1", 4444))
    ```
    
- `127.0.0.1` representa o loopback.
    
- `0.0.0.0` pode ser utilizado para associar o socket às interfaces IPv4 locais.
    
- `bind()` não cria uma conexão.
    
- `bind()` não inicia sozinho um servidor TCP.
    
- Em um servidor TCP, normalmente temos:
    
    ```text
    socket()
       ↓
    bind()
       ↓
    listen()
       ↓
    accept()
    ```
    
- Um erro como:
    
    ```text
    OSError: [Errno 98] Address already in use
    ```
    
    indica que o endereço solicitado já está sendo utilizado de forma incompatível.
    
- `ss -ltnp` e `lsof -i :4444` podem ajudar a identificar processos utilizando uma porta.
    
- Um cliente pode deixar o sistema operacional escolher automaticamente sua porta local.
    
- `bind()` trabalha com o **endereço local**, enquanto `connect()` será utilizado para estabelecer uma conexão com um **endereço remoto**.

---

# 6. Colocando o socket em modo de escuta com `listen()`

Depois de criar o socket e associá-lo a um endereço através de `bind()`, ainda falta uma etapa para transformar o socket em um socket capaz de **receber solicitações de conexão TCP**.

Essa etapa é realizada através de:

```python
listen()
```

Exemplo:

```python
sock.listen()
```

O fluxo básico de um servidor TCP passa a ser:

```text
socket()
   ↓
cria o socket
   ↓
bind()
   ↓
associa IP + porta
   ↓
listen()
   ↓
coloca o socket em modo de escuta
```

É importante entender que `listen()` é uma operação específica de sockets orientados a conexão, como os sockets TCP baseados em:

```python
socket.SOCK_STREAM
```

---

## 6.1 O que `listen()` faz?

A função `listen()` informa ao sistema operacional que o socket deve ser colocado em **modo de escuta para conexões de entrada**.

Exemplo:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))

server.listen()
```

Nesse ponto, o fluxo é:

```text
socket()
   ↓
socket TCP criado

bind()
   ↓
127.0.0.1:4444

listen()
   ↓
socket preparado para receber conexões
```

Podemos pensar em `listen()` como:

```text
"Este socket será usado para receber
solicitações de conexão."
```

---

## 6.2 `listen()` não aceita a conexão

Um detalhe muito importante:

```python
server.listen()
```

não significa:

```text
"aceite uma conexão agora"
```

Ele significa:

```text
"prepare este socket para receber conexões."
```

A operação que efetivamente aceita uma conexão é:

```python
server.accept()
```

Portanto:

```text
listen()
   ↓
prepara para receber conexões

accept()
   ↓
aceita uma conexão específica
```

Esse é um dos conceitos mais importantes do funcionamento de um servidor TCP.

---

## 6.3 O fluxo completo até `listen()`

Podemos visualizar:

```text
             SERVIDOR TCP

socket()
   │
   │ cria
   ↓
Socket TCP
   │
   │ bind()
   ↓
127.0.0.1:4444
   │
   │ listen()
   ↓
Socket em escuta
```

Somente depois disso teremos:

```python
server.accept()
```

para aceitar uma conexão recebida.

---

## 6.4 Sintaxe de `listen()`

A sintaxe é:

```python
socket.listen(backlog=-1)
```

Na prática, podemos utilizar simplesmente:

```python
server.listen()
```

ou:

```python
server.listen(5)
```

O parâmetro indica o tamanho desejado da fila de conexões pendentes.

Exemplo:

```python
server.listen(5)
```

Podemos interpretar conceitualmente como:

```text
"mantenha uma fila para conexões
 que chegaram e ainda não foram aceitas."
```

---

## 6.5 O que é a fila de conexões?

Imagine que nosso servidor execute:

```python
server.listen(5)
```

e vários clientes tentem estabelecer uma conexão ao mesmo tempo.

Podemos imaginar:

```text
             SERVIDOR
                 │
                 │
             listen()
                 │
                 ↓
        ┌─────────────────┐
        │ fila de espera  │
        ├─────────────────┤
        │ Cliente 1       │
        │ Cliente 2       │
        │ Cliente 3       │
        │ Cliente 4       │
        │ Cliente 5       │
        └─────────────────┘
```

Enquanto o programa servidor ainda não chamou:

```python
server.accept()
```

as conexões que puderem ser mantidas ficam aguardando na estrutura de fila administrada pelo sistema operacional.

O objetivo dessa fila é permitir que o sistema operacional mantenha conexões pendentes enquanto a aplicação ainda não as processou.

---

## 6.6 `backlog` não significa número máximo absoluto de clientes

Um erro comum é interpretar:

```python
server.listen(5)
```

como:

```text
"Meu servidor só pode ter 5 clientes."
```

Não é isso.

O valor está relacionado à fila de conexões pendentes que aguardam aceitação.

Ele não representa diretamente:

```text
número máximo de clientes conectados
```

Por exemplo, um servidor pode aceitar uma conexão:

```python
client, address = server.accept()
```

e depois a conexão deixa de ser uma solicitação pendente e passa a ser representada pelo socket retornado por `accept()`.

Portanto:

```text
backlog
   ↓
fila de conexões pendentes

não significa:

número total de conexões existentes
```

---

## 6.7 O socket de escuta

Depois de:

```python
server.listen()
```

o objeto:

```python
server
```

é conhecido conceitualmente como **listening socket** ou **socket de escuta**.

Ele possui uma função diferente do socket que será utilizado para conversar com o cliente.

Podemos representar:

```text
Listening socket
       │
       │ aceita conexão
       ↓
Client socket
       │
       │ comunicação
       ↓
Cliente
```

Isso é extremamente importante.

O socket que executa:

```python
server.listen()
```

não é o mesmo socket retornado por:

```python
server.accept()
```

---

## 6.8 `listen()` prepara o socket para `accept()`

Podemos pensar na relação:

```python
server.listen()
```

e:

```python
server.accept()
```

como:

```text
listen()
   ↓
"Estou pronto para receber conexões."

accept()
   ↓
"Agora me entregue uma conexão recebida."
```

Um servidor TCP normalmente possui:

```python
server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))

server.listen()

client, address = server.accept()
```

O fluxo é:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
```

---

## 6.9 O que acontece quando um cliente chama `connect()`?

Suponha que o servidor esteja executando:

```python
server.listen()
```

e esteja associado a:

```text
127.0.0.1:4444
```

Agora um cliente executa:

```python
client.connect(("127.0.0.1", 4444))
```

Temos:

```text
CLIENTE
   │
   │ connect()
   │
   ↓
127.0.0.1:4444
   │
   ↓
SERVIDOR
   │
   │ listen()
   ↓
fila de conexões
```

A solicitação de conexão é processada pelo sistema operacional.

Depois, o servidor pode chamar:

```python
server.accept()
```

para obter a conexão.

---

## 6.10 `accept()` será o próximo passo

Neste momento, é importante apenas entender a posição de `accept()` no fluxo.

Temos:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
```

Já estudamos:

```text
socket()
```

que cria o socket.

Depois:

```text
bind()
```

que associa o endereço local.

Agora:

```text
listen()
```

coloca o socket em modo de escuta.

A próxima etapa será:

```text
accept()
```

que permite ao servidor aceitar uma conexão recebida.

---

## 6.11 Exemplo de servidor até `listen()`

Podemos montar um servidor ainda incompleto:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))

server.listen()

print("Servidor aguardando conexões...")
```

Esse programa:

1. importa `socket`;
    
2. cria um socket TCP IPv4;
    
3. associa o socket a `127.0.0.1:4444`;
    
4. coloca o socket em modo de escuta;
    
5. imprime uma mensagem.
    

Podemos visualizar:

```text
Python
  ↓
socket()
  ↓
TCP / IPv4
  ↓
bind()
  ↓
127.0.0.1:4444
  ↓
listen()
  ↓
aguardando conexões
```

Entretanto, esse programa ainda não possui:

```python
accept()
```

Portanto, ele ainda não está retirando as conexões da fila para trabalhar com elas.

---

## 6.12 O que acontece se `listen()` for chamado antes de `bind()`?

Em um servidor TCP tradicional, normalmente fazemos:

```python
server.bind(("127.0.0.1", 4444))
server.listen()
```

A ordem faz parte do fluxo esperado.

Por isso, não devemos pensar em:

```python
server.listen()
server.bind(("127.0.0.1", 4444))
```

como a sequência normal.

O servidor primeiro precisa definir seu endereço local:

```text
bind()
   ↓
qual endereço local será utilizado?
```

Depois:

```text
listen()
   ↓
começar a escutar naquele endereço
```

Em outras palavras:

```text
bind()
   ↓
identidade local

listen()
   ↓
modo de escuta
```

---

## 6.13 `listen()` é específico do modelo orientado a conexão

O método:

```python
listen()
```

faz sentido para sockets que trabalham com um modelo de conexão, como:

```python
socket.SOCK_STREAM
```

Por isso, em um servidor TCP tradicional temos:

```python
server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

seguido por:

```python
server.bind(...)
server.listen(...)
```

Já UDP utiliza:

```python
socket.SOCK_DGRAM
```

e possui um modelo diferente.

Um servidor UDP normalmente não utiliza:

```python
listen()
accept()
```

Em vez disso, trabalha diretamente com datagramas utilizando métodos como:

```python
recvfrom()
sendto()
```

Portanto:

```text
TCP
 ↓
socket()
 ↓
bind()
 ↓
listen()
 ↓
accept()
```

Enquanto UDP possui um fluxo diferente:

```text
UDP
 ↓
socket()
 ↓
bind()
 ↓
recvfrom()
```

Esse contraste ficará mais claro quando estudarmos TCP e UDP na prática.

---

## 6.14 `listen()` não envia dados

Também é importante não confundir `listen()` com métodos de transmissão.

`listen()` não:

- envia dados;
    
- recebe dados da aplicação;
    
- estabelece uma conexão diretamente;
    
- lê mensagens;
    
- envia pacotes de aplicação.
    

Sua responsabilidade é preparar o socket para receber solicitações de conexão.

Podemos separar:

```text
listen()
   ↓
gerenciamento de conexões
```

enquanto métodos como:

```python
send()
sendall()
recv()
```

serão utilizados posteriormente para:

```text
transmissão de dados
```

---

## 6.15 O papel do kernel

Assim como acontece com `socket()` e `bind()`, o comportamento de `listen()` envolve o sistema operacional.

Quando fazemos:

```python
server.listen()
```

o Python solicita ao sistema operacional que aquele socket seja colocado no estado apropriado para receber conexões.

Podemos representar:

```text
Python
  │
  │ listen()
  ↓
Sistema operacional
  │
  ↓
socket em estado de escuta
  │
  ↓
gerenciamento de conexões TCP
```

O kernel passa a cuidar de aspectos da comunicação TCP enquanto a aplicação poderá posteriormente chamar:

```python
accept()
```

para obter uma conexão disponível.

---

## 6.16 Estado conceitual do socket

Até agora podemos acompanhar a evolução:

```text
socket()
   ↓
socket criado
```

Depois:

```text
bind()
   ↓
socket associado a IP + porta
```

Depois:

```text
listen()
   ↓
socket em modo de escuta
```

Podemos representar:

```text
                 SOCKET TCP

       socket()
           │
           ▼
     [CRIADO]
           │
           │ bind()
           ▼
 [ENDEREÇO ASSOCIADO]
           │
           │ listen()
           ▼
     [ESCUTANDO]
           │
           │ accept()
           ▼
 [CONEXÃO ACEITA]
```

Essa sequência será fundamental para entender servidores TCP.

---

## 6.17 Exemplo visual completo até `listen()`

Código:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))

server.listen()

print("Aguardando conexão...")
```

Fluxo:

```text
                SERVIDOR

          socket.socket()
                 │
                 ▼
          Socket TCP IPv4
                 │
                 │ bind()
                 ▼
          127.0.0.1:4444
                 │
                 │ listen()
                 ▼
          ┌──────────────┐
          │  ESCUTANDO   │
          └──────┬───────┘
                 │
                 │ conexões
                 ▼
          fila de espera
```

O próximo passo será retirar uma conexão dessa estrutura através de:

```python
server.accept()
```

---

## Resumo

- `listen()` coloca um socket TCP em **modo de escuta**.
    
- É utilizado normalmente depois de:
    
    ```python
    bind()
    ```
    
- O fluxo tradicional de um servidor TCP é:
    
    ```text
    socket()
        ↓
    bind()
        ↓
    listen()
        ↓
    accept()
    ```
    
- `listen()` não aceita uma conexão.
    
- `accept()` é responsável por aceitar uma conexão recebida.
    
- O parâmetro `backlog` está relacionado à fila de conexões pendentes.
    
- `listen(5)` não significa que o servidor só pode possuir cinco clientes.
    
- O socket que chama `listen()` é o **socket de escuta**.
    
- O socket de escuta não deve ser confundido com o socket retornado por `accept()`.
    
- `listen()` não transmite dados da aplicação.
    
- `listen()` é utilizado no modelo orientado a conexão, como TCP.
    
- UDP não utiliza o fluxo tradicional `listen()` → `accept()`.
    
- O sistema operacional gerencia a fila e o estado das conexões enquanto a aplicação utiliza `accept()` para obter uma conexão disponível.
    

---

# 7. Aceitando conexões com `accept()`

Depois de criar o socket, associá-lo a um endereço com `bind()` e colocá-lo em modo de escuta com `listen()`, o servidor está preparado para receber solicitações de conexão.

A próxima operação é:

```python
server.accept()
```

O método `accept()` é responsável por **aceitar uma conexão que está aguardando no socket de escuta**.

O fluxo do servidor TCP passa a ser:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
```

Podemos pensar nas responsabilidades dessa sequência:

```text
socket()
   ↓
criar o socket

bind()
   ↓
definir endereço local

listen()
   ↓
preparar para receber conexões

accept()
   ↓
aceitar uma conexão
```

---

## 7.1 Sintaxe de `accept()`

A utilização básica é:

```python
client, address = server.accept()
```

O método retorna **dois valores**:

```text
socket da conexão
        +
endereço do cliente
```

Por exemplo:

```python
client, address = server.accept()
```

Podemos visualizar:

```text
server.accept()
      ↓
┌──────────────────────┐
│ socket da conexão    │
│ endereço do cliente  │
└──────────────────────┘
      ↓
client, address
```

Isso é extremamente importante porque `accept()` não simplesmente retorna `True` ou `False`.

Ele fornece um **novo objeto socket** que será utilizado para conversar com aquele cliente.

---

## 7.2 O retorno de `accept()`

Considere:

```python
client, address = server.accept()
```

A variável:

```python
client
```

recebe um novo objeto socket.

Enquanto:

```python
address
```

recebe o endereço do cliente.

Em IPv4, normalmente teremos:

```python
("IP", porta)
```

Por exemplo:

```python
("127.0.0.1", 53142)
```

Então:

```text
client
   ↓
socket conectado ao cliente

address
   ↓
("127.0.0.1", 53142)
```

---

## 7.3 O socket de escuta não é o socket da comunicação

Esse é um dos conceitos mais importantes de servidores TCP.

Imagine:

```python
server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))

server.listen()

client, address = server.accept()
```

Depois de `accept()`, temos dois sockets diferentes:

```text
server
   ↓
socket de escuta

client
   ↓
socket da conexão com um cliente específico
```

Podemos representar:

```text
                 SERVIDOR

          ┌─────────────────┐
          │      server     │
          │ listening socket│
          └────────┬────────┘
                   │
                   │ accept()
                   ▼
          ┌─────────────────┐
          │      client     │
          │ connection sock │
          └─────────────────┘
                   │
                   │ TCP
                   ▼
                Cliente
```

O socket `server` continua existindo para receber outras conexões.

O socket `client` representa uma conexão específica.

---

## 7.4 Por que criar outro socket?

Essa arquitetura permite que um único servidor tenha várias conexões.

Imagine três clientes:

```text
Cliente A
    │
    ▼
┌──────────────┐
│              │
│   SERVER     │
│              │
└──────────────┘
    ▲
    │
Cliente B

    ▲
    │
Cliente C
```

O socket de escuta:

```text
server
```

fica responsável por aceitar conexões.

Para cada conexão aceita, o sistema fornece um socket diferente:

```text
server
   │
   ├── accept() → client_A
   │
   ├── accept() → client_B
   │
   └── accept() → client_C
```

Podemos visualizar:

```text
              SOCKET DE ESCUTA
                     │
          ┌──────────┼──────────┐
          │          │          │
          ▼          ▼          ▼
       socket_A   socket_B   socket_C
          │          │          │
          ▼          ▼          ▼
       Cliente A  Cliente B  Cliente C
```

Esse modelo é fundamental para servidores TCP.

---

## 7.5 `accept()` é bloqueante por padrão

Por padrão, quando executamos:

```python
client, address = server.accept()
```

o programa pode ficar **bloqueado esperando uma conexão**.

Por exemplo:

```python
print("Aguardando conexão...")

client, address = server.accept()

print("Cliente conectado!")
```

Se nenhum cliente se conectar, o programa normalmente permanecerá parado nesta linha:

```python
server.accept()
```

Podemos visualizar:

```text
Servidor
   │
   ▼
accept()
   │
   │
   │ nenhuma conexão
   │
   │
   └──────→ esperando...
```

Quando um cliente chega:

```text
Servidor
   │
   ▼
accept()
   │
   │ conexão disponível
   ▼
retorna socket + endereço
```

---

## 7.6 O que significa "bloqueante"?

Uma operação bloqueante é uma operação que pode fazer o fluxo atual do programa **esperar até que alguma condição necessária aconteça**.

No caso de:

```python
server.accept()
```

a condição é:

```text
uma conexão disponível para ser aceita
```

Então:

```text
accept()
   ↓
há conexão?
   │
   ├── não → espera
   │
   └── sim → retorna
```

Isso será importante futuramente quando estudarmos:

- `setblocking()`;
    
- timeouts;
    
- sockets não bloqueantes;
    
- `select`;
    
- `selectors`;
    
- programação concorrente;
    
- threads;
    
- `asyncio`.
    

Neste momento, basta entender que o comportamento padrão é bloqueante.

---

## 7.7 Exemplo mínimo usando `accept()`

Podemos montar nosso primeiro servidor TCP completo até o ponto de aceitar uma conexão:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))

server.listen()

print("Aguardando conexão...")

client, address = server.accept()

print("Cliente conectado!")
print("Endereço:", address)

client.close()
server.close()
```

O fluxo é:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
   ↓
client
   ↓
close()
```

---

## 7.8 O que acontece quando o cliente conecta?

Suponha que o servidor esteja executando:

```python
server.accept()
```

Agora um cliente executa:

```python
client.connect(("127.0.0.1", 4444))
```

O processo pode ser representado assim:

```text
CLIENTE                         SERVIDOR

socket()                        socket()
   │                               │
   │                               │
connect() ──────────────────────► │
                                   │
                                   │ accept()
                                   ▼
                              conexão aceita
```

Depois que a conexão é aceita:

```text
client
   ↓
socket da conexão
```

e:

```text
address
   ↓
endereço do cliente
```

são retornados pelo servidor.

---

## 7.9 O endereço retornado por `accept()`

Em IPv4:

```python
client, address = server.accept()
```

pode produzir:

```python
address = ("127.0.0.1", 53142)
```

Isso significa:

```text
IP do cliente:
127.0.0.1

Porta do cliente:
53142
```

A porta pode ser diferente da porta do servidor.

Por exemplo:

```text
Servidor:
127.0.0.1:4444

Cliente:
127.0.0.1:53142
```

A conexão pode ser representada como:

```text
127.0.0.1:53142
       │
       │ TCP
       ▼
127.0.0.1:4444
```

Portanto, não devemos confundir:

```text
4444
```

com a porta do cliente.

---

## 7.10 O servidor normalmente possui uma porta conhecida

Em nosso exemplo:

```python
server.bind(("127.0.0.1", 4444))
```

a porta:

```text
4444
```

é conhecida pelo cliente.

Por isso o cliente pode fazer:

```python
client.connect(("127.0.0.1", 4444))
```

O cliente sabe:

```text
IP do servidor
+
porta do servidor
```

O sistema operacional pode escolher automaticamente uma porta local para o cliente.

Assim:

```text
CLIENTE
127.0.0.1:53142
       │
       │
       ▼
SERVIDOR
127.0.0.1:4444
```

---

## 7.11 `accept()` e o TCP

É importante entender que `accept()` trabalha com o modelo de conexão do TCP.

O fluxo simplificado é:

```text
Cliente
   │
   │ solicitação de conexão
   ▼
Servidor
   │
   │ conexão processada
   ▼
listen/filas
   │
   │ accept()
   ▼
socket conectado
```

A comunicação de dados ocorrerá depois através do socket retornado:

```python
client
```

Por exemplo:

```python
data = client.recv(1024)
```

ou:

```python
client.sendall(b"Hello")
```

Portanto:

```text
server
   ↓
aceita conexões

client
   ↓
troca dados com aquele cliente
```

---

## 7.12 `accept()` não recebe os dados da aplicação

Outro ponto importante:

```python
client, address = server.accept()
```

não significa:

```text
"receba os dados enviados pelo cliente"
```

`accept()` aceita a **conexão**.

Para receber os dados da aplicação, utilizamos métodos como:

```python
client.recv(...)
```

Portanto:

```text
accept()
   ↓
aceita conexão

recv()
   ↓
recebe dados
```

Da mesma forma:

```text
send()
sendall()
   ↓
envia dados
```

Essa separação é essencial.

---

## 7.13 Um servidor TCP básico completo

Podemos agora montar um servidor que aceita uma conexão:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))

server.listen()

print("Servidor aguardando conexão...")

client, address = server.accept()

print(f"Cliente conectado: {address}")

client.close()
server.close()
```

A sequência é:

```text
1. socket()
      ↓
2. bind()
      ↓
3. listen()
      ↓
4. accept()
      ↓
5. client
      ↓
6. close()
```

Esse servidor aceita uma conexão e depois fecha os sockets.

Ele ainda não possui um loop para aceitar vários clientes.

---

## 7.14 Aceitando vários clientes

Para aceitar várias conexões, podemos colocar `accept()` dentro de um loop:

```python
while True:
    client, address = server.accept()

    print(f"Cliente conectado: {address}")

    client.close()
```

O fluxo passa a ser:

```text
server
   │
   ▼
accept()
   │
   ▼
cliente 1
   │
   ▼
close()
   │
   ▼
accept()
   │
   ▼
cliente 2
   │
   ▼
close()
   │
   ▼
accept()
   │
   ▼
...
```

Esse modelo permite que o servidor aceite conexões repetidamente.

Porém, existe uma limitação importante.

Se fizermos:

```python
client, address = server.accept()

client.recv(...)
```

e ficarmos ocupados tratando aquele cliente, o programa poderá deixar de aceitar outros clientes enquanto estiver bloqueado naquela comunicação.

Isso será importante quando estudarmos concorrência.

---

## 7.15 Socket de escuta e sockets de clientes

Podemos montar uma visão mais completa:

```text
                         SERVIDOR

                    ┌───────────────┐
                    │     server    │
                    │ listening sock│
                    └───────┬───────┘
                            │
                         accept()
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
         client_A       client_B       client_C
             │              │              │
             ▼              ▼              ▼
         Cliente A      Cliente B      Cliente C
```

O socket:

```text
server
```

não deve ser utilizado para trocar os dados da aplicação com cada cliente.

Os sockets:

```text
client_A
client_B
client_C
```

representam as conexões individuais.

---

## 7.16 Por que isso é importante para servidores reais?

Imagine um servidor web.

Milhares de clientes podem solicitar conexões.

O servidor não cria um único socket para representar todas as conversas.

Existe um socket responsável por escutar:

```text
listening socket
```

E cada conexão aceita possui seu próprio contexto de comunicação.

Simplificando:

```text
                SERVIDOR

              listening
                 socket
                   │
       ┌───────────┼───────────┐
       │           │           │
       ▼           ▼           ▼
    conexão 1   conexão 2   conexão 3
       │           │           │
       ▼           ▼           ▼
    cliente A   cliente B   cliente C
```

É essa arquitetura que permite que um servidor trabalhe com múltiplas conexões.

A forma como essas conexões são processadas — sequencialmente, com threads, processos, `select`, `selectors` ou `asyncio` — é outro assunto.

---

## 7.17 `accept()` retorna uma tupla de dois elementos

Podemos verificar diretamente:

```python
result = server.accept()

print(type(result))
```

O resultado é uma tupla contendo:

```text
(socket, endereço)
```

Por isso podemos fazer:

```python
client, address = server.accept()
```

Isso é chamado de **desempacotamento de tupla** em Python.

É equivalente conceitualmente a:

```python
result = server.accept()

client = result[0]
address = result[1]
```

Mas a forma mais comum e legível é:

```python
client, address = server.accept()
```

---

## 7.18 Verificando o socket retornado

Podemos verificar o tipo:

```python
print(type(client))
```

O resultado será semelhante a:

```text
<class 'socket.socket'>
```

Isso confirma que `accept()` retorna outro objeto socket.

Portanto:

```text
server
   ↓
socket de escuta

client
   ↓
socket conectado
```

Os dois são objetos da mesma classe:

```python
socket.socket
```

mas possuem **papéis diferentes** no servidor.

---

## 7.19 Uma analogia útil

Podemos imaginar um servidor como uma recepção.

```text
Recepção
   ↓
socket de escuta
```

A recepção não conversa detalhadamente com todos os visitantes ao mesmo tempo.

Ela recebe uma pessoa:

```text
accept()
   ↓
"Próximo."
```

E cria o contexto necessário para atender aquela pessoa:

```text
client socket
   ↓
conversa com aquele cliente
```

Enquanto a recepção continua existindo para receber outras pessoas.

A analogia não representa todos os detalhes internos do TCP, mas ajuda a entender a separação entre:

```text
escutar conexões
```

e:

```text
comunicar-se com uma conexão aceita
```

---

## 7.20 Modelo mental definitivo de `accept()`

Podemos resumir:

```text
socket()
   ↓
cria socket

bind()
   ↓
define endereço local

listen()
   ↓
prepara socket para conexões

accept()
   ↓
aceita uma conexão

       ↓

(socket, endereço)
       ↓
socket específico para comunicação
```

Ou visualmente:

```text
              SERVIDOR

       ┌──────────────────┐
       │ listening socket │
       └────────┬─────────┘
                │
                │ accept()
                ▼
       ┌──────────────────┐
       │ connected socket │
       └────────┬─────────┘
                │
                │ recv()/send()
                ▼
             CLIENTE
```

Essa separação entre **socket de escuta** e **socket de conexão** é um dos fundamentos mais importantes de servidores TCP.

---

## Resumo

- `accept()` aceita uma conexão recebida por um socket TCP em modo de escuta.
    
- A utilização comum é:
    
    ```python
    client, address = server.accept()
    ```
    
- `accept()` retorna:
    
    ```text
    socket da conexão
    +
    endereço do cliente
    ```
    
- O socket retornado por `accept()` é diferente do socket de escuta.
    
- O socket de escuta continua disponível para aceitar outras conexões.
    
- `accept()` é bloqueante por padrão.
    
- `accept()` aceita a conexão, mas não recebe os dados da aplicação.
    
- Para receber dados utilizamos métodos como:
    
    ```python
    recv()
    ```
    
- Para enviar dados utilizamos métodos como:
    
    ```python
    send()
    sendall()
    ```
    
- Um servidor pode chamar `accept()` repetidamente para aceitar vários clientes.
    
- O endereço retornado normalmente contém:
    
    ```python
    (IP, porta)
    ```
    
- A porta do cliente normalmente é diferente da porta conhecida do servidor.
    
- O fluxo básico de um servidor TCP agora é:
    
    ```text
    socket()
        ↓
    bind()
        ↓
    listen()
        ↓
    accept()
        ↓
    recv()/send()
    ```
    
- O socket de escuta é responsável por aceitar conexões.
    
- O socket retornado por `accept()` é utilizado para a comunicação com um cliente específico.
    

---

# 8. Estabelecendo uma conexão com `connect()`

Até agora estudamos principalmente o lado do **servidor TCP**:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
```

Agora precisamos entender o lado do **cliente**.

Um cliente TCP precisa solicitar uma conexão com um servidor. Para isso, utilizamos:

```python
connect()
```

Em Python:

```python
client.connect(endereco)
```

Por exemplo:

```python
client.connect(("127.0.0.1", 4444))
```

Essa operação informa ao sistema operacional que o socket deve tentar estabelecer uma conexão com o endereço especificado.

---

## 8.1 O que `connect()` faz?

Considere:

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

client.connect(("127.0.0.1", 4444))
```

O fluxo é:

```text
socket()
   ↓
cria socket TCP
   ↓
connect()
   ↓
solicita conexão com 127.0.0.1:4444
```

Podemos pensar em `connect()` como:

```text
"Quero estabelecer uma conexão
 com este endereço remoto."
```

Por isso, diferente de `bind()`, `connect()` trabalha principalmente com o **destino da conexão**.

---

## 8.2 `bind()` e `connect()` possuem funções diferentes

Essa diferença é fundamental.

`bind()`:

```python
server.bind(("127.0.0.1", 4444))
```

associa o socket a um **endereço local**.

`connect()`:

```python
client.connect(("127.0.0.1", 4444))
```

solicita uma conexão com um **endereço remoto**.

Podemos visualizar:

```text
bind()
   ↓
"Este é o meu endereço local."

connect()
   ↓
"Quero me conectar a este endereço."
```

Portanto:

```text
bind()
   ↓
LOCAL

connect()
   ↓
DESTINO
```

---

## 8.3 Sintaxe de `connect()`

A forma básica é:

```python
socket.connect(endereco)
```

Para IPv4:

```python
client.connect((ip, porta))
```

Exemplo:

```python
client.connect(("127.0.0.1", 4444))
```

A estrutura do endereço é semelhante à utilizada em `bind()`:

```python
("127.0.0.1", 4444)
```

onde:

```text
127.0.0.1
    ↓
endereço IP

4444
    ↓
porta
```

A diferença não está no formato da tupla.

A diferença está no **papel do endereço**.

---

## 8.4 Exemplo mínimo de cliente TCP

Podemos criar um cliente simples:

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

client.connect(("127.0.0.1", 4444))

print("Conectado ao servidor!")

client.close()
```

Para que isso funcione, precisamos ter um servidor TCP escutando:

```text
127.0.0.1:4444
```

Por exemplo:

```text
SERVIDOR
127.0.0.1:4444
     ↑
     │
     │ connect()
     │
CLIENTE
```

---

## 8.5 O servidor precisa estar preparado

Imagine que o cliente execute:

```python
client.connect(("127.0.0.1", 4444))
```

mas nenhum servidor esteja escutando nessa porta.

A tentativa de conexão poderá falhar com um erro semelhante a:

```text
ConnectionRefusedError: [Errno 111] Connection refused
```

Isso significa, de forma simplificada, que não havia um serviço aceitando a conexão naquele endereço naquele momento.

Podemos representar:

```text
CLIENTE
   │
   │ connect()
   ↓
127.0.0.1:4444
   │
   X
nenhum servidor disponível
```

Portanto, para testar um cliente, precisamos normalmente iniciar primeiro o servidor.

---

## 8.6 Servidor e cliente trabalhando juntos

Agora podemos combinar tudo o que aprendemos.

### Servidor

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))

server.listen()

print("Servidor aguardando conexão...")

client, address = server.accept()

print("Cliente conectado:", address)

client.close()
server.close()
```

### Cliente

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

client.connect(("127.0.0.1", 4444))

print("Conectado ao servidor!")

client.close()
```

O fluxo será:

```text
                     SERVIDOR

              socket()
                 ↓
              bind()
                 ↓
         127.0.0.1:4444
                 ↓
              listen()
                 ↓
              accept()
                 ↑
                 │
                 │ conexão
                 │
              connect()
                 ↑
                 │
               CLIENTE
```

---

## 8.7 O que acontece durante `connect()`?

Quando o cliente executa:

```python
client.connect(("127.0.0.1", 4444))
```

o sistema operacional inicia o processo necessário para estabelecer a conexão TCP.

Em uma conexão TCP tradicional, isso envolve o conhecido **three-way handshake**.

De forma simplificada:

```text
CLIENTE                    SERVIDOR

   SYN ───────────────────────►

       ◄──────────────── SYN-ACK

   ACK ───────────────────────►

       conexão estabelecida
```

Essas mensagens fazem parte do funcionamento do protocolo TCP.

A aplicação Python não precisa construir manualmente esses segmentos para utilizar uma conexão TCP normal.

O sistema operacional e a implementação do TCP cuidam desse processo.

---

## 8.8 O three-way handshake

Podemos entender o processo de maneira conceitual.

### 1. Cliente envia SYN

O cliente informa:

```text
"Quero iniciar uma conexão TCP."
```

Representado por:

```text
SYN
```

### 2. Servidor responde SYN-ACK

O servidor responde indicando que recebeu a solicitação e também está disposto a estabelecer a conexão:

```text
SYN + ACK
```

### 3. Cliente envia ACK

O cliente confirma:

```text
ACK
```

Depois disso, a conexão TCP é considerada estabelecida.

Visualmente:

```text
CLIENTE                         SERVIDOR

   SYN
    ───────────────────────────►

                   SYN + ACK
    ◄───────────────────────────

   ACK
    ───────────────────────────►

         CONEXÃO ESTABELECIDA
```

---

## 8.9 `connect()` e o socket do cliente

Depois de:

```python
client.connect(("127.0.0.1", 4444))
```

o objeto:

```python
client
```

passa a representar uma conexão TCP estabelecida com aquele destino, caso a operação tenha sido concluída com sucesso.

Agora podemos utilizar métodos de comunicação, como:

```python
client.send(...)
```

```python
client.sendall(...)
```

e:

```python
client.recv(...)
```

Portanto:

```text
socket()
   ↓
socket criado

connect()
   ↓
conexão estabelecida

send()/sendall()
   ↓
envia dados

recv()
   ↓
recebe dados
```

---

## 8.10 `connect()` é bloqueante por padrão

Assim como `accept()`, `connect()` normalmente possui comportamento bloqueante.

Quando fazemos:

```python
client.connect(("127.0.0.1", 4444))
```

o programa pode esperar até:

- a conexão ser estabelecida;
    
- ocorrer uma falha;
    
- ocorrer um timeout configurado;
    
- ou outro evento relacionado ao estabelecimento da conexão.
    

Podemos visualizar:

```text
connect()
   ↓
tentativa de conexão
   │
   ├── sucesso → retorna
   │
   └── falha → exceção
```

Por isso, se o destino estiver indisponível, o programa pode não simplesmente continuar imediatamente.

---

## 8.11 Timeout de conexão

Podemos configurar um timeout para evitar uma espera indefinida em determinadas situações.

Por exemplo:

```python
client.settimeout(5)
```

Depois:

```python
client.connect(("127.0.0.1", 4444))
```

Nesse caso, estamos configurando um limite de tempo para operações bloqueantes relacionadas ao socket.

O conceito é:

```text
settimeout(5)
      ↓
operações bloqueantes
      ↓
limite de espera
```

Se o tempo for excedido, Python poderá gerar uma exceção de timeout.

Esse assunto será aprofundado posteriormente quando estudarmos:

- `settimeout()`;
    
- sockets bloqueantes;
    
- sockets não bloqueantes;
    
- tratamento de exceções.
    

---

## 8.12 O sistema operacional pode escolher a porta local do cliente

Uma coisa interessante acontece quando o cliente executa:

```python
client.connect(("127.0.0.1", 4444))
```

sem executar `bind()` previamente.

O sistema operacional normalmente escolhe automaticamente uma porta local.

Por exemplo:

```text
CLIENTE
127.0.0.1:53142
       │
       │ TCP
       ▼
SERVIDOR
127.0.0.1:4444
```

Aqui:

```text
53142
```

é a porta local temporária do cliente.

Enquanto:

```text
4444
```

é a porta utilizada pelo servidor.

Portanto, uma conexão TCP pode ser identificada por quatro informações:

```text
IP origem
+
porta origem
+
IP destino
+
porta destino
```

Por exemplo:

```text
127.0.0.1:53142
        ↓
127.0.0.1:4444
```

Esse conjunto forma o contexto de uma conexão TCP.

---

## 8.13 O cliente não precisa chamar `bind()` normalmente

Um erro comum é pensar que todo cliente precisa fazer:

```python
client.bind(...)
```

antes de:

```python
client.connect(...)
```

Isso normalmente não é necessário.

O cliente pode simplesmente fazer:

```python
client = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

client.connect(("127.0.0.1", 4444))
```

O sistema operacional escolhe automaticamente um endereço local apropriado.

Isso deixa o código mais simples.

---

## 8.14 Quando um cliente pode usar `bind()`?

Existem situações em que um cliente pode precisar escolher explicitamente seu endereço local.

Por exemplo, quando queremos:

- utilizar uma interface de rede específica;
    
- utilizar uma porta local específica;
    
- controlar o endereço de origem;
    
- trabalhar com requisitos específicos de rede.
    

Nesse caso, podemos fazer:

```python
client.bind(("192.168.1.20", 5000))
client.connect(("192.168.1.10", 4444))
```

Teríamos:

```text
CLIENTE
192.168.1.20:5000
       │
       │ connect()
       ▼
SERVIDOR
192.168.1.10:4444
```

Mas isso é diferente do caso comum.

Na maioria das aplicações, o sistema operacional escolhe automaticamente o endereço e a porta local do cliente.

---

## 8.15 `connect()` não significa "enviar dados"

Outro erro conceitual comum:

```python
client.connect(("127.0.0.1", 4444))
```

não significa:

```text
"enviei uma mensagem para o servidor."
```

`connect()` estabelece a conexão.

Depois podemos transmitir dados através de:

```python
client.send(...)
```

ou:

```python
client.sendall(...)
```

E receber através de:

```python
client.recv(...)
```

Portanto:

```text
connect()
   ↓
estabelece conexão

send()/sendall()
   ↓
envia dados

recv()
   ↓
recebe dados
```

---

## 8.16 `connect()` e `accept()` trabalham juntos

Essas duas operações são complementares.

No cliente:

```python
client.connect(("127.0.0.1", 4444))
```

No servidor:

```python
client, address = server.accept()
```

Podemos visualizar:

```text
CLIENTE                         SERVIDOR

connect()
    │
    │
    ├─────────────────────────►
    │
    │                         accept()
    │                            │
    │                            ▼
    │                     socket da conexão
    │
    ▼
conexão estabelecida
```

Ou, de forma mais simples:

```text
Cliente
connect()
   │
   ▼
Servidor
accept()
```

As duas operações participam do estabelecimento da conexão vista pela aplicação.

---

## 8.17 O socket do cliente e o socket retornado por `accept()`

Considere:

```python
# Cliente
client.connect(("127.0.0.1", 4444))
```

e:

```python
# Servidor
connection, address = server.accept()
```

Agora temos:

```text
CLIENTE                         SERVIDOR

client  ◄════════════════════► connection
```

Os dois sockets representam os dois lados da mesma conexão TCP.

Podemos visualizar:

```text
┌──────────────┐              ┌────────────────┐
│   CLIENTE    │              │    SERVIDOR    │
│              │              │                │
│ client       │◄────────────►│ connection     │
└──────────────┘              └────────────────┘
```

Depois podemos fazer:

```text
CLIENTE                         SERVIDOR

send() ───────────────────────► recv()

recv() ◄─────────────────────── send()
```

Essa é a base da comunicação bidirecional do TCP.

---

## 8.18 Cliente e servidor podem enviar e receber

Depois que a conexão foi estabelecida, o modelo TCP é bidirecional.

Isso significa que ambos os lados podem enviar e receber dados.

Por exemplo:

```text
CLIENTE                         SERVIDOR

send() ───────────────────────► recv()

recv() ◄─────────────────────── send()
```

O cliente não é apenas um "enviador".

O servidor também não é apenas um "receptor".

Depois que a conexão TCP está estabelecida, ambos podem utilizar a conexão para comunicação nos dois sentidos.

Isso será explorado detalhadamente na próxima parte, quando estudarmos:

```python
send()
sendall()
recv()
```

---

## 8.19 Exemplo de conexão completa

### Servidor

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))
server.listen()

print("Aguardando conexão...")

connection, address = server.accept()

print("Conexão recebida de:", address)

connection.close()
server.close()
```

### Cliente

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

client.connect(("127.0.0.1", 4444))

print("Conectado!")

client.close()
```

Fluxo:

```text
                    SERVIDOR

                 socket()
                    ↓
                 bind()
                    ↓
             127.0.0.1:4444
                    ↓
                 listen()
                    ↓
                 accept()
                    ▲
                    │
                    │ conexão
                    │
                 connect()
                    ▲
                    │
                  CLIENTE
```

Depois da conexão:

```text
CLIENTE                         SERVIDOR

client  ◄════════════════════► connection
```

Agora existe um canal TCP através do qual os dois lados podem trocar dados.

---

## 8.20 Diferença entre `connect()` e `accept()`

Podemos resumir a diferença:

|Operação|Lado típico|Função|
|---|---|---|
|`connect()`|Cliente|Solicita uma conexão com um destino|
|`accept()`|Servidor|Aceita uma conexão recebida|
|`listen()`|Servidor|Coloca o socket em modo de escuta|
|`bind()`|Principalmente servidor|Associa o socket a um endereço local|

Fluxo:

```text
CLIENTE

socket()
   ↓
connect()
   ↓
comunicação
```

Servidor:

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
```

---

## 8.21 Modelo mental definitivo de `connect()`

Podemos pensar em:

```python
client.connect(("127.0.0.1", 4444))
```

como:

```text
"Tenho um socket TCP.
Quero estabelecer uma conexão
com o endpoint 127.0.0.1:4444."
```

Depois de uma conexão bem-sucedida:

```text
socket()
   ↓
connect()
   ↓
TCP estabelecido
   ↓
send()/sendall()
   ↓
recv()
```

Enquanto no servidor:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
   ↓
send()/sendall()
   ↓
recv()
```

A partir daqui, já temos a estrutura básica de uma comunicação TCP cliente-servidor.

---

## Resumo

- `connect()` é utilizado para solicitar uma conexão com um endereço remoto.
    
- Exemplo:
    
    ```python
    client.connect(("127.0.0.1", 4444))
    ```
    
- `connect()` normalmente é utilizado no lado do cliente.
    
- `bind()` associa um socket a um endereço local.
    
- `connect()` utiliza um endereço de destino.
    
- O cliente normalmente não precisa executar `bind()` manualmente.
    
- O sistema operacional pode escolher automaticamente a porta local do cliente.
    
- Uma conexão TCP pode ser representada por:
    
    ```text
    IP origem + porta origem
    +
    IP destino + porta destino
    ```
    
- O estabelecimento de uma conexão TCP envolve o **three-way handshake**:
    
    ```text
    SYN
    SYN-ACK
    ACK
    ```
    
- `connect()` não envia os dados da aplicação.
    
- Depois de conectar, podemos utilizar:
    
    ```python
    send()
    sendall()
    recv()
    ```
    
- No servidor, `accept()` retorna o socket correspondente à conexão aceita.
    
- O socket do cliente e o socket retornado por `accept()` representam os dois lados da mesma conexão TCP.
    
- Depois de estabelecida, a conexão TCP permite comunicação nos dois sentidos.
    
- O fluxo básico completo agora é:
    

```text
CLIENTE                         SERVIDOR

socket()                        socket()
   ↓                               ↓
connect()                       bind()
   │                               ↓
   │                            listen()
   │                               ↓
   └─────────────────────────► accept()
                                   │
                                   ▼
                              conexão TCP
                                   │
                     ┌─────────────┴─────────────┐
                     ↓                           ↓
                   send()                      recv()
                     ↑                           ↑
                     └─────────────┬─────────────┘
                                   │
                              comunicação
```

---

# 9. Enviando e recebendo dados

Depois de criar o socket, associá-lo a um endereço, colocá-lo em escuta e estabelecer a conexão, finalmente podemos realizar a parte principal da comunicação:

> **enviar e receber dados.**

Em TCP, os dados são tratados como um **fluxo de bytes**.

Isso significa que o socket não trabalha diretamente com `str` do Python.

Por exemplo:

```python
"Olá"
```

é uma string.

Já:

```python
b"Olá"
```

é uma sequência de bytes.

Como a comunicação de rede trabalha com bytes, precisamos normalmente **codificar** uma string antes de enviá-la e **decodificar** os bytes recebidos para voltar a uma string.

O fluxo básico é:

```text
STRING
   ↓
encode()
   ↓
BYTES
   ↓
socket.send() / socket.sendall()
   ↓
REDE
   ↓
socket.recv()
   ↓
BYTES
   ↓
decode()
   ↓
STRING
```

---

## 9.1 `send()`

O método `send()` é utilizado para enviar dados através de um socket conectado.

Sintaxe:

```python
socket.send(data)
```

Também é possível utilizar flags:

```python
socket.send(data, flags)
```

### Parâmetros

|Parâmetro|Tipo|Obrigatório|Padrão|Comportamento|
|---|---|---|---|---|
|`data`|bytes-like|Sim|—|Dados que serão enviados|
|`flags`|`int`|Não|`0`|Flags específicas para a operação|

Exemplo:

```python
client.send(b"Olá servidor!")
```

Nesse caso:

```text
b"Olá servidor!"
       ↓
      bytes
       ↓
      send()
       ↓
socket
       ↓
    servidor
```

---

## 9.2 `send()` retorna a quantidade de bytes enviada

Um detalhe muito importante é que `send()` **retorna a quantidade de bytes que conseguiu enviar**.

Exemplo:

```python
data = b"Olá servidor!"

enviados = client.send(data)

print(enviados)
```

Se todos os bytes forem enviados:

```text
data
 ↓
12 bytes
 ↓
send()
 ↓
12
```

O valor retornado pode ser menor que o tamanho total dos dados.

Por exemplo:

```python
data = b"A" * 10000

enviados = client.send(data)

print(enviados)
```

Poderíamos obter algo como:

```text
8192
```

Isso significa que naquele momento apenas 8192 bytes foram enviados.

Portanto:

```python
send()
```

**não garante que todos os dados fornecidos foram enviados em uma única chamada.**

---

## 9.3 Por que `send()` pode enviar apenas parte dos dados?

TCP trabalha com um fluxo de bytes e possui buffers internos.

Quando fazemos:

```python
socket.send(data)
```

estamos pedindo ao sistema operacional para enviar aqueles dados.

O sistema operacional pode aceitar apenas uma parte naquele momento.

Podemos imaginar:

```text
Aplicação
    │
    │ 10000 bytes
    ▼
socket.send()
    │
    │ aceita 5000
    ▼
Buffer do sistema operacional
    │
    ▼
Rede
```

Por isso o valor retornado por `send()` é importante:

```python
enviados = socket.send(data)
```

Ele informa quantos bytes foram aceitos/enviados pela chamada.

---

## 9.4 `sendall()`

Quando queremos enviar todos os dados de maneira simples, normalmente utilizamos:

```python
socket.sendall(data)
```

Exemplo:

```python
client.sendall(b"Olá servidor!")
```

A principal diferença é:

```text
send()
    ↓
pode enviar apenas uma parte

sendall()
    ↓
continua tentando até enviar tudo
```

Se ocorrer um erro durante o processo, `sendall()` lança uma exceção.

Em caso de sucesso, seu retorno é:

```python
None
```

Portanto:

```python
resultado = client.sendall(b"Olá")
print(resultado)
```

resulta em:

```text
None
```

Isso é diferente de `send()`:

```python
resultado = client.send(b"Olá")
print(resultado)
```

que retorna a quantidade de bytes enviados.

---

## 9.5 `send()` vs `sendall()`

|Método|Retorno|Pode enviar parcialmente?|Uso comum|
|---|---|---|---|
|`send()`|quantidade de bytes|Sim|Controle manual do envio|
|`sendall()`|`None` em sucesso|Internamente continua enviando|Envio simples de todos os dados|

Para códigos simples de cliente/servidor TCP:

```python
socket.sendall(data)
```

geralmente é a opção mais conveniente.

---

## 9.6 Enviando uma `str`

Não podemos fazer diretamente:

```python
client.send("Olá")
```

Isso gera um erro porque:

```text
"Olá"
 ↓
str
```

mas o socket espera algo compatível com bytes.

Precisamos converter:

```python
mensagem = "Olá"

client.send(mensagem.encode())
```

Ou:

```python
client.sendall(mensagem.encode())
```

O método:

```python
encode()
```

transforma uma string em bytes utilizando uma codificação.

Por padrão, podemos utilizar UTF-8:

```python
mensagem.encode("utf-8")
```

Exemplo:

```python
mensagem = "Olá servidor!"

dados = mensagem.encode("utf-8")

client.sendall(dados)
```

O fluxo é:

```text
"Olá servidor!"
      ↓
     str
      ↓
encode("utf-8")
      ↓
    bytes
      ↓
  sendall()
```

---

## 9.7 Recebendo dados com `recv()`

Para receber dados de um socket utilizamos:

```python
socket.recv(bufsize)
```

Exemplo:

```python
data = client.recv(1024)
```

O valor:

```python
1024
```

é a quantidade máxima de bytes que aquela chamada está preparada para retornar.

Não significa:

> "Receba exatamente 1024 bytes."

Significa:

> "Receba no máximo 1024 bytes nesta chamada."

---

## 9.8 Parâmetros de `recv()`

Sintaxe:

```python
socket.recv(bufsize, flags)
```

### Parâmetros

|Parâmetro|Tipo|Obrigatório|Padrão|Comportamento|
|---|---|---|---|---|
|`bufsize`|`int`|Sim|—|Quantidade máxima de bytes retornada|
|`flags`|`int`|Não|`0`|Flags específicas para recebimento|

Exemplo:

```python
data = client.recv(1024)
```

Aqui:

```text
socket
   ↓
recv(1024)
   ↓
até 1024 bytes
   ↓
retorna bytes
```

---

## 9.9 `recv(1024)` não significa receber 1024 bytes

Esse é um erro conceitual muito comum.

Imagine que o servidor envie:

```python
server.sendall(b"Oi")
```

O cliente faz:

```python
data = client.recv(1024)
```

O retorno pode ser:

```python
b"Oi"
```

e não:

```text
1024 bytes
```

O argumento `1024` representa o **limite máximo da quantidade de bytes retornados naquela chamada**.

Por exemplo:

```python
client.recv(1024)
```

pode retornar:

```text
b"A"
```

ou:

```text
b"Hello"
```

ou:

```text
b"A" * 500
```

ou:

```text
b"A" * 1024
```

Dependendo dos dados disponíveis e do comportamento da comunicação.

---

## 9.10 `recv()` retorna `bytes`

Assim como `send()` trabalha com bytes, `recv()` também retorna bytes.

Exemplo:

```python
data = client.recv(1024)

print(data)
```

Podemos obter:

```text
b'Olá servidor!'
```

O `b` indica que estamos trabalhando com um objeto `bytes`.

Para transformar esses bytes novamente em uma string:

```python
mensagem = data.decode("utf-8")
```

Exemplo:

```python
data = client.recv(1024)

mensagem = data.decode("utf-8")

print(mensagem)
```

Resultado:

```text
Olá servidor!
```

O fluxo inverso é:

```text
REDE
 ↓
recv()
 ↓
bytes
 ↓
decode("utf-8")
 ↓
str
```

---

## 9.11 `encode()` e `decode()`

É importante memorizar a direção de cada operação:

```text
str
 ↓
encode()
 ↓
bytes
```

e:

```text
bytes
 ↓
decode()
 ↓
str
```

Exemplo:

```python
mensagem = "Olá"

dados = mensagem.encode("utf-8")

print(dados)
```

Resultado semelhante a:

```text
b'Ol\xc3\xa1'
```

Depois:

```python
texto = dados.decode("utf-8")

print(texto)
```

Resultado:

```text
Olá
```

Portanto:

```python
str → encode() → bytes
bytes → decode() → str
```

---

## 9.12 Um exemplo completo de envio e recebimento

### Servidor

```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("127.0.0.1", 4444))
server.listen()

client, address = server.accept()

data = client.recv(1024)

print("Cliente:", data.decode("utf-8"))

client.sendall(b"Mensagem recebida!")

client.close()
server.close()
```

### Cliente

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

client.connect(("127.0.0.1", 4444))

client.sendall(b"Ol\u00e1 servidor!")

data = client.recv(1024)

print("Servidor:", data.decode("utf-8"))

client.close()
```

A comunicação acontece aproximadamente assim:

```text
                 SERVIDOR
                    │
                    │
              socket()
                    │
                  bind()
                    │
                 listen()
                    │
                 accept()
                    │
                    │
                    │
CLIENTE             │
   │                │
socket()            │
   │                │
connect() ──────────┤
   │                │
   │                │
sendall() ─────────►│
   │                │
   │              recv()
   │                │
   │              processa
   │                │
recv() ◄────────────┤
   │              sendall()
   │                │
   │                │
 close()          close()
```

---

## 9.13 O socket utilizado para comunicação é o socket retornado por `accept()`

No servidor temos:

```python
server = socket.socket(...)
```

Depois:

```python
server.bind(...)
server.listen()
```

E:

```python
client, address = server.accept()
```

Agora temos dois sockets diferentes:

```text
server
   ↓
socket de escuta

client
   ↓
socket da conexão específica
```

É o socket retornado por `accept()` que normalmente utilizamos para trocar dados com aquele cliente:

```python
data = client.recv(1024)

client.sendall(b"Resposta")
```

Não fazemos:

```python
server.recv(1024)
```

para conversar com o cliente aceito.

O `server` continua sendo responsável por aceitar novas conexões.

---

## 9.14 TCP não preserva mensagens

Esse é um dos conceitos mais importantes desta parte.

Imagine que o cliente faça:

```python
client.sendall(b"Olá")
client.sendall(b"Mundo")
```

Não devemos assumir que o servidor receberá:

```python
b"Olá"
```

e depois:

```python
b"Mundo"
```

Ele pode receber:

```python
b"OláMundo"
```

ou:

```python
b"OlaMun"
```

e depois:

```python
b"do"
```

ou outras combinações.

Isso acontece porque TCP fornece um:

> **fluxo de bytes**

e não um sistema de mensagens.

Portanto:

```text
sendall("Olá")
sendall("Mundo")
        ↓
       TCP
        ↓
fluxo contínuo de bytes
        ↓
recv()
```

O TCP não sabe que `"Olá"` e `"Mundo"` eram duas mensagens diferentes da aplicação.

---

## 9.15 Um `send()` pode aparecer em vários `recv()`

Imagine:

```python
client.sendall(b"ABCDEFGHIJ")
```

O servidor pode fazer:

```python
data = client.recv(5)
```

e receber:

```text
b"ABCDE"
```

Depois:

```python
data = client.recv(5)
```

e receber:

```text
b"FGHIJ"
```

Portanto:

```text
SEND
ABCDEFGHIJ
     ↓
     TCP
     ↓
RECV
ABCDE

RECV
FGHIJ
```

Isso é perfeitamente válido.

---

## 9.16 Vários `send()` podem aparecer em um único `recv()`

O contrário também pode acontecer.

Cliente:

```python
client.sendall(b"ABC")
client.sendall(b"DEF")
```

Servidor:

```python
data = client.recv(1024)
```

Pode receber:

```python
b"ABCDEF"
```

Portanto:

```text
send("ABC")
send("DEF")
       ↓
      TCP
       ↓
recv()
       ↓
"ABCDEF"
```

Por isso, aplicações que precisam trabalhar com **mensagens separadas** precisam definir algum protocolo próprio.

Por exemplo:

```text
MENSAGEM\n
```

ou:

```text
[tamanho][dados]
```

ou algum formato estruturado como:

```text
JSON
```

Esse assunto será importante posteriormente para construir protocolos de comunicação próprios.

---

## 9.17 `recv()` é normalmente bloqueante

Por padrão, sockets Python são criados em modo bloqueante.

Isso significa que:

```python
data = client.recv(1024)
```

pode fazer o programa ficar aguardando.

Por exemplo:

```text
Programa
   │
   │ recv()
   ▼
┌───────────────┐
│ aguardando    │
│ dados         │
└───────────────┘
   │
   │ cliente envia
   ▼
dados recebidos
```

Se nenhum dado chegar, o programa pode permanecer bloqueado naquela chamada.

Exemplo:

```python
print("Antes")

data = client.recv(1024)

print("Depois")
```

Se o outro lado não enviar nada:

```text
Antes
   ↓
recv()
   ↓
[aguardando...]
```

O:

```python
print("Depois")
```

não será executado enquanto a chamada não prosseguir.

---

## 9.18 `recv()` pode retornar menos dados do que esperamos

Outro erro comum seria fazer:

```python
data = client.recv(1024)
```

e pensar:

> "Agora recebi toda a mensagem."

Isso não é garantido no TCP.

Se o protocolo da aplicação espera receber, por exemplo, 10.000 bytes, não podemos simplesmente assumir:

```python
data = client.recv(10000)
```

e esperar necessariamente obter os 10.000 bytes.

Precisamos definir uma lógica para determinar:

> **quando a mensagem terminou.**

Uma estratégia simples seria conhecer antecipadamente o tamanho:

```text
Mensagem possui 5000 bytes
       ↓
receber até acumular 5000 bytes
```

Outra estratégia é utilizar um delimitador:

```text
Olá servidor!\n
```

A aplicação continua recebendo até encontrar:

```text
\n
```

Outra possibilidade é utilizar um protocolo estruturado que informe o tamanho da mensagem.

---

## 9.19 O que significa `b''` em `recv()`?

Um comportamento muito importante:

Se:

```python
data = client.recv(1024)
```

retornar:

```python
b""
```

isso normalmente significa que o outro lado **encerrou a conexão de forma ordenada**.

Exemplo:

```python
while True:
    data = client.recv(1024)

    if data == b"":
        break

    print(data)
```

Podemos interpretar:

```text
recv()
  ↓
dados?
  ├── sim → processa
  │
  └── b"" → conexão encerrada
```

Isso é diferente de:

```python
None
```

`recv()` retorna um objeto `bytes`.

Quando retorna:

```python
b""
```

temos uma sequência de bytes vazia indicando o fechamento ordenado da conexão TCP pelo peer.

---

## 9.20 Exemplo de servidor recebendo continuamente

Podemos utilizar um loop:

```python
while True:
    data = client.recv(1024)

    if data == b"":
        break

    print(data.decode("utf-8"))
```

O comportamento é:

```text
             recv()
               │
               ▼
          recebeu dados?
          /           \
        sim            não
         │              │
         ▼              ▼
     processa          b""
         │              │
         │              ▼
         │          encerra loop
         │
         └──────► recv()
```

Esse padrão é muito comum em servidores TCP.

---

## 9.21 Erros comuns durante envio e recebimento

### `BrokenPipeError`

Pode acontecer quando tentamos enviar dados para uma conexão que já foi fechada pelo outro lado.

Exemplo conceitual:

```text
Cliente
  │
  │ fecha conexão
  ▼
Servidor
  │
  │ sendall()
  ▼
BrokenPipeError
```

---

### `ConnectionResetError`

Pode ocorrer quando o outro lado encerra a conexão de forma abrupta.

Exemplo:

```text
Cliente
  │
  │ conexão abruptamente encerrada
  ▼
Servidor
  │
  │ recv()
  ▼
ConnectionResetError
```

---

### `TimeoutError`

Pode ocorrer quando configuramos um timeout e a operação demora além do limite definido.

Por exemplo:

```python
client.settimeout(5)
```

Nesse caso, uma operação bloqueante pode falhar por timeout se não houver progresso dentro do período configurado.

---

## 9.22 `send()` e `recv()` pertencem à conexão, não ao endereço

Depois que uma conexão TCP foi estabelecida:

```python
client.connect(("127.0.0.1", 4444))
```

podemos utilizar:

```python
client.sendall(...)
```

e:

```python
client.recv(...)
```

O endereço:

```python
("127.0.0.1", 4444)
```

foi utilizado para estabelecer a conexão.

Depois disso, a comunicação acontece através do socket.

Podemos pensar:

```text
connect()
    ↓
estabelece conexão
    ↓
socket conectado
    ↓
┌───────────────┐
│ send / recv   │
│ sendall       │
└───────────────┘
```

Não precisamos informar novamente:

```python
("127.0.0.1", 4444)
```

a cada envio.

---

## 9.23 Modelo mental completo desta parte

Até aqui, o fluxo TCP em Python pode ser visualizado assim:

```text
                SERVIDOR
                   │
              socket()
                   │
                 bind()
                   │
                listen()
                   │
                accept()
                   │
                   │
                   │
CLIENTE            │
   │               │
socket()           │
   │               │
connect() ─────────┤
   │               │
   │               │
sendall() ────────►│
   │             recv()
   │               │
   │               │
   │               │
recv() ◄───────────┤
   │             sendall()
   │               │
   │               │
 close()         close()
```

E a transformação dos dados:

```text
Cliente
   │
   │ str
   ▼
encode()
   │
   │ bytes
   ▼
sendall()
   │
   ▼
======== TCP ========
   │
   ▼
recv()
   │
   │ bytes
   ▼
decode()
   │
   ▼
Servidor
   │
   │ str
```

---

## Resumo da Parte

### `send()`

Envia dados e retorna a quantidade de bytes enviados.

```python
enviados = socket.send(data)
```

Pode enviar apenas uma parte dos dados.

### `sendall()`

Continua enviando até que todos os dados sejam enviados ou ocorra um erro.

```python
socket.sendall(data)
```

Em caso de sucesso:

```python
None
```

### `recv()`

Recebe até a quantidade máxima de bytes especificada.

```python
data = socket.recv(1024)
```

Não significa que exatamente 1024 bytes serão recebidos.

### `encode()`

Converte:

```text
str → bytes
```

Exemplo:

```python
mensagem.encode("utf-8")
```

### `decode()`

Converte:

```text
bytes → str
```

Exemplo:

```python
data.decode("utf-8")
```

### TCP não preserva mensagens

```text
send()
send()
   ↓
fluxo contínuo de bytes
   ↓
recv()
```

Por isso, a aplicação precisa definir como identificar o início e o fim das mensagens.

### `b""`

Se `recv()` retornar:

```python
b""
```

normalmente significa que o outro lado encerrou a conexão de forma ordenada.

### Conceito principal

> **TCP entrega um fluxo de bytes. `send()`/`sendall()` colocam bytes nesse fluxo e `recv()` retira bytes desse fluxo. O TCP não sabe onde uma mensagem da aplicação começa ou termina.**

---

# 10. Encerrando conexões

Depois de estabelecer uma conexão e trocar dados, chega o momento de encerrá-la.

Em Python, existem principalmente dois métodos relacionados ao encerramento de um socket:

```python
socket.close()
```

e:

```python
socket.shutdown()
```

Apesar de ambos estarem relacionados ao encerramento, eles possuem **funções diferentes**.

O modelo mental inicial é:

```text
shutdown()
    ↓
controla como a comunicação será encerrada

close()
    ↓
fecha o socket localmente
```

---

## 10.1 `close()`

O método `close()` fecha o socket.

Sintaxe:

```python
socket.close()
```

Exemplo:

```python
client.close()
```

Depois disso, aquele socket não deve mais ser utilizado para comunicação.

Um fluxo simples pode ser:

```python
client.sendall(b"Olá servidor!")

data = client.recv(1024)

client.close()
```

O fluxo é:

```text
socket conectado
      ↓
envia dados
      ↓
recebe dados
      ↓
close()
      ↓
socket fechado
```

---

## 10.2 O que acontece quando usamos `close()`?

Quando chamamos:

```python
client.close()
```

estamos informando ao sistema operacional que a aplicação terminou de utilizar aquele socket.

Podemos imaginar:

```text
Aplicação
   │
   │ close()
   ▼
Socket
   │
   ▼
Sistema operacional
   │
   ▼
recursos liberados
```

Isso é importante porque sockets consomem recursos do sistema operacional.

Assim como devemos fechar arquivos depois de utilizá-los:

```python
arquivo.close()
```

também devemos fechar sockets quando não precisamos mais deles:

```python
socket.close()
```

---

## 10.3 `close()` não é apenas "desconectar"

É comum pensar:

> `close()` simplesmente desconecta o socket.

A ideia é próxima, mas tecnicamente o método também encerra o **descritor/recurso local associado ao socket**.

Em sistemas Unix/Linux, o socket está associado a um descritor de arquivo.

Por isso:

```python
client.close()
```

faz com que o programa deixe de possuir aquele socket como um recurso utilizável.

---

## 10.4 `close()` depois de `accept()`

No servidor TCP podemos ter:

```python
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("127.0.0.1", 4444))
server.listen()

client, address = server.accept()
```

Agora temos dois sockets:

```text
server
   ↓
socket de escuta

client
   ↓
socket da conexão
```

Podemos fechar apenas a conexão com aquele cliente:

```python
client.close()
```

enquanto mantemos:

```python
server
```

aberto.

Assim:

```text
server
   │
   ├── continua escutando
   │
   └── client.close()
          ↓
      cliente atual desconectado
```

Isso é extremamente importante em servidores que atendem vários clientes.

---

## 10.5 Fechar o socket do cliente não significa fechar o servidor

Por exemplo:

```python
client.close()
```

não significa:

```text
servidor inteiro encerrado
```

Significa:

```text
socket daquela conexão
        ↓
       fechado
```

O socket:

```python
server
```

pode continuar funcionando.

Exemplo:

```python
while True:
    client, address = server.accept()

    data = client.recv(1024)

    print(data)

    client.sendall(b"OK")

    client.close()
```

Aqui:

```text
server
  │
  ├── accept()
  │
  ├── atende cliente
  │
  ├── close() conexão
  │
  └── volta para accept()
```

Esse padrão permite que o servidor continue aceitando novos clientes.

---

## 10.6 Fechando o socket do servidor

Quando o servidor realmente terminar:

```python
server.close()
```

Exemplo:

```python
client.close()
server.close()
```

Podemos pensar:

```text
client.close()
    ↓
fecha conexão específica

server.close()
    ↓
fecha socket de escuta
```

Depois de:

```python
server.close()
```

o servidor não poderá continuar utilizando aquele socket para:

```python
server.accept()
```

---

## 10.7 `shutdown()`

O método `shutdown()` possui uma finalidade diferente.

Sintaxe:

```python
socket.shutdown(how)
```

O parâmetro `how` determina **qual direção da comunicação será encerrada**.

Os valores normalmente utilizados são:

```python
socket.SHUT_RD
socket.SHUT_WR
socket.SHUT_RDWR
```

---

## 10.8 `SHUT_RD`

```python
socket.shutdown(socket.SHUT_RD)
```

Indica que a aplicação não deseja mais receber dados através daquele socket.

Podemos visualizar:

```text
        SOCKET

   envio       recebimento
     │              │
     │              X
     │         bloqueado
```

Ou:

```text
SHUT_RD
   ↓
encerra a direção de leitura
```

---

## 10.9 `SHUT_WR`

```python
socket.shutdown(socket.SHUT_WR)
```

Indica que a aplicação não deseja mais enviar dados.

Visualmente:

```text
        SOCKET

   envio       recebimento
     X              │
 bloqueado           │
```

Ou:

```text
SHUT_WR
   ↓
encerra a direção de escrita
```

Isso é útil quando queremos dizer:

> "Terminei de enviar dados, mas ainda quero receber dados."

---

## 10.10 `SHUT_RDWR`

```python
socket.shutdown(socket.SHUT_RDWR)
```

Encerra ambas as direções:

```text
        SOCKET

   envio       recebimento
     X              X
```

Ou:

```text
SHUT_RDWR
    ↓
encerra leitura + escrita
```

---

## 10.11 `shutdown()` não é igual a `close()`

Essa diferença é importante.

### `shutdown()`

Controla a comunicação:

```text
shutdown()
    ↓
encerra leitura/escrita
```

### `close()`

Fecha o socket localmente:

```text
close()
   ↓
libera o socket/recurso
```

Podemos resumir:

|Método|Função principal|
|---|---|
|`shutdown()`|Controla o encerramento das direções de comunicação|
|`close()`|Fecha o socket e libera o recurso local|

---

## 10.12 Um exemplo de `shutdown(SHUT_WR)`

Imagine um protocolo em que o cliente envia vários dados e depois precisa informar ao servidor:

> "Terminei de enviar, agora vou apenas receber."

Podemos fazer:

```python
client.sendall(b"Primeira parte")
client.sendall(b"Segunda parte")

client.shutdown(socket.SHUT_WR)

data = client.recv(1024)
```

O fluxo é:

```text
CLIENTE
   │
   │ send
   ▼
SERVIDOR
   │
   │ send
   ▼
CLIENTE
   │
   │ shutdown(SHUT_WR)
   ▼
fim dos envios
   │
   │ ainda pode receber
   ▼
recv()
```

Isso é chamado de **half-close** ou encerramento parcial da conexão.

---

## 10.13 Half-close

Uma conexão TCP possui duas direções independentes:

```text
CLIENTE ───────────────► SERVIDOR
        direção de envio

CLIENTE ◄─────────────── SERVIDOR
        direção de recebimento
```

Podemos encerrar apenas uma delas.

Por exemplo:

```python
client.shutdown(socket.SHUT_WR)
```

Isso significa:

```text
CLIENTE ───────X───────► SERVIDOR
        envio encerrado

CLIENTE ◄─────────────── SERVIDOR
        recebimento continua
```

O cliente não enviará mais dados, mas ainda poderá receber.

Essa característica pode ser útil em protocolos específicos.

---

## 10.14 Por que não usar somente `close()`?

Na maioria dos programas simples:

```python
client.close()
```

é suficiente.

Não precisamos necessariamente utilizar:

```python
client.shutdown(...)
```

antes de todo `close()`.

Por exemplo:

```python
client.sendall(b"Olá")

data = client.recv(1024)

client.close()
```

é perfeitamente normal.

`shutdown()` se torna interessante quando precisamos controlar **separadamente** o encerramento do envio e do recebimento.

---

## 10.15 `with` para fechar automaticamente

Assim como arquivos podem ser utilizados com:

```python
with open(...) as arquivo:
    ...
```

sockets também podem ser utilizados com `with`.

Exemplo:

```python
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as client:
    client.connect(("127.0.0.1", 4444))

    client.sendall(b"Olá servidor!")

    data = client.recv(1024)
```

Quando o bloco termina, o socket é fechado automaticamente.

Podemos visualizar:

```text
with socket(...)
       │
       ▼
socket criado
       │
       ▼
operações
       │
       ▼
fim do bloco
       │
       ▼
socket fechado
```

Isso reduz a chance de esquecer:

```python
client.close()
```

---

## 10.16 Exemplo com servidor utilizando `with`

Também podemos utilizar:

```python
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server:
    server.bind(("127.0.0.1", 4444))
    server.listen()

    client, address = server.accept()

    with client:
        data = client.recv(1024)

        print(data.decode("utf-8"))

        client.sendall(b"Mensagem recebida!")
```

Aqui existem dois contextos:

```text
with server
    │
    ├── bind()
    ├── listen()
    └── accept()
          │
          ▼
      with client
          │
          ├── recv()
          ├── sendall()
          │
          ▼
      client fechado
    │
    ▼
server fechado
```

Essa abordagem deixa explícito o ciclo de vida dos sockets.

---

## 10.17 O ciclo de vida de um socket TCP

Agora podemos visualizar praticamente tudo que aprendemos até aqui.

### Servidor

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
accept()
   │
   ▼
recv() / sendall()
   │
   ▼
close()
```

### Cliente

```text
socket()
   │
   ▼
connect()
   │
   ▼
sendall() / recv()
   │
   ▼
close()
```

Em uma representação conjunta:

```text
                    SERVIDOR
                       │
                    socket()
                       │
                     bind()
                       │
                    listen()
                       │
                    accept()
                       │
                       │
                       │
CLIENTE                │
   │                   │
socket()               │
   │                   │
connect() ─────────────┤
   │                   │
   │                   │
sendall() ────────────►│
   │                 recv()
   │                   │
   │                   │
recv() ◄───────────────┤
   │                 sendall()
   │                   │
   │                   │
close()             close()
```

---

## 10.18 O erro de esquecer o `close()`

Em programas pequenos, esquecer um `close()` pode não parecer importante.

Porém, em aplicações que criam muitas conexões, isso pode causar problemas.

Por exemplo:

```text
conexão 1 → não fechada
conexão 2 → não fechada
conexão 3 → não fechada
conexão 4 → não fechada
...
```

Cada socket utiliza recursos do sistema operacional.

Com muitas conexões abertas:

```text
recursos disponíveis
       ↓
      ↓↓↓
podem se esgotar
```

Por isso devemos tratar corretamente o ciclo de vida dos sockets.

---

## 10.19 Erro: utilizar um socket depois de fechá-lo

Depois de:

```python
client.close()
```

não devemos tentar:

```python
client.sendall(b"Olá")
```

ou:

```python
client.recv(1024)
```

porque aquele socket já foi fechado.

Conceitualmente:

```text
socket aberto
     │
     ▼
  close()
     │
     ▼
socket fechado
     │
     X
send()/recv()
```

Isso pode resultar em um erro como:

```text
OSError
```

dependendo da operação e do estado do socket.

---

## 10.20 `close()` e o problema da porta continuar aparecendo

Como já vimos anteriormente, um servidor pode apresentar:

```text
OSError: [Errno 98] Address already in use
```

quando tenta executar:

```python
server.bind(("127.0.0.1", 4444))
```

Isso pode acontecer por vários motivos, inclusive porque outra aplicação ainda está utilizando a porta ou porque uma conexão anterior deixou o endereço em um estado relacionado ao encerramento TCP.

Podemos verificar:

```bash
ss -ltnp | grep :4444
```

ou:

```bash
lsof -i :4444
```

O ponto importante é:

> fechar um socket não significa que todas as características relacionadas à conexão TCP desaparecem instantaneamente da rede.

O TCP possui estados de conexão e mecanismos próprios de encerramento.

Esses estados serão estudados com mais profundidade posteriormente.

---

## 10.21 O modelo mental definitivo de encerramento

Podemos resumir o conceito desta seção:

```text
shutdown()
    │
    ├── SHUT_RD
    │      ↓
    │   para leitura
    │
    ├── SHUT_WR
    │      ↓
    │   para escrita
    │
    └── SHUT_RDWR
           ↓
      para ambos
```

Enquanto:

```text
close()
   ↓
fecha o socket local
   ↓
libera o recurso
```

E, para um programa simples:

```python
client.close()
```

normalmente é suficiente.

---

## Resumo da Parte

### `close()`

Fecha o socket:

```python
socket.close()
```

É o método utilizado normalmente quando terminamos de utilizar uma conexão.

### `shutdown()`

Controla o encerramento das direções de comunicação:

```python
socket.shutdown(socket.SHUT_RD)
socket.shutdown(socket.SHUT_WR)
socket.shutdown(socket.SHUT_RDWR)
```

### `SHUT_RD`

Encerra a direção de recebimento.

### `SHUT_WR`

Encerra a direção de envio.

### `SHUT_RDWR`

Encerra ambas.

### Half-close

Permite encerrar apenas uma direção da conexão:

```python
client.shutdown(socket.SHUT_WR)
```

O socket deixa de enviar, mas ainda pode receber.

### `with`

Pode ser utilizado para garantir o fechamento automático:

```python
with socket.socket(...) as client:
    ...
```

### Ciclo básico

```text
SERVIDOR:

socket()
  ↓
bind()
  ↓
listen()
  ↓
accept()
  ↓
send() / recv()
  ↓
close()
```

```text
CLIENTE:

socket()
  ↓
connect()
  ↓
send() / recv()
  ↓
close()
```

### Conceito principal

> **`shutdown()` controla quais direções da comunicação serão encerradas, enquanto `close()` encerra o socket local e libera o recurso utilizado pela aplicação.**

---

# 11. O ciclo de vida de uma conexão TCP

Até agora vimos como criar sockets, associá-los a endereços, colocar um servidor em escuta, aceitar conexões, conectar um cliente e trocar dados.

Agora precisamos entender o que acontece **por baixo dessas chamadas**.

Uma conexão TCP não simplesmente passa de:

```text
desconectado
    ↓
conectado
```

Ela passa por diferentes **estados internos**.

Esses estados fazem parte do funcionamento do próprio protocolo TCP.

---

## 11.1 Por que existem estados TCP?

O TCP precisa controlar várias informações durante a vida de uma conexão.

Por exemplo:

- se a conexão está sendo estabelecida;
    
- se já foi estabelecida;
    
- se uma das partes começou a encerrá-la;
    
- se ambas as partes terminaram;
    
- se ainda existem dados aguardando confirmação;
    
- se o socket está aguardando uma conexão.
    

Por isso, o TCP utiliza uma **máquina de estados**.

Podemos imaginar:

```text
        conexão sendo criada
                 │
                 ▼
          estabelecimento
                 │
                 ▼
          ESTABLISHED
                 │
                 │
          troca de dados
                 │
                 ▼
          encerramento
                 │
                 ▼
            CLOSED
```

Na prática existem vários estados intermediários.

---

## 11.2 O estado `CLOSED`

`CLOSED` representa a ausência de uma conexão TCP ativa naquele endpoint.

Não significa necessariamente que o objeto Python:

```python
socket
```

não exista.

São conceitos diferentes.

Por exemplo:

```python
client = socket.socket(...)
```

cria um objeto socket em Python.

Mas isso não significa que ele já tenha uma conexão TCP estabelecida.

Podemos ter:

```text
objeto socket criado
        ↓
nenhuma conexão TCP
        ↓
CLOSED
```

Portanto:

> **socket Python criado ≠ conexão TCP estabelecida.**

---

## 11.3 O estado `LISTEN`

O estado `LISTEN` está relacionado ao servidor TCP.

Quando fazemos:

```python
server.bind(("127.0.0.1", 4444))
server.listen()
```

o socket passa a atuar como um socket de escuta.

Podemos visualizar:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
LISTEN
```

Nesse estado, o servidor está preparado para receber solicitações de conexão.

Exemplo:

```text
SERVIDOR

127.0.0.1:4444
      │
      ▼
   LISTEN
      │
      │
      ├── cliente 1
      ├── cliente 2
      └── cliente 3
```

---

## 11.4 `LISTEN` não significa que existe um cliente conectado

Isso é importante.

Quando fazemos:

```python
server.listen()
```

o servidor está preparado para conexões.

Mas ainda pode não existir nenhum cliente conectado.

Podemos ter:

```text
SERVIDOR
   │
   ▼
LISTEN
   │
   │
   └── nenhum cliente
```

Quando um cliente tenta se conectar:

```text
CLIENTE
   │
   │ connect()
   ▼
SERVIDOR
   │
   ▼
LISTEN
```

o TCP começa o processo de estabelecimento da conexão.

---

## 11.5 `SYN-SENT`

No lado do cliente, quando fazemos:

```python
client.connect(("127.0.0.1", 4444))
```

o TCP precisa iniciar o estabelecimento da conexão.

O cliente envia um segmento TCP contendo a flag:

```text
SYN
```

O cliente pode entrar no estado:

```text
SYN-SENT
```

Podemos imaginar:

```text
CLIENTE
   │
   │ SYN
   ├────────────────►
   │
   │
SYN-SENT
```

O significado é aproximadamente:

> "Enviei uma solicitação de sincronização e estou aguardando uma resposta."

---

## 11.6 `SYN-RECEIVED`

O servidor recebe o `SYN`.

Ele responde com:

```text
SYN + ACK
```

Nesse processo, o lado servidor pode entrar no estado:

```text
SYN-RECEIVED
```

Visualmente:

```text
CLIENTE                         SERVIDOR

SYN ───────────────────────────►
                                │
                                ▼
                           SYN-RECEIVED
                                │
SYN-ACK ◄───────────────────────┤
```

Agora o servidor está aguardando a confirmação final do cliente.

---

## 11.7 `ESTABLISHED`

O cliente recebe o:

```text
SYN-ACK
```

e envia:

```text
ACK
```

Temos então o famoso **three-way handshake**:

```text
CLIENTE                         SERVIDOR

SYN ───────────────────────────►

     ◄──────────────────── SYN-ACK

ACK ───────────────────────────►

       CONEXÃO ESTABELECIDA
```

Depois disso:

```text
CLIENTE                         SERVIDOR

ESTABLISHED ◄───────────────► ESTABLISHED
```

Agora ambos podem trocar dados.

---

## 11.8 O que `connect()` representa nesse processo?

Quando escrevemos:

```python
client.connect(("127.0.0.1", 4444))
```

não significa simplesmente:

> "marcar o socket como conectado."

O Python solicita ao sistema operacional que estabeleça uma conexão TCP.

O sistema operacional participa do handshake.

Simplificando:

```text
Python
  │
  │ connect()
  ▼
Sistema operacional
  │
  │ TCP
  ▼
rede
  │
  ▼
servidor
```

Por isso `connect()` pode bloquear enquanto o sistema operacional tenta estabelecer a conexão.

---

## 11.9 `accept()` e o estabelecimento da conexão

No servidor temos:

```python
client, address = server.accept()
```

É importante entender que o `accept()` não realiza sozinho todo o handshake TCP.

O kernel já participa do processamento das conexões recebidas.

O `accept()` permite que a aplicação obtenha uma conexão que foi estabelecida e está disponível para ser atendida.

Podemos visualizar:

```text
Cliente
   │
   │ SYN
   ▼
Kernel do servidor
   │
   │ SYN-ACK
   ▼
Cliente
   │
   │ ACK
   ▼
Kernel do servidor
   │
   ▼
fila de conexões
   │
   ▼
accept()
   │
   ▼
aplicação Python
```

Isso explica por que existe uma diferença entre:

```python
server
```

e:

```python
client
```

retornado por:

```python
client, address = server.accept()
```

---

## 11.10 O socket de escuta e o socket da conexão

O servidor possui:

```python
server = socket.socket(...)
```

Depois:

```python
server.bind(...)
server.listen()
```

Esse socket representa o endpoint que está aguardando conexões.

Quando:

```python
client, address = server.accept()
```

é executado, recebemos outro socket.

Visualmente:

```text
                 SERVIDOR

        socket de escuta
               │
               │ LISTEN
               │
               ▼
          server.accept()
               │
               ├──────────────► client 1
               │
               ├──────────────► client 2
               │
               └──────────────► client 3
```

Cada conexão aceita possui seu próprio socket para comunicação.

---

## 11.11 Uma conexão TCP é identificada por quatro informações

Uma conexão TCP pode ser identificada pelo conjunto:

```text
IP de origem
porta de origem
IP de destino
porta de destino
```

Por exemplo:

```text
Cliente:
192.168.1.10:53021

Servidor:
192.168.1.20:4444
```

Temos:

```text
192.168.1.10:53021
        │
        │ TCP
        ▼
192.168.1.20:4444
```

Podemos representar a conexão como:

```text
(src IP, src port, dst IP, dst port)
```

ou:

```text
(192.168.1.10, 53021,
 192.168.1.20, 4444)
```

Esse conjunto é conhecido como **four-tuple**.

---

## 11.12 Por que vários clientes podem usar a mesma porta do servidor?

Imagine um servidor:

```text
192.168.1.20:4444
```

recebendo conexões:

```text
192.168.1.10:53021
192.168.1.11:53022
192.168.1.12:53023
```

Todas estão conectadas à:

```text
192.168.1.20:4444
```

Isso é possível porque as conexões possuem combinações diferentes de origem e destino.

Visualmente:

```text
192.168.1.10:53021 ─────► 192.168.1.20:4444
192.168.1.11:53022 ─────► 192.168.1.20:4444
192.168.1.12:53023 ─────► 192.168.1.20:4444
```

O servidor consegue distinguir as conexões.

---

## 11.13 A porta do cliente normalmente é temporária

Quando fazemos:

```python
client.connect(("192.168.1.20", 4444))
```

normalmente não especificamos:

```python
client.bind(("192.168.1.10", 53021))
```

O sistema operacional escolhe uma **porta efêmera** para o cliente.

Podemos ter:

```text
Cliente
192.168.1.10:53021
        │
        ▼
Servidor
192.168.1.20:4444
```

Na próxima conexão, o cliente pode utilizar outra porta:

```text
192.168.1.10:53022
```

Isso permite que várias conexões sejam diferenciadas.

---

## 11.14 `TIME_WAIT`

Durante o encerramento de uma conexão TCP, podemos encontrar o estado:

```text
TIME_WAIT
```

Esse estado é importante para o funcionamento correto do TCP.

Ele ajuda a evitar que segmentos antigos de uma conexão anterior sejam confundidos com segmentos pertencentes a uma nova conexão.

Visualmente:

```text
conexão ativa
     │
     ▼
encerramento
     │
     ▼
 TIME_WAIT
     │
     │ aguarda período
     ▼
 CLOSED
```

Por isso, depois de fechar um servidor, podemos observar algo relacionado à porta por algum tempo.

Por exemplo:

```bash
ss -tan
```

pode mostrar estados como:

```text
TIME-WAIT
```

---

## 11.15 `TIME_WAIT` não significa que o programa ainda está executando

Isso é muito importante.

Imagine que executamos:

```python
server.close()
```

e depois verificamos:

```bash
ss -tan
```

Podemos encontrar:

```text
TIME-WAIT
```

Isso não significa necessariamente que:

```text
python server.py
```

continua executando.

São coisas diferentes:

```text
processo Python
      ↓
pode ter terminado

estado TCP
      ↓
pode permanecer temporariamente
```

---

## 11.16 Por que o TCP possui estados de encerramento?

Uma conexão TCP precisa ser encerrada de maneira coordenada.

Diferentemente de simplesmente remover um objeto Python da memória, os dois lados da conexão precisam trocar informações para que cada endpoint saiba o que está acontecendo.

Um encerramento normal pode ser simplificado como:

```text
CLIENTE                         SERVIDOR

FIN ──────────────────────────►

     ◄────────────────────── ACK

     ◄────────────────────── FIN

ACK ──────────────────────────►

       conexão encerrada
```

Aqui aparece outra flag importante:

```text
FIN
```

que indica que aquele lado terminou seu envio.

---

## 11.17 `FIN` representa encerramento de uma direção

Lembre-se de que TCP possui comunicação bidirecional.

```text
CLIENTE ───────────────► SERVIDOR
CLIENTE ◄─────────────── SERVIDOR
```

Quando um lado envia:

```text
FIN
```

ele está indicando que terminou de enviar dados naquela direção.

Isso está relacionado diretamente ao conceito de:

```python
shutdown(socket.SHUT_WR)
```

Portanto:

```text
shutdown(SHUT_WR)
        ↓
indica fim do envio
        ↓
TCP pode realizar o encerramento daquela direção
```

O `close()` também participa do encerramento do socket, mas o comportamento completo depende do estado da conexão e do sistema operacional.

---

## 11.18 Estados de encerramento

Existem vários estados relacionados ao encerramento, entre eles:

```text
FIN-WAIT-1
FIN-WAIT-2
CLOSE-WAIT
CLOSING
LAST-ACK
TIME-WAIT
CLOSED
```

Não é necessário decorar todos imediatamente.

O mais importante neste momento é compreender a ideia:

```text
ESTABLISHED
      │
      │ encerramento
      ▼
estados de fechamento
      │
      ▼
TIME-WAIT
      │
      ▼
CLOSED
```

Posteriormente, esses estados podem ser estudados individualmente quando forem necessários para análise de rede.

---

## 11.19 `CLOSE-WAIT`

Um estado particularmente interessante é:

```text
CLOSE-WAIT
```

Ele pode aparecer quando o outro lado já encerrou sua direção de envio, mas a aplicação local ainda não fechou seu socket.

Podemos imaginar:

```text
CLIENTE                         SERVIDOR

FIN ──────────────────────────►
                                │
                                ▼
                           CLOSE-WAIT
```

Isso pode ser importante na análise de servidores.

Se um processo apresentar muitas conexões permanentemente em:

```text
CLOSE-WAIT
```

pode existir um problema na aplicação que não está encerrando corretamente os sockets.

---

## 11.20 Verificando estados TCP no Linux

Como estamos trabalhando com Linux, podemos observar as conexões usando:

```bash
ss -tan
```

Por exemplo:

```text
State       Local Address:Port      Peer Address:Port
LISTEN      127.0.0.1:4444         0.0.0.0:*
ESTAB       127.0.0.1:4444         127.0.0.1:53021
```

Aqui:

```text
LISTEN
```

indica um socket aguardando conexões.

Enquanto:

```text
ESTAB
```

é a abreviação normalmente exibida pelo `ss` para:

```text
ESTABLISHED
```

Podemos também utilizar:

```bash
ss -tanp
```

O `-p` pode mostrar informações sobre o processo associado, quando disponíveis.

---

## 11.21 Observando um servidor Python

Imagine este servidor:

```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("127.0.0.1", 4444))
server.listen()

print("Servidor aguardando...")

client, address = server.accept()

print("Cliente conectado:", address)

data = client.recv(1024)

print(data.decode())

client.close()
server.close()
```

Enquanto o servidor estiver parado em:

```python
server.accept()
```

podemos verificar:

```bash
ss -ltn
```

e encontrar algo semelhante a:

```text
LISTEN  0  128  127.0.0.1:4444
```

Isso mostra que a porta está em estado de escuta.

---

## 11.22 Depois que o cliente conecta

Quando um cliente executa:

```python
client.connect(("127.0.0.1", 4444))
```

podemos observar uma conexão estabelecida:

```bash
ss -tan
```

Podemos encontrar algo semelhante a:

```text
ESTAB  0  0  127.0.0.1:4444   127.0.0.1:53021
ESTAB  0  0  127.0.0.1:53021  127.0.0.1:4444
```

Temos então:

```text
127.0.0.1:53021
        │
        │ TCP
        ▼
127.0.0.1:4444
```

A porta:

```text
4444
```

é a porta do servidor.

A porta:

```text
53021
```

é uma porta efêmera escolhida para aquela conexão do cliente.

---

## 11.23 Relação entre Python e os estados TCP

É importante separar três camadas:

```text
APLICAÇÃO
    │
    │ Python
    ▼
SOCKET / SISTEMA OPERACIONAL
    │
    │ TCP
    ▼
REDE
```

Quando fazemos:

```python
client.connect(...)
```

estamos utilizando uma API Python.

Por baixo:

```text
Python
  ↓
socket API
  ↓
kernel
  ↓
TCP
  ↓
rede
```

Da mesma forma:

```python
client.sendall(...)
```

não envia diretamente um pacote Ethernet.

A aplicação entrega dados ao sistema operacional, e o kernel/TCP cuida das etapas necessárias para transportá-los pela rede.

---

## 11.24 Modelo mental completo do ciclo TCP

Podemos reunir tudo:

```text
                    SERVIDOR

                 socket()
                    │
                    ▼
                  bind()
                    │
                    ▼
                 LISTEN
                    │
                    │
                    │
                    │ SYN
                    │◄──────────────── CLIENTE
                    │                  socket()
                    │                  connect()
                    │
                SYN-RECEIVED
                    │
                    │ SYN-ACK
                    ├────────────────►
                    │
                    │                  ACK
                    ◄──────────────────
                    │
                    ▼
               ESTABLISHED
                    │
                    │
             send / recv
                    │
                    │
                    ▼
                encerramento
                    │
                    ▼
              estados TCP
                    │
                    ▼
                TIME-WAIT
                    │
                    ▼
                 CLOSED
```

---

## Resumo da Parte

### Principais estados

```text
CLOSED
   ↓
LISTEN
   ↓
SYN-RECEIVED
   ↓
ESTABLISHED
   ↓
encerramento
   ↓
TIME-WAIT
   ↓
CLOSED
```

No lado cliente, durante a conexão, também podemos encontrar:

```text
SYN-SENT
```

### `LISTEN`

Indica que o servidor está aguardando conexões.

### `SYN-SENT`

Indica que o cliente enviou um `SYN` e está aguardando resposta.

### `SYN-RECEIVED`

Indica que o servidor recebeu o `SYN` e respondeu com `SYN-ACK`, aguardando a confirmação final.

### `ESTABLISHED`

Indica que a conexão TCP foi estabelecida e os dois lados podem trocar dados.

### `FIN`

É utilizado no encerramento de uma direção da conexão.

### `TIME-WAIT`

Estado temporário utilizado durante o encerramento da conexão TCP.

### `CLOSE-WAIT`

Pode aparecer quando o outro lado já encerrou sua direção de envio, mas a aplicação local ainda não fechou a conexão.

### Four-tuple

Uma conexão TCP pode ser identificada por:

```text
IP origem
porta origem
IP destino
porta destino
```

Por exemplo:

```text
192.168.1.10:53021
        ↓
192.168.1.20:4444
```

### Comando útil no Linux

```bash
ss -tan
```

Para observar estados TCP.

E:

```bash
ss -tanp
```

para obter também informações de processos quando disponíveis.

### Conceito principal

> **Uma conexão TCP possui um ciclo de vida controlado por uma máquina de estados. As chamadas Python como `connect()`, `listen()`, `accept()`, `send()`, `recv()` e `close()` são a interface da aplicação com o sistema operacional, enquanto o TCP mantém os estados e controla o estabelecimento, a transferência e o encerramento da conexão.**

---

# 12. Bloqueio, timeout e modos de operação

Até agora, vimos que várias operações de socket podem ficar aguardando.

Por exemplo:

```python
data = client.recv(1024)
```

pode esperar até que existam dados disponíveis.

Da mesma forma:

```python
client.connect(("127.0.0.1", 4444))
```

pode aguardar enquanto o sistema operacional tenta estabelecer a conexão.

Esse comportamento é chamado de **bloqueio** (_blocking_).

Entender bloqueio, timeout e modo não bloqueante é fundamental para construir servidores e clientes que não fiquem presos indefinidamente esperando uma operação.

---

## 12.1 O que significa uma operação bloqueante?

Uma operação bloqueante é uma operação que pode fazer o programa **esperar** até que alguma condição seja satisfeita.

Por exemplo:

```python
data = client.recv(1024)
```

Se nenhum dado estiver disponível, a execução pode ficar parada nessa linha.

Podemos visualizar:

```text
Programa
   │
   ▼
recv(1024)
   │
   ▼
aguardando dados
   │
   │
   │ dados chegam
   ▼
continua execução
```

Enquanto o `recv()` estiver bloqueado, as próximas instruções daquele fluxo de execução não serão executadas.

---

## 12.2 O comportamento padrão dos sockets

Por padrão, um socket Python é criado em **modo bloqueante**.

Por exemplo:

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

Depois:

```python
data = client.recv(1024)
```

Se não houver dados disponíveis, o programa poderá esperar.

Isso é conveniente para programas simples.

Por exemplo:

```python
data = client.recv(1024)

print("Mensagem recebida:", data)
```

Podemos interpretar:

```text
recv()
  ↓
espera
  ↓
dados chegam
  ↓
retorna
  ↓
print()
```

---

## 12.3 Por que o bloqueio pode ser útil?

O bloqueio nem sempre é um problema.

Em um programa simples, podemos querer exatamente esse comportamento.

Por exemplo:

```python
print("Aguardando mensagem...")

data = client.recv(1024)

print("Mensagem recebida!")
```

Enquanto o cliente não enviar nada:

```text
Aguardando mensagem...
       ↓
      recv()
       ↓
   aguardando...
```

Quando os dados chegam:

```text
Aguardando mensagem...
       ↓
      recv()
       ↓
dados recebidos
       ↓
Mensagem recebida!
```

Isso torna o código relativamente simples de entender.

---

## 12.4 O problema do bloqueio infinito

O problema aparece quando uma operação pode ficar aguardando indefinidamente.

Imagine:

```python
data = client.recv(1024)
```

e o outro lado:

- não envia dados;
    
- perdeu a conexão;
    
- travou;
    
- está muito lento;
    
- está esperando outra operação.
    

Dependendo da situação, nosso programa pode ficar preso aguardando.

Por exemplo:

```text
SERVIDOR

recv()
  │
  │
  │
  │ nenhum dado
  │
  │
  │
  └──────────────► continua aguardando
```

Em aplicações reais, normalmente precisamos controlar esse comportamento.

---

## 12.5 Timeout

Um **timeout** define um período máximo de espera para determinadas operações.

Em Python podemos utilizar:

```python
socket.settimeout(seconds)
```

Por exemplo:

```python
client.settimeout(5)
```

Isso significa que determinadas operações bloqueantes do socket terão um limite de aproximadamente:

```text
5 segundos
```

de espera.

Podemos visualizar:

```text
recv()
  │
  ├── dados chegam antes de 5s
  │       ↓
  │    retorna dados
  │
  └── 5s passam sem progresso
          ↓
      timeout
```

---

## 12.6 `settimeout()`

Sintaxe:

```python
socket.settimeout(value)
```

### Parâmetro

|Parâmetro|Tipo|Obrigatório|Padrão|Comportamento|
|---|---|---|---|---|
|`value`|`float` ou `None`|Sim|—|Tempo máximo de espera em segundos|

Exemplo:

```python
client.settimeout(5)
```

Podemos utilizar valores decimais:

```python
client.settimeout(2.5)
```

Nesse caso:

```text
2.5 segundos
```

---

## 12.7 Timeout não significa que o socket será fechado

Esse é um ponto importante.

Se fizermos:

```python
client.settimeout(5)
```

e depois:

```python
data = client.recv(1024)
```

o timeout não significa:

```text
5 segundos
   ↓
socket.close()
```

Significa:

```text
5 segundos sem a operação conseguir prosseguir
   ↓
operação gera timeout
```

O socket ainda pode existir e, dependendo da situação, podemos continuar utilizando-o.

---

## 12.8 Exceção de timeout

Quando uma operação excede o tempo configurado, podemos tratar o erro:

```python
import socket

client.settimeout(5)

try:
    data = client.recv(1024)
except socket.timeout:
    print("Tempo de espera excedido.")
```

O fluxo fica:

```text
recv()
  │
  ▼
aguarda
  │
  ├── dados chegam
  │      ↓
  │    continua
  │
  └── timeout
         ↓
socket.timeout
         ↓
except
```

Isso permite que o programa tome uma decisão em vez de ficar esperando indefinidamente.

---

## 12.9 Timeout no `connect()`

Timeout também pode ser relevante durante uma conexão.

Podemos fazer:

```python
client.settimeout(5)

client.connect(("192.168.1.100", 4444))
```

Se a conexão não puder ser estabelecida dentro das condições e do período configurado, a operação poderá gerar uma exceção relacionada a timeout.

Podemos tratar:

```python
try:
    client.connect(("192.168.1.100", 4444))
except socket.timeout:
    print("Tempo limite para conexão excedido.")
```

---

## 12.10 Definindo timeout diretamente

Também existe uma função para configurar o timeout padrão utilizado na criação de novos sockets:

```python
socket.setdefaulttimeout(timeout)
```

Exemplo:

```python
socket.setdefaulttimeout(5)
```

Isso é diferente de:

```python
client.settimeout(5)
```

A primeira configuração estabelece um padrão para sockets criados posteriormente.

Já:

```python
client.settimeout(5)
```

configura especificamente aquele socket.

Para a maioria dos programas, é mais claro configurar o socket explicitamente:

```python
client.settimeout(5)
```

---

## 12.11 Removendo o timeout

Podemos voltar ao comportamento bloqueante padrão utilizando:

```python
client.settimeout(None)
```

Ou seja:

```python
client.settimeout(5)
```

define timeout.

Depois:

```python
client.settimeout(None)
```

remove o timeout e retorna ao modo bloqueante.

Visualmente:

```text
settimeout(5)
     ↓
modo com timeout

settimeout(None)
     ↓
modo bloqueante
```

---

## 12.12 Modo não bloqueante

Além do modo bloqueante e do modo com timeout, podemos colocar o socket em **modo não bloqueante**.

Para isso:

```python
socket.setblocking(False)
```

Exemplo:

```python
client.setblocking(False)
```

Agora uma operação que normalmente bloquearia não deve ficar esperando indefinidamente.

Em vez disso, se a operação não puder ser realizada imediatamente, o sistema operacional pode indicar que a operação não está disponível naquele momento.

---

## 12.13 `setblocking()`

Sintaxe:

```python
socket.setblocking(flag)
```

### Parâmetro

|Parâmetro|Tipo|Obrigatório|Padrão|Comportamento|
|---|---|---|---|---|
|`flag`|`bool`|Sim|—|`True` para bloqueante, `False` para não bloqueante|

Exemplo bloqueante:

```python
client.setblocking(True)
```

Exemplo não bloqueante:

```python
client.setblocking(False)
```

---

## 12.14 Bloqueante vs não bloqueante

### Bloqueante

```python
client.setblocking(True)

data = client.recv(1024)
```

Se não houver dados:

```text
recv()
  ↓
aguarda
  ↓
aguarda
  ↓
aguarda
  ↓
dados chegam
  ↓
retorna
```

### Não bloqueante

```python
client.setblocking(False)

data = client.recv(1024)
```

Se não houver dados imediatamente:

```text
recv()
  ↓
não há dados
  ↓
retorna erro indicando que
a operação bloquearia
```

A aplicação então pode decidir o que fazer.

---

## 12.15 `BlockingIOError`

Em modo não bloqueante, operações que não podem ser realizadas imediatamente podem gerar:

```python
BlockingIOError
```

Exemplo:

```python
client.setblocking(False)

try:
    data = client.recv(1024)
except BlockingIOError:
    print("Nenhum dado disponível agora.")
```

O importante é entender a diferença:

```text
BLOQUEANTE
    ↓
espera até poder executar

NÃO BLOQUEANTE
    ↓
não espera
    ↓
informa que não pode executar agora
```

---

## 12.16 Por que usar modo não bloqueante?

O modo não bloqueante pode ser útil quando uma aplicação precisa lidar com várias operações de I/O sem ficar parada esperando uma delas.

Por exemplo, imagine um servidor com:

```text
Cliente A
Cliente B
Cliente C
Cliente D
```

Se o servidor executar:

```python
recv()
```

bloqueando em um cliente que não envia nada, ele pode deixar de atender os outros.

Em sistemas mais sofisticados, podemos utilizar mecanismos de multiplexação de I/O.

---

## 12.17 O problema de simplesmente usar `setblocking(False)`

Um erro comum é pensar:

> "Vou colocar todos os sockets como não bloqueantes e fazer um loop."

Por exemplo:

```python
while True:
    try:
        data = client.recv(1024)
    except BlockingIOError:
        pass
```

Isso pode resultar em um **busy loop**.

O programa pode ficar fazendo:

```text
recv()
recv()
recv()
recv()
recv()
recv()
recv()
...
```

sem esperar adequadamente.

Isso pode consumir CPU desnecessariamente.

---

## 12.18 Busy loop

Imagine:

```python
while True:
    try:
        data = client.recv(1024)
    except BlockingIOError:
        continue
```

Se não houver dados, o loop pode executar repetidamente:

```text
CPU
 │
 ├── recv()
 ├── erro
 ├── recv()
 ├── erro
 ├── recv()
 ├── erro
 ├── recv()
 ├── erro
 └── ...
```

Isso pode resultar em uso elevado de CPU.

Por isso, aplicações que precisam trabalhar com muitos sockets normalmente utilizam mecanismos específicos de espera por eventos.

---

## 12.19 Multiplexação de I/O

Python fornece mecanismos para trabalhar com múltiplos sockets.

Um dos principais módulos é:

```python
import selectors
```

Também existem mecanismos baseados em:

```python
select
poll
epoll
kqueue
```

Dependendo do sistema operacional e da abstração utilizada.

A ideia geral é:

```text
vários sockets
      │
      ▼
mecanismo de I/O
      │
      ▼
informa quais estão prontos
      │
      ▼
aplicação processa apenas os necessários
```

Em vez de ficar perguntando continuamente:

```text
"Tem dado?"
"Tem dado?"
"Tem dado?"
"Tem dado?"
```

podemos esperar até que o sistema informe que alguma operação está pronta.

---

## 12.20 Modelo mental da multiplexação

Imagine:

```text
CLIENTE A ──┐
CLIENTE B ──┤
CLIENTE C ──┼──► mecanismo de multiplexação
CLIENTE D ──┤
CLIENTE E ──┘
                    │
                    ▼
             quais estão prontos?
                    │
             ┌──────┴──────┐
             ▼             ▼
          cliente B     cliente D
             │             │
             ▼             ▼
           recv()        recv()
```

Isso permite que uma aplicação trabalhe com vários sockets de maneira eficiente.

---

## 12.21 `selectors` em Python

O módulo:

```python
selectors
```

fornece uma abstração de alto nível para multiplexação de I/O.

Exemplo conceitual:

```python
import selectors

selector = selectors.DefaultSelector()
```

Depois podemos registrar sockets:

```python
selector.register(server, selectors.EVENT_READ)
```

A aplicação pode então esperar eventos:

```python
events = selector.select()
```

A ideia é:

```text
socket 1 ──┐
socket 2 ──┤
socket 3 ──┼──► selector
socket 4 ──┤
socket 5 ──┘
               │
               ▼
        aguarda eventos
               │
               ▼
       socket disponível
```

Não é necessário dominar `selectors` ainda.

O importante neste momento é entender **por que ele existe**.

---

## 12.22 Timeout não é a mesma coisa que não bloqueante

Esses conceitos são relacionados, mas não são iguais.

### Bloqueante

```python
socket.setblocking(True)
```

A operação pode esperar indefinidamente.

### Timeout

```python
socket.settimeout(5)
```

A operação pode esperar, mas existe um limite.

### Não bloqueante

```python
socket.setblocking(False)
```

A operação não fica esperando por dados.

Podemos comparar:

|Modo|Pode esperar?|Limite de tempo|
|---|---|---|
|Bloqueante|Sim|Nenhum|
|Timeout|Sim|Definido|
|Não bloqueante|Não|Não espera|

---

## 12.23 Relação entre `setblocking()` e `settimeout()`

Existe uma relação importante.

Quando fazemos:

```python
client.setblocking(False)
```

estamos essencialmente colocando o socket em um modo equivalente a timeout de:

```python
0
```

Conceitualmente:

```text
timeout = None
    ↓
bloqueante

timeout > 0
    ↓
timeout

timeout = 0
    ↓
não bloqueante
```

Por isso, Python permite controlar o comportamento de bloqueio através dessas configurações.

---

## 12.24 Exemplo com timeout em um servidor

Podemos criar um servidor que não espere indefinidamente por um cliente:

```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("127.0.0.1", 4444))
server.listen()

server.settimeout(10)

try:
    client, address = server.accept()
    print("Cliente conectado:", address)

except socket.timeout:
    print("Nenhum cliente conectou dentro do tempo.")

finally:
    server.close()
```

O fluxo é:

```text
listen()
   ↓
accept()
   ↓
aguarda até 10 segundos
   │
   ├── cliente conecta
   │      ↓
   │   continua
   │
   └── timeout
          ↓
     socket.timeout
```

---

## 12.25 Timeout também pode ser utilizado no `recv()`

Exemplo:

```python
import socket

client.settimeout(5)

try:
    data = client.recv(1024)

except socket.timeout:
    print("O cliente demorou demais para enviar.")
```

Isso é útil em situações nas quais não queremos permitir que uma conexão fique esperando indefinidamente.

---

## 12.26 Timeout não substitui tratamento de erros

Mesmo utilizando:

```python
client.settimeout(5)
```

a comunicação ainda pode apresentar outros problemas.

Por exemplo:

```text
timeout
connection reset
broken pipe
connection refused
network unreachable
```

Por isso, aplicações reais normalmente tratam diferentes exceções.

Exemplo conceitual:

```python
try:
    data = client.recv(1024)

except socket.timeout:
    print("Tempo excedido.")

except ConnectionResetError:
    print("Conexão foi resetada.")

except OSError as error:
    print("Erro de socket:", error)
```

Não devemos assumir que todo problema de rede será um timeout.

---

## 12.27 Timeout é especialmente importante em aplicações reais

Imagine um servidor que mantém conexões de vários clientes.

Se cada cliente puder ficar indefinidamente em uma operação bloqueante:

```text
Cliente 1 → aguardando
Cliente 2 → aguardando
Cliente 3 → aguardando
Cliente 4 → aguardando
...
```

a aplicação pode acabar mantendo muitos recursos ocupados.

Timeouts, multiplexação e arquiteturas assíncronas são algumas das técnicas utilizadas para controlar esse tipo de situação.

---

## 12.28 O conceito de I/O

Socket é uma forma de **I/O**, ou _Input/Output_.

Nesse contexto:

```text
Input
  ↓
dados entrando na aplicação

Output
  ↓
dados saindo da aplicação
```

Por exemplo:

```python
data = client.recv(1024)
```

é uma operação de entrada.

Enquanto:

```python
client.sendall(data)
```

é uma operação de saída.

Podemos visualizar:

```text
REDE
  │
  │ dados
  ▼
recv()
  │
  ▼
APLICAÇÃO
  │
  │ dados
  ▼
sendall()
  │
  ▼
REDE
```

É por isso que sockets aparecem frequentemente em assuntos como:

- I/O bloqueante;
    
- I/O não bloqueante;
    
- multiplexação;
    
- programação assíncrona.
    

---

## 12.29 Relação com `asyncio`

Python também possui o módulo:

```python
asyncio
```

que permite construir aplicações assíncronas.

A ideia geral é diferente do modelo tradicional:

```python
data = client.recv(1024)
```

que pode bloquear o fluxo atual.

Com programação assíncrona, podemos trabalhar com operações de I/O que permitem que outras tarefas sejam executadas enquanto aguardamos.

Conceitualmente:

```text
Tarefa A
   │
   ├── aguardando rede
   │
   ▼
Tarefa B executa
   │
   ▼
Tarefa C executa
   │
   ▼
dados da tarefa A chegam
   │
   ▼
Tarefa A continua
```

Isso será estudado separadamente.

Neste momento, o mais importante é entender que:

> **bloqueio é uma propriedade do modo como a aplicação espera pelas operações de I/O.**

---

## 12.30 Modelo mental final desta parte

Podemos reunir os três comportamentos:

```text
                 SOCKET
                    │
        ┌───────────┼───────────┐
        │           │           │
        ▼           ▼           ▼
   BLOQUEANTE    TIMEOUT    NÃO BLOQUEANTE
        │           │           │
        │           │           │
        ▼           ▼           ▼
   espera até    espera até    não espera
   operação      determinado   pela operação
   prosseguir    limite
```

### Bloqueante

```python
client.setblocking(True)
```

```text
recv()
 ↓
espera
```

### Timeout

```python
client.settimeout(5)
```

```text
recv()
 ↓
espera
 ↓
máximo configurado
 ↓
socket.timeout
```

### Não bloqueante

```python
client.setblocking(False)
```

```text
recv()
 ↓
dados disponíveis?
 ├── sim → recebe
 └── não → operação não pode prosseguir agora
```

---

## Resumo da Parte

### Bloqueante

É o comportamento padrão.

```python
socket.setblocking(True)
```

A operação pode esperar indefinidamente.

### Timeout

Define um limite para a espera:

```python
socket.settimeout(5)
```

Pode gerar:

```python
socket.timeout
```

### Não bloqueante

Configurado com:

```python
socket.setblocking(False)
```

A operação não fica esperando.

Pode ocorrer:

```python
BlockingIOError
```

quando a operação não puder ser realizada naquele momento.

### `setblocking()`

Controla o modo de bloqueio:

```python
socket.setblocking(True)
socket.setblocking(False)
```

### `settimeout()`

Controla o tempo máximo de espera:

```python
socket.settimeout(5)
socket.settimeout(None)
```

### Multiplexação

Permite trabalhar com vários sockets sem bloquear individualmente em cada um:

```text
vários sockets
      ↓
select / poll / epoll / selectors
      ↓
eventos disponíveis
      ↓
processar sockets prontos
```

### Conceito principal

> **Um socket bloqueante pode esperar indefinidamente, um socket com timeout pode esperar por um período limitado e um socket não bloqueante não espera pela operação. Essa diferença é fundamental para entender servidores que trabalham com múltiplas conexões e operações de I/O.**

---

# 13. Protocolos de aplicação e delimitação de mensagens

## 13.1 O problema: TCP não conhece suas mensagens

Um dos conceitos mais importantes ao trabalhar com `SOCK_STREAM` é entender que o **TCP trabalha com um fluxo contínuo de bytes**, e não com mensagens individuais.

Por exemplo, imagine que o cliente execute:

```python
client.sendall(b"Hello")
client.sendall(b"World")
```

É tentador imaginar que o servidor receberá:

```text
Hello
World
```

Mas isso **não é garantido**.

O servidor poderia receber:

```python
b"HelloWorld"
```

Ou:

```python
b"Hel"
b"loWo"
b"rld"
```

Ou até:

```python
b"HelloW"
b"orld"
```

Isso acontece porque o TCP não mantém a informação de onde uma chamada `send()` ou `sendall()` começou ou terminou.

Para o TCP, existe apenas:

```text
HELLOWORLD
```

como uma sequência de bytes.

---

## 13.2 `send()` não cria uma mensagem

Considere:

```python
client.sendall(b"Mensagem 1")
client.sendall(b"Mensagem 2")
client.sendall(b"Mensagem 3")
```

O TCP não cria três objetos independentes chamados:

```text
Mensagem 1
Mensagem 2
Mensagem 3
```

Ele simplesmente coloca os bytes no fluxo:

```text
Mensagem 1Mensagem 2Mensagem 3
```

O receptor precisa descobrir **onde uma mensagem termina e onde a próxima começa**.

Essa responsabilidade pertence ao **protocolo da aplicação**.

---

## 13.3 O que é um protocolo de aplicação?

Um protocolo de aplicação é um conjunto de regras que determina **como os programas vão interpretar os dados transmitidos pela rede**.

Por exemplo, podemos criar uma regra:

```text
Cada mensagem termina com \n
```

Então:

```text
Olá\n
Tudo bem?\n
Sair\n
```

O TCP transportará os bytes normalmente, enquanto nossa aplicação interpreta:

```text
Olá
```

```text
Tudo bem?
```

```text
Sair
```

Nesse caso:

```text
TCP
↓
fluxo de bytes
↓
protocolo da aplicação
↓
mensagens
```

O TCP fornece o transporte.

O protocolo da aplicação define o significado dos dados.

---

## 13.4 Delimitação por caractere

Uma das formas mais simples de separar mensagens é utilizar um **delimitador**.

Por exemplo:

```text
\n
```

Podemos definir:

```text
mensagem + \n
```

Então:

```text
Olá\n
Python é legal\n
Sair\n
```

representa três mensagens.

O servidor pode acumular os bytes recebidos até encontrar `\n`.

---

## 13.5 Exemplo com mensagens terminadas em `\n`

Cliente:

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

client.connect(("127.0.0.1", 4444))

client.sendall(b"Olá servidor\n")
client.sendall(b"Estou estudando sockets\n")
client.sendall(b"Sair\n")

client.close()
```

O servidor não deve assumir que cada `recv()` representa uma mensagem.

Por exemplo:

```python
data = client.recv(1024)
```

poderia retornar:

```python
b"Ol\xc3\xa1 servidor\nEstou estudando sockets\n"
```

Nesse caso, duas mensagens chegaram no mesmo `recv()`.

Também poderia retornar apenas:

```python
b"Ol\xc3\xa1 serv"
```

A aplicação precisa continuar recebendo os dados.

---

## 13.6 Buffer de recebimento

Uma técnica comum é manter um **buffer** com os dados que ainda não foram processados.

Exemplo conceitual:

```python
buffer = b""

while True:
    data = client.recv(1024)

    if not data:
        break

    buffer += data
```

Imagine que os dados cheguem assim:

```text
recv() #1
b"Olá serv"
```

Depois:

```text
recv() #2
b"idor\nTudo "
```

Depois:

```text
recv() #3
b"bem?\n"
```

O buffer ficará:

```text
Olá servidor\nTudo bem?\n
```

A aplicação pode procurar `\n` e extrair as mensagens completas.

---

## 13.7 Extraindo mensagens do buffer

Podemos fazer:

```python
buffer = b""

while True:
    data = client.recv(1024)

    if not data:
        break

    buffer += data

    while b"\n" in buffer:
        message, buffer = buffer.split(b"\n", 1)

        print(message.decode())
```

A parte mais importante é:

```python
buffer.split(b"\n", 1)
```

O `1` significa que queremos realizar apenas uma divisão por vez.

Por exemplo:

```python
buffer = b"Olá\nTudo bem?\n"
```

Após:

```python
message, buffer = buffer.split(b"\n", 1)
```

teremos:

```python
message
```

com:

```python
b"Olá"
```

e:

```python
buffer
```

com:

```python
b"Tudo bem?\n"
```

Na próxima iteração, a segunda mensagem será processada.

---

## 13.8 Por que o buffer é necessário?

Imagine que o servidor receba:

```python
b"Olá"
```

mas a mensagem completa seja:

```text
Olá servidor
```

Se o programa interpretar imediatamente:

```python
data = client.recv(1024)

print(data.decode())
```

ele poderá considerar:

```text
Olá
```

como uma mensagem completa.

Mas isso estaria errado.

Talvez os próximos bytes ainda estejam chegando:

```text
 servidor
```

O buffer permite guardar os dados incompletos:

```text
recebido:
"Olá"

buffer:
"Olá"
```

Depois:

```text
recebido:
" servidor\n"

buffer:
"Olá servidor\n"
```

Agora temos uma mensagem completa.

---

## 13.9 Delimitadores precisam fazer parte do protocolo

O delimitador não pode ser escolhido aleatoriamente.

Imagine que definimos:

```text
\n
```

como final da mensagem.

Então o protocolo precisa especificar que:

```text
\n = fim da mensagem
```

Se o conteúdo da própria mensagem puder conter `\n`, precisamos definir uma forma de escapar esse caractere ou utilizar outro mecanismo.

Por exemplo:

```text
Mensagem normal\n
```

é simples.

Mas:

```text
Olá
Mundo
```

possui uma quebra de linha dentro do conteúdo.

Nesse caso, o protocolo precisa definir como diferenciar:

```text
quebra de linha dentro da mensagem
```

de:

```text
delimitador que encerra a mensagem
```

Por isso, protocolos mais complexos frequentemente utilizam outras formas de framing.

---

## 13.10 Mensagens de tamanho fixo

Outra possibilidade é definir que todas as mensagens possuem exatamente o mesmo tamanho.

Por exemplo:

```text
20 bytes
```

Então:

```text
[---------20 bytes---------]
[---------20 bytes---------]
[---------20 bytes---------]
```

O receptor sabe exatamente quantos bytes precisa receber para completar uma mensagem.

Porém, isso pode desperdiçar espaço quando as mensagens possuem tamanhos diferentes.

Por exemplo:

```text
Olá
```

possui poucos bytes, mas ainda ocuparia:

```text
20 bytes
```

---

## 13.11 Prefixo de tamanho

Uma solução mais flexível é enviar primeiro o **tamanho da mensagem** e depois os dados.

Por exemplo:

```text
[ tamanho ][ mensagem ]
```

Imagine:

```text
Olá
```

Possui 3 bytes.

Podemos enviar:

```text
[3][Olá]
```

O receptor primeiro lê o tamanho:

```text
3
```

Depois sabe que precisa receber exatamente:

```text
3 bytes
```

para completar a mensagem.

Esse método é conhecido como **length-prefix framing**.

---

## 13.12 Exemplo conceitual do length-prefix

Imagine a mensagem:

```text
Python
```

Ela possui:

```text
6 bytes
```

O protocolo poderia representar:

```text
[6][Python]
```

Outra mensagem:

```text
Olá
```

poderia ser:

```text
[3][Olá]
```

O receptor faz:

```text
1. Receber o tamanho
2. Interpretar o tamanho
3. Receber exatamente essa quantidade de bytes
4. Processar a mensagem
5. Repetir
```

Esse modelo é muito mais robusto para mensagens de tamanho variável.

---

## 13.13 `struct` para representar o tamanho

Em Python, a biblioteca `struct` pode ser utilizada para converter números em uma representação binária adequada para transmissão.

Exemplo:

```python
import struct

length = len(data)

header = struct.pack("!I", length)
```

Aqui:

```python
struct.pack("!I", length)
```

transforma o inteiro em bytes.

O prefixo:

```text
!
```

indica **network byte order**, ou seja, big-endian.

E:

```text
I
```

representa um inteiro sem sinal de 4 bytes.

Assim podemos construir:

```python
packet = header + data
```

E enviar:

```python
client.sendall(packet)
```

O formato será conceitualmente:

```text
[4 bytes contendo o tamanho][dados]
```

---

## 13.14 Recebendo exatamente uma quantidade de bytes

Aqui surge outro problema importante.

Suponha que precisamos receber:

```text
100 bytes
```

Não podemos simplesmente fazer:

```python
data = client.recv(100)
```

e assumir:

```python
len(data) == 100
```

Isso **não é garantido**.

Podemos receber:

```text
40 bytes
```

e depois:

```text
60 bytes
```

Por isso, precisamos continuar chamando `recv()` até obter a quantidade necessária.

Uma função auxiliar pode ser:

```python
def recv_exactly(sock, size):
    data = b""

    while len(data) < size:
        chunk = sock.recv(size - len(data))

        if not chunk:
            raise ConnectionError("Conexão encerrada antes dos dados completos")

        data += chunk

    return data
```

Agora:

```python
data = recv_exactly(client, 100)
```

garante que a função só retorna quando:

```text
100 bytes
```

forem recebidos.

Ou quando a conexão for encerrada antes disso, causando o erro definido pela função.

---

## 13.15 Por que `recv(size)` pode retornar menos que `size`?

Porque o argumento:

```python
recv(size)
```

significa aproximadamente:

> "Receba **até** `size` bytes."

Não significa:

> "Espere obrigatoriamente até receber `size` bytes."

Por exemplo:

```python
data = client.recv(1024)
```

pode retornar:

```text
10 bytes
```

```text
500 bytes
```

```text
1024 bytes
```

ou qualquer quantidade disponível dentro daquele limite.

Por isso:

```python
recv(1024)
```

não deve ser interpretado como:

```text
"receberei uma mensagem de 1024 bytes"
```

Mas como:

```text
"posso receber até 1024 bytes nesta chamada"
```

---

## 13.16 Comparando os principais mecanismos de framing

|Método|Como funciona|Vantagem|Limitação|
|---|---|---|---|
|Delimitador|Usa um marcador de fim|Simples|Precisa tratar o delimitador dentro do conteúdo|
|Tamanho fixo|Toda mensagem possui tamanho definido|Simples de implementar|Pode desperdiçar espaço|
|Prefixo de tamanho|Envia tamanho antes dos dados|Flexível e eficiente|Implementação mais complexa|
|Estrutura/protocolo|Define cabeçalho + payload|Muito flexível|Exige projeto do protocolo|

---

## 13.17 TCP fornece transporte, não significado

É importante separar as responsabilidades:

```text
┌──────────────────────────────┐
│ Aplicação                    │
│                              │
│ "LOGIN usuario senha"        │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Protocolo da aplicação       │
│                              │
│ Define formato das mensagens │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ TCP                          │
│                              │
│ Fluxo confiável de bytes     │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ IP                           │
│                              │
│ Endereçamento e roteamento   │
└──────────────┬───────────────┘
               │
               ▼
            Rede
```

O TCP não sabe que:

```text
LOGIN
```

é uma mensagem.

Ele não sabe que:

```text
JSON
```

representa dados.

Ele não sabe que:

```text
\n
```

significa final de mensagem.

Tudo isso é responsabilidade das camadas superiores.

---

## 13.18 Exemplo de um protocolo simples de chat

Podemos criar uma regra extremamente simples:

```text
Toda mensagem termina com \n
```

Cliente:

```python
client.sendall(b"Olá!\n")
client.sendall(b"Como você está?\n")
client.sendall(b"Sair\n")
```

Servidor:

```python
buffer = b""

while True:
    data = client.recv(1024)

    if not data:
        break

    buffer += data

    while b"\n" in buffer:
        message, buffer = buffer.split(b"\n", 1)

        message = message.decode()

        print("Cliente:", message)

        if message == "Sair":
            break
```

O ponto principal desse exemplo não é o chat em si.

É entender que:

```text
recv()
```

não representa uma mensagem.

O programa precisa implementar uma regra para descobrir onde cada mensagem termina.

---

## 13.19 Erro conceitual: `recv()` = uma mensagem

Este é um dos erros mais comuns de quem começa a estudar sockets.

Código:

```python
data = client.recv(1024)
message = data.decode()

print(message)
```

Isso pode funcionar em exemplos extremamente simples.

Mas não significa que o programa esteja implementando corretamente um protocolo.

O código está assumindo implicitamente:

```text
1 recv() = 1 mensagem
```

Em TCP, essa relação não existe.

O correto é pensar:

```text
recv()
    ↓
pedaço do fluxo
    ↓
buffer
    ↓
framing
    ↓
mensagem completa
```

---

## 13.20 Modelo mental definitivo

Ao utilizar TCP para criar um protocolo próprio, pense em três níveis:

### Nível 1 — Transporte

O TCP fornece:

```text
conexão
ordenação
retransmissão
controle de fluxo
entrega confiável
```

### Nível 2 — Framing

A aplicação precisa determinar:

```text
onde começa uma mensagem
onde termina uma mensagem
```

Por exemplo:

```text
\n
```

ou:

```text
[length][data]
```

### Nível 3 — Conteúdo

Depois de identificar a mensagem, a aplicação interpreta seu conteúdo.

Por exemplo:

```json
{
    "action": "login",
    "username": "admin"
}
```

Assim:

```text
TCP
 ↓
bytes
 ↓
framing
 ↓
mensagem
 ↓
interpretação
 ↓
ação da aplicação
```

Esse modelo será fundamental para compreender posteriormente:

- protocolos de rede;
    
- HTTP;
    
- WebSockets;
    
- APIs;
    
- servidores TCP;
    
- sistemas de chat;
    
- transferência de arquivos;
    
- protocolos binários;
    
- comunicação entre processos.
    

---

## Resumo da Parte

- TCP fornece um **fluxo de bytes**, não mensagens.
    
- Uma chamada `send()` não corresponde necessariamente a uma chamada `recv()`.
    
- Uma mensagem pode ser dividida entre vários `recv()`.
    
- Várias mensagens podem chegar em um único `recv()`.
    
- A aplicação precisa definir um mecanismo de **framing**.
    
- Delimitadores como `\n` são uma solução simples.
    
- Mensagens também podem utilizar tamanho fixo.
    
- O **length-prefix framing** envia o tamanho antes do conteúdo.
    
- `recv(n)` significa receber **até `n` bytes**, e não obrigatoriamente `n`.
    
- Para receber exatamente uma quantidade de bytes, é necessário utilizar um loop.
    
- `struct.pack()` pode ser usado para criar cabeçalhos binários.
    
- O TCP transporta os bytes; o protocolo da aplicação define o significado deles.
    
- O modelo fundamental é:
    

```text
TCP → fluxo de bytes
Framing → separação das mensagens
Aplicação → interpretação das mensagens
```

	---

# 14. Construindo um protocolo simples sobre TCP

## 14.1 O que é um protocolo próprio?

Até agora vimos que o TCP fornece apenas um **fluxo confiável de bytes**.

Isso significa que, se quisermos construir uma aplicação como:

- chat;
    
- transferência de arquivos;
    
- autenticação;
    
- API;
    
- controle remoto;
    
- comunicação entre programas;
    

precisamos definir **regras para interpretar esses bytes**.

Essas regras formam um protocolo.

Um protocolo próprio pode ser extremamente simples.

Por exemplo:

```text
CLIENTE → LOGIN allan senha123
SERVIDOR → OK
```

Ou:

```text
CLIENTE → MSG Olá servidor
SERVIDOR → OK
```

O importante é que **cliente e servidor concordem com o formato**.

---

## 14.2 Um protocolo precisa definir regras

Um protocolo de comunicação precisa responder perguntas como:

1. Como uma mensagem começa?
    
2. Como uma mensagem termina?
    
3. Como sabemos qual é o tipo da mensagem?
    
4. Como representamos os dados?
    
5. O servidor pode responder?
    
6. O que acontece quando ocorre um erro?
    
7. Como a conexão é encerrada?
    

Por exemplo, podemos criar o seguinte protocolo:

```text
LOGIN <usuario> <senha>\n
MSG <texto>\n
QUIT\n
```

Assim:

```text
LOGIN allan senha123\n
```

representa uma operação de login.

Enquanto:

```text
MSG Olá servidor\n
```

representa uma mensagem.

E:

```text
QUIT\n
```

representa uma solicitação para encerrar a sessão.

---

## 14.3 Comandos e dados

Uma estrutura comum em protocolos simples é separar:

```text
COMANDO + DADOS
```

Por exemplo:

```text
LOGIN allan senha123
```

pode ser interpretado como:

```text
comando = LOGIN
dados = allan senha123
```

Outro exemplo:

```text
MSG Olá mundo
```

pode ser:

```text
comando = MSG
dados = Olá mundo
```

Isso permite que o servidor saiba **qual operação deve executar**.

---

## 14.4 Exemplo de servidor

Podemos criar um servidor simples:

```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("127.0.0.1", 4444))
server.listen()

print("Servidor aguardando conexão...")

client, address = server.accept()

print(f"Cliente conectado: {address}")

buffer = b""

while True:
    data = client.recv(1024)

    if not data:
        break

    buffer += data

    while b"\n" in buffer:
        message, buffer = buffer.split(b"\n", 1)

        message = message.decode()

        print("Recebido:", message)

client.close()
server.close()
```

Observe que o servidor não trata cada `recv()` como uma mensagem.

Ele utiliza:

```python
buffer += data
```

e depois procura:

```python
b"\n"
```

para encontrar mensagens completas.

---

## 14.5 Interpretando comandos

Agora podemos transformar a mensagem em um comando.

Por exemplo:

```python
parts = message.split(" ", 1)
```

Se recebermos:

```text
MSG Olá servidor
```

teremos:

```python
parts[0]
```

igual a:

```text
MSG
```

e:

```python
parts[1]
```

igual a:

```text
Olá servidor
```

Podemos então fazer:

```python
command, data = message.split(" ", 1)
```

E:

```python
if command == "MSG":
    print("Mensagem:", data)
```

---

## 14.6 O segundo argumento do `split()`

Aqui existe um detalhe importante:

```python
message.split(" ", 1)
```

O `1` limita a quantidade de divisões.

Considere:

```text
MSG Olá meu amigo
```

Sem limite:

```python
message.split(" ")
```

resultaria em:

```python
["MSG", "Olá", "meu", "amigo"]
```

Com:

```python
message.split(" ", 1)
```

teremos:

```python
["MSG", "Olá meu amigo"]
```

Isso é útil porque queremos separar apenas:

```text
comando
```

de:

```text
restante da mensagem
```

---

## 14.7 Respostas do servidor

Um protocolo normalmente possui comunicação nos dois sentidos.

Por exemplo:

```text
CLIENTE → MSG Olá\n
SERVIDOR → OK\n
```

O servidor pode responder:

```python
client.sendall(b"OK\n")
```

O cliente pode então receber:

```python
response = client.recv(1024)
```

e interpretar:

```python
print(response.decode())
```

Novamente, em um protocolo real, não devemos assumir que uma chamada de `recv()` necessariamente contém toda a resposta.

A mesma regra de framing continua válida.

---

## 14.8 Protocolo requisição → resposta

Uma estrutura muito comum é:

```text
Cliente
   │
   │ requisição
   ▼
Servidor
   │
   │ resposta
   ▼
Cliente
```

Por exemplo:

```text
CLIENTE → PING\n
SERVIDOR → PONG\n
```

Outro exemplo:

```text
CLIENTE → ADD 10 20\n
SERVIDOR → RESULT 30\n
```

Outro:

```text
CLIENTE → GET usuario\n
SERVIDOR → USER allan\n
```

Esse modelo aparece em diversos sistemas reais.

---

## 14.9 Exemplo: protocolo `PING/PONG`

Podemos criar um protocolo extremamente simples.

Regra:

```text
PING\n
```

deve receber:

```text
PONG\n
```

Servidor:

```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("127.0.0.1", 4444))
server.listen()

print("Servidor aguardando conexão...")

client, address = server.accept()

buffer = b""

while True:
    data = client.recv(1024)

    if not data:
        break

    buffer += data

    while b"\n" in buffer:
        message, buffer = buffer.split(b"\n", 1)

        message = message.decode()

        if message == "PING":
            client.sendall(b"PONG\n")

        elif message == "QUIT":
            client.sendall(b"BYE\n")
            client.close()
            server.close()
            raise SystemExit
```

Agora temos um pequeno protocolo funcional.

---

## 14.10 Cliente do protocolo

O cliente pode ser:

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

client.connect(("127.0.0.1", 4444))

client.sendall(b"PING\n")

response = client.recv(1024)

print("Servidor:", response.decode())

client.close()
```

O fluxo será:

```text
Cliente
   │
   │ PING\n
   ▼
Servidor
   │
   │ PONG\n
   ▼
Cliente
```

Resultado:

```text
Servidor: PONG
```

---

## 14.11 O problema do `recv()` na resposta

O exemplo anterior funciona para demonstrar o conceito, mas existe uma limitação:

```python
response = client.recv(1024)
```

não garante que:

```text
PONG\n
```

será recebido inteiro nessa chamada.

Pode acontecer de receber:

```python
b"PO"
```

e depois:

```python
b"NG\n"
```

Por isso, uma implementação mais correta também utilizaria um buffer no cliente.

---

## 14.12 Função para receber uma mensagem delimitada

Podemos criar uma função reutilizável:

```python
def recv_message(sock, buffer):
    while b"\n" not in buffer:
        data = sock.recv(1024)

        if not data:
            raise ConnectionError("Conexão encerrada")

        buffer += data

    message, buffer = buffer.split(b"\n", 1)

    return message, buffer
```

Agora podemos fazer:

```python
buffer = b""

message, buffer = recv_message(client, buffer)

print(message.decode())
```

A função continua recebendo dados até encontrar:

```text
\n
```

---

## 14.13 Por que retornar o buffer?

Considere que o socket receba:

```text
PONG\nOK\n
```

em uma única chamada:

```python
recv()
```

O protocolo possui duas mensagens:

```text
PONG
```

e:

```text
OK
```

Quando fazemos:

```python
message, buffer = buffer.split(b"\n", 1)
```

a primeira mensagem é:

```text
PONG
```

e o restante permanece:

```text
OK\n
```

Por isso precisamos preservar o buffer.

Caso descartássemos o restante, perderíamos dados.

---

## 14.14 Um protocolo pode possuir estados

Protocolos mais elaborados podem exigir que determinadas operações ocorram em determinada ordem.

Por exemplo:

```text
1. Conectar
2. Autenticar
3. Enviar comandos
4. Encerrar
```

O servidor pode possuir estados:

```text
CONNECTED
     │
     ▼
AUTHENTICATED
     │
     ▼
READY
     │
     ▼
CLOSED
```

Assim, um cliente que tentar:

```text
MSG Olá\n
```

antes de autenticar pode receber:

```text
ERROR NOT_AUTHENTICATED\n
```

Isso é chamado de **máquina de estados do protocolo**.

---

## 14.15 Exemplo de protocolo com autenticação

Podemos definir:

```text
LOGIN allan senha123\n
```

Resposta:

```text
OK\n
```

Depois disso:

```text
MSG Olá servidor\n
```

Resposta:

```text
OK\n
```

Se o cliente tentar enviar:

```text
MSG Olá servidor\n
```

antes do login:

```text
ERROR LOGIN_REQUIRED\n
```

O servidor precisa manter o estado da conexão:

```python
authenticated = False
```

Depois de um login válido:

```python
authenticated = True
```

Então:

```python
if not authenticated:
    client.sendall(b"ERROR LOGIN_REQUIRED\n")
```

Esse conceito será muito importante quando começarmos a criar aplicações de rede maiores.

---

## 14.16 Protocolo não é necessariamente um padrão da Internet

Quando falamos em protocolo, não significa necessariamente:

```text
HTTP
DNS
FTP
SSH
```

Nós também podemos criar um protocolo privado para uma aplicação.

Por exemplo:

```text
MEU_PROTOCOLO/1.0
```

com regras próprias.

Entretanto, protocolos reais precisam ser projetados com muito mais cuidado.

Eles precisam considerar:

- compatibilidade;
    
- segurança;
    
- versionamento;
    
- erros;
    
- tamanho dos dados;
    
- encoding;
    
- autenticação;
    
- integridade;
    
- timeouts;
    
- limites;
    
- concorrência.
    

---

## 14.17 Versionamento do protocolo

Uma aplicação pode mudar ao longo do tempo.

Imagine que a versão inicial aceite:

```text
PING\n
```

Depois queremos adicionar:

```text
PING <id>\n
```

Clientes antigos podem não entender o novo formato.

Por isso, protocolos podem possuir versões.

Por exemplo:

```text
PROTO/1.0
```

ou:

```text
PROTO/2.0
```

Isso permite que cliente e servidor saibam quais regras estão utilizando.

---

## 14.18 Segurança não vem automaticamente do TCP

Outro conceito fundamental:

> TCP confiável não significa comunicação segura.

O TCP fornece características como:

```text
entrega ordenada
retransmissão
controle de fluxo
```

Mas não fornece automaticamente:

```text
criptografia
autenticação da identidade
confidencialidade
proteção contra leitura dos dados
```

Por exemplo, se enviarmos:

```python
client.sendall(b"senha123\n")
```

o TCP não transforma isso automaticamente em dados criptografados.

Para comunicação segura, normalmente utilizamos mecanismos adicionais, como **TLS**.

---

## 14.19 TCP + protocolo da aplicação + segurança

Uma arquitetura mais completa pode ser:

```text
┌──────────────────────────────┐
│ Aplicação                    │
│                              │
│ Login / Chat / Arquivos      │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Protocolo da aplicação       │
│                              │
│ framing + comandos + dados   │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ TLS                          │
│                              │
│ criptografia + autenticação  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ TCP                          │
│                              │
│ transporte confiável         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ IP                           │
└──────────────────────────────┘
```

Esse modelo ajuda a entender por que tecnologias como HTTPS não são simplesmente "um TCP diferente".

Existe uma composição de camadas.

---

## 14.20 Modelo mental desta parte

Ao construir uma aplicação TCP, pense:

```text
Socket
   ↓
TCP
   ↓
Fluxo de bytes
   ↓
Framing
   ↓
Mensagem
   ↓
Comando
   ↓
Dados
   ↓
Ação
   ↓
Resposta
```

Por exemplo:

```text
client.sendall(b"PING\n")
```

não significa simplesmente:

```text
"enviei uma mensagem"
```

O que realmente acontece conceitualmente é:

```text
"PING\n"
   ↓
bytes
   ↓
TCP
   ↓
rede
   ↓
TCP do servidor
   ↓
recv()
   ↓
buffer
   ↓
framing
   ↓
"PING"
   ↓
protocolo
   ↓
PONG
```

Essa separação é uma das bases para entender programação de redes.

---

## Resumo da Parte

- Um protocolo define **regras de comunicação** entre programas.
    
- TCP fornece o transporte, mas não define o significado dos dados.
    
- Podemos criar protocolos próprios sobre TCP.
    
- Um protocolo pode definir:
    
    - comandos;
        
    - argumentos;
        
    - respostas;
        
    - erros;
        
    - estados;
        
    - versionamento;
        
    - framing.
        
- Um modelo simples é:
    

```text
COMANDO DADOS\n
```

- Um protocolo pode seguir o modelo:
    

```text
requisição → resposta
```

- Cliente e servidor precisam concordar com o mesmo formato.
    
- Uma aplicação pode manter estados como:
    

```text
CONNECTED
AUTHENTICATED
READY
CLOSED
```

- TCP não fornece criptografia por padrão.
    
- Segurança é uma responsabilidade de camadas adicionais, como TLS.
    
- O fluxo completo pode ser entendido como:
    

```text
Aplicação
   ↓
Protocolo
   ↓
Framing
   ↓
TCP
   ↓
IP
   ↓
Rede
```

---

# 15. UDP e sockets `SOCK_DGRAM`

## 15.1 O que é UDP?

Até agora, o foco principal foi o **TCP**, utilizado em Python através de:

```python
socket.SOCK_STREAM
```

Agora vamos estudar outro protocolo de transporte muito importante: o **UDP**.

Em Python, um socket UDP normalmente é criado utilizando:

```python
socket.SOCK_DGRAM
```

Exemplo:

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

Diferentemente do TCP, o UDP trabalha com **datagramas**.

Podemos pensar em um datagrama como uma unidade individual de dados enviada pela aplicação.

---

## 15.2 TCP vs UDP

A diferença fundamental pode ser resumida assim:

```text
TCP
↓
fluxo de bytes

UDP
↓
datagramas
```

No TCP:

```text
send()
send()
send()
   ↓
fluxo contínuo de bytes
```

No UDP:

```text
sendto()
   ↓
datagrama

sendto()
   ↓
outro datagrama

sendto()
   ↓
outro datagrama
```

Cada envio UDP representa um datagrama separado.

---

## 15.3 UDP não estabelece uma conexão TCP

No TCP, normalmente temos:

```text
Cliente
   │
   │ connect()
   ▼
Servidor
   │
   │ accept()
   ▼
Conexão estabelecida
```

No UDP não existe esse processo de estabelecimento de conexão TCP.

Não temos:

```python
server.listen()
```

nem:

```python
server.accept()
```

para receber datagramas UDP.

O servidor normalmente faz:

```python
server.bind(("127.0.0.1", 4444))
```

e fica aguardando datagramas.

---

## 15.4 Criando um socket UDP

A criação é semelhante à do TCP:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)
```

A diferença principal está no tipo:

```python
socket.SOCK_DGRAM
```

Em vez de:

```python
socket.SOCK_STREAM
```

Temos:

```text
SOCK_STREAM → normalmente TCP
SOCK_DGRAM  → normalmente UDP
```

---

## 15.5 Servidor UDP

Um servidor UDP simples pode ser:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

server.bind(("127.0.0.1", 4444))

print("Servidor UDP aguardando dados...")

while True:
    data, address = server.recvfrom(1024)

    print("Cliente:", address)
    print("Mensagem:", data.decode())
```

Observe que não utilizamos:

```python
listen()
```

nem:

```python
accept()
```

O servidor simplesmente:

```text
bind()
  ↓
recvfrom()
```

---

## 15.6 `recvfrom()`

Para receber um datagrama UDP, utilizamos:

```python
data, address = server.recvfrom(1024)
```

### Retorno

O método retorna:

```text
(data, address)
```

Onde:

|Valor|Tipo|Significado|
|---|---|---|
|`data`|`bytes`|Dados recebidos|
|`address`|`tuple`|Endereço do remetente|

Por exemplo:

```python
data, address = server.recvfrom(1024)
```

pode resultar em:

```python
data
```

```text
b"Olá servidor"
```

e:

```python
address
```

```python
("127.0.0.1", 53241)
```

Assim sabemos quem enviou o datagrama.

---

## 15.7 `recvfrom()` preserva o datagrama

Essa é uma diferença extremamente importante em relação ao TCP.

Imagine que o cliente UDP envie:

```python
client.sendto(b"Olá servidor", address)
```

O servidor faz:

```python
data, address = server.recvfrom(1024)
```

Se o datagrama chegou inteiro, `data` representa **aquele datagrama**.

No TCP, não existe essa preservação de fronteira.

No UDP:

```text
Datagrama 1
───────────

Datagrama 2
───────────

Datagrama 3
───────────
```

são unidades independentes.

---

## 15.8 Enviando um datagrama com `sendto()`

O método mais comum para enviar dados UDP é:

```python
sendto(data, address)
```

Exemplo:

```python
client.sendto(
    b"Olá servidor",
    ("127.0.0.1", 4444)
)
```

Aqui:

```python
b"Olá servidor"
```

é o conteúdo.

E:

```python
("127.0.0.1", 4444)
```

é o destino.

---

## 15.9 Cliente UDP completo

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

server_address = ("127.0.0.1", 4444)

client.sendto(
    b"Olá servidor",
    server_address
)

client.close()
```

Observe que não existe:

```python
client.connect(...)
```

e não existe:

```python
client.accept()
```

O destino é informado diretamente para:

```python
sendto()
```

---

## 15.10 Servidor UDP completo

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

server.bind(("127.0.0.1", 4444))

print("Servidor UDP iniciado.")

while True:
    data, address = server.recvfrom(1024)

    print(f"Cliente: {address}")
    print(f"Mensagem: {data.decode()}")
```

Execução:

```text
Servidor UDP iniciado.
Cliente: ('127.0.0.1', 53241)
Mensagem: Olá servidor
```

O servidor pode receber datagramas de diferentes clientes através do mesmo socket.

---

## 15.11 Respondendo ao cliente

O servidor já recebe o endereço do remetente:

```python
data, address = server.recvfrom(1024)
```

Podemos utilizar esse endereço para responder:

```python
server.sendto(
    b"Mensagem recebida!",
    address
)
```

Então o servidor fica:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

server.bind(("127.0.0.1", 4444))

while True:
    data, address = server.recvfrom(1024)

    print("Recebido:", data.decode())

    server.sendto(
        b"Mensagem recebida!",
        address
    )
```

Isso já é suficiente para criar um pequeno **echo server UDP**.

---

## 15.12 Echo server

Um **echo server** simplesmente devolve ao remetente os mesmos dados que recebeu.

Exemplo:

```python
data, address = server.recvfrom(1024)

server.sendto(
    data,
    address
)
```

Se o cliente enviar:

```text
Olá
```

o servidor responde:

```text
Olá
```

Fluxo:

```text
Cliente
   │
   │ "Olá"
   ▼
Servidor UDP
   │
   │ "Olá"
   ▼
Cliente
```

Esse tipo de servidor é muito útil para estudar comunicação de rede.

---

## 15.13 `send()` e `sendto()` no UDP

Para UDP, o método mais comum quando não há uma conexão lógica configurada é:

```python
sendto()
```

Exemplo:

```python
client.sendto(
    b"Olá",
    ("127.0.0.1", 4444)
)
```

Já:

```python
send()
```

pode ser utilizado quando o socket UDP foi previamente associado a um destino através de:

```python
connect()
```

Por exemplo:

```python
client.connect(("127.0.0.1", 4444))

client.send(b"Olá")
```

Nesse caso, o socket UDP possui um destino padrão.

---

## 15.14 `connect()` em UDP

Aqui existe uma sutileza importante.

Quando fazemos:

```python
client.connect(("127.0.0.1", 4444))
```

em um socket UDP, isso **não cria uma conexão TCP**.

Não existe o three-way handshake do TCP:

```text
SYN
SYN-ACK
ACK
```

O `connect()` em UDP configura o destino padrão do socket no sistema operacional.

Depois disso, podemos usar:

```python
send()
```

e:

```python
recv()
```

em vez de:

```python
sendto()
```

e:

```python
recvfrom()
```

---

## 15.15 UDP não significa "sem endereço"

Mesmo sem uma conexão TCP, cada datagrama precisa ter um destino.

Por exemplo:

```python
client.sendto(
    b"Olá",
    ("127.0.0.1", 4444)
)
```

O endereço:

```python
("127.0.0.1", 4444)
```

indica:

```text
IP:
127.0.0.1

Porta:
4444
```

Portanto, UDP continua utilizando:

```text
IP + porta
```

para identificar endpoints.

---

## 15.16 UDP não garante entrega

Aqui está uma das maiores diferenças entre UDP e TCP.

O UDP não garante que o datagrama chegará ao destino.

Por exemplo:

```text
Cliente
   │
   │ Datagrama
   ▼
  rede
   │
   X
   │
Servidor
```

O datagrama pode ser perdido.

O UDP não possui automaticamente o mesmo mecanismo de retransmissão do TCP.

---

## 15.17 UDP não garante ordem

Imagine que o cliente envie:

```text
Datagrama 1 → "A"
Datagrama 2 → "B"
Datagrama 3 → "C"
```

O servidor pode receber:

```text
A
C
B
```

Em uma rede IP, não devemos assumir que os datagramas UDP chegarão na mesma ordem em que foram enviados.

Portanto:

```text
enviado:
A → B → C

recebido:
A → C → B
```

é algo que o protocolo da aplicação precisa estar preparado para tratar, caso a ordem seja importante.

---

## 15.18 UDP não garante ausência de duplicação

Também não devemos projetar uma aplicação assumindo que cada datagrama será processado exatamente uma vez.

Se a aplicação exige comportamento do tipo:

```text
uma operação → exatamente uma execução
```

ela precisa implementar mecanismos próprios para isso.

Por exemplo, podemos colocar um identificador:

```text
ID=123
```

em cada mensagem.

Assim o servidor pode identificar uma solicitação repetida.

---

## 15.19 UDP preserva o limite do datagrama

Apesar de não oferecer confiabilidade como TCP, o UDP possui uma característica muito importante:

> O limite de cada datagrama é preservado.

Se o cliente enviar:

```python
client.sendto(b"ABC", address)
client.sendto(b"DEF", address)
```

o servidor não deve interpretar isso como um único fluxo:

```text
ABCDEF
```

Cada chamada representa um datagrama separado.

Conceitualmente:

```text
Datagrama 1:
ABC

Datagrama 2:
DEF
```

Isso diferencia bastante UDP de TCP.

---

## 15.20 Tamanho do buffer

Considere:

```python
data, address = server.recvfrom(1024)
```

O valor:

```text
1024
```

define o tamanho máximo de dados que estamos preparados para receber naquela chamada.

Não devemos simplesmente assumir que:

```python
recvfrom(1024)
```

é uma forma de receber um datagrama arbitrariamente grande.

O tamanho do datagrama e o comportamento quando o buffer é insuficiente precisam ser considerados no projeto do protocolo.

---

## 15.21 UDP e perda de dados

Imagine um sistema de monitoramento enviando:

```text
temperatura = 25.1
temperatura = 25.2
temperatura = 25.3
temperatura = 25.4
```

Se um datagrama for perdido:

```text
25.1
25.2
X
25.4
```

talvez isso não seja um problema grave.

O próximo valor:

```text
25.4
```

ainda fornece uma informação atualizada.

Esse é um dos cenários onde UDP pode ser interessante.

---

## 15.22 Quando UDP pode ser útil?

UDP pode ser interessante quando:

- baixa latência é importante;
    
- pequenas perdas podem ser toleradas;
    
- a aplicação implementará sua própria confiabilidade;
    
- mensagens são independentes;
    
- não precisamos de uma conexão TCP tradicional;
    
- o protocolo precisa preservar mensagens individuais.
    

Exemplos de aplicações que historicamente utilizam UDP ou podem utilizá-lo incluem:

```text
DNS
DHCP
streaming em determinados contextos
jogos online
VoIP
telemetria
protocolos de descoberta
```

Isso não significa que todas essas aplicações utilizem exclusivamente UDP.

Protocolos modernos podem utilizar diferentes transportes dependendo da situação.

---

## 15.23 TCP vs UDP

|Característica|TCP|UDP|
|---|---|---|
|Tipo de socket|`SOCK_STREAM`|`SOCK_DGRAM`|
|Modelo|Fluxo de bytes|Datagramas|
|Conexão TCP|Sim|Não|
|`listen()`|Sim|Não|
|`accept()`|Sim|Não|
|`send()`|Sim|Sim, após `connect()`|
|`sendto()`|Não é o padrão|Sim|
|`recv()`|Sim|Sim, após `connect()`|
|`recvfrom()`|Não é o padrão|Sim|
|Entrega confiável|Sim|Não|
|Ordenação garantida|Sim|Não|
|Retransmissão automática|Sim|Não|
|Limite de mensagem preservado|Não|Sim|
|Controle de fluxo|Sim|Não da mesma forma|
|Overhead|Maior|Menor|

---

## 15.24 Um detalhe importante: UDP não significa necessariamente "mais rápido"

É comum encontrar a explicação:

```text
TCP = lento
UDP = rápido
```

Essa simplificação é inadequada.

UDP possui menos mecanismos integrados, como:

```text
retransmissão
controle de congestionamento
controle de fluxo
ordenação
```

Isso pode permitir menor overhead e baixa latência em determinados cenários.

Mas a velocidade real depende de:

- rede;
    
- tamanho dos dados;
    
- congestionamento;
    
- distância;
    
- implementação;
    
- protocolo da aplicação;
    
- quantidade de retransmissões necessárias.
    

Se a aplicação precisar implementar manualmente todos os mecanismos que o TCP já fornece, o benefício pode diminuir ou desaparecer.

---

## 15.25 UDP pode ter confiabilidade própria

Nada impede que um protocolo baseado em UDP implemente mecanismos próprios.

Por exemplo:

```text
ID: 100
SEQ: 1
DADOS: ...
```

O receptor poderia responder:

```text
ACK: 1
```

Se o remetente não receber o ACK:

```text
timeout
   ↓
retransmissão
```

Teríamos algo conceitualmente parecido com:

```text
Cliente
   │
   │ DATA #1
   ▼
Servidor
   │
   │ ACK #1
   ▼
Cliente
```

Se o ACK não chegar:

```text
Cliente
   │
   │ DATA #1
   X
   │
   │ timeout
   │
   └────── DATA #1 novamente
```

Isso demonstra que confiabilidade pode ser construída na camada da aplicação.

---

## 15.26 Modelo mental do UDP

O modelo mental correto é:

```text
Aplicação
   ↓
Datagrama
   ↓
UDP
   ↓
IP
   ↓
Rede
```

Cada datagrama é uma unidade independente.

Diferentemente do TCP:

```text
TCP:
bytes → bytes → bytes → bytes
```

UDP trabalha conceitualmente com:

```text
datagrama
datagrama
datagrama
datagrama
```

E cada datagrama possui:

```text
dados
+
informações necessárias para transporte
```

---

## 15.27 TCP e UDP no mesmo sistema

Um mesmo computador pode possuir:

```text
TCP 192.168.1.10:80
UDP 192.168.1.10:53
TCP 192.168.1.10:22
```

TCP e UDP possuem espaços de portas separados.

Portanto, é possível ter, por exemplo:

```text
TCP → porta 4444
UDP → porta 4444
```

simultaneamente.

Isso não representa necessariamente um conflito.

O protocolo de transporte faz parte da identificação da comunicação.

---

## Resumo da Parte

- UDP é um protocolo de transporte baseado em **datagramas**.
    
- Em Python, normalmente utilizamos:
    

```python
socket.SOCK_DGRAM
```

- UDP não utiliza o processo TCP de:
    

```text
connect()
SYN
SYN-ACK
ACK
accept()
```

- O servidor UDP normalmente utiliza:
    

```python
bind()
recvfrom()
```

- O cliente pode utilizar:
    

```python
sendto()
```

- `recvfrom()` retorna:
    

```python
(data, address)
```

- UDP preserva o limite dos datagramas.
    
- UDP não garante:
    
    - entrega;
        
    - ordem;
        
    - retransmissão;
        
    - confiabilidade;
        
    - exatamente uma entrega.
        
- UDP pode ser útil quando baixa latência, mensagens independentes ou tolerância a perdas são importantes.
    
- `connect()` em UDP **não significa que uma conexão TCP foi estabelecida**.
    
- Um protocolo baseado em UDP pode implementar sua própria confiabilidade.
    
- A diferença fundamental é:
    

```text
TCP
→ fluxo de bytes

UDP
→ datagramas
```

---

# 16. Comparando TCP e UDP na prática

## 16.1 Por que comparar TCP e UDP?

Agora que já entendemos individualmente:

- `SOCK_STREAM` e TCP;
    
- `SOCK_DGRAM` e UDP;
    
- fluxo de bytes;
    
- datagramas;
    
- conexões;
    
- framing;
    
- confiabilidade;
    
- `send()` / `recv()`;
    
- `sendto()` / `recvfrom()`,
    

podemos comparar os dois modelos diretamente.

A diferença não é simplesmente:

```text
TCP = confiável
UDP = não confiável
```

A diferença envolve **como os dados são transportados e quais responsabilidades ficam com o protocolo de transporte ou com a aplicação**.

---

## 16.2 Fluxo TCP

No TCP, a aplicação trabalha com um fluxo:

```text
Aplicação
    │
    │ bytes
    ▼
   TCP
    │
    ▼
  Rede
```

Imagine que a aplicação envie:

```python
client.sendall(b"ABC")
client.sendall(b"DEF")
client.sendall(b"GHI")
```

O TCP pode transportar isso como um fluxo:

```text
ABCDEFGHI
```

O receptor pode receber:

```python
b"ABCDEFGHI"
```

ou:

```python
b"ABC"
b"DEFG"
b"HI"
```

ou:

```python
b"A"
b"BCDE"
b"FGHI"
```

A divisão não é determinada pelas chamadas `send()`.

---

## 16.3 Datagramas UDP

No UDP, os limites dos datagramas são preservados.

Se enviarmos:

```python
client.sendto(b"ABC", address)
client.sendto(b"DEF", address)
client.sendto(b"GHI", address)
```

temos:

```text
Datagrama 1 → ABC
Datagrama 2 → DEF
Datagrama 3 → GHI
```

O receptor recebe cada datagrama individualmente.

Conceitualmente:

```text
recvfrom()
    ↓
ABC

recvfrom()
    ↓
DEF

recvfrom()
    ↓
GHI
```

Essa diferença é fundamental.

---

## 16.4 Confiabilidade

TCP possui mecanismos internos para fornecer uma transmissão confiável de bytes.

Entre os mecanismos envolvidos estão:

- números de sequência;
    
- confirmações;
    
- retransmissões;
    
- controle de fluxo;
    
- controle de congestionamento.
    

Isso permite que a aplicação trabalhe com uma abstração de fluxo confiável.

UDP não fornece esses mecanismos da mesma maneira.

Se um datagrama for perdido:

```text
Cliente
   │
   │ DATA
   X
   │
Servidor
```

não existe automaticamente uma retransmissão equivalente à realizada pelo TCP.

---

## 16.5 A confiabilidade tem um custo

Os mecanismos do TCP exigem processamento e comunicação adicional.

Por exemplo:

```text
dados
ACK
retransmissão
controle de sequência
controle de congestionamento
```

Isso não significa que TCP seja simplesmente "lento".

Significa que TCP possui **mais responsabilidades**.

UDP possui uma abstração mais simples:

```text
aplicação
   ↓
datagrama
   ↓
UDP
   ↓
IP
```

Se a aplicação não precisa de todas as garantias do TCP, ela pode utilizar UDP e definir apenas os mecanismos que realmente necessita.

---

## 16.6 Exemplo: transferência de arquivo

Imagine que queremos transferir:

```text
arquivo.zip
```

com:

```text
500 MB
```

Se alguns bytes forem perdidos, o arquivo poderá ficar corrompido.

Nesse cenário, confiabilidade é extremamente importante.

TCP é uma escolha natural porque fornece:

```text
ordenação
+
retransmissão
+
entrega confiável
```

A aplicação recebe um fluxo de bytes e pode reconstruir o arquivo.

---

## 16.7 Exemplo: atualização de posição em um jogo

Agora imagine um jogo enviando:

```text
posição X=100 Y=200
```

dezenas de vezes por segundo.

Imagine que um pacote contendo:

```text
X=100 Y=200
```

seja perdido.

Pouco depois chega:

```text
X=101 Y=203
```

Nesse caso, talvez não faça sentido retransmitir a posição antiga.

A informação mais recente já substituiu a anterior.

Portanto, dependendo da arquitetura do jogo, UDP pode ser uma opção adequada.

---

## 16.8 Exemplo: voz em tempo real

Imagine uma comunicação de voz.

Se um pequeno pacote de áudio for perdido:

```text
áudio 1
áudio 2
X
áudio 4
áudio 5
```

pode ser preferível continuar reproduzindo:

```text
áudio 1 → áudio 2 → áudio 4 → áudio 5
```

em vez de esperar uma retransmissão do áudio antigo.

Para aplicações em tempo real, **atrasar o fluxo inteiro pode ser pior do que perder uma pequena quantidade de dados**.

Por isso, protocolos de mídia em tempo real podem utilizar UDP ou tecnologias construídas sobre UDP.

---

## 16.9 Exemplo: DNS

Uma consulta DNS tradicional pode ser pequena:

```text
Qual é o IP de exemplo.com?
```

A resposta também pode ser relativamente pequena.

Em muitos cenários, UDP é conveniente porque:

```text
requisição
   ↓
datagrama
   ↓
resposta
```

não exige o estabelecimento de uma conexão TCP para cada consulta.

Porém, DNS também pode utilizar TCP em determinadas situações.

Portanto, não devemos memorizar:

```text
DNS = UDP
```

como uma regra absoluta.

O correto é entender **por que determinado transporte é usado em determinado contexto**.

---

## 16.10 TCP exige uma conexão lógica

No TCP, temos uma conexão identificada por dois endpoints:

```text
Cliente
192.168.1.10:53241

Servidor
192.168.1.20:4444
```

A conexão pode ser representada por:

```text
192.168.1.10:53241
        ↕
192.168.1.20:4444
```

O TCP mantém estado para essa comunicação.

---

## 16.11 UDP trabalha com datagramas independentes

No UDP:

```text
192.168.1.10:53241
        │
        │ datagrama
        ▼
192.168.1.20:4444
```

Outro datagrama pode vir de:

```text
192.168.1.11:53242
```

e também ser enviado para:

```text
192.168.1.20:4444
```

O servidor UDP pode receber ambos:

```text
Datagrama 1
origem: 192.168.1.10:53241

Datagrama 2
origem: 192.168.1.11:53242
```

Por isso:

```python
data, address = server.recvfrom(1024)
```

retorna o endereço do remetente.

---

## 16.12 O modelo de servidor TCP

Um servidor TCP normalmente segue:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
   ↓
recv()/send()
   ↓
close()
```

Representação:

```text
                 ┌──────────────┐
                 │   Servidor   │
                 └──────┬───────┘
                        │
                     listen()
                        │
                ┌───────┴───────┐
                │               │
             Cliente 1       Cliente 2
                │               │
             socket           socket
                │               │
             recv/send       recv/send
```

Cada chamada a:

```python
accept()
```

produz um socket específico para aquela conexão.

---

## 16.13 O modelo de servidor UDP

Um servidor UDP normalmente segue:

```text
socket()
   ↓
bind()
   ↓
recvfrom()
   ↓
sendto()
```

Não existe:

```text
listen()
accept()
```

O socket pode receber datagramas de diversos clientes:

```text
Cliente 1 ───┐
             │
Cliente 2 ───┼──→ Socket UDP
             │
Cliente 3 ───┘
```

Cada datagrama informa seu remetente.

---

## 16.14 Comparação dos fluxos

### TCP

```text
Cliente
   │
   │ connect()
   ▼
Servidor
   │
   │ accept()
   ▼
Socket conectado
   │
   ├── send()
   ├── recv()
   ├── send()
   └── recv()
```

### UDP

```text
Cliente
   │
   │ sendto()
   ▼
Servidor
   │
   │ recvfrom()
   ▼
Datagrama
   │
   └── sendto()
        ↓
      Cliente
```

---

## 16.15 Tabela de comparação

|Característica|TCP|UDP|
|---|---|---|
|Socket Python|`SOCK_STREAM`|`SOCK_DGRAM`|
|Unidade principal|Fluxo|Datagrama|
|Conexão TCP|Sim|Não|
|Handshake|Sim|Não|
|`listen()`|Sim|Não|
|`accept()`|Sim|Não|
|`send()`|Sim|Sim, com UDP conectado|
|`sendall()`|Sim|Não é o mecanismo típico|
|`sendto()`|Não|Sim|
|`recv()`|Sim|Sim, com UDP conectado|
|`recvfrom()`|Não é o mecanismo típico|Sim|
|Ordem|Garantida|Não garantida|
|Retransmissão|Automática|Não|
|Controle de fluxo|Sim|Não como TCP|
|Controle de congestionamento|Sim|Não como TCP|
|Limite de mensagem|Não preservado|Preservado|
|Estado da conexão|Mantido|Não há conexão TCP|
|Overhead|Maior|Menor|
|Complexidade da aplicação|Menor para transporte confiável|Pode ser maior dependendo do protocolo|

---

## 16.16 Um erro comum: "UDP não é confiável, então não serve para nada"

Isso está errado.

UDP simplesmente fornece **menos garantias**.

Isso pode ser exatamente o que uma aplicação deseja.

Imagine um protocolo que envia:

```text
telemetria
telemetria
telemetria
telemetria
telemetria
```

Se cada mensagem representa o estado atual de um dispositivo, perder uma delas talvez seja aceitável.

A aplicação pode simplesmente esperar a próxima.

---

## 16.17 Outro erro: "TCP sempre é melhor"

Também está errado.

TCP é excelente quando precisamos de:

```text
fluxo confiável
+
ordenado
+
retransmissão
```

Mas essas características podem não ser desejáveis em todos os cenários.

Por exemplo, se a aplicação precisa:

```text
baixa latência
+
mensagens independentes
+
aceitar alguma perda
```

UDP pode ser mais adequado.

A escolha depende dos requisitos da aplicação.

---

## 16.18 Escolhendo entre TCP e UDP

Uma forma prática de decidir:

### Use TCP quando:

```text
A perda de dados é inaceitável
```

e:

```text
A ordem dos dados importa
```

e:

```text
Precisamos de um fluxo confiável
```

Exemplos:

- transferência de arquivos;
    
- muitas APIs;
    
- SSH;
    
- bancos de dados;
    
- páginas web;
    
- comunicação em que todos os dados precisam chegar corretamente.
    

---

### Considere UDP quando:

```text
A aplicação tolera perdas
```

ou:

```text
A latência é mais importante que retransmitir dados antigos
```

ou:

```text
Precisamos preservar mensagens individuais
```

ou:

```text
Queremos implementar nosso próprio mecanismo de confiabilidade
```

Exemplos:

- determinados jogos online;
    
- telemetria;
    
- descoberta de serviços;
    
- determinados sistemas de voz e vídeo;
    
- protocolos específicos que utilizam datagramas.
    

---

## 16.19 Não confunda protocolo com aplicação

Um mesmo tipo de aplicação pode utilizar diferentes transportes dependendo do protocolo utilizado.

Por exemplo:

```text
Aplicação
   ↓
Protocolo
   ↓
Transporte
```

Não devemos pensar:

```text
"Essa aplicação usa UDP."
```

como uma verdade universal.

É melhor pensar:

```text
"Este protocolo utiliza UDP neste cenário."
```

A arquitetura pode variar conforme:

- versão;
    
- tamanho dos dados;
    
- segurança;
    
- necessidade de confiabilidade;
    
- latência;
    
- ambiente de rede.
    

---

## 16.20 Modelo mental final: TCP

```text
Aplicação
    │
    │ fluxo de bytes
    ▼
   TCP
    │
    ├── ordenação
    ├── retransmissão
    ├── confiabilidade
    ├── controle de fluxo
    └── controle de congestionamento
    │
    ▼
   IP
    │
    ▼
  Rede
```

---

## 16.21 Modelo mental final: UDP

```text
Aplicação
    │
    │ datagramas
    ▼
   UDP
    │
    └── transporte simples
    │
    ▼
   IP
    │
    ▼
  Rede
```

Se a aplicação precisar de:

```text
ACK
sequenciamento
retransmissão
detecção de duplicatas
```

ela poderá implementar esses mecanismos por conta própria.

---

## 16.22 O ponto mais importante

A pergunta correta não é:

> "TCP é melhor que UDP?"

ou:

> "UDP é melhor que TCP?"

A pergunta correta é:

> **"Quais garantias minha aplicação precisa?"**

Se a resposta for:

```text
Preciso que os dados cheguem corretamente e em ordem.
```

TCP provavelmente é uma boa escolha.

Se a resposta for:

```text
Preciso enviar mensagens independentes, tolero perdas e quero evitar retransmitir dados antigos.
```

UDP pode ser uma escolha melhor.

---

## Resumo da Parte

- TCP e UDP são protocolos de transporte, mas oferecem modelos diferentes.
    
- TCP fornece um **fluxo confiável e ordenado de bytes**.
    
- UDP fornece **datagramas independentes**.
    
- TCP possui mecanismos de:
    
    - retransmissão;
        
    - ordenação;
        
    - controle de fluxo;
        
    - controle de congestionamento.
        
- UDP não fornece essas garantias da mesma forma.
    
- TCP utiliza normalmente:
    

```text
socket()
bind()
listen()
accept()
send()/recv()
```

- UDP utiliza normalmente:
    

```text
socket()
bind()
sendto()/recvfrom()
```

- TCP não preserva fronteiras de mensagens.
    
- UDP preserva os limites dos datagramas.
    
- A escolha entre TCP e UDP depende dos requisitos da aplicação.
    
- Não existe um protocolo universalmente "melhor".
    
- O modelo mental principal é:
    

```text
TCP → fluxo confiável de bytes

UDP → datagramas independentes
```

---

# 17. Opções de socket com `setsockopt()`

## 17.1 O que são opções de socket?

Até agora criamos sockets utilizando:

```python
socket.socket()
```

e utilizamos métodos como:

```python
bind()
listen()
accept()
connect()
send()
recv()
```

Porém, o sistema operacional oferece diversas configurações que permitem alterar o comportamento de um socket.

Essas configurações são chamadas de **opções de socket**.

Em Python, o principal método utilizado para configurá-las é:

```python
setsockopt()
```

---

## 17.2 Sintaxe

A forma geral é:

```python
socket.setsockopt(level, optname, value)
```

Exemplo:

```python
server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)
```

A estrutura é:

```text
setsockopt(
    nível,
    opção,
    valor
)
```

---

## 17.3 Parâmetros

| Parâmetro | Tipo                | Obrigatório | Descrição                        |
| --------- | ------------------- | ----------: | -------------------------------- |
| `level`   | `int`               |         Sim | Nível onde a opção está definida |
| `optname` | `int`               |         Sim | Nome da opção                    |
| `value`   | `int`, `bytes` etc. |         Sim | Valor atribuído à opção          |

A opção mais comum para começar é:

```python
socket.SOL_SOCKET
```

Ela indica que estamos configurando uma opção relacionada ao próprio socket.

---

## 17.4 `SOL_SOCKET`

Exemplo:

```python
server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)
```

Aqui:

```python
socket.SOL_SOCKET
```

é o nível da opção.

Podemos pensar:

```text
SOL_SOCKET
     ↓
opções gerais do socket
```

Existem outros níveis relacionados a protocolos específicos.

Por exemplo:

```python
socket.IPPROTO_TCP
```

representa opções relacionadas ao TCP.

Assim:

```text
SOL_SOCKET
    ↓
opções gerais do socket

IPPROTO_TCP
    ↓
opções específicas do TCP
```

---

# 17.5 `SO_REUSEADDR`

Uma das opções mais conhecidas em servidores TCP é:

```python
socket.SO_REUSEADDR
```

Ela pode ser configurada assim:

```python
server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)
```

Normalmente ela aparece **antes do `bind()`**:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)

server.bind(("127.0.0.1", 4444))

server.listen()
```

A ordem é importante:

```text
socket()
   ↓
setsockopt()
   ↓
bind()
   ↓
listen()
```

---

## 17.6 Por que `SO_REUSEADDR` é útil?

Você já encontrou anteriormente um erro como:

```text
OSError: [Errno 98] Address already in use
```

Isso significa que o endereço local que você tentou utilizar não estava disponível naquele momento.

Um dos cenários comuns envolve o estado `TIME_WAIT` de conexões TCP anteriores.

Por exemplo:

```text
Servidor
127.0.0.1:4444
```

foi encerrado.

Logo depois você tenta iniciar novamente:

```python
server.bind(("127.0.0.1", 4444))
```

Dependendo das condições e das regras do sistema operacional, o `bind()` pode falhar.

`SO_REUSEADDR` pode permitir que o servidor reutilize o endereço local em situações apropriadas.

---

## 17.7 `SO_REUSEADDR` não significa "ignorar qualquer conflito"

É importante não interpretar:

```python
SO_REUSEADDR
```

como:

> "Pode usar a porta mesmo se outro processo estiver usando."

Isso não é o objetivo.

Por exemplo, se outro processo estiver realmente escutando:

```text
127.0.0.1:4444
```

simplesmente ativar:

```python
SO_REUSEADDR
```

não significa que dois servidores TCP poderão normalmente ocupar o mesmo endpoint.

O objetivo está relacionado principalmente à **reutilização de endereços em determinadas condições**, especialmente após conexões anteriores.

---

## 17.8 Exemplo completo com `SO_REUSEADDR`

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)

server.bind(("127.0.0.1", 4444))

server.listen()

print("Servidor aguardando conexão...")

client, address = server.accept()

print("Cliente conectado:", address)

client.close()
server.close()
```

A configuração ocorre antes do `bind()`:

```text
socket()
 ↓
setsockopt()
 ↓
bind()
 ↓
listen()
 ↓
accept()
```

---

# 17.9 `getsockopt()`

Se `setsockopt()` serve para **configurar** uma opção, `getsockopt()` serve para **consultar** uma opção.

Sintaxe básica:

```python
socket.getsockopt(level, optname)
```

Exemplo:

```python
value = server.getsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR
)

print(value)
```

Podemos verificar se a opção está habilitada.

---

## 17.10 Ativando e desativando opções booleanas

Algumas opções funcionam como valores booleanos.

Por exemplo:

```python
server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)
```

Ativa.

Para desativar:

```python
server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    0
)
```

Conceitualmente:

```text
1 → habilitado
0 → desabilitado
```

---

# 17.11 `SO_KEEPALIVE`

Outra opção importante é:

```python
socket.SO_KEEPALIVE
```

Ela pode ser habilitada:

```python
client.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_KEEPALIVE,
    1
)
```

O objetivo é permitir mecanismos de **keepalive TCP** para detectar determinadas situações em que uma conexão aparentemente estabelecida deixou de responder.

---

## 17.12 Por que keepalive existe?

Imagine:

```text
Cliente
   │
   │ conexão TCP
   │
   ▼
Servidor
```

A conexão fica estabelecida por muito tempo.

Por algum motivo, o caminho de rede deixa de funcionar:

```text
Cliente
   │
   X
   │
Servidor
```

A aplicação pode não descobrir imediatamente que o outro lado não está mais acessível.

O mecanismo TCP keepalive pode ajudar o sistema operacional a detectar esse tipo de situação.

---

## 17.13 Keepalive não é heartbeat da aplicação

É importante diferenciar:

```text
TCP keepalive
```

de:

```text
heartbeat da aplicação
```

Um heartbeat pode ser implementado pelo próprio protocolo:

```text
CLIENTE → PING
SERVIDOR → PONG
```

Nesse caso, a aplicação está conscientemente verificando se o serviço está respondendo.

Já o TCP keepalive é um mecanismo da camada TCP/sistema operacional.

São mecanismos diferentes.

---

# 17.14 `SO_RCVBUF`

Outra opção interessante é:

```python
socket.SO_RCVBUF
```

Ela está relacionada ao **buffer de recebimento** do socket.

Podemos consultar:

```python
size = client.getsockopt(
    socket.SOL_SOCKET,
    socket.SO_RCVBUF
)

print(size)
```

Esse buffer é utilizado pelo sistema operacional para armazenar dados recebidos antes que a aplicação os processe.

---

## 17.15 Buffer de recebimento

Imagine:

```text
Rede
  │
  ▼
Kernel
  │
  │ dados recebidos
  ▼
Buffer do socket
  │
  ▼
recv()
  │
  ▼
Aplicação
```

O kernel pode receber dados antes que a aplicação execute:

```python
recv()
```

Esses dados podem permanecer temporariamente no buffer associado ao socket.

---

# 17.16 `SO_SNDBUF`

Existe também:

```python
socket.SO_SNDBUF
```

relacionado ao buffer de envio.

Podemos consultar:

```python
size = client.getsockopt(
    socket.SOL_SOCKET,
    socket.SO_SNDBUF
)

print(size)
```

O modelo simplificado é:

```text
Aplicação
   │
   │ send()
   ▼
Buffer de envio
   │
   ▼
TCP/IP
   │
   ▼
Rede
```

Isso ajuda a entender por que:

```python
send()
```

não significa necessariamente que os dados já chegaram fisicamente ao destino.

---

# 17.17 `send()` e o kernel

Quando fazemos:

```python
client.send(data)
```

existe uma interação com o sistema operacional.

Simplificando:

```text
Python
  │
  │ send()
  ▼
Socket
  │
  ▼
Kernel
  │
  ▼
TCP
  │
  ▼
IP
  │
  ▼
Rede
```

A aplicação entrega os dados ao sistema operacional.

O kernel passa a gerenciar o envio através da pilha de rede.

---

# 17.18 `SO_ERROR`

Outra opção interessante é:

```python
socket.SO_ERROR
```

Ela pode ser consultada com:

```python
error = client.getsockopt(
    socket.SOL_SOCKET,
    socket.SO_ERROR
)

print(error)
```

Ela permite consultar um erro pendente associado ao socket em determinadas situações.

Se o valor for:

```text
0
```

isso normalmente indica ausência de erro pendente.

Valores diferentes de zero representam um código de erro.

---

# 17.19 `SO_BROADCAST`

Para determinados usos de UDP, existe:

```python
socket.SO_BROADCAST
```

Ela permite habilitar o envio de **broadcast** através do socket, quando suportado e apropriado.

Exemplo:

```python
sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_BROADCAST,
    1
)
```

Depois, um programa pode utilizar um endereço de broadcast apropriado.

Broadcast significa enviar um datagrama para múltiplos hosts de uma rede local que aceitem esse tipo de tráfego.

---

# 17.20 Unicast, broadcast e multicast

É importante diferenciar:

### Unicast

Um remetente:

```text
A
│
└──────→ B
```

Um destino.

---

### Broadcast

Um remetente:

```text
      ┌──→ B
      │
A ────┼──→ C
      │
      └──→ D
```

O objetivo é alcançar múltiplos hosts da rede local através de um endereço de broadcast.

---

### Multicast

Um remetente envia para um **grupo multicast**:

```text
           ┌──→ B
           │
A ───→ grupo multicast
           │
           └──→ D
```

Somente os hosts participantes do grupo recebem o tráfego multicast.

Esses conceitos aparecem principalmente em aplicações baseadas em UDP.

---

# 17.21 `SO_REUSEPORT`

Alguns sistemas operacionais também oferecem:

```python
socket.SO_REUSEPORT
```

Ela possui uma finalidade diferente de `SO_REUSEADDR`.

Dependendo do sistema operacional, pode permitir que múltiplos sockets façam `bind()` para o mesmo endereço/porta sob determinadas condições.

Isso pode ser utilizado em arquiteturas específicas para distribuição de tráfego.

Porém, não devemos assumir que:

```python
SO_REUSEPORT
```

possui exatamente o mesmo comportamento em todos os sistemas operacionais.

Seu comportamento depende da implementação da plataforma.

---

# 17.22 Nem toda opção existe em todo sistema

Um ponto importante:

```python
socket.SO_REUSEPORT
```

por exemplo, pode não estar disponível em todas as plataformas.

Por isso, aplicações portáveis precisam considerar:

```python
hasattr(socket, "SO_REUSEPORT")
```

Exemplo:

```python
if hasattr(socket, "SO_REUSEPORT"):
    server.setsockopt(
        socket.SOL_SOCKET,
        socket.SO_REUSEPORT,
        1
    )
```

Isso verifica se a constante existe na implementação atual.

---

# 17.23 Opções específicas do TCP

Nem todas as opções pertencem a:

```python
socket.SOL_SOCKET
```

Existem opções específicas do TCP.

Por exemplo:

```python
socket.IPPROTO_TCP
```

pode ser utilizado como nível:

```python
client.setsockopt(
    socket.IPPROTO_TCP,
    opção,
    valor
)
```

Isso nos leva a uma ideia importante:

```text
nível
  ↓
protocolo ou subsistema
  ↓
opção
```

---

# 17.24 Exemplo conceitual

Imagine:

```python
client.setsockopt(
    socket.IPPROTO_TCP,
    socket.TCP_NODELAY,
    1
)
```

A estrutura é:

```text
IPPROTO_TCP
      ↓
opção específica do TCP
      ↓
TCP_NODELAY
      ↓
valor 1
```

`TCP_NODELAY` está relacionado ao comportamento de envio de pequenos segmentos TCP e ao algoritmo de Nagle.

Não é necessário memorizar todos os detalhes dessa opção agora.

O mais importante é entender a estrutura:

```python
setsockopt(level, option, value)
```

---

# 17.25 Quando utilizar `setsockopt()`?

Não devemos utilizar opções de socket simplesmente porque existem.

Elas devem ser usadas quando existe uma necessidade específica.

Exemplos:

### Servidor TCP reiniciado frequentemente

Pode ser útil:

```python
SO_REUSEADDR
```

### Conexão de longa duração

Pode ser interessante estudar:

```python
SO_KEEPALIVE
```

### Aplicação específica com múltiplos workers

Pode fazer sentido investigar:

```python
SO_REUSEPORT
```

### Aplicação UDP com broadcast

Pode ser necessário:

```python
SO_BROADCAST
```

O importante é entender **por que** determinada opção está sendo utilizada.

---

# 17.26 Exemplo de configuração de servidor

Um servidor TCP comum pode ficar:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)

server.bind(("127.0.0.1", 4444))

server.listen()

print("Servidor iniciado.")

client, address = server.accept()

print("Cliente:", address)

client.close()
server.close()
```

Observe novamente a ordem:

```text
socket()
   ↓
setsockopt()
   ↓
bind()
   ↓
listen()
   ↓
accept()
```

---

# 17.27 Consultando uma opção

Podemos verificar:

```python
reuse = server.getsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR
)

print("SO_REUSEADDR:", reuse)
```

E consultar:

```python
keepalive = server.getsockopt(
    socket.SOL_SOCKET,
    socket.SO_KEEPALIVE
)

print("SO_KEEPALIVE:", keepalive)
```

Assim conseguimos inspecionar determinadas configurações do socket.

---

# 17.28 `setsockopt()` não altera o protocolo inteiro

É importante não pensar:

```python
setsockopt()
```

como algo que transforma:

```text
TCP → UDP
```

ou:

```text
UDP → TCP
```

O socket continua sendo do tipo criado originalmente.

Por exemplo:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

continua sendo um socket orientado a fluxo.

`setsockopt()` apenas configura determinadas propriedades suportadas pelo socket e pelo sistema operacional.

---

# 17.29 Relação com o kernel

Quando fazemos:

```python
server.setsockopt(...)
```

não estamos simplesmente alterando uma variável Python.

A configuração é repassada ao sistema operacional.

Podemos visualizar:

```text
Python
  │
  │ setsockopt()
  ▼
Kernel
  │
  ├── configuração do socket
  ├── TCP/UDP
  └── pilha de rede
```

Isso reforça o conceito estudado anteriormente:

```text
Python socket API
       ↓
      syscall
       ↓
     kernel
       ↓
   rede / hardware
```

A API `socket` é uma interface para recursos de rede disponibilizados pelo sistema operacional.

---

# 17.30 Tabela das principais opções estudadas

| Opção          | Nível comum   | Finalidade                                                   |
| -------------- | ------------- | ------------------------------------------------------------ |
| `SO_REUSEADDR` | `SOL_SOCKET`  | Permitir reutilização de endereço em determinadas condições  |
| `SO_REUSEPORT` | `SOL_SOCKET`  | Reutilização de porta em condições específicas da plataforma |
| `SO_KEEPALIVE` | `SOL_SOCKET`  | Habilitar TCP keepalive                                      |
| `SO_RCVBUF`    | `SOL_SOCKET`  | Consultar/configurar buffer de recebimento                   |
| `SO_SNDBUF`    | `SOL_SOCKET`  | Consultar/configurar buffer de envio                         |
| `SO_ERROR`     | `SOL_SOCKET`  | Consultar erro pendente                                      |
| `SO_BROADCAST` | `SOL_SOCKET`  | Permitir broadcast em sockets apropriados                    |
| `TCP_NODELAY`  | `IPPROTO_TCP` | Configuração relacionada ao algoritmo de Nagle               |

---

# 17.31 Modelo mental

Até agora temos:

```text
socket()
   ↓
cria o socket
   ↓
setsockopt()
   ↓
configura propriedades
   ↓
bind()
   ↓
associa endereço local
   ↓
listen()
   ↓
aguarda conexões TCP
   ↓
accept()
   ↓
obtém socket conectado
   ↓
send()/recv()
   ↓
comunicação
```

Para UDP:

```text
socket()
   ↓
setsockopt()
   ↓
configura propriedades
   ↓
bind()
   ↓
sendto()/recvfrom()
```

Assim, `setsockopt()` entra como uma etapa de **configuração do comportamento do socket**.

---

## Resumo da Parte

* `setsockopt()` permite configurar opções de um socket.
* A forma geral é:

```python
socket.setsockopt(level, optname, value)
```

* `getsockopt()` permite consultar determinadas opções:

```python
socket.getsockopt(level, optname)
```

* Uma das opções mais utilizadas em servidores é:

```python
socket.SO_REUSEADDR
```

* `SO_REUSEADDR` é especialmente útil em cenários de reinicialização de servidores e reutilização de endereços em determinadas condições.
* `SO_KEEPALIVE` habilita mecanismos de keepalive TCP.
* `SO_RCVBUF` está relacionado ao buffer de recebimento.
* `SO_SNDBUF` está relacionado ao buffer de envio.
* `SO_BROADCAST` permite broadcast em sockets apropriados.
* `SO_REUSEPORT` possui comportamento dependente da plataforma e pode ser utilizado em arquiteturas específicas.
* Algumas opções pertencem ao próprio socket:

```python
socket.SOL_SOCKET
```

enquanto outras são específicas do protocolo:

```python
socket.IPPROTO_TCP
```

* `setsockopt()` não transforma TCP em UDP nem altera o tipo fundamental do socket.
* As configurações são repassadas ao sistema operacional e afetam o comportamento do socket no kernel.
* O modelo mental é:

```text
socket()
    ↓
setsockopt()
    ↓
configuração
    ↓
bind()/connect()
    ↓
comunicação
```

---

# 18. Resolvendo nomes e endereços com a biblioteca `socket`

Até agora, trabalhamos principalmente com endereços IP diretamente:

```python
client.connect(("127.0.0.1", 4444))
```

Porém, na prática, aplicações normalmente não recebem um endereço IP diretamente do usuário.

É muito mais comum utilizarmos um **nome de host**:

```text
google.com
example.com
meuservidor.local
```

O sistema precisa então descobrir qual endereço IP está associado àquele nome.

É nesse processo que entra a **resolução de nomes**.

---

## 18.1 O que é resolução de nomes?

Resolução de nomes é o processo de transformar um nome de host em um endereço que possa ser utilizado para comunicação.

Por exemplo:

```text
example.com
     ↓
192.0.2.10
```

O programa conhece:

```text
example.com
```

mas para estabelecer uma conexão IP, ele precisa chegar a um endereço como:

```text
192.0.2.10
```

Esse processo normalmente envolve o **DNS (Domain Name System)**.

---

## 18.2 O papel do DNS

O DNS funciona, de forma simplificada, como um sistema de resolução de nomes.

Podemos pensar em:

```text
Nome
 ↓
DNS
 ↓
Endereço IP
```

Por exemplo:

```text
www.exemplo.com
        ↓
      DNS
        ↓
  203.0.113.20
```

Depois que o endereço é obtido, o socket pode utilizar esse endereço para estabelecer a comunicação:

```text
hostname
   ↓
resolução DNS
   ↓
IP
   ↓
connect()
   ↓
TCP
```

É importante separar essas etapas.

**DNS não é TCP.**

DNS é utilizado para descobrir endereços.

TCP é utilizado posteriormente para estabelecer uma comunicação confiável quando a aplicação escolhe TCP.

---

# 18.3 `socket.gethostbyname()`

A biblioteca `socket` possui funções para realizar resolução de nomes.

Uma das mais simples é:

```python
socket.gethostbyname(hostname)
```

Exemplo:

```python
import socket

ip = socket.gethostbyname("example.com")

print(ip)
```

O resultado será um endereço IPv4.

Por exemplo:

```text
192.0.2.10
```

O endereço retornado pode variar dependendo do domínio e da infraestrutura DNS.

---

## 18.4 Parâmetro de `gethostbyname()`

A função possui uma utilização simples:

```python
socket.gethostbyname(hostname)
```

|Parâmetro|Tipo|Obrigatório|Comportamento|
|---|---|---|---|
|`hostname`|`str`|Sim|Nome do host que será resolvido|

Exemplo:

```python
import socket

hostname = "example.com"

ip = socket.gethostbyname(hostname)

print(f"{hostname} -> {ip}")
```

Possível resultado:

```text
example.com -> 192.0.2.10
```

---

# 18.5 `gethostbyname()` trabalha com IPv4

Uma limitação importante:

```python
socket.gethostbyname()
```

é voltada para resolução de endereços **IPv4**.

IPv4 utiliza endereços como:

```text
192.168.1.10
10.0.0.5
8.8.8.8
```

IPv6 utiliza outro formato:

```text
2001:db8::1
```

Por isso, para aplicações modernas que precisam trabalhar de forma mais geral com IPv4 e IPv6, normalmente utilizamos:

```python
socket.getaddrinfo()
```

---

# 18.6 `socket.gethostbyname_ex()`

Existe também:

```python
socket.gethostbyname_ex(hostname)
```

Ela fornece mais informações do que `gethostbyname()`.

Exemplo:

```python
import socket

result = socket.gethostbyname_ex("example.com")

print(result)
```

O resultado possui uma estrutura semelhante a:

```python
(
    "example.com",
    [],
    ["192.0.2.10"]
)
```

Essa estrutura contém:

```text
canonical name
      ↓
aliases
      ↓
addresses
```

Podemos separar:

```python
hostname, aliases, addresses = socket.gethostbyname_ex("example.com")
```

E então:

```python
print(hostname)
print(aliases)
print(addresses)
```

---

# 18.7 Por que um domínio pode possuir vários IPs?

Um erro comum é imaginar que:

```text
domínio → um único IP
```

Isso não é necessariamente verdade.

Um domínio pode possuir vários endereços:

```text
example.com
     ↓
 ┌───┴────┐
 ↓        ↓
IP 1     IP 2
```

Por exemplo:

```text
example.com
    ↓
192.0.2.10
192.0.2.11
192.0.2.12
```

Isso pode ser utilizado para:

- distribuição de carga;
    
- redundância;
    
- disponibilidade;
    
- servidores em diferentes localidades;
    
- balanceamento de tráfego.
    

Por isso, aplicações de rede não devem assumir que um hostname sempre corresponde a um único endereço.

---

# 18.8 `socket.getaddrinfo()`

Para aplicações modernas, uma das funções mais importantes da biblioteca `socket` é:

```python
socket.getaddrinfo()
```

Ela é mais geral que:

```python
gethostbyname()
```

e pode fornecer informações necessárias para trabalhar com:

- IPv4;
    
- IPv6;
    
- TCP;
    
- UDP;
    
- diferentes famílias de endereço;
    
- diferentes tipos de socket.
    

Sua assinatura simplificada é:

```python
socket.getaddrinfo(
    host,
    port,
    family=0,
    type=0,
    proto=0,
    flags=0
)
```

---

# 18.9 Parâmetros de `getaddrinfo()`

|Parâmetro|Tipo|Obrigatório|Comportamento|
|---|---|---|---|
|`host`|`str` ou endereço|Sim|Hostname ou endereço a ser resolvido|
|`port`|`int` ou `str`|Sim|Porta ou serviço|
|`family`|constante `socket`|Não|Restringe a família de endereços|
|`type`|constante `socket`|Não|Restringe o tipo de socket|
|`proto`|`int`|Não|Restringe o protocolo|
|`flags`|`int`|Não|Modifica o comportamento da resolução|

Um exemplo simples:

```python
import socket

results = socket.getaddrinfo("example.com", 80)

for result in results:
    print(result)
```

---

# 18.10 Entendendo o resultado de `getaddrinfo()`

Cada resultado retornado possui informações que podem ser utilizadas para criar um socket.

Uma entrada possui aproximadamente esta estrutura:

```python
(
    family,
    type,
    proto,
    canonname,
    sockaddr
)
```

Por exemplo:

```python
(
    socket.AF_INET,
    socket.SOCK_STREAM,
    6,
    "",
    ("192.0.2.10", 80)
)
```

Podemos interpretar:

```text
AF_INET
   ↓
IPv4

SOCK_STREAM
   ↓
socket orientado a fluxo

6
   ↓
TCP

""
   ↓
nome canônico

("192.0.2.10", 80)
   ↓
endereço de destino
```

Isso é muito importante porque o `getaddrinfo()` não retorna apenas um IP.

Ele retorna informações suficientes para ajudar a determinar **como o socket deve ser utilizado**.

---

# 18.11 Usando `AF_UNSPEC`

Quando queremos aceitar tanto IPv4 quanto IPv6, podemos utilizar:

```python
socket.AF_UNSPEC
```

Exemplo:

```python
import socket

results = socket.getaddrinfo(
    "example.com",
    80,
    family=socket.AF_UNSPEC,
    type=socket.SOCK_STREAM
)

for result in results:
    print(result)
```

A ideia é:

```text
AF_UNSPEC
   ↓
não restringir para IPv4 ou IPv6
   ↓
retornar possibilidades disponíveis
```

Assim, a aplicação pode trabalhar com diferentes famílias de endereços.

---

# 18.12 Restringindo para IPv4

Podemos solicitar especificamente IPv4:

```python
import socket

results = socket.getaddrinfo(
    "example.com",
    80,
    family=socket.AF_INET,
    type=socket.SOCK_STREAM
)

for result in results:
    print(result)
```

Agora estamos dizendo:

```text
Quero:
    IPv4
    +
    SOCK_STREAM
```

---

# 18.13 Restringindo para IPv6

Também podemos solicitar IPv6:

```python
import socket

results = socket.getaddrinfo(
    "example.com",
    80,
    family=socket.AF_INET6,
    type=socket.SOCK_STREAM
)

for result in results:
    print(result)
```

Agora:

```text
AF_INET6
   ↓
IPv6
```

---

# 18.14 Obtendo somente combinações TCP

Podemos especificar:

```python
type=socket.SOCK_STREAM
```

Por exemplo:

```python
import socket

results = socket.getaddrinfo(
    "example.com",
    80,
    type=socket.SOCK_STREAM
)

for result in results:
    print(result)
```

Isso ajuda a filtrar os resultados para sockets orientados a fluxo, normalmente associados ao TCP.

---

# 18.15 Obtendo combinações UDP

Para UDP:

```python
import socket

results = socket.getaddrinfo(
    "example.com",
    53,
    type=socket.SOCK_DGRAM
)

for result in results:
    print(result)
```

Nesse caso:

```text
SOCK_DGRAM
    ↓
datagramas
    ↓
normalmente UDP
```

---

# 18.16 Usando o resultado para criar um socket

Uma das grandes vantagens de `getaddrinfo()` é que podemos utilizar diretamente os valores retornados para criar um socket.

Exemplo:

```python
import socket

results = socket.getaddrinfo(
    "example.com",
    80,
    type=socket.SOCK_STREAM
)

family, socktype, proto, canonname, sockaddr = results[0]

client = socket.socket(
    family,
    socktype,
    proto
)

client.connect(sockaddr)

print("Conectado!")

client.close()
```

Observe o fluxo:

```text
example.com
     ↓
getaddrinfo()
     ↓
family
type
protocol
address
     ↓
socket()
     ↓
connect()
```

Isso é muito mais geral do que simplesmente pegar um IPv4 com:

```python
gethostbyname()
```

---

# 18.17 Por que `getaddrinfo()` é tão importante?

Considere:

```python
client.connect(("example.com", 80))
```

O Python pode trabalhar com o hostname e realizar a resolução necessária internamente.

Ou seja, você não precisa necessariamente fazer:

```python
ip = socket.gethostbyname("example.com")

client.connect((ip, 80))
```

O próprio sistema pode resolver o hostname utilizado na conexão.

Então:

```python
client.connect(("example.com", 80))
```

pode envolver conceitualmente:

```text
example.com
      ↓
resolução de nome
      ↓
endereço IP
      ↓
TCP connect
      ↓
conexão
```

---

# 18.18 Resolução de nome e `connect()` são operações diferentes

É importante não confundir:

```python
socket.getaddrinfo()
```

com:

```python
socket.connect()
```

A primeira está relacionada à **descoberta/resolução do endereço**.

A segunda está relacionada ao **estabelecimento da comunicação**.

Podemos pensar:

```text
getaddrinfo()
     ↓
"Para onde devo conectar?"
     ↓
IP + porta
     ↓
connect()
     ↓
"Vou estabelecer a conexão."
```

No caso de TCP:

```text
DNS
 ↓
IP
 ↓
connect()
 ↓
SYN
 ↓
SYN-ACK
 ↓
ACK
 ↓
ESTABLISHED
```

---

# 18.19 `socket.gethostname()`

Também podemos descobrir o hostname da máquina local utilizando:

```python
socket.gethostname()
```

Exemplo:

```python
import socket

hostname = socket.gethostname()

print(hostname)
```

Possível resultado:

```text
mafiaboy
```

Isso representa o nome de host configurado para a máquina.

---

# 18.20 `socket.getfqdn()`

Existe também:

```python
socket.getfqdn()
```

FQDN significa:

```text
Fully Qualified Domain Name
```

Ou:

```text
Nome de domínio totalmente qualificado
```

Exemplo:

```python
import socket

name = socket.getfqdn()

print(name)
```

O resultado depende da configuração da máquina e da resolução de nomes disponível.

---

# 18.21 Resolução reversa com `getnameinfo()`

Até agora fizemos:

```text
hostname → IP
```

Também podemos realizar o caminho inverso:

```text
IP → nome
```

Para isso existe:

```python
socket.getnameinfo()
```

Exemplo:

```python
import socket

result = socket.getnameinfo(
    ("8.8.8.8", 53),
    0
)

print(result)
```

A resposta depende da existência de resolução reversa configurada para aquele endereço.

Portanto, não devemos assumir que:

```text
IP → sempre terá hostname
```

Isso não é obrigatório.

---

# 18.22 Erro `socket.gaierror`

Quando uma resolução de nome falha, podemos encontrar:

```python
socket.gaierror
```

Por exemplo:

```python
import socket

try:
    ip = socket.gethostbyname("dominio-que-nao-existe.example")
    print(ip)

except socket.gaierror as error:
    print(f"Erro de resolução: {error}")
```

Esse erro está relacionado à resolução de endereços.

Por exemplo:

```text
hostname inválido
       ↓
DNS não consegue resolver
       ↓
socket.gaierror
```

É diferente de um erro de conexão.

Por exemplo:

```text
resolução funcionou
       ↓
IP encontrado
       ↓
connect()
       ↓
servidor inacessível
       ↓
outro tipo de erro
```

Essa distinção é importante para diagnosticar problemas de rede.

---

# 18.23 DNS pode bloquear a aplicação

A resolução de nomes não deve ser considerada uma operação instantânea.

Quando fazemos:

```python
socket.getaddrinfo("example.com", 80)
```

o processo pode precisar consultar serviços de resolução de nomes.

Portanto:

```python
getaddrinfo()
```

pode bloquear.

Isso é importante principalmente em aplicações:

- servidores;
    
- ferramentas de rede;
    
- automações;
    
- aplicações concorrentes;
    
- aplicações assíncronas.
    

Uma aplicação que realiza várias resoluções de nomes de forma síncrona pode ficar esperando essas operações.

---

# 18.24 Hostname pode resultar em vários endereços

Outro ponto importante:

```python
results = socket.getaddrinfo(...)
```

pode retornar várias opções.

Por exemplo:

```text
example.com
     ↓
 ┌───┼─────────┐
 ↓   ↓         ↓
IPv4 IPv4      IPv6
```

A aplicação pode então tentar os endereços disponíveis.

Uma estratégia comum é percorrer os resultados:

```python
import socket

results = socket.getaddrinfo(
    "example.com",
    80,
    type=socket.SOCK_STREAM
)

for family, socktype, proto, canonname, sockaddr in results:
    print(sockaddr)
```

Assim conseguimos visualizar os possíveis destinos retornados.

---

# 18.25 Modelo mental completo

Podemos juntar tudo que aprendemos:

```text
Hostname
   │
   ▼
Resolução de nomes
   │
   ├── DNS
   │
   ▼
getaddrinfo()
   │
   ├── família
   ├── tipo
   ├── protocolo
   └── endereço
          │
          ▼
      socket()
          │
          ▼
      connect()
          │
          ▼
     Comunicação
```

Ou, de forma ainda mais simples:

```text
"example.com"
      ↓
"Qual endereço corresponde a esse nome?"
      ↓
IP + porta
      ↓
"Como devo criar o socket?"
      ↓
AF_INET / AF_INET6
SOCK_STREAM / SOCK_DGRAM
      ↓
socket()
      ↓
connect() / sendto()
```

---

# 18.26 `gethostbyname()` vs `getaddrinfo()`

|Característica|`gethostbyname()`|`getaddrinfo()`|
|---|---|---|
|IPv4|Sim|Sim|
|IPv6|Não é a opção geral|Sim|
|Retorna múltiplas informações de socket|Não|Sim|
|Família do endereço|Não|Sim|
|Tipo do socket|Não|Sim|
|Protocolo|Não|Sim|
|API mais geral|Não|Sim|
|Uso moderno|Limitado|Preferível|

Para código novo que precisa ser flexível, normalmente devemos pensar primeiro em:

```python
socket.getaddrinfo()
```

---

# 18.27 Exemplo prático completo

Um exemplo simples utilizando `getaddrinfo()`:

```python
import socket

host = "example.com"
port = 80

results = socket.getaddrinfo(
    host,
    port,
    type=socket.SOCK_STREAM
)

for family, socktype, proto, canonname, sockaddr in results:
    print(f"Família: {family}")
    print(f"Tipo: {socktype}")
    print(f"Protocolo: {proto}")
    print(f"Endereço: {sockaddr}")
    print()
```

A aplicação agora possui as informações necessárias para decidir como criar o socket.

---

# 18.28 Relação com ferramentas de rede

Esse conceito aparece constantemente em ferramentas de rede.

Por exemplo:

```text
ping example.com
```

ou:

```text
curl https://example.com
```

ou:

```text
nmap example.com
```

Antes de realizar determinadas operações, o programa pode precisar descobrir quais endereços correspondem ao nome informado.

Por isso, em redes, devemos distinguir:

```text
nome
↓
resolução
↓
endereço
↓
comunicação
```

O nome é uma forma conveniente para humanos.

O endereço é utilizado na comunicação da rede.

---

# 18.29 Erro conceitual comum

Não devemos pensar:

> "DNS conecta meu programa ao servidor."

O DNS não estabelece a conexão da aplicação com o serviço final.

Ele responde, de forma simplificada:

> "Esse nome corresponde a estes endereços."

Depois disso, outro protocolo e outra operação realizam a comunicação.

Por exemplo:

```text
DNS
 ↓
descobre IP
 ↓
TCP
 ↓
estabelece conexão
 ↓
HTTP
 ↓
troca dados da aplicação
```

Temos, portanto, diferentes camadas desempenhando funções diferentes.

---

# 18.30 Resumo da Parte

A biblioteca `socket` possui várias funções relacionadas à resolução de nomes e endereços.

As principais estudadas foram:

```python
socket.gethostbyname()
```

Resolve um hostname para um endereço IPv4.

```python
socket.gethostbyname_ex()
```

Retorna informações adicionais e possíveis endereços IPv4.

```python
socket.getaddrinfo()
```

É a API mais geral para obter informações de endereçamento e configuração de sockets, podendo trabalhar com IPv4, IPv6, TCP e UDP.

```python
socket.gethostname()
```

Obtém o hostname local.

```python
socket.getfqdn()
```

Obtém o nome de domínio totalmente qualificado, quando disponível.

```python
socket.getnameinfo()
```

Pode realizar resolução reversa de endereço para nome.

O conceito principal desta parte é:

```text
Hostname
   ↓
Resolução de nomes
   ↓
Endereço
   ↓
socket
   ↓
connect/sendto
   ↓
Comunicação
```

E o principal ponto para guardar:

> **Resolver um nome e estabelecer uma conexão são operações diferentes.**

---

# 19. Enviando e recebendo dados de forma segura e eficiente

Até agora já vimos como:

- criar sockets;
    
- associar endereços com `bind()`;
    
- colocar servidores em escuta com `listen()`;
    
- aceitar clientes com `accept()`;
    
- conectar clientes com `connect()`;
    
- enviar dados com `send()` e `sendall()`;
    
- receber dados com `recv()`;
    
- trabalhar com UDP;
    
- configurar opções com `setsockopt()`;
    
- resolver nomes com `getaddrinfo()`.
    

Agora precisamos aprofundar um dos pontos mais importantes da programação com sockets:

> **Como controlar corretamente o envio e o recebimento de dados.**

Isso é especialmente importante porque uma conexão TCP é um **fluxo contínuo de bytes**, e não uma sequência automática de mensagens.

---

# 19.1 TCP não sabe onde uma mensagem termina

Imagine que o cliente envie:

```python
client.sendall(b"OLA")
```

e depois:

```python
client.sendall(b"MUNDO")
```

O servidor pode receber:

```python
b"OLA"
```

depois:

```python
b"MUNDO"
```

Mas também pode receber:

```python
b"OLAMUNDO"
```

ou:

```python
b"OL"
```

e depois:

```python
b"AMUNDO"
```

Isso acontece porque TCP trabalha com um **fluxo de bytes**.

Portanto:

```text
send()
send()
send()
```

não significa:

```text
mensagem
mensagem
mensagem
```

No TCP temos:

```text
fluxo de bytes
```

---

# 19.2 O tamanho passado para `recv()` não é o tamanho da mensagem

Considere:

```python
data = client.recv(1024)
```

Isso significa:

> "Retorne no máximo 1024 bytes que estiverem disponíveis."

Não significa:

> "Espere até receber exatamente 1024 bytes."

Por exemplo, se existem apenas 50 bytes disponíveis:

```python
data = client.recv(1024)
```

pode retornar:

```text
50 bytes
```

Se existem 700:

```text
700 bytes
```

E se existirem 1024 ou mais:

```text
até 1024 bytes
```

Portanto:

```text
recv(1024)
     ↓
até 1024 bytes
```

---

# 19.3 `recv()` pode retornar menos dados do que você espera

Imagine que o cliente queira enviar:

```text
"ABCDEFGHIJ"
```

São 10 bytes.

O servidor faz:

```python
data = client.recv(10)
```

Não existe garantia de que receberá:

```python
b"ABCDEFGHIJ"
```

Pode receber:

```python
b"ABC"
```

e depois:

```python
b"DEFGHIJ"
```

Por isso, quando o protocolo exige uma quantidade específica de bytes, precisamos controlar explicitamente essa quantidade.

---

# 19.4 Criando uma função para receber exatamente N bytes

Podemos criar uma função:

```python
def recv_exactly(sock, size):
    data = b""

    while len(data) < size:
        chunk = sock.recv(size - len(data))

        if chunk == b"":
            raise ConnectionError("Conexão encerrada")

        data += chunk

    return data
```

Agora:

```python
data = recv_exactly(client, 10)
```

significa:

> Tente receber exatamente 10 bytes.

O fluxo será:

```text
recv_exactly(10)
       ↓
recebe alguns bytes
       ↓
ainda faltam?
       ↓
sim
       ↓
recv()
       ↓
continua
       ↓
10 bytes recebidos
       ↓
retorna
```

---

# 19.5 Por que verificamos `b""`?

Observe:

```python
if chunk == b"":
    raise ConnectionError("Conexão encerrada")
```

Isso é fundamental.

Quando uma conexão TCP é encerrada de forma ordenada pelo outro lado:

```python
recv()
```

retorna:

```python
b""
```

Isso não significa:

```text
"recebi uma mensagem vazia"
```

No contexto de TCP, significa que o fluxo chegou ao fim.

Podemos visualizar:

```text
Cliente
   │
   │ FIN
   ▼
Servidor
   │
   │ recv()
   ▼
b""
```

---

# 19.6 O perigo de fazer `data += chunk` repetidamente

A função anterior funciona conceitualmente:

```python
data += chunk
```

Porém, para grandes quantidades de dados, concatenar bytes repetidamente pode ser ineficiente.

Por exemplo:

```python
data = b""

while ...:
    data += chunk
```

Cada concatenação pode precisar criar um novo objeto `bytes`.

Para pequenas mensagens isso normalmente não é um problema.

Para grandes volumes de dados, podemos utilizar uma lista:

```python
chunks = []

while ...:
    chunks.append(chunk)

data = b"".join(chunks)
```

Assim:

```text
chunk 1
chunk 2
chunk 3
chunk 4
   ↓
lista
   ↓
b"".join()
   ↓
bytes final
```

---

# 19.7 Exemplo mais eficiente

Podemos reescrever:

```python
def recv_exactly(sock, size):
    chunks = []
    received = 0

    while received < size:
        chunk = sock.recv(size - received)

        if chunk == b"":
            raise ConnectionError("Conexão encerrada")

        chunks.append(chunk)
        received += len(chunk)

    return b"".join(chunks)
```

Agora:

```python
data = recv_exactly(client, 1024)
```

garante que a função só termine quando:

```text
1024 bytes
```

forem recebidos, ou quando ocorrer um erro/encerramento.

---

# 19.8 Limite de `recv()` e memória

Outro cuidado importante:

```python
sock.recv(1000000000)
```

não significa necessariamente que o Python receberá 1 GB imediatamente.

O valor passado para `recv()` representa o tamanho máximo solicitado para aquela operação.

Mesmo assim, devemos evitar valores absurdamente grandes sem necessidade.

Por exemplo:

```python
sock.recv(4096)
```

ou:

```python
sock.recv(8192)
```

são tamanhos comuns em muitos programas.

O tamanho adequado depende do protocolo e da aplicação.

---

# 19.9 Recebendo arquivos

Imagine que queremos transferir um arquivo.

Uma abordagem simples é enviar o arquivo em blocos:

```python
with open("arquivo.bin", "rb") as file:
    while True:
        chunk = file.read(4096)

        if not chunk:
            break

        sock.sendall(chunk)
```

O arquivo é dividido em pedaços:

```text
arquivo
   ↓
┌────────┬────────┬────────┬────────┐
│ 4096 B │ 4096 B │ 4096 B │ ...    │
└────────┴────────┴────────┴────────┘
```

Cada bloco é enviado através do socket.

---

# 19.10 Recebendo um arquivo

No outro lado:

```python
with open("recebido.bin", "wb") as file:
    while True:
        chunk = sock.recv(4096)

        if not chunk:
            break

        file.write(chunk)
```

O fluxo é:

```text
socket
  ↓
recv()
  ↓
chunk
  ↓
arquivo.write()
  ↓
disco
```

Porém, existe um problema importante:

> **Como o receptor sabe onde o arquivo termina?**

---

# 19.11 O encerramento da conexão não deve ser o protocolo

Uma solução ingênua seria:

```text
enviar arquivo
↓
fechar conexão
↓
receiver recebe b""
↓
arquivo terminou
```

Isso pode funcionar em uma aplicação extremamente simples.

Mas é uma abordagem limitada.

Se quisermos continuar utilizando a mesma conexão para outras operações:

```text
arquivo
↓
mensagem
↓
comando
↓
outro arquivo
```

não podemos usar o fechamento da conexão como delimitador de cada mensagem.

Precisamos de um protocolo.

---

# 19.12 Enviando o tamanho antes dos dados

Uma solução é informar primeiro o tamanho do conteúdo.

Por exemplo:

```text
[TAMANHO][DADOS]
```

Imagine que temos:

```text
10000 bytes
```

Podemos enviar:

```text
[10000][conteúdo]
```

O receptor primeiro lê o tamanho:

```text
10000
```

e então sabe exatamente quanto precisa receber.

Fluxo:

```text
Cliente
   │
   │ tamanho = 10000
   ▼
Servidor
   │
   │ espera 10000 bytes
   ▼
dados
```

---

# 19.13 Cabeçalho e payload

Essa estrutura é muito comum em protocolos.

Podemos chamar:

```text
HEADER
```

a parte que descreve a mensagem.

E:

```text
PAYLOAD
```

os dados propriamente ditos.

Por exemplo:

```text
┌───────────────┬─────────────────────────┐
│ HEADER        │ PAYLOAD                 │
│ tamanho: 1000│ 1000 bytes de dados     │
└───────────────┴─────────────────────────┘
```

O receptor primeiro interpreta o header.

Depois sabe como interpretar o payload.

---

# 19.14 Usando `struct`

Python possui a biblioteca:

```python
import struct
```

que pode ser utilizada para converter valores entre Python e uma representação binária.

Por exemplo:

```python
header = struct.pack("!I", len(data))
```

Aqui:

```text
!
```

indica ordem de bytes de rede (**big-endian**).

E:

```text
I
```

representa um inteiro sem sinal de 4 bytes.

Portanto:

```python
struct.pack("!I", 1000)
```

produz uma representação binária de:

```text
1000
```

---

# 19.15 Enviando uma mensagem com tamanho

Podemos criar:

```python
import struct

data = b"Hello, world!"

header = struct.pack("!I", len(data))

sock.sendall(header)
sock.sendall(data)
```

O protocolo agora é:

```text
┌──────────────┬────────────────┐
│ 4 bytes      │ N bytes        │
│ tamanho      │ dados          │
└──────────────┴────────────────┘
```

---

# 19.16 Recebendo a mensagem

O receptor pode fazer:

```python
import struct

header = recv_exactly(sock, 4)

length = struct.unpack("!I", header)[0]

data = recv_exactly(sock, length)
```

O processo é:

```text
recv 4 bytes
     ↓
interpreta tamanho
     ↓
descobre N
     ↓
recv exatamente N bytes
     ↓
mensagem completa
```

---

# 19.17 Esse modelo é extremamente importante

Essa estrutura:

```text
HEADER
+
PAYLOAD
```

aparece em inúmeros protocolos.

Por exemplo:

```text
┌──────────────┬──────────────────┐
│ comprimento  │ conteúdo         │
└──────────────┴──────────────────┘
```

Ou:

```text
┌──────────────┬─────────┬─────────┐
│ versão       │ tipo    │ payload │
└──────────────┴─────────┴─────────┘
```

Ou:

```text
┌────────┬──────────┬─────────────┐
│ versão │ comando  │ dados       │
└────────┴──────────┴─────────────┘
```

Esse conceito será muito importante quando estudarmos protocolos de rede mais profundamente.

---

# 19.18 Enviando vários campos

Imagine um protocolo simples:

```text
[VERSÃO][TIPO][TAMANHO][DADOS]
```

Poderíamos ter:

```text
VERSION = 1
TYPE = 2
LENGTH = 100
```

seguido por:

```text
100 bytes
```

O receptor lê:

```text
1. versão
2. tipo
3. tamanho
4. payload
```

Isso transforma uma sequência de bytes em uma estrutura que a aplicação consegue interpretar.

---

# 19.19 O socket não entende seu protocolo

É importante entender onde cada responsabilidade está.

O socket não sabe que:

```text
4 bytes = tamanho
```

Isso é uma regra criada pela aplicação.

O socket apenas transmite:

```text
bytes
```

Então:

```text
Aplicação
   ↓
protocolo
   ↓
bytes
   ↓
socket
   ↓
TCP
   ↓
rede
```

No outro lado:

```text
rede
   ↓
TCP
   ↓
socket
   ↓
bytes
   ↓
protocolo
   ↓
aplicação
```

---

# 19.20 Envio parcial com `send()`

Já vimos que:

```python
sock.send(data)
```

pode enviar apenas uma parte.

Por exemplo:

```python
data = b"A" * 10000

sent = sock.send(data)

print(sent)
```

Pode acontecer:

```text
10000
```

mas também:

```text
4096
```

ou outro valor menor.

Por isso, para enviar todos os dados:

```python
sock.sendall(data)
```

normalmente é mais conveniente.

---

# 19.21 Quando `send()` é útil?

`send()` ainda é importante.

Ele é útil quando queremos controlar manualmente o processo de envio.

Por exemplo:

```python
view = memoryview(data)

while view:
    sent = sock.send(view)
    view = view[sent:]
```

Aqui estamos implementando manualmente a lógica de envio parcial.

O processo é:

```text
dados
 ↓
send()
 ↓
quantos bytes foram enviados?
 ↓
remove os enviados
 ↓
envia o restante
```

`sendall()` já encapsula esse comportamento para nós.

---

# 19.22 `memoryview`

A classe:

```python
memoryview
```

permite trabalhar com uma visão sobre um objeto de bytes sem necessariamente criar cópias do conteúdo para cada operação.

Exemplo:

```python
data = b"ABCDEFGHIJ"

view = memoryview(data)

print(view[:5].tobytes())
```

Resultado:

```text
b'ABCDE'
```

Isso pode ser útil em código de rede de maior desempenho.

Porém, para aplicações simples:

```python
sendall()
```

normalmente é suficiente.

---

# 19.23 Tratando erros durante envio e recebimento

Operações de rede podem falhar.

Por exemplo:

```python
try:
    sock.sendall(data)

except BrokenPipeError:
    print("O outro lado fechou a conexão.")

except ConnectionResetError:
    print("A conexão foi resetada.")
```

Para recebimento:

```python
try:
    data = sock.recv(4096)

except TimeoutError:
    print("Tempo limite atingido.")

except ConnectionResetError:
    print("Conexão resetada.")
```

Isso é importante em aplicações reais.

---

# 19.24 Timeout durante `recv()`

Se configurarmos:

```python
sock.settimeout(5)
```

então:

```python
sock.recv(4096)
```

não ficará bloqueado indefinidamente.

Se nada acontecer durante o período configurado, poderá ocorrer:

```python
socket.timeout
```

Exemplo:

```python
import socket

sock.settimeout(5)

try:
    data = sock.recv(4096)

except socket.timeout:
    print("Nenhum dado recebido dentro do prazo.")
```

---

# 19.25 Timeout não significa conexão encerrada

É importante diferenciar:

```text
timeout
```

de:

```text
b""
```

Timeout:

```text
não chegou dado dentro do prazo
```

`b""`:

```text
peer encerrou o fluxo de forma ordenada
```

Portanto:

```python
if data == b"":
    ...
```

não deve ser tratado simplesmente como:

```text
"timeout"
```

São situações diferentes.

---

# 19.26 Modelo mental de recebimento

Quando fazemos:

```python
data = sock.recv(4096)
```

devemos pensar:

```text
Há bytes disponíveis?
       │
       ├── sim → retorna alguns bytes
       │
       └── não
             │
             ├── blocking → espera
             │
             ├── timeout → lança exceção
             │
             └── non-blocking → BlockingIOError
```

E se o peer encerrou a conexão:

```text
recv()
  ↓
b""
```

Esse modelo é muito mais preciso do que pensar:

> "`recv()` pega uma mensagem."

---

# 19.27 Fluxo completo de uma mensagem TCP

Podemos visualizar:

```text
APLICAÇÃO
    │
    │ mensagem
    ▼
PROTOCOLO DA APLICAÇÃO
    │
    │ framing
    ▼
BYTES
    │
    ▼
sendall()
    │
    ▼
SOCKET
    │
    ▼
TCP
    │
    ▼
REDE
    │
    ▼
TCP
    │
    ▼
SOCKET
    │
    ▼
recv()
    │
    ▼
BYTES
    │
    ▼
PROTOCOLO
    │
    ▼
MENSAGEM
```

O TCP garante a entrega ordenada do fluxo dentro das propriedades do protocolo, mas **não sabe onde a mensagem da aplicação começa ou termina**.

Essa responsabilidade continua sendo da aplicação.

---

# 19.28 Resumo da Parte

Nesta parte aprofundamos o envio e recebimento de dados.

Os principais conceitos foram:

```python
sock.recv(size)
```

Recebe **até** `size` bytes.

```python
sock.send(data)
```

Pode enviar apenas uma parte dos dados.

```python
sock.sendall(data)
```

Tenta enviar todos os dados.

Também vimos que:

```text
TCP = fluxo de bytes
```

e não:

```text
TCP = mensagens
```

Quando precisamos saber exatamente onde uma mensagem termina, devemos criar um mecanismo de **framing**, como:

```text
[HEADER][PAYLOAD]
```

ou:

```text
[TAMANHO][DADOS]
```

Também aprendemos a importância de:

```python
b""
```

como indicação de encerramento ordenado do fluxo, e de:

```python
socket.timeout
```

para indicar que uma operação ultrapassou o tempo limite configurado.

O modelo mental principal desta parte é:

```text
TCP entrega um fluxo de bytes.
A aplicação define como esses bytes representam mensagens.
```

---

# 20. Transferência de arquivos através de sockets

Agora que entendemos como controlar o envio e recebimento de bytes, podemos aplicar esses conceitos a uma situação muito comum em redes:

> **transferir arquivos entre duas máquinas através de sockets.**

À primeira vista, parece simples:

```text
arquivo
   ↓
socket
   ↓
rede
   ↓
socket
   ↓
arquivo
```

Mas uma transferência de arquivos correta precisa resolver vários problemas:

- como informar que uma transferência começou;
    
- como informar o nome do arquivo;
    
- como informar o tamanho;
    
- como enviar os dados em blocos;
    
- como detectar o fim do arquivo;
    
- como evitar receber dados incompletos;
    
- como detectar erros;
    
- como validar se o arquivo recebido está completo.
    

Por isso, transferência de arquivos é um excelente exemplo para entender como um **protocolo de aplicação** é construído sobre TCP.

---

# 20.1 O problema básico

Imagine que temos:

```text
Cliente
    │
    │ arquivo.txt
    ▼
Servidor
```

O cliente possui:

```text
arquivo.txt
```

e deseja enviá-lo ao servidor.

Uma implementação extremamente simples poderia fazer:

```python
with open("arquivo.txt", "rb") as file:
    data = file.read()

sock.sendall(data)
```

Porém, isso possui um problema:

> Como o servidor sabe quantos bytes pertencem ao arquivo?

O servidor poderia fazer:

```python
data = sock.recv(4096)
```

mas isso não significa:

```text
"receba o arquivo inteiro"
```

Significa apenas:

```text
"receba até 4096 bytes nesta operação"
```

---

# 20.2 Não devemos carregar arquivos gigantes na memória

Outra abordagem seria:

```python
with open("arquivo.iso", "rb") as file:
    data = file.read()

sock.sendall(data)
```

Isso funciona para arquivos pequenos.

Mas imagine:

```text
arquivo.iso
    ↓
10 GB
```

O programa tentaria carregar uma quantidade enorme de dados na memória.

Isso é desnecessário.

A abordagem correta é trabalhar com **chunks**, ou blocos.

```text
arquivo
   ↓
┌────────┬────────┬────────┬────────┐
│ chunk  │ chunk  │ chunk  │ chunk  │
└────────┴────────┴────────┴────────┘
   ↓        ↓        ↓        ↓
 socket   socket   socket   socket
```

---

# 20.3 Lendo o arquivo em blocos

Podemos utilizar:

```python
with open("arquivo.bin", "rb") as file:
    while True:
        chunk = file.read(4096)

        if not chunk:
            break

        sock.sendall(chunk)
```

Aqui:

```python
file.read(4096)
```

lê no máximo:

```text
4096 bytes
```

por vez.

O processo será:

```text
arquivo
   ↓
read(4096)
   ↓
chunk
   ↓
sendall()
   ↓
read(4096)
   ↓
chunk
   ↓
sendall()
   ↓
...
```

---

# 20.4 Por que usar `rb`?

Observe:

```python
open("arquivo.bin", "rb")
```

O modo:

```text
rb
```

significa:

```text
r = read
b = binary
```

Ou seja:

> abrir para leitura em modo binário.

Isso é importante porque um arquivo pode conter qualquer sequência de bytes.

Por exemplo:

```text
imagem
vídeo
executável
ZIP
PDF
ISO
banco de dados
```

Todos esses arquivos são compostos por bytes.

Não devemos tratá-los como texto:

```python
open("arquivo.bin", "r")
```

quando a intenção é realizar uma transferência binária genérica.

---

# 20.5 Recebendo os chunks

No servidor podemos fazer:

```python
with open("recebido.bin", "wb") as file:
    while True:
        chunk = sock.recv(4096)

        if not chunk:
            break

        file.write(chunk)
```

O modo:

```text
wb
```

significa:

```text
w = write
b = binary
```

Então:

```text
socket
   ↓
recv()
   ↓
bytes
   ↓
file.write()
   ↓
disco
```

---

# 20.6 Mas como sabemos quando o arquivo terminou?

Aqui temos novamente o problema do protocolo.

Se o cliente fizer:

```python
sock.sendall(chunk1)
sock.sendall(chunk2)
sock.sendall(chunk3)
```

o servidor não sabe automaticamente que:

```text
chunk3
```

foi o último.

TCP não possui um conceito de:

```text
"último chunk do arquivo"
```

Para TCP, tudo é apenas:

```text
fluxo de bytes
```

Precisamos criar uma regra.

---

# 20.7 Primeira solução: fechar a conexão

Uma solução simples é:

```text
Cliente
   │
   │ arquivo
   ▼
Servidor
   │
   │ b""
   ▼
fim
```

O cliente envia o arquivo e depois fecha a conexão:

```python
with open("arquivo.bin", "rb") as file:
    while True:
        chunk = file.read(4096)

        if not chunk:
            break

        sock.sendall(chunk)

sock.shutdown(socket.SHUT_WR)
```

O servidor continua recebendo:

```python
while True:
    chunk = sock.recv(4096)

    if not chunk:
        break

    file.write(chunk)
```

Quando receber:

```python
b""
```

sabe que o fluxo foi encerrado.

---

# 20.8 O problema dessa solução

Essa abordagem pode funcionar para um protocolo muito simples.

Mas imagine que queremos:

```text
conexão TCP
   ↓
enviar arquivo
   ↓
receber resposta
   ↓
enviar outro arquivo
   ↓
continuar comunicação
```

Não queremos fechar a conexão depois do primeiro arquivo.

Portanto:

```text
fim da conexão ≠ fim do arquivo
```

Precisamos de um protocolo melhor.

---

# 20.9 Segunda solução: enviar o tamanho do arquivo

Uma abordagem mais robusta é:

```text
[TAMANHO][ARQUIVO]
```

Por exemplo:

```text
arquivo = 1.500.000 bytes
```

O cliente envia primeiro:

```text
1500000
```

e depois:

```text
1.500.000 bytes
```

O servidor então sabe:

> "Preciso receber exatamente 1.500.000 bytes."

---

# 20.10 Estrutura do protocolo

Podemos definir:

```text
┌──────────────────┬───────────────────────────┐
│ TAMANHO DO ARQUIVO │ DADOS DO ARQUIVO       │
└──────────────────┴───────────────────────────┘
       HEADER                  PAYLOAD
```

Por exemplo:

```text
HEADER = 8 bytes
PAYLOAD = N bytes
```

Podemos utilizar um inteiro de 64 bits:

```python
struct.pack("!Q", file_size)
```

O `Q` representa um inteiro sem sinal de 8 bytes.

Assim podemos representar arquivos muito grandes.

---

# 20.11 Descobrindo o tamanho do arquivo

Podemos utilizar:

```python
import os

file_size = os.path.getsize("arquivo.bin")
```

Por exemplo:

```python
import os

size = os.path.getsize("arquivo.bin")

print(size)
```

Resultado:

```text
15728640
```

Isso significa:

```text
15.728.640 bytes
```

---

# 20.12 Enviando o cabeçalho

Agora podemos fazer:

```python
import os
import struct

file_size = os.path.getsize("arquivo.bin")

header = struct.pack("!Q", file_size)

sock.sendall(header)
```

O protocolo começa:

```text
Cliente
   │
   │ 8 bytes: tamanho
   ▼
Servidor
```

---

# 20.13 Enviando o arquivo

Depois:

```python
with open("arquivo.bin", "rb") as file:
    while True:
        chunk = file.read(4096)

        if not chunk:
            break

        sock.sendall(chunk)
```

Agora temos:

```text
HEADER
   ↓
PAYLOAD
```

---

# 20.14 Recebendo o tamanho

No servidor:

```python
header = recv_exactly(sock, 8)

file_size = struct.unpack("!Q", header)[0]
```

Agora:

```text
file_size
```

contém a quantidade de bytes esperada.

Por exemplo:

```text
15000000
```

O servidor sabe que precisa receber:

```text
15.000.000 bytes
```

---

# 20.15 Recebendo exatamente o tamanho informado

Podemos fazer:

```python
remaining = file_size

with open("recebido.bin", "wb") as file:
    while remaining > 0:
        chunk = sock.recv(min(4096, remaining))

        if not chunk:
            raise ConnectionError("Conexão encerrada antes do fim do arquivo")

        file.write(chunk)
        remaining -= len(chunk)
```

Observe:

```python
min(4096, remaining)
```

Se ainda faltarem:

```text
10000 bytes
```

pedimos:

```text
4096
```

Depois:

```text
4096
```

Depois:

```text
1808
```

E assim por diante.

---

# 20.16 Por que `remaining` é importante?

Temos:

```python
remaining = file_size
```

Depois de cada recebimento:

```python
remaining -= len(chunk)
```

Então:

```text
10000
 ↓
5904
 ↓
1808
 ↓
0
```

Quando chegar:

```text
0
```

sabemos que recebemos exatamente o tamanho anunciado.

---

# 20.17 O que acontece se a conexão cair?

Imagine:

```text
arquivo = 10 MB
```

O cliente conseguiu enviar:

```text
7 MB
```

e então a conexão caiu.

O servidor possui:

```text
7 MB
```

mas esperava:

```text
10 MB
```

Quando fizer:

```python
chunk = sock.recv(...)
```

e receber:

```python
b""
```

antes de chegar aos 10 MB, devemos considerar a transferência incompleta.

Por isso:

```python
if not chunk:
    raise ConnectionError(
        "Conexão encerrada antes do fim do arquivo"
    )
```

é importante.

---

# 20.18 O tamanho informado não deve ser confiado cegamente

Existe outro problema.

Imagine que um cliente malicioso envie:

```text
file_size = 999999999999999999
```

e nunca envie realmente esse volume de dados.

O servidor pode ficar esperando indefinidamente ou manter recursos ocupados.

Portanto, protocolos reais precisam impor limites.

Por exemplo:

```python
MAX_FILE_SIZE = 100 * 1024 * 1024
```

Ou seja:

```text
100 MB
```

Depois:

```python
if file_size > MAX_FILE_SIZE:
    raise ValueError("Arquivo muito grande")
```

---

# 20.19 Validação do tamanho

Podemos fazer:

```python
MAX_FILE_SIZE = 100 * 1024 * 1024

file_size = struct.unpack("!Q", header)[0]

if file_size > MAX_FILE_SIZE:
    raise ValueError("Tamanho de arquivo não permitido")
```

Assim:

```text
cliente
   ↓
informa tamanho
   ↓
servidor valida
   ↓
aceita ou rejeita
```

Nunca devemos assumir que os dados recebidos são confiáveis apenas porque vieram através de TCP.

---

# 20.20 Nome do arquivo

Agora surge outro problema:

> Qual será o nome do arquivo recebido?

Podemos incluir isso no protocolo.

Por exemplo:

```text
[HEADER][NOME][ARQUIVO]
```

Ou:

```text
[TAMANHO DO NOME]
[NOME]
[TAMANHO DO ARQUIVO]
[ARQUIVO]
```

Exemplo:

```text
┌────────────┬──────────────┬────────────┬──────────────┐
│ nome_size  │ nome         │ file_size │ dados        │
└────────────┴──────────────┴────────────┴──────────────┘
```

---

# 20.21 Nunca devemos confiar cegamente no nome recebido

Imagine um cliente enviando:

```text
../../../../etc/passwd
```

Se o servidor simplesmente fizer:

```python
open(nome, "wb")
```

poderíamos acabar escrevendo fora do diretório destinado aos uploads.

Esse tipo de vulnerabilidade é conhecido como **Path Traversal**.

Portanto, nomes de arquivos recebidos pela rede devem ser tratados como entrada não confiável.

---

# 20.22 Gerando um nome seguro

Uma abordagem simples é utilizar:

```python
from pathlib import Path

name = Path(received_name).name
```

Por exemplo:

```text
../../arquivo.txt
```

poderia ser reduzido para:

```text
arquivo.txt
```

Isso remove os componentes de diretório.

Mesmo assim, aplicações reais devem aplicar uma política adequada para nomes de arquivos.

Uma opção ainda mais segura é gerar o nome internamente:

```text
upload/
    8f91c2d4.bin
```

e não permitir que o cliente escolha diretamente o caminho de armazenamento.

---

# 20.23 Verificando a integridade do arquivo

Receber a quantidade correta de bytes não garante que o conteúdo seja o esperado.

Podemos utilizar um hash.

Por exemplo:

```python
import hashlib

sha256 = hashlib.sha256()
```

Durante o recebimento:

```python
while remaining > 0:
    chunk = sock.recv(min(4096, remaining))

    if not chunk:
        raise ConnectionError("Transferência incompleta")

    file.write(chunk)
    sha256.update(chunk)

    remaining -= len(chunk)
```

No final:

```python
digest = sha256.hexdigest()

print(digest)
```

Agora temos uma identificação criptográfica do conteúdo recebido.

---

# 20.24 Comparando hashes

O cliente pode calcular:

```text
SHA-256 do arquivo original
```

e enviar esse valor ao servidor.

O servidor calcula:

```text
SHA-256 do arquivo recebido
```

Depois compara:

```text
hash original
      ↓
      =
hash recebido
```

Se forem iguais:

```text
conteúdo provavelmente corresponde ao mesmo arquivo
```

Se forem diferentes:

```text
conteúdo diferente
```

Isso permite detectar corrupção ou alteração dos dados.

---

# 20.25 Hash não é criptografia

É importante não confundir:

```text
SHA-256
```

com:

```text
criptografia
```

Um hash é uma função de resumo.

Por exemplo:

```text
arquivo
   ↓
SHA-256
   ↓
256 bits
```

Ele não é utilizado para recuperar o arquivo original a partir do hash.

Também não fornece, sozinho, confidencialidade.

Se precisamos proteger o conteúdo durante o transporte, precisamos de mecanismos como **TLS**.

---

# 20.26 TCP não criptografa arquivos

Um erro comum seria pensar:

> "Estou usando TCP, então meu arquivo está seguro."

Não.

TCP fornece características de transporte, como:

- entrega ordenada;
    
- controle de fluxo;
    
- retransmissão;
    
- confiabilidade do fluxo.
    

Mas TCP não fornece:

```text
criptografia
autenticação do servidor
confidencialidade
integridade criptográfica da aplicação
```

Para isso podemos utilizar:

```text
TLS
```

Então:

```text
Aplicação
   ↓
TLS
   ↓
TCP
   ↓
IP
```

---

# 20.27 Exemplo completo de envio

Um cliente simples:

```python
import os
import struct

def send_file(sock, filename):
    file_size = os.path.getsize(filename)

    header = struct.pack("!Q", file_size)
    sock.sendall(header)

    with open(filename, "rb") as file:
        while True:
            chunk = file.read(4096)

            if not chunk:
                break

            sock.sendall(chunk)
```

Uso:

```python
send_file(sock, "arquivo.bin")
```

Fluxo:

```text
arquivo
   ↓
getsize()
   ↓
tamanho
   ↓
header
   ↓
sendall()
   ↓
chunks
   ↓
sendall()
```

---

# 20.28 Exemplo completo de recebimento

Servidor:

```python
import struct

def recv_exactly(sock, size):
    chunks = []
    received = 0

    while received < size:
        chunk = sock.recv(size - received)

        if not chunk:
            raise ConnectionError("Conexão encerrada")

        chunks.append(chunk)
        received += len(chunk)

    return b"".join(chunks)


def recv_file(sock, filename):
    header = recv_exactly(sock, 8)

    file_size = struct.unpack("!Q", header)[0]

    remaining = file_size

    with open(filename, "wb") as file:
        while remaining > 0:
            chunk = sock.recv(min(4096, remaining))

            if not chunk:
                raise ConnectionError(
                    "Conexão encerrada antes do fim do arquivo"
                )

            file.write(chunk)
            remaining -= len(chunk)
```

Agora temos:

```text
HEADER
  ↓
file_size
  ↓
receber exatamente file_size bytes
  ↓
salvar no disco
```

---

# 20.29 O protocolo completo

Nosso protocolo simplificado pode ser representado assim:

```text
┌──────────────────────┬──────────────────────────────┐
│ 8 bytes              │ N bytes                     │
│ tamanho do arquivo   │ conteúdo do arquivo         │
└──────────────────────┴──────────────────────────────┘
        HEADER                    PAYLOAD
```

Fluxo do cliente:

```text
open()
  ↓
getsize()
  ↓
pack()
  ↓
sendall(header)
  ↓
read chunk
  ↓
sendall(chunk)
  ↓
read chunk
  ↓
sendall(chunk)
  ↓
...
```

Fluxo do servidor:

```text
recv_exactly(8)
  ↓
unpack()
  ↓
descobre tamanho
  ↓
recv()
  ↓
write()
  ↓
recv()
  ↓
write()
  ↓
...
  ↓
remaining == 0
  ↓
arquivo completo
```

---

# 20.30 O protocolo pode evoluir

Nosso protocolo atualmente possui apenas:

```text
[TAMANHO][ARQUIVO]
```

Mas poderíamos evoluí-lo para:

```text
[VERSÃO]
[COMANDO]
[NOME]
[TAMANHO]
[HASH]
[DADOS]
```

Por exemplo:

```text
┌────────┬─────────┬──────┬────────┬──────────┬──────────┐
│ versão │ comando │ nome │ tamanho│ hash     │ dados    │
└────────┴─────────┴──────┴────────┴──────────┴──────────┘
```

Agora o servidor poderia saber:

```text
versão: 1
comando: UPLOAD
nome: foto.jpg
tamanho: 500000
hash: ...
dados: ...
```

Isso já começa a se parecer com um protocolo de aplicação real.

---

# 20.31 Transferência de arquivos não é apenas `sendall()`

Uma implementação ingênua:

```python
file = open("arquivo", "rb")
sock.sendall(file.read())
```

ignora vários problemas.

Uma implementação mais correta precisa pensar em:

```text
┌──────────────────────────────┐
│ tamanho                      │
├──────────────────────────────┤
│ limites                      │
├──────────────────────────────┤
│ chunks                       │
├──────────────────────────────┤
│ encerramento                 │
├──────────────────────────────┤
│ erros                        │
├──────────────────────────────┤
│ integridade                  │
├──────────────────────────────┤
│ nome seguro                  │
├──────────────────────────────┤
│ autenticação/autorização     │
├──────────────────────────────┤
│ criptografia                 │
└──────────────────────────────┘
```

É exatamente por isso que protocolos de transferência de arquivos reais possuem muito mais regras.

---

# 20.32 Modelo mental final

A transferência pode ser entendida assim:

```text
                 CLIENTE
                    │
                    │
              ┌─────▼─────┐
              │   HEADER  │
              │ tamanho   │
              └─────┬─────┘
                    │
                    │
              ┌─────▼─────┐
              │  PAYLOAD  │
              │  arquivo  │
              └─────┬─────┘
                    │
                    ▼
                  TCP
                    │
                    ▼
              ┌───────────┐
              │  SOCKET   │
              └─────┬─────┘
                    │
                    ▼
                 SERVIDOR
                    │
              lê o header
                    │
              descobre N bytes
                    │
              recebe N bytes
                    │
              grava no disco
```

A ideia central é:

> **O TCP transporta bytes; o protocolo da aplicação define como esses bytes representam um arquivo.**

---

# 20.33 Resumo da Parte

Nesta parte construímos o raciocínio necessário para realizar transferência de arquivos através de sockets TCP.

Aprendemos que:

- arquivos devem normalmente ser tratados em modo binário;
    
- arquivos grandes não devem ser carregados completamente na memória;
    
- devemos trabalhar com chunks;
    
- TCP não informa automaticamente o fim de um arquivo;
    
- fechar a conexão pode indicar fim, mas limita o protocolo;
    
- uma solução melhor é enviar o tamanho antes dos dados;
    
- `struct.pack()` e `struct.unpack()` podem ser utilizados para representar o tamanho em formato binário;
    
- `recv_exactly()` pode ser utilizado quando precisamos receber uma quantidade exata de bytes;
    
- o tamanho recebido deve ser validado;
    
- nomes de arquivos vindos da rede são entrada não confiável;
    
- hashes podem ajudar a verificar integridade;
    
- TCP não fornece criptografia;
    
- TLS pode ser utilizado para proteger a comunicação.
    

O modelo principal desta parte:

```text
ARQUIVO
   ↓
TAMANHO
   ↓
HEADER
   ↓
CHUNKS
   ↓
TCP
   ↓
CHUNKS
   ↓
TAMANHO ESPERADO
   ↓
ARQUIVO
```

---

# 21. Concorrência: atendendo múltiplos clientes

Até agora, nossos servidores foram construídos pensando principalmente em **um cliente por vez**.

Um servidor TCP básico costuma ter esta estrutura:

```python
server = socket.socket()
server.bind(("127.0.0.1", 4444))
server.listen()

client, address = server.accept()

data = client.recv(1024)
```

O problema aparece quando temos vários clientes:

```text
             ┌── Cliente 1
             │
Servidor ────┼── Cliente 2
             │
             └── Cliente 3
```

Um servidor real normalmente precisa conseguir atender mais de uma conexão.

Para isso, precisamos entender **concorrência**.

---

# 21.1 O problema de um servidor sequencial

Imagine este servidor:

```python
import socket

server = socket.socket()
server.bind(("127.0.0.1", 4444))
server.listen()

while True:
    client, address = server.accept()

    data = client.recv(1024)

    print(data)

    client.close()
```

A sequência é:

```text
accept()
   ↓
cliente 1
   ↓
recv()
   ↓
processa
   ↓
close()
   ↓
accept()
   ↓
cliente 2
```

Esse servidor trabalha de forma **sequencial**.

Enquanto está processando um cliente, não está processando outro.

---

# 21.2 O problema do bloqueio

Imagine que o cliente 1 conecte:

```text
Cliente 1
    │
    │ connect()
    ▼
Servidor
```

O servidor executa:

```python
data = client.recv(1024)
```

Agora imagine que o cliente 1 fique conectado, mas não envie nada.

Se o socket estiver em modo blocking:

```python
recv()
```

pode ficar esperando.

Enquanto isso:

```text
Cliente 1 → esperando
Servidor → esperando
Cliente 2 → tentando conectar
Cliente 3 → tentando conectar
```

O código não consegue avançar para:

```python
server.accept()
```

até que a operação atual termine ou seja interrompida por algum evento/erro.

Esse é um dos principais problemas de servidores sequenciais.

---

# 21.3 O modelo sequencial

Podemos visualizar:

```text
Servidor
   │
   ├── aceita Cliente 1
   │
   ├── processa Cliente 1
   │
   ├── encerra Cliente 1
   │
   ├── aceita Cliente 2
   │
   ├── processa Cliente 2
   │
   └── ...
```

Isso pode ser suficiente para:

- testes;
    
- ferramentas simples;
    
- scripts internos;
    
- aplicações com apenas um cliente.
    

Mas não escala bem para muitos clientes.

---

# 21.4 Primeira solução: uma thread por cliente

Uma solução tradicional é criar uma **thread** para cada conexão.

A ideia:

```text
                 Servidor
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
     Thread 1    Thread 2    Thread 3
        │           │           │
    Cliente 1    Cliente 2    Cliente 3
```

O thread principal fica responsável por aceitar conexões.

Cada cliente é entregue para uma thread separada.

---

# 21.5 O que é uma thread?

Uma thread é uma unidade de execução dentro de um processo.

Podemos pensar:

```text
Processo
   │
   ├── Thread principal
   ├── Thread 1
   ├── Thread 2
   └── Thread 3
```

As threads do mesmo processo compartilham recursos como:

- memória;
    
- descritores de arquivos;
    
- objetos;
    
- estado do processo.
    

Por isso, threads permitem que diferentes partes do programa executem de forma concorrente.

---

# 21.6 Criando uma thread em Python

A biblioteca padrão possui:

```python
import threading
```

Podemos criar:

```python
thread = threading.Thread(target=funcao)
```

E iniciar:

```python
thread.start()
```

Exemplo:

```python
import threading

def worker():
    print("Executando em uma thread")

thread = threading.Thread(target=worker)
thread.start()
```

A função:

```python
worker()
```

será executada pela nova thread.

---

# 21.7 Aplicando ao servidor

Podemos criar:

```python
import socket
import threading

def handle_client(client):
    data = client.recv(1024)

    print(data)

    client.close()


server = socket.socket()

server.bind(("127.0.0.1", 4444))
server.listen()

while True:
    client, address = server.accept()

    thread = threading.Thread(
        target=handle_client,
        args=(client,)
    )

    thread.start()
```

Agora:

```text
accept()
   ↓
cliente 1
   ↓
Thread 1
```

Enquanto isso:

```text
accept()
   ↓
cliente 2
   ↓
Thread 2
```

E:

```text
accept()
   ↓
cliente 3
   ↓
Thread 3
```

---

# 21.8 O que mudou?

Antes:

```text
accept()
 ↓
cliente 1
 ↓
recv()
 ↓
processamento
 ↓
close()
 ↓
accept()
```

Agora:

```text
accept()
 ↓
cliente 1
 ↓
Thread 1
 ↓
imediatamente volta
 ↓
accept()
 ↓
cliente 2
 ↓
Thread 2
```

O loop principal não precisa esperar o processamento completo do cliente.

---

# 21.9 Função `handle_client()`

É uma boa prática separar o tratamento do cliente:

```python
def handle_client(client):
    while True:
        data = client.recv(1024)

        if not data:
            break

        client.sendall(data)

    client.close()
```

Essa função possui responsabilidade sobre **uma conexão**.

Enquanto isso, o servidor principal possui responsabilidade sobre:

```text
aceitar novas conexões
```

Essa separação facilita bastante a organização do código.

---

# 21.10 Exemplo de servidor Echo concorrente

Um servidor completo:

```python
import socket
import threading


def handle_client(client, address):
    print(f"Cliente conectado: {address}")

    try:
        while True:
            data = client.recv(1024)

            if not data:
                break

            client.sendall(data)

    except ConnectionResetError:
        print(f"Cliente perdeu a conexão: {address}")

    finally:
        client.close()
        print(f"Cliente desconectado: {address}")


server = socket.socket()

server.bind(("127.0.0.1", 4444))
server.listen()

print("Servidor aguardando conexões...")

while True:
    client, address = server.accept()

    thread = threading.Thread(
        target=handle_client,
        args=(client, address)
    )

    thread.start()
```

Agora vários clientes podem permanecer conectados simultaneamente.

---

# 21.11 `daemon=True`

Podemos criar a thread como:

```python
thread = threading.Thread(
    target=handle_client,
    args=(client, address),
    daemon=True
)
```

O parâmetro:

```python
daemon=True
```

indica que a thread é uma **thread daemon**.

De forma simplificada, quando não existirem mais threads não-daemon mantendo o processo vivo, threads daemon não impedem o encerramento do processo.

Isso pode ser útil em servidores simples.

Porém, threads daemon não devem ser utilizadas como mecanismo para garantir que operações importantes terminem corretamente.

---

# 21.12 Um problema: criar threads infinitamente

Imagine:

```text
1 cliente → 1 thread
100 clientes → 100 threads
10.000 clientes → 10.000 threads
```

Isso pode se tornar um problema.

Cada thread possui custos associados:

- memória;
    
- gerenciamento pelo sistema operacional;
    
- troca de contexto;
    
- estruturas internas;
    
- processamento.
    

Portanto:

```text
1 cliente = 1 thread
```

não é necessariamente uma boa solução para milhares de conexões.

---

# 21.13 Thread pool

Uma alternativa é utilizar um conjunto limitado de threads.

Por exemplo:

```text
10 threads
```

para atender:

```text
1000 clientes
```

A ideia é:

```text
              Thread Pool
       ┌──────┬──────┬──────┐
       │ T1   │ T2   │ T3   │
       │ T4   │ T5   │ ...  │
       └──────┴──────┴──────┘
                 │
                 ▼
              tarefas
```

Quando uma thread termina uma tarefa, pode receber outra.

---

# 21.14 `ThreadPoolExecutor`

A biblioteca padrão fornece:

```python
from concurrent.futures import ThreadPoolExecutor
```

Podemos fazer:

```python
executor = ThreadPoolExecutor(max_workers=10)
```

Isso cria um pool com até 10 workers.

Depois:

```python
executor.submit(handle_client, client, address)
```

envia uma tarefa para o pool.

---

# 21.15 Servidor usando thread pool

Exemplo:

```python
import socket
from concurrent.futures import ThreadPoolExecutor


def handle_client(client, address):
    print(f"Cliente conectado: {address}")

    try:
        while True:
            data = client.recv(1024)

            if not data:
                break

            client.sendall(data)

    finally:
        client.close()


server = socket.socket()

server.bind(("127.0.0.1", 4444))
server.listen()

with ThreadPoolExecutor(max_workers=10) as executor:
    while True:
        client, address = server.accept()

        executor.submit(
            handle_client,
            client,
            address
        )
```

Agora temos:

```text
clientes
   │
   ▼
fila de tarefas
   │
   ▼
┌─────────────────────┐
│ Thread Pool         │
│                     │
│ T1 T2 T3 ... T10    │
└─────────────────────┘
```

---

# 21.16 Concorrência não significa necessariamente paralelismo

Esses conceitos são relacionados, mas não são exatamente iguais.

**Concorrência** significa que várias tarefas podem progredir de forma intercalada.

**Paralelismo** significa que tarefas podem realmente executar simultaneamente em diferentes unidades de execução.

Podemos representar:

```text
Concorrência:

Tarefa A ──┐   ┌───┐   ┌───
           └───┘   └───┘

Tarefa B     ┌───┐   ┌───┐
             └───┘   └───┘
```

As tarefas avançam de forma intercalada.

Paralelismo:

```text
CPU 1 → Tarefa A
CPU 2 → Tarefa B
```

acontece simultaneamente.

---

# 21.17 O GIL do CPython

Quando falamos de Python, existe um detalhe importante:

> O CPython possui o **GIL (Global Interpreter Lock)** em versões/configurações tradicionais.

O GIL limita a execução simultânea de bytecode Python por múltiplas threads dentro de um mesmo processo.

Isso significa que threads não são uma solução mágica para tornar código Python intensivo em CPU paralelamente executável.

Porém, isso não impede que threads sejam extremamente úteis para operações de **I/O**, como:

- sockets;
    
- arquivos;
    
- espera de rede;
    
- APIs;
    
- banco de dados.
    

Enquanto uma thread está bloqueada esperando I/O, outra pode avançar.

---

# 21.18 Por que threads funcionam bem para sockets?

Imagine:

```python
data = client.recv(4096)
```

A thread pode ficar esperando dados.

Enquanto isso:

```text
Thread 1
   ↓
esperando rede

Thread 2
   ↓
atendendo cliente

Thread 3
   ↓
esperando rede
```

Isso é útil porque servidores de rede passam grande parte do tempo esperando I/O.

---

# 21.19 O problema de memória compartilhada

Threads do mesmo processo compartilham memória.

Imagine:

```python
clients = []
```

e várias threads modificando:

```python
clients.append(client)
```

ao mesmo tempo.

Ou:

```python
counter += 1
```

sendo executado por várias threads.

Isso pode gerar condições de corrida dependendo da operação e do contexto.

Por isso existem mecanismos de sincronização.

---

# 21.20 `Lock`

A biblioteca `threading` fornece:

```python
threading.Lock()
```

Exemplo:

```python
import threading

lock = threading.Lock()

counter = 0


def increment():
    global counter

    with lock:
        counter += 1
```

A região:

```python
with lock:
```

é protegida pelo lock.

A ideia:

```text
Thread 1
   │
   ├── adquire lock
   │
   ├── modifica recurso
   │
   └── libera lock

Thread 2
   │
   └── espera
```

---

# 21.21 Condição de corrida

Uma **race condition** acontece quando o resultado depende da ordem de execução de operações concorrentes.

Imagine:

```python
counter += 1
```

Conceitualmente envolve:

```text
ler counter
   ↓
somar 1
   ↓
escrever counter
```

Se duas threads interferirem durante esse processo, podemos ter resultados inesperados.

Por isso:

```python
with lock:
    counter += 1
```

protege a seção crítica.

---

# 21.22 Não devemos proteger tudo com locks

Um erro comum é colocar:

```python
with lock:
```

ao redor de praticamente todo o código.

Isso pode reduzir a concorrência:

```text
Thread 1
   ↓
lock
   ↓
processa tudo
   ↓
unlock

Thread 2
   ↓
espera
```

O objetivo é proteger apenas os recursos compartilhados que realmente precisam de sincronização.

---

# 21.23 Servidor e estado global

Imagine:

```python
connected_clients = {}
```

e queremos registrar:

```text
cliente → socket
```

Várias threads podem acessar esse dicionário.

Podemos utilizar um lock:

```python
clients_lock = threading.Lock()

connected_clients = {}


def add_client(address, client):
    with clients_lock:
        connected_clients[address] = client
```

Assim evitamos alterações concorrentes descoordenadas.

---

# 21.24 Uma thread por cliente vs thread pool

|Estratégia|Vantagem|Problema|
|---|---|---|
|Thread por cliente|Simples|Pode criar threads demais|
|Thread pool|Limita recursos|Mais estrutura|
|Processo por cliente|Isolamento maior|Mais pesado|
|`selectors`|Muitas conexões com poucas threads|Mais complexo|
|`asyncio`|Excelente para I/O concorrente|Modelo de programação diferente|

Não existe uma solução universal.

A escolha depende da aplicação.

---

# 21.25 `selectors`

Python também fornece:

```python
import selectors
```

A ideia é acompanhar vários sockets simultaneamente sem precisar criar uma thread para cada conexão.

Conceitualmente:

```text
             selector
                │
       ┌────────┼────────┐
       ↓        ↓        ↓
    socket 1 socket 2 socket 3
       │        │        │
      pronto   espera   pronto
```

O sistema operacional informa quais sockets estão prontos para determinadas operações.

Isso permite que uma única thread gerencie muitas conexões.

---

# 21.26 Modelo com `selectors`

De forma simplificada:

```python
selector = selectors.DefaultSelector()
```

Registramos sockets:

```python
selector.register(
    sock,
    selectors.EVENT_READ
)
```

Depois:

```python
events = selector.select()
```

O programa recebe os sockets que possuem eventos disponíveis.

Esse modelo é muito importante para servidores que precisam lidar com muitas conexões.

---

# 21.27 Blocking vs multiplexação

Compare:

### Blocking tradicional

```text
recv(cliente 1)
    ↓
espera
    ↓
termina
    ↓
recv(cliente 2)
```

### Multiplexação

```text
selector
   ↓
┌───┼────┐
↓   ↓    ↓
C1  C2   C3
│   │    │
pronto? pronto? pronto?
```

O servidor consegue observar vários sockets.

---

# 21.28 `asyncio`

Outra abordagem é:

```python
import asyncio
```

O `asyncio` permite construir aplicações assíncronas.

Conceitualmente:

```text
event loop
    │
    ├── cliente 1
    ├── cliente 2
    ├── cliente 3
    └── cliente 4
```

Quando uma tarefa precisa esperar I/O:

```text
await
  ↓
cede controle
  ↓
outra tarefa executa
```

Isso permite alta concorrência sem necessariamente criar uma thread por conexão.

---

# 21.29 Qual abordagem escolher?

Para aprender sockets, uma progressão interessante é:

```text
1. servidor sequencial
        ↓
2. thread por cliente
        ↓
3. thread pool
        ↓
4. selectors
        ↓
5. asyncio
```

Cada etapa resolve problemas diferentes.

Para aplicações simples:

```text
threading
```

pode ser suficiente.

Para muitas conexões simultâneas:

```text
selectors
```

ou:

```text
asyncio
```

podem ser mais adequados.

---

# 21.30 Concorrência aumenta a complexidade

Um servidor concorrente precisa pensar em problemas adicionais:

```text
┌─────────────────────────────┐
│ múltiplos clientes          │
├─────────────────────────────┤
│ sincronização               │
├─────────────────────────────┤
│ race conditions             │
├─────────────────────────────┤
│ encerramento das threads    │
├─────────────────────────────┤
│ exceções                    │
├─────────────────────────────┤
│ limite de recursos          │
├─────────────────────────────┤
│ estado compartilhado        │
└─────────────────────────────┘
```

Por isso, tornar um servidor concorrente não significa simplesmente adicionar:

```python
threading.Thread(...)
```

É necessário pensar no modelo de execução como um todo.

---

# 21.31 Modelo mental final

Podemos visualizar um servidor moderno como:

```text
                    SERVIDOR
                       │
                       ▼
                    accept()
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Cliente 1    Cliente 2    Cliente 3
          │            │            │
          ▼            ▼            ▼
       Handler      Handler      Handler
          │            │            │
          └────────────┼────────────┘
                       ▼
                 recursos comuns
                       │
                       ▼
                 sincronização
```

Ou, utilizando multiplexação:

```text
                    SERVIDOR
                       │
                       ▼
                    Selector
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Socket 1     Socket 2     Socket 3
          │            │            │
          ▼            ▼            ▼
       pronto       esperando     pronto
```

O ponto fundamental é:

> **Um servidor precisa separar a aceitação de novas conexões do processamento individual de cada cliente.**

---

# 21.32 Resumo da Parte

Nesta parte estudamos concorrência em servidores TCP.

Aprendemos que um servidor puramente sequencial pode ficar bloqueado por um único cliente.

Uma solução simples é:

```python
threading.Thread(...)
```

criando uma thread para cada conexão.

Também vimos:

```python
ThreadPoolExecutor
```

para limitar a quantidade de workers.

E conhecemos outras abordagens:

```text
threading
    ↓
ThreadPoolExecutor
    ↓
selectors
    ↓
asyncio
```

Também estudamos conceitos fundamentais de concorrência:

- threads;
    
- concorrência;
    
- paralelismo;
    
- GIL do CPython;
    
- memória compartilhada;
    
- race condition;
    
- `Lock`;
    
- seção crítica;
    
- multiplexação de sockets.
    

O principal modelo mental:

```text
Servidor
   ↓
aceita conexões
   ↓
distribui trabalho
   ↓
processa clientes concorrentemente
   ↓
sincroniza recursos compartilhados quando necessário
```

# 22. Multiplexação de sockets com `selectors`

Quando um servidor precisa atender **muitos clientes simultaneamente**, uma alternativa ao uso de uma thread para cada cliente é utilizar **multiplexação de I/O**.

A ideia é simples:

> Em vez de ficar bloqueado esperando cada socket individualmente, o programa monitora vários sockets ao mesmo tempo e trabalha somente com aqueles que estão prontos para realizar alguma operação de I/O.

Em Python, uma das formas mais práticas de fazer isso é através da biblioteca `selectors`.

---

## 22.1 O que é multiplexação de I/O?

Multiplexação de I/O significa que **um único fluxo de execução pode monitorar vários sockets simultaneamente**.

Imagine um servidor conectado a 1.000 clientes:

```text
                 ┌── Cliente 1
                 │
                 ├── Cliente 2
                 │
Servidor ────────┼── Cliente 3
                 │
                 ├── Cliente 4
                 │
                 ├── ...
                 │
                 └── Cliente 1000
```

Uma abordagem seria criar uma thread para cada cliente:

```text
Servidor
   │
   ├── Thread → Cliente 1
   ├── Thread → Cliente 2
   ├── Thread → Cliente 3
   ├── Thread → Cliente 4
   └── ...
```

Outra abordagem é utilizar um único loop que pergunta ao sistema operacional:

```text
"Qual desses sockets está pronto para eu trabalhar agora?"
```

O sistema operacional responde:

```text
Cliente 2 → pronto para leitura
Cliente 7 → pronto para leitura
Cliente 15 → pronto para leitura
```

O programa então processa somente esses sockets.

---

## 22.2 Por que isso é útil?

Sockets normalmente trabalham com operações de I/O que podem bloquear.

Por exemplo:

```python
data = client.recv(1024)
```

Se não houver dados disponíveis, um socket em modo bloqueante pode fazer o programa ficar parado esperando.

Com muitos clientes, isso se torna um problema.

Com multiplexação:

```text
              ┌── Cliente 1 ── sem dados
              │
              ├── Cliente 2 ── dados disponíveis ✓
              │
Selector ─────┼── Cliente 3 ── sem dados
              │
              ├── Cliente 4 ── dados disponíveis ✓
              │
              └── Cliente 5 ── sem dados
```

O programa não precisa ficar esperando individualmente cada cliente.

Ele espera pelo conjunto de sockets.

---

## 22.3 A biblioteca `selectors`

O módulo `selectors` fornece uma abstração de alto nível para multiplexação de I/O.

```python
import selectors
```

O objeto principal é:

```python
selectors.DefaultSelector()
```

Exemplo:

```python
import selectors

selector = selectors.DefaultSelector()
```

O `DefaultSelector` escolhe automaticamente uma implementação apropriada para o sistema operacional.

Dependendo da plataforma, a implementação pode utilizar mecanismos como:

```text
Linux      → epoll
BSD/macOS  → kqueue
outros     → select/poll ou mecanismo disponível
```

Assim, normalmente você não precisa programar diretamente contra `epoll`, `kqueue` ou `poll`.

Você trabalha com a interface fornecida por `selectors`.

---

## 22.4 Registrando um socket

Para que o selector monitore um socket, precisamos registrá-lo:

```python
selector.register(sock, selectors.EVENT_READ)
```

A ideia é:

```text
socket
   │
   ▼
register()
   │
   ▼
selector começa a monitorar o socket
```

O selector pode monitorar diferentes tipos de eventos.

Os dois mais importantes são:

```python
selectors.EVENT_READ
selectors.EVENT_WRITE
```

---

## 22.5 `EVENT_READ`

`EVENT_READ` indica que estamos interessados em saber quando o socket estiver **pronto para uma operação de leitura**.

Exemplo:

```python
selector.register(client, selectors.EVENT_READ)
```

Depois disso, podemos perguntar ao selector quais sockets estão prontos.

```python
events = selector.select()
```

Se o cliente tiver dados disponíveis, ele poderá aparecer entre os eventos retornados.

---

## 22.6 `EVENT_WRITE`

Também podemos monitorar quando um socket estiver pronto para escrita:

```python
selector.register(
    client,
    selectors.EVENT_WRITE
)
```

Isso pode ser útil quando temos dados aguardando para serem enviados e não queremos bloquear tentando escrever.

Por exemplo:

```text
Aplicação possui dados para enviar
              │
              ▼
        socket possui espaço
        disponível no buffer
              │
              ▼
        socket fica pronto
        para escrita
              │
              ▼
            send()
```

Entretanto, servidores simples normalmente começam monitorando principalmente `EVENT_READ`.

---

## 22.7 `register()`

A assinatura conceitual é:

```python
selector.register(fileobj, events, data=None)
```

### Parâmetros

|Parâmetro|Tipo|Obrigatório|Comportamento|
|---|---|--:|---|
|`fileobj`|socket/objeto compatível|Sim|Objeto que será monitorado|
|`events`|`int`|Sim|Eventos desejados|
|`data`|qualquer objeto|Não|Dados associados ao registro|

Exemplo:

```python
selector.register(
    server,
    selectors.EVENT_READ
)
```

Podemos também associar informações ao socket:

```python
selector.register(
    client,
    selectors.EVENT_READ,
    data={"tipo": "cliente"}
)
```

Esse `data` pode ser recuperado posteriormente.

---

## 22.8 `select()`

Depois de registrar os sockets, usamos:

```python
events = selector.select()
```

Esse método espera até que algum socket esteja pronto.

Conceitualmente:

```text
              sockets registrados
                     │
                     ▼
              ┌─────────────┐
              │   selector  │
              └──────┬──────┘
                     │
              espera por eventos
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
     Cliente 1             Cliente 4
     pronto                pronto
```

O retorno contém informações sobre os sockets que estão prontos.

---

## 22.9 Entendendo o retorno de `select()`

Um exemplo:

```python
events = selector.select()

for key, mask in events:
    print(key)
    print(mask)
```

Cada item possui:

```text
key
mask
```

O `key` contém informações sobre o objeto registrado.

O `mask` informa quais eventos estão prontos.

Por exemplo:

```python
if mask & selectors.EVENT_READ:
    print("Socket pronto para leitura")
```

O operador:

```python
&
```

é utilizado porque `mask` pode representar uma combinação de eventos através de bits.

---

## 22.10 O `SelectorKey`

O `key` retornado pelo selector é um `SelectorKey`.

Ele possui informações como:

```python
key.fileobj
key.fd
key.events
key.data
```

### `key.fileobj`

É o objeto que foi registrado.

Por exemplo:

```python
client = key.fileobj
```

### `key.fd`

É o file descriptor associado ao objeto.

Por exemplo, conceitualmente:

```text
Socket Python
     │
     ▼
file descriptor
     │
     ▼
kernel
```

Podemos obter:

```python
print(key.fd)
```

### `key.events`

Mostra os eventos registrados.

```python
print(key.events)
```

### `key.data`

Contém o objeto que passamos em:

```python
selector.register(..., data=...)
```

Por exemplo:

```python
selector.register(
    client,
    selectors.EVENT_READ,
    data={"nome": "cliente1"}
)
```

Depois:

```python
print(key.data)
```

pode retornar:

```python
{"nome": "cliente1"}
```

---

## 22.11 `unregister()`

Quando um cliente desconecta, não devemos continuar monitorando o socket.

Para removê-lo:

```python
selector.unregister(client)
```

Depois podemos fechar:

```python
client.close()
```

Fluxo:

```text
Cliente desconecta
       │
       ▼
unregister()
       │
       ▼
close()
       │
       ▼
socket removido
```

É importante fazer isso porque manter sockets desnecessários registrados pode causar erros e desperdício de recursos.

---

## 22.12 `modify()`

Também podemos alterar os eventos monitorados.

```python
selector.modify(
    client,
    selectors.EVENT_WRITE
)
```

Por exemplo, inicialmente podemos monitorar apenas leitura:

```python
EVENT_READ
```

Depois, quando temos dados pendentes para enviar:

```text
EVENT_READ
     ↓
tem dados para enviar
     ↓
EVENT_READ | EVENT_WRITE
```

Podemos representar os dois eventos:

```python
selectors.EVENT_READ | selectors.EVENT_WRITE
```

O `|` significa uma combinação bit a bit.

Exemplo:

```python
selector.modify(
    client,
    selectors.EVENT_READ | selectors.EVENT_WRITE
)
```

---

## 22.13 Servidor Echo usando `selectors`

Agora podemos juntar os conceitos.

Um servidor TCP simples:

```python
import socket
import selectors

selector = selectors.DefaultSelector()

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)

server.bind(("127.0.0.1", 4444))
server.listen()
server.setblocking(False)

selector.register(
    server,
    selectors.EVENT_READ
)

while True:
    events = selector.select()

    for key, mask in events:

        sock = key.fileobj

        if sock is server:
            client, address = server.accept()

            print(f"Cliente conectado: {address}")

            client.setblocking(False)

            selector.register(
                client,
                selectors.EVENT_READ
            )

        else:
            data = sock.recv(1024)

            if data:
                print(f"Recebido: {data!r}")

                sock.sendall(data)

            else:
                print("Cliente desconectado")

                selector.unregister(sock)
                sock.close()
```

Esse servidor consegue monitorar vários clientes usando um único loop principal.

---

## 22.14 Entendendo o loop

A parte mais importante é:

```python
while True:
    events = selector.select()

    for key, mask in events:
        ...
```

Podemos visualizar:

```text
                    ┌───────────────┐
                    │ selector      │
                    └───────┬───────┘
                            │
                            ▼
                    select() bloqueia
                    esperando eventos
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
       server pronto                client pronto
              │                           │
              ▼                           ▼
          accept()                     recv()
              │                           │
              ▼                           ▼
      novo cliente                 processa dados
              │                           │
              └─────────────┬─────────────┘
                            │
                            ▼
                         select()
                            │
                            └──────► ...
```

O servidor não precisa fazer:

```text
recv() Cliente 1
espera
recv() Cliente 2
espera
recv() Cliente 3
espera
```

Ele pergunta ao sistema operacional quais sockets estão prontos e trabalha somente nesses sockets.

---

## 22.15 Por que o socket precisa ser não bloqueante?

Observe:

```python
server.setblocking(False)
```

e:

```python
client.setblocking(False)
```

Isso é importante porque o selector informa que o socket está **pronto**, mas ainda devemos evitar que uma operação de I/O bloqueie inesperadamente o loop.

Por exemplo:

```python
data = sock.recv(1024)
```

O objetivo é que, depois de o selector indicar que há algo para ler, essa operação seja realizada sem bloquear todo o servidor.

Caso uma operação bloqueante fique presa:

```text
selector
   │
   ▼
socket pronto
   │
   ▼
recv()
   │
   ▼
bloqueou inesperadamente
   │
   ▼
loop inteiro parado
```

Isso destruiria grande parte da vantagem da multiplexação.

---

## 22.16 Readiness não significa "mensagem completa"

Esse é um ponto extremamente importante.

O selector pode informar:

```text
"Este socket possui dados disponíveis para leitura."
```

Ele **não** está dizendo:

```text
"Este socket possui uma mensagem completa da sua aplicação."
```

Por exemplo, suponha que o protocolo da aplicação utilize:

```text
[4 bytes de tamanho][dados]
```

O selector pode indicar que existem apenas 2 bytes disponíveis.

Então:

```python
recv(1024)
```

pode retornar apenas:

```text
2 bytes
```

Mesmo que a mensagem completa tenha:

```text
1000 bytes
```

Por isso, continuam sendo necessários:

- buffers;
    
- delimitação de mensagens;
    
- prefixo de tamanho;
    
- processamento incremental.
    

O selector resolve **quando fazer I/O**.

Ele não resolve **como interpretar os dados recebidos**.

---

## 22.17 Selector + buffer de aplicação

Imagine:

```text
TCP
 │
 ▼
selector
 │
 ▼
recv()
 │
 ▼
buffer
 │
 ├── mensagem incompleta
 │
 └── mensagem completa
```

O programa pode manter um buffer por cliente:

```python
buffers = {}
```

Por exemplo:

```python
buffers[client] = b""
```

Quando chegam dados:

```python
data = client.recv(1024)

buffers[client] += data
```

Depois o protocolo pode tentar extrair mensagens completas.

Isso é especialmente importante em servidores reais.

---

## 22.18 `EVENT_READ` e `EVENT_WRITE` na prática

Podemos imaginar:

```text
EVENT_READ
    │
    └── "Quero saber quando posso ler."

EVENT_WRITE
    │
    └── "Quero saber quando posso escrever."
```

Um servidor pode inicialmente registrar:

```python
selector.register(
    client,
    selectors.EVENT_READ
)
```

Se tiver dados pendentes:

```python
selector.modify(
    client,
    selectors.EVENT_READ | selectors.EVENT_WRITE
)
```

Quando o socket estiver pronto para escrita:

```python
if mask & selectors.EVENT_WRITE:
    ...
```

Depois que todos os dados pendentes forem enviados, podemos remover `EVENT_WRITE` novamente.

Isso evita ficar monitorando escrita desnecessariamente.

---

## 22.19 `EVENT_WRITE` e backpressure

Imagine que o servidor precisa enviar uma quantidade enorme de dados para um cliente lento.

```text
Servidor
   │
   │ muitos dados
   ▼
buffer de envio
   │
   ▼
cliente lento
```

Se o servidor tentar enviar tudo de uma vez, o `send()` pode não conseguir enviar tudo imediatamente.

Em um servidor baseado em multiplexação, podemos manter os dados restantes em um buffer:

```text
dados pendentes
      │
      ▼
buffer da aplicação
      │
      ▼
EVENT_WRITE
      │
      ▼
socket pronto
      │
      ▼
send()
      │
      ▼
remove bytes enviados
```

Isso é chamado de **backpressure**: o ritmo de produção de dados precisa respeitar a capacidade de consumo do destino.

Esse conceito se torna especialmente importante em servidores de alto desempenho.

---

## 22.20 `select(timeout)`

Também podemos fornecer um timeout:

```python
events = selector.select(timeout=1)
```

Nesse caso, o selector espera no máximo aproximadamente:

```text
1 segundo
```

Se nenhum evento aparecer, o retorno pode ser vazio:

```python
[]
```

Isso permite que o servidor execute outras tarefas periodicamente.

Por exemplo:

```python
while True:

    events = selector.select(timeout=1)

    for key, mask in events:
        ...
    
    verificar_tarefas()
```

Podemos então ter:

```text
┌───────────────────────┐
│ selector.select(1)    │
└───────────┬───────────┘
            │
       eventos?
       /      \
     sim       não
      │         │
      ▼         ▼
 processa    tarefas
 clientes    periódicas
      │         │
      └────┬────┘
           ▼
        próximo loop
```

---

## 22.21 `selectors` versus `threading`

As duas abordagens podem resolver o problema de múltiplos clientes, mas funcionam de maneiras diferentes.

### Threads

```text
Servidor
   │
   ├── Thread 1 → Cliente 1
   ├── Thread 2 → Cliente 2
   ├── Thread 3 → Cliente 3
   └── Thread 4 → Cliente 4
```

Cada thread pode ficar bloqueada esperando I/O.

### `selectors`

```text
Servidor
   │
   └── Loop principal
          │
          ├── Cliente 1
          ├── Cliente 2
          ├── Cliente 3
          └── Cliente 4
```

Um único fluxo monitora todos os sockets.

### Comparação

|Característica|Threads|`selectors`|
|---|---|---|
|Modelo|Concorrência por threads|Multiplexação de I/O|
|Threads|Várias|Normalmente uma por loop|
|I/O bloqueante|Pode ser usado|Normalmente evita-se|
|Estado compartilhado|Mais complexo|Geralmente mais simples|
|Escala com muitos sockets|Pode consumir mais recursos|Muito adequado para I/O|
|Complexidade inicial|Mais simples|Mais complexo|
|Controle do loop|Menor|Maior|

Isso não significa que `selectors` seja sempre melhor.

A escolha depende da aplicação.

---

## 22.22 `selectors` não substitui o protocolo da aplicação

É importante separar as responsabilidades:

```text
┌───────────────────────────────┐
│ Protocolo da aplicação        │
│                               │
│ comandos, framing, autenticação│
└───────────────┬───────────────┘
                │
┌───────────────▼───────────────┐
│ selectors                     │
│                               │
│ quando fazer I/O              │
└───────────────┬───────────────┘
                │
┌───────────────▼───────────────┐
│ Socket / TCP                  │
│                               │
│ transporte de bytes           │
└───────────────┬───────────────┘
                │
                ▼
              Rede
```

Cada camada possui uma responsabilidade diferente.

O TCP transporta bytes.

O selector ajuda a descobrir quando os sockets estão prontos.

O protocolo da aplicação decide o significado desses bytes.

---

## 22.23 Erros comuns

### Tentar usar `recv()` sem verificar o fechamento

Errado:

```python
data = sock.recv(1024)
print(data)
```

Sem tratar:

```python
if not data:
```

O servidor pode continuar monitorando um socket que já foi encerrado pelo cliente.

---

### Esquecer `unregister()`

Ao fechar um socket monitorado:

```python
sock.close()
```

também devemos removê-lo do selector:

```python
selector.unregister(sock)
```

---

### Assumir que `recv()` recebe uma mensagem completa

Mesmo com `selectors`, continua valendo:

```text
TCP = stream de bytes
```

Portanto:

```python
recv(1024)
```

não significa:

```text
"receba exatamente uma mensagem"
```

---

### Usar socket bloqueante em um loop de multiplexação

Se uma operação bloquear indefinidamente:

```python
recv()
```

o loop inteiro pode ficar parado.

Por isso, servidores baseados em `selectors` normalmente utilizam sockets não bloqueantes.

---

## 22.24 Arquitetura mental

Uma forma boa de memorizar é:

```text
                 ┌──────────────────┐
                 │ Aplicação        │
                 │ protocolo/framing│
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │   selectors      │
                 │                  │
                 │ "quem está pronto│
                 │  para I/O?"      │
                 └────────┬─────────┘
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
          Socket 1     Socket 2    Socket 3
              │           │           │
              └───────────┼───────────┘
                          ▼
                         TCP
                          │
                          ▼
                         IP
                          │
                          ▼
                        Rede
```

O ponto principal é:

> **`selectors` não transporta os dados e não define o protocolo. Ele permite que o programa monitore vários objetos de I/O e trabalhe quando eles estiverem prontos.**

---

## 22.25 Fluxo completo de um servidor com `selectors`

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
setblocking(False)
   │
   ▼
selector.register(server, EVENT_READ)
   │
   ▼
┌─────────────────────────────┐
│         LOOP                │
│                             │
│ selector.select()           │
│         │                   │
│         ▼                   │
│ socket pronto?              │
│      /       \              │
│    server   client          │
│      │         │            │
│      ▼         ▼            │
│   accept()   recv()         │
│      │         │            │
│      ▼         ▼            │
│ register    processar       │
│ client      dados           │
│                             │
│ cliente fechou?             │
│      │                      │
│      ▼                      │
│ unregister()                │
│ close()                     │
└─────────────────────────────┘
```

---

## 22.26 Onde `selectors` se encaixa na evolução dos servidores

Até agora estudamos uma evolução natural:

```text
Servidor sequencial
       │
       ▼
Thread por cliente
       │
       ▼
ThreadPoolExecutor
       │
       ▼
selectors
       │
       ▼
asyncio
```

Cada etapa introduz uma forma diferente de lidar com concorrência e I/O.

`selectors` é particularmente importante para entender o que existe **por baixo de abstrações como `asyncio`**.

O conceito fundamental é:

```text
muitos sockets
      ↓
um mecanismo de espera
      ↓
somente sockets prontos são processados
```

---

## 22.27 Resumo da Parte

- **Multiplexação de I/O** permite monitorar vários sockets sem precisar de uma thread para cada cliente.
    
- `selectors.DefaultSelector()` fornece uma abstração portátil para mecanismos de multiplexação do sistema operacional.
    
- `register()` adiciona um socket ao selector.
    
- `unregister()` remove um socket.
    
- `modify()` altera os eventos monitorados.
    
- `select()` espera até que existam sockets prontos.
    
- `EVENT_READ` monitora disponibilidade para leitura.
    
- `EVENT_WRITE` monitora disponibilidade para escrita.
    
- `SelectorKey` fornece informações sobre o socket registrado.
    
- Sockets usados com `selectors` normalmente são configurados como **não bloqueantes**.
    
- Estar "pronto para leitura" **não significa que uma mensagem completa esteja disponível**.
    
- TCP continua sendo um **stream de bytes**, portanto framing e buffers continuam sendo responsabilidade da aplicação.
    
- `selectors` permite construir servidores eficientes para muitos clientes usando um loop de eventos.
    
- `selectors` é uma base conceitual importante para entender modelos assíncronos como `asyncio`.
    

**Modelo mental final:**

```text
                    Muitos clientes
                          │
                          ▼
                  ┌───────────────┐
                  │   selector    │
                  └───────┬───────┘
                          │
                    select()
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
          Socket A     Socket B     Socket C
          pronto        pronto       não pronto
             │            │
             ▼            ▼
           recv()       recv()
             │            │
             └──────┬─────┘
                    ▼
              protocolo da
                aplicação
```


# 23. Servidores assíncronos com `asyncio`

Até agora vimos diferentes formas de atender vários clientes:

```text
Servidor sequencial
        ↓
Thread por cliente
        ↓
ThreadPoolExecutor
        ↓
selectors
        ↓
asyncio
```

O `asyncio` leva o conceito de **I/O assíncrono** para uma abstração mais alta.

Em vez de trabalharmos diretamente com `selectors`, `epoll`, `kqueue` etc., podemos escrever o código utilizando:

```python
async
await
```

e deixar o event loop coordenar as operações de I/O.

---

## 23.1 O que é `asyncio`?

`asyncio` é um módulo da biblioteca padrão do Python para programação concorrente baseada em **corrotinas** e **event loop**.

Importamos:

```python
import asyncio
```

A ideia principal é:

```text
                ┌───────────────────┐
                │    Event Loop     │
                └─────────┬─────────┘
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
          Cliente A    Cliente B    Cliente C
          esperando    executando   esperando
             I/O           │           I/O
                           │
                           ▼
                     continua trabalho
```

Enquanto uma tarefa está esperando uma operação de I/O, o event loop pode executar outra tarefa.

---

## 23.2 O que é um event loop?

O **event loop** é o mecanismo responsável por coordenar tarefas assíncronas.

Podemos imaginar:

```text
┌─────────────────────────────┐
│         Event Loop          │
│                             │
│ verifica tarefas            │
│ verifica I/O                │
│ executa corrotinas prontas  │
│ aguarda eventos             │
└──────────────┬──────────────┘
               │
       ┌───────┼────────┐
       ▼       ▼        ▼
    tarefa 1 tarefa 2 tarefa 3
```

Ele fica executando continuamente e decide qual trabalho pode avançar.

Por exemplo:

```python
async def cliente():
    ...
```

Quando essa função precisa esperar uma operação assíncrona:

```python
await alguma_operacao()
```

a corrotina pode ceder o controle ao event loop.

---

## 23.3 `async def`

Uma função definida com:

```python
async def
```

é uma **função assíncrona**.

Exemplo:

```python
async def hello():
    print("Olá")
```

Porém, chamar:

```python
hello()
```

não executa a função da mesma maneira que uma função normal.

O resultado é uma **corrotina**.

```python
coroutine = hello()
```

Ela precisa ser executada por um event loop.

Uma forma simples é:

```python
asyncio.run(hello())
```

Exemplo completo:

```python
import asyncio

async def hello():
    print("Olá")

asyncio.run(hello())
```

---

## 23.4 O que é uma corrotina?

Uma corrotina é uma função assíncrona que pode **suspender sua execução e depois continuar de onde parou**.

Por exemplo:

```python
async def tarefa():
    print("Início")

    await asyncio.sleep(2)

    print("Fim")
```

Durante:

```python
await asyncio.sleep(2)
```

a corrotina fica aguardando.

O event loop pode utilizar esse tempo para executar outra tarefa.

Visualmente:

```text
Tarefa A
   │
   ▼
"Início"
   │
   ▼
await
   │
   │  espera
   │
   ├──────────────► Event Loop
   │                    │
   │                    ▼
   │                Tarefa B
   │                    │
   │                    ▼
   │                 executa
   │
   ▼
Tarefa A continua
   │
   ▼
"Fim"
```

---

## 23.5 O que significa `await`?

`await` significa, conceitualmente:

> "Preciso esperar o resultado desta operação assíncrona. Enquanto isso, permita que o event loop execute outras tarefas."

Exemplo:

```python
await asyncio.sleep(1)
```

O ponto importante é que isso **não significa necessariamente bloquear a thread inteira**.

A corrotina é suspensa e o event loop pode continuar trabalhando.

---

## 23.6 `asyncio.sleep()` versus `time.sleep()`

Essa diferença é muito importante.

### Bloqueante

```python
import time

time.sleep(5)
```

Isso bloqueia a thread durante o período.

### Assíncrono

```python
await asyncio.sleep(5)
```

Aqui a corrotina é suspensa e o event loop pode executar outras tarefas.

Por exemplo:

```python
import asyncio

async def tarefa(nome):
    print(f"{nome}: iniciou")

    await asyncio.sleep(2)

    print(f"{nome}: terminou")

async def main():
    await asyncio.gather(
        tarefa("A"),
        tarefa("B"),
    )

asyncio.run(main())
```

As duas tarefas podem progredir concorrentemente durante a espera.

---

## 23.7 Concorrência não significa paralelismo

Esse conceito continua sendo importante.

Imagine:

```text
CPU
 │
 ▼
Event Loop
 │
 ├── tarefa A
 ├── tarefa B
 └── tarefa C
```

Em um único thread, normalmente temos **concorrência**, não execução simultânea real das três tarefas.

O event loop alterna entre tarefas quando elas cedem o controle.

```text
tempo →

A A A ──await──
             B B ──await──
                       C C C
             A A
                  B B
```

O ganho principal está em aproveitar períodos de espera de I/O.

---

## 23.8 Por que isso funciona bem para I/O?

Imagine um servidor atendendo:

```text
Cliente A → esperando dados
Cliente B → esperando dados
Cliente C → esperando dados
Cliente D → enviando dados
```

Se utilizarmos um modelo bloqueante, poderíamos ficar presos esperando um cliente.

Com programação assíncrona:

```text
Cliente A
   │
   └── await I/O

Cliente B
   │
   └── await I/O

Cliente C
   │
   └── await I/O

Cliente D
   │
   └── pronto
       │
       ▼
     processa
```

O event loop aproveita melhor o tempo em que as tarefas estão esperando.

---

## 23.9 Criando várias tarefas com `asyncio.create_task()`

Podemos criar uma tarefa:

```python
task = asyncio.create_task(minha_corrotina())
```

Exemplo:

```python
import asyncio

async def tarefa(nome):
    print(f"{nome}: começou")

    await asyncio.sleep(2)

    print(f"{nome}: terminou")

async def main():
    task1 = asyncio.create_task(tarefa("A"))
    task2 = asyncio.create_task(tarefa("B"))

    await task1
    await task2

asyncio.run(main())
```

Aqui temos duas tarefas controladas pelo event loop.

---

## 23.10 `asyncio.gather()`

Quando queremos executar várias corrotinas e esperar pelos resultados, podemos utilizar:

```python
asyncio.gather()
```

Exemplo:

```python
async def main():
    await asyncio.gather(
        tarefa("A"),
        tarefa("B"),
        tarefa("C"),
    )
```

Podemos visualizar:

```text
             gather()
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
    tarefa A tarefa B tarefa C
       │        │        │
       └────────┼────────┘
                ▼
          todas concluídas
```

---

## 23.11 Criando um servidor TCP assíncrono

Agora podemos aplicar isso a sockets.

O `asyncio` possui uma API própria para servidores TCP:

```python
asyncio.start_server()
```

Um exemplo simples:

```python
import asyncio

async def handle_client(reader, writer):
    data = await reader.read(1024)

    print(f"Recebido: {data!r}")

    writer.write(data)

    await writer.drain()

    writer.close()
    await writer.wait_closed()

async def main():
    server = await asyncio.start_server(
        handle_client,
        "127.0.0.1",
        4444
    )

    async with server:
        await server.serve_forever()

asyncio.run(main())
```

Esse exemplo cria um servidor TCP assíncrono.

---

## 23.12 `asyncio.start_server()`

A função:

```python
asyncio.start_server()
```

é responsável por criar um servidor TCP assíncrono.

Uma forma simplificada da assinatura é:

```python
asyncio.start_server(
    client_connected_cb,
    host=None,
    port=None,
    *,
    family=0,
    flags=socket.AI_PASSIVE,
    sock=None,
    backlog=100,
    ssl=None,
    reuse_address=None,
    reuse_port=None,
    limit=2**16,
    **kwds
)
```

Os parâmetros mais importantes inicialmente são:

|Parâmetro|Função|
|---|---|
|`client_connected_cb`|Função chamada quando um cliente conecta|
|`host`|Endereço local|
|`port`|Porta|
|`family`|Família de endereços|
|`backlog`|Configuração da fila de conexões|
|`ssl`|Configuração de TLS|
|`reuse_address`|Opção relacionada ao endereço|
|`reuse_port`|Reutilização da porta, quando suportada|

Não é necessário memorizar todos os parâmetros agora.

O essencial é entender:

```python
server = await asyncio.start_server(
    handle_client,
    "127.0.0.1",
    4444
)
```

---

## 23.13 `reader` e `writer`

A função:

```python
async def handle_client(reader, writer):
```

recebe dois objetos principais:

```text
reader → leitura
writer  → escrita
```

Podemos pensar:

```text
             Cliente
                │
          ┌─────┴─────┐
          ▼           ▼
       Reader       Writer
          │           │
        read()      write()
```

Eles são abstrações assíncronas construídas sobre a comunicação de rede.

---

## 23.14 Lendo dados com `reader.read()`

Exemplo:

```python
data = await reader.read(1024)
```

Assim como:

```python
sock.recv(1024)
```

isso não significa:

```text
"receba exatamente uma mensagem de 1024 bytes"
```

Significa, conceitualmente:

```text
leia até 1024 bytes disponíveis
```

Pode retornar menos.

E se o cliente encerrar a conexão de forma ordenada, podemos receber:

```python
b""
```

Portanto:

```python
data = await reader.read(1024)

if not data:
    ...
```

continua sendo uma verificação importante.

---

## 23.15 Escrevendo com `writer.write()`

Para enviar dados:

```python
writer.write(data)
```

Por exemplo:

```python
writer.write(b"Hello")
```

Diferentemente de `socket.sendall()`, essa chamada não deve ser interpretada como "a aplicação já enviou fisicamente todos os bytes para o destino".

Ela coloca dados no mecanismo de escrita do transporte.

Por isso existe:

```python
await writer.drain()
```

---

## 23.16 `writer.drain()`

O:

```python
await writer.drain()
```

permite aguardar quando necessário para que o fluxo de escrita respeite o controle de fluxo interno.

Exemplo:

```python
writer.write(data)

await writer.drain()
```

Podemos imaginar:

```text
Aplicação
   │
   ▼
writer.write()
   │
   ▼
buffer de escrita
   │
   ▼
drain()
   │
   ▼
transporte
   │
   ▼
TCP
```

Isso é especialmente relevante quando a aplicação produz dados mais rapidamente do que eles podem ser enviados.

---

## 23.17 Encerrando o `writer`

Depois de terminar a comunicação:

```python
writer.close()
```

E podemos aguardar o fechamento:

```python
await writer.wait_closed()
```

Fluxo:

```text
writer.close()
     │
     ▼
inicia encerramento
     │
     ▼
await writer.wait_closed()
     │
     ▼
aguarda fechamento
```

---

## 23.18 O callback `handle_client`

Quando um cliente conecta, o servidor chama:

```python
handle_client(reader, writer)
```

A estrutura pode ser entendida assim:

```text
Cliente conecta
       │
       ▼
asyncio.start_server()
       │
       ▼
handle_client()
       │
       ├── read()
       ├── processa
       ├── write()
       ├── drain()
       └── close()
```

Isso elimina a necessidade de escrever manualmente:

```python
accept()
```

no código da aplicação.

O próprio `asyncio` gerencia essa parte.

---

## 23.19 Comparando socket tradicional com `asyncio`

### Socket tradicional

```python
server.accept()

data = client.recv(1024)

client.sendall(data)
```

### `asyncio`

```python
data = await reader.read(1024)

writer.write(data)

await writer.drain()
```

A ideia continua sendo a mesma:

```text
aceitar
   ↓
receber
   ↓
processar
   ↓
enviar
   ↓
encerrar
```

A diferença está no modelo de execução e nas abstrações usadas.

---

## 23.20 O que acontece internamente?

Podemos pensar no `asyncio` desta forma:

```text
Código Python
     │
     ▼
async / await
     │
     ▼
Corrotinas
     │
     ▼
Event Loop
     │
     ▼
Mecanismos de I/O do SO
     │
     ├── epoll
     ├── kqueue
     ├── select
     └── outros mecanismos
     │
     ▼
Sockets
     │
     ▼
TCP/IP
     │
     ▼
Rede
```

Portanto, `asyncio` não elimina os mecanismos que estudamos anteriormente.

Ele fornece uma abstração para trabalhar com eles.

---

## 23.21 `asyncio` não torna qualquer código assíncrono

Um erro comum é pensar:

```python
async def minha_funcao():
    ...
```

e concluir:

> "Agora tudo dentro dela é assíncrono."

Não é assim.

Por exemplo:

```python
async def tarefa():
    time.sleep(10)
```

continua sendo bloqueante.

O `asyncio` não consegue simplesmente interromper:

```python
time.sleep(10)
```

porque essa chamada bloqueia a thread.

O correto, nesse exemplo, seria:

```python
async def tarefa():
    await asyncio.sleep(10)
```

---

## 23.22 Um erro ainda mais perigoso: I/O bloqueante

Imagine:

```python
async def handle_client(reader, writer):
    resultado = requests.get(url)
```

Se `requests.get()` executar uma operação bloqueante longa, o event loop pode ficar parado durante essa operação.

Visualmente:

```text
Event Loop
    │
    ▼
handle_client()
    │
    ▼
requests.get()
    │
    X
 bloqueou
    │
    X
outras tarefas esperando
```

Por isso, em aplicações assíncronas precisamos ter cuidado com bibliotecas bloqueantes.

Existem bibliotecas específicas para operações assíncronas.

---

## 23.23 `asyncio` e CPU-bound

`asyncio` é especialmente interessante para:

- sockets;
    
- servidores web;
    
- APIs;
    
- proxies;
    
- clientes HTTP;
    
- WebSockets;
    
- serviços de rede;
    
- muitas conexões simultâneas.
    

Mas uma tarefa pesada de CPU pode bloquear o event loop.

Por exemplo:

```python
async def tarefa():
    for i in range(10_000_000_000):
        ...
```

Enquanto essa função estiver executando sem ceder controle:

```text
Event Loop
    │
    ▼
CPU pesada
    │
    X
outras tarefas não avançam
```

Nesse cenário, podem ser necessários:

- processos;
    
- `ProcessPoolExecutor`;
    
- código nativo;
    
- outras estratégias de paralelismo.
    

---

## 23.24 `asyncio` versus `selectors`

Os dois estão relacionados.

Com `selectors`, podemos trabalhar diretamente com o mecanismo de multiplexação:

```text
selectors
    │
    ▼
select()/poll()/epoll()/kqueue
    │
    ▼
sockets
```

Com `asyncio`:

```text
async/await
    │
    ▼
event loop
    │
    ▼
mecanismo de I/O
    │
    ▼
sockets
```

Ou seja:

> `asyncio` fornece uma abstração de nível mais alto para construir programas concorrentes orientados a I/O.

---

## 23.25 `asyncio` versus threads

Também podemos comparar:

|Característica|Threads|`asyncio`|
|---|---|---|
|Modelo|Threads|Corrotinas|
|Controle|Sistema operacional + Python|Event loop|
|I/O|Pode bloquear|Normalmente assíncrono|
|Memória por tarefa|Maior|Geralmente menor|
|Escala para muitas conexões|Pode ser mais pesada|Muito adequada|
|Código|Pode ser mais intuitivo|Exige `async`/`await`|
|CPU-bound|Não resolve automaticamente|Não resolve automaticamente|
|I/O-bound|Excelente|Excelente|

Não existe uma solução universalmente melhor.

---

## 23.26 Modelo mental final

O modelo mais importante desta parte é:

```text
                   Aplicação
                       │
                       ▼
                async / await
                       │
                       ▼
                  Corrotinas
                       │
                       ▼
                 Event Loop
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Cliente A    Cliente B    Cliente C
       aguardando   aguardando   executando
          I/O          I/O
                       │
                       ▼
                 mecanismo de I/O
                       │
                       ▼
                    Socket
                       │
                       ▼
                     TCP
                       │
                       ▼
                     IP
                       │
                       ▼
                    Rede
```

A grande ideia é:

> **Uma corrotina pode esperar I/O sem bloquear o event loop inteiro, permitindo que outras corrotinas avancem enquanto a operação está aguardando.**

---

## 23.27 Resumo da Parte

- `asyncio` é o framework assíncrono da biblioteca padrão do Python.
    
- O modelo é baseado em **corrotinas** e **event loop**.
    
- Funções assíncronas são definidas com `async def`.
    
- `await` permite suspender uma corrotina durante uma operação aguardável.
    
- `asyncio.run()` inicia um event loop para executar a corrotina principal.
    
- `asyncio.create_task()` agenda uma corrotina como tarefa.
    
- `asyncio.gather()` permite aguardar várias operações concorrentes.
    
- `asyncio.start_server()` permite criar servidores TCP assíncronos.
    
- `StreamReader` é utilizado para leitura.
    
- `StreamWriter` é utilizado para escrita.
    
- `reader.read()` continua respeitando as características de stream do TCP.
    
- `writer.write()` envia dados para o fluxo de saída.
    
- `writer.drain()` ajuda a lidar com o fluxo de escrita e backpressure.
    
- `writer.close()` inicia o encerramento.
    
- `await writer.wait_closed()` aguarda o fechamento.
    
- `asyncio` não transforma automaticamente código bloqueante em assíncrono.
    
- Operações bloqueantes dentro do event loop podem prejudicar todas as tarefas.
    
- `asyncio` é especialmente útil para aplicações **I/O-bound** com muitas conexões simultâneas.
    
- `asyncio` utiliza mecanismos de I/O do sistema operacional por baixo da abstração.
    

**Modelo final:**

```text
socket tradicional
       ↓
controle manual de I/O
       ↓
selectors
       ↓
multiplexação
       ↓
asyncio
       ↓
event loop + corrotinas
       ↓
programação assíncrona
```


---

# 24. TLS/SSL com sockets e a biblioteca `ssl`

Até agora trabalhamos principalmente com sockets TCP diretamente.

O TCP fornece:

- conexão;
    
- entrega confiável;
    
- ordenação dos bytes;
    
- retransmissão;
    
- controle de fluxo;
    
- controle de congestionamento.
    

Mas existe um problema fundamental:

> **TCP não criptografa os dados.**

Se fizermos:

```python
client.sendall(b"senha=123456")
```

os bytes são transportados pelo TCP, mas o TCP não fornece confidencialidade.

Para proteger a comunicação, podemos utilizar **TLS**.

---

## 24.1 O que é TLS?

**TLS (Transport Layer Security)** é um protocolo criptográfico utilizado para proteger comunicações através de uma rede.

Ele fornece principalmente:

```text
Confidencialidade
Integridade
Autenticação
```

Podemos imaginar:

```text
Sem TLS:

Aplicação
   │
   ▼
TCP
   │
   ▼
Rede
   │
   ▼
Servidor

Dados podem ser observados/modificados
por alguém que consiga interceptar a comunicação.
```

Com TLS:

```text
Aplicação
   │
   ▼
TLS
   │
   ▼
TCP
   │
   ▼
Rede
   │
   ▼
TCP
   │
   ▼
TLS
   │
   ▼
Aplicação
```

O TLS cria uma camada de segurança sobre o transporte.

---

## 24.2 TLS não substitui TCP

Uma confusão comum é pensar:

```text
TLS = protocolo de transporte
```

Não é essa a ideia.

Normalmente temos:

```text
Aplicação
     │
     ▼
    TLS
     │
     ▼
    TCP
     │
     ▼
     IP
     │
     ▼
    Rede
```

Por exemplo, no HTTPS:

```text
HTTP
 │
 ▼
TLS
 │
 ▼
TCP
 │
 ▼
IP
```

O HTTP continua sendo responsável pelo protocolo da aplicação.

O TLS protege a comunicação.

O TCP continua fornecendo o transporte confiável.

---

## 24.3 O que o TLS protege?

Imagine que um cliente envie:

```text
LOGIN allan
PASSWORD minha_senha
```

Sem criptografia, os dados da aplicação são enviados diretamente ao TCP.

Com TLS:

```text
Aplicação
    │
    ▼
dados originais
    │
    ▼
TLS
    │
    ▼
dados protegidos
    │
    ▼
TCP
    │
    ▼
Rede
```

Quem interceptar os pacotes não deverá conseguir simplesmente ler o conteúdo da aplicação.

---

## 24.4 Confidencialidade

A confidencialidade significa que o conteúdo da comunicação é protegido contra leitura por terceiros.

Por exemplo:

```text
Original:

senha=123456
```

O conteúdo transmitido através do TLS não aparece simplesmente como:

```text
senha=123456
```

para um observador da rede.

A criptografia transforma os dados em uma representação protegida.

Conceitualmente:

```text
plaintext
    │
    ▼
  TLS
    │
    ▼
ciphertext
    │
    ▼
  rede
```

No destino:

```text
ciphertext
    │
    ▼
  TLS
    │
    ▼
plaintext
```

---

## 24.5 Integridade

TLS também ajuda a detectar alterações indevidas nos dados durante o transporte.

Imagine:

```text
Cliente
   │
   │ "transferir=100"
   ▼
Rede
   │
   X alguém tenta alterar
   │
   ▼
Servidor
```

O mecanismo criptográfico do TLS permite detectar alterações que não foram produzidas legitimamente pela comunicação.

Isso protege contra modificações silenciosas dos dados.

---

## 24.6 Autenticação

TLS também pode fornecer autenticação.

No caso mais comum de HTTPS:

```text
Cliente
   │
   │ "Estou conectado ao servidor?"
   ▼
Servidor
   │
   ▼
certificado
```

O certificado permite ao cliente verificar a identidade apresentada pelo servidor, desde que a cadeia de confiança seja válida e o nome esperado corresponda.

Isso é fundamental para evitar que um atacante simplesmente se apresente como o servidor legítimo.

---

## 24.7 O certificado digital

Um certificado TLS normalmente contém informações como:

```text
Identidade do servidor
Nome(s) para os quais o certificado é válido
Chave pública
Autoridade certificadora
Período de validade
Assinatura da autoridade certificadora
```

Podemos visualizar:

```text
Certificado
├── Identidade
├── Domínios
├── Chave pública
├── Validade
├── CA
└── Assinatura
```

A assinatura permite que o cliente valide a origem do certificado dentro da cadeia de confiança.

---

## 24.8 CA — Certificate Authority

**CA (Certificate Authority)** é uma autoridade certificadora.

Exemplos conhecidos no ecossistema TLS incluem organizações que emitem certificados para servidores.

O modelo é aproximadamente:

```text
                 CA confiável
                     │
                     │ assina
                     ▼
              Certificado
                     │
                     ▼
                  Servidor
                     │
                     ▼
                  Cliente
```

O cliente possui um conjunto de autoridades confiáveis.

Quando recebe um certificado, pode verificar sua cadeia de confiança.

---

## 24.9 O handshake TLS

Antes de a aplicação utilizar a conexão protegida, cliente e servidor precisam estabelecer parâmetros criptográficos.

Isso acontece durante o **TLS handshake**.

Uma visão simplificada:

```text
Cliente                         Servidor
   │                                │
   │──── informações iniciais ─────►│
   │                                │
   │◄──── certificado/configuração ─│
   │                                │
   │──── informações necessárias ──►│
   │                                │
   │◄──── confirmação ──────────────│
   │                                │
   │════ comunicação protegida ═════│
```

O handshake real é mais complexo e depende da versão e da configuração do TLS.

O objetivo é estabelecer uma sessão criptograficamente protegida.

---

## 24.10 TLS 1.2 e TLS 1.3

Existem diferentes versões do TLS.

Atualmente, as versões modernas relevantes são principalmente:

```text
TLS 1.2
TLS 1.3
```

O TLS 1.3 simplificou e melhorou partes do protocolo de handshake e removeu mecanismos criptográficos considerados inadequados.

Ao desenvolver uma aplicação moderna, devemos utilizar configurações seguras fornecidas pela biblioteca e pelo sistema, em vez de tentar construir manualmente os mecanismos criptográficos.

---

## 24.11 A biblioteca `ssl` do Python

Python fornece o módulo:

```python
import ssl
```

A biblioteca `ssl` permite utilizar TLS sobre sockets.

Podemos imaginar:

```text
socket.socket()
      │
      ▼
socket TCP
      │
      ▼
ssl.SSLContext
      │
      ▼
TLS
```

O objeto mais importante para começar é:

```python
ssl.SSLContext
```

---

## 24.12 Por que utilizar `SSLContext`?

Em vez de configurar cada detalhe criptográfico diretamente em cada conexão, utilizamos um contexto.

Exemplo:

```python
context = ssl.create_default_context()
```

Esse contexto representa uma configuração TLS.

Podemos então utilizá-lo para criar uma conexão segura.

---

## 24.13 Cliente TLS

Um cliente pode criar um socket TCP:

```python
import socket
import ssl

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Depois podemos criar um contexto:

```python
context = ssl.create_default_context()
```

E envolver o socket:

```python
secure_sock = context.wrap_socket(
    sock,
    server_hostname="example.com"
)
```

Depois:

```python
secure_sock.connect(
    ("example.com", 443)
)
```

A ideia é:

```text
socket TCP
    │
    ▼
wrap_socket()
    │
    ▼
socket TLS
    │
    ▼
connect()
```

---

## 24.14 Por que `server_hostname` é importante?

Observe:

```python
server_hostname="example.com"
```

Esse parâmetro é importante para a autenticação do servidor.

Em conexões TLS modernas, o servidor pode hospedar vários domínios no mesmo endereço IP.

Por exemplo:

```text
IP: 203.0.113.10

    ├── exemplo.com
    ├── site.com
    └── api.com
```

O cliente precisa indicar qual nome está tentando acessar.

Esse mecanismo está relacionado ao **SNI (Server Name Indication)**.

Além disso, o nome é utilizado na verificação da identidade do certificado quando a validação está habilitada.

---

## 24.15 Forma mais simples para clientes

Para clientes, geralmente é melhor utilizar:

```python
ssl.create_default_context()
```

em vez de criar manualmente uma configuração insegura.

Exemplo:

```python
import socket
import ssl

context = ssl.create_default_context()

with socket.create_connection(
    ("example.com", 443)
) as sock:

    with context.wrap_socket(
        sock,
        server_hostname="example.com"
    ) as secure_sock:

        secure_sock.sendall(
            b"GET / HTTP/1.1\r\n"
            b"Host: example.com\r\n"
            b"Connection: close\r\n"
            b"\r\n"
        )

        response = secure_sock.recv(4096)

        print(response)
```

Aqui temos:

```text
socket.create_connection()
        │
        ▼
TCP
        │
        ▼
wrap_socket()
        │
        ▼
TLS handshake
        │
        ▼
comunicação protegida
```

---

## 24.16 `create_default_context()`

A função:

```python
ssl.create_default_context()
```

é uma maneira recomendada de obter uma configuração padrão apropriada para uso como cliente TLS.

Exemplo:

```python
context = ssl.create_default_context()
```

Ela configura o contexto para realizar verificações apropriadas de certificados em cenários comuns de cliente.

Isso é muito diferente de simplesmente desabilitar a verificação.

---

## 24.17 O erro perigoso: desabilitar a verificação

Podemos encontrar códigos como:

```python
context.check_hostname = False
```

ou:

```python
context.verify_mode = ssl.CERT_NONE
```

Essas configurações podem ser úteis em cenários muito específicos de laboratório, mas são perigosas para uma aplicação que precisa autenticar o servidor.

Por exemplo:

```text
Cliente
   │
   ▼
Atacante
   │
   ▼
Servidor falso
```

Se o cliente não verificar adequadamente o certificado e o nome do servidor, pode acabar estabelecendo uma conexão criptografada com o **atacante**, em vez do servidor legítimo.

Portanto:

> **Criptografia sem autenticação não garante que você esteja falando com o servidor correto.**

---

## 24.18 TLS não significa automaticamente "servidor confiável"

Imagine:

```text
Cliente ─── TLS ─── Atacante
```

A conexão pode estar criptografada.

Mas isso não significa que:

```text
Atacante = servidor legítimo
```

Por isso existem dois conceitos diferentes:

```text
Criptografia
    ↓
protege o conteúdo

Autenticação
    ↓
verifica a identidade
```

Uma conexão TLS corretamente configurada busca fornecer ambos.

---

## 24.19 Servidor TLS

No lado do servidor, precisamos de um certificado e da chave privada correspondente.

Conceitualmente:

```text
Servidor
├── certificado
└── chave privada
```

Criamos um contexto:

```python
context = ssl.SSLContext(
    ssl.PROTOCOL_TLS_SERVER
)
```

Depois carregamos o certificado:

```python
context.load_cert_chain(
    certfile="server.crt",
    keyfile="server.key"
)
```

E podemos envolver o socket de escuta:

```python
secure_server = context.wrap_socket(
    server,
    server_side=True
)
```

---

## 24.20 Exemplo de servidor TLS

Um exemplo didático:

```python
import socket
import ssl

context = ssl.SSLContext(
    ssl.PROTOCOL_TLS_SERVER
)

context.load_cert_chain(
    certfile="server.crt",
    keyfile="server.key"
)

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.bind(("127.0.0.1", 4444))
server.listen()

secure_server = context.wrap_socket(
    server,
    server_side=True
)

while True:
    client, address = secure_server.accept()

    print(f"Cliente conectado: {address}")

    data = client.recv(1024)

    if data:
        print(data)

        client.sendall(data)

    client.close()
```

Agora o fluxo é:

```text
Cliente
   │
   ▼
TLS
   │
   ▼
TCP
   │
   ▼
Servidor TLS
   │
   ▼
Aplicação
```

---

## 24.21 Onde ficam os certificados?

No servidor temos:

```text
server.crt
server.key
```

O certificado pode ser distribuído.

A chave privada **não deve ser distribuída**.

Podemos visualizar:

```text
server.crt
   │
   └── pode ser enviado aos clientes

server.key
   │
   └── permanece protegida no servidor
```

A chave privada é uma informação extremamente sensível.

Se um atacante obtiver a chave privada utilizada pelo servidor, as consequências podem ser graves, dependendo do cenário e da configuração.

---

## 24.22 Certificado não é a chave privada

É importante não confundir:

```text
Certificado
```

com:

```text
Chave privada
```

O certificado contém, entre outras informações, uma **chave pública**.

A chave privada correspondente deve permanecer protegida.

Conceitualmente:

```text
             Par de chaves
          ┌─────────────────┐
          │                 │
          ▼                 ▼
      Pública            Privada
          │                 │
          │                 └── protegida
          │
          ▼
      certificado
```

---

## 24.23 TLS e autenticação mútua

Até agora falamos do cenário:

```text
Cliente ── verifica ──► Servidor
```

Mas TLS também pode ser configurado para autenticação mútua.

Nesse cenário:

```text
Cliente ── autentica ──► Servidor
Cliente ◄─ autentica ─── Servidor
```

Isso é chamado de **mTLS (mutual TLS)**.

É utilizado em alguns ambientes onde tanto o servidor quanto o cliente precisam apresentar credenciais baseadas em certificados.

Exemplo conceitual:

```text
Cliente
  │
  │ certificado do cliente
  ▼
Servidor
  │
  │ certificado do servidor
  ▼
Cliente
```

Esse assunto é mais avançado, mas é importante conhecer o conceito.

---

## 24.24 TLS não resolve todos os problemas de segurança

Adicionar TLS não significa que a aplicação ficou automaticamente segura.

TLS protege principalmente o canal de comunicação.

Ainda podemos ter:

```text
TLS ✓
mas

SQL Injection
    ✗

Path Traversal
    ✗

Autenticação fraca
    ✗

Autorização incorreta
    ✗

RCE
    ✗

Validação de entrada ruim
    ✗
```

Por exemplo:

```text
Cliente
   │
   │ conexão TLS segura
   ▼
Servidor
   │
   ▼
"DELETE /todos_os_usuarios"
```

Se o servidor aceitar comandos perigosos sem autorização adequada, o TLS não impedirá isso.

TLS protege a comunicação.

A segurança da aplicação continua sendo responsabilidade do software.

---

## 24.25 TLS e o modelo das camadas

Agora podemos atualizar nosso modelo:

```text
┌──────────────────────────────┐
│ Aplicação                   │
│ HTTP / protocolo próprio    │
├──────────────────────────────┤
│ TLS                         │
│ criptografia/autenticação   │
├──────────────────────────────┤
│ TCP                         │
│ transporte confiável        │
├──────────────────────────────┤
│ IP                          │
│ endereçamento/roteamento    │
├──────────────────────────────┤
│ Link físico/rede local      │
└──────────────────────────────┘
```

Cada camada possui uma responsabilidade diferente.

---

## 24.26 TCP versus TLS

|Característica|TCP|TLS|
|---|---|---|
|Transporte confiável|Sim|Utiliza o transporte subjacente|
|Ordenação dos bytes|Sim|Utiliza o stream fornecido pelo TCP|
|Criptografia|Não|Sim|
|Integridade criptográfica|Não|Sim|
|Autenticação do servidor|Não|Sim, quando configurado/verificado|
|Protocolo de aplicação|Não|Também não|
|Porta própria|Não|Não necessariamente|
|Proteção contra espionagem|Não|Sim|

Portanto:

```text
TCP
+
TLS
+
protocolo de aplicação
```

formam uma pilha de comunicação muito comum.

---

## 24.27 HTTPS

O exemplo mais conhecido é o HTTPS:

```text
HTTP
  │
  ▼
TLS
  │
  ▼
TCP
  │
  ▼
IP
```

Quando acessamos:

```text
https://exemplo.com
```

o navegador normalmente estabelece uma conexão protegida por TLS antes de trocar os dados HTTP protegidos.

Assim:

```text
GET /login
```

é transportado dentro da sessão TLS.

---

## 24.28 O que um atacante na rede consegue observar?

Mesmo com TLS, alguns metadados podem continuar visíveis dependendo da versão, configuração e infraestrutura.

Por exemplo, um observador pode conseguir inferir informações como:

```text
IP de origem
IP de destino
porta
volume aproximado de tráfego
momento da comunicação
duração da conexão
```

TLS não significa:

```text
"nada sobre a comunicação é observável."
```

Significa principalmente que o **conteúdo protegido da sessão** não fica disponível em texto simples para um observador externo.

---

## 24.29 Modelo mental definitivo

A evolução do nosso socket é:

```text
Socket TCP
     │
     ▼
conexão confiável
     │
     ▼
TLS
     │
     ├── confidencialidade
     ├── integridade
     └── autenticação
     │
     ▼
Protocolo da aplicação
     │
     ├── HTTP
     ├── protocolo próprio
     └── outro protocolo
```

Podemos resumir assim:

```text
TCP  → transporta bytes de forma confiável
TLS  → protege esses bytes
App  → define o significado desses bytes
```

---

## 24.30 Resumo da Parte

- **TCP não criptografa os dados.**
    
- **TLS** fornece proteção criptográfica sobre o transporte.
    
- TLS oferece principalmente **confidencialidade, integridade e autenticação**.
    
- TLS normalmente funciona sobre TCP.
    
- HTTPS é essencialmente **HTTP sobre TLS sobre TCP**.
    
- Python fornece o módulo `ssl` para trabalhar com TLS.
    
- `ssl.SSLContext` representa uma configuração TLS.
    
- `ssl.create_default_context()` é uma forma adequada de criar um contexto padrão para clientes.
    
- `server_hostname` é importante para autenticação do servidor e está relacionado ao SNI.
    
- Servidores TLS precisam de certificado e chave privada.
    
- A chave privada deve permanecer protegida no servidor.
    
- Certificado e chave privada são coisas diferentes.
    
- Desabilitar `CERT_NONE` ou a verificação de hostname pode remover uma proteção essencial contra servidores falsos.
    
- **Criptografia não é a mesma coisa que autenticação.**
    
- TLS protege o canal, mas não corrige vulnerabilidades da aplicação.
    
- TLS também pode ser utilizado para autenticação mútua através de **mTLS**.
    
- `asyncio` e TLS podem ser combinados para construir servidores assíncronos seguros.
    
- O modelo geral fica:
    

```text
Aplicação
    │
    ▼
   TLS
    │
    ▼
   TCP
    │
    ▼
    IP
    │
    ▼
   Rede
```

---

# 25. Certificados, autoridades certificadoras e validação TLS

Na parte anterior vimos que o TLS pode fornecer **criptografia, integridade e autenticação**.

Agora precisamos entender uma questão fundamental:

> **Como o cliente sabe que o certificado apresentado pelo servidor realmente pertence ao servidor que ele está tentando acessar?**

É aqui que entram conceitos como:

- certificado digital;
    
- chave pública;
    
- chave privada;
    
- autoridade certificadora (CA);
    
- cadeia de confiança;
    
- hostname;
    
- validade;
    
- verificação de certificado.
    

---

## 25.1 O problema da autenticação

Imagine que você queira acessar:

```text
https://banco.example
```

Você estabelece uma conexão TLS.

Mas existe um problema:

```text
"Como sei que o servidor do outro lado é realmente banco.example?"
```

Um atacante poderia tentar fazer:

```text
Cliente
   │
   ▼
Atacante
   │
   ▼
Servidor legítimo
```

Se o cliente simplesmente aceitasse qualquer certificado apresentado, o atacante poderia criar seu próprio certificado e dizer:

```text
"Eu sou banco.example"
```

Por isso, não basta existir criptografia.

Precisamos verificar a **identidade do servidor**.

---

## 25.2 Certificado digital como identidade criptográfica

Um certificado digital pode ser entendido, de forma simplificada, como uma declaração assinada que associa:

```text
identidade
    +
chave pública
```

Por exemplo:

```text
Certificado
├── Nome: exemplo.com
├── Chave pública: ...
├── Validade: ...
├── Emissor: CA X
└── Assinatura da CA
```

A ideia é:

```text
"Esta chave pública está associada a este nome,
e uma autoridade confiável assinou essa afirmação."
```

---

## 25.3 Chave pública e chave privada

O sistema utiliza um par de chaves:

```text
┌─────────────────────┐
│ Par criptográfico   │
├─────────────────────┤
│ Chave pública       │
│ Chave privada       │
└─────────────────────┘
```

A chave pública pode ser distribuída.

A chave privada deve ser protegida.

Podemos representar:

```text
Servidor
   │
   ├── chave privada 🔒
   │
   └── chave pública
            │
            ▼
        certificado
```

O certificado contém a chave pública do servidor, juntamente com informações sobre sua identidade e a assinatura da autoridade certificadora.

---

## 25.4 O que uma CA faz?

Uma **CA (Certificate Authority)** é uma entidade responsável por emitir ou assinar certificados dentro de uma infraestrutura de confiança.

O modelo simplificado é:

```text
              CA
              │
              │ assina
              ▼
       Certificado
              │
              ▼
          Servidor
```

O cliente possui um conjunto de CAs consideradas confiáveis.

Assim, quando recebe um certificado, pode perguntar:

```text
"Quem assinou este certificado?"
```

e depois:

```text
"Eu confio nessa autoridade?"
```

---

## 25.5 Cadeia de confiança

Na prática, a confiança pode envolver vários certificados.

Podemos ter:

```text
Root CA
   │
   ▼
Intermediate CA
   │
   ▼
Certificado do servidor
```

Isso é chamado de **cadeia de certificação** ou **cadeia de confiança**.

Por exemplo:

```text
Root CA
  │
  └── assina
        │
        ▼
Intermediate CA
  │
  └── assina
        │
        ▼
Certificado exemplo.com
```

O cliente verifica essa cadeia até chegar a uma autoridade raiz que esteja em seu conjunto de confiança.

---

## 25.6 Root CA

A **Root CA** ocupa uma posição especial na cadeia.

Ela normalmente é uma autoridade cuja confiança já está configurada no sistema operacional, navegador ou ambiente de execução.

Podemos imaginar:

```text
Sistema operacional
       │
       ▼
Trust Store
       │
       ├── Root CA A
       ├── Root CA B
       ├── Root CA C
       └── ...
```

O cliente pode utilizar essa base de confiança para validar certificados.

---

## 25.7 Trust Store

O **trust store** é o conjunto de certificados de autoridades consideradas confiáveis por determinado ambiente.

Dependendo do sistema, ele pode estar localizado em diferentes arquivos ou diretórios.

No Linux, por exemplo, existem mecanismos do sistema para disponibilizar certificados CA confiáveis.

No Python, o contexto TLS pode utilizar as autoridades confiáveis disponibilizadas pelo sistema ou pela configuração do ambiente.

A ideia principal é:

```text
Servidor
   │
   ▼
certificado
   │
   ▼
cadeia
   │
   ▼
CA confiável?
   │
   ├── sim → pode continuar
   │
   └── não → falha de validação
```

---

## 25.8 Verificação do hostname

Mesmo que o certificado seja assinado por uma CA confiável, ainda existe outra pergunta:

> **O certificado foi emitido para o servidor que estou tentando acessar?**

Imagine:

```text
Cliente quer:
api.exemplo.com
```

Mas o certificado apresenta:

```text
outro-site.com
```

Mesmo que esse certificado seja válido e assinado por uma CA confiável, ele não corresponde ao hostname desejado.

Por isso, a validação também verifica o nome.

---

## 25.9 SAN — Subject Alternative Name

Nos certificados modernos, os nomes dos hosts são normalmente encontrados no campo:

```text
Subject Alternative Name
```

ou simplesmente:

```text
SAN
```

Um certificado pode conter vários nomes:

```text
SAN:
    exemplo.com
    www.exemplo.com
    api.exemplo.com
```

Assim, o mesmo certificado pode ser válido para diferentes nomes, desde que estejam adequadamente incluídos.

---

## 25.10 Wildcards

Certificados também podem utilizar nomes com wildcard em determinadas posições.

Por exemplo:

```text
*.example.com
```

Pode representar nomes como:

```text
www.example.com
api.example.com
mail.example.com
```

Mas não significa automaticamente:

```text
example.com
```

O comportamento de correspondência de nomes possui regras específicas.

Por isso, não devemos simplesmente pensar:

```text
*.example.com = qualquer coisa
```

---

## 25.11 Validade temporal

Um certificado possui um período de validade.

Podemos imaginar:

```text
Not Before
     │
     ▼
┌─────────────────────┐
│ certificado válido  │
└─────────────────────┘
     │
     ▼
Not After
```

Se o certificado estiver:

```text
ainda não válido
```

ou:

```text
expirado
```

a validação pode falhar.

Por isso, certificados possuem datas de início e término.

---

## 25.12 Assinatura digital do certificado

A CA utiliza sua chave privada para assinar o certificado.

De maneira simplificada:

```text
Informações do certificado
          │
          ▼
       assinatura
          │
          ▼
     Certificado
```

O cliente possui a chave pública correspondente da CA através de sua cadeia de confiança.

Assim, consegue verificar se o certificado foi realmente assinado pela autoridade correspondente e se não foi alterado.

---

## 25.13 Não confunda assinatura com criptografia do certificado

Um certificado assinado não significa:

```text
"o certificado inteiro está criptografado."
```

A assinatura digital tem como objetivo permitir verificar:

- autenticidade da assinatura;
    
- integridade dos dados assinados.
    

A criptografia da comunicação TLS é outra questão.

Portanto:

```text
Assinatura digital
        ↓
autenticidade/integridade

Criptografia TLS
        ↓
confidencialidade da comunicação
```

São conceitos relacionados, mas diferentes.

---

## 25.14 Processo simplificado de validação

Quando um cliente recebe um certificado, podemos imaginar:

```text
                 Certificado
                      │
                      ▼
             ┌─────────────────┐
             │ Está dentro da  │
             │ validade?       │
             └───────┬─────────┘
                     │
                sim  │  não
                     │
                     ▼
             ┌─────────────────┐
             │ Hostname        │
             │ corresponde?    │
             └───────┬─────────┘
                     │
                sim  │  não
                     │
                     ▼
             ┌─────────────────┐
             │ Cadeia confiável│
             │ até uma CA?     │
             └───────┬─────────┘
                     │
                sim  │  não
                     │
                     ▼
               Certificado
                  aceito
```

A implementação real possui mais detalhes, mas esse modelo é excelente para entender o conceito.

---

## 25.15 O que acontece se a validação falhar?

O Python pode gerar exceções relacionadas ao TLS, como:

```python
ssl.SSLCertVerificationError
```

Por exemplo, se o certificado apresentado não puder ser validado corretamente.

Conceitualmente:

```python
try:
    ...
except ssl.SSLCertVerificationError as e:
    print(f"Certificado inválido: {e}")
```

Isso é muito melhor do que simplesmente desabilitar a validação.

---

## 25.16 Exemplo de cliente corretamente configurado

Podemos utilizar:

```python
import socket
import ssl

context = ssl.create_default_context()

with socket.create_connection(
    ("example.com", 443)
) as sock:

    with context.wrap_socket(
        sock,
        server_hostname="example.com"
    ) as secure_sock:

        print(secure_sock.version())

        secure_sock.sendall(
            b"GET / HTTP/1.1\r\n"
            b"Host: example.com\r\n"
            b"Connection: close\r\n"
            b"\r\n"
        )

        while True:
            data = secure_sock.recv(4096)

            if not data:
                break

            print(data)
```

Aqui:

```text
create_default_context()
        │
        ▼
configuração TLS segura
        │
        ▼
wrap_socket()
        │
        ▼
server_hostname
        │
        ▼
handshake + validação
        │
        ▼
comunicação protegida
```

---

## 25.17 Obtendo informações do certificado

Depois de estabelecer uma conexão TLS, podemos consultar informações:

```python
certificate = secure_sock.getpeercert()
```

Exemplo:

```python
print(certificate)
```

O retorno pode conter informações estruturadas sobre o certificado do peer.

Também podemos consultar:

```python
secure_sock.version()
```

para descobrir a versão do TLS negociada.

Por exemplo, dependendo da configuração:

```text
TLSv1.3
```

---

## 25.18 `cipher()`

Também podemos consultar o conjunto criptográfico negociado:

```python
secure_sock.cipher()
```

Por exemplo:

```python
print(secure_sock.cipher())
```

Isso retorna informações sobre o cipher suite utilizado na sessão.

Não é necessário decorar nomes de cipher suites agora.

O importante é entender que durante o handshake o cliente e o servidor negociam parâmetros criptográficos compatíveis.

---

## 25.19 Negociação TLS

O cliente e o servidor precisam encontrar parâmetros que ambos suportem.

Conceitualmente:

```text
Cliente
 ├── versões suportadas
 ├── algoritmos suportados
 └── extensões
          │
          ▼
       Handshake
          │
          ▼
Servidor
 ├── versões suportadas
 ├── algoritmos suportados
 └── extensões
```

O protocolo negocia uma configuração compatível.

TLS moderno possui mecanismos para fazer essa negociação de maneira segura.

---

## 25.20 TLS não usa apenas "uma chave"

Uma simplificação comum é pensar:

```text
"TLS pega uma senha e criptografa tudo com ela."
```

A realidade é mais sofisticada.

Durante o handshake são estabelecidos segredos que serão utilizados para proteger a sessão.

Podemos simplificar:

```text
Handshake
    │
    ▼
estabelecimento de segredos
    │
    ▼
chaves de sessão
    │
    ▼
dados protegidos
```

As técnicas criptográficas utilizadas no handshake e na proteção dos dados possuem papéis diferentes.

---

## 25.21 Criptografia assimétrica e simétrica

TLS utiliza conceitos de criptografia assimétrica e simétrica.

### Assimétrica

Utiliza um par:

```text
chave pública
chave privada
```

Ela é útil para autenticação e estabelecimento seguro de parâmetros.

### Simétrica

Utiliza uma chave secreta compartilhada para proteger os dados da sessão.

De forma simplificada:

```text
Handshake
   │
   ├── autenticação
   └── estabelecimento de segredos
             │
             ▼
       chave(s) de sessão
             │
             ▼
      dados da aplicação
```

A criptografia simétrica é muito mais adequada para proteger grandes volumes de dados durante a sessão.

---

## 25.22 Por que não usar somente criptografia assimétrica?

Criptografia assimétrica possui custos computacionais diferentes da criptografia simétrica.

Por isso, não é eficiente imaginar:

```text
cada byte da comunicação
        ↓
criptografia assimétrica
```

Em vez disso, TLS utiliza mecanismos criptográficos diferentes para diferentes etapas da comunicação.

O resultado é uma combinação eficiente de:

```text
autenticação
+
estabelecimento seguro de chaves
+
criptografia simétrica da sessão
```

---

## 25.23 Forward Secrecy

Outro conceito importante é **Forward Secrecy**, também chamado de Perfect Forward Secrecy em determinados contextos.

A ideia simplificada é:

> O comprometimento posterior de uma chave de longo prazo não deve permitir automaticamente a descriptografia de sessões passadas que utilizaram segredos efêmeros apropriados.

Podemos imaginar:

```text
Sessão A → segredo A
Sessão B → segredo B
Sessão C → segredo C
```

Em vez de:

```text
todas as sessões
       │
       ▼
uma única chave de sessão permanente
```

Esse conceito ajuda a limitar o impacto de determinados comprometimentos futuros.

TLS moderno utiliza mecanismos de estabelecimento de chaves que fornecem essa propriedade em configurações apropriadas.

---

## 25.24 O perigo do "TLS sem validação"

Considere:

```python
context = ssl.create_default_context()

context.check_hostname = False
context.verify_mode = ssl.CERT_NONE
```

Agora imagine:

```text
Cliente
   │
   ▼
Atacante
   │
   ▼
Servidor legítimo
```

O cliente pode estabelecer uma conexão TLS com o atacante.

A conexão pode estar criptografada.

Mas o cliente não verificou corretamente a identidade do servidor.

Portanto:

```text
TLS
+
sem autenticação adequada
=
criptografia sem garantia de identidade
```

Esse é um erro muito importante em aplicações de segurança.

---

## 25.25 TLS e ataques Man-in-the-Middle

O ataque clássico relacionado a esse problema é o:

**Man-in-the-Middle (MITM)**.

Visualmente:

```text
Cliente
   │
   │ pensa que está falando com servidor
   ▼
Atacante
   │
   │ fala com servidor real
   ▼
Servidor
```

Sem autenticação adequada, o atacante pode tentar interceptar e intermediar a comunicação.

Com TLS corretamente validado:

```text
Cliente
   │
   │ verifica certificado
   ▼
Servidor legítimo
```

O atacante não consegue simplesmente apresentar qualquer certificado e ser aceito como o servidor legítimo.

---

## 25.26 Certificados autoassinados

Um certificado também pode ser **autoassinado**.

Nesse caso:

```text
Certificado
    │
    └── assinado pela própria chave associada
```

Isso não significa automaticamente:

```text
"certificado malicioso"
```

Certificados autoassinados podem ser úteis em:

- laboratórios;
    
- desenvolvimento;
    
- ambientes internos;
    
- testes;
    
- infraestrutura controlada.
    

O problema é que o cliente precisa ter uma forma explícita de confiar nessa autoridade/certificado.

Por isso, ao utilizar um certificado autoassinado em um ambiente de teste, podemos configurar o cliente para confiar explicitamente nele.

---

## 25.27 Desenvolvimento versus produção

Em laboratório, podemos utilizar:

```text
certificado autoassinado
```

Mas em produção precisamos pensar em:

```text
certificado válido
        +
cadeia de confiança
        +
hostname correto
        +
chave privada protegida
        +
renovação
        +
configuração TLS adequada
```

Não devemos transformar uma configuração de laboratório em configuração de produção simplesmente copiando o código.

---

## 25.28 TLS e portas

TLS não possui necessariamente uma porta própria.

Por exemplo:

```text
HTTPS → normalmente TCP 443
```

Mas podemos utilizar TLS sobre outras portas.

Por exemplo:

```text
Aplicação própria
      │
      ▼
TLS
      │
      ▼
TCP 4444
```

O número da porta é apenas parte do endereçamento.

O que importa é a configuração do protocolo em cada lado.

---

## 25.29 TLS sobre nosso servidor TCP

Podemos atualizar o modelo que estudamos anteriormente:

### TCP simples

```text
Servidor
   │
socket()
   │
bind()
   │
listen()
   │
accept()
   │
recv()/send()
```

### TCP + TLS

```text
Servidor
   │
socket()
   │
bind()
   │
listen()
   │
TLS
   │
handshake
   │
recv()/send()
```

A aplicação continua trabalhando com dados, mas agora existe uma camada criptográfica entre a aplicação e o transporte.

---

## 25.30 Arquitetura completa

Podemos finalmente juntar vários conceitos estudados:

```text
┌─────────────────────────────────────┐
│ Aplicação                           │
│ protocolo / autenticação / comandos │
├─────────────────────────────────────┤
│ TLS                                 │
│ criptografia / integridade / auth  │
├─────────────────────────────────────┤
│ TCP                                 │
│ stream confiável de bytes           │
├─────────────────────────────────────┤
│ IP                                  │
│ endereçamento e roteamento          │
├─────────────────────────────────────┤
│ Rede                                │
└─────────────────────────────────────┘
```

E no lado do servidor:

```text
Aplicação
    │
    ▼
TLS
    │
    ▼
Socket TCP
    │
    ▼
Kernel
    │
    ▼
TCP/IP
    │
    ▼
Rede
```

---

## 25.31 O que realmente precisamos validar?

Ao construir um cliente TLS, podemos pensar em uma lista mental:

```text
[✓] Certificado apresentado
[✓] Cadeia de confiança
[✓] CA confiável
[✓] Certificado dentro da validade
[✓] Hostname corresponde
[✓] Versão TLS adequada
[✓] Configuração criptográfica adequada
[✓] Chave privada protegida no servidor
```

Essa mentalidade é muito mais importante do que simplesmente memorizar funções da biblioteca.

---

## 25.32 Resumo da Parte

- Certificados digitais associam uma identidade a uma chave pública.
    
- A **CA** é responsável por assinar/emitir certificados dentro de uma cadeia de confiança.
    
- O cliente possui uma base de autoridades confiáveis chamada **trust store**.
    
- A validação envolve mais do que verificar se uma CA assinou o certificado.
    
- Também precisamos verificar:
    
    - validade temporal;
        
    - cadeia de confiança;
        
    - hostname;
        
    - assinatura;
        
    - políticas relevantes.
        
- O **SAN (Subject Alternative Name)** contém os nomes para os quais o certificado é válido.
    
- Certificados podem conter vários nomes.
    
- Wildcards possuem regras específicas de correspondência.
    
- A chave privada do servidor deve ser protegida.
    
- Certificado e chave privada são coisas diferentes.
    
- `ssl.create_default_context()` fornece uma configuração apropriada para clientes em cenários comuns.
    
- `ssl.SSLCertVerificationError` pode ocorrer quando a validação do certificado falha.
    
- `getpeercert()` permite consultar informações do certificado apresentado pelo peer.
    
- `version()` permite consultar a versão TLS negociada.
    
- `cipher()` permite consultar informações sobre o cipher suite utilizado.
    
- TLS utiliza criptografia assimétrica e simétrica em diferentes partes do processo.
    
- Mecanismos de estabelecimento de chaves podem fornecer **Forward Secrecy**.
    
- Desabilitar `CERT_NONE` e `check_hostname` pode remover proteções fundamentais.
    
- TLS corretamente configurado ajuda a impedir ataques **Man-in-the-Middle**.
    
- Certificados autoassinados podem ser úteis em laboratórios, mas exigem uma configuração explícita de confiança.
    
- TLS protege o canal, mas não corrige vulnerabilidades da aplicação.
    

**Modelo mental final:**

```text
Cliente
   │
   │ 1. conecta
   ▼
Servidor
   │
   │ 2. apresenta certificado
   ▼
Cliente
   │
   ├── CA confiável?
   ├── cadeia válida?
   ├── certificado dentro da validade?
   ├── hostname correto?
   └── assinatura válida?
          │
          ▼
     identidade validada
          │
          ▼
    sessão TLS protegida
          │
          ▼
    protocolo da aplicação
```

---

# 26. IPv6 com sockets

## 26.1 O que é IPv6?

**IPv6 (Internet Protocol version 6)** é a versão mais recente do protocolo IP, criada principalmente para solucionar a limitação de endereços do IPv4.

No IPv4, um endereço possui **32 bits**, permitindo aproximadamente 4,3 bilhões de endereços.

No IPv6, um endereço possui **128 bits**, permitindo uma quantidade extremamente maior de endereços.

Exemplo de IPv4:

```text
192.168.1.10
```

Exemplo de IPv6:

```text
2001:db8::10
```

O funcionamento de sockets continua seguindo a mesma ideia:

```text
Aplicação
    ↓
socket()
    ↓
TCP / UDP
    ↓
IPv4 ou IPv6
    ↓
Rede
```

A principal diferença está na **família de endereços** utilizada pelo socket.

No IPv4:

```python
socket.AF_INET
```

No IPv6:

```python
socket.AF_INET6
```

---

## 26.2 `AF_INET6`

Para criar um socket IPv6, utilizamos `AF_INET6`.

Exemplo TCP:

```python
import socket

server = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
```

Aqui:

```text
AF_INET6
   ↓
IPv6

SOCK_STREAM
   ↓
TCP
```

Portanto:

```python
socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
```

significa:

> Crie um socket TCP que utilize IPv6.

Para UDP:

```python
socket.socket(socket.AF_INET6, socket.SOCK_DGRAM)
```

---

## 26.3 Endereço de loopback IPv6

No IPv4, utilizamos:

```text
127.0.0.1
```

para representar o próprio computador.

No IPv6, o equivalente é:

```text
::1
```

Portanto:

```text
IPv4:
127.0.0.1

IPv6:
::1
```

Exemplo:

```python
server.bind(("::1", 4444))
```

Isso significa:

> Escute a porta `4444` somente através do endereço de loopback IPv6.

Assim como:

```python
server.bind(("127.0.0.1", 4444))
```

limita o servidor ao loopback IPv4.

---

## 26.4 O endereço `::`

Outro endereço importante é:

```text
::
```

Ele é o endereço IPv6 **não especificado**.

Em um `bind()`, ele pode ser utilizado para indicar que o socket deve escutar nas interfaces IPv6 disponíveis.

Exemplo:

```python
server.bind(("::", 4444))
```

Isso possui uma ideia semelhante ao:

```python
server.bind(("0.0.0.0", 4444))
```

no IPv4.

A diferença é a família:

```text
0.0.0.0
    ↓
IPv4

::
    ↓
IPv6
```

### Atenção

Assim como `0.0.0.0`, utilizar `::` **não significa "somente localhost"**.

Dependendo da configuração da máquina, firewall e interfaces disponíveis, o serviço poderá ficar acessível pela rede.

Para um servidor que deve ser apenas local, é mais restritivo utilizar:

```python
server.bind(("::1", 4444))
```

---

## 26.5 Estrutura do endereço de um socket IPv6

No IPv4, normalmente trabalhamos com:

```python
("127.0.0.1", 4444)
```

No IPv6, o endereço normalmente é representado por uma tupla de quatro elementos:

```python
("::1", 4444, 0, 0)
```

A estrutura é:

```text
(host, port, flowinfo, scopeid)
```

|Campo|Tipo|Função|
|---|---|---|
|`host`|`str`|Endereço IPv6|
|`port`|`int`|Porta|
|`flowinfo`|`int`|Informação de fluxo IPv6|
|`scopeid`|`int`|Identificador de escopo/interface|

Para operações comuns com IPv6, normalmente utilizamos apenas:

```python
("::1", 4444)
```

e o Python trata os demais campos conforme necessário.

Por exemplo:

```python
server.bind(("::1", 4444))
```

---

## 26.6 Servidor TCP IPv6

Um servidor TCP IPv6 básico pode ser construído assim:

```python
import socket

server = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)

server.bind(("::1", 4444))
server.listen()

print("Servidor aguardando conexão...")

client, address = server.accept()

print(f"Cliente conectado: {address}")

data = client.recv(1024)

print(f"Recebido: {data.decode()}")

client.sendall(b"Mensagem recebida!")

client.close()
server.close()
```

O fluxo continua sendo o mesmo que vimos com IPv4:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
   ↓
recv()
   ↓
sendall()
   ↓
close()
```

A principal diferença está na família:

```python
socket.AF_INET6
```

e no endereço:

```python
"::1"
```

---

## 26.7 Cliente TCP IPv6

O cliente correspondente:

```python
import socket

client = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)

client.connect(("::1", 4444))

client.sendall(b"Ola, servidor IPv6!")

response = client.recv(1024)

print(response.decode())

client.close()
```

Temos:

```text
CLIENTE
   |
   | connect(("::1", 4444))
   ↓
SERVIDOR
   |
   | bind(("::1", 4444))
   ↓
TCP + IPv6
```

---

## 26.8 IPv4 x IPv6

|Característica|IPv4|IPv6|
|---|---|---|
|Família|`AF_INET`|`AF_INET6`|
|Tamanho|32 bits|128 bits|
|Loopback|`127.0.0.1`|`::1`|
|Não especificado|`0.0.0.0`|`::`|
|Exemplo|`192.168.1.10`|`2001:db8::10`|
|TCP|`SOCK_STREAM`|`SOCK_STREAM`|
|UDP|`SOCK_DGRAM`|`SOCK_DGRAM`|

O protocolo de transporte não muda simplesmente porque estamos usando IPv6.

Podemos ter:

```text
IPv4 + TCP
IPv4 + UDP

IPv6 + TCP
IPv6 + UDP
```

---

## 26.9 `getaddrinfo()` e IPv6

Uma das formas mais importantes de escrever código que pode trabalhar com IPv4 e IPv6 é utilizar:

```python
socket.getaddrinfo()
```

Por exemplo:

```python
import socket

results = socket.getaddrinfo(
    "localhost",
    4444,
    socket.AF_UNSPEC,
    socket.SOCK_STREAM
)

for result in results:
    print(result)
```

O:

```python
socket.AF_UNSPEC
```

significa:

> Não restrinja a resolução a uma família específica de endereços.

Assim, o sistema pode retornar informações para IPv4, IPv6 ou ambos, dependendo da configuração e resolução daquele nome.

Isso é geralmente mais flexível do que assumir:

```python
AF_INET
```

ou:

```python
AF_INET6
```

sem necessidade.

---

## 26.10 Utilizando o resultado de `getaddrinfo()`

Cada resultado possui informações suficientes para criar o socket apropriado.

Exemplo:

```python
import socket

results = socket.getaddrinfo(
    "localhost",
    4444,
    socket.AF_UNSPEC,
    socket.SOCK_STREAM
)

for family, socktype, proto, canonname, sockaddr in results:
    print("Família:", family)
    print("Tipo:", socktype)
    print("Protocolo:", proto)
    print("Endereço:", sockaddr)
```

Podemos utilizar essas informações para tentar uma conexão:

```python
import socket

results = socket.getaddrinfo(
    "localhost",
    4444,
    socket.AF_UNSPEC,
    socket.SOCK_STREAM
)

for family, socktype, proto, canonname, sockaddr in results:
    try:
        client = socket.socket(family, socktype, proto)
        client.connect(sockaddr)

        print("Conectado!")
        break

    except OSError:
        client.close()
```

A ideia é:

```text
hostname
    ↓
getaddrinfo()
    ↓
endereços disponíveis
    ↓
família + tipo + protocolo
    ↓
socket apropriado
    ↓
connect()
```

Isso é especialmente útil em aplicações que precisam funcionar em ambientes IPv4 e IPv6.

---

## 26.11 `IPV6_V6ONLY`

Um detalhe importante de IPv6 é a opção:

```python
socket.IPV6_V6ONLY
```

Ela controla se um socket IPv6 pode também aceitar conexões IPv4 mapeadas para IPv6.

Por exemplo:

```python
server.setsockopt(
    socket.IPPROTO_IPV6,
    socket.IPV6_V6ONLY,
    1
)
```

Com:

```text
1
```

o socket fica somente IPv6.

Com:

```text
0
```

o comportamento pode permitir IPv4-mapped addresses em algumas plataformas.

Porém, **o comportamento padrão não é universal**. Ele depende do sistema operacional e da configuração.

Por isso, não devemos assumir que:

```python
AF_INET6
```

automaticamente significa:

```text
IPv4 + IPv6
```

ou:

```text
somente IPv6
```

O comportamento deve ser tratado explicitamente quando isso for importante para a aplicação.

---

## 26.12 Endereços IPv6 link-local e `scopeid`

Existem endereços IPv6 que possuem escopo local à interface, principalmente os endereços **link-local**, normalmente dentro de:

```text
fe80::/10
```

Um exemplo poderia aparecer como:

```text
fe80::1234:5678:abcd:ef01
```

Esses endereços podem existir simultaneamente em várias interfaces.

Por isso, pode ser necessário informar qual interface deve ser utilizada através do `scopeid`.

A estrutura:

```text
(host, port, flowinfo, scopeid)
```

permite representar essa informação.

Em situações comuns de:

```python
::1
```

ou endereços globais, normalmente não precisamos lidar manualmente com `scopeid`.

---

## 26.13 Tipos importantes de endereços IPv6

Algumas categorias importantes:

### Loopback

```text
::1
```

Representa o próprio computador.

---

### Unspecified

```text
::
```

Representa um endereço não especificado.

É especialmente importante em operações como `bind()`.

---

### Link-local

```text
fe80::/10
```

Utilizado para comunicação no enlace local.

---

### Multicast

```text
ff00::/8
```

Utilizado para comunicação multicast.

IPv6 não utiliza broadcast da mesma maneira que IPv4; muitos mecanismos que seriam associados a broadcast no IPv4 utilizam multicast no IPv6.

---

### Global unicast

São endereços utilizados para comunicação IPv6 roteável globalmente.

Um exemplo de documentação:

```text
2001:db8::1
```

O prefixo `2001:db8::/32` é reservado para documentação e exemplos.

---

## 26.14 Biblioteca `ipaddress`

O Python também possui a biblioteca:

```python
ipaddress
```

que permite trabalhar com endereços e redes IP sem precisar tratar tudo manualmente como strings.

Exemplo:

```python
import ipaddress

address = ipaddress.ip_address("2001:db8::1")

print(address)
print(address.version)
```

Resultado conceitual:

```text
2001:db8::1
6
```

Também podemos verificar se um endereço é IPv6:

```python
import ipaddress

address = ipaddress.ip_address("::1")

print(address.version)
```

Resultado:

```text
6
```

Para IPv4:

```python
address = ipaddress.ip_address("127.0.0.1")

print(address.version)
```

Resultado:

```text
4
```

Essa biblioteca é útil quando a aplicação precisa **validar, comparar ou manipular endereços e redes IP**.

---

## 26.15 Erros comuns com IPv6

### 1. Usar `AF_INET` com endereço IPv6

Exemplo incorreto:

```python
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("::1", 4444))
```

O socket foi criado para IPv4, mas o endereço é IPv6.

Isso pode resultar em erro de família de endereços.

O correto é:

```python
server = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)

server.bind(("::1", 4444))
```

---

### 2. Usar o formato IPv4 para IPv6

IPv4:

```python
("127.0.0.1", 4444)
```

IPv6:

```python
("::1", 4444)
```

Não devemos tentar colocar:

```text
[::1]:4444
```

como string de host dentro do `bind()`.

Esse formato com colchetes é comum na representação de IPv6 em URLs, mas não é o formato utilizado como host na tupla de endereço do socket.

---

### 3. IPv6 desabilitado ou indisponível

Uma máquina pode possuir IPv6 desabilitado ou configurado de maneira diferente.

Nesse caso, operações como:

```python
connect(("::1", 4444))
```

podem falhar.

Um erro comum pode ser:

```text
ConnectionRefusedError
```

quando não existe nenhum serviço escutando naquela porta.

Ou erros relacionados à resolução/família de endereço podem ocorrer dependendo da operação.

---

## 26.16 IPv6 e exposição de serviços

Um erro importante em segurança é assumir:

```python
bind(("::", 4444))
```

como sendo apenas local.

Não é.

A ideia é semelhante a:

```python
bind(("0.0.0.0", 4444))
```

no IPv4.

Quando um servidor é destinado somente para testes locais, podemos utilizar:

```python
bind(("127.0.0.1", 4444))
```

ou:

```python
bind(("::1", 4444))
```

Dependendo da aplicação, também pode ser necessário considerar que o serviço está disponível em **uma ou ambas as famílias**.

Por isso, ao analisar um serviço de rede, não basta verificar apenas:

```text
IPv4
```

Também devemos verificar:

```text
IPv6
```

Por exemplo:

```bash
ss -lnt
```

pode mostrar sockets IPv4 e IPv6.

---

## 26.17 Modelo mental

Podemos visualizar IPv4 e IPv6 assim:

```text
                    SOCKET
                       │
             ┌─────────┴─────────┐
             │                   │
          AF_INET             AF_INET6
             │                   │
           IPv4                 IPv6
             │                   │
       127.0.0.1                ::1
             │                   │
             └─────────┬─────────┘
                       │
                     TCP
                       │
                    Rede
```

A família do socket determina **como o endereço de rede será representado e utilizado**.

O protocolo de transporte continua separado:

```text
AF_INET  + SOCK_STREAM
    ↓
IPv4 + TCP

AF_INET6 + SOCK_STREAM
    ↓
IPv6 + TCP

AF_INET  + SOCK_DGRAM
    ↓
IPv4 + UDP

AF_INET6 + SOCK_DGRAM
    ↓
IPv6 + UDP
```

---

## 26.18 Resumo da Parte

- `AF_INET` representa IPv4.
    
- `AF_INET6` representa IPv6.
    
- IPv4 possui endereços de 32 bits.
    
- IPv6 possui endereços de 128 bits.
    
- O loopback IPv4 é `127.0.0.1`.
    
- O loopback IPv6 é `::1`.
    
- `0.0.0.0` e `::` representam endereços não especificados para suas respectivas famílias.
    
- Um socket IPv6 pode ser criado com:
    

```python
socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
```

- Endereços IPv6 em sockets normalmente possuem a estrutura:
    

```text
(host, port, flowinfo, scopeid)
```

- `getaddrinfo()` é uma ferramenta importante para trabalhar com IPv4 e IPv6 de maneira mais flexível.
    
- `IPV6_V6ONLY` controla um aspecto importante da coexistência entre IPv6 e IPv4, mas seu comportamento padrão pode variar entre sistemas.
    
- Endereços link-local podem exigir `scopeid`.
    
- `ipaddress` ajuda a validar e manipular endereços IP.
    
- `::` pode expor um serviço além do localhost, dependendo da configuração.
    
- IPv6 não altera o conceito fundamental de sockets:
    

```text
socket → endereço → conexão/comunicação → dados → fechamento
```

---

# 27. Unix Sockets (`AF_UNIX`)

## 27.1 O que são Unix Sockets?

Até agora, trabalhamos principalmente com sockets de rede:

```python
socket.AF_INET
```

e:

```python
socket.AF_INET6
```

Esses sockets são utilizados para comunicação através de **endereços IP**.

Porém, nem toda comunicação precisa passar por uma rede.

Quando dois processos estão rodando **na mesma máquina**, podemos utilizar **Unix Domain Sockets**, representados em Python por:

```python
socket.AF_UNIX
```

Eles permitem que processos locais se comuniquem utilizando um endereço associado normalmente a um **arquivo no sistema de arquivos**.

A ideia é:

```text
Processo A
    │
    │ Unix Socket
    ↓
Sistema Operacional
    ↓
Unix Socket
    │
    │
Processo B
```

Não precisamos utilizar:

```text
IP
porta TCP
roteamento IP
```

para estabelecer essa comunicação.

---

## 27.2 Unix Socket vs Socket de rede

Podemos comparar:

```text
TCP/IPv4:

Processo
   ↓
Socket
   ↓
TCP
   ↓
IPv4
   ↓
Interface de rede
   ↓
Rede
   ↓
Destino
```

Com:

```text
Unix Socket:

Processo
   ↓
Socket
   ↓
Kernel
   ↓
Unix Domain Socket
   ↓
Outro processo local
```

Isso torna Unix sockets especialmente interessantes para **comunicação entre processos na mesma máquina (IPC — Inter-Process Communication)**.

---

## 27.3 Criando um Unix Socket

Um socket Unix pode ser criado assim:

```python
import socket

server = socket.socket(
    socket.AF_UNIX,
    socket.SOCK_STREAM
)
```

Observe a diferença:

```text
IPv4:
AF_INET

IPv6:
AF_INET6

Unix:
AF_UNIX
```

Podemos combinar `AF_UNIX` com diferentes tipos de socket, como:

```python
socket.SOCK_STREAM
```

ou:

```python
socket.SOCK_DGRAM
```

Por exemplo:

```python
server = socket.socket(
    socket.AF_UNIX,
    socket.SOCK_STREAM
)
```

significa:

> Criar um Unix socket orientado a fluxo.

---

## 27.4 O endereço de um Unix Socket

Em um socket TCP, utilizamos algo como:

```python
server.bind(("127.0.0.1", 4444))
```

Existe:

```text
IP + porta
```

No Unix socket, normalmente utilizamos um **caminho do sistema de arquivos**:

```python
server.bind("/tmp/meu_socket.sock")
```

Por exemplo:

```text
/tmp/meu_socket.sock
```

Esse caminho identifica o socket local.

Portanto:

```text
TCP:

127.0.0.1:4444


Unix Socket:

/tmp/meu_socket.sock
```

---

## 27.5 Criando um servidor Unix Socket

Um servidor básico:

```python
import socket

socket_path = "/tmp/meu_socket.sock"

server = socket.socket(
    socket.AF_UNIX,
    socket.SOCK_STREAM
)

server.bind(socket_path)
server.listen()

print("Aguardando conexão...")

client, address = server.accept()

data = client.recv(1024)

print(f"Recebido: {data.decode()}")

client.sendall(b"Mensagem recebida!")

client.close()
server.close()
```

O fluxo é muito parecido com TCP:

```text
socket()
   ↓
bind()
   ↓
listen()
   ↓
accept()
   ↓
recv()
   ↓
sendall()
   ↓
close()
```

A principal diferença está no endereço.

No TCP:

```python
("127.0.0.1", 4444)
```

No Unix socket:

```python
"/tmp/meu_socket.sock"
```

---

## 27.6 O arquivo `.sock`

Quando fazemos:

```python
server.bind("/tmp/meu_socket.sock")
```

o sistema pode criar uma entrada no sistema de arquivos representando o Unix socket.

Podemos verificar:

```bash
ls -l /tmp/meu_socket.sock
```

Ele não é um arquivo comum contendo os dados da comunicação.

Esse ponto é importante.

O caminho serve para **identificar e localizar o endpoint do socket**.

Os dados continuam sendo tratados pelo kernel e pelo mecanismo de comunicação do Unix socket.

Podemos pensar:

```text
/tmp/meu_socket.sock
        │
        │ identifica
        ↓
Unix Domain Socket
        │
        ↓
Kernel
        │
        ↓
Outro processo
```

---

## 27.7 O cliente Unix Socket

O cliente também utiliza `AF_UNIX`:

```python
import socket

client = socket.socket(
    socket.AF_UNIX,
    socket.SOCK_STREAM
)

client.connect("/tmp/meu_socket.sock")

client.sendall(b"Ola, servidor!")

response = client.recv(1024)

print(response.decode())

client.close()
```

O fluxo é:

```text
Cliente
   │
   │ connect()
   ↓
/tmp/meu_socket.sock
   │
   ↓
Servidor
   │
   │ accept()
   ↓
Socket de comunicação
```

---

## 27.8 Servidor e cliente completos

### Servidor

```python
import socket
import os

socket_path = "/tmp/meu_socket.sock"

if os.path.exists(socket_path):
    os.remove(socket_path)

server = socket.socket(
    socket.AF_UNIX,
    socket.SOCK_STREAM
)

server.bind(socket_path)
server.listen()

print("Servidor aguardando conexão...")

client, _ = server.accept()

data = client.recv(1024)

print(f"Recebido: {data.decode()}")

client.sendall(b"Resposta do servidor")

client.close()
server.close()

os.remove(socket_path)
```

### Cliente

```python
import socket

socket_path = "/tmp/meu_socket.sock"

client = socket.socket(
    socket.AF_UNIX,
    socket.SOCK_STREAM
)

client.connect(socket_path)

client.sendall(b"Ola do cliente!")

response = client.recv(1024)

print(response.decode())

client.close()
```

A ordem de execução é:

```text
1. Iniciar servidor
2. Servidor cria /tmp/meu_socket.sock
3. Servidor executa listen()
4. Cliente executa connect()
5. Servidor executa accept()
6. Cliente envia dados
7. Servidor recebe
8. Servidor responde
9. Cliente recebe
10. Ambos fecham
```

---

## 27.9 Por que remover o socket antes de `bind()`?

Existe uma diferença importante em relação ao TCP.

Quando fazemos:

```python
server.bind("/tmp/meu_socket.sock")
```

o caminho pode já existir devido a uma execução anterior.

Por exemplo, o programa pode ter sido encerrado sem remover corretamente o socket.

Então uma nova tentativa de:

```python
bind()
```

pode falhar.

Por isso é comum encontrar:

```python
import os

if os.path.exists(socket_path):
    os.remove(socket_path)
```

antes do `bind()`.

Porém, existe um cuidado importante:

**não devemos remover cegamente qualquer arquivo existente naquele caminho.**

Em uma aplicação real, devemos garantir que o caminho pertence ao socket que nossa aplicação pretende utilizar.

---

## 27.10 Unix Socket e permissões

Uma vantagem importante dos Unix sockets é que eles podem utilizar as **permissões do sistema de arquivos**.

Por exemplo:

```bash
ls -l /tmp/meu_socket.sock
```

pode mostrar algo semelhante a:

```text
srwxr-xr-x 1 usuario usuario ... /tmp/meu_socket.sock
```

O primeiro caractere:

```text
s
```

indica que se trata de um **socket**.

As permissões:

```text
rwxr-xr-x
```

podem participar do controle de acesso ao socket.

Isso permite que o sistema operacional controle quais usuários/processos podem acessar aquele endpoint.

---

## 27.11 Segurança: Unix Socket não significa automaticamente "seguro"

É errado pensar:

> "Se não existe IP, então não existe problema de segurança."

Unix sockets ainda precisam de controle de acesso.

Por exemplo:

```text
Aplicação A
     │
     │ acesso permitido
     ↓
/tmp/app.sock
     ↑
     │
Aplicação B
```

Se as permissões forem configuradas incorretamente, outro processo local pode conseguir se conectar.

Portanto, devemos considerar:

- permissões;
    
- proprietário;
    
- grupo;
    
- diretório onde o socket está;
    
- autenticação da aplicação;
    
- autorização;
    
- usuários que podem acessar o endpoint.
    

---

## 27.12 Onde colocar o Unix Socket?

Um socket pode ficar em diferentes locais, mas devemos considerar as permissões e o ciclo de vida.

Exemplo:

```text
/tmp/meu_socket.sock
```

é conveniente para testes.

Aplicações de sistema podem utilizar locais específicos, como:

```text
/run/
```

ou diretórios próprios da aplicação.

Em ambientes Linux, serviços frequentemente utilizam Unix sockets para disponibilizar APIs locais.

---

## 27.13 Unix Socket e APIs locais

Um caso muito importante é utilizar Unix sockets como interface de comunicação entre:

```text
Aplicação
     ↓
Unix Socket
     ↓
Serviço local
```

Por exemplo:

```text
Aplicação web
      ↓
Unix Socket
      ↓
Servidor de aplicação
```

Isso permite que dois processos locais se comuniquem sem precisar abrir uma porta TCP acessível pela rede.

Esse padrão aparece em diversos softwares e serviços Linux.

---

## 27.14 Unix Socket com `SOCK_DGRAM`

Unix sockets também podem utilizar datagramas.

Exemplo:

```python
server = socket.socket(
    socket.AF_UNIX,
    socket.SOCK_DGRAM
)
```

Nesse caso, o modelo é baseado em datagramas em vez de stream.

O servidor pode utilizar:

```python
data, address = server.recvfrom(1024)
```

e enviar:

```python
server.sendto(data, address)
```

A ideia é semelhante ao UDP:

```text
SOCK_STREAM
    ↓
fluxo de bytes

SOCK_DGRAM
    ↓
datagramas
```

Porém, estamos falando de **Unix Domain Sockets**, e não de UDP/IP.

---

## 27.15 Unix Socket vs TCP localhost

Uma dúvida comum é:

> "Se ambos os processos estão na mesma máquina, por que não usar `127.0.0.1`?"

Podemos usar TCP localhost:

```text
127.0.0.1:4444
```

mas um Unix socket pode ser mais apropriado quando a comunicação é estritamente local.

Comparação:

|Característica|TCP localhost|Unix Socket|
|---|---|---|
|Família|`AF_INET`|`AF_UNIX`|
|Endereço|IP + porta|Caminho/socket local|
|Rede IP|Sim|Não|
|Comunicação remota|Possível|Não|
|Permissões do filesystem|Não diretamente|Sim|
|Uso típico|Serviços TCP|IPC local|
|Identificação|IP + porta|Path/socket|

---

## 27.16 Unix Socket não possui porta TCP

Isso é importante para diagnóstico.

Se temos:

```text
/tmp/meu_socket.sock
```

não devemos procurar:

```bash
ss -lnt | grep 4444
```

esperando encontrar esse socket como uma porta TCP.

Ele pertence a outra família:

```text
AF_UNIX
```

Podemos utilizar ferramentas como:

```bash
ss -lx
```

para listar Unix sockets.

Por exemplo:

```bash
ss -lx
```

pode mostrar caminhos de sockets Unix em escuta.

---

## 27.17 Unix Socket abstrato no Linux

No Linux existe ainda um recurso interessante chamado **abstract namespace** para Unix sockets.

Em vez de utilizar um caminho real no filesystem, o endereço pode existir apenas dentro do namespace de sockets do kernel.

Em Python, isso pode ser representado utilizando um endereço iniciado por `\0`.

Exemplo conceitual:

```python
socket_path = "\0meu_socket"
```

e:

```python
server.bind(socket_path)
```

Nesse caso:

```text
não existe:
/tmp/meu_socket.sock

existe:
um endereço mantido pelo kernel
```

Isso é específico do comportamento de sistemas Unix/Linux e não deve ser tratado como equivalente portátil a um pathname tradicional.

Para aplicações portáveis, o caminho tradicional é geralmente mais simples.

---

## 27.18 Limitações dos Unix Sockets

Unix sockets são excelentes para comunicação local, mas possuem limitações.

### Não são para comunicação remota

Não podemos fazer:

```text
Computador A
     ↓
/tmp/app.sock
     ↓
Computador B
```

O caminho pertence ao sistema local.

Para comunicação entre máquinas, normalmente utilizamos:

```text
IPv4
```

ou:

```text
IPv6
```

---

### Dependência do sistema operacional

`AF_UNIX` é associado ao modelo Unix/POSIX e seu suporte e recursos específicos variam entre sistemas operacionais.

Portanto, código baseado em Unix sockets não deve ser presumido como igualmente portátil em todos os ambientes.

---

### Gerenciamento do arquivo/socket

É necessário considerar:

- criação;
    
- permissões;
    
- existência anterior;
    
- remoção após encerramento;
    
- diretório utilizado;
    
- possíveis sockets abandonados.
    

---

## 27.19 Modelo mental

Agora temos três famílias importantes:

```text
                    SOCKET
                       │
        ┌──────────────┼──────────────┐
        │              │              │
     AF_INET        AF_INET6        AF_UNIX
        │              │              │
      IPv4           IPv6        Comunicação local
        │              │              │
  IP + porta       IP + porta     path/socket
```

E podemos pensar em:

```text
AF_INET
    ↓
Comunicação baseada em IPv4


AF_INET6
    ↓
Comunicação baseada em IPv6


AF_UNIX
    ↓
Comunicação entre processos locais
```

---

## 27.20 Resumo da Parte

- `AF_UNIX` representa Unix Domain Sockets.
    
- Unix sockets são utilizados principalmente para **comunicação entre processos locais**.
    
- Diferentemente de TCP/IP, não dependem de um endereço IP.
    
- Um Unix socket tradicional pode ser identificado por um caminho, como:
    

```text
/tmp/meu_socket.sock
```

- Um servidor pode seguir:
    

```python
socket()
bind()
listen()
accept()
recv()
sendall()
close()
```

- Um cliente pode seguir:
    

```python
socket()
connect()
sendall()
recv()
close()
```

- `SOCK_STREAM` fornece comunicação orientada a fluxo.
    
- `SOCK_DGRAM` fornece comunicação baseada em datagramas.
    
- Unix sockets podem utilizar permissões do sistema de arquivos como parte do controle de acesso.
    
- A ausência de uma porta TCP não significa ausência de riscos de segurança.
    
- `ss -lx` pode ser utilizado para visualizar Unix sockets.
    
- Linux também possui o **abstract namespace** para Unix sockets.
    
- Unix sockets são apropriados quando a comunicação precisa permanecer na mesma máquina.
    
- Para comunicação entre máquinas, normalmente utilizamos IPv4 ou IPv6.
    

O modelo geral agora fica:

```text
                 SOCKETS
                    │
       ┌────────────┼────────────┐
       │            │            │
    AF_INET      AF_INET6     AF_UNIX
       │            │            │
     IPv4         IPv6       IPC local
       │            │            │
   IP + porta   IP + porta    socket/path
```

---

# 28. Broadcast e Multicast

## 28.1 O que são Broadcast e Multicast?

Até agora, a maioria dos exemplos utilizou comunicação **um para um**:

```text
Cliente ─────────→ Servidor
```

Esse modelo é chamado de **unicast**.

Porém, existem situações em que um processo precisa enviar dados para vários destinos.

Podemos ter:

```text
          ┌──→ Cliente A
Servidor ─┼──→ Cliente B
          └──→ Cliente C
```

Existem diferentes formas de fazer isso.

As três categorias principais são:

```text
Unicast
   ↓
um remetente → um destino

Broadcast
   ↓
um remetente → vários destinos dentro de um domínio de broadcast

Multicast
   ↓
um remetente → grupo específico de destinatários
```

Broadcast e multicast não são simplesmente "um `send()` para vários sockets". Eles possuem mecanismos específicos na camada de rede e requisitos próprios.

---

## 28.2 Unicast

Antes de entender broadcast e multicast, precisamos consolidar o modelo tradicional.

Em uma comunicação unicast:

```text
Cliente A
    │
    │ dados
    ↓
Servidor
```

Existe um remetente e um destino específico.

Exemplo:

```python
client.sendall(b"Olá")
```

O socket está conectado a um destino específico.

Em termos conceituais:

```text
A ─────────→ B
```

---

## 28.3 Broadcast

Broadcast significa enviar um pacote para **todos os hosts alcançáveis dentro de determinado domínio de broadcast**.

No IPv4, um exemplo clássico é:

```text
192.168.1.255
```

para uma rede:

```text
192.168.1.0/24
```

Nesse caso:

```text
              ┌──→ Host A
              │
Host emissor ─┼──→ Host B
              │
              ├──→ Host C
              │
              └──→ Host D
```

O objetivo não é escolher um único host.

O pacote é destinado ao broadcast daquele domínio.

---

## 28.4 Broadcast não significa "Internet inteira"

Um erro comum é pensar:

> Broadcast envia para todos os computadores da Internet.

Não.

Broadcast IPv4 é limitado ao **domínio de broadcast local** e não é roteado normalmente através da Internet.

Por exemplo:

```text
Rede local
192.168.1.0/24
```

pode ter:

```text
192.168.1.255
```

como endereço de broadcast.

O roteador normalmente não encaminha esse broadcast para outras redes.

Portanto:

```text
Rede A
192.168.1.0/24
      │
      │ broadcast
      ↓
Hosts da própria rede
```

não significa:

```text
Internet inteira
```

---

## 28.5 Broadcast é principalmente associado ao IPv4

O IPv4 possui mecanismos explícitos de broadcast.

O IPv6 **não utiliza broadcast da mesma forma**.

Em IPv6, mecanismos que poderiam exigir broadcast no IPv4 normalmente utilizam **multicast**.

Por isso:

```text
IPv4
   └── Broadcast disponível

IPv6
   └── Multicast utilizado em situações equivalentes
```

Essa diferença é importante quando trabalhamos diretamente com sockets de rede.

---

## 28.6 `SO_BROADCAST`

Para enviar broadcast IPv4 usando um socket UDP, normalmente precisamos habilitar:

```python
socket.SO_BROADCAST
```

através de:

```python
setsockopt()
```

Exemplo:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_BROADCAST,
    1
)
```

Aqui:

```text
AF_INET
   ↓
IPv4

SOCK_DGRAM
   ↓
UDP

SO_BROADCAST
   ↓
Permite utilizar o socket para broadcast
```

---

## 28.7 Enviando um broadcast UDP

Depois de habilitar `SO_BROADCAST`, podemos enviar para um endereço de broadcast.

Exemplo:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_BROADCAST,
    1
)

sock.sendto(
    b"Mensagem de broadcast",
    ("192.168.1.255", 4444)
)

sock.close()
```

A estrutura é:

```text
socket()
   ↓
setsockopt(SO_BROADCAST)
   ↓
sendto()
   ↓
192.168.1.255:4444
```

Como estamos utilizando UDP:

```python
socket.SOCK_DGRAM
```

não existe uma conexão TCP tradicional.

---

## 28.8 Servidor UDP recebendo broadcast

Um socket UDP pode simplesmente fazer `bind()` na porta e receber o datagrama.

Exemplo:

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

server.bind(("0.0.0.0", 4444))

print("Aguardando broadcast...")

data, address = server.recvfrom(1024)

print(f"Mensagem: {data.decode()}")
print(f"Origem: {address}")

server.close()
```

O:

```python
("0.0.0.0", 4444)
```

significa que o socket está associado à porta `4444` nas interfaces IPv4 disponíveis.

Isso permite que ele receba datagramas destinados àquela porta, incluindo broadcasts recebidos pela máquina, desde que a rede e o sistema permitam.

---

## 28.9 Broadcast depende da configuração da rede

Um exemplo como:

```python
("192.168.1.255", 4444)
```

não deve ser tratado como um endereço universal de broadcast.

O endereço correto depende da **rede e da máscara**.

Por exemplo:

```text
Rede:
192.168.10.0/24

Broadcast:
192.168.10.255
```

Mas:

```text
Rede:
192.168.10.0/25
```

possui outro endereço de broadcast:

```text
192.168.10.127
```

Portanto, o broadcast depende da topologia e do prefixo da rede.

---

## 28.10 Broadcast limitado

Existe também o endereço:

```text
255.255.255.255
```

conhecido como **limited broadcast** no IPv4.

Ele representa um broadcast limitado ao domínio local apropriado.

Exemplo:

```python
sock.sendto(
    b"Mensagem",
    ("255.255.255.255", 4444)
)
```

Porém, seu funcionamento ainda depende do sistema operacional, interface e configuração da rede.

Não devemos assumir que qualquer rede permitirá esse envio.

---

## 28.11 Broadcast e segurança

Broadcast pode ser útil, mas possui consequências.

Se enviarmos:

```text
Mensagem
     ↓
Broadcast
     ↓
Todos os hosts do domínio
```

vários dispositivos podem receber o pacote mesmo que não tenham solicitado diretamente a comunicação.

Isso pode gerar:

- tráfego desnecessário;
    
- processamento em vários hosts;
    
- descoberta de dispositivos;
    
- exposição de informações;
    
- problemas de configuração;
    
- abuso em redes mal configuradas.
    

Por isso, protocolos que utilizam broadcast precisam definir cuidadosamente:

- formato da mensagem;
    
- porta;
    
- autenticação;
    
- validação;
    
- frequência de envio;
    
- tamanho dos pacotes.
    

---

# 28.12 Multicast

Multicast possui uma ideia diferente.

Em vez de enviar para:

```text
um destino
```

ou:

```text
todos os destinos
```

enviamos para um **grupo multicast**.

Imagine:

```text
             ┌──→ Cliente A
             │
Servidor ───→│──→ Cliente B
             │
             └──→ Cliente C
```

Mas somente os clientes que **participam daquele grupo** recebem os dados.

Podemos visualizar:

```text
             Grupo Multicast
             239.1.1.1
                  │
        ┌─────────┼─────────┐
        ↓         ↓         ↓
     Host A     Host B    Host C
```

Se o Host D não pertence ao grupo:

```text
Host D
   X
```

ele não participa daquela entrega multicast.

---

## 28.13 Endereços multicast IPv4

No IPv4, os endereços multicast pertencem ao intervalo:

```text
224.0.0.0/4
```

ou seja:

```text
224.0.0.0
até
239.255.255.255
```

Um endereço de exemplo:

```text
239.1.1.1
```

Pode ser utilizado como endereço multicast privado/administrativo em determinados contextos.

O conceito é:

```text
Servidor
   │
   │ envia para
   ↓
239.1.1.1
   │
   ├──→ Cliente A
   ├──→ Cliente B
   └──→ Cliente C
```

---

## 28.14 Multicast não é igual a broadcast

Essa diferença é fundamental.

### Broadcast

```text
Servidor
   │
   ↓
Broadcast
   │
   ├──→ Host A
   ├──→ Host B
   ├──→ Host C
   └──→ Host D
```

A intenção é alcançar todos os hosts daquele domínio de broadcast.

### Multicast

```text
Servidor
   │
   ↓
Grupo 239.1.1.1
   │
   ├──→ Host A
   ├──→ Host C
   └──→ Host F
```

Somente os membros interessados no grupo participam.

Portanto:

```text
Broadcast:
"todos"

Multicast:
"todos que participam deste grupo"
```

---

## 28.15 Entrando em um grupo multicast

Para receber multicast IPv4, o host normalmente precisa informar ao sistema operacional que deseja participar de determinado grupo.

Em Python, podemos utilizar:

```python
setsockopt()
```

com:

```python
IP_ADD_MEMBERSHIP
```

Exemplo:

```python
import socket
import struct

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)

sock.bind(("0.0.0.0", 4444))

group = socket.inet_aton("239.1.1.1")

membership = struct.pack(
    "4s4s",
    group,
    socket.inet_aton("0.0.0.0")
)

sock.setsockopt(
    socket.IPPROTO_IP,
    socket.IP_ADD_MEMBERSHIP,
    membership
)
```

Aqui existem várias etapas.

---

## 28.16 Entendendo `IP_ADD_MEMBERSHIP`

A parte:

```python
socket.IP_ADD_MEMBERSHIP
```

informa ao sistema operacional:

> Quero participar deste grupo multicast.

O grupo:

```python
"239.1.1.1"
```

é convertido para sua representação binária:

```python
socket.inet_aton("239.1.1.1")
```

Depois utilizamos:

```python
struct.pack()
```

para construir a estrutura esperada pela API de sockets.

Conceitualmente:

```text
"239.1.1.1"
     ↓
inet_aton()
     ↓
bytes do endereço IPv4
     ↓
struct.pack()
     ↓
estrutura de membership
     ↓
IP_ADD_MEMBERSHIP
```

---

## 28.17 Recebendo mensagens multicast

Depois de entrar no grupo, o socket pode utilizar:

```python
recvfrom()
```

normalmente:

```python
while True:
    data, address = sock.recvfrom(1024)

    print(
        f"{address}: "
        f"{data.decode()}"
    )
```

O fluxo completo é:

```text
Servidor
    │
    │ UDP
    ↓
239.1.1.1:4444
    │
    ├──→ Cliente A
    ├──→ Cliente B
    └──→ Cliente C
```

Desde que esses clientes tenham ingressado no grupo e a infraestrutura da rede permita o multicast.

---

## 28.18 Enviando multicast

O envio pode ser feito utilizando `sendto()`:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

sock.sendto(
    b"Mensagem multicast",
    ("239.1.1.1", 4444)
)

sock.close()
```

Não precisamos conectar o socket previamente para enviar o datagrama.

O destino é informado diretamente:

```python
sendto(data, address)
```

---

## 28.19 TTL do multicast

Multicast IPv4 possui um conceito importante chamado **TTL (Time To Live)**.

Podemos configurar:

```python
socket.IP_MULTICAST_TTL
```

Por exemplo:

```python
sock.setsockopt(
    socket.IPPROTO_IP,
    socket.IP_MULTICAST_TTL,
    1
)
```

Um TTL baixo pode limitar o alcance do multicast.

Isso é importante porque não queremos necessariamente que um multicast utilizado em uma rede local atravesse vários roteadores.

Podemos visualizar:

```text
TTL baixo
   ↓
Rede local

TTL maior
   ↓
Potencialmente mais roteadores
```

O alcance real também depende da infraestrutura e das políticas de roteamento multicast.

---

## 28.20 Broadcast vs Multicast vs Unicast

|Modelo|Destino|Exemplo|
|---|---|---|
|Unicast|Um host|`192.168.1.20`|
|Broadcast|Todos no domínio de broadcast|`192.168.1.255`|
|Multicast|Grupo específico|`239.1.1.1`|

Visualmente:

```text
UNicast

A ─────────→ B


BROADCAST

          ┌──→ B
          ├──→ C
A ────────┼──→ D
          └──→ E


MULTICAST

          ┌──→ B
          ├──→ D
A ────────┤
          └──→ F
```

---

## 28.21 Por que UDP é normalmente utilizado?

Broadcast e multicast estão normalmente associados a **UDP**.

Isso acontece porque UDP trabalha naturalmente com datagramas e permite enviar diretamente para um endereço de destino através de:

```python
sendto()
```

TCP possui outro modelo:

```text
TCP
   ↓
conexão ponto a ponto
   ↓
um peer específico
```

Não existe um mecanismo TCP equivalente a:

```text
TCP → broadcast
TCP → multicast
```

No modelo tradicional de sockets IP.

Portanto:

```text
UDP
 ├── Unicast
 ├── Broadcast IPv4
 └── Multicast

TCP
 └── Unicast orientado a conexão
```

---

## 28.22 IPv6 e multicast

Como vimos anteriormente, IPv6 não possui broadcast da mesma maneira que IPv4.

IPv6 utiliza multicast para diversas funções da própria arquitetura.

Os endereços multicast IPv6 começam com:

```text
ff00::/8
```

Por exemplo:

```text
ff02::1
```

é um endereço multicast IPv6 conhecido no contexto de todos os nós do enlace local.

Isso demonstra uma diferença importante:

```text
IPv4
 ├── Unicast
 ├── Broadcast
 └── Multicast

IPv6
 ├── Unicast
 └── Multicast
```

Essa é uma das razões pelas quais entender multicast é importante para compreender IPv6.

---

## 28.23 `SO_BROADCAST` vs `IP_ADD_MEMBERSHIP`

Não devemos confundir essas duas configurações.

### Broadcast

```python
socket.SO_BROADCAST
```

permite que o socket seja utilizado para envio de broadcast IPv4.

Exemplo:

```python
sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_BROADCAST,
    1
)
```

### Multicast

Para ingressar em um grupo IPv4:

```python
socket.IP_ADD_MEMBERSHIP
```

Exemplo:

```python
sock.setsockopt(
    socket.IPPROTO_IP,
    socket.IP_ADD_MEMBERSHIP,
    membership
)
```

Portanto:

```text
SO_BROADCAST
      ↓
Broadcast IPv4


IP_ADD_MEMBERSHIP
      ↓
Entrar em grupo multicast
```

São mecanismos diferentes.

---

## 28.24 Aplicações práticas

Broadcast pode ser utilizado em situações como:

- descoberta de dispositivos em uma rede local;
    
- descoberta de serviços;
    
- protocolos locais específicos;
    
- anúncios dentro de uma rede.
    

Multicast pode ser utilizado em:

- distribuição de dados para grupos;
    
- streaming em determinados ambientes;
    
- descoberta de serviços;
    
- protocolos de infraestrutura;
    
- aplicações que possuem muitos receptores interessados no mesmo fluxo.
    

Porém, a escolha depende da rede e do protocolo.

Não devemos escolher multicast simplesmente porque existem vários clientes.

---

## 28.25 Limitações e problemas práticos

### Broadcast pode gerar muito tráfego

Se uma máquina transmite frequentemente:

```text
broadcast → todos os hosts
```

muitos dispositivos podem precisar receber e processar os pacotes.

---

### Multicast depende da infraestrutura

Uma aplicação multicast pode funcionar perfeitamente em uma rede pequena e falhar em outra.

É necessário considerar:

- suporte do sistema operacional;
    
- switches;
    
- roteadores;
    
- configuração de multicast;
    
- firewall;
    
- interfaces;
    
- roteamento multicast.
    

---

### UDP não garante entrega

Tanto broadcast quanto multicast normalmente utilizam UDP.

Portanto, não temos automaticamente:

```text
entrega garantida
ordenação
retransmissão
controle de congestionamento TCP
```

Se a aplicação precisar dessas características, terá que implementar mecanismos próprios ou utilizar outro modelo.

---

## 28.26 Modelo mental final

Podemos pensar nos três modelos assim:

```text
                    ENVIO IP
                       │
          ┌────────────┼────────────┐
          │            │            │
       UNICAST      BROADCAST    MULTICAST
          │            │            │
          ↓            ↓            ↓
       1 host       todos os      grupo de
                     hosts         hosts
          │            │            │
          └────────────┼────────────┘
                       ↓
                      UDP
```

No IPv4:

```text
Unicast
    ↓
192.168.1.10

Broadcast
    ↓
192.168.1.255

Multicast
    ↓
239.1.1.1
```

No IPv6:

```text
Unicast
    ↓
2001:db8::10

Multicast
    ↓
ff02::1
```

---

## 28.27 Resumo da Parte

- **Unicast** envia para um destino específico.
    
- **Broadcast** envia para todos os hosts dentro de um domínio de broadcast IPv4.
    
- **Multicast** envia para um grupo específico de participantes.
    
- Broadcast é um mecanismo principalmente associado ao IPv4.
    
- IPv6 não utiliza broadcast da mesma forma e utiliza multicast para diversas funções.
    
- Para broadcast IPv4, podemos utilizar:
    

```python
socket.SO_BROADCAST
```

- Para participar de um grupo multicast IPv4, utilizamos:
    

```python
socket.IP_ADD_MEMBERSHIP
```

- Broadcast IPv4 depende da rede e da máscara.
    
- Endereços multicast IPv4 estão no intervalo:
    

```text
224.0.0.0/4
```

- Endereços multicast IPv6 utilizam:
    

```text
ff00::/8
```

- Broadcast e multicast normalmente são utilizados com UDP.
    
- UDP não garante entrega, ordem ou retransmissão.
    
- Multicast depende da infraestrutura da rede e pode não funcionar através de redes que não suportam roteamento multicast.
    
- `sendto()` permite enviar datagramas diretamente para um endereço.
    
- Broadcast e multicast devem ser utilizados com cuidado para evitar tráfego desnecessário e problemas de segurança.
    

A visão geral fica:

```text
             COMUNICAÇÃO IP
                    │
        ┌───────────┼───────────┐
        │           │           │
     Unicast    Broadcast    Multicast
        │           │           │
      1 → 1        1 → N       1 → grupo
        │           │           │
        └───────────┼───────────┘
                    │
                   UDP
```

---
# 29. Diagnóstico e depuração de sockets

## 29.1 Por que diagnosticar sockets é importante?

Quando um programa de rede não funciona, o erro nem sempre está no Python.

Podemos ter problemas em diferentes camadas:

```text
Aplicação Python
      ↓
Socket API
      ↓
Sistema operacional
      ↓
TCP/UDP
      ↓
Interface de rede
      ↓
Firewall
      ↓
Rede
      ↓
Servidor/cliente remoto
```

Por isso, quando aparece algo como:

```text
ConnectionRefusedError
```

não devemos simplesmente assumir:

> "O Python está com problema."

Precisamos descobrir **em qual etapa a comunicação está falhando**.

Uma boa depuração começa separando os problemas:

```text
1. O processo está executando?
2. O socket foi criado?
3. O bind funcionou?
4. Existe algum processo escutando?
5. A porta está correta?
6. O endereço está correto?
7. O firewall permite?
8. O cliente consegue chegar ao servidor?
9. A conexão foi estabelecida?
10. Os dados estão sendo enviados?
11. O servidor está recebendo?
12. O protocolo da aplicação está correto?
```

---

## 29.2 Primeiro diagnóstico: o processo está rodando?

Antes de investigar rede, precisamos verificar se o programa realmente está executando.

Por exemplo:

```bash
ps aux | grep python
```

Ou:

```bash
pgrep -af python
```

Também podemos verificar diretamente nosso processo:

```bash
pgrep -af server.py
```

Se o servidor não estiver rodando, não adianta investigar:

```text
TCP
porta
firewall
cliente
```

O primeiro problema é simplesmente:

```text
Servidor não está executando.
```

---

## 29.3 Verificando portas TCP com `ss`

No Linux, uma das ferramentas mais importantes para diagnosticar sockets é:

```bash
ss
```

Por exemplo:

```bash
ss -ltn
```

Significado:

```text
-l
↓
listening

-t
↓
TCP

-n
↓
não resolver nomes
```

Podemos procurar uma porta específica:

```bash
ss -ltn | grep :4444
```

Se existir um servidor TCP escutando nessa porta, podemos encontrar algo semelhante a:

```text
LISTEN 0 128 127.0.0.1:4444 0.0.0.0:*
```

Isso nos informa que existe um socket TCP em estado:

```text
LISTEN
```

na porta:

```text
4444
```

---

## 29.4 Entendendo a saída do `ss`

Considere:

```text
LISTEN 0 128 127.0.0.1:4444 0.0.0.0:*
```

Podemos interpretar:

```text
LISTEN
   ↓
socket aguardando conexões

127.0.0.1:4444
   ↓
endereço local

0.0.0.0:*
   ↓
peer remoto não definido
```

O ponto mais importante para iniciantes é:

```text
127.0.0.1:4444
```

Isso significa que o serviço está associado ao loopback.

Então:

```text
mesma máquina → pode acessar
outra máquina → não consegue acessar diretamente esse endereço
```

---

## 29.5 Verificando qual processo possui a porta

Podemos pedir ao `ss` informações sobre o processo:

```bash
sudo ss -ltnp
```

Exemplo:

```text
LISTEN 0 128 127.0.0.1:4444 0.0.0.0:* users:(("python3",pid=12345,fd=3))
```

Agora temos:

```text
processo:
python3

PID:
12345

file descriptor:
3
```

Isso é extremamente útil quando existe um conflito de porta.

---

## 29.6 Diagnóstico do erro `Address already in use`

Um erro que você pode encontrar ao trabalhar com sockets é:

```text
OSError: [Errno 98] Address already in use
```

Isso acontece quando o endereço que você está tentando utilizar já está ocupado ou em uma situação em que o bind não pode ser feito naquele momento.

Por exemplo:

```python
server.bind(("127.0.0.1", 4444))
```

pode falhar porque outro processo já está utilizando a porta.

Podemos investigar:

```bash
ss -ltnp | grep :4444
```

ou:

```bash
sudo lsof -i :4444
```

---

## 29.7 `lsof`

Outra ferramenta extremamente útil é:

```bash
lsof
```

Para verificar a porta:

```bash
sudo lsof -i :4444
```

Podemos obter algo semelhante a:

```text
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
python3  12345 usuario  3u  IPv4 ...        TCP 127.0.0.1:4444 (LISTEN)
```

Isso permite identificar:

- processo;
    
- PID;
    
- usuário;
    
- file descriptor;
    
- família de endereço;
    
- porta;
    
- estado.
    

---

## 29.8 Encerrar o processo que está ocupando a porta

Depois de descobrir o PID:

```text
12345
```

podemos verificar primeiro:

```bash
ps -p 12345 -f
```

Se tivermos certeza de que o processo pode ser encerrado:

```bash
kill 12345
```

Se ele não terminar normalmente, existe:

```bash
kill -9 12345
```

Porém, `SIGKILL` deve ser usado como último recurso, pois encerra o processo sem permitir uma finalização normal.

O ideal é:

```text
identificar
   ↓
entender
   ↓
encerrar normalmente
```

em vez de simplesmente executar:

```bash
kill -9
```

sem investigar.

---

## 29.9 `ConnectionRefusedError`

Outro erro muito comum:

```text
ConnectionRefusedError: [Errno 111] Connection refused
```

Isso normalmente significa que o host foi alcançado, mas a conexão TCP foi recusada.

Uma causa comum é:

```text
Cliente
   │
   │ connect()
   ↓
127.0.0.1:4444
   │
   X
nenhum servidor escutando
```

Por exemplo:

```python
client.connect(("127.0.0.1", 4444))
```

Se não existir um servidor escutando na porta, podemos receber:

```text
ConnectionRefusedError
```

---

## 29.10 Como investigar `ConnectionRefusedError`

Primeiro:

```bash
ss -ltn | grep :4444
```

Se não aparecer nada:

```text
não existe listener TCP nessa porta
```

Então devemos verificar:

```text
Servidor está executando?
        ↓
bind() funcionou?
        ↓
listen() foi executado?
        ↓
porta está correta?
        ↓
IP está correto?
```

Um fluxo prático:

```text
ConnectionRefusedError
        ↓
ss -ltn | grep :PORTA
        ↓
Existe LISTEN?
   ┌────┴────┐
  NÃO       SIM
   │          │
Servidor    Investigar
não está    endereço,
ouvindo     firewall,
            serviço etc.
```

---

## 29.11 `TimeoutError`

Outro problema comum:

```text
TimeoutError
```

Imagine:

```python
client.settimeout(5)
```

e depois:

```python
client.connect(("10.0.0.10", 4444))
```

Se a operação não concluir dentro do tempo configurado, podemos receber timeout.

Diferentemente de `ConnectionRefusedError`, timeout não significa necessariamente:

> "Não existe servidor."

Pode significar:

- pacote não chegou;
    
- firewall descartou silenciosamente;
    
- rota inexistente;
    
- host indisponível;
    
- serviço não respondeu;
    
- problema de rede;
    
- operação demorou mais que o limite.
    

Por isso:

```text
Connection refused
```

e:

```text
Timeout
```

são sintomas diferentes.

---

## 29.12 `ConnectionResetError`

Podemos encontrar:

```text
ConnectionResetError
```

Isso normalmente indica que a conexão TCP foi resetada pelo peer ou pela pilha de rede.

Modelo simplificado:

```text
Cliente
   │
   │ conexão
   ↓
Servidor
   │
   │ RST
   ↓
Cliente
```

Pode acontecer, por exemplo, quando:

- o processo remoto encerra abruptamente;
    
- o peer envia um reset;
    
- alguma condição da pilha TCP provoca reset;
    
- a aplicação fecha a conexão de maneira inesperada.
    

O erro não significa automaticamente que "o servidor está desligado".

---

## 29.13 `BrokenPipeError`

Esse erro pode aparecer quando tentamos escrever em uma conexão que já foi encerrada pelo outro lado.

Por exemplo:

```python
client.sendall(b"Mensagem")
```

e o peer já fechou a conexão.

Podemos receber:

```text
BrokenPipeError
```

A ideia é:

```text
Cliente                    Servidor
   │                          │
   │──── conexão ────────────→│
   │                          │
   │←──── close() ────────────│
   │                          │
   │──── sendall() ──────────→│
   │
   X BrokenPipeError
```

Por isso, aplicações reais precisam lidar com desconexões.

---

## 29.14 `recv()` retornando `b""`

Esse é um dos comportamentos mais importantes para entender.

Se:

```python
data = client.recv(1024)
```

retornar:

```python
b""
```

em uma conexão TCP bloqueante normal, isso indica que o outro lado realizou um **encerramento ordenado da conexão**.

Exemplo:

```python
while True:
    data = client.recv(1024)

    if not data:
        print("Cliente desconectou.")
        break

    print(data)
```

A lógica é:

```text
recv()
  ↓
dados?
 ┌───────┴────────┐
 ↓                ↓
sim              b""
 ↓                ↓
processa        peer encerrou
```

Não devemos confundir:

```python
b""
```

com:

```text
timeout
```

São situações diferentes.

---

## 29.15 Verificando conexões estabelecidas

Para visualizar conexões TCP atuais:

```bash
ss -tan
```

Onde:

```text
-t
↓
TCP

-a
↓
todos

-n
↓
endereços numéricos
```

Podemos encontrar:

```text
ESTAB
```

que representa:

```text
ESTABLISHED
```

Exemplo conceitual:

```text
ESTAB 0 0 127.0.0.1:4444 127.0.0.1:52834
```

Podemos interpretar:

```text
Servidor:
127.0.0.1:4444

Cliente:
127.0.0.1:52834
```

Observe que ambos utilizam o mesmo IP, mas possuem portas diferentes.

---

## 29.16 Entendendo o `ESTABLISHED`

Quando temos:

```text
ESTABLISHED
```

significa que existe uma conexão TCP estabelecida.

Podemos representar:

```text
Cliente
127.0.0.1:52834
       │
       │ TCP
       ↓
Servidor
127.0.0.1:4444
```

O servidor pode continuar utilizando a porta:

```text
4444
```

para aceitar novos clientes.

Cada conexão possui sua própria combinação de endpoints.

Por exemplo:

```text
Cliente A:
127.0.0.1:50001 → 127.0.0.1:4444

Cliente B:
127.0.0.1:50002 → 127.0.0.1:4444

Cliente C:
127.0.0.1:50003 → 127.0.0.1:4444
```

---

## 29.17 Verificando UDP

Para sockets UDP:

```bash
ss -lun
```

Onde:

```text
-l
↓
listening

-u
↓
UDP

-n
↓
numérico
```

Exemplo:

```text
UNCONN 0 0 0.0.0.0:4444 0.0.0.0:*
```

UDP não possui o mesmo estado `ESTABLISHED` que TCP.

Isso ocorre porque o modelo de UDP é baseado em datagramas e não em uma conexão TCP tradicional.

---

## 29.18 Verificando IPv6

Também precisamos prestar atenção à família do socket.

Podemos executar:

```bash
ss -ltn
```

e encontrar algo como:

```text
LISTEN 0 128 [::1]:4444 [::]:*
```

Aqui:

```text
[::1]:4444
```

representa um listener IPv6 no loopback.

Compare:

```text
IPv4:
127.0.0.1:4444

IPv6:
[::1]:4444
```

Isso é especialmente importante quando o programa funciona em IPv4 mas falha em IPv6, ou vice-versa.

---

## 29.19 Testando com `nc`

A ferramenta:

```bash
nc
```

ou:

```bash
netcat
```

é extremamente útil para testes rápidos de rede.

Se temos um servidor TCP:

```text
127.0.0.1:4444
```

podemos testar:

```bash
nc 127.0.0.1 4444
```

Isso cria um cliente TCP simples.

Podemos digitar:

```text
Olá servidor
```

e verificar se nosso programa Python recebe os dados.

Esse tipo de teste é muito útil porque permite separar:

```text
problema no cliente Python
```

de:

```text
problema no servidor Python
```

---

## 29.20 Testando UDP com `nc`

Também podemos utilizar UDP.

Por exemplo:

```bash
nc -u 127.0.0.1 4444
```

O parâmetro:

```text
-u
```

indica UDP.

Isso permite testar rapidamente um servidor UDP sem precisar escrever outro programa Python.

---

## 29.21 Testando conectividade com `nc -z`

Podemos utilizar:

```bash
nc -zv 127.0.0.1 4444
```

O significado aproximado:

```text
-z
↓
não enviar dados; apenas verificar

-v
↓
verbose
```

Se houver um serviço TCP escutando, podemos receber uma mensagem indicando que a conexão foi bem-sucedida.

Isso é útil para uma verificação rápida:

```text
Existe algo aceitando TCP nessa porta?
```

---

## 29.22 `curl` não é apenas para páginas web

Quando estamos testando serviços HTTP, podemos utilizar:

```bash
curl
```

Por exemplo:

```bash
curl http://127.0.0.1:8000
```

Isso permite verificar:

```text
DNS/resolução
conectividade
TCP
HTTP
resposta da aplicação
```

No entanto, `curl` é principalmente uma ferramenta para protocolos suportados por ele, como HTTP/HTTPS, e não um substituto genérico para qualquer protocolo de socket.

---

## 29.23 `ping` e uma limitação importante

Podemos utilizar:

```bash
ping 127.0.0.1
```

ou:

```bash
ping 192.168.1.10
```

para testar conectividade IP em determinados contextos.

Mas:

```text
ping funciona
```

não significa:

```text
porta TCP 4444 está aberta
```

São testes de camadas diferentes.

Por exemplo:

```text
ping
 ↓
IP/ICMP

nc
 ↓
TCP/UDP + porta

curl
 ↓
TCP/TLS + HTTP
```

Portanto, não devemos concluir:

> "O ping respondeu, então meu servidor TCP deveria funcionar."

Não necessariamente.

---

## 29.24 Capturando pacotes com `tcpdump`

Quando precisamos investigar profundamente o que está acontecendo na rede, podemos utilizar:

```bash
tcpdump
```

Por exemplo:

```bash
sudo tcpdump -i lo port 4444
```

Aqui:

```text
-i lo
↓
interface loopback

port 4444
↓
filtrar pela porta
```

Isso pode permitir observar o tráfego relacionado à porta.

Para um teste local:

```text
Cliente
127.0.0.1:XXXXX
      │
      │ TCP
      ↓
127.0.0.1:4444
```

podemos observar pacotes sendo trocados.

---

## 29.25 Observando o handshake TCP

Com uma captura adequada, podemos visualizar conceitualmente:

```text
Cliente                         Servidor

SYN ─────────────────────────────→

    ←──────────────────── SYN-ACK

ACK ─────────────────────────────→
```

Depois:

```text
Dados
────────────────────────────────→

      ←──────────────────────────
             Dados
```

E no encerramento:

```text
FIN
────────────────────────────────→

      ←──────────────────────────
                 ACK

      ←──────────────────────────
                 FIN

ACK
────────────────────────────────→
```

Isso ajuda a conectar aquilo que estudamos teoricamente com o comportamento real da rede.

---

## 29.26 Diagnóstico por camadas

Uma das melhores estratégias é diagnosticar de baixo para cima.

### Camada 1 — Processo

```bash
pgrep -af server.py
```

Pergunta:

> O programa está rodando?

---

### Camada 2 — Socket

```bash
ss -ltnp
```

Pergunta:

> Existe um socket escutando?

---

### Camada 3 — Endereço

Verificar:

```text
IP
porta
IPv4/IPv6
```

Pergunta:

> O cliente está tentando acessar o endereço correto?

---

### Camada 4 — Transporte

Testar:

```bash
nc -zv IP PORTA
```

Pergunta:

> A porta TCP está acessível?

---

### Camada 5 — Aplicação

Testar o protocolo real:

```bash
curl ...
```

ou o cliente específico.

Pergunta:

> O serviço está respondendo corretamente?

---

### Camada 6 — Pacotes

Se ainda existir dúvida:

```bash
tcpdump
```

Pergunta:

> Os pacotes realmente estão saindo e chegando?

---

## 29.27 Fluxo prático de troubleshooting

Imagine:

```python
client.connect(("192.168.1.50", 4444))
```

falhando.

Não devemos sair alterando código aleatoriamente.

Podemos seguir:

```text
1. O servidor está executando?
          ↓
2. O servidor fez bind()?
          ↓
3. O servidor está em LISTEN?
          ↓
4. Está escutando no IP correto?
          ↓
5. A porta está correta?
          ↓
6. Firewall permite?
          ↓
7. O cliente consegue alcançar o host?
          ↓
8. O TCP handshake acontece?
          ↓
9. A aplicação responde?
```

Isso transforma:

```text
"não funciona"
```

em:

```text
"o problema está nesta etapa específica"
```

---

## 29.28 Erros comuns de diagnóstico

### Erro 1 — Verificar apenas o código Python

Nem todo problema de socket está no código.

---

### Erro 2 — Confundir `127.0.0.1` com endereço da rede

Se o servidor fez:

```python
server.bind(("127.0.0.1", 4444))
```

outro computador não poderá acessar esse serviço através do IP da máquina.

---

### Erro 3 — Verificar a porta errada

Servidor:

```text
4444
```

Cliente:

```text
5555
```

Naturalmente não haverá conexão com o serviço esperado.

---

### Erro 4 — Confundir IPv4 e IPv6

Servidor:

```text
127.0.0.1
```

Cliente:

```text
::1
```

Podemos estar usando famílias diferentes.

---

### Erro 5 — Achar que `ping` testa uma porta

`ping` testa outra camada/protocolo.

Para TCP, utilize uma ferramenta apropriada, como:

```bash
nc
```

---

### Erro 6 — Confundir timeout com conexão recusada

```text
ConnectionRefusedError
```

e:

```text
TimeoutError
```

possuem causas e significados diferentes.

---

## 29.29 Ferramentas principais

|Ferramenta|Uso principal|
|---|---|
|`ss`|visualizar sockets e estados|
|`lsof`|descobrir processos associados a arquivos/portas|
|`nc`|testar TCP/UDP|
|`ping`|testar conectividade IP/ICMP|
|`curl`|testar serviços HTTP/HTTPS|
|`tcpdump`|capturar/analisar pacotes|
|`ps`|visualizar processos|
|`pgrep`|localizar processos pelo nome/PID|

Uma boa sequência para um problema TCP pode ser:

```bash
pgrep -af server.py
```

depois:

```bash
ss -ltnp
```

depois:

```bash
nc -zv 127.0.0.1 4444
```

e, se necessário:

```bash
sudo tcpdump -i lo port 4444
```

---

## 29.30 Modelo mental

Quando um socket falha, pense em camadas:

```text
             PROBLEMA
                │
                ↓
         ┌──────────────┐
         │ Aplicação    │
         └──────┬───────┘
                ↓
         ┌──────────────┐
         │ Socket API   │
         └──────┬───────┘
                ↓
         ┌──────────────┐
         │ TCP / UDP    │
         └──────┬───────┘
                ↓
         ┌──────────────┐
         │ IP / IPv6    │
         └──────┬───────┘
                ↓
         ┌──────────────┐
         │ Interface    │
         └──────┬───────┘
                ↓
         ┌──────────────┐
         │ Rede/Firewall│
         └──────────────┘
```

A pergunta não deve ser:

> "Por que meu socket não funciona?"

A pergunta deve ser:

> **"Em qual camada a comunicação está falhando?"**

Esse é um dos modelos mentais mais importantes para trabalhar profissionalmente com redes.

---

## 29.31 Resumo da Parte

- `ss` é uma das principais ferramentas Linux para diagnosticar sockets.
    
- `ss -ltn` mostra listeners TCP.
    
- `ss -lun` mostra listeners UDP.
    
- `ss -tan` mostra conexões TCP.
    
- `ss -ltnp` ajuda a descobrir qual processo possui uma porta.
    
- `lsof -i :PORTA` também pode identificar o processo associado à porta.
    
- `ConnectionRefusedError` geralmente indica uma conexão TCP recusada.
    
- `TimeoutError` indica que uma operação não terminou dentro do tempo permitido.
    
- `ConnectionResetError` indica um reset da conexão.
    
- `BrokenPipeError` pode acontecer ao escrever em uma conexão que o outro lado já encerrou.
    
- `recv()` retornando `b""` indica encerramento ordenado da conexão TCP.
    
- `nc` é excelente para testes rápidos de TCP e UDP.
    
- `ping` testa conectividade IP/ICMP, não uma porta TCP específica.
    
- `curl` é útil para testar serviços HTTP/HTTPS.
    
- `tcpdump` permite observar o tráfego real.
    
- O diagnóstico deve ser feito por camadas.
    
- O objetivo não é apenas descobrir **que** algo falhou, mas descobrir **onde** e **por quê**.
    

Modelo final:

```text
Processo
   ↓
Socket
   ↓
Porta/endereço
   ↓
TCP/UDP
   ↓
IP
   ↓
Interface
   ↓
Rede
   ↓
Destino
```

E, para depurar:

```text
"Não funciona"
      ↓
"Qual camada falhou?"
      ↓
"Qual evidência tenho?"
      ↓
"Qual ferramenta pode confirmar?"
      ↓
"Qual é a causa?"
```

---
# 30. Tratamento de erros e desconexões em servidores reais

## 30.1 Por que tratamento de erros é essencial?

Nos exemplos anteriores, muitos servidores tinham uma estrutura simples:

```python
client, address = server.accept()

data = client.recv(1024)

client.sendall(data)

client.close()
```

Isso funciona para estudar o conceito, mas um servidor real precisa considerar que **qualquer etapa da comunicação pode falhar**.

O cliente pode:

- fechar a conexão;
    
- perder a rede;
    
- enviar dados inválidos;
    
- enviar dados incompletos;
    
- permanecer conectado sem enviar nada;
    
- enviar uma quantidade enorme de dados;
    
- desconectar durante um `send()`;
    
- enviar uma mensagem que viola o protocolo.
    

O próprio sistema operacional também pode retornar erros.

Por isso, um servidor robusto precisa tratar erros sem necessariamente derrubar o processo inteiro.

A ideia é:

```text
Cliente com problema
       ↓
Erro tratado
       ↓
Conexão encerrada
       ↓
Servidor continua funcionando
       ↓
Outros clientes continuam sendo atendidos
```

---

## 30.2 Exceções de socket

A biblioteca `socket` utiliza as exceções normais do Python para comunicar vários erros.

Uma exceção importante é:

```python
OSError
```

Muitos erros de baixo nível relacionados a sockets derivam de `OSError`.

Por exemplo:

```python
try:
    server.bind(("127.0.0.1", 4444))
except OSError as error:
    print(f"Erro ao iniciar servidor: {error}")
```

Isso permite tratar problemas como:

- endereço já utilizado;
    
- permissão negada;
    
- endereço inválido;
    
- interface indisponível;
    
- falhas de sistema.
    

---

## 30.3 Exceções específicas

Além de `OSError`, existem exceções mais específicas.

Algumas importantes:

```text
socket.timeout
ConnectionRefusedError
ConnectionResetError
BrokenPipeError
ConnectionAbortedError
BlockingIOError
socket.gaierror
```

Podemos organizar conceitualmente:

```text
OSError
├── ConnectionError
│   ├── ConnectionRefusedError
│   ├── ConnectionResetError
│   ├── ConnectionAbortedError
│   └── BrokenPipeError
│
├── TimeoutError
│
└── ...
```

A hierarquia exata possui detalhes adicionais, mas o importante é entender que podemos capturar erros mais específicos antes de usar um tratamento genérico.

---

## 30.4 `try/except`

O mecanismo básico é:

```python
try:
    data = client.recv(1024)
except ConnectionResetError:
    print("Cliente encerrou a conexão abruptamente.")
```

Podemos tratar vários erros:

```python
try:
    data = client.recv(1024)

except ConnectionResetError:
    print("Conexão resetada.")

except socket.timeout:
    print("Timeout.")

except OSError as error:
    print(f"Erro de socket: {error}")
```

A ordem é importante.

Erros específicos devem normalmente ser tratados antes do tratamento genérico.

---

## 30.5 Não capturar `Exception` cegamente

É possível escrever:

```python
try:
    ...
except Exception:
    pass
```

Mas isso é uma prática ruim para um servidor real.

Esse código:

```python
except Exception:
    pass
```

basicamente diz:

> Se qualquer coisa der errado, ignore.

Isso pode esconder bugs graves.

Por exemplo:

```python
def process_message(data):
    return data["username"]
```

Se `data` for um `bytes` em vez de um dicionário, teremos um erro de programação.

Se fizermos:

```python
try:
    process_message(data)
except Exception:
    pass
```

o servidor pode continuar executando, mas ninguém saberá que existe um bug.

O ideal é:

```python
except ConnectionResetError:
    ...
```

ou:

```python
except OSError as error:
    ...
```

quando realmente sabemos que estamos tratando um erro de socket.

---

## 30.6 `finally`

O bloco:

```python
finally:
```

é executado independentemente de uma exceção ter ocorrido ou não.

Exemplo:

```python
client = None

try:
    client, address = server.accept()

    data = client.recv(1024)

    client.sendall(data)

except OSError as error:
    print(f"Erro: {error}")

finally:
    if client is not None:
        client.close()
```

Isso é útil para garantir a liberação de recursos.

Podemos pensar:

```text
try
 ↓
executa operação
 ↓
erro?
 ├── não → continua
 └── sim → except
 ↓
finally
 ↓
limpeza
```

---

## 30.7 `with` para gerenciamento de sockets

Quando possível, podemos utilizar um context manager:

```python
with socket.socket(...) as server:
    ...
```

Quando o bloco termina, o socket é fechado automaticamente.

Exemplo:

```python
import socket

with socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
) as server:

    server.bind(("127.0.0.1", 4444))
    server.listen()

    print("Servidor iniciado")

    client, address = server.accept()

    with client:
        data = client.recv(1024)
        client.sendall(data)
```

Isso reduz a possibilidade de esquecer:

```python
close()
```

---

## 30.8 O socket de escuta e o socket do cliente são diferentes

Esse conceito continua sendo fundamental.

Temos:

```text
server
  ↓
socket de escuta

client
  ↓
socket da conexão
```

Quando fazemos:

```python
client, address = server.accept()
```

o Python retorna um novo socket.

Então:

```text
server
  ↓
continua escutando

client
  ↓
comunicação com aquele cliente
```

Se um cliente desconectar:

```python
client.close()
```

não devemos fechar automaticamente:

```python
server.close()
```

caso o servidor ainda precise aceitar outros clientes.

---

## 30.9 Desconexão normal

Imagine:

```text
Cliente                     Servidor
   │                           │
   │──── conexão ─────────────→│
   │                           │
   │──── dados ───────────────→│
   │                           │
   │          close()          │
   │──────── FIN ─────────────→│
   │                           │
```

No servidor:

```python
data = client.recv(1024)
```

pode retornar:

```python
b""
```

Isso indica que o peer realizou um encerramento ordenado.

Podemos tratar:

```python
if data == b"":
    print("Cliente desconectou.")
    client.close()
```

---

## 30.10 Desconexão abrupta

Nem toda desconexão ocorre de maneira limpa.

Podemos ter:

```text
Cliente
   │
   │ conexão
   ↓
Servidor
   │
   │
   X
conexão interrompida
```

Nesse caso, o servidor pode receber:

```python
ConnectionResetError
```

Por exemplo:

```python
try:
    data = client.recv(1024)

except ConnectionResetError:
    print("Cliente perdeu a conexão.")
```

Isso não deve necessariamente derrubar o servidor inteiro.

A conexão problemática pode simplesmente ser encerrada.

---

## 30.11 Cliente desconectando durante `sendall()`

Também podemos ter problemas durante o envio.

Por exemplo:

```python
try:
    client.sendall(data)

except BrokenPipeError:
    print("Cliente fechou a conexão durante o envio.")

except ConnectionResetError:
    print("Conexão foi resetada pelo cliente.")
```

Isso é importante porque o fato de termos recebido dados anteriormente não garante que o cliente continuará conectado.

Uma conexão TCP pode mudar de estado a qualquer momento.

---

## 30.12 O servidor não deve confiar no cliente

Em aplicações reais, devemos assumir:

> **Tudo que vem do cliente é entrada não confiável.**

Isso inclui:

```text
dados
comandos
tamanho
nomes de arquivos
IDs
JSON
headers
strings
binários
```

Por exemplo, se nosso protocolo espera:

```text
LOGIN usuario senha
```

não devemos simplesmente assumir que o cliente sempre enviará isso corretamente.

O cliente pode enviar:

```text
AAAAAAAAAAAAAAAAAAAAAAAA...
```

ou:

```text
COMANDO_INEXISTENTE
```

ou:

```text
dados incompletos
```

ou uma entrada malformada.

---

## 30.13 Validação antes de processar

Um padrão importante é:

```text
receber
   ↓
validar
   ↓
interpretar
   ↓
processar
   ↓
responder
```

Não:

```text
receber
   ↓
executar imediatamente
```

Por exemplo:

```python
data = client.recv(1024)

if len(data) > 1024:
    client.close()
    return
```

Embora esse exemplo específico já esteja limitado pelo tamanho do `recv()`, a ideia é que o protocolo deve possuir limites explícitos.

---

## 30.14 Limites de tamanho

Imagine um protocolo que recebe uma mensagem:

```text
[TAMANHO][DADOS]
```

O cliente informa:

```text
999999999999999999
```

Se o servidor tentar simplesmente reservar toda essa quantidade de memória, poderá sofrer problemas.

Por isso:

```python
MAX_MESSAGE_SIZE = 1024 * 1024
```

e:

```python
if message_size > MAX_MESSAGE_SIZE:
    raise ValueError("Mensagem muito grande")
```

Esse tipo de limite é uma defesa importante.

---

## 30.15 Timeout para evitar clientes presos

Imagine:

```text
Cliente conecta
     ↓
Servidor aceita
     ↓
Cliente não envia nada
     ↓
Servidor fica esperando
```

Se o servidor utiliza:

```python
client.recv(1024)
```

em modo bloqueante, pode ficar esperando indefinidamente.

Podemos configurar:

```python
client.settimeout(10)
```

Agora:

```python
data = client.recv(1024)
```

não ficará bloqueado indefinidamente.

Depois do tempo configurado, podemos receber:

```python
socket.timeout
```

Exemplo:

```python
try:
    client.settimeout(10)
    data = client.recv(1024)

except socket.timeout:
    print("Cliente demorou demais.")
    client.close()
```

---

## 30.16 Timeout não substitui protocolo

Timeout é útil, mas não resolve todos os problemas.

Imagine que uma mensagem válida possa levar:

```text
30 segundos
```

para ser transmitida em uma conexão lenta.

Se configurarmos:

```python
client.settimeout(5)
```

podemos encerrar uma conexão válida prematuramente.

Portanto, os valores devem refletir o comportamento esperado da aplicação.

Além disso, protocolos podem precisar de:

- timeout de conexão;
    
- timeout de leitura;
    
- timeout de escrita;
    
- timeout de autenticação;
    
- timeout de inatividade.
    

---

## 30.17 Timeout de inatividade

Um conceito diferente é o **idle timeout**.

Imagine:

```text
Cliente conectado
       ↓
10 minutos sem enviar nada
       ↓
Servidor encerra conexão
```

Isso evita manter recursos ocupados indefinidamente.

Podemos controlar isso utilizando timestamps, timers ou mecanismos de timeout.

Conceitualmente:

```python
last_activity = time.monotonic()
```

e:

```python
if time.monotonic() - last_activity > IDLE_TIMEOUT:
    close_connection()
```

`time.monotonic()` é apropriado para medir durações porque não depende de alterações no relógio do sistema.

---

## 30.18 Servidor não deve morrer por causa de um cliente

Esse é um princípio importante.

Imagine:

```text
Servidor
   │
   ├── Cliente A → erro
   │
   ├── Cliente B → funcionando
   │
   └── Cliente C → funcionando
```

Se o Cliente A enviar algo inválido, o ideal é:

```text
Cliente A
   ↓
erro
   ↓
fecha conexão A
   ↓
Servidor continua
   ↓
B e C continuam funcionando
```

Não:

```text
Cliente A
   ↓
erro
   ↓
Servidor inteiro encerra
```

É exatamente por isso que servidores concorrentes precisam tratar erros **por conexão**.

---

## 30.19 Tratamento de erros em um servidor concorrente

Considere:

```python
def handle_client(client):
    try:
        while True:
            data = client.recv(1024)

            if not data:
                break

            client.sendall(data)

    except ConnectionResetError:
        print("Cliente desconectou abruptamente.")

    except OSError as error:
        print(f"Erro de socket: {error}")

    finally:
        client.close()
```

Cada cliente possui seu próprio tratamento:

```text
Cliente A
   ↓
handle_client(A)

Cliente B
   ↓
handle_client(B)

Cliente C
   ↓
handle_client(C)
```

Se A falhar:

```text
A → erro → fecha A

B → continua
C → continua
```

---

## 30.20 Logging

Em um servidor real, `print()` pode não ser suficiente.

Podemos utilizar:

```python
import logging
```

Exemplo:

```python
import logging

logging.basicConfig(
    level=logging.INFO
)

logging.info("Servidor iniciado")
```

Depois:

```python
logging.info("Cliente conectado")
```

ou:

```python
logging.error("Falha ao processar cliente")
```

Também existem níveis como:

```text
DEBUG
INFO
WARNING
ERROR
CRITICAL
```

Podemos pensar:

```text
DEBUG
 ↓
informações detalhadas

INFO
 ↓
eventos normais

WARNING
 ↓
situação anormal, mas não necessariamente fatal

ERROR
 ↓
erro

CRITICAL
 ↓
problema grave
```

---

## 30.21 Não registrar informações sensíveis

Logging é importante, mas não devemos registrar indiscriminadamente:

```text
senhas
tokens
chaves privadas
cookies
dados pessoais desnecessários
credenciais
```

Por exemplo, não devemos fazer:

```python
logging.info(f"Login: usuario={user}, senha={password}")
```

Mesmo durante desenvolvimento, esse hábito pode acabar indo para produção.

Melhor:

```python
logging.info("Tentativa de autenticação recebida")
```

e registrar apenas o necessário para diagnosticar o comportamento.

---

## 30.22 Encerramento gracioso

Um servidor pode precisar ser encerrado sem cortar clientes abruptamente.

Uma ideia simplificada:

```text
Recebe sinal de encerramento
        ↓
para de aceitar novos clientes
        ↓
termina operações atuais
        ↓
fecha conexões
        ↓
fecha socket de escuta
        ↓
encerra processo
```

Isso é chamado de **graceful shutdown**.

Em Python, podemos utilizar mecanismos como:

```python
signal
```

para reagir a sinais do sistema.

Por exemplo, um processo pode receber:

```text
SIGTERM
```

e iniciar o encerramento de maneira controlada.

---

## 30.23 `SO_REUSEADDR` e reinicialização

Durante o desenvolvimento, podemos reiniciar um servidor rapidamente.

Às vezes encontramos:

```text
OSError: [Errno 98] Address already in use
```

Uma configuração frequentemente utilizada é:

```python
server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)
```

antes de:

```python
server.bind(...)
```

Isso pode ajudar em situações envolvendo endereços que ainda estão em estados relacionados ao encerramento anterior.

Mas:

```text
SO_REUSEADDR
```

não significa:

> "Ignore qualquer conflito de porta."

Se outro processo estiver realmente escutando na mesma combinação de endereço/porta e as regras do sistema não permitirem o compartilhamento, o `bind()` ainda poderá falhar.

---

## 30.24 `shutdown()` antes de `close()`

Em determinadas aplicações, podemos utilizar:

```python
client.shutdown(socket.SHUT_RDWR)
```

antes de:

```python
client.close()
```

Isso permite indicar explicitamente que não queremos mais enviar nem receber.

Porém, não é obrigatório chamar `shutdown()` em toda situação.

Em muitos casos:

```python
client.close()
```

é suficiente.

O importante é entender que:

```text
shutdown()
    ↓
controla a direção da comunicação

close()
    ↓
libera o descritor/socket
```

---

## 30.25 Ordem recomendada ao tratar uma conexão

Um fluxo robusto pode ser:

```text
accept()
   ↓
configurar timeout
   ↓
receber dados
   ↓
validar framing
   ↓
validar tamanho
   ↓
validar conteúdo
   ↓
processar
   ↓
enviar resposta
   ↓
tratar desconexão/erro
   ↓
fechar socket
```

Visualmente:

```text
          ┌───────────────┐
          │   accept()    │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │    recv()     │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │    validar    │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │   processar   │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │   sendall()   │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │    close()    │
          └───────────────┘
```

Se algo falhar:

```text
             erro
              ↓
        tratar exceção
              ↓
       limpar recursos
              ↓
       fechar conexão
              ↓
      continuar servidor
```

---

## 30.26 Exemplo de servidor mais robusto

Um servidor simples pode ficar assim:

```python
import socket
import logging

HOST = "127.0.0.1"
PORT = 4444

logging.basicConfig(
    level=logging.INFO
)

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)

server.bind((HOST, PORT))
server.listen()

logging.info(
    f"Servidor ouvindo em {HOST}:{PORT}"
)

while True:
    client = None

    try:
        client, address = server.accept()

        logging.info(
            f"Cliente conectado: {address}"
        )

        client.settimeout(30)

        while True:
            data = client.recv(1024)

            if not data:
                logging.info(
                    f"Cliente desconectou: {address}"
                )
                break

            logging.info(
                f"Recebidos {len(data)} bytes"
            )

            client.sendall(data)

    except KeyboardInterrupt:
        logging.info("Encerrando servidor...")
        break

    except socket.timeout:
        logging.warning(
            "Timeout durante comunicação"
        )

    except ConnectionResetError:
        logging.warning(
            "Conexão resetada pelo cliente"
        )

    except BrokenPipeError:
        logging.warning(
            "Cliente fechou a conexão durante o envio"
        )

    except OSError as error:
        logging.error(
            f"Erro de socket: {error}"
        )

    finally:
        if client is not None:
            client.close()

server.close()
```

Esse exemplo ainda não é um servidor de produção, mas já demonstra conceitos importantes:

- tratamento de exceções;
    
- `SO_REUSEADDR`;
    
- timeout;
    
- logging;
    
- desconexão normal;
    
- reset de conexão;
    
- `BrokenPipeError`;
    
- limpeza com `finally`;
    
- encerramento por `KeyboardInterrupt`.
    

---

## 30.27 Um detalhe importante sobre o `while`

Observe:

```python
while True:
    client, address = server.accept()
```

O servidor continua aceitando novos clientes.

Dentro da conexão:

```python
while True:
    data = client.recv(1024)
```

o servidor continua recebendo dados daquele cliente.

Temos dois níveis:

```text
Servidor
   │
   ├── accept()
   │
   └── Cliente
         │
         ├── recv()
         ├── processa
         ├── sendall()
         └── recv()
```

Em um servidor sequencial, enquanto o segundo `while` estiver processando um cliente, outros clientes podem ficar esperando.

Por isso, os conceitos desta parte precisam ser combinados com o que estudamos sobre:

```text
threading
selectors
asyncio
```

para construir servidores concorrentes.

---

## 30.28 Segurança e tratamento de erros

Tratamento de erros também faz parte da segurança.

Um servidor que encerra ao receber uma entrada inválida pode sofrer uma forma simples de **negação de serviço**.

Por exemplo:

```text
Cliente malicioso
      ↓
entrada inválida
      ↓
exceção não tratada
      ↓
servidor encerra
      ↓
serviço indisponível
```

Um servidor mais robusto faz:

```text
Cliente malicioso
      ↓
entrada inválida
      ↓
erro tratado
      ↓
conexão encerrada
      ↓
servidor continua
```

Isso não significa que apenas `try/except` torna uma aplicação segura.

Também precisamos de:

- limites;
    
- validação;
    
- autenticação;
    
- autorização;
    
- controle de recursos;
    
- timeouts;
    
- logs;
    
- isolamento;
    
- protocolo bem definido.
    

---

## 30.29 Modelo mental

Um servidor real pode ser pensado como:

```text
                SERVIDOR
                   │
                   ↓
                accept()
                   │
          ┌────────┴────────┐
          ↓                 ↓
      Cliente A          Cliente B
          │                 │
       recv()             recv()
          │                 │
       validar            validar
          │                 │
      processar          processar
          │                 │
      sendall()          sendall()
          │                 │
        erro?              erro?
       ┌──┴──┐           ┌──┴──┐
      não   sim          não   sim
       │     │            │     │
       ↓     ↓            ↓     ↓
    continua trata       continua trata
             │                   │
             ↓                   ↓
          fecha A             fecha B
```

O princípio central é:

> **Uma falha em uma conexão não deve necessariamente significar uma falha no servidor inteiro.**

---

## 30.30 Resumo da Parte

- Servidores reais precisam tratar erros e desconexões.
    
- `OSError` é uma exceção importante para operações de baixo nível.
    
- Existem erros específicos como:
    
    - `ConnectionRefusedError`
        
    - `ConnectionResetError`
        
    - `BrokenPipeError`
        
    - `socket.timeout`
        
    - `BlockingIOError`
        
- `recv()` retornando `b""` indica encerramento ordenado da conexão TCP.
    
- `try/except` permite tratar falhas sem necessariamente derrubar o servidor.
    
- `finally` é útil para garantir limpeza de recursos.
    
- `with socket.socket(...)` ajuda a fechar sockets automaticamente.
    
- O socket retornado por `accept()` deve ser tratado separadamente do socket de escuta.
    
- Dados recebidos do cliente devem ser considerados **não confiáveis**.
    
- Protocolos devem validar:
    
    - formato;
        
    - tamanho;
        
    - conteúdo;
        
    - limites;
        
    - estado da comunicação.
        
- Timeouts ajudam a evitar conexões presas indefinidamente.
    
- Servidores concorrentes devem tratar erros por conexão.
    
- `logging` é preferível a depender apenas de `print()` em aplicações maiores.
    
- Informações sensíveis não devem ser registradas desnecessariamente.
    
- Graceful shutdown permite encerrar o servidor de maneira controlada.
    
- `SO_REUSEADDR` pode facilitar reinicializações, mas não elimina arbitrariamente conflitos de portas.
    
- Tratamento de erros também é uma preocupação de segurança.
    
- Um cliente com comportamento inválido não deveria derrubar o servidor inteiro.
    

O modelo principal desta parte é:

```text
Cliente
   ↓
conecta
   ↓
envia dados
   ↓
servidor recebe
   ↓
valida
   ↓
processa
   ↓
responde
   ↓
erro/desconexão?
   ├── não → continua
   └── sim → trata → fecha cliente
                         ↓
                  servidor continua
```

---
# 31. Arquitetura de um servidor TCP completo

Até agora estudamos cada peça de um servidor TCP separadamente:

- `socket()`
    
- `setsockopt()`
    
- `bind()`
    
- `listen()`
    
- `accept()`
    
- `recv()`
    
- `send()` / `sendall()`
    
- `shutdown()`
    
- `close()`
    
- tratamento de erros
    
- timeouts
    
- framing
    
- concorrência
    
- `selectors`
    
- `asyncio`
    
- TLS
    
- IPv4 e IPv6
    

Agora vamos juntar esses conceitos para entender **como um servidor TCP real é estruturado**.

A principal ideia desta parte é:

> Um servidor TCP não é apenas um `while True` com `accept()`. Ele possui várias responsabilidades diferentes, e separá-las torna o sistema mais fácil de entender, testar, manter e proteger.

---

## 31.1 O ciclo de vida de um servidor TCP

Um servidor TCP normalmente segue este fluxo:

```text
                    INICIALIZAÇÃO
                         │
                         ▼
                    socket()
                         │
                         ▼
                  setsockopt()
                         │
                         ▼
                      bind()
                         │
                         ▼
                     listen()
                         │
                         ▼
                  ┌──────────────┐
                  │  SERVIDOR    │
                  │   ONLINE     │
                  └──────┬───────┘
                         │
                         ▼
                      accept()
                         │
                         ▼
                ┌─────────────────┐
                │ Cliente conectado│
                └────────┬────────┘
                         │
                         ▼
                  receber dados
                         │
                         ▼
                 interpretar protocolo
                         │
                         ▼
                  executar lógica
                         │
                         ▼
                   enviar resposta
                         │
                         ▼
                  continuar sessão
                         │
                         ▼
                   desconexão
                         │
                         ▼
                  fechar conexão
                         │
                         └──────────────► accept()
```

O ponto importante é que existem **dois ciclos diferentes**:

```text
Ciclo do servidor
    └── aceita novos clientes

Ciclo da conexão
    └── conversa com um cliente específico
```

Isso é fundamental.

---

## 31.2 O socket de escuta não é o socket do cliente

Um servidor TCP normalmente possui pelo menos dois tipos de socket.

### Listening socket

É criado para receber novas conexões:

```python
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("127.0.0.1", 4444))
server.listen()
```

Depois:

```python
client, address = server.accept()
```

O `server` continua sendo o socket responsável por aceitar novos clientes.

---

### Client socket

O socket retornado pelo `accept()` representa uma conexão específica:

```python
client, address = server.accept()
```

Por exemplo:

```text
server
127.0.0.1:4444
     │
     ├── client A
     │     127.0.0.1:52131
     │
     ├── client B
     │     127.0.0.1:52132
     │
     └── client C
           127.0.0.1:52133
```

O servidor pode continuar escutando na porta `4444` enquanto conversa simultaneamente com vários clientes.

---

## 31.3 Separando as responsabilidades

Um servidor bem estruturado pode ser dividido conceitualmente em camadas:

```text
┌─────────────────────────────────────┐
│        Aplicação / Negócio          │
│                                     │
│  O que fazer com a informação?      │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│       Protocolo da aplicação        │
│                                     │
│  Como interpretar as mensagens?     │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│       Transporte / Socket            │
│                                     │
│ recv(), sendall(), close()          │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│             TCP/IP                  │
└─────────────────────────────────────┘
```

Essa separação é extremamente importante.

Por exemplo:

```text
TCP
↓
recebe bytes

Protocolo
↓
transforma bytes em mensagem

Aplicação
↓
decide o que fazer com a mensagem

Protocolo
↓
transforma resposta em bytes

TCP
↓
envia bytes
```

---

## 31.4 O servidor não deveria misturar tudo

Um exemplo ruim seria:

```python
while True:
    client, address = server.accept()

    data = client.recv(1024)

    if data == b"PING":
        client.sendall(b"PONG")

    elif data == b"LOGIN":
        ...

    elif data == b"DOWNLOAD":
        ...

    elif data == b"UPLOAD":
        ...

    ...
```

Esse modelo pode funcionar em um exemplo pequeno.

Porém, conforme o sistema cresce, tudo começa a ficar misturado:

```text
socket
protocolo
autenticação
validação
banco de dados
arquivos
permissões
logs
erros
respostas
```

Isso torna o código difícil de manter.

---

# 31.5 Uma arquitetura mais organizada

Podemos separar as responsabilidades:

```text
Servidor
   │
   ├── aceita conexão
   │
   ▼
Connection Handler
   │
   ├── recebe bytes
   │
   ▼
Protocol Parser
   │
   ├── interpreta mensagem
   │
   ▼
Application Logic
   │
   ├── executa operação
   │
   ▼
Response Builder
   │
   ├── cria resposta
   │
   ▼
Connection Handler
   │
   └── envia bytes
```

Por exemplo, imagine um protocolo:

```text
PING
LOGIN usuario senha
GET arquivo.txt
```

O socket não precisa saber o significado dessas mensagens.

Ele apenas transporta bytes.

Quem interpreta é a camada de protocolo.

---

## 31.6 Um exemplo de organização de arquivos

Um projeto maior poderia ser organizado assim:

```text
servidor_tcp/
│
├── server.py
├── protocol.py
├── handler.py
├── config.py
└── main.py
```

Cada arquivo possui uma responsabilidade.

### `server.py`

Responsável pela infraestrutura do servidor:

```text
socket
bind
listen
accept
shutdown
```

---

### `handler.py`

Responsável por uma conexão:

```text
recv
framing
tratamento da sessão
send
close
```

---

### `protocol.py`

Responsável por interpretar mensagens:

```text
PING
LOGIN
GET
UPLOAD
...
```

---

### `config.py`

Responsável por configurações:

```text
HOST
PORT
TIMEOUT
MAX_CLIENTS
TAMANHO_MÁXIMO
```

---

### `main.py`

Responsável por iniciar o sistema:

```text
carregar configuração
configurar logging
criar servidor
iniciar execução
```

Não existe uma única arquitetura obrigatória. O objetivo é evitar que todas as responsabilidades fiquem concentradas em uma função gigantesca.

---

# 31.7 O fluxo completo de uma requisição

Imagine um cliente enviando:

```text
PING
```

O fluxo poderia ser:

```text
Cliente
   │
   │ b"PING\n"
   ▼
TCP
   │
   ▼
recv()
   │
   ▼
buffer
   │
   ▼
parser
   │
   ▼
"PING"
   │
   ▼
application logic
   │
   ▼
"PONG"
   │
   ▼
encoder
   │
   ▼
sendall()
   │
   ▼
TCP
   │
   ▼
Cliente
```

Perceba que o TCP não sabe que existe um `PING`.

Para o TCP existem apenas bytes:

```text
50 49 4E 47 0A
```

O significado é criado pela aplicação.

---

# 31.8 O buffer pertence à conexão

Como já vimos, TCP é um fluxo de bytes.

Portanto, não podemos assumir:

```python
data = client.recv(1024)
```

e pensar:

```text
recv()
=
uma mensagem
```

Isso está errado.

Podemos receber:

```text
PING\nPONG\n
```

em uma única chamada.

Ou:

```text
PI
```

e depois:

```text
NG\n
```

Por isso cada conexão pode possuir seu próprio buffer:

```python
buffer = b""
```

Recebemos novos bytes:

```python
buffer += data
```

E extraímos mensagens completas:

```python
while b"\n" in buffer:
    message, buffer = buffer.split(b"\n", 1)

    process(message)
```

O restante permanece no buffer para a próxima mensagem.

---

# 31.9 Estado da conexão

Um servidor real muitas vezes precisa saber em qual estado o cliente está.

Por exemplo:

```text
CONNECTED
    │
    ▼
WAITING_AUTH
    │
    ▼
AUTHENTICATED
    │
    ▼
ACTIVE
    │
    ▼
CLOSING
```

Imagine um protocolo de autenticação:

```text
Cliente → LOGIN allan senha123
Servidor → OK
```

Depois disso:

```text
Cliente → GET arquivo.txt
```

O servidor pode permitir porque o cliente está autenticado.

Mas se alguém enviar:

```text
GET arquivo.txt
```

antes do login:

```text
Servidor → AUTH_REQUIRED
```

Portanto, uma conexão pode possuir informações como:

```python
client_state = {
    "authenticated": False,
    "username": None,
}
```

Em um projeto maior, isso pode ser representado por uma classe.

---

# 31.10 Exemplo de um handler simples

Podemos encapsular o estado de uma conexão:

```python
class ClientHandler:
    def __init__(self, client, address):
        self.client = client
        self.address = address
        self.buffer = b""
        self.authenticated = False

    def run(self):
        while True:
            data = self.client.recv(4096)

            if not data:
                break

            self.buffer += data

            while b"\n" in self.buffer:
                message, self.buffer = self.buffer.split(b"\n", 1)

                self.handle_message(message)

    def handle_message(self, message):
        if message == b"PING":
            self.client.sendall(b"PONG\n")
```

Aqui já temos uma separação interessante:

```text
ClientHandler
│
├── socket
├── endereço
├── buffer
├── estado
├── recebimento
└── processamento
```

---

# 31.11 Tratando erros no limite da conexão

Um erro importante de arquitetura é deixar uma exceção destruir o servidor inteiro.

Por exemplo:

```python
while True:
    client, address = server.accept()

    handle_client(client)
```

Se `handle_client()` gerar uma exceção não tratada:

```text
cliente malformado
       ↓
exceção
       ↓
servidor inteiro encerra
```

O ideal é criar uma fronteira de erro:

```python
while True:
    client, address = server.accept()

    try:
        handle_client(client)

    except Exception:
        logging.exception("Erro no cliente")

    finally:
        client.close()
```

Assim:

```text
Cliente A
   ↓
erro
   ↓
conexão A encerrada

Servidor
   ↓
continua funcionando

Cliente B
   ↓
aceito normalmente
```

---

# 31.12 Concorrência entra nesse ponto

Em um servidor sequencial:

```text
accept()
   ↓
cliente A
   ↓
handle A
   ↓
fim
   ↓
accept()
   ↓
cliente B
```

Se A ficar parado por muito tempo:

```text
Cliente A
   │
   └── recv() bloqueado
          │
          X
     servidor não processa B
```

Com threads:

```text
             ┌── Thread A → Cliente A
             │
Servidor ────┼── Thread B → Cliente B
             │
             └── Thread C → Cliente C
```

Com `selectors`:

```text
              ┌── Cliente A
              │
Selector ─────┼── Cliente B
              │
              └── Cliente C
```

Com `asyncio`:

```text
             Event Loop
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
     Task A    Task B    Task C
```

A arquitetura da aplicação pode continuar semelhante.

O que muda principalmente é **como as conexões são atendidas**.

---

# 31.13 Uma arquitetura funcional simples

Para um servidor pequeno, podemos começar com:

```python
import socket
import logging


HOST = "127.0.0.1"
PORT = 4444


def handle_client(client, address):
    try:
        client.settimeout(30)

        while True:
            data = client.recv(4096)

            if not data:
                break

            if data == b"PING\n":
                client.sendall(b"PONG\n")

            else:
                client.sendall(b"UNKNOWN\n")

    except socket.timeout:
        logging.info("Timeout: %s", address)

    except ConnectionResetError:
        logging.info("Conexão resetada: %s", address)

    except OSError:
        logging.exception("Erro de socket: %s", address)

    finally:
        client.close()


def run_server():
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    server.setsockopt(
        socket.SOL_SOCKET,
        socket.SO_REUSEADDR,
        1,
    )

    server.bind((HOST, PORT))
    server.listen()

    logging.info("Servidor ouvindo em %s:%s", HOST, PORT)

    try:
        while True:
            client, address = server.accept()

            logging.info("Cliente conectado: %s", address)

            handle_client(client, address)

    except KeyboardInterrupt:
        logging.info("Servidor encerrado")

    finally:
        server.close()


logging.basicConfig(
    level=logging.INFO,
)

run_server()
```

Esse servidor ainda é sequencial, mas sua estrutura já está organizada:

```text
run_server()
│
├── cria socket
├── configura socket
├── bind
├── listen
│
└── loop
    │
    ├── accept
    │
    └── handle_client
        │
        ├── recv
        ├── protocolo
        ├── sendall
        └── close
```

---

# 31.14 Evoluindo para múltiplos clientes

Podemos alterar somente a parte responsável pela concorrência.

Por exemplo:

```python
import threading

while True:
    client, address = server.accept()

    thread = threading.Thread(
        target=handle_client,
        args=(client, address),
        daemon=True,
    )

    thread.start()
```

Agora:

```text
                 Server
                   │
                accept()
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
    Thread A   Thread B   Thread C
        │          │          │
    Cliente A  Cliente B  Cliente C
```

A função `handle_client()` pode continuar sendo praticamente a mesma.

Isso demonstra uma vantagem de separar responsabilidades:

> A lógica de uma conexão não precisa conhecer todos os detalhes de como o servidor distribui as conexões.

---

# 31.15 Limites de recursos

Um servidor real precisa pensar em recursos.

Por exemplo:

```text
Quantidade de clientes
Tamanho máximo de mensagem
Tamanho máximo de arquivo
Quantidade de threads
Tempo máximo de conexão
Tempo máximo sem atividade
Memória utilizada
Número de requisições
```

Sem limites, um cliente malicioso pode tentar:

```text
enviar mensagem gigantesca
        ↓
buffer cresce
        ↓
memória cresce
        ↓
servidor pode sofrer DoS
```

Por isso:

```python
MAX_MESSAGE_SIZE = 1024 * 1024
```

Podemos verificar:

```python
if len(buffer) > MAX_MESSAGE_SIZE:
    raise ValueError("Mensagem muito grande")
```

O valor correto depende da aplicação.

---

# 31.16 Graceful shutdown

Um servidor também precisa saber como encerrar corretamente.

Um encerramento organizado pode ser:

```text
Receber sinal de encerramento
          │
          ▼
Parar de aceitar novos clientes
          │
          ▼
Finalizar conexões existentes
          │
          ▼
Fechar sockets
          │
          ▼
Liberar recursos
          │
          ▼
Encerrar processo
```

Isso é diferente de simplesmente matar o processo.

Em sistemas maiores, o shutdown pode precisar lidar com:

```text
threads
tasks
arquivos
banco de dados
filas
sockets
logs
```

---

# 31.17 Onde o TLS entra?

Se o servidor precisar de comunicação criptografada:

```text
Aplicação
    │
    ▼
Protocolo
    │
    ▼
TLS
    │
    ▼
TCP
    │
    ▼
IP
```

O fluxo continua conceitualmente igual:

```python
client.recv(...)
client.sendall(...)
```

A diferença é que o socket TCP é envolvido por TLS.

Isso mostra novamente a importância das camadas:

```text
Aplicação
    ↓
Protocolo
    ↓
TLS
    ↓
TCP
    ↓
IP
```

Cada camada possui uma responsabilidade diferente.

---

# 31.18 Arquitetura completa em visão geral

Podemos representar um servidor TCP mais completo assim:

```text
                         SERVIDOR
                            │
                ┌───────────▼───────────┐
                │   Listening Socket    │
                │ socket/bind/listen    │
                └───────────┬───────────┘
                            │
                         accept()
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
         Cliente A      Cliente B      Cliente C
             │              │              │
             ▼              ▼              ▼
         Handler A      Handler B      Handler C
             │              │              │
             ▼              ▼              ▼
          Buffer A       Buffer B       Buffer C
             │              │              │
             ▼              ▼              ▼
          Parser A       Parser B       Parser C
             │              │              │
             └──────────────┼──────────────┘
                            ▼
                    Lógica da aplicação
                            │
                            ▼
                       Resposta
                            │
                            ▼
                     sendall()/TLS
```

Essa é uma visão muito mais próxima de como devemos pensar em servidores reais.

---

# 31.19 O que deve ficar separado

Uma boa regra mental é:

```text
Socket
    ↓
transporta bytes

Framing
    ↓
define onde começa/termina uma mensagem

Protocolo
    ↓
define o significado da mensagem

Aplicação
    ↓
define o que fazer

Concorrência
    ↓
define como atender vários clientes

Segurança
    ↓
define como proteger comunicação e recursos

Observabilidade
    ↓
logs, métricas e diagnóstico
```

Não devemos esperar que o TCP resolva responsabilidades da aplicação.

---

# 31.20 Modelo mental definitivo

Quando você olhar para um servidor TCP, pense nesta sequência:

```text
1. CRIAR
   socket()

2. CONFIGURAR
   setsockopt()

3. ASSOCIAR
   bind()

4. ESCUTAR
   listen()

5. ACEITAR
   accept()

6. RECEBER
   recv()

7. INTERPRETAR
   protocolo/framing

8. PROCESSAR
   lógica da aplicação

9. RESPONDER
   send()/sendall()

10. REPETIR
    novas mensagens

11. ENCERRAR
    shutdown()/close()
```

E para múltiplos clientes:

```text
                    SERVIDOR
                       │
                    accept()
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Cliente A Cliente B Cliente C
             │         │         │
          handler    handler    handler
             │         │         │
             └─────────┼─────────┘
                       ▼
                aplicação
```

A grande evolução em relação aos primeiros exemplos de socket é perceber que **o socket é apenas uma parte da arquitetura**.

---

## Resumo da Parte

- Um servidor TCP possui um **listening socket** e sockets individuais para cada cliente.
    
- `accept()` cria/retorna o socket responsável pela comunicação com aquele cliente.
    
- O listening socket continua disponível para novas conexões.
    
- É importante separar:
    
    - transporte;
        
    - framing;
        
    - protocolo;
        
    - lógica da aplicação;
        
    - concorrência;
        
    - segurança;
        
    - observabilidade.
        
- TCP transporta bytes; ele não conhece comandos como `PING`, `LOGIN` ou `GET`.
    
- Cada conexão pode possuir seu próprio buffer e estado.
    
- Uma conexão pode passar por estados como autenticação e sessão ativa.
    
- Erros de um cliente não devem necessariamente derrubar o servidor inteiro.
    
- Servidores reais precisam impor limites de recursos.
    
- Threads, `selectors` e `asyncio` são formas diferentes de atender múltiplas conexões.
    
- TLS pode ser colocado acima do TCP e abaixo da aplicação.
    
- Um servidor bem projetado separa responsabilidades para facilitar manutenção, testes e segurança.
    

**Modelo principal:**

```text
socket
  ↓
configuração
  ↓
bind
  ↓
listen
  ↓
accept
  ↓
conexão do cliente
  ↓
receber bytes
  ↓
framing
  ↓
protocolo
  ↓
lógica da aplicação
  ↓
resposta
  ↓
envio
  ↓
encerramento
```

---