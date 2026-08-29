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


---


# Esperas no Selenium

## Por que o Selenium precisa esperar?

Uma página web não necessariamente carrega tudo de uma vez.

Quando fazemos:

```python
driver.get("https://www.google.com")
```

o navegador começa a carregar a página.

Dependendo do site, alguns elementos podem aparecer imediatamente, enquanto outros podem ser criados depois por JavaScript.

Por exemplo:

```text
driver.get()
    ↓
HTML começa a carregar
    ↓
JavaScript executa
    ↓
elementos são criados/modificados
    ↓
página fica pronta para interação
```

Se o código tentar localizar um elemento antes de ele existir, podemos receber uma exceção.

Exemplo:

```python
pesquisa = driver.find_element(
    By.NAME,
    "q"
)
```

Se o campo ainda não estiver disponível naquele momento, o Selenium pode falhar.

É por isso que existem as **esperas**.

---

# Tipos de espera

No Selenium existem diferentes formas de esperar.

As duas ideias mais importantes são:

```text
Implicit Wait
    ↓
espera global

Explicit Wait
    ↓
espera uma condição específica
```

Nesta anotação vamos focar principalmente em:

```python
WebDriverWait
```

e:

```python
EC
```

---

# `WebDriverWait`

## O que é?

`WebDriverWait` permite criar uma **espera explícita**.

Em vez de simplesmente falar:

```text
"espere 10 segundos"
```

podemos dizer:

```text
"espere no máximo 10 segundos
até determinada condição acontecer"
```

Exemplo:

```python
wait = WebDriverWait(
    driver,
    10
)
```

Isso cria um objeto responsável por realizar esperas.

---

## Importação

```python
from selenium.webdriver.support.ui import WebDriverWait
```

---

# Sintaxe

A forma básica é:

```python
WebDriverWait(
    driver,
    timeout
)
```

Os principais parâmetros são:

|Parâmetro|Tipo|Obrigatório|Padrão|Descrição|
|---|---|---|---|---|
|`driver`|`WebDriver`|Sim|—|Instância do navegador que será observada|
|`timeout`|`float`|Sim|—|Tempo máximo de espera em segundos|

Exemplo:

```python
wait = WebDriverWait(
    driver,
    10
)
```

Significa:

```text
utilize este navegador
      ↓
driver

espere no máximo
      ↓
10 segundos
```

---

# O `10` não significa necessariamente "durma por 10 segundos"

Essa diferença é importante.

Quando fazemos:

```python
wait = WebDriverWait(
    driver,
    10
)
```

não estamos dizendo:

```text
"pare o programa por 10 segundos"
```

Estamos dizendo:

```text
"quando eu pedir para você esperar uma condição,
espere no máximo 10 segundos por ela"
```

Se a condição acontecer depois de 2 segundos, a espera termina.

Exemplo:

```text
tempo: 0s
   ↓
elemento ainda não existe

tempo: 1s
   ↓
ainda não existe

tempo: 2s
   ↓
elemento apareceu

   ↓
continua imediatamente
```

Não precisa esperar os 10 segundos completos.

---

# `until()`

## O que é?

Depois de criar:

```python
wait = WebDriverWait(
    driver,
    10
)
```

utilizamos:

```python
wait.until(...)
```

para dizer **qual condição precisa acontecer**.

---

## Sintaxe

```python
wait.until(
    condition
)
```

O argumento principal é uma condição/função que será verificada até:

```text
condição verdadeira
```

ou até:

```text
timeout
```

---

# Exemplo simples

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Podemos ler isso de dentro para fora:

```text
(By.NAME, "q")
        ↓
locator

presence_of_element_located(...)
        ↓
condição

wait.until(...)
        ↓
espere até a condição acontecer
```

Portanto:

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

significa aproximadamente:

> Espere até existir na página um elemento localizado pelo `name="q"`.

---

# `EC`

## O que é?

`EC` é uma abreviação normalmente utilizada para:

```text
Expected Conditions
```

São condições prontas fornecidas pelo Selenium para serem usadas com `WebDriverWait`.

Importação:

```python
from selenium.webdriver.support import expected_conditions as EC
```

Aqui:

```python
as EC
```

cria um apelido.

Em vez de escrever:

```python
expected_conditions.presence_of_element_located(...)
```

podemos escrever:

```python
EC.presence_of_element_located(...)
```

---

# O que é uma Expected Condition?

É uma condição que o Selenium verifica repetidamente durante a espera.

Por exemplo:

```python
EC.presence_of_element_located(...)
```

pergunta:

```text
"O elemento já está presente?"
```

Enquanto:

```python
EC.visibility_of_element_located(...)
```

pergunta:

```text
"O elemento está visível?"
```

E:

```python
EC.element_to_be_clickable(...)
```

verifica se o elemento pode ser clicado.

---

# Estrutura geral

Podemos pensar em:

```text
WebDriverWait
      │
      ▼
    until()
      │
      ▼
     EC
      │
      └── condição
```

Exemplo:

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

---

# `presence_of_element_located()`

## O que é?

Essa condição espera até que um elemento esteja **presente no DOM**.

Importação através de:

```python
from selenium.webdriver.support import expected_conditions as EC
```

Uso:

```python
EC.presence_of_element_located(
    locator
)
```

---

## Parâmetro

|Parâmetro|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`locator`|`tuple`|Sim|Define como localizar o elemento|

Exemplo:

```python
(By.NAME, "q")
```

---

# O que significa DOM?

DOM significa:

```text
Document Object Model
```

É a representação da estrutura HTML da página que o navegador mantém.

Imagine:

```html
<html>
    <body>
        <input name="q">
    </body>
</html>
```

O navegador transforma isso em uma estrutura que pode ser manipulada pelo JavaScript e acessada pelo Selenium.

De forma simplificada:

```text
HTML
 │
 ▼
DOM
 │
 ├── html
 │    └── body
 │         └── input
 │
 ▼
Selenium pode localizar elementos
```

---

# Presença não significa visibilidade

Essa diferença é muito importante.

`presence_of_element_located()` verifica se o elemento **existe no DOM**.

Não significa necessariamente que ele:

- está visível;
    
- está na área visível da tela;
    
- pode ser clicado.
    

Por exemplo, um elemento pode existir no DOM mas estar escondido:

```html
<div style="display: none;">
    Conteúdo
</div>
```

O elemento existe, mas não está visível.

Nesse caso, `presence_of_element_located()` pode considerar a condição satisfeita.

---

# Exemplo do Google no seu código

Você utiliza:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Vamos desmontar completamente.

### Primeiro:

```python
(By.NAME, "q")
```

é o locator.

Significa:

```text
procure pelo atributo
name="q"
```

---

### Depois:

```python
EC.presence_of_element_located(
    (By.NAME, "q")
)
```

cria uma condição que significa:

```text
espere o elemento com name="q"
estar presente no DOM
```

---

### Finalmente:

```python
wait.until(
    ...
)
```

manda o `WebDriverWait` aguardar essa condição.

---

### Tudo junto:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Significa:

```text
WebDriverWait
     ↓
até que...
     ↓
exista um elemento
     ↓
localizado por NAME
     ↓
com valor "q"
     ↓
retorne esse elemento
```

Por isso você consegue fazer imediatamente:

```python
pesquisa.send_keys(
    docs
)
```

Porque `pesquisa` recebe o `WebElement` encontrado.

---

# O retorno de `presence_of_element_located()`

Quando a condição é satisfeita, ela retorna o elemento encontrado.

Conceitualmente:

```text
presence_of_element_located()
          ↓
      WebElement
```

Então:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

faz com que:

```text
pesquisa
   ↓
WebElement
```

Por isso:

```python
pesquisa.send_keys(docs)
```

funciona.

---

# `visibility_of_element_located()`

## O que é?

Essa condição espera até que o elemento esteja **presente e visível**.

Uso:

```python
wait.until(
    EC.visibility_of_element_located(
        (By.ID, "login")
    )
)
```

A diferença conceitual:

```text
presence
    ↓
existe no DOM

visibility
    ↓
existe no DOM
+
está visível
```

---

## Parâmetro

|Parâmetro|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`locator`|`tuple`|Sim|Locator do elemento|

Exemplo:

```python
(By.ID, "login")
```

---

# `presence` x `visibility`

Imagine:

```html
<button id="entrar" style="display:none;">
    Entrar
</button>
```

O elemento existe.

Então:

```python
EC.presence_of_element_located(
    (By.ID, "entrar")
)
```

pode considerar a condição satisfeita.

Mas:

```python
EC.visibility_of_element_located(
    (By.ID, "entrar")
)
```

não será satisfeita enquanto o botão continuar invisível.

---

# `element_to_be_clickable()`

## O que é?

Essa condição espera até que o elemento esteja em uma situação em que possa ser clicado.

Uso:

```python
botao = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "entrar")
    )
)
```

Depois:

```python
botao.click()
```

---

## Parâmetro

|Parâmetro|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`locator`|`tuple`|Sim|Locator do elemento|

Exemplo:

```python
(By.ID, "entrar")
```

---

# Comparando as três

```text
presence_of_element_located()
        ↓
elemento existe no DOM


visibility_of_element_located()
        ↓
elemento existe
+
está visível


element_to_be_clickable()
        ↓
elemento está visível
+
habilitado para interação/click
```

---

# Qual usar?

Não existe uma condição universalmente melhor.

Depende do que você pretende fazer.

