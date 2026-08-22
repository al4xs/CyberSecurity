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

---

# Headers

## O que são?

**Headers** são informações adicionais enviadas junto com uma requisição HTTP.

Eles permitem que o cliente informe informações ao servidor sobre a requisição.

Exemplo:

```http
GET / HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
```

Nesse exemplo temos três Headers:

```text
Host
User-Agent
Accept
```

Cada Header possui:

```text
Nome: Valor
```

Por exemplo:

```text
User-Agent: Mozilla/5.0
```

Onde:

```text
User-Agent
```

é o nome do Header.

E:

```text
Mozilla/5.0
```

é o valor.

---

# Estrutura de um Header

Podemos visualizar um Header dessa forma:

```text
Header
  │
  ├── Nome
  │
  └── Valor
```

Exemplo:

```text
User-Agent: Mozilla/5.0
```

```text
      User-Agent
           │
           ▼
      nome do Header
           │
           ▼
      Mozilla/5.0
           │
           ▼
       valor
```

Uma requisição pode possuir vários Headers:

```text
User-Agent: Mozilla/5.0
Accept: text/html
Authorization: Bearer TOKEN
```

---

# Headers no Requests

No Requests podemos enviar Headers utilizando o parâmetro:

```python
headers=
```

Exemplo:

```python
import requests

headers = {
    "User-Agent": "Meu User-Agent"
}

response = requests.get(
    "https://example.com",
    headers=headers
)
```

Nesse caso:

```python
headers
```

é um `dict` contendo os Headers que queremos enviar.

---

# Por que é utilizado um dict?

O Requests espera os Headers em uma estrutura semelhante a:

```python
{
    "Nome-Do-Header": "Valor"
}
```

Um `dict` é apropriado porque trabalha com pares:

```text
chave → valor
```

Exemplo:

```python
headers = {
    "User-Agent": "Meu User-Agent",
    "Accept": "text/html"
}
```

Podemos visualizar:

```text
headers
   │
   ├── "User-Agent" → "Meu User-Agent"
   │
   └── "Accept"     → "text/html"
```

A chave representa o nome do Header.

O valor representa o conteúdo daquele Header.

---

# Um Header

Podemos enviar apenas um Header:

```python
headers = {
    "User-Agent": "Meu User-Agent"
}
```

Depois:

```python
requests.get(
    url,
    headers=headers
)
```

---

# Vários Headers

Também podemos enviar vários Headers dentro do mesmo `dict`.

```python
headers = {
    "User-Agent": "Meu User-Agent",
    "Accept": "text/html",
    "Accept-Language": "pt-BR"
}
```

Depois:

```python
response = requests.get(
    url,
    headers=headers
)
```

O Requests enviará os Headers definidos na requisição.

---

# Exemplo completo

```python
import requests

url = "https://example.com"

headers = {
    "User-Agent": "Meu User-Agent",
    "Accept": "text/html"
}

response = requests.get(
    url,
    headers=headers
)

print(response.status_code)
```

---

# User-Agent

## O que é?

O:

```text
User-Agent
```

é um Header HTTP utilizado pelo cliente para informar ao servidor uma identificação sobre o software que está realizando a requisição.

Exemplo:

```http
User-Agent: Mozilla/5.0
```

Um navegador normalmente envia um User-Agent contendo informações relacionadas ao navegador e ao sistema.

Por exemplo:

```text
Mozilla/5.0 (...)
```

O conteúdo exato pode variar dependendo do navegador, sistema operacional e versão.

---

# User-Agent no Requests

Uma requisição simples:

```python
import requests

response = requests.get(
    "https://example.com"
)
```

é realizada pelo Requests e possui seus próprios Headers padrão.

Podemos definir nosso próprio User-Agent:

```python
headers = {
    "User-Agent": "Meu User-Agent"
}
```

E enviar:

```python
response = requests.get(
    "https://example.com",
    headers=headers
)
```

---

# Alterando o User-Agent

Podemos simplesmente modificar o valor:

```python
headers = {
    "User-Agent": "Mozilla/5.0"
}
```

Ou:

```python
headers = {
    "User-Agent": "MeuScanner/1.0"
}
```

Ou:

```python
headers = {
    "User-Agent": "PythonSecurityTool/1.0"
}
```

O valor é uma `str`.

---

# User-Agent como variável

Também podemos armazenar o valor em uma variável:

```python
user_agent = "MeuScanner/1.0"

headers = {
    "User-Agent": user_agent
}
```

Depois:

```python
response = requests.get(
    url,
    headers=headers
)
```

Isso é útil quando queremos alterar o User-Agent dinamicamente.

---

# User-Agent recebido por argumento

Podemos combinar `argparse` com Requests.

Exemplo:

```python
import argparse
import requests

parser = argparse.ArgumentParser()

parser.add_argument(
    "-u",
    "--url",
    required=True
)

parser.add_argument(
    "-A",
    "--user-agent",
    default="MeuScanner/1.0"
)

args = parser.parse_args()

headers = {
    "User-Agent": args.user_agent
}

response = requests.get(
    args.url,
    headers=headers
)

print(response.status_code)
```

Agora podemos executar:

```bash
python scanner.py -u https://example.com
```

Nesse caso será utilizado:

```text
MeuScanner/1.0
```

Ou podemos definir outro:

```bash
python scanner.py \
-u https://example.com \
-A "Mozilla/5.0"
```

---

# Headers e dict

É importante entender que:

```python
headers = {
    "User-Agent": "Meu User-Agent"
}
```

cria um `dict`.

Podemos verificar:

```python
print(type(headers))
```

Resultado:

```text
<class 'dict'>
```

Podemos acessar um valor específico:

```python
print(headers["User-Agent"])
```

Resultado:

```text
Meu User-Agent
```

---

# Adicionando Headers dinamicamente

Como `headers` é um `dict`, podemos adicionar novos valores normalmente.

Exemplo:

```python
headers = {
    "User-Agent": "Meu User-Agent"
}
```

Depois:

```python
headers["Accept"] = "text/html"
```

Agora o `dict` possui:

```python
{
    "User-Agent": "Meu User-Agent",
    "Accept": "text/html"
}
```

Também podemos fazer:

```python
headers["Authorization"] = "Bearer TOKEN"
```

