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
## 9. Configuração, tratamento de erros e logging

Uma aplicação Flask precisa lidar com diferentes ambientes, como desenvolvimento, testes e produção. Também precisa tratar erros sem mostrar informações internas aos usuários e registrar detalhes que ajudem o desenvolvedor a descobrir o que aconteceu.

Essas responsabilidades são divididas em três áreas:

- **Configuração:** define como a aplicação deve funcionar.
    
- **Tratamento de erros:** determina como a aplicação responde quando algo falha.
    
- **Logging:** registra informações importantes sobre o funcionamento da aplicação.
    

### 9.1. Configuração da aplicação

A configuração controla valores utilizados pelo Flask e pelas extensões instaladas.

Por exemplo, uma aplicação pode precisar de uma chave secreta para proteger os dados da sessão, de uma URI para acessar o banco de dados e de opções específicas para executar testes.

O Flask disponibiliza o atributo `app.config`, que funciona como um dicionário de configurações.

```
from flask import Flask

app = Flask(__name__)

app.config["DEBUG"] = False
app.config["TESTING"] = False
```

Analisando:

- `Flask(__name__)` cria a aplicação.
    
- `app.config` acessa as configurações.
    
- `app.config["DEBUG"] = False` desativa a opção de depuração nessa configuração.
    
- `app.config["TESTING"] = False` indica que a aplicação não está sendo configurada para testes.
    

Esses valores são exemplos. Em um projeto real, é preferível centralizar a configuração em vez de espalhar atribuições pelo código.

### 9.2. Configurações importantes

|Configuração|Finalidade|
|---|---|
|`DEBUG`|Ativa recursos de depuração durante o desenvolvimento.|
|`TESTING`|Indica que a aplicação está executando em modo de testes.|
|`SECRET_KEY`|Fornece a chave usada para assinar dados como os cookies de sessão do Flask.|
|`SESSION_COOKIE_HTTPONLY`|Impede que JavaScript comum leia o cookie de sessão diretamente.|
|`SESSION_COOKIE_SECURE`|Faz o navegador enviar o cookie apenas por HTTPS.|
|`SESSION_COOKIE_SAMESITE`|Controla em quais contextos de navegação o navegador envia o cookie.|
|`MAX_CONTENT_LENGTH`|Define um limite para o tamanho do corpo de uma requisição, ajudando a restringir uploads e outros conteúdos enviados.|

Nem toda configuração precisa ser definida manualmente: várias possuem valores padrão. Consulte a documentação da versão do Flask utilizada para verificar os valores e comportamentos exatos.

### 9.3. A importância da `SECRET_KEY`

O Flask utiliza `SECRET_KEY` para assinar dados, incluindo os dados armazenados nos cookies de sessão.

```
app.config["SECRET_KEY"] = "minha-chave-secreta"
```

O exemplo demonstra onde a configuração é definida, mas essa chave literal **não deve ser usada em uma aplicação real**. Uma chave previsível ou exposta pode comprometer a segurança dos dados assinados.

Uma opção melhor é carregar a chave de uma variável de ambiente:

```
import os

app.config["SECRET_KEY"] = os.environ.get("SECRET_KEY")
```

No terminal Linux, você pode definir uma chave para o ambiente atual:

```
export SECRET_KEY="$(python3 -c 'import secrets; print(secrets.token_hex(32))')"
```

Depois, execute a aplicação no mesmo terminal para que ela herde a variável.

**Atenção:** esse comando gera uma chave nova. Para um ambiente persistente, configure uma chave estável e mantenha-a protegida. Se a chave mudar entre inicializações, as sessões existentes assinadas com a chave anterior poderão deixar de funcionar.

Não armazene chaves reais, senhas ou tokens diretamente no repositório Git.

### 9.4. Organizando a configuração em uma classe

Em projetos maiores, podemos reunir as configurações em uma classe Python.

```
import os

class Config:
    SECRET_KEY = os.environ.get("SECRET_KEY")
    DEBUG = False
    TESTING = False
```

A aplicação pode carregar essa classe usando `from_object()`:

```
from flask import Flask

app = Flask(__name__)
app.config.from_object(Config)
```

O método `from_object()` carrega as configurações disponíveis no objeto informado, incluindo atributos apropriados da classe.

Isso ajuda a manter as configurações em um único local e facilita separar os ambientes.

#### Principais maneiras de carregar configurações

|Método|Comportamento|
|---|---|
|`app.config["CHAVE"] = valor`|Define uma configuração individual.|
|`app.config.from_mapping(...)`|Carrega configurações a partir de um mapeamento, como argumentos nomeados ou um dicionário.|
|`app.config.from_object(objeto)`|Carrega configurações de um objeto ou classe Python.|
|`app.config.from_pyfile(caminho)`|Carrega configurações de um arquivo Python.|

Exemplo com `from_mapping()`:

```
app.config.from_mapping(
    DEBUG=False,
    TESTING=False,
    MAX_CONTENT_LENGTH=16 * 1024 * 1024,
)
```

Nesse exemplo, `MAX_CONTENT_LENGTH` limita o tamanho do conteúdo de uma requisição a 16 MiB.

Esses métodos são recursos do Flask. Variáveis de ambiente, por sua vez, são fornecidas pelo sistema operacional ou pelo ambiente de execução.

### 9.5. Configuração usando Application Factory

No módulo de estrutura do projeto, vimos o padrão Application Factory: uma função que cria e configura a aplicação.

Podemos utilizar esse padrão para centralizar as configurações e verificar se valores obrigatórios foram fornecidos.

```
import os

from flask import Flask

def create_app(testing=False):
    app = Flask(__name__)

    app.config.from_mapping(
        SECRET_KEY=os.environ.get("SECRET_KEY"),
        DEBUG=False,
        TESTING=testing,
    )

    if not app.config["SECRET_KEY"]:
        raise RuntimeError(
            "A variável de ambiente SECRET_KEY não foi definida."
        )

    return app
```

Analisando o fluxo:

1. A função `create_app()` cria uma instância do Flask.
    
2. `from_mapping()` carrega as configurações.
    
3. `testing` permite indicar se a aplicação está sendo criada para testes.
    
4. A verificação impede que a aplicação continue sem uma chave secreta configurada.
    
5. `return app` entrega a aplicação configurada.
    

O argumento `testing` possui um valor padrão de `False`. Assim, podemos chamar `create_app()` sem argumento ou usar `create_app(testing=True)` em um teste.

A validação da chave acontece durante a criação da aplicação. Isso faz com que o erro de configuração seja identificado cedo, em vez de aparecer mais tarde durante uma requisição.

Em testes automatizados, é possível fornecer uma chave específica para o ambiente de testes, sem reutilizar segredos de produção.

### 9.6. Desenvolvimento, testes e produção

Cada ambiente possui necessidades diferentes.

**Desenvolvimento**

- Facilita identificar erros durante a implementação.
    
- Permite testar mudanças rapidamente.
    
- Pode utilizar dados locais e configurações específicas do desenvolvedor.
    

**Testes**

- Permite ativar comportamentos próprios do ambiente de testes.
    
- Pode utilizar um banco de dados separado.
    
- Deve evitar alterar dados reais de produção.
    

**Produção**

- Não deve expor o depurador interativo.
    
- Deve utilizar uma chave secreta protegida.
    
- Precisa registrar erros sem revelar informações internas ao usuário.
    
- Deve utilizar HTTPS quando o sistema é disponibilizado pela internet.
    

A opção `DEBUG` da aplicação e as opções de depuração da CLI do Flask estão relacionadas, mas não são conceitos idênticos em todas as formas de execução. Verifique a configuração efetiva e o comando de inicialização utilizado.

Nunca deixe o depurador interativo exposto publicamente em produção.

### 9.7. O que é tratamento de erros?

Uma aplicação web recebe requisições e tenta executar operações. Durante esse processo, algo pode dar errado:

- O usuário solicita uma página inexistente.
    
- Um registro não é encontrado.
    
- O usuário não tem autorização para acessar um recurso.
    
- Uma operação no banco de dados falha.
    
- O código contém um erro inesperado.
    

Sem tratamento adequado, a aplicação pode responder com uma página genérica ou apresentar uma resposta que não representa corretamente o problema.

O tratamento de erros permite definir respostas apropriadas para situações conhecidas.

Os códigos HTTP ajudam a comunicar o resultado:

|Código|Significado|
|---|---|
|`400`|Requisição inválida.|
|`401`|Autenticação necessária ou inválida, conforme o contexto.|
|`403`|Acesso proibido.|
|`404`|Recurso não encontrado.|
|`500`|Erro interno do servidor.|

O código adequado depende do motivo da falha. Por exemplo, um recurso inexistente normalmente resulta em `404`, enquanto uma exceção inesperada no servidor normalmente resulta em `500`.

### 9.8. Interrompendo uma requisição com `abort()`

O Flask disponibiliza `abort()` para interromper o processamento e gerar um erro HTTP.

```
from flask import abort

@app.route("/perfil/<int:user_id>")
def perfil(user_id):
    if user_id <= 0:
        abort(404)

    return f"Perfil do usuário {user_id}"
```

Nesse exemplo:

- A rota recebe um identificador inteiro.
    
- Se o valor for menor ou igual a zero, a execução é interrompida.
    
- `abort(404)` provoca um erro HTTP `404`.
    
- Se a condição não for atendida, a função retorna o conteúdo normalmente.
    

O exemplo é ilustrativo: em uma aplicação real, o identificador deve ser utilizado para procurar o usuário no banco de dados. Se o registro não existir, `abort(404)` pode representar essa situação.

`abort()` não é exclusivo de `404`. Ele também pode ser utilizado com outros códigos HTTP apropriados.

### 9.9. Criando páginas de erro personalizadas

Podemos utilizar `errorhandler()` para definir como a aplicação responde a um determinado código de erro.

```
from flask import render_template

@app.errorhandler(404)
def pagina_nao_encontrada(error):
    return render_template("errors/404.html"), 404
```

O decorador `@app.errorhandler(404)` registra uma função para tratar erros `404` da aplicação.

A função recebe o objeto de erro e retorna uma resposta HTTP. Neste exemplo, a resposta contém o template e o código de status `404`.

O arquivo correspondente pode ficar em:

```
templates/
└── errors/
    └── 404.html
```

Um template simples:

```
<!doctype html>
<html lang="pt-BR">
<head>
    <meta charset="utf-8">
    <title>Página não encontrada</title>
</head>
<body>
    <h1>Página não encontrada</h1>
    <p>O recurso solicitado não existe.</p>
    <a href="/">Voltar ao início</a>
</body>
</html>
```

O HTML apresentado é independente do tratamento feito pelo Flask. O framework encontra e renderiza o arquivo por meio de `render_template()`.

**Importante:** o código HTTP deve continuar correto. Se a página informa que um recurso não foi encontrado, retornar `200` pode confundir navegadores, mecanismos de busca, clientes de API e sistemas de monitoramento.

### 9.10. Tratando erros internos com `500`

Um erro `500` indica que ocorreu um problema interno ao processar a requisição.

Podemos definir uma página de erro específica:

```
@app.errorhandler(500)
def erro_interno(error):
    return render_template("errors/500.html"), 500
```

O arquivo pode ficar em `templates/errors/500.html`.

O usuário pode receber uma mensagem simples, como:

```
<h1>Algo deu errado</h1>
<p>Não foi possível concluir sua solicitação.</p>
```

A resposta pública não deve incluir traceback, caminho de arquivos internos, variáveis de ambiente, credenciais ou detalhes sensíveis do banco de dados.

Os detalhes técnicos devem ser investigados nos logs, com acesso restrito às pessoas responsáveis pelo sistema.

O Flask normalmente transforma exceções não tratadas em respostas `500` durante o processamento de requisições. Um manipulador personalizado pode definir a apresentação dessa resposta, mas não corrige a causa original do problema.

### 9.11. A diferença entre erro HTTP e exceção Python

Nem todo erro é igual.

Um `404` pode ocorrer porque uma rota não existe ou porque o código decidiu que um recurso não foi encontrado. Já uma exceção Python, como `TypeError`, pode indicar um problema inesperado na implementação.

Por exemplo:

```
def dividir(a, b):
    return a / b

resultado = dividir(10, 0)
```

A divisão por zero gera `ZeroDivisionError`.

Em uma rota Flask, uma exceção inesperada como essa pode resultar em uma resposta `500` se não for tratada.

Não é recomendável converter todo erro em `404` ou `400` sem analisar sua causa. Os códigos HTTP devem representar a situação real.

### 9.12. O que é logging?

Logging é o registro de eventos que acontecem durante a execução de um programa.

Em uma aplicação web, esses registros podem ajudar a responder perguntas como:

- Qual operação falhou?
    
- Em qual parte da aplicação ocorreu o problema?
    
- A falha foi esperada ou inesperada?
    
- A aplicação está recebendo requisições anormais?
    
- Uma integração externa está apresentando problemas?
    

O Python já possui o módulo padrão `logging`. Não é necessário instalar uma biblioteca externa apenas para começar a registrar mensagens.

O Flask também disponibiliza `app.logger`, um logger associado à aplicação.

### 9.13. Níveis de log

Os níveis indicam a gravidade ou a importância de cada registro.

|Nível|Uso comum|
|---|---|
|`DEBUG`|Detalhes úteis durante o desenvolvimento.|
|`INFO`|Eventos normais e relevantes da aplicação.|
|`WARNING`|Situações inesperadas que não interromperam necessariamente a operação.|
|`ERROR`|Falhas que impediram uma operação de funcionar corretamente.|
|`CRITICAL`|Problemas graves que podem comprometer a continuidade do serviço.|

Exemplo:

```
app.logger.debug("Iniciando processamento")
app.logger.info("Operação concluída")
app.logger.warning("Tentativa com parâmetro inesperado")
app.logger.error("Não foi possível concluir a operação")
```

O nível apropriado depende do evento. Não registre toda requisição como `ERROR`, pois isso dificulta encontrar falhas reais.

### 9.14. Registrando uma exceção com traceback

Quando uma exceção é capturada, `app.logger.exception()` pode registrar uma mensagem junto com o traceback da exceção atual.

```
@app.route("/relatorio")
def gerar_relatorio():
    try:
        resultado = calcular_relatorio()
        return resultado
    except Exception:
        app.logger.exception("Falha ao gerar o relatório")
        return "Não foi possível gerar o relatório.", 500
```

Nesse exemplo, `calcular_relatorio()` representa uma função hipotética da aplicação.

O fluxo é:

1. O código tenta gerar o relatório.
    
2. Se a operação terminar normalmente, retorna o resultado.
    
3. Se uma exceção for lançada, o bloco `except` será executado.
    
4. `app.logger.exception()` registra a mensagem e os detalhes da exceção atual.
    
5. O usuário recebe uma resposta genérica.
    

Esse exemplo mostra o mecanismo, mas capturar `Exception` indiscriminadamente pode esconder problemas que deveriam ser corrigidos ou propagados. Prefira capturar exceções específicas quando souber quais falhas são esperadas.

Por exemplo, ao converter um texto em inteiro, pode fazer sentido tratar `ValueError`. Já um erro de programação inesperado pode precisar ser registrado e tratado por um mecanismo central de erros.

Não retorne `str(error)` diretamente ao usuário: a mensagem pode revelar detalhes internos.

### 9.15. `logger.error()` versus `logger.exception()`

Os dois métodos servem para registrar falhas, mas não são intercambiáveis em todos os contextos.

```
app.logger.error("Não foi possível salvar o registro")
```

Registra a mensagem de erro.

```
try:
    salvar_registro()
except Exception:
    app.logger.exception("Falha ao salvar o registro")
```

Registra a mensagem e as informações da exceção ativa, incluindo o traceback.

Use `exception()` dentro de um bloco `except` quando precisar preservar o contexto técnico da falha. Fora desse contexto, o traceback da exceção atual pode não existir.

### 9.16. Não registre informações sensíveis

Logs podem ser úteis para diagnóstico, mas também podem se tornar um risco de segurança.

Evite registrar:

- Senhas.
    
- Tokens de autenticação.
    
- Chaves secretas.
    
- Cookies de sessão completos.
    
- Dados pessoais desnecessários.
    
- Conteúdo integral de requisições que possa conter credenciais.
    

Prefira mensagens que identifiquem a operação e o tipo de falha sem expor os dados utilizados.

Por exemplo:

```
app.logger.warning(
    "Falha na autenticação de uma tentativa de acesso"
)
```

Essa mensagem é mais segura do que registrar a senha recebida ou o token utilizado na tentativa.

Em aplicações maiores, também é importante controlar quem pode acessar os logs, por quanto tempo eles serão mantidos e como serão protegidos.

### 9.17. Configuração, extensões e ordem de inicialização

A ordem de inicialização importa. Algumas extensões precisam conhecer as configurações antes de serem inicializadas.

Por exemplo, uma extensão de banco de dados pode precisar da URI de conexão antes de receber a aplicação.

O padrão geral é:

1. Criar a aplicação.
    
2. Carregar as configurações.
    
3. Validar as configurações obrigatórias.
    
4. Inicializar as extensões.
    
5. Registrar Blueprints e manipuladores de erro.
    
6. Retornar a aplicação configurada.
    

A ordem exata pode variar conforme a extensão, mas carregar as configurações antes de inicializar componentes que dependem delas evita problemas difíceis de diagnosticar.

### 9.18. Erros comuns e como investigá-los

**A aplicação informa que** `**SECRET_KEY**` **não foi definida**

- Verifique se a variável de ambiente existe.
    
- Confirme que a aplicação está sendo executada no mesmo ambiente em que a variável foi definida.
    
- Confira se o nome utilizado no código corresponde ao nome da variável.
    

**As alterações de configuração parecem não ter efeito**

- Verifique se a configuração foi carregada na instância correta.
    
