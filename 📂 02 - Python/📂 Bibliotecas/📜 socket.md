A biblioteca `socket` fornece uma interface de baixo nível para comunicação de rede em Python.

Ela permite trabalhar diretamente com conceitos como:

- IP;
    
- portas;
    
- TCP;
    
- UDP;
    
- IPv4;
    
- IPv6;
    
- conexões;
    
- fluxo de bytes;
    
- datagramas;
    
- DNS;
    
- opções de socket;
    
- timeouts;
    
- comunicação concorrente;
    
- TLS;
    
- protocolos de aplicação;
    
- e, em determinadas plataformas, acesso a mecanismos de rede de baixo nível.
    

Para quem estuda **Python + Redes + Cybersecurity/Pentest**, entender `socket` é importante porque muitas ferramentas de rede nada mais fazem do que utilizar esses mesmos conceitos em níveis diferentes de abstração.

Por exemplo:

```text
requests
   │
   ▼
HTTP
   │
   ▼
TLS / HTTPS
   │
   ▼
TCP
   │
   ▼
IP
   │
   ▼
Ethernet
```

Quando utilizamos `socket`, podemos trabalhar muito mais próximo da camada de transporte e, em determinadas situações, até de camadas inferiores.

---

# 1. Fundamentos

## 1.1 O que é um socket?

Um socket é uma interface de comunicação disponibilizada pelo sistema operacional para que um processo possa enviar e receber dados através de uma comunicação.

Uma forma simples de imaginar:

```text
Aplicação Python
       │
       ▼
    socket
       │
       ▼
Sistema operacional
       │
       ▼
  pilha de rede
       │
       ▼
     rede
```

O socket não é o próprio TCP.

O socket é uma **interface de programação** através da qual o programa utiliza os mecanismos de comunicação oferecidos pelo sistema operacional.

Quando fazemos:

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

estamos pedindo ao sistema operacional algo equivalente a:

> Crie para mim um endpoint de comunicação IPv4 baseado em fluxo.

---

# 1.2 Socket, IP e porta

Esses conceitos são diferentes.

## IP

Identifica um endereço de rede.

Exemplo:

```text
192.168.1.20
```

## Porta

Identifica um endpoint lógico dentro daquele host.

Exemplo:

```text
443
```

## Socket

É o objeto/interface utilizado pelo programa para trabalhar com aquela comunicação.

Podemos pensar:

```text
IP
 │
 ├── porta 22
 ├── porta 80
 ├── porta 443
 └── porta 8080
```

Uma conexão TCP tradicional pode ser identificada pelo conjunto:

```text
IP origem
porta origem
IP destino
porta destino
protocolo
```

Por exemplo:

```text
192.168.1.10:53142
        │
        │ TCP
        ▼
192.168.1.20:443
```

---

# 1.3 Cliente e servidor

Uma aplicação de rede frequentemente utiliza o modelo:

```text
             SERVIDOR
                │
                │
         aguarda conexões
                │
                ▼
             CLIENTE
```

O servidor normalmente:

```python
socket()
bind()
listen()
accept()
recv()/sendall()
```

O cliente normalmente:

```python
socket()
connect()
sendall()/recv()
```

Isso não significa que cliente só recebe ou servidor só envia.

Depois que a conexão TCP foi estabelecida, ambos os lados podem enviar e receber dados.

```text
CLIENTE                         SERVIDOR
   │                               │
   │────── dados ────────────────►│
   │                               │
   │◄───── resposta ──────────────│
   │                               │
   │────── dados ────────────────►│
   │                               │
```

---

# 1.4 O que significa `AF_INET`?

```python
socket.AF_INET
```

Representa a família de endereços IPv4.

Exemplo:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

Aqui:

```text
AF_INET
   │
   ▼
IPv4
```

Um endereço IPv4 possui quatro octetos:

```text
192.168.1.10
```

---

# 1.5 `AF_INET6`

```python
socket.AF_INET6
```

Representa IPv6.

Exemplo:

```python
socket.socket(
    socket.AF_INET6,
    socket.SOCK_STREAM
)
```

Um endereço IPv6 pode ser:

```text
2001:db8::10
```

Um detalhe importante é que a estrutura do endereço IPv6 utilizada por vários métodos é diferente.

IPv4:

```python
('127.0.0.1', 4444)
```

IPv6:

```python
('::1', 4444, 0, 0)
```

Os campos adicionais estão relacionados a `flowinfo` e `scope_id`.

---

# 1.6 `SOCK_STREAM`

```python
socket.SOCK_STREAM
```

Representa um socket orientado a fluxo.

No uso mais comum:

```text
SOCK_STREAM
     │
     ▼
    TCP
```

TCP fornece um fluxo de bytes ordenado entre os endpoints.

Isso não significa que TCP preserve as chamadas `send()` realizadas pela aplicação.

Por exemplo:

```python
send(b'ABC')
send(b'DEF')
```

não significa que o receptor obrigatoriamente fará:

```python
recv() -> b'ABC'
recv() -> b'DEF'
```

Ele pode receber:

```text
ABCDEF
```

ou:

```text
ABC
DEF
```

ou até partes menores.

Esse conceito é fundamental.

---

# 1.7 `SOCK_DGRAM`

```python
socket.SOCK_DGRAM
```

É normalmente utilizado com UDP.

```text
SOCK_DGRAM
     │
     ▼
    UDP
```

Diferentemente do TCP, UDP trabalha com datagramas.

A aplicação envia:

```text
DATAGRAMA 1
DATAGRAMA 2
DATAGRAMA 3
```

O receptor recebe datagramas individualmente.

UDP não fornece, por si só, as garantias de entrega ordenada e retransmissão do TCP.

---

# 1.8 `SOCK_RAW`

```python
socket.SOCK_RAW
```

Permite acesso muito mais próximo dos pacotes de rede.

É utilizado em determinados tipos de:

- análise de pacotes;
    
- desenvolvimento de ferramentas de rede;
    
- ICMP;
    
- pesquisa;
    
- protocolos de baixo nível;
    
- ferramentas de diagnóstico;
    
- segurança.
    

Exemplo conceitual:

```python
raw = socket.socket(
    socket.AF_INET,
    socket.SOCK_RAW,
    socket.IPPROTO_ICMP
)
```

Raw sockets possuem restrições importantes e, dependendo da operação e do sistema operacional, podem exigir privilégios elevados.

---

# 1.9 `SOCK_SEQPACKET`

```python
socket.SOCK_SEQPACKET
```

Representa um socket orientado a conexão que preserva limites de mensagens.

Não é o tipo utilizado normalmente para TCP/IP tradicional.

Sua disponibilidade e utilidade dependem da família de sockets e do sistema operacional.

Por isso:

```text
SOCK_STREAM
```

é muito mais comum em aplicações TCP.

---

# 1.10 Combinações comuns

|Família|Tipo|Uso típico|
|---|---|---|
|`AF_INET`|`SOCK_STREAM`|TCP/IPv4|
|`AF_INET`|`SOCK_DGRAM`|UDP/IPv4|
|`AF_INET`|`SOCK_RAW`|Pacotes IPv4 de baixo nível|
|`AF_INET6`|`SOCK_STREAM`|TCP/IPv6|
|`AF_INET6`|`SOCK_DGRAM`|UDP/IPv6|
|`AF_UNIX`|`SOCK_STREAM`|Comunicação local entre processos|

A disponibilidade de famílias e tipos pode variar conforme o sistema operacional.

---

# 2. Criando um socket

A função principal é:

```python
socket.socket()
```

Forma comum:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

A assinatura conceitual é:

```python
socket.socket(
    family=AF_INET,
    type=SOCK_STREAM,
    proto=0,
    fileno=None
)
```

---

## `family`

Determina a família de endereços.

Exemplos:

```python
socket.AF_INET
socket.AF_INET6
socket.AF_UNIX
```

---

## `type`

Determina o tipo do socket.

Exemplos:

```python
socket.SOCK_STREAM
socket.SOCK_DGRAM
socket.SOCK_RAW
```

---

## `proto`

Permite especificar um protocolo.

Na maioria dos casos comuns:

```python
proto=0
```

é suficiente.

Quando necessário, podem ser utilizados valores como:

```python
socket.IPPROTO_TCP
socket.IPPROTO_UDP
socket.IPPROTO_ICMP
```

---

## `fileno`

Permite criar um objeto `socket` a partir de um descritor de arquivo existente.

É um recurso mais avançado e aparece em situações envolvendo:

- integração com APIs do sistema operacional;
    
- herança de descritores;
    
- processos;
    
- servidores avançados;
    
- gerenciamento de recursos de baixo nível.
    

---

# 3. Servidor TCP

O ciclo tradicional é:

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
recv()/send()
   │
   ▼
close()
```

---

# 4. `bind()`

Associa o socket a um endereço local.

```python
server.bind(('127.0.0.1', 4444))
```

A tupla:

```python
('127.0.0.1', 4444)
```

contém:

```text
IP
 │
 ▼
127.0.0.1

porta
 │
 ▼
4444
```

---

## `127.0.0.1`

Representa o loopback IPv4.

```text
máquina
   │
   ├── aplicação cliente
   │
   └── aplicação servidor
          │
          ▼
      127.0.0.1
