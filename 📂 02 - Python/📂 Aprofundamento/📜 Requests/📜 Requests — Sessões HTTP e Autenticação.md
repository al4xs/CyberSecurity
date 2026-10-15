## 1. O que é uma sessão HTTP?

Uma sessão HTTP permite que um cliente mantenha determinados dados entre várias requisições feitas a um servidor.

Por padrão, cada requisição HTTP é independente. Se você fizer uma requisição para uma página e depois outra para uma página diferente, o servidor não necessariamente saberá que as duas requisições vieram do mesmo usuário.

Para resolver isso, aplicações web utilizam mecanismos como:

- **Cookies:** pequenos dados enviados pelo servidor e armazenados pelo cliente.
    
- **Identificadores de sessão:** valores que permitem ao servidor reconhecer uma sessão específica.
    
- **Tokens:** credenciais utilizadas para autenticar requisições, especialmente em APIs.
    
- **Cabeçalhos HTTP:** campos que transportam informações adicionais nas requisições, incluindo credenciais quando necessário.
    

A biblioteca `requests` disponibiliza a classe `Session`, que facilita o gerenciamento desses dados entre requisições.

### 1.1. Exemplo de navegação sem sessão persistente

Imagine que uma aplicação tenha uma página de login e uma página privada.

```
Cliente Python                  Servidor Web
      |                               |
      |-------- POST /login --------->|
      |<------- Login aceito ---------|
      |                               |
      |------ GET /dashboard -------->|
      |<------- Não autorizado -------|
```

Esse comportamento pode acontecer quando a primeira requisição estabelece uma sessão, mas o cliente não preserva ou não envia o cookie necessário na segunda requisição.

O resultado depende da implementação do servidor e do mecanismo de autenticação utilizado.

### 1.2. Exemplo utilizando uma sessão

```
Cliente Python                  Servidor Web
      |                               |
      |-------- POST /login --------->|
      |<---- Cookie de sessão --------|
      |                               |
      |----- GET /dashboard --------->|
      |---- Cookie de sessão -------->|
      |<------ Página privada --------|
```

Com `requests.Session()`, os cookies recebidos podem ser armazenados e enviados automaticamente nas próximas requisições compatíveis com as regras de cookies.

**Importante:** criar uma sessão Python não significa que você está autenticado. A autenticação depende do servidor aceitar as credenciais e estabelecer o estado autenticado.

## 2. Criando uma sessão com `requests.Session()`

### 2.1. Sintaxe

```
import requests

session = requests.Session()
```

Explicação de cada linha:

|Código|Explicação|
|---|---|
|`import requests`|Importa a biblioteca responsável pelas requisições HTTP.|
|`requests.Session()`|Cria um objeto de sessão que pode ser reutilizado em várias requisições.|
|`session = ...`|Armazena o objeto na variável `session`, permitindo utilizá-lo posteriormente.|

O objeto `session` possui métodos semelhantes aos disponíveis no módulo `requests`, incluindo:

- `session.get()`
    
- `session.post()`
    
- `session.put()`
    
- `session.patch()`
    
- `session.delete()`
    
- `session.request()`
    
- `session.close()`
    

A diferença importante é que a sessão mantém configurações e cookies entre as requisições realizadas por meio daquele objeto.

### 2.2. Diferença entre `requests.get()` e `session.get()`

Uma requisição direta pode ser feita assim:

```
import requests

response = requests.get(
    "https://httpbin.org/get",
    timeout=10
)

print(response.status_code)
```

Nesse exemplo, `requests.get()` realiza uma requisição HTTP GET.

Agora, utilizando uma sessão:

```
import requests

session = requests.Session()

response = session.get(
    "https://httpbin.org/get",
    timeout=10
)

print(response.status_code)

session.close()
```

A segunda versão cria um objeto de sessão reutilizável. Neste exemplo, ele é utilizado para apenas uma requisição, mas poderia ser empregado em várias.

O método `close()` fecha os recursos associados à sessão, incluindo conexões mantidas pelo gerenciador de conexões.

O parâmetro `timeout=10` estabelece um limite de espera para a conexão e a leitura da resposta. Ele não representa necessariamente um limite absoluto de dez segundos para toda a operação.

## 3. Como funciona o armazenamento de cookies

Cookies são fundamentais para entender sessões HTTP.

Quando um servidor envia um cabeçalho `Set-Cookie`, o cliente pode armazenar o cookie e enviá-lo em requisições posteriores, desde que as regras de domínio, caminho, segurança e expiração permitam.

A classe `Session` mantém um recipiente de cookies acessível por meio de `session.cookies`.

### 3.1. Exemplo prático

O serviço de testes HTTPBin disponibiliza endpoints para demonstrar o funcionamento de cookies.

```
import requests

session = requests.Session()

response = session.get(
    "https://httpbin.org/cookies/set/session_demo/123",
    timeout=10
)

print("Status:", response.status_code)
print("Cookies:", session.cookies.get_dict())

session.close()
```

Esse exemplo depende de acesso à internet e da disponibilidade do serviço HTTPBin.

### Explicação linha por linha

**Linha 1**

```
import requests
```

Importa a biblioteca `requests`.

**Linha 2**

```
session = requests.Session()
```

Cria uma sessão que poderá armazenar cookies recebidos e reutilizar configurações entre requisições.

**Linha 3**

```
response = session.get(
```

Chama o método GET do objeto de sessão. A resposta HTTP final será armazenada na variável `response`.

**Linha 4**

```
"https://httpbin.org/cookies/set/session_demo/123",
```

Define a URL do endpoint de teste. Ele solicita que o serviço configure um cookie chamado `session_demo` com o valor `123`.

**Linha 5**

```
timeout=10
```

Define os limites de espera para conexão e leitura da resposta.

**Linha 6**

```
print("Status:", response.status_code)
```

Exibe o código de status HTTP da resposta final.

O HTTPBin pode responder com um redirecionamento antes de apresentar a resposta final. Como o `requests` segue redirecionamentos por padrão para GET, o status exibido pode ser o da página final, e não o do redirecionamento inicial.

**Linha 7**

```
print("Cookies:", session.cookies.get_dict())
```

Exibe os cookies armazenados pela sessão como um dicionário Python.

- `session.cookies`: recipiente de cookies da sessão.
    
- `.get_dict()`: converte os cookies armazenados em um dicionário simples de nomes e valores.
    

**Linha 8**

```
session.close()
```

Fecha a sessão e libera os recursos associados a ela.

### 3.2. Consultando os cookies em outra requisição

Você pode utilizar o mesmo objeto para consultar os cookies recebidos:

```
import requests

session = requests.Session()

try:
    session.get(
        "https://httpbin.org/cookies/set/session_demo/123",
        timeout=10
    ).raise_for_status()

    response = session.get(
        "https://httpbin.org/cookies",
        timeout=10
    )

    response.raise_for_status()

    print(response.json())

finally:
    session.close()
```

O que acontece:

1. A sessão é criada.
    
2. A primeira requisição solicita a configuração do cookie.
    
3. O método `raise_for_status()` verifica se a resposta final tem um status HTTP de erro, como 404 ou 500.
    
4. A segunda requisição consulta o endpoint que mostra os cookies recebidos.
    
5. `response.json()` interpreta o corpo da resposta como JSON e o converte em estruturas Python.
    
6. O bloco `finally` garante que `session.close()` seja executado, inclusive quando ocorre uma exceção.
    

Uma resposta possível da segunda requisição é:

```
{
  "cookies": {
    "session_demo": "123"
  }
}
```

O resultado exato depende dos cookies efetivamente aceitos e armazenados pela sessão.

## 4. Configurações persistentes da sessão

Além dos cookies, uma sessão pode manter cabeçalhos, parâmetros padrão e configurações de autenticação.

### 4.1. Cabeçalhos padrão com `session.headers`

```
import requests

session = requests.Session()

session.headers.update({
    "User-Agent": "MeuClientePython/1.0",
    "Accept": "application/json"
})

response = session.get(
    "https://httpbin.org/headers",
    timeout=10
)

print(response.json())

session.close()
```

Explicação:

- `session.headers`: cabeçalhos padrão utilizados nas requisições da sessão.
    
- `.update({...})`: adiciona ou atualiza os cabeçalhos definidos no dicionário.
    
- `User-Agent`: identifica o cliente HTTP. O valor acima é um identificador personalizado de exemplo.
    
- `Accept`: informa quais formatos de resposta o cliente prefere receber.
    
- `response.json()`: interpreta o JSON retornado pelo servidor.
    

Os cabeçalhos padrão são aplicados às requisições feitas pela sessão. Cabeçalhos definidos individualmente em uma requisição podem substituir os valores padrão correspondentes.

Não coloque senhas ou tokens diretamente nos cabeçalhos padrão de uma sessão que será utilizada para destinos diferentes sem verificar cuidadosamente onde essas credenciais serão enviadas.

### 4.2. Parâmetros padrão com `session.params`

Também é possível definir parâmetros de consulta padrão:

```
import requests

session = requests.Session()

session.params.update({
    "origem": "laboratorio",
    "versao": "1"
})

response = session.get(
    "https://httpbin.org/get",
    timeout=10
)

print(response.url)

session.close()
```

O atributo `session.params` mantém parâmetros que serão adicionados às requisições feitas pela sessão.

Neste exemplo, a URL final incluirá parâmetros de consulta equivalentes a:

```
?origem=laboratorio&versao=1
```

Isso é útil quando várias requisições precisam compartilhar parâmetros comuns. Tenha cuidado para não adicionar parâmetros padrão que não façam sentido para todos os endpoints.

## 5. Gerenciando a sessão com `with`

Uma forma recomendada de gerenciar recursos é utilizar um gerenciador de contexto.

```
import requests

with requests.Session() as session:
    response = session.get(
        "https://httpbin.org/get",
        timeout=10
    )

    response.raise_for_status()

    print(response.status_code)
```

Explicação:

- `with`: inicia um gerenciador de contexto.
    
- `requests.Session() as session`: cria a sessão e disponibiliza o objeto dentro do bloco.
    
- `response = session.get(...)`: realiza a requisição.
    
- `response.raise_for_status()`: gera uma exceção para respostas HTTP de erro, como 404 e 500.
    
- `print(response.status_code)`: imprime o status da resposta.
    

Ao sair do bloco `with`, a sessão é fechada automaticamente, inclusive quando ocorre uma exceção.

Essa abordagem costuma ser mais segura e legível do que depender de lembrar de chamar `close()` em vários pontos do programa.

## 6. Autenticação HTTP e autenticação de aplicação

É importante distinguir diferentes mecanismos de autenticação.

### 6.1. Autenticação HTTP Basic

O `requests` permite enviar credenciais usando autenticação HTTP Basic:

```
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    "https://httpbin.org/basic-auth/usuario/senha",
    auth=HTTPBasicAuth("usuario", "senha"),
    timeout=10
)

print(response.status_code)
```

O endpoint do HTTPBin é um exemplo público de demonstração. O usuário e a senha da URL e do código são valores de teste, não credenciais reais.

Explicação dos parâmetros:

|Parâmetro|Função|
|---|---|
|`url`|Endereço do endpoint que será consultado.|
|`auth`|Configuração de autenticação HTTP para a requisição.|
|`HTTPBasicAuth("usuario", "senha")`|Cria o objeto de autenticação Basic usando o usuário e a senha fornecidos.|
|`timeout=10`|Limita a espera de conexão e leitura.|

Na autenticação Basic, as credenciais são codificadas em Base64, mas **não são criptografadas por esse mecanismo**. Por isso, utilize HTTPS para proteger as credenciais durante o transporte.

Basic Auth não é a mesma coisa que o login de um formulário web. Uma aplicação pode autenticar usuários por cookies, tokens, formulários ou outros mecanismos próprios.

### 6.2. Autenticação por formulário e cookie de sessão

Em uma aplicação web que utiliza formulários, o fluxo pode ser:

1. O cliente acessa a página de login.
    
2. O servidor apresenta o formulário e, possivelmente, um token CSRF.
    
3. O cliente envia os dados de login.
    
4. O servidor valida as credenciais.
    
5. Se o login for aceito, o servidor estabelece uma sessão e pode enviar um cookie.
    
6. As requisições seguintes utilizam esse cookie para acessar recursos autorizados.
    

O código exato depende dos endpoints, dos campos do formulário, dos tokens exigidos e das regras implementadas pelo servidor.

Um exemplo ilustrativo de cliente para um laboratório próprio:

```
import requests

BASE_URL = "http://127.0.0.1:8000"

with requests.Session() as session:
    login_response = session.post(
        f"{BASE_URL}/login",
        data={
            "username": "aluno",
            "password": "senha-de-laboratorio"
        },
        timeout=10
    )

    login_response.raise_for_status()

    dashboard_response = session.get(
        f"{BASE_URL}/dashboard",
        timeout=10
    )

    dashboard_response.raise_for_status()

    print("Status do painel:", dashboard_response.status_code)
```

Este exemplo é ilustrativo: os caminhos `/login` e `/dashboard` e os campos `username` e `password` precisam existir na aplicação do laboratório para que o fluxo funcione. Ele não representa um serviço real configurado.

#### Explicação linha por linha

**1. Importação**

```
import requests
```

Importa a biblioteca HTTP.

**2. Endereço do laboratório**

```
BASE_URL = "http://127.0.0.1:8000"
```

Define a URL base da aplicação local.

- `127.0.0.1`: endereço de loopback, que aponta para a própria máquina.
    
- `8000`: porta de exemplo do servidor de laboratório.
    
- A constante `BASE_URL` evita repetir a URL em diferentes partes do código.
    

O protocolo HTTP sem TLS é aceitável apenas em um laboratório local controlado, com dados fictícios. Para credenciais reais, utilize HTTPS.

**3. Criação da sessão**

```
with requests.Session() as session:
```

Cria uma sessão e garante seu fechamento ao sair do bloco.

**4. Envio do formulário**

```
login_response = session.post(
    f"{BASE_URL}/login",
    data={
        "username": "aluno",
        "password": "senha-de-laboratorio"
    },
    timeout=10
)
```

- `session.post()`: envia uma requisição HTTP POST.
    
- `f"{BASE_URL}/login"`: monta a URL de login utilizando a constante.
    
- `data={...}`: envia os dados como formulário, normalmente no formato `application/x-www-form-urlencoded`.
    
- `username`: campo de usuário esperado pelo exemplo de aplicação.
    
- `password`: campo de senha esperado pelo exemplo.
    
- `timeout=10`: define limites de espera para conexão e leitura.
    

O dicionário `data` não significa que toda aplicação aceitará esses campos. É necessário respeitar o formato e os nomes definidos pelo servidor.

**5. Verificação da resposta**

```
login_response.raise_for_status()
```

Gera uma exceção para status HTTP de erro, como 400, 401, 403, 404 ou 500.

Entretanto, um status HTTP 200 não garante que o login foi bem-sucedido. Algumas aplicações exibem uma mensagem de erro na própria página e continuam respondendo com 200. Um cliente robusto precisa verificar os sinais de sucesso definidos pela aplicação.

**6. Acesso ao painel**

```
dashboard_response = session.get(
    f"{BASE_URL}/dashboard",
    timeout=10
)
```

Faz uma requisição GET ao painel utilizando o mesmo objeto de sessão. Se o servidor tiver estabelecido um cookie de sessão compatível, o `requests` poderá enviá-lo automaticamente.

**7. Verificação da resposta do painel**

```
dashboard_response.raise_for_status()
```

Verifica se a resposta do painel apresenta um status HTTP de erro.

Essa verificação não confirma, sozinha, que o conteúdo retornado é o painel correto ou que o usuário tem todas as permissões esperadas.

**8. Impressão do status**

```
print("Status do painel:", dashboard_response.status_code)
```

Exibe o código HTTP da resposta final do painel.

## 7. Tokens de autenticação

Algumas APIs utilizam tokens no cabeçalho `Authorization` em vez de cookies de sessão.

Um formato comum é o esquema Bearer:

```
import os
import requests

token = os.environ.get("API_TOKEN")

if not token:
    raise RuntimeError("A variável API_TOKEN não foi definida.")

with requests.Session() as session:
    session.headers.update({
        "Authorization": f"Bearer {token}",
        "Accept": "application/json"
    })

    response = session.get(
        "https://api.example.test/v1/profile",
        timeout=10
    )

    response.raise_for_status()

    print(response.json())
```

Este código demonstra a estrutura de um cliente para uma API hipotética. `api.example.test` não é um endpoint real de serviço.

### Explicação dos elementos novos

- `import os`: importa funções para acessar variáveis de ambiente e outras informações do sistema.
    
- `os.environ.get("API_TOKEN")`: lê a variável de ambiente chamada `API_TOKEN`. Se ela não existir, retorna `None`.
    
- `if not token`: verifica se o valor está ausente ou vazio.
    
- `raise RuntimeError(...)`: interrompe o programa com uma exceção explícita quando o token não foi configurado.
    
- `Authorization`: cabeçalho HTTP usado para transportar informações de autenticação.
    
- `Bearer {token}`: forma o valor do cabeçalho utilizando o esquema Bearer e o token fornecido.
    
- `Accept: application/json`: informa que o cliente prefere receber JSON.
    
- `response.json()`: interpreta o corpo da resposta como JSON.
    

Uma variável de ambiente pode ser definida temporariamente no terminal Bash ou Zsh:

```
export API_TOKEN="token-ficticio-para-laboratorio"
python3 cliente.py
```

O comando `export` disponibiliza a variável para o processo Python iniciado naquele terminal. Substitua o valor por um token apropriado ao seu laboratório.

Não coloque tokens reais diretamente no código, em capturas de tela ou em arquivos versionados. Variáveis de ambiente ajudam a separar a configuração do código, mas não substituem controles de acesso ao sistema e ao ambiente de execução.

## 8. Tratamento de erros em requisições com sessão

Uma requisição pode falhar por diversos motivos:

- Falha de conexão.
    
- Tempo limite excedido.
    
- Erro de resolução DNS.
    
- Problema de certificado TLS.
    
- Resposta HTTP de erro.
    
- Resposta que não contém o formato esperado.
    
- Sessão expirada ou autenticação rejeitada.
    

O exemplo seguinte demonstra um tratamento básico:

```
import requests

try:
    with requests.Session() as session:
        response = session.get(
            "https://httpbin.org/status/503",
            timeout=10
        )

        response.raise_for_status()

except requests.exceptions.Timeout:
    print("A requisição excedeu o tempo de espera.")

except requests.exceptions.ConnectionError:
    print("Não foi possível estabelecer a conexão.")

except requests.exceptions.HTTPError as error:
    print("O servidor retornou um erro HTTP:", error)

except requests.exceptions.RequestException as error:
    print("Ocorreu um erro na requisição:", error)
```

### O que cada bloco faz

- `try`: delimita o código que pode gerar exceções.
    
- `except requests.exceptions.Timeout`: trata especificamente erros de tempo limite.
    
- `except requests.exceptions.ConnectionError`: trata falhas de conexão.
    
- `except requests.exceptions.HTTPError`: trata erros HTTP levantados por `raise_for_status()`.
    
- `except requests.exceptions.RequestException`: captura outros erros da biblioteca que não foram tratados anteriormente.
    

A ordem importa: exceções específicas devem aparecer antes da exceção mais abrangente `RequestException`.

O endpoint `/status/503` do HTTPBin serve para testar uma resposta HTTP 503. O `raise_for_status()` transforma essa resposta em `HTTPError`.

**Diferença importante:** uma resposta 401 ou 403 não é automaticamente uma exceção. O `requests` recebe a resposta normalmente; `raise_for_status()` é que gera `HTTPError` para esse status.

## 9. Sessão HTTP não é sessão de terminal

O termo _sessão_ aparece em diferentes áreas da computação, mas os conceitos não são equivalentes.

|Conceito|Significado|
|---|---|
|`requests.Session`|Objeto Python que mantém configurações, cookies e recursos HTTP entre requisições.|
|Sessão de usuário web|Estado de autenticação e dados que a aplicação associa a um usuário, frequentemente por meio de cookies ou tokens.|
|Sessão de shell|Ambiente interativo de terminal, como Bash ou Zsh.|
|Sessão de socket|Conexão ou estado de comunicação entre processos, conforme o protocolo e a aplicação.|

Uma sessão HTTP não cria um terminal remoto, não abre automaticamente um socket de shell e não concede privilégios. Ela apenas facilita a comunicação HTTP com o servidor.

## 10. Erros e limitações importantes

### 10.1. Criar uma sessão não autentica automaticamente

```
session = requests.Session()
```

Essa instrução apenas cria o objeto. O servidor ainda precisa aceitar um mecanismo de autenticação válido.

### 10.2. Cookies podem expirar ou ser invalidados

Um cookie armazenado localmente pode deixar de funcionar porque o servidor encerrou a sessão, revogou o identificador ou exige nova autenticação.

### 10.3. Um status 200 não garante login bem-sucedido

Algumas aplicações devolvem uma página com mensagem de erro e status 200. O cliente precisa interpretar a resposta conforme o comportamento documentado da aplicação.

### 10.4. Tokens e cookies são credenciais

Não imprima valores sensíveis de cookies, tokens ou cabeçalhos `Authorization` em logs. Quem obtém uma credencial válida pode conseguir utilizá-la enquanto ela estiver ativa e autorizada.

### 10.5. Sessões não devem compartilhar credenciais indiscriminadamente

Se um mesmo objeto de sessão for utilizado com vários servidores, verifique cuidadosamente os destinos, os cabeçalhos padrão, os cookies e a configuração de autenticação. Não presuma que uma sessão seja apropriada para qualquer domínio.

### 10.6. Proteção CSRF pode ser necessária

Aplicações que autenticam usuários por cookies podem exigir um token CSRF adicional em determinadas operações. O formato e a forma de obtê-lo dependem da aplicação. Enviar apenas usuário e senha não garante que um formulário será aceito.

### 10.7. Use apenas ambientes autorizados

Pratique fluxos de autenticação, sessões e testes de requisição em aplicações próprias, laboratórios locais ou ambientes para os quais você tenha autorização. Não reutilize cookies ou tokens de terceiros.

## 11. Referência rápida dos principais recursos

|Recurso|Finalidade|
|---|---|
|`requests.Session()`|Cria uma sessão reutilizável.|
|`session.get()`|Realiza uma requisição GET usando a sessão.|
|`session.post()`|Realiza uma requisição POST usando a sessão.|
|`session.headers`|Acessa os cabeçalhos padrão da sessão.|
|`session.headers.update()`|Adiciona ou atualiza cabeçalhos padrão.|
|`session.params`|Acessa os parâmetros de consulta padrão.|
|`session.cookies`|Acessa os cookies armazenados na sessão.|
|`session.cookies.get_dict()`|Obtém uma representação em dicionário dos cookies.|
|`session.auth`|Configura autenticação padrão para requisições da sessão.|
|`session.close()`|Fecha a sessão e libera seus recursos associados.|
|`response.status_code`|Obtém o status HTTP da resposta.|
|`response.raise_for_status()`|Gera uma exceção para respostas HTTP de erro.|
|`response.json()`|Interpreta o corpo da resposta como JSON.|
|`timeout`|Define limites de espera de conexão e leitura.|

---
# 📜 Requests — Consumo de APIs REST

## 1. O que é uma API REST?

Uma API (_Application Programming Interface_) é uma interface que permite que sistemas diferentes se comuniquem por meio de regras definidas.

REST (_Representational State Transfer_) é um estilo arquitetural utilizado na construção de APIs que operam sobre recursos. Muitas APIs REST utilizam HTTP para receber requisições e devolver respostas em formatos como JSON.

Por exemplo, uma aplicação pode disponibilizar recursos de usuários:

```
GET    /api/users       → Consultar usuários
GET    /api/users/10    → Consultar o usuário de ID 10
POST   /api/users       → Criar um usuário
PUT    /api/users/10    → Substituir o usuário de ID 10
PATCH  /api/users/10    → Atualizar parcialmente o usuário de ID 10
DELETE /api/users/10    → Excluir o usuário de ID 10
```

Esses caminhos são exemplos ilustrativos. Os métodos permitidos e o comportamento de cada endpoint dependem da API utilizada.

### 1.1. Como funciona a comunicação

```
┌─────────────────────────┐
│ Programa Python         │
│                         │
│ requests.get(...)       │
└────────────┬────────────┘
             │
             │ Requisição HTTP
             ▼
┌─────────────────────────┐
│ API REST                │
│                         │
│ Valida a requisição     │
│ Executa a operação      │
│ Prepara a resposta      │
└────────────┬────────────┘
             │
             │ Resposta HTTP
             ▼
┌─────────────────────────┐
│ Programa Python         │
│                         │
│ status_code             │
│ headers                 │
│ json()                  │
└─────────────────────────┘
```

O programa Python envia uma requisição para um endpoint. A API processa a solicitação e devolve uma resposta HTTP, que pode conter dados, uma mensagem de erro ou nenhum conteúdo.

## 2. O que é JSON?

JSON (_JavaScript Object Notation_) é um formato textual utilizado para representar dados estruturados. É muito comum em APIs porque permite transmitir objetos, listas, números, textos, valores booleanos e valores nulos.

Exemplo de JSON:

```
{
  "id": 10,
  "nome": "João",
  "ativo": true,
  "permissoes": ["leitura", "consulta"],
  "perfil": null
}
```

Nesse objeto:

- `id`: número inteiro.
    
- `nome`: texto.
    
- `ativo`: valor booleano.
    
- `permissoes`: lista de textos.
    
- `perfil`: valor nulo.
    

O Python possui tipos equivalentes:

|JSON|Python|
|---|---|
|Objeto `{}`|`dict`|
|Array `[]`|`list`|
|String|`str`|
|Número inteiro|`int`|
|Número decimal|`float`|
|`true` / `false`|`True` / `False`|
|`null`|`None`|

A biblioteca `requests` consegue interpretar JSON recebido em uma resposta utilizando o método `response.json()`.

## 3. Fazendo uma requisição GET para uma API

Para aprender a consumir uma API, utilizaremos o JSONPlaceholder, um serviço público de testes que disponibiliza dados fictícios.

Endpoint de exemplo:

```
https://jsonplaceholder.typicode.com/users
```

### 3.1. Código inicial

```
import requests

url = "https://jsonplaceholder.typicode.com/users"

response = requests.get(
    url,
    timeout=10
)

response.raise_for_status()

users = response.json()

print(users)
```

### 3.2. Explicação linha por linha

**Linha 1 — Importação**

```
import requests
```

Importa a biblioteca utilizada para realizar requisições HTTP.

**Linha 2 — Definição da URL**

```
url = "https://jsonplaceholder.typicode.com/users"
```

Armazena o endpoint da API em uma variável.

Separar a URL do restante do código facilita sua reutilização e manutenção.

**Linhas 3 a 6 — Requisição GET**

```
response = requests.get(
    url,
    timeout=10
)
```

- `requests.get()`: realiza uma requisição HTTP GET.
    
- `url`: endereço do recurso que será consultado.
    
- `timeout=10`: define limites de espera para conexão e leitura da resposta.
    
- `response`: recebe o objeto `Response`, que contém o status, os cabeçalhos e o conteúdo retornado.
    

**Linha 7 — Verificação do status HTTP**

```
response.raise_for_status()
```

Gera uma exceção para respostas HTTP de erro, como 404 ou 500.

Sem essa verificação, o programa poderia tentar processar uma resposta de erro como se fosse uma resposta válida.

**Linha 8 — Conversão do JSON**

```
users = response.json()
```

Interpreta o corpo da resposta como JSON e converte os dados para estruturas Python.

Como o endpoint `/users` retorna uma lista de objetos, a variável `users` normalmente será uma lista de dicionários.

**Linha 9 — Exibição**

```
print(users)
```

Exibe a lista completa no terminal. Para trabalhar com os dados individualmente, é melhor percorrer a lista.

## 4. Acessando os dados retornados

Suponha que a API retorne um objeto como este:

