## O que é?

O **Selenium** é uma biblioteca utilizada para automatizar navegadores através de código.

Em vez de realizar uma requisição HTTP diretamente, como fazemos com `requests`, o Selenium controla um navegador e permite realizar ações semelhantes às de um usuário:

```text
Python
   │
   ▼
Selenium
   │
   ▼
WebDriver
   │
   ▼
Navegador
   │
   ▼
Página Web
```

Com Selenium podemos:

- abrir páginas;
    
- encontrar elementos HTML;
    
- clicar em botões e links;
    
- preencher campos;
    
- enviar formulários;
    
- ler textos;
    
- obter atributos HTML;
    
- executar JavaScript;
    
- navegar entre páginas;
    
- esperar elementos aparecerem;
    
- automatizar testes;
    
- interagir com páginas que utilizam JavaScript.
    

---

## Selenium x Requests

É importante entender a diferença entre as duas bibliotecas.

Com `requests`:

```python
import requests

response = requests.get(
    "https://example.com"
)
```

O Python realiza uma requisição HTTP diretamente.

```text
Python
   │
   ▼
HTTP Request
   │
   ▼
Servidor
   │
   ▼
HTTP Response
```

Com Selenium:

```python
from selenium import webdriver

driver = webdriver.Chrome()

driver.get(
    "https://example.com"
)
```

O Selenium controla um navegador.

```text
Python
   │
   ▼
Selenium
   │
   ▼
Chrome
   │
   ▼
Site
```

Uma diferença importante é que o navegador pode executar JavaScript e construir/modificar o DOM da página.

Por isso, Selenium é especialmente útil quando precisamos **interagir com a interface da página**, e não apenas receber uma resposta HTTP.

---

## Instalação

Para instalar o Selenium:

```bash
pip install selenium
```

Depois podemos importar:

```python
from selenium import webdriver
```

---

## Criando o navegador

A forma básica de iniciar o Chrome é:

```python
from selenium import webdriver

driver = webdriver.Chrome()
```

Aqui temos:

```python
driver
```

que é uma variável que recebe o objeto responsável por controlar o navegador.

Podemos pensar:

```text
webdriver.Chrome()
       │
       ▼
cria/controla uma instância do Chrome
       │
       ▼
driver
```

Depois disso podemos utilizar:

```python
driver.get(...)
driver.back()
driver.refresh()
driver.quit()
```

---

## O que é `driver`?

`driver` não é uma palavra reservada do Python.

É apenas o nome que normalmente damos à variável que representa o navegador controlado pelo Selenium.

Por exemplo:

```python
driver = webdriver.Chrome()
```

Poderíamos escrever:

```python
navegador = webdriver.Chrome()
```

e também funcionaria:

```python
navegador.get(
    "https://example.com"
)
```

O nome `driver` é apenas uma convenção muito utilizada.

---

# Navegação

O objeto `driver` possui métodos para controlar a navegação do navegador.

Os principais são:

```text
driver
│
├── get()
├── back()
├── forward()
├── refresh()
└── quit()
```

---

## `driver.get()`

### O que é?

`get()` faz o navegador acessar uma determinada URL.

Exemplo:

```python
driver.get(
    "https://www.google.com"
)
```

Depois dessa chamada, o navegador será direcionado para:

```text
https://www.google.com
```

---

### Sintaxe

```python
driver.get(url)
```

### Parâmetros

|Parâmetro|Tipo|Obrigatório|Padrão|Descrição|
|---|---|---|---|---|
|`url`|`str`|Sim|—|URL que o navegador deve acessar|

Exemplo:

```python
driver.get(
    "https://example.com"
)
```

`url` deve ser uma string contendo uma URL.

---

### Exemplo

```python
from selenium import webdriver

driver = webdriver.Chrome()

driver.get(
    "https://www.google.com"
)
```

---

## `driver.back()`

### O que é?

`back()` faz o navegador voltar para a página anterior do histórico.

Imagine:

```text
Google
   ↓
Site A
   ↓
Site B
```

Depois:

```python
driver.back()
```

O navegador volta:

```text
Google
   ↓
Site A
```

---

### Sintaxe

```python
driver.back()
```

### Parâmetros

Não possui parâmetros obrigatórios.

```python
driver.back()
```

---

### Exemplo

```python
driver.get(
    "https://example.com"
)

driver.get(
    "https://example.org"
)

driver.back()
```

