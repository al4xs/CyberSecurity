# 1. O que é Flask-Login?

O Flask-Login é uma biblioteca que ajuda a controlar o login dos usuários em aplicações Flask.

Imagine que você está desenvolvendo um site com uma página inicial e um painel administrativo. Você quer que qualquer pessoa possa visitar a página inicial, mas somente usuários que fizeram login possam entrar no painel.

Você poderia programar todo esse sistema manualmente, mas precisaria criar mecanismos para manter o usuário conectado, descobrir quem está acessando cada página e impedir o acesso de pessoas que não fizeram login.

O Flask-Login facilita esse trabalho, fornecendo funções e recursos prontos para lidar com essas tarefas.

Entre os principais recursos estão:

- `LoginManager`: conecta o Flask-Login à aplicação e configura o comportamento do login.
    
- `login_user()`: informa ao Flask-Login que um usuário fez login com sucesso.
    
- `logout_user()`: encerra o login do usuário atual.
    
- `current_user`: permite acessar informações sobre quem está usando a aplicação naquela requisição.
    
- `login_required`: impede que pessoas não autenticadas acessem determinadas páginas.
    
- `user_loader`: procura o usuário correspondente ao identificador guardado na sessão.
    

**Importante:** Flask-Login não cria um sistema completo de cadastro e login sozinho. Ele não cria tabelas no banco de dados, não salva senhas e não verifica automaticamente se a senha digitada está correta. Essas partes precisam ser implementadas pela aplicação.

---

# 2. Diferença entre autenticação e autorização

Antes de trabalhar com Flask-Login, precisamos entender dois conceitos importantes de segurança: autenticação e autorização.

## 2.1. Autenticação

A autenticação responde à pergunta:

**Quem é você?**

Ela serve para verificar a identidade de quem está tentando entrar na aplicação.

Imagine um formulário de login que solicita e-mail e senha. Quando o usuário envia os dados, a aplicação verifica se aquele e-mail existe e se a senha corresponde à senha cadastrada.

Se os dados estiverem corretos, a aplicação poderá considerar o usuário autenticado.

```
Usuário informa e-mail e senha
             │
             ▼
    Aplicação verifica
        as credenciais
             │
       ┌─────┴─────┐
       ▼           ▼
    Válidas     Inválidas
       │           │
       ▼           ▼
    Login       Acesso
  autorizado    recusado
```

O Flask-Login entra em ação depois que a aplicação verifica as credenciais: ele ajuda a manter o usuário conectado e reconhecê-lo nas próximas requisições.

Ele não confere automaticamente se o e-mail e a senha estão corretos. Essa verificação fica por conta do código que implementa o login.

## 2.2. Autorização

A autorização responde à pergunta:

**O que você tem permissão para fazer?**

Uma pessoa pode estar autenticada e ainda assim não ter permissão para realizar determinadas ações.

Por exemplo:

- Uma pessoa que não fez login não pode acessar o painel administrativo.
    
- Um usuário autenticado pode visualizar seu próprio perfil.
    
- Um administrador pode acessar funcionalidades administrativas.
    
- Um usuário comum não deve conseguir excluir a conta de outra pessoa sem autorização.
    

Veja a diferença:

|Conceito|O que faz|
|---|---|
|Autenticação|Verifica quem está acessando a aplicação.|
|Autorização|Define o que essa pessoa pode acessar ou fazer.|

O Flask-Login ajuda a controlar o acesso de usuários autenticados. Entretanto, ele não define sozinho quem é administrador ou quais permissões cada usuário possui.

Por exemplo, `@login_required` exige que o usuário esteja autenticado, mas não exige que ele seja administrador.

---

# 3. Como funciona uma sessão de login?

Imagine que você entra em um site, informa suas credenciais e consegue acessar sua conta.

Depois, clica em outra página do mesmo site. Você não precisa digitar sua senha novamente a cada clique.

Como o site sabe que você continua conectado?

A resposta envolve as sessões.

O protocolo HTTP não mantém automaticamente a identidade do usuário entre requisições diferentes. Cada vez que o navegador solicita uma página, a aplicação precisa de uma maneira de reconhecer quem está fazendo aquela solicitação.

## 3.1. Sessões no Flask

Por padrão, o Flask utiliza cookies de sessão para guardar informações que permitem manter o estado entre requisições.

Um cookie é um pequeno conjunto de dados que o navegador armazena e envia ao servidor nas requisições apropriadas.

O Flask assina o cookie de sessão usando uma chave secreta. Essa assinatura permite detectar alterações feitas sem a assinatura válida.

Isso não significa que o conteúdo do cookie esteja criptografado. Portanto, não coloque senhas ou outros dados secretos diretamente na sessão padrão do Flask.

O Flask-Login utiliza esse mecanismo para ajudar a reconhecer o usuário que fez login.

Veja uma representação simplificada:

```
┌─────────────────────┐
│ Navegador do usuário│
└──────────┬──────────┘
           │
           │ 1. Envia e-mail e senha
           ▼
┌─────────────────────┐
│ Aplicação Flask     │
│                     │
│ Verifica os dados   │
│ Registra o login    │
└──────────┬──────────┘
           │
           │ 2. Envia a resposta
           │    com o cookie
           ▼
┌─────────────────────┐
│ Navegador armazena  │
│ o cookie de sessão  │
└──────────┬──────────┘
           │
           │ 3. Solicita outra página
           │    enviando o cookie
           ▼
┌─────────────────────┐
│ Aplicação Flask     │
│                     │
│ Reconhece a sessão  │
│ Identifica usuário  │
└─────────────────────┘
```

O desenho mostra o funcionamento de maneira simplificada. Na prática, o Flask e o Flask-Login cuidam de detalhes como a assinatura da sessão e a recuperação da identidade do usuário.

## 3.2. Diferença entre cookie, sessão e usuário autenticado

Esses três conceitos estão relacionados, mas não significam a mesma coisa.

- **Cookie:** é um dado armazenado pelo navegador e enviado ao servidor quando as regras de envio são atendidas.
    
- **Sessão:** é o mecanismo que permite manter informações entre diferentes requisições.
    
- **Usuário autenticado:** é a pessoa cuja identidade a aplicação reconheceu depois de um login válido.
    

Por exemplo, o navegador pode enviar um cookie de sessão em uma requisição, mas isso não significa que o usuário tenha permissão para acessar todas as páginas.

A aplicação ainda precisa verificar a sessão e aplicar as regras de acesso.

---

# 4. Instalando Flask-Login

Antes de utilizar a biblioteca, precisamos instalá-la no ambiente Python do projeto.

Se você estiver usando um ambiente virtual chamado `.venv`, ative-o primeiro:

```
source .venv/bin/activate
```

Depois, instale o Flask-Login:

```
python -m pip install Flask-Login
```

Para conferir se a biblioteca foi instalada:

```
python -m pip show Flask-Login
```

Também podemos testar a importação diretamente:

```
python -c "from flask_login import LoginManager; print('Flask-Login instalado')"
```

Se aparecer a mensagem `Flask-Login instalado`, a importação funcionou.

## 4.1. Registrando a dependência do projeto

Se o projeto utiliza um arquivo `requirements.txt`, adicione a biblioteca a ele.

```
Flask
Flask-Login
```

Assim, outra pessoa poderá instalar as dependências do projeto com:

```
python -m pip install -r requirements.txt
```

Se o arquivo já contiver outras bibliotecas, mantenha-as e acrescente apenas a dependência necessária.

---

# 5. Criando o LoginManager

O `LoginManager` é o componente responsável por conectar o Flask-Login à aplicação e configurar como ele deve lidar com o login.

Vamos criar uma aplicação simples para entender sua utilização.

Estrutura inicial:

```
projeto/
├── app.py
└── templates/
    └── login.html
```

No arquivo `app.py`:

```
from flask import Flask
from flask_login import LoginManager

app = Flask(__name__)

app.config["SECRET_KEY"] = "chave-apenas-para-desenvolvimento"

login_manager = LoginManager(app)

login_manager.login_view = "login"


@app.route("/")
def home():
    return "Página inicial"


@app.route("/login")
def login():
    return "Página de login"


if __name__ == "__main__":
    app.run(debug=True)
```

Esse código configura o Flask-Login, mas ainda não implementa um login funcional. Por enquanto, as rotas apenas retornam textos.

Vamos entender as partes principais.

## 5.1. Criando a aplicação Flask

```
app = Flask(__name__)
```

Essa linha cria a aplicação Flask.

É por meio do objeto `app` que registramos rotas, definimos configurações e integramos bibliotecas adicionais.

## 5.2. Inicializando o LoginManager

```
login_manager = LoginManager(app)
```

Essa linha conecta o Flask-Login à aplicação.

Pense no `LoginManager` como o componente que organiza o funcionamento da autenticação dentro do Flask. Ele permite configurar, por exemplo, para qual página o usuário será enviado quando tentar acessar uma rota protegida sem ter feito login.

Também existe outra forma de inicialização, útil quando a aplicação é organizada em vários arquivos:

```
login_manager = LoginManager()

login_manager.init_app(app)
```

Nesse formato, criamos o gerenciador primeiro e o conectamos à aplicação depois.

As duas formas são válidas. A segunda costuma ser útil em projetos que utilizam fábricas de aplicação ou precisam inicializar as extensões separadamente.

## 5.3. Configurando a SECRET_KEY

```
app.config["SECRET_KEY"] = "chave-apenas-para-desenvolvimento"
```

A `SECRET_KEY` é uma chave secreta utilizada pelo Flask para assinar dados como o cookie de sessão.

Essa assinatura ajuda a detectar se alguém modificou o conteúdo do cookie sem possuir a chave correta.

O valor mostrado no exemplo serve apenas para estudo. Em uma aplicação real, não devemos utilizar uma chave previsível nem deixar uma chave de produção exposta no código-fonte.

Uma alternativa é carregar a chave de uma variável de ambiente:

```
import os

app.config["SECRET_KEY"] = os.environ["SECRET_KEY"]
```

Nesse caso, a variável `SECRET_KEY` precisa estar definida antes de iniciar a aplicação.

Se ela não estiver definida, o código gerará um erro. Isso ajuda a evitar que a aplicação inicie silenciosamente sem a configuração necessária.