```
{
  "id": 1,
  "name": "Leanne Graham",
  "username": "Bret",
  "email": "leanne@example.test"
}
```

Depois de executar `response.json()`, esses campos podem ser acessados como valores de um dicionário.

```
import requests

url = "https://jsonplaceholder.typicode.com/users/1"

response = requests.get(url, timeout=10)
response.raise_for_status()

user = response.json()

print("ID:", user["id"])
print("Nome:", user["name"])
print("Usuário:", user["username"])
print("E-mail:", user["email"])
```

### Entendendo os acessos

- `user["id"]`: obtém o valor associado à chave `id`.
    
- `user["name"]`: obtém o nome.
    
- `user["username"]`: obtém o nome de usuário.
    
- `user["email"]`: obtém o e-mail.
    

A notação com colchetes gera `KeyError` se a chave não existir.

Quando o campo for opcional, você pode utilizar `dict.get()`:

```
email = user.get("email")
telefone = user.get("phone", "Não informado")

print(email)
print(telefone)
```

- `user.get("email")`: devolve o valor de `email` ou `None` se a chave não existir.
    
- `user.get("phone", "Não informado")`: devolve o telefone ou o texto alternativo se a chave não existir.
    

Isso ajuda a construir clientes mais tolerantes a campos opcionais. Não use valores padrão para esconder silenciosamente a ausência de campos obrigatórios.

## 5. Percorrendo listas de objetos

Muitas APIs devolvem listas contendo diversos registros.

```
import requests

url = "https://jsonplaceholder.typicode.com/users"

response = requests.get(url, timeout=10)
response.raise_for_status()

users = response.json()

for user in users:
    print(f"ID: {user['id']}")
    print(f"Nome: {user['name']}")
    print(f"E-mail: {user['email']}")
    print("-" * 30)
```

### Explicação do fluxo

1. A requisição GET consulta o endpoint.
    
2. `raise_for_status()` verifica se houve um erro HTTP.
    
3. `response.json()` transforma a resposta em uma lista de dicionários.
    
4. `for user in users` percorre cada registro.
    
5. `user['id']`, `user['name']` e `user['email']` acessam os campos do registro atual.
    
6. `"-" * 30` repete o caractere `-` trinta vezes para separar os resultados visualmente.
    

Esse padrão é útil para ferramentas que precisam consultar inventários, registros de laboratório, informações públicas ou dados de serviços internos autorizados.

## 6. Enviando parâmetros de consulta com `params`

Uma API pode permitir filtros, pesquisas, paginação ou ordenação por meio de parâmetros na URL.

Por exemplo:

```
https://api.example.test/users?ativo=true&limite=10
```

Os parâmetros são os valores que aparecem depois de `?`. O caractere `&` separa os parâmetros.

Com `requests`, é preferível utilizar `params` em vez de montar manualmente a query string.

```
import requests

url = "https://jsonplaceholder.typicode.com/posts"

params = {
    "userId": 1
}

response = requests.get(
    url,
    params=params,
    timeout=10
)

response.raise_for_status()

posts = response.json()

print("URL consultada:", response.url)
print("Quantidade de registros:", len(posts))
```

### Explicação dos parâmetros

- `url`: endpoint que será consultado.
    
- `params`: dicionário contendo os parâmetros de consulta.
    
- `"userId": 1`: solicita os posts associados ao usuário de ID 1, conforme o comportamento desse serviço.
    
- `response.url`: mostra a URL final construída pelo `requests`.
    
- `len(posts)`: retorna a quantidade de elementos da lista.
    

O `requests` codifica os parâmetros conforme necessário. Isso evita erros comuns de montagem manual de URLs.

**Atenção:** parâmetros de consulta não são mecanismos de autorização. Uma API deve validar permissões no servidor, independentemente dos filtros enviados pelo cliente.

## 7. Enviando dados com POST

O método POST é utilizado frequentemente para criar recursos ou enviar dados para processamento.

O comportamento exato depende do endpoint.

### 7.1. Enviando JSON

```
import requests

url = "https://jsonplaceholder.typicode.com/posts"

payload = {
    "title": "Estudo de APIs",
    "body": "Exemplo de envio de dados usando Python.",
    "userId": 1
}

response = requests.post(
    url,
    json=payload,
    timeout=10
)

response.raise_for_status()

created_post = response.json()

print("Status:", response.status_code)
print("Resposta:", created_post)
```

### Explicação linha por linha

**Definição dos dados**

```
payload = {
    "title": "Estudo de APIs",
    "body": "Exemplo de envio de dados usando Python.",
    "userId": 1
}
```

Cria um dicionário Python com os campos que serão enviados.

O nome `payload` é uma convenção comum para identificar os dados transportados na requisição.

**Envio da requisição**

```
response = requests.post(
    url,
    json=payload,
    timeout=10
)
```

- `requests.post()`: realiza uma requisição HTTP POST.
    
- `url`: endpoint de destino.
    
- `json=payload`: serializa o dicionário para JSON e configura o cabeçalho de conteúdo apropriado.
    
- `timeout=10`: define os limites de espera para conexão e leitura.
    

O argumento `json=` é diferente de `data=`. Quando você usa `json=`, o `requests` realiza a serialização JSON automaticamente.

**Leitura da resposta**

```
created_post = response.json()
```

Interpreta o corpo da resposta como JSON.

O JSONPlaceholder simula a criação de recursos para fins de teste; não se deve presumir que o registro será persistido de forma permanente.

### 7.2. Diferença entre `json=` e `data=`

|Argumento|Comportamento|
|---|---|
|`json=payload`|Serializa os dados em JSON.|
|`data=payload` com dicionário|Envia os dados normalmente como formulário URL-encoded.|
|`data=texto`|Envia o texto fornecido como corpo, sem serialização JSON automática.|

Exemplo de envio como formulário:

```
response = requests.post(
    "https://httpbin.org/post",
    data={
        "username": "aluno",
        "curso": "Python"
    },
    timeout=10
)

response.raise_for_status()

print(response.json())
```

O HTTPBin permite observar como o servidor recebeu os dados enviados.

Utilize o formato exigido pela API. Enviar JSON quando o endpoint espera um formulário pode resultar em erro ou em dados não reconhecidos.

## 8. Outros métodos HTTP

Além de GET e POST, APIs podem utilizar PUT, PATCH e DELETE.

### 8.1. PUT

É utilizado frequentemente para substituir a representação de um recurso existente.

```
response = requests.put(
    "https://jsonplaceholder.typicode.com/posts/1",
    json={
        "id": 1,
        "title": "Título atualizado",
        "body": "Conteúdo atualizado",
        "userId": 1
    },
    timeout=10
)

response.raise_for_status()

print(response.json())
```

O exemplo utiliza um serviço de demonstração. O comportamento real de PUT depende da API.

### 8.2. PATCH

É utilizado para solicitar uma atualização parcial de um recurso.

```
response = requests.patch(
    "https://jsonplaceholder.typicode.com/posts/1",
    json={
        "title": "Novo título"
    },
    timeout=10
)

response.raise_for_status()

print(response.json())
```

Neste exemplo, o corpo contém apenas o campo que se pretende atualizar. A API precisa implementar o comportamento esperado para PATCH.

### 8.3. DELETE

É utilizado para solicitar a remoção de um recurso.

```
response = requests.delete(
    "https://jsonplaceholder.typicode.com/posts/1",
    timeout=10
)

response.raise_for_status()

print("Status:", response.status_code)
```

Uma resposta bem-sucedida pode utilizar status como 200 ou 204, dependendo da API.

Não presuma que toda resposta de sucesso contenha JSON. Um status 204 indica que a resposta não contém corpo; nesse caso, chamar `response.json()` não é apropriado.

**Importante:** operações de escrita devem ser realizadas apenas em recursos de teste ou sistemas nos quais você tenha autorização.

## 9. Utilizando `requests.Session()` com uma API

Se várias requisições precisarem compartilhar cabeçalhos, cookies ou configurações, uma sessão pode simplificar o código.

```
import requests

base_url = "https://jsonplaceholder.typicode.com"

with requests.Session() as session:
    session.headers.update({
        "Accept": "application/json"
    })

    response = session.get(
        f"{base_url}/users",
        timeout=10
    )

    response.raise_for_status()

    users = response.json()

    for user in users:
        print(user["id"], user["name"])
```

### O que muda ao utilizar uma sessão?

- `requests.Session()` cria um objeto reutilizável.
    
- `session.headers.update()` define um cabeçalho padrão.
    
- `session.get()` executa a requisição utilizando a sessão.
    
- O bloco `with` fecha a sessão automaticamente ao terminar.
    

Para uma única requisição simples, utilizar `requests.get()` diretamente costuma ser suficiente. Para várias requisições relacionadas, uma sessão facilita o reaproveitamento de configurações e conexões.

Autenticação por token ou cookie pode ser integrada à sessão quando a API exigir, respeitando as regras de segurança do serviço.

## 10. Tratamento de erros em clientes de API

Um cliente robusto precisa lidar com erros de rede, respostas HTTP inesperadas e dados inválidos.

```
import requests

url = "https://jsonplaceholder.typicode.com/users"

try:
    response = requests.get(
        url,
        timeout=10
    )

    response.raise_for_status()

    users = response.json()

    if not isinstance(users, list):
        raise ValueError("Formato inesperado: era esperada uma lista.")

    print("Usuários encontrados:", len(users))

except requests.exceptions.Timeout:
    print("A requisição excedeu o tempo de espera.")

except requests.exceptions.ConnectionError:
    print("Não foi possível estabelecer a conexão.")

except requests.exceptions.HTTPError as error:
    print("Erro HTTP:", error)

except requests.exceptions.RequestException as error:
    print("Erro de comunicação:", error)

except ValueError as error:
    print("Erro ao interpretar os dados:", error)
```

### Por que verificar o tipo de dado?

```
if not isinstance(users, list):
```

A função `isinstance()` verifica se um valor é de determinado tipo.

Neste caso, o código espera que `users` seja uma lista. Se a API devolver um objeto, uma string ou outra estrutura inesperada, o programa gera uma exceção em vez de continuar assumindo que os dados estão no formato correto.

A exceção `ValueError` também pode ser gerada por `response.json()` quando o corpo não contém JSON válido. O tratamento acima permite informar o problema ao usuário, embora em um programa maior seja útil distinguir erros de parsing de erros de validação da estrutura.

## 11. Paginação em APIs

Algumas APIs dividem grandes conjuntos de resultados em páginas para evitar retornar milhares de registros de uma só vez.

Um serviço pode utilizar parâmetros como:

```
?page=1&limit=20
```

Ou pode devolver um link para a página seguinte dentro da própria resposta.

Os nomes e o funcionamento desses parâmetros dependem da API.

Exemplo ilustrativo de paginação baseada em número de página:

```
import requests

base_url = "https://api.example.test/items"

page = 1
limit = 20

with requests.Session() as session:
    while True:
        response = session.get(
            base_url,
            params={
                "page": page,
                "limit": limit
            },
            timeout=10
        )

        response.raise_for_status()

        items = response.json()

        if not items:
            break

        for item in items:
            print(item)

        page += 1
```

Esse exemplo pressupõe que a API retorna uma lista em cada página e uma lista vazia quando não existem mais resultados. É necessário adaptar a condição de parada ao contrato real da API.

### Explicação do fluxo

1. `page = 1`: define a página inicial.
    
2. `limit = 20`: solicita até vinte registros por página, se esse parâmetro for suportado.
    
3. `while True`: inicia um laço que continua até encontrar uma condição explícita de parada.
    
4. `params={...}`: envia os parâmetros de paginação.
    
5. `response.raise_for_status()`: interrompe o processamento quando a resposta apresenta um status HTTP de erro.
    
6. `items = response.json()`: converte a resposta para uma estrutura Python.
    
7. `if not items`: encerra o laço quando a lista está vazia.
    
8. `for item in items`: processa cada registro.
    
9. `page += 1`: avança para a página seguinte.
    

Em aplicações reais, estabeleça limites de páginas, controle de erros e condições de parada. Um laço de paginação sem controle pode gerar requisições desnecessárias se a API não indicar corretamente o fim dos resultados.

## 12. Respostas HTTP que merecem tratamento específico

|Status|Significado geral|Tratamento possível|
|---|---|---|
|200|Requisição concluída com sucesso|Processar a resposta conforme o formato esperado.|
|201|Recurso criado|Ler o resultado se houver corpo.|
|204|Sucesso sem conteúdo|Não tentar interpretar um corpo JSON inexistente.|
|400|Requisição inválida|Verificar parâmetros e formato dos dados.|
|401|Não autenticado ou credenciais inválidas|Verificar o mecanismo de autenticação.|
|403|Acesso proibido|Verificar permissões e políticas de acesso.|
|404|Recurso não encontrado|Conferir o endpoint e o identificador.|
|429|Muitas requisições|Respeitar limites e cabeçalhos de espera, quando fornecidos.|
|500|Erro interno do servidor|Tratar a falha e consultar a documentação do serviço.|
|503|Serviço temporariamente indisponível|Considerar uma nova tentativa controlada, quando apropriado.|

Os códigos são significados HTTP gerais. A API pode fornecer detalhes adicionais em seu corpo de resposta.

### 12.1. Limites de requisições e status 429

Algumas APIs limitam a frequência de requisições por usuário, token ou endereço de origem.

Uma resposta 429 pode incluir o cabeçalho `Retry-After`, indicando quanto tempo o cliente deve esperar antes de tentar novamente.

Não implemente tentativas infinitas. Use limites de repetição, atrasos apropriados e respeite as regras de uso do serviço.

## 13. Boas práticas para clientes de API

- Defina `timeout` nas requisições para evitar esperas sem limite configurado.
    
- Utilize `raise_for_status()` quando o programa precisar interromper o processamento em caso de erro HTTP.
    
- Valide a estrutura e os tipos dos dados recebidos.
    
- Não presuma que toda resposta seja JSON.
    
- Não exponha senhas, cookies ou tokens em logs.
    
- Armazene credenciais fora do código-fonte.
    
- Respeite a documentação, os limites de requisição e as permissões da API.
    
- Utilize `Session` quando houver benefício em compartilhar configurações ou reutilizar conexões.
    
- Implemente paginação de acordo com o contrato da API.
    
- Para operações de escrita, considere os efeitos que a repetição de uma requisição pode produzir.
    

## 14. Referência rápida

| Método ou recurso             | Finalidade                                             |
| ----------------------------- | ------------------------------------------------------ |
| `requests.get()`              | Consultar um recurso.                                  |
| `requests.post()`             | Enviar dados para processamento ou criação.            |
| `requests.put()`              | Solicitar substituição de um recurso.                  |
| `requests.patch()`            | Solicitar atualização parcial.                         |
| `requests.delete()`           | Solicitar remoção de um recurso.                       |
| `params=`                     | Enviar parâmetros de consulta na URL.                  |
| `json=`                       | Serializar e enviar dados em JSON.                     |
| `data=`                       | Enviar dados como formulário ou corpo fornecido.       |
| `response.json()`             | Interpretar o corpo da resposta como JSON.             |
| `response.status_code`        | Consultar o status HTTP.                               |
| `response.raise_for_status()` | Gerar exceção para status HTTP de erro.                |
| `response.url`                | Consultar a URL final da resposta.                     |
| `response.headers`            | Consultar os cabeçalhos da resposta.                   |
| `timeout=`                    | Definir limites de espera para conexão e leitura.      |
| `requests.Session()`          | Reutilizar configurações e conexões entre requisições. |

---
# 📜 Requests — Proxies e Verificação TLS

## 1. O que é um proxy HTTP?

Um proxy é um intermediário entre um cliente e um servidor. Em vez de estabelecer a comunicação diretamente com o destino, o cliente envia a requisição ao proxy, que encaminha a solicitação conforme sua configuração.

No Python, a biblioteca `requests` permite configurar proxies para requisições HTTP e HTTPS.

### 1.1. Comunicação sem proxy

```
┌─────────────────┐
│ Cliente Python  │
│ requests.get()  │
└────────┬────────┘
         │
         │ Requisição HTTP
         ▼
┌─────────────────┐
│ Servidor destino│
└─────────────────┘
```

Nesse fluxo, o cliente faz a requisição diretamente ao servidor de destino.

### 1.2. Comunicação com proxy

```
┌─────────────────┐
│ Cliente Python  │
│ requests.get()  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Servidor proxy  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Servidor destino│
└─────────────────┘
```

O proxy recebe a requisição e participa do encaminhamento até o destino. Dependendo do tipo de proxy e de sua configuração, ele pode registrar informações, aplicar políticas de acesso ou controlar o tráfego.

**Importante:** utilizar um proxy não torna automaticamente uma conexão anônima, segura ou invisível. O destino, o proxy e outros componentes da rede ainda podem observar informações diferentes sobre a comunicação.

## 2. Configurando um proxy com `requests`

A configuração de proxies é feita normalmente com o parâmetro `proxies`.

### 2.1. Sintaxe básica

```
import requests

proxies = {
    "http": "http://127.0.0.1:8080",
    "https": "http://127.0.0.1:8080"
}

response = requests.get(
    "https://httpbin.org/ip",
    proxies=proxies,
    timeout=10
)

response.raise_for_status()

print(response.json())
```

Este exemplo pressupõe que exista um proxy HTTP acessível em `127.0.0.1:8080`. Se nenhum serviço estiver escutando nessa porta, a conexão poderá falhar.

### 2.2. Explicação linha por linha

**Linha 1 — Importação**

```
import requests
```

Importa a biblioteca utilizada para enviar requisições HTTP.

**Linhas 3 a 6 — Configuração do proxy**

```
proxies = {
    "http": "http://127.0.0.1:8080",
    "https": "http://127.0.0.1:8080"
}
```

Cria um dicionário que associa esquemas de URL às configurações de proxy.

|Elemento|Significado|
|---|---|
|`proxies`|Variável que armazena a configuração.|
|`"http"`|Configuração utilizada para URLs cujo esquema é HTTP.|
|`"https"`|Configuração utilizada para URLs cujo esquema é HTTPS.|
|`127.0.0.1`|Endereço de loopback, que aponta para a própria máquina.|
|`8080`|Porta de exemplo em que o proxy deve estar escutando.|

Observe que o valor do proxy para a chave `"https"` começa com `http://`. Isso pode ser correto: indica que o cliente se conecta ao proxy usando HTTP e, normalmente, estabelece um túnel `CONNECT` para o destino HTTPS. O tráfego até o destino continua protegido por TLS quando a conexão é estabelecida normalmente.

O esquema do valor indica como o cliente se conecta ao proxy; não precisa ser igual ao esquema da URL de destino.

**Linhas 8 a 12 — Requisição**

```
response = requests.get(
    "https://httpbin.org/ip",
    proxies=proxies,
    timeout=10
)
```

- `requests.get()`: envia uma requisição HTTP GET.
    
- `"https://httpbin.org/ip"`: endpoint público de teste que informa o endereço IP observado pelo serviço.
    
- `proxies=proxies`: utiliza a configuração definida anteriormente.
    
- `timeout=10`: define limites de espera para conexão e leitura.
    
- `response`: recebe o objeto de resposta HTTP.
    

**Linha 14 — Verificação de status**

```
response.raise_for_status()
```

Gera uma exceção se a resposta tiver um status HTTP de erro, como 404 ou 500.

**Linha 16 — Leitura da resposta**

```
print(response.json())
```

Interpreta o corpo da resposta como JSON e imprime os dados.

O endereço exibido normalmente será o endereço de saída que o serviço observa. O resultado depende da topologia da rede e da configuração do proxy.

## 3. Tipos de proxy e seus esquemas

O `requests` aceita diferentes esquemas de conexão com proxies, desde que o protocolo e o suporte necessário estejam disponíveis.

|Esquema de exemplo|Uso|
|---|---|
|`http://proxy.exemplo:8080`|Conexão com um proxy HTTP.|
|`https://proxy.exemplo:8443`|Conexão HTTPS com um proxy que ofereça suporte a esse esquema.|
|`socks5://proxy.exemplo:1080`|Proxy SOCKS5, normalmente com resolução de nomes feita pelo cliente.|
|`socks5h://proxy.exemplo:1080`|SOCKS5 com resolução de nomes delegada ao proxy.|

O suporte a SOCKS depende da instalação de uma dependência adicional. Pode ser instalado com:

```
python3 -m pip install "requests[socks]"
```

Esse comando instala o extra de SOCKS do `requests` no ambiente Python selecionado. Em ambientes com gerenciamento externo de pacotes, como algumas instalações Linux, utilize um ambiente virtual quando necessário.

Exemplo de configuração SOCKS:

```
import requests

proxies = {
    "http": "socks5h://127.0.0.1:1080",
    "https": "socks5h://127.0.0.1:1080"
}

response = requests.get(
    "https://httpbin.org/ip",
    proxies=proxies,
    timeout=10
)

response.raise_for_status()

print(response.json())
```

Esse exemplo também depende de um proxy SOCKS5 funcional na porta indicada.

### 3.1. Diferença entre `socks5` e `socks5h`

Em termos gerais:

- `socks5`: a resolução do nome do destino costuma ser feita pelo cliente.
    
- `socks5h`: a resolução do nome do destino é delegada ao proxy.
    

Essa distinção pode afetar a resolução DNS e a rede utilizada para localizar o destino. Não significa, por si só, que toda a comunicação esteja protegida contra observação ou que o uso de proxy seja anônimo.

## 4. Autenticação no proxy

Alguns proxies exigem usuário e senha para permitir conexões.

Quando o proxy suporta autenticação no formato de URL, as credenciais podem fazer parte da configuração.

```
import requests

proxies = {
    "http": "http://usuario:senha@127.0.0.1:8080",
    "https": "http://usuario:senha@127.0.0.1:8080"
}

response = requests.get(
    "https://httpbin.org/ip",
    proxies=proxies,
    timeout=10
)

response.raise_for_status()

print(response.json())
```

Os valores `usuario` e `senha` são fictícios e devem ser substituídos apenas pelas credenciais autorizadas do proxy utilizado.

**Cuidado:** credenciais inseridas diretamente em URLs podem aparecer em arquivos, logs, mensagens de erro ou históricos de terminal. Não coloque credenciais reais em código versionado.

Uma forma de separar a configuração do código é usar variáveis de ambiente:

```
import os
import requests

proxy_url = os.environ.get("HTTPS_PROXY_URL")

if not proxy_url:
    raise RuntimeError("A variável HTTPS_PROXY_URL não foi definida.")

proxies = {
    "http": proxy_url,
    "https": proxy_url
}

response = requests.get(
    "https://httpbin.org/ip",
    proxies=proxies,
    timeout=10
)

response.raise_for_status()

print(response.json())
```

Explicação:

- `import os`: importa funcionalidades do sistema operacional.
    
- `os.environ.get("HTTPS_PROXY_URL")`: lê a variável de ambiente.
    
- `if not proxy_url`: verifica se o valor está ausente ou vazio.
    
- `raise RuntimeError(...)`: interrompe o programa com uma mensagem clara quando a configuração não existe.
    
- `proxies`: associa o endereço configurado às requisições HTTP e HTTPS.
    

A variável de ambiente deve conter uma URL de proxy válida e compatível com o serviço utilizado. A separação da configuração ajuda a evitar que credenciais sejam escritas diretamente no código, mas é necessário proteger também o ambiente onde elas ficam armazenadas.

## 5. O que é TLS?

TLS (_Transport Layer Security_) é um protocolo criptográfico utilizado para proteger a comunicação entre sistemas.

Quando você acessa uma URL HTTPS, o cliente estabelece uma conexão TLS com o servidor ou com o ponto que termina essa conexão. O processo envolve a negociação dos parâmetros criptográficos e a validação do certificado conforme as regras de confiança aplicáveis.

O TLS ajuda a fornecer:

- **Confidencialidade:** protege o conteúdo transmitido contra leitura por terceiros que apenas observam o tráfego.
    
- **Integridade:** permite detectar alterações não autorizadas nos dados protegidos.
    
- **Autenticação do servidor:** ajuda o cliente a verificar a identidade do servidor por meio da validação do certificado e do nome do host.
    

HTTPS não garante que o site seja honesto ou que seus dados estejam seguros após chegarem ao servidor. Ele protege a comunicação de acordo com as propriedades e os limites do TLS.

## 6. Como o `requests` verifica certificados TLS

Por padrão, o `requests` verifica os certificados TLS das conexões HTTPS usando as autoridades certificadoras consideradas confiáveis no ambiente.

Considere:

```
import requests

response = requests.get(
    "https://httpbin.org/get",
    timeout=10
)

response.raise_for_status()

print(response.status_code)
```

Não foi necessário especificar `verify=True`, pois a verificação de certificados está habilitada por padrão.

Se a validação do certificado falhar, a requisição normalmente gera uma exceção relacionada a `requests.exceptions.SSLError`.

As causas possíveis incluem:

- Certificado expirado ou ainda não válido.
    
- Nome do host incompatível com o certificado.
    
- Certificado assinado por uma autoridade não confiável no ambiente.
    
- Cadeia de certificados incompleta.
    
- Relógio do sistema incorreto.
    
- Interceptação TLS por um proxy corporativo cujo certificado raiz não está configurado como confiável.
    

A causa deve ser investigada antes de modificar a configuração de segurança.

## 7. O parâmetro `verify`

O parâmetro `verify` controla a verificação do certificado TLS em requisições HTTPS.

### 7.1. Verificação habilitada

```
response = requests.get(
    "https://httpbin.org/get",
    verify=True,
    timeout=10
)
```

- `verify=True`: solicita a verificação normal do certificado.
    
- `timeout=10`: define limites de espera para conexão e leitura.
    

Como essa já é a configuração padrão, não é necessário escrever `verify=True` em todas as requisições.

### 7.2. Utilizando um certificado de autoridade certificadora específico

Em alguns ambientes, como laboratórios ou redes corporativas, pode ser necessário confiar em uma autoridade certificadora específica.

Nesse caso, o parâmetro pode receber o caminho de um arquivo PEM que contenha os certificados de CA apropriados:

```
import requests

response = requests.get(
    "https://servidor-laboratorio.example",
    verify="/caminho/para/ca-laboratorio.pem",
    timeout=10
)

response.raise_for_status()

print(response.status_code)
```

O domínio e o caminho são exemplos ilustrativos. O arquivo deve existir e conter uma cadeia de confiança apropriada para o servidor acessado.

O parâmetro `verify` aceita `True` ou um caminho para um arquivo de certificados confiáveis. A configuração correta depende do ambiente e da cadeia de certificação utilizada.

## 8. Por que evitar `verify=False`

É possível desativar a verificação do certificado:

```
response = requests.get(
    "https://httpbin.org/get",
    verify=False,
    timeout=10
)
```

Porém, **essa configuração não deve ser usada em requisições normais de produção**.

Quando `verify=False` é utilizado, o cliente deixa de validar a cadeia de certificados do servidor. Isso pode permitir que uma conexão com um intermediário malicioso seja aceita sem a validação esperada da identidade do servidor.

As consequências podem incluir:

- Exposição de credenciais.
    
- Interceptação de tokens.
    
- Leitura ou modificação do conteúdo da comunicação por um intermediário.
    
- Aceitação de um servidor cuja identidade não foi validada.
    

O `requests` normalmente emite um aviso de segurança quando a verificação é desabilitada.

Se a requisição falhar devido a um certificado, investigue o erro, verifique a data e hora do sistema, atualize as autoridades certificadoras confiáveis ou configure a CA correta do laboratório.

Não desative a verificação apenas para fazer desaparecer uma exceção.

## 9. Proxy HTTPS e interceptação TLS

Um proxy HTTP comum pode encaminhar uma conexão HTTPS por meio do método `CONNECT`. Nesse cenário, o proxy estabelece um túnel entre o cliente e o destino.

```
Cliente Python
      |
      | CONNECT servidor.example:443
      v
Proxy HTTP
      |
      | Túnel para o destino
      v
Servidor HTTPS
```