- Confira se outro trecho do código está sobrescrevendo o valor.
    
- Verifique se a configuração foi definida antes da inicialização da extensão dependente dela.
    

**A página personalizada de erro não aparece**

- Confirme que o manipulador foi registrado na aplicação correta.
    
- Verifique se o template existe no diretório esperado.
    
- Confira o código HTTP efetivamente retornado.
    
- Verifique se o erro ocorre durante o processamento de uma requisição e se o manipulador é aplicável a ele.
    

**O log não mostra o traceback esperado**

- Confirme se a exceção está sendo capturada no bloco correto.
    
- Utilize `logger.exception()` dentro do `except` quando precisar do traceback.
    
- Verifique os níveis de log e o destino configurado para os registros.
    

**A aplicação funciona localmente, mas falha em produção**

- Compare as variáveis de ambiente dos dois ambientes.
    
- Verifique as configurações de banco de dados e serviços externos.
    
- Examine os logs do servidor.
    
- Não habilite o depurador público como solução para investigar a falha.
    

**Um bloco** `**except**` **parece resolver tudo, mas os erros continuam**

- Verifique se o código está capturando exceções demais.
    
- Capture os tipos de exceção que realmente espera.
    
- Registre o contexto necessário para diagnosticar a falha.
    
- Não transforme silenciosamente uma operação malsucedida em uma resposta de sucesso.

---
## 10. Segurança Web no Flask

Segurança web é o conjunto de práticas utilizadas para proteger uma aplicação, seus usuários, seus dados e seus recursos contra acessos indevidos e entradas maliciosas.

O Flask oferece mecanismos úteis de segurança, mas não torna uma aplicação segura automaticamente. O desenvolvedor precisa compreender como as requisições são processadas, como os dados são armazenados e quais operações cada usuário pode executar.

Neste módulo, vamos estudar os principais riscos encontrados em aplicações Flask e como reduzi-los.

### 10.1. Os princípios básicos da segurança web

Antes de analisar vulnerabilidades específicas, é importante compreender alguns princípios.

**Validação de entrada:** os dados recebidos do usuário precisam ser verificados antes de serem utilizados.

**Autorização:** mesmo que um usuário esteja autenticado, ele só deve acessar os recursos para os quais possui permissão.

**Confidencialidade:** senhas, tokens e outras informações sensíveis não devem ser expostos indevidamente.

**Integridade:** a aplicação deve impedir alterações não autorizadas nos dados.

**Privilégio mínimo:** cada usuário e componente deve possuir apenas as permissões necessárias para executar suas funções.

Esses princípios se aplicam tanto a aplicações pequenas quanto a sistemas com banco de dados, APIs e múltiplos níveis de acesso.

### 10.2. SQL Injection

SQL Injection ocorre quando dados controlados pelo usuário são incorporados de maneira insegura a uma consulta SQL, permitindo que a estrutura da consulta seja alterada.

Considere um exemplo inseguro:

```
query = (
    "SELECT * FROM usuarios "
    f"WHERE email = '{email}'"
)
```

Se `email` contiver caracteres especiais ou fragmentos de SQL, a consulta final poderá ter um significado diferente do pretendido.

O problema não é simplesmente receber caracteres especiais. O problema é misturar dados externos diretamente com a estrutura do comando SQL.

#### Utilizando parâmetros

Uma maneira adequada de evitar essa situação é utilizar consultas parametrizadas.

Exemplo com SQLite:

```
import sqlite3

conexao = sqlite3.connect("app.db")

email = "usuario@exemplo.com"

cursor = conexao.execute(
    "SELECT id, email FROM usuarios WHERE email = ?",
    (email,),
)

usuarios = cursor.fetchall()
```

O `?` representa um parâmetro da consulta. O valor de `email` é fornecido separadamente, permitindo que o driver trate o conteúdo como dado, e não como parte da estrutura SQL.

A sintaxe dos marcadores depende do driver utilizado. Outros bancos de dados podem utilizar formatos diferentes.

#### Utilizando Flask-SQLAlchemy

Em uma aplicação que utiliza SQLAlchemy, prefira as operações da ORM ou consultas parametrizadas.

```
usuario = db.session.execute(
    db.select(Usuario).where(Usuario.email == email)
).scalar_one_or_none()
```

Nesse exemplo:

- `db.select(Usuario)` constrói uma consulta para o modelo `Usuario`.
    
- `.where(Usuario.email == email)` define a condição.
    
- `scalar_one_or_none()` retorna um resultado ou `None` quando nenhum registro corresponde à consulta.
    

O exemplo pressupõe que `db` seja a instância configurada do SQLAlchemy e `Usuario` seja um modelo existente.

**Boa prática:** não construa consultas SQL por concatenação de strings com dados externos.

### 10.3. XSS — Cross-Site Scripting

XSS ocorre quando conteúdo controlado por um usuário é interpretado pelo navegador como código executável, geralmente JavaScript.

Imagine uma aplicação que recebe o nome de uma pessoa e o exibe em uma página. Se a aplicação inserir o valor diretamente no HTML sem tratamento adequado, um conteúdo malicioso poderá ser interpretado pelo navegador.

#### Exibição segura com Jinja

O Flask utiliza Jinja como mecanismo de templates. Em arquivos HTML, a saída de expressões normalmente é escapada automaticamente.

```
<p>Olá, {{ nome }}!</p>
```

Se `nome` contiver conteúdo semelhante a uma tag HTML, o escape automático de Jinja impede que esse conteúdo seja interpretado como marcação executável no contexto HTML comum.

Por exemplo, uma entrada contendo uma tag de script tende a ser exibida como texto escapado, em vez de ser executada.

Esse comportamento depende do contexto e da configuração do template. Não significa que todo tipo de saída seja automaticamente seguro.

#### Evite desativar o escape sem necessidade

O filtro `safe` informa ao Jinja que a saída deve ser tratada como HTML confiável:

```
{{ conteudo | safe }}
```

Isso pode introduzir uma vulnerabilidade se `conteudo` tiver sido fornecido por um usuário ou vier de uma fonte não confiável.

Use `safe` apenas quando tiver certeza de que o conteúdo foi construído ou sanitizado de maneira apropriada para o contexto HTML.

**Regra prática:** não transforme entrada de usuário em HTML confiável apenas porque ela precisa aparecer na página.

### 10.4. CSRF — Cross-Site Request Forgery

CSRF é um ataque no qual um navegador autenticado é induzido a enviar uma requisição não desejada para uma aplicação em que o usuário já possui uma sessão válida.

Por exemplo, imagine uma aplicação em que um formulário permite alterar o endereço de e-mail de uma conta. Se a operação depender apenas do cookie de sessão, um site externo pode tentar induzir o navegador a enviar uma requisição usando essa sessão.

O problema é que o navegador pode incluir automaticamente cookies associados ao site de destino.

#### Proteção com Flask-WTF

Uma solução comum em aplicações Flask que utilizam formulários HTML é a extensão Flask-WTF, que oferece proteção CSRF.

Instalação:

```
pip install Flask-WTF
```

Configuração:

```
from flask import Flask
from flask_wtf.csrf import CSRFProtect

app = Flask(__name__)
app.config["SECRET_KEY"] = "substitua-por-uma-chave-segura"

csrf = CSRFProtect(app)
```

A chave literal é apenas ilustrativa. Em um projeto real, carregue-a de uma variável de ambiente, conforme estudado no módulo anterior.

Com a proteção CSRF habilitada, as requisições modificadoras de estado precisam fornecer um token válido, conforme as regras da extensão.

Em formulários renderizados pelo Flask-WTF:

```
<form method="POST">
    {{ form.hidden_tag() }}

    {{ form.email.label }}
    {{ form.email() }}

    <button type="submit">Salvar</button>
</form>
```

`hidden_tag()` inclui os campos ocultos necessários ao formulário, incluindo o token CSRF quando a proteção está configurada.

Em formulários HTML comuns, sem uma classe de formulário Flask-WTF, é necessário inserir o token CSRF de acordo com a configuração adotada.

Não desative a proteção CSRF indiscriminadamente para fazer um formulário funcionar. Investigue por que o token está ausente ou inválido.

### 10.5. Autenticação não é autorização

Esses conceitos são relacionados, mas diferentes.

**Autenticação** responde: quem é você?

**Autorização** responde: o que você pode fazer?

Uma aplicação pode autenticar corretamente um usuário e ainda apresentar uma falha de autorização caso permita que ele acesse dados de outras pessoas.

Considere uma rota:

```
@app.route("/documentos/<int:documento_id>")
@login_required
def visualizar_documento(documento_id):
    documento = db.session.get(Documento, documento_id)

    if documento is None:
        abort(404)

    return render_template(
        "documentos/detalhe.html",
        documento=documento,
    )
```

O decorador `@login_required` exige autenticação, mas **não verifica se o documento pertence ao usuário atual**.

Se a aplicação não realizar essa verificação, um usuário autenticado poderá tentar alterar o identificador na URL para acessar documentos de outras pessoas.

Uma verificação adicional pode ser necessária:

```
if documento.usuario_id != current_user.id:
    abort(403)
```

Esse trecho pressupõe que o modelo `Documento` tenha o campo `usuario_id` e que o usuário autenticado seja representado por `current_user`.

A regra correta depende do sistema: documentos compartilhados, administradores e usuários com permissões especiais podem precisar de políticas diferentes.

O importante é verificar a autorização no servidor, a cada operação protegida. Esconder um botão no HTML não substitui essa verificação.

### 10.6. IDOR e acesso a objetos

IDOR, sigla de _Insecure Direct Object Reference_, descreve uma situação em que uma aplicação permite acessar um objeto sem verificar adequadamente se o usuário possui autorização.

A vulnerabilidade pode aparecer quando o sistema utiliza identificadores previsíveis em URLs, formulários ou APIs.

Exemplo conceitual:

```
/documentos/100
/documentos/101
/documentos/102
```

Um identificador numérico não é, por si só, uma vulnerabilidade. O problema aparece quando a aplicação confia no identificador recebido sem validar as permissões de acesso.

Uma estratégia é buscar o objeto já considerando o usuário autorizado:

```
documento = db.session.execute(
    db.select(Documento).where(
        Documento.id == documento_id,
        Documento.usuario_id == current_user.id,
    )
).scalar_one_or_none()

if documento is None:
    abort(404)
```

Essa consulta pressupõe que `Documento` possua os campos `id` e `usuario_id`.

Ao limitar a consulta aos documentos do usuário, o código evita retornar diretamente um registro pertencente a outra pessoa. Dependendo dos requisitos do sistema, a aplicação pode optar por responder `403` ou `404` para objetos sem acesso.

**Importante:** a autorização deve ser aplicada a todas as operações relevantes, incluindo visualizar, editar, excluir, exportar e baixar arquivos.

### 10.7. Senhas e armazenamento seguro

Senhas não devem ser armazenadas em texto puro no banco de dados.

Se o banco for exposto, o armazenamento em texto puro revela diretamente as credenciais dos usuários.

Uma alternativa é armazenar hashes de senha gerados por algoritmos apropriados para senhas, como os disponibilizados por Werkzeug.

```
from werkzeug.security import generate_password_hash

senha = "senha-fornecida-pelo-usuario"

hash_senha = generate_password_hash(senha)
```

Para verificar a senha durante o login:

```
from werkzeug.security import check_password_hash

senha_valida = check_password_hash(
    hash_senha,
    senha_informada,
)
```

`generate_password_hash()` produz um hash apropriado para armazenar a senha. `check_password_hash()` compara a senha fornecida com o hash armazenado.

A verificação retorna um valor booleano que pode ser utilizado no fluxo de autenticação.

Não compare a senha recebida diretamente com o hash, porque o processo de geração inclui mecanismos como salt e parâmetros de derivação que tornam a comparação direta inadequada.

Também não utilize criptografia reversível como substituto de hashing de senhas. A aplicação normalmente precisa verificar uma senha, não recuperar seu texto original.

### 10.8. Cookies de sessão

O Flask oferece sessões para manter dados associados a um usuário entre requisições.

```
from flask import session

session["tema"] = "escuro"
```

Esse exemplo armazena uma preferência simples na sessão.

Na configuração padrão do Flask, os dados da sessão são armazenados em um cookie assinado pelo servidor. A assinatura ajuda a detectar alterações não autorizadas, mas **não torna o conteúdo secreto**.

Não armazene senhas, tokens privados ou informações confidenciais diretamente na sessão padrão.

Algumas configurações importantes:

```
app.config.update(
    SESSION_COOKIE_HTTPONLY=True,
    SESSION_COOKIE_SECURE=True,
    SESSION_COOKIE_SAMESITE="Lax",
)
```

- `SESSION_COOKIE_HTTPONLY=True`: impede o acesso ao cookie por JavaScript comum.
    
- `SESSION_COOKIE_SECURE=True`: restringe o envio do cookie a conexões HTTPS.
    
- `SESSION_COOKIE_SAMESITE="Lax"`: aplica uma política que restringe parte do envio de cookies em contextos entre sites.
    

Em desenvolvimento local sem HTTPS, `SESSION_COOKIE_SECURE=True` pode impedir que o navegador envie o cookie por HTTP. Configure os ambientes de acordo com a forma de execução e mantenha a proteção adequada em produção.

Essas opções ajudam a reduzir riscos, mas não substituem a proteção contra XSS, CSRF e falhas de autorização.

### 10.9. Validação de dados

Toda entrada externa deve ser tratada como não confiável até que seja validada.

As entradas podem vir de:

- Parâmetros de URL.
    
- Query strings.
    
- Formulários.
    
- JSON enviado a uma API.
    
- Cabeçalhos HTTP.
    
- Cookies.
    
- Arquivos enviados pelo usuário.
    
- Serviços externos.
    

Considere uma rota que recebe uma quantidade:

```
from flask import request, abort

@app.route("/comprar", methods=["POST"])
def comprar():
    quantidade_texto = request.form.get("quantidade", "")

    try:
        quantidade = int(quantidade_texto)
    except ValueError:
        abort(400)

    if quantidade < 1 or quantidade > 100:
        abort(400)

    return f"Quantidade solicitada: {quantidade}"
```

O código:

1. Obtém o valor enviado pelo formulário.
    
2. Tenta convertê-lo para inteiro.
    
3. Responde com `400` se a conversão falhar.
    
4. Verifica se o valor está dentro do intervalo permitido.
    

A validação deve corresponder às regras reais do negócio. Verificar apenas o tipo de um dado não garante que ele seja permitido.

Por exemplo, uma quantidade pode ser um inteiro válido e ainda exceder o limite aceito pela aplicação.

### 10.10. Upload de arquivos

Uploads exigem cuidado porque arquivos recebidos de usuários podem conter conteúdo malicioso, ser excessivamente grandes ou tentar sobrescrever arquivos existentes.

Medidas importantes incluem:

- Limitar o tamanho da requisição.
    
- Validar extensões e tipos de arquivo permitidos.
    
- Não confiar apenas no nome ou no `Content-Type` fornecido pelo navegador.
    
- Gerar nomes de armazenamento controlados pela aplicação.
    
- Evitar salvar arquivos em locais onde possam ser executados diretamente.
    
- Aplicar autorização ao upload e ao download.
    
- Considerar análise adicional dos arquivos, conforme o risco do sistema.
    

O Flask oferece `secure_filename()` por meio de Werkzeug para reduzir problemas com nomes de arquivos:

```
from werkzeug.utils import secure_filename

nome_seguro = secure_filename(
    arquivo.filename or ""
)
```

Isso ajuda a normalizar o nome, mas **não valida o conteúdo do arquivo nem garante que ele seja seguro**.

Para arquivos sensíveis, considere armazenar os arquivos fora do diretório público da aplicação e disponibilizá-los por uma rota que verifique as permissões do usuário.

### 10.11. Proteção contra exposição de informações

Mensagens de erro detalhadas são úteis durante o desenvolvimento, mas podem revelar informações internas se forem expostas publicamente.

Evite apresentar ao usuário:

- Tracebacks completos.
    
- Caminhos internos do servidor.
    
- Strings de conexão com o banco de dados.
    
- Chaves secretas.
    
- Tokens de autenticação.
    
- Detalhes da infraestrutura.
    

Em vez disso, apresente uma mensagem genérica e registre os detalhes técnicos em logs protegidos.

Também não coloque segredos em arquivos versionados, mensagens de erro, respostas de API ou logs de depuração.

### 10.12. HTTPS e comunicação segura

HTTPS protege a comunicação entre o cliente e o servidor usando TLS.

Sem HTTPS, informações transmitidas pela rede podem ficar expostas a quem consiga observar ou modificar o tráfego.

Em produção:

- Configure um certificado TLS válido.
    
- Utilize HTTPS para páginas, formulários e APIs.
    
- Configure cookies de sessão para serem enviados somente por HTTPS.
    
- Evite transmitir credenciais por conexões não criptografadas.
    
- Se houver um proxy reverso, configure corretamente a comunicação entre o proxy e a aplicação.
    

A configuração do Flask não substitui a configuração TLS do servidor web, proxy ou plataforma de hospedagem.

### 10.13. CORS não é um mecanismo de autenticação

CORS, sigla de _Cross-Origin Resource Sharing_, é um mecanismo do navegador que controla como páginas de uma origem podem acessar recursos de outra origem.

Ele é relevante principalmente para aplicações que disponibilizam APIs consumidas por páginas web em outros domínios.

Permitir uma origem no CORS não comprova a identidade do cliente e não substitui autenticação ou autorização.

Da mesma forma, restringir CORS não protege automaticamente todos os tipos de clientes. Ferramentas de linha de comando e outros programas podem fazer requisições sem aplicar as mesmas restrições de navegador.

Configure CORS de acordo com as origens realmente necessárias, evitando permissões amplas sem justificativa.

### 10.14. Checklist de segurança para uma aplicação Flask

