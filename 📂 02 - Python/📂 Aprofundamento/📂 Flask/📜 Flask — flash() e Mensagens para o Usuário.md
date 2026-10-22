# Flask — flash() e Mensagens para o Usuário

> [!info] Onde esta nota se encaixa Esta nota aprofunda o que ficou rápido em [[📜 Flask — Do Básico ao Avançado]]: o `redirect()` (seção 3.9), o `url_for()` (seção 3.10), o redirecionamento após POST (seção 4.10) e o uso de `flash()` no projeto final (seção 10 da parte do projeto, "Cadastro, login e logout"). Antes de ler, é bom já saber o que é uma rota, uma view function e um template Jinja.

> [!tip] Como foi testado Todos os exemplos e resultados desta nota foram executados com **Flask 3.1.3** (Jinja2 3.1.6, Werkzeug 3.1.7). Quando eu **não** testei algo, aviso no texto.

---

## 1. O problema

Imagine uma tela de cadastro. O usuário preenche o formulário, clica em **Cadastrar** e tudo dá certo. O que o site deve fazer agora?

Duas coisas, ao mesmo tempo:

1. Levar o usuário para outra página (por exemplo, a de login).
2. Avisar: **"Cadastro realizado com sucesso!"**

Parece simples, mas existe uma pegadinha. Veja a rota:

```python
@app.route("/cadastro", methods=["POST"])
def cadastro():
    # ... salva o usuário no banco ...
    return redirect(url_for("login"))
```

Onde fica a mensagem de sucesso? A rota `cadastro` termina no `redirect`. Quem vai mostrar a página é **outra rota**, a `login`. E a função `login` não sabe que acabou de acontecer um cadastro.

É exatamente esse buraco que o `flash()` resolve: **deixar um recado para a próxima página**.

---

## 2. O raciocínio para chegar na solução

Antes de olhar qualquer código, vamos fazer as perguntas certas. É isso que você vai repetir em outros projetos.

|Pergunta|Resposta neste caso|
|---|---|
|A mensagem aparece na **mesma** requisição ou na **próxima**?|Na próxima (depois do redirect).|
|A página vai mudar?|Sim, por causa do redirect.|
|A mensagem precisa sobreviver a essa mudança?|Sim.|
|Ela deve aparecer **uma vez só**?|Sim. Se o usuário atualizar a página, não deve aparecer de novo.|
|Preciso diferenciar sucesso de erro?|Sim, para pintar de verde ou vermelho.|

Com essas respostas, olhamos as opções:

**Opção A: passar a mensagem como variável do template.** Funciona quando **não há redirect**. Mas o redirect termina a requisição atual, e a variável morre junto. Não serve aqui.

**Opção B: passar a mensagem pela URL** (`/login?msg=Cadastro+feito`). Funciona, mas tem problemas: a mensagem fica visível na URL, qualquer pessoa pode montar um link com uma mensagem falsa, e ao atualizar a página ela aparece de novo.

**Opção C: guardar a mensagem em algum lugar que sobreviva entre requisições, e apagar depois de mostrar.** É exatamente o que precisamos. E o Flask já tem esse "lugar": a **sessão**.

> [!note] Reconstrução de raciocínio Isto é uma forma de **pensar** até chegar no `flash()`. Não é a história real de como o Flask foi criado. O objetivo é você aprender a analisar o problema.

---

## 3. Conceitos que precisamos entender antes

### 3.1. HTTP não tem memória

Cada requisição HTTP é independente. Quando o navegador pede `/login`, o servidor não lembra que, um segundo antes, o mesmo navegador pediu `/cadastro`.

### 3.2. Um redirect são duas requisições

```text
Navegador                               Servidor Flask
    |                                        |
    |--- POST /cadastro -------------------->|  (requisição 1)
    |                                        |  salva o usuário
    |<-- 302 Found, Location: /login --------|
    |                                        |
    |--- GET /login ------------------------>|  (requisição 2)
    |                                        |  monta a página de login
    |<-- 200 OK, HTML da página -------------|
```

São duas viagens separadas. O que a requisição 1 sabe, a requisição 2 **não** sabe, a menos que alguém guarde isso no meio do caminho.

