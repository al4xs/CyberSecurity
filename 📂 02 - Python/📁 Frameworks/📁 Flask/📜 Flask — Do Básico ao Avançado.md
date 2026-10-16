## 01. Fundamentos do Flask

### 1.1. O que é Flask?

Flask é um framework web escrito em Python, utilizado para desenvolver aplicações web, sites, painéis administrativos e APIs.

Para entender sua função, imagine que você queira criar um site com Python. O navegador do usuário acessa um endereço, o servidor recebe uma requisição, executa algum código e devolve uma resposta.

O Flask fornece recursos para conectar essas etapas sem que você precise implementar manualmente todo o funcionamento de um servidor web.

Por exemplo, com Flask, podemos criar uma rota que exibe uma mensagem:

```
from flask import Flask

app = Flask(__name__)

@app.route("/")
def inicio():
    return "Olá, mundo!"
```

Esse pequeno programa cria uma aplicação capaz de responder a uma requisição para a rota `/`.

Antes de analisar cada linha, é importante compreender alguns conceitos.

**Framework:** conjunto de ferramentas e estruturas que facilita o desenvolvimento de aplicações. Ele oferece uma maneira organizada de implementar funcionalidades comuns, como rotas e tratamento de requisições.

**Aplicação web:** programa que disponibiliza funcionalidades por meio de uma interface acessada normalmente por um navegador ou cliente HTTP.

**Rota:** associação entre um endereço e o código que deverá responder a uma requisição correspondente.

**Requisição HTTP:** mensagem enviada por um cliente, como um navegador, solicitando um recurso ou uma operação.

**Resposta HTTP:** mensagem devolvida pelo servidor ao cliente, contendo informações como o resultado da operação, o código de status e, quando aplicável, o conteúdo da página.

### 1.2. Como o Flask funciona?

O funcionamento básico pode ser representado assim:

```
Navegador
    |
    | Requisição HTTP: GET /
    v
Servidor de desenvolvimento
    |
    v
Aplicação Flask
    |
    v
Rota "/" -> função inicio()
    |
    | return "Olá, mundo!"
    v
Resposta HTTP
    |
    v
Navegador exibe: Olá, mundo!
```

Nesse exemplo, o navegador solicita a página inicial, representada pelo endereço `/`. O Flask identifica a rota correspondente, executa a função associada e utiliza seu retorno para construir a resposta.

Esse processo permite criar desde páginas simples até aplicações que consultam bancos de dados, autenticam usuários e disponibilizam APIs.

### 1.3. Flask é uma biblioteca ou um framework?

Flask é classificado como um **framework web para Python**.

Uma biblioteca normalmente oferece funcionalidades que você chama quando precisa. Um framework, além de oferecer funcionalidades, estabelece uma estrutura de funcionamento para a aplicação e participa do processamento das requisições.

Isso não significa que Flask e bibliotecas sejam categorias completamente isoladas: um framework pode utilizar diversas bibliotecas internamente e aceitar extensões externas.

Durante nosso estudo, encontraremos ferramentas relacionadas ao Flask, como:

- **Jinja:** linguagem de templates utilizada para gerar conteúdo, como páginas HTML dinâmicas.
    
- **Flask-SQLAlchemy:** integração entre Flask e o ORM SQLAlchemy.
    
- **Flask-Login:** recursos para gerenciar sessões de usuários autenticados.
    
- **Flask-WTF:** integração com WTForms para trabalhar com formulários e proteção CSRF.
    

Essas ferramentas serão estudadas conforme forem necessárias.

### 1.4. Preparando o ambiente de desenvolvimento

Antes de instalar o Flask, precisamos preparar um ambiente Python para o projeto.

É recomendado utilizar um ambiente virtual (`venv`) para manter as dependências de cada projeto separadas. Assim, instalar ou atualizar uma biblioteca em um projeto não precisa modificar as dependências dos demais.

No terminal, crie uma pasta para o projeto e entre nela:

```
mkdir flask_lab
cd flask_lab
```

**O que cada comando faz?**

- `mkdir flask_lab`: cria uma pasta chamada `flask_lab`.
    
- `cd flask_lab`: entra nessa pasta.
    

Em seguida, crie o ambiente virtual:

```
python3 -m venv .venv
```

Analisando o comando:

|Elemento|Função|
|---|---|
|`python3`|Executa o interpretador Python 3.|
|`-m`|Executa um módulo Python como programa.|
|`venv`|Módulo padrão do Python para criar ambientes virtuais.|
|`.venv`|Nome do diretório onde o ambiente será criado.|

O nome `.venv` é uma convenção, não uma exigência. Poderíamos utilizar outro nome.

Ative o ambiente virtual no Linux:

```
source .venv/bin/activate
```

O comando `source` executa o script de ativação no shell atual. O script ajusta o ambiente, incluindo o `PATH`, para que os comandos Python e `pip` utilizem as ferramentas do ambiente virtual.

Após a ativação, o terminal geralmente passa a exibir `(.venv)` no início do prompt.

Instale o Flask:

```
python -m pip install Flask
```

Aqui:

- `python`: executa o interpretador Python associado ao ambiente ativo.
    
- `-m pip`: executa o módulo `pip`, responsável pela instalação de pacotes.
    
- `install`: indica que queremos instalar um pacote.
    
- `Flask`: nome do pacote a ser instalado.
    

Usar `python -m pip` ajuda a garantir que a instalação seja feita pelo `pip` associado ao interpretador Python escolhido.

Para conferir a instalação:

```
python -m pip show Flask
```

Esse comando apresenta informações sobre o pacote instalado, incluindo a versão.

**Observação:** o comando de instalação não fixa uma versão específica. O `pip` instala uma versão compatível com o ambiente e com as restrições de dependências disponíveis. Em projetos reais, o controle das versões será importante para reproduzir o ambiente.

### 1.5. Criando a primeira aplicação Flask

Dentro da pasta `flask_lab`, crie um arquivo chamado `app.py`.

Adicione o seguinte código:

```
from flask import Flask

app = Flask(__name__)

@app.route("/")
def inicio():
    return "Olá, mundo!"
```

Agora vamos analisar cada linha.

**Linha 1 — Importando Flask**

```
from flask import Flask
```

O comando importa o objeto `Flask` do pacote `flask`.

- `from flask`: indica o módulo de origem.
    
- `import Flask`: importa o nome `Flask` para que possamos utilizá-lo diretamente no arquivo.
    

Sem essa importação, tentar executar `Flask(...)` produziria um `NameError`, porque o nome não estaria definido no escopo atual.

**Linha 2 — Criando a aplicação**

```
app = Flask(__name__)
```

Essa linha instancia a classe `Flask` e guarda o objeto criado na variável `app`.

Vamos separar seus elementos:

- `app`: nome da variável escolhida para armazenar a aplicação. Poderia ser outro nome, desde que o restante do código utilizasse o mesmo nome.
    
- `Flask`: classe que representa a aplicação Flask.
    
- `__name__`: variável especial do Python que identifica o nome do módulo atual.
    

O argumento `__name__` permite que o Flask identifique o módulo que está criando a aplicação. Essa informação ajuda o framework a localizar recursos associados ao projeto, como templates e arquivos estáticos.

**O parâmetro** `**import_name**`

Na assinatura usual da classe `Flask`, o primeiro parâmetro é `import_name`. Portanto, nesta expressão:

```
Flask(__name__)
```

o valor de `__name__` é passado para `import_name`.

Esse parâmetro é obrigatório na criação normal da aplicação: precisamos informar qual é o módulo de referência.

Não confunda o nome da variável `app` com o nome da classe `Flask`. A classe cria o objeto; a variável apenas guarda uma referência a ele.

**Linha 3 — Decorator de rota**

```
@app.route("/")
```

Essa linha registra uma rota para o caminho `/`, que representa a raiz da aplicação.

O símbolo `@` introduz um decorator Python. Nesse caso, o decorator `route()` da aplicação associa a função definida logo abaixo ao caminho especificado.

O método `route()` aceita vários parâmetros. Os mais relevantes neste início são:

|Parâmetro|Obrigatório?|Comportamento|
|---|---|---|
|`rule`|Sim|Caminho da URL que será registrado.|
|`methods`|Não|Métodos HTTP permitidos; por padrão, a rota aceita `GET`.|
|`defaults`|Não|Valores padrão associados a variáveis da rota.|
|`endpoint`|Não|Nome interno usado para identificar o endpoint; por padrão, deriva do nome da função.|

O argumento `"/"` é o valor de `rule`.

Quando acessamos a raiz da aplicação, por exemplo `http://127.0.0.1:5000/`, o Flask procura uma rota compatível com o caminho solicitado e com o método HTTP utilizado.

**Linha 4 — Criando a função**

```
def inicio():
```

A palavra-chave `def` define uma função Python chamada `inicio`.

Essa função será executada quando uma requisição corresponder à rota registrada, desde que o método HTTP também seja permitido.

Os parênteses estão vazios porque a função não recebe parâmetros explícitos. Isso não significa que ela não participe do processamento de uma requisição: o Flask gerencia o contexto da requisição e disponibiliza recursos como `request` quando necessário.

O nome `inicio` é uma escolha nossa. Poderíamos chamá-la de `home`, `index` ou outro nome válido.

**Linha 5 — Retornando o conteúdo**

```
return "Olá, mundo!"
```

A instrução `return` encerra a execução da função e devolve seu resultado ao código que a chamou.

Nesse caso, a função retorna uma string. O Flask transforma esse retorno em uma resposta HTTP.

O navegador recebe o conteúdo textual e o exibe na página.

Observe que não utilizamos `print()`. A função `print()` escreve no terminal; já o `return` fornece o resultado que o Flask utilizará para montar a resposta.

### 1.6. Executando a aplicação

Na mesma pasta em que está o arquivo `app.py`, execute:

```
python -m flask --app app run
```

O comando informa ao Flask qual aplicação deve ser carregada e inicia o servidor de desenvolvimento.

|Elemento|Função|
|---|---|
|`python -m flask`|Executa a interface de linha de comando do Flask.|
|`--app app`|Indica que o módulo de aplicação se chama `app`, correspondendo ao arquivo `app.py`.|
|`run`|Inicia o servidor de desenvolvimento.|

O argumento de `--app` pode ser escrito de outras formas, dependendo da estrutura do projeto. Neste exemplo, como `app.py` está no diretório atual e contém a variável `app`, a indicação `--app app` é suficiente.

O terminal deverá apresentar uma mensagem indicando que o servidor está em execução, normalmente em um endereço semelhante a:

```
http://127.0.0.1:5000
```

Abra esse endereço no navegador. Você deverá visualizar:

```
Olá, mundo!
```

Para encerrar o servidor, volte ao terminal e pressione `Ctrl+C`.

### 1.7. O que são `127.0.0.1` e a porta `5000`?

O endereço `127.0.0.1` é um endereço de loopback IPv4: ele permite acessar um serviço na própria máquina.

A porta `5000` é a porta padrão utilizada pelo comando `flask run`, salvo configuração diferente.

O endereço completo contém duas informações importantes:

- `127.0.0.1`: o endereço de rede em que o servidor está escutando.
    
- `5000`: a porta pela qual o serviço está acessível.
    

Por padrão, o servidor de desenvolvimento é destinado a testes locais. Para permitir acesso a partir de outras máquinas, seria necessário alterar o endereço de escuta e considerar a configuração de rede e segurança. Isso não transforma o servidor de desenvolvimento em um servidor apropriado para produção.

### 1.8. Entendendo o modo debug

Durante o desenvolvimento, é comum executar:

```
python -m flask --app app run --debug
```

O parâmetro `--debug` ativa o modo de depuração, que inclui o recarregamento automático em mudanças detectadas e um depurador para ajudar a investigar erros.

Ele é útil enquanto estamos programando, mas deve ser desativado em produção. O depurador pode expor informações sensíveis e, dependendo de sua configuração e acessibilidade, representar um risco grave.

Não confunda o modo debug com uma correção automática: ele ajuda a identificar problemas, mas não resolve a lógica incorreta do programa.

### 1.9. Erros comuns nesta etapa

**Erro:** `**ModuleNotFoundError: No module named 'flask'**`

O Python utilizado para executar o projeto não encontrou o pacote Flask.

Possíveis causas:

- O Flask ainda não foi instalado.
    
- O ambiente virtual não foi ativado.
    
- O pacote foi instalado em outro ambiente Python.
    

Verifique o interpretador e instale a dependência no ambiente correto:

```
python -m pip show Flask
python -m pip install Flask
```

**Erro:** `**Address already in use**`

A porta utilizada pelo servidor já está ocupada, por exemplo, porque outra instância do Flask continua em execução.

Você pode encerrar o outro servidor ou escolher uma porta diferente:

```
python -m flask --app app run --port 5001
```

Nesse exemplo, `--port` define a porta utilizada pelo servidor de desenvolvimento.

**Erro:** `**Could not locate a Flask application**`

O Flask não conseguiu localizar a aplicação indicada.

Confira:

- Se você está no diretório correto.
    
- Se o arquivo `app.py` existe.
    
- Se o arquivo contém a variável `app`.
    
- Se o valor passado a `--app` corresponde ao módulo e à aplicação existentes.
    

**Erro:** `**404 Not Found**` **no navegador**

O servidor pode estar funcionando normalmente, mas não existir uma rota correspondente ao caminho solicitado.

Neste exemplo, a rota registrada é `/`. Confira se o endereço acessado corresponde a esse caminho.

### 1.10. Observações importantes

- O Flask não substitui o Python: ele é uma ferramenta utilizada dentro de programas Python.
    
- `app` é apenas o nome convencional da variável que guarda a aplicação.
    
- `__name__` é uma variável especial do Python, não um parâmetro exclusivo do Flask.
    
- `@app.route()` registra a relação entre um caminho e uma função.
    
- `return` fornece o resultado que será utilizado para formar a resposta HTTP.
    
- O servidor iniciado com `flask run` é voltado ao desenvolvimento, não à publicação de aplicações em produção.

---
## 2. Rotas e funções de visualização

### 2.1. O que é uma rota?

Uma rota é uma regra que associa um caminho de URL a uma função que será executada quando uma requisição compatível chegar à aplicação.

Imagine que você esteja desenvolvendo um site com três páginas:

- `/` — página inicial.
    
- `/sobre` — página sobre o projeto.
    
- `/contato` — página de contato.
    

No Flask, podemos associar cada caminho a uma função Python.

```
from flask import Flask

app = Flask(__name__)


@app.route("/")
def inicio():
    return "Página inicial"


@app.route("/sobre")
def sobre():
    return "Sobre o projeto"


@app.route("/contato")
def contato():
    return "Entre em contato"
```

Vamos entender o que acontece:

1. O Flask cria a aplicação.
    
2. Cada `@app.route()` registra uma regra de URL.
    
3. Cada função define o comportamento associado à sua rota.
    
4. Quando uma requisição chega, o Flask procura uma regra compatível.
    
5. A função correspondente é executada e seu retorno é utilizado para construir a resposta HTTP.
    