Agora:

```python
{
    "User-Agent": "Meu User-Agent",
    "Accept": "text/html",
    "Authorization": "Bearer TOKEN"
}
```

---

# .update()

O método:

```python
.update()
```

é um método de `dict` utilizado para adicionar ou atualizar valores.

Exemplo:

```python
dados = {
    "nome": "Marcos"
}
```

Podemos adicionar:

```python
dados.update({
    "idade": 24
})
```

Agora:

```python
print(dados)
```

Resultado:

```python
{
    "nome": "Marcos",
    "idade": 24
}
```

---

# update() com vários valores

Podemos adicionar vários valores de uma vez:

```python
dados.update({
    "idade": 24,
    "cidade": "São Paulo",
    "profissao": "Programador"
})
```

Resultado:

```python
{
    "nome": "Marcos",
    "idade": 24,
    "cidade": "São Paulo",
    "profissao": "Programador"
}
```

---

# update() também atualiza valores existentes

Se a chave já existir:

```python
dados = {
    "nome": "Marcos",
    "idade": 20
}
```

Podemos atualizar:

```python
dados.update({
    "idade": 24
})
```

Agora:

```python
{
    "nome": "Marcos",
    "idade": 24
}
```

O valor antigo:

```text
20
```

foi substituído por:

```text
24
```

Portanto:

```text
chave não existe
       │
       ▼
    adiciona

chave já existe
       │
       ▼
    atualiza
```

---

# update() aplicado aos Headers

Podemos utilizar o mesmo conceito com Headers.

Inicialmente:

```python
headers = {
    "User-Agent": "Meu User-Agent"
}
```

Depois:

```python
headers.update({
    "Accept": "text/html"
})
```

Resultado:

```python
{
    "User-Agent": "Meu User-Agent",
    "Accept": "text/html"
}
```

---

# Atualizando um Header

Também podemos atualizar um Header existente.

```python
headers = {
    "User-Agent": "Meu User-Agent"
}
```

Depois:

```python
headers.update({
    "User-Agent": "Mozilla/5.0"
})
```

Agora:

```python
{
    "User-Agent": "Mozilla/5.0"
}
```

O valor anterior foi substituído.

---

# Session e Headers

Quando utilizamos uma `Session`, podemos definir Headers padrão para aquela Session.

Exemplo:

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})
```

Agora podemos realizar:

```python
response = session.get(
    "https://example.com"
)
```

O Header definido na Session será utilizado nas requisições realizadas através dela.

---

# O que significa Session?

Uma `Session` representa uma sessão de comunicação entre o nosso programa e os servidores.

Podemos criar uma Session:

```python
session = requests.Session()
```

Depois utilizamos:

```python
session.get()
```

em vez de:

```python
requests.get()
```

Exemplo:

```python
import requests

session = requests.Session()

response = session.get(
    "https://example.com"
)
```

---

# requests.get() x session.get()

Sem Session:

```python
requests.get(url)
```

Com Session:

```python
session = requests.Session()

session.get(url)
```

A Session permite manter determinadas informações e configurações entre várias requisições realizadas através daquela Session.

Por exemplo:

```text
Session
   │
   ├── Headers
   ├── Cookies
   └── outras configurações
```

Isso será especialmente útil quando fazemos várias requisições para o mesmo serviço.

---

# Headers diretamente na requisição

Podemos definir:

```python
headers = {
    "User-Agent": "MeuScanner/1.0"
}
```

E utilizar:

```python
requests.get(
    url,
    headers=headers
)
```

Nesse caso o Header é fornecido especificamente para aquela requisição.

---

# Headers na Session

Também podemos definir:

```python
session = requests.Session()

session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})
```

Depois:

```python
session.get(url1)
session.get(url2)
session.get(url3)
```

As requisições realizadas pela Session utilizarão essa configuração de Header.

Visualmente:

```text
Session
   │
   └── User-Agent: MeuScanner/1.0
          │
          ├── GET /login
          │
          ├── GET /admin
          │
          └── GET /api
```

---

# Quando utilizar Headers diretamente?

Quando o Header será utilizado apenas em uma determinada requisição.

Exemplo:

```python
requests.get(
    url,
    headers={
        "User-Agent": "Scanner/1.0"
    }
)
```

---

# Quando utilizar Headers na Session?

Quando queremos manter uma configuração para várias requisições.

Exemplo:

```python
session = requests.Session()

session.headers.update({
    "User-Agent": "Scanner/1.0"
})

session.get(url1)
session.get(url2)
session.get(url3)
```

Isso evita repetir:

```python
headers=headers
```

em todas as chamadas.

---

# Exemplo prático

Imagine que temos várias URLs:

```python
urls = [
    "https://example.com/",
    "https://example.com/login",
    "https://example.com/admin"
]
```

Sem Session:

```python
import requests

headers = {
    "User-Agent": "MeuScanner/1.0"
}

for url in urls:

    response = requests.get(
        url,
        headers=headers
    )

    print(
        response.status_code,
        url
    )
```

Com Session:

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})

for url in urls:

    response = session.get(url)

    print(
        response.status_code,
        url
    )
```

A segunda abordagem é especialmente interessante quando várias requisições fazem parte da mesma interação.

---

# Headers comuns

Existem diversos Headers HTTP.

Alguns importantes:

| Header | Função |
|---|---|
| `User-Agent` | Identifica o cliente |
| `Accept` | Informa tipos de conteúdo aceitos |
| `Accept-Language` | Idiomas preferidos |
| `Content-Type` | Tipo do conteúdo enviado |
| `Authorization` | Informações de autenticação |
| `Cookie` | Envia Cookies |
| `Referer` | Indica uma página de referência |
| `Host` | Indica o host solicitado |

---

# Accept

O Header:

```text
Accept
```

informa quais tipos de conteúdo o cliente aceita receber.

Exemplo:

```python
headers = {
    "Accept": "text/html"
}
```

Também podemos utilizar:

```python
headers = {
    "Accept": "application/json"
}
```

Isso é muito comum quando estamos trabalhando com APIs.

---

# Accept-Language

Indica uma preferência de idioma.

Exemplo:

```python
headers = {
    "Accept-Language": "pt-BR"
}
```

Podemos combinar:

```python
headers = {
    "User-Agent": "MeuScanner/1.0",
    "Accept": "text/html",
    "Accept-Language": "pt-BR"
}
```

---

# Content-Type

O Header:

```text
Content-Type
```

indica o tipo do conteúdo enviado no corpo da requisição.

Exemplo:

```text
Content-Type: application/json
```

Isso significa que o conteúdo enviado é JSON.

Outro exemplo:

```text
Content-Type: application/x-www-form-urlencoded
```

Esse Header será importante quando estudarmos:

```python
data=
```

e:

```python
json=
```

---

# Authorization

O Header:

```text
Authorization
```

é utilizado por muitas APIs para enviar informações de autenticação.

Exemplo:

```python
headers = {
    "Authorization": "Bearer TOKEN"
}
```

> [!WARNING]
> Nunca coloque tokens, senhas ou chaves reais diretamente em código que será compartilhado ou versionado.

---

# Referer

O Header:

```text
Referer
```

pode informar ao servidor a página de origem da requisição.

Exemplo:

```python
headers = {
    "Referer": "https://example.com/login"
}
```

É importante observar que o comportamento e a interpretação desses Headers dependem do servidor e da aplicação.

---

# Exemplo com vários Headers

```python
import requests

headers = {
    "User-Agent": "MeuScanner/1.0",
    "Accept": "text/html",
    "Accept-Language": "pt-BR",
    "Referer": "https://example.com/"
}

response = requests.get(
    "https://example.com/login",
    headers=headers,
    timeout=5
)

print(response.status_code)
```

---

# Exemplo aplicado à Cyber Security

Em um ambiente autorizado, podemos utilizar Headers para analisar como uma aplicação responde a diferentes clientes.

Por exemplo:

```python
import requests

url = "https://example.com"

user_agents = [
    "Mozilla/5.0",
    "MeuScanner/1.0",
    "PythonSecurityTool/1.0"
]

for user_agent in user_agents:

    headers = {
        "User-Agent": user_agent
    }

    response = requests.get(
        url,
        headers=headers,
        timeout=5
    )

    print(
        response.status_code,
        user_agent
    )
```

Isso permite comparar as respostas obtidas com diferentes valores de Header em um ambiente autorizado.

---

# Session com User-Agent

Uma forma mais organizada quando várias requisições utilizam o mesmo User-Agent:

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})

urls = [
    "https://example.com/",
    "https://example.com/login",
    "https://example.com/admin"
]

for url in urls:

    response = session.get(
        url,
        timeout=5
    )

    print(
        response.status_code,
        url
    )
```

Nesse exemplo:

```python
session.headers.update()
```

define um Header padrão para a Session.

Depois:

```python
session.get()
```

utiliza essa Session.

---

# Um detalhe importante sobre persistência

Quando falamos que uma Session possui "persistência", isso **não significa que o User-Agent foi salvo permanentemente no computador ou no site**.

A persistência ocorre durante a utilização daquele objeto `Session`.

Exemplo:

```python
session = requests.Session()

session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})
```

Enquanto esse objeto existir:

```python
session
```

podemos utilizar:

```python
session.get(url1)
session.get(url2)
session.get(url3)
```

e a configuração da Session continuará disponível.

Se o programa for encerrado:

```text
Programa
   │
   ▼
Session criada
   │
   ▼
Requisições
   │
   ▼
Programa encerrado
   │
   ▼
Session deixa de existir
```

Ao executar o programa novamente, uma nova Session será criada.

---

# Session não significa login automático

É importante não confundir:

```python
requests.Session()
```

com:

```text
login
```

Criar uma Session não significa que o usuário está autenticado.

A Session apenas fornece um objeto que pode manter configurações e estado entre requisições.

Por exemplo:

```python
session = requests.Session()
```

não significa:

```text
Usuário autenticado
```

A autenticação depende da aplicação e dos dados enviados.

---

# Resumo

| Conceito | Função |
|---|---|
| `headers=` | Envia Headers em uma requisição |
| `User-Agent` | Identifica o cliente |
| `Accept` | Indica tipos de conteúdo aceitos |
| `Authorization` | Pode transportar informações de autenticação |
| `Content-Type` | Indica o tipo do conteúdo enviado |
| `dict` | Estrutura utilizada para representar os Headers |
| `.update()` | Adiciona ou atualiza valores de um `dict` |
| `requests.Session()` | Cria uma Session |
| `session.headers` | Acessa os Headers da Session |
| `session.headers.update()` | Adiciona ou atualiza Headers da Session |
| `session.get()` | Realiza GET utilizando a Session |

---

# Fluxo completo

```text
                 Python
                    │
                    ▼
             requests.Session()
                    │
                    ▼
                 Session
                    │
                    ├── Headers
                    │      │
                    │      └── User-Agent
                    │
                    └── Cookies
                    │
                    ▼
              session.get()
                    │
                    ▼
              HTTP Request
                    │
                    ▼
                 Servidor
                    │
                    ▼
             HTTP Response
                    │
                    ▼
                 Python
```

---

# Conceito principal

O ponto mais importante desta parte é entender a relação:

```python
headers = {
    "User-Agent": "MeuScanner/1.0"
}
```

é um `dict`.

Esse `dict` pode ser passado para:

```python
requests.get(
    url,
    headers=headers
)
```

Ou podemos configurar uma Session:

```python
session = requests.Session()

session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})
```

E depois utilizar:

```python
session.get(url)
```

A principal diferença é que, na segunda abordagem, o Header passa a fazer parte da configuração da Session e pode ser reutilizado nas requisições feitas através dela.

---

# Session

## O que é?

O `Session` é um objeto fornecido pelo Requests que permite realizar várias requisições HTTP mantendo determinadas informações e configurações entre elas.

Para criar uma Session:

```python
import requests

session = requests.Session()
```

Agora:

```python
session
```

representa uma sessão de comunicação que pode ser reutilizada.

Podemos realizar:

```python
session.get(url)
```

```python
session.post(url)
```

```python
session.put(url)
```

```python
session.delete(url)
```

entre outras operações.

---

# Por que utilizar Session?

Imagine que um programa precisa realizar várias requisições:

```python
requests.get(url1)

requests.get(url2)

requests.get(url3)

requests.get(url4)
```

Cada chamada é feita diretamente através do módulo `requests`.

Com uma Session:

```python
session = requests.Session()