O navegador volta para `example.com`.

---

## `driver.forward()`

### O que é?

`forward()` avança no histórico do navegador.

Se estivermos:

```text
Página A
   ↓
Página B
   ↓
Página C
```

e fizermos:

```python
driver.back()
```

voltamos para:

```text
Página B
```

Então:

```python
driver.forward()
```

avança novamente para:

```text
Página C
```

---

### Sintaxe

```python
driver.forward()
```

### Parâmetros

Não possui parâmetros obrigatórios.

---

### Exemplo

```python
driver.get(
    "https://example.com"
)

driver.get(
    "https://example.org"
)

driver.back()

driver.forward()
```

---

## `driver.refresh()`

### O que é?

`refresh()` atualiza/recarrega a página atual.

Exemplo:

```python
driver.refresh()
```

Se o navegador estiver em:

```text
https://example.com
```

a página atual será recarregada.

---

### Sintaxe

```python
driver.refresh()
```

### Parâmetros

Não possui parâmetros obrigatórios.

---

### Exemplo

```python
driver.get(
    "https://example.com"
)

driver.refresh()
```

---

## `driver.quit()`

### O que é?

`quit()` encerra a sessão do navegador controlada pelo Selenium.

Exemplo:

```python
driver.quit()
```

Isso fecha o navegador e encerra a sessão do WebDriver.

---

### Sintaxe

```python
driver.quit()
```

### Parâmetros

Não possui parâmetros obrigatórios.

---

## Utilizando `try/finally`

É uma boa prática garantir que o navegador seja encerrado.

Exemplo:

```python
from selenium import webdriver

driver = webdriver.Chrome()

try:

    driver.get(
        "https://example.com"
    )

finally:

    driver.quit()
```

A ideia é:

```text
inicia navegador
      ↓
try
      ↓
executa automação
      ↓
aconteceu erro ou terminou?
      ↓
finally
      ↓
driver.quit()
      ↓
fecha navegador
```

Isso evita deixar uma sessão do navegador aberta caso ocorra uma exceção durante a automação.

---

# Estrutura básica de um programa Selenium

Um programa simples pode começar assim:

```python
from selenium import webdriver

driver = webdriver.Chrome()

try:

    driver.get(
        "https://www.google.com"
    )

finally:

    driver.quit()
```

O fluxo é:

```text
import Selenium
      ↓
criar driver
      ↓
abrir URL
      ↓
executar automação
      ↓
encerrar navegador
```

---

# `driver.current_url`

Além dos métodos, o `driver` possui propriedades.

Uma delas é:

```python
driver.current_url
```

Ela retorna a URL atualmente carregada no navegador.

Exemplo:

```python
driver.get(
    "https://example.com"
)

url = driver.current_url

print(url)
```

Resultado:

```text
https://example.com/
```

---

## Tipo do retorno

`driver.current_url` retorna uma:

```python
str
```

Portanto:

```python
url = driver.current_url
```

faz com que `url` receba uma string.

---

# `driver.title`

Outra propriedade muito utilizada é:

```python
driver.title
```

Ela retorna o título da página.

Por exemplo, se o HTML possui:

```html
<title>Google</title>
```

podemos fazer:

```python
titulo = driver.title

print(titulo)
```

Resultado:

```text
Google
```

O retorno é uma:

```python
str
```

---

# Métodos x propriedades

É importante perceber a diferença:

```python
driver.get(...)
```

possui:

```text
()
```

porque `get` é um **método**.

Já:

```python
driver.current_url
```

não possui:

```text
()
```

porque `current_url` é uma **propriedade**.

O mesmo acontece com:

```python
driver.title
```

Portanto:

```python
driver.get(url)
```

e:

```python
driver.current_url
```

não são utilizados da mesma maneira.

---

# `driver.execute_script()`

### O que é?

`execute_script()` permite executar código JavaScript dentro da página carregada no navegador.

Exemplo:

```python
driver.execute_script(
    "document.title = 'Novo título';"
)
```

O Selenium envia esse código JavaScript para ser executado no contexto da página.

---

### Sintaxe

```python
driver.execute_script(script, *args)
```

Os argumentos principais são:

|Parâmetro|Tipo|Obrigatório|Padrão|Descrição|
|---|---|---|---|---|
|`script`|`str`|Sim|—|Código JavaScript que será executado|
|`*args`|qualquer tipo|Não|nenhum|Valores/objetos enviados para o JavaScript|