**Atenção:** quem obtiver a chave secreta poderá comprometer a segurança dos dados de sessão assinados por ela. Mantenha-a privada e utilize uma chave forte, gerada de forma segura.

## 5.4. Configurando a página de login

```
login_manager.login_view = "login"
```

Essa configuração informa ao Flask-Login qual página deve ser aberta quando alguém tenta acessar uma rota protegida sem estar autenticado.

Por exemplo, se uma pessoa tentar abrir `/admin` sem ter feito login, o Flask-Login poderá redirecioná-la para a página de login.

O valor `"login"` é o nome do endpoint da rota, não necessariamente o endereço escrito no navegador.

Considere:

```
@app.route("/entrar")
def login():
    return "Página de login"
```

Nesse exemplo, o endereço da página é `/entrar`, mas o nome da função é `login`. Por padrão, o endpoint será `login`.

Por isso, a configuração abaixo continua correta:

```
login_manager.login_view = "login"
```

Se a rota utilizar um endpoint personalizado, a configuração deverá utilizar esse nome.

---

# 6. O que é o UserMixin?

O Flask-Login precisa saber algumas informações sobre o usuário, como se ele está autenticado e qual identificador deve ser utilizado para encontrá-lo novamente.

Para facilitar isso, a biblioteca oferece a classe `UserMixin`.

Considere este exemplo:

```
from flask_login import UserMixin


class Usuario(UserMixin):
    def __init__(self, id, email):
        self.id = id
        self.email = email
```

Aqui criamos uma classe chamada `Usuario`, que representa um usuário da aplicação.

O construtor recebe um identificador e um e-mail e os armazena no objeto.

A herança de `UserMixin` fornece métodos e propriedades que o Flask-Login espera encontrar em um objeto de usuário.

Entre eles:

|Propriedade ou método|Explicação simples|
|---|---|
|`is_authenticated`|Informa se o usuário é considerado autenticado.|
|`is_active`|Informa se a conta está ativa e pode ser autenticada.|
|`is_anonymous`|Informa se o objeto representa um usuário anônimo.|
|`get_id()`|Devolve o identificador do usuário como uma string.|

O atributo `id` é importante porque a implementação padrão de `get_id()` utiliza esse identificador.

**Importante:** `UserMixin` não cria usuários no banco de dados, não cadastra e-mails e não verifica senhas. Ele apenas fornece os comportamentos necessários para integrar a classe de usuário ao Flask-Login.

No exemplo, a classe ainda é independente de um banco de dados. Mais adiante, ela poderá ser adaptada para trabalhar com um modelo persistido.

---

# 7. O que é o user_loader?

Imagine que um usuário fez login com sucesso e, depois, acessou outra página.

O Flask-Login precisa descobrir qual usuário está associado à sessão daquela requisição.

Para isso, utilizamos o `user_loader`.

Ele registra uma função que recebe o identificador de um usuário e procura o objeto correspondente.

A estrutura básica é:

```
@login_manager.user_loader
def load_user(user_id):
    ...
```

O nome `load_user` é uma escolha nossa. O importante é registrar a função com `@login_manager.user_loader`.

A função recebe `user_id`, que é o identificador do usuário que precisa ser recuperado.

Ela deve devolver o objeto correspondente se ele existir ou `None` se não conseguir encontrar o usuário.

## 7.1. Exemplo com banco de dados

Se a aplicação utilizar Flask-SQLAlchemy e tiver um modelo chamado `Usuario`, a função poderá ser escrita assim:

```
@login_manager.user_loader
def load_user(user_id):
    return db.session.get(Usuario, int(user_id))
```

Esse exemplo pressupõe que `db` esteja configurado corretamente, que `Usuario` seja um modelo do banco de dados e que sua chave primária seja um número inteiro.

O método `db.session.get()` procura um registro pela chave primária.

A conversão `int(user_id)` transforma o identificador recebido em um número inteiro, pois o Flask-Login trabalha com o identificador como uma string.

Se o modelo utilizar UUID ou outro tipo de identificador, a implementação deverá ser adaptada.

## 7.2. Por que essa função é necessária?

Imagine o seguinte fluxo:

1. O usuário faz login.
    
2. O Flask-Login registra a identidade do usuário na sessão.
    
3. O navegador solicita outra página.
    
4. O Flask-Login recupera o identificador guardado na sessão.
    
5. O `user_loader` procura o usuário correspondente.
    
6. O usuário recuperado pode ser acessado por meio de `current_user`.
    

Em outras palavras, o `user_loader` funciona como uma ponte entre o identificador armazenado na sessão e o objeto de usuário que a aplicação precisa utilizar.

Ele não confere a senha nem realiza um novo login. Sua função é localizar o usuário já associado ao processo de autenticação.

---

# 8. Conhecendo os principais recursos do Flask-Login

Agora que entendemos a configuração inicial, podemos relacionar cada recurso à sua finalidade.