```

A comunicação permanece na própria máquina.

---

# 4.1 `0.0.0.0`

É comum um servidor utilizar:

```python
server.bind(('0.0.0.0', 4444))
```

Nesse contexto, o endereço representa todas as interfaces IPv4 locais.

Por exemplo:

```text
Wi-Fi       192.168.1.10
Ethernet    10.0.0.5
Loopback    127.0.0.1
```

Um socket associado a:

```text
0.0.0.0:4444
```

pode escutar através dessas interfaces, sujeito às regras do sistema e firewall.

`0.0.0.0` não significa que o cliente deve conectar em `0.0.0.0`.

---

# 5. `listen()`

Coloca um socket TCP em estado de escuta.

```python
server.listen()
```

Também podemos fornecer um backlog:

```python
server.listen(5)
```

O backlog está relacionado à fila de conexões pendentes.

Não deve ser interpretado simplesmente como:

> "o servidor só pode ter 5 clientes."

O socket que escuta e os sockets das conexões aceitas são recursos diferentes.

---

# 6. `accept()`

Aceita uma conexão pendente.

```python
client, address = server.accept()
```

Retorna:

```python
(conn, address)
```

Por exemplo:

```python
client, address = server.accept()

print(address)
```

pode produzir:

```text
('127.0.0.1', 53218)
```

---

# 6.1 Dois sockets diferentes

Depois de:

```python
client, address = server.accept()
```

temos:

```text
server
   │
   └── socket de escuta

client
   │
   └── socket da conexão específica
```

O servidor deve utilizar:

```python
client.recv()
client.sendall()
```

para conversar com aquele cliente.

Não:

```python
server.recv()
```

O socket de escuta existe para aceitar novas conexões.

---

# 7. `connect()`

Utilizado pelo cliente.

```python
client.connect(('127.0.0.1', 4444))
```

O cliente solicita uma conexão com:

```text
127.0.0.1:4444
```

Em TCP, essa chamada está associada ao estabelecimento da conexão.

---

# 8. TCP por baixo do código

Quando fazemos:

```python
client.connect(('192.168.1.20', 4444))
```

não acontece simplesmente:

```text
connect()
   ↓
"conectado"
```

Existe um processo de estabelecimento da conexão TCP.

---

# 8.1 Three-way handshake

Simplificadamente:

```text
CLIENTE                         SERVIDOR

   SYN ────────────────────────►

       ◄──────────────── SYN-ACK

   ACK ────────────────────────►

          CONEXÃO ESTABELECIDA
```

## SYN

O cliente envia um segmento com SYN para iniciar a conexão.

## SYN-ACK

O servidor responde indicando que recebeu o SYN e também apresenta seu próprio SYN.

## ACK

O cliente confirma o recebimento.

Depois disso, a conexão TCP pode transportar dados.

---

# 8.2 Sequence numbers

TCP utiliza números de sequência para acompanhar os bytes transmitidos.

Isso permite ao protocolo:

- identificar posição dos dados;
    
- detectar segmentos faltantes;
    
- ordenar dados;
    
- reconhecer recebimentos;
    
- auxiliar na retransmissão.
    

A aplicação Python normalmente não manipula esses números diretamente.

Eles são tratados pela implementação TCP do sistema operacional.

---

# 8.3 ACK

ACK significa acknowledgement.

É uma confirmação de recebimento no protocolo TCP.

Isso permite que TCP acompanhe o progresso da comunicação.

---

# 8.4 Retransmissão

Se determinados dados não forem reconhecidos como esperado, TCP pode retransmiti-los.

Isso contribui para a confiabilidade do fluxo.

Importante:

```text
Python
  │
  ▼
socket.sendall()
  │
  ▼
TCP do sistema operacional
  │
  ▼
rede
```

A aplicação não fica implementando manualmente toda a confiabilidade do TCP.

---

# 8.5 Controle de fluxo

TCP precisa evitar que um transmissor envie dados mais rapidamente do que o receptor consegue processar.

Para isso existe controle de fluxo.

O conceito está relacionado à janela de recepção.

---

# 8.6 Controle de congestionamento

Além do receptor, existe a própria rede.

Uma rede congestionada não deve ser inundada indefinidamente por um transmissor.

TCP possui mecanismos de controle de congestionamento para adaptar a transmissão às condições observadas.

Isso é diferente de controle de fluxo:

```text
Controle de fluxo
→ capacidade do receptor

Controle de congestionamento
→ capacidade/condições da rede
```

---

# 9. Recebendo dados com `recv()`

```python
data = client.recv(1024)
```

O argumento:

```python
1024
```

representa a quantidade máxima de bytes solicitada naquela chamada.

Não significa:

```text
"espere exatamente 1024 bytes"
```

Pode retornar:

```text
10 bytes
500 bytes
1024 bytes
```

ou outros valores válidos conforme o estado da conexão.

---

# 9.1 `recv()` é bloqueante?

Por padrão, sockets são bloqueantes.

Então:

```python
data = client.recv(1024)
```

pode fazer o programa esperar até que haja dados disponíveis ou a conexão seja encerrada.

---

# 9.2 O outro lado fechou

Se o peer encerra uma conexão TCP de forma ordenada, uma leitura pode retornar:

```python
b''
```

Por isso é comum:

```python
data = client.recv(1024)

if not data:
    break
```

---

# 10. `send()`

```python
sent = client.send(data)
```

Retorna a quantidade de bytes enviados naquela chamada.

Por exemplo:

```python
data = b'A' * 10000

sent = client.send(data)

print(sent)
```

Pode ser menor que `10000`.

Por isso, código que precisa garantir o envio completo precisa lidar com envio parcial.

---

# 11. `sendall()`

```python
client.sendall(data)
```

Continua enviando até que todos os bytes sejam enviados ou ocorra um erro.

Isso torna `sendall()` conveniente para aplicações comuns.

Porém, existe uma consequência importante:

Em caso de erro, a aplicação não recebe uma informação simples dizendo exatamente quantos bytes foram transmitidos com sucesso antes do erro.

---

# 12. `send()` x `sendall()`

|Método|Comportamento|
|---|---|
|`send()`|Pode enviar apenas parte dos dados|
|`sendall()`|Continua tentando enviar todos os dados|
|`sendto()`|Envia para um endereço específico, comum em UDP|

---

# 13. TCP não envia mensagens

Este é um dos conceitos mais importantes de toda a biblioteca.

TCP é um:

> **byte stream**

Não existe, para TCP, o conceito de:

```text
Mensagem 1
Mensagem 2
Mensagem 3
```

como existe na camada da aplicação.

Se o cliente fizer:

```python
client.sendall(b'HELLO')
client.sendall(b'WORLD')
```

o servidor pode receber:

```text
HELLOWORLD
```

em uma única chamada.

Ou:

```text
HEL
```

depois:

```text
LOWORLD
```

Ou outra divisão.

Portanto:

```python
sendall()
```

não cria fronteiras de mensagem.

---

# 14. Framing

Se nossa aplicação precisa saber onde uma mensagem termina, precisamos criar um protocolo de framing.

Existem várias estratégias.

---

# 14.1 Tamanho fixo

Exemplo:

```text
Cada mensagem possui 100 bytes
```

Problema:

- desperdício;
    
- limite fixo;
    
- mensagens maiores exigem fragmentação própria.
    

---

# 14.2 Delimitador

Podemos utilizar:

```text
Olá\n
Tudo bem?\n
```

O receptor lê até encontrar:

```text
\n
```

Muito utilizado em protocolos textuais.

---

# 14.3 Length-prefix

Uma estratégia mais robusta é:

```text
[ tamanho ][ payload ]
```

Por exemplo:

```text
[00000005][HELLO]
```

Ou em formato binário:

```text
[4 bytes tamanho][payload]
```

---

# 15. Implementando um protocolo com length-prefix

```python
import socket
import struct


def send_message(sock, message):
    data = message.encode()

    header = struct.pack('!I', len(data))

    sock.sendall(header)
    sock.sendall(data)


def recv_exact(sock, size):
    data = bytearray()

    while len(data) < size:
        chunk = sock.recv(size - len(data))

        if not chunk:
            raise ConnectionError('Conexão encerrada.')

        data.extend(chunk)

    return bytes(data)


def recv_message(sock):
    header = recv_exact(sock, 4)

    size = struct.unpack('!I', header)[0]

    data = recv_exact(sock, size)

    return data.decode()
```

Agora temos:

```text
HEADER
  │
  │ 4 bytes
  ▼
+----------+
| tamanho  |
+----------+
     │
     ▼
+----------------+
|    payload     |
+----------------+
```

---

# 15.1 Por que `recv_exact()` existe?

Porque:

```python
sock.recv(4)
```

não garante que receberemos exatamente quatro bytes.

Podemos receber:

```text
2 bytes
```

e depois:

```text
2 bytes
```

Por isso precisamos acumular:

```python
while len(data) < size:
```

Esse padrão é extremamente importante em programação de rede.

---

# 16. `struct`

O módulo `struct` permite converter valores Python para representações binárias estruturadas.

Neste exemplo:

```python
struct.pack('!I', len(data))
```

O formato:

```text
!
```

indica network byte order.

E:

```text
I
```

representa um inteiro sem sinal de 4 bytes.

Assim:

```text
Python inteiro
     │
     ▼
4 bytes
     │
     ▼