session.get(url1)
session.get(url2)
session.get(url3)
session.get(url4)
```

A Session permite centralizar configurações e manter determinados estados entre essas requisições.

Por exemplo:

```text
Session
   │
   ├── Headers
   │
   ├── Cookies
   │
   └── outras configurações
         │
         ▼
   ┌───────────────┐
   │               │
   ▼               ▼
GET /login     GET /admin
   │               │
   └───────┬───────┘
           ▼
        servidor
```

---

# Session como um estado compartilhado

Uma forma simples de entender uma Session é imaginar que ela funciona como um objeto que mantém determinadas informações enquanto o programa está executando.

Exemplo:

```python
session = requests.Session()
```

Podemos configurar:

```python
session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})
```

Depois realizar várias requisições:

```python
session.get(url1)
session.get(url2)
session.get(url3)
```

A configuração continua associada à Session.

---

# Session e Cookies

Um dos recursos mais importantes de uma Session é o gerenciamento de **Cookies**.

Cookies são pequenos dados enviados pelo servidor que podem ser armazenados pelo cliente e enviados novamente em requisições posteriores.

Exemplo simplificado:

```text
Cliente ──────── GET /login ────────► Servidor
Cliente ◄────── Set-Cookie ───────── Servidor

Cliente ──────── Cookie ────────────► Servidor
```

Um servidor pode responder:

```http
Set-Cookie: sessionid=abc123
```

O cliente pode armazenar esse Cookie.

Depois, em uma nova requisição:

```http
Cookie: sessionid=abc123
```

O servidor pode utilizar esse Cookie para reconhecer aquela sessão.

---

# O que é um Cookie?

Um Cookie é um dado associado a um domínio que pode ser enviado pelo cliente em requisições futuras.

Exemplo:

```text
sessionid=abc123
```

Outro exemplo:

```text
theme=dark
```

Podemos ter vários Cookies:

```text
sessionid=abc123
theme=dark
language=pt-BR
```

---

# Cookies e Session

Quando utilizamos:

```python
session = requests.Session()
```

o Requests fornece um mecanismo para manter Cookies recebidos durante a utilização daquela Session.

Exemplo:

```python
session = requests.Session()

response = session.get(
    "https://example.com"
)
```

Se o servidor enviar um Cookie apropriado, ele poderá ficar armazenado no Cookie Jar da Session.

Depois:

```python
session.get(
    "https://example.com/dashboard"
)
```

a Session poderá enviar os Cookies aplicáveis à nova requisição.

---

# Cookie Jar

Os Cookies da Session ficam associados ao:

```python
session.cookies
```

Podemos visualizar:

```python
print(session.cookies)
```

Dependendo dos Cookies recebidos, podemos obter uma representação semelhante a:

```text
<RequestsCookieJar[...]>
```

O objeto utilizado pelo Requests para administrar esses Cookies é chamado:

```text
RequestsCookieJar
```

---

# Visualizando Cookies

Exemplo:

```python
import requests

session = requests.Session()

response = session.get(
    "https://example.com"
)

print(session.cookies)
```

Também podemos iterar sobre os Cookies:

```python
for cookie in session.cookies:
    print(cookie.name)
    print(cookie.value)
```

---

# Obtendo nome e valor

Um Cookie possui principalmente:

```text
nome
valor
```

Exemplo:

```text
sessionid=abc123
```

Nesse caso:

```text
nome  → sessionid
valor → abc123
```

Podemos acessar:

```python
for cookie in session.cookies:

    print(
        cookie.name,
        cookie.value
    )
```

---

# Adicionando um Cookie manualmente

Também podemos adicionar Cookies à Session.

Exemplo:

```python
session.cookies.set(
    "teste",
    "123"
)
```

Agora podemos verificar:

```python
print(session.cookies)
```

---

# Exemplo

```python
import requests

session = requests.Session()

session.cookies.set(
    "usuario",
    "admin"
)

response = session.get(
    "https://example.com"
)

print(response.status_code)
```

Nesse caso o Cookie foi adicionado manualmente à Session.

---

# Cookies específicos de um domínio

Também podemos associar um Cookie a um domínio.

Exemplo:

```python
session.cookies.set(
    "sessionid",
    "abc123",
    domain="example.com"
)
```

Isso permite que o Cookie seja associado ao domínio correspondente.

---

# Removendo Cookies

Podemos limpar os Cookies da Session:

```python
session.cookies.clear()
```

Depois:

```python
print(session.cookies)
```

A Cookie Jar estará vazia, caso não existam outros Cookies adicionados posteriormente.

---

# Session e Login

Sessions são especialmente úteis em aplicações que utilizam autenticação baseada em Cookies.

Imagine um sistema:

```text
/login
/dashboard
/admin
/profile
```

Primeiro realizamos o login:

```python
session.post(
    login_url,
    data=credenciais
)
```

O servidor pode responder definindo um Cookie de sessão.

Depois:

```python
session.get(
    dashboard_url
)
```

A Session poderá enviar o Cookie recebido anteriormente.

Visualmente:

```text
             Session
                │
                ▼
          POST /login
                │
                ▼
             Servidor
                │
          Set-Cookie
                │
                ▼
          Session.cookies
                │
                ▼
        GET /dashboard
                │
          Cookie enviado
                │
                ▼
             Servidor
```

Isso é muito diferente de simplesmente fazer:

```python
requests.post(login_url)
```

e depois:

```python
requests.get(dashboard_url)
```

sem manter o estado apropriado.

---

# Exemplo de Login

Um exemplo genérico:

```python
import requests

session = requests.Session()

login_url = "https://example.com/login"
dashboard_url = "https://example.com/dashboard"

dados = {
    "username": "usuario",
    "password": "senha"
}

response = session.post(
    login_url,
    data=dados
)

print(response.status_code)

response = session.get(
    dashboard_url
)

