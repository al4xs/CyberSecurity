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