### 3.3. O que é a sessão

A **sessão** é um espaço para guardar pequenos dados de um usuário entre uma requisição e outra. No Flask, ela é usada como se fosse um dicionário:

```python
from flask import session

session["nome"] = "Ana"     # guardar
print(session.get("nome"))  # ler
```

Por padrão, o Flask guarda esses dados **dentro de um cookie** no navegador do usuário. O navegador devolve o cookie em toda requisição seguinte, e é assim que o servidor "lembra".

### 3.4. Por que precisa de `secret_key`

O cookie da sessão é **assinado** com a chave secreta (`app.secret_key`). A assinatura serve para o servidor perceber se alguém mexeu no cookie.

> [!warning] Assinado não é o mesmo que escondido Pela documentação do Flask, o cookie de sessão é **assinado**, mas o conteúdo pode ser lido por quem tem o cookie. Por isso: **nunca coloque senha, token ou dado sensível em `flash()`**.

Sem `secret_key`, a sessão não funciona, e o `flash()` quebra (veja a seção 9).

---

## 4. A sintaxe mínima

O `flash()` tem **dois lados**: um que **deixa** o recado e outro que **lê** o recado.

```python
from flask import Flask, flash, redirect, render_template, url_for

app = Flask(__name__)
app.secret_key = "troque-isto-por-algo-secreto"


@app.route("/cadastro", methods=["POST"])
def cadastro():
    # ... salvou o usuário ...
    flash("Cadastro realizado com sucesso!")   # lado 1: deixa o recado
    return redirect(url_for("login"))


@app.route("/login")
def login():
    return render_template("login.html")
```

E no template (`login.html`):

```jinja
{% for mensagem in get_flashed_messages() %}
    <p>{{ mensagem }}</p>
{% endfor %}
```

O fluxo completo:

```text
POST /cadastro
   |
   |  flash("Cadastro realizado!")
   |     |
   |     v
   |  session["_flashes"] = [("message", "Cadastro realizado!")]
   |     |
   |     v   (o Flask grava a sessão no cookie da resposta)
   v
302 -> /login   (com Set-Cookie: session=...)

GET /login      (o navegador manda o cookie de volta)
   |
   |  template chama get_flashed_messages()
   |     |
   |     v
   |  tira "_flashes" da sessão (apaga) e devolve a lista
   v
HTML com a mensagem  ->  na próxima atualização, não há mais nada
```

---

## 5. Os dois recursos em detalhe

### `flash()`

**O que é:** uma função do pacote `flask`.

**Para que serve:** guardar uma mensagem para ser exibida na **próxima** vez que alguém chamar `get_flashed_messages()`.

**Quando usar:** quando você precisa avisar o usuário sobre algo que aconteceu e vai **mudar de página** (redirect). Também funciona sem redirect (veja a seção 7).

**Quando evitar:**

- Para dados que a página precisa para funcionar (use variáveis do template).
- Para informações sensíveis.
- Para mensagens muito grandes (a sessão fica no cookie, que tem limite de tamanho).

**Sintaxe:**

```python
flash(message, category="message")
```

**Origem:** `from flask import flash`

|Informação|`message`|`category`|
|---|---|---|
|Tipo esperado|`str`|`str`|
|Obrigatório?|Sim|Não|
|Valor padrão|Nenhum|`"message"`|
|O que controla|O texto do aviso|Um "rótulo" para o aviso|
|Se omitir|Dá erro (`TypeError`)|Usa `"message"`|
|Valores aceitos|Qualquer texto|Qualquer texto. O Flask **recomenda** `"message"`, `"error"`, `"info"` e `"warning"`, mas não obriga|

**Retorno:** `None`. Ela não devolve nada, só guarda a mensagem.

**O que ela faz por dentro** (lido do código-fonte do Flask 3.1.3): pega a lista `session["_flashes"]` (ou uma lista vazia), acrescenta a tupla `(category, message)` e grava de volta na sessão.

---

### `get_flashed_messages()`

**O que é:** outra função do pacote `flask`. O Flask também a deixa disponível **automaticamente dentro dos templates Jinja**, por isso você pode chamá-la direto no HTML sem importar nada.