O primeiro argumento contém o JavaScript.

Exemplo:

```python
driver.execute_script(
    "document.title = 'Teste';"
)
```

---

# `arguments` dentro do JavaScript

Uma parte importante do seu código é:

```python
driver.execute_script(
    "arguments[0].click();",
    link
)
```

Aqui temos duas linguagens trabalhando juntas:

```text
Python
   │
   └── driver.execute_script(...)
              │
              ▼
        JavaScript
```

O segundo argumento:

```python
link
```

é enviado para o JavaScript.

Dentro do JavaScript, os argumentos recebidos ficam disponíveis através de:

```javascript
arguments
```

Nesse caso:

```javascript
arguments[0]
```

representa o primeiro argumento enviado pelo Python.

Como enviamos:

```python
link
```

temos:

```text
Python:

link
 │
 ▼
execute_script(..., link)
 │
 ▼
JavaScript:

arguments[0]
 │
 ▼
link
```

Então:

```javascript
arguments[0].click();
```

significa, conceitualmente:

```text
pegue o primeiro argumento
        ↓
esse argumento é o elemento link
        ↓
chame click()
```

---

# Exemplo completo

```python
link = driver.find_element(
    By.CSS_SELECTOR,
    "a"
)

driver.execute_script(
    "arguments[0].click();",
    link
)
```

Aqui:

```python
link
```

é um objeto `WebElement`.

Ele é enviado para:

```python
execute_script()
```

e fica disponível no JavaScript como:

```javascript
arguments[0]
```

---

# `execute_script()` não é necessário para todo clique

Normalmente, quando temos um `WebElement`, podemos simplesmente utilizar:

```python
link.click()
```

Por exemplo:

```python
link = driver.find_element(
    By.CSS_SELECTOR,
    "a"
)

link.click()
```

Portanto:

```python
link.click()
```

é diferente de:

```python
driver.execute_script(
    "arguments[0].click();",
    link
)
```

No primeiro caso, estamos utilizando o método `click()` do próprio `WebElement`.

No segundo, estamos executando JavaScript na página.

O `execute_script()` pode ser útil em situações específicas em que a interação normal do WebDriver não funciona como esperado.

---

# Estrutura geral do `driver`

Até aqui, podemos organizar mentalmente o objeto `driver` desta forma:

```text
driver
│
├── Navegação
│   ├── get()
│   ├── back()
│   ├── forward()
│   ├── refresh()
│   └── quit()
│
├── Informações da página
│   ├── current_url
│   └── title
│
└── JavaScript
    └── execute_script()
```

Essa é a base para entender as próximas partes do Selenium.

---

# Exemplo juntando os conceitos

```python
from selenium import webdriver

driver = webdriver.Chrome()

try:

    driver.get(
        "https://www.google.com"
    )

    print(
        f"URL: {driver.current_url}"
    )

    print(
        f"Título: {driver.title}"
    )

    driver.refresh()

finally:

    driver.quit()
```

Fluxo:

```text
webdriver.Chrome()
      ↓
driver
      ↓
get()
      ↓
Google
      ↓
current_url
      ↓
title
      ↓
refresh()
      ↓
quit()
```

---

# Resumo

Nesta primeira parte foram apresentados os fundamentos do Selenium:

```text
Selenium
│
├── webdriver.Chrome()
│
├── driver
│
├── Navegação
│   ├── get()
│   ├── back()
│   ├── forward()
│   ├── refresh()
│   └── quit()
│
├── Informações
│   ├── current_url
│   └── title
│
└── JavaScript
    └── execute_script()
```

Os conceitos mais importantes para memorizar neste momento são:

```python
driver = webdriver.Chrome()
```

Cria o navegador controlado pelo Selenium.

```python
driver.get(url)
```

Acessa uma URL.

```python
driver.back()
```

Volta no histórico.

```python
driver.forward()
```

Avança no histórico.

```python
driver.refresh()
```

Atualiza a página.

```python
driver.current_url
```

Obtém a URL atual.

```python
driver.title
```

Obtém o título da página.

```python
driver.execute_script(...)
```

Executa JavaScript na página.

```python
driver.quit()
```

Encerra a sessão do navegador.

---

# Localização de elementos com `By`

## O que é `By`?

`By` é uma classe do Selenium utilizada para definir **qual estratégia será usada para localizar um elemento HTML** na página.

