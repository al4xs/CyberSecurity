## 1. O que é Jinja e qual problema ele resolve?

O **Jinja** é um mecanismo de templates para Python. Ele permite criar um modelo de documento e preencher esse modelo com dados durante a execução da aplicação.

No desenvolvimento web com Flask, seu uso mais comum é gerar páginas HTML no servidor.

Imagine que você esteja desenvolvendo um sistema com uma página de perfil. Todos os usuários devem visualizar uma página com a mesma estrutura, mas cada um deve ver seu próprio nome.

Sem um mecanismo de templates, seria possível construir o HTML diretamente no Python:

```
nome = "Marcos"

html = f"<h1>Olá, {nome}!</h1>"
```

Nesse exemplo, a f-string do Python substitui `{nome}` pelo valor da variável.

O problema começa quando a página precisa ter dezenas de elementos HTML, condições, listas, formulários e partes reutilizáveis. Misturar toda a estrutura HTML com o código Python dificulta a manutenção.

O Jinja resolve esse problema separando duas responsabilidades:

- **Python e Flask:** processam a requisição, consultam os dados e decidem o que a aplicação deve fazer.
    
- **Jinja:** utiliza os dados recebidos para montar a apresentação HTML.
    

Veja como fica essa separação.

### Arquivo `app.py`

```
from flask import Flask, render_template

app = Flask(__name__)


@app.route("/")
def index():
    return render_template(
        "index.html",
        nome="Marcos",
    )
```

### Arquivo `templates/index.html`

```
<!doctype html>
<html lang="pt-BR">
<head>
    <meta charset="utf-8">
    <title>Página inicial</title>
</head>
<body>
    <h1>Olá, {{ nome }}!</h1>
</body>
</html>
```

### Entendendo o fluxo completo

**1.** `**app = Flask(__name__)**`

Cria a aplicação Flask. É essa aplicação que recebe as requisições HTTP e coordena a execução das rotas.

**2.** `**@app.route("/")**`

Registra uma rota para o caminho `/`. Quando o navegador solicita esse endereço, o Flask chama a função associada.

**3.** `**def index():**`

Define a função responsável por atender à rota. Ela não precisa receber argumentos nesse exemplo porque não estamos utilizando parâmetros de URL.

**4.** `**render_template("index.html", nome="Marcos")**`

Essa chamada realiza duas operações importantes:

- Solicita ao Jinja que carregue o template `index.html`.
    
- Fornece o valor `"Marcos"` ao template por meio da variável chamada `nome`.
    

O primeiro argumento identifica qual template deve ser carregado. O argumento `nome="Marcos"` é um argumento nomeado: o nome à esquerda do `=` será o identificador utilizado no template, e o valor à direita é o dado fornecido.

**5.** `**{{ nome }}**`

Esse trecho é uma expressão Jinja. Durante a renderização, o Jinja procura a variável `nome` no contexto do template e insere seu valor na saída.

O resultado final contém:

```
<h1>Olá, Marcos!</h1>
```

O navegador recebe o HTML renderizado, não o código Jinja original.

### O Jinja executa Python dentro do HTML?

Não exatamente. O Jinja possui sua própria linguagem de templates, com expressões, condições, laços, filtros e outras estruturas.

Ele permite operações simples, mas não aceita automaticamente qualquer instrução Python.

Por exemplo, isto é válido:

```
{% if usuario %}
    <p>Usuário disponível.</p>
{% endif %}
```

A estrutura `if` pertence à linguagem do Jinja. Ela não é uma instrução Python executada diretamente no arquivo HTML.

A ideia é manter o template focado na apresentação, deixando regras de negócio complexas, operações no banco e processamento dos dados no código Python.

## 2. Onde os templates ficam e como o Flask os encontra

Por padrão, o Flask procura os templates no diretório `templates` associado à aplicação.

Uma estrutura simples é:

```
projeto/
├── app.py
└── templates/
    ├── index.html
    ├── login.html
    └── perfil.html
```

Nesse caso, quando a rota utiliza:

```
return render_template("perfil.html")
```

o Flask procura o arquivo `perfil.html` no diretório de templates configurado.

### E se os templates estiverem em subdiretórios?

Considere:

```
projeto/
├── app.py
└── templates/
    └── auth/
        ├── login.html
        └── perfil.html
```

Para carregar o perfil, informe o caminho relativo ao diretório de templates:

```
return render_template("auth/perfil.html")
```

O trecho `"auth/perfil.html"` é uma string que indica a localização do arquivo dentro da estrutura de templates.

A barra `/` separa os diretórios. Não é necessário fornecer o caminho absoluto do arquivo no sistema operacional.

### Estrutura com Application Factory

Em projetos maiores, a aplicação pode ser organizada assim:

```
projeto/
└── app/
    ├── __init__.py
    ├── auth/
    │   └── routes.py
    └── templates/
        ├── base.html
        └── auth/
            └── login.html
```

Quando o Flask é criado com o pacote `app`, o diretório padrão de templates fica associado a essa aplicação. O caminho usado em `render_template()` continua sendo relativo ao diretório de templates configurado.

Blueprints também podem ter seus próprios diretórios de templates. A resolução depende da configuração do Blueprint e da aplicação.

### Erro: `jinja2.exceptions.TemplateNotFound`

Esse erro significa que o Jinja não conseguiu encontrar o template solicitado.

Exemplo:

```
return render_template("login.html")
```

Mas o arquivo está em `templates/auth/login.html`.

O Flask poderá apresentar uma mensagem semelhante a:

```
jinja2.exceptions.TemplateNotFound: login.html
```

**Como diagnosticar:**

1. Confira o nome do arquivo.
    
2. Verifique se ele está no diretório correto.
    
3. Confira o caminho passado para `render_template()`.
    
4. Se utilizar Blueprints, verifique também a configuração dos diretórios de templates.
    

A correção para a estrutura apresentada seria:

```
return render_template("auth/login.html")
```

O erro não significa necessariamente que o Jinja esteja instalado incorretamente. Frequentemente, o problema é apenas o caminho do arquivo.

## 3. A função `render_template()` em detalhes

A função `render_template()` é fornecida pelo Flask e serve para renderizar um template com os dados disponibilizados pela aplicação.

Importação:

```
from flask import render_template
```

Sem essa importação, o nome `render_template` não estará disponível no módulo e uma chamada poderá gerar:

```
NameError: name 'render_template' is not defined
```

### Exemplo

```
@app.route("/produto")
def produto():
    return render_template(
        "produto.html",
        nome="Teclado",
        preco=149.90,
        disponivel=True,
    )
```

Observe que os dados são passados como argumentos nomeados.

- `nome="Teclado"` cria uma variável de contexto chamada `nome`.
    
- `preco=149.90` cria uma variável de contexto chamada `preco`.
    
- `disponivel=True` cria uma variável de contexto chamada `disponivel`.
    

O Jinja recebe esses valores para utilizá-los durante a renderização.

No template `produto.html`:

```
<h1>{{ nome }}</h1>
<p>Preço: {{ preco }}</p>

{% if disponivel %}
    <p>Produto disponível.</p>
{% else %}
    <p>Produto indisponível.</p>
{% endif %}
```

### Por que existe uma vírgula depois de cada argumento?

Em uma chamada de função Python, os argumentos são separados por vírgulas. Por exemplo:

```
minha_funcao(nome="Marcos", idade=17)
```

No exemplo com várias linhas, cada argumento fica em sua própria linha para facilitar a leitura:

```
return render_template(
    "produto.html",
    nome="Teclado",
    preco=149.90,
    disponivel=True,
)
```

A vírgula depois de `disponivel=True` é uma vírgula final. Em chamadas Python como essa, ela é permitida e costuma facilitar a adição, remoção ou reorganização de argumentos.

Não é uma tupla sendo criada nesse exemplo. É uma chamada de função com vários argumentos.

Uma tupla seria algo como:

```
dados = ("Teclado", 149.90, True)
```

Essa distinção importa porque uma chamada de função e uma tupla são estruturas diferentes, embora ambas possam utilizar vírgulas.

### Argumentos principais de `render_template()`

|Argumento|Obrigatório?|Funcionamento|
|---|---|---|
|`template_name_or_list`|Sim|Nome do template ou lista de nomes candidatos.|
|`**context`|Não|Argumentos nomeados que serão disponibilizados como variáveis no template.|

O primeiro argumento é obrigatório porque o Flask precisa saber qual template deve renderizar.

O contexto adicional é opcional do ponto de vista da função. Você pode renderizar um template sem enviar variáveis próprias:

```
return render_template("sobre.html")
```

Isso funciona desde que o template não dependa de variáveis que deveriam ter sido fornecidas pela rota.

Também é possível passar uma lista de nomes candidatos para o primeiro argumento. O Flask tentará encontrar um template disponível conforme as regras de seleção da função.

### O que significa `**context`?

Na documentação Python, `**context` representa a possibilidade de fornecer vários argumentos nomeados adicionais à função.

No uso prático, isso permite:

```
return render_template(
    "perfil.html",
    nome="Marcos",
    nivel="iniciante",
)
```

O template passa a ter acesso às variáveis `nome` e `nivel`.

Não significa que você deva escrever literalmente `**context` em toda chamada. É uma forma de representar o comportamento dos argumentos nomeados na assinatura da função.

