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

