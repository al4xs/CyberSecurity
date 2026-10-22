## 1. Introdução ao Flask-WTF

O Flask permite receber dados enviados por formulários HTML usando `request`, mas precisamos implementar manualmente tarefas como validar os dados, organizar os campos e apresentar mensagens de erro.

O **Flask-WTF** facilita esse trabalho ao integrar o Flask com a biblioteca WTForms, que permite criar formulários em Python, validar os dados recebidos e gerenciar erros de validação.

Com ele, podemos:

- Criar formulários usando classes Python.
    
- Definir os campos e suas regras de validação.
    
- Verificar se os dados enviados são válidos.
    
- Apresentar mensagens de erro ao usuário.
    
- Integrar formulários com templates Jinja.
    
- Proteger formulários contra ataques CSRF.
    

### 1.1. Flask-WTF, WTForms e Flask

Essas três tecnologias trabalham juntas, mas têm responsabilidades diferentes.

|Tecnologia|Responsabilidade|
|---|---|
|Flask|Gerencia rotas, requisições, respostas e templates.|
|WTForms|Cria formulários e fornece ferramentas de validação.|
|Flask-WTF|Integra WTForms ao Flask e oferece recursos como proteção CSRF.|

O Flask-WTF não substitui o Flask. Ele adiciona recursos para facilitar o trabalho com formulários dentro de uma aplicação Flask.

## 2. Instalando as dependências

Com o ambiente virtual do projeto ativado, execute:

```
python -m pip install Flask-WTF
```

O pacote instala o Flask-WTF e suas dependências necessárias, incluindo o WTForms.

Para conferir a instalação:

```
python -m pip show Flask-WTF
```

Para visualizar as dependências instaladas:

```
python -m pip show WTForms
```

Em um projeto que utiliza um arquivo `requirements.txt`, você também pode registrar a dependência:

```
Flask-WTF
```

Depois, instale as dependências registradas com:

```
python -m pip install -r requirements.txt
```

É recomendável executar esses comandos dentro do ambiente virtual do projeto para evitar misturar as dependências entre diferentes aplicações Python.

## 3. Criando o primeiro formulário

Antes de utilizar o Flask-WTF, vamos observar como um formulário HTML simples pode enviar dados ao Flask.

### 3.1. Formulário HTML

```
<form method="POST">
    <label for="nome">Nome:</label>

    <input
        type="text"
        id="nome"
        name="nome"
    >

    <button type="submit">Enviar</button>
</form>
```

Nesse formulário:

- `method="POST"` indica que os dados serão enviados por uma requisição POST.
    
- `name="nome"` define a chave usada para identificar o campo enviado.
    
- `type="submit"` cria um botão que envia o formulário.
    

Quando o usuário digita um nome e envia o formulário, o navegador envia os dados ao servidor.

### 3.2. Recebendo os dados com Flask

```
from flask import Flask, request

app = Flask(__name__)


@app.route("/formulario", methods=["GET", "POST"])
def formulario():
    if request.method == "POST":
        nome = request.form.get("nome")

        return f"Olá, {nome}!"

    return """
        <form method="POST">
            <input type="text" name="nome">
            <button type="submit">Enviar</button>
        </form>
    """


if __name__ == "__main__":
    app.run(debug=True)
```

Esse exemplo recebe os dados usando `request.form.get("nome")`.

O problema é que ainda não verificamos se o campo foi preenchido, se o valor é adequado ou se outros campos obrigatórios foram enviados. Também estamos escrevendo o HTML diretamente na rota, o que dificulta a organização de uma aplicação maior.

O Flask-WTF ajuda a resolver essas questões, separando a definição e a validação do formulário da lógica da rota e da apresentação HTML.

## 4. Criando um formulário com Flask-WTF

Vamos criar um formulário usando uma classe Python.

Estrutura inicial:

```
meu_projeto/
├── app.py
└── templates/
    └── formulario.html
```

### 4.1. Definindo a classe do formulário

No arquivo `app.py`:

```
from flask import Flask
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField

app = Flask(__name__)


class NomeForm(FlaskForm):
    nome = StringField("Nome")
    enviar = SubmitField("Enviar")


if __name__ == "__main__":
    app.run(debug=True)
```

Vamos entender os elementos principais.

```
from flask_wtf import FlaskForm
```

Importa a classe `FlaskForm`, que será utilizada como base para os formulários da aplicação.

```
from wtforms import StringField, SubmitField
```

Importa dois tipos de campo:

- `StringField`: representa um campo destinado à entrada de texto.
    
- `SubmitField`: representa um campo de envio do formulário.
    

```
class NomeForm(FlaskForm):
```

Define um formulário chamado `NomeForm`, herdando de `FlaskForm`.

A herança permite que o formulário utilize os recursos oferecidos pela classe-base, incluindo o processamento dos dados recebidos e a validação.

```
nome = StringField("Nome")
```

Define um campo chamado `nome`. O texto `"Nome"` é o rótulo associado ao campo.

```
enviar = SubmitField("Enviar")
```

Define o botão de envio do formulário.

Observe que estamos declarando os campos como atributos da classe, em vez de criar os elementos HTML manualmente dentro da rota.

### 4.2. Criando o template HTML

Crie `templates/formulario.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Formulário</title>
</head>
<body>
    <h1>Meu formulário</h1>

    <form method="POST">
        {{ form.hidden_tag() }}

        {{ form.nome.label }}
        {{ form.nome() }}

        {{ form.enviar() }}
    </form>
</body>
</html>
```

O template utiliza a sintaxe Jinja para acessar os campos definidos na classe Python.

```
{{ form.nome.label }}
```

Renderiza o rótulo do campo.

```
{{ form.nome() }}
```

Renderiza o elemento HTML correspondente ao campo de texto.

```
{{ form.enviar() }}
```

Renderiza o botão de envio.

Já `form.hidden_tag()` renderiza os campos ocultos necessários ao formulário, incluindo o token CSRF quando a proteção está habilitada. Vamos estudar essa proteção com mais detalhes na Parte 4.

## 5. Integrando o formulário à rota

Agora vamos conectar a classe `NomeForm` ao Flask.

Atualize o arquivo `app.py`:

```
from flask import Flask, render_template
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField

app = Flask(__name__)

app.config["SECRET_KEY"] = "chave-de-desenvolvimento"


class NomeForm(FlaskForm):
    nome = StringField("Nome")
    enviar = SubmitField("Enviar")


@app.route("/formulario", methods=["GET", "POST"])
def formulario():
    form = NomeForm()

    if form.validate_on_submit():
        nome = form.nome.data
        return f"Olá, {nome}!"

    return render_template(
        "formulario.html",
        form=form,
    )


if __name__ == "__main__":
    app.run(debug=True)
```