print(response.status_code)
```

A Session é utilizada nas duas requisições:

```python
session.post()
```

e:

```python
session.get()
```

Isso permite que o estado mantido pela Session seja reutilizado.

> [!WARNING]
> O exemplo acima é apenas conceitual. O nome dos campos, método de autenticação, Cookies e demais detalhes dependem da aplicação real.

---

# Session não garante autenticação

É importante entender:

```python
session = requests.Session()
```

não significa que estamos autenticados.

A Session apenas fornece um mecanismo para manter estado e configurações.

A autenticação depende do servidor.

Por exemplo, uma aplicação pode utilizar:

```text
Cookie
```

ou:

```text
Authorization Header
```

ou:

```text
Token
```

ou:

```text
OAuth
```

ou outros mecanismos.

---

# Session e Headers

Podemos combinar Headers e Cookies na mesma Session.

Exemplo:

```python
session = requests.Session()

session.headers.update({
    "User-Agent": "MeuScanner/1.0",
    "Accept": "application/json"
})
```

Depois:

```python
session.get(url)
```

A Session utilizará essas configurações.

---

# Header específico x Header da Session

Podemos definir um Header padrão:

```python
session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})
```

E depois alterar apenas uma requisição:

```python
session.get(
    url,
    headers={
        "User-Agent": "OutroCliente/1.0"
    }
)
```

Nesse caso, o Header fornecido diretamente naquela chamada pode sobrescrever o valor padrão para aquela requisição.

A configuração original da Session continua existindo para as próximas requisições.

---

# Exemplo

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "Scanner/1.0"
})

response = session.get(
    "https://example.com"
)
```

Agora:

```python
response = session.get(
    "https://example.com/admin",
    headers={
        "User-Agent": "OutroScanner/2.0"
    }
)
```

A segunda requisição utiliza:

```text
OutroScanner/2.0
```

Mas a Session continua configurada com:

```text
Scanner/1.0
```

Para as próximas requisições.

---

# Session e Cookies: diferença importante

Headers e Cookies podem ser mantidos pela Session, mas possuem funções diferentes.

### Headers

São informações associadas às requisições.

Exemplo:

```text
User-Agent: Scanner/1.0
```

### Cookies

São dados que podem ser armazenados e posteriormente enviados ao servidor.

Exemplo:

```text
sessionid=abc123
```

Podemos visualizar:

```text
Session
   │
   ├── Headers
   │      │
   │      └── User-Agent
   │
   └── Cookies
          │
          └── sessionid
```

---

# Session e múltiplas requisições

Uma grande vantagem é poder realizar várias requisições utilizando a mesma Session.

Exemplo:

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "MeuScanner/1.0"
})

urls = [
    "https://example.com/",
    "https://example.com/login",
    "https://example.com/admin",
    "https://example.com/api"
]

for url in urls:

    response = session.get(
        url,
        timeout=5
    )

    print(
        response.status_code,
        url
    )
```

Todas as requisições utilizam:

```python
session
```

---

# requests.get() x session.get()

Podemos comparar:

### Sem Session

```python
import requests

response = requests.get(
    url
)
```

### Com Session

```python
import requests

session = requests.Session()

response = session.get(
    url
)
```

Para uma única requisição simples, a diferença de código pode parecer pequena.

A vantagem da Session aparece quando precisamos realizar várias requisições relacionadas.

---

# Reutilizando uma Session

Exemplo:

```python
session = requests.Session()

response1 = session.get(url1)

response2 = session.get(url2)

response3 = session.get(url3)
```

A mesma Session é utilizada nas três requisições.

Isso permite manter configurações e estado entre elas.

---

# Fechando uma Session

Quando terminamos de utilizar uma Session, podemos fechá-la:

```python
session.close()
```

Isso libera os recursos associados à Session.

Também podemos utilizar:

```python
with requests.Session() as session:

    response = session.get(
        url
    )
```

Quando o bloco `with` termina, a Session é fechada automaticamente.

---

# Context Manager

O `with` é útil para garantir que a Session seja fechada corretamente.

Exemplo:

```python
import requests

with requests.Session() as session:

    response = session.get(
        "https://example.com"
    )

    print(response.status_code)
```

Fluxo:

```text
with
 │
 ▼
Cria Session
 │
 ▼
Realiza requisições
 │
 ▼
Sai do bloco
 │
 ▼
Session é fechada
```

---

# Session em um Scanner Web

Sessions são úteis em ferramentas de enumeração Web que precisam realizar várias requisições.

Exemplo:

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "WebScanner/1.0"
})

paths = [
    "/",
    "/robots.txt",
    "/login",
    "/admin",
    "/api"
]

for path in paths:

    url = f"https://example.com{path}"

    response = session.get(
        url,
        timeout=5
    )

    print(
        response.status_code,
        url
    )
```

Esse padrão é bastante útil para ferramentas próprias de laboratório, CTFs e testes autorizados.

---

# Session em enumeração autenticada

Em um ambiente autorizado, podemos utilizar uma Session para manter o estado de uma autenticação enquanto enumeramos endpoints.

Exemplo conceitual:

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "SecurityScanner/1.0"
})

session.post(
    "https://example.com/login",
    data={
        "username": "usuario",
        "password": "senha"
    }
)

paths = [
    "/",
    "/dashboard",
    "/profile",
    "/admin"
]

for path in paths:

    response = session.get(
        f"https://example.com{path}"
    )

    print(
        response.status_code,
        path
    )
```

A vantagem é que todas as requisições fazem parte da mesma Session.

---

# Session como base de uma ferramenta

Uma estrutura comum para ferramentas Web é:

```text
Programa
   │
   ▼
Cria Session
   │
   ├── Configura User-Agent
   │
   ├── Configura Headers
   │
   ├── Configura Cookies
   │
   └── Configura autenticação
           │
           ▼
      Faz requisições
           │
           ▼
      Analisa Response
```

Isso torna o código mais organizado.

---

# Exemplo integrando argparse

Podemos combinar o que aprendemos anteriormente com `argparse`.

```python
import argparse
import requests

parser = argparse.ArgumentParser()

parser.add_argument(
    "-u",
    "--url",
    required=True
)

parser.add_argument(
    "-A",
    "--user-agent",
    default="WebScanner/1.0"
)

args = parser.parse_args()

session = requests.Session()

session.headers.update({
    "User-Agent": args.user_agent
})

response = session.get(
    args.url,
    timeout=5
)