rede
```

---

# 17. `sendto()`

É utilizado principalmente com UDP.

```python
sock.sendto(
    b'Hello',
    ('127.0.0.1', 9999)
)
```

Aqui o destino é informado diretamente.

Em contraste:

```python
sock.sendall(b'Hello')
```

é normalmente utilizado em um socket conectado.

---

# 18. `recvfrom()`

Usado frequentemente com UDP.

```python
data, address = sock.recvfrom(1024)
```

Retorna:

```text
data
address
```

Exemplo:

```python
print(data.decode())
print(address)
```

O servidor UDP consegue descobrir de qual endereço veio aquele datagrama.

---

# 19. Exemplo UDP

## Servidor

```python
import socket

server = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

server.bind(('127.0.0.1', 9999))

while True:
    data, address = server.recvfrom(1024)

    print(f'{address}: {data.decode()}')

    server.sendto(
        b'Recebido!',
        address
    )
```

## Cliente

```python
import socket

client = socket.socket(
    socket.AF_INET,
    socket.SOCK_DGRAM
)

client.sendto(
    b'Hello UDP',
    ('127.0.0.1', 9999)
)

data, address = client.recvfrom(1024)

print(data.decode())
```

---

# 20. TCP x UDP

|Característica|TCP|UDP|
|---|---|---|
|Conexão|Orientado a conexão|Sem conexão|
|Modelo|Fluxo de bytes|Datagramas|
|Ordem|Mantida pelo protocolo|Não garantida|
|Retransmissão|Sim|Não|
|Controle de congestionamento|Sim|Não no UDP básico|
|`connect()`|Sim|Opcional|
|`accept()`|Sim|Não|
|`sendall()`|Sim|Não é o método típico|
|`sendto()`|Não é o padrão|Sim|
|`recvfrom()`|Não é o padrão|Sim|
|Overhead|Maior|Menor|

---

# 21. Quando UDP pode ser melhor?

UDP pode ser interessante quando a aplicação prefere:

```text
menor overhead
+
baixa latência
+
controle próprio
```

Exemplos de áreas que podem utilizar UDP:

- DNS;
    
- streaming;
    
- jogos;
    
- descoberta de serviços;
    
- certos protocolos de voz/vídeo;
    
- aplicações onde perda ocasional pode ser preferível a esperar retransmissões.
    

Isso não significa:

> UDP é sempre mais rápido.

A escolha depende da aplicação.

---

# 22. `settimeout()`

Podemos definir um timeout:

```python
sock.settimeout(5)
```

Isso significa que operações bloqueantes do socket passam a ter um limite de espera.

Exemplo:

```python
import socket

sock = socket.socket()

sock.settimeout(3)

try:
    data = sock.recv(1024)
except socket.timeout:
    print('Tempo limite atingido.')
```

Isso é extremamente útil em ferramentas de rede.

Por exemplo, um scanner não pode ficar eternamente esperando cada porta.

---

# 23. `gettimeout()`

Retorna o timeout atual:

```python
timeout = sock.gettimeout()

print(timeout)
```

Pode retornar:

```text
None
```

quando o socket está em modo bloqueante sem timeout.

---

# 24. `setblocking()`

Podemos controlar o modo de bloqueio.

```python
sock.setblocking(True)
```

Modo não bloqueante:

```python
sock.setblocking(False)
```

É equivalente conceitualmente a configurar:

```python
sock.settimeout(0.0)
```

para modo não bloqueante.

---

# 25. Blocking x Non-blocking

## Blocking

```python
data = sock.recv(1024)
```

Pode esperar.

```text
recv()
 │
 │ aguardando
 │
 │ aguardando
 │
 ▼
dados disponíveis
```

## Non-blocking

```python
sock.setblocking(False)
```

A chamada retorna imediatamente.

Se não houver dados disponíveis, pode ocorrer:

```python
BlockingIOError
```

Isso é importante para servidores que precisam acompanhar muitos sockets.

---

# 26. `selectors`

Uma alternativa é utilizar:

```python
import selectors
```

O conceito:

```text
              selector
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
    socket1   socket2   socket3
       │         │         │
     pronto    pronto    esperando
```

O sistema operacional informa quais sockets podem ser processados sem bloquear.

Isso permite construir servidores concorrentes sem necessariamente criar uma thread para cada conexão.

---

# 27. `threading`

Modelo simples:

```text
Cliente 1 ──► Thread 1
Cliente 2 ──► Thread 2
Cliente 3 ──► Thread 3
```

Exemplo conceitual:

```python
threading.Thread(
    target=handle_client,
    args=(client,)
).start()
```

É simples de entender, mas milhares de conexões podem tornar esse modelo menos eficiente dependendo da aplicação.

---

# 28. `asyncio`

Outro modelo:

```text
event loop
    │
    ├── cliente 1
    ├── cliente 2
    ├── cliente 3
    ├── cliente 4
    └── cliente 5
```

Utiliza programação assíncrona.

É particularmente interessante para aplicações com muitas operações de I/O.

---

# 29. Comparação de concorrência

|Modelo|Característica|
|---|---|
|Sequencial|Simples, poucos clientes|
|`threading`|Fácil para múltiplas conexões|
|`selectors`|I/O multiplexing|
|`asyncio`|Programação assíncrona|

---

# 30. Race conditions

Quando múltiplas threads acessam o mesmo estado, pode ocorrer uma race condition.

Exemplo:

```python
clients = []
```

Duas threads podem tentar modificar essa estrutura simultaneamente.

Dependendo do código, pode ser necessário utilizar mecanismos de sincronização.

---

# 31. Deadlock

Um deadlock acontece quando tarefas ficam esperando umas pelas outras indefinidamente.

Exemplo conceitual:

```text
Thread A
   │
   └── espera recurso B

Thread B
   │
   └── espera recurso A
```

Nenhuma consegue continuar.

---

# 32. `getsockname()`

Retorna o endereço local do socket.

```python
sock.getsockname()
```

Exemplo:

```text
('127.0.0.1', 4444)
```

Útil para descobrir:

- IP local;
    
- porta local;
    
- endereço efetivamente associado.
    

---

# 33. `getpeername()`

Retorna o endereço do peer conectado.

```python
sock.getpeername()
```

Exemplo:

```text
('192.168.1.50', 52134)
```

Comparação:

```text
getsockname()
     │
     ▼
meu endereço

getpeername()
     │
     ▼
endereço do outro lado
```

---

# 34. `getsockopt()`

Consulta uma opção do socket.

```python
sock.getsockopt(
    socket.SOL_SOCKET,
    socket.SO_KEEPALIVE
)
```

---

# 35. `setsockopt()`

Modifica uma opção.

```python
sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_KEEPALIVE,
    1
)
```

Estrutura:

```python
sock.setsockopt(
    level,
    option,
    value
)
```

---

# 36. `SO_REUSEADDR`

Uma opção muito conhecida:

```python
server.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_REUSEADDR,
    1
)
```

Pode permitir que um endereço local seja reutilizado em determinadas situações.

É especialmente comum em servidores que são reiniciados rapidamente.

---

# 37. `Address already in use`

Erro:

```text
OSError: [Errno 98] Address already in use
```

Pode ocorrer quando:

```python
server.bind(('127.0.0.1', 4444))
```

tenta associar uma porta que ainda está ocupada ou não pode ser reutilizada naquele momento.

Investigue primeiro:

```bash
ss -ltnp | grep 4444
```

ou:

```bash
lsof -i :4444
```

Não devemos simplesmente colocar:

```python
SO_REUSEADDR
```

sem entender o problema.

Essa opção não significa:

> "forçar qualquer processo a usar a porta."

Ela modifica regras de reutilização do endereço conforme o comportamento do sistema operacional.

---

# 38. `SO_REUSEPORT`

Permite, em sistemas que suportam a opção, comportamentos de reutilização de porta diferentes de `SO_REUSEADDR`.

Seu funcionamento é dependente do sistema operacional.

Pode ser utilizado em arquiteturas específicas envolvendo múltiplos sockets associados ao mesmo endereço/porta.

Não deve ser tratado como simplesmente:

```text
SO_REUSEADDR = versão básica
SO_REUSEPORT = versão melhor
```

São mecanismos diferentes.

---

# 39. `SO_KEEPALIVE`

Ativa mecanismos de keepalive no socket TCP:

```python
sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_KEEPALIVE,
    1
)
```

Pode ajudar a detectar conexões que ficaram aparentemente abertas, mas cujo peer não está mais acessível.

O comportamento detalhado depende do sistema operacional e de outros parâmetros.

---

# 40. `SO_BROADCAST`

Permite determinadas operações de broadcast em sockets apropriados.

```python
sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_BROADCAST,
    1
)
```

É relevante principalmente para UDP.

Exemplo conceitual:

```text
cliente
   │
   │ broadcast
   ▼
rede local
 ┌─┼──────┐
 ▼ ▼      ▼
