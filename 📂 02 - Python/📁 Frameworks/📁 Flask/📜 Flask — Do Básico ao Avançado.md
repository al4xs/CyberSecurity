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