**Para que serve:** **ler** as mensagens guardadas e **apagá-las** da sessão.

**Sintaxe:**

```python
get_flashed_messages(with_categories=False, category_filter=())
```

**Origem:** `from flask import get_flashed_messages` (no Python) ou direto no template.

|Informação|`with_categories`|`category_filter`|
|---|---|---|
|Tipo esperado|`bool`|Lista (ou qualquer iterável) de `str`|
|Obrigatório?|Não|Não|
|Valor padrão|`False`|`()` (vazio, ou seja, sem filtro)|
|O que controla|Se devolve só os textos ou pares `(categoria, texto)`|Quais categorias devem ser devolvidas|
|Se omitir|Devolve só os textos|Devolve todas as categorias|

**Retorno:**

- Com `with_categories=False`: uma lista de textos, por exemplo `["Cadastro realizado!"]`.
- Com `with_categories=True`: uma lista de pares, por exemplo `[("success", "Cadastro realizado!")]`.

**Detalhe importante (documentado):** chamadas repetidas **na mesma requisição** devolvem as mesmas mensagens. A "limpeza" acontece na primeira chamada, mas o resultado fica guardado durante aquela requisição.

---

## 6. Construção progressiva

### Etapa 1: a mensagem simples

É o exemplo da seção 4. Resolve o problema principal, mas tem limitações:

- Toda mensagem tem a mesma cara. Não dá para pintar erro de vermelho e sucesso de verde.
- Você teria que repetir o bloco de mensagens em cada template.

### Etapa 2: adicionar categorias

```python
flash("Cadastro realizado!", "success")
flash("Confira seu e-mail.", "info")
```

Agora cada mensagem carrega um rótulo. Para ler o rótulo, precisamos pedir `with_categories=True`:

```jinja
{% for categoria, texto in get_flashed_messages(with_categories=true) %}
    <p class="{{ categoria }}">{{ texto }}</p>
{% endfor %}
```

> [!note] Dois detalhes do Jinja No Jinja, `true` é escrito em minúsculo. E o `for categoria, texto in ...` "desempacota" cada par `(categoria, texto)` em duas variáveis.

### Etapa 3: colocar no template-base

Em vez de repetir o bloco em cada página, colocamos **uma vez** no `base.html`. Todas as páginas que herdam dele passam a mostrar as mensagens. (Veja herança de templates em [[📜 Jinja — Templates e Herança]].)

`templates/base.html`:

```jinja
<!doctype html>
<html lang="pt-BR">
<body>
    {% with mensagens = get_flashed_messages(with_categories=true) %}
        {% for categoria, texto in mensagens %}
            <div class="alerta {{ categoria }}">{{ texto }}</div>
        {% endfor %}
    {% endwith %}

    {% block conteudo %}{% endblock %}
</body>
</html>
```

`templates/pagina.html`:

```jinja
{% extends "base.html" %}
{% block conteudo %}<h1>{{ titulo }}</h1>{% endblock %}
```

**Por que usar `{% with %}`?** Ele guarda o resultado numa variável (`mensagens`) que só existe dentro do bloco. Assim a função é chamada uma vez e o código fica mais legível.

### Etapa 4: o exemplo completo em uma rota real

```python
from flask import Flask, flash, redirect, render_template, request, url_for

app = Flask(__name__)
app.secret_key = "troque-isto"


@app.route("/cadastro", methods=["GET", "POST"])
def cadastro():
    if request.method == "POST":
        nome = request.form.get("nome", "").strip()

        if not nome:
            flash("Informe um nome.", "error")
            return render_template("pagina.html", titulo="Cadastro")

        flash(f"Usuário {nome} cadastrado!", "success")
        return redirect(url_for("login"))

    return render_template("pagina.html", titulo="Cadastro")


@app.route("/login")
def login():
    return render_template("pagina.html", titulo="Login")
```

Repare nos **dois caminhos**:

|Caminho|O que acontece|Usa redirect?|
|---|---|---|
|Nome vazio|`flash(..., "error")` e mostra o formulário de novo|Não|
|Nome válido|`flash(..., "success")` e vai para o login|Sim|

