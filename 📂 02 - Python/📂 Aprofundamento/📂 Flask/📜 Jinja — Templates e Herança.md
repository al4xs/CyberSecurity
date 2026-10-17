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
## 17. Contexto, escopo e resolução de variáveis no Jinja

O Jinja não acessa automaticamente todas as variáveis existentes no programa Python. Para que um template utilize determinado valor, esse valor precisa estar disponível no contexto de renderização ou por meio de recursos configurados no ambiente Jinja.

Entender como esse contexto funciona ajuda a organizar templates, evitar dependências ocultas e identificar por que uma variável está disponível em uma página, mas não em outra.

### 17.1. O que é o contexto de renderização?

O **contexto** é o conjunto de nomes e valores que o Jinja pode consultar durante a renderização de um template.

No Flask, a maneira mais comum de fornecer dados ao template é por meio de `render_template()`.

```
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def index():
    usuario = {
        "nome": "Marcos",
        "nivel": "iniciante"
    }

    return render_template(
        "index.html",
        usuario=usuario,
        titulo="Página inicial"
    )
```

No template `templates/index.html`:

```
<h1>{{ titulo }}</h1>

<p>Usuário: {{ usuario.nome }}</p>
<p>Nível: {{ usuario.nivel }}</p>
```

Nesse exemplo, `titulo` e `usuario` são nomes disponibilizados no contexto. O Jinja utiliza esses nomes para localizar os valores correspondentes.

A variável local `usuario` da função Python não seria automaticamente acessível ao template se não fosse passada ou disponibilizada por outro mecanismo.

### 17.2. Passando dados por meio de um dicionário

Também é possível reunir os dados em um dicionário e expandi-lo como argumentos nomeados usando `**`.

```
dados = {
    "titulo": "Painel",
    "nome": "Marcos",
    "notificacoes": 3
}

return render_template("painel.html", **dados)
```

O template recebe os nomes como variáveis independentes:

```
<h1>{{ titulo }}</h1>
<p>Olá, {{ nome }}.</p>
<p>Notificações: {{ notificacoes }}</p>
```

A expressão Python `**dados` expande as chaves do dicionário como argumentos nomeados. Nesse caso, equivale conceitualmente a escrever:

```
return render_template(
    "painel.html",
    titulo="Painel",
    nome="Marcos",
    notificacoes=3
)
```

As chaves utilizadas como argumentos precisam ser nomes válidos para argumentos nomeados da função. O dicionário não é automaticamente transformado em uma variável chamada `dados`: suas entradas são disponibilizadas individualmente.

Se você quiser acessar os valores por meio de um único objeto, passe o dicionário como um argumento:

```
return render_template("painel.html", dados=dados)
```

Então, no template:

```
<h1>{{ dados.titulo }}</h1>
<p>{{ dados.nome }}</p>
```

### 17.3. Como o Jinja resolve variáveis e atributos

O Jinja oferece duas formas comuns de acessar valores dentro de estruturas compostas:

- Notação com ponto: `objeto.atributo`
    
- Notação com colchetes: `objeto["chave"]`
    

Embora sejam parecidas, essas formas têm regras de resolução diferentes.

Considere:

```
dados = {
    "nome": "Marcos",
    "nivel": 3
}
```

No template:

```
<p>{{ dados.nome }}</p>
<p>{{ dados["nome"] }}</p>
<p>{{ dados["nivel"] }}</p>
```

As três expressões permitem acessar os valores desse dicionário.

A diferença fica importante quando um dicionário contém uma chave com o mesmo nome de um atributo ou método do objeto.

|Expressão|Ordem de resolução|
|---|---|
|`objeto.nome`|Primeiro tenta encontrar um atributo; depois, uma chave de mesmo nome|
|`objeto["nome"]`|Primeiro tenta encontrar uma chave; depois, um atributo de mesmo nome|

Por exemplo, dicionários Python possuem um método chamado `items`. Se o dicionário também tiver uma chave chamada `items`, as expressões abaixo poderão produzir resultados diferentes:

```
dados = {
    "items": "valor personalizado"
}
```

```
{{ dados.items }}
{{ dados["items"] }}
```

Na primeira expressão, o Jinja pode resolver `items` como o método do dicionário. Na segunda, a chave é priorizada, retornando `"valor personalizado"`.

**Boa prática:** quando a intenção for acessar uma chave específica de um dicionário, especialmente se ela puder coincidir com um atributo ou método, prefira a notação com colchetes.

### 17.4. Variáveis em templates incluídos

Por padrão, um template chamado por `include` recebe o contexto disponível no template que o incluiu.

Arquivo `templates/partials/usuario.html`:

```
<p>Usuário: {{ nome }}</p>
```

Arquivo `templates/painel.html`:

```
{% set nome = "Marcos" %}

{% include "partials/usuario.html" %}
```

O template incluído pode acessar `nome`, pois essa variável está disponível no contexto.

Também é possível passar informações específicas para o componente incluído:

```
{% include "partials/usuario.html" with context %}
```

Ou fornecer variáveis adicionais diretamente:

```
{% include "partials/usuario.html" with context %}
```

Nesse último exemplo, o comportamento é o mesmo da forma padrão, pois `with context` é o padrão. Para fornecer dados locais adicionais, uma abordagem é usar um bloco `with`:

```
{% with nome="Ana" %}
    {% include "partials/usuario.html" %}
{% endwith %}
```

Durante essa renderização, o template incluído recebe o valor de `nome` definido no bloco `with`.

Também é possível omitir o contexto herdado:

```
{% include "partials/usuario.html" without context %}
```

Nesse caso, o template incluído não recebe as variáveis locais do contexto chamador. Entretanto, isso não significa que todos os recursos globais e configurações do ambiente Jinja sejam removidos.

Use `without context` quando quiser reduzir a dependência de um fragmento em relação aos dados da página que o utiliza. Lembre-se de que o fragmento precisará obter seus dados de outra maneira, ou poderá encontrar variáveis indefinidas.

### 17.5. O que é um `context_processor` no Flask?

Um _context processor_ é uma função que disponibiliza variáveis adicionais ao contexto dos templates do Flask.

Ele é útil quando determinado valor precisa estar disponível em várias páginas, sem que cada rota tenha que passá-lo manualmente.

Por exemplo, imagine que várias páginas precisem exibir o nome do sistema no rodapé.

Sem um context processor, você poderia repetir o mesmo argumento em várias rotas:

```
@app.route("/")
def index():
    return render_template(
        "index.html",
        nome_sistema="Painel"
    )

@app.route("/sobre")
def sobre():
    return render_template(
        "sobre.html",
        nome_sistema="Painel"
    )
```

Isso funciona, mas duplica a configuração.

Com um context processor:

```
@app.context_processor
def disponibilizar_dados_globais():
    return {
        "nome_sistema": "Painel"
    }
```

Agora, os templates renderizados pelo Flask podem utilizar a variável:

```
<footer>
    {{ nome_sistema }}
</footer>
```

A função registrada com `@app.context_processor` retorna um dicionário. As chaves desse dicionário são adicionadas ao contexto dos templates.

O mecanismo é executado durante a preparação do contexto de renderização, não como uma função chamada diretamente pelo template.

#### Quando utilizar?

Use um context processor para valores comuns a várias páginas, por exemplo:

- Nome ou versão da aplicação.
    
- Informações necessárias para a navegação global.
    
- Dados compartilhados da interface.
    
- Estado de apresentação que precisa estar disponível em vários templates.
    

Não use esse mecanismo para despejar todos os dados da aplicação no contexto. Isso dificulta identificar de onde cada variável vem e pode provocar consultas desnecessárias ao banco de dados.

### 17.6. Context processors e conflitos de nomes

Imagine que um context processor disponibilize:

```
@app.context_processor
def contexto_global():
    return {
        "nome": "Sistema"
    }
```

Uma rota também pode passar um valor com esse nome:

```
@app.route("/")
def index():
    return render_template(
        "index.html",
        nome="Marcos"
    )
```

No contexto final, os argumentos fornecidos explicitamente a `render_template()` têm precedência sobre os valores de context processors quando existe um conflito de chave.

Assim, o template recebe `"Marcos"` para `nome` nessa renderização.

Conflitos desse tipo podem tornar o comportamento difícil de entender. Evite nomes genéricos para variáveis globais e prefira nomes que expressem claramente sua finalidade, como `nome_sistema`, `menu_principal` ou `versao_app`.

### 17.7. `app.context_processor` e `Blueprint.app_context_processor`

Quando uma aplicação utiliza Blueprints, existe uma diferença importante entre registrar um context processor na aplicação e registrá-lo em um Blueprint.

Na aplicação:

```
@app.context_processor
def contexto_global():
    return {
        "nome_sistema": "Painel"
    }
```

O objetivo é disponibilizar os dados globalmente aos templates renderizados pela aplicação.

Em um Blueprint:

```
from flask import Blueprint

admin = Blueprint("admin", __name__)

@admin.app_context_processor
def contexto_admin():
    return {
        "secao_atual": "Administração"
    }
```

O método `Blueprint.app_context_processor` registra o processador para a aplicação associada ao Blueprint. Portanto, ele não deve ser tratado como exclusivo das páginas daquele Blueprint.

Se o objetivo for adicionar variáveis apenas ao contexto das requisições atendidas por determinado Blueprint, utilize o mecanismo específico:

```
@admin.context_processor
def contexto_local_admin():
    return {
        "secao_admin": True
    }
```

Essa distinção é importante para evitar disponibilizar variáveis específicas de uma área em toda a aplicação.

### 17.8. Globais do ambiente Jinja

Além do contexto passado durante a renderização e dos context processors, o ambiente Jinja possui variáveis globais.

Essas variáveis são disponibilizadas pelo próprio ambiente de templates, e não precisam ser passadas manualmente por cada rota.

No Flask, é possível registrar uma variável global:

```
app.jinja_env.globals["nome_produto"] = "Painel"
```

Depois, o template pode utilizá-la:

```
<title>{{ nome_produto }}</title>
```

A diferença conceitual é:

|Mecanismo|Finalidade|
|---|---|
|Argumentos de `render_template()`|Dados específicos de uma renderização|
|`context_processor`|Valores adicionados ao contexto dos templates|
|`app.jinja_env.globals`|Valores disponibilizados globalmente pelo ambiente Jinja|

Use globais principalmente para valores estáveis e recursos realmente compartilhados. Para dados que variam por usuário, requisição ou página, prefira o contexto da renderização ou um context processor apropriado.

Não coloque informações confidenciais em globais: elas ficam disponíveis aos templates que podem acessá-las.

### 17.9. O escopo de variáveis definidas com `set`

A instrução `set` permite criar ou atribuir uma variável dentro de um template:

```
{% set titulo = "Página inicial" %}

<h1>{{ titulo }}</h1>
```

O valor pode ser utilizado nas expressões seguintes dentro do escopo em que estiver disponível.

Entretanto, não se deve presumir que uma atribuição feita dentro de um laço `for` alterará uma variável externa de maneira persistente.