Se apenas precisa que o elemento exista:

```python
EC.presence_of_element_located(...)
```

Se precisa que esteja visível:

```python
EC.visibility_of_element_located(...)
```

Se pretende clicar:

```python
EC.element_to_be_clickable(...)
```

---

# `WebDriverWait` + `EC` no seu código

Você possui:

```python
wait = WebDriverWait(
    driver,
    10
)
```

Depois:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Depois:

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

Ou seja, você criou uma espera reutilizável:

```text
wait
 ↓
WebDriverWait(driver, 10)
```

e pode utilizar várias vezes:

```python
wait.until(...)
wait.until(...)
wait.until(...)
```

Isso evita criar um novo `WebDriverWait` toda vez.

---

# `presence_of_all_elements_located()`

Você também utiliza:

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

Essa condição é semelhante a:

```python
EC.presence_of_element_located()
```

mas trabalha com **todos os elementos que correspondem ao locator**.

---

## Parâmetro

|Parâmetro|Tipo|Obrigatório|Descrição|
|---|---|---|---|
|`locator`|`tuple`|Sim|Define como os elementos serão localizados|

Exemplo:

```python
(By.CSS_SELECTOR, "div.yuRUbf a")
```

---

## Retorno

Quando encontra os elementos, retorna uma lista:

```text
[
    WebElement,
    WebElement,
    WebElement
]
```

Isso é compatível com:

```python
resultados = driver.find_elements(
    By.CSS_SELECTOR,
    "div.yuRUbf a"
)
```

---

# `presence_of_element_located()` x `presence_of_all_elements_located()`

### Um elemento

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Conceito:

```text
espere um elemento
```

### Vários elementos

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

Conceito:

```text
espere os elementos correspondentes
```

---

# O que `until()` faz internamente?

De forma simplificada, podemos imaginar:

```text
until(condição)
      │
      ▼
verifica condição
      │
      ├── aconteceu?
      │      │
      │      └── SIM → retorna resultado
      │
      └── NÃO
             │
             ▼
        espera um pouco
             │
             ▼
        verifica novamente
```

Esse processo continua até:

```text
condição satisfeita
```

ou:

```text
tempo máximo atingido
```

---

# O que acontece quando o timeout é atingido?

Se a condição não acontecer dentro do tempo definido:

```python
wait = WebDriverWait(
    driver,
    10
)
```

e os 10 segundos forem ultrapassados, o Selenium lança:

```text
TimeoutException
```

Por isso podemos tratar:

```python
from selenium.common.exceptions import TimeoutException
```

Exemplo:

```python
try:

    elemento = wait.until(
        EC.presence_of_element_located(
            (By.ID, "login")
        )
    )

except TimeoutException:

    print(
        "Elemento não apareceu dentro do tempo."
    )
```

---

# `lambda` com `until()`

No seu código existe outra forma importante de usar `until()`:

```python
wait.until(
    lambda d: d.execute_script(
        "return document.readyState"
    ) == "complete"
)
```

Aqui não estamos utilizando uma condição pronta do `EC`.

Estamos fornecendo nossa própria função:

```python
lambda d: ...
```

A ideia é:

```text
WebDriverWait
      ↓
until()
      ↓
executa a função
      ↓
verifica o resultado
```

Se a função retornar um valor considerado verdadeiro, a espera termina.

No seu caso:

```javascript
document.readyState
```

é consultado.

Quando retornar:

```text
"complete"
```

a condição:

```python
== "complete"
```

será verdadeira.

---

# `until()` pode esperar condições diferentes

Exemplo com elemento:

```python
wait.until(
    EC.presence_of_element_located(
        (By.ID, "login")
    )
)
```

Exemplo com visibilidade:

```python
wait.until(
    EC.visibility_of_element_located(
        (By.ID, "login")
    )
)
```

Exemplo com clique:

```python
wait.until(
    EC.element_to_be_clickable(
        (By.ID, "login")
    )
)
```

Exemplo com uma função própria:

```python
wait.until(
    lambda d: d.title == "Google"
)
```

---

# `time.sleep()` x `WebDriverWait`

No seu programa você também utiliza:

```python
time.sleep(2)
```

Isso é diferente de:

```python
wait.until(...)
```

### `time.sleep(2)`

Significa:

```text
pare por 2 segundos
```

Não importa se a página terminou antes.

```text
0s ─────────────── 2s
       espera
```

---

### `WebDriverWait`

Significa:

```text
espere até uma condição
por no máximo X segundos
```

Se a condição acontecer rapidamente:

```text
0s ── condição satisfeita
      ↓
   continua
```

---

# Por que `WebDriverWait` geralmente é melhor?

Imagine:

```python
time.sleep(10)
```

mas o elemento aparece em:

```text
1 segundo
```

Você desperdiçou aproximadamente:

```text
9 segundos
```

Com:

```python
wait.until(...)
```

a execução continua assim que a condição é satisfeita.

Por isso, para sincronizar a automação com uma página dinâmica, `WebDriverWait` costuma ser muito mais apropriado.

---

# Estrutura recomendada

Um padrão bastante comum:

```python
wait = WebDriverWait(
    driver,
    10
)

elemento = wait.until(
    EC.presence_of_element_located(
        (By.ID, "elemento")
    )
)
```

Depois:

```python
elemento.click()
```

ou:

```python
elemento.send_keys(
    "texto"
)
```

---

# Resumo mental

Quando encontrar:

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

leia:

```text
wait
 ↓
espere
 ↓
until
 ↓
até que
 ↓
presence_of_element_located
 ↓
o elemento esteja presente
 ↓
(By.NAME, "q")
 ↓
usando NAME
com valor "q"
```

E:

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

leia:

```text
espere até existirem
os elementos correspondentes
ao CSS Selector
"div.yuRUbf a"
```

---

# Estrutura das esperas

```text
Esperas
│
├── WebDriverWait
│      │
│      └── until()
│
└── EC
       │
       ├── presence_of_element_located()
       │
       ├── presence_of_all_elements_located()
       │
       ├── visibility_of_element_located()
       │
       └── element_to_be_clickable()
```

---

# Resumo

`WebDriverWait`:

```python
wait = WebDriverWait(
    driver,
    10
)
```

cria uma espera explícita com tempo máximo de 10 segundos.

`until()`:

```python
wait.until(
    condição
)
```

espera até que uma condição seja satisfeita.

`EC`:

```python
EC.presence_of_element_located(...)
```

fornece condições prontas para utilizar com `WebDriverWait`.

`presence_of_element_located()`:

```python
EC.presence_of_element_located(
    (By.NAME, "q")
)
```

espera um elemento estar presente no DOM.

`visibility_of_element_located()`:

```python
EC.visibility_of_element_located(
    (By.ID, "login")
)
```

espera o elemento estar presente e visível.

`element_to_be_clickable()`:

```python
EC.element_to_be_clickable(
    (By.ID, "entrar")
)
```

espera o elemento estar em condição de ser clicado.

`presence_of_all_elements_located()`:

```python
EC.presence_of_all_elements_located(
    (By.CSS_SELECTOR, "div.yuRUbf a")
)
```

espera os elementos correspondentes ao locator e retorna uma coleção/lista de elementos.

A ideia principal é:

```text
WebDriverWait
      ↓
until()
      ↓
condição
      ↓
elemento/resultado disponível
      ↓
continua a execução
```

---


# Esperas no Selenium

## Por que o Selenium precisa esperar?

Uma página web não necessariamente carrega tudo de uma vez.

Quando fazemos:

```python
driver.get("https://www.google.com")
```

o navegador começa a carregar a página.

Dependendo do site, alguns elementos podem aparecer imediatamente, enquanto outros podem ser criados depois por JavaScript.

Por exemplo:

```text
driver.get()
    ↓
HTML começa a carregar
    ↓
JavaScript executa
    ↓
elementos são criados/modificados
    ↓
página fica pronta para interação
```

Se o código tentar localizar um elemento antes de ele existir, podemos receber uma exceção.

Exemplo:

```python
pesquisa = driver.find_element(
    By.NAME,
    "q"
)
```

Se o campo ainda não estiver disponível naquele momento, o Selenium pode falhar.

É por isso que existem as **esperas**.

---

# Tipos de espera

No Selenium existem diferentes formas de esperar.

As duas ideias mais importantes são:

```text
Implicit Wait
    ↓
espera global

Explicit Wait
    ↓
espera uma condição específica
```

Nesta anotação vamos focar principalmente em:

```python
WebDriverWait
```

e:

```python
EC
```

---

# `WebDriverWait`

## O que é?

`WebDriverWait` permite criar uma **espera explícita**.

Em vez de simplesmente falar:

```text
"espere 10 segundos"
```

podemos dizer:

```text
"espere no máximo 10 segundos
até determinada condição acontecer"
```

Exemplo:

```python
wait = WebDriverWait(
    driver,
    10
)
```

Isso cria um objeto responsável por realizar esperas.

---

## Importação

```python
from selenium.webdriver.support.ui import WebDriverWait
```

---

# Sintaxe

A forma básica é:

```python
WebDriverWait(
    driver,
    timeout
)
```

Os principais parâmetros são:

| Parâmetro | Tipo        | Obrigatório | Padrão | Descrição                                 |
| --------- | ----------- | ----------- | ------ | ----------------------------------------- |
| `driver`  | `WebDriver` | Sim         | —      | Instância do navegador que será observada |
| `timeout` | `float`     | Sim         | —      | Tempo máximo de espera em segundos        |

