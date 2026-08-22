---
tags:
  - Python
  - Bibliotecas
  - Requests
  - HTTP
---
## O que é?

O **Requests** é uma biblioteca Python utilizada para realizar requisições HTTP e HTTPS.

Ela permite que um programa Python se comunique com servidores Web, APIs e aplicações que utilizam o protocolo HTTP.

Com o Requests podemos:

- Fazer requisições `GET`
- Fazer requisições `POST`
- Enviar Headers
- Enviar Cookies
- Enviar parâmetros
- Enviar dados
- Enviar JSON
- Fazer autenticação
- Trabalhar com sessões
- Analisar respostas HTTP
- Fazer download de arquivos
- Consumir APIs
- Automatizar tarefas Web

Exemplo básico:

```python
import requests

response = requests.get("https://example.com")

print(response.status_code)
```

Resultado:

```text
200
```

---

# Instalação

O Requests não faz parte da biblioteca padrão do Python.

Para instalar:

```bash
pip install requests
```

Ou:

```bash
python -m pip install requests
```

Depois podemos importar a biblioteca:

```python
import requests
```

> [!NOTE]
> Diferente de bibliotecas como `argparse`, o `requests` precisa ser instalado separadamente.

---

# Quando utilizar?

O Requests pode ser utilizado sempre que um programa Python precisar se comunicar com um servidor através de HTTP ou HTTPS.

Exemplos:

- Consumir APIs
- Automatizar sites
- Fazer Web Scraping
- Fazer download de arquivos
- Criar Web Crawlers
- Testar endpoints
- Enviar formulários
- Trabalhar com autenticação
- Criar ferramentas de automação

Em Cyber Security também pode ser utilizado em:

- Enumeradores Web
- Banner Grabbing
- Web Crawlers
- Reconhecimento
- Análise de aplicações Web
- Testes de APIs
- CTFs
- Laboratórios
- Ferramentas de Pentest autorizado

> [!WARNING]
> Utilize ferramentas de segurança somente em sistemas próprios, laboratórios, CTFs ou ambientes nos quais você possui autorização.

---

# HTTP

Antes de entender o Requests, é importante entender o funcionamento básico do HTTP.

Quando um cliente deseja acessar um recurso de um servidor, ele envia uma **HTTP Request**.

O servidor processa essa requisição e retorna uma **HTTP Response**.

O fluxo pode ser representado assim:

```text
Cliente
   │
   │ HTTP Request
   ▼
Servidor
   │
   │ HTTP Response
   ▼
Cliente
```

No nosso caso:

```text
Python
   │
   ▼
Requests
   │
   │ HTTP Request
   ▼
Servidor Web
   │
   │ HTTP Response
   ▼
Requests
   │
   ▼
Python
```

---

# HTTP Request

Uma Request é a requisição enviada pelo cliente para o servidor.

Uma requisição pode possuir várias informações.

De forma simplificada:

```text
Request
│
├── Method
├── URL
├── Headers
├── Parameters
├── Cookies
└── Body
```

Exemplo:

```http
GET /index.html HTTP/1.1
Host: example.com
User-Agent: MeuPrograma/1.0
```

Nesse exemplo:

```text
GET
```

é o método HTTP.

```text
/index.html
```

é o recurso solicitado.

```text
Host
User-Agent
```

são Headers.

---

# HTTP Response

Depois que o servidor recebe a Request, ele envia uma Response.

De forma simplificada:

```text
Response
│
├── Status Code
├── Headers
├── Cookies
└── Body
```

Exemplo:

```http
HTTP/1.1 200 OK
Content-Type: text/html

<html>
    <body>
        Hello World
    </body>
</html>
```

O Requests transforma essa resposta em um objeto Python.

Esse objeto normalmente será armazenado em uma variável:

```python
response
```

---

# Importando o Requests

Para utilizar a biblioteca:

```python
import requests
```

Depois podemos utilizar seus métodos:

```python
requests.get()
requests.post()
requests.put()
requests.delete()
```

Exemplo:

```python
import requests

response = requests.get("https://example.com")
```

---

# requests.get()

## O que é?

O método:

```python
requests.get()
```

realiza uma requisição HTTP utilizando o método:

```text
GET
```

O método `GET` normalmente é utilizado para solicitar algum recurso do servidor.

Exemplo:

```python
import requests

response = requests.get("https://example.com")
```

O fluxo é:

```text
requests
   │
   ▼
get()
   │
   ▼
https://example.com
   │
   ▼
Servidor
   │
   ▼
Response
```