Isso mostra que o `flash()` funciona nos dois casos (veja a seção 7).

**Resultado verificado:**

```text
POST nome vazio     ->  <div class="alerta error">Informe um nome.</div> <h1>Cadastro</h1>
POST nome "Ana"     ->  302
GET /login          ->  <div class="alerta success">Usuário Ana cadastrado!</div> <h1>Login</h1>
GET /login de novo  ->  <h1>Login</h1>      (a mensagem sumiu)
```

### Etapa 5: cores por categoria

As categorias viram classes CSS. Com CSS comum:

```html
<style>
    .alerta { padding: 10px; margin: 10px 0; border-radius: 6px; }
    .success { background: #d1fae5; }
    .error   { background: #fee2e2; }
    .info    { background: #dbeafe; }
    .warning { background: #fef3c7; }
</style>
```

Com Tailwind (veja [[📜 Tailwind CSS — Estilização de Interfaces]]), você pode mapear a categoria para classes dentro do Jinja:

```jinja
{% set cores = {
    "success": "bg-green-100 text-green-800",
    "error":   "bg-red-100 text-red-800",
    "info":    "bg-blue-100 text-blue-800",
    "warning": "bg-yellow-100 text-yellow-800"
} %}

{% for categoria, texto in get_flashed_messages(with_categories=true) %}
    <div class="p-3 my-2 rounded {{ cores.get(categoria, 'bg-gray-100 text-gray-800') }}">
        {{ texto }}
    </div>
{% endfor %}
```

O `cores.get(categoria, '...')` usa uma cor padrão quando a categoria não está no dicionário (por exemplo, a categoria `"message"`, que é o padrão do `flash()`).

> [!warning] Tailwind e classes montadas por texto Isso funciona porque as classes completas (`bg-green-100` etc.) aparecem escritas no template. Se você montasse o nome da classe juntando pedaços de texto, o Tailwind poderia não encontrá-la. Confira como o seu projeto carrega o Tailwind (CDN ou build) na nota de Tailwind.

### Etapa 6: filtrar por categoria

Com `category_filter`, dá para mostrar erros em um lugar e o resto em outro:

```jinja
{% for texto in get_flashed_messages(category_filter=["error"]) %}
    <div class="erro-no-topo">{{ texto }}</div>
{% endfor %}
```

Resultado testado:

```text
flash("a", "error"); flash("b", "info")
get_flashed_messages(category_filter=["error"])  ->  ["a"]
get_flashed_messages()                           ->  ["a", "b"]
```

> [!warning] Cuidado ao usar vários blocos A limpeza da sessão acontece na **primeira** chamada de `get_flashed_messages()`. Como as chamadas seguintes na mesma requisição devolvem a mesma lista, usar filtros em blocos diferentes funciona bem. O problema aparece só se **nenhum** bloco chamar a função: aí as mensagens ficam na sessão (veja a seção 9).

---

## 7. O que acontece por dentro (e o que eu verifiquei)

Para não ficar só na teoria, montei um teste com o `test_client()` do Flask e observei cada passo.

|O que testei|O que observei|
|---|---|
|`flash()` seguido de `redirect()`|Resposta `302` com `Location: /login` e um cookie `session` sendo enviado.|
|Conteúdo da sessão depois do `flash()`|`[('success', 'Cadastro realizado!'), ('info', 'Confira seu e-mail.')]`|
|Abrir `/login`|A página mostra as duas mensagens, no formato `[categoria] texto`.|
|Abrir `/login` de novo|Nenhuma mensagem. Elas foram apagadas.|
|`flash()` e ler na **mesma** requisição|Funciona. Duas chamadas seguidas de `get_flashed_messages()` devolvem a mesma lista, e a sessão fica sem `_flashes` depois.|
|Dar `flash()` e **não** ler em nenhuma página|As mensagens continuam na sessão, esperando a próxima leitura.|

Resumo do que isso significa:

```text
flash()                    ->  ESCREVE em session["_flashes"]
get_flashed_messages()     ->  LÊ e APAGA de session["_flashes"]
```

Ou seja, **o `flash()` não mostra nada sozinho.** Ele só guarda. Quem mostra é o template, ao chamar `get_flashed_messages()`.