Considere:

```
{% set total = 0 %}

{% for numero in numeros %}
    {% set total = total + numero %}
{% endfor %}

<p>Total: {{ total }}</p>
```

Esse código não é uma maneira confiável de acumular o total em `total`, porque as atribuições realizadas dentro de um bloco de laço possuem regras de escopo que impedem a atualização esperada da variável externa.

Para calcular valores derivados dos dados, geralmente é mais claro realizar o cálculo em Python e passar o resultado ao template:

```
numeros = [10, 20, 30]

total = sum(numeros)

return render_template(
    "numeros.html",
    total=total
)
```

No template:

```
<p>Total: {{ total }}</p>
```

Se for realmente necessário manter um estado mutável durante a renderização, o Jinja oferece `namespace`.

```
{% set estado = namespace(total=0) %}

{% for numero in numeros %}
    {% set estado.total = estado.total + numero %}
{% endfor %}

<p>Total: {{ estado.total }}</p>
```

O objeto criado com `namespace` permite alterar seus atributos dentro do laço, tornando o novo valor acessível depois dele.

Apesar disso, cálculos de negócio, agregações e transformações complexas devem normalmente ficar em Python. O `namespace` é mais adequado para necessidades pequenas de apresentação.

### 17.10. Valores indefinidos e `StrictUndefined`

Quando o template tenta acessar uma variável que não foi disponibilizada, o Jinja utiliza o tipo de valor indefinido configurado no ambiente.

No comportamento padrão, um valor indefinido pode aparecer como uma string vazia em algumas situações de renderização. Isso pode ocultar erros: uma variável digitada incorretamente talvez não provoque uma falha evidente.

Por exemplo:

```
<h1>{{ titulo_pagina }}</h1>
```

Se a rota enviar `titulo` em vez de `titulo_pagina`, o resultado pode ser uma página sem título, em vez de um erro claramente identificável.

Para tornar esses problemas mais visíveis durante o desenvolvimento, o Jinja oferece `StrictUndefined`.

No Flask:

```
from jinja2 import StrictUndefined

app.jinja_env.undefined = StrictUndefined
```

Com essa configuração, tentar imprimir, testar ou realizar operações incompatíveis com uma variável indefinida gera um erro, em vez de silenciosamente produzir um resultado vazio.

Isso ajuda a detectar:

- Nomes de variáveis digitados incorretamente.
    
- Dados que uma rota deixou de enviar.
    
- Dependências implícitas em templates incluídos.
    
- Campos que o template pressupõe existir, mas que não foram fornecidos.
    

A configuração deve ser testada com os templates da aplicação, pois ela pode revelar erros que antes passavam despercebidos. Não trate a configuração como substituta da validação dos dados nem como mecanismo de autorização.

### 17.11. Como decidir onde disponibilizar uma variável

|Situação|Local recomendado|
|---|---|
|Valor usado somente em uma página|Argumento de `render_template()`|
|Dados de um objeto ou resultado consultado pela rota|Python, antes da renderização|
|Valor comum a várias páginas|Context processor, se for apropriado|
|Valor estável e global do ambiente de templates|Global do Jinja|
|Componente reutilizável com entradas explícitas|Macro com parâmetros|
|Fragmento HTML reutilizável que depende do contexto chamador|`include`, se essa dependência for intencional|
|Cálculo complexo ou regra de negócio|Python, fora do template|

A regra prática é manter explícita a origem dos dados. Quanto mais uma página depende de variáveis que surgem implicitamente, mais difícil fica entender e testar seu comportamento.

---
## 18. Controle de espaços em branco, comentários e blocos avançados de herança

O Jinja não apenas substitui variáveis e executa estruturas de controle: ele também determina como o texto resultante será organizado.

Isso é importante porque espaços, quebras de linha e indentação existentes no template podem aparecer na saída renderizada. Em HTML, boa parte desses espaços é visualmente irrelevante, mas pode afetar a legibilidade do código-fonte gerado, respostas textuais, arquivos de configuração e outros formatos.

Nesta seção, veremos como controlar a saída e utilizar recursos avançados da herança de templates.

### 18.1. Como o Jinja trata espaços em branco

Considere este template:

```
<ul>
    {% for nome in nomes %}
        <li>{{ nome }}</li>
    {% endfor %}
</ul>
```

O Jinja interpreta o laço e produz o HTML correspondente. Dependendo da configuração do ambiente e da posição dos delimitadores, algumas quebras de linha e espaços presentes no template também podem permanecer na saída.

Em páginas HTML, a diferença visual costuma ser pequena. Porém, quando o template gera texto puro, JSON formatado, e-mails ou outros conteúdos, o controle de espaços pode ser mais importante.

O Jinja disponibiliza modificadores para remover espaços em branco próximos aos delimitadores.

### 18.2. Removendo espaços com `-`

O hífen (`-`) colocado junto ao delimitador de uma instrução Jinja remove espaços em branco adjacentes.

Considere:

```
{% for nome in nomes %}
    {{ nome }}
{% endfor %}
```

É possível modificar os delimitadores:

```
{%- for nome in nomes -%}
    {{ nome }}
{%- endfor -%}
```

O modificador pode ser utilizado nos delimitadores de instruções, expressões e comentários.

|Forma|Efeito|
|---|---|
|`{%-`|Remove espaços em branco imediatamente anteriores ao delimitador de abertura|
|`-%}`|Remove espaços em branco imediatamente posteriores ao delimitador de fechamento|
|`{{-`|Remove espaços anteriores à expressão|
|`-}}`|Remove espaços posteriores à expressão|
|`{#-`|Remove espaços anteriores ao comentário|
|`-#}`|Remove espaços posteriores ao comentário|

O comportamento é local ao delimitador: cada hífen controla o lado correspondente da região de template.

**Atenção:** usar os modificadores indiscriminadamente pode juntar partes do conteúdo que deveriam ficar separadas.

Por exemplo:

```
{% for nome in nomes -%}
{{ nome }}
{%- endfor %}
```

A remoção dos espaços ao redor das instruções pode produzir uma saída diferente daquela obtida com o template original.

Não adicione hífens automaticamente a todos os delimitadores. Primeiro identifique quais espaços realmente precisam ser removidos.

### 18.3. Removendo espaços com `+`

O sinal de mais (`+`) permite desativar a remoção de espaços que seria aplicada por configurações de controle de espaços do ambiente.

Por exemplo:

```
{%+ if mostrar %}
    Conteúdo
{% endif %}
```

O `+` pode aparecer junto ao delimitador de abertura ou de fechamento, conforme o lado cujo comportamento se deseja preservar.

Esse recurso é especialmente relevante quando o ambiente foi configurado para remover espaços em branco automaticamente.

Em um ambiente sem essa configuração, o sinal de mais geralmente não produz uma diferença prática na renderização.

### 18.4. Configurações globais de espaços em branco

O Jinja possui configurações que afetam o tratamento de espaços em branco durante a renderização.

No Flask, elas podem ser configuradas pelo ambiente Jinja:

```
app.jinja_env.trim_blocks = True
app.jinja_env.lstrip_blocks = True
```

As duas opções têm funções distintas.

|Configuração|Comportamento|
|---|---|
|`trim_blocks`|Remove a primeira quebra de linha após um bloco de instrução, quando aplicável|
|`lstrip_blocks`|Remove espaços e tabulações antes do início de um bloco de instrução na linha|

Essas opções ajudam a reduzir quebras de linha e espaços desnecessários no HTML gerado.

Exemplo:

```
<ul>
{% for nome in nomes %}
    <li>{{ nome }}</li>
{% endfor %}
</ul>
```

Com as opções habilitadas, a saída tende a conter menos espaços associados às linhas que possuem instruções Jinja.

Isso não significa que o HTML será automaticamente minificado. O Jinja continua preservando o conteúdo textual que não for removido pelas regras de tratamento de espaços.

**Boa prática:** escolha uma política consistente para o projeto. Misturar remoções globais com muitos modificadores locais pode tornar a saída difícil de prever.

### 18.5. Comentários do Jinja

Os comentários do Jinja utilizam a sintaxe:

```
{# Este comentário não aparece na saída renderizada. #}
```

Diferentemente dos comentários HTML, eles são removidos durante o processamento do template.

Compare:

```
<!-- Comentário HTML -->
<p>{{ nome }}</p>
```

Com:

```
{# Comentário Jinja #}
<p>{{ nome }}</p>
```

O comentário HTML permanece no código-fonte HTML gerado. O comentário Jinja não.

|Tipo|Sintaxe|Aparece no HTML final?|
|---|---|---|
|Comentário Jinja|`{# ... #}`|Não|
|Comentário HTML|`<!-- ... -->`|Sim|

Use comentários Jinja para registrar observações internas sobre o template sem enviá-las ao navegador.

Lembre-se de que nenhum dos dois tipos de comentário deve ser usado para armazenar senhas, tokens ou outras informações confidenciais.

### 18.6. O bloco `raw`

Em determinadas situações, é necessário exibir a sintaxe do próprio Jinja sem que ela seja interpretada.

Para isso, existe o bloco `raw`.

```
{% raw %}
    {{ nome }}
    {% if usuario %}
        Conteúdo
    {% endif %}
{% endraw %}
```

Dentro desse bloco, as expressões e instruções Jinja mostradas são tratadas como texto, em vez de serem executadas.

A saída inclui os delimitadores exibidos no exemplo.

Esse recurso é útil para:

- Documentação de sintaxe.
    
- Tutoriais sobre Jinja.
    
- Exemplos de código armazenados em templates.
    
- Conteúdo que precisa mostrar literalmente os delimitadores do mecanismo de templates.
    

O bloco `raw` não deve ser confundido com uma forma de tornar conteúdo confiável ou seguro. Ele controla a interpretação da sintaxe Jinja durante a renderização, não substitui o escapamento de saída HTML nem valida dados externos.

### 18.7. O bloco `super()` na herança

Na herança de templates, um template filho pode substituir o conteúdo de um bloco definido pelo template pai.

Entretanto, às vezes o filho precisa acrescentar conteúdo sem descartar tudo o que o pai já fornece. Para isso, existe `super()`.

Template pai, `templates/base.html`:

```
<!doctype html>
<html lang="pt-BR">
<head>
    <meta charset="utf-8">

    {% block extra_head %}
    <title>Painel</title>
    {% endblock %}
</head>
<body>
    {% block conteudo %}{% endblock %}
</body>
</html>
```

Template filho, `templates/painel.html`:

```
{% extends "base.html" %}

{% block extra_head %}
    {{ super() }}
    <meta name="description" content="Painel de controle">
{% endblock %}

{% block conteudo %}
    <h1>Painel</h1>
{% endblock %}
```

A chamada `super()` insere o conteúdo correspondente ao bloco do template pai no ponto em que foi chamada.

Sem ela, o bloco `extra_head` do filho substituiria o conteúdo original do pai, incluindo o elemento `<title>`.