print(response.status_code)
```

Executando:

```bash
python scanner.py \
-u https://example.com
```

Ou:

```bash
python scanner.py \
-u https://example.com \
-A "Mozilla/5.0"
```

---

# Session no código do scanner

Podemos aplicar diretamente ao scanner de caminhos.

Antes:

```python
response = requests.get(
    new_url,
    timeout=5
)
```

Com Session:

```python
session = requests.Session()

session.headers.update({
    "User-Agent": "WebScanner/1.0"
})
```

E dentro do loop:

```python
response = session.get(
    new_url,
    timeout=5
)
```

A estrutura passa a ser:

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "WebScanner/1.0"
})

for url in urls:

    response = session.get(
        url,
        timeout=5
    )
```

---

# Conceito fundamental

A ideia principal da Session pode ser resumida em:

```text
requests
    │
    └── Requisição individual

Session
    │
    ├── Headers
    ├── Cookies
    ├── Estado
    └── Requisições relacionadas
```

Ou seja:

```python
requests.get()
```

é útil para requisições simples.

Enquanto:

```python
session.get()
```

é especialmente útil quando várias requisições fazem parte do mesmo contexto.

---

# Resumo

| Conceito | Função |
|---|---|
| `requests.Session()` | Cria uma Session |
| `session.get()` | Realiza uma requisição GET usando a Session |
| `session.post()` | Realiza POST usando a Session |
| `session.headers` | Acessa os Headers padrão da Session |
| `session.headers.update()` | Adiciona ou atualiza Headers |
| `session.cookies` | Acessa os Cookies da Session |
| `session.cookies.set()` | Adiciona um Cookie |
| `session.cookies.clear()` | Remove os Cookies |
| `session.close()` | Fecha a Session |
| `with requests.Session()` | Gerencia automaticamente o fechamento |
| `RequestsCookieJar` | Estrutura utilizada para administrar Cookies |

---

# O que lembrar

Uma Session não é simplesmente um "User-Agent salvo".

Ela é um objeto que pode manter **configurações e estado entre várias requisições** realizadas através dela.

Por exemplo:

```python
session = requests.Session()
```

Podemos configurar:

```python
session.headers.update({
    "User-Agent": "Scanner/1.0"
})
```

Podemos receber e manter Cookies:

```python
session.cookies
```

E realizar várias requisições:

```python
session.get(url1)
session.get(url2)
session.get(url3)
```

Enquanto o programa estiver utilizando aquele objeto `session`, essas configurações e estados podem ser reutilizados.

---

# Fluxo final

```text
                 Session
                    │
          ┌─────────┴─────────┐
          │                   │
       Headers             Cookies
          │                   │
          ▼                   ▼
    User-Agent          sessionid=abc
          │                   │
          └─────────┬─────────┘
                    │
                    ▼
              session.get()
                    │
                    ▼
              HTTP Request
                    │
                    ▼
                 Servidor
                    │
                    ▼
              HTTP Response
                    │
                    ▼
                 Session
                    │
             mantém estado
                    │
                    ▼
           próxima requisição
```


---

# Enviando dados com Requests

Uma requisição HTTP não precisa conter apenas uma URL.

Podemos enviar informações para o servidor através de:

- parâmetros na URL;
- formulário;
- JSON;
- arquivos;
- Headers;
- Cookies.

O Requests fornece parâmetros específicos para cada situação.

Os principais são:

```python
params=
data=
json=
files=
```

Cada um possui uma finalidade diferente.

---

# params=

## O que é?

O parâmetro:

```python
params=
```

é utilizado para enviar **parâmetros na URL**.

Esses parâmetros normalmente aparecem depois de:

```text
?
```

Exemplo:

```text
https://example.com/search?q=python
```

Nesse caso:

```text
q=python
```

é um parâmetro da URL.

---

# Exemplo com params

Podemos escrever:

```python
import requests

response = requests.get(
    "https://example.com/search",
    params={
        "q": "python"
    }
)
```

O Requests transforma automaticamente o dicionário em parâmetros da URL.

A requisição será equivalente a:

```text
https://example.com/search?q=python
```

---

# O que params recebe?

Normalmente utilizamos um:

```python
dict
```

Exemplo:

```python
params = {
    "q": "python",
    "page": 2
}
```

Depois:

```python
response = requests.get(
    url,
    params=params
)
```

O Requests transforma os valores em parâmetros da URL.

Resultado conceitual:

```text
https://example.com/search?q=python&page=2
```

---

# params com vários valores

Exemplo:

```python
params = {
    "q": "python",
    "page": 2,
    "limit": 10
}

response = requests.get(
    "https://example.com/search",
    params=params
)
```

A URL resultante será equivalente a:

```text
https://example.com/search?q=python&page=2&limit=10
```

O Requests realiza o processo de codificação necessário.

---

# Por que utilizar params?

É muito utilizado em:

- mecanismos de busca;
- APIs;
- filtros;
- paginação;
- ordenação;
- consultas;
- endpoints de pesquisa.

Exemplo:

```text
/search?q=admin
```

```text
/users?page=2
```

```text
/products?category=books
```

---

# params em APIs

Imagine uma API:

```text
https://api.example.com/users
```

Ela permite filtrar usuários através de:

```text
?role=admin
```

Podemos utilizar:

```python
params = {
    "role": "admin"
}

response = requests.get(
    "https://api.example.com/users",
    params=params
)
```

---

# Visualizando a URL final

Depois da requisição podemos verificar:

```python
print(response.url)
```

Exemplo:

```text
https://api.example.com/users?role=admin
```

Isso é extremamente útil durante o desenvolvimento e análise de requisições.

---

# params x URL manual

Podemos fazer:

```python
url = "https://example.com/search?q=python"

response = requests.get(url)
```

Mas também:

```python
url = "https://example.com/search"

response = requests.get(
    url,
    params={
        "q": "python"
    }
)
```

A segunda forma normalmente é mais organizada.

Além disso, o Requests cuida da codificação dos parâmetros.

---

# Exemplo em Cyber Security

Durante a análise autorizada de uma aplicação Web, podemos testar diferentes valores de parâmetros.

Exemplo:

```python
params = {
    "id": 1
}

response = requests.get(
    "https://example.com/user",
    params=params
)

print(response.url)
print(response.status_code)
```

Podemos alterar o valor:

```python
params = {
    "id": 2
}
```

ou:

```python
params = {
    "id": 100
}
```