**Importante:** a definição de uma função não significa que ela será executada imediatamente. O decorator registra a função para que o Flask possa chamá-la posteriormente, durante o processamento de uma requisição.

### 2.2. O que é o decorator `@app.route()`?

O decorator `@app.route()` é um recurso utilizado para registrar rotas na aplicação Flask.

Exemplo:

```
@app.route("/perfil")
def perfil():
    return "Meu perfil"
```

Analisando cada parte:

- `@app.route("/perfil")`: registra a rota correspondente ao caminho `/perfil`.
    
- `def perfil():`: define a função Python associada à rota.
    
- `return "Meu perfil"`: devolve o conteúdo que será utilizado na resposta.
    

O método `route()` é um método da aplicação Flask. Ele recebe o caminho e permite configurar outras características da rota.

Sua assinatura pública, de forma simplificada, é:

```
app.route(
    rule,
    **options
)
```

|Parâmetro|Obrigatório?|Descrição|
|---|---|---|
|`rule`|Sim|Caminho da URL a ser registrado.|
|`**options`|Não|Argumentos nomeados adicionais que configuram a rota, como `methods` e `endpoint`.|

`**options` não é um parâmetro chamado `options` que você precisa passar como um dicionário. É uma notação do Python que permite receber argumentos nomeados adicionais.

Por exemplo:

```
@app.route("/perfil", methods=["GET"])
def perfil():
    return "Meu perfil"
```

Nesse caso, `methods=["GET"]` é uma opção passada ao decorator.

### 2.3. O que é um endpoint?

No Flask, um endpoint é o nome utilizado internamente para identificar uma rota registrada. Por padrão, esse nome deriva do nome da função de visualização.

Observe:

```
@app.route("/perfil")
def perfil():
    return "Meu perfil"
```

Nesse exemplo:

- Caminho da URL: `/perfil`.
    
- Nome da função: `perfil`.
    
- Endpoint padrão: `perfil`.
    

Esses conceitos são relacionados, mas não são iguais. A URL é o caminho acessado pelo cliente; o endpoint é o identificador utilizado pelo Flask.

Podemos personalizar o endpoint:

```
@app.route("/perfil", endpoint="pagina_perfil")
def perfil():
    return "Meu perfil"
```

Agora, o endpoint será `pagina_perfil`, embora o caminho continue sendo `/perfil` e a função continue se chamando `perfil`.

O parâmetro `endpoint` é opcional. Quando não é informado, o Flask utiliza o nome da função como padrão.

Os endpoints são especialmente úteis com `url_for()`, que estudaremos em detalhes mais adiante.

### 2.4. O que são funções de visualização (_view functions_)?

Uma função de visualização é a função Python chamada pelo Flask para processar uma requisição correspondente a uma rota.

Exemplo:

```
@app.route("/saudacao")
def saudacao():
    nome = "Maria"
    return f"Olá, {nome}!"
```

Linha por linha:

**Linha 1 — Registro da rota**

```
@app.route("/saudacao")
```

Registra o caminho `/saudacao`.

**Linha 2 — Definição da função**

```
def saudacao():
```

Define a função que será chamada quando a requisição corresponder à rota e ao método HTTP permitido.

**Linha 3 — Criação da variável**

```
nome = "Maria"
```

Cria uma variável local chamada `nome`, que armazena uma string.

**Linha 4 — Retorno**

```
return f"Olá, {nome}!"
```

A letra `f` antes da string indica uma _f-string_, recurso nativo do Python que permite inserir expressões dentro de `{}`.

O resultado dessa expressão será:

```
Olá, Maria!
```

O Flask utiliza essa string como conteúdo da resposta HTTP.

Uma função de visualização não precisa retornar apenas uma string. Dependendo do caso, pode retornar um dicionário que será convertido em JSON, uma resposta HTTP ou uma tupla contendo informações adicionais, como o código de status.

Se uma função de visualização terminar sem retornar um valor válido para o Flask, a aplicação poderá gerar um erro durante o processamento da requisição.

### 2.5. Métodos HTTP nas rotas

Uma URL não determina sozinha o que o Flask deve fazer. O método HTTP utilizado na requisição também pode influenciar a seleção da rota.

Os métodos mais comuns são:

|Método|Uso típico|
|---|---|
|`GET`|Consultar ou obter um recurso.|
|`POST`|Enviar dados para processamento, como um formulário.|
|`PUT`|Substituir um recurso existente ou criar um recurso em uma URI definida, conforme a API.|
|`PATCH`|Atualizar parcialmente um recurso.|
|`DELETE`|Solicitar a exclusão de um recurso.|

O comportamento concreto depende da aplicação. Esses métodos não executam operações automaticamente: o desenvolvedor precisa implementar a lógica correspondente.

Por padrão, uma rota criada com `@app.route()` aceita `GET`. O Flask também adiciona `HEAD` quando `GET` está habilitado e, nas configurações usuais, trata `OPTIONS` automaticamente.

Podemos definir os métodos permitidos utilizando `methods`.

```
@app.route("/mensagem", methods=["GET", "POST"])
def mensagem():
    return "Rota acessada"
```

Nesse exemplo, a rota aceita requisições `GET` e `POST`.

O parâmetro `methods` é opcional. Quando omitido, o comportamento padrão inclui `GET`.

Se um cliente enviar um método que não está permitido para aquela rota, o Flask normalmente responderá com o status `405 Method Not Allowed`, desde que o caminho corresponda a uma rota existente.

### 2.6. Criando uma rota que aceita GET e POST

Vamos criar um exemplo em que a resposta muda conforme o método utilizado.

```
from flask import Flask, request

app = Flask(__name__)


@app.route("/mensagem", methods=["GET", "POST"])
def mensagem():
    if request.method == "POST":
        return "Você enviou uma requisição POST"

    return "Você acessou a rota com GET"
```

Agora, analisaremos os elementos novos.

**Importação do objeto** `**request**`

```
from flask import Flask, request
```

Além de importar `Flask`, importamos `request`, um objeto disponibilizado pelo Flask para acessar informações da requisição atual.

**Definição dos métodos aceitos**

```
@app.route("/mensagem", methods=["GET", "POST"])
```

A opção `methods` recebe uma sequência de métodos HTTP permitidos. Aqui, utilizamos uma lista Python com duas strings.

**Verificação do método**

```
if request.method == "POST":
```

A propriedade `request.method` informa o método HTTP da requisição atual.

Se o método for `POST`, a condição será verdadeira e o Flask executará o bloco correspondente.

**Retorno da resposta**

```
return "Você enviou uma requisição POST"
```

Quando a condição for verdadeira, essa string será retornada como resposta.

**Retorno alternativo**

```
return "Você acessou a rota com GET"
```

Se a condição não for verdadeira, a execução chega ao último `return`.

Como a rota aceita apenas `GET` e `POST` explicitamente, os métodos adicionais `HEAD` e `OPTIONS` podem ser tratados conforme o comportamento automático do Flask. Para essas requisições, não se deve presumir que a função sempre será executada da mesma forma que em um `GET` comum.

### 2.7. Parâmetros de rota

Podemos criar rotas que recebem valores diretamente do caminho da URL.

Por exemplo, imagine que queremos exibir o perfil de um usuário identificado por um número.

```
@app.route("/usuario/<int:id>")
def usuario(id):
    return f"Usuário de ID {id}"
```

Acessando:

```
/usuario/10
```

A resposta será:

```
Usuário de ID 10
```

Vamos entender a sintaxe:

- `/usuario/`: parte fixa do caminho.
    
- `<int:id>`: segmento variável chamado `id`, que deve ser convertido para um número inteiro.
    
- `def usuario(id)`: a função recebe o valor extraído da URL como argumento.
    
- `return`: utiliza o valor recebido para construir a resposta.
    

O nome da variável na rota e o parâmetro da função precisam corresponder.

Por exemplo, se a rota utilizar `<int:user_id>`, a função deverá receber `user_id`:

```
@app.route("/usuario/<int:user_id>")
def usuario(user_id):
    return f"Usuário de ID {user_id}"
```

Se o nome não corresponder, o Flask poderá gerar um erro ao tentar chamar a função.

Os conversores mais comuns incluem:

|Conversor|Comportamento|
|---|---|
|`string`|Aceita um segmento de texto sem barras. É o conversor padrão.|
|`int`|Aceita valores inteiros positivos ou negativos no formato permitido pelo conversor.|
|`float`|Aceita valores numéricos de ponto flutuante no formato permitido.|
|`path`|Aceita texto que pode incluir barras.|
|`uuid`|Aceita identificadores no formato UUID.|

Por exemplo:

```
@app.route("/produto/<int:produto_id>")
def produto(produto_id):
    return f"Produto: {produto_id}"


@app.route("/arquivo/<path:caminho>")
def arquivo(caminho):
    return f"Caminho recebido: {caminho}"
```

O conversor `int` ajuda a restringir o formato do valor aceito pela rota, mas não substitui a validação da existência do registro ou das permissões de acesso.

Se a URL não corresponder ao formato esperado pelo conversor, a rota não será selecionada. Se nenhuma outra regra corresponder, o cliente receberá normalmente um `404 Not Found`.

### 2.8. Parâmetros opcionais e valores padrão

É importante distinguir dois conceitos diferentes:

1. Um parâmetro de rota pode ser opcional porque uma regra define um valor padrão.
    
2. Um parâmetro de uma função Python pode ser opcional porque sua definição fornece um valor padrão.
    

No Flask, podemos utilizar a opção `defaults`:

```
@app.route("/pagina/", defaults={"secao": "inicio"})
@app.route("/pagina/<secao>")
def pagina(secao):
    return f"Seção: {secao}"
```

Nesse exemplo:

- `/pagina/` utiliza o valor padrão `"inicio"`.
    
- `/pagina/contato` fornece o valor `"contato"` pela URL.
    
- A função recebe o argumento `secao` nos dois casos.
    

O parâmetro `defaults` é opcional e recebe um dicionário de valores padrão para variáveis da rota. Ele é útil quando queremos que caminhos diferentes utilizem a mesma função.

Isso é diferente de definir um valor padrão diretamente na função:

```
def saudacao(nome="visitante"):
    return f"Olá, {nome}!"
```

Nesse caso, `nome` é um parâmetro opcional da função Python, independentemente de qualquer configuração de rota.

### 2.9. Erros comuns ao trabalhar com rotas

**Erro:** `**404 Not Found**`

O Flask não encontrou uma regra compatível com o caminho solicitado.

Verifique:

- Se o caminho acessado corresponde ao registrado.
    
- Se há diferenças entre segmentos da URL.
    
- Se os conversores de parâmetros aceitam o formato fornecido.
    

**Erro:** `**405 Method Not Allowed**`

O caminho corresponde a uma rota, mas o método HTTP utilizado não está permitido.

Verifique o argumento `methods` e o método enviado pelo cliente.

**Erro:** `**TypeError**` **ao chamar a função de visualização**

Um caso comum ocorre quando os nomes dos parâmetros de rota não correspondem aos parâmetros esperados pela função.

Por exemplo:

```
@app.route("/usuario/<int:user_id>")
def usuario(id):
    return str(id)
```

A rota fornece o argumento `user_id`, mas a função espera `id`. Essa divergência pode provocar um erro ao processar a requisição.

A correção é utilizar o mesmo nome:

```
@app.route("/usuario/<int:user_id>")
def usuario(user_id):
    return str(user_id)
```

**Erro:** `**AssertionError**` **ao registrar rotas**

Isso pode acontecer quando tentamos registrar uma rota com um endpoint já utilizado de forma incompatível com as regras da aplicação.

Confira se os endpoints são únicos e se o registro das rotas está sendo realizado corretamente.

### 2.10. Resumo dos principais parâmetros

|Recurso|Parâmetro|Obrigatório?|Função|
|---|---|---|---|
|`app.route()`|`rule`|Sim|Define o caminho da rota.|
|`app.route()`|`methods`|Não|Define os métodos HTTP permitidos.|
|`app.route()`|`defaults`|Não|Define valores padrão para variáveis da rota.|
|`app.route()`|`endpoint`|Não|Define o identificador interno da rota.|
|Função de visualização|Parâmetros da rota|Conforme a regra|Recebem os valores extraídos do caminho.|

Os parâmetros disponíveis e o comportamento exato dependem da API utilizada. Ao estudar recursos mais avançados, consultaremos as assinaturas e a documentação da versão instalada para evitar assumir que todas as funções aceitam os mesmos argumentos.

---
## 3. Requisições e respostas HTTP

### 3.1. Como funciona uma requisição HTTP?

Quando acessamos um site, o navegador envia uma requisição HTTP ao servidor. O Flask recebe essa requisição, identifica a rota correspondente e executa a lógica necessária para produzir uma resposta.

Uma requisição HTTP pode conter:

- Método HTTP, como `GET` ou `POST`.
    
- Caminho solicitado, como `/usuarios`.
    
- Parâmetros na URL.
    
- Cabeçalhos HTTP.
    
- Cookies.
    
- Dados enviados no corpo da requisição.
    

A resposta pode conter:

- Código de status HTTP.
    
- Cabeçalhos.
    
- Cookies.
    
- Conteúdo HTML, texto, JSON ou outros tipos de dados.
    

O fluxo básico é:

```
Navegador
    |
    | Requisição HTTP
    v
Aplicação Flask
    |
    | Identifica a rota
    v
Função de visualização
    |
    | Processa os dados
    v
Resposta HTTP
    |
    v
Navegador
```

Vamos começar entendendo como acessar os dados recebidos.

### 3.2. O objeto `request`

O Flask disponibiliza o objeto `request`, que permite acessar informações sobre a requisição HTTP atual.

Para utilizá-lo, precisamos importá-lo:

```
from flask import Flask, request

app = Flask(__name__)


@app.route("/informacoes")
def informacoes():
    return f"Método utilizado: {request.method}"
```

Analisando as partes novas:

- `request`: objeto disponibilizado pelo Flask para consultar a requisição atual.
    
- `request.method`: propriedade que informa o método HTTP utilizado, como `GET` ou `POST`.
    
- `f"Método utilizado: {request.method}"`: f-string que insere o valor da propriedade na mensagem retornada.
    

Ao acessar `/informacoes` normalmente pelo navegador, a requisição será `GET`, e a resposta será:

```
Método utilizado: GET
```

O objeto `request` é disponibilizado pelo Flask durante o processamento da requisição. Ele funciona por meio do contexto da requisição, portanto não deve ser tratado como um objeto global que representa todas as requisições da aplicação.

### 3.3. Parâmetros de consulta: `request.args`

Os parâmetros de consulta são os valores enviados na parte da URL que aparece após o caractere `?`.

Por exemplo:

```
/busca?nome=Maria&idade=25
```

Nesse endereço, temos dois parâmetros:

- `nome`, com valor `Maria`.
    
- `idade`, com valor `25`.
    

Para acessá-los, utilizamos `request.args`.

```
from flask import Flask, request

app = Flask(__name__)


@app.route("/busca")
def busca():
    nome = request.args.get("nome", "visitante")
    return f"Olá, {nome}!"
```

**Linha por linha**

`nome = request.args.get("nome", "visitante")`