Exemplo:

```python
wait = WebDriverWait(
    driver,
    10
)
```

Significa:

```text
utilize este navegador
      ↓
driver

espere no máximo
      ↓
10 segundos
```

---

# O `10` não significa necessariamente "durma por 10 segundos"

Essa diferença é importante.

Quando fazemos:

```python
wait = WebDriverWait(
    driver,
    10
)
```

não estamos dizendo:

```text
"pare o programa por 10 segundos"
```

Estamos dizendo:

```text
"quando eu pedir para você esperar uma condição,
espere no máximo 10 segundos por ela"
```

Se a condição acontecer depois de 2 segundos, a espera termina.

Exemplo:

```text
tempo: 0s
   ↓
elemento ainda não existe

tempo: 1s
   ↓
ainda não existe

tempo: 2s
   ↓
elemento apareceu

   ↓
continua imediatamente
```

Não precisa esperar os 10 segundos completos.

---

# `until()`

## O que é?

Depois de criar:

```python
wait = WebDriverWait(
    driver,
    10
)
```

utilizamos:

```python
wait.until(...)
```

para dizer **qual condição precisa acontecer**.

---

## Sintaxe

```python
wait.until(
    condition
)
```

O argumento principal é uma condição/função que será verificada até:

```text
condição verdadeira
```

ou até:

```text
timeout
```

---

# Exemplo simples

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Podemos ler isso de dentro para fora:

```text
(By.NAME, "q")
        ↓
locator

presence_of_element_located(...)
        ↓
condição

wait.until(...)
        ↓
espere até a condição acontecer
```

Portanto:

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

significa aproximadamente:

> Espere até existir na página um elemento localizado pelo `name="q"`.

---

# `EC`

## O que é?

`EC` é uma abreviação normalmente utilizada para:

```text
Expected Conditions
```

São condições prontas fornecidas pelo Selenium para serem usadas com `WebDriverWait`.

Importação:

```python
from selenium.webdriver.support import expected_conditions as EC
```

Aqui:

```python
as EC
```

cria um apelido.

Em vez de escrever:

```python
expected_conditions.presence_of_element_located(...)
```

podemos escrever:

```python
EC.presence_of_element_located(...)
```

---

# O que é uma Expected Condition?

É uma condição que o Selenium verifica repetidamente durante a espera.

Por exemplo:

```python
EC.presence_of_element_located(...)
```

pergunta:

```text
"O elemento já está presente?"
```

Enquanto:

```python
EC.visibility_of_element_located(...)
```

pergunta:

```text
"O elemento está visível?"
```

E:

```python
EC.element_to_be_clickable(...)
```

verifica se o elemento pode ser clicado.

---

# Estrutura geral

Podemos pensar em:

```text
WebDriverWait
      │
      ▼
    until()
      │
      ▼
     EC
      │
      └── condição
```

Exemplo:

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

---

# `presence_of_element_located()`

## O que é?

Essa condição espera até que um elemento esteja **presente no DOM**.

Importação através de:

```python
from selenium.webdriver.support import expected_conditions as EC
```

Uso:

```python
EC.presence_of_element_located(
    locator
)
```

---

## Parâmetro

| Parâmetro | Tipo    | Obrigatório | Descrição                        |
| --------- | ------- | ----------- | -------------------------------- |
| `locator` | `tuple` | Sim         | Define como localizar o elemento |

Exemplo:

```python
(By.NAME, "q")
```

---

# O que significa DOM?

DOM significa:

```text
Document Object Model
```

É a representação da estrutura HTML da página que o navegador mantém.

Imagine:

```html
<html>
    <body>
        <input name="q">
    </body>
</html>
```

O navegador transforma isso em uma estrutura que pode ser manipulada pelo JavaScript e acessada pelo Selenium.

De forma simplificada:

```text
HTML
 │
 ▼
DOM
 │
 ├── html
 │    └── body
 │         └── input
 │
 ▼
Selenium pode localizar elementos
```

---

# Presença não significa visibilidade

Essa diferença é muito importante.

`presence_of_element_located()` verifica se o elemento **existe no DOM**.

Não significa necessariamente que ele:

* está visível;
* está na área visível da tela;
* pode ser clicado.

Por exemplo, um elemento pode existir no DOM mas estar escondido:

```html
<div style="display: none;">
    Conteúdo
</div>
```

O elemento existe, mas não está visível.

Nesse caso, `presence_of_element_located()` pode considerar a condição satisfeita.

---

# Exemplo do Google no seu código

Você utiliza:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Vamos desmontar completamente.

### Primeiro:

```python
(By.NAME, "q")
```

é o locator.

Significa:

```text
procure pelo atributo
name="q"
```

---

### Depois:

```python
EC.presence_of_element_located(
    (By.NAME, "q")
)
```

cria uma condição que significa:

```text
espere o elemento com name="q"
estar presente no DOM
```

---

### Finalmente:

```python
wait.until(
    ...
)
```

manda o `WebDriverWait` aguardar essa condição.

---

### Tudo junto:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Significa:

```text
WebDriverWait
     ↓
até que...
     ↓
exista um elemento
     ↓
localizado por NAME
     ↓
com valor "q"
     ↓
retorne esse elemento
```

Por isso você consegue fazer imediatamente:

```python
pesquisa.send_keys(
    docs
)
```

Porque `pesquisa` recebe o `WebElement` encontrado.

---

# O retorno de `presence_of_element_located()`

Quando a condição é satisfeita, ela retorna o elemento encontrado.

Conceitualmente:

```text
presence_of_element_located()
          ↓
      WebElement
```

Então:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

faz com que:

```text
pesquisa
   ↓
WebElement
```

Por isso:

```python
pesquisa.send_keys(docs)
```

funciona.

---

# `visibility_of_element_located()`

## O que é?

Essa condição espera até que o elemento esteja **presente e visível**.

Uso:

```python
wait.until(
    EC.visibility_of_element_located(
        (By.ID, "login")
    )
)
```

A diferença conceitual:

```text
presence
    ↓
existe no DOM

visibility
    ↓
existe no DOM
+
está visível
```

---

## Parâmetro

| Parâmetro | Tipo    | Obrigatório | Descrição           |
| --------- | ------- | ----------- | ------------------- |
| `locator` | `tuple` | Sim         | Locator do elemento |

Exemplo:

```python
(By.ID, "login")
```

---

# `presence` x `visibility`

Imagine:

```html
<button id="entrar" style="display:none;">
    Entrar
</button>
```

O elemento existe.

Então:

```python
EC.presence_of_element_located(
    (By.ID, "entrar")
)
```

pode considerar a condição satisfeita.

Mas:

```python
EC.visibility_of_element_located(
    (By.ID, "entrar")
)
```

não será satisfeita enquanto o botão continuar invisível.

---

# `element_to_be_clickable()`

## O que é?

Essa condição espera até que o elemento esteja em uma situação em que possa ser clicado.

Uso:

```python
botao = wait.until(
    EC.element_to_be_clickable(
        (By.ID, "entrar")
    )
)
```

Depois:

```python
botao.click()
```

---

## Parâmetro

| Parâmetro | Tipo    | Obrigatório | Descrição           |
| --------- | ------- | ----------- | ------------------- |
| `locator` | `tuple` | Sim         | Locator do elemento |

Exemplo:

```python
(By.ID, "entrar")
```

---

# Comparando as três

```text
presence_of_element_located()
        ↓
elemento existe no DOM


visibility_of_element_located()
        ↓
elemento existe
+
está visível


element_to_be_clickable()
        ↓
elemento está visível
+
habilitado para interação/click
```

---

# Qual usar?

Não existe uma condição universalmente melhor.

Depende do que você pretende fazer.

Se apenas precisa que o elemento exista:

```python
EC.presence_of_element_located(...)
```

Se precisa que esteja visível:

```python
EC.visibility_of_element_located(...)
```

Se pretende clicar:

```python
EC.element_to_be_clickable(...)
```

---

# `WebDriverWait` + `EC` no seu código

Você possui:

```python
wait = WebDriverWait(
    driver,
    10
)
```

Depois:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Depois:

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

Ou seja, você criou uma espera reutilizável:

```text
wait
 ↓
WebDriverWait(driver, 10)
```

e pode utilizar várias vezes:

```python
wait.until(...)
wait.until(...)
wait.until(...)
```

Isso evita criar um novo `WebDriverWait` toda vez.

---

# `presence_of_all_elements_located()`

Você também utiliza:

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

Essa condição é semelhante a:

```python
EC.presence_of_element_located()
```

mas trabalha com **todos os elementos que correspondem ao locator**.

---

## Parâmetro

| Parâmetro | Tipo    | Obrigatório | Descrição                                  |
| --------- | ------- | ----------- | ------------------------------------------ |
| `locator` | `tuple` | Sim         | Define como os elementos serão localizados |

Exemplo:

```python
(By.CSS_SELECTOR, "div.yuRUbf a")
```

---

## Retorno

Quando encontra os elementos, retorna uma lista:

```text
[
    WebElement,
    WebElement,
    WebElement
]
```

Isso é compatível com:

```python
resultados = driver.find_elements(
    By.CSS_SELECTOR,
    "div.yuRUbf a"
)
```

---

# `presence_of_element_located()` x `presence_of_all_elements_located()`

### Um elemento

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

