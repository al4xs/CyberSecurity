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