|Recurso|O que faz|
|---|---|
|`LoginManager`|Conecta o Flask-Login à aplicação e configura seu comportamento.|
|`SECRET_KEY`|Permite assinar os dados de sessão do Flask.|
|`login_view`|Informa para qual página enviar quem precisa fazer login.|
|`UserMixin`|Fornece métodos e propriedades esperados para objetos de usuário.|
|`user_loader`|Procura o usuário correspondente ao identificador da sessão.|
|`current_user`|Permite consultar quem está acessando a página atual.|
|`login_user()`|Registra o usuário como autenticado após a validação das credenciais.|
|`logout_user()`|Encerra o login atual.|
|`login_required`|Exige autenticação para acessar uma rota.|

Por exemplo, quando utilizarmos `current_user`, poderemos consultar informações do usuário atual sem precisar procurar manualmente sua identidade em cada rota.

E quando utilizarmos `login_required`, poderemos restringir o acesso a uma página para que somente usuários autenticados consigam entrar.

Esses recursos serão utilizados em conjunto para construir o fluxo de autenticação da aplicação.

---
# 9. Entendendo o fluxo de login na prática

Na parte anterior, conhecemos o `LoginManager`, o `UserMixin` e o `user_loader`. Agora vamos utilizar esses recursos para entender como funciona um login de verdade.

Quando uma pessoa preenche um formulário com e-mail e senha, a aplicação precisa realizar algumas etapas:

1. Receber os dados enviados pelo formulário.
    
2. Procurar o usuário correspondente.
    
3. Verificar se a senha informada está correta.
    
4. Registrar o usuário como autenticado.
    
5. Redirecionar o usuário para uma página apropriada.
    

O Flask-Login participa principalmente da quarta etapa e ajuda a manter a autenticação nas requisições seguintes. As demais etapas são responsabilidade da aplicação.

O fluxo pode ser representado assim:

```
Formulário de login
        │
        ▼
Recebe e-mail e senha
        │
        ▼
Procura o usuário
        │
        ▼
Verifica a senha
        │
   ┌────┴────┐
   ▼         ▼
 Correta   Incorreta
   │         │
   ▼         ▼
login_user  Exibe erro
   │
   ▼
Redireciona
```

Essa separação é importante porque autenticar alguém não é a mesma coisa que simplesmente receber um formulário.

---

# 10. Criando um usuário para testes

Antes de integrar o login a um banco de dados, podemos utilizar usuários armazenados temporariamente em memória.

Essa abordagem é útil para aprender o funcionamento do Flask-Login sem introduzir outras bibliotecas ao mesmo tempo.

Crie ou adapte o arquivo `app.py`:

```
from flask import Flask, render_template, request, redirect, url_for
from flask_login import LoginManager, UserMixin

app = Flask(__name__)

app.config["SECRET_KEY"] = "chave-apenas-para-desenvolvimento"

login_manager = LoginManager(app)
login_manager.login_view = "login"


class Usuario(UserMixin):
    def __init__(self, id, email):
        self.id = id
        self.email = email


usuarios = {
    "1": Usuario("1", "admin@example.com"),
}
```

Aqui criamos uma classe `Usuario` e um dicionário chamado `usuarios`.

O dicionário armazena objetos de usuário utilizando seus identificadores como chaves.

Por exemplo:

```
usuarios["1"]
```

Retorna o objeto associado ao identificador `"1"`.

Esse armazenamento é apenas didático. Os dados desaparecem quando o processo Python é reiniciado, e o exemplo ainda não implementa uma verificação de senha. Não utilize essa estrutura como sistema real de autenticação.

## 10.1. Por que o identificador é uma string?

O Flask-Login espera que `get_id()` retorne um identificador textual.

Como estamos utilizando `UserMixin`, o método padrão utiliza o atributo `id` do objeto. Por isso, definimos o identificador como `"1"`, e não como `1`.

Em aplicações que utilizam um banco de dados, o identificador pode ser convertido para o tipo apropriado durante a consulta.

---

# 11. Implementando o user_loader

Agora precisamos ensinar ao Flask-Login como recuperar o usuário armazenado no dicionário.

Adicione ao `app.py`:

```
@login_manager.user_loader
def load_user(user_id):
    return usuarios.get(user_id)
```

O método `dict.get()` procura uma chave no dicionário.

Se encontrar o identificador, retorna o objeto de usuário correspondente. Se não encontrar, retorna `None`.

Por exemplo:

```
usuarios.get("1")
```

Retorna o usuário de identificador `"1"`.

Já:

```
usuarios.get("999")
```

Retorna `None` se essa chave não existir.

O Flask-Login utiliza essa função quando precisa recuperar o usuário associado à sessão atual.

**Atenção:** como os usuários estão em memória, o login deixa de funcionar depois que o processo é reiniciado. Em uma aplicação real, normalmente substituímos esse dicionário por uma consulta ao banco de dados.

---

# 12. Entendendo current_user

O `current_user` permite acessar o usuário associado à requisição atual sem precisar recuperar manualmente sua identidade em cada rota.

Importe o recurso:

```
from flask_login import current_user
```

Depois, podemos utilizá-lo em uma rota:

```
@app.route("/")
def home():
    if current_user.is_authenticated:
        return f"Olá, {current_user.email}!"

    return "Você não está autenticado."
```

Observe a propriedade:

```
current_user.is_authenticated
```

Ela informa se o usuário atual é considerado autenticado pelo Flask-Login.