Em um túnel convencional, o conteúdo TLS entre o cliente e o destino permanece criptografado de ponta a ponta, embora o proxy consiga observar metadados da conexão e possa controlar seu encaminhamento.

Há também proxies de inspeção TLS, utilizados em algumas redes corporativas e laboratórios autorizados. Eles podem terminar a conexão TLS do cliente e estabelecer outra conexão com o servidor de destino.

```
Cliente Python
      |
      | TLS com o proxy
      v
Proxy de inspeção TLS
      |
      | Nova conexão TLS
      v
Servidor HTTPS
```

Nesse segundo cenário, o cliente valida o certificado apresentado pelo proxy. Para que a validação seja aceita, o ambiente pode precisar confiar explicitamente na autoridade certificadora utilizada pela inspeção.

Isso altera o modelo de confiança: o operador do proxy pode ter capacidade de inspecionar o tráfego descriptografado. A configuração deve ser conhecida e autorizada.

## 10. Tratando erros de proxy e TLS

Uma aplicação pode tratar erros comuns sem ocultar a causa da falha.

```
import requests

url = "https://httpbin.org/ip"

try:
    response = requests.get(
        url,
        timeout=10
    )

    response.raise_for_status()

    print(response.json())

except requests.exceptions.ProxyError as error:
    print("Erro de comunicação com o proxy:", error)

except requests.exceptions.SSLError as error:
    print("Falha na validação ou negociação TLS:", error)

except requests.exceptions.Timeout:
    print("A requisição excedeu o tempo de espera.")

except requests.exceptions.ConnectionError as error:
    print("Falha na conexão:", error)

except requests.exceptions.HTTPError as error:
    print("Erro HTTP:", error)

except requests.exceptions.RequestException as error:
    print("Erro de requisição:", error)
```

### Explicação dos blocos

- `try`: delimita a operação que pode gerar exceções.
    
- `ProxyError`: trata falhas relacionadas ao proxy, como impossibilidade de estabelecer a conexão necessária.
    
- `SSLError`: trata erros relacionados ao TLS, incluindo falhas de validação de certificados.
    
- `Timeout`: trata o esgotamento do tempo de espera configurado.
    
- `ConnectionError`: trata falhas de conexão que não foram capturadas por uma exceção mais específica.
    
- `HTTPError`: trata respostas HTTP de erro levantadas por `raise_for_status()`.
    
- `RequestException`: captura outras exceções da biblioteca.
    

A ordem dos blocos importa. Exceções específicas devem aparecer antes das exceções mais abrangentes.

A mensagem de erro pode conter detalhes sensíveis sobre URLs ou configurações. Em aplicações reais, revise o que será registrado nos logs antes de armazenar ou compartilhar essas mensagens.

## 11. Configurações de ambiente e proxies automáticos

O `requests` pode considerar configurações de proxy provenientes do ambiente, como `HTTP_PROXY`, `HTTPS_PROXY` e `NO_PROXY`.

Essas variáveis podem influenciar o comportamento de requisições, dependendo da configuração utilizada.

Exemplo de inspeção no Linux:

```
env | grep -i proxy
```

Explicação:

- `env`: lista variáveis de ambiente exportadas.
    
- `|`: encaminha a saída do primeiro comando para o próximo.
    
- `grep -i proxy`: procura linhas contendo `proxy`, sem diferenciar letras maiúsculas e minúsculas.
    

**Atenção:** a saída pode conter nomes de servidores internos, usuários ou credenciais. Não compartilhe essa saída sem revisar os valores.

Para configurar um proxy temporariamente no Bash ou Zsh, pode-se utilizar:

```
export HTTPS_PROXY="http://127.0.0.1:8080"
export HTTP_PROXY="http://127.0.0.1:8080"
```

Esses comandos afetam o ambiente do terminal e os processos iniciados a partir dele. O proxy precisa estar disponível no endereço configurado.

A variável `NO_PROXY` permite especificar destinos que devem ignorar o proxy, conforme a sintaxe aceita pelo ambiente e pela biblioteca.

Não presuma que a configuração do ambiente seja sempre desejável. Em aplicações que exigem controle rigoroso, avalie explicitamente como os proxies são configurados e se as variáveis de ambiente devem ser consideradas.

## 12. Boas práticas

- Utilize proxies apenas quando tiver autorização para isso.
    
- Defina `timeout` nas requisições.
    
- Mantenha a verificação TLS habilitada.
    
- Não utilize `verify=False` como solução permanente para erros de certificado.
    
- Proteja as credenciais do proxy e os tokens de autenticação.
    
- Evite registrar URLs que contenham usuário e senha.
    
- Investigue erros TLS em vez de ignorá-los.
    
- Configure autoridades certificadoras confiáveis somente quando conhecer sua origem.
    
- Entenda se o proxy apenas encaminha o tráfego ou também realiza inspeção TLS.
    
- Verifique as variáveis de ambiente que possam alterar o comportamento das requisições.
    
- Não presuma que um proxy garante anonimato ou impede a identificação do cliente.
    

## 13. Referência rápida

|Recurso|Finalidade|
|---|---|
|`proxies=`|Configura proxies para requisições.|
|`HTTP_PROXY`|Variável de ambiente frequentemente usada para configurar proxy HTTP.|
|`HTTPS_PROXY`|Variável de ambiente frequentemente usada para configurar proxy HTTPS.|
|`NO_PROXY`|Define destinos que devem ignorar o proxy, conforme a configuração suportada.|
|`verify=True`|Habilita a validação normal de certificados TLS.|
|`verify="/caminho/ca.pem"`|Especifica um arquivo de certificados de CA confiáveis.|
|`verify=False`|Desabilita a validação de certificados; deve ser evitado em uso normal.|
|`requests.exceptions.ProxyError`|Exceção associada a erros de proxy.|
|`requests.exceptions.SSLError`|Exceção associada a problemas de SSL/TLS.|
|`requests.exceptions.Timeout`|Exceção associada a limites de tempo excedidos.|
|`requests.exceptions.ConnectionError`|Exceção associada a falhas de conexão.|
|`requests.exceptions.RequestException`|Classe-base das exceções de requisição do `requests`.|

---
# 📜 Requests — Download e Streaming

## 1. O que é o download de arquivos com Requests?

A biblioteca `requests` permite baixar arquivos de servidores HTTP e HTTPS, como imagens, documentos, arquivos compactados, relatórios e outros recursos disponibilizados por uma URL.

Uma forma simples de fazer isso é utilizar `requests.get()` e acessar o conteúdo da resposta por meio de `response.content`.

```
import requests

url = "https://example.com/arquivo.txt"

response = requests.get(url, timeout=15)
response.raise_for_status()

with open("arquivo.txt", "wb") as arquivo:
    arquivo.write(response.content)
```

Nesse exemplo, o programa:

1. Importa a biblioteca `requests`.
    
2. Define a URL do arquivo.
    
3. Faz uma requisição HTTP GET.
    
4. Verifica se a resposta indica um erro HTTP.
    
5. Abre um arquivo local em modo de escrita binária.
    
6. Grava o conteúdo recebido no disco.
    

O endereço `example.com` é ilustrativo. Para testar um download real, substitua-o por uma URL que disponibilize um arquivo acessível.

### Limitação do método tradicional

O atributo `response.content` disponibiliza o corpo da resposta como bytes. Em um download grande, isso pode exigir bastante memória, pois o conteúdo completo precisa estar disponível antes de ser gravado pelo código.

Para arquivos pequenos, essa abordagem costuma ser suficiente. Para arquivos grandes, é preferível utilizar **streaming**.

---

## 2. O que é streaming?

Streaming é uma forma de processar os dados de uma resposta progressivamente, em vez de carregar todo o conteúdo de uma só vez na memória.

No Requests, isso é habilitado com o argumento `stream=True`.

Sem streaming:

```
response = requests.get(url)
```

Com streaming:

```
response = requests.get(url, stream=True)
```

A diferença principal é que, com `stream=True`, o Requests permite que o programa consuma o corpo da resposta aos poucos.

### Fluxo de um download com streaming

```
Servidor HTTP
     |
     | Envia os dados do arquivo
     v
Resposta HTTP
     |
     v
iter_content()
     |
     | Divide o conteúdo em blocos
     v
Blocos de bytes
     |
     v
Arquivo local no disco
```

O programa recebe cada bloco e o grava no arquivo antes de processar o próximo. Assim, evita manter o arquivo inteiro na memória.

**Importante:** `stream=True` não significa que o download ocorrerá necessariamente mais rápido. O principal benefício é controlar o consumo de memória.

---

## 3. O método `iter_content()`

O método `iter_content()` permite iterar sobre o conteúdo da resposta em blocos.

Sintaxe:

```
response.iter_content(chunk_size=8192)
```

|Parâmetro|Descrição|
|---|---|
|`chunk_size`|Quantidade máxima aproximada de bytes solicitada por bloco|
|Tipo|Inteiro|
|Unidade|Bytes|
|Exemplo|`8192` corresponde a 8 KiB|

O valor `8192` é uma escolha comum, não uma regra obrigatória. É possível utilizar outros tamanhos conforme a necessidade.

### Exemplo básico

```
import requests

url = "https://example.com/arquivo.bin"

with requests.get(url, stream=True, timeout=30) as response:
    response.raise_for_status()

    for bloco in response.iter_content(chunk_size=8192):
        if bloco:
            print(f"Bloco recebido: {len(bloco)} bytes")
```

Explicação:

- `import requests`: importa a biblioteca.
    
- `with requests.get(...) as response`: realiza a requisição e utiliza um gerenciador de contexto para garantir o fechamento da resposta ao sair do bloco.
    
- `stream=True`: habilita a leitura progressiva do corpo.
    
- `timeout=30`: configura o limite de espera da requisição conforme as regras de timeout do Requests.
    
- `response.raise_for_status()`: gera uma exceção para respostas HTTP de erro, como 404 ou 500.
    
- `response.iter_content(chunk_size=8192)`: produz os blocos de conteúdo.
    
- `for bloco in ...`: percorre cada bloco recebido.
    
- `if bloco`: ignora blocos vazios.
    
- `len(bloco)`: informa o tamanho do bloco atual em bytes.
    
- `print(...)`: exibe o tamanho recebido, sem gravar o conteúdo em disco.
    

O exemplo apenas demonstra a leitura. Para salvar o arquivo, precisamos escrever cada bloco em um arquivo local.

---

## 4. Baixando e gravando um arquivo em blocos

O exemplo seguinte mostra o padrão básico para baixar um arquivo e gravá-lo progressivamente.

```
import requests

url = "https://example.com/arquivo.bin"
destino = "arquivo.bin"

with requests.get(url, stream=True, timeout=30) as response:
    response.raise_for_status()

    with open(destino, "wb") as arquivo:
        for bloco in response.iter_content(chunk_size=8192):
            if bloco:
                arquivo.write(bloco)

print("Download concluído.")
```

### Entendendo `open()` e o modo `wb`

A função `open()` abre um arquivo local.

```
open(destino, "wb")
```

O segundo argumento define o modo de abertura:

- `w`: abre para escrita, criando o arquivo se ele não existir e substituindo seu conteúdo caso já exista.
    
- `b`: trabalha com dados binários, representados por objetos `bytes`.
    

A combinação `"wb"` é apropriada para arquivos como imagens, PDFs, arquivos ZIP e executáveis. Ela também pode ser usada para arquivos de texto, embora seja necessário considerar a codificação caso se deseje escrever texto diretamente.

### Por que não usar `arquivo.write(response.text)`?

`response.text` é destinado à interpretação do corpo como texto, utilizando uma codificação. Isso pode alterar a representação dos dados binários.

Para preservar os bytes recebidos, use `response.content` ou, no streaming, escreva diretamente os blocos produzidos por `iter_content()`.

### Por que há dois `with`?

O primeiro gerencia a resposta HTTP; o segundo gerencia o arquivo local.

```
with requests.get(url, stream=True, timeout=30) as response:
    with open(destino, "wb") as arquivo:
        ...
```

Ao sair de cada bloco, o recurso correspondente é fechado, inclusive quando ocorre uma exceção. Isso ajuda a evitar conexões abertas desnecessariamente e arquivos mantidos abertos.

---

## 5. Organizando o download com `pathlib`

A biblioteca padrão `pathlib` oferece uma maneira orientada a objetos de representar caminhos de arquivos.

```
from pathlib import Path

destino = Path("downloads") / "arquivo.bin"
destino.parent.mkdir(parents=True, exist_ok=True)
```

Entendendo cada parte:

- `Path("downloads")`: representa o diretório `downloads`.
    
- `/ "arquivo.bin"`: combina o diretório com o nome do arquivo.
    
- `destino.parent`: retorna o diretório pai do caminho.
    
- `mkdir(...)`: cria o diretório caso necessário.
    
- `parents=True`: permite criar diretórios intermediários.
    
- `exist_ok=True`: evita uma exceção caso o diretório já exista.
    

Agora podemos usar esse caminho no download:

```
import requests
from pathlib import Path

url = "https://example.com/arquivo.bin"

destino = Path("downloads") / "arquivo.bin"
destino.parent.mkdir(parents=True, exist_ok=True)

with requests.get(url, stream=True, timeout=30) as response:
    response.raise_for_status()

    with destino.open("wb") as arquivo:
        for bloco in response.iter_content(chunk_size=8192):
            if bloco:
                arquivo.write(bloco)

print(f"Arq
```

---
# 📜 Requests — Upload de Arquivos e Multipart/Form-Data

## 1. O que é upload de arquivos?

O upload é o processo de enviar dados de um cliente para um servidor. Com Python e `requests`, podemos enviar arquivos, imagens, documentos e outros dados para endpoints HTTP que aceitam esse tipo de operação.

Enquanto um download transfere dados do servidor para o cliente, um upload normalmente transfere dados do cliente para o servidor.

```
UPLOAD

Programa Python
      |
      | POST + arquivo
      v
Servidor HTTP
      |
      v
Processamento do arquivo
```

Um upload pode ser utilizado em aplicações web, APIs REST, sistemas de armazenamento e ferramentas de automação autorizadas.

O servidor precisa oferecer um endpoint que aceite uploads. Enviar um arquivo para uma URL que não implementa essa funcionalidade não fará com que ele seja armazenado automaticamente.

---

## 2. O argumento `files`

O Requests permite enviar arquivos por meio do argumento `files`, geralmente em uma requisição POST.

Exemplo:

```
import requests

url = "https://httpbin.org/post"

with open("documento.pdf", "rb") as arquivo:
    response = requests.post(
        url,
        files={"arquivo": arquivo},
        timeout=30,
    )

response.raise_for_status()
print(response.status_code)
```

Esse exemplo envia o arquivo local `documento.pdf` para um serviço de teste HTTP. O endpoint utilizado é ilustrativo para experimentação; não representa um sistema de armazenamento permanente.

### Entendendo cada parte

- `import requests`: importa a biblioteca responsável pelas requisições HTTP.
    
- `url`: define o endereço do endpoint que receberá o upload.
    
- `open("documento.pdf", "rb")`: abre o arquivo em modo de leitura binária.
    
- `with`: garante o fechamento do arquivo ao sair do bloco.
    
- `arquivo`: variável que representa o objeto de arquivo aberto.
    
- `requests.post(...)`: envia uma requisição HTTP POST.
    
- `files={"arquivo": arquivo}`: informa ao Requests que o corpo da requisição deve incluir um arquivo no campo chamado `arquivo`.
    
- `timeout=30`: configura os limites de espera da requisição.
    
- `response.raise_for_status()`: gera uma exceção caso o servidor retorne um status HTTP de erro.
    
- `response.status_code`: permite consultar o código HTTP retornado pelo servidor.
    

### Por que abrir com `"rb"`?

O modo `"rb"` significa _read binary_ — leitura binária.

- `r`: leitura.
    
- `b`: modo binário.
    

Esse modo permite ler os bytes originais do arquivo sem tentar decodificá-los como texto. É apropriado para PDFs, imagens, arquivos ZIP e outros formatos binários.

---

## 3. Como funciona o `multipart/form-data`?

Quando utilizamos `files`, o Requests normalmente prepara o corpo da requisição usando o tipo de conteúdo `multipart/form-data`.

Esse formato permite enviar vários campos em uma única requisição, incluindo campos de texto e arquivos.

Uma requisição simplificada pode ter esta estrutura:

```
POST /upload HTTP/1.1
Host: exemplo.com
Content-Type: multipart/form-data; boundary=----abc123

------abc123
Content-Disposition: form-data; name="descricao"

Documento de teste
------abc123
Content-Disposition: form-data; name="arquivo"; filename="documento.pdf"
Content-Type: application/pdf

[bytes do arquivo]
------abc123--
```

O exemplo acima é ilustrativo. Em uma requisição real, o Requests gera automaticamente o delimitador (`boundary`) e monta as partes do corpo.

### Conceitos importantes

- `multipart/form-data`: tipo de conteúdo utilizado para representar várias partes de um formulário.
    
- `boundary`: sequência que separa as partes do corpo.
    
- `Content-Disposition`: indica como cada parte deve ser interpretada, incluindo o nome do campo e, para arquivos, o nome do arquivo.
    
- `filename`: nome do arquivo informado na parte do formulário.
    
- `Content-Type`: tipo de conteúdo declarado para a requisição ou para uma parte específica.
    

**Não defina manualmente o cabeçalho** `**Content-Type: multipart/form-data**` **em uma requisição feita com** `**files**`, a menos que você também esteja montando corretamente o corpo e seu `boundary`. Normalmente, deixe o Requests gerar esse cabeçalho.

---

## 4. Enviando um arquivo e campos de texto

Muitos formulários exigem informações adicionais junto ao arquivo, como uma descrição, uma categoria ou um identificador.

Para isso, podemos combinar `files` e `data`.

```
import requests

url = "https://httpbin.org/post"

with open("documento.pdf", "rb") as arquivo:
    response = requests.post(
        url,
        data={
            "descricao": "Documento de teste",
            "categoria": "relatorio",
        },
        files={
            "arquivo": arquivo,
        },
        timeout=30,
    )

response.raise_for_status()
print(response.status_code)
```

### O que cada argumento representa?

`data` contém campos comuns do formulário:

```
data={
    "descricao": "Documento de teste",
    "categoria": "relatorio",
}
```

Nesse dicionário:

- `descricao` é o nome de um campo.
    
- `"Documento de teste"` é o valor enviado.
    
- `categoria` é outro campo.
    
- `"relatorio"` é o valor desse segundo campo.
    

Já `files` contém o arquivo que será enviado:

```
files={
    "arquivo": arquivo,
}
```

A chave `"arquivo"` é o nome do campo esperado pelo endpoint. O nome precisa corresponder ao que a aplicação do servidor espera.

Os nomes `descricao`, `categoria` e `arquivo` são exemplos. Uma API real pode utilizar outros nomes.

---

## 5. Enviando mais de um arquivo

O Requests também permite enviar vários arquivos em uma única requisição, desde que o endpoint aceite esse formato.

```
import requests

url = "https://httpbin.org/post"

with (
    open("documento.pdf", "rb") as pdf,
    open("imagem.png", "rb") as imagem,
):
    response = requests.post(
        url,
        files=[
            ("arquivos", ("documento.pdf", pdf, "application/pdf")),
            ("arquivos", ("imagem.png", imagem, "image/png")),
        ],
        timeout=30,
    )

response.raise_for_status()
print(response.status_code)
```

### Entendendo a estrutura de `files`

Cada item dessa lista representa uma parte do formulário:

```
("arquivos", ("documento.pdf", pdf, "application/pdf"))
```

A estrutura é:

```
(nome_do_campo, (nome_do_arquivo, objeto_arquivo, tipo_mime))
```

- `"arquivos"`: nome do campo recebido pelo servidor.
    
- `"documento.pdf"`: nome do arquivo informado na requisição.
    
- `pdf`: objeto de arquivo aberto.
    
- `"application/pdf"`: tipo MIME declarado para o conteúdo.
    

O segundo item segue a mesma estrutura para a imagem.

Uma lista de tuplas permite repetir o mesmo nome de campo, o que é útil quando um formulário aceita vários arquivos com o mesmo identificador.

O uso de `with (...)` com múltiplos gerenciadores de contexto é suportado nas versões modernas do Python. Ele mantém os dois arquivos abertos durante a requisição e os fecha ao sair do bloco.

---

## 6. Informando o tipo MIME

O tipo MIME descreve a natureza do conteúdo transmitido. Exemplos comuns:

|Tipo MIME|Uso habitual|
|---|---|
|`application/pdf`|PDF|
|`image/jpeg`|Imagem JPEG|
|`image/png`|Imagem PNG|
|`text/plain`|Texto simples|
|`application/zip`|Arquivo ZIP|
|`application/octet-stream`|Dados binários genéricos|

É possível informar o tipo MIME no valor associado ao arquivo:

```
files = {
    "arquivo": (
        "documento.pdf",
        arquivo,
        "application/pdf",
    )
}
```

O formato é:

```
(nome_do_arquivo, objeto_arquivo, tipo_mime)
```

O tipo MIME declarado é apenas uma informação transmitida ao servidor. Ele não comprova que o conteúdo realmente corresponde àquele formato. A aplicação que recebe o upload deve validar os arquivos de acordo com seus requisitos de segurança.

---

## 7. Enviando dados com `data` versus `json` versus `files`

Esses três argumentos têm finalidades diferentes.

|Argumento|Uso habitual|Formato|
|---|---|---|
|`data=`|Campos de formulário|Formulário codificado ou multipart quando combinado com `files`|
|`json=`|Enviar dados estruturados de uma API|JSON|
|`files=`|Enviar arquivos|Normalmente `multipart/form-data`|

### Exemplo com `data`

```
response = requests.post(
    url,
    data={"usuario": "teste"},
)
```

Esse formato é útil para enviar campos simples de formulário.

### Exemplo com `json`

```
response = requests.post(
    url,
    json={
        "usuario": "teste",
        "ativo": True,
    },
)
```

O argumento `json` serializa os dados para JSON e configura o cabeçalho apropriado.

### Exemplo com `files`

```
with open("documento.pdf", "rb") as arquivo:
    response = requests.post(
        url,
        files={"arquivo": arquivo},
    )
```

Esse formato é apropriado quando o endpoint espera receber um arquivo.

Não escolha o argumento apenas pelo tipo de informação que você possui. Consulte a documentação da API para saber qual estrutura o endpoint exige.

---

## 8. Tratamento de erros em uploads

Uma requisição de upload pode falhar por problemas de conexão, timeout, autenticação, validação ou limitações do servidor.

```
import requests

url = "https://httpbin.org/post"

try:
    with open("documento.pdf", "rb") as arquivo:
        response = requests.post(
            url,
            files={"arquivo": arquivo},
            timeout=(5, 30),
        )

    response.raise_for_status()
    print("Resposta HTTP:", response.status_code)

except FileNotFoundError:
    print("O arquivo local não foi encontrado.")

except PermissionError:
    print("Sem permissão para ler o arquivo.")

except requests.exceptions.Timeout:
    print("A requisição excedeu o tempo limite.")

except requests.exceptions.HTTPError as erro:
    print("O servidor retornou um erro HTTP:", erro)

except requests.exceptions.RequestException as erro:
    print("Falha na requisição:", erro)

except OSError as erro:
    print("Erro ao acessar o arquivo:", erro)
```

### Entendendo os tipos de exceção

- `FileNotFoundError`: o caminho especificado não corresponde a um arquivo acessível.
    
- `PermissionError`: o sistema operacional não permite a operação solicitada.
    
- `Timeout`: a requisição excedeu um limite de espera.
    
- `HTTPError`: `raise_for_status()` detectou uma resposta HTTP de erro.
    
- `RequestException`: classe-base para diversas falhas relacionadas às requisições do Requests.
    
- `OSError`: categoria de erros de operações do sistema, incluindo problemas de acesso a arquivos.
    

A ordem dos `except` importa: coloque as exceções mais específicas antes das mais abrangentes.

**Atenção:** um timeout não significa necessariamente que o servidor não recebeu o arquivo. Se a conexão falhar depois de o servidor processar o upload, repetir a requisição pode causar duplicação. Quando a API oferecer suporte, utilize mecanismos como chaves de idempotência ou identificadores únicos de operação.

---

## 9. Boas práticas de segurança

Ao desenvolver uma aplicação que envia arquivos:

1. **Confirme o endpoint correto.** Um upload deve ser enviado somente a um serviço que aceite esse tipo de operação.
    
2. **Use HTTPS.** Isso protege os dados em trânsito quando o certificado TLS é validado corretamente.
    
3. **Configure timeouts.** Evite esperas excessivas durante operações de rede.
    
4. **Trate os erros HTTP.** Não considere todo status de resposta um sucesso.
    
5. **Evite expor credenciais.** Tokens, senhas e chaves não devem ser escritos diretamente no código nem registrados em logs.
    
6. **Respeite os limites da API.** O servidor pode impor restrições de tamanho, formato e quantidade de arquivos.
    
7. **Valide os arquivos no servidor.** Nome, extensão e tipo MIME informados pelo cliente não são suficientes para garantir segurança.
    
8. **Evite repetição cega.** Uma falha de comunicação pode ocorrer após o servidor ter recebido e processado o upload.
    
9. **Feche os arquivos.** Utilize `with` para garantir a liberação dos recursos.
    
10. **Use somente endpoints autorizados.** Em laboratórios de segurança, trabalhe com serviços de teste sob seu controle ou com permissão.
    

## 10. Referência rápida

| Objetivo                         | Recurso                                    |
| -------------------------------- | ------------------------------------------ |
| Enviar um arquivo                | `files={"arquivo": objeto_arquivo}`        |
| Enviar campos de formulário      | `data={"campo": "valor"}`                  |
| Enviar JSON                      | `json={"campo": "valor"}`                  |
| Enviar arquivo binário           | `open(caminho, "rb")`                      |
| Informar nome e MIME             | `("nome.pdf", arquivo, "application/pdf")` |
| Enviar vários arquivos           | Lista de tuplas em `files`                 |
| Verificar erro HTTP              | `response.raise_for_status()`              |
| Definir timeout                  | `timeout=(5, 30)`                          |
| Fechar o arquivo automaticamente | `with open(...) as arquivo`                |
| Tratar falhas de rede            | `requests.exceptions.RequestException`     |

---
# 📜 Requests — Autenticação, Tokens e Cabeçalhos HTTP

## 1. O papel dos cabeçalhos HTTP

Os cabeçalhos HTTP transportam metadados sobre uma requisição ou resposta. Eles podem indicar o formato dos dados, as preferências do cliente, informações de autenticação e outras características da comunicação.

Com `requests`, os cabeçalhos de uma requisição são definidos principalmente pelo argumento `headers`.

```
import requests

url = "https://httpbin.org/headers"

response = requests.get(
    url,
    headers={
        "User-Agent": "MeuPrograma/1.0",
        "Accept": "application/json",
    },
    timeout=15,
)

response.raise_for_status()
print(response.json())
```

### Entendendo cada parte

- `requests.get(...)`: realiza uma requisição HTTP GET.
    
- `headers={...}`: recebe um dicionário com os cabeçalhos que serão enviados.
    
- `"User-Agent"`: identifica o cliente HTTP. O valor pode identificar uma aplicação, mas não comprova a identidade de quem a executa.
    
- `"Accept"`: informa quais tipos de conteúdo o cliente prefere receber.
    
- `timeout=15`: configura o limite de espera da requisição.
    
- `response.raise_for_status()`: gera uma exceção para respostas HTTP de erro.
    
- `response.json()`: tenta interpretar o corpo da resposta como JSON.
    

O endereço utilizado é um endpoint de teste. O conteúdo retornado depende do serviço e não deve ser confundido com dados de uma API de produção.

## 2. Cabeçalhos comuns

