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