### Erro: variável ausente no template

Considere:

```
return render_template("perfil.html", nome="Marcos")
```

E o template:

```
<h1>{{ usuario }}</h1>
```

A rota forneceu `nome`, mas o template tenta acessar `usuario`.

Dependendo da configuração do ambiente Jinja, o resultado pode ser uma saída vazia ou um erro de variável indefinida.

Em configurações que tratam variáveis indefinidas de forma estrita, pode aparecer um erro semelhante a:

```
jinja2.exceptions.UndefinedError: 'usuario' is undefined
```

A correção é utilizar o nome correto:

```
<h1>{{ nome }}</h1>
```

O importante é entender que os nomes usados no template precisam corresponder aos dados enviados no contexto.

## 4. Os três delimitadores fundamentais do Jinja

O Jinja utiliza delimitadores diferentes para expressões, instruções e comentários. Saber diferenciá-los evita boa parte dos erros de sintaxe.

### 4.1. Expressões: `{{ ... }}`

As expressões são utilizadas quando queremos produzir um valor na saída renderizada.

Exemplo:

```
<p>Olá, {{ nome }}!</p>
```

O que acontece:

1. O Jinja identifica `{{ ... }}` como uma expressão.
    
2. Procura a variável `nome` no contexto.
    
3. Obtém o valor correspondente.
    
4. Converte o valor para sua representação textual apropriada e aplica o escapamento automático quando configurado para esse template.
    
5. Insere o resultado no HTML.
    

Se `nome` for `"Marcos"`, a saída será:

```
<p>Olá, Marcos!</p>
```

As chaves duplas não aparecem no resultado final.

Expressões também podem realizar operações:

```
<p>Subtotal: {{ quantidade * preco }}</p>
```

Se `quantidade` for `3` e `preco` for `10`, o Jinja calculará `3 * 10` e renderizará `30`.

Isso é útil para pequenas operações de apresentação. Cálculos financeiros e regras importantes de negócio devem ser tratados com o tipo numérico e a lógica adequados no Python.

### 4.2. Instruções: `{% ... %}`

As instruções controlam o comportamento da renderização.

Exemplo:

```
{% if usuario %}
    <p>Bem-vindo!</p>
{% endif %}
```

O Jinja avalia a condição e decide se o parágrafo deve aparecer. As instruções não são incluídas diretamente no HTML final.

O delimitador `{%` inicia uma instrução e `%}` encerra essa instrução.

Algumas estruturas precisam de um fechamento próprio, como `if`/`endif` e `for`/`endfor`. Outros recursos, como certas instruções de herança ou inclusão, têm formas sintáticas diferentes.

### Erro: `TemplateSyntaxError`

Se uma instrução estiver incompleta ou tiver sintaxe inválida, o Jinja pode apresentar um erro como:

```
jinja2.exceptions.TemplateSyntaxError
```

Por exemplo:

```
{% if usuario %}
    <p>Bem-vindo!</p>
```

O bloco `if` não foi encerrado.

Correção:

```
{% if usuario %}
    <p>Bem-vindo!</p>
{% endif %}
```

A mensagem de erro normalmente indica o template e uma linha aproximada do problema. Use essa informação para localizar a estrutura que está incompleta ou incorreta.

### 4.3. Comentários: `{# ... #}`

Os comentários Jinja servem para registrar observações no próprio template sem enviá-las na saída renderizada.

```
{# Esta página apresenta os dados do usuário. #}

<h1>{{ nome }}</h1>
```

O comentário é removido durante a renderização.

Isso é diferente de um comentário HTML:

```
<!-- Comentário HTML -->
```

Um comentário HTML pode aparecer no código-fonte recebido pelo navegador. O comentário Jinja não é incluído no HTML renderizado.

Use comentários para explicar uma decisão de apresentação ou registrar uma observação útil para quem mantém o template.

## 5. Passando tipos diferentes de dados ao Jinja

O contexto de um template pode receber strings, números, valores booleanos, listas, dicionários e objetos Python.

O Jinja não precisa receber todos esses dados como strings. Ele consegue trabalhar com vários tipos de valores e apresentar os resultados durante a renderização.

### 5.1. Strings

Python:

```
return render_template(
    "perfil.html",
    nome="Marcos",
)
```

Jinja:

```
<p>Nome: {{ nome }}</p>
```

O valor `"Marcos"` é uma string Python. Ao renderizar `{{ nome }}`, o Jinja utiliza esse valor para produzir o conteúdo textual.

A variável `nome` não precisa ter o mesmo nome da variável original que existia na função Python antes da chamada. O que importa é o nome usado no argumento nomeado de `render_template()`.

Por exemplo:

```
nome_completo = "Marcos Silva"

return render_template(
    "perfil.html",
    nome=nome_completo,
)
```

O template utiliza `nome`, porque esse foi o nome definido no contexto, embora a variável original no Python seja `nome_completo`.

### 5.2. Números

Python:

```
return render_template(
    "produto.html",
    preco=99.90,
    quantidade=3,
)
```

Jinja:

```
<p>Preço: {{ preco }}</p>
<p>Quantidade: {{ quantidade }}</p>
<p>Subtotal: {{ preco * quantidade }}</p>
```

A multiplicação acontece durante a avaliação da expressão Jinja.

O preço e a quantidade não precisam ser strings. Nesse exemplo, o Python envia um número decimal e um inteiro.

Entretanto, para cálculos financeiros, `float` pode produzir resultados com pequenas diferenças de representação. Quando a precisão monetária importa, considere utilizar `Decimal` no Python e controlar a formatação da saída.

### 5.3. Booleanos

Python:

```
return render_template(
    "perfil.html",
    autenticado=True,
)
```

O valor `True` é um booleano Python. No template:

```
{% if autenticado %}
    <p>Você está autenticado.</p>
{% else %}
    <p>Você não está autenticado.</p>
{% endif %}
```

O Jinja avalia o valor de `autenticado` como uma condição.

- Se for verdadeiro, renderiza o primeiro parágrafo.
    
- Se for falso, renderiza o conteúdo do `else`.
    

**Atenção:** esse exemplo demonstra apenas uma condição de apresentação. Em uma aplicação Flask real, o estado de autenticação deve ser determinado pelo mecanismo de autenticação do servidor, não por um booleano enviado livremente pelo navegador.

### 5.4. Listas

Python:

```
tarefas = [
    "Estudar Python",
    "Praticar Flask",
    "Revisar Jinja",
]

return render_template(
    "tarefas.html",
    tarefas=tarefas,
)
```

A lista contém três strings. Os colchetes `[]` são a sintaxe de criação de uma lista Python, e cada item é separado por vírgula.

Na chamada de `render_template()`, o argumento `tarefas=tarefas` associa a variável de contexto `tarefas` à lista criada no Python.

O nome aparece duas vezes, mas cada ocorrência tem uma função:

- À esquerda do `=`, está o nome que o template utilizará.
    
- À direita do `=`, está a variável Python cujo valor será enviado.
    

No template:

```
<ul>
    {% for tarefa in tarefas %}
        <li>{{ tarefa }}</li>
    {% endfor %}
</ul>
```

O Jinja percorre a lista e executa o conteúdo do bloco para cada item.

Na primeira iteração, `tarefa` representa `"Estudar Python"`. Na segunda, representa `"Praticar Flask"`. Na terceira, representa `"Revisar Jinja"`.

A expressão `{{ tarefa }}` insere o item atual no HTML.

O resultado é uma lista HTML com três elementos `<li>`.

### 5.5. Dicionários

Python:

```
usuario = {
    "nome": "Marcos",
    "nivel": "iniciante",
    "ativo": True,
}

return render_template(
    "perfil.html",
    usuario=usuario,
)
```

Um dicionário armazena associações entre chaves e valores. Nesse caso, `"nome"`, `"nivel"` e `"ativo"` são as chaves.

No template:

```
<h1>{{ usuario["nome"] }}</h1>
<p>Nível: {{ usuario["nivel"] }}</p>
```

O acesso por colchetes pede o valor associado à chave especificada.

Também é possível escrever:

```
<h1>{{ usuario.nome }}</h1>
```

No Jinja, a notação com ponto pode procurar um atributo ou uma chave do objeto, conforme as regras de resolução. Quando é importante deixar explícito que se deseja uma chave de dicionário, a notação com colchetes costuma ser mais clara.

### Erro: chave inexistente

Se o template tentar acessar uma chave que não existe, o resultado depende do modo de acesso e da configuração do ambiente.

Por exemplo:

```
{{ usuario["email"] }}
```

Se `email` não estiver presente, o valor poderá ser tratado como indefinido ou poderá gerar um erro em um ambiente estrito.

A solução depende da regra de negócio: forneça a chave quando ela for obrigatória ou trate a ausência quando ela for opcional.

## 6. Condições com `if`, `elif` e `else`

As condições permitem que o template apresente conteúdos diferentes conforme os valores recebidos.

Sintaxe:

```
{% if condição %}
    Conteúdo A
{% elif outra_condição %}
    Conteúdo B
{% else %}
    Conteúdo C
{% endif %}
```

Os blocos `elif` e `else` são opcionais. O `if` inicia a estrutura condicional e `endif` encerra o bloco.

### Exemplo prático

Python:

```
return render_template(
    "painel.html",
    nivel="administrador",
)
```