- `request.args`: coleção dos parâmetros da query string.
    
- `.get()`: método que permite consultar um parâmetro pelo nome.
    
- `"nome"`: chave que queremos consultar.
    
- `"visitante"`: valor retornado caso a chave não esteja presente.
    

O método `get()` aceita um segundo argumento opcional, utilizado como valor padrão. Se a chave não existir, o método retorna esse valor em vez de lançar `KeyError`.

Exemplos:

|URL|Resultado|
|---|---|
|`/busca?nome=Maria`|`Olá, Maria!`|
|`/busca?nome=João`|`Olá, João!`|
|`/busca`|`Olá, visitante!`|

**Importante:** os valores da query string são recebidos como texto. Se o parâmetro representar uma idade, um preço ou outra quantidade, será necessário validar e converter o valor conforme a necessidade.

Também é importante diferenciar a ausência de um parâmetro de um parâmetro presente com valor vazio. Por exemplo, `?nome=` fornece uma string vazia; o valor padrão de `get()` não é utilizado nesse caso.

### 3.4. Dados de formulários: `request.form`

Quando um formulário HTML utiliza o método `POST`, os campos enviados no corpo da requisição podem ser acessados por `request.form`, desde que sejam enviados como dados de formulário compatíveis.

Considere este formulário:

```
<form action="/cadastro" method="POST">
    <label for="nome">Nome:</label>
    <input type="text" id="nome" name="nome">

    <button type="submit">Enviar</button>
</form>
```

O atributo `name="nome"` identifica o campo enviado ao servidor. O atributo `id` é usado, entre outras coisas, para associar o campo ao `label`; ele não substitui o atributo `name no envio dos dados.

No Flask:

```
from flask import Flask, request

app = Flask(__name__)


@app.route("/cadastro", methods=["GET", "POST"])
def cadastro():
    if request.method == "POST":
        nome = request.form.get("nome", "")
        return f"Nome recebido: {nome}"

    return "Envie o formulário de cadastro."
```

Analisando os elementos:

- `methods=["GET", "POST"]`: permite que a rota aceite os dois métodos.
    
- `request.method == "POST"`: verifica se a requisição atual utiliza `POST`.
    
- `request.form`: contém os campos enviados como dados de formulário compatíveis.
    
- `request.form.get("nome", "")`: obtém o campo `nome`; se ele não existir, retorna uma string vazia.
    

O formulário será exibido apenas se a aplicação tiver uma página HTML que o apresente. O exemplo acima retorna uma mensagem textual no `GET`; ele demonstra o processamento no servidor, mas ainda não renderiza o formulário HTML.

**Uma distinção importante:** utilizar `POST` não torna os dados automaticamente seguros ou secretos. A validação continua necessária, e a comunicação deve utilizar HTTPS quando houver dados sensíveis.

### 3.5. Dados JSON: `request.json`

APIs frequentemente recebem dados em JSON, um formato de texto utilizado para representar estruturas como objetos, listas, strings, números e valores booleanos.

Um cliente poderia enviar:

```
{
    "nome": "Maria",
    "idade": 25
}
```

No Flask, podemos acessar o conteúdo JSON da requisição.

```
from flask import Flask, request

app = Flask(__name__)


@app.route("/api/usuarios", methods=["POST"])
def criar_usuario():
    dados = request.json

    if not isinstance(dados, dict):
        return {"erro": "Era esperado um objeto JSON"}, 400

    nome = dados.get("nome", "")

    if not nome:
        return {"erro": "O campo nome é obrigatório"}, 400

    return {
        "mensagem": "Dados recebidos",
        "nome": nome
    }, 201
```

**Análise do código**

`dados = request.json`

Obtém os dados JSON interpretados pelo Flask a partir da requisição. O cliente deve enviar um corpo JSON válido e utilizar o tipo de conteúdo apropriado, normalmente `Content-Type: application/json`.

`isinstance(dados, dict)`

Verifica se o resultado é um dicionário Python. Isso é importante porque um documento JSON válido pode representar outros tipos, como uma lista ou um número, e não necessariamente um objeto.

`dados.get("nome", "")`

Obtém o campo `nome` ou retorna uma string vazia quando ele não existe.

`return {"erro": "O campo nome é obrigatório"}, 400`

Retorna um dicionário que o Flask serializa como JSON e define o código de status HTTP `400 Bad Request`.

`return {...}, 201`

Retorna um objeto JSON com o status `201 Created`, apropriado quando uma operação cria um recurso.

**Observação sobre erros de JSON:** dependendo da versão do Flask e da forma de acesso, um corpo inválido ou uma requisição com tipo de conteúdo incorreto pode resultar em um erro HTTP, em vez de simplesmente fornecer um dicionário vazio. Para APIs que precisam tratar esses casos de forma específica, utilizaremos os métodos de leitura JSON com tratamento explícito de erros.

### 3.6. Uploads de arquivos: `request.files`

Quando um formulário envia arquivos utilizando `multipart/form-data`, o Flask disponibiliza os arquivos recebidos por meio de `request.files`.

Exemplo de formulário HTML:

```
<form action="/upload" method="POST" enctype="multipart/form-data">
    <input type="file" name="arquivo">
    <button type="submit">Enviar arquivo</button>
</form>
```

O atributo `enctype="multipart/form-data"` permite o envio de arquivos no formulário. O campo utiliza `name="arquivo"` para que o servidor consiga identificá-lo.

No Flask:

```
from flask import Flask, request

app = Flask(__name__)


@app.route("/upload", methods=["POST"])
def upload():
    arquivo = request.files.get("arquivo")

    if arquivo is None or arquivo.filename == "":
        return {"erro": "Nenhum arquivo foi selecionado"}, 400

    return {"mensagem": "Arquivo recebido"}, 200
```

**O que acontece aqui?**

- `request.files`: coleção dos arquivos recebidos.
    
- `.get("arquivo")`: tenta obter o arquivo enviado no campo chamado `arquivo`.
    
- `arquivo.filename`: nome informado para o arquivo.
    

Esse exemplo apenas verifica se um arquivo foi recebido. Ele não salva o arquivo no disco.

Em aplicações reais, um upload precisa de validação de tamanho, tipo e conteúdo, além de uma estratégia segura para armazenamento e nomes de arquivo. O nome fornecido pelo cliente não deve ser considerado confiável.

### 3.7. Construindo respostas com `jsonify()`

O Flask possui a função `jsonify()`, utilizada para criar respostas JSON.

```
from flask import Flask, jsonify

app = Flask(__name__)


@app.route("/api/status")
def status():
    return jsonify({
        "aplicacao": "Flask",
        "online": True
    })
```

A função `jsonify()` recebe valores que possam ser serializados em JSON e constrói uma resposta HTTP com o tipo de conteúdo apropriado.

No exemplo, o navegador ou cliente HTTP receberá um conteúdo semelhante a:

```
{
  "aplicacao": "Flask",
  "online": true
}
```

O valor Python `True` é representado como `true` no JSON.

Em versões atuais do Flask, retornar diretamente um dicionário ou uma lista também permite criar uma resposta JSON em muitos casos. `jsonify()` continua útil quando queremos construir explicitamente uma resposta JSON ou personalizar a resposta com outros recursos.

### 3.8. Construindo respostas com `make_response()`

A função `make_response()` permite criar ou modificar explicitamente um objeto de resposta HTTP.

Ela é útil quando precisamos configurar cabeçalhos, cookies ou outros aspectos da resposta.

```
from flask import Flask, make_response

app = Flask(__name__)


@app.route("/cabecalho")
def cabecalho():
    resposta = make_response("Resposta personalizada", 200)
    resposta.headers["X-Aplicacao"] = "Flask"

    return resposta
```

**Linha por linha**

`make_response("Resposta personalizada", 200)`

Cria um objeto de resposta com o conteúdo indicado e o status HTTP `200`.

- Primeiro argumento: conteúdo ou resposta a ser utilizada.
    
- Segundo argumento: código de status, opcional neste uso.
    

`resposta.headers["X-Aplicacao"] = "Flask"`

Adiciona um cabeçalho HTTP personalizado. O prefixo `X-` é utilizado aqui apenas como exemplo; cabeçalhos personalizados devem ter nomes e finalidades bem definidos.

`return resposta`

Devolve o objeto de resposta ao Flask, que o envia ao cliente.

### 3.9. Redirecionamentos com `redirect()`

Um redirecionamento instrui o cliente a acessar outro endereço.

```
from flask import Flask, redirect

app = Flask(__name__)


@app.route("/inicio")
def inicio():
    return redirect("/painel")


@app.route("/painel")
def painel():
    return "Você chegou ao painel"
```

Quando o cliente acessa `/inicio`, o Flask responde com um status de redirecionamento e informa o endereço de destino. O navegador normalmente faz uma nova requisição para `/painel`.

A função `redirect()` aceita como argumento a localização de destino. Ela também permite configurar o código de status HTTP, cujo padrão usual é `302`.

Redirecionar não é a mesma coisa que chamar outra função Python: trata-se de uma resposta HTTP enviada ao cliente, que então realiza outra requisição.

### 3.10. Gerando URLs com `url_for()`

Escrever caminhos manualmente em vários pontos da aplicação pode tornar o código difícil de manter. A função `url_for()` permite gerar uma URL a partir do nome de um endpoint registrado.

```
from flask import Flask, redirect, url_for

app = Flask(__name__)


@app.route("/")
def inicio():
    return redirect(url_for("painel"))


@app.route("/painel")
def painel():
    return "Página do painel"
```

Analisando:

- `url_for("painel")`: gera a URL correspondente ao endpoint `painel`.
    
- `redirect(...)`: cria uma resposta que redireciona o cliente para essa URL.
    

Por padrão, o endpoint deriva do nome da função de visualização. Se a rota utilizar um `endpoint` personalizado, `url_for()` deverá receber esse identificador.

A função também permite preencher variáveis de rota:

```
@app.route("/usuario/<int:user_id>")
def usuario(user_id):
    return f"Usuário {user_id}"


@app.route("/exemplo")
def exemplo():
    return redirect(url_for("usuario", user_id=10))
```

Nesse caso, `url_for()` gera uma URL compatível com a rota do usuário, preenchendo `user_id` com o valor `10`.

### 3.11. Códigos de status HTTP

O código de status informa ao cliente o resultado geral do processamento da requisição.

|Código|Significado comum|
|---|---|
|`200 OK`|Requisição processada com sucesso.|
|`201 Created`|Um recurso foi criado.|
|`302 Found`|Redirecionamento temporário.|
|`400 Bad Request`|Requisição inválida.|
|`401 Unauthorized`|Autenticação necessária ou inválida, conforme o contexto.|
|`403 Forbidden`|Acesso recusado.|
|`404 Not Found`|Recurso ou rota não encontrado.|
|`405 Method Not Allowed`|Método HTTP não permitido para a rota.|
|`500 Internal Server Error`|Erro interno inesperado no servidor.|

Os códigos não substituem o tratamento correto das condições da aplicação. Por exemplo, um erro de validação pode justificar `400`, enquanto uma criação bem-sucedida pode utilizar `201`.

### 3.12. Diferenças entre os principais recursos

|Recurso|Para que serve|
|---|---|
|`request.args`|Ler parâmetros da query string.|
|`request.form`|Ler campos de formulários.|
|`request.json`|Acessar dados JSON da requisição.|
|`request.files`|Acessar arquivos enviados.|
|`jsonify()`|Construir uma resposta JSON.|
|`make_response()`|Construir ou personalizar uma resposta HTTP.|
|`redirect()`|Enviar uma resposta de redirecionamento.|
|`url_for()`|Gerar uma URL a partir de um endpoint.|

Esses recursos não são intercambiáveis. Cada um resolve uma parte diferente do processamento HTTP, e a escolha depende do tipo de dado recebido ou da resposta que queremos produzir.

---
## 4. HTML, templates e formulários

Até aqui, as funções de visualização do Flask podiam retornar textos diretamente. Porém, em aplicações reais, normalmente precisamos apresentar páginas HTML, exibir dados dinâmicos e permitir que o usuário interaja com a aplicação.

Para isso, o Flask utiliza o sistema de templates do **Jinja**, integrado ao framework.

### 4.1. O que são templates?

Um template é um arquivo que contém a estrutura de uma página, geralmente HTML, com espaços reservados para inserir dados dinamicamente.

Por exemplo, uma página estática pode conter:

```
<h1>Olá, visitante!</h1>
```

Mas uma aplicação pode precisar mostrar o nome de cada usuário. Em vez de criar um HTML diferente para cada pessoa, podemos utilizar um template:

```
<h1>Olá, {{ nome }}!</h1>
```

A expressão `{{ nome }}` é interpretada pelo Jinja. Quando o Flask renderiza o template, o valor da variável é inserido no HTML resultante.

Isso permite separar responsabilidades:

- **Python:** processa dados, executa a lógica e prepara as informações.
    
- **HTML:** define a estrutura da página.
    
- **Jinja:** permite inserir dados e controlar partes do HTML.
    

### 4.2. Organizando os arquivos

Por padrão, o Flask procura templates em um diretório chamado `templates`, localizado na raiz da aplicação.

Uma estrutura simples pode ser:

```
meu_projeto/
├── app.py
└── templates/
    ├── index.html
    └── perfil.html
```

O arquivo `app.py` contém o código Python, enquanto os arquivos dentro de `templates/` contêm as páginas HTML.

Também é possível criar subdiretórios dentro de `templates/` para organizar páginas maiores.

### 4.3. Renderizando um template com `render_template()`

A função `render_template()` carrega um template, processa suas expressões Jinja e retorna o HTML renderizado como resposta.

Exemplo de `app.py`:

```
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def inicio():
    return render_template("index.html")
```

Arquivo `templates/index.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Minha aplicação</title>
</head>
<body>
    <h1>Minha primeira página Flask</h1>
    <p>Esta página foi renderizada por um template.</p>
</body>
</html>
```

#### Analisando o código Python

- `from flask import Flask, render_template`: importa a classe responsável pela aplicação e a função usada para renderizar templates.
    
- `app = Flask(__name__)`: cria a aplicação Flask.
    
- `@app.route("/")`: associa a URL `/` à função `inicio`.
    
- `def inicio()`: define a função que será executada quando a rota for acessada.
    
- `render_template("index.html")`: procura o arquivo `index.html` no diretório de templates e gera o HTML final.
    
- `return`: entrega o resultado como resposta HTTP.
    

#### Parâmetros de `render_template()`

|Parâmetro|Obrigatório?|Comportamento|
|---|---|---|
|`template_name_or_list`|Sim|Nome de um template ou lista de nomes candidatos.|
|`**context`|Não|Dados enviados ao template como variáveis.|

O primeiro argumento identifica o template. Os argumentos nomeados adicionais fornecem os dados que estarão disponíveis durante a renderização.

Exemplo:

```
return render_template(
    "index.html",
    nome="Marcos",
    idade=17
)
```

No HTML, as variáveis podem ser utilizadas assim:

```
<h1>Olá, {{ nome }}!</h1>
<p>Idade: {{ idade }}</p>
```

O resultado renderizado conterá o nome e a idade fornecidos pelo Python.

### 4.4. Exibindo dados dinâmicos

Um dos usos mais importantes dos templates é apresentar informações que mudam conforme a requisição ou os dados da aplicação.

Arquivo `app.py`:

```
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/perfil/<nome>")
def perfil(nome):
    return render_template(
        "perfil.html",
        nome=nome
    )