Com `super()`, o filho preserva esse conteúdo e acrescenta a descrição.

O recurso é útil quando vários templates precisam estender uma estrutura comum sem duplicar o conteúdo já definido.

### 18.8. Blocos aninhados

Os blocos de herança podem ser organizados dentro de outros blocos, desde que a estrutura seja válida.

Por exemplo, um template base pode fornecer um bloco para o conteúdo principal e outro para scripts específicos:

```
{% block conteudo %}
    <main>
        {% block conteudo_principal %}{% endblock %}
    </main>
{% endblock %}
```

Um template filho pode substituir o bloco externo e definir o interno:

```
{% extends "base.html" %}

{% block conteudo %}
    {{ super() }}

    {% block conteudo_principal %}
        <h1>Área administrativa</h1>
    {% endblock %}
{% endblock %}
```

Nesse exemplo, é necessário prestar atenção à relação entre os blocos e à chamada de `super()`: ela preserva o conteúdo herdado, incluindo o conteúdo do bloco interno presente no pai. Se o filho redefinir o bloco interno, essa redefinição também será utilizada conforme a estrutura de herança.

Blocos aninhados permitem criar pontos de extensão mais específicos em layouts complexos, mas uma hierarquia excessiva pode dificultar a leitura. Utilize-os quando houver uma necessidade real de personalização.

### 18.9. O bloco `scoped`

Por padrão, blocos definidos dentro de laços não recebem automaticamente as variáveis locais do laço quando são sobrescritos por um template filho.

O modificador `scoped` permite que o contexto local seja disponibilizado ao bloco.

Template pai:

```
<ul>
    {% for usuario in usuarios %}
        <li>
            {% block item_usuario scoped %}
                {{ usuario.nome }}
            {% endblock %}
        </li>
    {% endfor %}
</ul>
```

O template filho pode substituir o bloco:

```
{% extends "usuarios.html" %}

{% block item_usuario %}
    <strong>{{ usuario.nome }}</strong>
{% endblock %}
```

Como o bloco pai foi declarado com `scoped`, a variável local `usuario` fica disponível dentro da implementação sobrescrita.

Esse recurso é útil quando um bloco precisa ser personalizado por herança, mas depende de variáveis criadas por uma estrutura de controle externa.

Não confunda `scoped` com a criação de uma variável global: ele trata do contexto disponibilizado ao bloco.

### 18.10. O bloco `required`

O modificador `required` indica que um bloco deve ser implementado por um template descendente, em vez de utilizar uma implementação vazia ou padrão.

Exemplo:

```
{% block conteudo required %}{% endblock %}
```

Um template que estenda esse template deverá fornecer uma implementação para o bloco obrigatório. Se o requisito não for atendido, o Jinja produzirá um erro durante a renderização.

O bloco obrigatório precisa conter apenas espaços em branco e comentários em sua implementação original, conforme as regras do Jinja.

Esse recurso é útil para layouts abstratos que definem uma estrutura comum, mas exigem que cada template concreto forneça determinada parte do conteúdo.

Por exemplo, um template base pode exigir que cada página declare seu conteúdo principal, evitando páginas que estendam a estrutura sem implementar a área esperada.

### 18.11. O objeto especial `self`

Dentro de um template, `self` permite acessar os blocos definidos pelo próprio template, incluindo as implementações resultantes da herança.

Por exemplo:

```
{% block titulo %}
    Painel administrativo
{% endblock %}

<title>{{ self.titulo() }}</title>
```

A chamada `self.titulo()` renderiza o conteúdo do bloco `titulo`.

Esse mecanismo pode ser útil quando o conteúdo de um bloco precisa ser reutilizado em outra parte do mesmo template.

Entretanto, não confunda `self` com `self` de uma classe Python. Aqui, ele representa o objeto de blocos do template Jinja.

Para layouts simples, pode ser mais fácil definir uma variável e utilizá-la em mais de um local. Reserve `self` para casos em que seja importante reutilizar o resultado de um bloco, respeitando a estrutura de herança.

### 18.12. Erros comuns no controle de espaços e na herança

|Problema|Causa provável|Como investigar|
|---|---|---|
|HTML gerado sem espaços esperados|Uso excessivo de `-` ou configuração global de espaços|Examine os delimitadores e as opções `trim_blocks` e `lstrip_blocks`|
|Comentários aparecem no navegador|Foi usado comentário HTML em vez de comentário Jinja|Utilize `{# ... #}` para comentários internos|
|O bloco do pai desapareceu|O filho sobrescreveu o bloco sem chamar `super()`|Adicione `{{ super() }}` quando precisar preservar o conteúdo herdado|
|Variável do laço não aparece no bloco sobrescrito|O bloco não recebeu o escopo local esperado|Verifique se a declaração do bloco precisa de `scoped`|
|Template filho falha por falta de conteúdo|Existe um bloco `required` sem implementação|Implemente o bloco no template descendente|
|Sintaxe Jinja aparece como texto|O conteúdo está dentro de um bloco `raw`|Verifique onde o bloco começa e termina|
|Saída HTML tem muitas quebras de linha|Espaços do template foram preservados|Avalie as configurações globais e os modificadores locais|

### 18.13. Escolhendo o recurso correto

| Necessidade                                                    | Recurso                |
| -------------------------------------------------------------- | ---------------------- |
| Remover espaços em branco em um ponto específico               | Modificadores `-`      |
| Preservar espaços diante de uma configuração global de remoção | Modificadores `+`      |
| Ocultar comentários da saída renderizada                       | `{# ... #}`            |
| Exibir literalmente a sintaxe Jinja                            | `raw`                  |
| Preservar o conteúdo original de um bloco pai                  | `super()`              |
| Disponibilizar variáveis locais a um bloco sobrescrito         | `scoped`               |
| Exigir uma implementação em templates descendentes             | `required`             |
| Reutilizar o conteúdo renderizado de um bloco                  | `self.nome_do_bloco()` |

---
## 19. Ambiente Jinja, filtros personalizados, testes personalizados e funções globais

O Jinja possui recursos nativos para manipular valores, verificar condições e disponibilizar informações aos templates. Entretanto, em aplicações Flask, pode surgir a necessidade de criar filtros próprios, testes específicos ou funções reutilizáveis que não existem na biblioteca padrão.

Para isso, o Flask permite registrar recursos no ambiente Jinja.

Antes de estudar cada mecanismo, é importante distinguir suas finalidades:

|Recurso|Finalidade|Sintaxe no template|
|---|---|---|
|Filtro personalizado|Transformar ou formatar um valor|`{{ valor\|meu_filtro }}`|
|Teste personalizado|Verificar se um valor satisfaz uma condição|`{% if valor is meu_teste %}`|
|Função global|Executar uma função disponível no ambiente Jinja|`{{ minha_funcao(valor) }}`|
|Variável global|Disponibilizar um valor global aos templates|`{{ nome_variavel }}`|

Esses recursos são registrados no ambiente Jinja, mas cada um possui uma forma de utilização diferente.

### 19.1. Criando um filtro personalizado

Um filtro recebe um valor e produz um resultado. Ele é útil quando determinada transformação precisa ser aplicada diretamente na expressão do template.

Imagine que uma aplicação precise apresentar identificadores de usuários em letras maiúsculas.

O Python pode executar essa transformação antes de renderizar a página, mas, quando a operação é uma regra de apresentação reutilizável, um filtro pode deixar o template mais expressivo.

No Flask:

```
@app.template_filter("maiusculas")
def aplicar_maiusculas(valor):
    return str(valor).upper()
```

No template:

```
<p>{{ nome|maiusculas }}</p>
```

Se `nome` contiver `"marcos"`, o resultado será:

```
<p>MARCOS</p>
```

O decorador `@app.template_filter("maiusculas")` registra a função no ambiente de templates usando o nome `maiusculas`.

A função recebe o valor colocado antes do caractere `|`. O valor retornado pela função passa a ser o resultado daquela expressão.

A transformação não altera automaticamente a variável original em Python.

#### Registrando um filtro sem decorador

O mesmo recurso pode ser registrado por meio do método `add_template_filter()`:

```
def aplicar_maiusculas(valor):
    return str(valor).upper()

app.add_template_filter(
    aplicar_maiusculas,
    name="maiusculas"
)
```

As duas abordagens registram um filtro. O decorador costuma ser conveniente quando a função foi criada especificamente para esse propósito; o método permite registrar funções já existentes.

### 19.2. Filtros com argumentos

Um filtro também pode receber argumentos adicionais.

Imagine que seja necessário limitar o tamanho de um texto e acrescentar um sufixo quando ele ultrapassar determinado comprimento.

```
@app.template_filter("limitar_texto")
def limitar_texto(valor, limite=30, sufixo="..."):
    texto = str(valor)

    if len(texto) <= limite:
        return texto

    return texto[:limite] + sufixo
```

No template:

```
<p>{{ descricao|limitar_texto }}</p>

<p>{{ descricao|limitar_texto(15) }}</p>

<p>{{ descricao|limitar_texto(15, " […]") }}</p>
```

O primeiro exemplo utiliza os valores padrão: limite de 30 caracteres e sufixo `"..."`.

O segundo define o limite como 15. O terceiro define tanto o limite quanto o sufixo.

O Jinja passa o valor original como primeiro argumento e os argumentos escritos entre parênteses como argumentos adicionais da função.

É possível utilizar argumentos nomeados:

```
{{ descricao|limitar_texto(limite=20, sufixo="...") }}
```

Isso facilita a leitura quando um filtro recebe vários parâmetros.

**Atenção:** esse filtro é apenas um exemplo de apresentação. Ele corta o texto por quantidade de caracteres, não por palavras, e não trata necessariamente limites de caracteres Unicode compostos por múltiplos pontos de código como o usuário os percebe visualmente.

### 19.3. Filtros que trabalham com datas e valores

Filtros personalizados também podem encapsular regras de formatação.

Por exemplo:

```
from datetime import datetime

@app.template_filter("data_br")
def data_br(valor):
    if valor is None:
        return "Não informada"

    if not isinstance(valor, datetime):
        return "Data inválida"

    return valor.strftime("%d/%m/%Y")
```

No template:

```
<p>Cadastro: {{ usuario.criado_em|data_br }}</p>
```

A função verifica o tipo recebido antes de chamar `strftime()`. Isso evita tentar formatar como `datetime` um valor de tipo inesperado.

Contudo, retornar `"Data inválida"` não substitui a validação dos dados na aplicação. Se uma data inválida indicar um problema de integridade, a lógica responsável por receber ou persistir esse dado deve tratar o problema adequadamente.

Também é importante decidir se o filtro deve aceitar apenas `datetime`, aceitar `date` ou converter strings. A interface do filtro deve ter um contrato claro para que os templates não dependam de comportamentos ambíguos.

### 19.4. Cuidados com o escapamento de HTML em filtros

Por padrão, o Flask configura o Jinja para realizar escapamento automático em templates HTML.

Considere um filtro que devolva conteúdo contendo tags:

```
@app.template_filter("mensagem_html")
def mensagem_html(valor):
    return f"<strong>{valor}</strong>"
```

No template:

```
{{ nome|mensagem_html }}
```

Como o resultado é uma string comum, o escapamento automático pode transformar os caracteres especiais em entidades HTML. Assim, as tags podem aparecer como texto, em vez de serem interpretadas como marcação.

Esse comportamento ajuda a impedir que strings sejam interpretadas indevidamente como HTML.

Não resolva o problema simplesmente marcando todo resultado como seguro. Se o valor recebido vier de um usuário, ele poderá conter conteúdo malicioso.

Para gerar marcação HTML, prefira construir a estrutura no próprio template:

```
<strong>{{ nome }}</strong>
```

Se for realmente necessário produzir HTML dentro de um filtro, será preciso tratar cuidadosamente o escapamento dos valores e a confiança na marcação gerada. O uso de `Markup` ou do filtro `safe` exige atenção especial: ambos podem fazer com que conteúdo deixe de passar pelo escapamento automático.

### 19.5. Criando um teste personalizado

Enquanto um filtro transforma um valor, um teste verifica se ele satisfaz uma condição.

Por exemplo, imagine que o sistema precise identificar se um usuário pertence ao grupo administrativo.

```
@app.template_test("administrador")
def eh_administrador(usuario):
    return (
        usuario is not None
        and getattr(usuario, "perfil", None) == "admin"
    )
```

No template:

```
{% if usuario is administrador %}
    <p>Área administrativa</p>
{% endif %}
```

O Jinja chama o teste passando o valor que aparece antes de `is`. O resultado deve ser verdadeiro ou falso.

O uso de `getattr()` permite verificar o atributo sem provocar um `AttributeError` quando o objeto não o possui. Isso não significa que o objeto seja válido para qualquer outra operação.

#### Registrando o teste com `add_template_test()`

A mesma função pode ser registrada explicitamente:

```
def eh_administrador(usuario):
    return (
        usuario is not None
        and getattr(usuario, "perfil", None) == "admin"
    )

app.add_template_test(
    eh_administrador,
    name="administrador"
)
```

O nome registrado é utilizado na expressão Jinja:

```
{% if usuario is administrador %}
    ...
{% endif %}
```

Um teste personalizado pode ser útil para condições recorrentes de apresentação, como verificar um estado de um objeto ou uma propriedade específica.

### 19.6. Testes personalizados com argumentos

Assim como filtros, testes podem receber argumentos.

```
@app.template_test("maior_que")
def maior_que(valor, limite):
    return valor is not None and valor > limite
```

No template:

```
{% if idade is maior_que(17) %}
    <p>Idade acima de 17.</p>
{% endif %}
```

O valor `idade` é passado como primeiro argumento e `17` como argumento adicional.

O exemplo pressupõe que `idade`, quando não for `None`, possa ser comparada numericamente com `limite`. Se o valor for uma string ou outro tipo incompatível, a comparação poderá gerar um erro.

Portanto, defina quais tipos o teste aceita e valide os dados antes da renderização quando necessário.

### 19.7. Filtros e testes: qual escolher?

Compare as expressões:

```
{{ preco|formatar_preco }}
```

```
{% if preco is preco_valido %}
    ...
{% endif %}
```

A primeira expressão transforma ou formata um valor para apresentação. A segunda verifica uma propriedade e retorna um resultado booleano.

|Necessidade|Recurso indicado|
|---|---|
|Converter um texto para maiúsculas|Filtro|
|Formatar uma data para exibição|Filtro|
|Limitar o comprimento de um texto|Filtro|
|Verificar se um valor está dentro de uma condição|Teste|
|Verificar se um objeto possui determinada característica|Teste|
|Escolher entre duas apresentações|Teste combinado com `if`|

Embora seja possível escrever uma função que faça mais de uma dessas coisas, manter cada recurso com uma responsabilidade clara torna os templates mais fáceis de ler.

### 19.8. Registrando funções globais

Uma função global é uma função que pode ser chamada diretamente no template sem precisar ser passada explicitamente em cada chamada de `render_template()`.

Por exemplo:

```
def nome_exibicao(usuario):
    if usuario is None:
        return "Visitante"

    return getattr(usuario, "nome", "Usuário")

app.add_template_global(
    nome_exibicao,
    name="nome_exibicao"
)
```

No template:

```
<p>Olá, {{ nome_exibicao(usuario) }}.</p>
```

A expressão chama a função registrada no ambiente Jinja.

A diferença em relação a um filtro é a forma de invocação:

```
{{ valor|meu_filtro }}
```

```
{{ minha_funcao(valor) }}
```

Um filtro é especialmente adequado a transformações encadeadas. Uma função global é mais apropriada quando a operação é naturalmente expressa como uma chamada de função.

### 19.9. Funções globais com decorador

O Flask também oferece o decorador `@app.template_global()`:

```
@app.template_global("versao_formatada")
def versao_formatada(major, minor):
    return f"{major}.{minor}"
```

No template:

```
<footer>
    Versão {{ versao_formatada(2, 5) }}
</footer>
```

O resultado será:

```
<footer>
    Versão 2.5
</footer>
```

O decorador registra a função com o nome informado. Se o nome não for especificado, o Flask utiliza o nome da função.

Use funções globais para operações pequenas, previsíveis e adequadas à camada de apresentação. Funções que executam operações de negócio complexas devem permanecer na camada Python responsável por essas regras.

### 19.10. Variáveis globais e funções globais não são a mesma coisa

O ambiente Jinja permite disponibilizar tanto valores quanto funções.

Uma variável global:

```
app.add_template_global("2.5", name="versao_app")
```

Uma função global:

```
app.add_template_global(
    lambda major, minor: f"{major}.{minor}",
    name="formatar_versao"
)
```

No template:

```
<p>Versão atual: {{ versao_app }}</p>

<p>Versão formatada: {{ formatar_versao(2, 5) }}</p>
```

A primeira expressão lê um valor. A segunda executa uma função.

Embora ambas sejam registradas como globais, elas têm contratos diferentes.

Para valores que mudam de acordo com a requisição, o usuário ou a página, não utilize uma variável global como substituto automático do contexto. Considere os mecanismos de passagem de dados e os context processors explicados na seção anterior.

### 19.11. O ambiente Jinja e o registro direto de recursos

O Flask expõe o ambiente Jinja por meio de `app.jinja_env`.

É possível registrar filtros, testes e funções diretamente nas coleções do ambiente:

```
app.jinja_env.filters["maiusculas"] = aplicar_maiusculas
app.jinja_env.tests["administrador"] = eh_administrador
app.jinja_env.globals["nome_exibicao"] = nome_exibicao
```

Essas atribuições registram os recursos nos respectivos espaços de nomes do ambiente.

Entretanto, nas aplicações Flask, os métodos e decoradores da aplicação costumam ser mais claros:

- `@app.template_filter()` ou `app.add_template_filter()`
    
- `@app.template_test()` ou `app.add_template_test()`
    
- `@app.template_global()` ou `app.add_template_global()`
    

Esses mecanismos tornam a intenção do código explícita e ajudam a manter o registro dos recursos organizado.

**Importante:** configure o ambiente antes de começar a renderizar templates. Alterar configurações e registros do ambiente depois que a aplicação já começou a processar requisições pode gerar comportamentos difíceis de controlar em ambientes concorrentes.

### 19.12. Boas práticas para recursos personalizados

- **Use nomes descritivos:** `data_br`, `maior_que` e `nome_exibicao` deixam a intenção mais clara do que nomes genéricos.
    
- **Defina o contrato de entrada:** documente os tipos aceitos e o comportamento diante de `None` ou valores inesperados.
    
- **Mantenha as funções previsíveis:** prefira operações que retornem resultados sem alterar estado global.
    
- **Evite consultas ao banco dentro de filtros e funções globais:** isso pode ocultar operações caras durante a renderização e provocar consultas repetidas.
    
- **Não confunda apresentação com autorização:** um teste Jinja pode ocultar ou mostrar elementos da interface, mas não impede que alguém acesse diretamente uma rota protegida.
    
- **Preserve o escapamento automático:** não marque strings arbitrárias como HTML seguro.
    
- **Registre recursos uma única vez:** centralize os registros na inicialização da aplicação ou em uma função de configuração apropriada.
    
- **Teste os casos de borda:** valores ausentes, tipos incorretos, textos vazios e entradas inesperadas devem ter comportamentos definidos.
    

### 19.13. Exemplo integrado

O exemplo a seguir reúne um filtro, um teste e uma função global em uma aplicação Flask pequena.

```
from flask import Flask, render_template

app = Flask(__name__)


@app.template_filter("maiusculas")
def maiusculas(valor):
    return str(valor).upper()


@app.template_test("ativo")
def ativo(usuario):
    return (
        usuario is not None
        and getattr(usuario, "ativo", False) is True
    )


@app.template_global("saudacao")
def saudacao(nome):
    return f"Olá, {nome}!"


@app.route("/")
def index():
    usuario = {
        "nome": "Marcos",
        "ativo": True
    }

    return render_template(
        "index.html",
        usuario=usuario
    )


if __name__ == "__main__":
    app.run(debug=True)
```

Arquivo `templates/index.html`:

```
<h1>{{ saudacao(usuario.nome) }}</h1>

<p>Nome: {{ usuario.nome|maiusculas }}</p>

{% if usuario is ativo %}
    <p>O usuário está ativo.</p>
{% else %}
    <p>O usuário está inativo.</p>
{% endif %}
```

Nesse exemplo:

1. `maiusculas` transforma o nome em letras maiúsculas.
    
2. `ativo` verifica a condição do usuário e retorna um booleano.
    
3. `saudacao` constrói uma mensagem a partir do nome.
    
4. `render_template()` fornece o objeto `usuario` ao contexto da renderização.
    

Os recursos personalizados não substituem a necessidade de organizar corretamente as responsabilidades da aplicação. Eles ampliam a expressividade do template e devem ser utilizados quando tornam a apresentação mais clara, não para transferir toda a lógica Python para o Jinja.

---
## 20. Variáveis e funções disponibilizadas pelo Flask nos templates

O Flask integra o Jinja ao sistema de requisições da aplicação. Por isso, além das variáveis que você passa por `render_template()` e dos recursos personalizados registrados no ambiente, determinados objetos e funções ficam disponíveis automaticamente nos templates.

Esses recursos permitem acessar informações da requisição atual, gerar URLs, trabalhar com mensagens temporárias e consultar configurações da aplicação sem precisar passar cada elemento manualmente em todas as rotas.

### 20.1. `request`: informações da requisição atual

O objeto `request`, disponibilizado pelo Flask nos templates, representa a requisição HTTP que está sendo processada.

Por exemplo, em uma rota:

```
from flask import render_template

@app.route("/perfil")
def perfil():
    return render_template("perfil.html")
```

No template `templates/perfil.html`:

```
<p>Método HTTP: {{ request.method }}</p>
<p>Caminho: {{ request.path }}</p>
```