Quando queremos encontrar um elemento, precisamos informar duas coisas:

```text
1. COMO procurar
2. O QUE procurar
```

Por exemplo:

```python
driver.find_element(
    By.NAME,
    "q"
)
```

Nesse caso:

```text
By.NAME
   ↓
como procurar

"q"
   ↓
o que procurar
```

O Selenium então procura um elemento cujo atributo `name` seja:

```html
name="q"
```

---

## Importando `By`

Para utilizar `By`:

```python
from selenium.webdriver.common.by import By
```

Depois podemos utilizar:

```python
By.ID
By.NAME
By.CLASS_NAME
By.TAG_NAME
By.CSS_SELECTOR
By.XPATH
```

---

# Estrutura do `By`

As principais estratégias são:

```text
By
│
├── ID
├── NAME
├── CLASS_NAME
├── TAG_NAME
├── CSS_SELECTOR
└── XPATH
```

Todas elas são utilizadas para localizar elementos, mas cada uma utiliza uma forma diferente de identificação.

---

# `By.ID`

## O que é?

`By.ID` procura um elemento através do atributo HTML `id`.

Imagine o seguinte HTML:

```html
<input
    id="usuario"
    type="text"
>
```

Podemos localizar esse elemento com:

```python
elemento = driver.find_element(
    By.ID,
    "usuario"
)
```

O Selenium procura:

```text
id="usuario"
```

---

## Parâmetros

Quando utilizamos:

```python
driver.find_element(
    By.ID,
    "usuario"
)
```

temos:

|Argumento|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`By.ID`|`str`/estratégia|Sim|Define que a busca será feita pelo `id`|
|`"usuario"`|`str`|Sim|Valor do atributo `id`|

---

## Exemplo

HTML:

```html
<input id="email">
```

Python:

```python
email = driver.find_element(
    By.ID,
    "email"
)
```

Depois podemos trabalhar com o elemento:

```python
email.send_keys(
    "teste@example.com"
)
```

---

## Quando utilizar `By.ID`?

Quando o elemento possui um `id` confiável e estável.

Exemplo:

```html
<input id="login">
```

Nesse caso:

```python
driver.find_element(
    By.ID,
    "login"
)
```

é geralmente uma forma simples de localizar o elemento.

---

# `By.NAME`

## O que é?

`By.NAME` procura pelo atributo HTML:

```html
name=""
```

Exemplo:

```html
<input
    name="q"
    type="text"
>
```

Podemos localizar:

```python
pesquisa = driver.find_element(
    By.NAME,
    "q"
)
```

---

## O que acontece?

O Selenium procura um elemento que possua:

```html
name="q"
```

Portanto:

```python
By.NAME
```

significa:

```text
procure pelo atributo name
```

e:

```python
"q"
```

é o valor procurado.

---

## Parâmetros

|Argumento|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`By.NAME`|estratégia|Sim|Informa que a busca utiliza `name`|
|`"q"`|`str`|Sim|Valor do atributo `name`|

---

## Exemplo do seu código

Você utiliza:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Aqui:

```python
(By.NAME, "q")
```

é uma tupla contendo:

```text
(By.NAME, "q")
    │       │
    │       └── valor procurado
    │
    └────────── estratégia
```

O Selenium interpreta isso como:

```text
Procure um elemento
pelo atributo NAME
cujo valor seja "q"
```

---

# `By.CLASS_NAME`

## O que é?

`By.CLASS_NAME` procura um elemento através do atributo:

```html
class=""
```

Exemplo:

```html
<div class="resultado">
```

Podemos localizar:

```python
elemento = driver.find_element(
    By.CLASS_NAME,
    "resultado"
)
```

---

## Parâmetros

|Argumento|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`By.CLASS_NAME`|estratégia|Sim|Informa que a busca utiliza classe CSS|
|`"resultado"`|`str`|Sim|Nome da classe procurada|

---

## Cuidado com múltiplas classes

Considere:

```html
<div class="resultado ativo">
```

Existem duas classes:

```text
resultado
ativo
```

Podemos procurar:

```python
driver.find_element(
    By.CLASS_NAME,
    "resultado"
)
```

ou:

```python
driver.find_element(
    By.CLASS_NAME,
    "ativo"
)
```

Mas não devemos passar:

```python
driver.find_element(
    By.CLASS_NAME,
    "resultado ativo"
)
```

porque `By.CLASS_NAME` espera uma classe individual.