Conceito:

```text
espere um elemento
```

### Vários elementos

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

Conceito:

```text
espere os elementos correspondentes
```

---

# O que `until()` faz internamente?

De forma simplificada, podemos imaginar:

```text
until(condição)
      │
      ▼
verifica condição
      │
      ├── aconteceu?
      │      │
      │      └── SIM → retorna resultado
      │
      └── NÃO
             │
             ▼
        espera um pouco
             │
             ▼
        verifica novamente
```

Esse processo continua até:

```text
condição satisfeita
```

ou:

```text
tempo máximo atingido
```

---

# O que acontece quando o timeout é atingido?

Se a condição não acontecer dentro do tempo definido:

```python
wait = WebDriverWait(
    driver,
    10
)
```

e os 10 segundos forem ultrapassados, o Selenium lança:

```text
TimeoutException
```

Por isso podemos tratar:

```python
from selenium.common.exceptions import TimeoutException
```

Exemplo:

```python
try:

    elemento = wait.until(
        EC.presence_of_element_located(
            (By.ID, "login")
        )
    )

except TimeoutException:

    print(
        "Elemento não apareceu dentro do tempo."
    )
```

---

# `lambda` com `until()`

No seu código existe outra forma importante de usar `until()`:

```python
wait.until(
    lambda d: d.execute_script(
        "return document.readyState"
    ) == "complete"
)
```

Aqui não estamos utilizando uma condição pronta do `EC`.

Estamos fornecendo nossa própria função:

```python
lambda d: ...
```

A ideia é:

```text
WebDriverWait
      ↓
until()
      ↓
executa a função
      ↓
verifica o resultado
```

Se a função retornar um valor considerado verdadeiro, a espera termina.

No seu caso:

```javascript
document.readyState
```

é consultado.

Quando retornar:

```text
"complete"
```

a condição:

```python
== "complete"
```

será verdadeira.

---

# `until()` pode esperar condições diferentes

Exemplo com elemento:

```python
wait.until(
    EC.presence_of_element_located(
        (By.ID, "login")
    )
)
```

Exemplo com visibilidade:

```python
wait.until(
    EC.visibility_of_element_located(
        (By.ID, "login")
    )
)
```

Exemplo com clique:

```python
wait.until(
    EC.element_to_be_clickable(
        (By.ID, "login")
    )
)
```

Exemplo com uma função própria:

```python
wait.until(
    lambda d: d.title == "Google"
)
```

---

# `time.sleep()` x `WebDriverWait`

No seu programa você também utiliza:

```python
time.sleep(2)
```

Isso é diferente de:

```python
wait.until(...)
```

### `time.sleep(2)`

Significa:

```text
pare por 2 segundos
```

Não importa se a página terminou antes.

```text
0s ─────────────── 2s
       espera
```

---

### `WebDriverWait`

Significa:

```text
espere até uma condição
por no máximo X segundos
```

Se a condição acontecer rapidamente:

```text
0s ── condição satisfeita
      ↓
   continua
```

---

# Por que `WebDriverWait` geralmente é melhor?

Imagine:

```python
time.sleep(10)
```

mas o elemento aparece em:

```text
1 segundo
```

Você desperdiçou aproximadamente:

```text
9 segundos
```

Com:

```python
wait.until(...)
```

a execução continua assim que a condição é satisfeita.

Por isso, para sincronizar a automação com uma página dinâmica, `WebDriverWait` costuma ser muito mais apropriado.

---

# Estrutura recomendada

Um padrão bastante comum:

```python
wait = WebDriverWait(
    driver,
    10
)

elemento = wait.until(
    EC.presence_of_element_located(
        (By.ID, "elemento")
    )
)
```

Depois:

```python
elemento.click()
```

ou:

```python
elemento.send_keys(
    "texto"
)
```

---

# Resumo mental

Quando encontrar:

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

leia:

```text
wait
 ↓
espere
 ↓
until
 ↓
até que
 ↓
presence_of_element_located
 ↓
o elemento esteja presente
 ↓
(By.NAME, "q")
 ↓
usando NAME
com valor "q"
```

E:

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

leia:

```text
espere até existirem
os elementos correspondentes
ao CSS Selector
"div.yuRUbf a"
```

---

# Estrutura das esperas

```text
Esperas
│
├── WebDriverWait
│      │
│      └── until()
│
└── EC
       │
       ├── presence_of_element_located()
       │
       ├── presence_of_all_elements_located()
       │
       ├── visibility_of_element_located()
       │
       └── element_to_be_clickable()
```

---

# Resumo

`WebDriverWait`:

```python
wait = WebDriverWait(
    driver,
    10
)
```

cria uma espera explícita com tempo máximo de 10 segundos.

`until()`:

```python
wait.until(
    condição
)
```

espera até que uma condição seja satisfeita.

`EC`:

```python
EC.presence_of_element_located(...)
```

fornece condições prontas para utilizar com `WebDriverWait`.

`presence_of_element_located()`:

```python
EC.presence_of_element_located(
    (By.NAME, "q")
)
```

espera um elemento estar presente no DOM.

`visibility_of_element_located()`:

```python
EC.visibility_of_element_located(
    (By.ID, "login")
)
```

espera o elemento estar presente e visível.

`element_to_be_clickable()`:

```python
EC.element_to_be_clickable(
    (By.ID, "entrar")
)
```

espera o elemento estar em condição de ser clicado.

`presence_of_all_elements_located()`:

```python
EC.presence_of_all_elements_located(
    (By.CSS_SELECTOR, "div.yuRUbf a")
)
```

espera os elementos correspondentes ao locator e retorna uma coleção/lista de elementos.

A ideia principal é:

```text
WebDriverWait
      ↓
until()
      ↓
condição
      ↓
elemento/resultado disponível
      ↓
continua a execução
```


---


# `undetected_chromedriver`

## O que é?

`undetected_chromedriver` é uma biblioteca baseada no Selenium que fornece uma forma alternativa de iniciar e controlar o Chrome.

Importação:

```python
import undetected_chromedriver as uc
```

O:

```python
as uc
```

é apenas um **apelido**.

Em vez de escrever:

```python
undetected_chromedriver.Chrome()
```

podemos escrever:

```python
uc.Chrome()
```

---

# Selenium x `undetected_chromedriver`

O Selenium fornece a API para controlar o navegador.

Por exemplo:

```python
driver.get(...)
driver.find_element(...)
driver.execute_script(...)
```

Já o `undetected_chromedriver` fornece uma implementação para iniciar o Chrome de uma maneira que tenta reduzir alguns sinais de automação.

Podemos visualizar:

```text
Selenium
   │
   ├── API de automação
   ├── find_element()
   ├── WebDriverWait
   ├── WebElement
   └── execute_script()
          │
          ▼
undetected_chromedriver
          │
          ▼
      ChromeDriver
          │
          ▼
        Chrome
```

O `undetected_chromedriver` **não substitui todo o Selenium**.

Você continua utilizando várias classes do Selenium normalmente:

```python
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
```

---

# Importante sobre "undetected"

O nome `undetected` pode dar a impressão de que o navegador se torna impossível de detectar.

Não é isso.

A biblioteca tenta reduzir determinados sinais que podem identificar automação, mas:

```text
undetected ≠ impossível de detectar
```

Sites podem utilizar diversas técnicas diferentes para identificar automação, e nenhuma biblioteca garante anonimato ou invisibilidade.

Para estudar Selenium, o mais importante é entender a automação em si.

---

# `uc.Chrome()`

Para iniciar o navegador:

```python
driver = uc.Chrome()
```

Isso cria o WebDriver baseado no Chrome.

Depois:

```python
driver
```

é utilizado da mesma maneira que um driver Selenium convencional.

Exemplo:

```python
driver.get(
    "https://example.com"
)

print(driver.title)

driver.quit()
```

---

# `version_main`

No seu código:

```python
driver = uc.Chrome(
    version_main=151
)
```

O argumento:

```python
version_main
```

permite informar a versão principal do Chrome que deve ser considerada pelo `undetected_chromedriver`.

Exemplo:

```python
version_main=151
```

significa a versão principal:

```text
151
```

---

## Parâmetro

|Parâmetro|Tipo|Obrigatório|Padrão|Descrição|
|---|---|---|---|---|
|`version_main`|`int`|Não|automático|Versão principal do Chrome a ser utilizada|

Portanto:

```python
uc.Chrome()
```

pode ser suficiente em muitos ambientes.

Você pode especificar:

```python
uc.Chrome(
    version_main=151
)
```

quando precisa trabalhar com uma versão específica.

---

# Por que a versão do Chrome importa?

O navegador Chrome e o ChromeDriver precisam ser compatíveis.

Conceitualmente:

```text
Chrome
  ↕
ChromeDriver
  ↕
Selenium
```

Se houver incompatibilidade de versões, podem aparecer problemas ao iniciar o navegador ou durante a automação.

Por isso, quando você utiliza:

```python
version_main=151
```

está informando a versão principal esperada.

---

# `uc.Chrome()` possui vários parâmetros

O construtor do `undetected_chromedriver` possui diversos parâmetros além de:

```python
version_main
```

Você não precisa memorizar todos inicialmente.

Os mais importantes para começar são:

```python
uc.Chrome()
```

e:

```python
uc.Chrome(
    version_main=151
)
```

Outras configurações podem ser adicionadas conforme surgir necessidade específica, como opções do navegador, argumentos do Chrome, perfil, porta etc.