---

## 8. Comparando com as alternativas

|Abordagem|Funciona com redirect?|Some sozinha?|Quando escolher|
|---|---|---|---|
|`flash()`|Sim|Sim, depois de lida|Avisos curtos após uma ação, principalmente com redirect|
|Variável em `render_template(..., erro="...")`|Não|Não se aplica|Erros de validação mostrados na mesma página, sem redirect|
|Mensagem na URL (`?msg=...`)|Sim|Não (fica na URL)|Evite para mensagens de aviso. Qualquer pessoa pode forjar um link|
|`session["msg"] = ...` manual|Sim|Não, você precisa lembrar de apagar|Quase nunca. O `flash()` já faz isso do jeito certo|

> [!tip] Regra prática Pergunte: **"Vou mudar de página?"** Se sim, `flash()`. Se não, uma variável no `render_template()` costuma ser mais simples e direta. Mas usar `flash()` sem redirect também é válido, como mostramos na etapa 4, e tem a vantagem de manter **todos** os avisos no mesmo lugar do template.

---

## 9. Erros comuns e como diagnosticar

### Erro 1: `RuntimeError: The session is unavailable because no secret key was set`

**Situação:** você chamou `flash()` sem definir `app.secret_key`.

**Mensagem real** (testada):

```text
RuntimeError: The session is unavailable because no secret key was set.
Set the secret_key on the application to something unique and secret.
```

**Causa confirmada:** o `flash()` grava em `session`, e a sessão exige a chave secreta.

**Como corrigir:**

```python
app.secret_key = "algo-longo-e-secreto"
```

Ou, em projetos organizados, `SECRET_KEY` na configuração (veja a seção 9 de [[📜 Flask — Do Básico ao Avançado]]). Em produção, a chave **não** deve ficar escrita no código.

**Como evitar:** configure a chave logo no começo do projeto.

### Erro 2: a mensagem não aparece

Em vez de trocar código aleatoriamente, siga esta lista de investigação:

|Passo|O que verificar|Como verificar|
|---|---|---|
|1|O `flash()` realmente foi executado?|Coloque um `print("passou aqui")` antes dele.|
|2|Existe `secret_key`?|Se não existisse, teria dado o erro 1.|
|3|O template da página de destino chama `get_flashed_messages()`?|Abra o HTML e procure. É a causa mais comum.|
|4|O template herda do `base.html`?|Veja se tem `{% extends "base.html" %}`.|
|5|O bloco com as mensagens está **fora** de `{% block %}` no base?|Se estiver dentro de um bloco que a página filha sobrescreve, ele some.|
|6|Outra rota leu a mensagem antes?|Uma rota intermediária que renderizou um template com o bloco consome as mensagens.|
|7|O navegador está aceitando cookies?|Sem cookies, a sessão não volta e o `flash()` perde o recado.|

**Hipótese ou fato?** Os passos 1 a 6 eu consigo confirmar olhando o código. O passo 7 depende do navegador do usuário, então é uma hipótese a ser testada.

### Erro 3: a mensagem aparece na página "errada"

**Situação:** você dá `flash()` e, em vez de aparecer onde esperava, aparece mais tarde, em outra tela.

**Causa:** como vimos, uma mensagem que **ninguém lê** continua na sessão. Ela aparece na primeira página que chamar `get_flashed_messages()`.

**Como investigar:** imprima a sessão na rota suspeita:

```python
from flask import session
print(session.get("_flashes"))
```

**Como evitar:** garanta que o bloco de mensagens esteja no `base.html`, para que **toda** página consuma as mensagens.

### Erro 4: o HTML na mensagem aparece como texto

**Situação:** `flash("<b>Atenção</b>")` mostra `<b>Atenção</b>` na tela, em vez de negrito.

**Causa:** o Jinja escapa o HTML por padrão (testado: a saída vira `&lt;b&gt;negrito?&lt;/b&gt;`). Isso é uma **proteção**, e é bom que exista.