```

Arquivo `templates/perfil.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Perfil</title>
</head>
<body>
    <h1>Perfil de {{ nome }}</h1>
    <p>Bem-vindo à sua página de perfil.</p>
</body>
</html>
```

Ao acessar `/perfil/Marcos`, o Flask captura `Marcos` como parâmetro da rota e o envia ao template.

O Jinja substitui `{{ nome }}` pelo valor recebido. Assim, a mesma página pode exibir diferentes nomes sem precisar duplicar o arquivo HTML.

### 4.5. Variáveis, condições e loops no Jinja

Além de exibir variáveis, o Jinja permite controlar a renderização do HTML.

#### Exibindo variáveis

```
<p>Usuário: {{ nome }}</p>
<p>E-mail: {{ email }}</p>
```

As expressões entre `{{ }}` exibem valores no HTML renderizado.

#### Utilizando condições

```
{% if autenticado %}
    <p>Você está autenticado.</p>
{% else %}
    <p>Faça login para continuar.</p>
{% endif %}
```

As instruções entre `{% %}` controlam a execução de estruturas do template.

A variável `autenticado` precisa ser enviada pelo Python ou estar disponível no contexto do template.

#### Percorrendo listas

No Python:

```
@app.route("/linguagens")
def linguagens():
    nomes = ["Python", "JavaScript", "C"]
    return render_template(
        "linguagens.html",
        linguagens=nomes
    )
```

No arquivo `templates/linguagens.html`:

```
<h1>Linguagens</h1>

<ul>
    {% for linguagem in linguagens %}
        <li>{{ linguagem }}</li>
    {% endfor %}
</ul>
```

O `for` percorre os elementos da lista, gerando um elemento `<li>` para cada linguagem.

Essas estruturas pertencem à sintaxe do Jinja, não à sintaxe comum do HTML nem à do Python executado diretamente no arquivo `app.py`.

### 4.6. Criando um diretório para arquivos estáticos

Aplicações web também precisam de arquivos que não são processados como templates, como folhas de estilo CSS, JavaScript e imagens.

Por padrão, o Flask procura esses arquivos no diretório `static`.

Estrutura:

```
meu_projeto/
├── app.py
├── static/
│   ├── css/
│   │   └── estilo.css
│   └── imagens/
│       └── logo.png
└── templates/
    └── index.html
```

Para carregar um CSS no HTML, utilize `url_for()`:

```
<link
    rel="stylesheet"
    href="{{ url_for('static', filename='css/estilo.css') }}"
>
```

A função `url_for()` gera uma URL para um endpoint do Flask. Nesse caso, `static` é o endpoint padrão para servir arquivos estáticos, e `filename` identifica o caminho do arquivo dentro de `static/`.

É preferível utilizar `url_for()` em vez de escrever manualmente o caminho completo, pois isso reduz problemas quando a aplicação muda de configuração ou de localização.

### 4.7. Criando formulários HTML

Um formulário permite que o usuário envie informações à aplicação, como nome, e-mail, senha ou termos de pesquisa.

Exemplo:

```
<form action="/cadastro" method="POST">
    <label for="nome">Nome:</label>
    <input type="text" id="nome" name="nome" required>

    <button type="submit">Cadastrar</button>
</form>
```

Principais elementos:

- `<form>`: define o formulário.
    
- `action`: indica a URL que receberá os dados.
    
- `method`: define o método HTTP utilizado no envio.
    
- `<input>`: cria um campo de entrada.
    
- `name`: identifica o campo enviado na requisição.
    
- `required`: exige que o campo seja preenchido no navegador.
    
- `<button type="submit">`: envia o formulário.
    

**Atenção:** o atributo `name` é essencial para identificar os campos recebidos pelo Flask. O atributo `id`, por sua vez, é útil para associar o campo ao seu `<label>` e para referenciá-lo em CSS ou JavaScript.

O `required` ajuda na validação no navegador, mas não substitui a validação no servidor. Uma requisição pode ser construída fora do navegador e enviada diretamente à aplicação.

### 4.8. Recebendo os dados com `request.form`

Quando um formulário é enviado com `method="POST"` e os dados são codificados como um formulário HTML convencional, o Flask disponibiliza os campos por meio de `request.form`.

Exemplo completo:

```
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route("/cadastro", methods=["GET", "POST"])
def cadastro():
    if request.method == "POST":
        nome = request.form.get("nome", "").strip()

        if not nome:
            return "O nome é obrigatório.", 400

        return f"Cadastro recebido para {nome}."

    return render_template("cadastro.html")
```

Arquivo `templates/cadastro.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Cadastro</title>
</head>
<body>
    <h1>Cadastro</h1>

    <form action="{{ url_for('cadastro') }}" method="POST">
        <label for="nome">Nome:</label>
        <input type="text" id="nome" name="nome" required>

        <button type="submit">Enviar</button>
    </form>
</body>
</html>
```

#### Entendendo o fluxo

1. O navegador acessa `/cadastro` com uma requisição `GET`.
    
2. Como o método não é `POST`, a função renderiza `cadastro.html`.
    
3. O usuário preenche o campo `nome` e envia o formulário.
    
4. O navegador envia uma requisição `POST` para `/cadastro`.
    
5. O Flask identifica o método por meio de `request.method`.
    
6. `request.form.get("nome", "")` tenta obter o campo chamado `nome`. Se ele não existir, retorna uma string vazia.
    
7. `.strip()` remove espaços em branco do início e do fim do texto.
    
8. Se o nome estiver vazio, a aplicação responde com o status HTTP `400`.
    
9. Caso contrário, a aplicação retorna uma mensagem de confirmação.
    

#### Por que utilizar `request.form.get()`?

Existem duas formas comuns de acessar um campo:

```
nome = request.form["nome"]
```

```
nome = request.form.get("nome")
```

A primeira forma pode gerar um erro `BadRequestKeyError` se o campo não existir. A segunda retorna `None` quando a chave não existe e nenhum valor padrão é fornecido.

Também é possível definir um valor padrão:

```
nome = request.form.get("nome", "")
```

Nesse caso, uma chave ausente resulta em uma string vazia.

O valor padrão evita o erro por chave ausente, mas não significa que o dado seja válido. A aplicação ainda precisa verificar o conteúdo recebido.

### 4.9. GET e POST em formulários

O método HTTP escolhido muda a maneira como os dados são enviados e interpretados.

|Característica|GET|POST|
|---|---|---|
|Uso comum|Pesquisas e filtros|Cadastros e envio de dados|
|Dados de formulário|Normalmente na URL, como parâmetros de consulta|Normalmente no corpo da requisição|
|`request.args`|Lê parâmetros da URL|Também pode ler parâmetros presentes na URL|
|`request.form`|Geralmente não contém os campos enviados por um formulário GET|Lê campos de formulários convencionais enviados no corpo|
|Sensibilidade dos dados|Valores podem aparecer na URL e no histórico|Os dados não aparecem automaticamente na URL, mas isso não os torna seguros por si só|

Um formulário GET pode ser útil para uma busca:

```
<form action="{{ url_for('buscar') }}" method="GET">
    <input type="text" name="q" placeholder="Pesquisar">
    <button type="submit">Buscar</button>
</form>
```

No Flask:

```
@app.route("/buscar")
def buscar():
    termo = request.args.get("q", "")
    return f"Você pesquisou por: {termo}"
```

Ao pesquisar `Flask`, o navegador poderá acessar uma URL semelhante a `/buscar?q=Flask`.

Já formulários POST são normalmente utilizados para operações que enviam dados para processamento, como criação ou alteração de registros. O método HTTP, isoladamente, não fornece autenticação, autorização ou proteção contra falsificação de requisições.

### 4.10. Redirecionando após um POST

Depois de processar um formulário, uma boa prática comum é redirecionar o navegador para outra rota. Esse padrão é conhecido como **Post/Redirect/Get (PRG)**.

Exemplo:

```
from flask import Flask, redirect, render_template, request, url_for

app = Flask(__name__)

@app.route("/cadastro", methods=["GET", "POST"])
def cadastro():
    if request.method == "POST":
        nome = request.form.get("nome", "").strip()

        if not nome:
            return "O nome é obrigatório.", 400

        # Aqui poderia ocorrer a gravação do cadastro no banco.
        return redirect(url_for("sucesso"))

    return render_template("cadastro.html")


@app.route("/sucesso")
def sucesso():
    return "Cadastro processado com sucesso."
```

Quando o formulário é enviado corretamente, o Flask responde com um redirecionamento para `/sucesso`. O navegador então faz uma nova requisição GET para essa rota.

Isso reduz o risco de o usuário reenviar acidentalmente o mesmo formulário ao atualizar a página de confirmação. Porém, não impede duplicações em todos os cenários: operações críticas podem precisar de validação de duplicidade ou mecanismos de idempotência.

#### Funções utilizadas

- `redirect(location, code=302)`: cria uma resposta de redirecionamento. O destino é obrigatório; o código HTTP é opcional e, por padrão, é `302`.
    
- `url_for(endpoint, **values)`: gera a URL de um endpoint. O nome do endpoint é obrigatório; os demais valores são usados para preencher parâmetros de rota ou gerar parâmetros de consulta quando aplicável.
    

No exemplo, `url_for("sucesso")` gera a URL da função associada ao endpoint `sucesso`, e `redirect()` instrui o navegador a acessá-la.

### 4.11. Erros comuns com templates e formulários

`**TemplateNotFound**`

O Flask não encontrou o template solicitado. Verifique se o arquivo existe no diretório `templates`, se o nome está correto e se a estrutura de diretórios corresponde ao esperado.

`**BuildError**` **ao utilizar** `**url_for()**`

O endpoint informado não foi encontrado ou os parâmetros necessários para gerar a URL não foram fornecidos. Por padrão, o endpoint de uma rota costuma ser o nome da função associada a ela.

**O campo chega vazio ao Flask**

Verifique se o elemento HTML possui o atributo `name`, se o formulário está enviando o campo correto e se o código está consultando a fonte adequada, como `request.form` para um formulário POST convencional.

**Erro** `**405 Method Not Allowed**`

A rota foi acessada com um método HTTP que não está autorizado na configuração dela. Se a mesma rota deve exibir um formulário e processar seu envio, configure os métodos necessários, como `GET` e `POST`.

**O HTML não exibe o valor esperado**

Confirme se o Python enviou a variável ao `render_template()` com o mesmo nome utilizado no template. Por exemplo, `nome="Marcos"` disponibiliza a variável `nome`, não uma variável chamada `usuario`.

**Dados inválidos são aceitos**

Valide os dados no servidor. A validação do HTML melhora a experiência do usuário, mas não deve ser considerada uma barreira de segurança.

### 4.12. Segurança básica ao renderizar e receber dados

O Flask utiliza o mecanismo de autoescape do Jinja nos templates HTML convencionais. Isso ajuda a evitar que valores inseridos em expressões como `{{ nome }}` sejam interpretados como HTML ou JavaScript executável.

Ainda assim, há cuidados importantes:

- Não utilize `|safe` para dados não confiáveis sem entender suas consequências.
    
- Valide os dados recebidos no servidor, independentemente das validações HTML.
    
- Não armazene senhas em texto puro.
    
- Não considere o método POST um mecanismo de criptografia; utilize HTTPS em produção.
    
- Em formulários que alteram dados ou executam ações autenticadas, implemente proteção contra CSRF quando aplicável.
    
- A validação dos dados não substitui a autenticação nem a autorização.
    
- Evite concatenar entradas do usuário em consultas SQL; utilize mecanismos de parametrização ou ORM apropriados.
    

Esses cuidados serão aprofundados nas partes de banco de dados, autenticação e segurança web.

---
## 5. Estrutura da aplicação, Blueprints e Application Factory

Nos primeiros exemplos, toda a aplicação Flask ficou concentrada em um único arquivo, geralmente `app.py`. Essa abordagem é útil para aprender, mas pode dificultar a manutenção de projetos maiores.

Imagine uma aplicação que possui:

- Autenticação e cadastro de usuários.
    
- Painel administrativo.
    
- Gerenciamento de produtos.
    
- API para comunicação com outras aplicações.
    
- Integração com banco de dados.
    

Colocar todas as rotas, configurações e regras de negócio em um único arquivo torna o código difícil de navegar e testar.

O Flask oferece recursos que ajudam a organizar a aplicação. Entre eles estão os **Blueprints** e o padrão **Application Factory**.

### 5.1. Uma aplicação pequena em um único arquivo

Considere este exemplo:

```
from flask import Flask

app = Flask(__name__)


@app.route("/")
def inicio():
    return "Página inicial"


@app.route("/sobre")
def sobre():
    return "Sobre a aplicação"


@app.route("/contato")
def contato():
    return "Entre em contato"
```

O código funciona, mas cada nova funcionalidade aumenta o tamanho do arquivo.

O problema não é ter um arquivo único em si. Para aplicações pequenas, isso pode ser perfeitamente adequado. A necessidade de separar os módulos aparece quando a quantidade de funcionalidades começa a dificultar a leitura, a manutenção e os testes.

### 5.2. Organizando os arquivos por responsabilidade

Uma estrutura possível para uma aplicação maior é:

```
meu_projeto/
├── app/
│   ├── __init__.py
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── principal.py
│   │   └── usuarios.py
│   ├── templates/
│   └── static/
├── config.py
└── run.py
```

Nessa estrutura:

- `app/__init__.py`: pode conter a função que cria e configura a aplicação.
    
- `app/routes/`: agrupa as rotas por funcionalidade.
    
- `app/templates/`: armazena os templates HTML.
    
- `app/static/`: armazena CSS, JavaScript e imagens.
    
- `config.py`: concentra configurações, quando essa separação é útil.
    
- `run.py`: cria ou importa a aplicação para iniciá-la.
    

Essa é uma organização possível, não uma estrutura obrigatória imposta pelo Flask. Projetos diferentes podem adotar estruturas distintas.

### 5.3. O que são Blueprints?

Um **Blueprint** é um recurso do Flask que permite agrupar rotas e outros componentes relacionados para registrá-los em uma aplicação.

Ele ajuda a separar funcionalidades sem precisar transformar cada módulo em uma aplicação Flask independente.

Por exemplo, podemos criar um Blueprint para as páginas principais e outro para os usuários.

Arquivo `app/routes/principal.py`:

```
from flask import Blueprint

principal_bp = Blueprint("principal", __name__)


@principal_bp.route("/")
def inicio():
    return "Página inicial"


@principal_bp.route("/sobre")
def sobre():
    return "Sobre a aplicação"
```

Arquivo `app/routes/usuarios.py`:

```
from flask import Blueprint

usuarios_bp = Blueprint("usuarios", __name__)


@usuarios_bp.route("/usuarios")
def listar_usuarios():
    return "Lista de usuários"