PC PC     PC
```

---

# 41. `SO_RCVBUF`

Relaciona-se ao buffer de recepção.

```python
sock.getsockopt(
    socket.SOL_SOCKET,
    socket.SO_RCVBUF
)
```

Pode ser configurado:

```python
sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_RCVBUF,
    65536
)
```

O valor efetivo pode depender do sistema operacional.

---

# 42. `SO_SNDBUF`

Relaciona-se ao buffer de envio.

```python
sock.setsockopt(
    socket.SOL_SOCKET,
    socket.SO_SNDBUF,
    65536
)
```

Novamente, o sistema operacional pode aplicar suas próprias regras.

---

# 43. `SO_RCVTIMEO` e `SO_SNDTIMEO`

São opções relacionadas a timeout de recepção e transmissão em plataformas que as suportam.

Em Python, para aplicações portáveis, frequentemente é mais simples utilizar:

```python
sock.settimeout(5)
```

---

# 44. `TCP_NODELAY`

Relacionado ao algoritmo de Nagle em TCP.

```python
sock.setsockopt(
    socket.IPPROTO_TCP,
    socket.TCP_NODELAY,
    1
)
```

Desabilitar Nagle pode reduzir determinadas latências para pequenos envios, mas pode aumentar o número de segmentos e overhead.

Não é:

> "sempre deixe TCP_NODELAY ligado."

É uma decisão de engenharia.

---

# 45. `shutdown()`

Permite encerrar uma direção da comunicação.

```python
sock.shutdown(how)
```

Valores:

```python
socket.SHUT_RD
socket.SHUT_WR
socket.SHUT_RDWR
```

---

# 46. `SHUT_RD`

```python
sock.shutdown(socket.SHUT_RD)
```

Indica que o socket não será mais utilizado para leitura.

---

# 47. `SHUT_WR`

```python
sock.shutdown(socket.SHUT_WR)
```

Indica que a aplicação terminou de enviar dados.

Isso é conhecido como **half-close**.

Podemos ter:

```text
CLIENTE
   │
   │ não envia mais
   │
   │────── FIN ──────►
   │
   │ ainda pode receber
   │
   ▼
SERVIDOR
```

---

# 48. `SHUT_RDWR`

Encerra ambas as direções:

```python
sock.shutdown(socket.SHUT_RDWR)
```

---

# 49. `shutdown()` x `close()`

São coisas diferentes.

## `shutdown()`

Controla a comunicação do socket.

```python
sock.shutdown(socket.SHUT_WR)
```

Pode dizer:

> termine minha transmissão, mas ainda quero receber.

## `close()`

Libera o socket/recurso.

```python
sock.close()
```

Em uma aplicação normal:

```text
shutdown()
   │
   ▼
controle da conexão
   │
   ▼
close()
   │
   ▼
liberação do recurso
```

Nem sempre é necessário chamar `shutdown()` antes de `close()`.

---

# 50. TCP FIN

Quando uma conexão é encerrada de maneira ordenada, TCP utiliza FIN.

Simplificadamente:

```text
CLIENTE                    SERVIDOR

FIN ─────────────────────►

    ◄──────────────── ACK

    ◄──────────────── FIN

ACK ─────────────────────►
```

Isso é diferente de um reset abrupto.

---

# 51. TCP RST

RST significa reset.

Pode ocorrer quando uma conexão é rejeitada ou encerrada de maneira abrupta em determinadas situações.

Isso pode aparecer na aplicação como:

```python
ConnectionResetError
```

---

# 52. TIME_WAIT

Após o encerramento de determinadas conexões TCP, um endpoint pode permanecer em:

```text
TIME_WAIT
```

por algum tempo.

Isso é comportamento normal do TCP.

É uma das razões pelas quais servidores podem apresentar situações relacionadas a reutilização de endereço/porta após reinicializações rápidas.

---

# 53. Estados TCP

Entre os estados importantes estão:

```text
LISTEN
SYN-SENT
SYN-RECEIVED
ESTABLISHED
FIN-WAIT-1
FIN-WAIT-2
CLOSE-WAIT
CLOSING
LAST-ACK
TIME-WAIT
CLOSED
```

Podemos visualizar:

```text
LISTEN
  │
  ▼
SYN-RECEIVED
  │
  ▼
ESTABLISHED
  │
  ▼
FIN-WAIT
  │
  ▼
TIME-WAIT
  │
  ▼
CLOSED
```

O fluxo exato depende de qual lado inicia o encerramento.

---

# 54. `recv_into()`

Normalmente:

```python
data = sock.recv(1024)
```

cria/retorna um objeto de bytes.

Com:

```python
buffer = bytearray(1024)

size = sock.recv_into(buffer)
```

os dados são escritos no buffer fornecido.

Isso pode ser útil em situações de otimização ou quando queremos controlar a memória utilizada.

---

# 55. `recvfrom()`

```python
data, address = sock.recvfrom(1024)
```

É particularmente importante para UDP.

---

# 56. `recvmsg()`

Em sistemas que oferecem suporte, `recvmsg()` fornece acesso a recursos mais avançados de mensagens de socket.

Pode retornar:

```python
data, ancdata, flags, address
```

O `ancdata` representa dados auxiliares/ancillary data.

Isso permite funcionalidades avançadas de baixo nível.

---

# 57. `sendmsg()`

Correspondente avançado de envio:

```python
sock.sendmsg(...)
```

Pode trabalhar com:

- múltiplos buffers;
    
- ancillary data;
    
- determinados mecanismos específicos do sistema.
    

Um caso avançado em Unix é passar descritores de arquivo através de `AF_UNIX`.

Isso está muito além de um servidor TCP básico, mas é importante conhecer a existência desse mecanismo quando estudamos sockets em profundidade.

---

# 58. `fileno()`

Retorna o descritor associado ao socket:

```python
fd = sock.fileno()

print(fd)
```

No Linux, pode ser algo como:

```text
3
```

O descritor é um recurso do processo.

Podemos enxergar:

```text
Python
 │
 ▼
socket object
 │
 ▼
file descriptor
 │
 ▼
kernel
```

---

# 59. `detach()`

Remove o descritor de arquivo do objeto socket.

```python
fd = sock.detach()
```

Depois disso, o socket Python deixa de possuir aquele descritor.

É um recurso avançado.

---

# 60. `dup()`

Cria uma duplicação do socket.

```python
new_sock = sock.dup()
```

Útil em situações avançadas envolvendo:

- processos;
    
- descritores;
    
- gerenciamento de recursos.
    

---

# 61. `makefile()`

Permite criar um objeto semelhante a arquivo associado ao socket.

```python
file = sock.makefile('r')
```

Isso pode ser útil para protocolos baseados em linhas.

Por exemplo:

```text
GET / HTTP/1.1
Host: example.com
```

É importante entender que o socket continua sendo um socket; `makefile()` fornece uma interface adicional para leitura/escrita.

---

# 62. `set_inheritable()` e `get_inheritable()`

Controlam se o descritor associado ao socket pode ser herdado por processos filhos em determinados ambientes.

```python
sock.set_inheritable(True)
```

Consulta:

```python
sock.get_inheritable()
```

São recursos de nível mais baixo, mais relevantes em gerenciamento de processos e descritores.

---

# 63. DNS

A biblioteca `socket` também fornece funções relacionadas à resolução de nomes.

Por exemplo:

```python
socket.gethostbyname('example.com')
```

Pode retornar:

```text
93.184.216.34
```

---

# 64. `gethostbyname()`

```python
socket.gethostbyname(hostname)
```

Converte um hostname em um endereço IPv4.

Exemplo:

```python
ip = socket.gethostbyname('example.com')

print(ip)
```

É uma função simples, mas limitada em comparação com `getaddrinfo()`.

---

# 65. `gethostbyname_ex()`

```python
socket.gethostbyname_ex('example.com')
```

Pode fornecer:

- hostname;
    
- aliases;
    
- endereços IPv4.
    

---

# 66. `getaddrinfo()`

Uma das funções mais importantes:

```python
socket.getaddrinfo()
```

Ela permite obter informações necessárias para criar uma conexão de rede.

Exemplo:

```python
results = socket.getaddrinfo(
    'example.com',
    443,
    type=socket.SOCK_STREAM
)
```

Pode retornar entradas contendo:

```text
family
type
proto
canonname
sockaddr
```

Ela pode fornecer endereços IPv4 e IPv6 dependendo dos parâmetros e do sistema.

---

# 67. Por que `getaddrinfo()` é importante?

Porque uma aplicação moderna não deveria simplesmente assumir:

```text
hostname → um único IPv4
```

Um hostname pode possuir:

```text
IPv4
IPv6
múltiplos endereços
```

Então:

```python
getaddrinfo()
```

é muito mais adequado para código de rede genérico.

---

# 68. `getfqdn()`

Obtém um nome de domínio totalmente qualificado quando disponível.

```python
socket.getfqdn()
```

---

# 69. `getnameinfo()`

Realiza uma resolução reversa de endereço/serviço.

Conceitualmente:

```text
IP + porta
     │
     ▼
nome + serviço
```

---

# 70. `getservbyname()`

Permite consultar o nome de um serviço por porta/protocolo.

Exemplo conceitual:

```python
socket.getservbyname(
    'http',
    'tcp'
)
```

Resultado:

```text
80
```

---

# 71. `getservbyport()`

Faz o caminho inverso:

```python
socket.getservbyport(443, 'tcp')
```

Pode retornar:

```text
https
```

Isso é útil para ferramentas que precisam transformar portas em nomes de serviços conhecidos.

---

# 72. `socket.create_connection()`

Existe uma abstração útil:

```python
socket.create_connection(
    ('example.com', 443)
)
```

Ela simplifica a criação de uma conexão TCP.

É muito útil quando não precisamos controlar manualmente cada etapa de:

```text
socket()
connect()
```

---

# 73. `socket.create_server()`

Versões modernas do Python também oferecem:

```python
socket.create_server(...)
```

para simplificar a criação de servidores TCP.

Mesmo assim, aprender:

```text
socket()
bind()
listen()
accept()
```

continua sendo essencial para compreender o que está acontecendo.

---

# 74. IPv6

Criando um socket:

```python
server = socket.socket(
    socket.AF_INET6,
    socket.SOCK_STREAM
)
```

Servidor:

```python
server.bind(('::1', 4444))
server.listen()
```

Cliente:

```python
client.connect(('::1', 4444))
```

`::1` é o loopback IPv6.

Equivale conceitualmente a:

```text
IPv4 → 127.0.0.1
IPv6 → ::1
```

---

# 75. IPv4 x IPv6

|Característica|IPv4|IPv6|
|---|---|---|
|Família|`AF_INET`|`AF_INET6`|
|Endereço|32 bits|128 bits|
|Loopback|`127.0.0.1`|`::1`|
|Exemplo|`192.168.1.10`|`2001:db8::10`|
|Tupla típica|`(ip, port)`|`(ip, port, flowinfo, scopeid)`|

---

# 76. Dual stack

Uma aplicação pode precisar aceitar IPv4 e IPv6.

Uma abordagem é criar sockets separados:

```text
TCP IPv4
    │
    └── AF_INET

