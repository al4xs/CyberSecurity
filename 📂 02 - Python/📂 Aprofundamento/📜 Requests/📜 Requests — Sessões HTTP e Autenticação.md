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