> [!danger] Não use `|safe` em mensagens que incluem dados do usuário Se você escrever `{{ texto|safe }}` e a mensagem tiver um nome digitado pelo usuário (como `f"Usuário {nome} cadastrado"`), alguém pode digitar um script no campo e ele será executado no navegador (XSS). Deixe o escape ligado. Se quiser destaque visual, faça isso com CSS, não com HTML dentro da mensagem.

### Erro 5: o `flash()` dentro de uma API JSON

**Situação:** você usa `flash()` em uma rota que devolve JSON.

**Por que não faz sentido:** o `flash()` depende de uma **página** que leia a mensagem depois. Uma API não tem essa página. Para APIs, devolva a mensagem dentro do próprio JSON (veja a seção 11 de [[📜 Flask — Do Básico ao Avançado]]).

---

## 10. Cuidados de segurança

1. **Não coloque dados sensíveis** (senha, token, CPF) em `flash()`. O cookie é assinado, mas o conteúdo pode ser lido.
2. **Mensagens de login devem ser genéricas.** `"Usuário ou senha inválidos."` é melhor do que `"Senha incorreta para este usuário"`, porque a segunda frase confirma que o usuário existe.
3. **Cadastro é um caso diferente.** `"Esse nome de usuário já está em uso"` revela que o nome existe, mas muitas vezes é necessário para a pessoa corrigir o cadastro. Entenda isso como uma decisão consciente de projeto, não como um detalhe esquecido.
4. **Não use `|safe`** com texto que veio do usuário.
5. **Mantenha as mensagens curtas.** Pela documentação, o cookie de sessão tem limite de tamanho, então muitas mensagens longas podem estourar esse limite.

---

## 11. Como reutilizar em outros projetos

O raciocínio é sempre o mesmo:

```text
fiz uma ação  ->  quero avisar o resultado  ->  vou mudar de página?
     |                                               |
     |                                  sim: flash() + redirect
     |                                  não: flash() ou variável no template
```

|Situação|Mensagem de exemplo|Categoria|Redirect para|
|---|---|---|---|
|Cadastro de usuário|"Cadastro realizado. Faça login."|`success`|Login|
|Login com erro|"Usuário ou senha inválidos."|`error`|Fica na página|
|Criar projeto|"Projeto criado."|`success`|Lista de projetos|
|Editar informações|"Alterações salvas."|`success`|Página do item|
|Excluir registro|"Registro excluído."|`info`|Lista|
|Upload de arquivo|"Arquivo enviado." ou "Formato não permitido."|`success` / `error`|Lista de arquivos|
|Painel administrativo|"Você não tem permissão para isso."|`warning`|Painel|

**O que fica igual:** o par `flash()` na rota e `get_flashed_messages()` no `base.html`.

**O que muda:** o texto, a categoria e o destino do redirect.

**O que depende do contexto:**

- Em uma exclusão, o ideal é o `flash()` **depois** de confirmar que a exclusão deu certo, e não antes.
- Em upload, valide o arquivo primeiro. Só dê `flash("Arquivo enviado.", "success")` quando tiver certeza.
- Se uma operação falhar no banco, não dê mensagem de sucesso. Dê `flash("Não foi possível salvar.", "error")` depois de tratar a falha (veja a seção 6.14 da nota principal).

### Exemplo de adaptação: excluir um projeto

```python
@projetos_bp.route("/<int:projeto_id>/excluir", methods=["POST"])
@login_required
def excluir(projeto_id):
    projeto = db.session.get(Projeto, projeto_id)

    if projeto is None or projeto.user_id != current_user.id:
        flash("Projeto não encontrado.", "error")
        return redirect(url_for("projetos.lista"))

    db.session.delete(projeto)
    db.session.commit()

    flash("Projeto excluído.", "info")
    return redirect(url_for("projetos.lista"))
```

Repare na ordem: **primeiro** a verificação e a operação, **depois** a mensagem. Se a mensagem viesse antes, poderia dizer "excluído" mesmo quando a exclusão falhasse.

---

## 12. Mensagens automáticas do Flask-Login

Se você usa [[📜 Flask-Login — Autenticação e Sessões]], uma rota protegida com `@login_required` pode fazer o `flash()` **por você**. O `LoginManager` tem os atributos `login_view`, `login_message` e `login_message_category`, que controlam para onde o usuário vai e qual aviso aparece.

