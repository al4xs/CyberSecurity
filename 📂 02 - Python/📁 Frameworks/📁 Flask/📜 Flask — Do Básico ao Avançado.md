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