|Cabeçalho|Finalidade|
|---|---|
|`User-Agent`|Identifica o cliente HTTP declarado|
|`Accept`|Indica os tipos de conteúdo aceitos|
|`Content-Type`|Descreve o formato do corpo enviado|
|`Authorization`|Transporta credenciais ou informações de autorização|
|`Accept-Language`|Indica preferências de idioma|
|`Cache-Control`|Expressa diretivas relacionadas ao cache|
|`Referer`|Pode informar a página de origem de uma navegação|

A presença de um cabeçalho não garante que o servidor o aceite ou o utilize. O comportamento depende da aplicação e de sua configuração.

### `Accept` versus `Content-Type`

Esses cabeçalhos possuem funções diferentes:

- `Accept`: informa o tipo de resposta que o cliente prefere receber.
    
- `Content-Type`: descreve o formato do corpo da requisição ou resposta.
    

Por exemplo:

```
headers = {
    "Accept": "application/json",
    "Content-Type": "application/json",
}
```

Esse dicionário declara preferência por uma resposta JSON e indica que o corpo enviado é JSON. Para enviar dados serializados automaticamente, normalmente é mais simples utilizar o argumento `json=`:

```
response = requests.post(
    "https://httpbin.org/post",
    json={"usuario": "teste"},
    timeout=15,
)
```

Nesse caso, o Requests serializa o dicionário e configura o cabeçalho de conteúdo apropriado.

## 3. Autenticação HTTP Basic

A autenticação Basic transmite um nome de usuário e uma senha codificados em Base64 no cabeçalho `Authorization`.

**Base64 não é criptografia.** Ele apenas representa os dados em outro formato. Por isso, Basic Auth deve ser utilizado sobre HTTPS com validação TLS ativa.

O Requests oferece o argumento `auth`:

```
import requests

url = "https://httpbin.org/basic-auth/aluno/senha-demo"

response = requests.get(
    url,
    auth=("aluno", "senha-demo"),
    timeout=15,
)

response.raise_for_status()
print(response.status_code)
```

### Como funciona `auth`

```
auth=("aluno", "senha-demo")
```

O argumento recebe uma tupla com:

1. O nome de usuário.
    
2. A senha.
    

O Requests monta o cabeçalho de autenticação Basic. O exemplo usa credenciais fictícias em um endpoint de teste; em uma aplicação real, utilize as credenciais fornecidas pelo serviço autorizado.

Não coloque senhas reais diretamente no código-fonte, especialmente em repositórios compartilhados.

## 4. Bearer Token

Um Bearer Token é uma credencial que permite ao portador realizar operações autorizadas conforme as regras do servidor. Ele costuma ser utilizado em APIs REST.

O formato habitual do cabeçalho é:

```
Authorization: Bearer TOKEN_DE_EXEMPLO
```

Com Requests:

```
import os
import requests

token = os.environ.get("API_TOKEN")

if not token:
    raise RuntimeError("A variável API_TOKEN não foi configurada.")

response = requests.get(
    "https://httpbin.org/bearer",
    headers={
        "Authorization": f"Bearer {token}",
        "Accept": "application/json",
    },
    timeout=15,
)

response.raise_for_status()
print(response.json())
```

### Entendendo o código

- `import os`: importa funcionalidades de interação com o ambiente do sistema.
    
- `os.environ.get("API_TOKEN")`: lê a variável de ambiente `API_TOKEN`; retorna `None` se ela não existir.
    
- `if not token`: verifica se não foi fornecido um valor utilizável.
    
- `raise RuntimeError(...)`: interrompe a execução com uma mensagem explicativa.
    
- `f"Bearer {token}"`: constrói o valor do cabeçalho incluindo o token.
    
- `response.json()`: interpreta a resposta como JSON.
    

A variável de ambiente precisa ser configurada antes da execução. Em um shell compatível com Bash ou Zsh, por exemplo:

```
export API_TOKEN='TOKEN_DE_TESTE'
python programa.py
```

Substitua o valor pelo token apropriado para o serviço que você está autorizado a utilizar. Não compartilhe tokens em mensagens, capturas de tela ou registros de depuração.

## 5. Reutilizando cabeçalhos com `Session`

Quando várias requisições precisam utilizar os mesmos cabeçalhos, uma sessão permite configurar esses valores uma única vez.

```
import requests

with requests.Session() as session:
    session.headers.update({
        "User-Agent": "MeuPrograma/1.0",
        "Accept": "application/json",
    })

    primeira = session.get(
        "https://httpbin.org/headers",
        timeout=15,
    )
    primeira.raise_for_status()

    segunda = session.get(
        "https://httpbin.org/headers",
        timeout=15,
    )
    segunda.raise_for_status()
```

### O que acontece nesse exemplo?

- `requests.Session()`: cria uma sessão que pode reutilizar conexões e manter configurações entre requisições.
    
- `session.headers`: coleção de cabeçalhos padrão da sessão.
    
- `.update({...})`: adiciona os cabeçalhos indicados ou atualiza os valores existentes.
    
- `session.get(...)`: envia uma requisição usando as configurações da sessão.
    
- `with`: fecha a sessão ao sair do bloco.
    

Os cabeçalhos definidos na sessão são aplicados às requisições feitas por ela, salvo quando são substituídos ou modificados para uma requisição específica.

Uma sessão não significa, por si só, que o usuário está autenticado. A autenticação depende das credenciais, cookies, tokens e regras implementadas pelo servidor.

## 6. Diferença entre cookies e tokens

Cookies e tokens podem participar de mecanismos de autenticação, mas não são a mesma coisa.

**Cookies**

- São armazenados e enviados conforme as regras de cookies HTTP.
    
- Podem representar uma sessão mantida pelo servidor.
    
- Podem ser recebidos em cabeçalhos `Set-Cookie` e enviados posteriormente em `Cookie`.
    
- Podem ser gerenciados automaticamente por `requests.Session()`.
    

**Tokens**

- São valores utilizados como credenciais ou comprovantes de autorização, conforme o sistema.
    
- Podem ser enviados no cabeçalho `Authorization`.
    
- Podem ter prazo de validade, permissões e regras de renovação.
    
- Podem exigir mecanismos próprios de obtenção e atualização.
    

Exemplo de token:

```
headers = {
    "Authorization": "Bearer TOKEN_DE_EXEMPLO",
}
```

Exemplo de cookie enviado explicitamente:

```
headers = {
    "Cookie": "session_id=VALOR_DE_EXEMPLO",
}
```

Em aplicações reais, normalmente é preferível deixar o Requests gerenciar cookies por meio de uma sessão, em vez de montar manualmente o cabeçalho `Cookie`.

Não confunda o cookie de sessão de uma aplicação com uma sessão TCP. São conceitos de camadas e finalidades diferentes.

## 7. Tratamento de respostas de autenticação

Respostas HTTP podem indicar problemas de autenticação ou autorização.

|Status|Significado geral|
|---|---|
|`200 OK`|A requisição foi processada com sucesso|
|`401 Unauthorized`|A requisição não apresentou autenticação válida ou necessária|
|`403 Forbidden`|O servidor recusou a operação por falta de permissão ou outra política|
|`404 Not Found`|O recurso não foi encontrado ou não está sendo disponibilizado|
|`429 Too Many Requests`|O servidor está limitando a frequência de requisições|
|`500 Internal Server Error`|Ocorreu um erro interno no servidor|

A interpretação exata depende da API. Por exemplo, alguns serviços podem utilizar `404` para não revelar a existência de recursos protegidos.

### Exemplo de tratamento

```
import requests

try:
    response = requests.get(
        "https://httpbin.org/bearer",
        timeout=15,
    )

    response.raise_for_status()

except requests.exceptions.HTTPError as erro:
    print(f"Erro HTTP: {erro}")

except requests.exceptions.RequestException as erro:
    print(f"Erro de comunicação: {erro}")
```

O endpoint de teste pode retornar uma resposta de autenticação não autorizada quando não recebe a credencial esperada.

`raise_for_status()` gera `HTTPError` para status HTTP de erro. Já `RequestException` permite tratar outras falhas de comunicação do Requests.

Não interprete automaticamente um status `200` como prova de que todos os requisitos de negócio foram atendidos. Verifique também o conteúdo e o contrato da API.

## 8. Boas práticas de segurança

1. **Utilize HTTPS com verificação TLS habilitada.** Isso ajuda a proteger as credenciais em trânsito.
    
2. **Não registre tokens ou senhas nos logs.** O cabeçalho `Authorization` deve ser tratado como dado sensível.
    
3. **Evite credenciais fixas no código.** Utilize variáveis de ambiente ou um gerenciador de segredos apropriado.
    
4. **Limite as permissões dos tokens.** Prefira credenciais com apenas os acessos necessários.
    
5. **Respeite a validade das credenciais.** Tokens expirados podem exigir renovação conforme a documentação da API.
    
6. **Não confunda autenticação com autorização.** Identificar ou autenticar um usuário não significa que ele pode acessar qualquer recurso.
    
7. **Não compartilhe sessões autenticadas.** Cookies e tokens podem conceder acesso à conta associada.
    
8. **Siga os limites do serviço.** Trate respostas `429` conforme a documentação e os cabeçalhos de limitação disponibilizados.
    

## 9. Referência rápida

|Objetivo|Recurso|
|---|---|
|Definir cabeçalhos por requisição|`headers={...}`|
|Configurar cabeçalhos padrão|`session.headers.update({...})`|
|Usar HTTP Basic|`auth=("usuario", "senha")`|
|Enviar Bearer Token|`Authorization: Bearer ...`|
|Ler variável de ambiente|`os.environ.get("API_TOKEN")`|
|Reutilizar conexões e cookies|`requests.Session()`|
|Verificar erro HTTP|`response.raise_for_status()`|
|Consultar status|`response.status_code`|
|Interpretar JSON|`response.json()`|

---
# 📜 Requests — Inspeção de Respostas e Validação de Conteúdo

## 1. Por que inspecionar uma resposta HTTP?

Uma requisição HTTP bem-sucedida do ponto de vista da comunicação não garante que o conteúdo recebido seja o esperado.

Uma resposta pode ter status `200 OK`, mas conter:

- Uma página HTML de erro da aplicação.
    
- Um JSON com uma mensagem de erro de negócio.
    
- Um documento vazio.
    
- Um formato diferente do esperado.
    
- Dados incompletos ou inválidos.
    

Por isso, depois de realizar uma requisição com `requests`, é importante verificar o status HTTP, o tipo de conteúdo e a estrutura dos dados antes de utilizá-los.

O fluxo geral é:

```
Requisição HTTP
      |
      v
Verificar status
      |
      v
Inspecionar cabeçalhos
      |
      v
Interpretar o conteúdo
      |
      v
Validar os dados
      |
      v
Utilizar o resultado
```

Cada etapa tem uma finalidade diferente. O status HTTP informa como o servidor classificou a operação; os cabeçalhos descrevem a resposta; a validação verifica se o conteúdo atende às necessidades do programa.

---

## 2. Consultando o status HTTP

O atributo `response.status_code` contém o código de status retornado pelo servidor.

```
import requests

response = requests.get(
    "https://httpbin.org/get",
    timeout=10,
)

print(response.status_code)
```

O resultado esperado para esse endpoint de teste é normalmente `200`.

### Utilizando `raise_for_status()`

```
response.raise_for_status()
```

Esse método gera uma exceção `requests.exceptions.HTTPError` quando o status indica um erro HTTP, geralmente códigos entre `400` e `599`.

Exemplo:

```
import requests

try:
    response = requests.get(
        "https://httpbin.org/status/404",
        timeout=10,
    )

    response.raise_for_status()

except requests.exceptions.HTTPError as erro:
    print(f"Erro HTTP: {erro}")
```

O endpoint de teste retorna o status `404`, fazendo com que `raise_for_status()` gere uma exceção.

**Importante:** o método não considera automaticamente todos os status fora da faixa `200` como erros. Respostas como `3xx` podem representar redirecionamentos e, por padrão, o Requests costuma segui-los automaticamente em requisições GET.

---

## 3. Inspecionando os cabeçalhos da resposta

Os cabeçalhos podem ajudar a identificar o formato do conteúdo e outras características da resposta.

```
import requests

response = requests.get(
    "https://httpbin.org/get",
    timeout=10,
)

response.raise_for_status()

print(response.headers)
```

O atributo `response.headers` disponibiliza os cabeçalhos da resposta em uma estrutura semelhante a um dicionário.

Para consultar um cabeçalho específico:

```
tipo_conteudo = response.headers.get("Content-Type")
print(tipo_conteudo)
```

### Entendendo o código

- `response.headers`: coleção dos cabeçalhos recebidos.
    
- `.get("Content-Type")`: procura o cabeçalho `Content-Type`.
    
- `tipo_conteudo`: armazena o valor encontrado, ou `None` se o cabeçalho estiver ausente.
    
- `print(...)`: apresenta o valor.
    

Um resultado pode ser semelhante a:

```
application/json
```

ou:

```
text/html; charset=utf-8
```

O valor exato depende do endpoint.

O `Content-Type` é uma indicação fornecida pelo servidor. Não é uma prova definitiva de que o corpo corresponde realmente ao formato declarado.

---

## 4. Verificando o tipo de conteúdo

Se o programa espera JSON, é útil verificar o cabeçalho antes de tentar interpretar o corpo.

```
import requests

response = requests.get(
    "https://httpbin.org/get",
    timeout=10,
)

response.raise_for_status()

content_type = response.headers.get("Content-Type", "")
content_type = content_type.lower()

if "application/json" in content_type:
    dados = response.json()
    print(dados)
else:
    print("A resposta não declarou JSON.")
```

### Entendendo as operações

- `.get("Content-Type", "")`: retorna o cabeçalho ou uma string vazia se ele não existir.
    
- `.lower()`: converte as letras para minúsculas, permitindo comparar o valor sem diferenciar maiúsculas de minúsculas.
    
- `"application/json" in content_type`: verifica se essa sequência aparece no cabeçalho.
    
- `response.json()`: tenta interpretar o corpo como JSON.
    

Algumas APIs utilizam tipos como `application/problem+json` ou outros tipos terminados em `+json`. Se o programa precisar aceitar diferentes tipos de mídia JSON, a validação deverá considerar essas possibilidades.

Mesmo quando o cabeçalho indica JSON, o corpo pode estar malformado. Por isso, também é necessário tratar erros de decodificação.

---

## 5. Interpretando JSON com segurança

O método `response.json()` converte um corpo JSON em objetos Python.

Por exemplo, o JSON:

```
{
    "usuario": "aluno",
    "ativo": true,
    "tentativas": 3
}
```

é convertido aproximadamente para:

```
{
    "usuario": "aluno",
    "ativo": True,
    "tentativas": 3,
}
```

Observe as diferenças:

|JSON|Python|
|---|---|
|`object`|`dict`|
|`array`|`list`|
|`string`|`str`|
|`number`|`int` ou `float`|
|`true` / `false`|`True` / `False`|
|`null`|`None`|

### Tratando JSON inválido

```
import requests

try:
    response = requests.get(
        "https://httpbin.org/html",
        timeout=10,
    )

    response.raise_for_status()
    dados = response.json()

except requests.exceptions.JSONDecodeError:
    print("A resposta não contém JSON válido.")

except requests.exceptions.RequestException as erro:
    print(f"Falha na requisição: {erro}")
```

O endpoint `/html` fornece HTML, não JSON. Por isso, a tentativa de interpretar o corpo com `response.json()` normalmente gera uma exceção de decodificação.

O Requests disponibiliza `requests.exceptions.JSONDecodeError` para esse tipo de situação. O tratamento de exceções de JSON pode variar conforme a versão e as dependências instaladas; consulte a documentação da versão utilizada.

---

## 6. Validando a estrutura dos dados

Conseguir interpretar JSON não significa que o resultado contém os campos esperados.

Considere uma API que deveria retornar:

```
{
    "nome": "Ana",
    "idade": 20
}
```

O programa pode validar a estrutura antes de utilizar os dados:

```
dados = {
    "nome": "Ana",
    "idade": 20,
}

if not isinstance(dados, dict):
    raise ValueError("A resposta precisa ser um objeto JSON.")

if not isinstance(dados.get("nome"), str):
    raise ValueError("O campo 'nome' precisa ser uma string.")

if not isinstance(dados.get("idade"), int):
    raise ValueError("O campo 'idade' precisa ser um inteiro.")

print(dados["nome"])
```

### Entendendo `isinstance()`

A função `isinstance(objeto, tipo)` verifica se um objeto é uma instância do tipo informado.

Exemplos:

```
isinstance("Ana", str)       # True
isinstance(20, int)          # True
isinstance([], list)         # True
isinstance({}, dict)         # True
```

### Por que usar `.get()`?

```
dados.get("nome")
```

O método `.get()` permite consultar uma chave sem gerar `KeyError` quando ela não existe. Nesse caso, o resultado será `None`, a menos que outro valor padrão seja informado.

Em comparação:

```
dados["nome"]
```

O acesso direto gera `KeyError` se a chave estiver ausente.

**Detalhe importante:** em Python, `bool` é uma subclasse de `int`. Portanto, `isinstance(True, int)` retorna `True`. Se o programa precisar aceitar apenas inteiros que não sejam booleanos, pode utilizar:

```
if not isinstance(dados.get("idade"), int) or isinstance(dados.get("idade"), bool):
    raise ValueError("O campo 'idade' precisa ser um inteiro.")
```

Essa distinção pode ser importante quando os dados recebidos precisam seguir um contrato estrito.

---

## 7. Diferença entre status HTTP e erros de negócio

Uma API pode retornar `200 OK` mesmo quando uma operação não foi concluída conforme a expectativa do cliente.

Por exemplo:

```
{
    "sucesso": false,
    "mensagem": "Operação não permitida."
}
```

Esse JSON pode ser uma resposta válida do ponto de vista da sintaxe e, ainda assim, indicar que a operação falhou.

O programa deve verificar os campos definidos no contrato da API:

```
dados = response.json()

if dados.get("sucesso") is True:
    print("Operação concluída.")
else:
    print("A aplicação informou uma falha.")
```

Nesse exemplo, `is True` exige que o valor seja exatamente o booleano `True`. Isso evita tratar valores como `1` como se fossem necessariamente o booleano esperado.

Não invente campos como `sucesso`, `status` ou `mensagem` sem consultar a documentação da API. Os nomes e os valores válidos dependem do serviço.

---

## 8. Consultando o tamanho e o conteúdo da resposta

O Requests disponibiliza diferentes atributos para acessar o corpo da resposta.

### `response.text`

Retorna o corpo interpretado como texto, utilizando a codificação determinada pelo Requests.

```
print(response.text)
```

É útil para HTML, XML, JSON em formato textual e outros conteúdos textuais.

### `response.content`

Retorna o corpo como bytes.

```
print(response.content)
```

É apropriado quando precisamos dos dados binários originais, como no download de arquivos.

### `response.encoding`

Informa a codificação utilizada para interpretar `response.text`.

```
print(response.encoding)
```

### `len(response.content)`

Informa o número de bytes do corpo que foi carregado:

```
print(len(response.content))
```

Para respostas não transmitidas em streaming, esse valor representa o tamanho do conteúdo armazenado em `response.content`. Com `stream=True`, evite acessar `response.content` indiscriminadamente, pois isso pode consumir a resposta inteira e aumentar o uso de memória.

---

## 9. Verificando redirecionamentos

O Requests segue redirecionamentos automaticamente em métodos como GET, por padrão. É possível inspecionar o histórico e a URL final.

```
import requests

response = requests.get(
    "https://httpbin.org/redirect/2",
    timeout=10,
)

response.raise_for_status()

print("URL final:", response.url)
print("Redirecionamentos:", len(response.history))
```

### Atributos importantes

- `response.url`: URL associada à resposta final.
    
- `response.history`: lista das respostas intermediárias de redirecionamento.
    
- `len(response.history)`: quantidade de respostas intermediárias registradas.
    

Se for necessário inspecionar a primeira resposta sem seguir o redirecionamento, utilize:

```
response = requests.get(
    "https://httpbin.org/redirect/2",
    allow_redirects=False,
    timeout=10,
)

print(response.status_code)
print(response.headers.get("Location"))
```

`allow_redirects=False` desabilita o seguimento automático. O cabeçalho `Location` pode indicar o destino do redirecionamento, quando presente.

Redirecionamentos merecem atenção em aplicações que enviam credenciais ou dados sensíveis, especialmente quando o destino muda de domínio.

---

## 10. Exemplo completo: consulta e validação de JSON

O exemplo abaixo reúne status HTTP, tratamento de erros, validação do tipo de conteúdo e verificação básica da estrutura.

```
import requests


def consultar_usuario(url: str) -> dict:
    try:
        response = requests.get(
            url,
            headers={"Accept": "application/json"},
            timeout=(3, 10),
        )

        response.raise_for_status()

        content_type = response.headers.get("Content-Type", "").lower()

        if "json" not in content_type:
            raise ValueError("A resposta não declarou um tipo de conteúdo JSON.")

        dados = response.json()

        if not isinstance(dados, dict):
            raise ValueError("A resposta JSON precisa ser um objeto.")

        if "id" not in dados:
            raise ValueError("O campo obrigatório 'id' está ausente.")

        return dados

    except requests.exceptions.Timeout as erro:
        raise RuntimeError("A consulta excedeu o tempo limite.") from erro

    except requests.exceptions.RequestException as erro:
        raise RuntimeError("Não foi possível consultar o serviço.") from erro


usuario = consultar_usuario("https://jsonplaceholder.typicode.com/users/1")
print(usuario["id"])
print(usuario["name"])
```

### Conceitos utilizados

- `def consultar_usuario(...)`: define uma função para encapsular a consulta.
    
- `url: str`: anota o argumento como string.
    
- `-> dict`: indica que a função pretende retornar um dicionário.
    
- `headers={"Accept": "application/json"}`: declara a preferência por uma resposta JSON.
    
- `timeout=(3, 10)`: configura timeout de conexão e leitura.
    
- `raise_for_status()`: detecta respostas HTTP de erro.
    
- `"json" not in content_type`: realiza uma verificação simples do cabeçalho.
    
- `isinstance(dados, dict)`: verifica se o JSON foi convertido para um dicionário.
    
- `"id" not in dados`: verifica a presença de uma chave obrigatória.
    
- `raise ValueError(...)`: indica que a resposta não atende ao formato esperado.
    
- `raise RuntimeError(...) from erro`: cria uma exceção de nível superior preservando a causa original.
    

O exemplo utiliza uma API pública de testes. A validação apresentada é intencionalmente básica: uma aplicação real também pode precisar verificar tipos, intervalos, campos opcionais e regras específicas do contrato da API.

## 11. Referência rápida

|Objetivo|Recurso|
|---|---|
|Consultar o status HTTP|`response.status_code`|
|Detectar erro HTTP|`response.raise_for_status()`|
|Ler cabeçalhos|`response.headers`|
|Consultar um cabeçalho opcional|`response.headers.get("Content-Type")`|
|Interpretar JSON|`response.json()`|
|Capturar erro de decodificação|`requests.exceptions.JSONDecodeError`|
|Ler conteúdo textual|`response.text`|
|Ler conteúdo binário|`response.content`|
|Consultar codificação|`response.encoding`|
|Consultar URL final|`response.url`|
|Inspecionar redirecionamentos|`response.history`|
|Desativar redirecionamentos automáticos|`allow_redirects=False`|
|Validar o tipo de um objeto|`isinstance(objeto, tipo)`|
|Consultar chave opcional|`dicionario.get("chave")`|
|Preservar causa de uma exceção|`raise NovaExcecao(...) from erro`|

---
# 📜 Requests — Paginação, Limites de Requisição e Rate Limiting

## 1. O que é paginação em APIs?

Quando uma API possui muitos registros, ela pode não retornar todos de uma vez. Em vez disso, divide os resultados em páginas. Esse mecanismo é chamado de **paginação** (_pagination_).

Imagine uma API que disponibiliza 1.000 usuários. Retornar todos em uma única resposta pode aumentar o consumo de memória, o tempo de processamento e o tamanho da transferência.

Uma API paginada pode retornar 100 registros por requisição, exigindo várias consultas para obter o conjunto completo.

```
API com 1.000 registros
          |
          v
   +------+------+
   |      |      |
 Página  Página  Página
   1       2       3
 100     100     100
registros registros registros
```

A quantidade de registros por página e a forma de navegar entre elas dependem da API. Não existe um único padrão de paginação para todos os serviços.

## 2. Paginação com `page` e `limit`

Um modelo comum utiliza dois parâmetros:

- `page`: indica qual página deve ser consultada.
    
- `limit`: define a quantidade máxima de registros solicitados por página.
    

Exemplo de URL:

```
https://api.example.com/users?page=2&limit=100
```

Nesse exemplo, o cliente solicita a segunda página, com um limite de 100 registros.

Com Requests, os parâmetros podem ser enviados utilizando `params`:

```
import requests

url = "https://httpbin.org/get"

response = requests.get(
    url,
    params={
        "page": 2,
        "limit": 100,
    },
    timeout=10,
)

response.raise_for_status()
print(response.url)
```

### Entendendo o código

- `params`: recebe os parâmetros da query string.
    
- `"page": 2`: informa o valor do parâmetro `page`.
    
- `"limit": 100`: informa o limite solicitado.
    
- `response.url`: mostra a URL efetivamente utilizada pelo Requests.
    

O Requests codifica os parâmetros para compor a URL. O endpoint `httpbin.org/get` é apenas um serviço de teste que permite observar a requisição; ele não implementa uma API real de usuários paginados.

## 3. Percorrendo páginas com um laço

Quando a API utiliza números de página, podemos usar um laço `while` ou `for` para percorrer os resultados.

O exemplo abaixo demonstra o fluxo utilizando uma estrutura de resposta fictícia, sem depender de um serviço externo específico.

```
def processar_paginas(buscar_pagina, limite=100):
    pagina = 1
    registros = []

    while True:
        resposta = buscar_pagina(
            page=pagina,
            limit=limite,
        )

        itens = resposta["items"]

        if not itens:
            break

        registros.extend(itens)
        pagina += 1

    return registros
```

### Entendendo cada parte

`**def processar_paginas(buscar_pagina, limite=100):**`

Define uma função que recebe outra função, `buscar_pagina`, e um limite de registros por página.

O parâmetro `limite=100` tem um valor padrão: se o chamador não informar outro valor, será utilizado `100`.

`**pagina = 1**`

Inicializa a paginação na primeira página. Algumas APIs começam em zero, portanto é necessário consultar a documentação do serviço.

`**registros = []**`

Cria uma lista vazia para reunir os itens obtidos.

`**while True:**`

Inicia um laço que continuará até que uma condição interna provoque sua interrupção.

`**resposta = buscar_pagina(...)**`

Chama a função responsável por consultar a API. O exemplo pressupõe que ela aceite os argumentos `page` e `limit`.

`**itens = resposta["items"]**`

Obtém a lista de itens de uma resposta cujo formato esperado contém a chave `items`.

`**if not itens: break**`

Interrompe o laço quando a página não contém itens. Esse critério só é adequado se a API documentar que uma lista vazia indica o fim dos resultados.

`**registros.extend(itens)**`

Adiciona os elementos de `itens` individualmente à lista `registros`.

`**pagina += 1**`

Avança para a próxima página.

`**return registros**`

Devolve a lista completa acumulada.

### Limitações do exemplo

A função pressupõe um formato específico de resposta e não implementa tratamento de erros, limite máximo de páginas nem proteção contra APIs que repetem indefinidamente a mesma página.

Além disso, algumas APIs retornam metadados como `total_pages`, `next`, `has_more` ou um cursor. Nesses casos, a condição de parada deve seguir o contrato real do serviço.

## 4. Implementando a consulta HTTP de cada página

Veja como conectar a ideia anterior a uma função que utiliza Requests:

```
import requests


def buscar_pagina(page: int, limit: int = 100) -> dict:
    response = requests.get(
        "https://api.example.com/users",
        params={
            "page": page,
            "limit": limit,
        },
        timeout=(3, 10),
    )

    response.raise_for_status()
    return response.json()
```

