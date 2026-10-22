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