Para procurar combinações de classes, podemos utilizar `CSS_SELECTOR`.

---

# `By.TAG_NAME`

## O que é?

`By.TAG_NAME` procura pelo nome da tag HTML.

Exemplo:

```html
<a href="https://example.com">
    Example
</a>
```

A tag é:

```text
a
```

Podemos procurar:

```python
link = driver.find_element(
    By.TAG_NAME,
    "a"
)
```

---

## Outro exemplo

HTML:

```html
<input>
<input>
<input>
```

Podemos procurar elementos `input`:

```python
driver.find_elements(
    By.TAG_NAME,
    "input"
)
```

---

## Parâmetros

|Argumento|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`By.TAG_NAME`|estratégia|Sim|Procura pelo nome da tag|
|`"input"`|`str`|Sim|Nome da tag HTML|

---

# `By.CSS_SELECTOR`

## O que é?

`By.CSS_SELECTOR` permite utilizar **seletores CSS** para localizar elementos.

É uma das estratégias mais importantes do Selenium porque permite criar seletores muito mais específicos.

Exemplo:

```html
<input
    id="email"
    class="campo"
    type="text"
>
```

Podemos procurar pelo ID:

```python
driver.find_element(
    By.CSS_SELECTOR,
    "#email"
)
```

Pela classe:

```python
driver.find_element(
    By.CSS_SELECTOR,
    ".campo"
)
```

Pelo tipo:

```python
driver.find_element(
    By.CSS_SELECTOR,
    "input"
)
```

---

## Estrutura dos seletores CSS

Alguns exemplos básicos:

|Seletor|Significado|
|---|---|
|`#email`|elemento com `id="email"`|
|`.campo`|elemento com classe `campo`|
|`input`|elementos `<input>`|
|`a`|elementos `<a>`|
|`div`|elementos `<div>`|

---

## Selecionando por atributo

HTML:

```html
<input
    name="q"
    type="text"
>
```

Podemos utilizar:

```python
driver.find_element(
    By.CSS_SELECTOR,
    'input[name="q"]'
)
```

Aqui temos:

```text
input
  ↓
tipo da tag

[name="q"]
  ↓
atributo
```

---

# O seletor do seu código

No seu programa você utiliza:

```python
By.CSS_SELECTOR,
"div.yuRUbf a"
```

Isso significa:

```text
div.yuRUbf a
│   │      │
│   │      └── elemento <a>
│   │
│   └────────── classe yuRUbf
│
└────────────── elemento <div>
```

O espaço entre:

```css
div.yuRUbf a
```

significa que queremos encontrar um elemento `<a>` que esteja dentro de um:

```html
<div class="yuRUbf">
```

Conceitualmente:

```text
<div class="yuRUbf">
        │
        └── <a>
```

---

## Outro exemplo

HTML:

```html
<div class="resultado">
    <a href="https://example.com">
        Example
    </a>
</div>
```

Podemos localizar o link com:

```python
link = driver.find_element(
    By.CSS_SELECTOR,
    "div.resultado a"
)
```

---

# `By.XPATH`

## O que é?

`By.XPATH` utiliza XPath para localizar elementos dentro do documento HTML.

Exemplo:

```html
<input
    name="q"
    type="text"
>
```

Podemos utilizar:

```python
driver.find_element(
    By.XPATH,
    '//input[@name="q"]'
)
```

Aqui:

```text
//input
   ↓
procure elementos input

[@name="q"]
   ↓
cujo atributo name seja q
```

---

## Parâmetros

|Argumento|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`By.XPATH`|estratégia|Sim|Informa que será utilizado XPath|
|expressão XPath|`str`|Sim|Define o elemento procurado|

---

## Exemplos

Por ID:

```python
driver.find_element(
    By.XPATH,
    '//*[@id="login"]'
)
```

Por `name`:

```python
driver.find_element(
    By.XPATH,
    '//input[@name="q"]'
)
```

Por texto:

```python
driver.find_element(
    By.XPATH,
    '//button[text()="Entrar"]'
)
```

---

# CSS Selector x XPath

Os dois conseguem realizar buscas bastante específicas.

Exemplo com CSS:

```python
driver.find_element(
    By.CSS_SELECTOR,
    'input[name="q"]'
)
```

Exemplo equivalente usando XPath:

```python
driver.find_element(
    By.XPATH,
    '//input[@name="q"]'
)
```

Para seletores simples, CSS costuma ser bastante legível.