### 5.1. Configurando a SECRET_KEY

```
app.config["SECRET_KEY"] = "chave-de-desenvolvimento"
```

A chave secreta é utilizada por recursos do Flask e extensões que precisam assinar ou proteger determinados dados, incluindo a proteção CSRF do Flask-WTF.

O valor apresentado é apenas ilustrativo. Em uma aplicação real, utilize uma chave longa, aleatória e mantida fora do código-fonte, por exemplo, em uma variável de ambiente.

Não publique a chave real em um repositório.

### 5.2. Criando uma instância do formulário

```
form = NomeForm()
```

Cria um objeto do formulário. Ele será utilizado para acessar os campos, processar os dados da requisição e verificar a validade do envio.

### 5.3. Validando o envio

```
if form.validate_on_submit():
```

O método `validate_on_submit()` verifica se a requisição atual é um envio de formulário compatível com POST e se a validação configurada para os campos foi aprovada.

Como ainda não adicionamos validadores ao campo `nome`, não estamos exigindo que o usuário preencha esse campo. Essa regra será adicionada na próxima parte.

### 5.4. Acessando os dados

```
nome = form.nome.data
```

O atributo `.data` contém o valor processado do campo.

Isso evita que a rota precise buscar manualmente o campo em `request.form`. O Flask-WTF e o WTForms organizam esse processamento dentro do objeto do formulário.

### 5.5. Renderizando o template

```
return render_template(
    "formulario.html",
    form=form,
)
```

Envia o objeto `form` ao template, permitindo que o Jinja renderize os campos definidos na classe Python.

Se o formulário não for enviado ou se a validação falhar, o template será renderizado novamente. Quando adicionarmos validadores e mensagens de erro, poderemos mostrar ao usuário o que precisa ser corrigido.

## 6. Fluxo de funcionamento

```
Usuário acessa /formulario
          |
          v
Flask cria NomeForm()
          |
          v
Template renderiza os campos
          |
          v
Usuário preenche e envia
          |
          v
Flask-WTF processa os dados
          |
          v
validate_on_submit()
          |
          v
Dados válidos? ---- Não ----> Renderiza o formulário novamente
          |
         Sim
          |
          v
A aplicação utiliza os dados
```

O fluxo separa três responsabilidades:

- **Classe do formulário:** define os campos e suas regras.
    
- **Rota Flask:** decide o que fazer com o envio.
    
- **Template Jinja:** apresenta os campos e os resultados na interface.
    

Essa separação torna os formulários mais fáceis de manter e permite reutilizar regras de validação em diferentes partes da aplicação.

---
## 7. Entendendo os campos dos formulários

Na parte anterior, criamos um formulário usando `FlaskForm`, `StringField` e `SubmitField`.

Agora vamos aprofundar os tipos de campos disponíveis no WTForms e entender como configurá-los para diferentes situações.

Cada campo representa um tipo de dado que o usuário pode preencher ou enviar. A escolha depende da informação que queremos receber.

Por exemplo:

- Nome: `StringField`.
    
- Senha: `PasswordField`.
    
- E-mail: `EmailField`.
    
- Número inteiro: `IntegerField`.
    
- Caixa de seleção: `BooleanField`.
    
- Data: `DateField`.
    
- Área de texto: `TextAreaField`.
    
- Opções de uma lista: `SelectField`.
    
- Botão de envio: `SubmitField`.
    

Os campos ficam disponíveis no módulo `wtforms` e podem ser utilizados nas classes que herdam de `FlaskForm`.

## 8. Principais tipos de campos

### 8.1. StringField — texto simples

```
from flask_wtf import FlaskForm
from wtforms import StringField


class CadastroForm(FlaskForm):
    nome = StringField("Nome")
```

O `StringField` representa um campo de texto de uma linha.

Normalmente, ele será renderizado como:

```
<input type="text" name="nome" id="nome">
```

O WTForms gera os atributos HTML a partir da definição do campo e das opções fornecidas.

Esse campo pode ser usado para nomes, títulos, apelidos e outras informações textuais curtas.

### 8.2. PasswordField — senhas

```
from wtforms import PasswordField


class LoginForm(FlaskForm):
    senha = PasswordField("Senha")
```

O `PasswordField` representa um campo de senha. No HTML, ele é renderizado como um campo do tipo `password`, que oculta visualmente os caracteres digitados.

Isso **não criptografa a senha nem a protege durante o envio por si só**. A aplicação ainda precisa utilizar HTTPS e tratar as senhas corretamente no servidor, por exemplo, armazenando hashes seguros em vez das senhas originais.

### 8.3. EmailField — endereço de e-mail

```
from wtforms import EmailField


class ContatoForm(FlaskForm):
    email = EmailField("E-mail")
```

O `EmailField` representa um campo de entrada de e-mail, normalmente renderizado com `type="email"`.

Ele ajuda o navegador a apresentar uma interface apropriada para esse tipo de dado, mas o campo sozinho não garante que o endereço seja válido ou que pertença ao usuário.

Para validar o formato do e-mail no servidor, podemos adicionar um validador, como `Email`, que será estudado na próxima parte.

### 8.4. IntegerField — números inteiros

```
from wtforms import IntegerField


class IdadeForm(FlaskForm):
    idade = IntegerField("Idade")
```

O `IntegerField` tenta converter o valor recebido para um número inteiro Python.

Se o usuário enviar um valor que não possa ser convertido, o processamento do campo poderá gerar um erro de validação.

A conversão para inteiro não significa que o número seja adequado para a aplicação. Por exemplo, ainda precisamos definir se uma idade negativa deve ser aceita.

### 8.5. BooleanField — valor verdadeiro ou falso

```
from wtforms import BooleanField


class PreferenciasForm(FlaskForm):
    receber_novidades = BooleanField(
        "Desejo receber novidades"
    )
```

O `BooleanField` representa um campo booleano, normalmente renderizado como uma caixa de seleção.

Ele é útil para opções que podem estar marcadas ou desmarcadas, como uma preferência de interface.

Não confunda uma caixa de seleção com uma confirmação automaticamente confiável: o servidor deve processar e validar os dados de acordo com a finalidade do campo.

### 8.6. DateField — datas

```
from wtforms import DateField


class EventoForm(FlaskForm):
    data_evento = DateField("Data do evento")
```

O `DateField` processa o valor como uma data Python, normalmente um objeto `datetime.date`.

Podemos especificar o formato esperado usando `format`:

```
data_evento = DateField(
    "Data do evento",
    format="%Y-%m-%d",
)
```

O formato `%Y-%m-%d` corresponde a ano, mês e dia, como `2026-10-22`.

Se o valor não puder ser convertido para o formato esperado, a validação do campo poderá falhar.