Esse conceito é utilizado em ferramentas de enumeração e testes de parâmetros.

> [!WARNING]
> Testes de segurança devem ser realizados somente em aplicações próprias ou em ambientes nos quais você possui autorização.

---

# data=

## O que é?

O parâmetro:

```python
data=
```

é utilizado principalmente para enviar dados no corpo da requisição, especialmente dados de formulários.

Por exemplo:

```text
username=admin&password=123
```

Esse formato é conhecido como:

```text
application/x-www-form-urlencoded
```

---

# Exemplo simples

```python
import requests

dados = {
    "username": "admin",
    "password": "123456"
}

response = requests.post(
    "https://example.com/login",
    data=dados
)
```

O Requests envia os dados no corpo da requisição.

---

# data recebe um dict

Assim como:

```python
params=
```

podemos passar um dicionário.

Exemplo:

```python
dados = {
    "nome": "Allan",
    "idade": 24
}
```

Depois:

```python
requests.post(
    url,
    data=dados
)
```

---

# params e data são diferentes

Essa diferença é fundamental.

### params

Envia os dados na URL.

```python
requests.get(
    url,
    params={
        "id": 10
    }
)
```

Resultado conceitual:

```text
GET /user?id=10
```

---

### data

Envia os dados no corpo da requisição.

```python
requests.post(
    url,
    data={
        "id": 10
    }
)
```

Resultado conceitual:

```text
POST /user

id=10
```

---

# Comparação

```text
params
   │
   ▼
URL
 │
 └── ?id=10


data
 │
 ▼
Body
 │
 └── id=10
```

---

# data em formulários HTML

Imagine um formulário:

```html
<form method="POST">

    <input name="username">

    <input name="password">

</form>
```

O navegador pode enviar algo semelhante a:

```text
username=admin&password=123456
```

Com Requests:

```python
dados = {
    "username": "admin",
    "password": "123456"
}

response = requests.post(
    url,
    data=dados
)
```

---

# Exemplo de Login

```python
import requests

session = requests.Session()

dados = {
    "username": "usuario",
    "password": "senha"
}

response = session.post(
    "https://example.com/login",
    data=dados
)

print(response.status_code)
```

Esse padrão é comum quando uma aplicação utiliza formulários tradicionais.

---

# json=

## O que é?

O parâmetro:

```python
json=
```

é utilizado para enviar dados no formato **JSON**.

JSON é muito utilizado em APIs modernas.

Exemplo:

```json
{
    "username": "admin",
    "password": "123456"
}
```

---

# Exemplo com json

```python
import requests

dados = {
    "username": "admin",
    "password": "123456"
}

response = requests.post(
    "https://api.example.com/login",
    json=dados
)
```

O Requests converte o dicionário para JSON.

---

# O que acontece internamente?

Quando fazemos:

```python
requests.post(
    url,
    json=dados
)
```

com:

```python
dados = {
    "username": "admin",
    "password": "123456"
}
```

o Requests serializa os dados para JSON.

Conceitualmente:

```python
dict
 │
 ▼
JSON
 │
 ▼
HTTP Request Body
```

---

# JSON x data

Essa é uma das diferenças mais importantes da biblioteca.

### data=

Normalmente utilizado para dados de formulário.

```python
requests.post(
    url,
    data={
        "username": "admin"
    }
)
```

### json=

Utilizado para enviar JSON.

```python
requests.post(
    url,
    json={
        "username": "admin"
    }
)
```

Embora ambos recebam um dicionário, o formato enviado é diferente.

---

# Exemplo visual

Com:

```python
data={
    "username": "admin"
}
```

podemos ter um corpo semelhante a:

```text
username=admin
```

Com:

```python
json={
    "username": "admin"
}
```

o corpo será JSON:

```json
{
    "username": "admin"
}
```

---

# Content-Type

O servidor precisa saber qual formato está recebendo.

Um Header importante para isso é:

```http
Content-Type
```

Por exemplo:

```http
Content-Type: application/json
```

Ao utilizar:

```python
json=dados
```

o Requests cuida do envio apropriado do JSON e do Header correspondente.

---

# Não confundir json= com json.dumps()

Podemos encontrar código como:

```python
import json

dados = {
    "username": "admin"
}

corpo = json.dumps(dados)
```

Nesse caso:

```python
json.dumps()
```

converte manualmente o dicionário para uma string JSON.

Com Requests, normalmente podemos simplificar utilizando:

```python
requests.post(
    url,
    json=dados
)
```

Em vez de:

```python
requests.post(
    url,
    data=json.dumps(dados)
)
```

Para o uso comum de APIs, `json=` é mais conveniente.

---

# Exemplo de API

Imagine que uma API aceite:

```json
{
    "username": "admin",
    "role": "user"
}
```

Podemos enviar:

```python
dados = {
    "username": "admin",
    "role": "user"
}

response = requests.post(
    "https://api.example.com/users",
    json=dados
)

print(response.status_code)
```

---

# json e APIs

O parâmetro `json=` é extremamente comum em:

- APIs REST;
- aplicações Web modernas;
- sistemas de autenticação;
- APIs de automação;
- ferramentas de segurança;
- scripts de integração.

Exemplo:

```python
response = requests.post(
    api_url,
    json={
        "target": "example.com",
        "scan": "quick"
    }
)
```

---

# files=

## O que é?

O parâmetro:

```python
files=
```

é utilizado para enviar arquivos em uma requisição HTTP.

É comum em formulários que possuem:

```html
<input type="file">
```

---

# Exemplo básico

Imagine que exista um arquivo:

```text
arquivo.txt
```

Podemos fazer:

```python
import requests

with open(
    "arquivo.txt",
    "rb"
) as arquivo:

    response = requests.post(
        "https://example.com/upload",
        files={
            "file": arquivo
        }
    )
```

O arquivo será enviado através da requisição.

---

# Por que utilizar "rb"?

Utilizamos:

```python
"rb"
```

para abrir o arquivo em modo:

```text
read binary
```

ou:

```text
leitura binária
```

Isso é adequado para envio de arquivos.

---

# files recebe um dict

Assim como outros parâmetros do Requests, podemos utilizar um dicionário.

Exemplo:

```python
files = {
    "file": arquivo
}
```

O nome:

```text
file
```