Antes de disponibilizar uma aplicação publicamente, verifique os seguintes pontos:

- O modo de depuração não está exposto em produção.
    
- A `SECRET_KEY` é forte, estável e mantida fora do código versionado.
    
- Consultas ao banco de dados utilizam parâmetros ou recursos seguros da ORM.
    
- Templates escapam corretamente conteúdo não confiável.
    
- A aplicação não utiliza `safe` indiscriminadamente.
    
- Formulários que modificam dados estão protegidos contra CSRF quando aplicável.
    
- As rotas verificam autenticação e autorização no servidor.
    
- As senhas são armazenadas usando hashes apropriados.
    
- Os cookies de sessão possuem configurações adequadas ao ambiente.
    
- Entradas externas são validadas conforme as regras do negócio.
    
- Uploads têm limites, validações e armazenamento apropriado.
    
- Respostas públicas não revelam detalhes internos.
    
- Logs não armazenam senhas, tokens ou outros segredos.
    
- A comunicação externa utiliza HTTPS.
    
- As dependências são mantidas atualizadas e avaliadas quanto a vulnerabilidades.
    

Essas medidas reduzem riscos comuns, mas a segurança também depende da arquitetura, das permissões do banco de dados, da infraestrutura e das regras específicas de cada aplicação.

---
## 11. Construção de APIs com Flask

Uma API (_Application Programming Interface_) permite que diferentes aplicações se comuniquem por meio de uma interface definida.

Em uma aplicação web tradicional, uma rota pode retornar HTML para ser exibido diretamente pelo navegador. Em uma API, uma rota frequentemente retorna dados estruturados, como JSON, que podem ser consumidos por um frontend, um aplicativo móvel, outro servidor ou um programa Python.

O Flask permite construir APIs sem exigir uma biblioteca adicional para as funcionalidades básicas de roteamento e respostas JSON.

### 11.1. Como funciona uma API HTTP

Considere uma aplicação que gerencia usuários. Um cliente pode solicitar uma lista de usuários, criar um registro ou consultar um usuário específico.

A comunicação normalmente segue este fluxo:

1. O cliente envia uma requisição HTTP.
    
2. O Flask identifica a rota e o método HTTP.
    
3. A função associada à rota processa a requisição.
    
4. A aplicação valida os dados e executa a operação necessária.
    
5. O servidor devolve uma resposta HTTP com um código de status e, frequentemente, um corpo JSON.
    

Exemplo de uma requisição:

```
GET /api/usuarios HTTP/1.1
Host: exemplo.com
Accept: application/json
```

Uma resposta possível:

```
HTTP/1.1 200 OK
Content-Type: application/json
```

```
{
  "usuarios": [
    {
      "id": 1,
      "nome": "Ana"
    },
    {
      "id": 2,
      "nome": "Carlos"
    }
  ]
}
```

O cliente não precisa receber uma página HTML completa. Ele pode interpretar os dados JSON e decidir como apresentá-los.

### 11.2. O que é JSON?

JSON (_JavaScript Object Notation_) é um formato textual para representar dados estruturados. Apesar de seu nome, é utilizado por aplicações escritas em diversas linguagens.

Os principais tipos de dados JSON são:

|Tipo JSON|Exemplo|
|---|---|
|Objeto|`{"nome": "Ana"}`|
|Array|`["Python", "Flask"]`|
|String|`"Olá"`|
|Número|`42`|
|Booleano|`true` ou `false`|
|Nulo|`null`|

Um objeto JSON se parece com um dicionário Python:

```
usuario = {
    "id": 1,
    "nome": "Ana",
    "ativo": True,
}
```

O Flask consegue converter estruturas Python compatíveis em uma resposta JSON.

É importante observar que os valores booleanos e nulos possuem representações diferentes entre Python e JSON: `True` corresponde a `true`, `False` a `false` e `None` a `null`.

### 11.3. Criando uma rota que retorna JSON

O Flask disponibiliza `jsonify()` para criar respostas JSON.

```
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/api/status")
def status():
    return jsonify({
        "status": "online",
        "versao": "1.0",
    })
```

Analisando o código:

- `Flask(__name__)` cria a aplicação.
    
- `@app.route("/api/status")` registra a rota.
    
- `status()` é a função executada quando a rota corresponde à requisição.
    
- `jsonify()` converte o dicionário em uma resposta JSON.
    
- O Flask define o tipo de conteúdo da resposta como JSON.
    

Ao acessar `/api/status`, o cliente recebe uma resposta semelhante a:

```
{
  "status": "online",
  "versao": "1.0"
}
```

O valor `1.0` é uma string nesse exemplo porque foi definido entre aspas.

### 11.4. `jsonify()` versus `json.dumps()`

Essas ferramentas estão relacionadas, mas possuem finalidades diferentes.

`**jsonify()**`

É disponibilizado pelo Flask e constrói uma resposta HTTP JSON apropriada para uma rota.

```
return jsonify({"mensagem": "Olá"})
```

`**json.dumps()**`

É disponibilizado pelo módulo padrão `json` do Python e converte um objeto Python compatível em uma string JSON.

```
import json

texto_json = json.dumps({
    "mensagem": "Olá"
})
```

`json.dumps()` não cria, por si só, uma resposta Flask com os cabeçalhos HTTP apropriados. Para uma rota comum, `jsonify()` costuma ser a opção mais direta.

### 11.5. Métodos HTTP e operações da API

O método HTTP indica a intenção da requisição. Em APIs que seguem convenções REST, alguns métodos são associados a operações comuns.

|Método|Uso habitual|
|---|---|
|`GET`|Consultar recursos.|
|`POST`|Criar um recurso ou executar uma operação.|
|`PUT`|Substituir completamente a representação de um recurso.|
|`PATCH`|Atualizar parcialmente um recurso.|
|`DELETE`|Excluir um recurso.|

Essas convenções ajudam a tornar uma API previsível, mas o comportamento exato depende do contrato definido pela aplicação.

Por padrão, uma rota Flask simples aceita `GET` e pode responder a `HEAD`. Para aceitar outros métodos, precisamos configurá-los.

```
@app.route("/api/usuarios", methods=["GET", "POST"])
def usuarios():
    ...
```

A rota acima permite tanto `GET` quanto `POST`. O tratamento deve diferenciar as operações para que cada método tenha o comportamento esperado.

### 11.6. Construindo uma API com múltiplos métodos

Vamos criar um exemplo didático com dados mantidos em memória.

```
from flask import Flask, jsonify, request

app = Flask(__name__)

usuarios = [
    {"id": 1, "nome": "Ana"},
    {"id": 2, "nome": "Carlos"},
]

@app.route("/api/usuarios", methods=["GET"])
def listar_usuarios():
    return jsonify({"usuarios": usuarios})
```

A rota `GET /api/usuarios` retorna os usuários existentes.

A estrutura é simples, mas há uma limitação importante: os dados estão em uma lista Python. Eles não são persistidos em um banco de dados e podem desaparecer quando o processo da aplicação for reiniciado.

Esse modelo serve para aprender o funcionamento das rotas, não para armazenar dados de uma aplicação real.

### 11.7. Recebendo JSON com `request.get_json()`

Quando um cliente envia dados JSON, podemos utilizar `request.get_json()` para obter o conteúdo convertido em estruturas Python.

Exemplo de requisição:

```
POST /api/usuarios
Content-Type: application/json
```

```
{
  "nome": "Mariana"
}
```

No Flask:

```
from flask import request

@app.route("/api/exemplo", methods=["POST"])
def exemplo():
    dados = request.get_json()

    return jsonify({
        "recebido": dados
    })
```

Se o cliente enviar um objeto JSON válido, `dados` normalmente será um dicionário Python.

A chamada não garante que os dados tenham o formato esperado pela aplicação. É necessário validar os campos, seus tipos e seus valores.

Nas versões atuais do Flask, `request.get_json()` pode produzir um erro HTTP `415 Unsupported Media Type` quando o tipo de conteúdo não indica JSON. Conteúdo JSON inválido também pode resultar em erro de requisição. O comportamento específico depende dos argumentos utilizados e da versão instalada.

### 11.8. Validando dados recebidos

Não devemos presumir que uma requisição contém todos os campos necessários.

Uma rota que cria um usuário precisa verificar se o nome foi informado e se está no formato aceito.

```
from flask import request, jsonify

@app.route("/api/usuarios", methods=["POST"])
def criar_usuario():
    dados = request.get_json()

    if not isinstance(dados, dict):
        return jsonify({
            "erro": "O corpo deve ser um objeto JSON."
        }), 400

    nome = dados.get("nome")

    if not isinstance(nome, str) or not nome.strip():
        return jsonify({
            "erro": "O campo 'nome' é obrigatório."
        }), 400

    return jsonify({
        "mensagem": "Dados válidos.",
        "nome": nome.strip(),
    }), 201
```

Analisando:

1. `request.get_json()` tenta interpretar o corpo da requisição como JSON.
    
2. `isinstance(dados, dict)` verifica se o resultado é um objeto representado por um dicionário Python.
    
3. `dados.get("nome")` procura o campo sem gerar `KeyError` caso ele não exista.
    
4. A validação verifica se o valor é uma string não vazia.
    
5. `strip()` remove espaços no início e no final.
    
6. O código `400` indica que os dados enviados são inválidos.
    
7. O código `201` indica que um recurso foi criado.
    

O exemplo valida os dados, mas ainda não salva um usuário. Uma API real precisa executar a operação de persistência e lidar com erros do banco de dados.

### 11.9. Códigos de status HTTP

Uma API deve retornar códigos de status coerentes com o resultado da operação.

|Código|Significado comum em APIs|
|---|---|
|`200 OK`|A operação foi concluída com sucesso.|
|`201 Created`|Um recurso foi criado.|
|`204 No Content`|A operação teve sucesso e não há conteúdo a retornar.|
|`400 Bad Request`|A requisição é inválida.|
|`401 Unauthorized`|A autenticação está ausente ou é inválida.|
|`403 Forbidden`|O cliente não tem permissão para executar a operação.|
|`404 Not Found`|O recurso não foi encontrado.|
|`405 Method Not Allowed`|O método não é permitido para aquela rota.|
|`409 Conflict`|A operação conflita com o estado atual do recurso.|
|`415 Unsupported Media Type`|O tipo de conteúdo enviado não é aceito.|
|`422 Unprocessable Content`|O conteúdo foi entendido, mas não atende às regras de validação adotadas pela API.|
|`500 Internal Server Error`|Ocorreu um erro inesperado no servidor.|

O significado exato deve ser aplicado de acordo com o contexto e o contrato da API. Por exemplo, uma aplicação pode optar por utilizar `400` ou `422` para determinadas falhas de validação, desde que mantenha um comportamento coerente.

### 11.10. Retornando status e cabeçalhos

Uma função de rota Flask pode retornar o corpo e o código HTTP juntos:

```
return jsonify({"mensagem": "Criado"}), 201
```

Também podemos construir uma resposta e configurar seus cabeçalhos:

```
resposta = jsonify({
    "mensagem": "Operação concluída"
})

resposta.headers["X-App-Version"] = "1.0"

return resposta, 200
```

O cabeçalho `X-App-Version` é um exemplo ilustrativo. Cabeçalhos personalizados devem ter uma finalidade clara e não devem expor informações sensíveis.

### 11.11. Consultando um recurso pelo identificador

Em APIs, é comum utilizar um parâmetro na URL para identificar um recurso específico.

```
@app.route("/api/usuarios/<int:usuario_id>")
def obter_usuario(usuario_id):
    usuario = next(
        (
            item
            for item in usuarios
            if item["id"] == usuario_id
        ),
        None,
    )

    if usuario is None:
        return jsonify({
            "erro": "Usuário não encontrado."
        }), 404

    return jsonify(usuario), 200
```

O conversor `<int:usuario_id>` restringe o parâmetro da rota a um inteiro.

A expressão `next(..., None)` procura o primeiro usuário cujo identificador corresponda ao recebido. Se não houver correspondência, o resultado será `None`.

Se o usuário não existir, a API retorna `404`. Caso exista, retorna seus dados em JSON.

Esse exemplo depende da lista `usuarios` definida anteriormente.

Em uma aplicação real, a busca deve normalmente ocorrer no banco de dados. Além disso, o servidor precisa verificar se o cliente tem autorização para acessar aquele registro.

### 11.12. Atualizando um recurso

Uma API pode permitir atualização completa ou parcial.

Exemplo de atualização parcial usando `PATCH`:

```
@app.route(
    "/api/usuarios/<int:usuario_id>",
    methods=["PATCH"],
)
def atualizar_usuario(usuario_id):
    dados = request.get_json()

    if not isinstance(dados, dict):
        return jsonify({
            "erro": "O corpo deve ser um objeto JSON."
        }), 400

    usuario = next(
        (
            item
            for item in usuarios
            if item["id"] == usuario_id
        ),
        None,
    )

    if usuario is None:
        return jsonify({
            "erro": "Usuário não encontrado."
        }), 404

    if "nome" in dados:
        nome = dados["nome"]

        if not isinstance(nome, str) or not nome.strip():
            return jsonify({
                "erro": "Nome inválido."
            }), 400

        usuario["nome"] = nome.strip()

    return jsonify(usuario), 200
```

O exemplo altera apenas os campos fornecidos e reconhecidos. Neste caso, somente `nome` é aceito.

Em uma API real, também é importante definir o que deve acontecer quando nenhum campo permitido é enviado, quando aparecem campos desconhecidos ou quando a atualização não altera nenhum valor.

Para atualizações completas com `PUT`, defina quais campos precisam ser fornecidos e como a substituição deve funcionar. Não trate automaticamente `PUT` e `PATCH` como operações idênticas.

### 11.13. Excluindo um recurso

Uma rota `DELETE` pode remover um recurso existente:

```
@app.route(
    "/api/usuarios/<int:usuario_id>",
    methods=["DELETE"],
)
def excluir_usuario(usuario_id):
    for indice, usuario in enumerate(usuarios):
        if usuario["id"] == usuario_id:
            usuarios.pop(indice)
            return "", 204

    return jsonify({
        "erro": "Usuário não encontrado."
    }), 404
```

O código percorre a lista até encontrar o identificador correspondente.

Quando encontra o usuário, remove o item e retorna `204 No Content`, sem corpo de resposta. Caso não encontre, retorna `404`.

Esse exemplo é apenas didático. Em sistemas reais, a exclusão precisa considerar autorização, regras de negócio, integridade dos relacionamentos e persistência no banco de dados.

### 11.14. Organização de endpoints com Blueprints

À medida que a API cresce, colocar todas as rotas no mesmo arquivo torna o código difícil de manter.

Os Blueprints, estudados anteriormente, permitem agrupar rotas relacionadas.

Exemplo de estrutura:

```
app/
├── __init__.py
└── routes/
    ├── __init__.py
    └── usuarios.py
```

No arquivo `app/routes/usuarios.py`:

```
from flask import Blueprint, jsonify

usuarios_bp = Blueprint(
    "usuarios",
    __name__,
    url_prefix="/api/usuarios",
)

@usuarios_bp.route("/", methods=["GET"])
def listar():
    return jsonify({"usuarios": []})
```

Na fábrica da aplicação:

```
from flask import Flask
from app.routes.usuarios import usuarios_bp

def create_app():
    app = Flask(__name__)

    app.register_blueprint(usuarios_bp)

    return app
```

O `url_prefix` adiciona um prefixo às rotas registradas no Blueprint.

Neste exemplo, a rota `"/"` do Blueprint corresponde ao caminho `/api/usuarios/`. Se a aplicação precisar aceitar também a forma sem a barra final, defina e teste esse comportamento explicitamente.

Os Blueprints ajudam a separar responsabilidades, mas não implementam validação, autenticação ou autorização automaticamente.

### 11.15. Autenticação e autorização em APIs

Uma API pode exigir autenticação antes de permitir determinadas operações.

Dependendo do tipo de cliente e da arquitetura, a autenticação pode utilizar cookies de sessão, tokens ou outros mecanismos.

Independentemente do mecanismo escolhido, é necessário distinguir:

- **Autenticação:** verificar a identidade do cliente.
    
- **Autorização:** verificar se essa identidade pode executar a operação solicitada.
    

Por exemplo, autenticar um usuário não significa que ele pode excluir qualquer outro usuário.

Também é importante proteger endpoints de leitura quando os dados são privados, e não apenas endpoints que modificam informações.

Evite criar mecanismos próprios de tokens ou criptografia sem necessidade. Escolha uma abordagem adequada ao projeto e utilize bibliotecas mantidas e documentadas.

### 11.16. Tratamento de erros em APIs

Páginas HTML personalizadas são úteis para interfaces web tradicionais, mas uma API geralmente precisa retornar erros estruturados.

Exemplo:

```
return jsonify({
    "erro": "Recurso não encontrado.",
    "codigo": "USUARIO_NAO_ENCONTRADO",
}), 404
```

Uma resposta estruturada permite que o cliente identifique a categoria do erro sem precisar interpretar uma página HTML.

É útil manter um formato consistente entre os endpoints, incluindo mensagens claras e códigos de status adequados.

Não inclua tracebacks, senhas, tokens ou detalhes internos do servidor no JSON de erro.

Para aplicações maiores, os manipuladores de erro podem ser centralizados, desde que preservem o código HTTP e o formato de resposta apropriados.

### 11.17. Testando uma API com o cliente de testes do Flask

O Flask fornece um cliente de testes que permite executar requisições sem iniciar um servidor HTTP real.

```
def test_status_api(app):
    client = app.test_client()

    resposta = client.get("/api/status")

    assert resposta.status_code == 200
    assert resposta.json["status"] == "online"
```

O exemplo pressupõe que `app` seja uma aplicação Flask criada para o teste e que a rota `/api/status` esteja registrada.

- `test_client()` cria um cliente para simular requisições.
    