### 8.7. TextAreaField — texto com várias linhas

```
from wtforms import TextAreaField


class ArtigoForm(FlaskForm):
    conteudo = TextAreaField("Conteúdo")
```

O `TextAreaField` representa uma área de texto com várias linhas.

É adequado para descrições, comentários, artigos e outras informações que podem ocupar mais de uma linha.

No HTML, ele corresponde ao elemento `<textarea>`.

### 8.8. SelectField — seleção de opções

```
from wtforms import SelectField


class PerfilForm(FlaskForm):
    linguagem = SelectField(
        "Linguagem",
        choices=[
            ("python", "Python"),
            ("javascript", "JavaScript"),
            ("c", "C"),
        ],
    )
```

O `SelectField` permite selecionar uma opção entre as disponíveis.

Cada item de `choices` contém dois valores:

```
("python", "Python")
```

- `"python"` é o valor associado à opção.
    
- `"Python"` é o texto apresentado ao usuário.
    

Assim, o usuário vê a palavra Python, enquanto o valor processado pelo formulário é `"python"`.

O servidor ainda precisa verificar se a opção recebida é permitida. Em um formulário convencional, o WTForms utiliza as escolhas configuradas para validar o valor selecionado.

### 8.9. SubmitField — botão de envio

```
from wtforms import SubmitField


class CadastroForm(FlaskForm):
    enviar = SubmitField("Cadastrar")
```

O `SubmitField` representa um botão de envio do formulário.

Ele não substitui a lógica de processamento na rota. Depois que o usuário envia os dados, o Flask precisa decidir o que fazer com eles.

## 9. Configurando atributos HTML dos campos

Além do rótulo, podemos configurar atributos HTML para controlar a apresentação e o comportamento dos campos no navegador.

Por exemplo:

```
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField


class CadastroForm(FlaskForm):
    nome = StringField(
        "Nome",
        render_kw={
            "placeholder": "Digite seu nome",
            "class": "campo-nome",
            "autocomplete": "name",
        },
    )

    enviar = SubmitField("Cadastrar")
```

O argumento `render_kw` recebe um dicionário com atributos HTML que serão aplicados ao campo renderizado.

Nesse exemplo:

- `placeholder` apresenta uma dica dentro do campo.
    
- `class` define uma classe CSS.
    
- `autocomplete` informa ao navegador que o campo pode receber um nome salvo.
    

Esses atributos ajudam na interface, mas não substituem a validação no servidor. Um usuário pode enviar uma requisição sem utilizar a interface do navegador.

Também podemos definir alguns atributos diretamente no template:

```
{{ form.nome(class="campo-nome", placeholder="Digite seu nome") }}
```

Essa abordagem é útil quando os atributos variam conforme o contexto de apresentação.

## 10. Acessando os valores dos campos

Depois que o formulário é processado, podemos acessar seus valores pelo atributo `.data`.

Considere:

```
class CadastroForm(FlaskForm):
    nome = StringField("Nome")
    idade = IntegerField("Idade")
```

Na rota:

```
form = CadastroForm()

if form.validate_on_submit():
    nome = form.nome.data
    idade = form.idade.data
```

Nesse caso:

- `form.nome.data` contém o nome processado.
    
- `form.idade.data` contém a idade convertida para inteiro, se o processamento for bem-sucedido.
    

O valor de um campo não precisa ser necessariamente uma string. O tipo depende do campo utilizado e do processamento realizado pelo WTForms.

Por isso, é importante conhecer o tipo de campo antes de utilizar seu valor em cálculos, comparações ou operações no banco de dados.

## 11. Exemplo integrado com vários campos

Vamos criar um formulário de cadastro simples com nome, e-mail, senha e uma linguagem preferida.

No arquivo `app.py`:

```
from flask import Flask, render_template
from flask_wtf import FlaskForm
from wtforms import (
    StringField,
    EmailField,
    PasswordField,
    SelectField,
    SubmitField,
)

app = Flask(__name__)
app.config["SECRET_KEY"] = "chave-de-desenvolvimento"


class CadastroForm(FlaskForm):
    nome = StringField("Nome")
    email = EmailField("E-mail")
    senha = PasswordField("Senha")

    linguagem = SelectField(
        "Linguagem preferida",
        choices=[
            ("python", "Python"),
            ("javascript", "JavaScript"),
            ("c", "C"),
        ],
    )

    enviar = SubmitField("Cadastrar")


@app.route("/cadastro", methods=["GET", "POST"])
def cadastro():
    form = CadastroForm()

    if form.validate_on_submit():
        nome = form.nome.data
        email = form.email.data
        linguagem = form.linguagem.data

        return (
            f"Cadastro recebido: {nome}, "
            f"{email}, linguagem: {linguagem}"
        )

    return render_template(
        "cadastro.html",
        form=form,
    )


if __name__ == "__main__":
    app.run(debug=True)
```

Crie `templates/cadastro.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Cadastro</title>
</head>
<body>
    <h1>Cadastro</h1>

    <form method="POST">
        {{ form.hidden_tag() }}

        <div>
            {{ form.nome.label }}
            {{ form.nome() }}
        </div>

        <div>
            {{ form.email.label }}
            {{ form.email() }}
        </div>

        <div>
            {{ form.senha.label }}
            {{ form.senha() }}
        </div>

        <div>
            {{ form.linguagem.label }}
            {{ form.linguagem() }}
        </div>

        {{ form.enviar() }}
    </form>
</body>
</html>
```

Esse exemplo demonstra como definir vários campos, renderizá-los no template e acessar os valores processados na rota.

**Importante:** o exemplo é apenas didático. Ainda não adicionamos regras para exigir o preenchimento dos campos, validar o formato do e-mail ou definir requisitos para a senha. Também não estamos salvando o cadastro no banco de dados. Essas etapas exigem validação e tratamento adicionais.

A senha não é exibida na resposta e, em uma aplicação real, não deve ser armazenada em texto puro.

---
## 12. Entendendo os validadores do WTForms

Na parte anterior, criamos formulários com campos como `StringField`, `EmailField`, `PasswordField` e `SelectField`.

Porém, definir um campo não significa que os dados recebidos sejam válidos.

Por exemplo, este campo aceita texto, mas não exige que o usuário preencha alguma coisa:

```
nome = StringField("Nome")
```

Se quisermos exigir o preenchimento, precisamos adicionar um validador.

Os **validadores** são regras que verificam se os dados enviados atendem às condições definidas pela aplicação.

Eles são importados do módulo `wtforms.validators` e podem ser associados aos campos por meio do argumento `validators`.

## 13. DataRequired — exigindo o preenchimento

O `DataRequired` verifica se o campo contém um valor considerado preenchido.