TCP IPv6
    │
    └── AF_INET6
```

Ou utilizar mecanismos de dual-stack quando disponíveis.

Não devemos assumir que o comportamento é idêntico em todos os sistemas operacionais.

---

# 77. TLS e segurança

A biblioteca:

```python
socket
```

não fornece criptografia por padrão.

Isto:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)
```

é apenas comunicação TCP.

Se fizermos:

```python
client.sendall(b'senha=123456')
```

os bytes não se tornam automaticamente criptografados.

---

# 78. `ssl`

Python possui o módulo:

```python
import ssl
```

Ele fornece suporte para TLS através de sockets.

A arquitetura passa a ser:

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
```

---

# 79. `SSLContext`

Uma forma moderna de trabalhar com TLS é:

```python
context = ssl.create_default_context()
```

No cliente:

```python
with socket.create_connection(
    ('example.com', 443)
) as sock:

    with context.wrap_socket(
        sock,
        server_hostname='example.com'
    ) as secure_sock:

        print(secure_sock.version())
```

Agora temos:

```text
Python
  │
  ▼
SSLSocket
  │
  ▼
TCP socket
  │
  ▼
Internet
```

---

# 80. O que o TLS acrescenta?

TLS pode fornecer:

- confidencialidade;
    
- integridade;
    
- autenticação do servidor;
    
- negociação criptográfica;
    
- proteção contra modificação dos dados em trânsito.
    

---

# 81. Certificados

O servidor apresenta um certificado durante o handshake TLS.

O cliente pode verificar:

```text
certificado
     │
     ├── validade
     ├── cadeia de confiança
     └── identidade/hostname
```

A autenticação depende da configuração correta do `SSLContext`.

---

# 82. Chaves pública e privada

Simplificando:

```text
Servidor
 ├── chave privada
 └── certificado/chave pública
```

A chave privada deve permanecer protegida.

O certificado permite que o cliente valide a identidade apresentada pelo servidor dentro do modelo de confiança utilizado.

---

# 83. TCP puro x TLS x HTTPS

```text
TCP puro

Aplicação
   ↓
TCP
   ↓
IP
```

```text
TLS sobre TCP

Aplicação
   ↓
TLS
   ↓
TCP
   ↓
IP
```

```text
HTTPS

HTTP
 ↓
TLS
 ↓
TCP
 ↓
IP
```

HTTPS não é um protocolo de transporte separado.

É essencialmente:

```text
HTTP sobre TLS
```

---

# 84. "Criptografar antes de enviar" não é automaticamente segurança

Fazer:

```python
encrypted_data = encrypt(data)

sock.sendall(encrypted_data)
```

não resolve automaticamente:

- autenticação;
    
- gerenciamento de chaves;
    
- replay;
    
- integridade;
    
- negociação criptográfica;
    
- validação de identidade;
    
- forward secrecy;
    
- proteção contra MITM.
    

É por isso que protocolos como TLS existem.

---

# 85. Protocolos de aplicação

`socket` permite conversar diretamente com protocolos de aplicação.

Por exemplo:

```text
HTTP
DNS
FTP
SMTP
POP3
IMAP
IRC
```

A estrutura pode ser:

```text
Aplicação
   │
   ▼
Protocolo de aplicação
   │
   ▼
TCP/UDP
   │
   ▼
IP
   │
   ▼
Link
```

---

# 86. HTTP manualmente

Podemos criar uma conexão TCP:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

sock.connect(('example.com', 80))
```

Depois enviar uma requisição HTTP:

```python
request = (
    'GET / HTTP/1.1\r\n'
    'Host: example.com\r\n'
    'Connection: close\r\n'
    '\r\n'
)

sock.sendall(request.encode())
```

Depois:

```python
response = b''

while True:
    data = sock.recv(4096)

    if not data:
        break

    response += data

print(response.decode(errors='replace'))
```

Aqui vemos claramente:

```text
socket
  ↓
TCP
  ↓
HTTP
```

---

# 87. DNS

DNS normalmente utiliza UDP ou TCP dependendo da situação e do tipo de operação.

A arquitetura pode ser:

```text
Aplicação DNS
     │
     ▼
 UDP/TCP
     │
     ▼
    IP
```

A biblioteca `socket` permite trabalhar com sockets UDP/TCP, mas não implementa automaticamente o protocolo DNS completo.

---

# 88. FTP

FTP tradicional utiliza TCP e possui uma arquitetura baseada em conexões de controle e dados.

Com `socket`, podemos estudar diretamente como os comandos são transportados.

---

# 89. SMTP

SMTP é um protocolo de aplicação normalmente transportado por TCP.

Uma comunicação simplificada pode parecer:

```text
CLIENTE
   │
   │ EHLO
   ▼
SERVIDOR
   │
   │ resposta
   ▼
CLIENTE
```

A aplicação utiliza o socket para transportar os bytes do protocolo.

---

# 90. SSH

SSH utiliza TCP como transporte tradicional.

Porém, implementar SSH corretamente não significa simplesmente:

```python
socket.connect(...)
```

SSH possui um protocolo complexo de:

- negociação;
    
- troca de chaves;
    
- autenticação;
    
- criptografia;
    
- integridade;
    
- canais.
    

Por isso, para uso real, utilizamos implementações específicas de SSH em vez de tentar implementar o protocolo manualmente.

---

# 91. Cybersecurity e `socket`

`socket` é especialmente útil para entender ferramentas de segurança porque permite observar diretamente o comportamento dos serviços.

Exemplos educacionais em:

- localhost;
    
- máquinas próprias;
    
- laboratórios;
    
- CTFs;
    
- ambientes autorizados.
    

---

# 92. Banner grabbing

Um serviço pode enviar uma identificação quando recebemos sua conexão.

Exemplo genérico:

```python
import socket

sock = socket.socket()

sock.settimeout(3)

sock.connect(('127.0.0.1', 4444))

banner = sock.recv(1024)

print(banner.decode(errors='replace'))

sock.close()
```

O objetivo é compreender:

```text
porta aberta
      ↓
conexão TCP
      ↓
resposta do serviço
      ↓
análise do banner
```

Um banner pode revelar informações como:

```text
nome do serviço
versão
protocolo
mensagem inicial
```

Mas banners não são uma fonte necessariamente confiável de identificação.

---

# 93. Verificação de portas

Podemos testar uma porta TCP em um laboratório:

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

sock.settimeout(1)

result = sock.connect_ex(
    ('127.0.0.1', 4444)
)

if result == 0:
    print('Porta aberta')
else:
    print('Porta não acessível')

sock.close()
```

---

# 94. `connect_ex()`

É semelhante a `connect()`, mas retorna um código de erro em vez de gerar uma exceção para determinados erros de conexão.

Exemplo:

```python
result = sock.connect_ex(
    ('127.0.0.1', 4444)
)
```

Podemos utilizar:

```python
if result == 0:
    print('Conectou')
else:
    print(f'Falhou: {result}')
```

Isso pode ser conveniente para scanners simples.

---

# 95. Scanner TCP educacional

Em um laboratório próprio:

```python
import socket

host = '127.0.0.1'

for port in range(1, 1025):

    sock = socket.socket(
        socket.AF_INET,
        socket.SOCK_STREAM
    )

    sock.settimeout(0.2)

    result = sock.connect_ex(
        (host, port)
    )

    if result == 0:
        print(f'[+] Porta aberta: {port}')

    sock.close()
```

O conceito é:

```text
porta 1
  ↓
connect_ex()

porta 2
  ↓
connect_ex()

porta 3
  ↓
connect_ex()

...
```

Esse é um scanner extremamente simples.

Ferramentas reais precisam lidar com:

- paralelismo;
    
- timeouts;
    
- retransmissões;
    
- estados;
    
- IPv6;
    
- resolução DNS;
    
- filtros;
    
- firewalls;
    
- SYN scanning;
    
- banners;
    
- serviços;
    
- performance.
    

---

# 96. Por que `socket` é importante para Pentest?

Porque permite entender o que ferramentas maiores estão fazendo.

Por exemplo:

```text
nmap
 │
 ├── descoberta
 ├── TCP
 ├── UDP
 ├── portas
 ├── serviços
 └── fingerprinting
```

Antes de aprender uma ferramenta como Nmap profundamente, é muito útil compreender:

```text
IP
 ↓
porta
 ↓
TCP
 ↓
handshake
 ↓