```

Observe esta linha:

```
principal_bp = Blueprint("principal", __name__)
```

Ela cria um Blueprint chamado `principal`.

O objeto `principal_bp` passa a funcionar como um ponto de registro para as rotas daquele módulo. Em vez de utilizar `@app.route()`, utilizamos `@principal_bp.route()`.

O Blueprint ainda precisa ser registrado em uma aplicação Flask para que suas rotas sejam disponibilizadas.

### 5.4. Registrando os Blueprints

No arquivo `app/__init__.py`:

```
from flask import Flask

from app.routes.principal import principal_bp
from app.routes.usuarios import usuarios_bp


def criar_app():
    app = Flask(__name__)

    app.register_blueprint(principal_bp)
    app.register_blueprint(usuarios_bp)

    return app
```

No arquivo `run.py`:

```
from app import criar_app

app = criar_app()
```

Ao executar a aplicação, o Flask recebe uma instância que já possui os Blueprints registrados.

O fluxo é:

1. `criar_app()` cria a aplicação Flask.
    
2. Os módulos de rotas disponibilizam seus Blueprints.
    
3. `register_blueprint()` registra cada Blueprint na aplicação.
    
4. A aplicação passa a conhecer as rotas registradas.
    
5. O servidor pode atender às requisições normalmente.
    

A função `register_blueprint()` é um método da aplicação Flask. Seu argumento principal é o Blueprint que será registrado.

### 5.5. Adicionando prefixos às rotas

Um Blueprint pode receber um prefixo de URL quando é registrado. Isso é útil para agrupar rotas relacionadas.

Exemplo:

```
app.register_blueprint(
    usuarios_bp,
    url_prefix="/usuarios"
)
```

Agora considere que o Blueprint foi definido assim:

```
from flask import Blueprint

usuarios_bp = Blueprint("usuarios", __name__)


@usuarios_bp.route("/")
def listar_usuarios():
    return "Lista de usuários"


@usuarios_bp.route("/novo")
def novo_usuario():
    return "Novo usuário"
```

Com o prefixo `/usuarios`, as rotas finais serão:

|Rota definida no Blueprint|URL final|
|---|---|
|`/`|`/usuarios/`|
|`/novo`|`/usuarios/novo`|

O prefixo é aplicado durante o registro. Portanto, a rota declarada como `/novo` não precisa repetir `/usuarios`.

Essa técnica ajuda a manter as rotas agrupadas e evita repetir o mesmo trecho de URL em todas as declarações.

### 5.6. Entendendo o nome dos endpoints

O endpoint é o identificador utilizado pelo Flask para referenciar uma rota. Por padrão, o endpoint de uma função de visualização corresponde ao nome dessa função, com o nome do Blueprint incluído quando a rota pertence a um Blueprint.

Exemplo:

```
usuarios_bp = Blueprint("usuarios", __name__)


@usuarios_bp.route("/novo")
def novo_usuario():
    return "Novo usuário"
```

O endpoint dessa rota será:

```
usuarios.novo_usuario
```

Por isso, podemos utilizar `url_for()` assim:

```
from flask import url_for

url = url_for("usuarios.novo_usuario")
```

O resultado será a URL associada ao endpoint, considerando a configuração de registro do Blueprint.

Ao utilizar `url_for()` dentro de um template:

```
<a href="{{ url_for('usuarios.novo_usuario') }}">
    Cadastrar usuário
</a>
```

o Flask gera o caminho correspondente à rota registrada.

Se um endpoint pertencer a um Blueprint, normalmente é necessário incluir o nome do Blueprint e o nome da função separados por um ponto.

### 5.7. O que é Application Factory?

**Application Factory** é um padrão em que a aplicação Flask é criada por uma função, em vez de depender exclusivamente de uma instância global criada diretamente no início do módulo.

Uma função de criação pode ser chamada de `create_app()` ou `criar_app()`. O nome é uma escolha do desenvolvedor, mas `create_app` é uma convenção bastante utilizada no ecossistema Flask.

Exemplo:

```
from flask import Flask


def create_app():
    app = Flask(__name__)

    @app.route("/")
    def inicio():
        return "Aplicação criada por uma factory"

    return app
```

A função executa três operações principais:

1. Cria a instância Flask.
    
2. Configura a aplicação e registra seus componentes.
    
3. Retorna a instância pronta para ser utilizada.
    

O benefício é poder criar aplicações configuradas de maneiras diferentes, o que é especialmente útil em testes e em projetos que possuem configurações distintas para desenvolvimento e produção.

### 5.8. Separando a configuração

A aplicação pode receber configurações por meio do objeto `app.config`.

Exemplo de `config.py`:

```
class Config:
    SECRET_KEY = "substitua-por-uma-chave-segura"
    TESTING = False


class TestConfig(Config):
    TESTING = True
```

A factory pode carregar uma dessas configurações:

```
from flask import Flask


def create_app(testing=False):
    app = Flask(__name__)

    if testing:
        app.config.from_object("config.TestConfig")
    else:
        app.config.from_object("config.Config")

    return app
```

A chamada `from_object()` carrega valores de configuração de um objeto Python identificado pela string fornecida.

Nesse exemplo, o parâmetro `testing` da função `create_app()` é um parâmetro Python comum. Ele não é um parâmetro especial do Flask.

A opção `TESTING` também é uma configuração do Flask. Ela informa que a aplicação está sendo executada em modo de teste, o que pode alterar o tratamento de exceções e o comportamento do cliente de testes.

**Importante:** a chave secreta acima é apenas um marcador de exemplo. Não utilize uma chave fixa e previsível em produção. Use uma chave forte, mantida fora do código-fonte e protegida de acordo com o ambiente de execução.

### 5.9. Registrando Blueprints dentro da factory

Podemos combinar os dois recursos: a factory cria a aplicação, carrega as configurações e registra os Blueprints.

Arquivo `app/__init__.py`:

```
from flask import Flask

from app.routes.principal import principal_bp
from app.routes.usuarios import usuarios_bp


def create_app(testing=False):
    app = Flask(__name__)

    if testing:
        app.config["TESTING"] = True

    app.register_blueprint(principal_bp)
    app.register_blueprint(
        usuarios_bp,
        url_prefix="/usuarios"
    )

    return app
```

Arquivo `run.py`:

```
from app import create_app

app = create_app()
```

Nesse exemplo, o Blueprint `principal_bp` mantém suas rotas sem um prefixo adicional, enquanto `usuarios_bp` recebe o prefixo `/usuarios`.

A factory também pode ser o local onde são inicializadas extensões, registradas páginas de erro e configurados recursos compartilhados. A ordem exata depende das dependências e da arquitetura escolhida.

### 5.10. Introdução às extensões e ao padrão `init_app()`

Algumas funcionalidades do Flask são fornecidas por extensões externas, como Flask-SQLAlchemy, Flask-Login e Flask-Migrate.

Em uma aplicação que utiliza a factory, é comum inicializar uma extensão separadamente e associá-la à aplicação durante a criação.

Um exemplo ilustrativo:

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()


def create_app():
    app = Flask(__name__)

    app.config["SQLALCHEMY_DATABASE_URI"] = (
        "sqlite:///aplicacao.db"
    )

    db.init_app(app)

    return app
```

Nesse exemplo:

- `SQLAlchemy()` cria o objeto da extensão sem associá-lo imediatamente a uma aplicação específica.
    
- `app.config[...]` define a configuração usada pela extensão.
    
- `db.init_app(app)` inicializa a extensão para aquela instância Flask.
    

Esse padrão facilita a criação de aplicações distintas, por exemplo, uma para testes e outra para desenvolvimento.

O exemplo apenas mostra a integração com a factory. O funcionamento do ORM, a configuração do banco e as operações com modelos serão estudados na parte específica de Flask-SQLAlchemy.

### 5.11. Executando a aplicação

Se o arquivo `run.py` contém:

```
from app import create_app

app = create_app()
```

você pode iniciar o servidor de desenvolvimento com:

```
flask --app run run --debug
```

O comando informa ao Flask que a aplicação está no módulo `run` e que a instância a ser utilizada se chama `app`. O segundo `run` inicia o servidor de desenvolvimento, enquanto `--debug` ativa o modo de depuração.

Durante o desenvolvimento, o modo de depuração facilita a identificação de erros. Não o habilite em um servidor de produção acessível publicamente.

Também é possível apontar o comando para uma factory, conforme a estrutura do projeto:

```
flask --app 'app:create_app()' run --debug
```

Nesse caso, o Flask importa o módulo `app` e chama a factory `create_app()` para obter a aplicação.

Escolha o comando de acordo com a estrutura real do projeto. Não é necessário usar os dois formatos ao mesmo tempo.

### 5.12. Erros comuns na organização do Flask

`**ModuleNotFoundError**` **ao importar um módulo**

O Python não conseguiu localizar o pacote ou módulo indicado. Verifique a estrutura dos diretórios, a existência de `__init__.py` quando necessário e o diretório a partir do qual a aplicação está sendo executada.

`**NameError**` **ao registrar um Blueprint**

O objeto utilizado em `register_blueprint()` não está definido naquele escopo. Confirme se o Blueprint foi criado e importado corretamente.

`**BuildError**` **ao utilizar** `**url_for()**`

O endpoint informado não corresponde a uma rota registrada ou faltam parâmetros de rota. Para rotas em Blueprints, verifique o prefixo do endpoint, como `usuarios.novo_usuario`.

**A rota não aparece na aplicação**

Confira se o Blueprint foi registrado na instância que está sendo executada. Criar um Blueprint, por si só, não o adiciona automaticamente à aplicação.

**Importações circulares**

Esse problema pode acontecer quando módulos importam uns aos outros de forma que um depende da inicialização incompleta do outro. Uma factory bem organizada e uma separação clara das responsabilidades ajudam a reduzir esse risco.

**A extensão não encontra a aplicação**

Em projetos com Application Factory, verifique se a extensão foi inicializada para a instância correta, seguindo o padrão de integração recomendado por ela, como `init_app(app)` quando aplicável.

### 5.13. Boas práticas de organização

- Comece com a estrutura mais simples que atende às necessidades do projeto.
    
- Separe módulos quando as funcionalidades começarem a crescer.
    
- Utilize Blueprints para agrupar rotas relacionadas.
    
- Utilize uma factory quando precisar criar aplicações com configurações distintas ou facilitar os testes.
    
- Mantenha configurações sensíveis fora do código-fonte.
    
- Evite importar a aplicação global de vários módulos quando uma arquitetura baseada em factory puder reduzir dependências circulares.
    
- Não confunda Blueprint com uma aplicação Flask independente: ele agrupa componentes que serão registrados em uma aplicação.
    
- Não crie diretórios e camadas desnecessários apenas para seguir uma estrutura considerada ideal; a organização deve facilitar a manutenção do projeto.
---
## 6. Banco de dados e ORM com Flask-SQLAlchemy

Até agora, os exemplos do Flask retornavam textos e páginas HTML. Porém, quando precisamos cadastrar usuários, armazenar produtos ou manter informações entre diferentes acessos, precisamos de um mecanismo de persistência.

É aí que entra o banco de dados.

### 6.1. O que é um banco de dados?

Um banco de dados é um sistema utilizado para armazenar, organizar, consultar e modificar informações.

Imagine um sistema de cadastro de usuários. Cada usuário possui informações como nome, e-mail e idade. Em vez de manter esses dados apenas em variáveis Python, podemos armazená-los em um banco de dados para recuperá-los posteriormente.

Um banco relacional organiza as informações em tabelas, compostas por colunas e registros.

Exemplo de uma tabela `usuarios`:

|id|nome|email|
|---|---|---|
|1|Ana|[ana@example.com](mailto:ana@example.com)|
|2|Pedro|[pedro@example.com](mailto:pedro@example.com)|
|3|Marcos|[marcos@example.com](mailto:marcos@example.com)|

Nesse exemplo:

- **Tabela:** `usuarios`.
    
- **Colunas:** `id`, `nome` e `email`.
    
- **Registros:** cada linha que representa um usuário.
    
- **Chave primária:** `id`, utilizada para identificar cada registro de maneira única.
    

O banco de dados pode continuar armazenando essas informações mesmo depois que o servidor Flask for encerrado.

### 6.2. O que é SQL?

SQL significa _Structured Query Language_. É uma linguagem utilizada para trabalhar com bancos de dados relacionais.

Por exemplo, uma consulta SQL para buscar todos os usuários seria:

```
SELECT * FROM usuarios;
```

Para inserir um registro:

```
INSERT INTO usuarios (nome, email)
VALUES ('Ana', 'ana@example.com');
```

Para atualizar um registro:

```
UPDATE usuarios
SET nome = 'Ana Silva'
WHERE id = 1;
```

Para excluir um registro:

```
DELETE FROM usuarios
WHERE id = 1;
```

Esses comandos são executados pelo sistema de banco de dados. O Flask não é um banco de dados e não executa essas operações sozinho: ele utiliza bibliotecas para se comunicar com o banco.

**Atenção:** o `WHERE` limita quais registros serão atualizados ou excluídos. Sem uma condição adequada, um `UPDATE` ou `DELETE` pode afetar todos os registros da tabela.

### 6.3. O que é um ORM?

ORM significa _Object-Relational Mapping_, ou mapeamento objeto-relacional.

Um ORM permite representar registros do banco de dados como objetos Python. Em vez de escrever manualmente cada consulta SQL, podemos trabalhar com classes, atributos e métodos fornecidos pela biblioteca.

Por exemplo, um usuário pode ser representado assim:

```
usuario = Usuario(
    nome="Ana",
    email="ana@example.com"
)
```

A classe `Usuario` representa o modelo dos dados. Quando esse objeto é persistido no banco, seus atributos correspondem às colunas configuradas no modelo.

O ORM ajuda a integrar a lógica da aplicação com o banco de dados, mas não elimina a necessidade de compreender SQL, relacionamentos, índices, transações e segurança.

### 6.4. O que é Flask-SQLAlchemy?

O Flask-SQLAlchemy é uma extensão do Flask que integra o SQLAlchemy à aplicação.

O SQLAlchemy é a biblioteca que fornece o ORM e ferramentas de acesso a bancos de dados. O Flask-SQLAlchemy adapta essa integração ao contexto de uma aplicação Flask, facilitando a configuração e o uso da sessão de banco de dados.

As responsabilidades ficam divididas assim:

- **Flask:** recebe requisições HTTP e organiza a aplicação web.
    
- **Flask-SQLAlchemy:** integra o acesso ao banco com a aplicação Flask.
    
- **SQLAlchemy:** fornece o ORM e os mecanismos de comunicação com o banco.
    
- **Sistema de banco de dados:** armazena e consulta os dados efetivamente.
    

O Flask-SQLAlchemy é uma extensão externa. Ele não faz parte do núcleo do Flask e precisa ser instalado separadamente.

### 6.5. Instalando as dependências

Ative o ambiente virtual do projeto e execute:

```
python -m pip install Flask Flask-SQLAlchemy
```

Para verificar se a extensão está disponível:

```
python -m pip show Flask-SQLAlchemy
```