XPath possui recursos próprios para navegar pela estrutura do documento e trabalhar com relações entre elementos.

---

# Resumo das estratégias

```text
By.ID
   ↓
atributo id

By.NAME
   ↓
atributo name

By.CLASS_NAME
   ↓
classe CSS

By.TAG_NAME
   ↓
nome da tag HTML

By.CSS_SELECTOR
   ↓
seletor CSS

By.XPATH
   ↓
expressão XPath
```

Exemplo:

```python
driver.find_element(
    By.ID,
    "login"
)
```

```python
driver.find_element(
    By.NAME,
    "q"
)
```

```python
driver.find_element(
    By.CLASS_NAME,
    "resultado"
)
```

```python
driver.find_element(
    By.TAG_NAME,
    "input"
)
```

```python
driver.find_element(
    By.CSS_SELECTOR,
    "input[name='q']"
)
```

```python
driver.find_element(
    By.XPATH,
    '//input[@name="q"]'
)
```

---

# Como escolher o locator?

Uma regra prática é procurar primeiro por um identificador simples e confiável.

Por exemplo:

```text
ID
 ↓
NAME
 ↓
CSS_SELECTOR
 ↓
XPATH
```

Mas isso não significa que exista uma ordem obrigatória.

A escolha depende da estrutura do HTML.

Se existe:

```html
<input id="email">
```

podemos simplesmente utilizar:

```python
By.ID
```

Se temos:

```html
<input name="q">
```

podemos utilizar:

```python
By.NAME
```

Se precisamos de uma estrutura mais específica:

```python
By.CSS_SELECTOR
```

ou:

```python
By.XPATH
```

podem ser melhores.

---

# O segundo argumento de `find_element()`

Uma coisa importante é entender que:

```python
driver.find_element(
    By.NAME,
    "q"
)
```

possui dois argumentos relacionados à busca:

```text
(By.NAME, "q")
   │       │
   │       └── valor utilizado na busca
   │
   └────────── estratégia de localização
```

Isso é exatamente o que aparece no seu código:

```python
(By.NAME, "q")
```

e:

```python
(By.CSS_SELECTOR, "div.yuRUbf a")
```

Essas duas partes formam um **locator**.

Podemos pensar:

```text
locator
│
├── estratégia
│
└── valor
```

Exemplo:

```python
(By.NAME, "q")
```

```text
estratégia → By.NAME
valor      → "q"
```

---

# `By` não encontra o elemento sozinho

Um ponto importante:

```python
By.NAME
```

sozinho não procura nada.

Ele apenas informa **como a busca deve ser realizada**.

Quem efetivamente realiza a busca é, por exemplo:

```python
driver.find_element(...)
```

ou:

```python
driver.find_elements(...)
```

Portanto:

```text
By
 ↓
define a estratégia

find_element()
 ↓
realiza a busca
```

---

# Estrutura mental

Podemos representar:

```text
driver
   │
   ▼
find_element()
   │
   ├── By.ID
   ├── By.NAME
   ├── By.CLASS_NAME
   ├── By.TAG_NAME
   ├── By.CSS_SELECTOR
   └── By.XPATH
```

Na próxima etapa veremos a diferença entre:

```python
find_element()
```

e:

```python
find_elements()
```

Essa diferença é fundamental para entender por que seu código faz:

```python
resultados = driver.find_elements(
    By.CSS_SELECTOR,
    "div.yuRUbf a"
)
```

e depois:

```python
link = resultados[numero]
```

---

# Resumo

`By` define **como o Selenium deve localizar um elemento**.

Principais estratégias:

```python
By.ID
By.NAME
By.CLASS_NAME
By.TAG_NAME
By.CSS_SELECTOR
By.XPATH
```

Um locator possui:

```python
(By.NAME, "q")
```

onde:

```text
By.NAME
   ↓
estratégia

"q"
   ↓
valor procurado
```

Exemplo:

```python
pesquisa = driver.find_element(
    By.NAME,
    "q"
)
```

significa:

```text
procure um elemento
   ↓
utilizando o atributo name
   ↓
com valor "q"
```

Enquanto:

```python
resultados = driver.find_elements(
    By.CSS_SELECTOR,
    "div.yuRUbf a"
)
```

significa:

```text
procure TODOS os elementos
   ↓
utilizando CSS Selector
   ↓
que correspondam a "div.yuRUbf a"
```