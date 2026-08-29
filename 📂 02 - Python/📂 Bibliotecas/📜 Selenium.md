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

---


# Busca de elementos

## O que é `find_element()`?

`find_element()` é um método utilizado para **localizar um único elemento** dentro da página.

Exemplo:

```python
elemento = driver.find_element(
    By.ID,
    "login"
)
```

O Selenium procura um elemento que corresponda ao locator informado.

Se encontrar, retorna um objeto:

```text
WebElement
```

Esse objeto representa o elemento HTML encontrado.

---

## Sintaxe

```python
driver.find_element(
    by,
    value
)
```

Os principais argumentos são:

|Parâmetro|Tipo|Obrigatório|Padrão|Descrição|
|---|---|---|---|---|
|`by`|`str`|Sim|—|Estratégia utilizada para localizar o elemento|
|`value`|`str`|Sim|—|Valor utilizado pela estratégia|

Na prática, normalmente utilizamos constantes da classe `By`:

```python
driver.find_element(
    By.ID,
    "login"
)
```

---

## O que acontece quando usamos `find_element()`?

Imagine:

```html
<input id="email">
```

Quando fazemos:

```python
email = driver.find_element(
    By.ID,
    "email"
)
```

o Selenium:

```text
procura na página
      ↓
encontra <input id="email">
      ↓
cria/retorna uma referência ao elemento
      ↓
email
```

A variável:

```python
email
```

passa a representar aquele elemento.

---

## O retorno de `find_element()`

Quando encontra o elemento, o retorno é um:

```text
WebElement
```

Por isso podemos fazer:

```python
email.click()
```

ou:

```python
email.send_keys(
    "teste@example.com"
)
```

ou:

```python
print(email.text)
```

O `WebElement` possui seus próprios métodos e propriedades.

---

# O que é `WebElement`?

`WebElement` representa um elemento HTML que foi localizado pelo Selenium.

Por exemplo:

```html
<button id="entrar">
    Entrar
</button>
```

Depois:

```python
botao = driver.find_element(
    By.ID,
    "entrar"
)
```

Agora:

```python
botao
```

representa o elemento:

```html
<button id="entrar">
    Entrar
</button>
```

Podemos pensar:

```text
HTML
 │
 └── <button id="entrar">
             │
             ▼
        find_element()
             │
             ▼
         WebElement
             │
      ┌──────┼────────┐
      ▼      ▼        ▼
    click  text   get_attribute()
```

---

# `find_elements()`

## O que é?

`find_elements()` é semelhante ao `find_element()`, mas em vez de procurar **um elemento**, procura **todos os elementos que correspondem ao locator**.

Exemplo:

```html
<a href="https://example.com">
    Example
</a>

<a href="https://example.org">
    Example.org
</a>

<a href="https://example.net">
    Example.net
</a>
```

Podemos fazer:

```python
links = driver.find_elements(
    By.TAG_NAME,
    "a"
)
```

Agora:

```python
links
```

contém vários elementos.

---

## Sintaxe

```python
driver.find_elements(
    by,
    value
)
```

Os parâmetros são:

|Parâmetro|Tipo|Obrigatório|Padrão|Descrição|
|---|---|---|---|---|
|`by`|`str`|Sim|—|Estratégia utilizada para localizar|
|`value`|`str`|Sim|—|Valor utilizado na busca|

---

## Retorno de `find_elements()`

Diferentemente de `find_element()`, o retorno é uma lista de `WebElement`.

Conceitualmente:

```python
[
    WebElement,
    WebElement,
    WebElement
]
```

Por exemplo:

```python
links = driver.find_elements(
    By.TAG_NAME,
    "a"
)
```

Podemos percorrer:

```python
for link in links:
    print(link.text)
```

---

# Diferença entre `find_element()` e `find_elements()`

Essa diferença é fundamental.

### `find_element()`

Retorna **um único `WebElement`**.

```python
elemento = driver.find_element(
    By.ID,
    "login"
)
```

Resultado conceitual:

```text
WebElement
```

---

### `find_elements()`

Retorna **uma lista de `WebElement`**.

```python
elementos = driver.find_elements(
    By.TAG_NAME,
    "a"
)
```

Resultado conceitual:

```text
[
    WebElement,
    WebElement,
    WebElement
]
```

---

# E se nenhum elemento for encontrado?

Existe uma diferença importante.

Com:

```python
driver.find_element(...)
```

se nenhum elemento for encontrado, o Selenium lança uma exceção:

```text
NoSuchElementException
```

Exemplo:

```python
try:

    elemento = driver.find_element(
        By.ID,
        "elemento-inexistente"
    )

except Exception as erro:

    print(erro)
```

---

Com:

```python
driver.find_elements(...)
```

se nenhum elemento for encontrado, normalmente o retorno é uma lista vazia:

```python
[]
```

Exemplo:

```python
elementos = driver.find_elements(
    By.ID,
    "elemento-inexistente"
)

print(elementos)
```