Se o navegador acessar `/perfil` por meio de uma requisição GET, o template poderá apresentar o método `GET` e o caminho `/perfil`.

Alguns atributos e propriedades úteis:

|Atributo|Significado|
|---|---|
|`request.method`|Método HTTP utilizado, como `GET` ou `POST`|
|`request.path`|Caminho da URL, sem a query string|
|`request.args`|Parâmetros enviados na query string|
|`request.form`|Dados de formulário enviados no corpo de uma requisição apropriada|
|`request.is_json`|Indica se a requisição é identificada como JSON|
|`request.endpoint`|Nome do endpoint associado à requisição, quando disponível|

Por exemplo, para a URL `/busca?q=python`:

```
<p>Termo pesquisado: {{ request.args.get("q", "") }}</p>
```

A expressão acessa o parâmetro `q`. O segundo argumento de `get()` define um valor alternativo quando o parâmetro não existe.

**Atenção:** o conteúdo de `request.args` e `request.form` vem da requisição e deve ser tratado como entrada não confiável. Não utilize esses valores como prova de identidade, autorização ou validade de uma operação.

Além disso, embora seja possível realizar verificações com `request` dentro do template, regras importantes sobre métodos HTTP, validação e permissões devem ficar na aplicação Python.

### 20.2. `session`: dados da sessão do usuário

O objeto `session` permite consultar os dados armazenados na sessão da requisição atual.

No Flask, a sessão padrão utiliza um cookie assinado para proteger a integridade dos dados. Isso não significa que o conteúdo seja criptografado ou que deva ser tratado como um armazenamento secreto.

Exemplo de uma rota:

```
from flask import session, render_template

@app.route("/painel")
def painel():
    return render_template("painel.html")
```

No template:

```
{% if session.get("usuario_id") %}
    <p>Existe um identificador de usuário na sessão.</p>
{% else %}
    <p>Nenhum identificador de usuário foi encontrado.</p>
{% endif %}
```

A expressão `session.get("usuario_id")` consulta a chave sem provocar um erro apenas porque ela não existe.

Entretanto, a presença de uma chave na sessão não deve ser utilizada, por si só, como mecanismo de autorização. A aplicação deve verificar a identidade e as permissões de acordo com seu sistema de autenticação.

Também evite expor no template valores de sessão que não precisem ser apresentados ao usuário.

### 20.3. `g`: dados associados ao contexto da requisição

O objeto `g` é utilizado pelo Flask para armazenar dados temporários associados ao contexto da aplicação durante uma requisição.

Ele pode ser preenchido por funções executadas antes da renderização, como uma função registrada com `before_request`.

```
from flask import g, render_template

@app.before_request
def preparar_contexto():
    g.nome_aplicacao = "Painel"

@app.route("/")
def index():
    return render_template("index.html")
```

No template:

```
<footer>
    {{ g.nome_aplicacao }}
</footer>
```

Nesse exemplo, a função `preparar_contexto()` atribui um valor a `g.nome_aplicacao`, e o template consegue consultá-lo.

O objeto `g` não é um armazenamento permanente entre requisições. Ele existe para manter dados associados ao contexto atual e é descartado quando esse contexto termina.

É comum utilizá-lo para dados que precisam ser compartilhados durante o processamento de uma requisição. Para dados persistentes, utilize o mecanismo apropriado, como um banco de dados.

### 20.4. `config`: configurações da aplicação

O Flask disponibiliza o objeto `config` nos templates. Ele permite consultar valores de configuração da aplicação.

Por exemplo, durante a configuração:

```
app.config["NOME_APLICACAO"] = "Painel"
```

No template:

```
<title>{{ config["NOME_APLICACAO"] }}</title>
```

O uso de colchetes permite consultar explicitamente a chave do dicionário de configuração.

Esse mecanismo pode ser útil para valores de apresentação que realmente precisem estar disponíveis no template. Entretanto, não é recomendável expor indiscriminadamente as configurações da aplicação.

**Nunca exiba a configuração inteira no HTML.** Ela pode conter chaves secretas, credenciais e outras informações internas. Disponibilize apenas os valores necessários e apropriados para apresentação.

### 20.5. `url_for()`: geração de URLs

A função `url_for()` é uma das ferramentas mais importantes disponíveis nos templates Flask.

Ela gera uma URL a partir do nome de um endpoint, em vez de exigir que você escreva o caminho diretamente no HTML.

Considere:

```
@app.route("/")
def index():
    return render_template("index.html")

@app.route("/perfil")
def perfil():
    return render_template("perfil.html")
```

No template:

```
<a href="{{ url_for('index') }}">Início</a>
<a href="{{ url_for('perfil') }}">Perfil</a>
```

O Flask resolve os nomes dos endpoints e gera os caminhos correspondentes.

Essa abordagem evita depender de caminhos escritos manualmente. Se o caminho de uma rota mudar, os templates que utilizam `url_for()` poderão continuar funcionando sem alterações, desde que os nomes dos endpoints permaneçam corretos.

#### Passando parâmetros para um endpoint

Considere uma rota dinâmica:

```
@app.route("/usuarios/<int:usuario_id>")
def detalhe_usuario(usuario_id):
    return render_template(
        "usuario.html",
        usuario_id=usuario_id
    )
```

No template:

```
<a href="{{ url_for('detalhe_usuario', usuario_id=42) }}">
    Ver usuário
</a>
```

O Flask utiliza `usuario_id=42` para preencher o parâmetro da rota.

Argumentos que não correspondem aos parâmetros da rota normalmente são acrescentados à query string:

```
<a href="{{ url_for('index', origem='menu') }}">
    Início
</a>
```

Isso gera uma URL com o parâmetro `origem`, desde que a geração da URL seja válida para o endpoint.

#### URLs de arquivos estáticos

O Flask também utiliza `url_for()` para gerar URLs de arquivos estáticos.

Por exemplo:

```
<link
    rel="stylesheet"
    href="{{ url_for('static', filename='css/style.css') }}"
>
```

O endpoint `static` é utilizado para localizar o arquivo dentro do diretório de arquivos estáticos configurado na aplicação.

Essa forma é preferível a escrever manualmente `/static/css/style.css`, pois respeita a configuração da aplicação e facilita a manutenção.

### 20.6. `get_flashed_messages()`: recuperação de mensagens temporárias

O Flask possui um mecanismo chamado _flash messages_, utilizado para disponibilizar mensagens temporárias, geralmente após uma operação realizada pelo usuário.

Uma rota pode registrar uma mensagem:

```
from flask import flash, redirect, url_for

@app.route("/salvar")
def salvar():
    flash("Operação realizada com sucesso.", "success")
    return redirect(url_for("index"))
```

No template, `get_flashed_messages()` recupera as mensagens registradas:

```
{% with mensagens = get_flashed_messages(with_categories=true) %}
    {% if mensagens %}
        {% for categoria, mensagem in mensagens %}
            <div class="alert alert-{{ categoria }}">
                {{ mensagem }}
            </div>
        {% endfor %}
    {% endif %}
{% endwith %}
```

Quando `with_categories=true` é utilizado, cada item retornado contém a categoria e a mensagem.

No exemplo da rota, a categoria é `success`. Ela pode ser usada para escolher a aparência da notificação.

O bloco `with` limita o escopo da variável `mensagens` ao trecho em que ela é utilizada.

#### Sem categorias

Se você não precisar das categorias, pode utilizar:

```
{% for mensagem in get_flashed_messages() %}
    <p>{{ mensagem }}</p>
{% endfor %}
```

Por padrão, as mensagens recuperadas são removidas da fila de mensagens flash da sessão. Isso significa que, depois de recuperadas, normalmente não ficam disponíveis para serem exibidas novamente em outra renderização.

**Atenção:** as categorias retornadas por `flash()` são valores usados para apresentação. Não as utilize como prova de que uma operação foi autorizada ou concluída corretamente.

### 20.7. `current_user` e Flask-Login

Ao utilizar Flask-Login, a integração disponibiliza `current_user` nos templates por meio de seu context processor.

Exemplo:

```
{% if current_user.is_authenticated %}
    <p>Olá, {{ current_user.nome }}.</p>
{% else %}
    <p>Você não está autenticado.</p>
{% endif %}
```

O atributo `is_authenticated` indica se o objeto atual representa um usuário autenticado segundo o Flask-Login.

O atributo `nome`, por outro lado, depende de como o modelo de usuário foi definido. Se o seu modelo utilizar `username`, `name` ou outro campo, será necessário usar o atributo correspondente.

Também é importante distinguir a apresentação da autorização: esconder um botão no template não impede que alguém tente acessar diretamente a rota correspondente. As permissões devem ser verificadas no servidor.

Sem Flask-Login ou outro mecanismo que disponibilize essa variável, não presuma que `current_user` estará presente automaticamente.

### 20.8. O contexto de requisição e o contexto de aplicação

Alguns objetos do Flask dependem de contextos ativos durante o processamento da requisição.

Os dois contextos mais importantes são:

|Contexto|Finalidade|
|---|---|
|Contexto de requisição|Disponibiliza informações da requisição HTTP atual|
|Contexto de aplicação|Disponibiliza informações associadas à aplicação Flask atual|

O objeto `request` depende do contexto de requisição. Já `current_app` representa a aplicação ativa no contexto de aplicação, e `g` está associado ao contexto de aplicação, sendo normalmente utilizado durante a requisição.

O Flask prepara os contextos necessários ao processar uma requisição HTTP. Por isso, os recursos apropriados ficam disponíveis durante a renderização de templates.

Entretanto, tentar acessar esses objetos fora de um contexto apropriado pode provocar erros como `RuntimeError: Working outside of request context` ou `RuntimeError: Working outside of application context`.

Esses erros não indicam necessariamente um problema no template. Podem indicar que o código Python está tentando acessar um recurso contextual fora do ciclo de vida esperado pelo Flask.

### 20.9. Evitando lógica excessiva nos templates

Embora o Jinja permita acessar informações da requisição, da sessão e da aplicação, isso não significa que toda decisão deva ser implementada no template.

Por exemplo, é aceitável utilizar:

```
{% if current_user.is_authenticated %}
    <p>Bem-vindo.</p>
{% endif %}
```

Entretanto, decisões de autorização, validações de negócio e operações que modificam dados devem ser executadas e verificadas no lado Python.

O template deve receber os dados necessários e controlar principalmente como eles serão apresentados.

### 20.10. Referência rápida

|Nome|Disponibilidade|Uso típico|
|---|---|---|
|`request`|Integração padrão do Flask|Consultar informações da requisição|
|`session`|Integração padrão do Flask|Consultar dados da sessão|
|`g`|Integração padrão do Flask|Acessar dados temporários do contexto|
|`config`|Integração padrão do Flask|Consultar configurações selecionadas|
|`url_for()`|Integração padrão do Flask|Gerar URLs de endpoints|
|`get_flashed_messages()`|Integração padrão do Flask|Recuperar mensagens temporárias|
|`current_user`|Disponibilizado pelo Flask-Login quando integrado|Consultar o usuário atual|
|Variáveis de `render_template()`|Passadas pela rota|Disponibilizar dados específicos da página|
|Variáveis de context processors|Registradas na aplicação ou no Blueprint|Compartilhar valores no contexto dos templates|

