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