Esta anotação reúne bibliotecas e módulos Python úteis para automação, Linux, redes, Web, análise de dados técnicos e desenvolvimento de ferramentas para laboratórios de Cyber Security.

A ideia não é estudar todas de uma vez.

Use esta anotação como referência para saber:

- Para que cada biblioteca serve.
    
- Onde ela é útil.
    
- Quando vale a pena estudar.
    
- Como ela aparece em projetos reais.
    
- Como ela pode ser combinada com outras bibliotecas.
    

---

# `os`

## Para que serve

Permite interagir com recursos do sistema operacional.

Muito útil para:

- Linux.
    
- Arquivos.
    
- Diretórios.
    
- Variáveis de ambiente.
    
- Usuários.
    
- Automação.
    
- Enumeração local.
    

---

## Onde pode ser utilizada

### Linux

- Descobrir diretório atual.
    
- Listar arquivos.
    
- Ler variáveis de ambiente.
    
- Obter UID/GID.
    
- Manipular arquivos e diretórios.
    

### Cyber Security

Pode ajudar na construção de ferramentas de:

- Enumeração local.
    
- Coleta de informações.
    
- Automação de tarefas.
    
- Processamento de arquivos.
    

---

## Exemplo

```python
import os

print(os.getcwd())
print(os.listdir("."))
print(os.getenv("HOME"))
print(os.getuid())
```

---

# `pathlib`

## Para que serve

Manipulação moderna de caminhos, arquivos e diretórios.

É uma alternativa mais organizada a várias operações de `os.path`.

---

## Onde pode ser utilizada

- Linux.
    
- Windows.
    
- Scripts de enumeração.
    
- Busca de arquivos.
    
- Processamento de wordlists.
    
- Organização de resultados.
    

---

## Exemplo

```python
from pathlib import Path

arquivo = Path("/etc/passwd")

print(arquivo.exists())
print(arquivo.name)
print(arquivo.is_file())
```

---

## Exemplo procurando arquivos

```python
from pathlib import Path

for arquivo in Path("/tmp").iterdir():

    print(arquivo)
```

---

# `subprocess`

## Para que serve

Executar programas e comandos externos através do Python.

Muito útil para combinar Python com ferramentas do sistema.

---

## Onde pode ser utilizada

### Linux

Executar comandos como:

- `whoami`
    
- `id`
    
- `ip`
    
- `ss`
    
- programas instalados no sistema.
    

### Automação

Executar outras ferramentas e capturar:

- stdout.
    
- stderr.
    
- código de retorno.
    

---

## Exemplo

```python
import subprocess

resultado = subprocess.run(
    ["whoami"],
    capture_output=True,
    text=True
)

print(resultado.stdout)
```

---

## Exemplo verificando retorno

```python
import subprocess

resultado = subprocess.run(
    ["id"],
    capture_output=True,
    text=True
)

print(resultado.returncode)
print(resultado.stdout)
print(resultado.stderr)
```

---

# `sys`

## Para que serve

Interagir com recursos relacionados ao próprio interpretador Python.

Muito útil para:

- Argumentos.
    
- Entrada e saída.
    
- Encerrar programas.
    
- Manipular caminhos de módulos.
    

---

## Onde pode ser utilizada

- Ferramentas CLI.
    
- Scripts.
    
- Automação.
    
- Tratamento de erros.
    
- Entrada via terminal.
    

---

## Exemplo

```python
import sys

print(sys.argv)
```

---

## Encerrando programa

```python
import sys

sys.exit(1)
```

---

# `re`

## Para que serve

Trabalhar com expressões regulares (Regex).

Muito útil para:

- Extrair dados.
    
- Validar padrões.
    
- Buscar IPs.
    
- URLs.
    
- Hashes.
    
- E-mails.
    
- Informações em logs.
    

---

## Onde pode ser utilizada

### Cyber Security

- Parsing de saída de ferramentas.
    
- Análise de logs.
    
- Extração de indicadores.
    
- Processamento de respostas HTTP.
    
- Busca de padrões em arquivos.
    

---

## Exemplo

```python
import re

texto = """
Host: 192.168.1.10
Host: 10.10.10.20
"""

ips = re.findall(
    r"\d+\.\d+\.\d+\.\d+",
    texto
)

print(ips)
```

---

# `socket`

## Para que serve

Comunicação de rede em baixo nível.

Permite trabalhar diretamente com:

- TCP.
    
- UDP.
    
- Clientes.
    
- Servidores.
    
- Conexões de rede.
    

---

## Onde pode ser utilizada

- Banner grabbing.
    
- Clientes TCP.
    
- Servidores TCP.
    
- Scanners simples.
    
- Comunicação entre hosts.
    