- `client.get()` envia uma requisição de teste.
    
- `status_code` verifica o código HTTP retornado.
    
- `resposta.json` permite acessar os dados JSON quando a resposta contém JSON válido.
    

Esse teste pode ser executado por uma ferramenta como pytest, conforme será aprofundado no módulo de testes.

Testar uma API envolve mais do que verificar o caminho de sucesso. Também é necessário testar dados inválidos, recursos inexistentes, falhas de autenticação, falta de permissão e outros cenários relevantes.

### 11.18. Limitações do armazenamento em memória

Os exemplos anteriores utilizam uma lista Python para manter os usuários.

Essa abordagem possui limitações importantes:

- Os dados podem desaparecer ao reiniciar a aplicação.
    
- Cada processo pode ter uma cópia diferente dos dados.
    
- Não existe persistência confiável.
    
- Não há controle adequado de concorrência para operações complexas.
    
- Não existem garantias de integridade próprias de um banco de dados.
    

Para aplicações reais, utilize uma camada de persistência apropriada, como a que foi estudada com Flask-SQLAlchemy.

Além disso, evite armazenar informações privadas em respostas JSON sem necessidade e aplique as regras de autorização antes de devolver os dados.

### 11.19. Boas práticas na construção de APIs

- Utilize métodos HTTP de maneira coerente.
    
- Retorne códigos de status que representem corretamente o resultado.
    
- Valide o corpo, os parâmetros e os tipos dos dados recebidos.
    
- Mantenha um formato de erro consistente.
    
- Separe as rotas em Blueprints conforme a organização do projeto.
    
- Utilize um banco de dados para persistir os dados necessários.
    
- Verifique autenticação e autorização no servidor.
    
- Evite retornar campos internos ou sensíveis dos modelos.
    
- Não exponha exceções internas em respostas JSON.
    
- Teste os casos de sucesso e de falha.
    
- Documente os endpoints, os parâmetros, os códigos de resposta e os requisitos de autenticação.
    

Uma API bem estruturada não é apenas um conjunto de rotas que retornam JSON. Ela também precisa de um contrato previsível, validação rigorosa, tratamento de erros, persistência e controle de acesso.

---
## 12. Testes automatizados com pytest e Flask

Testes automatizados são programas que verificam se determinadas partes de uma aplicação funcionam como esperado.

Em uma aplicação Flask, podemos testar rotas, respostas HTTP, validações, autenticação, permissões e operações relacionadas ao banco de dados sem precisar executar manualmente cada cenário pelo navegador.

O Flask possui um cliente de testes integrado. O `pytest`, por sua vez, é uma ferramenta externa que facilita a escrita, a organização e a execução dos testes.

Neste módulo, vamos entender como essas ferramentas trabalham juntas.

### 12.1. Por que testar uma aplicação?

Imagine uma rota responsável por cadastrar usuários. Ela pode funcionar corretamente quando recebe dados válidos, mas apresentar problemas quando:

- O nome não é informado.
    
- O e-mail já está cadastrado.
    
- O usuário não está autenticado.
    
- O banco de dados apresenta uma falha.
    
- A aplicação retorna um código HTTP incorreto.
    

Testar apenas o caminho de sucesso não é suficiente.

Um conjunto de testes pode verificar tanto o comportamento esperado quanto a maneira como a aplicação reage a situações inválidas.

Os testes também ajudam a evitar regressões: problemas que reaparecem depois de uma alteração no código.

### 12.2. O que é pytest?

O `pytest` é uma ferramenta de testes para Python. Ele identifica funções de teste, executa essas funções e informa quais verificações passaram ou falharam.

Instalação:

```
python3 -m pip install pytest
```

Em projetos organizados, é recomendável instalar ferramentas de desenvolvimento em um ambiente virtual e registrar as dependências utilizadas.

Exemplo de um teste simples:

```
def somar(a, b):
    return a + b


def test_somar():
    assert somar(2, 3) == 5
```

O `assert` verifica uma condição.

Se `somar(2, 3)` retornar `5`, o teste passa. Se retornar outro valor, o pytest informa a falha.

Por convenção, os arquivos de teste frequentemente utilizam nomes como `test_calculos.py`, e as funções de teste começam com `test_`.

### 12.3. Instalando as dependências de teste

Em um projeto Flask, podemos instalar o Flask e o pytest no ambiente virtual:

```
python3 -m venv .venv
source .venv/bin/activate
python -m pip install Flask pytest
```

Os comandos criam um ambiente virtual, ativam esse ambiente e instalam as dependências.

Se o projeto já possui um ambiente virtual e um arquivo de dependências, utilize a configuração existente em vez de criar outro ambiente desnecessariamente.

Para executar os testes:

```
python -m pytest
```

O pytest procura automaticamente arquivos e funções que correspondem às convenções de descoberta de testes.

Para executar um arquivo específico:

```
python -m pytest tests/test_app.py
```

Para mostrar mais detalhes sobre cada teste:

```
python -m pytest -v
```

### 12.4. O cliente de testes do Flask

O Flask disponibiliza `app.test_client()` para simular requisições HTTP à aplicação.

Esse cliente permite testar rotas sem iniciar um servidor web real nem realizar uma conexão de rede externa.

Considere a aplicação:

```
from flask import Flask, jsonify

app = Flask(__name__)


@app.route("/api/status")
def status():
    return jsonify({"status": "online"})
```

Um teste correspondente:

```
def test_status():
    client = app.test_client()

    resposta = client.get("/api/status")

    assert resposta.status_code == 200
    assert resposta.json == {"status": "online"}
```

Analisando:

1. `app.test_client()` cria o cliente de testes.
    
2. `client.get()` simula uma requisição `GET`.
    
3. `resposta.status_code` contém o código HTTP retornado.
    
4. `resposta.json` permite acessar o JSON da resposta, quando válido.
    
5. Os `assert` verificam se a resposta corresponde ao esperado.
    

O teste não precisa abrir um navegador. O Flask processa a requisição dentro do ambiente de teste.

### 12.5. Separando a aplicação dos testes

Em projetos maiores, é melhor evitar misturar a definição da aplicação com a execução dos testes.

Uma estrutura simples pode ser:

```
meu_projeto/
├── app.py
└── tests/
    └── test_app.py
```

O arquivo `app.py`:

```
from flask import Flask, jsonify


def create_app():
    app = Flask(__name__)

    @app.route("/api/status")
    def status():
        return jsonify({"status": "online"})

    return app
```

A função `create_app()` é uma Application Factory: ela cria e retorna uma instância configurada da aplicação.

O arquivo `tests/test_app.py`:

```
from app import create_app


def test_status():
    app = create_app()
    client = app.test_client()

    resposta = client.get("/api/status")

    assert resposta.status_code == 200
    assert resposta.json["status"] == "online"
```

Separar a criação da aplicação da execução do servidor facilita a criação de instâncias específicas para desenvolvimento e testes.

### 12.6. Utilizando fixtures do pytest

Uma fixture é um recurso que prepara algo necessário para um teste, como uma aplicação configurada, um cliente HTTP ou uma conexão de teste.

O pytest disponibiliza o decorador `@pytest.fixture` para definir fixtures.

```
import pytest

from app import create_app


@pytest.fixture
def app():
    app = create_app()
    app.config.update(TESTING=True)

    return app


@pytest.fixture
def client(app):
    return app.test_client()
```

Nesse exemplo:

- A fixture `app` cria uma aplicação para os testes.
    
- `TESTING=True` indica que a aplicação está sendo utilizada no ambiente de testes.
    
- A fixture `client` recebe a aplicação preparada e cria um cliente de testes.
    

O pytest identifica os argumentos de uma função de teste que correspondem a fixtures e fornece os valores automaticamente.

Podemos utilizar o cliente assim:

```
def test_status(client):
    resposta = client.get("/api/status")

    assert resposta.status_code == 200
    assert resposta.json["status"] == "online"
```

Não é necessário chamar manualmente a fixture `client`. O pytest cuida de sua execução e injeta o resultado na função de teste.

### 12.7. Testando diferentes códigos HTTP

Um teste pode verificar tanto a resposta de sucesso quanto as respostas de erro.

Considere uma rota:

```
from flask import Flask, abort, jsonify

app = Flask(__name__)


@app.route("/api/usuarios/<int:usuario_id>")
def obter_usuario(usuario_id):
    if usuario_id != 1:
        abort(404)

    return jsonify({
        "id": 1,
        "nome": "Ana",
    })
```

Testes:

```
def test_usuario_existente(client):
    resposta = client.get("/api/usuarios/1")

    assert resposta.status_code == 200
    assert resposta.json["nome"] == "Ana"


def test_usuario_inexistente(client):
    resposta = client.get("/api/usuarios/99")

    assert resposta.status_code == 404
```

O primeiro teste verifica o comportamento de sucesso. O segundo verifica o comportamento de erro.

A condição que define o usuário existente é apenas ilustrativa. Em uma aplicação real, essa verificação normalmente seria realizada consultando o banco de dados.

### 12.8. Testando o envio de JSON

O cliente de testes permite enviar JSON sem precisar montar manualmente o corpo da requisição e seus cabeçalhos.

Considere uma rota que valida um campo `nome`:

```
from flask import request, jsonify


@app.route("/api/usuarios", methods=["POST"])
def criar_usuario():
    dados = request.get_json()

    if not isinstance(dados, dict):
        return jsonify({"erro": "JSON inválido"}), 400

    nome = dados.get("nome")

    if not isinstance(nome, str) or not nome.strip():
        return jsonify({"erro": "Nome obrigatório"}), 400

    return jsonify({"nome": nome.strip()}), 201
```

Teste de sucesso:

```
def test_criar_usuario(client):
    resposta = client.post(
        "/api/usuarios",
        json={"nome": "Mariana"},
    )

    assert resposta.status_code == 201
    assert resposta.json["nome"] == "Mariana"
```

Teste de entrada inválida:

```
def test_criar_usuario_sem_nome(client):
    resposta = client.post(
        "/api/usuarios",
        json={},
    )

    assert resposta.status_code == 400
    assert resposta.json["erro"] == "Nome obrigatório"
```

O argumento `json` faz com que o cliente envie os dados no formato JSON apropriado.

Observe que a rota de exemplo não persiste o usuário. Os testes verificam o comportamento da validação e da resposta, não uma operação real no banco de dados.

### 12.9. Testando os métodos HTTP

É importante verificar se cada rota aceita os métodos previstos.

Por exemplo, se uma rota foi projetada para receber apenas `GET`, uma requisição `POST` normalmente deve resultar em `405 Method Not Allowed`.

```
def test_metodo_nao_permitido(client):
    resposta = client.post("/api/status")

    assert resposta.status_code == 405
```

Esse teste pressupõe que `/api/status` aceite `GET`, mas não `POST`, e que nenhum outro manipulador altere esse comportamento.

Testar métodos incorretos ajuda a detectar rotas configuradas de forma diferente da planejada.

### 12.10. Testando formulários HTML

O cliente de testes também pode enviar dados de formulário.

Considere uma rota:

```
from flask import request, redirect, url_for, render_template_string


@app.route("/contato", methods=["GET", "POST"])
def contato():
    if request.method == "POST":
        nome = request.form.get("nome", "").strip()

        if not nome:
            return "Nome obrigatório", 400

        return redirect(url_for("contato"))

    return render_template_string(
        '<form method="post">'
        '<input name="nome">'
        '<button type="submit">Enviar</button>'
        '</form>'
    )
```

Teste de envio inválido:

```
def test_contato_sem_nome(client):
    resposta = client.post(
        "/contato",
        data={"nome": ""},
    )

    assert resposta.status_code == 400
```

O argumento `data` envia os valores como dados de formulário.

Em aplicações que utilizam Flask-WTF e proteção CSRF, os testes também precisam considerar o token CSRF. Não é recomendável desativar essa proteção em produção para facilitar os testes.

### 12.11. Testando redirecionamentos

Uma rota pode redirecionar o cliente para outra página.

Por exemplo:

```
@app.route("/inicio")
def inicio():
    return "Página inicial"


@app.route("/ir-inicio")
def ir_inicio():
    return redirect(url_for("inicio"))
```

Podemos testar o redirecionamento:

```
def test_redirecionamento(client):
    resposta = client.get(
        "/ir-inicio",
        follow_redirects=False,
    )

    assert resposta.status_code == 302
    assert resposta.headers["Location"].endswith("/inicio")
```

O parâmetro `follow_redirects=False` impede que o cliente siga automaticamente o redirecionamento.

Assim, o teste consegue verificar a resposta original e o cabeçalho `Location`.

Se o objetivo for testar o conteúdo da página final, é possível usar `follow_redirects=True` e verificar a resposta após o redirecionamento.

### 12.12. Testando autenticação e autorização

Em uma aplicação que utiliza Flask-Login, alguns endpoints devem estar acessíveis apenas para usuários autenticados.

Os testes devem verificar os dois cenários:

- Usuário não autenticado tentando acessar uma rota protegida.
    
- Usuário autenticado tentando acessar uma rota para a qual não possui permissão.
    

O comportamento esperado depende da configuração da aplicação. Uma rota protegida por `login_required` pode redirecionar para a página de login, enquanto uma API pode retornar `401`.

Além disso, um usuário autenticado ainda pode receber `403` ou `404` ao tentar acessar um recurso sem autorização.

Não basta testar se o login funciona. Também é necessário testar se o sistema impede que usuários acessem dados de outras pessoas.

### 12.13. Testando operações com banco de dados

Quando uma aplicação utiliza Flask-SQLAlchemy, os testes precisam de um banco de dados isolado para evitar alterar informações reais.

Uma estratégia comum é utilizar um banco SQLite separado para testes, quando ele for compatível com as funcionalidades utilizadas pelo projeto.

Exemplo conceitual de configuração:

```
app.config.update(
    TESTING=True,
    SQLALCHEMY_DATABASE_URI="sqlite://",
)
```

A configuração precisa ser aplicada antes da inicialização da extensão do banco de dados, se ela depender da URI durante a inicialização.

O endereço `sqlite://` pode criar um banco SQLite em memória, dependendo da configuração do SQLAlchemy. O ciclo de vida da conexão e a forma como o banco é compartilhado precisam ser considerados para que as tabelas permaneçam disponíveis durante cada teste.

Um fluxo de teste com banco de dados normalmente inclui:

1. Criar a aplicação de teste.
    
2. Configurar o banco isolado.
    
3. Inicializar a extensão.
    
4. Criar as tabelas necessárias.
    
5. Executar o teste.
    
6. Remover os dados ou descartar o banco ao final.
    

Não utilize o banco de produção em testes automatizados. Um teste de exclusão, atualização ou cadastro pode modificar dados reais.

### 12.14. Testes parametrizados

Às vezes, precisamos verificar o mesmo comportamento com diferentes entradas. Em vez de repetir a função de teste, podemos utilizar `pytest.mark.parametrize`.

```
import pytest


@pytest.mark.parametrize(
    ("entrada", "esperado"),
    [
        (2, 4),
        (3, 6),
        (5, 10),
    ],
)
def test_dobro(entrada, esperado):
    assert entrada * 2 == esperado
```

O pytest executa a função para cada par de valores informado.

Nesse exemplo, o mesmo teste verifica três entradas diferentes.

A parametrização é útil para testar validações, conversões, códigos de status e várias combinações de dados sem duplicar a lógica do teste.

### 12.15. Interpretando os resultados do pytest

Ao executar:

```
python -m pytest -v
```

o pytest apresenta os testes encontrados e seus resultados.

Os símbolos e as mensagens podem variar conforme as opções de execução, mas os resultados mais comuns incluem:

- `PASSED`: o teste passou.
    
- `FAILED`: o teste foi executado, mas uma verificação falhou.
    
- `ERROR`: ocorreu um erro durante a preparação ou execução do teste, por exemplo em uma fixture.
    

Quando um teste falha, examine:

1. O nome do teste.
    
2. A linha apontada pelo pytest.
    
3. O valor esperado.
    
4. O valor realmente recebido.
    
5. O traceback e a mensagem de erro.
    

Uma falha de teste não significa necessariamente que a implementação está errada. O próprio teste pode estar verificando um comportamento incorreto. É necessário comparar o resultado com o requisito esperado.

### 12.16. Organização dos testes no projeto

À medida que o projeto cresce, separe os testes por área funcional.

Uma estrutura possível:

```
meu_projeto/
├── app/
│   ├── __init__.py
│   ├── routes/
│   ├── models/
│   └── templates/
├── tests/
│   ├── conftest.py
│   ├── test_rotas.py
│   ├── test_usuarios.py
│   ├── test_autenticacao.py
│   └── test_api.py
└── pytest.ini
```

O arquivo `conftest.py` pode conter fixtures compartilhadas pelos testes daquele diretório e seus subdiretórios.

O arquivo `pytest.ini` permite centralizar opções do pytest. Ele não é obrigatório em todos os projetos.

Evite criar muitos arquivos sem necessidade. A estrutura deve acompanhar a complexidade real da aplicação.

### 12.17. O que os testes não garantem

Uma aplicação com testes passando não é necessariamente segura, correta ou livre de defeitos.

Os testes verificam os comportamentos que foram definidos e cobertos. Eles podem não detectar problemas que não foram considerados.

Além dos testes automatizados, podem ser necessárias revisões de código, testes de integração, verificações de segurança, monitoramento e testes realizados em ambientes próximos da produção.

Também é importante manter os testes independentes: um teste não deve depender da ordem em que outro teste foi executado nem de dados deixados por uma execução anterior.

### 12.18. Boas práticas para testes Flask

- Teste caminhos de sucesso e cenários de erro.
    
- Verifique códigos HTTP e conteúdo das respostas.
    
- Utilize fixtures para compartilhar a preparação da aplicação.
    
- Mantenha os dados de teste isolados dos dados reais.
    
- Teste autenticação e autorização separadamente.
    