---

# URL

O primeiro parâmetro normalmente passado para `requests.get()` é a URL.

Exemplo:

```python
requests.get(
    "https://example.com"
)
```

A URL informa para qual servidor e recurso a requisição será enviada.

Exemplo:

```text
https://example.com/login
```

Podemos dividir:

```text
https://
   │
   └── protocolo

example.com
   │
   └── domínio

/login
   │
   └── recurso
```

---

# Armazenando a Response

Normalmente armazenamos o resultado de uma requisição em uma variável.

```python
response = requests.get(
    "https://example.com"
)
```

Agora:

```text
response
```

contém um objeto do tipo:

```text
Response
```

Podemos verificar:

```python
print(type(response))
```

Resultado semelhante a:

```text
<class 'requests.models.Response'>
```

---

# Response

O objeto `Response` representa a resposta recebida do servidor.

Ele possui diversos atributos e métodos que podemos utilizar para analisar essa resposta.

Os mais importantes inicialmente são:

| Atributo / Método | Função |
|---|---|
| `response.status_code` | Código HTTP |
| `response.text` | Conteúdo como texto |
| `response.content` | Conteúdo como bytes |
| `response.headers` | Headers da resposta |
| `response.cookies` | Cookies recebidos |
| `response.url` | URL final |
| `response.json()` | Converte JSON para Python |

---

# response.status_code

Retorna o código HTTP da resposta.

Exemplo:

```python
import requests

response = requests.get(
    "https://example.com"
)

print(response.status_code)
```

Resultado:

```text
200
```

---

# Principais Status Codes

Alguns códigos HTTP importantes:

| Código | Significado |
|---:|---|
| `200` | OK |
| `201` | Created |
| `204` | No Content |
| `301` | Moved Permanently |
| `302` | Found |
| `304` | Not Modified |
| `400` | Bad Request |
| `401` | Unauthorized |
| `403` | Forbidden |
| `404` | Not Found |
| `405` | Method Not Allowed |
| `429` | Too Many Requests |
| `500` | Internal Server Error |
| `502` | Bad Gateway |
| `503` | Service Unavailable |

---

# Verificando Status Code

Podemos utilizar condições para verificar a resposta.

```python
import requests

response = requests.get(
    "https://example.com"
)

if response.status_code == 200:
    print("Página encontrada")

elif response.status_code == 404:
    print("Página não encontrada")
```

Também podemos verificar vários códigos:

```python
if response.status_code in (200, 201):
    print("Requisição realizada com sucesso")
```

---

# response.text

O atributo:

```python
response.text
```

retorna o conteúdo da resposta como uma `str`.

Exemplo:

```python
import requests

response = requests.get(
    "https://example.com"
)

print(response.text)
```

Se o servidor retornar HTML, podemos receber algo semelhante a:

```html
<!DOCTYPE html>
<html>
    <body>
        <h1>Hello World</h1>
    </body>
</html>
```

---

# Verificando conteúdo da página

Podemos utilizar `response.text` para procurar determinada informação dentro da resposta.

Exemplo:

```python
import requests

response = requests.get(
    "https://example.com"
)

if "Hello" in response.text:
    print("Texto encontrado")
```

Outro exemplo:

```python
if "login" in response.text.lower():
    print("Possível página de login encontrada")
```

Isso pode ser útil em automações e ferramentas de análise Web.

---

# response.content

O atributo:

```python
response.content
```

retorna o conteúdo da resposta em formato `bytes`.

Exemplo:

```python
import requests

response = requests.get(
    "https://example.com"
)

print(type(response.content))
```

Resultado:

```text
<class 'bytes'>
```

---

# text x content

A principal diferença:

```text
response.text
      │
      ▼
     str
```

Enquanto:

```text
response.content
      │
      ▼
    bytes
```

Exemplo:

```python
print(type(response.text))
```

Resultado:

```text
<class 'str'>
```

E:

```python
print(type(response.content))
```

Resultado:

```text
<class 'bytes'>
```

---

# Quando utilizar content?

O `response.content` é especialmente útil quando estamos trabalhando com dados binários.

Por exemplo:

- Imagens
- PDFs
- Arquivos
- Downloads
- Outros dados binários

Exemplo:

```python
import requests

response = requests.get(
    "https://example.com/arquivo.pdf"
)

with open("arquivo.pdf", "wb") as file:
    file.write(response.content)
```

O:

```text
wb
```

significa:

```text
write binary
```

Ou seja, o arquivo será escrito em modo binário.

---

# response.url

O atributo:

```python
response.url
```

retorna a URL associada à resposta.

Exemplo:

```python
import requests

response = requests.get(
    "https://example.com"
)

print(response.url)
```

Resultado semelhante a:

```text
https://example.com/
```

Esse atributo também é útil quando existem redirecionamentos.

---

# response.headers

O atributo:

```python
response.headers
```

permite acessar os Headers enviados pelo servidor.

Exemplo:

```python
import requests

response = requests.get(
    "https://example.com"
)

print(response.headers)
```

Podemos receber algo semelhante a:

```text
{
    'Content-Type': 'text/html',
    'Content-Length': '1256',
    'Server': 'nginx'
}
```

Podemos acessar um Header específico:

```python
print(
    response.headers.get("Content-Type")
)
```

---

# Request Headers x Response Headers

É importante diferenciar os dois.

## Request Headers

São enviados:

```text
Cliente → Servidor
```

Exemplo:

```text
User-Agent
Accept
Authorization
```

---

## Response Headers

São enviados:

```text
Servidor → Cliente
```

Exemplo:

```text
Content-Type
Content-Length
Server
Set-Cookie
```

Visualmente:

```text
          Request
Python ──────────────► Servidor
        Headers


          Response
Python ◄────────────── Servidor
        Headers
```

Os Headers enviados pelo nosso programa serão estudados com mais detalhes na [[Requests - Parte 2 - Headers e Session]].

---

# requests.get() - Parâmetros básicos

A forma simplificada é:

```python
requests.get(url)
```

Porém, o método aceita vários parâmetros.

Uma representação simplificada:

```python
requests.get(
    url,
    params=None,
    **kwargs
)
```

Os principais parâmetros que encontraremos são:

| Parâmetro | Função |
|---|---|
| `url` | URL da requisição |
| `params` | Parâmetros da URL |
| `headers` | Headers HTTP |
| `cookies` | Cookies |
| `auth` | Autenticação |
| `timeout` | Tempo limite |
| `allow_redirects` | Permitir redirecionamentos |
| `verify` | Verificação de certificado TLS/SSL |
| `proxies` | Utilização de proxy |
| `stream` | Controle do download da resposta |

Nem todos precisam ser utilizados em todas as requisições.

---

# timeout=

O parâmetro:

```python
timeout=
```

define quanto tempo o Requests deve esperar por uma resposta antes de gerar um erro de timeout.

Exemplo:

```python
import requests

response = requests.get(
    "https://example.com",
    timeout=5
)
```

Nesse exemplo:

```text
timeout=5
```

define um limite de tempo de 5 segundos.

---

# Por que utilizar timeout?

Sem um timeout, dependendo da situação, uma requisição pode ficar aguardando por muito tempo.

Em ferramentas de automação isso pode ser um problema.

Exemplo:

```python
try:

    response = requests.get(
        "https://example.com",
        timeout=5
    )

except requests.Timeout:

    print("Tempo limite excedido")
```

---

# Tratamento de erros

O Requests possui exceções próprias.

Podemos capturar erros utilizando:

```python
try:
    response = requests.get(
        "https://example.com",
        timeout=5
    )

except requests.RequestException as error:

    print(f"Erro: {error}")
```

`RequestException` é uma classe base para várias exceções relacionadas às requisições.

Exemplo:

```python
try:

    response = requests.get(
        "https://example.com",
        timeout=5
    )

except requests.RequestException as error:

    print(f"[!] Erro: {error}")
```

---

# Exemplo completo

Um exemplo simples utilizando os conceitos estudados:

```python
import requests

url = "https://example.com"

try:

    response = requests.get(
        url,
        timeout=5
    )

    print(f"URL: {response.url}")
    print(f"Status: {response.status_code}")
    print(f"Tipo: {response.headers.get('Content-Type')}")

except requests.RequestException as error:

    print(f"[!] Erro: {error}")
```

Possível saída:

```text
URL: https://example.com/
Status: 200
Tipo: text/html
```

---

# Exemplo aplicado em Cyber Security

> [!WARNING]
> O exemplo abaixo é voltado para aprendizado e deve ser executado somente contra sistemas autorizados.

Podemos utilizar o Requests para verificar uma lista de caminhos Web.

```python
import requests

url = "https://example.com"

paths = [
    "admin",
    "login",
    "robots.txt",
    "api"
]

for path in paths:

    target = f"{url}/{path}"

    try:

        response = requests.get(
            target,
            timeout=5
        )

        print(
            response.status_code,
            target
        )

    except requests.RequestException as error:

        print(
            f"[!] Erro: {error}"
        )
```