Jinja:

```
{% if nivel == "administrador" %}
    <h1>Painel administrativo</h1>
{% elif nivel == "moderador" %}
    <h1>Painel de moderação</h1>
{% else %}
    <h1>Painel do usuário</h1>
{% endif %}
```

O operador `==` compara dois valores para verificar se são iguais.

O Jinja avalia as condições em ordem:

1. Verifica se `nivel` é igual a `"administrador"`.
    
2. Se não for, verifica se `nivel` é igual a `"moderador"`.
    
3. Se nenhuma condição for verdadeira, renderiza o bloco `else`, caso ele exista.
    

Como o valor enviado é `"administrador"`, somente o primeiro título aparece.

### Operadores de comparação

|Operador|Significado|
|---|---|
|`==`|Igual a.|
|`!=`|Diferente de.|
|`>`|Maior que.|
|`<`|Menor que.|
|`>=`|Maior ou igual a.|
|`<=`|Menor ou igual a.|

### Operadores lógicos

```
{% if usuario.ativo and usuario.nivel == "administrador" %}
    <p>Administrador ativo.</p>
{% endif %}
```

A condição combina duas verificações:

- `usuario.ativo`: verifica o valor de `ativo`.
    
- `usuario.nivel == "administrador"`: verifica o nível do usuário.
    

O operador `and` exige que as duas condições sejam verdadeiras.

Já `or` permite que pelo menos uma condição seja verdadeira, enquanto `not` inverte o valor lógico de uma expressão.

**Limite de segurança:** mostrar ou esconder um botão no template não impede que alguém tente acessar diretamente a URL correspondente. A autorização precisa ser validada também na rota que executa a operação.

## 7. Laços com `for`

O `for` permite repetir um bloco de apresentação para cada elemento de uma sequência ou de outro objeto iterável.

Sintaxe:

```
{% for item in itens %}
    {{ item }}
{% endfor %}
```

Aqui:

- `item` é a variável que representa o elemento atual.
    
- `itens` é a coleção recebida pelo template.
    
- O bloco entre `for` e `endfor` é renderizado para cada elemento.
    

### Exemplo com uma lista de usuários

Python:

```
usuarios = [
    {"nome": "Ana", "ativo": True},
    {"nome": "Bruno", "ativo": False},
    {"nome": "Carla", "ativo": True},
]

return render_template(
    "usuarios.html",
    usuarios=usuarios,
)
```

Cada item da lista é um dicionário com duas chaves: `nome` e `ativo`.

Template:

```
<ul>
    {% for usuario in usuarios %}
        <li>
            {{ usuario["nome"] }}

            {% if usuario["ativo"] %}
                — Ativo
            {% else %}
                — Inativo
            {% endif %}
        </li>
    {% endfor %}
</ul>
```

O Jinja executa o bloco para cada dicionário. A variável `usuario` passa a representar o dicionário atual durante cada iteração.

A condição interna verifica o valor da chave `ativo` desse usuário.

Assim, Ana e Carla aparecem como ativas, enquanto Bruno aparece como inativo.

### A variável especial `loop`

Dentro de um `for`, o Jinja disponibiliza `loop`, um objeto especial com informações sobre a iteração atual.

Exemplo:

```
<ol>
    {% for tarefa in tarefas %}
        <li>
            {{ loop.index }}. {{ tarefa }}
        </li>
    {% endfor %}
</ol>
```

`loop.index` fornece a posição atual começando em 1. Isso é útil para numerar os elementos sem precisar adicionar um contador manual no Python.

Outros atributos:

|Atributo|Funcionamento|
|---|---|
|`loop.index`|Posição atual começando em 1.|
|`loop.index0`|Posição atual começando em 0.|
|`loop.first`|Indica a primeira iteração.|
|`loop.last`|Indica a última iteração.|
|`loop.length`|Quantidade de elementos, quando disponível.|
|`loop.revindex`|Posição inversa começando em 1, quando disponível.|
|`loop.revindex0`|Posição inversa começando em 0, quando disponível.|

Esses atributos pertencem ao objeto especial do Jinja. Não é necessário criá-lo no Python.

### O `else` de um laço

O Jinja permite associar um `else` a um `for`:

```
<ul>
    {% for tarefa in tarefas %}
        <li>{{ tarefa }}</li>
    {% else %}
        <li>Nenhuma tarefa cadastrada.</li>
    {% endfor %}
</ul>
```

Esse `else` é executado quando o laço não realiza nenhuma iteração, como acontece quando a lista está vazia.

Isso permite tratar a ausência de resultados sem criar uma condição separada no Python.

É importante não confundir esse comportamento com o `else` de um `if`: no `for`, ele trata a ausência de iterações.

## 8. Escapamento automático e segurança

Quando o Jinja renderiza valores em templates HTML, o escapamento automático ajuda a impedir que caracteres especiais sejam interpretados como marcação HTML.

Considere que um usuário envie o seguinte texto:

```
comentario = "<script>alert('teste')</script>"

return render_template(
    "comentario.html",
    comentario=comentario,
)
```

No template:

```
<p>{{ comentario }}</p>
```

O Jinja escapa os caracteres especiais de HTML, quando o escapamento automático está habilitado para esse template. Assim, o texto não é interpretado como uma tag `<script>` nesse contexto.

Isso reduz o risco de XSS (_Cross-Site Scripting_), uma vulnerabilidade na qual conteúdo controlado por alguém pode ser interpretado pelo navegador como código executável.

### O filtro `safe`

O filtro `safe` informa ao Jinja que o conteúdo deve ser tratado como HTML confiável:

```
{{ conteudo|safe }}
```

O filtro não sanitiza o HTML. Ele não remove automaticamente scripts ou atributos perigosos. Sua função é indicar que o conteúdo não deve passar pelo escapamento automático normal.

Se `conteudo` for controlado por um usuário e contiver HTML malicioso, usar `safe` sem sanitização apropriada pode criar uma vulnerabilidade.

Portanto, não use `safe` simplesmente porque deseja que um texto apareça formatado.

### Escapamento não substitui outras proteções

O escapamento de HTML é específico ao contexto em que o valor é inserido. Ele não substitui:

- validação dos dados;
    
- autorização no servidor;
    
- proteção CSRF;
    
- tratamento adequado de valores inseridos em JavaScript, CSS ou URLs;
    
- sanitização de HTML quando a aplicação realmente permite conteúdo HTML fornecido por usuários.
    

## 9. Filtros do Jinja

Os filtros modificam a apresentação de um valor durante a renderização.

Sua sintaxe utiliza o caractere `|`:

```
{{ valor|filtro }}
```

O valor à esquerda é enviado ao filtro. O resultado produzido pelo filtro passa a ser a saída da expressão ou pode ser utilizado por outro filtro.

### Filtros comuns

|Filtro|Função|Exemplo|
|---|---|---|
|`upper`|Converte texto para maiúsculas.|`{{ nome|upper }}`|
|`lower`|Converte texto para minúsculas.|`{{ nome|lower }}`|
|`title`|Aplica capitalização de título.|`{{ nome|title }}`|
|`capitalize`|Coloca a primeira letra em maiúscula e as demais em minúsculas.|`{{ nome|capitalize }}`|
|`trim`|Remove espaços nas extremidades.|`{{ nome|trim }}`|
|`length`|Retorna o comprimento ou a quantidade de elementos.|`{{ tarefas|length }}`|
|`default`|Fornece um valor alternativo para valores indefinidos e, opcionalmente, falsos.|`{{ apelido|default("Sem apelido") }}`|
|`join`|Junta elementos de uma sequência em uma string.|`{{ nomes|join(", ") }}`|
|`replace`|Substitui ocorrências de um texto.|`{{ texto|replace("a", "o") }}`|
|`safe`|Marca o conteúdo como confiável para renderização HTML.|`{{ conteudo|safe }}`|

### Aplicando vários filtros

Considere o valor:

```
nome = "  mArCoS  "
```

No template:

```
<p>{{ nome|trim|lower }}</p>
```

O primeiro filtro, `trim`, remove os espaços nas extremidades. O segundo, `lower`, converte o resultado para minúsculas.

O valor final é `marcos`.

A ordem importa: os filtros são aplicados da esquerda para a direita.

### O filtro `default`

Considere:

```
{{ apelido|default("Sem apelido") }}
```

O primeiro argumento de `default` é o valor a ser utilizado quando a variável não está definida.

O texto `"Sem apelido"` é uma string literal: ele será usado como alternativa, não interpretado como nome de variável.

Por padrão, uma string vazia que está definida não é necessariamente substituída. Para também substituir valores falsos, como `""`, `0` e `False`, é possível usar o segundo argumento:

```
{{ apelido|default("Sem apelido", true) }}
```

O segundo argumento é opcional. Quando definido como verdadeiro, faz o filtro também substituir valores considerados falsos.

Use esse comportamento com cuidado: zero ou `False` podem ser valores válidos para determinadas aplicações.

## 10. Testes de valores com `is`

No Jinja, `is` permite testar uma característica de um valor.

Exemplo:

```
{% if tarefas is defined %}
    <p>A variável tarefas foi fornecida.</p>
{% endif %}
```

`defined` é um teste do Jinja que verifica se a variável está definida no contexto.