Se o usuário estiver autenticado, podemos acessar seus atributos, como `email`.

Caso contrário, o Flask-Login disponibiliza um usuário anônimo, que não representa uma conta autenticada.

## 12.1. Por que current_user é útil?

Sem esse recurso, seria necessário criar manualmente um mecanismo para descobrir quem está acessando cada página.

Com `current_user`, podemos consultar o usuário atual diretamente nas rotas e nos templates.

Por exemplo, em um painel administrativo:

```
@app.route("/perfil")
def perfil():
    if current_user.is_authenticated:
        return f"Perfil de {current_user.email}"

    return "Nenhum usuário autenticado."
```

Esse exemplo demonstra a consulta à identidade, mas ainda não protege a rota com `login_required`. Na próxima seção veremos uma maneira mais direta de fazer isso.

---

# 13. Protegendo rotas com login_required

O Flask-Login fornece o decorador `login_required`, utilizado para restringir o acesso a rotas que exigem autenticação.

Importe-o:

```
from flask_login import login_required
```

Considere uma página administrativa:

```
@app.route("/admin")
@login_required
def admin():
    return "Painel administrativo"
```

O decorador `@login_required` informa que o usuário precisa estar autenticado para acessar essa rota.

Se uma pessoa não autenticada tentar acessar `/admin`, o Flask-Login utilizará a configuração `login_view` para encaminhá-la à página de login.

No nosso exemplo:

```
login_manager.login_view = "login"
```

O endpoint `login` será utilizado como destino.

## 13.1. Entendendo a diferença entre as duas abordagens

Sem `login_required`, poderíamos verificar manualmente a autenticação:

```
@app.route("/admin")
def admin():
    if not current_user.is_authenticated:
        return redirect(url_for("login"))

    return "Painel administrativo"
```

Essa abordagem funciona, mas exige repetir a verificação em cada rota protegida.

Com o decorador:

```
@app.route("/admin")
@login_required
def admin():
    return "Painel administrativo"
```

A intenção fica explícita e o código fica mais simples.

Para rotas que exigem apenas autenticação, prefira `login_required`. Verificações manuais continuam úteis quando a lógica de acesso é mais específica.

**Importante:** `login_required` não verifica se o usuário é administrador. Ele apenas exige que esteja autenticado. As permissões administrativas precisam ser verificadas separadamente.

---

# 14. Implementando o formulário de login

Agora vamos criar uma rota que recebe os dados de um formulário.

No `app.py`, acrescente:

```
from flask import render_template, request, redirect, url_for
```

Se esses imports já estiverem presentes, não é necessário repeti-los.

A rota de login pode ser escrita assim:

```
@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        email = request.form.get("email", "").strip()

        if email == "admin@example.com":
            return "Usuário encontrado."

        return "Usuário não encontrado."

    return render_template("login.html")
```

Esse código é apenas um exercício para entender o envio de formulários. Ele verifica o e-mail, mas não valida senha e não autentica o usuário. Portanto, ainda não é um login funcional.

## 14.1. Criando login.html

Crie o arquivo `templates/login.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login</title>
</head>
<body>
    <h1>Entrar</h1>

    <form method="POST">
        <label for="email">E-mail</label>
        <input
            type="email"
            id="email"
            name="email"
            required
        >

        <button type="submit">Entrar</button>
    </form>
</body>
</html>
```

O formulário utiliza `method="POST"` para enviar os dados ao servidor no corpo da requisição.

O atributo `name` é importante porque é por meio dele que o Flask identifica os campos em `request.form`.

Por exemplo:

```
email = request.form.get("email", "").strip()
```

Essa linha busca o campo `email`, utiliza uma string vazia se ele não existir e remove espaços em branco no início e no final.

O atributo HTML `required` ajuda a impedir o envio de um campo vazio pelo navegador, mas não substitui a validação no servidor. Um cliente pode enviar requisições diretamente, sem utilizar o formulário visual.

---

# 15. Implementando login_user

A função `login_user()` registra um usuário como autenticado.

Entretanto, **ela não verifica a senha por conta própria**. Antes de chamá-la, a aplicação precisa verificar se as credenciais são válidas.

Importe a função:

```
from flask_login import login_user
```

Podemos agora completar o fluxo didático usando o usuário armazenado em memória:

```
@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        email = request.form.get("email", "").strip()

        usuario = next(
            (
                user
                for user in usuarios.values()
                if user.email == email
            ),
            None,
        )

        if usuario is None:
            return "Credenciais inválidas.", 401

        login_user(usuario)

        return redirect(url_for("admin"))

    return render_template("login.html")
```

Esse exemplo localiza o usuário pelo e-mail e chama `login_user()` quando o encontra.

A expressão `next(..., None)` retorna o primeiro usuário correspondente ou `None` caso nenhum seja encontrado.

**Limitação importante:** esse fluxo ainda não é seguro nem adequado para produção. Como não há senha, qualquer pessoa que conheça o e-mail cadastrado poderá autenticar-se. Não utilize esse código como sistema real de login.

Ele serve para demonstrar a função de `login_user()`. Na implementação completa, a aplicação deverá verificar uma senha armazenada com hash seguro antes de autenticar o usuário.

