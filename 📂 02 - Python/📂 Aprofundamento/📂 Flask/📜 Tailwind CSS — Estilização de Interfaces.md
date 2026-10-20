# 1. O que é Tailwind CSS?

O **Tailwind CSS** é um framework CSS baseado no conceito _Utility-First_ (utilitários primeiro). Ele permite estilizar elementos HTML diretamente por meio de classes utilitárias, sem precisar criar uma classe CSS personalizada para cada componente da interface.

Diferentemente da abordagem tradicional, na qual criamos classes no CSS e depois as associamos aos elementos HTML, o Tailwind disponibiliza classes que representam propriedades específicas de estilização.

Com isso, podemos controlar espaçamento, cores, dimensões, posicionamento, tipografia, bordas, sombras e diversos outros aspectos diretamente no HTML.

## 1.1. O que significa Utility-First?

Para entender o Tailwind, primeiro vamos observar uma abordagem tradicional de CSS.

Imagine que queremos criar um botão azul com texto branco, espaçamento interno e bordas arredondadas.

Utilizando CSS tradicional:

```
<button class="botao">
    Entrar
</button>
```

```
.botao {
    background-color: #2563eb;
    color: white;
    padding: 12px 24px;
    border-radius: 8px;
    border: none;
}
```

Nesse modelo, criamos a classe `botao` e definimos suas propriedades em um arquivo CSS.

Com Tailwind CSS, podemos construir o mesmo botão diretamente no HTML:

```
<button class="bg-blue-600 text-white px-6 py-3 rounded-lg border-0">
    Entrar
</button>
```

Observe as classes utilizadas:

|Classe Tailwind|Propriedade CSS|Função|
|---|---|---|
|`bg-blue-600`|`background-color`|Define a cor de fundo azul|
|`text-white`|`color`|Define a cor do texto como branco|
|`px-6`|`padding-left` e `padding-right`|Adiciona espaçamento horizontal interno|
|`py-3`|`padding-top` e `padding-bottom`|Adiciona espaçamento vertical interno|
|`rounded-lg`|`border-radius`|Arredonda as bordas|
|`border-0`|`border-width: 0px`|Remove a largura da borda|

**A ideia principal:** as classes Tailwind são uma forma prática de aplicar propriedades CSS sem precisar escrever manualmente todas as declarações.

## 1.2. Entendendo a diferença entre a classe e o CSS

Quando escrevemos:

```
<div class="bg-blue-300">
    Conteúdo
</div>
```

A classe `bg-blue-300` é interpretada pelo CSS gerado pelo Tailwind. Ela aplica uma cor de fundo azul definida pela paleta do framework.

O equivalente conceitual em CSS é:

```
.elemento {
    background-color: var(--color-blue-300);
}
```

No Tailwind CSS v4, essa variável representa uma cor da paleta padrão. O valor exato pode ser consultado no CSS gerado ou na configuração de tema da versão utilizada.

Se quisermos escrever a propriedade CSS manualmente, poderíamos usar uma cor equivalente:

```
.elemento {
    background-color: #93c5fd;
}
```

Esse hexadecimal é um exemplo de azul claro correspondente à paleta padrão do Tailwind. A classe Tailwind, entretanto, é a forma recomendada de expressar essa cor quando estamos usando o framework.

É importante entender que **a classe Tailwind não é uma propriedade CSS**. Ela é um nome de classe para o qual o framework gera uma regra CSS.

## 2. Tailwind CSS não é uma biblioteca de componentes

É importante não confundir Tailwind CSS com ferramentas como Bootstrap.

O Tailwind fornece classes utilitárias para construir interfaces, mas não disponibiliza automaticamente componentes completos com comportamento próprio, como menus interativos, modais e sistemas de navegação.

Por exemplo:

```
<button class="bg-indigo-600 text-white px-5 py-2 rounded-md">
    Cadastrar
</button>
```

Esse botão é construído pela combinação de classes utilitárias.

Já uma biblioteca de componentes pode fornecer um botão pronto com estilos e comportamentos definidos.

Portanto:

- **Tailwind CSS:** fornece utilitários para construir a estilização.
    
- **Bibliotecas de componentes:** fornecem componentes visuais previamente construídos.
    
- **CSS tradicional:** permite definir manualmente as regras de estilização.
    

Essas abordagens podem ser utilizadas em conjunto.

## 3. Como o Tailwind CSS funciona?

O Tailwind identifica as classes utilizadas nos arquivos do projeto e gera o CSS correspondente.

De maneira simplificada:

1. Escrevemos classes utilitárias nos arquivos HTML, Jinja ou outros arquivos reconhecidos.
    
2. O Tailwind identifica as classes presentes nesses arquivos.
    
3. O compilador gera as regras CSS correspondentes.
    
4. O navegador carrega o arquivo CSS gerado.
    
5. Os elementos HTML recebem a estilização.
    

```
Arquivos do projeto
       |
       v
Tailwind identifica as classes
       |
       v
Geração das regras CSS
       |
       v
Arquivo CSS compilado
       |
       v
Navegador aplica os estilos
```

O navegador não interpreta as classes Tailwind diretamente como propriedades CSS. Ele recebe as regras CSS geradas pelo compilador.

## 4. Classes Tailwind e suas propriedades CSS

Esta seção serve como referência para relacionar a sintaxe do Tailwind às propriedades CSS tradicionais.

Os exemplos abaixo utilizam a paleta e a escala padrão do Tailwind CSS v4. Algumas classes podem gerar valores diferentes se o tema do projeto for personalizado.

### 4.1. Cores de fundo — `background-color`

As classes que começam com `bg-` geralmente controlam o fundo de um elemento.

|Classe Tailwind|Propriedade CSS|Equivalente conceitual|
|---|---|---|
|`bg-red-500`|`background-color`|`background-color: #ef4444;`|
|`bg-blue-300`|`background-color`|`background-color: #93c5fd;`|
|`bg-blue-600`|`background-color`|`background-color: #2563eb;`|
|`bg-green-500`|`background-color`|`background-color: #22c55e;`|
|`bg-black`|`background-color`|`background-color: #000;`|
|`bg-white`|`background-color`|`background-color: #fff;`|
|`bg-transparent`|`background-color`|`background-color: transparent;`|

Exemplo:

```
<div class="bg-blue-300">
    Fundo azul claro
</div>
```

O resultado será equivalente, em termos de cor, a uma regra CSS como:

```
div {
    background-color: #93c5fd;
}
```

A classe `bg-blue-300` representa a cor azul de intensidade 300 na paleta padrão. O número não representa uma medida em pixels.

### 4.2. Cores do texto — `color`

As classes que começam com `text-` podem controlar a cor do texto, dependendo do sufixo utilizado.

|Classe Tailwind|Propriedade CSS|Equivalente conceitual|
|---|---|---|
|`text-white`|`color`|`color: white;`|
|`text-black`|`color`|`color: black;`|
|`text-blue-600`|`color`|`color: #2563eb;`|
|`text-red-500`|`color`|`color: #ef4444;`|
|`text-gray-500`|`color`|`color: #6b7280;`|

Exemplo:

```
<p class="text-blue-600">
    Este texto é azul.
</p>
```

Equivalente conceitual:

```
p {
    color: #2563eb;
}
```

A família `text-` também possui classes para tamanho, alinhamento e outras propriedades tipográficas. Por isso, precisamos observar o sufixo para identificar a função da classe.

### 4.3. Espaçamento interno — `padding`

O `padding` define o espaço interno entre o conteúdo de um elemento e sua borda.