Isso é diferente de verificar se a lista contém elementos. Uma variável pode estar definida e conter uma lista vazia.

### Testes comuns

|Teste|Significado|
|---|---|
|`defined`|A variável está definida.|
|`undefined`|A variável não está definida.|
|`none`|O valor é `None`.|
|`string`|O valor é uma string.|
|`number`|O valor é numérico.|
|`iterable`|O valor é iterável.|
|`mapping`|O valor é um mapeamento, como um dicionário.|
|`sequence`|O valor corresponde ao teste de sequência do Jinja.|

Exemplo:

```
{% if usuario is defined and usuario is not none %}
    <p>Os dados do usuário estão disponíveis.</p>
{% else %}
    <p>Usuário não informado.</p>
{% endif %}
```

A primeira parte verifica se `usuario` está definido. A segunda verifica se seu valor não é `None`.

O operador lógico `and` exige que ambas sejam verdadeiras.

Esses testes são úteis quando a ausência de um valor é uma situação esperada. Se a variável for obrigatória para a página, é melhor garantir que a rota forneça o valor corretamente, em vez de esconder o problema com verificações genéricas.

## 11. Erros comuns e diagnóstico

Os erros de templates geralmente aparecem no terminal onde o servidor Flask está sendo executado. O navegador pode mostrar uma página de erro HTTP, enquanto o terminal contém a exceção e o rastreamento que ajudam a localizar o problema.

### `TemplateNotFound`

**Exemplo:**

```
jinja2.exceptions.TemplateNotFound: perfil.html
```

**O que significa:** o Jinja não encontrou o template solicitado.

**Causas possíveis:**

- arquivo inexistente;
    
- nome ou extensão incorretos;
    
- caminho relativo incorreto;
    
- configuração de templates incompatível com a estrutura do projeto.
    

**Como resolver:** confira a localização do arquivo e o nome passado para `render_template()`.

### `UndefinedError`

**Exemplo:**

```
jinja2.exceptions.UndefinedError: 'usuario' is undefined
```

**O que significa:** o template tentou acessar uma variável que não está disponível, e o ambiente está configurado para tratar essa situação como erro.

**Causas possíveis:**

- a rota não enviou a variável;
    
- o nome foi escrito de forma diferente no Python e no template;
    
- uma variável que deveria existir não foi inicializada.
    

**Como resolver:** confira os argumentos de `render_template()` e os nomes utilizados no template.

### `TemplateSyntaxError`

**Exemplo de código incorreto:**

```
{% if usuario %}
    <p>Olá!</p>
```

**O que significa:** o Jinja não conseguiu interpretar a sintaxe do template.

**Causa:** o bloco `if` foi aberto, mas não foi encerrado.

**Correção:**

```
{% if usuario %}
    <p>Olá!</p>
{% endif %}
```

Confira as estruturas abertas e seus respectivos fechamentos, além da posição dos delimitadores.

### `NameError` no Python

**Exemplo:**

```
NameError: name 'render_template' is not defined
```

**O que significa:** o Python tentou utilizar um nome que não está disponível no escopo atual.

**Causa comum:** esquecer de importar a função.

**Correção:**

```
from flask import render_template
```

Esse é um erro do Python, não um erro de sintaxe do Jinja.

### Página renderizada sem dados

**Python:**

```
return render_template(
    "perfil.html",
    nome_usuario="Marcos",
)
```

**Template incorreto:**

```
<h1>{{ nome }}</h1>
```

O contexto contém `nome_usuario`, mas o template utiliza `nome`.

**Correção:**

```
<h1>{{ nome_usuario }}</h1>
```

Se a aplicação não estiver usando um ambiente estrito, o nome incorreto pode resultar em uma saída vazia em vez de gerar uma exceção. Por isso, uma página sem dados nem sempre indica que o template deixou de ser carregado.

### HTML aparecendo como texto

**Causa possível:** o escapamento automático está convertendo caracteres especiais em representações seguras.

Antes de alterar o comportamento de segurança, verifique se a aplicação realmente precisa renderizar HTML fornecido como conteúdo. Não utilize `safe` indiscriminadamente para eliminar o escapamento.

## 12. Boas práticas

- Mantenha regras de negócio e operações de banco no Python.
    
- Utilize templates para apresentação e lógica visual simples.
    
- Forneça explicitamente os dados que a página precisa.
    
- Mantenha consistência entre os nomes do contexto e os nomes utilizados no Jinja.
    
- Trate listas vazias e dados opcionais quando essas situações forem esperadas.
    
- Utilize filtros para pequenas transformações de apresentação.
    
- Aproveite o escapamento automático e evite marcar conteúdo não confiável como seguro.
    
- Não confunda esconder elementos no HTML com proteger rotas.
    
- Utilize `url_for()` para gerar URLs a partir dos endpoints Flask.
    
- Investigue o rastreamento de erros no terminal antes de alterar o código aleatoriamente.
    
- Evite expressões muito complexas nos templates; quando uma transformação exige muitas regras, considere prepará-la no Python.
    

## 13. Referência rápida da sintaxe

|Sintaxe|Finalidade|
|---|---|
|`{{ valor }}`|Avalia uma expressão e insere o resultado na saída.|
|`{% if ... %}`|Inicia uma condição.|
|`{% elif ... %}`|Adiciona uma condição alternativa.|
|`{% else %}`|Define uma alternativa para uma condição ou um laço, conforme o contexto.|
|`{% endif %}`|Encerra um bloco condicional.|
|`{% for ... in ... %}`|Percorre uma coleção.|
|`{% endfor %}`|Encerra o bloco de repetição.|
|`{# comentário #}`|Insere um comentário que não aparece na saída renderizada.|
|`{{ valor|filtro }}`|Aplica um filtro a um valor.|
|`variavel is defined`|Verifica se uma variável está definida.|

Os templates Jinja são processados no servidor para produzir a saída final. As expressões e instruções do Jinja não são, por si mesmas, executadas pelo navegador.

---
## 14. Jinja e Flask-WTF: renderizando formulários

O Jinja permite construir a apresentação de um formulário HTML, mas não é responsável por validar os dados recebidos pelo servidor. Para trabalhar com formulários de maneira estruturada no Flask, uma opção comum é utilizar a extensão **Flask-WTF**, que integra o WTForms ao Flask e oferece recursos como validação, geração de campos e proteção CSRF.

Essas tecnologias trabalham em conjunto, mas possuem responsabilidades diferentes:

- **HTML:** define os elementos do formulário, como campos de texto, senhas e botões.
    
- **Jinja:** renderiza o formulário e apresenta os dados recebidos pelo template.
    
- **Flask-WTF:** integra os formulários ao Flask e facilita a validação e a proteção CSRF.
    
- **WTForms:** fornece a estrutura dos campos, os validadores e o processamento dos dados do formulário.
    
- **Flask:** recebe a requisição HTTP e coordena o processamento na rota.
    

Essa separação permite que o Python defina a estrutura e as regras de validação, enquanto o Jinja controla como os campos e as mensagens aparecem na página.

### 14.1. Como um formulário chega ao Jinja

Em um formulário convencional, o HTML define os campos que o navegador apresenta. Quando o usuário envia o formulário, o navegador envia os dados ao servidor por meio de uma requisição HTTP.

Com Flask-WTF, a estrutura do formulário é normalmente definida em uma classe Python. A rota cria uma instância dessa classe e a disponibiliza ao template.

Considere um formulário de login.

Arquivo `forms.py`:

```
from flask_wtf import FlaskForm
from wtforms import PasswordField, StringField, SubmitField
from wtforms.validators import DataRequired, Length


class LoginForm(FlaskForm):
    usuario = StringField(
        "Usuário",
        validators=[
            DataRequired(),
            Length(min=3, max=30),
        ],
    )

    senha = PasswordField(
        "Senha",
        validators=[
            DataRequired(),
        ],
    )

    enviar = SubmitField("Entrar")
```

Essa classe define três campos: `usuario`, `senha` e `enviar`.

Alguns elementos importantes:

- `FlaskForm` é a classe-base que integra o formulário ao Flask-WTF.
    
- `StringField` representa um campo de texto.
    
- `PasswordField` representa um campo de senha.
    
- `SubmitField` representa um controle de envio do formulário.
    
- `DataRequired()` exige que o campo tenha um valor considerado válido para esse validador.
    
- `Length(min=3, max=30)` limita o comprimento do valor aceito pelo campo.
    

Os validadores são executados durante a validação do formulário. Definir um campo não significa que seus dados já foram validados.

Na rota, o formulário pode ser disponibilizado ao Jinja:

```
from flask import render_template
from .forms import LoginForm


@app.route("/login", methods=["GET", "POST"])
def login():
    form = LoginForm()

    return render_template(
        "login.html",
        form=form,
    )
```

Nesse exemplo, a rota cria uma instância de `LoginForm` e envia essa instância ao template com o nome `form`.

A rota aceita `GET` e `POST`, mas ainda não processa o envio. Essa parte será importante quando estudarmos `validate_on_submit()`.

No arquivo `templates/login.html`, o Jinja pode acessar os campos da instância:

```
<form method="post">
    {{ form.hidden_tag() }}

    <div>
        {{ form.usuario.label }}
        {{ form.usuario() }}
    </div>

    <div>
        {{ form.senha.label }}
        {{ form.senha() }}
    </div>

    {{ form.enviar() }}
</form>
```