## 15.1. O que acontece ao chamar login_user()?

Quando executamos:

```
login_user(usuario)
```

O Flask-Login registra o usuário como autenticado para as requisições seguintes, utilizando a sessão.

Depois do redirecionamento para `/admin`, o `current_user` poderá representar o usuário autenticado, desde que a função `user_loader` consiga recuperá-lo.

A chamada não cria um registro no banco de dados, não cadastra o usuário e não valida suas credenciais. Ela pressupõe que a aplicação já tenha decidido que o usuário pode fazer login.

---

# 16. Encerrando a sessão com logout_user

O logout permite que o usuário encerre sua sessão de autenticação.

Importe a função:

```
from flask_login import logout_user
```

Crie uma rota:

```
@app.route("/logout", methods=["POST"])
@login_required
def logout():
    logout_user()

    return redirect(url_for("login"))
```

Ao executar `logout_user()`, o Flask-Login encerra o estado de autenticação atual.

Depois, o usuário é redirecionado à página de login.

## 16.1. Por que utilizar POST no logout?

Encerrar uma sessão altera o estado de autenticação do usuário. Por isso, é preferível utilizar uma requisição POST em vez de GET.

Isso evita que uma simples visita a um link, uma prévia automática ou outro mecanismo que faça uma requisição GET provoque o logout inesperadamente.

Em aplicações reais, proteja essa operação contra CSRF. O Flask-WTF oferece ferramentas para isso, assunto que será estudado em uma anotação posterior.

Um formulário para chamar a rota seria:

```
<form method="POST" action="{{ url_for('logout') }}">
    <button type="submit">Sair</button>
</form>
```

Esse formulário ainda precisará incluir o token CSRF quando a proteção estiver habilitada.

---

# 17. Resumo do fluxo implementado

Agora conhecemos os principais passos para construir um fluxo básico de autenticação com Flask-Login.

```
GET /login
    │
    ▼
Exibe formulário
    │
    ▼
POST /login
    │
    ▼
Recebe as credenciais
    │
    ▼
Valida os dados
    │
    ▼
login_user(usuario)
    │
    ▼
Redireciona para /admin
    │
    ▼
@login_required
    │
    ▼
Permite o acesso autenticado
```

O fluxo de saída segue outro caminho:

```
Usuário autenticado
        │
        ▼
Envia formulário de logout
        │
        ▼
POST /logout
        │
        ▼
logout_user()
        │
        ▼
Redireciona para /login
```

A principal ideia é separar as responsabilidades:

- O formulário recebe as credenciais.
    
- A aplicação verifica se as credenciais estão corretas.
    
- `login_user()` registra a autenticação.
    
- `current_user` permite consultar o usuário atual.
    
- `login_required` protege rotas.
    
- `logout_user()` encerra a autenticação.
    

Essa separação facilita a manutenção e evita confundir a verificação de senhas com o gerenciamento da sessão.

---
# 18. Por que não devemos armazenar senhas em texto puro?

Na parte anterior, criamos um fluxo de login didático. Porém, ele tinha uma limitação importante: verificava apenas o e-mail e não validava a senha.

Em uma aplicação real, precisamos armazenar as senhas de forma segura.

Imagine que uma tabela de usuários contenha estes dados:

|E-mail|Senha|
|---|---|
|[usuario@example.com](mailto:usuario@example.com)|minhaSenha123|

Se alguém conseguir acessar essa tabela, poderá ler diretamente as senhas dos usuários.

Isso acontece porque elas estão armazenadas em texto puro, sem qualquer proteção.

A abordagem correta é armazenar um **hash da senha**, utilizando um algoritmo apropriado para senhas, e verificar posteriormente se a senha digitada corresponde a esse hash.

O processo fica assim:

```
Cadastro
   │
   ▼
Usuário define a senha
   │
   ▼
Aplicação gera o hash
   │
   ▼
Banco armazena o hash
```

Durante o login:

```
Usuário informa a senha
          │
          ▼
Aplicação recupera o hash
armazenado no banco
          │
          ▼
Verifica a senha informada
          │
     ┌────┴────┐
     ▼         ▼
  Correta   Incorreta
     │         │
     ▼         ▼
  Login      Recusa
```

O sistema não precisa recuperar a senha original. Ele verifica se a senha fornecida corresponde ao hash armazenado.

## 18.1. Hash não é criptografia reversível

Um hash é o resultado de uma função que transforma uma entrada em uma representação derivada dela.

A criptografia convencional permite recuperar os dados originais quando se possui a chave apropriada. Um hash de senha não foi projetado para funcionar dessa maneira.

Para senhas, devemos utilizar algoritmos específicos, como scrypt, Argon2id ou PBKDF2, com parâmetros adequados. Funções rápidas como SHA-256, usadas isoladamente, não são uma escolha apropriada para armazenar senhas, pois facilitam tentativas massivas de adivinhação.

O Werkzeug, que acompanha o ecossistema Flask, oferece funções práticas para essa finalidade.

---

# 19. Gerando e verificando hashes com Werkzeug

O Werkzeug disponibiliza duas funções úteis:

- `generate_password_hash()`: gera o hash de uma senha.
    