```
from flask_wtf import FlaskForm
from wtforms import StringField
from wtforms.validators import DataRequired


class CadastroForm(FlaskForm):
    nome = StringField(
        "Nome",
        validators=[DataRequired()],
    )
```

Observe:

```
validators=[DataRequired()]
```

O argumento `validators` recebe uma lista de validadores. Utilizamos `DataRequired()` porque estamos criando uma instância do validador.

Se o usuário enviar o formulário sem preencher o campo, a validação falhará.

Também podemos definir uma mensagem personalizada:

```
nome = StringField(
    "Nome",
    validators=[
        DataRequired(message="O nome é obrigatório.")
    ],
)
```

Assim, quando a validação falhar, o formulário poderá apresentar a mensagem definida.

**Importante:** `DataRequired` verifica o valor após o processamento do campo e considera inválidos valores falsy, como uma string vazia. Para verificar se o valor bruto enviado não está vazio, sem depender desse comportamento, existe `InputRequired`. A escolha depende do tipo de campo e da regra desejada.

## 14. Length — limitando o tamanho do texto

O `Length` permite definir o tamanho mínimo e máximo de um valor textual.

```
from wtforms.validators import DataRequired, Length


nome = StringField(
    "Nome",
    validators=[
        DataRequired(),
        Length(min=3, max=50),
    ],
)
```

Nesse exemplo:

- `min=3`: exige pelo menos três caracteres.
    
- `max=50`: permite no máximo cinquenta caracteres.
    

Também podemos personalizar a mensagem:

```
nome = StringField(
    "Nome",
    validators=[
        DataRequired(),
        Length(
            min=3,
            max=50,
            message="O nome deve ter entre 3 e 50 caracteres.",
        ),
    ],
)
```

Os validadores são executados na ordem em que aparecem na lista. Nesse exemplo, primeiro verificamos se o campo foi preenchido e depois verificamos o comprimento.

## 15. Email — validando o formato do e-mail

O `Email` verifica se o valor informado possui um formato de endereço de e-mail reconhecido pelo validador.

```
from wtforms import EmailField
from wtforms.validators import DataRequired, Email


email = EmailField(
    "E-mail",
    validators=[
        DataRequired(),
        Email(message="Informe um e-mail válido."),
    ],
)
```

A presença de `EmailField` não substitui o validador `Email`.

O campo define o tipo de entrada; o validador verifica o formato do valor recebido no servidor.

Mesmo quando o endereço passa nessa verificação, isso não comprova que a caixa postal existe ou pertence ao usuário. Para confirmar a posse do endereço, uma aplicação pode enviar um link de verificação.

## 16. EqualTo — comparando dois campos

O `EqualTo` verifica se o valor de um campo é igual ao valor de outro campo do formulário.

É útil em cadastros que pedem a confirmação da senha.

```
from flask_wtf import FlaskForm
from wtforms import PasswordField
from wtforms.validators import DataRequired, EqualTo


class CadastroForm(FlaskForm):
    senha = PasswordField(
        "Senha",
        validators=[DataRequired()],
    )

    confirmar_senha = PasswordField(
        "Confirmar senha",
        validators=[
            DataRequired(),
            EqualTo(
                "senha",
                message="As senhas não são iguais.",
            ),
        ],
    )
```

O argumento `"senha"` indica o nome do outro campo que será comparado.

Se os valores forem diferentes, a validação falhará.

A confirmação serve para evitar erros de digitação, mas não substitui a política de segurança da senha nem o armazenamento por hash.

## 17. NumberRange — limitando valores numéricos

O `NumberRange` permite estabelecer limites para valores numéricos.

```
from wtforms import IntegerField
from wtforms.validators import DataRequired, NumberRange


class IdadeForm(FlaskForm):
    idade = IntegerField(
        "Idade",
        validators=[
            DataRequired(),
            NumberRange(
                min=18,
                max=120,
                message="A idade deve estar entre 18 e 120.",
            ),
        ],
    )
```

Nesse exemplo, o campo precisa conter um valor numérico processável e a validação exige que ele esteja entre 18 e 120, inclusive.

O `IntegerField` cuida da conversão para inteiro; o `NumberRange` verifica o intervalo.

## 18. Optional — permitindo campos opcionais

Nem todo campo precisa ser obrigatório.

O `Optional` interrompe a validação do campo quando a entrada é considerada vazia, limpando os erros produzidos até aquele ponto.

```
from wtforms import StringField
from wtforms.validators import Optional, Length


apelido = StringField(
    "Apelido",
    validators=[
        Optional(),
        Length(max=30),
    ],
)
```

Nesse exemplo, o usuário pode deixar o campo vazio. Se preencher o apelido, o valor deverá respeitar o limite de trinta caracteres.

A ordem é importante: `Optional()` aparece antes de `Length()` para que uma entrada vazia não seja rejeitada pelo validador de comprimento.

## 19. Combinando vários validadores

Um campo pode ter várias regras simultaneamente.

```
from wtforms import StringField
from wtforms.validators import DataRequired, Length


usuario = StringField(
    "Usuário",
    validators=[
        DataRequired(message="Informe um usuário."),
        Length(
            min=3,
            max=20,
            message="O usuário deve ter entre 3 e 20 caracteres.",
        ),
    ],
)
```

Nesse caso, o valor precisa:

1. Estar preenchido.
    
2. Ter pelo menos três caracteres.
    
3. Ter no máximo vinte caracteres.
    

Cada validador verifica uma condição específica. Isso facilita a manutenção porque podemos alterar ou combinar regras sem colocar todas as verificações dentro da rota.

## 20. Acessando os erros de validação

Quando a validação falha, o WTForms disponibiliza as mensagens de erro por meio do atributo `errors` de cada campo.

Considere este formulário:

```
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired, Length


class CadastroForm(FlaskForm):
    nome = StringField(
        "Nome",
        validators=[
            DataRequired(message="O nome é obrigatório."),
            Length(min=3, message="Digite pelo menos 3 caracteres."),
        ],
    )

    enviar = SubmitField("Cadastrar")
```

Na rota:

```
@app.route("/cadastro", methods=["GET", "POST"])
def cadastro():
    form = CadastroForm()

    if form.validate_on_submit():
        return f"Cadastro recebido: {form.nome.data}"

    return render_template(
        "cadastro.html",
        form=form,
    )
```

Se o usuário enviar um valor inválido, `validate_on_submit()` retornará `False` e o formulário será renderizado novamente.

No template, podemos acessar os erros do campo:

```
<form method="POST">
    {{ form.hidden_tag() }}

    {{ form.nome.label }}
    {{ form.nome() }}

    {% for erro in form.nome.errors %}
        <p>{{ erro }}</p>
    {% endfor %}

    {{ form.enviar() }}
</form>
```