A expressão `form.usuario` acessa o campo `usuario` da instância do formulário. Já `form.usuario()` chama esse campo para gerar sua representação HTML.

Essa diferença é importante: acessar o campo permite utilizar suas propriedades, enquanto chamá-lo normalmente gera o elemento HTML correspondente.

### 14.2. Como funciona `form.campo()`

No Jinja, um campo WTForms pode ser chamado como uma função:

```
{{ form.usuario() }}
```

Essa chamada gera um elemento HTML correspondente ao campo. Para um `StringField`, o resultado será semelhante a:

```
<input id="usuario" name="usuario" type="text" value="">
```

O HTML exato pode variar conforme o tipo de campo, seus atributos, os dados recebidos e as configurações utilizadas.

O WTForms gera atributos como `id` e `name` a partir da definição do campo. O atributo `name` é especialmente importante porque identifica o campo nos dados enviados pelo navegador.

Também é possível passar atributos HTML na chamada:

```
{{ form.usuario(class_="campo-texto", placeholder="Seu usuário") }}
```

O resultado será semelhante a:

```
<input
    class="campo-texto"
    id="usuario"
    name="usuario"
    placeholder="Seu usuário"
    type="text"
    value=""
>
```

O argumento `placeholder` define o texto de orientação que aparece no campo vazio. O argumento `class_` permite definir a classe CSS do elemento.

O sufixo `_` em `class_` existe porque `class` é uma palavra reservada da linguagem Python. O WTForms utiliza `class_` como forma de receber o atributo HTML `class`.

Não é necessário escrever manualmente o elemento `<input>` quando o campo já está definido no formulário. Entretanto, você continua podendo controlar a estrutura externa do HTML com `<div>`, `<label>`, classes CSS e outros elementos.

### 14.3. A diferença entre `form.campo.label` e `form.campo()`

Observe:

```
{{ form.usuario.label }}
{{ form.usuario() }}
```

Essas duas expressões têm funções diferentes.

`form.usuario.label` representa o rótulo associado ao campo. Como o campo foi definido com `"Usuário"`, o WTForms pode gerar:

```
<label for="usuario">Usuário</label>
```

Já `form.usuario()` gera o controle de entrada:

```
<input id="usuario" name="usuario" type="text" value="">
```

O atributo `for` do `<label>` normalmente corresponde ao `id` do campo. Essa associação permite que o usuário clique no rótulo para focar o campo correspondente e contribui para a acessibilidade do formulário.

A estrutura completa pode ficar assim:

```
<div class="form-group">
    {{ form.usuario.label }}
    {{ form.usuario(class_="form-control") }}
</div>
```

O Jinja controla onde cada elemento aparece. O WTForms fornece a representação dos campos e rótulos.

### 14.4. Enviando atributos HTML para os campos

A chamada de um campo pode receber atributos adicionais:

```
{{ form.usuario(
    class_="form-control",
    placeholder="Digite seu usuário",
    autocomplete="username",
    required=True
) }}
```

Os argumentos correspondem a atributos HTML que o campo deverá utilizar.

|Argumento|Finalidade|
|---|---|
|`class_`|Define a classe CSS do elemento.|
|`placeholder`|Apresenta uma orientação dentro do campo.|
|`autocomplete`|Orienta o navegador sobre o preenchimento automático.|
|`required=True`|Adiciona o atributo HTML `required`.|

O atributo HTML `required` ajuda o navegador a verificar se o campo foi preenchido antes de enviar o formulário. Entretanto, ele não substitui a validação no servidor.

Uma requisição pode ser enviada sem passar pelas validações do navegador. Por isso, os validadores WTForms continuam necessários.

Os atributos também podem ser definidos diretamente na classe Python, dependendo da configuração desejada. A chamada no template é útil quando a apresentação precisa variar entre páginas ou componentes.

### 14.5. O método `form.hidden_tag()` e os campos ocultos

O método:

```
{{ form.hidden_tag() }}
```

renderiza os campos ocultos do formulário, incluindo o campo CSRF quando a proteção está habilitada e configurada.

Um campo oculto é um elemento HTML que não aparece visualmente como um campo convencional:

```
<input type="hidden" name="csrf_token" value="...">
```

O valor real do token é gerado pelo mecanismo de proteção; não deve ser escrito manualmente como uma string fixa.

A função de `hidden_tag()` não é esconder os erros nem validar todos os campos. Ela facilita a inclusão dos campos ocultos que pertencem ao formulário.

Em formulários Flask-WTF com proteção CSRF habilitada, incluir `form.hidden_tag()` é uma maneira comum de garantir que o token seja enviado junto com os demais dados.

Exemplo:

```
<form method="post">
    {{ form.hidden_tag() }}

    {{ form.usuario.label }}
    {{ form.usuario() }}

    {{ form.senha.label }}
    {{ form.senha() }}

    {{ form.enviar() }}
</form>
```

A inclusão do token no formulário é apenas parte da proteção. O servidor também precisa validar esse token ao receber a requisição.

### 14.6. Exibindo erros de validação

Um formulário pode ser renderizado corretamente e, ainda assim, conter dados inválidos. Por exemplo, o usuário pode enviar um campo vazio ou um nome com comprimento inferior ao permitido.

O WTForms disponibiliza os erros associados a cada campo por meio de `field.errors`.

Considere:

```
{{ form.usuario.label }}
{{ form.usuario() }}

{% for erro in form.usuario.errors %}
    <p class="erro">{{ erro }}</p>
{% endfor %}
```

O funcionamento é o seguinte:

1. `form.usuario` acessa o campo de usuário.
    
2. `form.usuario.errors` fornece a coleção de mensagens de erro associadas ao campo.
    
3. O `for` percorre as mensagens existentes.
    
4. `{{ erro }}` apresenta cada mensagem no HTML renderizado.
    

Se houver dois erros, por exemplo, o laço poderá produzir dois parágrafos. Se não houver erros, o laço não terá iterações e nenhum parágrafo de erro será gerado.

A coleção `errors` é fornecida pelo WTForms. Você não precisa criar manualmente uma lista de erros no template.

### 14.7. Exibindo os erros de todos os campos

Quando um formulário contém vários campos, pode ser útil apresentar as mensagens logo abaixo de cada um.

```
<form method="post">
    {{ form.hidden_tag() }}

    <div class="form-group">
        {{ form.usuario.label }}
        {{ form.usuario() }}

        {% for erro in form.usuario.errors %}
            <p class="erro">{{ erro }}</p>
        {% endfor %}
    </div>

    <div class="form-group">
        {{ form.senha.label }}
        {{ form.senha() }}

        {% for erro in form.senha.errors %}
            <p class="erro">{{ erro }}</p>
        {% endfor %}
    </div>

    {{ form.enviar() }}
</form>
```

Cada campo possui sua própria coleção de erros. Por isso, `form.usuario.errors` e `form.senha.errors` não representam a mesma coisa.

Essa abordagem oferece controle explícito sobre a posição das mensagens, mas pode gerar repetição quando muitos formulários utilizam o mesmo padrão visual.

Uma alternativa é criar um componente Jinja reutilizável para renderizar um campo e seus erros. A herança, os blocos e a inclusão de templates são recursos que permitem organizar esse tipo de estrutura.

### 14.8. Entendendo `form.validate_on_submit()`

O método `validate_on_submit()` é fornecido pelo Flask-WTF. Ele verifica se a requisição atual corresponde a um envio de formulário que deve ser validado e executa a validação.

Uma rota pode utilizar:

```
@app.route("/login", methods=["GET", "POST"])
def login():
    form = LoginForm()

    if form.validate_on_submit():
        # Os dados passaram pela validação.
        pass

    return render_template("login.html", form=form)
```

No fluxo habitual:

- Em uma requisição `GET`, a página é exibida com o formulário.
    
- Em uma requisição `POST`, o método processa os dados enviados e executa a validação.
    
- Se a validação falhar, o método retorna `False`, e os erros ficam disponíveis nos campos.
    
- Se a validação passar, o método retorna `True`, permitindo que a rota prossiga com o processamento dos dados.
    

O método não significa que o usuário está autenticado, que o cadastro foi salvo ou que a operação está autorizada. Ele indica que o formulário passou pelas validações configuradas.

A lógica posterior ainda precisa realizar a operação correspondente, como consultar as credenciais no banco, criar um registro ou verificar permissões.

### 14.9. Diferença entre validação no navegador e no servidor

Considere um campo com:

```
{{ form.usuario(required=True) }}
```

O atributo `required` é interpretado pelo navegador. Ele pode impedir o envio normal do formulário enquanto o campo estiver vazio.

Por outro lado, um validador Python como:

```
DataRequired()
```

é executado durante a validação do WTForms no servidor.

A distinção é importante:

|Validação|Onde ocorre|Objetivo|
|---|---|---|
|Atributos HTML, como `required`|Navegador|Melhorar a experiência de preenchimento.|
|Validadores WTForms|Servidor|Verificar os dados recebidos pela aplicação.|
|Regras de negócio|Aplicação Python|Verificar condições específicas da operação.|

Uma aplicação segura não deve depender exclusivamente das verificações realizadas pelo navegador, pois elas podem ser contornadas.