Essa função é um modelo para adaptar a uma API real.

- `page: int`: anota o parâmetro `page` como inteiro.
    
- `limit: int = 100`: define um limite padrão de 100 registros.
    
- `timeout=(3, 10)`: define os timeouts de conexão e leitura.
    
- `raise_for_status()`: interrompe o fluxo se houver um erro HTTP.
    
- `response.json()`: interpreta o corpo como JSON.
    

O domínio `api.example.com` é ilustrativo. Para utilizá-la, substitua a URL e confirme os nomes dos parâmetros e o formato da resposta na documentação da API escolhida.

## 5. Outros modelos de paginação

Nem toda API utiliza `page` e `limit`. Existem vários modelos comuns.

### Offset e limit

O parâmetro `offset` indica quantos registros devem ser ignorados antes de retornar os próximos resultados.

```
params = {
    "offset": 200,
    "limit": 100,
}
```

Nesse exemplo, o cliente solicita até 100 registros após os primeiros 200.

### Cursor

Algumas APIs retornam um cursor que identifica a posição a partir da qual a próxima página deve ser obtida.

```
params = {
    "cursor": "CURSOR_DE_EXEMPLO",
}
```

O valor do cursor costuma ser fornecido pela própria API. Ele não deve ser inventado nem tratado como um número de página.

### Link para a próxima página

Outra possibilidade é a resposta fornecer uma URL ou um campo `next` indicando a próxima página.

```
{
    "items": [
        {"id": 1},
        {"id": 2}
    ],
    "next": "https://api.example.com/users?cursor=abc"
}
```

Nesse modelo, o programa deve seguir o mecanismo de navegação documentado pela API. O exemplo de JSON acima é ilustrativo.

## 6. O que é rate limiting?

**Rate limiting** é um mecanismo utilizado por servidores para limitar a frequência ou a quantidade de requisições feitas por um cliente durante determinado período.

Por exemplo, uma API pode permitir até 60 requisições por minuto para uma credencial. Ao ultrapassar esse limite, pode responder com o status HTTP `429 Too Many Requests`.

Os limites podem ser aplicados por usuário, token, endereço IP, endpoint ou outros critérios definidos pelo serviço.

### Exemplo de fluxo

```
Cliente Python
      |
      v
Envia requisições
      |
      v
Servidor verifica o limite
      |
   +--+--+
   |     |
 Dentro  Limite
 limite  excedido
   |     |
   v     v
Resposta 429
normal   Too Many Requests
```

Os limites reais dependem da política da API. Não presuma que todos os serviços utilizam o mesmo número de requisições ou o mesmo período.

## 7. Detectando o status `429`

Uma forma simples de identificar uma resposta de rate limiting é consultar o status HTTP.

```
import requests

response = requests.get(
    "https://httpbin.org/status/429",
    timeout=10,
)

if response.status_code == 429:
    print("O servidor informou que o limite foi excedido.")
else:
    response.raise_for_status()
    print("Resposta sem erro HTTP.")
```

O endpoint de teste retorna o status `429`. Em uma API real, o comportamento e os cabeçalhos adicionais dependerão do serviço.

Observe que esse código trata o status explicitamente. Se chamássemos `raise_for_status()` antes da condição, uma resposta `429` geraria `HTTPError` e a condição posterior não seria executada, a menos que a exceção fosse capturada.

## 8. Interpretando `Retry-After`

Uma resposta `429` ou `503` pode conter o cabeçalho `Retry-After`, que informa quanto tempo o cliente deve aguardar antes de tentar novamente.

O valor pode ser expresso como um número de segundos ou como uma data HTTP, conforme a especificação.

```
retry_after = response.headers.get("Retry-After")

if retry_after is not None:
    print("Valor de Retry-After:", retry_after)
```

O método `.get()` retorna o valor do cabeçalho ou `None` caso ele não exista.

Não interprete sempre o valor como um inteiro: ele também pode conter uma data. Para implementar uma espera correta, consulte a especificação HTTP e a documentação da API.

Quando o cabeçalho estiver ausente, o cliente precisa seguir uma política de espera própria, com limites apropriados.

## 9. Pausa entre requisições

Em automações autorizadas, pode ser útil controlar a frequência das consultas.

```
import time
import requests

url = "https://httpbin.org/get"

for numero in range(3):
    response = requests.get(url, timeout=10)
    response.raise_for_status()

    print("Requisição concluída:", numero + 1)
    time.sleep(1)
```

### Entendendo o controle de frequência

- `range(3)`: gera três valores, de `0` a `2`.
    
- `numero + 1`: transforma esses valores em números de tentativa de `1` a `3` para exibição.
    
- `time.sleep(1)`: pausa a execução por aproximadamente um segundo entre as iterações.
    

A pausa reduz a frequência das chamadas feitas por esse laço, mas não garante o cumprimento de todos os limites de uma API. Outros processos, usuários ou políticas do servidor também podem afetar o rate limiting.

O exemplo é uma demonstração básica, não uma política completa de retries.

## 10. Combinando paginação, timeout e tratamento de erros

Ao consultar muitas páginas, é importante evitar loops ilimitados e tratar falhas de comunicação.

O exemplo abaixo demonstra uma estrutura adaptável para APIs que retornam `items` e `next_page`.

```
import requests


def obter_todos_os_itens(url: str) -> list:
    itens_completos = []
    pagina = 1
    max_paginas = 1000

    with requests.Session() as session:
        while pagina <= max_paginas:
            try:
                response = session.get(
                    url,
                    params={"page": pagina, "limit": 100},
                    timeout=(3, 10),
                )

                response.raise_for_status()
                dados = response.json()

            except requests.exceptions.RequestException as erro:
                raise RuntimeError(
                    f"Falha ao consultar a página {pagina}."
                ) from erro

            if not isinstance(dados, dict):
                raise ValueError("A resposta precisa ser um objeto JSON.")

            itens = dados.get("items")

            if not isinstance(itens, list):
                raise ValueError("O campo 'items' precisa ser uma lista.")

            if not itens:
                return itens_completos

            itens_completos.extend(itens)

            proxima_pagina = dados.get("next_page")

            if proxima_pagina is None:
                return itens_completos

            pagina = proxima_pagina

    raise RuntimeError("O limite máximo de páginas foi atingido.")
```

### O que esse exemplo pressupõe?

A API precisa retornar um objeto JSON com:

- `items`: lista de registros da página.
    
- `next_page`: número da próxima página, ou `null` quando não houver outra página.
    

Esse formato é hipotético. APIs reais podem utilizar outras estruturas.

O limite `max_paginas` funciona como uma proteção adicional. Ele não substitui o mecanismo oficial de paginação nem garante que todos os registros tenham sido obtidos.

Também é necessário validar o tipo e o valor de `next_page` antes de atribuí-lo a `pagina` em um programa de produção.

## 11. Boas práticas

1. Leia a documentação da API para descobrir o mecanismo de paginação.
    
2. Não presuma que a primeira página é `0` ou `1`; confirme o padrão do serviço.
    
3. Defina um timeout para cada requisição.
    
4. Respeite os limites de frequência informados pela API.
    
5. Trate o status `429` e considere o cabeçalho `Retry-After`.
    
6. Limite a quantidade máxima de páginas ou iterações.
    
7. Valide o formato da resposta antes de acessar seus campos.
    
8. Evite acumular milhões de registros em uma única lista se o processamento puder ser feito por página.
    
9. Utilize uma `Session` quando fizer sentido reutilizar conexões e configurações.
    
10. Evite repetir automaticamente operações que possam alterar dados sem considerar idempotência e duplicação.
    

## 12. Referência rápida

|Objetivo|Recurso|
|---|---|
|Paginação por página|`params={"page": 1, "limit": 100}`|
|Paginação por offset|`params={"offset": 0, "limit": 100}`|
|Enviar cursor|`params={"cursor": cursor}`|
|Identificar rate limiting|`response.status_code == 429`|
|Ler a orientação de espera|`response.headers.get("Retry-After")`|
|Pausar execução|`time.sleep(segundos)`|
|Reutilizar conexões|`requests.Session()`|
|Limitar iterações|`max_paginas`|
|Validar lista recebida|`isinstance(itens, list)`|
|Detectar erros HTTP|`response.raise_for_status()`|
|Configurar timeout|`timeout=(3, 10)`|

---
# 📜 Requests — Web Scraping com HTML e Beautiful Soup

## 1. O que é Web Scraping?

**Web scraping** é a técnica de obter informações de páginas web por meio de um programa. Com Python, podemos utilizar `requests` para buscar o conteúdo de uma página e `Beautiful Soup` para analisar o HTML recebido.

As duas bibliotecas possuem funções diferentes:

- `requests`: realiza a requisição HTTP e recebe a resposta do servidor.
    
- `Beautiful Soup`: interpreta a estrutura HTML e facilita a localização de elementos, textos e atributos.
    

O Requests não é, por si só, um parser HTML. Ele obtém o conteúdo; a análise da estrutura é realizada por uma ferramenta apropriada.

### Fluxo básico

```
Programa Python
      |
      v
requests.get()
      |
      v
Servidor Web
      |
      v
Resposta HTTP com HTML
      |
      v
Beautiful Soup
      |
      v
Elementos, textos e links
```

O scraping pode ser útil para coletar informações públicas, acompanhar páginas, estudar a estrutura de um site ou automatizar tarefas autorizadas. Antes de coletar dados, verifique os termos de uso, as regras de acesso e as restrições aplicáveis ao site.

---

## 2. Instalando Beautiful Soup

O Beautiful Soup é distribuído pelo pacote `beautifulsoup4`.

Em um terminal Linux, você pode instalá-lo no ambiente virtual do projeto:

```
python -m pip install requests beautifulsoup4
```

O comando instala:

- `requests`: biblioteca para requisições HTTP.
    
- `beautifulsoup4`: biblioteca para análise de HTML e XML.
    

É recomendável utilizar um ambiente virtual para separar as dependências do projeto das bibliotecas instaladas globalmente.

Para utilizar o parser `html.parser`, não é necessário instalar um pacote adicional: ele faz parte da biblioteca padrão do Python.

---

## 3. Obtendo o HTML com Requests

Antes de analisar uma página, precisamos obter o conteúdo HTML.

```
import requests

url = "https://example.com"

response = requests.get(
    url,
    timeout=(3, 10),
)

response.raise_for_status()

html = response.text

print(html[:500])
```

### Entendendo cada parte

- `import requests`: importa a biblioteca.
    
- `url`: armazena o endereço da página.
    
- `requests.get(...)`: envia uma requisição HTTP GET.
    
- `timeout=(3, 10)`: define os limites de espera para conexão e leitura.
    
- `response.raise_for_status()`: gera uma exceção se a resposta indicar um erro HTTP.
    
- `response.text`: disponibiliza o corpo da resposta como texto.
    
- `html[:500]`: utiliza slicing para obter os primeiros 500 caracteres.
    

O domínio `example.com` é um endereço reservado para exemplos. Substitua-o pelo endereço que deseja analisar e tem autorização para acessar.

### Por que utilizar `response.text`?

O HTML normalmente é texto, portanto `response.text` é conveniente para esse tipo de processamento. A codificação utilizada depende das informações disponíveis na resposta e da forma como o Requests determina a codificação.

Para conteúdo binário, como imagens ou arquivos compactados, utilize `response.content` em vez de tratar os bytes como HTML textual.

---

## 4. Criando um objeto Beautiful Soup

Depois de obter o HTML, podemos passá-lo ao Beautiful Soup.

```
from bs4 import BeautifulSoup

soup = BeautifulSoup(html, "html.parser")
```

### Entendendo os argumentos

- `BeautifulSoup(...)`: cria um objeto que representa o documento analisado.
    
- `html`: contém o texto HTML obtido com Requests.
    
- `"html.parser"`: seleciona o parser HTML incluído no Python.
    

A variável `soup` passa a oferecer métodos para localizar elementos, acessar atributos e extrair textos.

Por exemplo, considere este HTML:

```
<html>
  <head>
    <title>Página de exemplo</title>
  </head>
  <body>
    <h1>Bem-vindo</h1>
    <p>Conteúdo da página.</p>
  </body>
</html>
```

O Beautiful Soup interpreta as tags e permite navegar pela estrutura sem precisar procurar manualmente cada sequência de caracteres.

---

## 5. Encontrando elementos com `find()`

O método `find()` procura o primeiro elemento que corresponde aos critérios informados.

```
titulo = soup.find("h1")

if titulo:
    print(titulo.get_text(strip=True))
```

### Entendendo o código

- `soup.find("h1")`: procura o primeiro elemento `<h1>`.
    
- `titulo`: armazena o elemento encontrado ou `None` se não houver correspondência.
    
- `if titulo`: verifica se um elemento foi encontrado.
    
- `titulo.get_text(strip=True)`: extrai o texto do elemento e remove espaços em branco nas extremidades.
    

Se o HTML contiver:

```
<h1>Bem-vindo</h1>
```

O resultado será:

```
Bem-vindo
```

### Por que verificar o resultado?

Se o elemento não existir, `find()` retorna `None`. Tentar executar `titulo.get_text()` nesse caso causaria um `AttributeError`.

A verificação evita esse erro:

```
elemento = soup.find("h1")

if elemento is not None:
    texto = elemento.get_text(strip=True)
```

---

## 6. Encontrando vários elementos com `find_all()`

Enquanto `find()` retorna o primeiro elemento correspondente, `find_all()` retorna uma coleção de elementos encontrados.

Considere este HTML:

```
<ul>
  <li>Linux</li>
  <li>Python</li>
  <li>Redes</li>
</ul>
```

Para obter os itens da lista:

```
itens = soup.find_all("li")

for item in itens:
    print(item.get_text(strip=True))
```

Resultado:

```
Linux
Python
Redes
```

### Entendendo cada parte

- `soup.find_all("li")`: localiza todos os elementos `<li>` correspondentes.
    
- `itens`: recebe uma lista de elementos.
    
- `for item in itens`: percorre cada elemento encontrado.
    
- `item.get_text(strip=True)`: extrai o texto do elemento atual.
    

Se não houver correspondências, `find_all()` retorna uma lista vazia. Nesse caso, o laço simplesmente não executa nenhuma iteração.

---

## 7. Localizando elementos por classe CSS

Em HTML, o atributo `class` permite identificar ou agrupar elementos.

Exemplo:

```
<div class="produto">
  <h2>Teclado</h2>
</div>

<div class="produto">
  <h2>Mouse</h2>
</div>
```

Podemos localizar os elementos com a classe `produto`:

```
produtos = soup.find_all("div", class_="produto")

for produto in produtos:
    print(produto.get_text(" ", strip=True))
```

Resultado:

```
Teclado
Mouse
```

### Por que utilizar `class_`?

`class` é uma palavra reservada da linguagem Python. Por isso, o Beautiful Soup disponibiliza o argumento `class_` para pesquisar pelo atributo HTML `class`.

O método `get_text(" ", strip=True)` utiliza um espaço como separador entre textos descendentes e remove espaços em branco nas extremidades. Isso ajuda a evitar que palavras de elementos diferentes sejam concatenadas sem separação.

### Localizando por identificador

O atributo HTML `id` costuma identificar um elemento específico.

```
elemento = soup.find("div", id="principal")
```

Nesse exemplo, o Beautiful Soup procura um `<div>` cujo atributo `id` seja `principal`.

IDs devem ser únicos em um documento HTML válido, mas páginas reais podem conter HTML malformado ou identificadores duplicados.

---

## 8. Extraindo atributos de elementos

Além do texto, podemos precisar obter atributos como `href`, `src` ou `alt`.

Considere:

```
<a href="https://example.com/documentacao">Documentação</a>
```

Para extrair o endereço do link:

```
link = soup.find("a")

if link:
    href = link.get("href")
    texto = link.get_text(strip=True)

    print("Texto:", texto)
    print("URL:", href)
```

Resultado esperado:

```
Texto: Documentação
URL: https://example.com/documentacao
```

### O método `.get()`

Os elementos do Beautiful Soup permitem acessar atributos por meio de `.get()`.

```
href = link.get("href")
```

Se o atributo não existir, o método retorna `None` por padrão, em vez de gerar `KeyError`.

Outros exemplos:

```
imagem = soup.find("img")

if imagem:
    src = imagem.get("src")
    alt = imagem.get("alt", "")
```

- `src`: pode conter o endereço da imagem.
    
- `alt`: pode conter o texto alternativo da imagem.
    
- `get("alt", "")`: retorna uma string vazia caso o atributo esteja ausente.
    

O valor de `src` pode ser relativo, como `/imagens/foto.png`, e não necessariamente uma URL completa.

---

## 9. Transformando URLs relativas em URLs completas

Uma página pode conter links relativos:

```
<a href="/documentacao">Documentação</a>
```

O valor `/documentacao` não contém o domínio. Para transformá-lo em uma URL absoluta, utilize `urljoin()` do módulo `urllib.parse`.

```
from urllib.parse import urljoin

base_url = "https://example.com/pagina"
href = "/documentacao"

url_completa = urljoin(base_url, href)

print(url_completa)
```

Resultado:

```
https://example.com/documentacao
```

### Entendendo `urljoin()`

- `base_url`: URL usada como referência.
    
- `href`: endereço encontrado no atributo HTML.
    
- `urljoin(...)`: combina a referência com o endereço relativo segundo as regras de resolução de URLs.
    

O resultado também pode ser uma URL absoluta para outro domínio, caso o atributo contenha um endereço absoluto. Portanto, não presuma que todo link extraído aponta para o mesmo site.

---

## 10. Seletores CSS com `select()`

O Beautiful Soup também permite localizar elementos usando seletores CSS por meio de `select()`.

Exemplo de HTML:

```
<div class="artigo">
  <h2 class="titulo">Introdução ao Linux</h2>
  <p class="resumo">Conceitos básicos.</p>
</div>
```

Código:

```
titulos = soup.select("div.artigo h2.titulo")

for titulo in titulos:
    print(titulo.get_text(strip=True))
```

O seletor:

```
div.artigo h2.titulo
```

significa:

- `div`: selecionar elementos `<div>`.
    
- `.artigo`: exigir a classe `artigo`.
    
- espaço: procurar um descendente dentro do elemento correspondente.
    
- `h2`: selecionar elementos `<h2>`.
    
- `.titulo`: exigir a classe `titulo`.
    

### Outros seletores comuns

|Seletor|Significado|
|---|---|
|`p`|Todos os parágrafos|
|`.produto`|Elementos com a classe `produto`|
|`#principal`|Elemento com o ID `principal`|
|`a.link`|Links com a classe `link`|
|`div p`|Parágrafos descendentes de `div`|
|`ul > li`|Elementos `li` filhos diretos de `ul`|
|`a[href]`|Links que possuem o atributo `href`|

`select()` retorna uma lista de elementos. Para obter somente a primeira correspondência, você pode utilizar `select_one()`.

```
titulo = soup.select_one("h2.titulo")
```

Se nenhum elemento corresponder ao seletor, `select_one()` retorna `None`.

---

## 11. Exemplo completo: extraindo títulos e links

O exemplo abaixo reúne Requests e Beautiful Soup para extrair títulos e links de uma página de teste.

```
import requests
from bs4 import BeautifulSoup
from urllib.parse import urljoin


url = "https://example.com"

response = requests.get(
    url,
    timeout=(3, 10),
)

response.raise_for_status()

soup = BeautifulSoup(response.text, "html.parser")

for link in soup.find_all("a", href=True):
    texto = link.get_text(" ", strip=True)
    href = link.get("href")
    url_completa = urljoin(response.url, href)

    print(f"Texto: {texto}")
    print(f"URL: {url_completa}")
    print("-" * 40)
```

### Entendendo os detalhes

`**find_all("a", href=True)**`

Procura elementos `<a>` que tenham o atributo `href`.

`**get_text(" ", strip=True)**`

Extrai o texto do link, separando os textos descendentes por espaços e removendo espaços nas extremidades.

`**link.get("href")**`

Obtém o valor do atributo `href`.

`**urljoin(response.url, href)**`

Resolve o endereço relativo utilizando a URL final da resposta como referência. Isso também considera redirecionamentos seguidos pelo Requests.

`**print("-" * 40)**`

Repete o caractere `-` quarenta vezes, criando um separador visual entre os resultados.

O exemplo demonstra a extração estrutural de links. Ele não verifica se os destinos existem, se estão acessíveis ou se são seguros.

---

## 12. Limitação importante: páginas renderizadas com JavaScript

O Requests recebe a resposta HTTP disponibilizada pelo servidor. Ele não executa JavaScript como um navegador.

Isso significa que, se uma página preencher seus elementos somente após executar JavaScript no navegador, o HTML obtido pelo Requests pode não conter os dados esperados.

Antes de trocar de ferramenta, inspecione a resposta recebida e verifique se os dados já estão presentes no HTML. Algumas aplicações carregam dados por endpoints HTTP que podem ser consultados diretamente, desde que seu uso seja autorizado e respeite as regras do serviço.

Se a página depender realmente de renderização no navegador, ferramentas de automação de navegador, como Selenium, podem ser mais adequadas. A anotação principal de Selenium na sua biblioteca pode servir como referência para esse cenário.

---

## 13. Boas práticas para Web Scraping

1. **Verifique as regras de acesso do site.** Respeite os termos de uso, as políticas aplicáveis e as permissões necessárias.
    
2. **Configure timeouts.** Não deixe requisições aguardando indefinidamente.
    
3. **Verifique erros HTTP.** Utilize `raise_for_status()` antes de processar a resposta.
    
4. **Trate HTML ausente ou alterado.** A estrutura de uma página pode mudar sem aviso.
    
5. **Valide os elementos encontrados.** `find()` e `select_one()` podem retornar `None`.
    
6. **Não confie cegamente em atributos.** Links e nomes de arquivos podem ser relativos ou apontar para outros domínios.
    
7. **Controle a frequência das requisições.** Evite sobrecarregar os servidores e respeite os limites de acesso.
    
8. **Não colete dados pessoais indiscriminadamente.** Considere privacidade, base legal e finalidade da coleta.
    
9. **Não presuma que o HTML contém toda a informação.** Conteúdo dinâmico pode ser carregado separadamente.
    
10. **Evite depender de seletores frágeis.** Mudanças de layout podem invalidar seletores muito específicos.
    

## 14. Referência rápida

| Objetivo                             | Recurso                               |
| ------------------------------------ | ------------------------------------- |
| Obter uma página                     | `requests.get(url)`                   |
| Verificar erro HTTP                  | `response.raise_for_status()`         |
| Ler o HTML como texto                | `response.text`                       |
| Criar o parser                       | `BeautifulSoup(html, "html.parser")`  |
| Encontrar o primeiro elemento        | `soup.find("tag")`                    |
| Encontrar vários elementos           | `soup.find_all("tag")`                |
| Buscar por classe                    | `soup.find_all("div", class_="nome")` |
| Buscar por ID                        | `soup.find("div", id="principal")`    |
| Extrair texto                        | `elemento.get_text(strip=True)`       |
| Extrair atributo                     | `elemento.get("href")`                |
| Utilizar seletores CSS               | `soup.select("div.classe a")`         |
| Obter a primeira correspondência CSS | `soup.select_one("h2.titulo")`        |
| Resolver URLs relativas              | `urljoin(base_url, href)`             |

---
# 📜 Requests — Download e Streaming de Arquivos

## 1. O que é o download de arquivos com Requests?

A biblioteca `requests` permite baixar arquivos de servidores HTTP e HTTPS, como imagens, documentos, arquivos de texto, arquivos compactados e outros conteúdos disponibilizados por uma URL.

O processo básico consiste em:

1. Enviar uma requisição HTTP para a URL.
    
2. Receber a resposta do servidor.
    
3. Verificar se a resposta indica sucesso.
    
4. Obter o conteúdo recebido.
    
5. Gravar esse conteúdo em um arquivo local.
    

Exemplo de fluxo:

```
Programa Python
      |
      | GET https://exemplo.com/arquivo.zip
      v
Servidor HTTP
      |
      | Resposta contendo os bytes do arquivo
      v
Programa Python
      |
      | Grava os dados no disco
      v
arquivo.zip
```

Uma diferença importante é que **receber o conteúdo de um arquivo e salvá-lo no disco são operações distintas**. O Requests cuida da comunicação HTTP; o Python pode usar `open()` ou `pathlib` para gravar os dados localmente.

## 2. Download básico com `requests.get()`

Para arquivos pequenos, podemos acessar o conteúdo recebido pela propriedade `response.content`.

```
import requests

url = "https://example.com/arquivo.txt"

response = requests.get(url, timeout=15)
response.raise_for_status()

with open("arquivo.txt", "wb") as arquivo:
    arquivo.write(response.content)
```

### Explicação linha por linha

**Linha 1 — importar a biblioteca**

```
import requests
```

Importa o Requests, permitindo realizar requisições HTTP.

**Linha 2 — definir a URL**

```
url = "https://example.com/arquivo.txt"
```

Armazena o endereço do recurso que queremos solicitar. `example.com` é um domínio reservado para exemplos; essa URL não garante que exista um arquivo para baixar.

**Linha 3 — enviar a requisição**

```
response = requests.get(url, timeout=15)
```

- `requests.get()` envia uma requisição HTTP GET.
    
- `url` indica o recurso solicitado.
    
- `timeout=15` limita a espera pela conexão e pelos dados recebidos, conforme os limites de timeout do Requests.
    
- `response` recebe o objeto `Response`, que contém o status HTTP, os cabeçalhos e o conteúdo da resposta.
    

O timeout não representa necessariamente um limite total de 15 segundos para toda a transferência. Para definir limites separados de conexão e leitura, podemos usar uma tupla, como `timeout=(5, 15)`.

**Linha 4 — verificar o status HTTP**

```
response.raise_for_status()
```

Gera uma exceção para respostas HTTP de erro, normalmente status `4xx` ou `5xx`.

Isso evita gravar silenciosamente uma página de erro no lugar do arquivo esperado. Não garante, por si só, que o conteúdo seja realmente o arquivo pretendido.

**Linha 5 — abrir o arquivo**

```
with open("arquivo.txt", "wb") as arquivo:
```

- `open()` abre ou cria um arquivo local.
    
- `"arquivo.txt"` é o caminho de destino.
    
- `"wb"` significa _write binary_: gravação em modo binário.
    
- `with` garante que o arquivo seja fechado ao sair do bloco, inclusive se ocorrer uma exceção.
    
- `as arquivo` atribui o objeto aberto à variável `arquivo`.
    

O modo binário é adequado para arquivos de texto e arquivos não textuais, pois grava os bytes sem tentar convertê-los automaticamente para caracteres.

**Linha 6 — gravar o conteúdo**

```
arquivo.write(response.content)
```

A propriedade `response.content` fornece o corpo da resposta em bytes. O método `write()` grava esses bytes no arquivo.

### Limitação desse método

`response.content` mantém o corpo inteiro da resposta disponível em memória. Para arquivos grandes, isso pode consumir muita RAM.

É nesse cenário que o streaming se torna útil.

## 3. O que é streaming?

Streaming é uma forma de processar os dados gradualmente, em vez de exigir que o conteúdo inteiro seja carregado em memória antes de começar o processamento.

No Requests, usamos o parâmetro `stream=True` para solicitar que o corpo da resposta seja consumido sob demanda.

```
response = requests.get(
    url,
    stream=True,
    timeout=(5, 30),
)
```

Nesse exemplo:

- `url`: endereço do recurso.
    
- `stream=True`: permite consumir o corpo da resposta gradualmente.
    
- `timeout=(5, 30)`: define um timeout de conexão de 5 segundos e um timeout de leitura de 30 segundos.
    

O streaming não significa que o Requests não armazene nenhum dado internamente. Significa que o programa pode consumir a resposta em partes, evitando carregar todo o arquivo de uma vez.

## 4. O método `iter_content()`