A ideia inicial é:

```text
uc.Chrome()
    ↓
inicia Chrome

uc.Chrome(version_main=151)
    ↓
inicia Chrome considerando versão principal 151
```

---

# Imports do seu projeto

Seu código começa com:

```python
import argparse
import time

import undetected_chromedriver as uc

from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
```

Cada import possui uma função diferente.

Podemos separar:

```text
Python
│
├── argparse
└── time

Biblioteca externa
│
├── undetected_chromedriver
└── selenium
```

---

# `argparse`

## O que é?

`argparse` é uma biblioteca padrão do Python utilizada para criar argumentos de linha de comando.

Seu programa pode ser executado assim:

```bash
python3 main.py -q 10 -d "python selenium"
```

Em vez de deixar os valores fixos no código, o usuário fornece os valores pelo terminal.

---

# `ArgumentParser`

Você utiliza:

```python
parser = argparse.ArgumentParser(
    description="Navega pelos resultados de uma pesquisa."
)
```

`ArgumentParser` cria o objeto responsável por interpretar os argumentos.

O parâmetro:

```python
description
```

é opcional.

Ele serve para explicar o programa quando o usuário executa:

```bash
python3 main.py --help
```

---

# `add_argument()`

Você utiliza:

```python
parser.add_argument(
    "-q",
    "--quantidade",
    type=int,
    default=5,
    help="Quantidade de sites para visitar. Padrão: 5"
)
```

Isso cria um argumento de linha de comando.

---

## Parâmetros

|Parâmetro|Tipo|Obrigatório|Função|
|---|---|---|---|
|`"-q"`|`str`|Não|Forma curta|
|`"--quantidade"`|`str`|Não|Forma longa|
|`type=int`|função|Não|Converte o valor para `int`|
|`default=5`|qualquer|Não|Valor usado se argumento não for fornecido|
|`help`|`str`|Não|Descrição exibida no `--help`|

Então:

```bash
python3 main.py -q 10
```

produz:

```python
args.quantidade
```

com:

```text
10
```

como `int`.

---

# Argumento obrigatório

Você utiliza:

```python
parser.add_argument(
    "-d",
    "--docs",
    required=True,
    help="Termo que será pesquisado no Google."
)
```

O:

```python
required=True
```

significa que o usuário **precisa fornecer esse argumento**.

Portanto:

```bash
python3 main.py
```

não é suficiente.

Precisamos fornecer:

```bash
python3 main.py -d "selenium python"
```

---

# `args`

Depois:

```python
args = parser.parse_args()
```

o `argparse` interpreta os argumentos enviados pelo terminal.

Por exemplo:

```bash
python3 main.py -q 10 -d "selenium"
```

resultará conceitualmente em:

```python
args.quantidade
# 10

args.docs
# "selenium"
```

---

# `parse_args()`

```python
args = parser.parse_args()
```

é o momento em que o Python lê os argumentos fornecidos pelo usuário.

Fluxo:

```text
Terminal
   ↓
python3 main.py -q 10 -d "selenium"
   ↓
argparse
   ↓
parse_args()
   ↓
args
   ├── quantidade = 10
   └── docs = "selenium"
```

---

# Validação da quantidade

Seu código possui:

```python
if args.quantidade <= 0:
    print("A quantidade deve ser maior que 0.")
    return
```

Isso impede valores como:

```bash
-q 0
```

ou:

```bash
-q -5
```

A lógica é:

```text
quantidade <= 0?
      │
   ┌──┴──┐
  SIM   NÃO
   │      │
   ▼      ▼
erro    continua
```

---

# A função `search()`

Seu programa possui:

```python
def search(quantidade, docs):
```

Ela recebe dois valores:

```text
quantidade
docs
```

A responsabilidade dela é executar a automação.

Podemos visualizar:

```text
main()
 │
 ├── lê argumentos
 │
 └── search(
       quantidade,
       docs
    )
          │
          ▼
      Selenium
          │
          ▼
      pesquisa
          │
          ▼
      resultados
          │
          ▼
      visita sites
```

---

# Fluxo completo do seu programa

Seu programa pode ser entendido como várias etapas.

```text
1. Importações
      ↓
2. Criar argumentos CLI
      ↓
3. Ler argumentos
      ↓
4. Validar quantidade
      ↓
5. Chamar search()
      ↓
6. Criar navegador
      ↓
7. Criar WebDriverWait
      ↓
8. Abrir Google
      ↓
9. Encontrar campo de pesquisa
      ↓
10. Digitar pesquisa
      ↓
11. Enviar pesquisa
      ↓
12. Encontrar resultados
      ↓
13. Selecionar resultado
      ↓
14. Obter href
      ↓
15. Clicar
      ↓
16. Esperar página
      ↓
17. Obter título
      ↓
18. Obter URL
      ↓
19. Salvar dados
      ↓
20. Voltar
      ↓
21. Repetir
      ↓
22. Exibir resultados
      ↓
23. Fechar navegador
```

---

# Analisando o início do `search()`

Você faz:

```python
driver = uc.Chrome(
    version_main=151
)
```

Aqui:

```text
uc.Chrome()
      ↓
cria navegador
      ↓
driver
```

Depois:

```python
wait = WebDriverWait(
    driver,
    10
)
```

Agora temos:

```text
driver
  ↓
navegador

wait
  ↓
sistema de espera
```

---

# Abrindo o Google

```python
driver.get(
    "https://www.google.com"
)
```

O navegador navega para o Google.

Depois:

```python
pesquisa = wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

O programa espera o campo de pesquisa existir.

Depois:

```python
pesquisa.send_keys(
    docs
)
```

digita o termo.

E:

```python
pesquisa.submit()
```

envia o formulário.

---

# `submit()`

`submit()` é um método do `WebElement`.

Exemplo:

```python
pesquisa.submit()
```

Ele tenta enviar o formulário associado ao elemento.

No contexto do seu código:

```text
campo de pesquisa
      ↓
send_keys()
      ↓
digita termo
      ↓
submit()
      ↓
envia pesquisa
```

---

# Encontrando resultados

Depois da pesquisa:

```python
wait.until(
    EC.presence_of_all_elements_located(
        (By.CSS_SELECTOR, "div.yuRUbf a")
    )
)
```

O Selenium espera os elementos correspondentes ao seletor aparecerem.

Depois:

```python
resultados = driver.find_elements(
    By.CSS_SELECTOR,
    "div.yuRUbf a"
)
```

obtém os elementos.

A variável:

```python
resultados
```

é uma coleção de `WebElement`.

---

# Selecionando um resultado

Você possui:

```python
link = resultados[numero]
```

Se:

```text
numero = 0
```

então:

```python
link = resultados[0]
```

é o primeiro elemento.

Se:

```text
numero = 1
```

então:

```python
link = resultados[1]
```

é o segundo.

Isso funciona porque listas Python utilizam índice iniciado em `0`.

```text
índice
  0 → primeiro
  1 → segundo
  2 → terceiro
  3 → quarto
```

---

# Obtendo o `href`

Você faz:

```python
url = link.get_attribute(
    "href"
)
```

`get_attribute()` lê um atributo HTML do elemento.

Se o HTML fosse:

```html
<a href="https://example.com">
    Example
</a>
```

então:

```python
link.get_attribute(
    "href"
)
```

retornaria:

```text
https://example.com
```

---

# Clicando

Depois:

```python
driver.execute_script(
    "arguments[0].click();",
    link
)
```

Como vimos anteriormente:

```text
arguments[0]
      ↓
link

.click()
      ↓
executa clique
```

---

# Esperando a navegação

Depois você utiliza:

```python
wait.until(
    lambda d: d.execute_script(
        "return document.readyState"
    ) == "complete"
)
```

A função verifica:

```text
document.readyState
       ↓
   "complete"?
       ↓
      SIM
       ↓
 continua
```

---

# Coletando informações

Depois:

```python
titulo = driver.title
```

obtém o título.

E:

```python
driver.current_url
```

obtém a URL atual.

Então você salva:

```python
sites_visitados.append({
    "title": titulo,
    "url": driver.current_url
})
```

O resultado fica semelhante a:

```python
[
    {
        "title": "Site 1",
        "url": "https://site1.com"
    },
    {
        "title": "Site 2",
        "url": "https://site2.com"
    }
]
```

---

# `append()`

Aqui:

```python
sites_visitados.append(...)
```

`append()` é um método de listas Python.

Ele adiciona um novo elemento ao final da lista.

Começamos:

```python
sites_visitados = []
```

Depois:

```python
sites_visitados.append(
    {
        "title": "Site 1",
        "url": "https://site1.com"
    }
)
```

Agora:

```python
sites_visitados
```

contém:

```python
[
    {
        "title": "Site 1",
        "url": "https://site1.com"
    }
]
```

---

# Voltando para a pesquisa

Depois de visitar:

```python
driver.back()
```

O navegador volta para a página anterior.

Depois:

```python
wait.until(
    EC.presence_of_element_located(
        (By.NAME, "q")
    )
)
```

espera novamente o campo de pesquisa existir.

Então o loop continua.

---

# O `for`

Seu código:

```python
for numero in range(quantidade):
```

controla quantos resultados serão processados.

Se:

```text
quantidade = 5
```

então:

```python
range(5)
```

produz:

```text
0
1
2
3
4
```

Portanto são cinco iterações.

---

# Por que `numero + 1`?

Você exibe:

```python
print(
    f"[{numero + 1}/{quantidade}]"
)
```

O índice começa em `0`, mas para o usuário queremos mostrar:

```text
[1/5]
[2/5]
[3/5]
[4/5]
[5/5]
```

Por isso:

```python
numero + 1
```

---

# Tratamento de exceções

Você utiliza:

```python
try:

    # automação