### 14.10. Proteção CSRF nos formulários

CSRF (_Cross-Site Request Forgery_) é um tipo de ataque em que um navegador autenticado é induzido a enviar uma requisição indesejada para uma aplicação na qual o usuário já possui uma sessão válida.

O Flask-WTF oferece integração com proteção CSRF para formulários. Em uma configuração usual, o formulário contém um token que o servidor verifica quando recebe a requisição.

Para usar essa proteção corretamente, é necessário configurar uma chave secreta para a aplicação e manter a proteção habilitada.

Exemplo de configuração:

```
app.config["SECRET_KEY"] = "valor-secreto-de-exemplo"
```

O valor apresentado é apenas ilustrativo. Em uma aplicação real, utilize um segredo forte, privado e configurado por um mecanismo apropriado, como uma variável de ambiente. Não publique a chave real no código versionado.

No template:

```
<form method="post">
    {{ form.hidden_tag() }}

    <!-- Campos do formulário -->
</form>
```

O `hidden_tag()` inclui o token CSRF do formulário quando a proteção está habilitada.

**Importante:** CSRF e validação de campos são proteções diferentes. Um formulário pode conter campos válidos e, ainda assim, apresentar um token CSRF ausente ou inválido. Da mesma forma, um token válido não significa que os dados enviados são corretos ou que a operação está autorizada.

### 14.11. Erros comuns com formulários Flask-WTF

**Erro:** `**form**` **não está definido**

Se o template contém:

```
{{ form.usuario() }}
```

mas a rota não disponibilizou uma variável chamada `form`, o template poderá produzir uma variável indefinida ou um erro, dependendo da configuração do Jinja.

Verifique se a rota criou o formulário e o enviou ao template:

```
form = LoginForm()
return render_template("login.html", form=form)
```

**Erro: campo não aparece como esperado**

Verifique se está acessando o campo correto e se a chamada inclui os atributos desejados:

```
{{ form.usuario() }}
```

Acessar `form.usuario.label` apresenta o rótulo, enquanto chamar `form.usuario()` gera o controle HTML.

**Erros de validação não aparecem**

Confira se o template percorre `form.campo.errors` e se a rota executa a validação antes de renderizar novamente o formulário.

```
{% for erro in form.usuario.errors %}
    <p>{{ erro }}</p>
{% endfor %}
```

**Erro de CSRF**

Se o servidor rejeitar a requisição por causa do token CSRF, confira se o formulário inclui `form.hidden_tag()`, se a proteção está configurada corretamente e se o formulário foi processado no fluxo esperado.

Não desabilite a proteção automaticamente para eliminar o erro. Primeiro identifique por que o token não está sendo enviado ou validado.

**O formulário aceita dados inesperados**

Confira os validadores definidos na classe Python. A apresentação de um campo no template não determina, por si só, quais valores o servidor deve aceitar.

Também verifique as regras de negócio que precisam ser aplicadas após a validação do WTForms.

### 14.12. Boas práticas ao integrar Jinja e Flask-WTF

- Defina os campos e validadores na classe Python, em vez de concentrar todas as regras no HTML.
    
- Utilize `form.campo.label` para apresentar rótulos associados aos controles.
    
- Utilize `form.campo()` para renderizar os elementos correspondentes.
    
- Inclua `form.hidden_tag()` em formulários que utilizam a proteção CSRF do Flask-WTF.
    
- Percorra `form.campo.errors` para apresentar mensagens de validação.
    
- Utilize `validate_on_submit()` no fluxo de processamento de formulários quando apropriado.
    
- Não confunda validação bem-sucedida com autenticação, autorização ou persistência no banco.
    
- Trate os dados recebidos no servidor, mesmo quando o HTML utiliza atributos como `required`.
    
- Mantenha segredos fora do código público e configure corretamente a chave da aplicação.
    
- Reutilize componentes Jinja quando vários formulários compartilharem a mesma estrutura visual.

---
## 15. Componentes reutilizáveis com Jinja: `include`, macros e parâmetros

Em aplicações Flask, várias páginas compartilham elementos visuais. Um sistema pode ter o mesmo menu de navegação, cabeçalho, rodapé, mensagens de alerta e estrutura de formulários em diferentes telas.

Copiar o mesmo HTML para cada template funciona inicialmente, mas gera um problema de manutenção: quando um componente precisa ser alterado, é necessário localizar e modificar todas as cópias.

O Jinja oferece mecanismos para reutilizar partes dos templates. Entre os principais estão:

- `include`: incorpora o conteúdo renderizado de outro template.
    
- `macro`: define uma função reutilizável que produz conteúdo de template.
    
- Parâmetros: permitem fornecer valores a macros para que o mesmo componente seja utilizado em contextos diferentes.
    

Esses mecanismos têm finalidades relacionadas, mas não são equivalentes.

### 15.1. A instrução `include`

A instrução `include` permite incorporar outro template no ponto em que ela aparece.

Sintaxe:

```
{% include "caminho/do_template.html" %}
```

O caminho é relativo ao diretório de templates configurado no Flask, seguindo o mesmo princípio utilizado por `render_template()`.

Considere esta estrutura:

```
projeto/
├── app.py
└── templates/
    ├── base.html
    ├── painel.html
    └── components/
        ├── navbar.html
        └── footer.html
```

O arquivo `components/navbar.html` pode conter a navegação:

```
<nav>
    <a href="/">Início</a>
    <a href="/perfil">Perfil</a>
    <a href="/sobre">Sobre</a>
</nav>
```

Em `painel.html`, podemos incorporar esse componente:

```
<!doctype html>
<html lang="pt-BR">
<head>
    <meta charset="utf-8">
    <title>Painel</title>
</head>
<body>
    {% include "components/navbar.html" %}

    <main>
        <h1>Painel do usuário</h1>
        <p>Bem-vindo ao sistema.</p>
    </main>

    {% include "components/footer.html" %}
</body>
</html>
```

Quando o Jinja renderiza `painel.html`, ele também renderiza os templates incluídos e insere seus resultados na saída.

O navegador recebe um documento HTML final. Ele não precisa conhecer a existência do `include`.

### 15.2. O contexto disponível em um template incluído

Por padrão, um template incluído tem acesso ao contexto disponível no ponto da inclusão.

Considere a rota:

```
@app.route("/perfil")
def perfil():
    return render_template(
        "perfil.html",
        nome="Marcos",
    )
```

O arquivo `components/saudacao.html` contém:

```
<p>Olá, {{ nome }}!</p>
```

O template principal pode incluir esse componente:

```
<h1>Perfil</h1>

{% include "components/saudacao.html" %}
```

Como `nome` faz parte do contexto da renderização, o template incluído pode utilizá-lo. O resultado será:

```
<h1>Perfil</h1>
<p>Olá, Marcos!</p>
```

Isso permite criar componentes que dependem de informações fornecidas pela rota sem precisar passar individualmente cada variável na instrução `include`.

Entretanto, essa dependência do contexto pode dificultar a compreensão de um componente quando ele utiliza muitas variáveis sem deixar claro quais são necessárias.

Quando a intenção é criar um componente com entradas explícitas e reutilizáveis, uma macro pode ser uma escolha melhor.

### 15.3. A opção `with context` e `without context`

O Jinja permite controlar se o template incluído recebe o contexto da renderização.

Exemplo:

```
{% include "components/saudacao.html" with context %}
```

A opção `with context` explicita que o contexto deve estar disponível para o template incluído. Esse é o comportamento padrão de `include`.

Também existe:

```
{% include "components/saudacao.html" without context %}
```

Nesse caso, o template incluído não recebe o contexto atual.

Isso significa que variáveis fornecidas pela rota, como `nome`, não estarão disponíveis nele apenas por terem sido passadas ao template principal.

Use `without context` quando a separação do contexto for intencional e o template puder funcionar sem os dados da página que o inclui.

### 15.4. O comportamento de `ignore missing`

Normalmente, se o Jinja não encontrar o template especificado em `include`, a renderização falhará com um erro semelhante a `TemplateNotFound`.

Quando a ausência do componente é aceitável, pode-se utilizar `ignore missing`:

```
{% include "components/banner.html" ignore missing %}
```

Se `banner.html` não existir, o Jinja não interromperá a renderização apenas por causa dessa ausência.

Essa opção também pode ser combinada com `with context` ou `without context`:

```
{% include "components/banner.html" ignore missing with context %}
```

Use essa possibilidade somente quando o componente for realmente opcional. Se um template obrigatório desaparecer por engano, ignorar a ausência pode esconder um problema de organização do projeto.

O modificador `ignore missing` não substitui a verificação dos caminhos de templates nem resolve erros de sintaxe dentro de um arquivo que foi encontrado.

### 15.5. Incluindo um template condicionalmente

É possível controlar a inclusão usando uma condição:

```
{% if mostrar_banner %}
    {% include "components/banner.html" %}
{% endif %}
```

Nesse caso, o Jinja avalia `mostrar_banner`. Se a condição for verdadeira, incorpora o componente. Caso contrário, não o inclui.

A variável precisa estar disponível no contexto. Por exemplo:

```
return render_template(
    "painel.html",
    mostrar_banner=True,
)
```

Essa condição controla a apresentação. Ela não deve ser utilizada como mecanismo de autorização para operações protegidas.