O método `iter_content()` permite percorrer o corpo da resposta em blocos de bytes.

Sintaxe:

```
response.iter_content(chunk_size=8192)
```

### Parâmetros

|Parâmetro|Significado|
|---|---|
|`chunk_size`|Tamanho solicitado para cada bloco, em bytes|
|Retorno|Um iterador que fornece blocos do conteúdo|

O valor `8192` corresponde a 8 KiB. Esse tamanho é uma escolha comum, mas não é obrigatório. Podemos escolher blocos menores ou maiores conforme a aplicação.

O tamanho efetivo dos blocos pode variar; o último bloco também pode ser menor. Blocos vazios devem ser ignorados durante a gravação.

Exemplo:

```
for bloco in response.iter_content(chunk_size=8192):
    if bloco:
        arquivo.write(bloco)
```

### Explicação

`**for bloco in ...**`

Percorre os blocos de dados disponibilizados pelo iterador. A variável `bloco` recebe os bytes de cada parte.

`**if bloco:**`

Verifica se o bloco contém dados. Blocos vazios são ignorados.

`**arquivo.write(bloco)**`

Grava cada parte no arquivo antes de avançar para o próximo bloco.

Dessa forma, não precisamos manter o arquivo inteiro na memória para salvá-lo.

## 5. Download completo usando streaming

O exemplo abaixo reúne streaming, timeout, validação do status HTTP e gravação binária.

```
import requests

url = "https://example.com/arquivo.zip"
destino = "arquivo.zip"

with requests.get(
    url,
    stream=True,
    timeout=(5, 30),
) as response:
    response.raise_for_status()

    with open(destino, "wb") as arquivo:
        for bloco in response.iter_content(chunk_size=8192):
            if bloco:
                arquivo.write(bloco)
```

### Entendendo a estrutura

`**with requests.get(...) as response:**`

Usa o objeto `Response` como gerenciador de contexto. Ao sair do bloco, a resposta é fechada, liberando os recursos associados.

`**response.raise_for_status()**`

Valida o status HTTP antes de começar a gravar o conteúdo.

`**with open(destino, "wb") as arquivo:**`

Abre o destino para gravação binária. Se o arquivo já existir, o modo `"wb"` trunca seu conteúdo anterior.

`**for bloco in response.iter_content(...)**`

Consome o conteúdo progressivamente.

`**if bloco:**`

Evita tentar gravar um bloco vazio.

`**arquivo.write(bloco)**`

Grava os bytes recebidos.

### Por que usar dois blocos `with`?

Cada bloco administra um recurso diferente:

- O primeiro cuida da resposta HTTP.
    
- O segundo cuida do arquivo local.
    

Os dois recursos são fechados quando seus respectivos blocos terminam, inclusive quando ocorre uma exceção.

**Importante:** se a transferência falhar no meio, o arquivo de destino poderá conter apenas parte dos dados. O uso de `with` fecha os recursos, mas não torna o download automaticamente completo nem remove arquivos parciais.

## 6. Usando `pathlib` para definir o destino

A biblioteca padrão `pathlib` oferece uma maneira organizada de trabalhar com caminhos.

```
from pathlib import Path

destino = Path("downloads") / "arquivo.zip"
destino.parent.mkdir(parents=True, exist_ok=True)
```

### Explicação

`**from pathlib import Path**`

Importa a classe `Path`, utilizada para representar e manipular caminhos de arquivos e diretórios.

`**Path("downloads") / "arquivo.zip"**`

Monta o caminho `downloads/arquivo.zip`. O operador `/` combina partes do caminho de maneira compatível com o sistema operacional.

`**destino.parent**`

Retorna o diretório pai do caminho, neste caso `downloads`.

`**mkdir(parents=True, exist_ok=True)**`

- `parents=True`: cria os diretórios intermediários que ainda não existirem.
    
- `exist_ok=True`: não gera erro apenas porque o diretório já existe.
    

Podemos utilizar esse caminho diretamente no exemplo de streaming:

```
with open(destino, "wb") as arquivo:
    ...
```

A função `open()` aceita objetos `Path`, portanto não é necessário converter o caminho para string.

## 7. Verificando o tamanho informado pelo servidor

O cabeçalho HTTP `Content-Length`, quando presente, pode informar o tamanho do corpo da resposta em bytes.

```
content_length = response.headers.get("Content-Length")
```

O método `.get()` tenta obter o valor do cabeçalho. Se ele não existir, retorna `None`.

Os cabeçalhos HTTP são acessados por meio de `response.headers`.

### Convertendo o valor

```
if content_length is not None:
    tamanho_esperado = int(content_length)
```

O valor de um cabeçalho é representado como texto. `int()` converte a string numérica em um inteiro.

É necessário validar o valor antes de confiar nele: um servidor pode omitir o cabeçalho ou enviar um valor inválido. Também não se deve presumir que ele sempre estará disponível.

### Contando os bytes gravados

```
total_bytes = 0

for bloco in response.iter_content(chunk_size=8192):
    if bloco:
        arquivo.write(bloco)
        total_bytes += len(bloco)
```

- `total_bytes = 0`: inicializa o contador.
    
- `len(bloco)`: retorna o número de bytes do bloco.
    
- `total_bytes += len(bloco)`: soma o tamanho do bloco ao total acumulado.
    

Ao final, `total_bytes` representa a quantidade de bytes que o código gravou.

Comparar esse total com um `Content-Length` válido pode ajudar a detectar uma transferência incompleta, mas não comprova a integridade do arquivo. O cabeçalho pode estar ausente ou não representar o tamanho de uma forma que permita essa comparação direta, como em respostas com conteúdo comprimido ou transferido em partes.

Para verificar integridade com mais confiança, utilize também um hash de referência confiável, quando disponível.

## 8. Exemplo completo com diretório, streaming e tratamento de erros

O exemplo a seguir grava o download em um arquivo temporário dentro do diretório de destino. Somente depois de terminar a transferência ele renomeia o arquivo para o nome final.

```
from pathlib import Path

import requests

url = "https://example.com/arquivo.zip"

destino = Path("downloads") / "arquivo.zip"
temporario = destino.with_name(destino.name + ".part")

destino.parent.mkdir(parents=True, exist_ok=True)

try:
    with requests.get(
        url,
        stream=True,
        timeout=(5, 30),
    ) as response:
        response.raise_for_status()

        with temporario.open("wb") as arquivo:
            for bloco in response.iter_content(chunk_size=8192):
                if bloco:
                    arquivo.write(bloco)

    temporario.replace(destino)
    print(f"Download concluído: {destino}")

except requests.exceptions.Timeout:
    print("O download excedeu o tempo de espera.")

except requests.exceptions.HTTPError as erro:
    print(f"Erro HTTP: {erro}")

except requests.exceptions.RequestException as erro:
    print(f"Erro na requisição: {erro}")

except OSError as erro:
    print(f"Erro ao gravar ou renomear o arquivo: {erro}")
```

### Análise das partes importantes

**Arquivo temporário**

```
temporario = destino.with_name(destino.name + ".part")
```

Cria um caminho com o mesmo diretório e o nome acrescido de `.part`. Por exemplo, `arquivo.zip.part`.

Isso ajuda a distinguir um download em andamento de um arquivo que já recebeu o nome final.

**Criação do diretório**

```
destino.parent.mkdir(parents=True, exist_ok=True)
```

Garante que o diretório de destino exista antes da gravação.

**Gravação em streaming**

O conteúdo é escrito progressivamente em `temporario`, sem precisar ser carregado por inteiro na memória.

**Renomeação final**

```
temporario.replace(destino)
```

Substitui o destino final pelo arquivo temporário quando a transferência terminou sem lançar uma exceção.

Essa renomeação no mesmo sistema de arquivos costuma ser uma forma prática de evitar que o nome final apareça antes de o download terminar. Ainda assim, ela não garante a integridade do conteúdo e não equivale, por si só, a uma operação de gravação durável.

**Tratamento de erros**

- `Timeout`: a operação excedeu o limite de espera.
    
- `HTTPError`: o servidor retornou um status HTTP de erro, detectado por `raise_for_status()`.
    
- `RequestException`: cobre outros erros do Requests, como problemas de conexão.
    
- `OSError`: cobre falhas locais de arquivos e diretórios, como falta de permissão ou espaço em disco.
    

Esse exemplo não implementa retomada automática. Se a transferência falhar, um arquivo `.part` poderá permanecer no disco. Em uma aplicação real, decida se deseja removê-lo, mantê-lo para diagnóstico ou implementar retomada com validação apropriada.

## 9. `response.content` versus `response.iter_content()`

|Característica|`response.content`|`response.iter_content()`|
|---|---|---|
|Forma de consumo|Corpo completo em bytes|Blocos de bytes|
|Uso de memória|Pode crescer com o tamanho do arquivo|Pode manter o consumo mais controlado|
|Ideal para|Arquivos pequenos e respostas simples|Arquivos grandes ou transferências longas|
|Requer `stream=True`|Não|Recomendado para controlar o consumo da resposta desde o início|
|Gravação incremental|Não é necessária para obter o conteúdo|Sim, normalmente feita em um laço|

A escolha depende do tamanho do recurso e da forma como o programa precisa processar os dados. Streaming não torna a rede necessariamente mais rápida; sua principal vantagem é permitir consumir e gravar o conteúdo progressivamente.

## 10. Erros e limitações comuns

### Baixar uma página de erro como se fosse um arquivo

**Causa:** o programa grava o corpo da resposta sem validar o status HTTP.

**Prevenção:**

```
response.raise_for_status()
```

Isso evita prosseguir silenciosamente diante de um status HTTP de erro, mas ainda é necessário validar se a resposta corresponde ao arquivo esperado.

### Falta de espaço em disco

**Causa:** o sistema não tem espaço suficiente para gravar o conteúdo.

**Prevenção:** tratar `OSError` e, quando necessário, verificar o espaço disponível antes de iniciar a transferência.

### Arquivo parcial

**Causa:** queda de conexão, timeout ou erro durante a gravação.

**Prevenção:** usar um arquivo temporário e só promover o arquivo ao nome final após concluir a transferência. Para confirmar integridade, compare tamanho e hash com referências confiáveis, quando disponíveis.

### Arquivo inesperado

**Causa:** a URL retorna HTML, JSON ou outro conteúdo, embora o programa espere um arquivo binário.

**Prevenção:** inspecionar o status HTTP, o cabeçalho `Content-Type`, o tamanho e, quando necessário, a estrutura ou o hash do conteúdo. O `Content-Type` é uma indicação, não uma garantia de que o arquivo seja válido.

### Download muito demorado

**Causa:** servidor lento, rede instável ou grande volume de dados.

**Prevenção:** configurar timeouts adequados, tratar erros e implementar tentativas controladas quando isso fizer sentido. A retomada de um download exige suporte apropriado do servidor, por exemplo, requisições com cabeçalho `Range`, e validação dos dados já recebidos.

### Consumo excessivo de memória

**Causa:** usar `response.content` para carregar um arquivo muito grande.

**Prevenção:** usar `stream=True` e `iter_content()` para gravar os dados em blocos.

## 11. Boas práticas

- Defina timeouts para não deixar requisições esperando indefinidamente.
    
- Verifique o status HTTP antes de gravar o conteúdo.
    
- Use modo binário (`"wb"`) para salvar arquivos.
    
- Prefira streaming para arquivos grandes.
    
- Feche as respostas e os arquivos com `with`.
    
- Crie o diretório de destino antes de iniciar a gravação.
    
- Trate erros de rede e erros locais separadamente quando isso ajudar no diagnóstico.
    
- Não confie cegamente em nomes de arquivos fornecidos por servidores. Valide-os e impeça que caminhos externos escapem do diretório de destino.
    
- Considere limites de tamanho para evitar downloads inesperadamente grandes.
    
- Use hashes de fontes confiáveis quando precisar verificar a integridade do arquivo.
    
- Baixe apenas recursos que você tem autorização para acessar e respeite as regras do serviço.
    

## 12. Referência rápida

| Elemento                                  | Finalidade                                  |
| ----------------------------------------- | ------------------------------------------- |
| `response.content`                        | Acessar o corpo completo como bytes         |
| `stream=True`                             | Consumir o corpo da resposta sob demanda    |
| `response.iter_content(chunk_size=8192)`  | Iterar pelos dados em blocos                |
| `response.raise_for_status()`             | Gerar exceção para status HTTP de erro      |
| `response.headers.get("Content-Length")`  | Consultar o tamanho informado pelo servidor |
| `Path("downloads")`                       | Representar um caminho de diretório         |
| `Path.mkdir(parents=True, exist_ok=True)` | Criar o diretório, incluindo pais           |
| `Path.replace()`                          | Renomear ou substituir o caminho de destino |
| `timeout=(5, 30)`                         | Definir timeouts de conexão e leitura       |
| `with`                                    | Gerenciar o fechamento de recursos          |

---
# 📜 Requests — Upload de Arquivos e Envio de Dados Multipart

## 1. O que é um upload HTTP?

Um upload é o envio de dados do cliente para um servidor. Com a biblioteca `requests`, podemos enviar arquivos, formulários, JSON e outros tipos de conteúdo em requisições HTTP.

Exemplos de situações em que uploads são utilizados:

- Enviar uma imagem para um site.
    
- Fazer upload de um documento para uma API.
    
- Enviar um arquivo de log para um sistema autorizado de monitoramento.
    
- Submeter dados de um formulário web.
    
- Enviar um arquivo para um endpoint de armazenamento.
    

O método HTTP utilizado depende da API. `POST` é comum para criar recursos ou submeter dados, enquanto `PUT` também pode ser utilizado para criar ou substituir um recurso em uma URL específica.

## 2. Enviando dados de formulário com `data=`

O parâmetro `data=` permite enviar dados de formulário, normalmente codificados como `application/x-www-form-urlencoded`, quando seu valor é um dicionário.

```
import requests

url = "https://httpbin.org/post"

dados = {
    "nome": "Ana",
    "curso": "Python",
}

response = requests.post(
    url,
    data=dados,
    timeout=15,
)

response.raise_for_status()

print(response.status_code)
print(response.json())
```

### Explicação linha por linha

**Importar a biblioteca**

```
import requests
```

Importa o Requests para realizar requisições HTTP.

**Definir a URL**

```
url = "https://httpbin.org/post"
```

Define o endpoint que receberá os dados. O `httpbin.org` é um serviço de teste de requisições; não é necessário utilizar esse serviço em aplicações reais.

**Criar o dicionário**

```
dados = {
    "nome": "Ana",
    "curso": "Python",
}
```

Cria um dicionário Python com os campos que serão enviados.

- `"nome"` e `"curso"` são as chaves.
    
- `"Ana"` e `"Python"` são os valores.
    

**Enviar os dados**

```
response = requests.post(url, data=dados, timeout=15)
```

- `requests.post()` envia uma requisição HTTP POST.
    
- `url` indica o destino.
    
- `data=dados` envia os campos do formulário.
    
- `timeout=15` define os limites de espera da requisição.
    
- `response` recebe a resposta do servidor.
    

**Validar a resposta**

```
response.raise_for_status()
```

Gera uma exceção se a resposta possuir um status HTTP de erro, evitando continuar silenciosamente diante de uma resposta `4xx` ou `5xx`.

**Ler o resultado**

```
print(response.status_code)
print(response.json())
```

- `response.status_code` contém o código de status HTTP.
    
- `response.json()` tenta interpretar o corpo da resposta como JSON e devolve objetos Python, como dicionários e listas.
    

O uso de `.json()` pressupõe que a resposta contenha JSON válido. Caso contrário, a interpretação poderá falhar.

## 3. Diferença entre `data=` e `json=`

Os dois parâmetros enviam dados no corpo de uma requisição, mas possuem finalidades diferentes.

|Parâmetro|Uso comum|Formato enviado|
|---|---|---|
|`data=`|Formulários e dados codificados|Formulário URL-encoded quando recebe um dicionário|
|`json=`|APIs que recebem JSON|JSON serializado automaticamente|

### Exemplo com `data=`

```
requests.post(
    url,
    data={"usuario": "ana"},
    timeout=15,
)
```

Esse formato é adequado quando o endpoint espera um formulário tradicional.

### Exemplo com `json=`

```
requests.post(
    url,
    json={"usuario": "ana"},
    timeout=15,
)
```

Nesse caso, o Requests serializa o dicionário para JSON e define o cabeçalho `Content-Type` apropriado.

O formato correto depende da documentação do servidor. Enviar JSON para um endpoint que espera um formulário pode causar erro, mesmo que os campos tenham os nomes corretos.

## 4. Enviando um arquivo com `files=`

Para enviar arquivos usando o formato `multipart/form-data`, o Requests oferece o parâmetro `files=`.

```
import requests

url = "https://httpbin.org/post"

with open("relatorio.txt", "rb") as arquivo:
    response = requests.post(
        url,
        files={"arquivo": arquivo},
        timeout=30,
    )

response.raise_for_status()

print(response.status_code)
```

### Explicação

**Abrir o arquivo**

```
with open("relatorio.txt", "rb") as arquivo:
```

- `open()` abre o arquivo local.
    
- `"relatorio.txt"` é o caminho do arquivo.
    
- `"rb"` significa leitura binária.
    
- `with` fecha o arquivo automaticamente ao sair do bloco.
    
- `arquivo` é o objeto usado para ler o conteúdo.
    

O modo binário é apropriado para arquivos que podem conter bytes que não representam texto, como imagens e PDFs.

**Enviar o arquivo**

```
files={"arquivo": arquivo}
```

O dicionário associa o nome do campo esperado pelo servidor ao objeto de arquivo.

Aqui, `"arquivo"` é o nome do campo multipart. Ele não precisa ser igual ao nome do arquivo local.

**Realizar a requisição**

```
response = requests.post(
    url,
    files={"arquivo": arquivo},
    timeout=30,
)
```

O Requests constrói o corpo `multipart/form-data`, incluindo o delimitador (_boundary_) e os cabeçalhos necessários para separar as partes. Não defina manualmente o cabeçalho `Content-Type: multipart/form-data` nesse caso, pois o Requests precisa incluir o `boundary` correto.

O endpoint de destino deve aceitar uploads multipart. Caso contrário, o servidor poderá rejeitar a requisição.

## 5. Enviando um arquivo com nome e tipo de conteúdo definidos

Em algumas APIs, é necessário controlar o nome enviado ou declarar o tipo MIME do arquivo.

O parâmetro `files=` aceita tuplas com informações adicionais.

```
import requests

url = "https://httpbin.org/post"

with open("relatorio.txt", "rb") as arquivo:
    response = requests.post(
        url,
        files={
            "documento": (
                "relatorio-enviado.txt",
                arquivo,
                "text/plain",
            )
        },
        timeout=30,
    )

response.raise_for_status()
```

A tupla possui os seguintes elementos:

```
("relatorio-enviado.txt", arquivo, "text/plain")
```

1. `"relatorio-enviado.txt"`: nome do arquivo apresentado ao servidor.
    
2. `arquivo`: objeto que fornece o conteúdo.
    
3. `"text/plain"`: tipo MIME declarado para o arquivo.
    

O nome transmitido não precisa ser igual ao nome local. O tipo MIME é uma declaração, não uma verificação automática do conteúdo real.

Outros tipos MIME comuns incluem:

|Tipo de conteúdo|MIME type|
|---|---|
|Texto simples|`text/plain`|
|JSON|`application/json`|
|PDF|`application/pdf`|
|PNG|`image/png`|
|JPEG|`image/jpeg`|
|ZIP|`application/zip`|

A API pode exigir um tipo MIME específico ou realizar sua própria validação.

## 6. Enviando campos de formulário e arquivos juntos

Um formulário pode conter campos de texto e arquivos na mesma requisição multipart.

```
import requests

url = "https://httpbin.org/post"

with open("relatorio.txt", "rb") as arquivo:
    response = requests.post(
        url,
        data={
            "titulo": "Relatório",
            "categoria": "estudo",
        },
        files={
            "arquivo": arquivo,
        },
        timeout=30,
    )

response.raise_for_status()

print(response.json())
```

### O que acontece nessa requisição?

- `data=` fornece campos de formulário.
    
- `files=` fornece o arquivo.
    
- O Requests combina as partes no corpo multipart.
    
- O servidor recebe os campos e o arquivo na mesma requisição.
    

A API determina os nomes dos campos e os valores esperados. Utilizar `titulo`, `categoria` e `arquivo` só funciona se o endpoint reconhecer esses campos.

## 7. Enviando múltiplos arquivos

Se a API aceitar vários arquivos, podemos utilizar uma lista de partes multipart.

```
import requests

url = "https://httpbin.org/post"

with (
    open("imagem1.png", "rb") as arquivo1,
    open("imagem2.png", "rb") as arquivo2,
):
    response = requests.post(
        url,
        files=[
            ("arquivos", ("imagem1.png", arquivo1, "image/png")),
            ("arquivos", ("imagem2.png", arquivo2, "image/png")),
        ],
        timeout=30,
    )

response.raise_for_status()
```

### Por que utilizar uma lista?

Uma lista permite repetir o mesmo nome de campo, como `"arquivos"`, para enviar várias partes com esse nome.

Cada tupla contém:

1. O nome do campo esperado pela API.
    
2. O nome do arquivo enviado.
    
3. O objeto de arquivo.
    
4. O tipo MIME declarado.
    

No exemplo, cada tupla possui três elementos agrupados depois do nome do campo: nome do arquivo, objeto de arquivo e tipo MIME. O Requests aceita essa estrutura.

O servidor precisa estar preparado para interpretar múltiplas partes com o mesmo nome de campo.

## 8. Enviando arquivos com `requests.Session()`

Uma sessão pode ser útil quando várias requisições precisam reutilizar configurações, cookies ou autenticação.

```
import requests

url = "https://httpbin.org/post"

with requests.Session() as session:
    session.headers.update({
        "User-Agent": "MeuClientePython/1.0",
    })

    with open("relatorio.txt", "rb") as arquivo:
        response = session.post(
            url,
            files={"arquivo": arquivo},
            timeout=30,
        )

    response.raise_for_status()
```

### Entendendo a sessão

**Criar a sessão**

```
with requests.Session() as session:
```

Cria uma sessão HTTP. O objeto pode reutilizar conexões e manter cookies entre requisições.

**Configurar cabeçalhos**

```
session.headers.update({
    "User-Agent": "MeuClientePython/1.0",
})
```

Define um cabeçalho padrão para as requisições feitas pela sessão.

**Enviar o arquivo**

```
response = session.post(
    url,
    files={"arquivo": arquivo},
    timeout=30,
)
```

Envia o arquivo utilizando as configurações da sessão.

A sessão não elimina a necessidade de abrir e fechar corretamente o arquivo, configurar timeouts e validar a resposta HTTP.

## 9. Enviando um arquivo para uma API autenticada

Algumas APIs exigem um token de acesso. O token pode ser enviado em um cabeçalho de autorização, de acordo com a documentação do serviço.

```
import os
import requests

url = "https://api.example.com/uploads"
token = os.environ.get("API_TOKEN")

if not token:
    raise RuntimeError("A variável API_TOKEN não foi configurada.")

with open("relatorio.pdf", "rb") as arquivo:
    response = requests.post(
        url,
        headers={
            "Authorization": f"Bearer {token}",
        },
        files={
            "arquivo": (
                "relatorio.pdf",
                arquivo,
                "application/pdf",
            )
        },
        timeout=30,
    )

response.raise_for_status()
print(response.status_code)
```

### Elementos importantes

**Obter o token**

```
token = os.environ.get("API_TOKEN")
```

Lê o valor da variável de ambiente `API_TOKEN`. Se ela não existir, o resultado será `None`.

**Validar a configuração**

```
if not token:
    raise RuntimeError("A variável API_TOKEN não foi configurada.")
```

Interrompe o programa com uma mensagem clara quando o token não está disponível.

**Montar o cabeçalho**

```
"Authorization": f"Bearer {token}"
```

A f-string insere o valor do token no cabeçalho de autorização.

O endereço `api.example.com` é ilustrativo. Para utilizar o código, substitua-o pelo endpoint real e siga o mecanismo de autenticação documentado pela API.

Não grave tokens diretamente no código nem os imprima em logs. Utilize HTTPS e proteja as variáveis de ambiente e os arquivos que contenham credenciais.

## 10. Tratamento de erros durante o upload

Uploads podem falhar por problemas de rede, autenticação, validação do arquivo, tamanho máximo permitido ou indisponibilidade do servidor.

```
import requests

try:
    with open("relatorio.pdf", "rb") as arquivo:
        response = requests.post(
            "https://httpbin.org/post",
            files={"arquivo": arquivo},
            timeout=(5, 30),
        )

    response.raise_for_status()

except FileNotFoundError:
    print("O arquivo local não foi encontrado.")

except PermissionError:
    print("Sem permissão para abrir o arquivo.")

except requests.exceptions.Timeout:
    print("A requisição excedeu o tempo de espera.")

except requests.exceptions.HTTPError as erro:
    print(f"O servidor retornou um erro HTTP: {erro}")

except requests.exceptions.RequestException as erro:
    print(f"Falha na requisição: {erro}")

except OSError as erro:
    print(f"Erro local de arquivo: {erro}")
```

### Por que separar os erros?

- `FileNotFoundError`: o caminho não aponta para um arquivo encontrado.
    
- `PermissionError`: o programa não possui permissão para acessar o arquivo.
    
- `Timeout`: a requisição excedeu um limite de espera.
    
- `HTTPError`: `raise_for_status()` identificou um status HTTP de erro.
    
- `RequestException`: outros erros de requisição, incluindo falhas de conexão.
    
- `OSError`: outros erros de entrada e saída, incluindo falhas locais de arquivo.
    

O bloco `except OSError` pode capturar erros de arquivo que não foram capturados pelos blocos anteriores. A ordem das exceções importa porque algumas classes são subclasses de outras.

## 11. Uploads não são automaticamente idempotentes

Um detalhe importante: se uma requisição falhar após o servidor processar o arquivo, o cliente pode não saber se a operação foi concluída.

Por exemplo:

1. O cliente envia um arquivo.
    
2. O servidor recebe e salva o arquivo.
    
3. A conexão cai antes de o cliente receber a resposta.
    
4. O cliente observa um erro e tenta novamente.
    
5. O servidor pode processar o mesmo arquivo outra vez.
    

Isso pode gerar duplicação, dependendo do comportamento da API.

Antes de implementar tentativas automáticas, verifique:

- Se o endpoint permite repetir a operação com segurança.
    
- Se a API oferece uma chave de idempotência.
    
- Se existe um identificador único para o recurso.
    
- Se o servidor retorna informações que permitem verificar o resultado da primeira tentativa.
    

Não presuma que todo `POST` pode ser repetido sem efeitos colaterais.

## 12. Boas práticas de segurança

- Envie arquivos somente para endpoints confiáveis e autorizados.
    
- Utilize HTTPS para proteger a comunicação em trânsito.
    
- Não inclua credenciais ou tokens em logs.
    
- Valide o caminho do arquivo local antes de abri-lo.
    
- Considere o tamanho do arquivo e os limites aceitos pelo servidor.
    
- Não confie apenas na extensão ou no MIME type declarado para determinar o tipo real do arquivo.
    
- Evite enviar arquivos sensíveis para serviços de teste públicos.
    
- Valide o status HTTP e, quando necessário, o corpo da resposta para confirmar o resultado da operação.
    
- Não repita uploads automaticamente sem avaliar o risco de duplicação.
    
- Consulte a documentação da API para conhecer os nomes dos campos, formatos aceitos, autenticação e limites.
    

## 13. Referência rápida