- Verifique a validação de formulários e JSON.
    
- Utilize parametrização para entradas variadas.
    
- Evite testes que dependam de serviços externos sem necessidade.
    
- Não ignore falhas apenas para obter uma execução verde.
    
- Execute a suíte de testes depois de mudanças importantes no código.
    

Os testes automatizados ajudam a transformar requisitos da aplicação em verificações repetíveis, facilitando a manutenção e reduzindo o risco de regressões.

---
## 13. Produção e deploy de aplicações Flask

Desenvolver uma aplicação Flask localmente é diferente de disponibilizá-la para outras pessoas pela internet.

No ambiente de desenvolvimento, normalmente executamos o código diretamente, fazemos alterações frequentes e utilizamos ferramentas de depuração. Em produção, precisamos considerar disponibilidade, segurança, gerenciamento de processos, configuração, logs e capacidade de atender às requisições.

O processo de disponibilizar uma aplicação em um ambiente onde ela possa ser acessada é chamado de _deploy_ ou implantação.

### 13.1. Desenvolvimento versus produção

Durante o desenvolvimento, podemos executar a aplicação com o servidor integrado do Flask:

```
flask --app app run --debug
```

Esse comando pressupõe que o módulo `app` disponibilize uma aplicação Flask ou uma fábrica de aplicação reconhecida pelo Flask.

A opção `--debug` ativa recursos de depuração, incluindo recarga automática do código em condições apropriadas.

Esse servidor é destinado ao desenvolvimento. **Não utilize o servidor de desenvolvimento do Flask como servidor de produção.**

Em produção, utilize um servidor WSGI adequado e configure a infraestrutura de acordo com os requisitos da aplicação.

### 13.2. O que é WSGI?

WSGI significa _Web Server Gateway Interface_. É uma especificação que define uma interface entre servidores web compatíveis e aplicações Python.

O Flask é uma aplicação WSGI. Isso permite que ele seja executado por servidores que implementam essa interface.

O fluxo simplificado é:

```
Cliente HTTP
     |
     v
Servidor web / proxy reverso
     |
     v
Servidor WSGI
     |
     v
Aplicação Flask
     |
     v
Banco de dados e outros serviços
```

Cada componente pode ter uma responsabilidade diferente:

- **Cliente HTTP:** navegador, aplicativo móvel ou outro programa.
    
- **Servidor web ou proxy reverso:** pode receber conexões externas, terminar TLS e encaminhar requisições.
    
- **Servidor WSGI:** executa a aplicação Python e coordena o processamento das requisições.
    
- **Flask:** executa as rotas, os manipuladores de erro e a lógica da aplicação.
    
- **Serviços externos:** banco de dados, armazenamento de arquivos e outras dependências.
    

Essa arquitetura é um modelo comum, não uma exigência de que todos os projetos tenham exatamente os mesmos componentes.

### 13.3. O que é Gunicorn?

Gunicorn é um servidor WSGI utilizado para executar aplicações Python em ambientes Unix, incluindo diversas distribuições Linux.

Instalação:

```
python -m pip install gunicorn
```

Considere uma estrutura simples:

```
meu_projeto/
├── app.py
└── .venv/
```

Se `app.py` contém:

```
from flask import Flask

app = Flask(__name__)


@app.route("/")
def inicio():
    return "Aplicação Flask em execução"
```

Podemos executar a aplicação com:

```
gunicorn --bind 127.0.0.1:8000 app:app
```

A expressão `app:app` possui duas partes:

- O primeiro `app` é o módulo Python, correspondente ao arquivo `app.py`.
    
- O segundo `app` é o objeto Flask definido dentro desse módulo.
    

O argumento `--bind 127.0.0.1:8000` faz o servidor escutar na interface de loopback, na porta `8000`.

Essa configuração é útil quando o Gunicorn será acessado por um proxy reverso na mesma máquina. Para acessá-lo diretamente a partir de outro computador, seria necessário considerar a interface de escuta, o firewall e as medidas de segurança apropriadas.

O Gunicorn é uma opção comum em Linux, mas não é a única alternativa de servidor WSGI.

### 13.4. Executando uma Application Factory

No módulo de estrutura do Flask, estudamos o padrão Application Factory, no qual uma função cria e retorna a aplicação.

Exemplo de `app.py`:

```
from flask import Flask


def create_app():
    app = Flask(__name__)

    @app.route("/")
    def inicio():
        return "Aplicação em produção"

    return app
```

Nesse caso, não existe necessariamente uma variável global chamada `app` contendo a instância Flask. Existe a função `create_app()`.

O Gunicorn pode utilizar a fábrica:

```
gunicorn --bind 127.0.0.1:8000 'app:create_app()'
```

A expressão `app:create_app()` informa que o Gunicorn deve carregar o módulo `app` e chamar a fábrica para obter a aplicação WSGI.

Se a fábrica exigir argumentos, eles precisam ser fornecidos de acordo com a forma como ela foi implementada e com a sintaxe aceita pelo servidor.

### 13.5. Utilizando workers

Um servidor WSGI pode utilizar múltiplos workers para processar requisições.

No Gunicorn, um worker é um processo ou unidade de execução responsável por atender requisições, conforme a classe de worker utilizada.

Exemplo:

```
gunicorn --workers 2 --bind 127.0.0.1:8000 app:app
```

A opção `--workers 2` solicita dois workers.

O número ideal depende de fatores como:

- Memória disponível.
    
- Quantidade de requisições simultâneas.
    
- Tempo de processamento.
    
- Tipo de operação realizada.
    
- Dependências externas e conexões com o banco de dados.
    

Mais workers não significam automaticamente melhor desempenho. Cada processo pode consumir memória adicional e manter suas próprias conexões e estruturas de dados.

Em uma máquina com recursos limitados, comece com uma configuração modesta e avalie o consumo e o comportamento real antes de aumentar a quantidade de workers.

### 13.6. Configuração por variáveis de ambiente

Aplicações em produção frequentemente precisam receber configurações específicas do ambiente, como:

- Chave secreta.
    
- URI do banco de dados.
    
- Credenciais de serviços externos.
    
- Endereço de armazenamento.
    
- Opções de integração.
    

Esses valores não devem ficar fixos no código versionado.

No Flask, podemos ler variáveis de ambiente com `os.environ` ou `os.getenv()`.

```
import os

from flask import Flask


def create_app():
    app = Flask(__name__)

    app.config["SECRET_KEY"] = os.environ["SECRET_KEY"]
    app.config["DEBUG"] = False

    return app
```

A expressão `os.environ["SECRET_KEY"]` acessa uma variável de ambiente obrigatória. Se ela não existir, Python gera `KeyError`, fazendo a inicialização falhar.

Essa falha antecipada pode ser útil para impedir que uma aplicação seja iniciada com uma configuração obrigatória ausente.

Também é possível usar `os.getenv()`, que retorna `None` quando a variável não existe, a menos que seja fornecido um valor padrão. Nesse caso, o desenvolvedor precisa validar explicitamente o resultado.

Não coloque chaves reais diretamente no código-fonte ou em arquivos versionados.

### 13.7. Definindo variáveis no Linux

Para definir uma variável no terminal atual:

```
export SECRET_KEY="substitua-por-uma-chave-segura"
```

Para definir a URI de um banco de dados:

```
export DATABASE_URL="sqlite:///app.db"
```

Esses exemplos mostram como o processo pode receber configurações. A URI do banco deve corresponder ao banco de dados e à extensão utilizados pela aplicação.

Em seguida, o programa pode ser iniciado a partir desse terminal.

Entretanto, variáveis definidas com `export` dessa maneira geralmente existem apenas para aquele processo e seus processos filhos. Elas não são automaticamente persistidas para futuras sessões nem configuradas em serviços gerenciados pelo sistema.

Em produção, utilize o mecanismo de configuração de ambiente fornecido pelo sistema de implantação, pelo gerenciador de serviços ou pela plataforma de hospedagem.

### 13.8. Configurando um banco de dados para produção

Aplicações que utilizam Flask-SQLAlchemy precisam receber a URI correta antes da inicialização da extensão.

Exemplo:

```
import os

from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()


def create_app():
    app = Flask(__name__)

    app.config["SQLALCHEMY_DATABASE_URI"] = os.environ[
        "DATABASE_URL"
    ]

    db.init_app(app)

    return app
```

Nesse exemplo:

1. A aplicação Flask é criada.
    
2. A URI do banco de dados é carregada.
    
3. A extensão SQLAlchemy é inicializada com a aplicação.
    
4. A fábrica retorna a aplicação configurada.
    

O código pressupõe que o driver necessário esteja instalado e que a URI corresponda ao banco utilizado.

O valor `sqlite:///app.db` pode ser útil para um projeto simples, mas não é automaticamente a melhor opção para uma aplicação que precisa de acesso concorrente, persistência gerenciada, backups e operação em múltiplos servidores.

A escolha do banco depende das necessidades do projeto.

**Importante:** criar tabelas automaticamente durante cada inicialização não substitui um processo de migração. Em aplicações com dados importantes, mudanças de estrutura devem ser planejadas e aplicadas de forma controlada, utilizando ferramentas como Flask-Migrate e Alembic.

### 13.9. Proxy reverso

Um proxy reverso recebe requisições dos clientes e as encaminha para um ou mais servidores internos.

Nginx é um exemplo de software frequentemente utilizado nessa função.

Uma arquitetura comum é:

```
Navegador
    |
    | HTTPS
    v
Nginx
    |
    | HTTP interno
    v
Gunicorn
    |
    v
Flask
```

O Nginx pode receber conexões públicas, lidar com TLS e encaminhar requisições para o Gunicorn, que escuta em uma interface local.

Uma configuração conceitual de Nginx pode conter:

```
server {
    listen 80;
    server_name exemplo.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Esse exemplo encaminha as requisições recebidas na porta `80` para um serviço local na porta `8000`.

Ele não configura HTTPS. Para uma implantação pública, é necessário configurar TLS e redirecionamentos apropriados, conforme a infraestrutura utilizada.

O proxy reverso também não substitui a autenticação, a autorização ou a validação implementadas na aplicação.

### 13.10. Cabeçalhos encaminhados pelo proxy

Quando uma aplicação Flask está atrás de um proxy reverso, alguns detalhes da requisição original podem ser diferentes daqueles observados pelo processo Python.

Por exemplo, o Flask pode receber a requisição por HTTP internamente, embora o usuário tenha acessado o site por HTTPS.

Proxies podem encaminhar informações por cabeçalhos como `X-Forwarded-Proto` e `X-Forwarded-For`.

Esses cabeçalhos só devem ser confiados quando a aplicação estiver atrás de um proxy conhecido e configurado corretamente.

O Flask pode utilizar o middleware `ProxyFix`, disponibilizado pelo Werkzeug, para interpretar determinados cabeçalhos encaminhados.

Exemplo conceitual:

```
from werkzeug.middleware.proxy_fix import ProxyFix

app.wsgi_app = ProxyFix(
    app.wsgi_app,
    x_for=1,
    x_proto=1,
    x_host=1,
)
```

Os valores numéricos indicam quantos proxies confiáveis devem ser considerados para cada cabeçalho.

Os valores acima não devem ser copiados indiscriminadamente: eles precisam corresponder à topologia real da infraestrutura. Confiar em cabeçalhos fornecidos diretamente por clientes não confiáveis pode permitir falsificação de informações sobre IP, protocolo ou host.

### 13.11. Arquivos estáticos

Arquivos estáticos incluem CSS, JavaScript, imagens e outros recursos que não precisam ser gerados dinamicamente para cada requisição.

O Flask oferece um mecanismo próprio para disponibilizar arquivos estáticos, normalmente a partir do diretório `static`.

Exemplo:

```
meu_projeto/
├── app.py
├── static/
│   ├── css/
│   │   └── estilo.css
│   └── img/
│       └── logo.png
└── templates/
    └── index.html
```

Em templates Jinja:

```
<link
    rel="stylesheet"
    href="{{ url_for('static', filename='css/estilo.css') }}"
>
```

Em produção, arquivos estáticos também podem ser servidos por um proxy reverso, CDN ou serviço de armazenamento, dependendo da arquitetura.

A aplicação precisa garantir que esses arquivos estejam disponíveis no destino do deploy. Arquivos que existem apenas no computador de desenvolvimento não serão automaticamente copiados para outro ambiente.

### 13.12. Logs em produção

Os logs ajudam a identificar falhas, acompanhar o funcionamento e investigar comportamentos inesperados.

Em uma aplicação Flask, podemos registrar eventos usando `app.logger`:

```
@app.route("/processar")
def processar():
    app.logger.info("Iniciando processamento")

    try:
        executar_operacao()
    except Exception:
        app.logger.exception("Falha no processamento")
        return "Não foi possível concluir a operação.", 500

    return "Processamento concluído"
```

`executar_operacao()` representa uma função hipotética.

O exemplo registra a falha com traceback, mas uma aplicação real deve avaliar quais exceções são esperadas e tratar cada tipo de falha de maneira apropriada.

Em produção, também é necessário definir onde os logs serão armazenados, quem pode acessá-los e por quanto tempo serão mantidos.

Não registre senhas, tokens de autenticação, cookies completos ou chaves secretas.

### 13.13. Gerenciamento do processo

Iniciar o Gunicorn em um terminal pode ser suficiente durante um teste local, mas um serviço de produção precisa de uma estratégia para manter o processo sob controle.

Um gerenciador de serviços, como o systemd em distribuições Linux, pode iniciar a aplicação, reiniciá-la quando necessário e gerenciar sua execução.

Um serviço precisa considerar:

- Usuário do sistema responsável pelo processo.
    
- Diretório de trabalho.
    
- Caminho do ambiente virtual.
    
- Variáveis de ambiente.
    
- Política de reinicialização.
    
- Permissões de arquivos.
    
- Destino dos logs.
    

Evite executar a aplicação como `root` sem necessidade. Um usuário de serviço com permissões limitadas reduz o impacto de determinados comprometimentos.

O gerenciador de serviços deve ser configurado de acordo com o sistema operacional e a infraestrutura utilizados.

### 13.14. Saúde e monitoramento

Uma aplicação pode estar em execução sem conseguir atender corretamente às requisições.

Por isso, sistemas de produção frequentemente possuem verificações de saúde (_health checks_).

Uma rota simples pode indicar que o processo Flask está respondendo:

```
from flask import jsonify


@app.route("/health")
def health():
    return jsonify({"status": "ok"}), 200