Em um projeto existente, mantenha as versões compatíveis com as demais dependências. Depois de instalar as bibliotecas, é uma boa prática registrar as dependências do projeto em um arquivo como `requirements.txt`.

Por exemplo:

```
python -m pip freeze > requirements.txt
```

Esse comando registra as versões dos pacotes instalados no ambiente atual. Em projetos maiores, é importante garantir que o ambiente não contenha dependências irrelevantes antes de gerar o arquivo.

### 6.6. Configurando o banco de dados

Para aprender, podemos utilizar SQLite. Ele armazena o banco em um arquivo e não exige a execução de um servidor de banco de dados separado.

Exemplo de estrutura:

```
meu_projeto/
├── app.py
└── instance/
```

O diretório `instance` é um local convencional do Flask para dados específicos da instância da aplicação, como um banco SQLite local.

Exemplo de `app.py`:

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

app.config["SQLALCHEMY_DATABASE_URI"] = (
    "sqlite:///aplicacao.db"
)

db = SQLAlchemy(app)
```

Vamos analisar as partes importantes:

- `Flask(__name__)`: cria a aplicação Flask.
    
- `SQLALCHEMY_DATABASE_URI`: define a URI de conexão utilizada pela extensão.
    
- `sqlite:///aplicacao.db`: configura uma conexão com SQLite usando um caminho relativo ao diretório de instância da aplicação no padrão atual do Flask-SQLAlchemy.
    
- `SQLAlchemy(app)`: cria a extensão e a associa à aplicação.
    

Com essa configuração, o banco SQLite será criado no local correspondente à URI quando o mecanismo de criação das tabelas ou outra operação que precise do banco for executado. A simples configuração da URI não cria todas as tabelas automaticamente.

O formato da URI depende do sistema de banco de dados utilizado. PostgreSQL e MySQL, por exemplo, utilizam configurações de conexão diferentes.

### 6.7. O que são modelos?

Um modelo é uma classe Python que descreve a estrutura de uma entidade persistida no banco de dados.

Considere o modelo de usuário:

```
class Usuario(db.Model):
    id = db.Column(
        db.Integer,
        primary_key=True
    )

    nome = db.Column(
        db.String(100),
        nullable=False
    )

    email = db.Column(
        db.String(150),
        nullable=False
    )
```

Nesse exemplo, `Usuario` representa a tabela de usuários.

#### Analisando o modelo

`**class Usuario(db.Model):**`

Define uma classe de modelo baseada em `db.Model`. A extensão utiliza essa classe para mapear os objetos Python à estrutura relacional.

`**id = db.Column(db.Integer, primary_key=True)**`

Cria uma coluna inteira chamada `id` e a define como chave primária.

O banco utiliza essa chave para identificar cada registro. Em uma configuração convencional do SQLite, o valor da chave primária inteira pode ser gerado automaticamente quando o registro é inserido sem um identificador explícito.

`**nome = db.Column(db.String(100), nullable=False)**`

Cria uma coluna textual com limite declarado de 100 caracteres no tipo SQLAlchemy e impede que a coluna aceite `NULL`.

`**email = db.Column(db.String(150), nullable=False)**`

Define uma coluna textual para armazenar o e-mail e exige um valor não nulo.

`nullable=False` não garante que o texto seja um e-mail válido. Essa validação precisa ser realizada pela aplicação ou por mecanismos adicionais.

### 6.8. Criando as tabelas

Definir uma classe de modelo não significa que a tabela já exista no banco.

Para criar as tabelas correspondentes aos modelos registrados, podemos utilizar `db.create_all()` dentro do contexto da aplicação.

Exemplo didático completo:

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

app.config["SQLALCHEMY_DATABASE_URI"] = (
    "sqlite:///aplicacao.db"
)

db = SQLAlchemy(app)


class Usuario(db.Model):
    id = db.Column(
        db.Integer,
        primary_key=True
    )

    nome = db.Column(
        db.String(100),
        nullable=False
    )

    email = db.Column(
        db.String(150),
        nullable=False
    )


with app.app_context():
    db.create_all()
```

#### O que é o contexto da aplicação?

O contexto da aplicação disponibiliza a instância Flask ativa para recursos que dependem dela.

Em uma aplicação Flask-SQLAlchemy, operações como acessar a sessão do banco precisam estar associadas ao contexto correto da aplicação.

Por isso, no exemplo, `with app.app_context():` estabelece esse contexto antes da chamada a `db.create_all()`.

#### O que `create_all()` faz?

O método cria as tabelas mapeadas que ainda não existem no banco conectado.

Ele **não é um sistema de migrações** e não atualiza automaticamente a estrutura de tabelas já existentes para refletir todas as mudanças feitas nos modelos. Para controlar alterações de esquema ao longo do tempo, utilizamos ferramentas de migração, como Flask-Migrate e Alembic.

### 6.9. Inserindo registros

Depois de configurar o banco e definir o modelo, podemos criar objetos Python e adicioná-los à sessão.

Exemplo:

```
with app.app_context():
    usuario = Usuario(
        nome="Ana",
        email="ana@example.com"
    )

    db.session.add(usuario)
    db.session.commit()
```

O que acontece:

1. `Usuario(...)` cria um objeto Python.
    
2. `db.session.add(usuario)` adiciona o objeto à sessão, preparando-o para persistência.
    
3. `db.session.commit()` confirma a transação e persiste as alterações no banco.
    

A sessão mantém o controle das alterações realizadas nos objetos durante a transação. Adicionar um objeto à sessão não significa necessariamente que ele já foi gravado de forma definitiva.

Se a transação falhar, o `commit()` pode lançar uma exceção. Em aplicações reais, o código deve tratar falhas e, quando necessário, executar `db.session.rollback()` para reverter a transação pendente.

### 6.10. Consultando registros

Podemos utilizar consultas do SQLAlchemy para recuperar objetos do banco.

Para obter todos os usuários:

```
usuarios = db.session.execute(
    db.select(Usuario)
).scalars().all()
```

Analisando:

- `db.select(Usuario)`: constrói uma consulta que seleciona registros do modelo `Usuario`.
    
- `db.session.execute(...)`: executa a consulta por meio da sessão.
    
- `.scalars()`: extrai os objetos correspondentes aos resultados escalares da consulta.
    
- `.all()`: obtém todos os resultados em uma lista.
    

Para percorrer os resultados:

```
for usuario in usuarios:
    print(usuario.nome, usuario.email)
```

Cada elemento é um objeto `Usuario`, por isso podemos acessar os atributos `nome` e `email`.

#### Buscando um registro pelo identificador

```
usuario = db.session.get(Usuario, 1)

if usuario is not None:
    print(usuario.nome)
else:
    print("Usuário não encontrado")
```

`db.session.get(Usuario, 1)` busca a entidade cuja chave primária é `1`. Se não existir um registro correspondente, o resultado será `None`.

Essa forma é apropriada quando conhecemos a chave primária e queremos recuperar uma entidade por ela.

#### Filtrando por um campo

```
usuarios = db.session.execute(
    db.select(Usuario).where(
        Usuario.nome == "Ana"
    )
).scalars().all()
```

O método `.where()` adiciona uma condição à consulta. Nesse caso, selecionamos os usuários cujo nome corresponde a `"Ana"`.

Observe que `Usuario.nome == "Ana"` é uma expressão do SQLAlchemy. Ela não executa uma comparação Python comum entre o atributo de uma instância e uma string: constrói uma condição que será interpretada na consulta SQL.

### 6.11. Atualizando registros

Para atualizar um usuário, podemos recuperar o objeto, modificar seus atributos e confirmar a transação.

```
with app.app_context():
    usuario = db.session.get(Usuario, 1)

    if usuario is not None:
        usuario.nome = "Ana Silva"
        db.session.commit()
```

O fluxo é:

1. Recuperar o usuário pelo identificador.
    
2. Verificar se o registro existe.
    
3. Modificar o atributo `nome`.
    
4. Confirmar a transação.
    

O SQLAlchemy acompanha as alterações dos objetos persistentes associados à sessão. Ao executar `commit()`, as mudanças pendentes são sincronizadas com o banco.

Se o usuário não existir, a condição evita tentar modificar um objeto inexistente.

### 6.12. Excluindo registros

Para excluir um registro:

```
with app.app_context():
    usuario = db.session.get(Usuario, 1)

    if usuario is not None:
        db.session.delete(usuario)
        db.session.commit()
```

`db.session.delete(usuario)` marca o objeto para exclusão dentro da sessão. A exclusão é efetivada no banco quando a transação é confirmada.

Em aplicações reais, ações de exclusão devem considerar permissões, validações e possíveis dependências entre registros. Também é importante avaliar se a aplicação precisa de exclusão lógica, histórico de auditoria ou retenção de dados.

### 6.13. Relacionando o banco às rotas Flask

Até agora, as operações foram executadas diretamente dentro de um contexto da aplicação. Em uma aplicação web, normalmente elas são acionadas pelas rotas.

Exemplo:

```
from flask import Flask, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

app.config["SQLALCHEMY_DATABASE_URI"] = (
    "sqlite:///aplicacao.db"
)

db = SQLAlchemy(app)


class Usuario(db.Model):
    id = db.Column(
        db.Integer,
        primary_key=True
    )

    nome = db.Column(
        db.String(100),
        nullable=False
    )


@app.route("/usuarios")
def listar_usuarios():
    usuarios = db.session.execute(
        db.select(Usuario)
    ).scalars().all()

    return jsonify([
        {
            "id": usuario.id,
            "nome": usuario.nome
        }
        for usuario in usuarios
    ])
```

A rota `/usuarios` consulta os registros e retorna uma representação JSON.

O fluxo é:

1. O navegador ou cliente HTTP envia uma requisição.
    
2. O Flask executa a função `listar_usuarios()`.
    
3. A consulta recupera os registros do banco.
    
4. A compreensão de lista transforma cada objeto em um dicionário.
    
5. `jsonify()` converte a estrutura em uma resposta JSON.
    

Nesse exemplo, os campos retornados foram escolhidos explicitamente. Isso evita expor automaticamente todos os atributos de um modelo, o que é especialmente importante quando existem dados sensíveis.

A tabela precisa existir antes que a rota possa consultar seus registros. Em um projeto real, prepare o banco com um processo de inicialização ou migração apropriado, em vez de depender de uma rota web para criar tabelas.

### 6.14. Tratando falhas de transação

Operações de banco de dados podem falhar por motivos como restrições violadas, perda de conexão ou problemas de integridade.

Quando uma transação falha, a sessão pode precisar ser revertida antes que novas operações sejam realizadas.

Exemplo geral:

```
try:
    db.session.add(usuario)
    db.session.commit()
except Exception:
    db.session.rollback()
    raise
```

Nesse exemplo:

- `try` envolve a operação que pode falhar.
    
- `commit()` tenta confirmar a transação.
    
- `except` captura a exceção.
    
- `rollback()` reverte as alterações pendentes da transação.
    
- `raise` propaga a exceção para que o erro não seja ocultado.
    

Capturar `Exception` é útil para ilustrar a necessidade de rollback, mas não deve ser a estratégia final para todos os casos. Em código de produção, prefira tratar as exceções esperadas de forma específica, registrar informações úteis para diagnóstico e evitar expor detalhes internos ao usuário.

Também não é recomendável ignorar uma falha e responder como se o cadastro tivesse sido concluído.

### 6.15. Erros comuns com Flask-SQLAlchemy

`**RuntimeError: Working outside of application context**`

Uma operação dependente da aplicação foi executada sem um contexto Flask ativo. Em scripts independentes, utilize `with app.app_context():`. Nas rotas Flask convencionais, o contexto da requisição normalmente já disponibiliza o contexto da aplicação.

`**no such table**`

O banco não contém a tabela consultada. Verifique se o modelo foi importado e registrado, se o banco conectado é o esperado e se o processo de criação das tabelas ou de migração foi executado.

**Alterações no modelo não aparecem no banco**

Modificar a classe Python não altera automaticamente a estrutura de uma tabela existente. Utilize migrações para controlar mudanças no esquema.

**O registro não é persistido**

Verifique se o objeto foi adicionado à sessão e se a transação foi confirmada. Se o `commit()` falhar, examine a exceção e faça rollback quando necessário.

**Violação de chave ou restrição de integridade**

O dado pode violar uma restrição do banco, como uma chave única, uma chave estrangeira ou uma coluna obrigatória. Valide os dados e trate os erros apropriados.

**Consulta retorna uma lista vazia**

Isso pode significar que não existem registros que correspondam à consulta, que os dados foram inseridos em outro banco ou que os filtros estão incorretos. Confirme a URI de conexão e a condição utilizada na consulta.

### 6.16. Boas práticas iniciais

- Não armazene todos os dados somente em variáveis Python se eles precisarem persistir entre execuções.
    
- Utilize modelos para representar as entidades persistidas.
    
- Valide os dados antes de gravá-los.
    
- Utilize consultas parametrizadas ou os mecanismos de consulta do SQLAlchemy em vez de concatenar entradas do usuário em SQL.
    
- Não exponha todos os atributos dos modelos automaticamente nas respostas HTTP.
    
- Trate falhas de transação e utilize rollback quando necessário.
    
- Não utilize `create_all()` como substituto de migrações em aplicações que precisam evoluir o esquema.
    
- Mantenha credenciais de banco e segredos fora do código-fonte.
    
- Para aplicações com dados importantes, planeje backups, permissões, índices e políticas de retenção.
    
- Utilize transações para manter operações relacionadas consistentes.

---
## 7. Cadastro, login e áreas restritas

Em uma aplicação web, nem todas as páginas devem estar disponíveis para qualquer visitante. Um sistema pode ter páginas públicas, uma área de usuário autenticado e funcionalidades restritas a determinados perfis.

Para implementar isso corretamente, precisamos compreender três conceitos: identidade do usuário, autenticação e autorização.

### 7.1. Autenticação e autorização

**Autenticação** é o processo de verificar quem está tentando acessar a aplicação.

Por exemplo, o usuário informa e-mail e senha, e o sistema verifica se as credenciais correspondem a uma conta existente.

**Autorização** é o processo de verificar quais ações esse usuário pode realizar.

Por exemplo, um usuário pode estar autenticado, mas não ter permissão para excluir contas ou acessar o painel administrativo.

A diferença é importante:

|Conceito|Pergunta respondida|Exemplo|
|---|---|---|
|Autenticação|Quem é você?|Validar as credenciais do login|
|Autorização|O que você pode fazer?|Verificar se o usuário possui permissão administrativa|
|Sessão|Como a aplicação mantém o estado de autenticação entre requisições?|Identificar o usuário em acessos posteriores|

Estar autenticado não significa possuir todas as permissões.

### 7.2. Como funciona um login web?

O protocolo HTTP é, em sua essência, sem estado: cada requisição é independente. Para manter um usuário autenticado entre várias requisições, uma aplicação precisa de um mecanismo para reconhecer esse usuário.

O fluxo típico de login é:

1. O usuário acessa a página de login.
    
2. O navegador envia as credenciais por meio de um formulário.
    
3. O servidor procura a conta correspondente.
    
4. O servidor verifica a senha.
    
5. Se as credenciais forem válidas, a aplicação estabelece o estado de autenticação.
    
6. Nas próximas requisições, esse estado permite identificar o usuário.
    
7. As rotas protegidas verificam se a autenticação é necessária e, quando aplicável, se o usuário possui a autorização exigida.
    

A senha não deve ser armazenada em texto puro. O sistema deve guardar um hash de senha produzido por uma função apropriada para esse fim.

### 7.3. Preparando o Flask-Login

O Flask-Login é uma extensão externa que facilita o gerenciamento da autenticação de usuários em aplicações Flask.

Ela fornece recursos para:

- Identificar o usuário autenticado.
    
- Manter o estado de login por meio da sessão.
    
- Disponibilizar o usuário atual por meio de `current_user`.
    
- Proteger rotas com `@login_required`.
    
- Encerrar a autenticação com `logout_user()`.
    

O Flask-Login não cria automaticamente um banco de dados, um formulário de login, um sistema de cadastro ou regras completas de autorização. Essas partes precisam ser implementadas pela aplicação ou por outras extensões.

Instale a dependência no ambiente virtual:

```
python -m pip install Flask-Login
```

Como o exemplo também utilizará Flask-SQLAlchemy, mantenha essa extensão instalada.

### 7.4. Configurando a aplicação e o modelo de usuário

O Flask-Login precisa de uma forma de identificar e carregar o usuário autenticado. Quando utilizamos um modelo de usuário próprio, esse modelo deve implementar a interface esperada pela extensão.

Exemplo didático com Flask-SQLAlchemy:

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_login import LoginManager, UserMixin

app = Flask(__name__)

app.config["SECRET_KEY"] = (
    "substitua-por-um-segredo-forte"
)

app.config["SQLALCHEMY_DATABASE_URI"] = (
    "sqlite:///aplicacao.db"
)

db = SQLAlchemy(app)

login_manager = LoginManager()
login_manager.init_app(app)


class Usuario(UserMixin, db.Model):
    id = db.Column(
        db.Integer,
        primary_key=True
    )

    email = db.Column(
        db.String(150),
        unique=True,
        nullable=False
    )

    senha_hash = db.Column(
        db.String(256),
        nullable=False
    )
```