| Elemento                         | Finalidade                                   |
| -------------------------------- | -------------------------------------------- |
| `data=`                          | Enviar campos de formulário                  |
| `json=`                          | Serializar e enviar dados JSON               |
| `files=`                         | Enviar arquivos em `multipart/form-data`     |
| `"rb"`                           | Abrir um arquivo para leitura binária        |
| `timeout=30`                     | Configurar o tempo de espera                 |
| `response.raise_for_status()`    | Detectar status HTTP de erro                 |
| `requests.Session()`             | Reutilizar configurações, cookies e conexões |
| `headers={"Authorization": ...}` | Enviar um cabeçalho de autorização           |
| `os.environ.get()`               | Obter uma variável de ambiente               |
| `with open(...)`                 | Garantir o fechamento do arquivo             |

---

# 📜 Requests — Download de Dados, Codificação e Compressão HTTP

## 1. Entendendo os dados recebidos por HTTP

Quando o Requests recebe uma resposta HTTP, o corpo da resposta pode conter diferentes tipos de dados:

- Texto HTML de uma página web.
    
- Dados estruturados em JSON.
    
- Imagens e documentos binários.
    
- Conteúdo comprimido durante a transferência.
    
- Dados que precisam ser interpretados com uma codificação específica.
    

O Requests disponibiliza diferentes formas de acessar o corpo da resposta. A escolha depende do tipo de conteúdo e do que o programa precisa fazer com ele.

```
import requests

response = requests.get(
    "https://httpbin.org/json",
    timeout=15,
)

response.raise_for_status()

print(response.status_code)
print(response.headers.get("Content-Type"))
print(response.text)
```

### Explicação

- `requests.get(...)`: envia uma requisição HTTP GET.
    
- `timeout=15`: define os limites de espera da conexão e da leitura.
    
- `response.raise_for_status()`: gera uma exceção se o status HTTP indicar erro.
    
- `response.status_code`: retorna o código de status da resposta.
    
- `response.headers.get("Content-Type")`: consulta o tipo de conteúdo informado pelo servidor.
    
- `response.text`: disponibiliza o corpo como texto, utilizando a codificação selecionada pelo Requests.
    

O exemplo usa um endpoint público de demonstração. Em aplicações reais, a URL e o conteúdo recebido dependem do serviço consultado.

## 2. Diferença entre `response.content` e `response.text`

Essas propriedades acessam o corpo da resposta de maneiras diferentes.

### `response.content`

Retorna o conteúdo como bytes.

```
conteudo = response.content

print(type(conteudo))
```

A função `type()` permite verificar o tipo do objeto. Nesse caso, o resultado será `bytes`.

Bytes são adequados para dados que precisam ser preservados em sua forma binária, como imagens, PDFs e arquivos compactados.

### `response.text`

Retorna o conteúdo como uma string Python.

```
texto = response.text

print(type(texto))
```

O resultado será `str`.

Para transformar bytes em texto, é necessário utilizar uma codificação de caracteres. O Requests tenta selecionar uma codificação a partir das informações disponíveis na resposta, incluindo seus cabeçalhos.

### Comparação

|Propriedade|Tipo retornado|Uso comum|
|---|---|---|
|`response.content`|`bytes`|Arquivos binários e conteúdo bruto|
|`response.text`|`str`|HTML, texto e outros conteúdos textuais|
|`response.json()`|Objeto Python|Corpos JSON válidos|

A propriedade `response.json()` interpreta o corpo como JSON. Ela não garante que o servidor retornou um status de sucesso; para isso, verifique também `response.status_code` ou utilize `response.raise_for_status()`.

## 3. O que é codificação de caracteres?

Codificação de caracteres é a forma como caracteres textuais são representados em bytes.

O UTF-8 é uma codificação amplamente utilizada na web.

Considere este exemplo:

```
texto = "Olá, mundo!"

conteudo = texto.encode("utf-8")

print(conteudo)
```

### Explicação

- `texto` contém uma string Python.
    
- `.encode("utf-8")` transforma a string em bytes usando UTF-8.
    
- `conteudo` passa a armazenar um objeto `bytes`.
    

A operação inversa é realizada com `.decode()`:

```
texto_recuperado = conteudo.decode("utf-8")

print(texto_recuperado)
```

O método `.decode("utf-8")` interpreta os bytes como texto codificado em UTF-8.

Se os bytes não forem válidos nessa codificação, a operação poderá gerar `UnicodeDecodeError`.

## 4. Consultando a codificação utilizada pelo Requests

O atributo `response.encoding` informa a codificação que o Requests utiliza para transformar o corpo em texto por meio de `response.text`.

```
import requests

response = requests.get(
    "https://example.com",
    timeout=15,
)

response.raise_for_status()

print(response.encoding)
```

O valor pode ser uma string, como `"utf-8"`, ou `None`, dependendo das informações disponíveis.

### Definindo uma codificação manualmente

Quando você conhece a codificação correta do conteúdo, pode defini-la explicitamente:

```
response.encoding = "utf-8"

texto = response.text
```

A atribuição informa ao Requests qual codificação deve utilizar ao produzir `response.text`.

**Atenção:** não defina uma codificação aleatoriamente para tentar corrigir caracteres incorretos. A codificação escolhida precisa corresponder aos bytes recebidos. Caso contrário, o texto poderá continuar incorreto.

Se precisar examinar os bytes originais, utilize `response.content`.

## 5. O que é compressão HTTP?

Servidores podem comprimir o corpo de uma resposta para reduzir a quantidade de dados transferidos pela rede.

Algoritmos comuns incluem:

- `gzip`
    
- `deflate`
    
- `br`, quando há suporte apropriado no cliente
    

A negociação de compressão normalmente utiliza o cabeçalho `Accept-Encoding`.

Um cliente pode indicar os formatos que aceita:

```
Accept-Encoding: gzip, deflate
```

O servidor pode responder com um cabeçalho como:

```
Content-Encoding: gzip
```

O primeiro cabeçalho informa os formatos aceitos pelo cliente. O segundo indica a codificação de conteúdo aplicada à resposta.

A compressão HTTP é diferente da codificação de caracteres: a primeira reduz ou transforma a representação do conteúdo durante a transferência; a segunda define como interpretar bytes como texto.

## 6. Como o Requests trata a compressão?

Em condições normais, o Requests negocia formatos de compressão suportados e descomprime automaticamente o conteúdo quando necessário.

Por isso, normalmente você pode acessar o corpo da resposta diretamente:

```
import requests

response = requests.get(
    "https://httpbin.org/gzip",
    timeout=15,
)

response.raise_for_status()

print(response.json())
```

O endpoint de demonstração retorna uma resposta relacionada a gzip. O Requests cuida da descompressão HTTP necessária para que o corpo possa ser interpretado como JSON.

Na maioria dos casos, você não precisa executar manualmente uma função de descompressão para ler o corpo da resposta.

### Atenção à diferença entre tamanho transferido e tamanho descomprimido

O tamanho do conteúdo após a descompressão pode ser diferente do tamanho transferido pela rede.

Consequentemente:

- `Content-Length`, quando presente, refere-se ao tamanho do corpo da mensagem conforme transmitido na representação HTTP correspondente, que pode estar comprimida.
    
- `len(response.content)` mede os bytes disponibilizados após o processamento normal feito pelo Requests, incluindo a descompressão aplicável.
    
- Os dois valores não precisam coincidir quando há compressão.
    

Não utilize essa comparação isoladamente para determinar se uma transferência está completa ou se um arquivo é íntegro.

## 7. Inspecionando os cabeçalhos de compressão

Podemos consultar os cabeçalhos da resposta para entender como o conteúdo foi enviado.

```
import requests

response = requests.get(
    "https://httpbin.org/gzip",
    timeout=15,
)

response.raise_for_status()

print(response.headers.get("Content-Encoding"))
print(response.headers.get("Content-Length"))
```

### Explicação

- `response.headers`: coleção de cabeçalhos da resposta.
    
- `.get("Content-Encoding")`: consulta o algoritmo de codificação do conteúdo, se informado.
    
- `.get("Content-Length")`: consulta o tamanho informado para o corpo, se disponível.
    

A ausência de `Content-Encoding` não significa que ocorreu um erro. O servidor pode ter enviado o conteúdo sem compressão ou utilizado outra forma de transferência.

Da mesma maneira, `Content-Length` pode não estar presente em todas as respostas.

## 8. Quando é necessário acessar os bytes originais da transferência?

Em aplicações comuns, `response.content` é suficiente para obter o conteúdo processado pelo Requests.

Porém, existem situações específicas em que é necessário inspecionar os dados antes da descompressão HTTP, por exemplo, ao investigar detalhes de uma transferência ou implementar um processamento de baixo nível.

Nesses casos, é necessário compreender a diferença entre:

- O corpo da resposta disponibilizado pela interface de alto nível do Requests.
    
- Os bytes transmitidos pela camada HTTP.
    
- Os dados descomprimidos.
    
- O texto interpretado por uma codificação de caracteres.
    

O streaming com `response.iter_content()` normalmente fornece blocos já descomprimidos quando a descompressão HTTP automática está ativa. Portanto, não presuma que esses blocos representam exatamente os bytes comprimidos transmitidos pela rede.

O acesso de baixo nível pode depender de `response.raw` e das opções de leitura da resposta. Como isso envolve detalhes de transporte, não é recomendável utilizá-lo sem uma necessidade específica.

## 9. Validando conteúdo antes de processá-lo

O cabeçalho `Content-Type` indica o tipo de conteúdo declarado pelo servidor.

Exemplos:

|Cabeçalho|Significado|
|---|---|
|`text/html`|Documento HTML|
|`application/json`|JSON|
|`application/pdf`|Documento PDF|
|`image/png`|Imagem PNG|
|`application/octet-stream`|Dados binários genéricos|

Exemplo de inspeção:

```
import requests

response = requests.get(
    "https://example.com",
    timeout=15,
)

response.raise_for_status()

content_type = response.headers.get("Content-Type", "")

if "text/html" in content_type.lower():
    print("A resposta declara conteúdo HTML.")
else:
    print(f"Tipo declarado: {content_type or 'não informado'}")
```

### Explicação

`**response.headers.get("Content-Type", "")**`

Consulta o cabeçalho e retorna uma string vazia se ele estiver ausente.

`**.lower()**`

Converte a string para letras minúsculas, permitindo fazer a comparação sem diferenciar maiúsculas e minúsculas.

`**if "text/html" in ...**`

Verifica se a indicação `text/html` aparece no valor do cabeçalho.

Essa verificação é apenas uma inspeção inicial. O cabeçalho não prova que o conteúdo realmente seja HTML, pois a resposta pode estar malformada ou ter sido declarada incorretamente.

## 10. Erros comuns

### Caracteres acentuados aparecem incorretamente

**Possível causa:** a codificação utilizada para interpretar o corpo não corresponde à codificação real dos bytes.

**Como investigar:**

```
print(response.encoding)
print(response.headers.get("Content-Type"))
```

Confira a codificação declarada e, quando necessário, consulte a documentação do serviço.

### A resposta não pode ser interpretada como JSON

**Possível causa:** o servidor retornou HTML, texto simples, conteúdo vazio ou JSON inválido.

**Como investigar:**

```
print(response.status_code)
print(response.headers.get("Content-Type"))
print(response.text[:500])
```

O recorte dos primeiros 500 caracteres ajuda a inspecionar uma resposta textual. Evite imprimir conteúdo sensível, tokens, dados pessoais ou respostas de autenticação nos logs.

### `Content-Length` não coincide com o tamanho do conteúdo

**Possível causa:** a resposta passou por descompressão automática ou o cabeçalho não está disponível ou não representa o tamanho esperado para a comparação realizada.

**Como investigar:** entenda em qual etapa o tamanho está sendo medido e não utilize apenas essa diferença para concluir que houve corrupção.

### O arquivo baixado não abre

**Possíveis causas:**

- A URL retornou uma página de erro.
    
- O corpo não é do tipo de arquivo esperado.
    
- A transferência foi interrompida.
    
- O arquivo já estava inválido no servidor.
    

**Como investigar:** verifique o status HTTP, os cabeçalhos, o tamanho e, se possível, a integridade por hash ou por um validador específico do formato.

## 11. Boas práticas

- Utilize `response.content` para trabalhar com bytes e `response.text` para trabalhar com texto.
    
- Utilize `response.json()` quando o corpo tiver JSON válido.
    
- Verifique o status HTTP antes de processar uma resposta como se fosse válida.
    
- Consulte `Content-Type` e `Content-Encoding` quando precisar entender o conteúdo recebido.
    
- Não confunda compressão HTTP com codificação de caracteres.
    
- Não suponha que `Content-Length` sempre esteja presente.
    
- Não confie exclusivamente nos cabeçalhos para validar o tipo real de um arquivo.
    
- Prefira streaming para arquivos grandes, conforme explicado na anotação sobre downloads.
    
- Evite registrar corpos de resposta completos quando puderem conter informações sensíveis.
    

## 12. Referência rápida

| Elemento                      | Finalidade                                                      |
| ----------------------------- | --------------------------------------------------------------- |
| `response.content`            | Acessar o corpo como bytes                                      |
| `response.text`               | Acessar o corpo como texto                                      |
| `response.json()`             | Interpretar JSON e retornar objetos Python                      |
| `response.encoding`           | Consultar ou definir a codificação utilizada em `response.text` |
| `.encode("utf-8")`            | Converter texto em bytes                                        |
| `.decode("utf-8")`            | Interpretar bytes como texto                                    |
| `Content-Type`                | Declarar o tipo de mídia da resposta                            |
| `Content-Encoding`            | Indicar a codificação aplicada ao conteúdo, como gzip           |
| `Content-Length`              | Informar o tamanho do corpo, quando disponível                  |
| `response.raise_for_status()` | Gerar exceção para status HTTP de erro                          |

---

# 📜 Requests — Redirecionamentos HTTP e Histórico de Requisições

## 1. O que são redirecionamentos HTTP?

Um redirecionamento HTTP acontece quando um servidor responde indicando que o cliente deve buscar um recurso em outro endereço.

Isso é comum quando:

- Uma página mudou de URL.
    
- Um site redireciona HTTP para HTTPS.
    
- Uma rota antiga aponta para uma nova.
    
- Um sistema direciona o usuário para uma página de autenticação.
    
- Uma aplicação encaminha uma requisição para outro endpoint.
    

Os redirecionamentos são identificados principalmente pelos códigos de status HTTP da família `3xx`.

Exemplo simplificado:

```
Cliente Python
     |
     | GET /pagina-antiga
     v
Servidor
     |
     | 302 Found
     | Location: /pagina-nova
     v
Cliente Python
     |
     | GET /pagina-nova
     v
Servidor
     |
     | 200 OK
     v
Resposta final
```

O cabeçalho `Location` indica o endereço para o qual o cliente deve ser direcionado.

Por padrão, métodos como `requests.get()` seguem redirecionamentos automaticamente, dentro dos limites configurados pelo Requests.

## 2. Fazendo uma requisição que segue redirecionamentos

```
import requests

url = "http://httpbin.org/redirect/2"

response = requests.get(
    url,
    timeout=15,
)

print(response.status_code)
print(response.url)
print(response.history)
```

### Explicação linha por linha

**Importar a biblioteca**

```
import requests
```

Importa o Requests para realizar requisições HTTP.

**Definir a URL**

```
url = "http://httpbin.org/redirect/2"
```

Define um endpoint de demonstração que realiza dois redirecionamentos.

**Enviar a requisição**

```
response = requests.get(
    url,
    timeout=15,
)
```

- `requests.get()` envia uma requisição GET.
    
- `url` é o endereço inicial.
    
- `timeout=15` define os limites de espera da conexão e da leitura.
    
- O Requests segue redirecionamentos automaticamente por padrão para GET.
    

**Consultar o status final**

```
print(response.status_code)
```

Exibe o código de status da resposta final recebida. No exemplo, espera-se normalmente `200`.

**Consultar a URL final**

```
print(response.url)
```

Exibe a URL associada à resposta final após os redirecionamentos.

**Consultar o histórico**

```
print(response.history)
```

Retorna uma lista de objetos `Response` correspondentes às respostas intermediárias dos redirecionamentos.

Cada objeto permite examinar o status, os cabeçalhos e a URL daquela etapa.

## 3. A propriedade `response.history`

A propriedade `history` é útil quando precisamos entender o caminho percorrido por uma requisição.

```
for etapa in response.history:
    print(etapa.status_code, etapa.url)
```

### Explicação

- `response.history`: lista das respostas intermediárias.
    
- `for etapa in ...`: percorre cada resposta intermediária.
    
- `etapa.status_code`: consulta o status daquela etapa.
    
- `etapa.url`: consulta a URL associada à resposta.
    

A variável `etapa` é apenas um nome escolhido para representar cada objeto `Response` do histórico.

Para também visualizar a resposta final:

```
print("Status final:", response.status_code)
print("URL final:", response.url)
```

O histórico não substitui o status final. Uma cadeia de redirecionamentos pode terminar em sucesso, erro HTTP ou outra resposta que precise ser tratada.

## 4. Desativando os redirecionamentos automáticos

Em algumas situações, queremos analisar a primeira resposta do servidor sem seguir o destino indicado.

Para isso, usamos `allow_redirects=False`.

```
import requests

response = requests.get(
    "http://httpbin.org/redirect/1",
    allow_redirects=False,
    timeout=15,
)

print(response.status_code)
print(response.headers.get("Location"))
```

### O que muda?

`**allow_redirects=False**`

Desativa o acompanhamento automático dos redirecionamentos nessa requisição.

Assim, o objeto `response` representa a resposta inicial do servidor, permitindo inspecionar seu status e o cabeçalho `Location`.

O valor de `Location` pode ser uma URL absoluta ou um caminho relativo.

Quando o redirecionamento não é seguido, `response.history` normalmente fica vazio, porque não houve uma sequência automática de respostas intermediárias.

## 5. Entendendo os códigos de redirecionamento

Nem todos os códigos `3xx` possuem exatamente o mesmo significado.

|Código|Nome|Significado geral|
|---|---|---|
|`301`|Moved Permanently|O recurso foi movido permanentemente|
|`302`|Found|Redirecionamento temporário|
|`303`|See Other|Indica buscar outro recurso, normalmente com GET|
|`304`|Not Modified|Indica que a representação não mudou em uma requisição condicional|
|`307`|Temporary Redirect|Redirecionamento temporário que preserva o método|
|`308`|Permanent Redirect|Redirecionamento permanente que preserva o método|

O código `304` é uma resposta especial associada a requisições condicionais e cache. Ele não deve ser tratado como um redirecionamento comum para outra URL.

### Por que `307` e `308` são importantes?

Esses códigos indicam que o cliente deve preservar o método HTTP utilizado na requisição original.

Por exemplo, se a requisição original for POST, o redirecionamento não deve transformá-la em GET.

Em contraste, comportamentos históricos associados a `301` e `302` podem levar clientes a transformar POST em GET. O código `303` indica explicitamente a obtenção de outro recurso, normalmente usando GET.

O comportamento concreto também depende do cliente HTTP e da forma como ele implementa o protocolo.

## 6. Inspecionando os cabeçalhos de uma resposta de redirecionamento

Os cabeçalhos fornecem informações sobre a resposta e o destino indicado pelo servidor.

```
import requests

response = requests.get(
    "http://httpbin.org/redirect/1",
    allow_redirects=False,
    timeout=15,
)

print("Status:", response.status_code)
print("Location:", response.headers.get("Location"))
print("Content-Type:", response.headers.get("Content-Type"))
```

### Elementos utilizados

- `response.status_code`: código HTTP da resposta inicial.
    
- `response.headers`: coleção de cabeçalhos.
    
- `.get("Location")`: consulta o destino indicado pelo servidor.
    
- `.get("Content-Type")`: consulta o tipo de conteúdo declarado na resposta.
    

Se um cabeçalho não existir, `.get()` retorna `None`, a menos que outro valor padrão seja especificado.

Por exemplo:

```
destino = response.headers.get("Location", "sem destino informado")
```

Nesse caso, `destino` receberá o valor do cabeçalho ou o texto alternativo caso ele esteja ausente.

## 7. Resolvendo URLs relativas com `urljoin()`

O cabeçalho `Location` pode conter apenas um caminho, como `/nova-pagina`, em vez de uma URL completa.

Para combinar um endereço base com um destino relativo, utilizamos `urljoin()` da biblioteca padrão `urllib.parse`.

```
from urllib.parse import urljoin

base = "https://example.com/pagina-antiga"
destino = "/nova-pagina"

url_final = urljoin(base, destino)

print(url_final)
```

Resultado esperado:

```
https://example.com/nova-pagina
```

### Explicação

**Importar** `**urljoin()**`

```
from urllib.parse import urljoin
```

Importa a função responsável por combinar uma URL base com outra referência.

**Definir o endereço base**

```
base = "https://example.com/pagina-antiga"
```

Representa a URL a partir da qual a referência relativa será resolvida.

**Definir o destino**

```
destino = "/nova-pagina"
```

É um caminho relativo à raiz do domínio.

**Combinar os valores**

```
url_final = urljoin(base, destino)
```

Gera a URL resultante.

**Atenção:** `urljoin()` também aceita destinos absolutos. Se o valor recebido vier de uma fonte não confiável, ele poderá apontar para outro domínio. Antes de seguir manualmente um destino, valide o esquema e o domínio permitidos.

## 8. Acompanhando redirecionamentos manualmente

Desativar o acompanhamento automático permite analisar cada resposta individualmente. Também é possível implementar um fluxo manual quando a aplicação precisa validar os destinos.

O exemplo abaixo limita o número de etapas e aceita apenas destinos HTTPS no mesmo domínio do endereço inicial.

```
from urllib.parse import urljoin, urlparse

import requests

url_atual = "https://httpbin.org/redirect/1"
dominio_permitido = urlparse(url_atual).hostname
max_redirecionamentos = 5

with requests.Session() as session:
    for etapa in range(max_redirecionamentos + 1):
        response = session.get(
            url_atual,
            allow_redirects=False,
            timeout=15,
        )

        if response.status_code not in (301, 302, 303, 307, 308):
            response.raise_for_status()
            print("URL final:", response.url)
            print("Status:", response.status_code)
            break

        location = response.headers.get("Location")

        if not location:
            raise RuntimeError("Redirecionamento sem cabeçalho Location.")

        proxima_url = urljoin(url_atual, location)
        partes = urlparse(proxima_url)

        if partes.scheme != "https":
            raise RuntimeError("Redirecionamento fora do esquema HTTPS.")

        if partes.hostname != dominio_permitido:
            raise RuntimeError("Redirecionamento para domínio não permitido.")

        url_atual = proxima_url
    else:
        raise RuntimeError("Limite de redirecionamentos excedido.")
```

### Explicação das partes principais

`**urlparse()**`

Separa uma URL em componentes, como esquema, hostname, caminho e parâmetros.

`**dominio_permitido**`

Guarda o hostname inicial. No exemplo, o código permite apenas redirecionamentos para o mesmo hostname.

`**max_redirecionamentos**`

Define o limite de etapas que o programa pode acompanhar.

`**allow_redirects=False**`

Permite que o programa valide cada destino antes de fazer a próxima requisição.

`**response.status_code not in (...)**`

Verifica se a resposta não pertence aos códigos de redirecionamento selecionados. Caso não pertença, o programa trata a resposta como resultado final desse fluxo.

`**response.raise_for_status()**`

Gera uma exceção se o status final indicar erro HTTP.

`**urljoin(url_atual, location)**`

Resolve o destino informado pelo servidor, inclusive quando ele é relativo.

**Validação do esquema e hostname**

O código só aceita HTTPS e o hostname definido inicialmente. Essa verificação é uma proteção básica, não uma política completa contra todas as formas de SSRF (_Server-Side Request Forgery_).

Em aplicações que acessam URLs controladas por usuários, também podem ser necessárias restrições de IP, DNS, portas, destinos internos e mudanças de endereço durante a resolução.

O exemplo utiliza GET em todas as etapas. Fluxos com outros métodos exigem atenção especial às regras de preservação do método e do corpo.

## 9. Redirecionamentos e autenticação

Redirecionamentos podem interferir no comportamento da autenticação.

Considere um cliente que envia:

```
headers = {
    "Authorization": "Bearer TOKEN_DE_EXEMPLO",
}
```

Se o servidor redirecionar a requisição para outro endereço, o cliente precisa tratar as credenciais de forma segura.

O Requests possui regras para lidar com cabeçalhos de autenticação durante redirecionamentos, mas não se deve presumir que qualquer redirecionamento será seguro para qualquer tipo de credencial.

Boas práticas:

- Prefira HTTPS desde a URL inicial.
    
- Não siga automaticamente destinos desconhecidos quando a requisição contém credenciais sensíveis.
    
- Verifique o domínio de destino quando a aplicação exige que as requisições permaneçam em um serviço específico.
    
- Evite inserir tokens diretamente em URLs.
    
- Não registre cabeçalhos de autenticação em logs.
    
- Consulte as regras de autenticação e redirecionamento da API utilizada.
    

## 10. Redirecionamentos não são o mesmo que uma sessão autenticada

Uma resposta `302` que leva a uma página de login não significa que a autenticação foi concluída.

Da mesma forma, uma resposta `200 OK` após seguir um redirecionamento não comprova que o usuário está autenticado: a resposta pode ser apenas o HTML da página de login.

Em aplicações web, verifique sinais específicos do fluxo de autenticação, como:

- Cookies esperados.
    
- Conteúdo da página final.
    
- Dados retornados pela API.
    
- Código de estado ou resposta documentada pelo serviço.
    

O comportamento depende da aplicação. Não existe um único indicador universal de autenticação válida para todos os sites.

## 11. Erros e limitações comuns

### O histórico está vazio

**Possível causa:** nenhum redirecionamento automático ocorreu ou a requisição foi realizada com `allow_redirects=False`.

**Como verificar:**

```
print(response.url)
print(response.history)
```

### A URL final é diferente da URL inicial

Isso pode ser normal quando o servidor redireciona para outro endereço.

Inspecione o histórico e os códigos de status para identificar as etapas.

### O redirecionamento aponta para outro domínio

Pode ser legítimo, mas também pode ser inesperado. Quando o domínio de destino importa para a segurança, valide-o antes de continuar.

### O servidor entra em um ciclo de redirecionamentos

Um servidor pode direcionar para uma URL que leva novamente à anterior. O Requests limita o acompanhamento automático e pode gerar `TooManyRedirects`.

Se estiver implementando acompanhamento manual, estabeleça um limite explícito de etapas.

### A resposta final possui status `4xx` ou `5xx`

O redirecionamento pode ter ocorrido corretamente, mas o destino final pode ter falhado. Utilize `raise_for_status()` para tratar esses casos.

## 12. Referência rápida

|Elemento|Finalidade|
|---|---|
|`allow_redirects=False`|Desativar redirecionamentos automáticos|
|`response.history`|Consultar respostas intermediárias|
|`response.url`|Consultar a URL associada à resposta final|
|`response.status_code`|Consultar o status HTTP|
|`response.headers.get("Location")`|Obter o destino indicado pelo servidor|
|`urljoin(base, destino)`|Resolver um destino relativo|
|`urlparse(url)`|Separar componentes de uma URL|
|`TooManyRedirects`|Indicar que o limite de redirecionamentos foi excedido|
|`response.raise_for_status()`|Gerar exceção para status HTTP de erro|
|`timeout=15`|Definir limites de espera para a requisição|

---
# 📜 Requests — Cache HTTP, ETag e Requisições Condicionais

## 1. O que é cache HTTP?

O cache HTTP é um mecanismo que permite reutilizar respostas anteriores quando elas ainda podem ser utilizadas.

Imagine um programa que consulta a mesma API repetidamente. Se o conteúdo não mudou, talvez não seja necessário transferir novamente toda a resposta.

O cache pode ajudar a:

- Reduzir o volume de dados transferidos.
    
- Diminuir a latência de determinadas requisições.
    
- Reduzir o trabalho de servidores e clientes.
    
- Evitar downloads desnecessários.
    
- Melhorar a eficiência de programas que consultam recursos repetidamente.
    

O cache pode existir no navegador, em um proxy, em uma CDN, no servidor ou na própria aplicação Python.

