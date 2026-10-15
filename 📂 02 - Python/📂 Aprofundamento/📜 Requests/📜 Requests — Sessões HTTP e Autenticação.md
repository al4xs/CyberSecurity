# 📜 Requests — Sessões HTTP e Autenticação

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