- Estudo de protocolos.
    

---

## Exemplo

```python
import socket

sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM
)

sock.settimeout(3)

sock.connect(
    ("127.0.0.1", 80)
)
```

---

# `ipaddress`

## Para que serve

Manipular endereços e redes IPv4 e IPv6.

---

## Onde pode ser utilizada

- Subnetting.
    
- Enumeração de redes.
    
- Validação de IP.
    
- CIDR.
    
- Ordenação de IPs.
    
- Geração de hosts.
    

---

## Exemplo

```python
import ipaddress

rede = ipaddress.ip_network(
    "192.168.1.0/24"
)

for host in rede.hosts():

    print(host)
```

---

## Validando IP

```python
import ipaddress

ip = ipaddress.ip_address(
    "192.168.1.10"
)

print(ip)
```

---

# `json`

## Para que serve

Ler e gerar dados no formato JSON.

Muito utilizado em:

- APIs.
    
- Web.
    
- Configurações.
    
- Relatórios.
    
- Resultados estruturados.
    

---

## Onde pode ser utilizada

### Web

APIs normalmente retornam JSON.

### Ferramentas

Resultados podem ser exportados para JSON.

---

## Exemplo

```python
import json

dados = {
    "host": "10.10.10.10",
    "porta": 80
}

resultado = json.dumps(
    dados,
    indent=4
)

print(resultado)
```

---

# `base64`

## Para que serve

Codificar e decodificar dados em Base64.

Muito comum em:

- HTTP.
    
- Tokens.
    
- APIs.
    
- Arquivos.
    
- Protocolos.
    
- Dados binários.
    

---

## Exemplo

```python
import base64

texto = b"admin"

codificado = base64.b64encode(texto)

print(codificado)
```

---

## Decodificando

```python
original = base64.b64decode(
    codificado
)

print(original)
```

---

# `binascii`

## Para que serve

Conversões entre dados binários e representações como hexadecimal.

Muito útil para:

- Binários.
    
- Redes.
    
- Engenharia Reversa.
    
- Protocolos.
    

---

## Exemplo

```python
import binascii

dados = b"Python"

hexadecimal = binascii.hexlify(dados)

print(hexadecimal)
```

---

# `hashlib`

## Para que serve

Calcular hashes.

Suporta algoritmos como:

- MD5.
    
- SHA-1.
    
- SHA-256.
    
- SHA-512.
    

---

## Onde pode ser utilizada

- Integridade de arquivos.
    
- Comparação de dados.
    
- Identificação de arquivos.
    
- Análise de Malware.
    
- Ferramentas de segurança.
    

---

## Exemplo

```python
import hashlib

texto = b"admin"

hash_sha256 = hashlib.sha256(
    texto
).hexdigest()

print(hash_sha256)
```

---

# `struct`

## Para que serve

Converter valores Python para estruturas binárias e vice-versa.

Muito importante para trabalhar com:

- Protocolos.
    
- Arquivos binários.
    
- Engenharia Reversa.
    
- Exploit Development.
    
- Pacotes de rede.
    

---

## Exemplo

```python
import struct

dados = struct.pack(
    "<I",
    1337
)

print(dados)
```

---

## Convertendo novamente

```python
numero = struct.unpack(
    "<I",
    dados
)

print(numero)
```

---

# `argparse`

## Para que serve

Criar ferramentas de linha de comando.

Permite criar argumentos como:

```text
-h
--help
--host
--port
--timeout
```

---

## Onde pode ser utilizada

Praticamente qualquer ferramenta CLI.

Exemplo:

```bash
python scanner.py --host 10.10.10.10 --port 80
```

---

## Exemplo

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "--host",
    required=True
)

args = parser.parse_args()

print(args.host)
```

---

# `logging`

## Para que serve

Registrar eventos e erros de programas.

Melhor do que utilizar somente:

```python
print()
```

em ferramentas maiores.

---

## Onde pode ser utilizada

- Debug.
    
- Relatórios.
    
- Logs de scanner.
    
- Erros.
    
- Informações de execução.
    

---

## Exemplo

```python
import logging

logging.basicConfig(
    level=logging.INFO
)

logging.info(
    "Programa iniciado"
)
```

---

# `concurrent.futures`

## Para que serve

Executar várias tarefas concorrentemente.

Muito útil quando existem várias operações independentes.

---

## Onde pode ser utilizada

- Processamento de vários arquivos.
    
- Várias requisições.
    
- Vários hosts.
    
- Tarefas de rede.
    
- Automação.
    

---

## Exemplo

```python
from concurrent.futures import ThreadPoolExecutor

def processar(numero):

    return numero * 2