Se um botão de administração estiver escondido, por exemplo, a rota correspondente ainda precisa verificar se o usuário tem permissão para executar a operação.

### 15.6. O que são macros?

Uma macro é um recurso do Jinja que permite definir um bloco reutilizável com parâmetros.

Ela se parece conceitualmente com uma função: recebe argumentos e produz uma saída de template.

Sintaxe básica:

```
{% macro nome_do_componente(parametro) %}
    Conteúdo do componente
{% endmacro %}
```

A macro é definida no template e pode ser chamada em outros pontos após estar disponível no contexto da renderização.

Por exemplo, o arquivo `components/macros.html` pode conter:

```
{% macro saudacao(nome) %}
    <p>Olá, {{ nome }}!</p>
{% endmacro %}
```

A macro recebe um parâmetro chamado `nome`. Dentro de seu corpo, o Jinja utiliza esse valor para produzir o parágrafo.

Para reutilizá-la em `perfil.html`:

```
{% from "components/macros.html" import saudacao %}

<h1>Perfil</h1>

{{ saudacao("Marcos") }}
{{ saudacao("Ana") }}
```

O resultado contém duas saudações:

```
<h1>Perfil</h1>
<p>Olá, Marcos!</p>
<p>Olá, Ana!</p>
```

Observe a diferença em relação a `include`: a macro recebe valores explícitos por meio dos argumentos da chamada. Isso facilita a reutilização de um componente em situações diferentes.

### 15.7. Importando macros com `from`

A instrução:

```
{% from "components/macros.html" import saudacao %}
```

importa a macro `saudacao` para que ela possa ser chamada diretamente:

```
{{ saudacao("Marcos") }}
```

A expressão `"components/macros.html"` indica o arquivo que contém a definição. O nome `saudacao` identifica o recurso que será importado.

Se o arquivo tiver várias macros, é possível importar mais de uma:

```
{% from "components/macros.html" import saudacao, botao %}
```

A partir daí, ambas podem ser chamadas no template, desde que estejam definidas no arquivo importado.

Também é possível importar todas as macros públicas de um arquivo para um namespace:

```
{% import "components/macros.html" as componentes %}
```

Nesse caso, a chamada precisa utilizar o nome do namespace:

```
{{ componentes.saudacao("Marcos") }}
```

O namespace ajuda a evitar conflitos entre nomes de macros, principalmente quando um projeto possui muitos componentes.

### 15.8. Criando uma macro com vários parâmetros

Uma macro pode receber vários argumentos:

```
{% macro botao(texto, url, classe="botao") %}
    <a href="{{ url }}" class="{{ classe }}">
        {{ texto }}
    </a>
{% endmacro %}
```

Essa macro recebe três parâmetros:

|Parâmetro|Obrigatório na chamada?|Finalidade|
|---|---|---|
|`texto`|Sim|Conteúdo textual do link.|
|`url`|Sim|Destino do link.|
|`classe`|Não|Classe CSS; utiliza `"botao"` quando omitida.|

O parâmetro `classe` tem um valor padrão. Por isso, a chamada pode fornecer somente os dois primeiros argumentos:

```
{{ componentes.botao("Acessar", "/painel") }}
```

Ou pode especificar uma classe diferente:

```
{{ componentes.botao("Excluir", "/excluir", "botao-perigo") }}
```

O valor padrão é utilizado quando a chamada não fornece um argumento para aquele parâmetro.

Esse mecanismo é útil para componentes que compartilham a mesma estrutura, mas precisam variar em detalhes como texto, destino ou estilo.

**Atenção à segurança:** os valores inseridos em atributos HTML precisam ser tratados de acordo com o contexto. O escapamento automático do Jinja ajuda a proteger valores inseridos em HTML, mas não substitui validação de URLs, controle de acesso ou outras medidas específicas. Não utilize `safe` para contornar o escapamento de conteúdo não confiável.

### 15.9. Macros para campos de formulário

Uma aplicação pode ter vários formulários com o mesmo padrão visual: rótulo, campo e mensagens de erro.

Em vez de repetir o HTML, podemos definir uma macro que receba um campo WTForms.

Arquivo `templates/components/forms.html`:

```
{% macro render_field(field) %}
    <div class="form-group">
        {{ field.label }}
        {{ field() }}

        {% for erro in field.errors %}
            <p class="erro">{{ erro }}</p>
        {% endfor %}
    </div>
{% endmacro %}
```

O parâmetro `field` representa o campo recebido na chamada.

Dentro da macro:

- `field.label` apresenta o rótulo.
    
- `field()` renderiza o controle HTML.
    
- `field.errors` fornece as mensagens de validação.
    
- O `for` apresenta cada mensagem existente.
    

No template de login:

```
{% from "components/forms.html" import render_field %}

<form method="post">
    {{ form.hidden_tag() }}

    {{ render_field(form.usuario) }}
    {{ render_field(form.senha) }}

    {{ form.enviar() }}
</form>
```

A macro recebe primeiro o campo de usuário e depois o campo de senha. Em cada chamada, o parâmetro `field` representa o campo correspondente.

Assim, o mesmo componente pode ser utilizado em vários formulários, mantendo a apresentação consistente.

A macro não cria o campo WTForms e não executa sozinha a validação. Ela apenas recebe o campo já definido no Python e controla como ele será apresentado.

### 15.10. Diferença entre `include`, macros e herança

Os três recursos ajudam a evitar repetição, mas resolvem problemas diferentes.

|Recurso|Uso principal|Exemplo|
|---|---|---|
|`include`|Incorporar um template reutilizável.|Menu, rodapé ou banner.|
|`macro`|Criar um componente parametrizado que produz conteúdo.|Botão, campo de formulário ou mensagem.|
|Herança (`extends`)|Definir uma estrutura-base com áreas que os templates filhos podem substituir.|Layout comum de várias páginas.|

Um arquivo incluído normalmente contém um trecho de apresentação que será incorporado ao template atual.

Uma macro é mais apropriada quando você deseja definir uma interface explícita de parâmetros e chamar o mesmo componente com valores diferentes.

A herança, por sua vez, permite que um template filho aproveite a estrutura de outro template e substitua blocos definidos pelo template-base.

Esses recursos podem ser combinados no mesmo projeto. Por exemplo, uma página pode herdar de `base.html`, incluir um menu e chamar macros para renderizar os campos de um formulário.

### 15.11. Organização recomendada dos componentes

Uma estrutura possível é:

```
templates/
├── base.html
├── painel.html
├── login.html
└── components/
    ├── navbar.html
    ├── footer.html
    ├── banner.html
    └── forms.html
```

Nesse exemplo:

- `base.html` define a estrutura geral da página.
    
- `navbar.html` contém a navegação.
    
- `footer.html` contém o rodapé.
    
- `banner.html` contém um componente de apresentação opcional.
    
- `forms.html` reúne macros relacionadas a formulários.
    

A organização exata depende do tamanho do projeto. Não é necessário criar um arquivo para cada pequeno trecho de HTML. A separação deve facilitar a manutenção, sem tornar a estrutura excessivamente fragmentada.

### 15.12. Erros comuns ao reutilizar componentes

`**TemplateNotFound**` **em um** `**include**`

Confira se o caminho informado corresponde à localização do template dentro do diretório configurado.

```
{% include "components/navbar.html" %}
```

**Macro indefinida**

Se o template chama:

```
{{ render_field(form.usuario) }}
```

mas não importou `render_field`, o nome pode não estar disponível.

Confira a importação:

```
{% from "components/forms.html" import render_field %}
```

**Quantidade incorreta de argumentos**

Uma chamada que não fornece um parâmetro obrigatório pode gerar um erro de argumentos da macro.

Confira a definição e a chamada. Parâmetros com valores padrão podem ser omitidos; os demais precisam receber os valores esperados.

**Variável ausente no template incluído**

Se um template incluído depende de uma variável do contexto, verifique se ela foi fornecida e se a inclusão não utiliza `without context`.

**Componente aparece, mas os dados não são exibidos**

Confira os parâmetros passados à macro e os nomes utilizados em seu corpo. O Jinja não adivinha qual variável deve ser associada a cada parâmetro.

### 15.13. Boas práticas para componentes Jinja

- Utilize `include` para incorporar trechos de templates que precisam ser reutilizados.
    
- Prefira macros quando o componente tiver parâmetros explícitos.
    
- Utilize namespaces ao importar conjuntos de macros com nomes potencialmente conflitantes.
    
- Mantenha a estrutura geral da página separada dos componentes menores.
    
- Evite dependências implícitas excessivas do contexto nos componentes reutilizáveis.
    
- Não utilize `ignore missing` para esconder a ausência de templates obrigatórios.
    
- Preserve o escapamento automático e trate valores de acordo com o contexto em que serão inseridos.
    
- Mantenha validação, autorização e regras de negócio no servidor, sem depender apenas da apresentação do template.

---
## 16. Macros avançadas: argumentos, atributos HTML e componentes configuráveis

Na seção anterior, vimos como as macros permitem reutilizar componentes Jinja, como botões, campos de formulário e mensagens. Agora vamos aprofundar o funcionamento dos parâmetros e entender como construir componentes mais flexíveis sem duplicar templates.

Uma macro pode receber argumentos obrigatórios, argumentos com valores padrão e coleções de argumentos. Também pode gerar atributos HTML de maneira dinâmica.