A expressão `form.nome.errors` contém uma lista de mensagens de erro associadas ao campo.

O laço `for` percorre essas mensagens e apresenta cada uma delas na página.

## 21. Exibindo erros de todos os campos

Em formulários maiores, não é necessário escrever um laço separado para cada campo. Podemos percorrer os campos e seus erros.

```
<form method="POST">
    {{ form.hidden_tag() }}

    {% for campo in form %}
        {% if campo.type != "CSRFTokenField" %}
            <div>
                {{ campo.label }}
                {{ campo() }}

                {% for erro in campo.errors %}
                    <p>{{ erro }}</p>
                {% endfor %}
            </div>
        {% endif %}
    {% endfor %}
</form>
```

O laço externo percorre os campos do formulário, enquanto o interno percorre os erros de cada campo.

A condição evita renderizar novamente o campo CSRF, já que `hidden_tag()` já inclui os campos ocultos necessários.

Essa abordagem é útil para formulários dinâmicos, mas em interfaces mais elaboradas pode ser melhor renderizar cada campo explicitamente para controlar a estrutura HTML e o estilo.

## 22. Validação no navegador e no servidor

Alguns campos podem incluir atributos HTML, como `required`, `minlength` e `maxlength`.

Por exemplo:

```
nome = StringField(
    "Nome",
    render_kw={
        "required": True,
        "minlength": 3,
        "maxlength": 50,
    },
    validators=[
        DataRequired(),
        Length(min=3, max=50),
    ],
)
```

Esses atributos ajudam o navegador a orientar o usuário, mas podem ser ignorados por quem envia uma requisição diretamente.

Por isso, a validação feita no servidor continua sendo necessária.

Uma aplicação confiável não depende exclusivamente das verificações realizadas pelo HTML ou pelo JavaScript do navegador.

---
## 23. Entendendo o ataque CSRF

CSRF significa _Cross-Site Request Forgery_, ou falsificação de requisição entre sites.

É um tipo de ataque em que alguém tenta induzir o navegador de uma pessoa autenticada a enviar uma requisição indesejada para uma aplicação na qual ela já possui uma sessão ativa.

Imagine que você esteja conectado ao painel administrativo do seu portfólio Flask. O navegador possui um cookie de sessão que permite à aplicação reconhecer você como administrador.

Se uma rota permitir publicar um artigo por meio de uma requisição POST sem verificar se ela foi realmente iniciada pelo seu formulário legítimo, um site malicioso poderá tentar induzir seu navegador a enviar uma requisição para essa rota.

O navegador pode enviar automaticamente os cookies associados ao destino, dependendo das condições da requisição e das configurações dos cookies. Isso pode permitir que uma ação seja executada com a sessão da vítima.

O problema não é simplesmente o uso do método POST. **POST, sozinho, não impede CSRF.**

### 23.1. Exemplo de uma rota vulnerável

Considere uma rota que publica um artigo:

```
@app.route("/admin/publicar", methods=["POST"])
@login_required
def publicar():
    titulo = request.form.get("titulo")

    # Publica o artigo...
    return "Artigo publicado!"
```

Essa rota verifica se o usuário está autenticado, mas não verifica se a requisição contém uma proteção contra CSRF.

O `login_required` protege o acesso de usuários não autenticados. Ele não comprova que a requisição foi iniciada por um formulário legítimo da aplicação.

Uma proteção CSRF ajuda a verificar essa origem esperada da ação.

## 24. Como funciona a proteção CSRF

Uma abordagem comum é utilizar um token CSRF: um valor associado ao contexto da sessão ou do formulário, que o servidor verifica quando recebe uma requisição que altera dados.

O fluxo simplificado é:

```
Usuário acessa o formulário
          |
          v
Servidor disponibiliza um token CSRF
          |
          v
Formulário envia o token junto com os dados
          |
          v
Servidor verifica o token recebido
          |
          v
Token válido? ---- Não ----> Requisição rejeitada
          |
         Sim
          |
          v
Aplicação continua o processamento
```

O token dificulta que um site externo produza uma requisição válida sem conhecer o valor esperado.

A proteção depende de uma implementação correta: o token precisa ser verificado no servidor, e a chave usada para protegê-lo deve permanecer secreta.

Um token CSRF também não substitui autenticação, autorização, validação dos dados ou HTTPS.

## 25. Configurando a proteção CSRF no Flask-WTF

O Flask-WTF oferece proteção CSRF integrada.

Em uma aplicação que utiliza `FlaskForm`, a proteção pode ser habilitada configurando uma chave secreta:

```
from flask import Flask

app = Flask(__name__)

app.config["SECRET_KEY"] = "chave-de-desenvolvimento"
```

A `SECRET_KEY` é utilizada para proteger dados assinados e participa da proteção CSRF padrão do Flask-WTF.

O valor apresentado é apenas ilustrativo. Em produção, gere uma chave aleatória forte e armazene-a em uma variável de ambiente, em vez de deixá-la no código-fonte.

Por exemplo:

```
import os

app.config["SECRET_KEY"] = os.environ["SECRET_KEY"]
```

Nesse caso, a variável de ambiente `SECRET_KEY` precisa estar configurada antes de iniciar a aplicação.

Por padrão, o Flask-WTF habilita a proteção CSRF para formulários `FlaskForm`. Também é possível configurar a proteção global para requisições que alteram dados, inclusive quando a aplicação não utiliza `FlaskForm` em todas as rotas.

## 26. Utilizando o token no template

Quando utilizamos um formulário criado com `FlaskForm`, podemos inserir os campos ocultos com:

```
<form method="POST">
    {{ form.hidden_tag() }}

    {{ form.nome.label }}
    {{ form.nome() }}

    {{ form.enviar() }}
</form>
```

O método `hidden_tag()` renderiza os campos ocultos do formulário, incluindo o campo do token CSRF quando a proteção está habilitada.

Também podemos renderizar o token explicitamente:

```
<form method="POST">
    <input
        type="hidden"
        name="csrf_token"
        value="{{ form.csrf_token.current_token }}"
    >

    {{ form.nome.label }}
    {{ form.nome() }}

    {{ form.enviar() }}
</form>
```

Escolha uma das abordagens. Não é necessário incluir as duas no mesmo formulário.

A primeira costuma ser mais prática porque também renderiza outros campos ocultos definidos no formulário.

**Importante:** não basta colocar um campo chamado `csrf_token` no HTML. O valor precisa corresponder a um token válido, e o servidor precisa verificar esse token.

## 27. Protegendo formulários HTML que não utilizam FlaskForm

Em algumas situações, podemos ter um formulário HTML simples que não foi definido por uma classe `FlaskForm`.

Se a proteção CSRF global estiver habilitada, podemos utilizar o `CSRFProtect` para proteger as requisições compatíveis.