Resultado:

```text
[]
```

Isso é uma diferença extremamente importante.

---

# Índices de `find_elements()`

Como `find_elements()` retorna uma lista, podemos utilizar índices.

Exemplo:

```python
links = driver.find_elements(
    By.TAG_NAME,
    "a"
)
```

Primeiro elemento:

```python
links[0]
```

Segundo:

```python
links[1]
```

Terceiro:

```python
links[2]
```

Exemplo:

```python
link = links[0]

print(link.text)
```

---

# Exemplo do seu código

Você possui:

```python
resultados = driver.find_elements(
    By.CSS_SELECTOR,
    "div.yuRUbf a"
)
```

O Selenium procura todos os elementos que correspondem a:

```css
div.yuRUbf a
```

O resultado é uma lista:

```text
resultados
│
├── WebElement
├── WebElement
├── WebElement
├── WebElement
└── ...
```

Depois seu código faz:

```python
link = resultados[numero]
```

Por exemplo, se:

```python
numero = 0
```

então:

```python
link = resultados[0]
```

pega o primeiro resultado.

Se:

```python
numero = 1
```

então:

```python
link = resultados[1]
```

pega o segundo.

---

# `len()`

Como `find_elements()` retorna uma lista, podemos utilizar:

```python
len(resultados)
```

para descobrir quantos elementos foram encontrados.

Exemplo:

```python
resultados = driver.find_elements(
    By.CSS_SELECTOR,
    "div.yuRUbf a"
)

print(
    len(resultados)
)
```

Se foram encontrados 10 elementos:

```text
10
```

---

# A lógica do seu `if`

Seu código possui:

```python
if numero >= len(resultados):
    print("Não existem mais resultados disponíveis.")
    break
```

Imagine que:

```text
len(resultados) = 5
```

Os índices válidos são:

```text
0
1
2
3
4
```

Quando:

```python
numero = 5
```

temos:

```python
5 >= 5
```

que é:

```text
True
```

Então o código executa:

```python
break
```

Isso evita tentar:

```python
resultados[5]
```

porque o índice `5` não existe nessa lista.

---

# `WebElement.click()`

## O que é?

Depois que encontramos um elemento, podemos utilizar:

```python
elemento.click()
```

para clicar nele.

Exemplo:

```python
botao = driver.find_element(
    By.ID,
    "entrar"
)

botao.click()
```

---

## Sintaxe

```python
elemento.click()
```

Não possui parâmetros obrigatórios.

O elemento já foi definido através do objeto `WebElement`.

---

# `send_keys()`

## O que é?

`send_keys()` envia teclas/caracteres para um elemento.

É muito utilizado para preencher campos.

Exemplo:

```html
<input name="q">
```

Python:

```python
pesquisa = driver.find_element(
    By.NAME,
    "q"
)

pesquisa.send_keys(
    "Selenium Python"
)
```

O resultado será semelhante a digitar manualmente:

```text
Selenium Python
```

no campo.

---

## Sintaxe

```python
elemento.send_keys(*value)
```

O argumento recebe os caracteres/teclas que devem ser enviados.

Exemplo:

```python
pesquisa.send_keys(
    "Selenium"
)
```

---

## Tipo

O texto normalmente é uma:

```python
str
```

Exemplo:

```python
pesquisa.send_keys(
    "Cyber Security"
)
```

---

# `send_keys()` também pode enviar teclas especiais

O Selenium possui teclas especiais através de:

```python
from selenium.webdriver.common.keys import Keys
```

Por exemplo:

```python
campo.send_keys(
    Keys.ENTER
)
```

Isso envia a tecla Enter.

Outro exemplo:

```python
campo.send_keys(
    "texto",
    Keys.ENTER
)
```

Conceitualmente:

```text
digita "texto"
      ↓
pressiona ENTER
```

---

# `clear()`

## O que é?

`clear()` limpa o conteúdo de um campo de entrada.

Imagine:

```text
+----------------------+
| Selenium Python      |
+----------------------+
```

Depois:

```python
campo.clear()
```

fica:

```text
+----------------------+
|                      |
+----------------------+
```

---

## Sintaxe

```python
campo.clear()
```

Não possui parâmetros obrigatórios.

---

## Exemplo

```python
campo = driver.find_element(
    By.NAME,
    "q"
)

campo.send_keys(
    "primeira pesquisa"
)

campo.clear()

campo.send_keys(
    "segunda pesquisa"
)
```

---

# `WebElement.text`

## O que é?

`text` é uma propriedade que permite obter o texto visível associado ao elemento.

HTML:

```html
<h1>
    Página inicial
</h1>
```

Python:

```python
titulo = driver.find_element(
    By.TAG_NAME,
    "h1"
)

print(titulo.text)
```

Resultado:

```text
Página inicial
```

---

## É método ou propriedade?

`text` é uma propriedade.

Por isso utilizamos:

```python
elemento.text
```