```

Essa rota verifica apenas se o Flask consegue responder. Ela não garante que o banco de dados, o armazenamento ou todas as integrações estejam funcionando.

Se a infraestrutura precisar verificar dependências, crie verificações específicas e controle quais informações são expostas publicamente.

O monitoramento também pode acompanhar latência, taxa de erros, consumo de recursos e disponibilidade dos serviços externos.

### 13.15. Segurança antes do deploy

Antes de disponibilizar a aplicação, revise as configurações de segurança.

- Mantenha o modo de depuração desativado em produção.
    
- Utilize uma `SECRET_KEY` protegida e estável.
    
- Não publique credenciais no repositório.
    
- Utilize HTTPS para conexões públicas.
    
- Configure os cookies de sessão adequadamente.
    
- Mantenha autenticação e autorização nas rotas protegidas.
    
- Utilize consultas parametrizadas ou recursos seguros da ORM.
    
- Evite mensagens de erro que revelem detalhes internos.
    
- Mantenha dependências atualizadas.
    
- Restrinja permissões do usuário que executa a aplicação.
    
- Faça backups e teste os procedimentos de recuperação.
    
- Utilize configurações e dados separados para desenvolvimento, testes e produção.
    

Essas medidas precisam ser adaptadas ao sistema e aos riscos específicos da aplicação.

### 13.16. Problemas comuns durante a implantação

**O Gunicorn não consegue importar a aplicação**

Verifique o módulo e o objeto informados no comando, o diretório de trabalho, as dependências instaladas e a ativação do ambiente correto.

Por exemplo, `app:app` exige um módulo importável chamado `app` com um objeto Flask chamado `app`.

**A aplicação não encontra uma variável de ambiente**

Verifique se a variável está configurada no processo que inicia o serviço. Uma variável exportada em um terminal não é necessariamente herdada por um serviço iniciado pelo systemd ou por outra ferramenta.

**O proxy retorna** `**502 Bad Gateway**`

Verifique se o Gunicorn está em execução, se está escutando no endereço configurado e se o Nginx consegue se conectar a ele.

**A aplicação funciona localmente, mas falha no servidor**

Compare as versões do Python, as dependências instaladas, as variáveis de ambiente, as permissões e as configurações do banco de dados.

**Os usuários perdem sessões após reinicializações**

Verifique se a `SECRET_KEY` é estável e está configurada de forma consistente entre as instâncias que atendem a aplicação.

**Os arquivos enviados pelo usuário desaparecem**

Verifique onde os arquivos são armazenados e se esse local é persistente. Alguns ambientes de implantação utilizam sistemas de arquivos temporários ou recriam os diretórios a cada atualização.

**As alterações no banco de dados não são aplicadas**

Verifique se as migrações necessárias foram criadas e executadas no ambiente correto. A simples publicação de uma nova versão do código não garante que a estrutura do banco tenha sido atualizada.

### 13.17. Checklist de implantação

Antes de considerar o deploy concluído, confirme:

- A aplicação inicia corretamente no ambiente de destino.
    
- O servidor WSGI está configurado.
    
- O modo de depuração está desativado.
    
- As variáveis de ambiente obrigatórias estão definidas.
    
- O banco de dados está acessível.
    
- As migrações necessárias foram aplicadas.
    
- O proxy reverso está encaminhando as requisições corretamente, quando utilizado.
    
- HTTPS está configurado para acesso público.
    
- Os arquivos estáticos estão disponíveis.
    
- Os logs podem ser consultados sem expor dados sensíveis.
    
- O processo pode ser monitorado e reiniciado.
    
- Os backups e procedimentos de recuperação foram planejados.
    
- Os testes automatizados foram executados.
    
- As permissões do processo e dos arquivos foram revisadas.
    

Uma implantação confiável envolve mais do que fazer o servidor iniciar. Ela exige que a aplicação seja configurada, monitorada, protegida e mantida de maneira consistente.

---
# Módulo 14 — Projeto Prático Integrado com Flask

## 1. Objetivo do projeto

Neste módulo, vamos integrar os principais conceitos estudados durante o curso em uma aplicação web funcional: um **gerenciador de tarefas com cadastro, autenticação, banco de dados, formulários, API e testes automatizados**.

O objetivo não é apenas fazer a aplicação funcionar, mas entender como as diferentes partes de um projeto Flask se comunicam.

### Funcionalidades

- Cadastro de usuários.
    
- Login e logout.
    
- Páginas protegidas por autenticação.
    
- Criação, listagem, edição e exclusão de tarefas.
    
- Armazenamento de dados com Flask-SQLAlchemy.
    
- Formulários protegidos contra CSRF.
    
- API que retorna tarefas em JSON.
    
- Testes automatizados com pytest.
    
- Configuração adequada para desenvolvimento e produção.
    

## 2. Arquitetura da aplicação

Vamos utilizar o padrão Application Factory, Blueprints e extensões inicializadas separadamente.

Uma estrutura possível é:

```
gerenciador/
├── app/
│   ├── __init__.py
│   ├── extensions.py
│   ├── models.py
│   ├── auth/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   └── forms.py
│   ├── tasks/
│   │   ├── __init__.py
│   │   └── routes.py
│   ├── api/
│   │   ├── __init__.py
│   │   └── routes.py
│   └── templates/
│       ├── base.html
│       ├── auth/
│       │   ├── login.html
│       │   └── register.html
│       └── tasks/
│           └── index.html
├── tests/
│   ├── conftest.py
│   ├── test_auth.py
│   └── test_tasks.py
├── config.py
├── requirements.txt
└── run.py
```

Essa é uma estrutura de referência. Se o gerador `startproject` que você desenvolveu utiliza nomes ou diretórios diferentes, adapte os arquivos à estrutura dele em vez de criar uma segunda estrutura desnecessária.

### Responsabilidade de cada arquivo

|Arquivo|Responsabilidade|
|---|---|
|`__init__.py`|Cria e configura a aplicação Flask.|
|`extensions.py`|Declara extensões, como banco e login.|
|`models.py`|Define os modelos e relacionamentos do banco.|
|`auth/routes.py`|Gerencia cadastro, login e logout.|
|`auth/forms.py`|Define formulários de autenticação.|
|`tasks/routes.py`|Gerencia as tarefas pela interface web.|
|`api/routes.py`|Disponibiliza endpoints JSON.|
|`templates/`|Contém as páginas HTML.|
|`tests/`|Contém os testes automatizados.|
|`config.py`|Centraliza as configurações.|
|`run.py`|Ponto de entrada para executar a aplicação localmente.|

## 3. Dependências

Crie e ative um ambiente virtual antes de instalar as bibliotecas:

```
python3 -m venv .venv
source .venv/bin/activate
```

Instale as dependências principais:

```
pip install Flask Flask-SQLAlchemy Flask-Login Flask-WTF pytest
```

Registre as dependências do ambiente:

```
pip freeze > requirements.txt
```

O arquivo `requirements.txt` permite reproduzir o ambiente de dependências. Para projetos que serão distribuídos ou implantados em produção, é importante controlar as versões utilizadas.

## 4. Inicialização das extensões

No arquivo `app/extensions.py`:

```
from flask_login import LoginManager
from flask_sqlalchemy import SQLAlchemy
from flask_wtf.csrf import CSRFProtect

db = SQLAlchemy()
login_manager = LoginManager()
csrf = CSRFProtect()
```

Aqui, as extensões são declaradas sem estarem vinculadas imediatamente a uma instância específica do Flask.

Isso permite inicializá-las posteriormente dentro da Application Factory.

### Por que fazer dessa maneira?

Se cada módulo criasse sua própria instância de `SQLAlchemy`, por exemplo, o projeto poderia acabar utilizando objetos de banco diferentes. Centralizar as extensões evita essa inconsistência.

Também facilita a criação de aplicações de teste com configurações próprias.

## 5. Configuração da aplicação

No arquivo `config.py`:

```
import os

class Config:
    SECRET_KEY = os.environ.get("SECRET_KEY")
    SQLALCHEMY_DATABASE_URI = os.environ.get(
        "DATABASE_URL",
        "sqlite:///gerenciador.db",
    )
    SQLALCHEMY_TRACK_MODIFICATIONS = False
```

### Entendendo as configurações

- `SECRET_KEY`: chave usada pelo Flask e por componentes como as sessões e a proteção CSRF.
    
- `DATABASE_URL`: endereço do banco de dados.
    
- `sqlite:///gerenciador.db`: utiliza um banco SQLite local quando nenhuma URL é informada.
    
- `SQLALCHEMY_TRACK_MODIFICATIONS`: desativa um recurso de rastreamento de modificações que normalmente não é necessário.
    

A chave secreta não deve ser escrita diretamente no código nem publicada no repositório.

Para desenvolvimento, configure uma chave no ambiente do terminal:

```
export SECRET_KEY='uma-chave-local-de-desenvolvimento'
```

Em produção, utilize uma chave forte, aleatória e armazenada no ambiente de implantação ou em um gerenciador de segredos.

Como a configuração anterior não fornece uma chave padrão, a aplicação deve verificar se `SECRET_KEY` foi configurada antes de iniciar.

## 6. Criando a Application Factory

No arquivo `app/__init__.py`:

```
from flask import Flask

from config import Config
from app.extensions import db, login_manager, csrf


def create_app(test_config=None):
    app = Flask(__name__)
    app.config.from_object(Config)

    if test_config:
        app.config.update(test_config)

    if not app.config.get("SECRET_KEY"):
        raise RuntimeError("Configure a variável SECRET_KEY.")

    db.init_app(app)
    login_manager.init_app(app)
    csrf.init_app(app)

    login_manager.login_view = "auth.login"

    from app.auth.routes import auth_bp
    from app.tasks.routes import tasks_bp
    from app.api.routes import api_bp

    app.register_blueprint(auth_bp, url_prefix="/auth")
    app.register_blueprint(tasks_bp, url_prefix="/tasks")
    app.register_blueprint(api_bp, url_prefix="/api")

    return app
```

### Como o código funciona

1. `Flask(__name__)` cria a aplicação.
    
2. `app.config.from_object(Config)` carrega as configurações.
    
3. `app.config.update(test_config)` permite substituir configurações em testes.
    
4. `db.init_app(app)` inicializa a extensão de banco.
    
5. `login_manager.init_app(app)` configura o gerenciamento de autenticação.
    
6. `csrf.init_app(app)` ativa a proteção CSRF.
    
7. `register_blueprint()` registra as rotas dos módulos.
    
8. `return app` entrega a aplicação pronta.
    

Os imports dos Blueprints são feitos dentro da função para ajudar a evitar ciclos de importação.

**Importante:** `create_app()` não cria automaticamente as tabelas do banco. A criação inicial do esquema deve ser controlada separadamente; em projetos que evoluem, utilize migrações.

## 7. Criando os modelos do banco

No arquivo `app/models.py`:

```
from flask_login import UserMixin
from werkzeug.security import generate_password_hash

from app.extensions import db


class User(UserMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(
        db.String(80),
        unique=True,
        nullable=False,
    )
    password_hash = db.Column(
        db.String(255),
        nullable=False,
    )

    tasks = db.relationship(
        "Task",
        backref="owner",
        lazy=True,
        cascade="all, delete-orphan",
    )

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        from werkzeug.security import check_password_hash

        return check_password_hash(self.password_hash, password)


class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(200), nullable=False)
    completed = db.Column(
        db.Boolean,
        nullable=False,
        default=False,
    )
    user_id = db.Column(
        db.Integer,
        db.ForeignKey("user.id"),
        nullable=False,
    )
```

### Entendendo o relacionamento

Um usuário pode possuir várias tarefas, mas cada tarefa pertence a um único usuário.

```
User
 ├── Task 1
 ├── Task 2
 └── Task 3
```

A chave estrangeira `user_id` identifica o proprietário de cada tarefa.

O relacionamento `tasks` permite acessar as tarefas de um usuário. Já `owner`, criado por `backref`, permite acessar o usuário responsável por uma tarefa.

`UserMixin` fornece propriedades e métodos esperados pelo Flask-Login, como a identificação do usuário e o estado de autenticação.

### Por que não guardar a senha diretamente?

O método `set_password()` armazena um hash de senha, não a senha original. O método `check_password()` verifica se uma senha informada corresponde ao hash armazenado.

Nunca armazene senhas em texto puro.

**Atenção ao** `**cascade**`**:** a configuração apresentada pode excluir as tarefas quando um usuário é excluído. Em aplicações reais, essa política deve ser escolhida conscientemente, considerando retenção de dados, auditoria e regras de negócio.

## 8. Integrando o Flask-Login

Ainda em `app/models.py`, registre a função que recupera um usuário pelo identificador da sessão:

```
from app.extensions import login_manager


@login_manager.user_loader
def load_user(user_id):
    return db.session.get(User, int(user_id))
```

O Flask-Login armazena a identificação do usuário na sessão e utiliza essa função para recuperar o objeto correspondente em requisições posteriores.

O valor recebido é convertido para inteiro porque o identificador definido no modelo é do tipo `Integer`.

Em uma aplicação com identificadores de outro tipo, a conversão precisaria acompanhar o modelo utilizado.

## 9. Criando os formulários

No arquivo `app/auth/forms.py`:

```
from flask_wtf import FlaskForm
from wtforms import PasswordField, StringField, SubmitField
from wtforms.validators import (
    DataRequired,
    Length,
    EqualTo,
)


class RegisterForm(FlaskForm):
    username = StringField(
        "Usuário",
        validators=[
            DataRequired(),
            Length(min=3, max=80),
        ],
    )

    password = PasswordField(
        "Senha",
        validators=[
            DataRequired(),
            Length(min=8),
        ],
    )

    confirm_password = PasswordField(
        "Confirmar senha",
        validators=[
            DataRequired(),
            EqualTo("password", message="As senhas não coincidem."),
        ],
    )

    submit = SubmitField("Cadastrar")


class LoginForm(FlaskForm):
    username = StringField(
        "Usuário",
        validators=[DataRequired()],
    )

    password = PasswordField(
        "Senha",
        validators=[DataRequired()],
    )

    submit = SubmitField("Entrar")
```

### O que os validadores fazem?

- `DataRequired()`: exige um valor.
    
- `Length(min=3, max=80)`: limita o comprimento do nome de usuário.
    
- `Length(min=8)`: exige uma senha com pelo menos oito caracteres.
    
- `EqualTo("password")`: exige que a confirmação seja igual ao campo `password`.
    

Essas validações melhoram a experiência do usuário, mas não substituem todas as regras de segurança. Uma aplicação real também precisa considerar senhas comprometidas, limitação de tentativas de login e outras medidas de proteção.

## 10. Cadastro, login e logout

No arquivo `app/auth/__init__.py`, deixe o pacote pronto para receber os módulos. Em `app/auth/routes.py`:

```
from flask import Blueprint, flash, redirect, render_template, url_for
from flask_login import login_user, logout_user, current_user
from sqlalchemy import select

from app.extensions import db
from app.models import User
from app.auth.forms import LoginForm, RegisterForm

auth_bp = Blueprint("auth", __name__)


@auth_bp.route("/register", methods=["GET", "POST"])
def register():
    if current_user.is_authenticated:
        return redirect(url_for("tasks.index"))

    form = RegisterForm()

    if form.validate_on_submit():
        username = form.username.data.strip()

        existing_user = db.session.scalar(
            select(User).where(User.username == username)
        )

        if existing_user:
            flash("Esse nome de usuário já está em uso.")
            return render_template("auth/register.html", form=form)

        user = User(username=username)
        user.set_password(form.password.data)

        db.session.add(user)
        db.session.commit()

        flash("Cadastro realizado. Faça login.")
        return redirect(url_for("auth.login"))

    return render_template("auth/register.html", form=form)


@auth_bp.route("/login", methods=["GET", "POST"])
def login():
    if current_user.is_authenticated:
        return redirect(url_for("tasks.index"))

    form = LoginForm()

    if form.validate_on_submit():
        username = form.username.data.strip()

        user = db.session.scalar(
            select(User).where(User.username == username)
        )

        if user and user.check_password(form.password.data):
            login_user(user)
            return redirect(url_for("tasks.index"))

        flash("Usuário ou senha inválidos.")

    return render_template("auth/login.html", form=form)


@auth_bp.route("/logout", methods=["POST"])
def logout():
    logout_user()
    return redirect(url_for("auth.login"))
```

### Fluxo do cadastro

1. O usuário acessa `/auth/register`.
    
2. O formulário é exibido.
    
3. O navegador envia os dados por POST.
    
4. `validate_on_submit()` verifica o método e a validade do formulário.
    
5. A aplicação verifica se o nome já existe.
    
6. A senha é transformada em hash.
    
7. O usuário é salvo no banco.
    
8. O usuário é redirecionado para o login.
    

A restrição `unique=True` no modelo também protege a unicidade no banco. Em uma aplicação concorrente, ainda é necessário tratar a possível violação de unicidade no momento do `commit()`.

### Fluxo do login

A aplicação busca o usuário pelo nome e verifica a senha com `check_password()`. Se as credenciais forem válidas, `login_user(user)` inicia a sessão autenticada.

O logout utiliza POST porque altera o estado da sessão. O formulário de logout deve incluir o token CSRF.

## 11. Criando a interface HTML

No arquivo `app/templates/base.html`:

```
<!doctype html>
<html lang="pt-BR">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{% block title %}Gerenciador de tarefas{% endblock %}</title>
</head>
<body>
    {% with messages = get_flashed_messages() %}
        {% if messages %}
            <ul>
                {% for message in messages %}
                    <li>{{ message }}</li>
                {% endfor %}
            </ul>
        {% endif %}
    {% endwith %}

    <main>
        {% block content %}{% endblock %}
    </main>
</body>
</html>
```

Esse arquivo utiliza herança de templates do Jinja. As páginas filhas podem preencher os blocos `title` e `content` sem duplicar toda a estrutura HTML.

No arquivo `app/templates/auth/login.html`:

```
{% extends "base.html" %}

{% block title %}Entrar{% endblock %}

{% block content %}
<h1>Entrar</h1>

<form method="post">
    {{ form.hidden_tag() }}

    <div>
        {{ form.username.label }}
        {{ form.username() }}
        {% for error in form.username.errors %}
            <p>{{ error }}</p>
        {% endfor %}
    </div>

    <div>
        {{ form.password.label }}
        {{ form.password() }}
        {% for error in form.password.errors %}
            <p>{{ error }}</p>
        {% endfor %}
    </div>

    {{ form.submit() }}
</form>

<a href="{{ url_for('auth.register') }}">Criar conta</a>
{% endblock %}
```

O método `hidden_tag()` inclui os campos ocultos necessários ao formulário, incluindo o token CSRF.

O WTForms também disponibiliza os erros de validação por campo, permitindo informar ao usuário o que precisa ser corrigido.

O template de cadastro pode seguir o mesmo padrão, utilizando os campos `username`, `password` e `confirm_password`.

## 12. Implementando o CRUD de tarefas

CRUD significa _Create, Read, Update, Delete_: criar, consultar, atualizar e excluir dados.

No arquivo `app/tasks/__init__.py`, prepare o pacote. Em `app/tasks/routes.py`:

```
from flask import Blueprint, abort, redirect, render_template, request, url_for
from flask_login import current_user, login_required
from sqlalchemy import select

from app.extensions import db
from app.models import Task

tasks_bp = Blueprint("tasks", __name__)


@tasks_bp.route("/", methods=["GET", "POST"])
@login_required
def index():
    if request.method == "POST":
        title = request.form.get("title", "").strip()

        if not title or len(title) > 200:
            abort(400, description="Título inválido.")

        task = Task(
            title=title,
            user_id=current_user.id,
        )

        db.session.add(task)
        db.session.commit()

        return redirect(url_for("tasks.index"))

    tasks = db.session.scalars(
        select(Task)
        .where(Task.user_id == current_user.id)
        .order_by(Task.id.desc())
    ).all()

    return render_template("tasks/index.html", tasks=tasks)


@tasks_bp.route("/<int:task_id>/complete", methods=["POST"])
@login_required
def complete(task_id):
    task = db.session.scalar(
        select(Task).where(
            Task.id == task_id,
            Task.user_id == current_user.id,
        )
    )

    if task is None:
        abort(404)

    task.completed = not task.completed
    db.session.commit()

    return redirect(url_for("tasks.index"))


@tasks_bp.route("/<int:task_id>/delete", methods=["POST"])
@login_required
def delete(task_id):
    task = db.session.scalar(
        select(Task).where(
            Task.id == task_id,
            Task.user_id == current_user.id,
        )
    )

    if task is None:
        abort(404)

    db.session.delete(task)
    db.session.commit()

    return redirect(url_for("tasks.index"))
```

### O ponto mais importante: autorização por proprietário

Observe a consulta:

```
select(Task).where(
    Task.id == task_id,
    Task.user_id == current_user.id,
)
```

Ela não procura apenas a tarefa pelo identificador. Também exige que ela pertença ao usuário autenticado.

Isso impede que uma pessoa tente alterar ou excluir tarefas de outra pessoa simplesmente modificando o ID na URL.

O decorador `@login_required` exige autenticação, mas **não garante, sozinho, que o usuário tenha permissão sobre cada registro**. A aplicação deve verificar a propriedade ou a autorização necessária em cada operação.