#### Entendendo o código

`**SECRET_KEY**`

É uma configuração utilizada pelo Flask para proteger dados assinados, incluindo os dados de sessão mantidos pelo mecanismo padrão de sessões.

O valor acima é apenas um marcador didático. Em produção, utilize um segredo forte, gerado de forma segura e armazenado fora do código-fonte.

`**LoginManager()**`

Cria o gerenciador de login do Flask-Login.

`**login_manager.init_app(app)**`

Inicializa a extensão para a aplicação Flask.

`**class Usuario(UserMixin, db.Model)**`

Define o modelo de usuário e inclui `UserMixin`, que fornece implementações comuns dos métodos e atributos esperados pelo Flask-Login.

`**id**`

Representa a chave primária utilizada para identificar o usuário no banco.

`**email**`

Armazena o e-mail. A opção `unique=True` estabelece uma restrição de unicidade no banco de dados, impedindo que dois registros tenham o mesmo valor nessa coluna.

`**senha_hash**`

Armazena o hash da senha, não a senha original.

A restrição `unique=True` no banco não substitui o tratamento de possíveis conflitos durante o cadastro. A aplicação deve tratar erros de integridade quando duas tentativas concorrentes tentarem cadastrar o mesmo e-mail.

### 7.5. Carregando o usuário autenticado

O Flask-Login precisa recuperar o usuário associado à sessão. Para isso, utilizamos o decorador `@login_manager.user_loader`.

```
@login_manager.user_loader
def carregar_usuario(user_id):
    try:
        identificador = int(user_id)
    except (TypeError, ValueError):
        return None

    return db.session.get(Usuario, identificador)
```

Analisando o exemplo:

- `@login_manager.user_loader`: registra a função que carrega o usuário a partir do identificador salvo no estado de autenticação.
    
- `user_id`: recebe o identificador do usuário como uma string.
    
- `int(user_id)`: converte o identificador para inteiro, conforme o tipo da chave primária deste exemplo.
    
- `db.session.get(Usuario, identificador)`: procura o registro pela chave primária.
    
- `return None`: indica que o usuário não foi encontrado ou que o identificador não pôde ser convertido.
    

A conversão para inteiro é apropriada porque o modelo usa uma chave primária inteira. Se o modelo utilizar outro tipo de identificador, a função deverá ser adaptada.

### 7.6. Criando um formulário de login

O formulário envia o e-mail e a senha ao servidor. Para este exemplo, vamos utilizar um template HTML chamado `templates/login.html`.

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
    <h1>Entrar</h1>

    <form method="POST">
        <label for="email">E-mail:</label>
        <input
            type="email"
            id="email"
            name="email"
            autocomplete="username"
            required
        >

        <label for="senha">Senha:</label>
        <input
            type="password"
            id="senha"
            name="senha"
            autocomplete="current-password"
            required
        >

        <button type="submit">Entrar</button>
    </form>
</body>
</html>
```

O atributo `method="POST"` envia os campos no corpo da requisição. Os atributos `name="email"` e `name="senha"` permitem recuperar os dados com `request.form`.

O tipo `password` mascara visualmente a entrada, mas não criptografa o dado enviado. Por isso, credenciais devem ser transmitidas por HTTPS em aplicações reais.

### 7.7. Protegendo as senhas com hash

Nunca armazene senhas em texto puro no banco de dados.

O Werkzeug, utilizado pelo ecossistema Flask, oferece funções para gerar e verificar hashes de senha.

```
from werkzeug.security import (
    generate_password_hash,
    check_password_hash,
)
```

Para gerar um hash:

```
senha_hash = generate_password_hash(senha)
```

Para verificar uma senha:

```
valida = check_password_hash(
    usuario.senha_hash,
    senha_informada
)
```

A função `generate_password_hash()` recebe a senha original e produz um hash com os parâmetros e o método de derivação configurados pela biblioteca.

A função `check_password_hash()` recebe o hash armazenado e a senha informada, retornando `True` quando a senha corresponde ao hash e `False` caso contrário.

Não compare a senha informada diretamente com o hash: a função de verificação é responsável por realizar a comparação apropriada.

### 7.8. Implementando a rota de login

Agora podemos combinar o formulário, o modelo de usuário e o Flask-Login.

```
from flask import (
    render_template,
    request,
    redirect,
    url_for,
)
from flask_login import login_user
from werkzeug.security import check_password_hash


@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        email = request.form.get(
            "email", ""
        ).strip().lower()

        senha = request.form.get("senha", "")

        usuario = db.session.execute(
            db.select(Usuario).where(
                Usuario.email == email
            )
        ).scalar_one_or_none()

        if (
            usuario is not None
            and check_password_hash(
                usuario.senha_hash,
                senha
            )
        ):
            login_user(usuario)

            return redirect(
                url_for("painel")
            )

        return "E-mail ou senha inválidos.", 401

    return render_template("login.html")
```

#### Como funciona?

1. A requisição `GET` exibe o formulário.
    
2. A requisição `POST` inicia o processamento das credenciais.
    
3. O e-mail é normalizado com `strip()` e `lower()`.
    
4. A consulta procura um usuário com o e-mail informado.
    
5. `scalar_one_or_none()` retorna o usuário encontrado, `None` se não houver resultado ou gera uma exceção se a consulta produzir múltiplos resultados.
    
6. `check_password_hash()` verifica a senha.
    
7. `login_user(usuario)` registra o usuário como autenticado na sessão.
    
8. `redirect(url_for("painel"))` redireciona o navegador para o endpoint `painel`.
    
9. Se as credenciais não forem válidas, a aplicação responde com uma mensagem genérica.
    

A mensagem genérica evita revelar diretamente se um e-mail está cadastrado. Em sistemas reais, também é importante aplicar limites de tentativas, monitoramento e outras medidas contra ataques automatizados.

A normalização com `lower()` deve seguir a política de identidade adotada pela aplicação. O tratamento de endereços de e-mail exige cuidado para não criar inconsistências entre cadastro, autenticação e regras do banco.

### 7.9. Protegendo uma rota com `@login_required`

Depois de implementar o login, podemos proteger as páginas que exigem autenticação.

```
from flask import render_template
from flask_login import login_required, current_user


@app.route("/painel")
@login_required
def painel():
    return render_template(
        "painel.html",
        usuario=current_user
    )
```

O decorador `@login_required` verifica se o usuário está autenticado antes de permitir a execução normal da função.

Se não estiver autenticado, o Flask-Login utiliza seu comportamento de redirecionamento para a página de login, quando ela foi configurada.

Para indicar explicitamente o endpoint da página de login:

```
login_manager.login_view = "login"
```

Essa configuração informa ao Flask-Login qual endpoint utilizar quando uma pessoa não autenticada tenta acessar uma rota protegida.

No template `templates/painel.html`:

```
<h1>Bem-vindo, {{ usuario.email }}</h1>
<p>Esta página exige autenticação.</p>
```

O objeto `current_user` representa o usuário atual no contexto da requisição. Quando existe um usuário autenticado, podemos acessar seus atributos permitidos, como o e-mail.

**Importante:** `@login_required` protege contra acesso anônimo, mas não verifica automaticamente se o usuário tem permissão para cada operação. Regras de autorização precisam ser implementadas separadamente.

### 7.10. Encerrando a sessão de login

Para permitir que o usuário saia da aplicação, utilizamos `logout_user()`.

```
from flask import redirect, url_for
from flask_login import login_required, logout_user


@app.route("/logout", methods=["POST"])
@login_required
def logout():
    logout_user()
    return redirect(url_for("login"))
```

A função `logout_user()` encerra o estado de autenticação gerenciado pelo Flask-Login.

Neste exemplo, a rota aceita apenas `POST`, pois sair da conta altera o estado da sessão. Em aplicações reais, o formulário de logout também deve possuir proteção contra CSRF.

Um formulário HTML pode enviar a requisição:

```
<form action="{{ url_for('logout') }}" method="POST">
    <button type="submit">Sair</button>
</form>
```

O HTML acima ilustra o envio, mas ainda não inclui um token CSRF. Essa proteção será abordada com mais detalhes na parte dedicada a formulários e segurança.

### 7.11. Cadastro de usuários

O cadastro cria uma nova conta e precisa armazenar o hash da senha.

Um exemplo simplificado de rota:

```
from flask import request, redirect, url_for
from sqlalchemy.exc import IntegrityError
from werkzeug.security import generate_password_hash


@app.route("/cadastro", methods=["POST"])
def cadastro():
    email = request.form.get(
        "email", ""
    ).strip().lower()

    senha = request.form.get("senha", "")

    if not email or not senha:
        return "E-mail e senha são obrigatórios.", 400

    usuario = Usuario(
        email=email,
        senha_hash=generate_password_hash(senha)
    )

    try:
        db.session.add(usuario)
        db.session.commit()
    except IntegrityError:
        db.session.rollback()
        return "Não foi possível cadastrar essa conta.", 409

    return redirect(url_for("login"))
```

Esse exemplo pressupõe que o modelo `Usuario` já está definido e que a aplicação possui um contexto válido, como ocorre durante uma requisição Flask normal.

O fluxo é:

1. Receber os dados do formulário.
    
2. Validar os campos obrigatórios.
    
3. Gerar o hash da senha.
    
4. Criar o objeto de usuário.
    
5. Adicionar o objeto à sessão.
    
6. Confirmar a transação.
    
7. Tratar uma possível violação de integridade, como um e-mail duplicado.
    
8. Redirecionar para a página de login.
    

O exemplo não substitui uma implementação completa de cadastro. Uma aplicação real também precisa validar o formato do e-mail, definir uma política adequada para senhas, limitar tentativas abusivas, utilizar CSRF e tratar outros erros de maneira controlada.

### 7.12. Autenticação não é autorização

Considere uma aplicação com usuários comuns e administradores.

Os dois tipos de usuário podem estar autenticados, mas apenas administradores devem conseguir acessar determinadas funcionalidades.

Uma verificação simples poderia ser:

```
from flask import abort
from flask_login import login_required, current_user


@app.route("/admin")
@login_required
def admin():
    if not current_user.is_admin:
        abort(403)

    return "Painel administrativo"
```

Esse exemplo pressupõe que o modelo `Usuario` possui um atributo `is_admin`, armazenado e controlado corretamente pela aplicação.

O status administrativo não deve ser aceito diretamente de um formulário de cadastro público. Caso contrário, qualquer usuário poderia tentar atribuir privilégios a si mesmo.

O código também demonstra a diferença entre dois resultados:

- **401:** indica que a requisição não possui autenticação válida em contextos nos quais esse status é apropriado.
    
- **403:** indica que o acesso foi recusado por falta de autorização.
    

Em aplicações que utilizam redirecionamentos de login no navegador, o comportamento para usuários anônimos pode ser diferente do de uma API HTTP. A resposta adequada depende da interface e do contrato da aplicação.

### 7.13. Limitações e cuidados de segurança

O Flask-Login facilita o gerenciamento de autenticação, mas não resolve todos os aspectos de segurança de uma aplicação.

- **Senhas:** armazene hashes produzidos por funções adequadas para senhas; nunca guarde senhas em texto puro.
    
- **HTTPS:** utilize HTTPS para proteger credenciais e cookies durante o transporte.
    
- **Cookies:** configure opções como `Secure`, `HttpOnly` e `SameSite` conforme a arquitetura e as necessidades da aplicação.
    
- **CSRF:** proteja operações que alteram estado quando a autenticação utiliza cookies.
    
- **Autorização:** valide permissões no servidor em cada operação protegida.
    
- **Tentativas de login:** implemente limites e monitoramento contra tentativas automatizadas.
    
- **Segredos:** mantenha `SECRET_KEY` e credenciais fora do código-fonte e não os publique em repositórios.
    
- **Validação:** valide os dados no servidor, independentemente da validação do navegador.
    
- **Mensagens de erro:** evite revelar se determinada conta existe durante a autenticação.
    
- **Sessões:** defina uma política adequada para expiração, encerramento de sessão e revogação de acesso quando necessário.
    

A implementação correta depende do tipo de aplicação. Uma API que utiliza tokens, por exemplo, pode exigir um mecanismo de autenticação diferente daquele empregado em um site tradicional baseado em sessões.

---
## 8. Interfaces com Jinja e Tailwind CSS

Uma aplicação web não precisa apenas executar a lógica corretamente: ela também precisa apresentar informações de forma organizada e oferecer uma interface fácil de utilizar.

No Flask, podemos separar essa responsabilidade em três partes:

- **Flask:** processa requisições, executa funções e prepara os dados.
    
- **Jinja:** gera HTML dinâmico, reutiliza estruturas e controla a exibição de conteúdo.
    
- **Tailwind CSS:** fornece classes CSS utilitárias para definir aparência, espaçamento, cores, responsividade e outros aspectos visuais.
    

Jinja é integrado ao Flask. Tailwind CSS é uma ferramenta externa de estilização e não é uma extensão Python.

### 8.1. O problema de repetir HTML

Imagine que uma aplicação tenha cinco páginas: início, login, cadastro, painel e perfil.

Todas precisam apresentar o mesmo cabeçalho, menu e rodapé. Uma abordagem seria copiar o mesmo HTML para cada arquivo.

Isso funciona, mas cria um problema: quando o cabeçalho precisar mudar, será necessário modificar todos os templates que o repetem.

A herança de templates do Jinja permite definir uma estrutura comum em um arquivo-base e reutilizá-la nas páginas.

Estrutura de exemplo:

```
meu_projeto/
├── app.py
├── templates/
│   ├── base.html
│   ├── index.html
│   ├── login.html
│   └── painel.html
└── static/
    └── css/
        └── estilo.css