O fluxo é:

```text
paths.txt
    │
    ├── admin
    ├── login
    ├── robots.txt
    └── api
         │
         ▼
    requests.get()
         │
         ▼
      Servidor
         │
         ▼
      Response
         │
         ▼
    status_code
```

Esse conceito pode servir como base para ferramentas de enumeração Web em ambientes autorizados.

---

# Exemplo baseado em um scanner

Um exemplo mais próximo de uma ferramenta de linha de comando:

```python
import requests
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "-u",
    "--url",
    required=True,
    help="URL do alvo"
)

args = parser.parse_args()

url = args.url.rstrip("/")

try:

    with open(
        "paths.txt",
        "r",
        encoding="utf-8"
    ) as files:

        for path in files:

            path = path.strip().lstrip("/")

            if not path:
                continue

            target = f"{url}/{path}"

            response = requests.get(
                target,
                timeout=5
            )

            print(
                response.status_code,
                target
            )

except KeyboardInterrupt:

    print("\n[!] Programa encerrado...")

except requests.RequestException as error:

    print(f"[!] Erro: {error}")
```

Nesse exemplo estamos combinando duas bibliotecas:

```python
import argparse
import requests
```

O:

```text
argparse
```

é responsável por receber argumentos do terminal.

O:

```text
requests
```

é responsável por realizar as requisições HTTP.

---

# Fluxo do programa

```text
Usuário
   │
   │ -u https://example.com
   ▼
argparse
   │
   │ args.url
   ▼
Requests
   │
   │ GET
   ▼
Servidor
   │
   │ Response
   ▼
status_code
   │
   ▼
Terminal
```

---

# Métodos HTTP disponíveis

Além do:

```python
requests.get()
```

o Requests possui métodos para outros métodos HTTP.

| Método HTTP | Requests |
|---|---|
| GET | `requests.get()` |
| POST | `requests.post()` |
| PUT | `requests.put()` |
| PATCH | `requests.patch()` |
| DELETE | `requests.delete()` |
| HEAD | `requests.head()` |
| OPTIONS | `requests.options()` |

Exemplo:

```python
requests.get(url)
```

```python
requests.post(url)
```

```python
requests.put(url)
```

```python
requests.delete(url)
```

O estudo desses métodos será aprofundado nas próximas partes.

---

# Resumo

| Conceito | Função |
|---|---|
| `requests` | Biblioteca para HTTP/HTTPS |
| `requests.get()` | Realiza uma requisição GET |
| `response` | Objeto que representa a resposta |
| `status_code` | Código HTTP |
| `text` | Conteúdo como `str` |
| `content` | Conteúdo como `bytes` |
| `headers` | Headers da resposta |
| `url` | URL da resposta |
| `timeout` | Limite de espera |
| `RequestException` | Exceções de requisição |

---

# Fluxo geral

```text
                    REQUESTS
                       │
                       ▼
                  requests.get()
                       │
                       ▼
                 HTTP Request
                       │
                       ▼
                    SERVIDOR
                       │
                       ▼
                 HTTP Response
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
    status_code     headers        body
                                      │
                              ┌───────┴───────┐
                              ▼               ▼
                           text            content
                            str              bytes
```

---

# O que estudar na próxima parte?

Na [[Requests - Parte 2 - Headers e Session]] vamos aprofundar:

- `headers=`
- O que são Headers
- Request Headers
- Response Headers
- `User-Agent`
- `Accept`
- `Authorization`
- `Cookie`
- Como criar um `dict` de Headers
- Como passar Headers no `requests.get()`
- `Session()`
- `session.get()`
- `session.headers`
- `session.headers.update()`
- Persistência durante a execução
- Cookies mantidos pela Session
- Diferença entre `requests.get()` e `session.get()`
- Exemplos aplicados à Cyber Security

---

# Conclusão

O Requests facilita a comunicação entre programas Python e servidores que utilizam HTTP/HTTPS.

O conceito fundamental é:

```text
Request
   │
   ▼
Servidor
   │
   ▼
Response
```

Através do objeto `Response`, conseguimos analisar informações como:

```python
response.status_code
response.text
response.content
response.headers
response.url
```

Esses conceitos formam a base para posteriormente trabalhar com:

```text
Headers
Session
Cookies
Parameters
POST
JSON
Authentication
APIs
Web Security
```

A partir daqui, o próximo passo é entender como controlar **o que o Python envia para o servidor**, principalmente através de Headers e Sessions.