> [!note] Limite do que eu confirmei Nesta sessão o Flask-Login não estava instalado, então **não testei** esse comportamento. Confirme na documentação oficial do Flask-Login antes de depender dele.

A vantagem é que, se o seu `base.html` já exibe as mensagens, o aviso automático aparece sem código extra.

---

## 13. Como testar o `flash()` no seu projeto

**Teste manual:**

1. Faça a ação que dá `flash()`.
2. Confira se a mensagem aparece.
3. Atualize a página. A mensagem **deve sumir**.
4. Repita pelo caminho de erro e confira a cor/categoria.

**Teste automatizado com pytest** (veja a seção 12 da nota principal):

```python
def test_cadastro_mostra_mensagem(client):
    resposta = client.post(
        "/cadastro",
        data={"nome": "Ana"},
        follow_redirects=True,
    )
    assert "Usuário Ana cadastrado!" in resposta.get_data(as_text=True)
```

O `follow_redirects=True` faz o cliente de teste seguir o redirect, como o navegador faria. Sem isso, ele para na resposta `302` e a mensagem ainda não foi lida.

**Inspecionar a sessão** (útil para depurar):

```python
with client.session_transaction() as sessao:
    print(sessao.get("_flashes"))
```

---

## 14. Resumo rápido

|Ideia|Resumo|
|---|---|
|Para que serve|Deixar um aviso para a próxima página|
|Onde fica guardado|Na sessão (`session["_flashes"]`), por padrão dentro de um cookie assinado|
|O que precisa|`app.secret_key` definida|
|Quem mostra|O template, com `get_flashed_messages()`|
|Aparece quantas vezes|Uma vez. A leitura apaga|
|Categorias|`"message"` (padrão), `"success"`, `"error"`, `"info"`, `"warning"`. Você pode inventar outras|
|Erro mais comum|Esquecer o bloco no `base.html` ou esquecer a `secret_key`|
|Não fazer|Dados sensíveis na mensagem, `|

---

## 15. Exercícios

Tente resolver **antes** de pedir a correção. Quando quiser, me mande sua resposta e eu corrijo explicando o raciocínio.

**Nível 1 — Reconhecimento**

1. Explique com suas palavras: por que o `flash()` precisa de `secret_key`?
2. Qual é o papel de cada função: `flash()` e `get_flashed_messages()`?

**Nível 2 — Modificação**

3. No exemplo da etapa 4, o que acontece se você trocar `redirect(url_for("login"))` por `render_template("pagina.html", titulo="Login")`? A mensagem ainda aparece? Preveja primeiro, depois teste.
4. O que muda se você remover `with_categories=true` do template mas continuar usando `for categoria, texto in ...`? Preveja o erro.

**Nível 3 — Implementação**

5. Crie uma rota `/contato` com um formulário de mensagem. Se o campo estiver vazio, mostre um erro. Se estiver preenchido, mostre sucesso e redirecione para a home. Faça sem copiar o exemplo da nota.

**Nível 4 — Transferência**

6. Adapte o mesmo raciocínio para um sistema de upload: "Arquivo enviado" e "Formato não permitido". O que muda em relação ao cadastro? Onde o `flash()` fica na função?

**Nível 5 — Diagnóstico**

7. Um colega diz: "Meu `flash("Salvo!")` nunca aparece." O código dele:

```python
@app.route("/salvar", methods=["POST"])
def salvar():
    flash("Salvo!")
    return redirect("/lista")

@app.route("/lista")
def lista():
    return "<h1>Lista</h1>"
```

Quais são as possíveis causas? Em que ordem você investigaria? (Dica: olhe o que a rota `/lista` devolve.)

---

## 16. Próximos passos

- Colocar o bloco de mensagens no `base.html` do seu projeto e testar os quatro tipos de categoria.
- Revisar o uso de `flash()` no projeto final e conferir se há mensagens com dados sensíveis.
- Continuar a série: aprofundar **formulários e validação** ([[📜 Flask-WTF — Formulários e CSRF]]) e **tratamento de erros** (seção 9 da nota principal), que se conectam diretamente com o que mostramos aqui.