|Classe Tailwind|Propriedade CSS|Significado|
|---|---|---|
|`p-4`|`padding`|Espaçamento interno em todos os lados|
|`px-6`|`padding-left` e `padding-right`|Espaçamento horizontal|
|`py-3`|`padding-top` e `padding-bottom`|Espaçamento vertical|
|`pt-4`|`padding-top`|Espaçamento superior|
|`pb-4`|`padding-bottom`|Espaçamento inferior|
|`pl-4`|`padding-left`|Espaçamento à esquerda|
|`pr-4`|`padding-right`|Espaçamento à direita|

Na escala padrão do Tailwind CSS v4, uma unidade de espaçamento geralmente corresponde a `0.25rem`.

Considerando uma configuração padrão de navegador com fonte-base de 16 pixels:

|Classe|Valor aproximado|Equivalente CSS|
|---|---|---|
|`p-1`|4 px|`padding: 0.25rem;`|
|`p-2`|8 px|`padding: 0.5rem;`|
|`p-3`|12 px|`padding: 0.75rem;`|
|`p-4`|16 px|`padding: 1rem;`|
|`p-6`|24 px|`padding: 1.5rem;`|
|`p-8`|32 px|`padding: 2rem;`|

Exemplo:

```
<button class="px-6 py-3">
    Entrar
</button>
```

Equivalente conceitual:

```
button {
    padding-left: 1.5rem;
    padding-right: 1.5rem;
    padding-top: 0.75rem;
    padding-bottom: 0.75rem;
}
```

### 4.4. Espaçamento externo — `margin`

O `margin` controla o espaço externo de um elemento.

|Classe Tailwind|Propriedade CSS|Significado|
|---|---|---|
|`m-4`|`margin`|Margem em todos os lados|
|`mx-auto`|`margin-left` e `margin-right`|Margens horizontais automáticas|
|`my-4`|`margin-top` e `margin-bottom`|Margens verticais|
|`mt-4`|`margin-top`|Margem superior|
|`mb-4`|`margin-bottom`|Margem inferior|
|`ml-4`|`margin-left`|Margem esquerda|
|`mr-4`|`margin-right`|Margem direita|

Exemplo:

```
<div class="mx-auto mt-4">
    Conteúdo centralizado horizontalmente
</div>
```

Equivalente conceitual:

```
div {
    margin-left: auto;
    margin-right: auto;
    margin-top: 1rem;
}
```

**Diferença importante:**

- `padding`: espaço interno do elemento.
    
- `margin`: espaço externo ao redor do elemento.
    

### 4.5. Bordas e arredondamento — `border` e `border-radius`

As classes `border-*` controlam aspectos das bordas. Já as classes `rounded-*` controlam o arredondamento dos cantos.

|Classe Tailwind|Propriedade CSS|Significado|
|---|---|---|
|`border`|`border-width`|Adiciona uma borda com a largura padrão|
|`border-0`|`border-width`|Define largura zero|
|`border-2`|`border-width`|Define borda com 2 px|
|`border-red-500`|`border-color`|Define a cor da borda|
|`rounded-none`|`border-radius`|Remove o arredondamento|
|`rounded-sm`|`border-radius`|Arredondamento pequeno|
|`rounded-md`|`border-radius`|Arredondamento médio|
|`rounded-lg`|`border-radius`|Arredondamento grande|
|`rounded-full`|`border-radius`|Arredondamento máximo, útil para círculos e elementos em formato de pílula|

Exemplo:

```
<button class="border-2 border-red-500 rounded-lg">
    Botão com borda
</button>
```

Equivalente conceitual:

```
button {
    border-width: 2px;
    border-color: #ef4444;
    border-radius: 0.5rem;
}
```

As classes de cor e arredondamento podem ser combinadas independentemente.

### 4.6. Tipografia — tamanho, peso e alinhamento

As classes que começam com `text-` também podem definir tamanho e alinhamento. As classes `font-*` controlam propriedades da fonte.

|Classe Tailwind|Propriedade CSS|Significado|
|---|---|---|
|`text-sm`|`font-size`|Texto pequeno|
|`text-base`|`font-size`|Tamanho-base do texto|
|`text-lg`|`font-size`|Texto grande|
|`text-xl`|`font-size`|Texto extra grande|
|`text-3xl`|`font-size`|Texto bastante grande|
|`font-normal`|`font-weight`|Peso normal|
|`font-medium`|`font-weight`|Peso médio|
|`font-semibold`|`font-weight`|Peso seminegrito|
|`font-bold`|`font-weight`|Peso negrito|
|`text-left`|`text-align`|Alinhamento à esquerda|
|`text-center`|`text-align`|Alinhamento centralizado|
|`text-right`|`text-align`|Alinhamento à direita|

Exemplo:

```
<h1 class="text-3xl font-bold text-center">
    Painel administrativo
</h1>
```

Equivalente conceitual:

```
h1 {
    font-size: 1.875rem;
    line-height: 2.25rem;
    font-weight: 700;
    text-align: center;
}
```

Observe que algumas classes, como `text-3xl`, podem definir mais de uma propriedade CSS, incluindo o tamanho e a altura da linha.

### 4.7. Dimensões — `width` e `height`

As classes `w-*` e `h-*` controlam largura e altura.

|Classe Tailwind|Propriedade CSS|Significado|
|---|---|---|
|`w-full`|`width`|Ocupa 100% da largura disponível|
|`w-1/2`|`width`|Ocupa 50% da largura disponível|
|`w-screen`|`width`|Utiliza a largura da janela de visualização|
|`h-full`|`height`|Ocupa 100% da altura do elemento de referência|
|`h-screen`|`height`|Utiliza a altura da janela de visualização|
|`min-h-screen`|`min-height`|Define a altura mínima da tela|

Exemplo:

```
<div class="w-full min-h-screen">
    Conteúdo da página
</div>
```

Equivalente conceitual:

```
div {
    width: 100%;
    min-height: 100vh;
}
```

O comportamento exato depende do tipo de classe e do contexto de layout.

### 4.8. Layout com Flexbox

O Tailwind disponibiliza classes utilitárias para utilizar o Flexbox, um modelo de layout do CSS.

|Classe Tailwind|Propriedade CSS|Significado|
|---|---|---|
|`flex`|`display: flex`|Ativa o Flexbox|
|`flex-col`|`flex-direction: column`|Organiza os itens em coluna|
|`flex-row`|`flex-direction: row`|Organiza os itens em linha|
|`items-center`|`align-items: center`|Alinha itens no eixo transversal|
|`justify-center`|`justify-content: center`|Centraliza itens no eixo principal|
|`justify-between`|`justify-content: space-between`|Distribui itens com espaço entre eles|
|`flex-wrap`|`flex-wrap: wrap`|Permite que os itens quebrem para outra linha|
|`gap-4`|`gap`|Define espaçamento entre os itens|

Exemplo:

```
<div class="flex items-center justify-center gap-4">
    <span>Usuário</span>
    <button>Entrar</button>
</div>
```

Equivalente conceitual:

```
div {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 1rem;
}
```

### 4.9. Exibição — `display`

As classes de exibição definem como os elementos participam do layout.

|Classe Tailwind|Propriedade CSS|Significado|
|---|---|---|
|`block`|`display: block`|Exibe como elemento de bloco|
|`inline`|`display: inline`|Exibe como elemento em linha|
|`inline-block`|`display: inline-block`|Combina características de elementos em linha e de bloco|
|`hidden`|`display: none`|Oculta o elemento e o remove do layout|
|`grid`|`display: grid`|Ativa o CSS Grid|

Exemplo:

```
<div class="hidden">
    Este elemento não será exibido.
</div>
```

Equivalente conceitual:

```
div {
    display: none;
}
```

### 4.10. Sombras e transparência

As classes de sombra controlam o efeito visual de profundidade. As classes de opacidade controlam a transparência do elemento.