deve corresponder ao nome esperado pelo formulário ou pela API.

---

# Formulário com upload

Imagine:

```html
<form method="POST" enctype="multipart/form-data">

    <input
        type="file"
        name="arquivo"
    >

</form>
```

O nome esperado pelo servidor é:

```text
arquivo
```

Então:

```python
files = {
    "arquivo": arquivo
}
```

---

# Upload com dados adicionais

Também podemos enviar dados junto com o arquivo.

Exemplo:

```python
import requests

dados = {
    "descricao": "arquivo de teste"
}

with open(
    "arquivo.txt",
    "rb"
) as arquivo:

    response = requests.post(
        "https://example.com/upload",
        data=dados,
        files={
            "file": arquivo
        }
    )
```

Nesse caso:

```text
data=
```

envia os campos do formulário.

Enquanto:

```text
files=
```

envia o arquivo.

---

# Estrutura da requisição

Podemos imaginar:

```text
POST /upload
        │
        ├── Campos do formulário
        │      │
        │      └── descricao
        │
        └── Arquivo
               │
               └── arquivo.txt
```

Esse tipo de requisição normalmente utiliza:

```text
multipart/form-data
```

---

# params + data + json + files

Esses parâmetros podem parecer semelhantes porque todos enviam informações.

Mas cada um possui uma finalidade diferente.

| Parâmetro | Local/Formato | Uso comum |
|---|---|---|
| `params=` | URL | Query parameters |
| `data=` | Body | Formulários |
| `json=` | Body | APIs JSON |
| `files=` | Body | Upload de arquivos |

---

# Exemplo comparativo

## params

```python
requests.get(
    url,
    params={
        "id": 10
    }
)
```

Resultado conceitual:

```text
GET /user?id=10
```

---

## data

```python
requests.post(
    url,
    data={
        "username": "admin"
    }
)
```

Resultado conceitual:

```text
POST /login

username=admin
```

---

## json

```python
requests.post(
    url,
    json={
        "username": "admin"
    }
)
```

Resultado conceitual:

```text
POST /api/login

{
    "username": "admin"
}
```

---

## files

```python
requests.post(
    url,
    files={
        "file": arquivo
    }
)
```

Resultado conceitual:

```text
POST /upload

multipart/form-data

arquivo
```

---

# Utilizando com Session

Todos esses parâmetros também podem ser utilizados com uma Session.

Exemplo:

```python
session = requests.Session()
```

Depois:

```python
session.get(
    url,
    params={
        "page": 1
    }
)
```

Ou:

```python
session.post(
    url,
    data={
        "username": "admin"
    }
)
```

Ou:

```python
session.post(
    url,
    json={
        "username": "admin"
    }
)
```

Ou:

```python
session.post(
    url,
    files={
        "file": arquivo
    }
)
```

A diferença é que a requisição é realizada através da Session.

---

# Exemplo prático para Cyber Security

Em um ambiente de laboratório, podemos criar um script para testar diferentes valores de um parâmetro.

Exemplo:

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "SecurityLab/1.0"
})

valores = [
    "admin",
    "guest",
    "test"
]

for valor in valores:

    response = session.get(
        "https://example.com/user",
        params={
            "username": valor
        },
        timeout=5
    )

    print(
        response.status_code,
        response.url
    )
```

Aqui estamos combinando:

```python
Session
```

com:

```python
headers
```

e:

```python
params
```

Esse padrão é útil para automação de testes autorizados.

---

# Testando uma API

Podemos também automatizar requisições JSON.

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "SecurityLab/1.0"
})

dados = {
    "username": "admin",
    "action": "test"
}

response = session.post(
    "https://api.example.com/test",
    json=dados,
    timeout=5
)

print(response.status_code)
print(response.text)
```

---

# Testando um formulário

Para uma aplicação que utiliza formulário:

```python
dados = {
    "username": "admin",
    "password": "123456"
}

response = session.post(
    url,
    data=dados,
    timeout=5
)
```

---

# Testando parâmetros GET

```python
params = {
    "id": 1,
    "page": 1
}

response = session.get(
    url,
    params=params,
    timeout=5
)

print(response.url)
```

---

# Ordem mental para escolher o parâmetro

Quando precisar enviar alguma informação, pense:

```text
Preciso enviar algo na URL?
        │
       SIM
        │
        ▼
     params=
```

Caso não:

```text
Preciso enviar um formulário?
        │
       SIM
        │
        ▼
      data=
```

Caso seja uma API JSON:

```text
Preciso enviar JSON?
        │
       SIM
        │
        ▼
      json=
```

Caso seja um arquivo:

```text
Preciso enviar arquivo?
        │
       SIM
        │
        ▼
      files=
```

---

# Resumo

```text
params=
   │
   └── parâmetros da URL

data=
   │
   └── dados de formulário

json=
   │
   └── dados em JSON

files=
   │
   └── upload de arquivos
```

Os quatro parâmetros são fundamentais para trabalhar com Requests.

---

# Exemplo completo

```python
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "SecurityLab/1.0"
})

# Parâmetros na URL
response = session.get(
    "https://example.com/search",
    params={
        "q": "admin"
    },
    timeout=5
)

# Formulário
response = session.post(
    "https://example.com/login",
    data={
        "username": "admin",
        "password": "123456"
    },
    timeout=5
)

# JSON
response = session.post(
    "https://api.example.com/users",
    json={
        "username": "admin",
        "role": "user"
    },
    timeout=5
)
```

Esse exemplo reúne os principais mecanismos estudados até agora:

```text
Session
   │
   ├── Headers
   │
   ├── GET
   │    └── params=
   │
   └── POST
        ├── data=
        └── json=
```

---

# Resumo da Parte

Nesta parte estudamos:

- `params=`;
- `data=`;
- `json=`;
- `files=`;
- diferença entre parâmetros da URL e Body;
- formulários HTML;
- JSON em APIs;
- upload de arquivos;
- `Content-Type`;
- utilização desses parâmetros com `Session`;
- aplicação em automação e testes de segurança autorizados.

Na próxima parte vamos estudar o **Response** em profundidade: `status_code`, `headers`, `text`, `content`, `json()`, `url`, `cookies`, `history`, `raise_for_status()` e como analisar respostas HTTP em scripts de Cyber Security.