except Exception as erro:

    print(erro)
```

Isso permite capturar exceções que aconteçam durante aquela tentativa.

Depois:

```python
try:
    driver.get(...)
except:
    pass
```

tenta voltar para a pesquisa caso alguma operação tenha falhado.

---

# Uma observação importante sobre `except Exception`

Seu código usa:

```python
except Exception as erro:
```

Isso captura praticamente todas as exceções comuns derivadas de `Exception`.

É útil durante desenvolvimento para evitar que um erro em um resultado encerre imediatamente todo o programa.

Porém, em programas maiores, é melhor capturar exceções específicas quando você sabe quais podem acontecer.

Por exemplo:

```python
from selenium.common.exceptions import TimeoutException
```

e:

```python
except TimeoutException:
    ...
```

Isso deixa o tratamento mais preciso.

---

# `KeyboardInterrupt`

Você também pode tratar:

```python
except KeyboardInterrupt:
```

Esse erro acontece normalmente quando o usuário pressiona:

```text
Ctrl + C
```

No seu código:

```python
except KeyboardInterrupt:
    print("\nPrograma encerrado.")
```

permite apresentar uma mensagem antes do encerramento.

---

# `finally`

No final:

```python
finally:
    driver.quit()
```

Isso garante que o navegador seja encerrado ao sair da função `search()`.

A estrutura:

```python
try:
    ...
finally:
    driver.quit()
```

é muito útil para recursos que precisam ser liberados.

---

# `if __name__ == "__main__"`

No final do seu arquivo:

```python
if __name__ == "__main__":
    main()
```

Essa estrutura verifica se o arquivo está sendo executado diretamente.

Quando fazemos:

```bash
python3 main.py
```

temos:

```text
__name__
   ↓
"__main__"
```

Então:

```python
main()
```

é executado.

---

# Por que isso é útil?

Imagine que seu arquivo se chama:

```text
main.py
```

Executando diretamente:

```bash
python3 main.py
```

temos:

```python
__name__ == "__main__"
```

Mas se outro arquivo fizer:

```python
import main
```

o código do módulo é carregado como módulo, e:

```python
__name__
```

não será `"__main__"`.

Assim:

```python
if __name__ == "__main__":
    main()
```

impede que `main()` seja executada automaticamente apenas porque o arquivo foi importado.

---

# Estrutura final do projeto

Seu programa possui uma arquitetura simples e já bastante organizada:

```text
main.py
│
├── imports
│
├── search()
│   │
│   ├── cria Chrome
│   ├── cria WebDriverWait
│   ├── abre Google
│   ├── encontra pesquisa
│   ├── pesquisa
│   ├── encontra resultados
│   ├── visita resultados
│   ├── coleta title/url
│   └── salva informações
│
└── main()
    │
    ├── configura argparse
    ├── lê argumentos
    ├── valida quantidade
    └── chama search()
```

---

# Fluxo conceitual de todas as peças

Agora podemos juntar tudo que foi estudado:

```text
Python
 │
 ├── argparse
 │      ↓
 │   argumentos do terminal
 │
 └── Selenium
        │
        ├── uc.Chrome()
        │      ↓
        │   navegador
        │
        ├── driver
        │      │
        │      ├── get()
        │      ├── back()
        │      ├── find_element()
        │      ├── find_elements()
        │      ├── execute_script()
        │      ├── title
        │      ├── current_url
        │      └── quit()
        │
        ├── By
        │      ↓
        │   como localizar
        │
        ├── WebElement
        │      ↓
        │   elemento encontrado
        │
        └── WebDriverWait
               │
               └── EC
                    ↓
                 espera condições
```

---

# Exemplo mínimo juntando os conceitos

Um exemplo reduzido seria:

```python
import undetected_chromedriver as uc

from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC


driver = uc.Chrome()

wait = WebDriverWait(
    driver,
    10
)

try:

    driver.get(
        "https://www.google.com"
    )

    pesquisa = wait.until(
        EC.presence_of_element_located(
            (By.NAME, "q")
        )
    )

    pesquisa.send_keys(
        "Python Selenium"
    )

    pesquisa.submit()

finally:

    driver.quit()
```

Esse pequeno programa já reúne vários conceitos:

```text
uc.Chrome()
     ↓
cria navegador

driver.get()
     ↓
abre página

WebDriverWait
     ↓
espera

EC.presence_of_element_located()
     ↓
localiza condição

By.NAME
     ↓
estratégia de localização

WebElement
     ↓
elemento encontrado

send_keys()
     ↓
digita

submit()
     ↓
envia

driver.quit()
     ↓
encerra
```

---

# Mapa geral do Selenium estudado

```text
Selenium
│
├── WebDriver
│   │
│   ├── get()
│   ├── back()
│   ├── forward()
│   ├── refresh()
│   ├── quit()
│   ├── execute_script()
│   ├── current_url
│   └── title
│
├── By
│   │
│   ├── ID
│   ├── NAME
│   ├── CLASS_NAME
│   ├── TAG_NAME
│   ├── CSS_SELECTOR
│   └── XPATH
│
├── Busca
│   │
│   ├── find_element()
│   └── find_elements()
│
├── WebElement
│   │
│   ├── click()
│   ├── send_keys()
│   ├── clear()
│   ├── text
│   ├── get_attribute()
│   └── submit()
│
└── Esperas
    │
    ├── WebDriverWait
    │   └── until()
    │
    └── EC
        ├── presence_of_element_located()
        ├── presence_of_all_elements_located()
        ├── visibility_of_element_located()
        └── element_to_be_clickable()
```

---

# Mapa do `undetected_chromedriver`

```text
undetected_chromedriver
│
├── import
│   └── import undetected_chromedriver as uc
│
└── Chrome
    │
    ├── uc.Chrome()
    │
    └── uc.Chrome(
            version_main=151
        )
```

---

# O mais importante para memorizar

Não é necessário decorar todas as classes e métodos imediatamente.

Primeiro entenda estas relações:

```text
driver
 ↓
controla o navegador
```

```text
By
 ↓
define como encontrar
```

```text
find_element()
 ↓
encontra um elemento
```

```text
find_elements()
 ↓
encontra vários
```

```text
WebElement
 ↓
representa um elemento HTML
```

```text
WebDriverWait
 ↓
espera
```

```text
EC
 ↓
define o que esperar
```

```text
execute_script()
 ↓
executa JavaScript
```

E:

```text
uc.Chrome()
 ↓
inicia o Chrome através do undetected_chromedriver
```

---

# Conclusão

O seu código combina corretamente vários componentes importantes da automação web:

```text
argparse
   ↓
entrada do usuário

undetected_chromedriver
   ↓
inicialização do navegador

Selenium
   ↓
controle do navegador

By
   ↓
localização

WebElement
   ↓
interação

WebDriverWait + EC
   ↓
sincronização

execute_script()
   ↓
JavaScript

try / except / finally
   ↓
tratamento de erros e encerramento
```

Esse fluxo é uma boa base para começar a construir automações maiores com Selenium.

A partir daqui, os próximos assuntos naturais para aprofundar são **seletores CSS e XPath**, **iframes**, **abas/janelas**, **cookies**, **JavaScript**, **ações de mouse/teclado**, **download/upload de arquivos** e **tratamento mais avançado de exceções e waits**.

---


## Exceções e `except`

## O que são exceções?

Durante uma automação Selenium, várias coisas podem dar errado:

- o elemento não aparecer;
    
- o elemento desaparecer;
    
- o elemento estar bloqueado;
    
- o navegador fechar;
    
- uma página demorar demais;
    
- um seletor estar errado;
    
- uma janela não existir;
    
- o elemento estar dentro de um `iframe`;
    
- a conexão com o navegador ser perdida.
    

Quando isso acontece, o Selenium normalmente gera uma **exceção**.

Exemplo:

```python
elemento = driver.find_element(
    By.ID,
    "login"
)
```

Se o elemento não existir, o Selenium pode gerar:

```text
NoSuchElementException
```

Sem tratamento, o programa pode parar.

Podemos tratar:

```python
try:

    elemento = driver.find_element(
        By.ID,
        "login"
    )

except NoSuchElementException:

    print("Elemento não encontrado.")
```

---

# Estrutura básica

A estrutura fundamental é:

```python
try:

    # código que pode gerar erro

except:

    # código executado quando ocorre uma exceção
```

Exemplo:

```python
try:

    driver.find_element(
        By.ID,
        "login"
    )

except:

    print("Erro ao encontrar elemento.")
```

---

# Por que usar exceções específicas?

É possível fazer:

```python
except Exception as erro:
    print(erro)
```

Mas isso captura praticamente qualquer exceção comum.

É melhor, quando possível, tratar o problema específico:

```python
except TimeoutException:
    print("A página demorou demais.")

except NoSuchElementException:
    print("Elemento não encontrado.")