serviço
```

---

# 97. Raw sockets

Raw socket:

```python
socket.socket(
    socket.AF_INET,
    socket.SOCK_RAW,
    socket.IPPROTO_ICMP
)
```

permite trabalhar em nível diferente de:

```python
SOCK_STREAM
```

Com TCP normal:

```text
Aplicação
   ↓
socket TCP
   ↓
kernel constrói/manipula TCP/IP
   ↓
rede
```

Com raw socket, podemos ter acesso muito mais próximo dos pacotes.

---

# 98. Cabeçalhos de rede

Um pacote IPv4 contém campos como:

```text
+-----------------------------+
| Version / IHL               |
+-----------------------------+
| Total Length                |
+-----------------------------+
| Identification              |
+-----------------------------+
| Flags / Fragment Offset      |
+-----------------------------+
| TTL | Protocol | Checksum    |
+-----------------------------+
| Source IP                   |
+-----------------------------+
| Destination IP              |
+-----------------------------+
| Payload                     |
+-----------------------------+
```

Dentro do payload IP pode existir:

```text
TCP
UDP
ICMP
```

---

# 99. TCP dentro do IP

Conceitualmente:

```text
Ethernet
└── IP
    └── TCP
        └── HTTP
            └── dados
```

Cada camada adiciona informações próprias.

---

# 100. ICMP

ICMP é utilizado para mensagens de controle e diagnóstico.

Por exemplo, o conceito por trás do:

```bash
ping
```

envolve ICMP Echo Request/Echo Reply em redes IP tradicionais.

Raw sockets podem ser utilizados em determinadas implementações para trabalhar com ICMP diretamente.

---

# 101. Privilégios

Operações de baixo nível, especialmente raw sockets, podem exigir privilégios elevados.

No Linux, por exemplo, determinadas operações podem exigir:

```text
root
```

ou capacidades específicas.

Isso existe porque acesso direto a determinados mecanismos de rede pode permitir:

- construção de pacotes;
    
- captura;
    
- spoofing;
    
- interação de baixo nível com a rede.
    

Por isso sistemas operacionais restringem esse acesso.

---

# 102. Erros de socket

Programas de rede precisam tratar erros.

---

## `ConnectionRefusedError`

Normalmente indica que a conexão foi recusada.

Causas possíveis:

```text
nenhum serviço escutando
firewall rejeitando
porta incorreta
serviço indisponível
```

---

## `ConnectionResetError`

A conexão foi resetada pelo peer ou pela pilha de rede em determinada situação.

Pode acontecer quando:

```text
cliente ↔ servidor
       │
       └── conexão encerrada abruptamente
```

---

## `BrokenPipeError`

A aplicação tentou escrever em uma conexão que já não está disponível para escrita.

Exemplo conceitual:

```text
CLIENTE
   │
   │ sendall()
   ▼
SERVIDOR
   │
   └── conexão já encerrada
```

---

## `TimeoutError`

Pode ocorrer quando uma operação excede o timeout configurado.

```python
sock.settimeout(2)
```

---

## `OSError`

Muitos erros de baixo nível relacionados ao sistema operacional aparecem como subclasses de `OSError`.

Exemplo:

```text
OSError: [Errno 98] Address already in use
```

---

# 103. Debugging

Ao receber:

```text
Connection refused
```

não devemos simplesmente mudar a porta aleatoriamente.

Verifique:

```text
1. O servidor está executando?
2. Está escutando a porta correta?
3. Está associado ao IP correto?
4. O firewall permite?
5. O cliente está usando o endereço correto?
6. O serviço está realmente TCP?
```

No Linux:

```bash
ss -ltnp
```

Pode ajudar a descobrir sockets TCP em escuta.

Para UDP:

```bash
ss -lunp
```

---

# 104. `ss`

Exemplo:

```bash
ss -ltnp
```

Interpretação:

```text
-l → listening
-t → TCP
-n → não resolver nomes
-p → mostrar processo
```

---

# 105. Arquitetura de um chat TCP

Um chat simples:

```text
                 SERVIDOR
                    │
             listening socket
                    │
          ┌─────────┼─────────┐
          │         │         │
          ▼         ▼         ▼
       Cliente A Cliente B Cliente C
          │         │         │
        thread    thread    thread
```

Cada cliente possui:

```text
socket
nome
estado
thread/tarefa
```

O servidor mantém uma estrutura de clientes:

```python
clients = {
    client_socket: 'Marcos',
    another_socket: 'João',
}
```

---

# 106. Broadcast

Quando uma mensagem deve chegar a todos os clientes:

```text
Marcos
   │
   │ "Olá!"
   ▼
SERVIDOR
 ┌─┼──────────────┐
 ▼ ▼              ▼
João             Pedro
```

O servidor precisa percorrer os sockets conectados e enviar a mensagem.

---

# 107. Cuidados com múltiplos clientes

Um servidor real precisa lidar com:

- cliente desconectando;
    
- exceções;
    
- sockets inválidos;
    
- concorrência;
    
- remoção de clientes;
    
- mensagens incompletas;
    
- framing;
    
- limites de tamanho;
    
- autenticação;
    
- autorização;
    
- timeouts.
    

Um simples:

```python
clients.append(client)
```

não é suficiente para um servidor robusto.

---

# 108. Protocolos próprios

Podemos criar um pequeno protocolo:

```text
CLIENTE
   │
   │ LOGIN Marcos
   ▼
SERVIDOR
   │
   │ OK
   ▼
CLIENTE
   │
   │ MSG 0005 Hello
   ▼
SERVIDOR
```

Um protocolo precisa definir:

- formato;
    
- tamanho;
    
- comandos;
    
- respostas;
    
- erros;
    
- autenticação;
    
- encerramento;
    
- versão.
    

---

# 109. Exemplo de protocolo

Podemos definir:

```text
[4 bytes tamanho]
[1 byte tipo]
[payload]
```

Por exemplo:

```text
HEADER
├── tamanho
└── tipo

BODY
└── payload
```

Tipos:

```text
0x01 = LOGIN
0x02 = MESSAGE
0x03 = LOGOUT
0x04 = ERROR
```

Agora temos algo mais próximo de um protocolo real.

---

# 110. Segurança de protocolos próprios

Um protocolo próprio precisa considerar:

```text
Entrada do usuário
       ↓
servidor
       ↓
parser
```

Nunca devemos confiar automaticamente nos dados recebidos.

Um cliente malicioso pode enviar:

```text
tamanho = 4294967295
```

Se o servidor tentar alocar isso diretamente, pode ocorrer:

- consumo excessivo de memória;
    
- DoS;
    
- travamento.
    

Portanto:

```python
MAX_MESSAGE_SIZE = 1024 * 1024
```

e:

```python
if size > MAX_MESSAGE_SIZE:
    raise ValueError('Mensagem muito grande')
```

é uma defesa importante.

---

# 111. Socket e validação de entrada

Tudo recebido pela rede deve ser considerado:

```text
INPUT NÃO CONFIÁVEL
```

Isso vale para:

- strings;
    
- JSON;
    
- comandos;
    
- tamanhos;
    
- nomes;
    
- arquivos;
    
- identificadores;
    
- números.
    

Nunca devemos assumir que o cliente é confiável apenas porque conseguiu estabelecer uma conexão TCP.

---

# 112. TCP não fornece autenticação da aplicação

TCP responde:

> Existe uma conexão entre esses endpoints.

TCP não responde:

> Essa pessoa é realmente quem afirma ser?

Para isso precisamos de mecanismos da aplicação ou de protocolos superiores:

```text
TCP
+
TLS
+
autenticação
```

---

# 113. Socket + TLS + autenticação

Uma arquitetura mais realista:

```text
Cliente
  │
  ├── TCP
  │
  ├── TLS
  │
  ├── certificado
  │
  └── autenticação
          │
          ▼
       Servidor
```

Cada camada resolve um problema diferente.

---

# 114. `socket` x `requests`

`requests` trabalha em uma camada muito mais alta.

Com:

```python
requests.get('https://example.com')
```

você não precisa controlar manualmente:

```text
socket()
connect()
TLS
HTTP headers
recv()
```

A biblioteca abstrai grande parte disso.

Já com:

```python
socket.socket(...)
```

você começa muito mais próximo da infraestrutura de comunicação.

---

# 115. `socket` x HTTP

Não são concorrentes diretos.

```text
socket
```

é uma interface de comunicação.

```text
HTTP
```

é um protocolo de aplicação.

Podemos ter:

```text
HTTP
 ↓
TCP socket
```

---

# 116. `socket` x `requests`

```text
requests
   │
   ├── HTTP
   ├── TLS
   ├── sockets
   └── parsing
```

Enquanto:

```text
socket
   │
   └── comunicação de baixo nível
```

---

# 117. Projeto 1 — Chat TCP

Objetivo:

```text
Servidor
   │
   ├── Cliente 1
   ├── Cliente 2
   └── Cliente 3
```

Requisitos:

- nome do usuário;
    
- múltiplos clientes;
    
- `threading`;
    
- broadcast;
    
- desconexão;
    
- comando `/exit`.
    

Conceitos:

```text
socket
bind
listen
accept
recv
sendall
threading
```

---

# 118. Projeto 2 — Protocolo próprio

Criar:

```text
[4 bytes tamanho]
[payload]
```

Implementar:

```python
send_message()
recv_exact()
recv_message()
```

Depois adicionar:

```text
LOGIN
MESSAGE
LOGOUT
ERROR
```

---

# 119. Projeto 3 — Cliente HTTP manual

Construir:

```text
socket
  ↓