|Classe Tailwind|Propriedade CSS|Significado|
|---|---|---|
|`shadow-sm`|`box-shadow`|Sombra pequena|
|`shadow-md`|`box-shadow`|Sombra média|
|`shadow-lg`|`box-shadow`|Sombra grande|
|`shadow-none`|`box-shadow`|Remove a sombra|
|`opacity-50`|`opacity`|Define opacidade de 50%|
|`opacity-100`|`opacity`|Define opacidade de 100%|

Exemplo:

```
<div class="shadow-lg opacity-100">
    Cartão com sombra
</div>
```

Equivalente conceitual:

```
div {
    box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1),
                0 4px 6px -4px rgb(0 0 0 / 0.1);
    opacity: 1;
}
```

Os valores exatos das sombras dependem da definição da escala e da versão do tema.

## 5. Como interpretar os nomes das classes

Muitas classes Tailwind seguem padrões que ajudam a identificar rapidamente sua finalidade.

|Prefixo ou padrão|Uso comum|Exemplo|
|---|---|---|
|`bg-`|Fundo|`bg-blue-600`|
|`text-`|Cor, tamanho ou alinhamento do texto|`text-white`, `text-lg`|
|`p-`|Espaçamento interno|`p-4`|
|`m-`|Espaçamento externo|`m-4`|
|`w-`|Largura|`w-full`|
|`h-`|Altura|`h-screen`|
|`border-`|Bordas|`border-2`|
|`rounded-`|Arredondamento|`rounded-lg`|
|`shadow-`|Sombras|`shadow-md`|
|`font-`|Peso ou família da fonte|`font-bold`|
|`flex-`|Comportamento Flexbox|`flex-col`|
|`grid-`|Configurações de Grid|`grid-cols-3`|

Esses padrões facilitam a leitura do HTML, mas não substituem a consulta à documentação oficial quando uma classe for desconhecida.

## 6. Tailwind CSS e Flask

O Tailwind pode ser utilizado normalmente em aplicações Flask.

O Flask é responsável pelas rotas, pela lógica do servidor e pela renderização dos templates Jinja. O Tailwind é responsável pela geração dos estilos CSS.

Uma estrutura simples de projeto:

```
meu_projeto/
├── app.py
├── templates/
│   └── index.html
├── static/
│   ├── src/
│   │   └── input.css
│   └── css/
│       └── output.css
├── package.json
└── package-lock.json
```

|Arquivo ou diretório|Responsabilidade|
|---|---|
|`app.py`|Aplicação Flask|
|`templates/`|Templates Jinja|
|`static/`|Arquivos estáticos servidos pelo Flask|
|`static/src/input.css`|Arquivo de entrada do Tailwind|
|`static/css/output.css`|CSS compilado|
|`package.json`|Dependências e scripts npm|

O Flask não precisa conhecer internamente o funcionamento do Tailwind. Ele disponibiliza o CSS compilado por meio da pasta `static`.

## 7. Preparando o Tailwind CSS no projeto

O Tailwind CSS v4 possui um processo de instalação diferente das versões anteriores. Nesta anotação, utilizaremos o CLI oficial.

### 7.1. Verificando o Node.js e o npm

Verifique se estão instalados:

```
node --version
npm --version
```

Se os comandos não estiverem disponíveis, instale o Node.js e o npm antes de continuar.

### 7.2. Inicializando o npm

Na raiz do projeto Flask:

```
npm init -y
```

Esse comando cria o arquivo `package.json`, utilizado para registrar as dependências e os scripts do projeto.

### 7.3. Instalando o Tailwind CSS

Execute:

```
npm install -D tailwindcss @tailwindcss/cli
```

|Pacote|Função|
|---|---|
|`tailwindcss`|Mecanismo principal do Tailwind|
|`@tailwindcss/cli`|Interface de linha de comando para compilar o CSS|

A opção `-D` registra os pacotes como dependências de desenvolvimento.

### 7.4. Criando o arquivo de entrada

Crie os diretórios e o arquivo:

```
mkdir -p static/src
mkdir -p static/css
touch static/src/input.css
```

Dentro de `static/src/input.css`, escreva:

```
@import "tailwindcss";

@source "../../templates";
```

A diretiva `@import` importa o Tailwind CSS v4.

A diretiva `@source` informa explicitamente que os templates Jinja devem ser examinados para identificar as classes utilizadas. O caminho é relativo ao arquivo `input.css`.

### 7.5. Compilando o CSS

Na raiz do projeto, execute:

```
npx @tailwindcss/cli -i ./static/src/input.css -o ./static/css/output.css --watch
```

|Argumento|Função|
|---|---|
|`npx`|Executa um pacote disponível no ambiente npm|
|`@tailwindcss/cli`|Executa o compilador|
|`-i`|Define o arquivo de entrada|
|`-o`|Define o arquivo de saída|
|`--watch`|Monitora alterações e recompila automaticamente|

Mantenha esse comando em execução durante o desenvolvimento.

## 8. Utilizando o CSS compilado no Jinja

No arquivo `templates/index.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Minha aplicação Flask</title>

    <link
        rel="stylesheet"
        href="{{ url_for('static', filename='css/output.css') }}"
    >
</head>
<body class="bg-gray-100 p-8">

    <main class="mx-auto max-w-3xl rounded-lg bg-white p-6 shadow-md">
        <h1 class="text-3xl font-bold text-blue-600">
            Olá, Tailwind CSS!
        </h1>

        <p class="mt-4 text-gray-600">
            Este é um exemplo de integração entre Flask, Jinja e Tailwind.
        </p>

        <button class="mt-6 rounded-lg bg-blue-600 px-6 py-3 font-semibold text-white">
            Testar Tailwind
        </button>
    </main>

</body>
</html>
```

Observe a referência ao CSS:

```
{{ url_for('static', filename='css/output.css') }}
```

O `url_for()` gera a URL do arquivo estático disponibilizado pelo Flask. Isso evita escrever manualmente o caminho da URL.

### 8.1. Executando a aplicação

Com o compilador Tailwind em execução, abra outro terminal e inicie o Flask:

```
flask --app app run --debug
```

A aplicação poderá ser acessada em:

```
http://127.0.0.1:5000
```

Se tudo estiver configurado corretamente, o título, o parágrafo e o botão serão exibidos com os estilos definidos pelas classes Tailwind.

O Flask e o compilador Tailwind são processos independentes. Durante o desenvolvimento, ambos precisam estar em execução.

## 9. Cuidados com classes Tailwind dinâmicas no Jinja

O Tailwind identifica classes por meio da análise dos arquivos do projeto. Por isso, devemos evitar construir nomes de classes dinamicamente usando fragmentos de texto.

Exemplo problemático:

```
<div class="bg-{{ cor }}-500">
    Conteúdo
</div>
```

Se `cor` tiver o valor `red`, o navegador receberá `bg-red-500`, mas o Tailwind pode não gerar essa classe porque não encontrou o nome completo durante a análise dos arquivos.

Uma alternativa é utilizar classes completas:

```
{% if cor == 'red' %}
    <div class="bg-red-500">
        Conteúdo
    </div>
{% elif cor == 'blue' %}
    <div class="bg-blue-500">
        Conteúdo
    </div>
{% endif %}
```

Dessa maneira, o Tailwind consegue identificar as classes.

Outra alternativa é utilizar um mapeamento no Python, garantindo que os valores sejam escolhidos entre classes previamente definidas:

```
cores = {
    "erro": "bg-red-500",
    "sucesso": "bg-green-500",
    "informacao": "bg-blue-500",
}
```

No template, a classe completa deve ser recebida e utilizada sem construir seus nomes por fragmentos. Também é necessário garantir que todas as classes possíveis estejam presentes nos arquivos analisados pelo Tailwind ou sejam explicitamente incluídas na compilação.