### Template das tarefas

No arquivo `app/templates/tasks/index.html`:

```
{% extends "base.html" %}

{% block title %}Minhas tarefas{% endblock %}

{% block content %}
<h1>Minhas tarefas</h1>

<form method="post">
    <input
        type="hidden"
        name="csrf_token"
        value="{{ csrf_token() }}"
    >

    <label for="title">Nova tarefa</label>
    <input id="title" name="title" maxlength="200" required>

    <button type="submit">Adicionar</button>
</form>

<ul>
    {% for task in tasks %}
        <li>
            {% if task.completed %}
                <s>{{ task.title }}</s>
            {% else %}
                {{ task.title }}
            {% endif %}

            <form method="post"
                  action="{{ url_for('tasks.complete', task_id=task.id) }}">
                <input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
                <button type="submit">
                    {% if task.completed %}Reabrir{% else %}Concluir{% endif %}
                </button>
            </form>

            <form method="post"
                  action="{{ url_for('tasks.delete', task_id=task.id) }}">
                <input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
                <button type="submit">Excluir</button>
            </form>
        </li>
    {% else %}
        <li>Você ainda não tem tarefas.</li>
    {% endfor %}
</ul>

<form method="post" action="{{ url_for('auth.logout') }}">
    <input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
    <button type="submit">Sair</button>
</form>
{% endblock %}
```

O atributo `required` no HTML melhora a experiência no navegador, mas a validação no servidor continua necessária, pois os dados enviados pelo cliente podem ser modificados.

## 13. Criando uma API JSON

Uma API permite que outros programas interajam com a aplicação sem depender da interface HTML.

No arquivo `app/api/__init__.py`, prepare o pacote. Em `app/api/routes.py`:

```
from flask import Blueprint, jsonify
from flask_login import current_user, login_required
from sqlalchemy import select

from app.extensions import db
from app.models import Task

api_bp = Blueprint("api", __name__)


@api_bp.route("/tasks", methods=["GET"])
@login_required
def list_tasks():
    tasks = db.session.scalars(
        select(Task)
        .where(Task.user_id == current_user.id)
        .order_by(Task.id.desc())
    ).all()

    return jsonify([
        {
            "id": task.id,
            "title": task.title,
            "completed": task.completed,
        }
        for task in tasks
    ])
```

A rota `GET /api/tasks` retorna as tarefas do usuário autenticado em JSON.

Exemplo de resposta:

```
[
  {
    "id": 3,
    "title": "Estudar Flask",
    "completed": false
  },
  {
    "id": 2,
    "title": "Praticar SQLAlchemy",
    "completed": true
  }
]
```

A API reaproveita o mesmo banco e as mesmas regras de propriedade das tarefas.

**Observação sobre autenticação:** esse exemplo utiliza a sessão do Flask-Login. Ele não é, por si só, uma API de autenticação por token para clientes externos. Para esse cenário, seria necessário definir um mecanismo próprio de autenticação, autorização e proteção contra abuso. Em requisições autenticadas por cookie, mantenha uma estratégia CSRF apropriada para as operações que modificam dados.

## 14. Criando o ponto de entrada

No arquivo `run.py`:

```
from app import create_app

app = create_app()
```

Para iniciar localmente, com a variável `SECRET_KEY` configurada:

```
flask --app run run --debug
```

O modo de depuração deve ser usado apenas durante o desenvolvimento. Não o habilite em produção.

Antes de executar a aplicação pela primeira vez, configure o esquema do banco. Para um exercício inicial, é possível criar as tabelas explicitamente em um contexto da aplicação:

```
from app import create_app
from app.extensions import db
from app import models

app = create_app()

with app.app_context():
    db.create_all()
```

Esse trecho pode ser executado uma única vez em um script separado de inicialização. O `import models` garante que os modelos sejam carregados antes da criação das tabelas.

`db.create_all()` cria tabelas ausentes, mas não atualiza automaticamente a estrutura de tabelas existentes quando os modelos mudam. Para evoluir o banco sem recriá-lo, utilize Flask-Migrate e Alembic.

## 15. Testando a aplicação com pytest

Os testes verificam se a aplicação se comporta como esperado e ajudam a detectar regressões quando o código é alterado.

Crie `tests/conftest.py`:

```
import pytest

from app import create_app
from app.extensions import db


@pytest.fixture
def app():
    app = create_app({
        "TESTING": True,
        "SECRET_KEY": "chave-exclusiva-dos-testes",
        "SQLALCHEMY_DATABASE_URI": "sqlite://",
        "WTF_CSRF_ENABLED": False,
    })

    with app.app_context():
        db.create_all()
        yield app
        db.session.remove()
        db.drop_all()


@pytest.fixture
def client(app):
    return app.test_client()
```

### Entendendo as fixtures

- `app`: cria uma aplicação configurada para testes.
    
- `TESTING=True`: ativa o modo de teste.
    
- `sqlite://`: utiliza um banco SQLite em memória para esse exemplo.
    
- `WTF_CSRF_ENABLED=False`: simplifica os testes HTTP apresentados aqui.
    
- `db.create_all()`: cria as tabelas antes dos testes.
    
- `db.drop_all()`: remove as tabelas ao terminar.
    

Desativar CSRF nesse contexto é uma conveniência para testes específicos, não uma recomendação para produção. Também é importante criar testes próprios que confirmem o funcionamento da proteção CSRF.

Crie `tests/test_auth.py`:

```
def test_login_page_returns_success(client):
    response = client.get("/auth/login")

    assert response.status_code == 200
    assert b"Entrar" in response.data
```

Esse teste verifica se a página de login é acessível e retorna HTTP 200.

Execute:

```
pytest -v
```

### Próximos testes necessários

Depois do primeiro teste, amplie a cobertura para verificar:

- Cadastro com dados válidos.
    
- Rejeição de nomes de usuário duplicados.
    
- Login com credenciais corretas e incorretas.
    
- Redirecionamento de usuários não autenticados.
    
- Criação e conclusão de tarefas.
    
- Impossibilidade de acessar tarefas de outro usuário.
    
- Proteção CSRF nos formulários.
    
- Respostas e permissões dos endpoints da API.
    

Não basta testar apenas se uma página abre. Em uma aplicação com usuários e dados privados, os testes de autorização são especialmente importantes.

## 16. Preparando o projeto para produção

Antes de publicar a aplicação:

1. Configure uma `SECRET_KEY` forte fora do código-fonte.
    
2. Desative o modo de depuração.
    
3. Use um servidor WSGI de produção, como Gunicorn, em vez do servidor de desenvolvimento do Flask.
    
4. Configure corretamente o banco de dados e as migrações.
    
5. Habilite HTTPS no ambiente de implantação.
    
6. Revise cookies, sessões, CSRF e permissões.
    
7. Configure logs e monitoramento de erros.
    
8. Execute os testes automatizados antes de cada implantação.
    
9. Não exponha arquivos `.env`, bancos locais ou credenciais no repositório.
    
10. Verifique se as consultas sempre respeitam as permissões do usuário autenticado.
    

O comando utilizado para executar a aplicação em produção depende da forma como o ambiente é configurado. Por exemplo, Gunicorn pode importar o objeto `app` definido em `run.py`, mas o processo deve receber as variáveis de ambiente e as configurações de rede apropriadas.

## 17. Como os componentes se conectam

O fluxo de uma operação típica pode ser representado assim:

```
Navegador
   |
   | Requisição HTTP
   v
Blueprint / Rota
   |
   | Verifica autenticação e autorização
   v
Validação dos dados
   |
   v
Modelo SQLAlchemy
   |
   | Consulta ou alteração
   v
Banco de dados
   |
   | Resultado
   v
Template HTML ou resposta JSON
   |
   v
Navegador ou cliente da API
```

Cada camada possui uma responsabilidade específica:

- **Rota:** recebe a requisição e coordena a operação.
    
- **Formulário e validação:** verificam os dados enviados.
    
- **Autenticação e autorização:** determinam quem está acessando e o que pode fazer.
    
- **Modelo:** representa os dados e seus relacionamentos.
    
- **Banco:** persiste os registros.
    
- **Template ou JSON:** apresenta ou disponibiliza o resultado.
    
- **Testes:** verificam se os fluxos funcionam e se as regras de segurança são respeitadas.
    

Essa separação torna a aplicação mais fácil de entender, testar, manter e ampliar.

---
# 📚 Material Complementar — Playlist de Flask

## Flask — Lan Code

**Canal:** Lan Code

**Playlist:** Flask — Seriezinha paia de flask