TCP
  ↓
HTTP
```

Enviar:

```http
GET / HTTP/1.1
Host: example.com
Connection: close
```

e analisar a resposta.

Objetivo:

entender que HTTP é apenas um protocolo de aplicação transportado por uma conexão.

---

# 120. Projeto 4 — TCP Banner Grabber

Criar uma ferramenta que:

```text
IP
 ↓
porta
 ↓
connect()
 ↓
recv()
 ↓
banner
```

Depois melhorar:

- timeout;
    
- tratamento de erros;
    
- múltiplas portas;
    
- identificação de serviços;
    
- salvamento de resultados.
    

Utilizar somente contra sistemas próprios ou autorizados.

---

# 121. Projeto 5 — Scanner TCP

Começar com:

```python
connect_ex()
```

Depois evoluir para:

```text
scanner sequencial
       ↓
scanner threaded
       ↓
scanner com selectors
       ↓
scanner IPv4/IPv6
```

O objetivo é compreender como o desempenho muda conforme a arquitetura.

---

# 122. Projeto 6 — Servidor concorrente

Criar três versões:

```text
server.py
    ↓
sequencial
```

Depois:

```text
server_thread.py
    ↓
threading
```

Depois:

```text
server_selector.py
    ↓
selectors
```

E comparar.

---

# 123. Exercício de análise de rede

Execute:

```bash
ss -ltnp
```

Identifique:

```text
processo
IP local
porta
estado
```

Depois crie um servidor:

```python
server.bind(('127.0.0.1', 4444))
server.listen()
```

Execute novamente:

```bash
ss -ltnp
```

Observe o estado:

```text
LISTEN
```

Isso conecta diretamente o código Python ao estado observado no sistema operacional.

---

# 124. Exercício: observar conexão

Execute um servidor TCP.

Depois conecte o cliente.

Observe:

```bash
ss -tnp
```

Antes:

```text
LISTEN
```

Depois da conexão:

```text
LISTEN
ESTABLISHED
```

O socket de escuta continua existindo enquanto o socket da conexão aparece separadamente.

Esse exercício ajuda a visualizar a diferença entre:

```text
listening socket
```

e:

```text
connected socket
```

---

# 125. Exercício: provocar timeout

```python
import socket

sock = socket.socket()

sock.settimeout(2)

try:
    sock.connect(('127.0.0.1', 65000))
except socket.timeout:
    print('Timeout')
except OSError as error:
    print(error)
```

Analise a diferença entre:

```text
porta recusando
```

e:

```text
host/rota simplesmente não respondendo
```

---

# 126. Exercício: descobrir seu socket

Depois de conectar:

```python
print(sock.getsockname())
print(sock.getpeername())
```

Compare:

```text
getsockname()
```

com:

```text
getpeername()
```

Observe que o cliente possui uma porta local que normalmente não foi escolhida manualmente.

---

# 127. Porta efêmera

Quando o cliente faz:

```python
client.connect(
    ('127.0.0.1', 4444)
)
```

normalmente não precisamos fazer:

```python
client.bind(
    ('127.0.0.1', 50000)
)
```

O sistema operacional pode escolher automaticamente uma porta local efêmera.

Assim:

```text
Cliente
127.0.0.1:53218
       │
       │ TCP
       ▼
Servidor
127.0.0.1:4444
```

---

# 128. Quádrupla identificação TCP

Uma conexão TCP pode ser diferenciada por:

```text
IP origem
porta origem
IP destino
porta destino
```

Exemplo:

```text
192.168.1.10:53122
        │
        ▼
192.168.1.20:443
```

Outra conexão:

```text
192.168.1.10:53123
        │
        ▼
192.168.1.20:443
```

pode coexistir porque possui porta de origem diferente.

---

# 129. Escalando para muitos clientes

Imagine:

```text
Cliente A → 192.168.1.10:50001
Cliente B → 192.168.1.10:50002
Cliente C → 192.168.1.10:50003
```

Todos podem se conectar ao mesmo:

```text
Servidor
192.168.1.20:4444
```

O servidor diferencia as conexões através dos endpoints.

---

# 130. O que acontece quando o cliente desconecta?

No lado do servidor:

```python
data = client.recv(1024)
```

eventualmente pode retornar:

```python
b''
```

Então:

```python
if not data:
    break
```

O servidor deve:

```text
detectar
   ↓
remover cliente
   ↓
fechar socket
   ↓
limpar recursos
```

---

# 131. O que acontece se o servidor fechar?

O cliente pode receber:

```text
b''
```

ou encontrar uma exceção dependendo de como a conexão foi encerrada e do estado da comunicação.

Por isso uma aplicação real precisa tratar desconexões.

---

# 132. Limites e robustez

Um servidor não deve assumir:

```text
mensagem pequena
cliente confiável
cliente rápido
cliente sempre conectado
```

Um cliente pode:

```text
conectar
não enviar nada
esperar
enviar dados gigantes
desconectar
reconectar
```

Isso deve ser considerado no design.

---

# 133. Segurança defensiva

Servidores baseados em socket devem considerar:

- timeout;
    
- limite de tamanho;
    
- limite de conexões;
    
- autenticação;
    
- autorização;
    
- validação;
    
- TLS;
    
- logging;
    
- tratamento de exceções;
    
- encerramento correto;
    
- proteção contra abuso.
    

---

# 134. Modelo mental definitivo

Quando olhar para:

```python
client.sendall(data)
```

não pense apenas:

> "envia uma mensagem."

Pense:

```text
Aplicação
   │
   ▼
socket Python
   │
   ▼
kernel
   │
   ▼
TCP
   │
   ▼
IP
   │
   ▼
rede
   │
   ▼
IP remoto
   │
   ▼
TCP remoto
   │
   ▼
socket remoto
   │
   ▼
aplicação remota
```

Esse é o modelo mental que começa a separar quem apenas sabe usar `socket` de quem realmente entende programação de rede.

---

# 135. Mapa mental da biblioteca

```text
socket
│
├── Criação
│   └── socket()
│
├── TCP
│   ├── bind()
│   ├── listen()
│   ├── accept()
│   ├── connect()
│   ├── send()
│   ├── sendall()
│   ├── recv()
│   └── shutdown()
│
├── UDP
│   ├── sendto()
│   ├── recvfrom()
│   └── broadcast/multicast
│
├── Configuração
│   ├── settimeout()
│   ├── setblocking()
│   ├── setsockopt()
│   └── getsockopt()
│
├── Informações
│   ├── getsockname()
│   ├── getpeername()
│   └── fileno()
│
├── DNS
│   ├── getaddrinfo()
│   ├── getnameinfo()
│   ├── gethostbyname()
│   └── getfqdn()
│
├── Baixo nível
│   ├── SOCK_RAW
│   ├── recvmsg()
│   ├── sendmsg()
│   └── descritores
│
└── Segurança
    └── ssl
        ├── TLS
        ├── certificados
        └── SSLContext
```

---

# 136. Comparações importantes

## TCP x UDP

|TCP|UDP|
|---|---|
|Conexão|Datagramas|
|Fluxo de bytes|Mensagens/datagramas|
|Ordenação|Não garantida|
|Retransmissão|Sim|
|`accept()`|Sim|
|`sendall()`|Comum|
|`sendto()`|Não é o modelo principal|
|`recvfrom()`|Não é o modelo principal|

---

## `send()` x `sendall()`

|`send()`|`sendall()`|
|---|---|
|Retorna quantidade enviada|Retorna `None` em sucesso|
|Pode enviar parcialmente|Tenta enviar tudo|
|Aplicação deve tratar restante|Mais simples para uso comum|

---

## `recv()` x `recvfrom()`

|`recv()`|`recvfrom()`|
|---|---|
|Recebe dados|Recebe dados + endereço|
|Comum em TCP conectado|Muito comum em UDP|
|Retorna bytes|Retorna `(data, address)`|

---

## Blocking x Non-blocking

|Blocking|Non-blocking|
|---|---|
|Pode esperar|Retorna imediatamente|
|Mais simples|Mais complexo|
|Bom para programas simples|Útil para I/O multiplexado|
|Pode bloquear thread|Trabalha bem com `selectors`|

---

## `threading` x `selectors` x `asyncio`

|`threading`|`selectors`|`asyncio`|
|---|---|---|
|Threads|I/O multiplexing|Assíncrono|
|Simples de entender|Mais baixo nível|Alto nível assíncrono|
|Fácil para poucos clientes|Bom para muitos sockets|Bom para aplicações assíncronas|

---

## TCP puro x TLS

```text
TCP puro

dados
 ↓
TCP
 ↓
IP
```

```text
TLS

dados
 ↓
TLS
 ↓
TCP
 ↓
IP
```

TLS adiciona proteção criptográfica e mecanismos de autenticação.

---

# 137. Armadilhas importantes

## `recv(1024)` não significa receber exatamente 1024 bytes

Errado:

```python
data = sock.recv(1024)
# "recebi exatamente 1024"
```

Correto:

```text
"tente receber até 1024 bytes"
```

---

## `send()` não significa enviar tudo

Errado:

```python
sock.send(data)
# "todos os dados foram enviados"
```

Correto:

```text
send() retorna quanto foi enviado
```

---

## `sendall()` não cria mensagens

Errado:

```python
sendall(b'A')
sendall(b'B')
```

não significa que o receptor terá necessariamente:

```text
A
B
```

TCP é fluxo de bytes.

---

## `accept()` não devolve o socket de escuta

```python
client, address = server.accept()
```

`server` continua escutando.

`client` representa a conexão aceita.

---

## `close()` não é `shutdown()`

```text
shutdown()
→ controla a direção da comunicação