## 10. Consultando a equivalência entre Tailwind e CSS

Quando encontrar uma classe desconhecida, siga este processo:

1. Identifique o prefixo da classe, como `bg-`, `p-`, `rounded-` ou `flex-`.
    
2. Consulte a documentação oficial para descobrir a utilidade correspondente.
    
3. Observe a regra CSS gerada pelo compilador quando precisar conhecer os valores exatos.
    
4. Compare a regra com os conceitos de CSS tradicional.
    
5. Experimente alterar a classe e observar o resultado no navegador.
    

Por exemplo, se encontrar:

```
<div class="bg-blue-300 p-4 rounded-lg shadow-md">
    Conteúdo
</div>
```

Você poderá interpretar a estilização como uma combinação de:

```
div {
    background-color: #93c5fd;
    padding: 1rem;
    border-radius: 0.5rem;
    /* A sombra é definida pelas regras de shadow-md. */
}
```

O código CSS acima é uma representação conceitual das utilidades usadas, não necessariamente a saída literal completa do compilador.

A documentação oficial é a referência principal para confirmar o comportamento de cada classe:

- [Tailwind CSS — Documentação oficial](https://tailwindcss.com/docs)
    
- [Tailwind CSS — Cores de fundo](https://tailwindcss.com/docs/background-color)
    
- [Tailwind CSS — Padding](https://tailwindcss.com/docs/padding)
    
- [Tailwind CSS — Margin](https://tailwindcss.com/docs/margin)
    
- [Tailwind CSS — Border Radius](https://tailwindcss.com/docs/border-radius)
    
- [Tailwind CSS — Font Size](https://tailwindcss.com/docs/font-size)
    
- [Tailwind CSS — Flexbox](https://tailwindcss.com/docs/flex)
    

---
## 21. Estados interativos no Tailwind CSS

Até agora, vimos como utilizar classes utilitárias para definir cores, espaçamentos, dimensões e layouts responsivos. Entretanto, interfaces também precisam reagir às ações do usuário.

Um botão pode mudar de cor quando o cursor passa sobre ele, um campo pode receber destaque quando ganha foco e um elemento pode apresentar uma aparência diferente quando está desabilitado.

No CSS tradicional, esses comportamentos são definidos com pseudoclasses:

```
button:hover {
    background-color: #1d4ed8;
}

input:focus {
    outline: 2px solid #2563eb;
}

button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
}
```

No Tailwind CSS, utilizamos variantes que aplicam classes utilitárias quando determinadas condições são atendidas.

|Variante Tailwind|CSS equivalente|
|---|---|
|`hover:bg-blue-700`|`:hover { background-color: #1d4ed8; }`|
|`focus:ring-2`|Aplica um anel visual quando o elemento recebe foco|
|`active:scale-95`|`:active { transform: scale(0.95); }`|
|`disabled:opacity-50`|`:disabled { opacity: 0.5; }`|
|`first:rounded-t-lg`|`:first-child { border-top-left-radius: 0.5rem; border-top-right-radius: 0.5rem; }`|
|`last:border-b-0`|`:last-child { border-bottom-width: 0; }`|

As equivalências são conceituais: a saída CSS exata depende da versão do Tailwind e das classes utilizadas.

### 21.1. A variante `hover:`

A variante `hover:` aplica um estilo quando o ponteiro está sobre um elemento em dispositivos que suportam esse tipo de interação.

```
<button class="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700">
    Entrar
</button>
```

Equivalência aproximada em CSS:

```
button {
    background-color: #2563eb;
    color: white;
    padding: 0.5rem 1rem;
    border-radius: 0.5rem;
}

button:hover {
    background-color: #1d4ed8;
}
```

O botão possui fundo azul inicialmente e fica mais escuro quando o cursor passa sobre ele.

A variante não substitui o estilo inicial. Ela define uma condição adicional para o mesmo elemento.

### 21.2. A variante `focus:`

A variante `focus:` é utilizada quando um elemento recebe foco, por exemplo, quando o usuário seleciona um campo de formulário com o teclado ou clica nele.

```
<input
    type="email"
    placeholder="Seu e-mail"
    class="border border-gray-300 rounded-lg px-3 py-2 focus:border-blue-500 focus:outline-none"
>
```

Equivalência aproximada:

```
input {
    border: 1px solid #d1d5db;
    border-radius: 0.5rem;
    padding: 0.5rem 0.75rem;
}

input:focus {
    border-color: #3b82f6;
    outline: none;
}
```

O campo muda a cor da borda ao receber foco.

**Atenção:** remover o `outline` sem oferecer outra indicação visual de foco prejudica a navegação por teclado. Se remover o contorno padrão, substitua-o por um indicador perceptível.

### 21.3. A variante `focus-visible:`

A variante `focus-visible:` permite aplicar estilos quando o navegador considera que o foco deve ser indicado visualmente, normalmente durante a navegação por teclado.

```
<button class="rounded-lg bg-blue-600 px-4 py-2 text-white focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600">
    Salvar
</button>
```

Equivalência CSS:

```
button:focus-visible {
    outline: 2px solid #2563eb;
    outline-offset: 2px;
}
```

Essa abordagem ajuda a manter uma indicação de foco clara para quem navega utilizando o teclado.

### 21.4. A variante `active:`

A variante `active:` representa o estado ativo de um elemento, normalmente enquanto o usuário pressiona um botão ou link.

```
<button class="rounded-lg bg-blue-600 px-4 py-2 text-white active:scale-95">
    Confirmar
</button>
```

Equivalência CSS:

```
button:active {
    transform: scale(0.95);
}
```

O botão diminui ligeiramente enquanto está sendo pressionado.

Esse efeito pode tornar a interação mais perceptível, mas deve ser discreto para não prejudicar a usabilidade.

### 21.5. A variante `disabled:`

A variante `disabled:` aplica estilos quando um elemento de formulário está desabilitado.

```
<button
    disabled
    class="rounded-lg bg-blue-600 px-4 py-2 text-white disabled:cursor-not-allowed disabled:opacity-50"
>
    Enviando...
</button>
```

Equivalência CSS:

```
button:disabled {
    cursor: not-allowed;
    opacity: 0.5;
}
```

O atributo HTML `disabled` é o responsável por desabilitar o botão. A classe `disabled:opacity-50` apenas modifica sua aparência.

Isso é importante: **estilizar um elemento como desabilitado não significa que ele realmente esteja desabilitado.**

### 21.6. Combinando variantes

É possível combinar várias variantes para definir comportamentos diferentes em um mesmo elemento.

```
<button class="rounded-lg bg-blue-600 px-4 py-2 text-white hover:bg-blue-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600 active:scale-95 disabled:cursor-not-allowed disabled:opacity-50">
    Enviar formulário
</button>
```

Nesse exemplo, o botão:

- Possui fundo azul inicialmente.
    
- Fica mais escuro quando recebe `hover`.
    
- Apresenta um contorno visível quando recebe foco apropriado.
    
- Diminui ligeiramente quando é pressionado.
    
- Fica visualmente atenuado quando desabilitado.
    

Cada variante representa uma condição específica. O Tailwind combina essas condições com as classes utilitárias que definem os estilos.

---

## 22. Variantes estruturais: `first:`, `last:`, `odd:` e `even:`

Além dos estados de interação, o Tailwind possui variantes que permitem selecionar elementos de acordo com sua posição dentro de uma estrutura.

Elas são úteis para listas, tabelas, menus e conjuntos de cartões.

### 22.1. `first:` e `last:`

Considere uma lista com três elementos:

```
<ul class="divide-y divide-gray-200">
    <li class="first:pt-0 last:pb-0 py-3">
        Primeiro item
    </li>

    <li class="first:pt-0 last:pb-0 py-3">
        Segundo item
    </li>

    <li class="first:pt-0 last:pb-0 py-3">
        Terceiro item
    </li>
</ul>
```

Equivalência CSS:

```
li {
    padding-top: 0.75rem;
    padding-bottom: 0.75rem;
}

li:first-child {
    padding-top: 0;
}

li:last-child {
    padding-bottom: 0;
}
```

O primeiro elemento perde o espaçamento superior, enquanto o último perde o espaçamento inferior.

### 22.2. `odd:` e `even:`

Essas variantes selecionam elementos em posições ímpares e pares.

```
<table class="w-full border-collapse">
    <tbody>
        <tr class="odd:bg-gray-100 even:bg-white">
            <td class="p-3">Python</td>
        </tr>

        <tr class="odd:bg-gray-100 even:bg-white">
            <td class="p-3">Flask</td>
        </tr>

        <tr class="odd:bg-gray-100 even:bg-white">
            <td class="p-3">SQLAlchemy</td>
        </tr>
    </tbody>
</table>
```

Equivalência CSS:

```
tr:nth-child(odd) {
    background-color: #f3f4f6;
}

tr:nth-child(even) {
    background-color: white;
}
```

O resultado é uma tabela com cores alternadas, facilitando a leitura das linhas.

---

## 23. Transições com Tailwind CSS

Uma transição controla como uma propriedade CSS muda de um valor para outro ao longo do tempo.

Sem transição, a mudança de cor de um botão pode acontecer imediatamente. Com uma transição, a mudança pode ocorrer gradualmente.

### 23.1. `transition`

A classe `transition` aplica uma transição a um conjunto de propriedades CSS definido pelo Tailwind.

```
<button class="rounded-lg bg-blue-600 px-4 py-2 text-white transition hover:bg-blue-700">
    Passar o cursor
</button>
```

Equivalência conceitual:

```
button {
    transition-property:
        color,
        background-color,
        border-color,
        text-decoration-color,
        fill,
        stroke,
        opacity,
        box-shadow,
        transform,
        filter,
        backdrop-filter;

    transition-duration: 150ms;
}
```

A lista exata de propriedades e os valores padrão dependem da versão do Tailwind. Consulte a documentação da versão utilizada quando precisar de precisão sobre a saída gerada.

### 23.2. Controlando a duração

As classes `duration-*` definem por quanto tempo a transição ocorre.

|Classe|CSS equivalente|
|---|---|
|`duration-75`|`transition-duration: 75ms;`|
|`duration-150`|`transition-duration: 150ms;`|
|`duration-300`|`transition-duration: 300ms;`|
|`duration-500`|`transition-duration: 500ms;`|
|`duration-700`|`transition-duration: 700ms;`|

Exemplo:

```
<button class="rounded-lg bg-blue-600 px-4 py-2 text-white transition duration-300 hover:bg-blue-700">
    Transição de 300 ms
</button>
```

O botão muda de cor ao longo de 300 milissegundos.

### 23.3. Controlando a curva de animação

A curva de temporização define como a velocidade da transição varia ao longo do tempo.

|Classe|CSS equivalente|
|---|---|
|`ease-linear`|`transition-timing-function: linear;`|
|`ease-in`|`transition-timing-function: ease-in;`|
|`ease-out`|`transition-timing-function: ease-out;`|
|`ease-in-out`|`transition-timing-function: ease-in-out;`|

Exemplo:

```
<button class="rounded-lg bg-blue-600 px-4 py-2 text-white transition duration-300 ease-in-out hover:bg-blue-700">
    Transição suave
</button>
```

A curva `ease-in-out` começa e termina suavemente.

### 23.4. Controlando propriedades específicas

Quando queremos transicionar somente uma propriedade, podemos utilizar classes mais específicas.

```
<div class="bg-blue-600 text-white transition-colors duration-300 hover:bg-blue-700">
    Muda apenas as cores
</div>
```

Equivalência CSS:

```
.elemento {
    transition-property: color, background-color,
        border-color, text-decoration-color, fill, stroke;
    transition-duration: 300ms;
}
```

Outras classes úteis incluem:

|Classe|Propriedade CSS|
|---|---|
|`transition-colors`|Transições de cores|
|`transition-opacity`|`transition-property: opacity;`|
|`transition-shadow`|Transições de sombra|
|`transition-transform`|`transition-property: transform;`|
|`transition-all`|`transition-property: all;`|
|`transition-none`|`transition-property: none;`|

Evite usar `transition-all` sem necessidade. Transicionar todas as propriedades pode produzir efeitos inesperados e dificultar a identificação do que está animando.

### 23.5. Atraso da transição

A classe `delay-*` define quanto tempo o navegador espera antes de iniciar a transição.

```
<div class="transition-opacity delay-150 duration-300 hover:opacity-50">
    Passe o cursor sobre este elemento
</div>
```

Equivalência CSS:

```
.elemento {
    transition-property: opacity;
    transition-delay: 150ms;
    transition-duration: 300ms;
}
```

O atraso pode ser útil para criar efeitos coordenados, mas atrasos excessivos podem fazer a interface parecer lenta.

---

## 24. Transformações com Tailwind CSS

Transformações modificam a apresentação geométrica de um elemento, sem necessariamente alterar o espaço que ele ocupa no fluxo normal do documento.

O Tailwind oferece classes para escala, rotação, translação e outras transformações.

### 24.1. Escala com `scale-*`

```
<div class="scale-105">
    Elemento ampliado em 5%
</div>
```

Equivalência CSS:

```
.elemento {
    scale: 105%;
}
```

A classe `scale-95`, por sua vez, reduz a escala para 95%.

Um exemplo de cartão interativo:

```
<article class="rounded-xl border border-gray-200 p-5 transition-transform duration-200 hover:scale-105">
    <h2 class="text-lg font-semibold">Projeto Python</h2>
    <p class="mt-2 text-gray-600">Uma descrição do projeto.</p>
</article>
```

O cartão cresce ligeiramente quando o cursor passa sobre ele.

### 24.2. Rotação com `rotate-*`

```
<div class="rotate-45">
    Elemento rotacionado
</div>
```

Equivalência CSS:

```
.elemento {
    rotate: 45deg;
}
```

A rotação pode ser utilizada em ícones, indicadores e elementos decorativos.

### 24.3. Translação com `translate-*`

```
<div class="translate-x-4 translate-y-2">
    Elemento deslocado
</div>
```

Equivalência CSS:

```
.elemento {
    translate: calc(var(--spacing) * 4) calc(var(--spacing) * 2);
}
```

No tema padrão do Tailwind v4, a escala de espaçamento normalmente corresponde a `0.25rem` por unidade. Nesse caso, `translate-x-4` representa 1rem horizontalmente e `translate-y-2` representa 0.5rem verticalmente.

A implementação CSS exata pode variar conforme a versão e a configuração do Tailwind.

### 24.4. Combinando transformação e transição

```
<button class="rounded-lg bg-indigo-600 px-4 py-2 text-white transition-transform duration-200 hover:scale-105 active:scale-95">
    Interagir
</button>
```

O botão aumenta ligeiramente ao receber `hover` e diminui quando é pressionado.

Use transformações com moderação. Movimentos exagerados podem distrair o usuário ou prejudicar a experiência em dispositivos móveis.

---

## 25. Animações prontas do Tailwind

Além de transições, o Tailwind disponibiliza algumas animações utilitárias.

Uma transição normalmente representa a mudança entre estados de uma propriedade. Uma animação pode executar uma sequência de etapas definida por `@keyframes`.

### 25.1. `animate-spin`

Utilizada para indicar carregamento ou atividade contínua.

```
<div class="size-6 animate-spin rounded-full border-4 border-gray-300 border-t-blue-600"></div>
```

Equivalência CSS aproximada:

```
@keyframes spin {
    to {
        transform: rotate(360deg);
    }
}

.elemento {
    animation: spin 1s linear infinite;
}
```

### 25.2. `animate-ping`

Cria um efeito de expansão e desaparecimento, frequentemente usado em indicadores de status.

```
<span class="relative flex size-3">
    <span class="absolute inline-flex size-full animate-ping rounded-full bg-green-400 opacity-75"></span>
    <span class="relative inline-flex size-3 rounded-full bg-green-500"></span>
</span>
```

### 25.3. `animate-pulse`

Cria uma pulsação de opacidade, útil em placeholders de carregamento.

```
<div class="animate-pulse space-y-3">
    <div class="h-4 w-3/4 rounded bg-gray-300"></div>
    <div class="h-4 w-full rounded bg-gray-300"></div>
    <div class="h-4 w-5/6 rounded bg-gray-300"></div>
</div>
```

### 25.4. `animate-bounce`

Cria um movimento de salto.

```
<div class="animate-bounce">
    ↓
</div>
```

É possível utilizar essa animação para chamar a atenção para um indicador, mas não é recomendável aplicá-la indiscriminadamente a elementos importantes da interface.

### 25.5. Removendo animações

A classe `animate-none` remove a animação aplicada pelo Tailwind.

```
<div class="animate-none">
    Sem animação
</div>
```

Equivalência CSS:

```
.elemento {
    animation: none;
}
```

---

## 26. Criando animações personalizadas

As animações prontas não atendem a todos os cenários. Quando precisamos de um comportamento específico, podemos definir nossos próprios `@keyframes`.

No Tailwind CSS v4, podemos declarar uma animação personalizada utilizando CSS e variáveis de tema.

```
@import "tailwindcss";

@theme {
    --animate-fade-in: fade-in 300ms ease-out both;

    @keyframes fade-in {
        from {
            opacity: 0;
            transform: translateY(8px);
        }

        to {
            opacity: 1;
            transform: translateY(0);
        }
    }
}
```

Depois, podemos utilizar a classe correspondente no HTML:

```
<div class="animate-fade-in rounded-xl border p-5">
    <h2 class="text-lg font-semibold">Painel administrativo</h2>
    <p class="mt-2 text-gray-600">Conteúdo exibido com uma animação de entrada.</p>
</div>
```

A variável `--animate-fade-in` registra a definição da animação no tema. O Tailwind pode gerar a classe utilitária `animate-fade-in` a partir dessa configuração.

O comportamento é equivalente, conceitualmente, a:

```
.fade-in {
    animation: fade-in 300ms ease-out both;
}

@keyframes fade-in {
    from {
        opacity: 0;
        transform: translateY(8px);
    }

    to {
        opacity: 1;
        transform: translateY(0);
    }
}
```

A diferença é que, na primeira abordagem, o Tailwind integra a definição ao sistema de utilitários.

---

## 27. Acessibilidade e preferência por movimento reduzido

Uma interface não deve depender exclusivamente de cores, movimentos ou interações com o mouse para transmitir informações.

Além disso, alguns usuários preferem reduzir animações por motivos de conforto ou acessibilidade.

O navegador disponibiliza a media query `prefers-reduced-motion` para indicar essa preferência.

### 27.1. A variante `motion-reduce:`

No Tailwind, podemos utilizar `motion-reduce:` para aplicar estilos quando o usuário prefere movimento reduzido.

```
<div class="animate-pulse motion-reduce:animate-none">
    Indicador de carregamento
</div>
```

Equivalência CSS:

```
.elemento {
    animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}

@media (prefers-reduced-motion: reduce) {
    .elemento {
        animation: none;
    }
}
```

A definição exata da animação padrão pode variar conforme a versão do Tailwind.

### 27.2. Reduzindo transições

Também podemos aplicar uma duração menor ou remover transições quando o usuário prefere movimento reduzido.

```
<button class="transition-transform duration-200 hover:scale-105 motion-reduce:transition-none motion-reduce:hover:scale-100">
    Ação
</button>
```

Nesse caso, a interface deixa de aplicar a transição e evita o aumento do elemento para quem opta por movimento reduzido.

### 27.3. Boas práticas de acessibilidade

Ao criar interfaces com Tailwind:

- Mantenha um indicador de foco visível para navegação por teclado.
    
- Não dependa apenas de cores para comunicar sucesso, erro ou estado.
    
- Garanta contraste suficiente entre texto e fundo.
    
- Associe rótulos aos campos de formulário.
    
- Use elementos HTML semânticos, como `button`, `nav`, `main` e `form`.
    
- Evite animações contínuas sem finalidade clara.
    
- Respeite a preferência por movimento reduzido.
    
- Não use classes visuais para substituir atributos HTML ou validações reais.
    

O Tailwind facilita a aplicação dos estilos, mas **não torna uma interface acessível automaticamente**. A acessibilidade depende também da estrutura HTML, do comportamento e das decisões de design.

---

## 28. Aplicando os conceitos em um botão de formulário Flask

Considere um formulário de login renderizado com Jinja em uma aplicação Flask.

```
<form method="post" action="{{ url_for('login') }}" class="mx-auto max-w-md space-y-4 rounded-xl border border-gray-200 bg-white p-6 shadow-sm">
    <div>
        <label for="email" class="mb-1 block text-sm font-medium text-gray-700">
            E-mail
        </label>

        <input
            id="email"
            name="email"
            type="email"
            autocomplete="email"
            required
            class="w-full rounded-lg border border-gray-300 px-3 py-2 transition-colors duration-200 focus:border-blue-500 focus:outline-2 focus:outline-offset-2 focus:outline-blue-500"
        >
    </div>

    <div>
        <label for="password" class="mb-1 block text-sm font-medium text-gray-700">
            Senha
        </label>

        <input
            id="password"
            name="password"
            type="password"
            autocomplete="current-password"
            required
            class="w-full rounded-lg border border-gray-300 px-3 py-2 transition-colors duration-200 focus:border-blue-500 focus:outline-2 focus:outline-offset-2 focus:outline-blue-500"
        >
    </div>

    <button
        type="submit"
        class="w-full rounded-lg bg-blue-600 px-4 py-2 font-medium text-white transition-colors duration-200 hover:bg-blue-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600 active:bg-blue-800"
    >
        Entrar
    </button>
</form>
```

Nesse exemplo, diferentes grupos de utilitários possuem responsabilidades específicas:

|Classes|Responsabilidade|
|---|---|
|`mx-auto max-w-md`|Centralização horizontal e largura máxima|
|`space-y-4`|Espaçamento vertical entre os elementos filhos|
|`border rounded-xl shadow-sm`|Borda, cantos arredondados e sombra|
|`focus:border-blue-500`|Alteração da borda ao receber foco|
|`focus:outline-2`|Indicador visual de foco|
|`transition-colors duration-200`|Transição gradual de cores|
|`hover:bg-blue-700`|Mudança de fundo ao passar o cursor|
|`active:bg-blue-800`|Mudança de fundo enquanto o botão está ativo|

O atributo `action="{{ url_for('login') }}"` utiliza o Jinja para gerar a URL da rota Flask chamada `login`. Já as classes Tailwind controlam somente a apresentação visual.

O formulário ainda precisa de uma implementação Flask que processe os dados, valide as credenciais e trate os erros. As classes CSS não implementam autenticação nem segurança no servidor.

---

## 29. Como depurar estados e animações no navegador

Quando uma classe parece não funcionar, não presuma imediatamente que o Tailwind está com defeito.

Faça uma verificação organizada:

1. Abra as ferramentas de desenvolvedor do navegador.
    
2. Inspecione o elemento HTML.
    
3. Confirme se a classe está presente no atributo `class`.
    
4. Verifique se o CSS compilado contém a regra correspondente.
    
5. Confira se outra regra CSS está sobrescrevendo o estilo.
    
6. Para estados como `hover` e `focus`, force o estado na ferramenta de inspeção, quando disponível.
    
7. Verifique se a configuração do Tailwind está detectando os arquivos HTML e Jinja.
    

No Tailwind v4, o mecanismo de detecção de fontes identifica os arquivos que devem ser analisados para gerar as classes utilizadas. Se um template não for detectado, classes presentes nele podem não aparecer no CSS compilado.

Para um projeto Flask, confirme que o caminho de `templates/` está incluído na detecção de fontes quando a configuração utilizada exigir isso.

Exemplo de configuração CSS:

```
@import "tailwindcss";
@source "../../templates";
```

O caminho de `@source` é relativo ao arquivo CSS em que a diretiva aparece. Portanto, ajuste-o conforme a estrutura real do projeto.

Também é importante distinguir duas situações:

- **A classe está no HTML, mas a regra não existe no CSS compilado:** investigue a detecção de fontes e a compilação.
    
- **A regra existe, mas o visual não muda:** investigue a condição do estado, a especificidade CSS e as regras que podem estar sobrescrevendo o estilo.
    

---
## 30. Dark mode no Tailwind CSS

O _dark mode_ permite oferecer uma interface com cores escuras, normalmente para reduzir o brilho visual e permitir que o usuário escolha entre um tema claro e um escuro.

No CSS tradicional, podemos utilizar uma classe no elemento raiz ou uma media query para definir o tema. No Tailwind, utilizamos a variante `dark:` para aplicar classes quando o modo escuro está ativo.

Exemplo:

```
<div class="bg-white text-gray-900 dark:bg-gray-900 dark:text-white">
    <h1 class="text-2xl font-bold">Painel administrativo</h1>
    <p class="mt-2 text-gray-600 dark:text-gray-300">
        Conteúdo da aplicação.
    </p>
</div>
```

Equivalência conceitual em CSS:

```
.painel {
    background-color: white;
    color: #111827;
}

.painel p {
    color: #4b5563;
}

.dark .painel {
    background-color: #111827;
    color: white;
}

.dark .painel p {
    color: #d1d5db;
}
```

As classes `dark:bg-gray-900` e `dark:text-white` não representam cores aplicadas permanentemente. Elas definem estilos condicionais que dependem de como o modo escuro está configurado.

**Importante:** a variante `dark:` não significa, por si só, que haverá um botão para alternar o tema. É necessário definir como a aplicação ativa o modo escuro.

---

## 31. Dark mode automático com a preferência do sistema

Por padrão, o Tailwind CSS utiliza a preferência de esquema de cores do sistema operacional ou navegador para determinar o comportamento da variante `dark:`.

Essa preferência é acessada por meio da media query CSS `prefers-color-scheme`.

Exemplo em HTML:

```
<div class="bg-white text-gray-900 dark:bg-gray-900 dark:text-white">
    Tema adaptado à preferência do sistema.
</div>
```

Equivalência CSS:

```
.painel {
    background-color: white;
    color: #111827;
}

@media (prefers-color-scheme: dark) {
    .painel {
        background-color: #111827;
        color: white;
    }
}
```

Se o sistema estiver configurado para o modo escuro, as regras condicionais serão aplicadas.

Esse comportamento é útil quando queremos adaptar a interface automaticamente, sem implementar um controle manual de tema.

Entretanto, em um painel administrativo, pode ser interessante permitir que o próprio usuário escolha entre tema claro, escuro ou automático.

---

## 32. Ativando o dark mode manualmente no Tailwind CSS v4

No Tailwind CSS v4, podemos redefinir a variante `dark` para que ela seja ativada pela presença de uma classe `.dark` em um elemento ancestral.

No arquivo CSS de entrada:

```
@import "tailwindcss";

@custom-variant dark (&:where(.dark, .dark *));
```

Com essa configuração, podemos ativar o modo escuro adicionando a classe `dark` ao elemento raiz da página.

```
<html lang="pt-BR" class="dark">
    <body>
        <main class="min-h-screen bg-white text-gray-900 dark:bg-gray-900 dark:text-white">
            <h1 class="text-2xl font-bold">Painel administrativo</h1>
        </main>
    </body>
</html>
```

Nesse exemplo, a classe `dark` no elemento `html` ativa as variantes `dark:` utilizadas dentro dele.

A expressão:

```
@custom-variant dark (&:where(.dark, .dark *));
```

define um seletor que corresponde ao próprio elemento `.dark` e aos elementos que estejam dentro de um ancestral com essa classe.

O uso de `:where()` ajuda a manter a especificidade do seletor baixa.

**Observação:** essa configuração é específica para o Tailwind CSS v4. Em projetos com versões anteriores, a configuração pode ser diferente.

---

## 33. Alternando o tema com JavaScript

Depois de configurar o Tailwind para utilizar a classe `.dark`, podemos alternar o tema com JavaScript.

O exemplo abaixo é independente de Flask, mas pode ser utilizado em um template Jinja.

```
<button
    id="theme-toggle"
    type="button"
    class="rounded-lg border px-4 py-2"
>
    Alternar tema
</button>

<script>
    const button = document.querySelector("#theme-toggle");

    button.addEventListener("click", () => {
        document.documentElement.classList.toggle("dark");
    });
</script>
```

A linha:

```
document.documentElement.classList.toggle("dark");
```

adiciona a classe `dark` ao elemento `html` quando ela não existe e remove a classe quando ela já está presente.

O Tailwind não executa essa lógica. Ele apenas fornece as classes CSS necessárias para a apresentação visual.

### 33.1. Persistindo a preferência com `localStorage`

Se o usuário alternar o tema e recarregar a página, a classe será perdida caso a preferência não seja armazenada.

Podemos utilizar `localStorage` para guardar a escolha no navegador.

```
<script>
    const root = document.documentElement;
    const button = document.querySelector("#theme-toggle");

    const savedTheme = localStorage.getItem("theme");

    if (savedTheme === "dark") {
        root.classList.add("dark");
    } else if (savedTheme === "light") {
        root.classList.remove("dark");
    }

    button.addEventListener("click", () => {
        const isDark = root.classList.toggle("dark");

        localStorage.setItem("theme", isDark ? "dark" : "light");
    });
</script>
```

Nesse exemplo:

- `localStorage.getItem("theme")` recupera a preferência salva.
    
- `classList.toggle("dark")` alterna o tema.
    
- `localStorage.setItem(...)` salva a escolha.
    
- Ao recarregar a página, a preferência é reaplicada.
    

Esse código pressupõe que o botão `theme-toggle` exista na página antes da execução do script.

Em aplicações reais, também é importante evitar uma mudança visual brusca entre o carregamento inicial e a aplicação do tema. Para isso, a preferência pode ser aplicada antes de o conteúdo da página ser renderizado.

**Segurança:** não armazene senhas, tokens de autenticação ou outros segredos em `localStorage`. Nesse exemplo, ele é utilizado somente para uma preferência visual.

---

## 34. Organizando o dark mode em templates Jinja

Em uma aplicação Flask, é comum ter um template base responsável por estruturar as páginas e incluir os arquivos CSS e JavaScript.

Por exemplo, em `templates/base.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>{% block title %}Meu painel{% endblock %}</title>

    <link
        rel="stylesheet"
        href="{{ url_for('static', filename='css/output.css') }}"
    >
</head>
<body class="min-h-screen bg-white text-gray-900 dark:bg-gray-900 dark:text-white">

    <header class="border-b border-gray-200 p-4 dark:border-gray-700">
        <nav class="mx-auto flex max-w-5xl items-center justify-between">
            <a href="{{ url_for('home') }}" class="font-bold">
                Meu painel
            </a>

            <button
                id="theme-toggle"
                type="button"
                class="rounded-lg border border-gray-300 px-3 py-2 dark:border-gray-600"
            >
                Alternar tema
            </button>
        </nav>
    </header>

    <main class="mx-auto max-w-5xl p-6">
        {% block content %}{% endblock %}
    </main>

    <script src="{{ url_for('static', filename='js/theme.js') }}" defer></script>
</body>
</html>
```

Uma página que estende esse template pode concentrar apenas seu conteúdo específico:

```
{% extends "base.html" %}

{% block title %}Início{% endblock %}

{% block content %}
    <section class="rounded-xl border border-gray-200 p-6 dark:border-gray-700">
        <h1 class="text-2xl font-bold">Página inicial</h1>
        <p class="mt-2 text-gray-600 dark:text-gray-300">
            Conteúdo da página.
        </p>
    </section>
{% endblock %}
```

O uso de `extends` permite reutilizar a estrutura principal. Assim, a configuração do tema pode permanecer no template base, enquanto as páginas individuais definem somente seus próprios conteúdos.

O JavaScript de alternância pode ser colocado em `static/js/theme.js`, evitando repetir o mesmo código em cada template.

---

## 35. Variáveis de tema com `@theme`

O Tailwind CSS v4 utiliza variáveis CSS de tema para definir valores que participam do sistema de utilitários.

Podemos personalizar cores, fontes, espaçamentos, pontos de quebra e animações por meio da diretiva `@theme`.

Exemplo:

```
@import "tailwindcss";

@theme {
    --color-brand-500: #7c3aed;
    --color-brand-600: #6d28d9;

    --font-display: "Inter", sans-serif;

    --breakpoint-tablet: 50rem;
}
```

Essas definições permitem utilizar classes correspondentes, como:

```
<div class="bg-brand-500 font-display tablet:grid">
    Conteúdo personalizado
</div>
```

Equivalências conceituais:

|Classe|CSS equivalente|
|---|---|
|`bg-brand-500`|`background-color: #7c3aed;`|
|`bg-brand-600`|`background-color: #6d28d9;`|
|`font-display`|`font-family: "Inter", sans-serif;`|
|`tablet:grid`|Aplica `display: grid` a partir do breakpoint `tablet`|

A classe `tablet:grid` utiliza o breakpoint personalizado de `50rem`, ou 800 pixels quando a unidade raiz equivale a 16 pixels.

A fonte `Inter` precisa estar disponível para o navegador, por exemplo, por meio de um arquivo de fonte local ou de uma fonte já instalada. Declarar a família no tema não instala a fonte automaticamente.

### 35.1. `@theme` versus variáveis CSS comuns

As duas formas estão relacionadas, mas não possuem exatamente o mesmo objetivo.

Variáveis CSS comuns:

```
:root {
    --cor-principal: #7c3aed;
}
```

Podem ser consumidas em CSS:

```
.botao {
    background-color: var(--cor-principal);
}
```

Já variáveis declaradas em `@theme` são integradas ao sistema de tema do Tailwind e podem originar utilitários.

Por exemplo:

```
@theme {
    --color-brand-500: #7c3aed;
}
```

Isso permite utilizar:

```
<button class="bg-brand-500">Salvar</button>
```

Portanto, utilize variáveis CSS comuns quando precisar de valores reutilizáveis no CSS e `@theme` quando quiser integrar esses valores ao sistema de utilitários do Tailwind.

---

## 36. Criando classes próprias com `@utility`

Embora o Tailwind incentive a composição de classes utilitárias diretamente no HTML, existem situações em que uma utilidade personalizada é conveniente.

No Tailwind CSS v4, podemos declarar uma utilidade com `@utility`.

```
@import "tailwindcss";

@utility text-balance {
    text-wrap: balance;
}
```

Depois, a classe pode ser utilizada normalmente:

```
<h1 class="text-3xl font-bold text-balance">
    Uma descrição de título que pode ocupar mais de uma linha
</h1>
```

Equivalência CSS:

```
.text-balance {
    text-wrap: balance;
}
```

A propriedade `text-wrap: balance` tenta distribuir o texto em várias linhas de maneira visualmente equilibrada, especialmente útil em títulos.

### 36.1. Quando criar uma utilidade personalizada?

Considere criar uma utilidade quando:

- Uma regra representa uma necessidade recorrente do projeto.
    
- O comportamento não é oferecido diretamente por uma classe existente.
    
- A classe possui uma finalidade clara e específica.
    
- A abstração melhora a manutenção do código.
    

Por outro lado, evite criar uma classe própria para cada combinação trivial de propriedades. Isso pode duplicar funcionalidades que o Tailwind já oferece.

A diretiva `@utility` é uma funcionalidade do Tailwind CSS v4. Em versões anteriores, o mecanismo de extensão de classes pode ser diferente.

---

## 37. Reutilizando estilos com componentes Flask

Em projetos Flask, uma mesma combinação de classes pode aparecer em muitos templates.

Por exemplo:

```
<button class="rounded-lg bg-blue-600 px-4 py-2 font-medium text-white transition-colors hover:bg-blue-700">
    Salvar
</button>
```

Se esse botão aparecer repetidamente em várias páginas, alterar todas as ocorrências pode ser trabalhoso.

Uma possibilidade é utilizar macros Jinja ou componentes reutilizáveis de template.

### 37.1. Exemplo com macro Jinja

Crie `templates/components.html`:

```
{% macro button(text, type="button") %}
    <button
        type="{{ type }}"
        class="rounded-lg bg-blue-600 px-4 py-2 font-medium text-white transition-colors hover:bg-blue-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600"
    >
        {{ text }}
    </button>
{% endmacro %}
```

Depois, em outro template:

```
{% from "components.html" import button %}

{{ button("Salvar") }}
```

Para criar um botão de envio de formulário:

```
{{ button("Entrar", "submit") }}
```

A macro recebe o texto e o tipo do botão e gera a estrutura HTML com as classes.

Essa abordagem é diferente de criar uma classe CSS personalizada: a macro reutiliza a estrutura HTML, os atributos e as classes Tailwind em conjunto.

### 37.2. Cuidados com macros e HTML

O argumento `text` é renderizado pelo mecanismo de autoescape do Jinja quando usado em um contexto HTML normal, ajudando a evitar que conteúdo fornecido pelo usuário seja interpretado como HTML.

Não desative o escape sem necessidade, por exemplo, utilizando `|safe` sobre conteúdo não confiável.

Macros também podem receber parâmetros adicionais para classes, atributos ou variações visuais. Entretanto, quanto mais parâmetros forem adicionados, mais importante se torna manter uma interface clara e consistente.

---

## 38. Boas práticas para personalizar o Tailwind

Ao personalizar um projeto Tailwind CSS v4:

- Use `@theme` para definir valores que devem participar do sistema de utilitários.
    
- Use `@utility` para criar utilidades específicas e reutilizáveis.
    
- Use variáveis CSS comuns para valores consumidos diretamente pelo CSS.
    
- Centralize comportamentos compartilhados em templates base ou macros Jinja quando isso facilitar a manutenção.
    
- Evite duplicar regras existentes no Tailwind.
    
- Não confunda a classe visual de um elemento com seu comportamento real.
    
- Teste o resultado no navegador e verifique o CSS compilado.
    
- Confirme a versão do Tailwind antes de utilizar diretivas específicas, como `@custom-variant`, `@theme` e `@utility`.
    

A personalização deve complementar o sistema de utilitários, e não criar uma camada paralela desnecessária de estilos.