e não:

```python
elemento.text()
```

---

## Tipo do retorno

Normalmente retorna:

```python
str
```

---

# `get_attribute()`

## O que é?

`get_attribute()` permite obter o valor de um atributo HTML de um `WebElement`.

Imagine:

```html
<a
    href="https://example.com"
    class="link"
>
    Example
</a>
```

Podemos obter o `href`:

```python
link = driver.find_element(
    By.TAG_NAME,
    "a"
)

url = link.get_attribute(
    "href"
)

print(url)
```

Resultado:

```text
https://example.com
```

---

## Sintaxe

```python
elemento.get_attribute(name)
```

### Parâmetros

|Parâmetro|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`name`|`str`|Sim|Nome do atributo HTML que será obtido|

Exemplo:

```python
link.get_attribute(
    "href"
)
```

Aqui:

```text
"href"
   ↓
nome do atributo
```

---

# Exemplos de atributos

HTML:

```html
<input
    id="email"
    name="email"
    type="text"
    placeholder="Digite seu email"
>
```

Podemos obter:

```python
elemento.get_attribute(
    "id"
)
```

```python
elemento.get_attribute(
    "name"
)
```

```python
elemento.get_attribute(
    "type"
)
```

```python
elemento.get_attribute(
    "placeholder"
)
```

---

# O `get_attribute("href")` do seu código

Você utiliza:

```python
url = link.get_attribute(
    "href"
)
```

Imagine que `link` represente:

```html
<a href="https://example.com">
    Example
</a>
```

Então:

```python
link.get_attribute("href")
```

retorna:

```text
https://example.com
```

Isso permite obter o endereço do link **sem precisar clicar nele primeiro**.

---

# `click()` x `get_attribute()`

São operações diferentes.

```python
link.click()
```

interage com o elemento.

Enquanto:

```python
link.get_attribute(
    "href"
)
```

consulta uma informação do elemento.

Podemos fazer ambos:

```python
link = driver.find_element(
    By.TAG_NAME,
    "a"
)

url = link.get_attribute(
    "href"
)

link.click()
```

Primeiro obtemos a URL e depois clicamos.

---

# `WebElement` no seu programa

No seu código:

```python
link = resultados[numero]
```

`link` é um:

```text
WebElement
```

Por isso você consegue fazer:

```python
link.get_attribute(
    "href"
)
```

e:

```python
link.click()
```

O objeto `link` possui métodos e propriedades próprios.

---

# Estrutura do `WebElement`

Podemos organizar:

```text
WebElement
│
├── click()
│
├── send_keys()
│
├── clear()
│
├── text
│
└── get_attribute()
```

### `click()`

Interage clicando no elemento.

```python
elemento.click()
```

### `send_keys()`

Envia texto/teclas.

```python
elemento.send_keys(
    "texto"
)
```

### `clear()`

Limpa o conteúdo de um campo.

```python
elemento.clear()
```

### `text`

Obtém o texto do elemento.

```python
elemento.text
```

### `get_attribute()`

Obtém o valor de um atributo HTML.

```python
elemento.get_attribute(
    "href"
)
```

---

# Exemplo completo

HTML hipotético:

```html
<input
    id="pesquisa"
    name="q"
    type="text"
    placeholder="Pesquisar"
>

<a
    href="https://example.com"
    class="resultado"
>
    Example
</a>
```

Python:

```python
campo = driver.find_element(
    By.ID,
    "pesquisa"
)

campo.clear()

campo.send_keys(
    "Selenium Python"
)

link = driver.find_element(
    By.CLASS_NAME,
    "resultado"
)

print(
    link.text
)

print(
    link.get_attribute(
        "href"
    )
)

link.click()
```

Fluxo:

```text
find_element()
      ↓
WebElement
      ↓
clear()
      ↓
send_keys()
      ↓
find_element()
      ↓
WebElement
      ↓
text
      ↓
get_attribute()
      ↓
click()
```

---

# Resumo

```text
driver
│
├── find_element()
│      ↓
│   WebElement
│
└── find_elements()
       ↓
   list[WebElement]
```

`find_element()`:

```python
elemento = driver.find_element(
    By.ID,
    "login"
)
```

Retorna um `WebElement`.

`find_elements()`:

```python
elementos = driver.find_elements(
    By.TAG_NAME,
    "a"
)
```

Retorna uma lista de `WebElement`.

Depois que temos um `WebElement`, podemos utilizar:

```python
elemento.click()
```

```python
elemento.send_keys(
    "texto"
)
```

```python
elemento.clear()
```

```python
elemento.text
```

```python
elemento.get_attribute(
    "href"
)
```

A diferença entre:

```python
find_element()
```

e:

```python
find_elements()
```

é uma das partes mais importantes para dominar o Selenium:

```text
find_element()
      ↓
1 elemento
      ↓
WebElement

find_elements()
      ↓
vários elementos
      ↓
lista de WebElement
```