---
## 21. Depuração de templates, rastreamento de erros e organização do ambiente Jinja

Quando uma aplicação Flask cresce, os templates passam a envolver herança, macros, includes, filtros personalizados, context processors e dados recebidos de diferentes rotas.

Nesse cenário, um erro visual nem sempre está no arquivo HTML que apresenta o problema. Ele pode ter origem na rota, em um template pai, em uma macro importada ou em uma variável que não foi disponibilizada corretamente.

Esta seção aborda como identificar a origem desses problemas e organizar o ambiente Jinja para facilitar a manutenção.

### 21.1. Como o Jinja identifica os templates

Por padrão, o Flask procura templates no diretório `templates` associado à aplicação.

Uma estrutura possível:

```
meu_projeto/
├── app.py
├── templates/
│   ├── base.html
│   ├── index.html
│   ├── painel.html
│   ├── partials/
│   │   └── navbar.html
│   └── macros/
│       └── formularios.html
└── static/
    ├── css/
    └── js/
```

A chamada:

```
return render_template("painel.html")
```

procura `painel.html` na estrutura de templates configurada.

Para um arquivo dentro de uma subpasta:

```
return render_template("partials/navbar.html")
```

O caminho é relativo ao diretório de templates.

O mesmo princípio se aplica a `include` e `extends`:

```
{% extends "base.html" %}
{% include "partials/navbar.html" %}
```

Os caminhos precisam corresponder à organização real dos arquivos e ao carregador de templates utilizado.

### 21.2. Erro `TemplateNotFound`

Um dos erros mais comuns é:

```
jinja2.exceptions.TemplateNotFound: painel.html
```

Isso indica que o carregador de templates não encontrou o template solicitado.

Possíveis causas:

- O nome do arquivo está incorreto.
    
- O arquivo está em outra pasta.
    
- O caminho passado para `render_template()` não corresponde à estrutura real.
    
- O diretório de templates foi configurado incorretamente.
    
- O arquivo não está presente no ambiente em que a aplicação está sendo executada.
    

Por exemplo, esta estrutura:

```
templates/
└── admin/
    └── painel.html
```

exige:

```
return render_template("admin/painel.html")
```

e não:

```
return render_template("painel.html")
```

A exceção também pode ocorrer dentro de um `include` ou `extends`. Nesse caso, o template principal pode existir, mas algum arquivo referenciado por ele não foi encontrado.

Para investigar, confira primeiro o nome e o caminho do arquivo indicado no erro. Depois, examine os templates que o incluem ou estendem.

### 21.3. Erro `UndefinedError`

Outro erro frequente ocorre quando o template tenta acessar um valor indefinido de maneira incompatível com o comportamento configurado no Jinja.

Exemplo:

```
<p>{{ usuario.nome }}</p>
```

Se `usuario` não estiver disponível, a renderização pode produzir um erro semelhante a:

```
jinja2.exceptions.UndefinedError
```

A causa pode ser uma variável não enviada pela rota:

```
return render_template("perfil.html")
```

quando o template espera receber `usuario`.

A correção é disponibilizar o dado quando ele for necessário:

```
return render_template(
    "perfil.html",
    usuario=usuario
)
```

Se o dado for opcional, o template e a aplicação devem definir explicitamente como lidar com sua ausência.

Por exemplo:

```
{% if usuario is defined %}
    <p>{{ usuario.nome }}</p>
{% else %}
    <p>Usuário não disponível.</p>
{% endif %}
```

Esse teste verifica se a variável existe no contexto. Ele não garante que `usuario` tenha todos os atributos esperados ou que os dados sejam válidos.

### 21.4. Erros de sintaxe Jinja

Os templates também podem falhar por delimitadores ausentes, instruções incompletas ou blocos malformados.

Exemplo incorreto:

```
{% if usuario.is_authenticated %}
    <p>Bem-vindo.</p>
```

O bloco `if` não foi encerrado com `endif`.

Forma corrigida:

```
{% if usuario.is_authenticated %}
    <p>Bem-vindo.</p>
{% endif %}
```

Outro exemplo:

```
{{ usuario.nome
```

A expressão está incompleta porque não possui o delimitador de fechamento `}}`.

Ao investigar erros de sintaxe, procure a primeira linha indicada pelo traceback e confira também as linhas próximas. Dependendo da estrutura do template, a origem real pode estar em um bloco aberto anteriormente.

### 21.5. Entendendo o traceback do Flask e do Jinja

O traceback é o registro da sequência de chamadas que levou a uma exceção.

Em um erro de renderização, ele pode mostrar chamadas de funções do Flask, do Jinja e do código da aplicação.

A estratégia mais útil é identificar:

1. O tipo da exceção.
    
2. A mensagem principal.
    
3. O arquivo de template envolvido, quando indicado.
    
4. A linha associada ao erro.
    
5. A rota ou função Python que iniciou a renderização.
    

Por exemplo, se o traceback indicar um erro em `templates/painel.html`, examine a expressão daquela linha e os valores que a rota forneceu ao template.

Se o erro surgir durante uma macro, confira também os argumentos recebidos por ela.

Se aparecer dentro de um template incluído, investigue o arquivo incluído e a forma como o contexto está sendo transmitido.

Não presuma que toda exceção durante a renderização seja causada pelo HTML. Um filtro personalizado pode lançar uma exceção Python, assim como uma propriedade de um objeto pode executar código e falhar quando acessada.

### 21.6. Modo de depuração do Flask

Durante o desenvolvimento, o modo de depuração pode facilitar a investigação de erros.

```
app.run(debug=True)
```

O modo de depuração fornece informações adicionais sobre exceções e, no servidor de desenvolvimento, permite o recarregamento automático em situações apropriadas.

Entretanto, ele não deve ser habilitado em produção. A apresentação de informações internas de erros pode expor detalhes da aplicação e comprometer sua segurança.

O servidor de desenvolvimento também não deve ser tratado como servidor de produção.

### 21.7. Utilizando `StrictUndefined` para detectar erros

Como visto na seção sobre contexto, o comportamento padrão de variáveis indefinidas pode permitir que determinados erros passem despercebidos.

Durante o desenvolvimento, é possível configurar:

```
from jinja2 import StrictUndefined

app.jinja_env.undefined = StrictUndefined
```

Assim, referências indevidas a variáveis indefinidas tendem a provocar exceções explícitas.

Isso ajuda a identificar situações como:

```
<h1>{{ titulo_pagina }}</h1>
```

quando a rota enviou somente:

```
return render_template(
    "index.html",
    titulo="Início"
)
```

O nome `titulo_pagina` não corresponde ao nome disponibilizado pela rota.

A configuração estrita pode revelar problemas existentes em templates antigos, por isso deve ser validada antes de ser adotada em uma aplicação que já possui muitos templates.

### 21.8. Inspecionando os templates carregados

O ambiente Jinja do Flask pode ser acessado por meio de `app.jinja_env`.

É possível inspecionar um template utilizando o carregador configurado:

```
with app.app_context():
    template = app.jinja_env.get_template("painel.html")
    print(template.name)
```

O contexto de aplicação é utilizado porque a operação ocorre fora do processamento normal de uma requisição.

O método `get_template()` retorna um objeto de template quando o arquivo pode ser localizado e carregado. Se o template não existir, poderá ocorrer `TemplateNotFound`.

Esse recurso pode ajudar em scripts de diagnóstico e testes. Normalmente, não é necessário chamar `get_template()` manualmente em todas as rotas, pois `render_template()` já realiza esse trabalho.

### 21.9. Verificando quais variáveis um template utiliza

O Jinja possui ferramentas de análise que podem identificar nomes utilizados por um template.

Por exemplo:

```
from jinja2 import meta

with app.app_context():
    ambiente = app.jinja_env
    codigo = ambiente.loader.get_source(
        ambiente,
        "painel.html"
    )[0]

    arvore = ambiente.parse(codigo)
    variaveis = meta.find_undeclared_variables(arvore)

    print(variaveis)
```

O exemplo utiliza o carregador para obter o código-fonte, analisa esse código e identifica nomes não declarados explicitamente no template.

O resultado não deve ser interpretado como uma lista perfeita de todos os dados obrigatórios que a rota precisa fornecer. Alguns nomes podem vir de globais, context processors, recursos de extensões ou outros mecanismos. Também existem limitações inerentes à análise estática.

Esse recurso é mais útil como auxílio de diagnóstico do que como substituto da execução de testes.

### 21.10. Organizando filtros, testes e funções globais

Em uma aplicação pequena, é possível registrar todos os recursos diretamente em `app.py`.

Em aplicações maiores, essa organização pode dificultar a manutenção. Uma alternativa é separar os recursos personalizados em um módulo.

Exemplo de estrutura:

```
meu_projeto/
├── app.py
├── template_helpers.py
└── templates/
    └── index.html
```

No arquivo `template_helpers.py`:

```
def maiusculas(valor):
    return str(valor).upper()


def registrar_recursos(app):
    app.add_template_filter(
        maiusculas,
        name="maiusculas"
    )
```

No arquivo de inicialização:

```
from flask import Flask
from template_helpers import registrar_recursos

app = Flask(__name__)

registrar_recursos(app)
```

A função `registrar_recursos()` recebe a aplicação e registra os filtros nela.

Essa organização separa a definição dos auxiliares da inicialização da aplicação, sem exigir que cada filtro seja declarado no arquivo principal.

Em aplicações que utilizam o padrão _application factory_, a mesma função pode receber a instância criada dentro de `create_app()`.

### 21.11. Organização com application factory

O padrão _application factory_ consiste em criar a aplicação por meio de uma função, em vez de depender exclusivamente de uma instância global criada na importação do módulo.

Exemplo:

```
from flask import Flask
from template_helpers import registrar_recursos


def create_app():
    app = Flask(__name__)

    registrar_recursos(app)

    return app
```

O ponto importante para o Jinja é que os filtros, testes, funções globais e outras configurações sejam registrados na instância correta da aplicação.

Se o registro ocorrer em uma instância diferente daquela que está atendendo às requisições, os recursos esperados poderão não estar disponíveis.

Esse padrão também facilita criar aplicações com configurações diferentes em testes e outros ambientes.

### 21.12. Testando a renderização de templates

O Flask fornece um cliente de testes que permite simular requisições sem precisar iniciar um servidor acessível externamente.

Exemplo:

```
def test_pagina_inicial(app):
    cliente = app.test_client()

    resposta = cliente.get("/")

    assert resposta.status_code == 200
    assert b"Página inicial" in resposta.data
```

O exemplo pressupõe que `app` seja uma instância Flask fornecida pelo ambiente de testes.

A primeira verificação confirma que a rota respondeu com o código HTTP esperado. A segunda verifica se determinado texto está presente no corpo da resposta.