- `check_password_hash()`: verifica se uma senha corresponde ao hash armazenado.
    

Importe as funções:

```
from werkzeug.security import (
    generate_password_hash,
    check_password_hash,
)
```

## 19.1. Gerando o hash

```
senha = "minhaSenha123"

senha_hash = generate_password_hash(senha)

print(senha_hash)
```

A variável `senha_hash` recebe uma representação derivada da senha, incluindo as informações necessárias para verificar a senha posteriormente.

O resultado não será igual ao texto original da senha.

Por padrão, o Werkzeug utiliza um método apropriado para hashing de senhas. O método e seus parâmetros podem variar conforme a versão instalada, portanto consulte a documentação correspondente ao ambiente do projeto.

Não é necessário criar manualmente um salt para o uso normal dessa função: o mecanismo de hashing cuida disso.

## 19.2. Verificando a senha

```
senha_informada = "minhaSenha123"

if check_password_hash(senha_hash, senha_informada):
    print("Senha correta")
else:
    print("Senha incorreta")
```

A função recebe o hash armazenado e a senha informada. Ela retorna `True` quando a senha corresponde ao hash e `False` caso contrário.

Por exemplo:

```
check_password_hash(senha_hash, "outraSenha")
```

Retorna `False` se a senha não corresponder.

**Importante:** o hash deve ser salvo no banco de dados durante o cadastro. Durante o login, recuperamos esse hash e utilizamos `check_password_hash()` para verificar a senha digitada.

---

# 20. Adaptando a classe Usuario

Na parte anterior, a classe `Usuario` continha apenas um identificador e um e-mail. Agora podemos acrescentar o hash da senha.

```
from flask_login import UserMixin
from werkzeug.security import generate_password_hash, check_password_hash


class Usuario(UserMixin):
    def __init__(self, id, email, senha_hash):
        self.id = id
        self.email = email
        self.senha_hash = senha_hash

    def verificar_senha(self, senha):
        return check_password_hash(self.senha_hash, senha)
```

Vamos analisar as alterações.

```
self.senha_hash = senha_hash
```

Armazena no objeto o hash da senha, não a senha original.

O método:

```
def verificar_senha(self, senha):
    return check_password_hash(self.senha_hash, senha)
```

Recebe a senha digitada e verifica se ela corresponde ao hash armazenado.

Assim, a lógica de verificação fica dentro da classe `Usuario`, evitando repetir a mesma chamada em vários pontos da aplicação.

O método `verificar_senha()` é uma escolha de nome nossa. O Flask-Login não exige que esse método exista: ele é criado para organizar a lógica da aplicação.

---

# 21. Criando um usuário de teste com hash

Para continuar o exemplo sem banco de dados, podemos criar um usuário temporário em memória.

```
usuarios = {
    "1": Usuario(
        id="1",
        email="admin@example.com",
        senha_hash=generate_password_hash("SenhaForte!2026"),
    ),
}
```

Nesse exemplo, a senha é convertida em hash durante a criação do objeto.

Isso serve apenas para estudo. Em uma aplicação real, não devemos criar contas administrativas com senhas fixas no código-fonte.

Também não devemos utilizar essa abordagem para armazenar usuários permanentemente: os dados em memória desaparecem quando o processo Python é reiniciado.

Em uma aplicação real, o cadastro deve gerar o hash e persistir o usuário no banco de dados.

---

# 22. Implementando um login com verificação de senha

Agora podemos adaptar a rota de login para verificar tanto o e-mail quanto a senha.

No arquivo `app.py`:

```
from flask import (
    Flask,
    render_template,
    request,
    redirect,
    url_for,
    flash,
)

from flask_login import (
    LoginManager,
    UserMixin,
    login_user,
)

from werkzeug.security import (
    generate_password_hash,
    check_password_hash,
)
```

Considere que a classe `Usuario` e o dicionário `usuarios` já foram definidos conforme as seções anteriores.

A rota pode ser implementada assim:

```
@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        email = request.form.get("email", "").strip()
        senha = request.form.get("senha", "")

        usuario = next(
            (
                user
                for user in usuarios.values()
                if user.email == email
            ),
            None,
        )

        if usuario is None or not usuario.verificar_senha(senha):
            flash("E-mail ou senha inválidos.", "error")
            return redirect(url_for("login"))

        login_user(usuario)

        flash("Login realizado com sucesso.", "success")
        return redirect(url_for("admin"))

    return render_template("login.html")
```

O código pressupõe que `Usuario` tenha o método `verificar_senha()` definido anteriormente.

## 22.1. Entendendo a validação

Primeiro, recebemos os campos do formulário:

```
email = request.form.get("email", "").strip()
senha = request.form.get("senha", "")
```

O e-mail passa por `strip()` para remover espaços no início e no final. A senha não passa por essa operação, pois espaços podem fazer parte de uma senha válida.

Depois, procuramos o usuário:

```
usuario = next(
    (
        user
        for user in usuarios.values()
        if user.email == email
    ),
    None,
)
```

Se nenhum usuário corresponder ao e-mail, `usuario` será `None`.

A condição seguinte verifica se o usuário não existe ou se a senha está incorreta:

```
if usuario is None or not usuario.verificar_senha(senha):
```