close()
→ fecha/libera o socket
```

---

## TCP não criptografa

```python
socket.SOCK_STREAM
```

não significa:

```text
seguro
```

TCP fornece transporte, não criptografia de aplicação.

---

# 138. 🧠 O que devo dominar depois desta anotação

Depois de estudar este material, você deve conseguir explicar sem decorar:

### Fundamentos

- o que é um socket;
    
- diferença entre socket e protocolo;
    
- relação entre IP, porta e socket;
    
- diferença entre cliente e servidor;
    
- diferença entre IPv4 e IPv6;
    
- `AF_INET`;
    
- `AF_INET6`;
    
- `SOCK_STREAM`;
    
- `SOCK_DGRAM`;
    
- conceito de `SOCK_RAW`.
    

### TCP

- por que TCP precisa de conexão;
    
- three-way handshake;
    
- SYN;
    
- SYN-ACK;
    
- ACK;
    
- sequence numbers;
    
- ACKs;
    
- retransmissão;
    
- controle de fluxo;
    
- congestionamento;
    
- FIN;
    
- RST;
    
- TIME_WAIT;
    
- half-close;
    
- `shutdown()`;
    
- `close()`.
    

### Python

Você deve saber utilizar e explicar:

```python
socket()
bind()
listen()
accept()
connect()
connect_ex()
send()
sendall()
recv()
sendto()
recvfrom()
settimeout()
setblocking()
getsockname()
getpeername()
getsockopt()
setsockopt()
shutdown()
close()
```

E conhecer:

```python
fileno()
detach()
dup()
makefile()
recv_into()
sendmsg()
recvmsg()
```

---

### UDP

Você deve conseguir criar:

```text
UDP client
UDP server
```

e explicar:

```text
sendto()
recvfrom()
```

---

### TCP framing

Você deve entender por que:

```python
recv(1024)
```

não é suficiente para definir mensagens.

E saber implementar:

```text
[4 bytes tamanho][payload]
```

utilizando:

```python
struct.pack()
struct.unpack()
```

e uma função como:

```python
recv_exact()
```

---

### Concorrência

Você deve saber diferenciar:

```text
sequencial
threading
selectors
asyncio
```

e entender por que um servidor simples baseado em:

```python
recv()
input()
sendall()
```

pode bloquear uma atividade enquanto espera outra.

---

### Segurança

Você deve saber explicar:

```text
socket TCP
   ≠
socket seguro
```

e entender a arquitetura:

```text
Aplicação
   ↓
TLS
   ↓
TCP
   ↓
IP
```

Além disso, deve compreender:

- certificados;
    
- chave pública/privada;
    
- `SSLContext`;
    
- autenticação;
    
- confidencialidade;
    
- integridade;
    
- diferença entre TCP e TLS.
    

---

### Cybersecurity

Você deve conseguir construir, em laboratório autorizado:

```text
TCP client
TCP server
UDP client
UDP server
banner grabber
scanner TCP simples
servidor concorrente
chat TCP
protocolo próprio
cliente HTTP manual
```

E, principalmente, explicar **o que acontece na rede quando cada programa executa**.

---

# 139. 🧪 Exercícios práticos

## Nível 1 — Fundamentos

### Exercício 1

Crie um servidor TCP em:

```text
127.0.0.1:4444
```

Ele deve:

1. criar o socket;
    
2. fazer `bind()`;
    
3. fazer `listen()`;
    
4. aceitar um cliente;
    
5. imprimir o IP e a porta do cliente.
    

---

### Exercício 2

Crie um cliente que:

1. conecta;
    
2. descobre sua porta local com `getsockname()`;
    
3. descobre o endereço remoto com `getpeername()`;
    
4. imprime os dois.
    

---

### Exercício 3

Faça o cliente enviar:

```text
Olá servidor
```

e o servidor responder:

```text
Olá cliente
```

---

## Nível 2 — TCP

### Exercício 4

Faça um servidor echo:

```text
CLIENTE
   │
   │ Hello
   ▼
SERVIDOR
   │
   │ Hello
   ▼
CLIENTE
```

---

### Exercício 5

Faça o cliente enviar várias mensagens.

Descubra experimentalmente se:

```python
sendall(b'ABC')
sendall(b'DEF')
```

sempre resulta em:

```text
recv() → ABC
recv() → DEF
```

Não assuma o resultado. Observe o comportamento.

---

### Exercício 6

Implemente:

```python
recv_exact(sock, size)
```

que garanta que exatamente `size` bytes sejam acumulados ou que a conexão seja considerada encerrada.

---

## Nível 3 — Protocolos

### Exercício 7

Crie:

```text
[4 bytes tamanho][payload]
```

Implemente:

```python
send_message()
recv_message()
```

---

### Exercício 8

Adicione tipos de mensagem:

```text
LOGIN
MESSAGE
LOGOUT
```

---

## Nível 4 — Concorrência

### Exercício 9

Transforme seu servidor de um único cliente em:

```text
múltiplos clientes
```

utilizando:

```python
threading.Thread
```

---

### Exercício 10

Adicione:

```text
nome do usuário
```

e faça:

```text
Marcos: Olá
João: Fala!
Pedro: Cheguei
```

---

### Exercício 11

Implemente broadcast:

```text
Marcos
  │
  │ Olá!
  ▼
Servidor
 ├────► João
 └────► Pedro
```

---

## Nível 5 — UDP

### Exercício 12

Crie um servidor UDP usando:

```python
recvfrom()
```

e um cliente utilizando:

```python
sendto()
```

---

### Exercício 13

Implemente um servidor UDP que responda:

```text
PING
```

com:

```text
PONG
```

---

## Nível 6 — Networking

### Exercício 14

Use:

```bash
ss -ltnp
```

para observar seu servidor.

Explique:

```text
LISTEN
ESTABLISHED
TIME-WAIT
```

---

### Exercício 15

Crie um programa que mostre:

```text
IP local
porta local
IP remoto
porta remota
```

utilizando:

```python
getsockname()
getpeername()
```

---

## Nível 7 — Cybersecurity

### Exercício 16

Crie um scanner TCP simples para:

```text
127.0.0.1
```

Verifique apenas:

```text
1–1024
```

Use:

```python
connect_ex()
```

---

### Exercício 17

Adicione:

```python
settimeout()
```

e compare o comportamento.

---

### Exercício 18

Transforme o scanner sequencial em uma versão usando:

```python
threading
```

Compare o tempo.

---

### Exercício 19

Crie um banner grabber para serviços do seu laboratório.

Fluxo:

```text
IP
 ↓
porta
 ↓
connect()
 ↓
recv()
 ↓
banner
```

---

## Nível 8 — Avançado

### Exercício 20

Reimplemente seu servidor utilizando:

```python
selectors
```

Sem criar uma thread por cliente.

---

### Exercício 21

Crie uma versão utilizando:

```python
asyncio
```

---

### Exercício 22

Crie um servidor TCP com:

```text
TLS
+
certificado
+
SSLContext
```

Compare:

```text
TCP puro
```

com:

```text
TCP + TLS
```

---

### Exercício 23

Estude raw sockets em um laboratório Linux autorizado.

Objetivo:

```text
IP
 ↓
ICMP
 ↓
raw socket
```

e compreenda quais partes do pacote são fornecidas pelo kernel e quais podem ser manipuladas pela aplicação.

---

# 140. Resumo final

O caminho completo de aprendizado pode ser visualizado assim:

```text
                    SOCKET
                       │
        ┌──────────────┴──────────────┐
        │                             │
       TCP                           UDP
        │                             │
 socket()                        socket()
 bind()                          bind()*
 listen()                        sendto()
 accept()                        recvfrom()
 connect()                       ...
 send()/sendall()
 recv()
        │
        ▼
   BYTE STREAM
        │
        ▼
    FRAMING
        │
        ▼
   PROTOCOLO
        │
        ▼
      TLS
        │
        ▼
  APLICAÇÃO
        │
        ├── HTTP
        ├── DNS
        ├── FTP
        ├── SMTP
        ├── IRC
        └── protocolos próprios
```

E para cybersecurity:

```text
socket
   │
   ├── conexão TCP
   ├── UDP
   ├── portas
   ├── banners
   ├── serviços
   ├── protocolos
   ├── timeouts
   ├── scanners
   ├── raw packets
   └── análise de rede
             │
             ▼
        Cybersecurity
```

O principal objetivo não é memorizar:

```python
socket()
bind()
listen()
accept()
```

O objetivo é conseguir olhar para um programa de rede e entender:

```text
O que o processo está fazendo?
        ↓
Qual socket está sendo utilizado?
        ↓
Qual IP?
        ↓
Qual porta?
        ↓
Qual protocolo?
        ↓
O socket está bloqueando?
        ↓
O que está acontecendo no TCP/IP?
        ↓
Que dados estão sendo transmitidos?
        ↓
Como o protocolo da aplicação organiza esses dados?
        ↓
Existe autenticação?
        ↓
Existe criptografia?
        ↓
O que acontece se o cliente for malicioso?
```

Esse é o ponto em que `socket` deixa de ser apenas uma biblioteca Python e passa a ser uma ferramenta para **entender como aplicações realmente se comunicam pela rede**.