Essas verificações podem detectar:

- Templates ausentes.
    
- Erros durante a renderização.
    
- Variáveis que não aparecem como esperado.
    
- Alterações involuntárias no HTML gerado.
    
- Problemas na integração entre rotas e templates.
    

Para aplicações que utilizam macros e herança, também é útil testar as páginas que efetivamente usam esses recursos, e não apenas verificar se os arquivos existem.

### 21.13. Boas práticas de diagnóstico

- Leia a mensagem principal da exceção antes de alterar o template.
    
- Confira o caminho do template indicado no erro.
    
- Compare os nomes usados no template com os dados enviados pela rota.
    
- Verifique os argumentos recebidos por macros e filtros personalizados.
    
- Utilize `StrictUndefined` durante o desenvolvimento quando apropriado.
    
- Não exponha o depurador interativo em produção.
    
- Centralize os registros de recursos personalizados.
    
- Teste a renderização das páginas mais importantes.
    
- Não confunda um template que renderiza sem erro com uma página segura ou uma operação autorizada.
    

---
## 22. Blocos `call`, `caller` e filtros aplicados a blocos de conteúdo

### 22.1. O problema de passar conteúdo para uma macro

Uma macro do Jinja permite criar componentes reutilizáveis. Normalmente, os valores são passados por argumentos:

```
{% macro alerta(titulo, mensagem) %}
    <div class="alerta">
        <h3>{{ titulo }}</h3>
        <p>{{ mensagem }}</p>
    </div>
{% endmacro %}

{{ alerta("Atenção", "Verifique os dados informados.") }}
```

Esse formato funciona quando o componente recebe valores simples, como textos, números ou listas.

Entretanto, pode ser necessário passar um conteúdo HTML mais elaborado para dentro do componente, contendo vários elementos, condições ou expressões Jinja.

Uma solução seria criar uma macro para cada combinação de conteúdo, mas isso reduziria a flexibilidade do componente.

O Jinja oferece os blocos `call` e a variável especial `caller` para resolver esse problema.

### 22.2. O bloco `call`

O bloco `call` permite chamar uma macro e fornecer um bloco de conteúdo como parte da chamada.

Considere esta macro:

```
{% macro painel(titulo) %}
    <section class="painel">
        <h2>{{ titulo }}</h2>

        <div class="painel-conteudo">
            {{ caller() }}
        </div>
    </section>
{% endmacro %}
```

Ela pode ser utilizada assim:

```
{% call painel("Informações do usuário") %}
    <p>Nome: João</p>
    <p>Status: Ativo</p>
    <a href="/perfil">Ver perfil</a>
{% endcall %}
```

O resultado renderizado será semelhante a:

```
<section class="painel">
    <h2>Informações do usuário</h2>

    <div class="painel-conteudo">
        <p>Nome: João</p>
        <p>Status: Ativo</p>
        <a href="/perfil">Ver perfil</a>
    </div>
</section>
```

O conteúdo escrito entre `call` e `endcall` fica disponível para a macro por meio de `caller()`.

A macro controla onde esse conteúdo será inserido. Ela pode chamá-lo dentro de um contêiner, depois de um título ou em outra posição adequada à estrutura do componente.

### 22.3. Como funciona `caller()`

`caller` é uma variável especial disponibilizada no contexto de uma macro chamada com `call`. Ela representa o bloco de conteúdo fornecido pelo código que realizou a chamada.

A expressão:

```
{{ caller() }}
```

executa esse bloco e insere o conteúdo renderizado naquele ponto.

É importante diferenciar os dois papéis:

- `call`: realiza a chamada da macro e fornece o bloco de conteúdo.
    
- `caller`: permite que a macro execute o conteúdo fornecido pelo chamador.
    

Uma macro que utiliza `caller()` sem ter recebido um bloco de chamada compatível pode gerar um erro de execução.

Se uma macro também for utilizada sem `call`, mas o conteúdo for opcional, é possível verificar se `caller` está definido:

```
{% macro painel(titulo) %}
    <section class="painel">
        <h2>{{ titulo }}</h2>

        {% if caller is defined %}
            <div class="painel-conteudo">
                {{ caller() }}
            </div>
        {% endif %}
    </section>
{% endmacro %}
```

Assim, a macro pode ser chamada com ou sem um bloco de conteúdo:

```
{{ painel("Painel vazio") }}
```

Ou:

```
{% call painel("Painel personalizado") %}
    <p>Conteúdo fornecido pelo chamador.</p>
{% endcall %}
```

### 22.4. Passando argumentos para `caller`

O bloco de conteúdo também pode receber argumentos. Isso é útil quando a macro percorre uma coleção e precisa fornecer cada elemento ao conteúdo personalizado.

Exemplo de macro que renderiza uma lista:

```
{% macro lista_personalizada(usuarios) %}
    <ul>
        {% for usuario in usuarios %}
            <li>
                {{ caller(usuario) }}
            </li>
        {% endfor %}
    </ul>
{% endmacro %}
```

A chamada correspondente utiliza uma variável entre parênteses depois de `call`:

```
{% call(usuario) lista_personalizada(usuarios) %}
    <strong>{{ usuario.nome }}</strong>
    <span>{{ usuario.status }}</span>
{% endcall %}
```

O parâmetro `usuario` declarado em `call(usuario)` recebe o argumento enviado pela macro em `caller(usuario)`.

O fluxo é:

1. A macro percorre a coleção `usuarios`.
    
2. Para cada elemento, chama `caller(usuario)`.
    
3. O bloco definido pelo chamador recebe esse elemento.
    
4. O bloco renderiza os campos desejados.
    
5. O resultado é inserido no item da lista.
    

Isso permite reutilizar a lógica de iteração sem obrigar a macro a conhecer a estrutura HTML específica de cada elemento.

A macro pode, por exemplo, controlar a lista e sua organização, enquanto o chamador decide se cada usuário será apresentado com nome, status, links ou outros campos.

### 22.5. Diferença entre macros convencionais e `call`

|Característica|Macro convencional|Macro com `call`|
|---|---|---|
|Entrada principal|Argumentos explícitos|Argumentos e bloco de conteúdo|
|Conteúdo personalizado|Normalmente montado pela própria macro|Fornecido pelo chamador|
|Reutilização|Reaproveita estrutura e lógica|Reaproveita estrutura, permitindo conteúdo variável|
|Uso típico|Botões, campos e elementos padronizados|Painéis, diálogos, listas e contêineres personalizados|

Não é necessário usar `call` para toda macro. Se o componente precisa receber apenas valores e gerar uma estrutura previsível, uma macro convencional costuma ser mais simples.

Use `call` quando o componente precisa controlar a estrutura externa, mas permitir que quem o utiliza determine parte do conteúdo interno.

### 22.6. O bloco `filter`

O bloco `filter` aplica um filtro Jinja ao conteúdo renderizado dentro dele.

Exemplo:

```
{% filter upper %}
    texto convertido para maiúsculas
{% endfilter %}
```

O resultado será:

```
TEXTO CONVERTIDO PARA MAIÚSCULAS
```

Diferentemente de uma expressão como:

```
{{ nome | upper }}
```

que aplica o filtro a uma expressão específica, o bloco `filter` aplica o filtro ao conteúdo inteiro do bloco.

É possível utilizar filtros encadeados:

```
{% filter trim | upper %}
    mensagem de exemplo
{% endfilter %}
```

Nesse caso, o conteúdo renderizado passa primeiro por `trim`, que remove espaços em branco das extremidades, e depois por `upper`, que converte os caracteres para maiúsculas.

O bloco pode conter texto e expressões Jinja:

```
{% filter upper %}
    Usuário: {{ usuario.nome }}
    Status: {{ usuario.status }}
{% endfilter %}
```

O filtro será aplicado ao resultado renderizado dessas expressões.

### 22.7. Quando utilizar `filter` em vez de uma expressão

Uma expressão simples costuma ser suficiente quando o filtro deve atuar sobre uma variável:

```
<p>{{ descricao | upper }}</p>
```

O bloco `filter` é útil quando existe um trecho maior que precisa passar pelo mesmo processamento:

```
{% filter upper %}
    <p>Nome: {{ usuario.nome }}</p>
    <p>Status: {{ usuario.status }}</p>
{% endfilter %}
```

Entretanto, é necessário ter cuidado ao aplicar filtros de texto a conteúdo HTML. Um filtro como `upper` modifica também o texto presente nas tags e nos atributos, podendo produzir resultados indesejados. O bloco `filter` não deve ser tratado como um mecanismo de sanitização de HTML.

### 22.8. Escapamento e segurança ao usar `call`

O conteúdo fornecido a uma macro por meio de `call` continua sujeito às regras de renderização e escapamento do ambiente Jinja.

Em templates HTML renderizados pelo Flask, o escapamento automático normalmente protege valores inseridos por expressões como `{{ usuario.nome }}`.

Por exemplo:

```
{% macro painel(titulo) %}
    <section>
        <h2>{{ titulo }}</h2>
        <div>{{ caller() }}</div>
    </section>
{% endmacro %}

{% call painel(titulo) %}
    <p>{{ descricao }}</p>
{% endcall %}
```

O título e a descrição são renderizados de acordo com as regras de escapamento aplicáveis ao ambiente e aos valores utilizados.

Não utilize `|safe` indiscriminadamente para fazer o conteúdo aparecer como HTML. Esse filtro desativa o escapamento para o valor marcado como seguro e pode permitir injeção de HTML ou JavaScript quando o conteúdo contém dados não confiáveis.

Também não confunda a capacidade de passar um bloco HTML para uma macro com uma garantia de que todo conteúdo produzido será seguro. A segurança depende da origem dos dados, do contexto em que são inseridos e das regras de escapamento aplicadas.

### 22.9. Erros comuns

**Chamar** `**caller()**` **sem utilizar** `**call**`

Se a macro depende de `caller()`, mas é chamada sem um bloco de conteúdo, pode ocorrer um erro porque não existe um bloco chamador disponível.

Quando o conteúdo for opcional, verifique `caller is defined` antes de executá-lo.

**Declarar argumentos incompatíveis**

Se o bloco de chamada fornece argumentos, a macro precisa invocar `caller()` com a quantidade e a ordem esperadas.

Por exemplo, um bloco declarado com `call(usuario)` espera receber um argumento correspondente quando a macro executar `caller(usuario)`.

**Confundir o escopo da macro com o do bloco chamador**

O bloco fornecido em `call` pode utilizar variáveis disponíveis no contexto em que foi definido. Já os argumentos explicitamente enviados por `caller(...)` são a forma adequada de transmitir valores calculados pela macro para esse bloco.

**Usar** `**filter**` **como sanitizador**

Filtros como `upper`, `trim` e `replace` transformam conteúdo, mas não garantem que HTML seja seguro. A proteção contra conteúdo malicioso exige escapamento adequado e validação compatível com o contexto de saída.

### 22.10. Referência rápida