```

Assim o programa consegue tomar decisões diferentes dependendo do problema.

---

# Importando as exceções do Selenium

As exceções ficam em:

```python
from selenium.common.exceptions import ...
```

Podemos importar somente as que precisamos:

```python
from selenium.common.exceptions import (
    TimeoutException,
    NoSuchElementException,
    ElementClickInterceptedException,
    ElementNotInteractableException,
    StaleElementReferenceException,
    NoSuchWindowException,
    WebDriverException
)
```

---

# `TimeoutException`

## O que é?

`TimeoutException` ocorre quando uma operação que deveria acontecer dentro de determinado tempo não acontece.

É uma das exceções mais importantes para quem utiliza:

```python
WebDriverWait
```

Exemplo:

```python
wait = WebDriverWait(
    driver,
    10
)

try:

    elemento = wait.until(
        EC.presence_of_element_located(
            (By.ID, "login")
        )
    )

except TimeoutException:

    print("O elemento não apareceu em 10 segundos.")
```

---

## O que aconteceu?

O Selenium ficou esperando:

```text
0s
 ↓
1s
 ↓
2s
 ↓
...
 ↓
10s
 ↓
elemento não apareceu
 ↓
TimeoutException
```

---

## Exemplo real

Imagine uma página que possui:

```html
<input id="email">
```

Mas o elemento demora para aparecer.

Você pode fazer:

```python
try:

    email = wait.until(
        EC.visibility_of_element_located(
            (By.ID, "email")
        )
    )

except TimeoutException:

    print("Campo de email não apareceu.")
```

---

## Quando usar?

Principalmente com:

```python
WebDriverWait
```

e:

```python
until()
```

É uma das exceções que você provavelmente mais utilizará em automações Selenium.

---

# `NoSuchElementException`

## O que é?

Ocorre quando o Selenium tenta encontrar um elemento que não existe naquele momento.

Exemplo:

```python
from selenium.common.exceptions import NoSuchElementException

try:

    login = driver.find_element(
        By.ID,
        "login"
    )

except NoSuchElementException:

    print("Elemento não encontrado.")
```

---

## Exemplo real

Se você fizer:

```python
driver.find_element(
    By.ID,
    "botao_inexistente"
)
```

e esse elemento não estiver no DOM, pode ocorrer:

```text
NoSuchElementException
```

---

# Diferença entre `find_element()` e `WebDriverWait`

Existe uma diferença importante.

### Busca direta

```python
driver.find_element(
    By.ID,
    "login"
)
```

Se não encontrar, pode gerar:

```text
NoSuchElementException
```

### Espera explícita

```python
wait.until(
    EC.presence_of_element_located(
        (By.ID, "login")
    )
)
```

Se o elemento não aparecer dentro do tempo:

```text
TimeoutException
```

Por isso, quando estamos esperando elementos dinâmicos, geralmente utilizamos `WebDriverWait`.

---

# `ElementClickInterceptedException`

## O que é?

Ocorre quando você tenta clicar em um elemento, mas **outro elemento está bloqueando o clique**.

Exemplo:

```python
try:

    botao.click()

except ElementClickInterceptedException:

    print("Outro elemento está bloqueando o clique.")
```

---

## Exemplo real

Imagine:

```text
Página
│
├── Botão "Comprar"
│
└── Modal cobrindo o botão
```

Você tenta:

```python
botao.click()
```

Mas o modal está na frente.

O Selenium pode gerar:

```text
ElementClickInterceptedException
```

---

## Possível solução

Primeiro espere o elemento estar clicável:

```python
try:

    botao = wait.until(
        EC.element_to_be_clickable(
            (By.ID, "comprar")
        )
    )

    botao.click()

except ElementClickInterceptedException:

    print("O clique foi bloqueado.")
```

Dependendo da página, pode ser necessário fechar o modal ou esperar que ele desapareça.

---

# `ElementNotInteractableException`

## O que é?

O elemento existe, mas não pode ser utilizado naquele momento.

Por exemplo:

```text
elemento existe
      ↓
mas está escondido/desabilitado
      ↓
não pode interagir
```

Exemplo:

```python
try:

    campo = driver.find_element(
        By.ID,
        "email"
    )

    campo.send_keys("teste@example.com")

except ElementNotInteractableException:

    print("O elemento não pode receber interação.")
```

---

## Diferença para `NoSuchElementException`

```text
NoSuchElementException
    ↓
elemento não foi encontrado

ElementNotInteractableException
    ↓
elemento existe
mas não pode ser utilizado
```

---

# `StaleElementReferenceException`

Essa é uma exceção muito importante em páginas dinâmicas.

## O que significa "stale"?

`stale` significa que a referência que você tinha para aquele elemento ficou **desatualizada/inválida**.

Imagine:

```python
botao = driver.find_element(
    By.ID,
    "botao"
)
```

Agora `botao` representa determinado elemento.

A página atualiza o DOM:

```text
DOM antigo
   ↓
página atualiza
   ↓
DOM novo
```

O elemento antigo pode ser removido e recriado.

Então:

```python
botao.click()
```

pode gerar:

```text
StaleElementReferenceException
```

---

# Exemplo real

```python
try:

    botao = driver.find_element(
        By.ID,
        "atualizar"
    )

    driver.refresh()

    botao.click()

except StaleElementReferenceException:

    print("A referência do elemento ficou inválida.")
```

O problema é:

```text
botao
 ↓
referência antiga
 ↓
refresh()
 ↓
DOM reconstruído
 ↓
botao não representa mais o elemento atual
```

---

# Solução comum

Encontrar o elemento novamente:

```python
try:

    botao = driver.find_element(
        By.ID,
        "atualizar"
    )

    driver.refresh()

    botao = driver.find_element(
        By.ID,
        "atualizar"
    )

    botao.click()

except StaleElementReferenceException:

    print("Elemento ficou obsoleto.")
```

Em automações mais complexas, também podemos implementar uma tentativa novamente.

---

# `NoSuchWindowException`

## O que é?

Ocorre quando o Selenium tenta trabalhar com uma janela ou aba que não existe mais.

Exemplo conceitual:

```text
Janela A
Janela B
```

Se a janela B for fechada:

```text
Janela A
```

e o código tentar acessar a janela B, pode ocorrer:

```text
NoSuchWindowException
```

---

## Exemplo

```python
from selenium.common.exceptions import NoSuchWindowException

try:

    driver.switch_to.window(
        janela
    )

except NoSuchWindowException:

    print("Essa janela não existe mais.")
```

Isso é particularmente relevante quando trabalhamos com:

```python
driver.window_handles
```

e:

```python
driver.switch_to.window()
```

---

# `NoSuchFrameException`

## O que é?

Ocorre quando você tenta acessar um `iframe` que não existe.

Exemplo:

```python
from selenium.common.exceptions import NoSuchFrameException

try:

    driver.switch_to.frame(
        "meu_iframe"
    )

except NoSuchFrameException:

    print("Iframe não encontrado.")
```

---

## Exemplo real

Se a página possuir:

```html
<iframe id="pagamento">
</iframe>
```

podemos fazer:

```python
driver.switch_to.frame(
    "pagamento"
)
```

Mas se esse iframe não existir, pode ocorrer:

```text
NoSuchFrameException
```

---

# `NoSuchAlertException`

## O que é?

Ocorre quando tentamos interagir com um alerta JavaScript que não existe.

Exemplo:

```python
from selenium.common.exceptions import NoAlertPresentException

try:

    alerta = driver.switch_to.alert

    alerta.accept()

except NoAlertPresentException:

    print("Não existe nenhum alerta.")
```

---

# `UnexpectedAlertPresentException`

É praticamente o problema inverso.

Ocorre quando existe um alerta inesperado na página e ele interfere na operação que o Selenium estava tentando realizar.

Exemplo:

```text
Selenium tenta clicar
       ↓
JavaScript abre alert()
       ↓
alerta bloqueia a página
       ↓
UnexpectedAlertPresentException
```

Tratamento:

```python
from selenium.common.exceptions import UnexpectedAlertPresentException

try:

    botao.click()

except UnexpectedAlertPresentException:

    print("Existe um alerta bloqueando a operação.")
```

---

# `InvalidSelectorException`

## O que é?

Ocorre quando o seletor utilizado é inválido.

Por exemplo, um XPath malformado:

```python
driver.find_element(
    By.XPATH,
    "//div[@id="
)
```

Isso pode gerar:

```text
InvalidSelectorException
```

---

## Exemplo de tratamento

```python
from selenium.common.exceptions import InvalidSelectorException

try:

    elemento = driver.find_element(
        By.XPATH,
        xpath
    )

except InvalidSelectorException:

    print("XPath ou seletor inválido.")
```

Normalmente, porém, esse tipo de erro deve ser corrigido no código em vez de simplesmente ignorado.

---

# `WebDriverException`

## O que é?

É uma exceção mais geral relacionada ao WebDriver.

Exemplo:

```python
from selenium.common.exceptions import WebDriverException

try:

    driver.get(
        "https://example.com"
    )

except WebDriverException as erro:

    print(f"Erro do WebDriver: {erro}")
```

Ela pode representar vários problemas relacionados ao navegador, WebDriver ou comunicação com ele.

---

# `SessionNotCreatedException`

Essa exceção pode aparecer quando o Selenium/ChromeDriver não consegue criar a sessão do navegador.

Um motivo comum pode ser incompatibilidade ou problema na configuração do navegador/driver.

Exemplo:

```python
from selenium.common.exceptions import SessionNotCreatedException

try:

    driver = uc.Chrome()