No arquivo `app.py`:

```
from flask import Flask
from flask_wtf.csrf import CSRFProtect

app = Flask(__name__)

app.config["SECRET_KEY"] = "chave-de-desenvolvimento"

csrf = CSRFProtect(app)
```

A chamada `CSRFProtect(app)` registra a proteção CSRF global na aplicação.

Com isso, requisições que alteram dados, como POST, PUT, PATCH e DELETE, precisam fornecer um token válido, conforme a configuração da extensão.

### 27.1. Gerando um token para o template

O Flask-WTF disponibiliza a função `generate_csrf()`:

```
from flask_wtf.csrf import generate_csrf

@app.route("/formulario")
def formulario():
    return render_template(
        "formulario.html",
        csrf_token=generate_csrf(),
    )
```

No template:

```
<form method="POST" action="{{ url_for('salvar') }}">
    <input
        type="hidden"
        name="csrf_token"
        value="{{ csrf_token }}"
    >

    <label for="titulo">Título:</label>
    <input type="text" id="titulo" name="titulo">

    <button type="submit">Salvar</button>
</form>
```

Quando o formulário for enviado, o token acompanhará os outros campos da requisição.

O `CSRFProtect` verificará o token recebido antes de permitir que a requisição protegida continue normalmente.

## 28. Integrando CSRF com uma rota POST

Considere o exemplo completo de um formulário HTML simples protegido globalmente:

```
import os

from flask import Flask, render_template, request
from flask_wtf.csrf import CSRFProtect, generate_csrf

app = Flask(__name__)

app.config["SECRET_KEY"] = os.environ["SECRET_KEY"]

csrf = CSRFProtect(app)


@app.route("/formulario")
def formulario():
    return render_template(
        "formulario.html",
        csrf_token=generate_csrf(),
    )


@app.route("/salvar", methods=["POST"])
def salvar():
    titulo = request.form.get("titulo", "")

    return f"Título recebido: {titulo}"


if __name__ == "__main__":
    app.run(debug=True)
```

No ambiente, configure uma chave secreta antes de iniciar o servidor. Para um teste local no shell, você pode gerar uma com:

```
export SECRET_KEY="$(python -c 'import secrets; print(secrets.token_hex(32))')"
```

Depois, execute:

```
python app.py
```

A rota `/formulario` disponibiliza o token. O formulário envia esse valor para `/salvar`, e a proteção global verifica a requisição.

Se uma requisição POST protegida não apresentar um token válido, a extensão normalmente rejeitará a requisição com um erro HTTP 400.

Esse exemplo é didático. Uma aplicação real deve validar os dados recebidos e implementar o comportamento adequado após salvar as informações.

## 29. CSRF e o logout do Flask-Login

Na anotação de Flask-Login, utilizamos uma rota de logout que aceita somente POST:

```
@app.route("/logout", methods=["POST"])
@login_required
def logout():
    logout_user()
    return redirect(url_for("login"))
```

O formulário correspondente é:

```
<form method="POST" action="{{ url_for('logout') }}">
    <button type="submit">Sair</button>
</form>
```

Quando o Flask-WTF está configurado para proteger esse formulário, precisamos incluir o token CSRF.

Se a rota utiliza um `FlaskForm`, podemos usar `form.hidden_tag()`. Se a proteção global está habilitada e o formulário não utiliza `FlaskForm`, podemos incluir o token gerado por `generate_csrf()`.

Isso adiciona uma proteção contra requisições de logout forjadas, evitando depender apenas do fato de a rota aceitar POST.

## 30. O que acontece quando o token é inválido?

Se o token estiver ausente, incorreto ou expirado, o Flask-WTF poderá rejeitar a requisição.

Durante o desenvolvimento, é importante distinguir um erro CSRF de um erro na lógica da rota. Verifique se:

- A aplicação está configurada com uma `SECRET_KEY`.
    
- O formulário realmente envia o token.
    
- O token é gerado pela proteção CSRF da aplicação.
    
- A sessão e o token não ficaram inválidos após alterações de configuração.
    
- O formulário envia os dados ao endpoint esperado.
    

Não resolva erros CSRF desabilitando a proteção indiscriminadamente. Em vez disso, identifique a causa e corrija a geração ou o envio do token.

---
## 31. Integrando Flask-WTF com Flask-Login

Até aqui, aprendemos a criar formulários com `FlaskForm`, validar campos e proteger requisições com CSRF.

Agora vamos integrar esses recursos a um fluxo comum de uma aplicação Flask: cadastro de usuários, login e logout.

A ideia é separar as responsabilidades:

- **Flask-WTF:** define os campos e valida os dados enviados.
    
- **Flask-Login:** gerencia a autenticação e a sessão do usuário.
    
- **Flask:** recebe as requisições, executa as rotas e renderiza os templates.
    
- **Banco de dados:** armazena os usuários de forma persistente.
    

Neste exemplo, utilizaremos um dicionário em memória para manter o foco nos formulários e na autenticação. Os dados serão perdidos quando a aplicação for reiniciada. Em um projeto real, o cadastro deve ser persistido no banco de dados.

## 32. Criando os formulários de cadastro e login

Vamos criar um arquivo `forms.py` para separar a definição dos formulários da lógica das rotas.

Estrutura do projeto:

```
meu_projeto/
├── app.py
├── forms.py
└── templates/
    ├── cadastro.html
    └── login.html
```

No arquivo `forms.py`:

```
from flask_wtf import FlaskForm
from wtforms import (
    StringField,
    PasswordField,
    SubmitField,
)
from wtforms.validators import (
    DataRequired,
    Email,
    EqualTo,
    Length,
)


class CadastroForm(FlaskForm):
    nome = StringField(
        "Nome",
        validators=[
            DataRequired(message="Informe seu nome."),
            Length(
                min=3,
                max=50,
                message="O nome deve ter entre 3 e 50 caracteres.",
            ),
        ],
    )

    email = StringField(
        "E-mail",
        validators=[
            DataRequired(message="Informe seu e-mail."),
            Email(message="Informe um e-mail válido."),
        ],
    )

    senha = PasswordField(
        "Senha",
        validators=[
            DataRequired(message="Informe uma senha."),
            Length(
                min=8,
                message="A senha deve ter pelo menos 8 caracteres.",
            ),
        ],
    )

    confirmar_senha = PasswordField(
        "Confirmar senha",
        validators=[
            DataRequired(message="Confirme sua senha."),
            EqualTo(
                "senha",
                message="As senhas não coincidem.",
            ),
        ],
    )

    enviar = SubmitField("Cadastrar")


class LoginForm(FlaskForm):
    email = StringField(
        "E-mail",
        validators=[
            DataRequired(message="Informe seu e-mail."),
            Email(message="Informe um e-mail válido."),
        ],
    )

    senha = PasswordField(
        "Senha",
        validators=[
            DataRequired(message="Informe sua senha."),
        ],
    )

    enviar = SubmitField("Entrar")
```