**Link:** [https://www.youtube.com/playlist?list=PLyOx5V4KUjDf3-flamHYkJ2ygYmdRDdE9](https://www.youtube.com/playlist?list=PLyOx5V4KUjDf3-flamHYkJ2ygYmdRDdE9)

**Conteúdo:** playlist com 6 vídeos sobre Flask.

### Quando utilizar este material?

Esta playlist é uma referência complementar à documentação **Flask — Do Básico ao Avançado**. As aulas podem ser utilizadas para:

- Revisar conceitos estudados na documentação.
    
- Tirar dúvidas durante o desenvolvimento de aplicações Flask.
    
- Acompanhar explicações em vídeo quando um conceito não ficar claro apenas pela leitura.
    
- Reforçar o aprendizado por meio de uma abordagem diferente da documentação escrita.
    

### Como aproveitar a playlist

Utilize esta documentação como material de consulta técnica e a playlist como apoio ao aprendizado. Ao encontrar uma dúvida, procure o tópico correspondente na anotação e, se necessário, assista às aulas para reforçar a compreensão.

O objetivo é ter duas fontes complementares de estudo: **uma documentação organizada para consulta rápida e aulas em vídeo para aprofundar a compreensão dos conceitos.**

---
---

# Módulo 15 — Extensões do Projeto Prático: Flash, Migrações, CLI e Administração

## 1. Objetivo do módulo

No Módulo 14 construímos o gerenciador de tarefas. Agora vamos evoluir o mesmo projeto com recursos que aparecem em quase toda aplicação real:

- Mensagens flash com categorias e estilo.
- Redirecionamento seguro após o login (`next`).
- Migrações de banco com Flask-Migrate.
- Comandos próprios no terminal, como `create-admin`.
- Área administrativa protegida por papel de usuário.
- Testes para tudo isso.
- Variáveis de ambiente com `.env`.

Todos os exemplos usam os nomes do Módulo 14: `User`, `Task`, `auth_bp`, `extensions.py` e a Application Factory.

## 2. Mensagens flash em profundidade

### 2.1. O que é `flash()`?

`flash()` guarda uma mensagem temporária para ser exibida ao usuário na próxima renderização de template. Ela é usada para avisos como "Cadastro realizado" ou "Usuário ou senha inválidos".

Fluxo típico:

```
POST /auth/register
    |
    | flash("Cadastro realizado. Faça login.")
    | redirect(url_for("auth.login"))
    v
GET /auth/login
    |
    | get_flashed_messages() no template
    v
Mensagem exibida e removida
```

### 2.2. Como funciona por dentro?

- A mensagem é gravada na **sessão** do Flask, que fica em um cookie assinado com a `SECRET_KEY`.
- Sem `SECRET_KEY`, `flash()` não funciona.
- Quando `get_flashed_messages()` é chamada, as mensagens são lidas e **removidas** da sessão. Elas aparecem uma vez só.
- Como ficam em cookie, o tamanho é limitado (por volta de 4 KB). Use mensagens curtas.

### 2.3. Categorias

O segundo argumento de `flash()` é a categoria:

```
flash("Cadastro realizado. Faça login.", "success")
flash("Usuário ou senha inválidos.", "error")
flash("Faça login para continuar.", "info")
```

A categoria é só um texto. Você escolhe os nomes (`success`, `error`, `info`, `warning`...) e decide como cada um será exibido.

Atualize as rotas do Módulo 14 para informar a categoria:

```
flash("Esse nome de usuário já está em uso.", "error")
flash("Cadastro realizado. Faça login.", "success")
flash("Usuário ou senha inválidos.", "error")
```

### 2.4. Exibindo as categorias no `base.html`

Use `with_categories=True` para receber pares `(categoria, mensagem)`:

```
{% set flash_classes = {
    "success": "bg-green-100 text-green-800",
    "error": "bg-red-100 text-red-800",
    "info": "bg-blue-100 text-blue-800",
    "warning": "bg-yellow-100 text-yellow-800",
} %}

{% with messages = get_flashed_messages(with_categories=true) %}
    {% for category, message in messages %}
        <div class="mb-2 rounded p-3 {{ flash_classes.get(category, 'bg-gray-100 text-gray-800') }}">
            {{ message }}
        </div>
    {% endfor %}
{% endwith %}
```

Entendendo o código:

- `{% set flash_classes = {...} %}`: cria um dicionário Jinja ligando cada categoria às classes do Tailwind.
- `get_flashed_messages(with_categories=true)`: devolve as mensagens junto com a categoria.
- `flash_classes.get(category, '...')`: usa uma classe padrão se a categoria não estiver no dicionário.
- `{{ message }}`: o Jinja escapa o conteúdo automaticamente, o que protege contra XSS.

### 2.5. Mensagens do próprio Flask-Login

Quando um usuário anônimo acessa uma rota com `@login_required`, o Flask-Login também usa `flash()` internamente. Personalize o texto e a categoria na factory:

```
login_manager.login_view = "auth.login"
login_manager.login_message = "Faça login para acessar esta página."
login_manager.login_message_category = "info"
```

### 2.6. Cuidados com flash

- Não coloque informações sensíveis na mensagem, porque ela passa pelo cookie de sessão.
- Não marque como seguro (`|safe` ou `Markup`) texto que contenha dados digitados pelo usuário.
- Em mensagens de login, não revele se o usuário existe. Por isso a mensagem é "Usuário ou senha inválidos."
- Use `flash()` antes de `redirect()` (padrão Post/Redirect/Get). Depois de um `render_template()` direto, a mensagem também aparece, mas no mesmo carregamento da página.

## 3. Redirecionamento seguro após o login (`next`)

Quando o Flask-Login envia um usuário anônimo para `/auth/login`, ele adiciona o endereço original na query string:

```
/auth/login?next=/tasks/
```

Depois do login, o ideal é voltar para esse endereço. Mas **nunca redirecione cegamente** para o valor de `next`. Um atacante poderia montar `?next=https://site-malicioso.com` (vulnerabilidade chamada _open redirect_).

Crie `app/auth/utils.py`:

```
from urllib.parse import urlsplit


def is_safe_redirect_url(target):
    if not target:
        return False

    if "\\" in target or target.startswith("//"):
        return False

    parts = urlsplit(target)

    return not parts.scheme and not parts.netloc and target.startswith("/")
```

A função aceita apenas caminhos internos do próprio site, como `/tasks/`. Ela recusa:

- Endereços completos, como `https://outro-site.com`.
- Endereços sem esquema, como `//outro-site.com`.
- Barras invertidas, que alguns navegadores tratam como barras normais.

Na rota de login:

```
from flask import request
from app.auth.utils import is_safe_redirect_url

...

if user and user.check_password(form.password.data):
    login_user(user)

    next_page = request.args.get("next")

    if not is_safe_redirect_url(next_page):
        next_page = url_for("tasks.index")

    return redirect(next_page)
```

Para o `next` sobreviver ao envio do formulário, o `<form>` do login deve postar para a mesma URL. Com `<form method="post">` sem `action`, a query string é mantida.

## 4. Migrações com Flask-Migrate

### 4.1. Por que migrar?

Vamos adicionar uma coluna `is_admin` à tabela `user`. Se o banco já existe, `db.create_all()` **não altera** tabelas existentes. Para evoluir o esquema sem perder dados, usamos **migrações**.

- **Alembic:** ferramenta que registra alterações do esquema em arquivos versionados.
- **Flask-Migrate:** integra o Alembic ao Flask e ao Flask-SQLAlchemy.

Uma migração é um arquivo Python com duas funções:

- `upgrade()`: aplica a mudança.
- `downgrade()`: desfaz a mudança.

### 4.2. Instalando e configurando

```
pip install Flask-Migrate
pip freeze > requirements.txt
```

Em `app/extensions.py`:

```
from flask_login import LoginManager
from flask_migrate import Migrate
from flask_sqlalchemy import SQLAlchemy
from flask_wtf.csrf import CSRFProtect

db = SQLAlchemy()
login_manager = LoginManager()
csrf = CSRFProtect()
migrate = Migrate()
```

Na factory (`app/__init__.py`):

```
from app.extensions import db, login_manager, csrf, migrate

...

db.init_app(app)
migrate.init_app(app, db)
login_manager.init_app(app)
csrf.init_app(app)

from app import models  # garante que os modelos sejam carregados
```

O `import models` é importante porque o Alembic só enxerga os modelos que já foram importados.

### 4.3. Criando o banco pela primeira vez

```
flask --app run db init
flask --app run db migrate -m "cria tabelas iniciais"
flask --app run db upgrade
```

O que cada comando faz:

|Comando|Função|
|---|---|
|`db init`|Cria a pasta `migrations/` (só uma vez por projeto).|
|`db migrate -m "..."`|Compara os modelos com o banco e gera um arquivo de migração.|
|`db upgrade`|Aplica as migrações pendentes ao banco.|
|`db downgrade`|Desfaz a última migração.|
|`db current`|Mostra a versão atual do banco.|
|`db history`|Lista o histórico de migrações.|

A partir de agora, `flask db upgrade` substitui o script com `db.create_all()` do Módulo 14.

**Estudo local:** se você já criou o banco com `create_all()`, o jeito mais simples é apagar o arquivo do banco (com Flask-SQLAlchemy 3.x, o SQLite relativo fica na pasta `instance/`) e recriá-lo com `db upgrade`. Com dados reais, consulte a documentação do Alembic sobre `stamp` antes de mexer.

### 4.4. Adicionando `is_admin` ao modelo

Em `app/models.py`, dentro de `User`:

```
is_admin = db.Column(
    db.Boolean,
    nullable=False,
    default=False,
    server_default=db.false(),
)
```

Diferença entre os dois valores padrão:

- `default=False`: valor aplicado **pelo Python/SQLAlchemy** ao criar um objeto.
- `server_default=db.false()`: valor definido **no banco**. Ele é necessário para adicionar uma coluna `NOT NULL` a uma tabela que já tem linhas, porque o banco precisa saber o valor das linhas existentes.

Gere e aplique a migração:

```
flask --app run db migrate -m "adiciona is_admin ao usuario"
flask --app run db upgrade
```

### 4.5. Cuidados com migrações

- **Revise o arquivo gerado** em `migrations/versions/` antes de aplicar. O `migrate` automático não detecta tudo; uma renomeação de coluna, por exemplo, pode virar "remover coluna + criar coluna", o que apaga dados.
- O SQLite tem suporte limitado a `ALTER TABLE`. Alterações como remover ou modificar colunas podem exigir o _batch mode_ do Alembic.
- Versione a pasta `migrations/` junto com o código.
- Faça backup antes de rodar `upgrade` em produção.
- Nunca edite uma migração que já foi aplicada em outro ambiente; crie uma nova.

## 5. Comandos personalizados com Click: `create-admin`

### 5.1. O que é a CLI do Flask?

O comando `flask` é construído com a biblioteca **Click**. Podemos registrar comandos próprios em `app.cli`, que funcionam assim:

```
flask --app run create-admin
```

Eles servem para tarefas administrativas que **não devem** virar uma rota pública: criar o primeiro administrador, popular dados de exemplo, limpar registros antigos etc.

### 5.2. Por que não criar o admin pelo cadastro?

A seção 7.12 já alertou: o status administrativo **não deve** vir de um formulário público. Mas então como nasce o primeiro administrador? Por um comando executado por quem tem acesso ao servidor.

### 5.3. Criando `app/cli.py`

```
import click
from sqlalchemy import select

from app.extensions import db
from app.models import User, Task


def register_commands(app):
    @app.cli.command("create-admin")
    @click.option("--username", prompt="Nome de usuário", help="Nome do administrador.")
    @click.password_option()
    def create_admin(username, password):
        """Cria um usuário administrador."""
        username = username.strip()

        if len(password) < 8:
            raise click.ClickException("A senha deve ter pelo menos 8 caracteres.")

        existing_user = db.session.scalar(
            select(User).where(User.username == username)
        )

        if existing_user:
            raise click.ClickException(f"O usuário '{username}' já existe.")

        user = User(username=username, is_admin=True)
        user.set_password(password)

        db.session.add(user)
        db.session.commit()

        click.secho(f"Administrador '{username}' criado.", fg="green")

    @app.cli.command("promote-admin")
    @click.argument("username")
    def promote_admin(username):
        """Transforma um usuário existente em administrador."""
        user = db.session.scalar(
            select(User).where(User.username == username)
        )

        if user is None:
            raise click.ClickException(f"Usuário '{username}' não encontrado.")

        user.is_admin = True
        db.session.commit()

        click.secho(f"'{username}' agora é administrador.", fg="green")

    @app.shell_context_processor
    def shell_context():
        return {"db": db, "User": User, "Task": Task}
```

Registre na factory, depois das extensões:

```
from app.cli import register_commands

register_commands(app)
```

### 5.3. Entendendo o código

|Elemento|Função|
|---|---|
|`@app.cli.command("create-admin")`|Registra o comando com esse nome no terminal.|
|`@click.option("--username", prompt=...)`|Aceita `--username ana`; se omitido, pergunta no terminal.|
|`@click.password_option()`|Cria a opção `--password`, que pergunta a senha **sem exibir** o que é digitado e pede confirmação.|
|`@click.argument("username")`|Argumento posicional: `flask promote-admin ana`.|
|`click.ClickException`|Mostra `Error: ...` e encerra com código de saída diferente de 0.|
|`click.secho(..., fg="green")`|Imprime texto colorido.|
|`@app.shell_context_processor`|Importa `db`, `User` e `Task` automaticamente no `flask shell`.|

Comandos registrados com `@app.cli.command` já rodam dentro de um **contexto da aplicação**. Por isso `db.session` funciona sem `with app.app_context():`.

### 5.4. Usando os comandos

```
flask --app run create-admin
flask --app run create-admin --username ana
flask --app run promote-admin ana
flask --app run --help
flask --app run create-admin --help
```

O texto entre aspas triplas dentro da função (_docstring_) vira a descrição exibida no `--help`.

### 5.5. Cuidados

- Prefira deixar a senha ser digitada no prompt. Passar a senha por argumento a deixa no histórico do shell e na lista de processos.
- Para automação (containers, CI), a opção pode ler uma variável de ambiente com `envvar="ADMIN_PASSWORD"`. Nesse caso, trate essa variável como segredo.
- Aplique no comando as mesmas regras de senha do formulário de cadastro.
- O comando exige acesso ao servidor e ao banco. Esse acesso é o que garante a segurança do primeiro administrador.

### 5.6. `flask shell`

O `flask shell` abre um console Python com a aplicação já carregada:

```
flask --app run shell
```

```
>>> User.query.count()
>>> from sqlalchemy import select
>>> db.session.scalars(select(User)).all()
```

É útil para inspecionar dados durante o estudo. Para tarefas repetíveis, prefira um comando CLI.

## 6. Autorização por papéis: área administrativa

### 6.1. O decorator `admin_required`

Em vez de repetir `if not current_user.is_admin: abort(403)` em cada rota, criamos um decorator. Em `app/auth/decorators.py`:

```
from functools import wraps

from flask import abort
from flask_login import current_user

from app.extensions import login_manager


def admin_required(view):
    @wraps(view)
    def wrapped_view(*args, **kwargs):
        if not current_user.is_authenticated:
            return login_manager.unauthorized()

        if not current_user.is_admin:
            abort(403)

        return view(*args, **kwargs)

    return wrapped_view
```

Pontos importantes:

- `current_user.is_authenticated` é verificado primeiro. Um usuário anônimo não tem o atributo `is_admin`, e acessá-lo causaria um erro.
- `login_manager.unauthorized()` aplica o mesmo comportamento do `@login_required` (redirecionar para o login).
- `abort(403)` indica que o usuário está autenticado, mas **não tem permissão**.
- `@wraps(view)` preserva o nome e os metadados da função original. Sem ele, todas as views decoradas ficariam com o nome `wrapped_view` e o Flask reclamaria de endpoints duplicados.

### 6.2. Criando o Blueprint de administração

Estrutura:

```
app/
└── admin/
    ├── __init__.py
    └── routes.py
```

Em `app/admin/routes.py`:

```
from flask import Blueprint, flash, redirect, render_template, url_for
from flask_login import current_user
from sqlalchemy import select

from app.auth.decorators import admin_required
from app.extensions import db
from app.models import User

admin_bp = Blueprint("admin", __name__)


@admin_bp.route("/users")
@admin_required
def users():
    all_users = db.session.scalars(
        select(User).order_by(User.username)
    ).all()

    return render_template("admin/users.html", users=all_users)


@admin_bp.route("/users/<int:user_id>/delete", methods=["POST"])
@admin_required
def delete_user(user_id):
    user = db.get_or_404(User, user_id)

    if user.id == current_user.id:
        flash("Você não pode excluir a própria conta por aqui.", "error")
        return redirect(url_for("admin.users"))

    username = user.username

    db.session.delete(user)
    db.session.commit()

    flash(f"Usuário '{username}' excluído.", "success")
    return redirect(url_for("admin.users"))
```

Observações:

- A exclusão usa **POST**, nunca GET. Uma ação que altera dados não deve ser disparada por um link comum.
- `db.get_or_404()` busca pela chave primária e devolve 404 se o registro não existir.
- O nome do usuário é guardado em `username` **antes** de excluir, porque o objeto deixa de estar disponível depois do `commit()`.
- Lembre do `cascade` do modelo (seção 7 do Módulo 14): excluir o usuário exclui as tarefas dele.

Registre na factory:

```
from app.admin.routes import admin_bp

app.register_blueprint(admin_bp, url_prefix="/admin")
```

### 6.3. Template `admin/users.html`

Em `app/templates/admin/users.html`:

```
{% extends "base.html" %}

{% block title %}Usuários{% endblock %}

{% block content %}
<h1>Usuários</h1>

<table>
    <thead>
        <tr>
            <th>ID</th>
            <th>Usuário</th>
            <th>Admin</th>
            <th>Ações</th>
        </tr>
    </thead>
    <tbody>
        {% for user in users %}
        <tr>
            <td>{{ user.id }}</td>
            <td>{{ user.username }}</td>
            <td>{{ "Sim" if user.is_admin else "Não" }}</td>
            <td>
                <form method="post" action="{{ url_for('admin.delete_user', user_id=user.id) }}">
                    <input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
                    <button type="submit">Excluir</button>
                </form>
            </td>
        </tr>
        {% endfor %}
    </tbody>
</table>
{% endblock %}
```

Formulários HTML que você escreve à mão precisam incluir o token CSRF. A função `csrf_token()` fica disponível nos templates quando o `CSRFProtect` está ativo.

### 6.4. Mostrando o link apenas para administradores

No `base.html`:

```
{% if current_user.is_authenticated and current_user.is_admin %}
    <a href="{{ url_for('admin.users') }}">Administração</a>
{% endif %}
```

**Atenção:** esconder o link é só uma questão de interface. A proteção de verdade está no `@admin_required` do servidor. Um usuário comum ainda poderia digitar `/admin/users` na barra de endereço, e é o decorator que o impede.

### 6.5. Limites do modelo `is_admin`

Um campo booleano é suficiente quando só existem "usuário" e "administrador". Se surgirem mais papéis (editor, moderador, gerente), considere uma coluna `role` ou uma tabela `Role` com permissões. Evite espalhar verificações soltas pelo código; centralize-as em decorators ou funções.

## 7. Testando os novos recursos

Crie `tests/test_admin.py`:

```
from app.extensions import db
from app.models import User


def create_user(username, password, is_admin=False):
    user = User(username=username, is_admin=is_admin)
    user.set_password(password)
    db.session.add(user)
    db.session.commit()
    return user


def login(client, username, password):
    return client.post(
        "/auth/login",
        data={"username": username, "password": password},
        follow_redirects=True,
    )


def test_anonymous_user_is_redirected_to_login(client):
    response = client.get("/admin/users")

    assert response.status_code == 302
    assert "/auth/login" in response.headers["Location"]


def test_common_user_gets_403(client):
    create_user("ana", "senha-segura-123")
    login(client, "ana", "senha-segura-123")

    response = client.get("/admin/users")

    assert response.status_code == 403


def test_admin_can_list_users(client):
    create_user("root", "senha-segura-123", is_admin=True)
    login(client, "root", "senha-segura-123")

    response = client.get("/admin/users")

    assert response.status_code == 200


def test_invalid_login_shows_flash_message(client):
    response = login(client, "ninguem", "senha-errada")

    assert "Usuário ou senha inválidos." in response.get_data(as_text=True)
```

Pontos de atenção:

- `follow_redirects=True` faz o cliente seguir o redirecionamento e devolver a página final, onde a mensagem flash aparece.
- Usamos `get_data(as_text=True)` em vez de `response.data` porque o texto tem acentos, e literais `b"..."` do Python não aceitam caracteres fora do ASCII.
- Cada teste cria seus próprios usuários. Como a fixture `app` recria o banco, os testes não dependem uns dos outros.

Crie `tests/test_cli.py`:

```
from sqlalchemy import select

from app.extensions import db
from app.models import User


def test_create_admin_command(app):
    runner = app.test_cli_runner()

    result = runner.invoke(
        args=["create-admin", "--username", "ana"],
        input="senha-segura-123\nsenha-segura-123\n",
    )

    assert result.exit_code == 0

    user = db.session.scalar(select(User).where(User.username == "ana"))

    assert user is not None
    assert user.is_admin is True


def test_create_admin_rejects_short_password(app):
    runner = app.test_cli_runner()

    result = runner.invoke(
        args=["create-admin", "--username", "ana"],
        input="123\n123\n",
    )

    assert result.exit_code != 0
```

- `app.test_cli_runner()` executa os comandos do Flask dentro dos testes, sem abrir um terminal.
- O parâmetro `input` simula o que o usuário digitaria: a senha e a confirmação, uma por linha.

Execute:

```
pytest -v
```

## 8. Variáveis de ambiente com `.env`

No Módulo 14 definimos `SECRET_KEY` com `export` no terminal. Isso precisa ser repetido a cada novo terminal. Uma alternativa é o arquivo `.env`.

Instale:

```
pip install python-dotenv
```

Com `python-dotenv` instalado, o comando `flask` carrega automaticamente os arquivos `.env` e `.flaskenv` da pasta do projeto.

Exemplo de `.env`:

```
SECRET_KEY=troque-por-uma-chave-aleatoria
DATABASE_URL=sqlite:///gerenciador.db
```

Para gerar uma chave aleatória:

```
python -c "import secrets; print(secrets.token_hex(32))"
```

Diferença entre os dois arquivos:

|Arquivo|Uso|Vai para o Git?|
|---|---|---|
|`.env`|Segredos e valores específicos de cada máquina.|**Não.**|
|`.env.example`|Modelo com os nomes das variáveis, sem valores reais.|Sim.|
|`.flaskenv`|Opções públicas do Flask, como `FLASK_DEBUG=1`.|Sim.|

Crie um `.gitignore` com pelo menos:

```
.venv/
.env
instance/
__pycache__/
*.pyc
```

**Importante:** esse carregamento automático vale para o comando `flask`. Servidores como o Gunicorn não leem o `.env` por conta própria. Em produção, defina as variáveis no ambiente de implantação (seções 13.6 e 13.7).

## 9. Estrutura final do projeto

```
gerenciador/
├── app/
│   ├── __init__.py            # chama migrate.init_app e register_commands
│   ├── extensions.py          # agora inclui migrate
│   ├── models.py              # User agora tem is_admin
│   ├── cli.py                 # novo: create-admin, promote-admin, shell
│   ├── admin/                 # novo
│   │   ├── __init__.py
│   │   └── routes.py
│   ├── auth/
│   │   ├── __init__.py
│   │   ├── routes.py          # flash com categorias e next seguro
│   │   ├── forms.py
│   │   ├── decorators.py      # novo: admin_required
│   │   └── utils.py           # novo: is_safe_redirect_url
│   └── templates/
│       └── admin/
│           └── users.html     # novo
├── migrations/                # novo (versionar no Git)
├── tests/
│   ├── test_admin.py          # novo
│   └── test_cli.py            # novo
├── .env
├── .env.example
├── .gitignore
├── config.py
├── requirements.txt
└── run.py
```

## 10. Fluxo completo para colocar um administrador no ar

```
1. pip install -r requirements.txt
2. Criar o .env com SECRET_KEY e DATABASE_URL
3. flask --app run db upgrade
4. flask --app run create-admin
5. flask --app run run --debug
6. Entrar com o administrador e acessar /admin/users
```

## 11. Erros comuns neste módulo

|Sintoma|Causa provável|
|---|---|
|`flash()` não mostra nada|`get_flashed_messages()` ausente no `base.html` ou `SECRET_KEY` não configurada.|
|`db migrate` gera uma migração vazia|Os modelos não foram importados antes da geração.|
|Erro ao adicionar coluna `NOT NULL`|Falta `server_default` em uma tabela que já tem linhas.|
|`No such command 'create-admin'`|`register_commands(app)` não foi chamado na factory, ou falta `--app run`.|
|`AttributeError: 'AnonymousUserMixin' ... 'is_admin'`|Verificação de `is_admin` antes de `is_authenticated`.|
|`AssertionError: View function mapping is overwriting an existing endpoint`|Decorator sem `@wraps`.|
|`400 Bad Request` ao excluir usuário|Formulário sem o campo `csrf_token`.|
|Teste de CLI trava esperando entrada|Falta o parâmetro `input` com a senha e a confirmação.|

## 12. Resumo do módulo

- `flash(mensagem, categoria)` guarda mensagens na sessão; `get_flashed_messages(with_categories=true)` as exibe uma única vez.
- O parâmetro `next` deve ser validado antes de ser usado em um redirecionamento.
- Flask-Migrate versiona as alterações do banco; revise sempre a migração gerada antes de aplicar.
- Comandos com `@app.cli.command` rodam no contexto da aplicação e servem para tarefas administrativas, como `create-admin`.
- A senha do administrador deve ser digitada no prompt (`click.password_option()`), não passada por argumento.
- O `admin_required` centraliza a autorização por papel e depende de `@wraps`.
- Esconder um link não protege a rota; a verificação precisa estar no servidor.
- Cada recurso novo precisa de teste, principalmente os de autorização.
- Segredos ficam no `.env`, que nunca é versionado.

## 13. Próximos temas sugeridos

Assuntos que combinam com o projeto e ainda não aparecem na anotação:

- **Paginação:** `db.paginate()` para listar muitas tarefas ou usuários.
- **Filtros e variáveis globais no Jinja:** `@app.template_filter` e `@app.context_processor`.
- **Hooks de requisição:** `before_request`, `after_request` e o objeto `g`.
- **Limitação de tentativas de login:** Flask-Limiter.
- **Recuperação de senha por e-mail:** tokens assinados com `itsdangerous` e envio com Flask-Mail.
- **SQLAlchemy 2.0 moderno:** `Mapped`, `mapped_column` e `back_populates` no lugar de `backref`.
- **Docker:** empacotar a aplicação com Gunicorn.
- **CORS e autenticação por token** para uma API consumida por front-end separado.

---