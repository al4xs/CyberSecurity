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