with ThreadPoolExecutor(
    max_workers=4
) as executor:

    resultado = executor.map(
        processar,
        range(10)
    )

print(
    list(resultado)
)
```

---

# `ctypes`

## Para que serve

Interagir com bibliotecas nativas escritas em C.

É um assunto mais avançado.

---

## Onde pode ser utilizada

- Windows API.
    
- Linux libraries.
    
- Engenharia Reversa.
    
- Malware Analysis.
    
- Low-level.
    
- Interoperabilidade Python/C.
    

---

## Exemplo simples

```python
import ctypes

print(
    ctypes.sizeof(
        ctypes.c_int
    )
)
```

---

# Bibliotecas externas

Depois de dominar melhor a biblioteca padrão, algumas bibliotecas externas são muito úteis.

---

# `requests`

## Para que serve

Realizar requisições HTTP.

É uma das bibliotecas mais importantes para automação Web.

---

## Onde pode ser utilizada

- APIs.
    
- Web.
    
- Automação.
    
- Testes em laboratórios.
    
- Coleta de conteúdo HTTP.
    

---

## Exemplo

```python
import requests

response = requests.get(
    "https://example.com"
)

print(
    response.status_code
)

print(
    response.headers
)
```

---

# `Scapy`

## Para que serve

Criar, enviar, receber e analisar pacotes de rede.

Muito útil para estudar protocolos.

---

## Onde pode ser utilizada

- Redes.
    
- TCP/IP.
    
- ICMP.
    
- ARP.
    
- Sniffing em laboratório.
    
- Construção de pacotes.
    
- Análise de protocolos.
    

---

## Exemplo

```python
from scapy.all import IP, ICMP

pacote = (
    IP(dst="192.168.1.1")
    /
    ICMP()
)

pacote.show()
```

---

# `pwntools`

## Para que serve

Framework Python voltado para Binary Exploitation e CTFs.

---

## Estudar quando?

Somente depois de possuir uma base melhor em:

- C.
    
- Assembly.
    
- Stack.
    
- Buffer Overflow.
    
- ELF.
    
- GDB.
    
- Exploit Development.
    

---

## Onde pode ser utilizada

- CTF.
    
- Binary Exploitation.
    
- Exploit Development.
    
- ROP.
    
- Comunicação com desafios remotos.
    

---

# `BeautifulSoup`

## Para que serve

Realizar parsing de HTML e XML.

---

## Onde pode ser utilizada

- Web scraping.
    
- Extração de links.
    
- Automação Web.
    
- Parsing de páginas HTML.
    

---

# `Paramiko`

## Para que serve

Implementar comunicação SSH utilizando Python.

---

## Onde pode ser utilizada

- Administração.
    
- Automação.
    
- Laboratórios.
    
- Execução remota autorizada.
    

---

# `dnspython`

## Para que serve

Trabalhar com DNS através de Python.

---

## Onde pode ser utilizada

- Consultas DNS.
    
- A.
    
- AAAA.
    
- MX.
    
- TXT.
    
- NS.
    
- Automação de enumeração DNS em ambientes autorizados.
    

---

# `cryptography`

## Para que serve

Trabalhar com recursos criptográficos.

---

## Onde pode ser utilizada

- Criptografia.
    
- Certificados.
    
- Chaves.
    
- Assinaturas digitais.
    
- TLS.
    
- Segurança de aplicações.
    

---

# Ordem recomendada de estudo

```text
1. os

↓

2. pathlib

↓

3. subprocess

↓

4. sys

↓

5. re

↓

6. socket

↓

7. ipaddress

↓

8. json

↓

9. base64 / binascii

↓

10. hashlib

↓

11. logging

↓

12. struct

↓

13. concurrent.futures

↓

14. requests

↓

15. Scapy

↓

16. ctypes

↓

17. pwntools
```

---

# Agrupando por objetivo

## Linux e Sistema

```text
os
pathlib
subprocess
sys
logging
```

---

## Redes

```text
socket
ipaddress
struct
Scapy
dnspython
```

---

## Web

```text
requests
json
re
BeautifulSoup
```

---

## Dados e Encoding

```text
json
base64
binascii
hashlib
```

---

## Binary Exploitation / Engenharia Reversa

```text
struct
ctypes
pwntools
```

---

## Automação

```text
argparse
logging
subprocess
concurrent.futures
pathlib
```

---

# Regra de estudo

Não tente estudar todas as bibliotecas antes de criar projetos.

Utilize este ciclo:

```text
Aprender biblioteca

↓

Testar funções principais

↓

Criar pequenos exemplos

↓

Usar em um projeto

↓

Encontrar limitações

↓

Estudar recursos mais avançados
```

A biblioteca deve resolver um problema real do seu projeto, e não apenas ser decorada.