### 32.1. Por que separar os formulários?

O arquivo `forms.py` concentra os campos e suas regras de validação.

Assim, as rotas não precisam repetir todas as regras de cada campo. Se precisarmos alterar o tamanho mínimo da senha, por exemplo, podemos fazer isso na definição do formulário.

O `CadastroForm` possui quatro campos de entrada, enquanto o `LoginForm` utiliza somente e-mail e senha.

Observe que os validadores verificam os dados recebidos, mas ainda não confirmam se o e-mail já está cadastrado ou se a senha corresponde à senha armazenada. Essas verificações pertencem à lógica de negócio da aplicação.

## 33. Criando a aplicação com Flask-Login

No arquivo `app.py`:

```
from flask import (
    Flask,
    render_template,
    redirect,
    url_for,
    flash,
)
from flask_login import (
    LoginManager,
    UserMixin,
    login_user,
    logout_user,
    login_required,
    current_user,
)
from werkzeug.security import (
    generate_password_hash,
    check_password_hash,
)

from forms import CadastroForm, LoginForm


app = Flask(__name__)
app.config["SECRET_KEY"] = "chave-de-desenvolvimento"

login_manager = LoginManager(app)
login_manager.login_view = "login"


usuarios = {}
proximo_id = 1


class Usuario(UserMixin):
    def __init__(self, id, nome, email, senha_hash):
        self.id = id
        self.nome = nome
        self.email = email
        self.senha_hash = senha_hash


@login_manager.user_loader
def carregar_usuario(user_id):
    try:
        return usuarios.get(int(user_id))
    except (TypeError, ValueError):
        return None
```

### 33.1. Configurando o LoginManager

O `LoginManager` integra o Flask-Login à aplicação.

A configuração:

```
login_manager.login_view = "login"
```

indica qual endpoint deve ser utilizado para redirecionar usuários não autenticados quando tentam acessar uma rota protegida.

### 33.2. Criando o modelo simplificado de usuário

A classe `Usuario` herda de `UserMixin`, que fornece propriedades e métodos esperados pelo Flask-Login.

O atributo `senha_hash` armazena o hash da senha, e não a senha original.

Neste exemplo, os objetos ficam no dicionário `usuarios`. Essa estrutura serve apenas para demonstrar o fluxo e não oferece persistência, controle de concorrência ou gerenciamento adequado de usuários para produção.

### 33.3. Recuperando o usuário da sessão

O Flask-Login utiliza a função registrada com `user_loader` para recuperar o usuário associado à sessão.

```
@login_manager.user_loader
def carregar_usuario(user_id):
    try:
        return usuarios.get(int(user_id))
    except (TypeError, ValueError):
        return None
```

O identificador recebido é convertido para inteiro e utilizado para consultar o dicionário.

Se o identificador for inválido ou não existir, a função retorna `None`, indicando que o usuário não foi encontrado.

## 34. Implementando a rota de cadastro

Acrescente ao `app.py`:

```
@app.route("/cadastro", methods=["GET", "POST"])
def cadastro():
    global proximo_id

    form = CadastroForm()

    if form.validate_on_submit():
        email = form.email.data.strip().lower()

        if any(
            usuario.email == email
            for usuario in usuarios.values()
        ):
            flash("Esse e-mail já está cadastrado.", "error")

            return render_template(
                "cadastro.html",
                form=form,
            )

        usuario = Usuario(
            id=proximo_id,
            nome=form.nome.data.strip(),
            email=email,
            senha_hash=generate_password_hash(form.senha.data),
        )

        usuarios[proximo_id] = usuario
        proximo_id += 1

        flash("Cadastro realizado com sucesso!", "success")

        return redirect(url_for("login"))

    return render_template(
        "cadastro.html",
        form=form,
    )
```

### 34.1. Validando o formulário

```
if form.validate_on_submit():
```

Verifica se o formulário foi enviado por POST e se os validadores configurados foram aprovados.

Se os dados forem inválidos, o código não cria o usuário e renderiza novamente o formulário.

### 34.2. Normalizando o e-mail

```
email = form.email.data.strip().lower()
```

`strip()` remove espaços em branco das extremidades, enquanto `lower()` converte as letras para minúsculas.

Isso ajuda a manter uma representação consistente dos e-mails nesse exemplo. Em uma aplicação real, a política de normalização e a unicidade precisam ser definidas e aplicadas também no banco de dados.

### 34.3. Verificando e-mails duplicados

```
if any(
    usuario.email == email
    for usuario in usuarios.values()
):
```

O `any()` retorna `True` quando pelo menos um usuário possui o e-mail informado.

Se o endereço já existir, a aplicação apresenta uma mensagem e não cria outro usuário com o mesmo e-mail.

Em um sistema real, essa verificação precisa ser reforçada por uma restrição de unicidade no banco de dados. Uma consulta anterior, isoladamente, não impede duplicações em acessos concorrentes.

### 34.4. Armazenando o hash da senha

```
senha_hash=generate_password_hash(form.senha.data)
```

Gera um hash apropriado para armazenamento de senhas usando as ferramentas do Werkzeug.

A aplicação deve armazenar esse hash, nunca a senha original. Durante o login, a senha fornecida será comparada com o hash por meio de `check_password_hash()`.

### 34.5. Utilizando flash e redirect

```
flash("Cadastro realizado com sucesso!", "success")
return redirect(url_for("login"))
```

`flash()` registra uma mensagem temporária para ser apresentada em uma requisição posterior.

`redirect()` redireciona o navegador, e `url_for("login")` gera a URL do endpoint de login.

Esse padrão evita que uma atualização da página repita automaticamente o envio do formulário de cadastro.

## 35. Criando o template de cadastro

Crie `templates/cadastro.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Cadastro</title>
</head>
<body>
    <h1>Criar conta</h1>

    {% with mensagens = get_flashed_messages(with_categories=true) %}
        {% for categoria, mensagem in mensagens %}
            <p>{{ mensagem }}</p>
        {% endfor %}
    {% endwith %}

    <form method="POST">
        {{ form.hidden_tag() }}

        <div>
            {{ form.nome.label }}
            {{ form.nome() }}

            {% for erro in form.nome.errors %}
                <p>{{ erro }}</p>
            {% endfor %}
        </div>

        <div>
            {{ form.email.label }}
            {{ form.email() }}

            {% for erro in form.email.errors %}
                <p>{{ erro }}</p>
            {% endfor %}
        </div>

        <div>
            {{ form.senha.label }}
            {{ form.senha() }}

            {% for erro in form.senha.errors %}
                <p>{{ erro }}</p>
            {% endfor %}
        </div>

        <div>
            {{ form.confirmar_senha.label }}
            {{ form.confirmar_senha() }}

            {% for erro in form.confirmar_senha.errors %}
                <p>{{ erro }}</p>
            {% endfor %}
        </div>

        {{ form.enviar() }}
    </form>

    <p>
        Já possui conta?
        <a href="{{ url_for('login') }}">Entrar</a>
    </p>
</body>
</html>
```