except SessionNotCreatedException:

    print("Não foi possível criar a sessão do navegador.")
```

No caso do `undetected_chromedriver`, problemas de versão/configuração também podem estar envolvidos.

---

# `ElementNotVisibleException`

Em versões modernas do Selenium, muitas situações relacionadas à visibilidade são tratadas através de outras exceções/condições, então não é uma exceção que você normalmente precisa colocar no seu código novo.

Em vez de depender disso, normalmente é melhor utilizar:

```python
EC.visibility_of_element_located()
```

Exemplo:

```python
try:

    campo = wait.until(
        EC.visibility_of_element_located(
            (By.ID, "email")
        )
    )

except TimeoutException:

    print("Campo não ficou visível.")
```

---

# `ElementNotSelectableException`

Também é uma exceção que raramente precisa ser tratada diretamente em automações modernas.

Quando a preocupação é saber se um elemento pode ser utilizado, geralmente é mais interessante utilizar as condições do `Expected Conditions`.

Por exemplo:

```python
EC.element_to_be_clickable()
```

---

# Exceções mais importantes para memorizar

Você não precisa decorar todas.

Comece por estas:

```text
TimeoutException
NoSuchElementException
ElementClickInterceptedException
ElementNotInteractableException
StaleElementReferenceException
WebDriverException
```

Depois:

```text
NoSuchWindowException
NoSuchFrameException
NoAlertPresentException
UnexpectedAlertPresentException
InvalidSelectorException
SessionNotCreatedException
```

---

# Exemplo real de vários `except`

Uma automação pode fazer:

```python
try:

    botao = wait.until(
        EC.element_to_be_clickable(
            (By.ID, "login")
        )
    )

    botao.click()

except TimeoutException:

    print("O botão não ficou disponível.")

except ElementClickInterceptedException:

    print("Outro elemento bloqueou o clique.")

except ElementNotInteractableException:

    print("O botão não pode ser utilizado.")

except StaleElementReferenceException:

    print("O elemento ficou desatualizado.")

except WebDriverException as erro:

    print(f"Erro do WebDriver: {erro}")
```

A ordem é importante.

As exceções mais específicas devem aparecer antes das mais genéricas.

---

# Por que a ordem importa?

Imagine:

```python
except WebDriverException:
    ...
```

antes de:

```python
except TimeoutException:
    ...
```

Como várias exceções do Selenium possuem uma hierarquia de classes, uma exceção mais geral pode capturar uma exceção específica antes que ela chegue ao `except` específico.

Por isso:

```python
except TimeoutException:
    ...

except WebDriverException:
    ...
```

é melhor do que:

```python
except WebDriverException:
    ...

except TimeoutException:
    ...
```

---

# `except Exception as erro`

Também podemos capturar uma exceção geral:

```python
try:

    ...

except Exception as erro:

    print(
        f"Erro: {erro}"
    )
```

O:

```python
as erro
```

guarda o objeto da exceção na variável:

```python
erro
```

Assim podemos imprimir:

```python
print(erro)
```

ou:

```python
print(
    type(erro).__name__
)
```

---

# Descobrindo qual exceção aconteceu

Durante o desenvolvimento, pode ser útil:

```python
except Exception as erro:

    print(
        f"Tipo: {type(erro).__name__}"
    )

    print(
        f"Mensagem: {erro}"
    )
```

Por exemplo:

```text
Tipo: TimeoutException
Mensagem: Message: timeout...
```

Isso ajuda a entender o que aconteceu.

---

# `finally`

Além de `try` e `except`, existe:

```python
finally
```

Ele é executado independentemente de ocorrer uma exceção ou não.

Exemplo:

```python
try:

    driver.get(
        "https://example.com"
    )

except WebDriverException as erro:

    print(erro)

finally:

    driver.quit()
```

Fluxo:

```text
try
 │
 ├── sucesso ──────┐
 │                 │
 └── erro ─────────┤
                   ↓
                finally
                   ↓
              driver.quit()
```

---

# `else`

Também existe:

```python
else
```

Ele é executado somente quando não ocorreu exceção.

Exemplo:

```python
try:

    elemento = driver.find_element(
        By.ID,
        "login"
    )

except NoSuchElementException:

    print("Elemento não encontrado.")

else:

    print("Elemento encontrado.")
```

Fluxo:

```text
try
 │
 ├── erro → except
 │
 └── sucesso → else
```

---

# Estrutura completa

Podemos combinar:

```python
try:

    ...

except TimeoutException:

    ...

except NoSuchElementException:

    ...

except WebDriverException:

    ...

else:

    ...

finally:

    driver.quit()
```

Cada parte possui uma responsabilidade:

```text
try
 ↓
tente executar

except
 ↓
trate erros

else
 ↓
execute se deu certo

finally
 ↓
execute sempre
```

---

# `continue` dentro do `except`

Isso é muito útil em loops.

Imagine:

```python
for url in urls:

    try:

        driver.get(url)

        titulo = driver.title

        print(titulo)

    except TimeoutException:

        print(
            f"Timeout: {url}"
        )

        continue
```

Se uma página der timeout:

```text
URL 1 → sucesso
URL 2 → sucesso
URL 3 → timeout
          ↓
       except
          ↓
       continue
          ↓
URL 4 → continua normalmente
```

Isso evita que um único erro encerre todo o processamento.

---

# Exemplo semelhante ao seu projeto

No seu projeto você visitava vários resultados:

```python
for numero in range(quantidade):

    try:

        resultados = driver.find_elements(
            By.CSS_SELECTOR,
            "div.yuRUbf a"
        )

        link = resultados[numero]

        driver.execute_script(
            "arguments[0].click();",
            link
        )

        titulo = driver.title

        print(titulo)

        driver.back()

    except TimeoutException:

        print("A página demorou demais.")
        continue

    except StaleElementReferenceException:

        print("O elemento ficou obsoleto.")
        continue

    except WebDriverException as erro:

        print(
            f"Erro do Selenium: {erro}"
        )
        continue
```

A vantagem é que:

```text
resultado 1
   ↓
sucesso

resultado 2
   ↓
erro

resultado 3
   ↓
continua

resultado 4
   ↓
sucesso
```

O programa não precisa morrer porque uma página apresentou um problema.

---

# Não use `except: pass` sem motivo

Evite:

```python
except:
    pass
```

Isso simplesmente ignora o erro.

Exemplo:

```python
try:

    driver.get(url)

except:
    pass
```

Se alguma coisa der errado, você não saberá o que aconteceu.

Durante o aprendizado, é muito melhor:

```python
except Exception as erro:

    print(
        f"Erro: {erro}"
    )
```

Ou, melhor ainda, utilizar a exceção específica:

```python
except TimeoutException:

    print("Timeout.")
```

---

# Estratégia recomendada

Uma boa estrutura para seus projetos Selenium é:

```python
try:

    # operação Selenium

except TimeoutException:

    # problema de tempo

except NoSuchElementException:

    # elemento não encontrado

except ElementClickInterceptedException:

    # clique bloqueado

except StaleElementReferenceException:

    # referência antiga

except WebDriverException as erro:

    # outros problemas do WebDriver

finally:

    # limpeza necessária
```

---

# Tabela de referência rápida

|Exceção|O que significa|
|---|---|
|`TimeoutException`|Espera ultrapassou o tempo limite|
|`NoSuchElementException`|Elemento não encontrado|
|`ElementClickInterceptedException`|Clique bloqueado por outro elemento|
|`ElementNotInteractableException`|Elemento existe, mas não pode ser utilizado|
|`StaleElementReferenceException`|Referência do elemento ficou desatualizada|
|`NoSuchWindowException`|Janela/aba não existe mais|
|`NoSuchFrameException`|`iframe` não encontrado|
|`NoAlertPresentException`|Não existe alerta para acessar|
|`UnexpectedAlertPresentException`|Alerta inesperado está bloqueando a operação|
|`InvalidSelectorException`|Seletor inválido|
|`SessionNotCreatedException`|Sessão do navegador não pôde ser criada|
|`WebDriverException`|Erro geral relacionado ao WebDriver|

---

# Regra mental

Quando uma automação Selenium falhar, pense primeiro:

```text
O que aconteceu?
      │
      ├── demorou demais?
      │       ↓
      │   TimeoutException
      │
      ├── elemento não existe?
      │       ↓
      │   NoSuchElementException
      │
      ├── elemento existe mas não interage?
      │       ↓
      │   ElementNotInteractableException
      │
      ├── clique bloqueado?
      │       ↓
      │   ElementClickInterceptedException
      │
      ├── elemento ficou velho?
      │       ↓
      │   StaleElementReferenceException
      │
      └── problema geral no navegador?
              ↓
          WebDriverException
```

---

# Resumo

As exceções fazem parte do controle de fluxo de uma automação Selenium.

Em vez de pensar:

```text
"deu erro → programa morreu"
```

podemos pensar:

```text
"deu erro → descubro qual erro → tomo uma decisão"
```

Por exemplo:

```python
try:

    elemento = wait.until(
        EC.element_to_be_clickable(
            (By.ID, "login")
        )
    )

    elemento.click()

except TimeoutException:

    print("O botão não apareceu a tempo.")

except ElementClickInterceptedException:

    print("O botão estava bloqueado.")

except WebDriverException as erro:

    print(f"Erro do navegador: {erro}")
```

Essa abordagem deixa a automação mais **resistente, previsível e fácil de depurar**.