**Importante:** o Requests não implementa automaticamente um sistema completo de cache persistente para todas as respostas. Ele fornece acesso aos cabeçalhos HTTP necessários para que a aplicação implemente ou utilize uma estratégia de cache.

## 2. Como funciona o cache HTTP?

Um servidor pode informar por quanto tempo uma resposta pode ser reutilizada sem precisar ser revalidada.

Por exemplo:

```
Cache-Control: max-age=300
```

Esse cabeçalho informa que a resposta pode ser considerada fresca por até 300 segundos, ou cinco minutos, segundo as regras aplicáveis ao cache.

Fluxo simplificado:

```
Aplicação Python
       |
       | GET /dados
       v
Servidor HTTP
       |
       | 200 OK
       | Cache-Control: max-age=300
       v
Aplicação recebe os dados
       |
       | Guarda a resposta em cache
       v
Nova consulta dentro do prazo
       |
       | Reutiliza os dados em cache
       v
Evita uma nova transferência
```

Esse fluxo é conceitual. Para reutilizar a resposta no próprio programa, é necessário implementar o armazenamento em cache ou utilizar uma biblioteca que ofereça essa funcionalidade.

## 3. Inspecionando cabeçalhos de cache com Requests

Podemos consultar os cabeçalhos de uma resposta HTTP para entender as instruções de cache fornecidas pelo servidor.

```
import requests

url = "https://httpbin.org/cache/60"

response = requests.get(
    url,
    timeout=15,
)

response.raise_for_status()

print(response.status_code)
print(response.headers.get("Cache-Control"))
print(response.headers.get("Expires"))
print(response.headers.get("Date"))
```

### Explicação linha por linha

**Importar Requests**

```
import requests
```

Importa a biblioteca para realizar a requisição HTTP.

**Definir a URL**

```
url = "https://httpbin.org/cache/60"
```

Define um endpoint de demonstração relacionado a cache. O comportamento e os cabeçalhos recebidos dependem da resposta efetivamente fornecida pelo serviço.

**Enviar a requisição**

```
response = requests.get(url, timeout=15)
```

- `requests.get()` envia uma requisição GET.
    
- `url` indica o recurso.
    
- `timeout=15` define os limites de espera da conexão e da leitura.
    

**Validar o status HTTP**

```
response.raise_for_status()
```

Gera uma exceção quando a resposta indica um erro HTTP `4xx` ou `5xx`.

**Consultar os cabeçalhos**

```
response.headers.get("Cache-Control")
```

Consulta as instruções de cache fornecidas pelo servidor.

Os outros cabeçalhos consultados são:

- `Expires`: pode indicar uma data e hora de expiração.
    
- `Date`: indica a data e hora associadas à mensagem HTTP.
    

Esses cabeçalhos não precisam estar presentes em todas as respostas.

## 4. O cabeçalho `Cache-Control`

`Cache-Control` é um dos principais cabeçalhos utilizados para controlar o comportamento do cache HTTP.

Algumas diretivas importantes:

|Diretiva|Significado geral|
|---|---|
|`max-age=300`|A resposta pode permanecer fresca por até 300 segundos|
|`no-cache`|A resposta armazenada precisa ser revalidada antes de ser reutilizada|
|`no-store`|O cache não deve armazenar a resposta|
|`private`|A resposta é destinada a cache privado, como o do cliente|
|`public`|A resposta pode ser armazenada por caches compartilhados, respeitadas as regras aplicáveis|
|`must-revalidate`|Uma resposta vencida precisa ser revalidada antes de ser reutilizada|

### Atenção à diferença entre `no-cache` e `no-store`

Essas diretivas não significam a mesma coisa.

- `no-cache` não proíbe necessariamente o armazenamento. Ele exige revalidação antes da reutilização da resposta armazenada.
    
- `no-store` instrui os caches a não armazenarem a resposta.
    

Essas diretivas não substituem uma política completa de segurança para dados sensíveis. A aplicação deve considerar também logs, armazenamento local, permissões e o ciclo de vida dos dados.

## 5. O que é ETag?

`ETag` é um validador associado a uma representação de um recurso HTTP.

O servidor pode retornar um cabeçalho semelhante a:

```
ETag: "versao-abc123"
```

O valor identifica uma versão específica da representação. Ele pode ser baseado em um hash, em uma versão interna ou em outro mecanismo implementado pelo servidor.

A ETag não precisa ser um hash criptográfico e não deve ser tratada automaticamente como prova de integridade ou autenticidade.

Quando a representação muda, o servidor pode fornecer uma ETag diferente.

Isso permite que o cliente pergunte se a representação ainda corresponde à versão que já possui.

## 6. O cabeçalho `If-None-Match`

Depois de receber uma ETag, o cliente pode enviá-la em uma requisição posterior usando `If-None-Match`.

Exemplo:

```
If-None-Match: "versao-abc123"
```

O significado é, de forma simplificada: “envie a representação se ela não corresponder a este validador”.

Se a representação não tiver mudado e as condições HTTP forem atendidas, o servidor poderá responder:

```
304 Not Modified
```

Nesse caso, o servidor informa que a representação não precisa ser transferida novamente para aquela requisição condicional.

O cliente pode reutilizar a representação que já possui, respeitando as regras de cache HTTP.

## 7. Implementando uma requisição condicional com Requests

O exemplo a seguir mostra como enviar manualmente uma ETag em uma segunda requisição.

```
import requests

url = "https://example.com/recurso"

primeira = requests.get(
    url,
    timeout=15,
)

primeira.raise_for_status()

etag = primeira.headers.get("ETag")

if etag:
    segunda = requests.get(
        url,
        headers={"If-None-Match": etag},
        timeout=15,
    )

    if segunda.status_code == 304:
        print("A representação continua válida.")
    else:
        segunda.raise_for_status()
        print("A representação foi atualizada.")
        print(segunda.text)
else:
    print("O servidor não forneceu uma ETag.")
```

### Explicação das etapas

**Primeira requisição**

```
primeira = requests.get(url, timeout=15)
```

Obtém a resposta inicial do recurso.

**Validar a resposta**

```
primeira.raise_for_status()
```

Verifica se a resposta inicial indica um erro HTTP.

**Obter a ETag**

```
etag = primeira.headers.get("ETag")
```

Obtém o valor do cabeçalho `ETag`, caso esteja presente.

**Enviar** `**If-None-Match**`

```
headers={"If-None-Match": etag}
```

Solicita que o servidor compare a representação atual com o validador fornecido.

**Tratar** `**304**`

```
if segunda.status_code == 304:
```

Verifica se o servidor indicou que a representação não foi modificada para a requisição condicional.

O status `304` não é um erro HTTP. Por isso, não se deve esperar que `raise_for_status()` o trate como falha.

**Limitação do exemplo:** o código demonstra a validação condicional, mas não implementa um cache completo. Para reutilizar o conteúdo anterior, a aplicação precisa guardar a representação inicial e associá-la à ETag, além de respeitar os cabeçalhos e as regras de cache pertinentes.

## 8. O cabeçalho `Last-Modified`

Alguns servidores fornecem `Last-Modified` para indicar quando a representação foi modificada pela última vez.

Exemplo:

```
Last-Modified: Tue, 13 Oct 2026 12:00:00 GMT
```

O cliente pode usar esse valor em uma requisição posterior:

```
If-Modified-Since: Tue, 13 Oct 2026 12:00:00 GMT
```

Se o recurso não tiver sido modificado desde a data informada, o servidor poderá responder com `304 Not Modified`.

### Exemplo com Requests

```
import requests

url = "https://example.com/recurso"

primeira = requests.get(
    url,
    timeout=15,
)

primeira.raise_for_status()

ultima_modificacao = primeira.headers.get("Last-Modified")

if ultima_modificacao:
    segunda = requests.get(
        url,
        headers={"If-Modified-Since": ultima_modificacao},
        timeout=15,
    )

    if segunda.status_code == 304:
        print("O recurso não foi modificado.")
    else:
        segunda.raise_for_status()
        print("Resposta atualizada recebida.")
```

O servidor pode não fornecer `Last-Modified`, e a precisão desse mecanismo depende da implementação e das regras HTTP aplicáveis.

Quando ETag e data de modificação são utilizados juntos, as regras de precedência dos validadores HTTP devem ser respeitadas. Não presuma que todos os servidores suportam ambos.

## 9. ETag forte e ETag fraca

Existem duas formas comuns de ETag.

Uma ETag forte:

```
ETag: "abc123"
```

Uma ETag fraca:

```
ETag: W/"abc123"
```

O prefixo `W/` indica uma ETag fraca.

Em termos gerais:

- Uma ETag forte identifica uma representação com critérios mais rigorosos de equivalência.
    
- Uma ETag fraca indica equivalência semântica, permitindo diferenças que não alterem o significado relevante da representação.
    

Essa distinção importa porque os validadores podem ser comparados de maneiras diferentes dependendo do cabeçalho condicional utilizado.

Para a maioria dos programas Python que consomem APIs, o comportamento correto é preservar o valor fornecido pelo servidor e seguir as regras HTTP, em vez de tentar interpretar ou modificar manualmente a ETag.

## 10. Cache e respostas de APIs

Uma API pode utilizar cache para recursos relativamente estáveis, como:

- Listas públicas de categorias.
    
- Metadados de produtos.
    
- Informações de documentação.
    
- Arquivos estáticos.
    
- Dados públicos que não mudam frequentemente.
    

Por outro lado, respostas personalizadas, informações privadas e dados que mudam constantemente podem exigir políticas diferentes.

Não é adequado armazenar indiscriminadamente todas as respostas em cache. A decisão deve considerar:

1. Se o conteúdo pode ser reutilizado.
    
2. Por quanto tempo pode ser considerado válido.
    
3. Se a resposta depende da identidade do usuário.
    
4. Se o servidor permite armazenamento.
    
5. Se existem dados sensíveis.
    
6. Como invalidar o conteúdo quando ele muda.
    

Em particular, respostas que dependem de cookies ou de cabeçalhos de autorização exigem cuidado para que os dados de um usuário não sejam reutilizados para outro.

## 11. Por que o Requests não resolve todo o cache automaticamente?

O Requests é principalmente uma biblioteca de cliente HTTP. Ele envia requisições, recebe respostas e disponibiliza ferramentas para interpretar e manipular esses dados.

Embora possa manter conexões reutilizáveis por meio de `Session`, **reutilizar uma conexão não é a mesma coisa que reutilizar uma resposta em cache**.

Uma sessão:

```
import requests

with requests.Session() as session:
    primeira = session.get(
        "https://example.com",
        timeout=15,
    )

    segunda = session.get(
        "https://example.com",
        timeout=15,
    )
```

Pode reutilizar conexões TCP quando isso for possível, mas não significa que a segunda requisição receberá automaticamente o corpo da primeira resposta sem consultar o servidor.

Para adicionar cache de respostas, é possível implementar armazenamento e validação próprios ou utilizar uma biblioteca complementar compatível com Requests.

## 12. Erros e limitações comuns

### O servidor não fornece `ETag`

Nem todas as respostas possuem ETag. Nesse caso, o cliente pode utilizar outros mecanismos suportados pelo servidor, como `Last-Modified`, ou simplesmente realizar a requisição normal.

### O servidor retorna `304`, mas o programa não possui o conteúdo anterior

O status `304` não fornece uma nova representação completa. O cliente precisa ter a versão armazenada e saber como reutilizá-la. Sem isso, não há conteúdo anterior disponível para recuperar apenas a partir da resposta `304`.

### A resposta continua sendo consultada toda vez

Isso pode ser esperado. Uma requisição condicional ainda faz uma chamada HTTP; a economia está principalmente na possibilidade de evitar a transferência do corpo completo.

### O conteúdo em cache fica desatualizado

O programa pode ter implementado uma política incorreta de expiração ou ignorado os cabeçalhos do servidor. Utilize validadores HTTP e respeite as diretivas de cache aplicáveis.

### Dados privados são reutilizados indevidamente

Isso pode ocorrer quando o cache não diferencia usuários, sessões ou contextos de autorização. Não compartilhe respostas personalizadas entre usuários sem uma política que garanta a separação correta.

## 13. Boas práticas

- Inspecione `Cache-Control`, `ETag` e `Last-Modified` quando precisar entender a política de cache do servidor.
    
- Utilize `If-None-Match` ou `If-Modified-Since` conforme os validadores disponibilizados.
    
- Trate `304 Not Modified` separadamente de respostas de sucesso com corpo completo.
    
- Não presuma que o Requests possui cache persistente automático.
    
- Não confunda reutilização de conexões por `Session` com cache de respostas.
    
- Respeite as diretivas HTTP de armazenamento e revalidação.
    
- Não armazene dados sensíveis indiscriminadamente.
    
- Evite implementar cache próprio sem considerar expiração, invalidação, concorrência e separação de usuários.
    
- Não trate uma ETag como prova criptográfica de autenticidade ou integridade.
    

## 14. Referência rápida

|Elemento|Finalidade|
|---|---|
|`Cache-Control`|Controlar políticas de cache|
|`max-age`|Definir o tempo máximo de frescor, em segundos|
|`no-cache`|Exigir revalidação antes da reutilização|
|`no-store`|Instruir caches a não armazenar a resposta|
|`ETag`|Identificar uma versão de representação|
|`If-None-Match`|Revalidar usando uma ETag|
|`Last-Modified`|Informar quando a representação foi modificada|
|`If-Modified-Since`|Revalidar usando uma data de modificação|
|`304 Not Modified`|Indicar que a representação não precisa ser retransmitida|
|`response.headers.get()`|Consultar cabeçalhos HTTP|
|`requests.Session()`|Reutilizar configurações e conexões, não necessariamente respostas em cache|

---
# 📜 Requests — Rate Limiting, HTTP 429 e Retry-After

## 1. O que é rate limiting?

Rate limiting é um mecanismo que limita a quantidade de requisições que um cliente pode realizar durante determinado período.

Uma API pode estabelecer, por exemplo, um limite de 60 requisições por minuto para determinado usuário, token ou endereço IP.

Os limites variam conforme o serviço e podem depender de diferentes fatores:

- Identidade do usuário.
    
- Token de autenticação.
    
- Endereço IP de origem.
    
- Endpoint consultado.
    
- Plano de acesso.
    
- Volume de requisições em determinado intervalo.
    

O objetivo pode ser proteger a infraestrutura, distribuir recursos entre clientes e reduzir abusos ou sobrecarga.

**Importante:** não existe um limite universal de requisições por minuto. É necessário consultar a documentação da API utilizada.

## 2. O que significa HTTP 429?

O status `429 Too Many Requests` indica que o cliente realizou requisições em uma frequência superior à permitida pelo servidor.

Exemplo de resposta:

```
HTTP/1.1 429 Too Many Requests
Content-Type: application/json
Retry-After: 30
```

Nesse exemplo, o servidor indica que o cliente deve aguardar 30 segundos antes de tentar novamente.

O cabeçalho `Retry-After` pode informar um intervalo em segundos ou uma data HTTP. Seu significado e a política de repetição devem ser interpretados conforme o protocolo e a documentação do serviço.

Uma resposta `429` não significa necessariamente que o endereço IP foi bloqueado permanentemente. O limite pode ser temporário, vinculado à conta ou específico de um endpoint.

## 3. Identificando uma resposta 429 com Requests

Podemos verificar o status HTTP antes de decidir como tratar a resposta.

```
import requests

url = "https://api.example.com/dados"

response = requests.get(
    url,
    timeout=15,
)

if response.status_code == 429:
    print("Limite de requisições atingido.")
elif response.status_code >= 400:
    print(f"Erro HTTP: {response.status_code}")
else:
    print("Requisição concluída.")
```

### Explicação linha por linha

**Enviar a requisição**

```
response = requests.get(url, timeout=15)
```

- `requests.get()` envia uma requisição GET.
    
- `url` contém o endereço do endpoint.
    
- `timeout=15` define os limites de espera da conexão e da leitura.
    
- `response` armazena a resposta HTTP.
    

O domínio `api.example.com` é ilustrativo. Para utilizar o exemplo, substitua-o por um endpoint real e autorizado.

**Verificar o status 429**

```
if response.status_code == 429:
```

A condição compara o código HTTP recebido com `429`.

**Verificar outros erros HTTP**

```
elif response.status_code >= 400:
```

Códigos HTTP de `400` a `599` normalmente representam erros do cliente ou do servidor.

**Tratar os demais resultados**

```
else:
    print("Requisição concluída.")
```

Essa mensagem apenas indica que o status não entrou nas condições anteriores. Em uma aplicação real, valide também o status esperado e o conteúdo da resposta.

## 4. Consultando o cabeçalho `Retry-After`

O servidor pode informar quanto tempo o cliente deve esperar antes de realizar outra tentativa.

```
retry_after = response.headers.get("Retry-After")

print(retry_after)
```

### Entendendo o código

- `response.headers` contém os cabeçalhos HTTP recebidos.
    
- `.get("Retry-After")` consulta o cabeçalho.
    
- `retry_after` recebe uma string com o valor informado ou `None` se o cabeçalho não existir.
    

O valor não deve ser presumido como um número. O cabeçalho também pode conter uma data HTTP.

### Exemplo em segundos

```
Retry-After: 30
```

Nesse caso, o valor representa um intervalo de 30 segundos.

### Exemplo com data HTTP

```
Retry-After: Thu, 15 Oct 2026 17:00:00 GMT
```

Nesse formato, o servidor indica uma data e hora após a qual a nova tentativa pode ser realizada.

É importante distinguir esses dois formatos para não interpretar uma data como se fosse um número de segundos.

## 5. Convertendo `Retry-After` em tempo de espera

O exemplo a seguir aceita os dois formatos comuns do cabeçalho.

```
from datetime import datetime, timezone
from email.utils import parsedate_to_datetime


def calcular_espera(valor):
    if not valor:
        return None

    try:
        segundos = int(valor)
        return max(0, segundos)
    except ValueError:
        pass

    try:
        data_retentativa = parsedate_to_datetime(valor)

        if data_retentativa.tzinfo is None:
            data_retentativa = data_retentativa.replace(
                tzinfo=timezone.utc
            )

        agora = datetime.now(timezone.utc)

        return max(
            0,
            (data_retentativa - agora).total_seconds(),
        )

    except (TypeError, ValueError, OverflowError):
        return None
```

### Explicação

**Importar ferramentas de data e hora**

```
from datetime import datetime, timezone
from email.utils import parsedate_to_datetime
```

- `datetime` permite trabalhar com datas e horários.
    
- `timezone.utc` representa o fuso horário UTC.
    
- `parsedate_to_datetime()` interpreta datas no formato utilizado por cabeçalhos HTTP, quando válidas.
    

**Definir a função**

```
def calcular_espera(valor):
```

Cria uma função que recebe o valor de `Retry-After` e tenta convertê-lo em uma quantidade de segundos.

**Verificar se o cabeçalho existe**

```
if not valor:
    return None
```

Se o valor estiver ausente ou vazio, a função retorna `None`, indicando que não conseguiu calcular uma espera a partir do cabeçalho.

**Tentar interpretar um número**

```
segundos = int(valor)
return max(0, segundos)
```

Se o valor puder ser convertido para inteiro, a função o interpreta como segundos.

`max(0, segundos)` impede que um número negativo resulte em uma espera negativa.

**Interpretar uma data**

```
data_retentativa = parsedate_to_datetime(valor)
```

Se a conversão numérica falhar, a função tenta interpretar o valor como uma data HTTP.

**Calcular a diferença**

```
(data_retentativa - agora).total_seconds()
```

Subtrai a data atual da data informada pelo servidor e transforma a diferença em segundos.

O resultado é limitado a zero ou mais.

**Tratar valores inválidos**

Se a string não representar um número válido nem uma data reconhecível, a função retorna `None`.

Em aplicações reais, também é prudente estabelecer um limite máximo de espera e evitar depender exclusivamente do relógio local, que pode estar incorreto.

## 6. Implementando uma tentativa com espera

Uma forma simples de lidar com `429` é consultar o cabeçalho e aguardar antes de tentar novamente.

```
import time
import requests

url = "https://api.example.com/dados"

response = requests.get(
    url,
    timeout=15,
)

if response.status_code == 429:
    retry_after = response.headers.get("Retry-After")

    if retry_after and retry_after.isdigit():
        espera = int(retry_after)
        time.sleep(espera)

        response = requests.get(
            url,
            timeout=15,
        )

response.raise_for_status()
```

### Explicação

**Importar** `**time**`

```
import time
```

Importa funções relacionadas ao tempo.

**Consultar** `**Retry-After**`

```
retry_after = response.headers.get("Retry-After")
```

Obtém o tempo indicado pelo servidor, se estiver disponível.

**Verificar se é numérico**

```
if retry_after and retry_after.isdigit():
```

A condição verifica se o cabeçalho existe e contém apenas dígitos.

Esse exemplo simples só trata o formato em segundos. Ele não interpreta o formato de data HTTP.

**Aguardar**

```
time.sleep(espera)
```

Suspende a execução da thread atual durante o intervalo especificado.

**Repetir a requisição**

O programa realiza uma nova requisição depois da espera.

**Validar o resultado**

```
response.raise_for_status()
```

Gera uma exceção se a resposta final indicar um status HTTP de erro.

### Limitações desse exemplo

- Realiza no máximo uma nova tentativa.
    
- Não interpreta datas HTTP em `Retry-After`.
    
- Não estabelece um limite máximo para a espera.
    
- Não trata especificamente falhas de conexão.
    
- Não garante que repetir a requisição seja seguro para qualquer endpoint.
    

O exemplo serve para demonstrar o mecanismo básico, não como implementação completa para produção.

## 7. Implementando tentativas limitadas

Em uma aplicação real, é útil definir um número máximo de tentativas e uma política de espera.

```
import time

import requests


def consultar_api(url, max_tentativas=3):
    for tentativa in range(max_tentativas):
        response = requests.get(
            url,
            timeout=(5, 20),
        )

        if response.status_code != 429:
            response.raise_for_status()
            return response

        retry_after = response.headers.get("Retry-After")

        if retry_after and retry_after.isdigit():
            espera = int(retry_after)
        else:
            espera = 2 ** tentativa

        if tentativa < max_tentativas - 1:
            time.sleep(espera)

    raise RuntimeError(
        "Limite de tentativas atingido após respostas HTTP 429."
    )
```

### Explicação das partes principais

**Parâmetro** `**max_tentativas**`

Define o número máximo de requisições que a função tentará realizar.

**Laço** `**for**`

```
for tentativa in range(max_tentativas):
```

Percorre os índices das tentativas, começando em zero.

Com `max_tentativas=3`, os índices serão `0`, `1` e `2`.

**Verificar se a resposta não é 429**

```
if response.status_code != 429:
    response.raise_for_status()
    return response
```

Se o servidor não retornou `429`, a função verifica se existe outro erro HTTP. Se não houver, retorna o objeto `Response`.

**Calcular a espera**

```
espera = 2 ** tentativa
```

O operador `**` realiza exponenciação.

A sequência de espera será de 1, 2 e 4 segundos para os índices 0, 1 e 2, respectivamente, quando esse caminho for utilizado.

**Evitar esperar após a última tentativa**

```
if tentativa < max_tentativas - 1:
    time.sleep(espera)
```

Não há motivo para aguardar se o programa não fará outra tentativa.

**Informar que as tentativas acabaram**

```
raise RuntimeError(...)
```

Gera uma exceção quando todas as tentativas terminam em `429`.

### Limitações

Esse exemplo é didático. Uma implementação de produção deve:

- Interpretar os dois formatos de `Retry-After`.
    
- Limitar o tempo máximo de espera.
    
- Tratar falhas de rede e timeouts.
    
- Considerar jitter para evitar que vários clientes repitam simultaneamente.
    
- Registrar tentativas sem expor informações sensíveis.
    
- Respeitar os limites documentados pelo serviço.
    

Também é importante observar que o código só repete respostas `429`; ele não tenta novamente automaticamente diante de falhas de conexão.

## 8. O que é exponential backoff?

Exponential backoff é uma estratégia em que o intervalo entre tentativas aumenta progressivamente.

Uma fórmula simples é:

[  
t_n = \min(t_{\max}, t_0 \times 2^n)  
]

Onde:

- (t_n): tempo de espera da tentativa atual.
    
- (t_{\max}): limite máximo de espera.
    
- (t_0): intervalo inicial.
    
- (n): índice da tentativa.
    

Por exemplo, com intervalo inicial de 1 segundo e limite máximo de 8 segundos:

|Índice|Espera calculada|
|---|---|
|0|1 segundo|
|1|2 segundos|
|2|4 segundos|
|3|8 segundos|
|4|8 segundos|
|5|8 segundos|

A estratégia reduz a frequência de tentativas quando um serviço está sobrecarregado.

### Jitter

Jitter adiciona uma variação aleatória à espera para evitar que vários clientes façam novas tentativas exatamente no mesmo instante.

Um exemplo conceitual é escolher um tempo aleatório entre zero e o intervalo calculado.

O jitter deve ser combinado com limites razoáveis de tempo e com a política de repetição apropriada para o serviço.

## 9. O cabeçalho `Retry-After` e a política de repetição

Quando o servidor fornece `Retry-After`, o cliente deve considerar essa indicação ao planejar a próxima tentativa.

Uma estratégia cuidadosa pode:

1. Ler o cabeçalho.
    
2. Interpretar o intervalo ou a data.
    
3. Aplicar limites operacionais à espera.
    
4. Aguardar pelo tempo apropriado.
    
5. Repetir somente se a operação puder ser repetida com segurança.
    

Se a política do serviço exigir aguardar um intervalo maior do que o limite operacional do cliente, não é correto ignorar silenciosamente a orientação e repetir imediatamente. O programa pode encerrar a operação, agendar a tentativa para depois ou informar que o limite de espera foi excedido.

## 10. Rate limiting não é o mesmo que bloquear o cliente

Uma resposta `429` normalmente representa uma limitação de frequência, mas os sistemas podem aplicar políticas diferentes.

Por exemplo:

- Limite por minuto.
    
- Limite diário.
    
- Limite de requisições simultâneas.
    
- Limite por endpoint.
    
- Limite por usuário ou token.
    

Algumas APIs informam limites e cotas por meio de cabeçalhos próprios. Os nomes e a semântica desses cabeçalhos não são universais.

Não presuma que mudar de IP, criar novas sessões ou repetir requisições rapidamente seja uma solução apropriada. O correto é respeitar a política de acesso, autenticação e frequência do serviço.

## 11. Boas práticas

- Consulte a documentação da API antes de definir a frequência das requisições.
    
- Trate `429` explicitamente quando o serviço utilizar esse status para rate limiting.
    
- Considere `Retry-After` quando estiver presente.
    
- Defina limites de tentativas e de tempo total de espera.
    
- Utilize backoff com jitter quando apropriado.
    
- Não repita operações que possam gerar efeitos duplicados sem verificar sua segurança.
    
- Separe erros HTTP de falhas de rede.
    
- Evite laços de repetição infinitos.
    
- Não utilize tentativas automáticas para contornar controles de acesso.
    
- Registre informações úteis para diagnóstico sem expor tokens ou dados sensíveis.
    

## 12. Referência rápida

|Elemento|Finalidade|
|---|---|
|`429 Too Many Requests`|Indicar excesso de requisições|
|`Retry-After`|Informar quando uma nova tentativa pode ocorrer|
|`response.status_code`|Consultar o status HTTP|
|`response.headers.get()`|Consultar um cabeçalho|
|`time.sleep(segundos)`|Suspender a execução durante um intervalo|
|`range(max_tentativas)`|Controlar o número de tentativas|
|`2 ** tentativa`|Calcular uma espera exponencial simples|
|Exponential backoff|Aumentar progressivamente o intervalo entre tentativas|
|Jitter|Introduzir variação no intervalo entre tentativas|
|`response.raise_for_status()`|Gerar exceção para status HTTP de erro|

---