|Sintaxe|Finalidade|
|---|---|
|`{% call macro(...) %}...{% endcall %}`|Chama uma macro fornecendo um bloco de conteúdo|
|`{{ caller() }}`|Renderiza o bloco fornecido pelo chamador|
|`{{ caller(valor) }}`|Renderiza o bloco passando um argumento|
|`{% call(item) macro(...) %}`|Declara a variável que recebe o argumento enviado ao bloco|
|`{% if caller is defined %}`|Verifica se a macro recebeu um bloco chamador|
|`{% filter upper %}...{% endfilter %}`|Aplica um filtro ao conteúdo renderizado do bloco|
|`{% filter trim \| upper %}...{% endfilter %}`|Encadeia filtros sobre o conteúdo do bloco|

---
## 23. Geração de URLs com `url_for()` nos templates Jinja

### 23.1. Por que gerar URLs dinamicamente?

Em aplicações Flask, os templates frequentemente precisam gerar links para outras páginas, enviar formulários ou carregar arquivos estáticos, como CSS, JavaScript e imagens.

Uma possibilidade seria escrever os caminhos diretamente no HTML:

```
<a href="/usuarios">Usuários</a>
```

Entretanto, esse caminho fica vinculado à estrutura atual da aplicação. Se a rota mudar, os templates que utilizam esse caminho também poderão precisar de alterações.

O Flask disponibiliza a função `url_for()` no contexto dos templates Jinja. Ela gera uma URL a partir do nome de um endpoint e dos parâmetros necessários.

Exemplo em Python:

```
from flask import Flask, render_template

app = Flask(__name__)


@app.route("/usuarios")
def listar_usuarios():
    return render_template("usuarios.html")
```

No template:

```
<a href="{{ url_for('listar_usuarios') }}">
    Listar usuários
</a>
```

O Flask identifica o endpoint `listar_usuarios` e gera o caminho correspondente.

A principal vantagem é que o template não precisa conhecer diretamente o caminho definido na rota.

### 23.2. O que é um endpoint no Flask?

Um endpoint é o identificador utilizado pelo Flask para localizar uma função ou um destino de rota.

Por padrão, quando uma rota é associada a uma função de visualização, o nome da função é utilizado como endpoint.

```
@app.route("/perfil")
def perfil():
    return render_template("perfil.html")
```

Nesse exemplo:

- Caminho: `/perfil`
    
- Função Python: `perfil`
    
- Endpoint padrão: `perfil`
    

O template pode gerar a URL usando:

```
<a href="{{ url_for('perfil') }}">Meu perfil</a>
```

O argumento passado para `url_for()` é o nome do endpoint, não necessariamente o caminho da URL.

Isso significa que o seguinte uso está incorreto, considerando a rota anterior:

```
{{ url_for('/perfil') }}
```

O Flask procuraria um endpoint com esse nome e normalmente lançaria um `BuildError` caso ele não existisse.

### 23.3. Passando parâmetros para `url_for()`

Quando uma rota contém parâmetros dinâmicos, eles podem ser enviados como argumentos nomeados.

Considere:

```
@app.route("/usuarios/<int:id>")
def visualizar_usuario(id):
    return render_template("usuario.html", usuario_id=id)
```

A rota exige um parâmetro inteiro chamado `id`.

No template:

```
<a href="{{ url_for('visualizar_usuario', id=42) }}">
    Visualizar usuário
</a>
```

A URL gerada será:

```
/usuarios/42
```

O nome do argumento precisa corresponder ao parâmetro esperado pela rota.

Também é possível utilizar uma variável do contexto Jinja:

```
<a href="{{ url_for('visualizar_usuario', id=usuario.id) }}">
    {{ usuario.nome }}
</a>
```

Nesse caso, o valor de `usuario.id` é utilizado para construir a URL.

Se o template precisar gerar links para vários registros, isso pode ser feito dentro de um laço:

```
{% for usuario in usuarios %}
    <a href="{{ url_for('visualizar_usuario', id=usuario.id) }}">
        {{ usuario.nome }}
    </a>
{% endfor %}
```

Cada link recebe o identificador do respectivo usuário.

### 23.4. Parâmetros de consulta (_query string_)

Nem todo parâmetro precisa fazer parte do caminho da rota. Valores adicionais enviados para `url_for()` que não correspondem a variáveis da rota normalmente são incluídos na query string.

Exemplo:

```
@app.route("/pesquisa")
def pesquisar():
    return render_template("pesquisa.html")
```

No template:

```
<a href="{{ url_for('pesquisar', termo='python', pagina=2) }}">
    Continuar pesquisa
</a>
```

A URL gerada será semelhante a:

```
/pesquisa?termo=python&pagina=2
```

A ordem dos parâmetros da query string pode variar.

Esse mecanismo é útil para filtros, paginação, pesquisas e outras informações que não precisam compor o caminho principal da rota.

Para obter esses valores na aplicação, o Flask permite consultar `request.args`:

```
from flask import request


@app.route("/pesquisa")
def pesquisar():
    termo = request.args.get("termo", "")
    pagina = request.args.get("pagina", default=1, type=int)

    return render_template(
        "pesquisa.html",
        termo=termo,
        pagina=pagina,
    )
```

O uso de `type=int` permite solicitar a conversão do parâmetro para inteiro. Se o valor não puder ser convertido, o resultado será o valor padrão informado.

### 23.5. Gerando URLs para arquivos estáticos

O Flask normalmente disponibiliza arquivos estáticos a partir do diretório `static` da aplicação.

Uma estrutura comum é:

```
meu_projeto/
├── app.py
├── static/
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── app.js
│   └── img/
│       └── logo.png
└── templates/
    └── index.html
```

Em vez de escrever manualmente o caminho dos arquivos, utilize `url_for()` com o endpoint `static`.

Para carregar um CSS:

```
<link
    rel="stylesheet"
    href="{{ url_for('static', filename='css/style.css') }}"
>
```

Para carregar um arquivo JavaScript:

```
<script src="{{ url_for('static', filename='js/app.js') }}"></script>
```

Para exibir uma imagem:

```
<img
    src="{{ url_for('static', filename='img/logo.png') }}"
    alt="Logotipo"
>
```

O argumento `filename` identifica o caminho relativo ao diretório de arquivos estáticos.

Esse padrão permite que o Flask gere a URL apropriada conforme a configuração da aplicação.

### 23.6. Enviando formulários para uma rota

O atributo `action` de um formulário HTML define para onde os dados serão enviados.

Com Jinja, é possível gerar esse destino dinamicamente:

```
@app.route("/login", methods=["GET", "POST"])
def login():
    ...
```

No template:

```
<form method="post" action="{{ url_for('login') }}">
    <label for="email">E-mail</label>
    <input id="email" name="email" type="email" required>

    <label for="senha">Senha</label>
    <input id="senha" name="senha" type="password" required>

    <button type="submit">Entrar</button>
</form>
```

O Flask recebe a requisição na rota `login`, desde que o método HTTP seja permitido e a aplicação implemente o tratamento correspondente.

**Importante:** `url_for()` apenas gera a URL. Ele não implementa autenticação, validação dos dados nem proteção contra CSRF. Essas responsabilidades precisam ser tratadas pela aplicação e, quando aplicável, por mecanismos próprios, como os fornecidos pelo Flask-WTF.

### 23.7. URLs externas e o parâmetro `external`

Por padrão, `url_for()` geralmente gera um caminho relativo à aplicação, como `/perfil`.

Quando é necessário gerar uma URL absoluta, pode-se utilizar `external=True`:

```
{{ url_for('perfil', external=True) }}
```

O resultado será semelhante a:

```
https://exemplo.com/perfil
```

O domínio e o esquema utilizados dependem do contexto da requisição e da configuração do Flask.

Isso pode ser útil para gerar links absolutos em e-mails, notificações ou outros conteúdos consumidos fora da página atual.

Não confunda `external=True` com a validação de um destino externo. O parâmetro não transforma uma URL fornecida pelo usuário em um destino confiável.

### 23.8. Rotas dentro de Blueprints

Quando uma rota pertence a um Blueprint, seu endpoint normalmente inclui o nome do Blueprint como prefixo, separado por um ponto.

Exemplo:

```
from flask import Blueprint, render_template

usuarios_bp = Blueprint("usuarios", __name__)


@usuarios_bp.route("/<int:id>")
def detalhe(id):
    return render_template("usuarios/detalhe.html", usuario_id=id)
```

Depois de registrar o Blueprint na aplicação, o endpoint da rota será:

```
usuarios.detalhe
```

No template:

```
<a href="{{ url_for('usuarios.detalhe', id=usuario.id) }}">
    Abrir perfil
</a>
```

O nome usado em `url_for()` precisa corresponder ao endpoint registrado. Se o nome do Blueprint mudar, os endpoints prefixados também poderão mudar.

Esse mecanismo ajuda a organizar aplicações maiores em módulos, mantendo os templates desacoplados dos caminhos concretos.

### 23.9. Erros comuns ao utilizar `url_for()`

**Endpoint inexistente**

```
{{ url_for('usuario_detalhe') }}
```

Se não existir um endpoint com esse nome, o Flask normalmente lançará um `BuildError`.

Verifique o nome da função, o nome explícito do endpoint, quando configurado, e o prefixo de Blueprint.

**Parâmetro obrigatório ausente**

Se uma rota exige um parâmetro como `id`, a chamada precisa fornecer esse valor:

```
{{ url_for('visualizar_usuario') }}
```

A chamada anterior não é suficiente para uma rota que exige `id`.

**Nome de parâmetro incorreto**

Se a rota espera `id`, passar `usuario_id` não preencherá automaticamente esse parâmetro.

```
{{ url_for('visualizar_usuario', usuario_id=42) }}
```

Como `usuario_id` não corresponde ao parâmetro obrigatório `id`, a construção da URL poderá falhar.

**Caminho de arquivo estático incorreto**

```
{{ url_for('static', filename='style.css') }}
```

Essa chamada procura o arquivo na raiz do diretório estático. Se o arquivo estiver em `static/css/style.css`, o argumento precisa incluir `css/`.

**Confundir geração de URL com autorização**

Um link escondido no template não impede que alguém acesse diretamente a rota. A aplicação precisa verificar permissões no servidor sempre que a rota for acessada.

### 23.10. Referência rápida

|Expressão|Finalidade|
|---|---|
|`url_for('perfil')`|Gera a URL do endpoint `perfil`|
|`url_for('visualizar_usuario', id=42)`|Gera uma URL com parâmetro de rota|
|`url_for('pesquisar', pagina=2)`|Acrescenta um parâmetro à query string, se `pagina` não for parâmetro do caminho|
|`url_for('static', filename='css/style.css')`|Gera a URL de um arquivo estático|
|`url_for('login')`|Pode ser usado no atributo `action` de um formulário|
|`url_for('perfil', external=True)`|Solicita uma URL absoluta|
|`url_for('usuarios.detalhe', id=42)`|Gera a URL de uma rota pertencente a um Blueprint|

---