O template apresenta os campos e percorre os erros de cada um.

A chamada `form.hidden_tag()` inclui o token CSRF do formulário. Como o formulário utiliza `FlaskForm`, não precisamos criar manualmente o campo oculto do token.

A mensagem flash é renderizada por meio de `get_flashed_messages(with_categories=true)`, que permite receber tanto a mensagem quanto sua categoria.

Em uma interface com CSS, a categoria pode ser usada para aplicar estilos diferentes a mensagens de sucesso e de erro.

## 36. Implementando a rota de login

Acrescente ao `app.py`:

```
@app.route("/login", methods=["GET", "POST"])
def login():
    form = LoginForm()

    if form.validate_on_submit():
        email = form.email.data.strip().lower()
        senha = form.senha.data

        usuario = next(
            (
                usuario
                for usuario in usuarios.values()
                if usuario.email == email
            ),
            None,
        )

        if (
            usuario is not None
            and check_password_hash(usuario.senha_hash, senha)
        ):
            login_user(usuario)

            flash("Login realizado com sucesso!", "success")

            return redirect(url_for("perfil"))

        flash("E-mail ou senha incorretos.", "error")

    return render_template(
        "login.html",
        form=form,
    )
```

### 36.1. Procurando o usuário

O `next()` percorre os usuários até encontrar o primeiro que tenha o e-mail informado.

O segundo argumento, `None`, define o valor retornado quando nenhum usuário correspondente é encontrado.

Essa consulta é suficiente para o exemplo em memória. Com um banco de dados, o ideal seria buscar o usuário diretamente por meio de uma consulta apropriada.

### 36.2. Conferindo a senha

```
check_password_hash(usuario.senha_hash, senha)
```

Compara a senha recebida com o hash armazenado.

A função retorna `True` se a senha corresponder ao hash e `False` caso contrário.

Não é necessário gerar um novo hash da senha recebida e compará-lo diretamente com o hash armazenado, pois algoritmos seguros de hash de senhas normalmente utilizam salt e parâmetros próprios.

### 36.3. Iniciando a sessão autenticada

```
login_user(usuario)
```

Informa ao Flask-Login que o usuário foi autenticado e inicia a sessão de login gerenciada pela extensão.

A aplicação não precisa criar manualmente um cookie de autenticação para reproduzir o funcionamento básico do Flask-Login.

## 37. Criando o template de login

Crie `templates/login.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
    <h1>Entrar</h1>

    {% with mensagens = get_flashed_messages(with_categories=true) %}
        {% for categoria, mensagem in mensagens %}
            <p>{{ mensagem }}</p>
        {% endfor %}
    {% endwith %}

    <form method="POST">
        {{ form.hidden_tag() }}

        <div>
            {{ form.email.label }}
            {{ form.email() }}

            {% for erro in form.email.errors %}
                <p>{{ erro }}</p>
            {% endfor %}
        </div>

        <div>
            {{ form.senha.label }}
            {{ form.senha() }}

            {% for erro in form.senha.errors %}
                <p>{{ erro }}</p>
            {% endfor %}
        </div>

        {{ form.enviar() }}
    </form>

    <p>
        Ainda não possui conta?
        <a href="{{ url_for('cadastro') }}">Cadastrar-se</a>
    </p>
</body>
</html>
```

Esse template segue a mesma estrutura do cadastro, mas utiliza somente os campos definidos em `LoginForm`.

As mensagens de erro dos campos são apresentadas separadamente das mensagens flash. Essa distinção é útil porque os erros de validação pertencem ao formulário atual, enquanto as mensagens flash podem comunicar resultados de uma operação anterior.

## 38. Protegendo o perfil e implementando o logout

Acrescente ao `app.py`:

```
@app.route("/perfil")
@login_required
def perfil():
    return render_template("perfil.html")


@app.route("/logout", methods=["POST"])
@login_required
def logout():
    logout_user()

    flash("Você saiu da sua conta.", "success")

    return redirect(url_for("login"))
```

Crie `templates/perfil.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Perfil</title>
</head>
<body>
    <h1>Olá, {{ current_user.nome }}!</h1>

    <p>E-mail: {{ current_user.email }}</p>

    <form method="POST" action="{{ url_for('logout') }}">
        <input
            type="hidden"
            name="csrf_token"
            value="{{ csrf_token() }}"
        >

        <button type="submit">Sair</button>
    </form>
</body>
</html>
```

### 38.1. Protegendo o perfil

O decorador `login_required` impede que usuários não autenticados acessem a rota protegida.

O template pode acessar `current_user` porque o Flask-Login disponibiliza esse objeto durante as requisições.

### 38.2. Encerrando a sessão

`logout_user()` encerra a sessão autenticada gerenciada pelo Flask-Login.

A rota aceita somente POST, e o formulário de logout inclui um token CSRF.

Nesse template, `csrf_token()` está disponível porque a proteção CSRF do Flask-WTF está configurada. Se você não configurou essa proteção globalmente, essa chamada não estará disponível automaticamente nesse formato.

O uso de POST evita que um simples acesso por link GET encerre a sessão. O token CSRF acrescenta proteção contra requisições forjadas.

## 39. Completando a aplicação

O `app.py` deve conter as importações, a configuração, a classe `Usuario`, o `user_loader` e as rotas de cadastro, login, perfil e logout apresentadas nesta parte.

No final do arquivo, utilize:

```
if __name__ == "__main__":
    app.run(debug=True)
```

Para executar:

```
python app.py
```

Acesse `/cadastro` para criar uma conta, depois `/login` para entrar e `/perfil` para visualizar os dados do usuário autenticado.

**Limitações importantes deste exemplo:**

- Os usuários ficam em memória e desaparecem quando o processo é reiniciado.
    
- A chave secreta está ilustrada com um valor fixo; em produção, deve ser obtida de uma variável de ambiente.
    
- O servidor de desenvolvimento com `debug=True` não deve ser utilizado como servidor de produção.
    
- O exemplo não implementa limitação de tentativas de login, recuperação de senha, verificação de e-mail ou gerenciamento de permissões administrativas.
    
- Uma aplicação real deve usar um banco de dados, impor unicidade de e-mail no banco e configurar HTTPS e cookies de sessão seguros.
    

---