```

### 8.2. Criando um template-base com Jinja

O arquivo `base.html` pode conter a estrutura que será compartilhada pelas páginas.

Arquivo `templates/base.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>{% block title %}Minha aplicação{% endblock %}</title>

    {% block head %}{% endblock %}
</head>
<body>
    <header>
        <nav>
            <a href="{{ url_for('inicio') }}">Início</a>
            <a href="{{ url_for('login') }}">Login</a>
        </nav>
    </header>

    <main>
        {% block content %}{% endblock %}
    </main>

    <footer>
        <p>Minha aplicação Flask</p>
    </footer>
</body>
</html>
```

#### Entendendo as expressões do Jinja

`**{% block title %}...{% endblock %}**`

Define um bloco que um template filho pode substituir. Nesse caso, o bloco controla o título da página.

`**{% block head %}{% endblock %}**`

Cria uma área para que páginas específicas adicionem elementos ao `<head>`, como folhas de estilo ou metadados.

`**{% block content %}{% endblock %}**`

Define o espaço em que cada página colocará seu conteúdo principal.

`**{{ url_for('inicio') }}**`

Gera a URL associada ao endpoint `inicio`, evitando depender de um caminho escrito manualmente.

Os blocos são mecanismos de composição do Jinja. Eles não são elementos HTML e não aparecem como instruções no HTML final enviado ao navegador.

### 8.3. Estendendo o template-base

Agora podemos criar uma página que herda a estrutura de `base.html`.

Arquivo `templates/index.html`:

```
{% extends "base.html" %}

{% block title %}Página inicial{% endblock %}

{% block content %}
    <h1>Bem-vindo!</h1>
    <p>Esta é a página inicial da aplicação.</p>
{% endblock %}
```

A instrução `{% extends "base.html" %}` indica que o template utiliza a estrutura definida em `base.html`.

O bloco `title` substitui o título padrão, enquanto `content` fornece o conteúdo principal da página.

A instrução `extends` deve ficar no início do template, antes do conteúdo normal, para que a herança seja processada corretamente.

No Python, a rota pode continuar simples:

```
from flask import Flask, render_template

app = Flask(__name__)


@app.route("/")
def inicio():
    return render_template("index.html")
```

Quando o Flask renderiza `index.html`, o Jinja combina os blocos do template filho com a estrutura do template-base.

### 8.4. Enviando dados para templates que herdam uma estrutura

A herança não impede que o Python envie dados dinâmicos.

Arquivo `app.py`:

```
@app.route("/perfil/<nome>")
def perfil(nome):
    return render_template(
        "perfil.html",
        nome=nome
    )
```

Arquivo `templates/perfil.html`:

```
{% extends "base.html" %}

{% block title %}Perfil de {{ nome }}{% endblock %}

{% block content %}
    <h1>Olá, {{ nome }}!</h1>
    <p>Este conteúdo foi gerado dinamicamente.</p>
{% endblock %}
```

O parâmetro `nome` é recebido pela rota e enviado ao template. O Jinja utiliza seu valor tanto no título quanto no conteúdo.

Por padrão, o Jinja aplica autoescape a variáveis renderizadas em templates HTML convencionais. Isso ajuda a impedir que conteúdo fornecido pelo usuário seja interpretado diretamente como marcação HTML executável. Não desative essa proteção para dados não confiáveis sem uma justificativa e tratamento adequados.

### 8.5. Reutilizando pequenos componentes com `include`

A herança é útil para reutilizar a estrutura geral de uma página. Para reutilizar partes menores, podemos utilizar `include`.

Estrutura:

```
templates/
├── base.html
├── index.html
└── partials/
    ├── navbar.html
    └── footer.html
```

Arquivo `templates/partials/navbar.html`:

```
<nav>
    <a href="{{ url_for('inicio') }}">Início</a>
    <a href="{{ url_for('login') }}">Entrar</a>
</nav>
```

No arquivo `base.html`, podemos inserir esse fragmento:

```
<header>
    {% include "partials/navbar.html" %}
</header>
```

A instrução `include` renderiza o template incluído no local em que foi declarada.

Ela é útil para elementos como menus, avisos, cartões e rodapés compartilhados. Por padrão, o template incluído tem acesso ao contexto disponível durante a renderização, incluindo variáveis passadas ao template principal.

### 8.6. Utilizando condicionais em interfaces

Uma interface frequentemente precisa mudar de acordo com o estado da aplicação.

Por exemplo, podemos exibir uma mensagem diferente para usuários autenticados e visitantes.

```
{% if usuario %}
    <p>Bem-vindo, {{ usuario.email }}!</p>
{% else %}
    <p>Entre na sua conta para continuar.</p>
{% endif %}
```

O bloco `if` avalia a expressão fornecida. Se ela for verdadeira, o primeiro trecho será renderizado; caso contrário, o Jinja renderizará o trecho `else`, quando presente.

A variável `usuario` precisa estar disponível no contexto do template. Ela pode ser enviada pela função de visualização ou disponibilizada por outro mecanismo de contexto.

**Importante:** esconder um botão no HTML não protege uma operação. As permissões devem ser verificadas no servidor, na rota que executa a ação.

### 8.7. O que é Tailwind CSS?

Tailwind CSS é um framework CSS baseado em classes utilitárias.

Em vez de criar uma classe CSS específica para cada componente, você combina classes que representam propriedades de estilo.

Por exemplo, em CSS tradicional:

```
<h1 class="titulo">Minha aplicação</h1>
```

```
.titulo {
    font-size: 2rem;
    font-weight: bold;
    color: #2563eb;
}
```

Com Tailwind CSS, o mesmo título pode ser estilizado diretamente no HTML:

```
<h1 class="text-3xl font-bold text-blue-600">
    Minha aplicação
</h1>
```

As classes representam propriedades específicas:

- `text-3xl`: tamanho de texto.
    
- `font-bold`: peso da fonte.
    
- `text-blue-600`: cor do texto.
    

O Tailwind não substitui o HTML nem o Flask. Ele fornece classes CSS que precisam estar disponíveis no navegador para que os estilos sejam aplicados.

### 8.8. Adicionando Tailwind CSS ao template

Para aprender rapidamente, é possível utilizar o Play CDN do Tailwind em um ambiente experimental.

No arquivo `base.html`, adicione no `<head>`:

```
<script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
```

Depois, utilize as classes Tailwind em seus elementos:

```
<h1 class="text-3xl font-bold text-blue-600">
    Painel Flask
</h1>
```

Esse método é conveniente para experimentar classes e desenvolver protótipos. O Play CDN é destinado a desenvolvimento, não à implantação de uma aplicação de produção.

Para produção, configure o Tailwind conforme sua versão e ferramenta de build, gerando o CSS necessário e servindo o arquivo resultante pela aplicação.

### 8.9. Construindo uma página com Tailwind

Podemos utilizar o template-base para criar uma página com aparência mais organizada.

Arquivo `templates/index.html`:

```
{% extends "base.html" %}

{% block title %}Início{% endblock %}

{% block content %}
<section class="mx-auto max-w-4xl px-6 py-12">
    <div class="rounded-2xl bg-white p-8 shadow-md">
        <h1 class="text-3xl font-bold text-slate-900">
            Bem-vindo à aplicação
        </h1>

        <p class="mt-4 text-slate-600">
            Uma interface construída com Flask, Jinja e Tailwind CSS.
        </p>

        <a
            href="{{ url_for('login') }}"
            class="mt-6 inline-block rounded-lg bg-blue-600 px-5 py-3 font-semibold text-white hover:bg-blue-700"
        >
            Entrar
        </a>
    </div>
</section>
{% endblock %}
```

Algumas classes utilizadas:

|Classe|Efeito|
|---|---|
|`mx-auto`|Centraliza horizontalmente um elemento dentro do espaço disponível, quando suas dimensões permitem.|
|`max-w-4xl`|Define uma largura máxima.|
|`px-6`|Adiciona espaçamento horizontal interno.|
|`py-12`|Adiciona espaçamento vertical interno.|
|`rounded-2xl`|Arredonda os cantos.|
|`bg-white`|Define fundo branco.|
|`p-8`|Adiciona espaçamento interno.|
|`shadow-md`|Aplica uma sombra predefinida.|
|`text-slate-900`|Define uma cor escura para o texto.|
|`hover:bg-blue-700`|Aplica outra cor de fundo quando o elemento recebe o estado de hover.|

O Tailwind possui convenções próprias para espaçamento, cores, tamanhos e estados. Os valores exatos disponíveis dependem da versão e da configuração utilizada.

### 8.10. Criando cartões reutilizáveis

Um cartão é um componente visual que agrupa informações relacionadas.

Podemos criar um fragmento de template para representar um cartão simples.

Arquivo `templates/partials/card.html`:

```
<div class="rounded-xl border border-slate-200 bg-white p-6 shadow-sm">
    <h2 class="text-xl font-semibold text-slate-900">
        {{ titulo }}
    </h2>

    <p class="mt-2 text-slate-600">
        {{ descricao }}
    </p>
</div>
```

O cartão recebe duas variáveis: `titulo` e `descricao`.

Para utilizá-lo, podemos incluir o fragmento a partir de outro template. Quando o mesmo componente precisar ser repetido várias vezes com dados diferentes, macros do Jinja podem ser uma alternativa mais apropriada, pois permitem definir componentes com parâmetros explícitos.

A escolha entre `include` e macros depende da complexidade e da forma de reutilização desejada.

### 8.11. Macros do Jinja

Uma macro permite definir um trecho reutilizável de template que recebe argumentos.

Arquivo `templates/partials/componentes.html`:

```
{% macro botao(texto, url, cor="blue") %}
    <a
        href="{{ url }}"
        class="inline-block rounded-lg px-4 py-2 font-semibold text-white
        {% if cor == 'blue' %}
            bg-blue-600 hover:bg-blue-700
        {% elif cor == 'green' %}
            bg-green-600 hover:bg-green-700
        {% endif %}"
    >
        {{ texto }}
    </a>
{% endmacro %}
```

A macro `botao` recebe:

- `texto`: conteúdo que aparecerá no botão.
    
- `url`: destino do link.
    
- `cor`: parâmetro opcional, com valor padrão `"blue"`.
    

Para utilizar a macro em outro template:

```
{% from "partials/componentes.html" import botao %}

{{ botao("Acessar painel", url_for("painel")) }}

{{ botao("Novo cadastro", url_for("cadastro"), "green") }}
```

O primeiro botão utiliza a cor padrão; o segundo fornece `"green"` explicitamente.

O exemplo demonstra a composição de templates. Em uma aplicação real, confirme que as classes de estilo estão disponíveis no CSS gerado pelo Tailwind.

### 8.12. Design responsivo

Uma interface responsiva adapta sua apresentação a diferentes tamanhos de tela.

O Tailwind oferece variantes responsivas que aplicam classes a partir de determinados pontos de quebra.

Exemplo:

```
<div class="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
    <div class="rounded-lg bg-white p-4 shadow">Cartão 1</div>
    <div class="rounded-lg bg-white p-4 shadow">Cartão 2</div>
    <div class="rounded-lg bg-white p-4 shadow">Cartão 3</div>
</div>
```

Nesse exemplo:

- `grid`: ativa o layout de grade.
    
- `grid-cols-1`: utiliza uma coluna como configuração inicial.
    
- `md:grid-cols-2`: utiliza duas colunas a partir do ponto de quebra `md`.
    
- `lg:grid-cols-3`: utiliza três colunas a partir do ponto de quebra `lg`.
    
- `gap-4`: define o espaçamento entre os itens.
    

As variantes `md:` e `lg:` representam condições responsivas. Os pontos de quebra concretos dependem da configuração e da versão do Tailwind utilizada.

### 8.13. Organização do CSS e arquivos estáticos

Quando uma aplicação utiliza CSS próprio, ele pode ser armazenado no diretório `static`.

Exemplo:

```
meu_projeto/
├── app.py
├── templates/
│   ├── base.html
│   └── index.html
└── static/
    └── css/
        └── estilo.css
```

No template-base:

```
<link
    rel="stylesheet"
    href="{{ url_for('static', filename='css/estilo.css') }}"
>
```

O uso de `url_for('static', filename='...')` permite gerar o caminho correto para o arquivo estático.

Tailwind e CSS tradicional podem coexistir. Entretanto, convém estabelecer uma organização consistente para evitar regras duplicadas, estilos conflitantes e manutenção desnecessária.

### 8.14. Erros comuns com Jinja e Tailwind

`**TemplateNotFound**`

O template não foi localizado. Verifique o diretório `templates`, o nome do arquivo e o caminho utilizado em `render_template()` ou `include`.

`**UndefinedError**` **no Jinja**

Uma variável ou atributo esperado não está disponível no contexto. Verifique o nome da variável, os dados enviados pela rota e se o objeto realmente possui o atributo solicitado.

**A herança não funciona como esperado**

Confira se `{% extends %}` está no início do template e se os nomes dos blocos correspondem aos definidos no template-base.

**O CSS não é carregado**

Confirme se o arquivo está dentro do diretório estático configurado e se o caminho gerado por `url_for()` corresponde ao arquivo existente.

**As classes Tailwind não produzem efeito**

Verifique se o CSS do Tailwind foi carregado, se as classes são válidas na versão utilizada e se o processo de build incluiu os templates Flask como fontes de classes.

**Classes dinâmicas não aparecem no CSS de produção**

Ferramentas de build podem não detectar classes montadas dinamicamente em strings. Em vez de construir nomes de classes arbitrariamente, prefira mapear opções conhecidas para classes completas ou configurar a inclusão das classes conforme a versão da ferramenta.

### 8.15. Boas práticas

- Utilize um template-base para a estrutura compartilhada.
    
- Use `include` para fragmentos de HTML e macros quando precisar de componentes parametrizados.
    
- Mantenha a lógica de negócio no Python e a apresentação nos templates.
    
- Evite utilizar `|safe` em dados não confiáveis.
    
- Gere URLs internas com `url_for()` sempre que apropriado.
    
- Utilize Tailwind para agilizar a estilização, mas mantenha uma organização consistente das classes.
    
- Teste a interface em diferentes larguras de tela.
    
- Para produção, gere e sirva os arquivos CSS adequados em vez de depender do Play CDN.
    
- Lembre-se de que esconder elementos visualmente não substitui controles de autorização no servidor.

---
