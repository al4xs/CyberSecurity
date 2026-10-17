# Parte 1 — Fundamentos do Jinja e Templates

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