Esses recursos são úteis quando vários componentes possuem a mesma estrutura, mas precisam variar em detalhes como texto, classes CSS, atributos de acessibilidade e comportamento visual.

### 16.1. Parâmetros obrigatórios e valores padrão

Considere esta macro:

```
{% macro botao(texto, classe="botao", tipo="button") %}
    <button type="{{ tipo }}" class="{{ classe }}">
        {{ texto }}
    </button>
{% endmacro %}
```

Ela recebe três parâmetros:

|Parâmetro|Obrigatório?|Valor padrão|Finalidade|
|---|---|---|---|
|`texto`|Sim|Nenhum|Conteúdo textual do botão.|
|`classe`|Não|`"botao"`|Classe CSS do botão.|
|`tipo`|Não|`"button"`|Tipo do elemento HTML.|

A macro pode ser chamada assim:

```
{{ botao("Abrir painel") }}
```

Como somente `texto` foi informado, os outros parâmetros utilizam seus valores padrão.

A saída será semelhante a:

```
<button type="button" class="botao">
    Abrir painel
</button>
```

Também é possível alterar os parâmetros opcionais:

```
{{ botao("Salvar", "botao-primario", "submit") }}
```

Nesse caso, a macro recebe os três valores na ordem em que os parâmetros foram definidos.

### 16.2. Argumentos nomeados em macros

Além de fornecer argumentos pela posição, você pode indicar o nome de cada parâmetro:

```
{{ botao(
    texto="Salvar alterações",
    tipo="submit",
    classe="botao-primario"
) }}
```

Essa forma é útil quando uma macro possui vários parâmetros opcionais. Ela torna a chamada mais legível e reduz a dependência da ordem dos argumentos.

O nome utilizado na chamada precisa corresponder a um parâmetro aceito pela macro.

Por exemplo, `classe` não é automaticamente equivalente a `class`. Se a macro define um parâmetro chamado `classe`, a chamada deve utilizar esse nome.

### 16.3. Parâmetros variáveis com `varargs`

Em macros Jinja, `varargs` permite acessar argumentos posicionais adicionais que não foram associados aos parâmetros explicitamente declarados.

Exemplo:

```
{% macro listar_titulos(principal, secundario) %}
    <h1>{{ principal }}</h1>
    <h2>{{ secundario }}</h2>

    {% for titulo in varargs %}
        <h3>{{ titulo }}</h3>
    {% endfor %}
{% endmacro %}
```

Chamada:

```
{{ listar_titulos(
    "Painel",
    "Configurações",
    "Perfil",
    "Segurança",
    "Preferências"
) }}
```

Os dois primeiros argumentos são associados aos parâmetros `principal` e `secundario`. Os argumentos posicionais restantes ficam disponíveis em `varargs`.

Assim, o laço percorre os títulos adicionais.

Esse recurso pode ser útil quando a quantidade de argumentos posicionais varia. Entretanto, em componentes de interface, parâmetros explícitos costumam ser mais fáceis de compreender e manter.

### 16.4. Parâmetros variáveis nomeados com `kwargs`

O recurso `kwargs` disponibiliza argumentos nomeados adicionais que não correspondem aos parâmetros declarados na macro.

Considere:

```
{% macro campo_simples(nome, rotulo) %}
    <label for="{{ nome }}">{{ rotulo }}</label>
    <input
        id="{{ nome }}"
        name="{{ nome }}"
        {% for atributo, valor in kwargs.items() %}
            {{ atributo }}="{{ valor }}"
        {% endfor %}
    >
{% endmacro %}
```

A chamada pode incluir atributos adicionais:

```
{{ campo_simples(
    "email",
    "E-mail",
    type="email",
    placeholder="voce@exemplo.com",
    autocomplete="email"
) }}
```

Os parâmetros `nome` e `rotulo` são declarados explicitamente. Os argumentos `type`, `placeholder` e `autocomplete` são adicionais e ficam disponíveis por meio de `kwargs`.

O método `items()` de um dicionário permite percorrer os pares de chave e valor. Nesse contexto, cada par representa um atributo e seu valor.

**Atenção:** esse exemplo demonstra o mecanismo, mas não deve ser utilizado sem cuidados em produção. Nomes e valores de atributos precisam ser controlados, e valores não confiáveis não devem ser inseridos indiscriminadamente em atributos HTML. Para campos WTForms, normalmente é preferível utilizar os mecanismos próprios de renderização dos campos.

### 16.5. O problema de gerar atributos HTML manualmente

Ao criar componentes configuráveis, pode parecer conveniente gerar qualquer atributo recebido:

```
<input {{ atributo }}="{{ valor }}">
```

Entretanto, essa abordagem exige cuidado. Um atributo HTML não é apenas texto decorativo: dependendo do nome e do valor, ele pode alterar o comportamento do elemento.

Por isso, componentes de produção devem definir quais atributos aceitam ou utilizar recursos de renderização que tratem os atributos de maneira apropriada.

Por exemplo, uma macro que aceita somente uma classe CSS e um texto tem uma interface mais restrita:

```
{% macro mensagem(texto, classe="info") %}
    <p class="{{ classe }}">{{ texto }}</p>
{% endmacro %}
```

Essa restrição torna o componente mais previsível e fácil de manter.

### 16.6. Argumentos posicionais versus nomeados

Considere:

```
{% macro alerta(titulo, mensagem, classe="alerta-info") %}
    <div class="{{ classe }}">
        <strong>{{ titulo }}</strong>
        <p>{{ mensagem }}</p>
    </div>
{% endmacro %}
```

Uma chamada posicional seria:

```
{{ alerta("Atenção", "Confira os dados", "alerta-aviso") }}
```

Uma chamada com argumentos nomeados seria:

```
{{ alerta(
    titulo="Atenção",
    mensagem="Confira os dados",
    classe="alerta-aviso"
) }}
```

As duas formas fornecem os mesmos valores.

A forma posicional é compacta, enquanto a forma nomeada pode facilitar a leitura em chamadas maiores.

Quando a macro possui vários argumentos opcionais, prefira uma chamada que deixe explícito o significado dos valores.

### 16.7. Utilizando macros para mensagens de alerta

Uma aplicação pode exibir mensagens de tipos diferentes: informação, sucesso, aviso e erro.

Uma macro pode centralizar a estrutura HTML:

```
{% macro alerta(titulo, mensagem, tipo="info") %}
    <section class="alerta alerta-{{ tipo }}">
        <h2>{{ titulo }}</h2>
        <p>{{ mensagem }}</p>
    </section>
{% endmacro %}
```

Depois de importar a macro:

```
{{ componentes.alerta(
    "Operação concluída",
    "Os dados foram salvos.",
    "sucesso"
) }}
```

A classe final será `alerta alerta-sucesso`.

A macro controla a estrutura, enquanto o argumento `tipo` permite variar a classe CSS.

A classe, por si só, não cria o estilo visual. É necessário que o CSS da aplicação defina as regras correspondentes.

Também é importante não utilizar essa classe como única indicação de significado. Para mensagens de erro, por exemplo, o texto deve explicar o problema, e a interface deve manter uma apresentação compreensível para pessoas que não distinguem cores.

### 16.8. Macros não são funções Python comuns

Embora uma macro se pareça com uma função, ela pertence à linguagem de templates do Jinja.

Uma macro é declarada no template:

```
{% macro saudacao(nome) %}
    <p>Olá, {{ nome }}!</p>
{% endmacro %}
```

Ela é chamada durante a renderização:

```
{{ saudacao("Marcos") }}
```

Isso não significa que o Python possa importar diretamente essa definição como se fosse uma função comum de um módulo `.py`.

No Flask, o Python prepara os dados e solicita a renderização. O Jinja interpreta a macro dentro do ambiente de templates e produz a saída correspondente.

Quando uma operação exige acesso ao banco de dados, regras de negócio ou processamento complexo, ela deve permanecer na camada Python apropriada.

### 16.9. Escolhendo a melhor estratégia para um componente

Antes de criar uma macro, avalie a necessidade de reutilização.

- Se deseja incorporar um trecho HTML completo, `include` pode ser suficiente.
    
- Se deseja gerar um componente com parâmetros explícitos, uma macro costuma ser adequada.
    
- Se deseja compartilhar uma estrutura de página e permitir que páginas filhas substituam áreas específicas, utilize herança com `extends` e `block`.
    
- Se o componente exige processamento complexo, prepare os dados no Python e mantenha o template focado na apresentação.
    

A melhor solução é aquela que reduz repetição sem tornar o comportamento difícil de entender.

### 16.10. Cuidados com segurança e manutenção

- Não trate argumentos recebidos de usuários como HTML confiável.
    
- Não aplique `safe` indiscriminadamente aos resultados das macros.
    
- Evite permitir atributos HTML arbitrários sem uma lista de valores aceitos.
    
- Prefira nomes de parâmetros claros e consistentes.
    
- Utilize valores padrão apenas quando eles representarem um comportamento realmente desejado.
    
- Evite macros excessivamente genéricas que tentam resolver muitos problemas diferentes.
    
- Mantenha operações de banco, autorização e regras de negócio fora das macros.
    
- Lembre-se de que gerar um botão ou link não garante que a ação correspondente esteja autorizada no servidor.
---