O operador `or` permite rejeitar o login quando qualquer uma dessas situações acontece. Como Python utiliza avaliação de curto-circuito, `usuario.verificar_senha(senha)` não será executado quando `usuario` for `None`.

A mensagem é propositalmente genérica:

```
flash("E-mail ou senha inválidos.", "error")
```

Isso evita revelar diretamente se um e-mail está cadastrado, o que poderia facilitar a enumeração de contas.

Por fim, quando as credenciais são válidas:

```
login_user(usuario)
```

O Flask-Login registra o usuário como autenticado.

**Atenção:** o armazenamento em memória continua sendo apenas didático. A implementação não possui persistência, limitação de tentativas ou controles adicionais necessários para um sistema de produção.

---

# 23. Atualizando o formulário HTML

Como a rota agora recebe e-mail e senha, precisamos acrescentar o campo de senha ao formulário.

Arquivo `templates/login.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login</title>
</head>
<body>
    <h1>Entrar</h1>

    <form method="POST">
        <label for="email">E-mail</label>
        <input
            type="email"
            id="email"
            name="email"
            autocomplete="username"
            required
        >

        <label for="senha">Senha</label>
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

O atributo `type="password"` faz com que o navegador oculte visualmente os caracteres digitados. Isso não criptografa a senha e não substitui HTTPS.

Os atributos `autocomplete="username"` e `autocomplete="current-password"` ajudam os navegadores e gerenciadores de senhas a identificar os campos.

O atributo `required` melhora a experiência do formulário, mas a aplicação também deve validar os dados recebidos no servidor.

---

# 24. Exibindo mensagens com flash

A função `flash()` permite armazenar uma mensagem temporária para que ela seja exibida na próxima página.

Ela é útil para comunicar o resultado de operações como login, logout, cadastro e atualização de dados.

Exemplo:

```
from flask import flash

flash("Login realizado com sucesso.", "success")
```

O primeiro argumento é o texto da mensagem. O segundo é uma categoria escolhida pela aplicação, que pode ser utilizada para definir a aparência da mensagem.

No exemplo, usamos as categorias `success` e `error`.

## 24.1. Exibindo as mensagens no template

Podemos criar um fragmento reutilizável.

Arquivo `templates/components/flash_messages.html`:

```
{% with messages = get_flashed_messages(with_categories=true) %}
    {% if messages %}
        <div class="flash-messages">
            {% for category, message in messages %}
                <div class="flash-message flash-{{ category }}">
                    {{ message }}
                </div>
            {% endfor %}
        </div>
    {% endif %}
{% endwith %}
```

O trecho:

```
get_flashed_messages(with_categories=true)
```

retorna as mensagens acompanhadas de suas categorias.

O `with` limita o escopo da variável `messages` ao bloco, e o `for` percorre as mensagens disponíveis.

Para incluir esse fragmento em `login.html`, utilize:

```
{% include "components/flash_messages.html" %}
```

Coloque a inclusão no local em que as mensagens devem aparecer, normalmente antes do formulário.

Se o projeto utilizar Tailwind CSS, as classes de apresentação podem ser adicionadas diretamente ao fragmento. Nesse caso, lembre-se de que o Tailwind precisa detectar essas classes para incluí-las no CSS compilado.

---

# 25. Por que utilizar redirect depois do POST?

Depois de receber e processar um formulário, é comum redirecionar o usuário para outra página.

Por exemplo:

```
return redirect(url_for("admin"))
```

Essa abordagem segue o padrão _Post/Redirect/Get_.

O navegador envia uma requisição POST para processar os dados. Depois, o servidor responde com um redirecionamento, e o navegador faz uma nova requisição GET para carregar a página de destino.

O fluxo é:

```
POST /login
     │
     ▼
Valida credenciais
     │
     ▼
Registra autenticação
     │
     ▼
Redireciona para /admin
     │
     ▼
GET /admin
```

Isso evita que uma simples atualização da página de destino repita diretamente o envio original do formulário.

Entretanto, redirecionar não substitui a validação das credenciais, a proteção CSRF ou a verificação de permissões.

---

# 26. Cuidados antes de utilizar esse código em produção

O exemplo apresentado nesta parte é voltado ao aprendizado. Antes de utilizá-lo em um sistema real, algumas mudanças são necessárias:

- Persistir os usuários em um banco de dados.
    
- Armazenar hashes de senha gerados por um método apropriado.
    
- Validar as credenciais no servidor antes de chamar `login_user()`.
    
- Utilizar HTTPS e uma `SECRET_KEY` forte, mantida fora do código-fonte.
    
- Implementar proteção CSRF nos formulários que alteram o estado da aplicação.
    
- Considerar limitação de tentativas de login e mecanismos de monitoramento.
    
- Configurar os cookies de sessão adequadamente para o ambiente de produção.
    
- Verificar permissões separadamente da autenticação.
    
- Evitar revelar se um e-mail está cadastrado por meio de mensagens de erro excessivamente específicas.
    

O Flask-Login resolve uma parte importante do processo de autenticação, mas a segurança de um sistema de login depende também do armazenamento das credenciais, da validação dos dados e das proteções implementadas na aplicação.

---