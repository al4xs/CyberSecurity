
---

# find

> O comando `find` é uma das ferramentas mais poderosas do Linux para localizar arquivos e diretórios.
>
> Diferente do comando `ls`, que apenas lista o conteúdo de um diretório, o `find` percorre recursivamente toda a árvore de diretórios procurando arquivos que atendam aos critérios definidos.
>
> É um dos comandos mais utilizados por administradores de sistemas, desenvolvedores, analistas forenses, pentesters e profissionais de Cyber Security.

---

# O que é?

O `find` é um comando utilizado para pesquisar objetos dentro do sistema de arquivos.

Esses objetos podem ser:

- Arquivos;
- Diretórios;
- Links simbólicos;
- Pipes;
- Sockets;
- Dispositivos;
- Entre outros.

Ao contrário de comandos como `ls`, que apenas mostram o conteúdo imediato de um diretório, o `find` percorre automaticamente todos os seus subdiretórios até encontrar os arquivos que atendem aos critérios informados.

---

# Para que serve?

O comando `find` é utilizado quando precisamos localizar arquivos ou diretórios sem conhecer exatamente sua localização.

Alguns exemplos:

- Encontrar um arquivo de configuração;
- Procurar backups esquecidos;
- Localizar arquivos de log;
- Buscar arquivos grandes;
- Encontrar arquivos modificados recentemente;
- Procurar binários SUID;
- Localizar arquivos graváveis;
- Encontrar scripts;
- Procurar arquivos ocultos;
- Automatizar tarefas em Shell Script.

Em ambientes de Pentest e CTF, o `find` é uma das ferramentas mais importantes durante a fase de enumeração.

---

# Quando utilizar?

Sempre que precisar responder perguntas como:

> Onde está determinado arquivo?

> Existe algum backup perdido?

> Existem arquivos com permissões especiais?

> Existe algum diretório gravável?

> Qual usuário é o proprietário deste arquivo?

> Existem arquivos modificados recentemente?

Em vez de navegar manualmente por dezenas de diretórios utilizando `cd` e `ls`, podemos deixar que o `find` faça todo o trabalho.

---

# Como o find funciona?

Imagine a seguinte estrutura:

```text
/

├── home
│   ├── allan
│   │   ├── documento.txt
│   │   ├── script.sh
│   │   └── fotos
│   │
│   └── maria
│       ├── backup.zip
│       └── senha.txt
│
├── etc
│   └── passwd
│
└── var
    └── www
        └── index.php
```

Quando executamos:

```bash
find /home
```

o `find` faz algo semelhante a isto:

```text
Entrar em /home

↓

Encontrar allan

↓

Entrar em allan

↓

Ler todos os arquivos

↓

Entrar em fotos

↓

Voltar

↓

Entrar em maria

↓

Ler todos os arquivos

↓

Continuar até terminar
```

Observe que ele entra automaticamente em todos os diretórios.

Esse comportamento é chamado de **busca recursiva**.

---

# Busca recursiva

Uma busca recursiva significa que o programa percorre todos os diretórios filhos automaticamente.

Por exemplo:

```text
/home

├── usuario1
│   ├── Downloads
│   ├── Documentos
│   └── Imagens
│
├── usuario2
│   ├── Desktop
│   └── Projetos
│
└── usuario3
```

O `find` visita todos esses diretórios sem que seja necessário executar vários comandos.

Esse é um dos principais motivos pelos quais ele é tão poderoso.

---

# Diferença entre ls e find

É comum iniciantes confundirem esses dois comandos.

Embora ambos trabalhem com arquivos e diretórios, seus objetivos são diferentes.

## ls

O comando `ls` apenas lista o conteúdo de um diretório.

Exemplo:

```bash
ls /home
```

Saída:

```text
allan
maria
```

Se quisermos listar os arquivos dentro de `allan`, será necessário executar outro comando.

```bash
ls /home/allan
```

Saída:

```text
documento.txt
script.sh
fotos
```

---

## find

Agora veja o mesmo exemplo utilizando o `find`.

```bash
find /home
```

Saída:

```text
/home
/home/allan
/home/allan/documento.txt
/home/allan/script.sh
/home/allan/fotos
/home/maria
/home/maria/backup.zip
/home/maria/senha.txt
```

Observe que o `find` percorreu toda a árvore de diretórios automaticamente.

---

# Estrutura do comando

Todo comando `find` possui uma estrutura semelhante.

Exemplo:

```bash
find / -type f -name "*.txt"
```

Visualmente:

```text
find
 │
 ├── /
 ├── -type f
 └── -name "*.txt"
```

Cada parte possui uma função específica.

| Parte | Função |
|--------|--------|
| `find` | Programa responsável pela busca |
| `/` | Diretório onde a busca começará |
| `-type` | Filtra o tipo do objeto |
| `f` | Arquivo comum |
| `-name` | Filtra pelo nome |
| `"*.txt"` | Arquivos terminados em `.txt` |

Nos próximos capítulos aprenderemos detalhadamente cada uma dessas partes.

---

# Como ler um comando find

Uma boa prática é aprender a traduzir o comando para uma linguagem natural.

Exemplo:

```bash
find /home -type f -name "*.pdf"
```

Podemos interpretar assim:

> Procure, dentro do diretório `/home`, apenas arquivos cujo nome termine com `.pdf`.

Outro exemplo:

```bash
find /tmp
```

Tradução:

> Percorra todo o diretório `/tmp` mostrando tudo o que encontrar.

Quanto mais você praticar essa tradução mental, mais fácil será entender comandos complexos.

---

# O find não procura apenas pelo nome

Esse é um erro muito comum.

Muitos iniciantes acreditam que o `find` serve apenas para localizar arquivos pelo nome.

Na realidade, ele consegue pesquisar utilizando dezenas de critérios diferentes.

Por exemplo:

- Nome;
- Caminho;
- Tipo;
- Usuário;
- Grupo;
- Permissões;
- Data de modificação;
- Data de acesso;
- Tamanho;
- Quantidade de links;
- Arquivos vazios;
- Expressões regulares;
- Entre muitos outros.

É justamente essa flexibilidade que faz do `find` uma das ferramentas mais importantes do Linux.

---

# Onde o find é utilizado?

O comando `find` aparece praticamente em todas as áreas que utilizam Linux.

## Administração de Sistemas

Exemplos:

- Encontrar logs;
- Localizar backups;
- Procurar arquivos grandes;
- Remover arquivos antigos.

---

## Shell Script

É comum utilizar o `find` para automatizar tarefas.

Exemplo:

```bash
find /tmp -type f
```

Depois utilizar os resultados em um loop ou em outro comando.

---

## Pentest

Durante um teste de invasão, o `find` é utilizado para localizar:

- Arquivos SUID;
- Arquivos graváveis;
- Scripts;
- Backups;
- Arquivos de configuração;
- Credenciais;
- Chaves SSH.

---

## CTF

Em laboratórios de Capture The Flag é extremamente comum utilizar o `find` para descobrir:

- Arquivos escondidos;
- Scripts vulneráveis;
- Backups esquecidos;
- Arquivos `.env`;
- Bancos de dados;
- Configurações incorretas.

Em muitos desafios, a resposta está escondida em algum arquivo encontrado pelo `find`.

---

# Resumo

Até este ponto aprendemos que:

- O `find` é um programa de busca.
- Ele percorre diretórios de forma recursiva.
- Pode localizar muito mais do que apenas nomes de arquivos.
- É uma das ferramentas mais importantes do Linux.
- É amplamente utilizado em Administração de Sistemas, Shell Script, Pentest e CTF.

Na próxima parte começaremos a estudar cada componente do comando individualmente, entendendo exatamente o que fazem:

- Diretório inicial (`/`, `/home`, `/tmp`, etc.);
- `-type`;
- `-name`;
- `-iname`;
- Wildcards (`*`, `?`, `[]`);
- E como combinar esses elementos corretamente.

---

# Diretório Inicial da Busca

Antes de procurar qualquer arquivo, o `find` precisa saber **onde a busca começará**.

Esse local é chamado de **diretório inicial** (*Starting Directory*).

Ele sempre é o primeiro argumento informado após o comando `find`.

Sintaxe:

```bash
find DIRETORIO [EXPRESSÕES] [AÇÕES]
```

Exemplo:

```bash
find /home
```

Neste exemplo:

```text
find
 │
 └── /home
```

O `find` começará a busca dentro de:

```text
/home
```

e percorrerá automaticamente todos os seus subdiretórios.

---

# Como o find percorre os diretórios?

Imagine a seguinte estrutura.

```text
/home

├── allan
│   ├── Desktop
│   ├── Downloads
│   └── Documentos
│
├── maria
│   ├── Projetos
│   └── Imagens
│
└── joao
```

Ao executar:

```bash
find /home
```

o `find` faz algo semelhante a isto:

```text
Entrar em /home

↓

Entrar em allan

↓

Entrar em Desktop

↓

Voltar

↓

Entrar em Downloads

↓

Voltar

↓

Entrar em Documentos

↓

Voltar

↓

Entrar em maria

↓

Entrar em Projetos

↓

Voltar

↓

Entrar em Imagens

↓

Voltar

↓

Entrar em joao

↓

Fim
```

Esse processo é chamado de **busca recursiva**.

---

# Utilizando `/`

Quando utilizamos apenas:

```bash
find /
```

a busca começa na raiz do sistema.

Estrutura simplificada:

```text
/

├── bin
├── boot
├── dev
├── etc
├── home
├── media
├── opt
├── root
├── srv
├── tmp
├── usr
├── var
└── ...
```

Isso significa que praticamente todo o sistema será percorrido.

---

## Quando utilizar?

Quando você não sabe onde o arquivo pode estar.

Muito comum em:

- Pentest;
- CTF;
- Administração Linux;
- Forense.

Exemplo:

```bash
find / -name "config.php"
```

Saída:

```text
/var/www/html/config.php
```

---

## Vantagens

- Procura em praticamente todo o sistema.
- Ideal quando a localização é desconhecida.

---

## Desvantagens

Pode demorar.

Dependendo das permissões do usuário, aparecerão diversas mensagens como:

```text
Permission denied
```

Por isso normalmente utilizamos:

```bash
2>/dev/null
```

que já estudamos na anotação de redirecionamentos.

---

# Utilizando `/home`

Também é muito comum iniciar a busca apenas no diretório dos usuários.

Exemplo:

```bash
find /home
```

Agora somente os diretórios dentro de:

```text
/home
```

serão analisados.

---

## Quando utilizar?

Quando deseja pesquisar apenas arquivos dos usuários.

Muito utilizado para localizar:

- Documentos;
- Chaves SSH;
- Backups;
- Scripts;
- Arquivos `.env`;
- Arquivos `.txt`;
- Credenciais.

---

## Exemplo

```bash
find /home -name "*.txt"
```

Saída:

```text
/home/allan/anotacoes.txt

/home/maria/senhas.txt
```

---

# Utilizando `.`

O ponto (`.`) representa o **diretório atual**.

Antes de entender isso, veja:

```bash
pwd
```

Saída:

```text
/home/allan
```

Agora execute:

```bash
find .
```

Na prática, o comando será equivalente a:

```bash
find /home/allan
```

O `find` começará exatamente no diretório atual.

---

## Quando utilizar?

É o mais comum durante o desenvolvimento de programas e Shell Scripts.

Exemplo:

```bash
find . -name "*.py"
```

Saída:

```text
./main.py

./api/app.py

./tests/test.py
```

Observe que apenas o projeto atual foi pesquisado.

---

# Utilizando `..`

Os dois pontos (`..`) representam o diretório pai.

Exemplo:

Imagine que estamos em:

```text
/home/allan/Documentos
```

Executando:

```bash
find ..
```

a busca começará em:

```text
/home/allan
```

---

# Utilizando caminhos específicos

Nada impede que a busca seja iniciada em qualquer diretório.

Por exemplo.

```bash
find /etc
```

Pesquisará apenas:

```text
/etc
```

---

```bash
find /var/www
```

Pesquisará apenas:

```text
/var/www
```

---

```bash
find /opt
```

Pesquisará apenas:

```text
/opt
```

---

## Essa é uma boa prática

Quanto menor for o diretório pesquisado, mais rápida será a execução.

Em vez de utilizar:

```bash
find /
```

prefira:

```bash
find /var/www
```

quando souber que o arquivo está relacionado ao servidor Web.

---

# Caminhos absolutos

Um caminho absoluto sempre começa com:

```text
/
```

Exemplos:

```text
/home/allan

/etc

/opt

/usr/bin

/var/www
```

Independentemente do diretório atual, o resultado será sempre o mesmo.

---

# Caminhos relativos

Um caminho relativo depende da posição atual do usuário.

Exemplo:

```bash
find .
```

ou

```bash
find ..
```

Esses comandos mudam de comportamento dependendo do diretório onde você estiver.

---

# Exemplo comparando

Imagine:

```text
pwd
```

Saída:

```text
/home/allan
```

Agora:

```bash
find .
```

Resultado:

```text
/home/allan
```

Se mudarmos para:

```bash
cd /opt
```

e executarmos novamente:

```bash
find .
```

Agora o resultado será:

```text
/opt
```

---

# Qual utilizar?

## Caminho absoluto

Ideal quando:

- Scripts;
- Automação;
- Pentest;
- CTF.

Evita erros.

---

## Caminho relativo

Ideal quando:

- Desenvolvimento;
- Projeto atual;
- Pequenas buscas.

---

# Boas práticas

✔ Utilize caminhos específicos sempre que possível.

✔ Evite utilizar `find /` sem necessidade.

✔ Quanto menor o diretório inicial, mais rápida será a busca.

✔ Durante Pentests, normalmente vale mais a pena pesquisar diretamente em:

```text
/opt

/home

/var/www

/tmp

/dev/shm

/usr/local/bin
```

do que pesquisar o sistema inteiro.

---

# Resumo

| Caminho | Significado |
|----------|------------|
| `/` | Raiz do sistema |
| `/home` | Diretório dos usuários |
| `/etc` | Arquivos de configuração |
| `/opt` | Aplicações instaladas manualmente |
| `/var/www` | Aplicações Web |
| `.` | Diretório atual |
| `..` | Diretório pai |

---

# Próxima parte

Agora que entendemos **onde** o `find` inicia a busca, aprenderemos como filtrar os resultados utilizando:

- `-type`
- `-name`
- `-iname`
- Wildcards (`*`, `?`, `[]`)

Esses parâmetros são a base de praticamente todos os comandos `find` utilizados em Linux, Shell Script, Pentest e CTF.

---

# Como o find decide o que mostrar?

Até agora vimos que o `find` percorre diretórios recursivamente.

Mas imagine o seguinte comando:

```bash
find /home
```

Resultado:

```text
/home
/home/allan
/home/allan/Desktop
/home/allan/Desktop/foto.png
/home/allan/Downloads
/home/allan/script.sh
/home/maria
/home/maria/documento.pdf
```

Ele mostrou **tudo**.

Na prática, quase nunca queremos listar todos os arquivos do sistema.

Normalmente queremos encontrar algo específico.

Por exemplo:

- Apenas arquivos `.txt`;
- Apenas diretórios;
- Apenas arquivos pertencentes ao root;
- Apenas arquivos maiores que 100 MB;
- Apenas arquivos modificados hoje.

Para isso utilizamos **filtros**.

---

# O que são filtros?

Filtros são condições utilizadas para dizer ao `find` quais arquivos devem aparecer na saída.

Imagine a seguinte pergunta.

> Quero encontrar apenas arquivos PDF.

O `find` fará algo parecido com isto:

```text
Arquivo encontrado

↓

É um PDF?

↓

Sim → Mostrar

↓

Não → Ignorar

↓

Próximo arquivo
```

Isso acontece para todos os arquivos encontrados.

---

# Como o find trabalha internamente?

Imagine novamente esta estrutura.

```text
/home

├── allan
│   ├── foto.png
│   ├── senha.txt
│   ├── script.sh
│   └── backup.zip
│
└── maria
    ├── documento.pdf
    └── imagem.jpg
```

Agora execute:

```bash
find /home -name "*.txt"
```

O `find` faz aproximadamente isto:

```text
Entrar em /home

↓

Encontrou foto.png

↓

Termina com .txt?

↓

Não

↓

Ignorar

↓

Encontrou senha.txt

↓

Termina com .txt?

↓

Sim

↓

Mostrar

↓

Encontrou script.sh

↓

Não

↓

Ignorar

↓

Encontrou backup.zip

↓

Não

↓

Ignorar

↓

Encontrou documento.pdf

↓

Não

↓

Ignorar
```

Resultado final:

```text
/home/allan/senha.txt
```

---

# O que são expressões?

Toda condição utilizada pelo `find` recebe o nome de **expressão** (*Expression*).

Exemplo:

```bash
-name "*.txt"
```

é uma expressão.

---

Outro exemplo.

```bash
-type f
```

Também é uma expressão.

---

Outro.

```bash
-user root
```

Também é uma expressão.

---

Na prática, podemos pensar assim:

```text
Expressão

↓

Condição

↓

Filtro
```

São praticamente a mesma ideia.

---

# Podemos utilizar várias expressões?

Sim.

Na verdade, é aí que o `find` se torna extremamente poderoso.

Exemplo:

```bash
find /home -type f -name "*.txt"
```

Agora existem duas condições.

```text
Arquivo encontrado

↓

É arquivo?

↓

Sim

↓

Termina com .txt?

↓

Sim

↓

Mostrar
```

Observe que agora ele precisa atender às duas condições.

---

# O conceito de AND

Quando colocamos várias expressões lado a lado, o `find` utiliza um operador chamado **AND**.

Mesmo que ele não apareça escrito.

Exemplo:

```bash
find /home -type f -name "*.txt"
```

Internamente o `find` interpreta como:

```text
-type f

AND

-name "*.txt"
```

Ou seja.

O arquivo precisa:

✔ Ser um arquivo.

E

✔ Terminar com `.txt`.

---

# Exemplo prático

Imagine estes arquivos.

```text
documento.txt

foto.png

backup.zip

Downloads/

script.sh
```

Comando:

```bash
find . -type f -name "*.txt"
```

Resultado:

```text
documento.txt
```

Por quê?

Porque:

✔ É um arquivo.

✔ Termina com `.txt`.

---

Agora veja este.

```text
Downloads
```

Ele termina com `.txt`?

Não.

Além disso:

Nem é um arquivo.

Portanto será ignorado.

---

# O conceito de OR

Às vezes queremos encontrar arquivos diferentes.

Por exemplo.

Arquivos:

```text
.zip

.tar
```

Nesse caso utilizamos:

```bash
-o
```

Que significa:

```text
OR

OU
```

Exemplo:

```bash
find . -name "*.zip" -o -name "*.tar"
```

Tradução:

> Procure arquivos terminados em `.zip` **ou** `.tar`.

---

Fluxo:

```text
Arquivo encontrado

↓

É ZIP?

↓

Sim → Mostrar

↓

Não

↓

É TAR?

↓

Sim → Mostrar

↓

Não

↓

Ignorar
```

---

# O conceito de NOT

Também podemos inverter uma condição.

Utilizando:

```bash
!
```

Exemplo:

```bash
find . ! -name "*.txt"
```

Tradução:

> Mostre tudo, exceto arquivos terminados em `.txt`.

---

Outro exemplo.

```bash
find . ! -type d
```

Tradução:

> Mostre tudo que **não** for diretório.

---

# O find pensa como um filtro

É importante mudar a forma de pensar.

O `find` não procura diretamente um arquivo.

Ele percorre o sistema fazendo perguntas.

Imagine.

```bash
find . -type f -name "*.pdf"
```

O pensamento dele será parecido com isto.

```text
Arquivo encontrado

↓

É um arquivo?

↓

Não?

↓

Ignorar

↓

Sim?

↓

Termina com .pdf?

↓

Não?

↓

Ignorar

↓

Sim?

↓

Mostrar
```

Sempre pense dessa forma.

Isso ajuda muito quando os comandos ficam grandes.

---

# Por que aprender isso?

Porque quase todos os comandos do `find` seguem exatamente essa lógica.

Veja este exemplo.

```bash
find /var/www \
-type f \
-user www-data \
-name "*.php"
```

Mesmo sem conhecer todas as flags, você já consegue interpretar.

Tradução:

> Procure arquivos dentro de `/var/www` que pertençam ao usuário `www-data` e cujo nome termine com `.php`.

Observe como o comando deixa de parecer complicado quando entendemos sua lógica.

---

# Erro comum

Muitos iniciantes enxergam o `find` assim:

```bash
find /alguma/coisa -alguma-flag
```

E tentam decorar cada comando.

O ideal é pensar assim.

```text
find

↓

Onde procurar?

↓

O que procurar?

↓

Quais condições?

↓

O que fazer quando encontrar?
```

Essa forma de pensar facilitará o entendimento de praticamente qualquer comando do `find`.

---

# Resumo

Até aqui aprendemos que:

- O `find` percorre diretórios recursivamente.
- Cada condição é chamada de expressão.
- Podemos combinar várias expressões.
- O operador padrão é o **AND**.
- O operador `-o` representa **OR**.
- O operador `!` inverte uma condição.
- O `find` funciona como um grande filtro aplicado a cada arquivo encontrado.

---

# Próxima parte

Agora começaremos a estudar as primeiras expressões do `find`.

Na próxima seção veremos:

- `-type`
- Tipos de arquivos
- Diferença entre arquivos, diretórios e links
- Quando utilizar cada tipo
- Exemplos em Linux
- Exemplos em Shell Script
- Exemplos em Pentest
- Exemplos em CTF

---

# Tipos de Objetos do Sistema de Arquivos

Antes de aprender a utilizar a opção:

```bash
-type
```

precisamos entender um conceito importante.

No Linux, **nem tudo é considerado um arquivo comum**.

O sistema operacional trata diversos recursos como arquivos.

Por exemplo:

- Arquivos comuns;
- Diretórios;
- Links simbólicos;
- Pipes;
- Sockets;
- Dispositivos.

Quando utilizamos:

```bash
find /
```

o `find` pode encontrar qualquer um desses objetos.

Por isso existe a opção:

```bash
-type
```

que permite filtrar exatamente qual tipo desejamos encontrar.

---

# Como o Linux enxerga os arquivos?

Imagine um armário.

Dentro dele existem vários objetos diferentes.

```text
Armário

├── Camiseta
├── Calça
├── Sapato
├── Mochila
└── Livro
```

Você poderia dizer:

> Quero apenas os livros.

O Linux funciona da mesma forma.

Dentro de um diretório podem existir diferentes tipos de objetos.

O `find` permite escolher quais deles serão mostrados.

---

# A opção -type

Sintaxe:

```bash
find DIRETORIO -type TIPO
```

Onde:

```text
TIPO
```

é representado por uma única letra.

As mais utilizadas são:

| Tipo | Significado |
|------|-------------|
| `f` | Arquivo comum |
| `d` | Diretório |
| `l` | Link simbólico |
| `s` | Socket |
| `p` | Pipe (FIFO) |
| `b` | Dispositivo de bloco |
| `c` | Dispositivo de caractere |

Durante o dia a dia você utilizará principalmente:

- `f`
- `d`
- `l`

As demais aparecem com menor frequência.

---

# Arquivo comum (`f`)

É o tipo mais conhecido.

Representa arquivos utilizados para armazenar dados.

Exemplos:

```text
script.sh

foto.png

senha.txt

config.php

backup.zip

database.sql

video.mp4
```

Todos esses arquivos são considerados:

```text
Arquivos comuns
```

---

## Como procurar?

```bash
find /home -type f
```

Resultado:

```text
/home/allan/script.sh

/home/allan/documento.txt

/home/allan/foto.png
```

Observe que apenas arquivos comuns são exibidos.

Os diretórios são ignorados.

---

## Quando utilizar?

É a opção mais utilizada.

Principalmente para localizar:

- Scripts;
- Backups;
- Arquivos de configuração;
- Arquivos de texto;
- Bancos de dados;
- Credenciais.

---

## Pentest

Muito utilizada.

Exemplo:

```bash
find /var/www -type f
```

Objetivo:

Listar todos os arquivos da aplicação Web.

---

# Diretórios (`d`)

Os diretórios são utilizados para organizar arquivos.

Exemplo:

```text
/home

/home/allan

/home/maria

/opt

/tmp

/var/www
```

Todos eles são diretórios.

---

## Como procurar?

```bash
find /home -type d
```

Resultado:

```text
/home

/home/allan

/home/allan/Desktop

/home/allan/Downloads

/home/maria
```

Observe que apenas diretórios aparecem.

Os arquivos são ignorados.

---

## Quando utilizar?

Muito útil quando desejamos:

- Conhecer a estrutura de um sistema;
- Descobrir diretórios escondidos;
- Encontrar diretórios graváveis;
- Mapear uma aplicação.

---

## Pentest

Exemplo:

```bash
find /var/www -type d
```

Pode revelar diretórios como:

```text
uploads

backup

admin

private

config
```

Mesmo que estejam vazios.

---

# Links simbólicos (`l`)

Os links simbólicos (*Symbolic Links* ou *Symlinks*) funcionam como atalhos.

Imagine um atalho na Área de Trabalho do Windows.

Ele aponta para outro arquivo.

No Linux o conceito é semelhante.

Exemplo:

```text
/home/allan/python

↓

/usr/bin/python3
```

O primeiro arquivo é apenas um link.

O programa real está em outro local.

---

## Como procurar?

```bash
find /usr/bin -type l
```

Resultado:

```text
/usr/bin/python

/usr/bin/editor

/usr/bin/sh
```

---

## Quando utilizar?

Durante Pentests, links simbólicos podem revelar:

- Caminhos importantes;
- Arquivos compartilhados;
- Configurações inesperadas.

---

# Pipes (`p`)

Pipes (FIFO) são utilizados para comunicação entre processos.

Eles são menos comuns para iniciantes.

Exemplo:

```text
Processo A

↓

Pipe

↓

Processo B
```

---

## Como procurar?

```bash
find /tmp -type p
```

---

## Quando utilizar?

Normalmente:

- Administração Linux;
- Desenvolvimento;
- Análise de processos.

Em Pentest aparecem com pouca frequência.

---

# Sockets (`s`)

Sockets são utilizados para comunicação entre processos.

Exemplo:

```text
Programa A

↓

Socket

↓

Programa B
```

---

## Como procurar?

```bash
find /run -type s
```

---

## Quando utilizar?

Principalmente durante:

- Troubleshooting;
- Administração Linux;
- Forense.

---

# Dispositivos de bloco (`b`)

Representam dispositivos que armazenam dados em blocos.

Exemplo:

```text
HD

SSD

Pendrive
```

Normalmente aparecem em:

```text
/dev
```

---

## Como procurar?

```bash
find /dev -type b
```

---

## Exemplos

```text
/dev/sda

/dev/sda1

/dev/nvme0n1
```

---

# Dispositivos de caractere (`c`)

São dispositivos que trabalham caractere por caractere.

Exemplos:

```text
Terminal

Mouse

Teclado
```

---

## Como procurar?

```bash
find /dev -type c
```

---

## Exemplos

```text
/dev/null

/dev/random

/dev/tty

/dev/zero
```

---

# Como descobrir o tipo de um arquivo?

Além do `find`, podemos utilizar:

```bash
ls -l
```

Exemplo:

```text
-rw-r--r-- arquivo.txt

drwxr-xr-x Downloads

lrwxrwxrwx python -> python3
```

Observe o primeiro caractere.

| Letra | Tipo |
|--------|------|
| `-` | Arquivo comum |
| `d` | Diretório |
| `l` | Link simbólico |
| `p` | Pipe |
| `s` | Socket |
| `b` | Dispositivo de bloco |
| `c` | Dispositivo de caractere |

Esse é exatamente o mesmo conceito utilizado pela opção:

```bash
-type
```

---

# Qual tipo é mais utilizado?

No dia a dia.

⭐⭐⭐⭐⭐

```bash
-type f
```

Arquivos.

---

⭐⭐⭐⭐⭐

```bash
-type d
```

Diretórios.

---

⭐⭐⭐☆☆

```bash
-type l
```

Links simbólicos.

---

⭐⭐☆☆☆

```bash
-type s
```

Sockets.

---

⭐☆☆☆☆

```bash
-type p
```

Pipes.

---

# Resumo

| Tipo | Uso mais comum |
|------|----------------|
| `f` | Arquivos |
| `d` | Diretórios |
| `l` | Links simbólicos |
| `p` | Pipes |
| `s` | Sockets |
| `b` | Dispositivos de bloco |
| `c` | Dispositivos de caractere |

Até este ponto você já consegue entender completamente comandos como:

```bash
find /var/www -type f
```

ou

```bash
find /home -type d
```

Na próxima parte aprenderemos uma das opções mais utilizadas do `find`:

- `-name`
- `-iname`
- Wildcards (`*`, `?`, `[]`)

Essas opções são responsáveis por filtrar arquivos utilizando seus nomes.

---

# Wildcards (Globbing)

Antes de aprender as opções:

```bash
-name
```

e

```bash
-iname
```

precisamos entender um conceito muito importante do Linux chamado **Wildcard** (ou **Globbing**).

Wildcards são caracteres especiais utilizados para representar um ou mais caracteres.

Eles permitem realizar buscas flexíveis sem precisar conhecer exatamente o nome de um arquivo.

Imagine que você possui centenas de arquivos.

```text
documento.txt
senhas.txt
config.txt
backup.zip
imagem.png
script.sh
```

Em vez de procurar cada arquivo individualmente, podemos utilizar wildcards.

---

# O caractere `*`

O wildcard mais utilizado é:

```text
*
```

Ele significa:

> Qualquer quantidade de caracteres (inclusive nenhum).

Exemplo:

```text
*.txt
```

Tradução:

> Qualquer nome terminado em `.txt`.

O `*` pode representar:

```text
documento

config

teste

123

qualquercoisa
```

Portanto:

```text
*.txt
```

corresponde a:

```text
documento.txt

config.txt

teste.txt

123.txt
```

---

# Outro exemplo

```text
backup*
```

Resultado:

```text
backup

backup1

backup_final

backup_antigo

backup.tar
```

Observe que agora o nome precisa começar com:

```text
backup
```

Depois disso qualquer sequência de caracteres é aceita.

---

# Outro exemplo

```text
*config*
```

Resultado:

```text
config

config.php

meu_config

config_backup

teste_config_old
```

Nesse caso o nome apenas precisa conter a palavra:

```text
config
```

em qualquer posição.

---

# O caractere `?`

O wildcard:

```text
?
```

representa **apenas um único caractere**.

Exemplo:

```text
file?.txt
```

Resultado:

```text
file1.txt

file2.txt

fileA.txt

fileX.txt
```

Não corresponde a:

```text
file10.txt
```

Porque existem dois caracteres após:

```text
file
```

---

# O caractere `[]`

Também podemos definir um conjunto de caracteres.

Exemplo:

```text
file[123].txt
```

Resultado:

```text
file1.txt

file2.txt

file3.txt
```

Não corresponde a:

```text
file4.txt
```

---

Outro exemplo:

```text
[a-z]
```

Representa:

```text
qualquer letra minúscula.
```

---

Outro exemplo:

```text
[A-Z]
```

Representa:

```text
qualquer letra maiúscula.
```

---

Outro exemplo:

```text
[0-9]
```

Representa:

```text
qualquer número.
```

---

# Resumindo os Wildcards

| Wildcard | Significado |
|----------|-------------|
| `*` | Qualquer quantidade de caracteres |
| `?` | Apenas um caractere |
| `[]` | Conjunto de caracteres |

Esses três são utilizados em diversos comandos do Linux.

Não apenas no `find`.

Você encontrará esses caracteres em:

- `ls`
- `cp`
- `mv`
- `rm`
- `tar`
- `grep`
- Shell Script
- Bash

---

# A opção -name

Agora que entendemos os wildcards, podemos estudar a opção mais utilizada do `find`.

Sintaxe:

```bash
-name PADRÃO
```

Ela permite filtrar arquivos utilizando seu nome.

---

# Como funciona?

Imagine que temos:

```text
documento.txt

config.php

backup.zip

script.sh

imagem.png
```

Executando:

```bash
find . -name "*.txt"
```

O `find` faz a seguinte pergunta para cada arquivo.

```text
O nome termina com ".txt"?

↓

Sim

↓

Mostrar

↓

Não

↓

Ignorar
```

Resultado:

```text
./documento.txt
```

---

# Exemplo simples

```bash
find . -name "config.php"
```

Resultado:

```text
./config.php
```

Observe que agora não utilizamos wildcards.

O nome precisa ser exatamente:

```text
config.php
```

---

# Procurando todos os arquivos TXT

```bash
find /home -name "*.txt"
```

Resultado:

```text
/home/allan/anotacoes.txt

/home/maria/senhas.txt

/home/joao/lista.txt
```

---

# Procurando scripts Shell

```bash
find . -name "*.sh"
```

Resultado:

```text
./backup.sh

./scanner.sh

./exploit.sh
```

---

# Procurando arquivos PHP

```bash
find /var/www -name "*.php"
```

Resultado:

```text
/var/www/index.php

/var/www/login.php

/var/www/admin/config.php
```

Muito utilizado em Pentest Web.

---

# Procurando Backups

```bash
find / -name "*.bak" 2>/dev/null
```

Resultado:

```text
/etc/passwd.bak

/var/www/config.php.bak
```

---

# Procurando arquivos compactados

```bash
find /home -name "*.zip"
```

Resultado:

```text
backup.zip

documentos.zip

projeto.zip
```

---

# Atenção

A opção:

```bash
-name
```

é **case-sensitive**.

Ou seja.

Ela diferencia letras maiúsculas de minúsculas.

Imagine:

```text
Backup.zip

backup.zip
```

Agora execute:

```bash
find . -name "backup.zip"
```

Resultado:

```text
backup.zip
```

O arquivo:

```text
Backup.zip
```

não será encontrado.

---

# Quando utilizar?

Sempre que souber total ou parcialmente o nome do arquivo.

É muito utilizada para localizar:

- Arquivos de configuração;
- Backups;
- Scripts;
- Bancos de dados;
- Arquivos de texto;
- Arquivos específicos.

---

# Casos de uso em Shell Script

Localizar todos os arquivos de log.

```bash
find /var/log -name "*.log"
```

---

Localizar todos os scripts.

```bash
find . -name "*.sh"
```

---

# Casos de uso em Pentest

Procurar arquivos de configuração.

```bash
find /var/www -name "*.php"
```

---

Procurar backups.

```bash
find / -name "*.bak" 2>/dev/null
```

---

Procurar arquivos `.env`.

```bash
find / -name ".env" 2>/dev/null
```

---

# Erros comuns

## Esquecer as aspas

Errado:

```bash
find . -name *.txt
```

Correto:

```bash
find . -name "*.txt"
```

As aspas evitam que o próprio Shell expanda o wildcard antes do `find` receber o argumento.

---

## Esquecer o wildcard

```bash
-name txt
```

Isso procura apenas um arquivo chamado:

```text
txt
```

O correto normalmente será:

```bash
-name "*.txt"
```

---

# Resumo

A opção:

```bash
-name
```

permite localizar arquivos utilizando seu nome.

Ela aceita wildcards e é uma das opções mais utilizadas do comando `find`.

Na próxima parte estudaremos:

- `-iname`
- Diferença entre `-name` e `-iname`
- Quando utilizar cada uma
- Casos práticos
- Depois entraremos em `-path` e `-ipath`.

---

# A opção `-iname`

Até agora aprendemos a utilizar:

```bash
-name
```

Essa opção procura arquivos utilizando seu nome.

Porém, ela possui uma limitação importante.

Ela diferencia letras maiúsculas de minúsculas.

Esse comportamento é chamado de **Case Sensitive**.

---

# O que significa Case Sensitive?

Um sistema **Case Sensitive** diferencia letras maiúsculas de minúsculas.

Por exemplo:

```text
config.php
```

é diferente de:

```text
Config.php
```

que também é diferente de:

```text
CONFIG.PHP
```

Embora os três nomes pareçam semelhantes, para o Linux eles representam arquivos completamente diferentes.

---

# Exemplo

Imagine o seguinte diretório.

```text
.

├── config.php
├── Config.php
├── CONFIG.PHP
├── login.php
└── index.php
```

Agora execute:

```bash
find . -name "config.php"
```

Resultado:

```text
./config.php
```

Observe que apenas um arquivo foi encontrado.

Os demais foram ignorados.

---

# Como resolver isso?

Para realizar buscas ignorando letras maiúsculas e minúsculas existe a opção:

```bash
-iname
```

O "i" significa:

```text
Insensitive
```

Ou seja:

```text
Case Insensitive
```

---

# Como funciona?

Sintaxe:

```bash
find DIRETORIO -iname PADRÃO
```

Exemplo:

```bash
find . -iname "config.php"
```

Resultado:

```text
./config.php

./Config.php

./CONFIG.PHP
```

Agora todos os arquivos foram encontrados.

---

# Diferença entre `-name` e `-iname`

## Utilizando `-name`

```bash
find . -name "*.jpg"
```

Arquivos:

```text
foto.jpg

Foto.jpg

FOTO.JPG
```

Resultado:

```text
foto.jpg
```

---

## Utilizando `-iname`

```bash
find . -iname "*.jpg"
```

Resultado:

```text
foto.jpg

Foto.jpg

FOTO.JPG
```

Observe como todas as variações foram encontradas.

---

# Quando utilizar `-iname`?

Utilize quando não tiver certeza da capitalização do nome.

Por exemplo.

Você sabe que existe um arquivo chamado:

```text
backup.zip
```

Mas não sabe se ele foi salvo como:

```text
backup.zip

Backup.zip

BACKUP.ZIP
```

Nesse caso:

```bash
find . -iname "*backup*"
```

é uma excelente escolha.

---

# Procurando arquivos PHP

```bash
find /var/www -iname "*.php"
```

Resultado:

```text
index.php

Config.PHP

Login.Php

ADMIN.PHP
```

---

# Procurando imagens

```bash
find . -iname "*.png"
```

Resultado:

```text
foto.PNG

imagem.png

Logo.PnG
```

---

# Procurando backups

```bash
find / -iname "*.bak" 2>/dev/null
```

Resultado:

```text
passwd.BAK

config.bak

Backup.Bak
```

---

# Casos de uso em Pentest

Durante um Pentest, muitas vezes encontramos arquivos criados manualmente pelos desenvolvedores.

Nem sempre eles seguem um padrão.

Exemplo:

```text
Config.php

config.php

CONFIG.PHP

Database.php

DATABASE.php
```

Utilizando:

```bash
-name
```

alguns arquivos podem passar despercebidos.

Já:

```bash
-iname
```

encontra todos eles.

---

# Casos de uso em CTF

Em diversos laboratórios os organizadores utilizam nomes incomuns justamente para dificultar a enumeração.

Exemplo:

```text
Secret.txt

FLAG.TXT

Backup.ZIP
```

Utilizando:

```bash
find / -iname "*.txt"
```

todos esses arquivos serão encontrados.

---

# Comparando

| Opção | Diferencia maiúsculas? |
|--------|------------------------|
| `-name` | Sim |
| `-iname` | Não |

---

# Boas práticas

Utilize:

```bash
-name
```

quando souber exatamente o nome do arquivo.

Exemplo:

```text
config.php
```

---

Utilize:

```bash
-iname
```

quando não souber como o arquivo foi nomeado.

---

# Erros comuns

## Pensar que `-iname` é mais lento

Na maioria dos casos, a diferença é praticamente imperceptível.

Escolha a opção pensando na necessidade da busca, e não em desempenho.

---

## Utilizar `-iname` sempre

Embora funcione, muitas vezes é desnecessário.

Se você sabe exatamente o nome do arquivo, prefira:

```bash
-name
```

---

# Exemplo comparando os dois

Estrutura:

```text
.

├── backup.zip
├── Backup.zip
├── BACKUP.ZIP
└── script.sh
```

Comando:

```bash
find . -name "*.zip"
```

Resultado:

```text
backup.zip
```

Agora:

```bash
find . -iname "*.zip"
```

Resultado:

```text
backup.zip

Backup.zip

BACKUP.ZIP
```

---

# Dica para Pentesters

Durante uma enumeração Linux, normalmente prefiro utilizar:

```bash
-iname
```

quando procuro:

- arquivos `.env`;
- backups;
- arquivos PHP;
- arquivos de configuração;
- bancos de dados;
- scripts.

Isso reduz as chances de deixar passar um arquivo importante apenas porque o desenvolvedor utilizou letras maiúsculas.

---

# Resumo

A opção:

```bash
-iname
```

funciona exatamente como:

```bash
-name
```

A única diferença é que ela ignora letras maiúsculas e minúsculas.

Na próxima parte estudaremos outro conceito extremamente importante:

- `-path`
- `-ipath`
- Como procurar utilizando caminhos completos
- Diferença entre pesquisar pelo nome e pesquisar pelo caminho
- Casos reais de Pentest e CTF

---

# A opção `-path`

Até agora aprendemos a localizar arquivos utilizando seus nomes.

Exemplo:

```bash
find . -name "*.php"
```

Esse comando verifica apenas o **nome do arquivo**.

Mas imagine o seguinte cenário.

```text
/var/www/html/index.php

/var/www/admin/index.php

/opt/site/index.php
```

Todos os arquivos possuem exatamente o mesmo nome:

```text
index.php
```

Como encontrar apenas os arquivos que estão dentro de:

```text
/var/www
```

Para isso utilizamos:

```bash
-path
```

---

# O que é `-path`?

A opção:

```bash
-path
```

permite filtrar utilizando o **caminho completo** (*pathname*).

Enquanto:

```bash
-name
```

analisa apenas o nome do arquivo,

```bash
-path
```

analisa todo o caminho.

---

# Comparando

Imagine esta estrutura.

```text
/

├── home
│   └── allan
│       └── config.php
│
├── var
│   └── www
│       └── config.php
│
└── opt
    └── backup
        └── config.php
```

Agora execute:

```bash
find / -name "config.php"
```

Resultado:

```text
/home/allan/config.php

/var/www/config.php

/opt/backup/config.php
```

Observe que o `find` olhou apenas para o nome.

---

Agora:

```bash
find / -path "/var/www/*"
```

Resultado:

```text
/var/www

/var/www/config.php
```

Agora o caminho completo foi utilizado como filtro.

---

# Como funciona?

Imagine este arquivo.

```text
/var/www/html/admin/login.php
```

Quando utilizamos:

```bash
-path "/var/www/*"
```

o `find` faz aproximadamente esta pergunta.

```text
O caminho começa com:

/var/www/

?

↓

Sim

↓

Mostrar

↓

Não

↓

Ignorar
```

---

# Exemplo simples

```bash
find / -path "/etc/*"
```

Resultado:

```text
/ etc/passwd

/etc/group

/etc/shadow

/etc/hosts
```

Observe que tudo localizado dentro de:

```text
/etc
```

será retornado.

---

# Outro exemplo

```bash
find / -path "*/.git"
```

Resultado:

```text
/home/allan/projeto/.git

/opt/api/.git

/var/www/site/.git
```

Muito utilizado por desenvolvedores.

---

# Procurando diretórios Upload

Imagine vários projetos.

```text
/var/www/site1/uploads

/var/www/site2/uploads

/home/allan/uploads
```

Agora:

```bash
find / -path "*/uploads"
```

Resultado:

```text
/var/www/site1/uploads

/var/www/site2/uploads

/home/allan/uploads
```

---

# Procurando arquivos .env

```bash
find / -path "*/.env"
```

Resultado:

```text
/var/www/site/.env

/home/allan/projeto/.env
```

Muito utilizado em Pentest.

---

# Casos de uso em Pentest

Durante uma enumeração Linux, é comum procurar:

Diretórios Git.

```bash
find / -path "*/.git"
```

---

Arquivos `.env`.

```bash
find / -path "*/.env"
```

---

Diretórios Upload.

```bash
find / -path "*/uploads"
```

---

Diretórios Backup.

```bash
find / -path "*/backup*"
```

---

# Diferença entre `-name` e `-path`

Essa é uma dúvida muito comum.

Imagine novamente.

```text
/var/www/index.php

/home/allan/index.php

/opt/site/index.php
```

---

Com:

```bash
find / -name "index.php"
```

Resultado:

```text
Todos os arquivos chamados index.php.
```

O diretório não importa.

---

Agora:

```bash
find / -path "/var/www/*"
```

Resultado:

```text
Tudo que estiver dentro de /var/www.
```

Mesmo que os arquivos tenham nomes diferentes.

---

# Resumindo

`-name`

↓

Analisa apenas:

```text
Nome do arquivo
```

---

`-path`

↓

Analisa:

```text
Caminho completo
```

---

# A opção `-ipath`

Assim como:

```bash
-name
```

possui:

```bash
-iname
```

A opção:

```bash
-path
```

também possui sua versão que ignora letras maiúsculas e minúsculas.

```bash
-ipath
```

---

# Como funciona?

Imagine.

```text
/VAR/WWW

/Var/WWW

/var/www
```

Agora:

```bash
find / -ipath "/var/www/*"
```

Resultado:

Todos esses caminhos serão encontrados.

---

# Comparando

| Opção | Ignora maiúsculas? |
|---------|-------------------|
| `-path` | ❌ Não |
| `-ipath` | ✅ Sim |

---

# Quando utilizar?

Utilize:

```bash
-path
```

quando souber exatamente o caminho.

---

Utilize:

```bash
-ipath
```

quando não tiver certeza da capitalização.

---

# Casos de uso em Shell Script

Procurar todos os diretórios Git.

```bash
find . -path "*/.git"
```

---

Encontrar ambientes virtuais Python.

```bash
find . -path "*/venv"
```

---

Localizar diretórios de cache.

```bash
find . -path "*/cache"
```

---

# Casos de uso em Pentest

Procurar diretórios administrativos.

```bash
find /var/www -path "*/admin"
```

---

Procurar diretórios Upload.

```bash
find /var/www -path "*/upload*"
```

---

Procurar diretórios Backup.

```bash
find / -path "*/backup*"
```

---

Procurar arquivos `.env`.

```bash
find / -path "*/.env"
```

---

# Erros comuns

## Confundir `-name` com `-path`

Lembre-se.

```bash
-name
```

↓

Analisa somente o nome.

---

```bash
-path
```

↓

Analisa o caminho completo.

---

## Esquecer os Wildcards

Errado:

```bash
find / -path "/var/www"
```

Isso encontrará apenas o próprio diretório:

```text
/var/www
```

Se o objetivo for pesquisar tudo dentro dele, normalmente utilizamos:

```bash
find / -path "/var/www/*"
```

---

# Boas práticas

Sempre pense:

> Quero filtrar pelo nome?

Use:

```bash
-name
```

---

Quero filtrar pela localização?

Use:

```bash
-path
```

---

# Resumo

| Opção | Filtra por |
|---------|------------|
| `-name` | Nome do arquivo |
| `-iname` | Nome (ignora maiúsculas) |
| `-path` | Caminho completo |
| `-ipath` | Caminho (ignora maiúsculas) |

Até aqui você já domina praticamente todas as opções relacionadas à busca por nomes e caminhos no `find`.

Na próxima parte estudaremos uma das opções mais importantes para Linux, Shell Script e Pentest:

- `-perm`
- Como funcionam as permissões no `find`
- Permissões simbólicas e numéricas
- Casos reais de uso
- Busca por arquivos SUID, SGID e Sticky Bit

---

# Permissões no Linux

Antes de estudar a opção:

```bash
-perm
```

precisamos entender como as permissões funcionam no Linux.

Praticamente tudo no sistema possui permissões.

Por exemplo:

- Arquivos;
- Diretórios;
- Scripts;
- Binários;
- Dispositivos.

Essas permissões determinam quem pode:

- Ler;
- Escrever;
- Executar.

---

# Como visualizar as permissões?

O comando mais utilizado é:

```bash
ls -l
```

Exemplo:

```text
-rwxr-xr-- 1 root root 15240 jan 10 12:30 script.sh
```

Vamos analisar apenas esta parte.

```text
-rwxr-xr--
```

Ela representa todas as permissões do arquivo.

---

# Estrutura

Visualmente:

```text
-rwxr-xr--
││││││││││
││││││││└┴── Outros
│││└┴┴┴──── Grupo
│└┴┴┴──────── Dono
└──────────── Tipo
```

Cada posição possui um significado.

---

# O primeiro caractere

O primeiro caractere representa o tipo do objeto.

| Letra | Significado |
|--------|-------------|
| `-` | Arquivo comum |
| `d` | Diretório |
| `l` | Link simbólico |
| `p` | Pipe |
| `s` | Socket |
| `b` | Dispositivo de bloco |
| `c` | Dispositivo de caractere |

Exemplo:

```text
-rwxr-xr--
```

O primeiro caractere é:

```text
-
```

Logo:

É um arquivo comum.

---

Outro exemplo:

```text
drwxr-xr-x
```

Agora o primeiro caractere é:

```text
d
```

Portanto:

É um diretório.

---

# Os próximos nove caracteres

Observe novamente.

```text
-rwxr-xr--
```

Agora ignore o primeiro caractere.

```text
rwxr-xr--
```

Esses nove caracteres representam as permissões.

Eles são divididos em três grupos.

```text
rwx

r-x

r--
```

Cada grupo possui três posições.

---

# Primeiro grupo

```text
rwx
```

Representa o proprietário (**Owner**).

Também chamado de:

```text
User
```

---

# Segundo grupo

```text
r-x
```

Representa o grupo (**Group**).

---

# Terceiro grupo

```text
r--
```

Representa todos os outros usuários (**Others**).

---

Visualmente.

```text
rwx | r-x | r--
 │      │      │
 │      │      └── Outros
 │      └──────── Grupo
 └────────────── Dono
```

---

# O significado de cada letra

Cada posição possui apenas quatro possibilidades.

| Letra | Significado |
|--------|-------------|
| `r` | Read (Leitura) |
| `w` | Write (Escrita) |
| `x` | Execute (Execução) |
| `-` | Permissão ausente |

---

# Read (`r`)

Permite ler o conteúdo do arquivo.

Exemplo.

```text
r--
```

Significa.

Pode ler.

Não pode escrever.

Não pode executar.

---

# Write (`w`)

Permite modificar o arquivo.

Exemplo.

```text
rw-
```

Agora pode:

✔ Ler.

✔ Escrever.

✘ Executar.

---

# Execute (`x`)

Permite executar o arquivo.

Exemplo.

```text
--x
```

O usuário pode executar.

Mas não pode:

- Ler;
- Modificar.

---

# Exemplo completo

```text
-rwxr-xr--
```

Vamos dividir.

```text
rwx
```

Dono.

Pode:

✔ Ler

✔ Escrever

✔ Executar

---

Grupo.

```text
r-x
```

Pode:

✔ Ler

✘ Escrever

✔ Executar

---

Outros.

```text
r--
```

Podem:

✔ Ler

✘ Escrever

✘ Executar

---

# Permissões numéricas

Além da forma simbólica, o Linux também utiliza números.

Cada permissão possui um valor.

| Permissão | Valor |
|-----------|------:|
| `r` | 4 |
| `w` | 2 |
| `x` | 1 |

Esses valores podem ser somados.

---

## Exemplos

```text
rwx
```

Cálculo.

```text
4 + 2 + 1 = 7
```

Resultado.

```text
7
```

---

Outro.

```text
rw-
```

```text
4 + 2 = 6
```

Resultado.

```text
6
```

---

Outro.

```text
r-x
```

```text
4 + 1 = 5
```

---

Outro.

```text
r--
```

```text
4
```

---

Outro.

```text
---
```

```text
0
```

---

# Tabela completa

| Permissão | Número |
|-----------|--------|
| `rwx` | 7 |
| `rw-` | 6 |
| `r-x` | 5 |
| `r--` | 4 |
| `-wx` | 3 |
| `-w-` | 2 |
| `--x` | 1 |
| `---` | 0 |

---

# Exemplo famoso

```text
755
```

Na forma simbólica.

```text
rwxr-xr-x
```

Traduzindo.

Dono:

```text
rwx
```

Grupo.

```text
r-x
```

Outros.

```text
r-x
```

---

Outro exemplo.

```text
644
```

Resultado.

```text
rw-r--r--
```

Muito utilizado em arquivos de configuração.

---

# Como isso se relaciona com o find?

A opção:

```bash
-perm
```

permite pesquisar arquivos utilizando essas permissões.

Por exemplo.

```bash
find / -perm 644
```

Traduzindo.

> Procure arquivos cuja permissão seja exatamente:

```text
rw-r--r--
```

Outro exemplo.

```bash
find / -perm 755
```

↓

```text
rwxr-xr-x
```

---

Mais adiante aprenderemos buscas como:

```bash
find / -perm -4000
```

```bash
find / -perm -2000
```

```bash
find / -perm -1000
```

que são extremamente importantes durante Pentests.

---

# Dica

Antes de decorar:

```bash
find / -perm 755
```

aprenda primeiro a interpretar:

```text
rwxr-xr-x
```

Quando você entende as permissões do Linux, a opção `-perm` passa a fazer muito mais sentido.

---

# Resumo

Até este ponto aprendemos:

- Como visualizar permissões com `ls -l`;
- O significado do primeiro caractere;
- Dono, Grupo e Outros;
- Permissões `r`, `w` e `x`;
- Forma simbólica;
- Forma numérica;
- Como essas permissões serão utilizadas pela opção `-perm`.

Na próxima parte estudaremos finalmente a opção:

- `-perm`
- Diferença entre `644`, `755` e `777`
- Modos exato, parcial e qualquer permissão
- Casos reais de Pentest
- SUID, SGID e Sticky Bit utilizando `find`.

---

# A opção `-perm`

A opção:

```bash
-perm
```

permite localizar arquivos e diretórios com base em suas permissões.

Ela pode trabalhar com:

- Permissões exatas;
- Bits obrigatórios;
- Qualquer bit presente;
- Permissões especiais;
- Forma numérica;
- Forma simbólica.

---

# Sintaxe

```bash
find DIRETORIO -perm MODO
```

Exemplo:

```bash
find . -perm 644
```

Tradução:

> Procure objetos cuja permissão seja exatamente `644`.

---

# Exemplo básico

Imagine estes arquivos:

```text
arquivo1.txt → 644
arquivo2.txt → 600
script.sh    → 755
backup.sh    → 744
```

Comando:

```bash
find . -type f -perm 644
```

Resultado:

```text
./arquivo1.txt
```

Apenas o arquivo com permissão exatamente igual a `644` será exibido.

---

# As três formas principais de usar `-perm`

Existem três formas muito importantes:

```bash
-perm  MODO
-perm -MODO
-perm /MODO
```

Elas possuem significados diferentes.

---

# Correspondência exata

```bash
-perm 644
```

Sem nenhum símbolo antes do número, o `find` procura uma correspondência exata.

Exemplo:

```bash
find . -type f -perm 644
```

Isso encontra apenas arquivos com:

```text
rw-r--r--
```

Não encontra:

```text
600
640
664
755
```

Mesmo que algumas permissões sejam parecidas.

---

## Quando utilizar?

Use quando precisa encontrar um modo específico.

Exemplos:

```bash
find /var/www -type f -perm 644
```

```bash
find /home -type d -perm 700
```

---

# Todos os bits especificados

```bash
-perm -MODO
```

Quando existe um hífen antes do modo, todos os bits informados precisam estar presentes.

O arquivo pode possuir permissões adicionais.

Exemplo:

```bash
find . -type f -perm -600
```

O valor:

```text
600
```

representa:

```text
rw-------
```

O comando procura arquivos em que o proprietário possua:

- Leitura;
- Escrita.

Eles podem possuir outras permissões também.

Por isso, ele pode encontrar:

```text
600
640
644
660
664
700
755
777
```

Desde que os bits de leitura e escrita do proprietário estejam presentes.

---

# Como pensar em `-perm -MODO`

Pense assim:

> O arquivo precisa possuir pelo menos todos esses bits.

Exemplo:

```bash
-perm -400
```

O bit `400` representa leitura do proprietário.

O comando encontra arquivos em que o dono consegue ler.

---

Outro exemplo:

```bash
-perm -200
```

Encontra arquivos em que o dono possui permissão de escrita.

---

Outro exemplo:

```bash
-perm -100
```

Encontra arquivos executáveis pelo proprietário.

---

# Qualquer bit especificado

```bash
-perm /MODO
```

Com uma barra antes do modo, basta que pelo menos um dos bits informados esteja presente.

Exemplo:

```bash
find . -type f -perm /600
```

O modo `600` contém:

- `400` → leitura do proprietário;
- `200` → escrita do proprietário.

O arquivo será encontrado se possuir pelo menos um desses bits.

Assim, podem aparecer arquivos com:

```text
400
200
600
644
755
777
```

Desde que possuam leitura ou escrita do proprietário.

---

# Comparando as três formas

| Forma | Significado |
|---|---|
| `-perm 644` | Permissão exatamente igual a `644` |
| `-perm -644` | Todos os bits de `644` precisam existir |
| `-perm /644` | Pelo menos um dos bits de `644` precisa existir |

---

# Exemplo visual

Imagine os arquivos:

```text
a.txt → 644
b.txt → 600
c.txt → 444
d.txt → 666
e.txt → 755
```

## Comando exato

```bash
find . -type f -perm 644
```

Resultado:

```text
a.txt
```

---

## Todos os bits

```bash
find . -type f -perm -600
```

Resultado possível:

```text
a.txt
b.txt
d.txt
e.txt
```

Todos possuem leitura e escrita do proprietário.

---

## Qualquer bit

```bash
find . -type f -perm /600
```

Resultado possível:

```text
a.txt
b.txt
c.txt
d.txt
e.txt
```

Basta possuir leitura ou escrita do proprietário.

---

# Permissões simbólicas

O `find` também aceita modos simbólicos em algumas implementações.

Exemplo:

```bash
find . -perm u=rwx
```

Significa:

```text
O proprietário possui exatamente leitura, escrita e execução.
```

Outro exemplo:

```bash
find . -perm -u=x
```

Significa:

```text
O bit de execução do proprietário precisa estar presente.
```

Entretanto, em scripts e anotações de Pentest, a forma octal costuma ser mais comum e mais fácil de comparar visualmente.

---

# Procurar arquivos executáveis

## Executáveis pelo proprietário

```bash
find . -type f -perm -100
```

---

## Executáveis pelo grupo

```bash
find . -type f -perm -010
```

---

## Executáveis por outros usuários

```bash
find . -type f -perm -001
```

---

## Executáveis por qualquer categoria

```bash
find . -type f -perm /111
```

O modo:

```text
111
```

representa os três bits de execução.

Com `/111`, basta que o arquivo seja executável pelo:

- Proprietário;
- Grupo;
- Ou outros.

---

# Procurar arquivos graváveis

## Graváveis pelo proprietário

```bash
find . -type f -perm -200
```

---

## Graváveis pelo grupo

```bash
find . -type f -perm -020
```

---

## Graváveis por outros

```bash
find . -type f -perm -002
```

Essa última busca é especialmente importante durante enumerações.

---

# Arquivos world-writable

Arquivos graváveis por qualquer usuário possuem o bit:

```text
002
```

Exemplo:

```bash
find / -type f -perm -002 2>/dev/null
```

Tradução:

> Procure arquivos comuns que possam ser modificados por outros usuários.

Esses arquivos podem ser perigosos quando:

- São executados pelo `root`;
- São utilizados por serviços;
- São scripts de backup;
- São arquivos de configuração;
- Estão em diretórios importantes.

---

# Diretórios world-writable

```bash
find / -type d -perm -002 2>/dev/null
```

Isso encontra diretórios nos quais outros usuários podem escrever.

Exemplos esperados:

```text
/tmp
/var/tmp
/dev/shm
```

Esses diretórios normalmente possuem proteção adicional com Sticky Bit.

---

# Permissões especiais

Além de `r`, `w` e `x`, o Linux possui três bits especiais.

| Valor | Nome |
|---:|---|
| `4000` | SUID |
| `2000` | SGID |
| `1000` | Sticky Bit |

Esses valores ocupam a primeira posição em modos de quatro dígitos.

Exemplo:

```text
4755
```

Separando:

```text
4 → SUID
755 → permissões normais
```

---

# Procurar SUID

```bash
find / -type f -perm -4000 2>/dev/null
```

Tradução:

> Procure arquivos comuns que possuam o bit SUID.

Esse é um dos comandos mais usados em Linux Post Exploitation.

Exemplo de saída:

```text
/usr/bin/passwd
/usr/bin/su
/usr/local/bin/backup
```

Nem todo resultado é vulnerável.

O objetivo é identificar binários incomuns, personalizados ou exploráveis.

---

# Por que usamos `-4000` e não `4000`?

Com:

```bash
-perm 4000
```

a permissão precisaria ser exatamente:

```text
4000
```

Isso raramente acontece.

Um binário SUID normalmente possui algo como:

```text
4755
```

Por isso usamos:

```bash
-perm -4000
```

O hífen significa:

> O bit SUID precisa estar presente, mas outros bits podem existir.

---

# Procurar SGID

```bash
find / -type f -perm -2000 2>/dev/null
```

O SGID faz com que o processo seja executado com o grupo efetivo do arquivo.

Exemplo:

```text
-rwxr-sr-x
```

---

# Procurar Sticky Bit

```bash
find / -type d -perm -1000 2>/dev/null
```

O Sticky Bit costuma aparecer em diretórios compartilhados.

Exemplo clássico:

```text
/tmp
```

Mesmo que vários usuários possam criar arquivos, cada um normalmente só consegue remover os próprios arquivos.

---

# Procurar SUID ou SGID

```bash
find / -type f -perm /6000 2>/dev/null
```

Porque:

```text
4000 → SUID
2000 → SGID
6000 → os dois bits combinados
```

Como usamos:

```bash
/6000
```

basta possuir SUID ou SGID.

---

# Procurar SUID e SGID ao mesmo tempo

```bash
find / -type f -perm -6000 2>/dev/null
```

Nesse caso, os dois bits precisam estar presentes.

Isso é bem menos comum.

---

# Procurar arquivos com permissão `777`

```bash
find / -type f -perm 777 2>/dev/null
```

Isso procura correspondência exata.

Para encontrar arquivos em que todos os usuários possuem leitura, escrita e execução, mas que podem possuir bits especiais adicionais:

```bash
find / -type f -perm -0777 2>/dev/null
```

---

# O zero inicial

Às vezes você verá:

```bash
-perm -04000
```

ou:

```bash
-perm -4000
```

Na prática, ambos representam o modo octal relacionado ao SUID.

Também é comum escrever permissões comuns assim:

```text
0644
0755
0777
```

O zero inicial ajuda a deixar claro que o valor está sendo interpretado como octal.

---

# Casos de uso em Administração Linux

## Encontrar arquivos com permissão exata `644`

```bash
find /var/www -type f -perm 644
```

---

## Encontrar diretórios com permissão exata `755`

```bash
find /var/www -type d -perm 755
```

---

## Encontrar arquivos graváveis por outros

```bash
find /srv -type f -perm -002
```

---

# Casos de uso em Shell Script

## Verificar scripts executáveis

```bash
find ./scripts -type f -perm /111
```

---

## Encontrar arquivos com escrita para o grupo

```bash
find ./projeto -type f -perm -020
```

---

# Casos de uso em Pentest e CTF

## Enumerar SUID

```bash
find / -type f -perm -4000 2>/dev/null
```

---

## Enumerar SGID

```bash
find / -type f -perm -2000 2>/dev/null
```

---

## Procurar arquivos world-writable

```bash
find / -type f -perm -002 2>/dev/null
```

---

## Procurar diretórios world-writable sem Sticky Bit

```bash
find / -type d -perm -002 ! -perm -1000 2>/dev/null
```

Tradução:

> Procure diretórios graváveis por outros usuários que não possuam Sticky Bit.

Esses diretórios podem representar uma configuração insegura.

---

# Diferença entre `-writable` e `-perm`

O `find` também possui:

```bash
-writable
```

Ela verifica se o usuário atual consegue escrever no objeto.

Já:

```bash
-perm
```

verifica os bits de permissão informados.

Exemplo:

```bash
find / -type f -writable 2>/dev/null
```

Pergunta:

> O usuário atual consegue escrever nesse arquivo?

Enquanto:

```bash
find / -type f -perm -002 2>/dev/null
```

Pergunta:

> O arquivo possui escrita habilitada para outros usuários?

Essas duas buscas não são exatamente iguais, porque ACLs, proprietário, grupo e outras regras podem afetar o acesso real.

---

# Erros comuns

## Usar modo exato sem querer

```bash
find / -perm 4000
```

Normalmente não encontra os binários SUID esperados.

Prefira:

```bash
find / -perm -4000
```

---

## Confundir hífen com opção

Em:

```bash
-perm -4000
```

o primeiro hífen pertence a:

```text
-perm
```

O segundo hífen define o tipo de comparação do modo.

---

## Achar que todo arquivo SUID é vulnerável

Programas como:

```text
passwd
su
mount
umount
```

podem aparecer legitimamente.

É necessário analisar:

- Caminho;
- Proprietário;
- Versão;
- Funções;
- Presença no GTFOBins;
- Se o programa preserva privilégios.

---

## Confundir `777` com SUID

```text
777
```

representa permissões comuns completas.

```text
4777
```

representa:

- SUID;
- Permissões comuns `777`.

O primeiro dígito possui significado especial.

---

# Tabela de consulta rápida

| Objetivo | Comando |
|---|---|
| Permissão exatamente `644` | `find . -perm 644` |
| Todos os bits de `600` | `find . -perm -600` |
| Qualquer bit de `600` | `find . -perm /600` |
| Executável por alguém | `find . -type f -perm /111` |
| Gravável por outros | `find / -type f -perm -002 2>/dev/null` |
| SUID | `find / -type f -perm -4000 2>/dev/null` |
| SGID | `find / -type f -perm -2000 2>/dev/null` |
| Sticky Bit | `find / -type d -perm -1000 2>/dev/null` |
| SUID ou SGID | `find / -type f -perm /6000 2>/dev/null` |

---

# Resumo

A opção:

```bash
-perm
```

filtra objetos pelos bits de permissão.

As três formas principais são:

```text
MODO  → correspondência exata
-MODO → todos os bits precisam existir
/MODO → qualquer bit pode existir
```

Durante Pentest e CTF, os usos mais importantes são:

```bash
find / -type f -perm -4000 2>/dev/null
```

```bash
find / -type f -perm -2000 2>/dev/null
```

```bash
find / -type f -perm -002 2>/dev/null
```

Na próxima parte estudaremos:

- `-user`;
- `-group`;
- `-uid`;
- `-gid`;
- `-nouser`;
- `-nogroup`;
- Como localizar arquivos por proprietário e grupo.

---

# Procurando por proprietário e grupo

Além de procurar arquivos por nome, tipo ou permissões, o `find` também pode filtrar os resultados com base em:

- Proprietário;
- Grupo;
- UID;
- GID;
- Arquivos sem usuário válido;
- Arquivos sem grupo válido.

Essas buscas são úteis em:

- Administração Linux;
- Auditoria de permissões;
- Investigação de arquivos abandonados;
- Pentest;
- CTF;
- Linux Post Exploitation.

---

# Proprietário de um arquivo

Todo arquivo no Linux possui um proprietário.

Para visualizar:

```bash
ls -l arquivo.txt
```

Exemplo:

```text
-rw-r--r-- 1 allan desenvolvedores 1200 jul 31 10:30 arquivo.txt
```

Separando:

```text
allan            → proprietário
desenvolvedores  → grupo
```

O proprietário geralmente é o usuário que criou o arquivo, embora isso possa ser alterado com comandos como:

```bash
chown
```

---

# A opção `-user`

A opção:

```bash
-user
```

procura arquivos pertencentes a um determinado usuário.

## Sintaxe

```bash
find DIRETORIO -user USUARIO
```

## Exemplo

```bash
find /home -user allan
```

Tradução:

> Procure dentro de `/home` todos os objetos pertencentes ao usuário `allan`.

Possível saída:

```text
/home/allan
/home/allan/documento.txt
/home/allan/script.sh
/home/allan/.bashrc
```

---

# Procurar apenas arquivos de um usuário

```bash
find /home -type f -user allan
```

Agora o `find` retorna apenas arquivos comuns pertencentes a `allan`.

---

# Procurar diretórios de um usuário

```bash
find /home -type d -user allan
```

---

# Procurar arquivos pertencentes ao root

```bash
find / -type f -user root 2>/dev/null
```

Esse comando pode retornar milhares de arquivos.

Por isso, normalmente combinamos com outros filtros.

Exemplo:

```bash
find /usr/local/bin -type f -user root 2>/dev/null
```

---

# Uso em Pentest

Durante uma enumeração, pode ser útil procurar arquivos que:

- Pertençam ao `root`;
- Estejam em diretórios personalizados;
- Possam ser executados;
- Possam ser modificados pelo usuário atual.

Exemplo:

```bash
find /opt -type f -user root 2>/dev/null
```

Perguntas:

- O arquivo pertence ao root?
- É um script?
- É executado automaticamente?
- Posso modificá-lo?
- Ele chama outros comandos?

---

# Combinar `-user` com `-writable`

```bash
find / -type f -user root -writable 2>/dev/null
```

Tradução:

> Procure arquivos comuns pertencentes ao root que o usuário atual consegue modificar.

Esse resultado merece investigação porque pode indicar:

- Script executado por root;
- Configuração insegura;
- Arquivo de serviço;
- Arquivo de backup;
- Programa personalizado.

> [!important]
> Um arquivo pertencer ao root e ser gravável não garante escalação de privilégios. É necessário descobrir como ele é utilizado.

---

# A opção `-group`

A opção:

```bash
-group
```

procura arquivos pertencentes a um grupo específico.

## Sintaxe

```bash
find DIRETORIO -group GRUPO
```

## Exemplo

```bash
find /var/www -group www-data
```

Tradução:

> Procure dentro de `/var/www` objetos cujo grupo seja `www-data`.

---

# Procurar arquivos de um grupo

```bash
find /var/www -type f -group www-data
```

---

# Procurar diretórios de um grupo

```bash
find /var/www -type d -group www-data
```

---

# Uso em Pentest

Grupos podem conceder acesso a recursos específicos.

Exemplos de grupos relevantes:

```text
docker
lxd
adm
disk
shadow
www-data
backup
developers
```

Exemplo:

```bash
find / -group developers 2>/dev/null
```

Isso pode revelar arquivos compartilhados entre membros daquele grupo.

---

# Combinar proprietário e grupo

```bash
find /var/www -type f -user www-data -group www-data
```

Tradução:

> Procure arquivos comuns pertencentes ao usuário `www-data` e ao grupo `www-data`.

Como as expressões estão lado a lado, o `find` aplica um `AND` implícito.

Internamente:

```text
-type f
AND
-user www-data
AND
-group www-data
```

---

# UID e GID

Além dos nomes dos usuários e grupos, o Linux utiliza identificadores numéricos.

## UID

```text
User ID
```

Identifica um usuário.

## GID

```text
Group ID
```

Identifica um grupo.

Para visualizar:

```bash
id
```

Exemplo:

```text
uid=1000(allan) gid=1000(allan) groups=1000(allan),27(sudo)
```

---

# A opção `-uid`

A opção:

```bash
-uid
```

procura arquivos pelo UID numérico.

## Exemplo

```bash
find /home -uid 1000
```

Isso encontra objetos pertencentes ao usuário cujo UID é `1000`.

---

# Quando utilizar `-uid`?

É útil quando:

- O nome do usuário não aparece;
- O arquivo pertence a um UID sem conta correspondente;
- Você está analisando sistemas de arquivos copiados;
- Está investigando containers;
- Está analisando um backup de outro sistema.

---

# A opção `-gid`

A opção:

```bash
-gid
```

procura arquivos pelo GID numérico.

## Exemplo

```bash
find /var/www -gid 33
```

Em algumas distribuições, o grupo `www-data` pode utilizar o GID `33`.

> [!note]
> O valor numérico depende do sistema. Sempre confirme com `id`, `getent passwd` ou `getent group`.

---

# Descobrir o UID de um usuário

```bash
id -u allan
```

Saída:

```text
1000
```

---

# Descobrir o GID principal

```bash
id -g allan
```

Saída:

```text
1000
```

---

# Consultar usuários e grupos

## Usuários

```bash
getent passwd
```

Usuário específico:

```bash
getent passwd allan
```

## Grupos

```bash
getent group
```

Grupo específico:

```bash
getent group developers
```

---

# A opção `-nouser`

A opção:

```bash
-nouser
```

encontra arquivos cujo UID não corresponde a nenhum usuário atualmente conhecido pelo sistema.

## Exemplo

```bash
find / -nouser 2>/dev/null
```

---

# Por que isso acontece?

Um arquivo pode ter sido criado por um usuário que depois foi removido.

Exemplo:

```text
Arquivo criado pelo UID 1002
        ↓
Usuário removido
        ↓
Arquivo continua existindo
        ↓
UID 1002 não possui nome associado
```

Nesse caso, `ls -l` pode mostrar um número no lugar do nome:

```text
-rw-r--r-- 1 1002 1002 500 jul 31 arquivo.txt
```

---

# Quando `-nouser` é útil?

Em:

- Auditorias;
- Migrações;
- Backups restaurados;
- Containers;
- Sistemas antigos;
- Investigação forense;
- Limpeza administrativa.

Durante um Pentest, arquivos sem proprietário válido podem indicar:

- Aplicações removidas incorretamente;
- Dados antigos;
- Backups esquecidos;
- Scripts abandonados;
- Configurações históricas.

---

# A opção `-nogroup`

A opção:

```bash
-nogroup
```

encontra arquivos cujo GID não corresponde a nenhum grupo conhecido.

## Exemplo

```bash
find / -nogroup 2>/dev/null
```

---

# Procurar arquivos sem usuário ou grupo

```bash
find / \( -nouser -o -nogroup \) 2>/dev/null
```

Tradução:

> Procure objetos sem usuário válido ou sem grupo válido.

Os parênteses serão explicados em detalhes na parte de operadores.

---

# Procurar apenas arquivos comuns sem proprietário válido

```bash
find / -type f -nouser 2>/dev/null
```

---

# Procurar somente em diretórios importantes

Em vez de pesquisar todo o sistema:

```bash
find / -nouser 2>/dev/null
```

pode ser melhor começar por:

```bash
find /home -nouser 2>/dev/null
```

```bash
find /opt -nouser 2>/dev/null
```

```bash
find /var/www -nouser 2>/dev/null
```

```bash
find /usr/local -nouser 2>/dev/null
```

Isso reduz ruído e acelera a análise.

---

# Arquivos pertencentes a outro usuário

Durante um CTF, pode ser útil procurar arquivos pertencentes a um usuário específico.

Exemplo:

```bash
find / -type f -user john 2>/dev/null
```

Possíveis descobertas:

```text
/home/john/.ssh/id_rsa
/var/backups/john.zip
/opt/scripts/john-backup.sh
```

Nem todos serão legíveis, mas os caminhos já podem revelar informações úteis.

---

# Arquivos do root em diretórios personalizados

```bash
find /opt /usr/local/bin -type f -user root 2>/dev/null
```

Esse comando inicia a busca em dois locais:

```text
/opt
/usr/local/bin
```

e procura arquivos pertencentes ao root.

Esses diretórios são interessantes porque geralmente contêm:

- Aplicações personalizadas;
- Scripts administrativos;
- Ferramentas instaladas manualmente;
- Binários não pertencentes ao sistema-base.

---

# Múltiplos diretórios iniciais

O `find` aceita mais de um diretório inicial.

Exemplo:

```bash
find /opt /usr/local/bin /var/backups -type f -user root 2>/dev/null
```

Ele pesquisará nos três locais.

Essa forma pode ser melhor do que executar três comandos separados.

---

# Procurar arquivos do root graváveis pelo grupo

```bash
find / -type f -user root -perm -020 2>/dev/null
```

Tradução:

> Procure arquivos comuns pertencentes ao root com escrita habilitada para o grupo.

Depois, verifique se o usuário atual pertence a esse grupo:

```bash
id
```

---

# Procurar arquivos do root graváveis por outros

```bash
find / -type f -user root -perm -002 2>/dev/null
```

Esses resultados merecem prioridade, especialmente quando forem:

- Scripts;
- Configurações;
- Arquivos executados por serviços;
- Arquivos localizados em `/opt`;
- Arquivos localizados em `/usr/local/bin`.

---

# Procurar arquivos pertencentes ao grupo atual

Primeiro descubra o grupo:

```bash
id -gn
```

Exemplo de saída:

```text
allan
```

Depois:

```bash
find / -group allan 2>/dev/null
```

Em Shell Script:

```bash
find / -group "$(id -gn)" 2>/dev/null
```

---

# Exemplo em Shell Script

```bash
#!/usr/bin/env bash

usuario_atual=$(id -un)
grupo_atual=$(id -gn)

printf 'Arquivos pertencentes ao usuário %s:\n' "$usuario_atual"
find "$HOME" -type f -user "$usuario_atual"

printf '\nArquivos pertencentes ao grupo %s:\n' "$grupo_atual"
find "$HOME" -type f -group "$grupo_atual"
```

---

# Como interpretar os resultados

Ao encontrar um arquivo, não analise apenas o proprietário.

Pergunte:

1. Onde o arquivo está?
2. Qual é seu grupo?
3. Quais são suas permissões?
4. É legível?
5. É gravável?
6. É executável?
7. Quem utiliza esse arquivo?
8. É chamado por algum serviço?
9. É chamado por um Cron Job?
10. É um arquivo personalizado?

Use:

```bash
ls -la /caminho/do/arquivo
```

```bash
stat /caminho/do/arquivo
```

```bash
file /caminho/do/arquivo
```

---

# Diferença entre `-user` e `-uid`

| Opção | Pesquisa por |
|---|---|
| `-user allan` | Nome do usuário |
| `-uid 1000` | Identificador numérico |

---

# Diferença entre `-group` e `-gid`

| Opção | Pesquisa por |
|---|---|
| `-group developers` | Nome do grupo |
| `-gid 1001` | Identificador numérico |

---

# Erros comuns

## Presumir que o UID `1000` sempre é o mesmo usuário

O UID `1000` frequentemente pertence ao primeiro usuário comum, mas isso não é uma regra universal.

Confirme:

```bash
getent passwd 1000
```

---

## Achar que arquivo do root é automaticamente perigoso

Grande parte do sistema pertence ao root.

O que merece atenção é a combinação:

```text
pertence ao root
+
usuário consegue modificar
+
é utilizado por processo privilegiado
```

---

## Pesquisar todo o sistema sem necessidade

Este comando:

```bash
find / -user root 2>/dev/null
```

gera muitos resultados.

Prefira combinar critérios:

```bash
find /opt -type f -user root -writable 2>/dev/null
```

---

## Ignorar grupos

Às vezes o usuário não é proprietário, mas pertence ao grupo do arquivo.

Verifique:

```bash
id
```

e compare com:

```bash
ls -l arquivo
```

---

# Mini cheatsheet

| Objetivo | Comando |
|---|---|
| Arquivos de um usuário | `find /home -type f -user allan` |
| Arquivos de um grupo | `find /var/www -type f -group www-data` |
| Buscar pelo UID | `find /home -uid 1000` |
| Buscar pelo GID | `find /var/www -gid 33` |
| Sem usuário válido | `find / -nouser 2>/dev/null` |
| Sem grupo válido | `find / -nogroup 2>/dev/null` |
| Arquivos do root graváveis | `find / -type f -user root -writable 2>/dev/null` |
| Root e escrita para outros | `find / -type f -user root -perm -002 2>/dev/null` |
| Root em diretórios personalizados | `find /opt /usr/local/bin -type f -user root 2>/dev/null` |

---

# Resumo

As opções relacionadas à propriedade são:

```text
-user     → nome do proprietário
-group    → nome do grupo
-uid      → UID numérico
-gid      → GID numérico
-nouser   → UID sem usuário correspondente
-nogroup  → GID sem grupo correspondente
```

Durante uma enumeração, não basta encontrar um arquivo pertencente ao root.

O cenário realmente interessante costuma ser:

```text
arquivo privilegiado
+
permissão inadequada
+
uso por processo ou tarefa privilegiada
```

Na próxima parte estudaremos os três tempos principais dos arquivos:

- `mtime`;
- `ctime`;
- `atime`;
- `mmin`, `cmin` e `amin`;
- `-newer`;
- Diferença entre modificação do conteúdo, alteração dos metadados e acesso.

---

# Procurando arquivos por data e tempo

O `find` também permite localizar arquivos com base em quando eles foram:

- Modificados;
- Acessados;
- Alterados nos metadados;
- Criados antes ou depois de outro arquivo;
- Modificados há determinados dias ou minutos.

Esses filtros são úteis em:

- Administração Linux;
- Limpeza de arquivos antigos;
- Shell Script;
- Investigação forense;
- Análise de incidentes;
- Pentest;
- CTF;
- Busca por arquivos criados ou modificados recentemente.

---

# Os tempos principais de um arquivo

No Linux, um arquivo normalmente possui três tempos importantes:

| Tempo | Nome | O que representa |
|---|---|---|
| `mtime` | Modification Time | Última alteração no conteúdo |
| `ctime` | Change Time | Última alteração nos metadados ou conteúdo |
| `atime` | Access Time | Último acesso ao conteúdo |

Esses tempos costumam causar confusão porque `mtime` e `ctime` parecem representar a mesma coisa.

Eles não são iguais.

---

# Visualizando os tempos

O comando:

```bash
stat arquivo.txt
```

mostra informações detalhadas sobre um arquivo.

Exemplo:

```text
Access: 2026-07-31 10:20:15.000000000
Modify: 2026-07-30 18:45:10.000000000
Change: 2026-07-30 19:02:44.000000000
 Birth: 2026-07-20 14:10:00.000000000
```

Separando:

```text
Access → atime
Modify → mtime
Change → ctime
Birth  → data de criação, quando suportada
```

> [!note]
> Nem todos os sistemas de arquivos armazenam ou disponibilizam a data de criação da mesma forma.

---

# `mtime` — Modification Time

O `mtime` representa a última vez em que o **conteúdo** do arquivo foi modificado.

Exemplo:

```bash
echo "Nova linha" >> arquivo.txt
```

Esse comando altera o conteúdo do arquivo.

Portanto, o `mtime` será atualizado.

---

## Operações que normalmente alteram o `mtime`

- Editar o conteúdo;
- Acrescentar dados;
- Remover dados;
- Sobrescrever o arquivo;
- Salvar o arquivo em um editor.

Exemplo:

```bash
printf "teste\n" > arquivo.txt
```

O conteúdo mudou.

Logo:

```text
mtime atualizado
```

---

# A opção `-mtime`

A opção:

```bash
-mtime
```

procura arquivos com base no tempo de modificação do conteúdo, medido em períodos de 24 horas.

## Sintaxe

```bash
find DIRETORIO -mtime VALOR
```

---

# Entendendo os valores

As três formas principais são:

```text
-mtime N
-mtime -N
-mtime +N
```

Cada uma possui um significado diferente.

---

# `-mtime N`

```bash
find . -mtime 2
```

Procura arquivos modificados em uma faixa correspondente a aproximadamente dois períodos completos de 24 horas atrás.

O `find` trabalha com intervalos arredondados de 24 horas.

Por isso, não é correto interpretar simplesmente como:

> Exatamente há dois dias no relógio.

---

# `-mtime -N`

```bash
find . -mtime -2
```

Procura arquivos modificados há menos de dois períodos completos de 24 horas.

Na prática, é utilizado para localizar arquivos recentes.

Exemplo:

```bash
find /var/www -type f -mtime -1
```

Tradução aproximada:

> Procure arquivos modificados nas últimas 24 horas.

---

# `-mtime +N`

```bash
find . -mtime +30
```

Procura arquivos modificados há mais de 30 períodos completos de 24 horas.

É muito utilizado para localizar arquivos antigos.

Exemplo:

```bash
find /var/log -type f -mtime +30
```

Tradução:

> Procure arquivos de log modificados há mais de 30 dias.

---

# Comparando

| Expressão | Significado aproximado |
|---|---|
| `-mtime 7` | Modificados na faixa correspondente a 7 dias |
| `-mtime -7` | Modificados há menos de 7 dias |
| `-mtime +7` | Modificados há mais de 7 dias |

---

# Exemplo prático

Imagine:

```text
arquivo1.txt → modificado hoje
arquivo2.txt → modificado há 3 dias
arquivo3.txt → modificado há 15 dias
arquivo4.txt → modificado há 60 dias
```

Comando:

```bash
find . -type f -mtime -7
```

Resultado esperado:

```text
./arquivo1.txt
./arquivo2.txt
```

---

Agora:

```bash
find . -type f -mtime +30
```

Resultado:

```text
./arquivo4.txt
```

---

# `ctime` — Change Time

O `ctime` representa a última alteração no **estado do inode** ou nos metadados do arquivo.

Pode ser atualizado quando ocorrem alterações como:

- Permissões;
- Proprietário;
- Grupo;
- Quantidade de links;
- Conteúdo;
- Outros metadados.

> [!important]
> `ctime` não significa Creation Time.
>
> Ele significa Change Time.

Essa é uma confusão muito comum.

---

# Exemplo alterando o `ctime`

```bash
chmod 600 arquivo.txt
```

O conteúdo do arquivo não foi alterado.

Porém, suas permissões mudaram.

Resultado:

```text
mtime → pode permanecer igual
ctime → atualizado
```

---

Outro exemplo:

```bash
chown root:root arquivo.txt
```

O proprietário e o grupo foram alterados.

Logo:

```text
ctime atualizado
```

---

# A opção `-ctime`

A opção:

```bash
-ctime
```

procura arquivos com base na última alteração dos metadados ou conteúdo.

## Exemplos

Arquivos alterados recentemente:

```bash
find . -type f -ctime -1
```

Arquivos cujo estado foi alterado há mais de 30 dias:

```bash
find . -type f -ctime +30
```

---

# Quando `ctime` é útil?

É especialmente útil em:

- Auditoria;
- Investigação forense;
- Análise de permissões;
- Identificação de mudanças recentes;
- Busca por arquivos que tiveram proprietário ou grupo alterados;
- Investigação de incidentes.

Durante um CTF, pode ajudar a encontrar arquivos cujas permissões foram modificadas recentemente.

---

# `atime` — Access Time

O `atime` representa a última vez em que o conteúdo do arquivo foi acessado.

Exemplo:

```bash
cat arquivo.txt
```

Esse comando lê o conteúdo.

Dependendo das opções de montagem do sistema de arquivos, o `atime` pode ser atualizado.

---

# A opção `-atime`

A opção:

```bash
-atime
```

procura arquivos com base no último acesso.

## Exemplos

Arquivos acessados recentemente:

```bash
find . -type f -atime -1
```

Arquivos não acessados há mais de 90 dias:

```bash
find . -type f -atime +90
```

---

# Atenção ao `atime`

Muitos sistemas Linux utilizam opções como:

```text
relatime
```

ou:

```text
noatime
```

Essas opções reduzem ou desativam atualizações frequentes do `atime`.

Isso melhora o desempenho do sistema.

Consequentemente, o `atime` pode não ser atualizado em toda leitura.

Para verificar as opções de montagem:

```bash
mount
```

ou:

```bash
findmnt
```

> [!note]
> Não confie no `atime` como uma prova absoluta de que um arquivo foi ou não foi acessado.

---

# Comparando `mtime`, `ctime` e `atime`

| Ação | `mtime` | `ctime` | `atime` |
|---|:---:|:---:|:---:|
| Ler o arquivo | Não | Não | Pode atualizar |
| Alterar conteúdo | Sim | Sim | Não necessariamente |
| Alterar permissões | Não | Sim | Não |
| Alterar proprietário | Não | Sim | Não |
| Alterar grupo | Não | Sim | Não |
| Renomear arquivo | Geralmente não | Sim | Não |

---

# Exemplo completo

Arquivo inicial:

```text
arquivo.txt
```

## Ler o arquivo

```bash
cat arquivo.txt
```

Possível resultado:

```text
atime atualizado
mtime igual
ctime igual
```

---

## Alterar o conteúdo

```bash
echo "teste" >> arquivo.txt
```

Resultado:

```text
mtime atualizado
ctime atualizado
```

---

## Alterar permissões

```bash
chmod 600 arquivo.txt
```

Resultado:

```text
ctime atualizado
mtime igual
```

---

# Filtros em minutos

Além das versões medidas em dias, o `find` possui versões medidas em minutos:

| Opção | Tempo analisado |
|---|---|
| `-mmin` | Modificação do conteúdo em minutos |
| `-cmin` | Alteração dos metadados em minutos |
| `-amin` | Último acesso em minutos |

Essas opções são úteis para buscas mais precisas.

---

# A opção `-mmin`

Procura arquivos de acordo com o tempo de modificação, em minutos.

## Modificados nos últimos 30 minutos

```bash
find . -type f -mmin -30
```

## Modificados há mais de 60 minutos

```bash
find . -type f -mmin +60
```

## Modificados na faixa de 10 minutos

```bash
find . -type f -mmin 10
```

---

# A opção `-cmin`

Procura arquivos cujos metadados ou conteúdo foram alterados recentemente.

## Exemplo

```bash
find /etc -type f -cmin -60 2>/dev/null
```

Tradução:

> Procure arquivos em `/etc` cujo estado foi alterado nos últimos 60 minutos.

Isso pode ajudar a identificar:

- Permissões alteradas;
- Proprietários alterados;
- Configurações modificadas;
- Arquivos substituídos.

---

# A opção `-amin`

Procura arquivos pelo último acesso, medido em minutos.

## Exemplo

```bash
find /home -type f -amin -15 2>/dev/null
```

Tradução:

> Procure arquivos acessados nos últimos 15 minutos.

Novamente, o resultado depende de como o sistema de arquivos trata o `atime`.

---

# Dias versus minutos

| Dias | Minutos |
|---|---|
| `-mtime` | `-mmin` |
| `-ctime` | `-cmin` |
| `-atime` | `-amin` |

Use as opções em dias para buscas amplas.

Use as opções em minutos para análises recentes e mais específicas.

---

# A opção `-daystart`

Por padrão, `-mtime`, `-ctime` e `-atime` calculam o tempo com base no momento atual e em períodos completos de 24 horas.

A opção:

```bash
-daystart
```

faz o cálculo começar no início do dia atual.

## Exemplo

```bash
find . -daystart -mtime 0
```

Isso procura arquivos modificados desde o início do dia atual, de acordo com a forma como o `find` calcula os intervalos.

> [!note]
> A posição de `-daystart` pode afetar expressões de tempo que aparecem depois dela. Coloque-a antes dos testes de tempo relacionados.

---

# A opção `-newer`

A opção:

```bash
-newer
```

compara a data de modificação de um arquivo com outro arquivo de referência.

## Sintaxe

```bash
find DIRETORIO -newer ARQUIVO_REFERENCIA
```

---

# Exemplo

```bash
touch referencia.txt
```

Depois:

```bash
find . -type f -newer referencia.txt
```

O resultado mostrará arquivos modificados depois de:

```text
referencia.txt
```

---

# Quando `-newer` é útil?

É útil quando queremos responder:

> Quais arquivos mudaram depois de determinado momento?

Por exemplo, podemos criar um marcador antes de iniciar uma instalação:

```bash
touch antes-da-instalacao
```

Executar a instalação.

Depois:

```bash
find /opt -type f -newer antes-da-instalacao 2>/dev/null
```

Isso ajuda a identificar arquivos modificados ou criados depois do marcador.

---

# Criando um arquivo de referência com data específica

O comando `touch` pode criar um arquivo com uma data escolhida.

Exemplo:

```bash
touch -d "2026-07-01 00:00:00" referencia
```

Depois:

```bash
find /var/log -type f -newer referencia
```

Isso procura arquivos modificados depois de 1º de julho de 2026.

---

# Intervalo entre duas datas

Podemos usar dois arquivos de referência.

Primeiro:

```bash
touch -d "2026-07-01" inicio
touch -d "2026-07-15" fim
```

Depois:

```bash
find . -type f -newer inicio ! -newer fim
```

Tradução:

> Procure arquivos modificados depois de `inicio`, mas não depois de `fim`.

Esse tipo de busca é útil em:

- Análise de incidentes;
- Forense;
- Auditoria;
- Investigação de alterações.

---

# Variações de `-newer`

Em implementações GNU do `find`, também podem existir comparações mais específicas usando:

```bash
-newerXY
```

Onde `X` e `Y` determinam quais tempos serão comparados.

Exemplos possíveis:

```text
a → atime
B → birth time, quando suportado
c → ctime
m → mtime
t → data literal
```

Exemplo:

```bash
find . -newermt "2026-07-01"
```

Essa forma compara o `mtime` dos arquivos com uma data textual.

---

# `-newermt`

A expressão:

```bash
-newermt
```

é muito útil no GNU `find`.

Ela permite informar uma data diretamente, sem criar um arquivo de referência.

## Exemplo

```bash
find . -type f -newermt "2026-07-01"
```

Tradução:

> Procure arquivos modificados depois de 1º de julho de 2026.

---

## Arquivos modificados hoje

```bash
find . -type f -newermt "today"
```

---

## Arquivos modificados nas últimas duas horas

```bash
find . -type f -newermt "2 hours ago"
```

---

## Arquivos modificados entre duas datas

```bash
find . -type f \
-newermt "2026-07-01" \
! -newermt "2026-07-15"
```

> [!warning]
> `-newermt` é comum no GNU `find`, mas não é uma opção portátil para todas as implementações Unix.

---

# Casos de uso em Administração Linux

## Logs modificados nas últimas 24 horas

```bash
find /var/log -type f -mtime -1
```

---

## Arquivos antigos há mais de 90 dias

```bash
find /tmp -type f -mtime +90
```

---

## Configurações alteradas na última hora

```bash
find /etc -type f -cmin -60 2>/dev/null
```

---

# Casos de uso em Shell Script

## Remover arquivos temporários antigos

Primeiro, teste apenas listando:

```bash
find /tmp/minha-aplicacao -type f -mtime +7
```

Depois de confirmar os resultados:

```bash
find /tmp/minha-aplicacao -type f -mtime +7 -delete
```

> [!warning]
> Nunca utilize `-delete` antes de conferir cuidadosamente quais arquivos serão afetados.

---

## Listar arquivos recentes

```bash
arquivos=$(find ./projeto -type f -mmin -30)

printf '%s\n' "$arquivos"
```

Para nomes contendo espaços ou quebras de linha, métodos baseados em `-print0` são mais seguros e serão estudados posteriormente.

---

# Casos de uso em Pentest e CTF

## Encontrar arquivos modificados recentemente

```bash
find / -type f -mmin -60 2>/dev/null
```

Pode revelar:

- Arquivos criados pelo desafio;
- Scripts modificados recentemente;
- Configurações recentes;
- Backups;
- Logs;
- Arquivos temporários.

---

## Procurar alterações recentes em `/opt`

```bash
find /opt -type f -ctime -1 2>/dev/null
```

Isso pode ajudar a identificar programas ou scripts personalizados recentemente alterados.

---

## Arquivos Web modificados recentemente

```bash
find /var/www -type f -mtime -7 2>/dev/null
```

Pode revelar:

- Uploads;
- Web shells em laboratório;
- Arquivos de configuração alterados;
- Backups criados recentemente;
- Código atualizado.

---

## Arquivos criados depois de um marcador

Antes de executar uma ação no laboratório:

```bash
touch /tmp/marcador
```

Depois da ação:

```bash
find /tmp /var/tmp /dev/shm -type f -newer /tmp/marcador 2>/dev/null
```

Isso ajuda a identificar arquivos novos ou modificados depois daquele momento.

---

# Como interpretar resultados recentes

Um arquivo recente não é automaticamente importante.

Pergunte:

1. Quem é o proprietário?
2. Qual processo o criou?
3. Onde ele está?
4. Quais permissões possui?
5. É um script?
6. É uma configuração?
7. É um log?
8. É um arquivo temporário?
9. É utilizado por um serviço?
10. Pode ser modificado pelo usuário atual?

Comandos úteis:

```bash
ls -la arquivo
```

```bash
stat arquivo
```

```bash
file arquivo
```

```bash
lsof arquivo 2>/dev/null
```

---

# Erros comuns

## Confundir `ctime` com data de criação

Errado:

```text
ctime = creation time
```

Correto:

```text
ctime = change time
```

Ele representa alterações no estado do arquivo, incluindo metadados.

---

## Interpretar `-mtime 1` como “nas últimas 24 horas”

Para arquivos das últimas 24 horas, normalmente use:

```bash
-mtime -1
```

O valor sem sinal trabalha com uma faixa arredondada específica.

---

## Ignorar o arredondamento

As opções em dias trabalham com períodos completos de 24 horas.

Para maior precisão, utilize:

```bash
-mmin
```

ou:

```bash
-newermt
```

quando disponível.

---

## Confiar completamente no `atime`

O `atime` pode ser reduzido ou desativado por opções de montagem.

Sempre interprete esse dado com cuidado.

---

## Apagar arquivos sem testar

Antes de:

```bash
find /tmp -type f -mtime +30 -delete
```

execute primeiro:

```bash
find /tmp -type f -mtime +30 -print
```

Confirme os resultados antes de remover qualquer coisa.

---

# Mini cheatsheet

| Objetivo | Comando |
|---|---|
| Modificados nas últimas 24 horas | `find . -type f -mtime -1` |
| Modificados há mais de 30 dias | `find . -type f -mtime +30` |
| Modificados nos últimos 30 minutos | `find . -type f -mmin -30` |
| Metadados alterados na última hora | `find . -type f -cmin -60` |
| Acessados há mais de 90 dias | `find . -type f -atime +90` |
| Mais novos que um arquivo | `find . -type f -newer referencia` |
| Mais novos que uma data | `find . -type f -newermt "2026-07-01"` |
| Modificados hoje | `find . -type f -newermt "today"` |
| Entre duas datas | `find . -newermt "2026-07-01" ! -newermt "2026-07-15"` |

---

# Resumo

Os filtros de tempo principais são:

```text
-mtime → alteração do conteúdo em dias
-ctime → alteração do estado ou metadados em dias
-atime → último acesso em dias

-mmin  → alteração do conteúdo em minutos
-cmin  → alteração do estado em minutos
-amin  → último acesso em minutos
```

A opção:

```bash
-newer
```

compara arquivos com um arquivo de referência.

Em GNU `find`, opções como:

```bash
-newermt
```

permitem comparar diretamente com datas textuais.

Na próxima parte estudaremos:

- `-size`;
- Unidades de tamanho;
- Arquivos maiores e menores que determinado valor;
- `-empty`;
- `-links`;
- Como procurar arquivos grandes, vazios ou com múltiplos hard links.

---

# Procurando arquivos por tamanho

A opção:

```bash
-size
```

permite localizar arquivos de acordo com o espaço ocupado.

Ela é útil para encontrar:

- Arquivos muito grandes;
- Arquivos pequenos;
- Backups;
- Bancos de dados;
- Imagens de disco;
- Logs que cresceram demais;
- Arquivos suspeitos;
- Arquivos vazios.

---

# Sintaxe

```bash
find DIRETORIO -size TAMANHO
```

Exemplo:

```bash
find /home -type f -size +100M
```

Tradução:

> Procure dentro de `/home` arquivos comuns maiores que 100 MiB.

---

# Estrutura da opção

```text
-size +100M
       │ │ │
       │ │ └── unidade
       │ └──── valor
       └────── tipo de comparação
```

Neste exemplo:

| Parte | Significado |
|---|---|
| `-size` | Filtra pelo tamanho |
| `+` | Maior que |
| `100` | Valor informado |
| `M` | Unidade em MiB |

---

# As três formas principais

Assim como algumas outras expressões do `find`, `-size` aceita três formas:

```bash
-size N
-size +N
-size -N
```

Elas possuem significados diferentes.

---

# Tamanho correspondente

```bash
-size N
```

Procura arquivos correspondentes à quantidade de unidades informada.

Exemplo:

```bash
find . -type f -size 10M
```

O `find` procura arquivos que ocupem a faixa correspondente a 10 unidades de MiB segundo o arredondamento utilizado pela ferramenta.

> [!note]
> Quando a unidade não é `c` (bytes), o GNU `find` arredonda o tamanho do arquivo para cima na unidade escolhida.
>
> Por isso, `-size 10M` não deve ser entendido como uma comparação byte por byte absolutamente exata.

Para comparações exatas em bytes, utilize:

```bash
-size 10485760c
```

Porque:

```text
10 MiB = 10 × 1024 × 1024 bytes
       = 10485760 bytes
```

---

# Maior que determinado tamanho

```bash
-size +N
```

O sinal:

```text
+
```

significa:

> Maior que a quantidade informada.

Exemplo:

```bash
find / -type f -size +100M 2>/dev/null
```

Tradução:

> Procure arquivos comuns maiores que 100 MiB.

Esse é um dos usos mais comuns de `-size`.

---

# Menor que determinado tamanho

```bash
-size -N
```

O sinal:

```text
-
```

significa:

> Menor que a quantidade informada.

Exemplo:

```bash
find . -type f -size -10k
```

Tradução:

> Procure arquivos com tamanho inferior a 10 KiB, considerando a unidade escolhida pelo `find`.

---

# Comparando as três formas

| Expressão | Significado |
|---|---|
| `-size 10M` | Tamanho correspondente à faixa de 10 MiB |
| `-size +10M` | Maior que 10 MiB |
| `-size -10M` | Menor que 10 MiB |

---

# Unidades disponíveis

O número informado pode receber uma letra que define a unidade.

| Unidade | Significado |
|---|---|
| `c` | Bytes |
| `w` | Palavras de 2 bytes |
| `b` | Blocos de 512 bytes |
| `k` | Kibibytes de 1024 bytes |
| `M` | Mebibytes de 1024 × 1024 bytes |
| `G` | Gibibytes de 1024 × 1024 × 1024 bytes |

---

# Diferença entre KB e KiB

Embora seja comum dizer:

```text
KB
MB
GB
```

as unidades utilizadas pelo GNU `find` com:

```text
k
M
G
```

são baseadas em potências de 1024.

Tecnicamente:

```text
1 KiB = 1024 bytes
1 MiB = 1024 KiB
1 GiB = 1024 MiB
```

No uso cotidiano, muitas pessoas chamam essas unidades apenas de KB, MB e GB.

---

# Unidade padrão

Quando nenhuma unidade é informada, o `find` utiliza:

```text
b
```

Isto significa blocos de:

```text
512 bytes
```

Exemplo:

```bash
find . -size 10
```

Não significa necessariamente 10 bytes.

Significa uma faixa correspondente a:

```text
10 blocos de 512 bytes
```

Por isso, é uma boa prática informar sempre a unidade:

```bash
-size 10c
```

```bash
-size 10k
```

```bash
-size 10M
```

---

# Procurar por bytes

Utilize:

```text
c
```

Exemplo:

```bash
find . -type f -size 100c
```

Procura arquivos com exatamente 100 bytes.

---

## Maiores que 1.000 bytes

```bash
find . -type f -size +1000c
```

---

## Menores que 500 bytes

```bash
find . -type f -size -500c
```

---

# Procurar por KiB

Utilize:

```text
k
```

Exemplo:

```bash
find . -type f -size +500k
```

Tradução:

> Procure arquivos maiores que 500 KiB.

---

# Procurar por MiB

Utilize:

```text
M
```

Exemplo:

```bash
find /home -type f -size +100M
```

Pode encontrar:

```text
/home/allan/Downloads/video.mp4
/home/allan/backup.tar.gz
/home/maria/maquina-virtual.iso
```

---

# Procurar por GiB

Utilize:

```text
G
```

Exemplo:

```bash
find / -type f -size +1G 2>/dev/null
```

Tradução:

> Procure arquivos maiores que 1 GiB.

Isso pode revelar:

- Imagens de disco;
- Máquinas virtuais;
- Backups;
- Bancos de dados;
- Logs muito grandes;
- Arquivos temporários.

---

# Exemplo prático

Imagine estes arquivos:

```text
nota.txt        → 2 KiB
script.sh       → 8 KiB
backup.zip      → 150 MiB
database.sql    → 600 MiB
imagem.iso      → 4 GiB
```

## Arquivos maiores que 100 MiB

```bash
find . -type f -size +100M
```

Resultado:

```text
./backup.zip
./database.sql
./imagem.iso
```

---

## Arquivos menores que 10 KiB

```bash
find . -type f -size -10k
```

Resultado possível:

```text
./nota.txt
./script.sh
```

---

## Arquivos maiores que 1 GiB

```bash
find . -type f -size +1G
```

Resultado:

```text
./imagem.iso
```

---

# Mostrar tamanho dos arquivos encontrados

O `find` mostra apenas o caminho por padrão.

Exemplo:

```bash
find /home -type f -size +100M
```

Saída:

```text
/home/allan/backup.tar.gz
```

Para visualizar detalhes:

```bash
find /home -type f -size +100M -exec ls -lh {} \;
```

Possível saída:

```text
-rw-r--r-- 1 allan allan 850M jul 31 10:30 /home/allan/backup.tar.gz
```

A opção `-exec` será estudada detalhadamente em outra parte.

---

# Utilizando `-ls`

Também podemos usar a ação:

```bash
-ls
```

Exemplo:

```bash
find /home -type f -size +100M -ls
```

Ela apresenta informações como:

- inode;
- blocos;
- permissões;
- links;
- proprietário;
- grupo;
- tamanho;
- data;
- caminho.

---

# Procurar arquivos grandes em diretórios específicos

Em vez de pesquisar todo o sistema:

```bash
find / -type f -size +100M 2>/dev/null
```

comece pelos diretórios mais prováveis.

```bash
find /home -type f -size +100M 2>/dev/null
```

```bash
find /var -type f -size +100M 2>/dev/null
```

```bash
find /opt -type f -size +100M 2>/dev/null
```

```bash
find /tmp -type f -size +100M 2>/dev/null
```

Isso reduz o tempo e a quantidade de resultados.

---

# Casos de uso em Administração Linux

## Encontrar logs muito grandes

```bash
find /var/log -type f -size +100M
```

---

## Encontrar arquivos maiores que 1 GiB

```bash
find / -type f -size +1G 2>/dev/null
```

---

## Encontrar arquivos pequenos

```bash
find /home -type f -size -1k
```

---

## Encontrar arquivos grandes modificados recentemente

```bash
find /var -type f -size +100M -mtime -7 2>/dev/null
```

Tradução:

> Procure em `/var` arquivos maiores que 100 MiB modificados nos últimos sete dias.

---

# Casos de uso em Pentest e CTF

## Procurar bancos de dados grandes

```bash
find /var/www /opt /home \
-type f \
\( -iname "*.sql" -o -iname "*.db" -o -iname "*.sqlite" \) \
-size +1M 2>/dev/null
```

---

## Procurar backups grandes

```bash
find /home /opt /var/backups \
-type f \
\( -iname "*.zip" -o -iname "*.tar" -o -iname "*.gz" \) \
-size +10M 2>/dev/null
```

---

## Procurar arquivos grandes em diretórios temporários

```bash
find /tmp /var/tmp /dev/shm -type f -size +10M 2>/dev/null
```

Pode revelar:

- Arquivos transferidos;
- Arquivos temporários;
- Backups;
- Binários;
- Dados deixados por aplicações.

---

# A opção `-empty`

A opção:

```bash
-empty
```

localiza:

- Arquivos comuns vazios;
- Diretórios vazios.

---

# Arquivo vazio

Um arquivo vazio possui:

```text
0 bytes
```

Exemplo:

```bash
touch vazio.txt
```

Depois:

```bash
ls -l vazio.txt
```

Saída:

```text
-rw-r--r-- 1 allan allan 0 jul 31 11:00 vazio.txt
```

---

# Procurar arquivos vazios

```bash
find . -type f -empty
```

Resultado:

```text
./vazio.txt
./logs/erro.log
./config/placeholder
```

---

# Procurar diretórios vazios

```bash
find . -type d -empty
```

Resultado:

```text
./cache
./uploads
./temporario
```

---

# Diferença entre `-empty` e `-size 0c`

Para arquivos comuns, estas buscas podem produzir resultados semelhantes:

```bash
find . -type f -empty
```

```bash
find . -type f -size 0c
```

Entretanto, `-empty` também pode verificar diretórios vazios:

```bash
find . -type d -empty
```

Já `-size 0c` não expressa diretamente a ideia de um diretório sem entradas.

---

# Quando `-empty` é útil?

Em:

- Limpeza de arquivos;
- Identificação de logs vazios;
- Busca por arquivos marcadores;
- Diretórios de upload vazios;
- Verificação de estruturas incompletas;
- Shell Scripts;
- Auditorias.

---

# Remover arquivos vazios

Primeiro, liste:

```bash
find ./logs -type f -empty -print
```

Depois de confirmar:

```bash
find ./logs -type f -empty -delete
```

> [!warning]
> Sempre teste com `-print` antes de utilizar `-delete`.

---

# Remover diretórios vazios

Primeiro:

```bash
find ./projeto -type d -empty -print
```

Depois:

```bash
find ./projeto -type d -empty -delete
```

O `find` normalmente processa a árvore de forma adequada para essa ação, mas é necessário analisar cuidadosamente o diretório inicial e os filtros utilizados.

---

# Arquivos vazios em Pentest

Arquivos vazios podem funcionar como:

- Marcadores;
- Flags de estado;
- Arquivos de bloqueio;
- Indicação de funcionalidades;
- Arquivos criados por scripts;
- Placeholders.

Exemplos:

```text
/opt/app/maintenance
/var/run/programa.lock
/var/www/uploads/.keep
```

O fato de estarem vazios não significa que sejam inúteis.

O nome e a localização podem revelar como uma aplicação funciona.

---

# O que são links físicos?

Antes de estudar:

```bash
-links
```

precisamos entender os **hard links**.

No Linux, um nome de arquivo aponta para um inode.

O inode armazena metadados e referencia os blocos de dados do arquivo.

Representação simplificada:

```text
arquivo.txt
     │
     ▼
   inode
     │
     ▼
   dados
```

---

# Hard link

Um hard link é outro nome apontando para o mesmo inode.

Exemplo:

```text
arquivo.txt ───┐
               ├── inode 12345 ─── dados
copia.txt   ───┘
```

Os dois nomes representam o mesmo conteúdo no sistema de arquivos.

---

# Criando um hard link

```bash
ln arquivo.txt copia.txt
```

Agora:

```bash
ls -li arquivo.txt copia.txt
```

Possível saída:

```text
12345 -rw-r--r-- 2 allan allan 100 arquivo.txt
12345 -rw-r--r-- 2 allan allan 100 copia.txt
```

Observe:

- O inode é igual;
- A quantidade de links é `2`.

---

# Quantidade de links

Na saída de:

```bash
ls -l
```

existe uma coluna com a quantidade de hard links.

Exemplo:

```text
-rw-r--r-- 2 allan allan 100 arquivo.txt
           │
           └── quantidade de links
```

---

# A opção `-links`

A opção:

```bash
-links
```

procura objetos de acordo com a quantidade de hard links.

## Sintaxe

```bash
find DIRETORIO -links N
```

---

# Exemplo

```bash
find . -type f -links 2
```

Tradução:

> Procure arquivos comuns com exatamente dois hard links.

---

# Maior ou menor quantidade

Assim como outras expressões numéricas, podemos utilizar:

```text
-links N
-links +N
-links -N
```

---

## Exatamente dois links

```bash
find . -type f -links 2
```

---

## Mais de dois links

```bash
find . -type f -links +2
```

---

## Menos de dois links

```bash
find . -type f -links -2
```

Para arquivos comuns, isso geralmente significa arquivos com apenas um nome associado.

---

# Procurar arquivos com múltiplos hard links

```bash
find /home -type f -links +1 2>/dev/null
```

Tradução:

> Procure arquivos comuns que possuam mais de um hard link.

---

# Por que isso é útil?

Em:

- Auditoria;
- Investigação forense;
- Análise de arquivos duplicados;
- Identificação de nomes diferentes para os mesmos dados;
- Verificação de comportamento suspeito;
- Administração de armazenamento.

Durante um CTF ou análise forense, um arquivo sensível pode possuir outro nome apontando para o mesmo inode.

---

# Encontrar todos os nomes de um inode

Primeiro descubra o inode:

```bash
ls -li arquivo.txt
```

Exemplo:

```text
12345 -rw-r--r-- 2 allan allan 100 arquivo.txt
```

Depois:

```bash
find / -inum 12345 2>/dev/null
```

Possível resultado:

```text
/home/allan/arquivo.txt
/opt/backup/copia.txt
```

A opção:

```bash
-inum
```

procura pelo número do inode.

Ela será mencionada novamente na seção de exemplos avançados.

---

# Hard link versus link simbólico

| Característica | Hard link | Link simbólico |
|---|---|---|
| Aponta para | Mesmo inode | Caminho de outro arquivo |
| Inode | Igual ao arquivo original | Inode próprio |
| Pode ficar quebrado | Normalmente não | Sim |
| Criação | `ln origem destino` | `ln -s origem destino` |
| Busca no `find` | `-links`, `-inum` | `-type l` |

---

# Casos de uso em Shell Script

## Verificar arquivos vazios

```bash
if find ./dados -type f -empty -print -quit | grep -q .; then
    echo "Existem arquivos vazios."
fi
```

A ação:

```bash
-quit
```

encerra a busca após o primeiro resultado e será abordada depois.

---

## Listar arquivos grandes

```bash
find "$HOME" -type f -size +500M -print
```

---

## Listar arquivos com múltiplos links

```bash
find "$HOME" -type f -links +1 -print
```

---

# Combinando tamanho e tempo

## Arquivos grandes e antigos

```bash
find /var/log -type f -size +100M -mtime +30
```

Tradução:

> Procure logs maiores que 100 MiB e modificados há mais de 30 dias.

---

## Backups recentes e grandes

```bash
find /var/backups \
-type f \
-size +10M \
-mtime -7
```

---

# Combinando tamanho e nome

```bash
find /home \
-type f \
-iname "*.zip" \
-size +100M
```

Tradução:

> Procure arquivos ZIP maiores que 100 MiB.

---

# Como interpretar arquivos grandes

Ao encontrar um arquivo grande, pergunte:

1. Qual é o tipo?
2. Quem é o proprietário?
3. Quando foi modificado?
4. Está sendo utilizado?
5. É um banco de dados?
6. É um backup?
7. É um log?
8. É uma imagem de disco?
9. Contém informações sensíveis?
10. Pode ser removido com segurança?

Comandos úteis:

```bash
file arquivo
```

```bash
stat arquivo
```

```bash
du -h arquivo
```

```bash
ls -lh arquivo
```

```bash
lsof arquivo 2>/dev/null
```

---

# Diferença entre tamanho aparente e espaço em disco

O tamanho apresentado por:

```bash
ls -lh
```

pode ser diferente do espaço realmente ocupado, especialmente em arquivos esparsos.

Compare:

```bash
ls -lh arquivo
```

com:

```bash
du -h arquivo
```

Um arquivo esparso pode apresentar um tamanho lógico grande, mas ocupar menos blocos físicos no disco.

A opção `-size` trabalha com o tamanho do arquivo conforme os dados de `stat`, seguindo as unidades e o arredondamento definidos pelo `find`.

---

# Erros comuns

## Esquecer a unidade

```bash
find . -size 100
```

Isso utiliza blocos de 512 bytes, não 100 bytes.

Prefira:

```bash
find . -size 100c
```

ou:

```bash
find . -size 100k
```

---

## Confundir `+` e `-`

```bash
-size +100M
```

Significa:

```text
maior que 100 MiB
```

Enquanto:

```bash
-size -100M
```

significa:

```text
menor que 100 MiB
```

---

## Interpretar `-size 10M` como igualdade byte por byte

As unidades são arredondadas pelo `find`.

Para precisão em bytes, use:

```bash
-size Nc
```

---

## Excluir arquivos sem verificar

Antes de usar:

```bash
-delete
```

sempre execute a mesma busca com:

```bash
-print
```

---

## Considerar todo arquivo grande suspeito

Arquivos grandes podem ser completamente legítimos:

- Bancos de dados;
- Logs;
- Backups;
- Imagens de sistema;
- Máquinas virtuais.

O tamanho é apenas uma pista.

---

# Mini cheatsheet

| Objetivo | Comando |
|---|---|
| Maiores que 100 MiB | `find . -type f -size +100M` |
| Menores que 10 KiB | `find . -type f -size -10k` |
| Exatamente 100 bytes | `find . -type f -size 100c` |
| Maiores que 1 GiB | `find / -type f -size +1G 2>/dev/null` |
| Arquivos vazios | `find . -type f -empty` |
| Diretórios vazios | `find . -type d -empty` |
| Mais de um hard link | `find . -type f -links +1` |
| Exatamente dois links | `find . -type f -links 2` |
| Buscar pelo inode | `find / -inum 12345 2>/dev/null` |
| ZIP maior que 100 MiB | `find /home -type f -iname "*.zip" -size +100M` |
| Logs grandes e antigos | `find /var/log -type f -size +100M -mtime +30` |

---

# Resumo

As principais expressões estudadas foram:

```text
-size  → filtra pelo tamanho
-empty → encontra arquivos ou diretórios vazios
-links → filtra pela quantidade de hard links
-inum  → procura por número de inode
```

As formas de comparação numérica são:

```text
N  → valor correspondente
+N → maior que N
-N → menor que N
```

Na próxima parte estudaremos como controlar a profundidade e o percurso da busca utilizando:

- `-maxdepth`;
- `-mindepth`;
- `-depth`;
- `-prune`;
- `-xdev`;
- `-mount`;
- Como impedir que o `find` atravesse diretórios ou sistemas de arquivos desnecessários.


---

# Controlando a profundidade e o percurso da busca

Por padrão, o `find` percorre recursivamente todos os subdiretórios encontrados a partir do caminho inicial.

Exemplo:

```bash
find /home
```

Estrutura:

```text
/home
├── allan
│   ├── Documentos
│   │   └── projetos
│   │       └── bash
│   └── Downloads
└── maria
    └── backups
```

O `find` pode entrar em todos esses níveis.

Em algumas situações, isso é desnecessário.

Podemos querer:

- Analisar apenas o diretório atual;
- Limitar a quantidade de níveis;
- Ignorar determinado diretório;
- Processar primeiro os arquivos mais profundos;
- Evitar sistemas de arquivos montados;
- Impedir que a busca entre em `/proc`, `/sys` ou diretórios muito grandes.

Para isso, existem opções como:

```text
-maxdepth
-mindepth
-depth
-prune
-xdev
-mount
```

---

# O que é profundidade?

A profundidade representa quantos níveis abaixo do diretório inicial o objeto está localizado.

Imagine:

```text
/projeto
├── README.md
├── src
│   ├── main.c
│   └── libs
│       └── helper.c
└── tests
    └── teste.c
```

Considerando `/projeto` como o ponto inicial:

| Caminho | Profundidade |
|---|---:|
| `/projeto` | 0 |
| `/projeto/README.md` | 1 |
| `/projeto/src` | 1 |
| `/projeto/src/main.c` | 2 |
| `/projeto/src/libs` | 2 |
| `/projeto/src/libs/helper.c` | 3 |

O próprio diretório inicial está na profundidade:

```text
0
```

---

# A opção `-maxdepth`

A opção:

```bash
-maxdepth
```

define a profundidade máxima que o `find` poderá alcançar.

## Sintaxe

```bash
find DIRETORIO -maxdepth N
```

Onde:

```text
N
```

representa a quantidade máxima de níveis.

---

# `-maxdepth 0`

```bash
find /projeto -maxdepth 0
```

Resultado:

```text
/projeto
```

O `find` mostra apenas o próprio diretório inicial.

Ele não entra em nenhum subdiretório.

---

# `-maxdepth 1`

```bash
find /projeto -maxdepth 1
```

Resultado:

```text
/projeto
/projeto/README.md
/projeto/src
/projeto/tests
```

O `find` mostra:

- O diretório inicial;
- Seus filhos imediatos.

Ele não entra mais profundamente em `src` ou `tests`.

---

# `-maxdepth 2`

```bash
find /projeto -maxdepth 2
```

Resultado:

```text
/projeto
/projeto/README.md
/projeto/src
/projeto/src/main.c
/projeto/src/libs
/projeto/tests
/projeto/tests/teste.c
```

O arquivo:

```text
/projeto/src/libs/helper.c
```

não aparece porque está na profundidade `3`.

---

# Exemplo filtrando arquivos

```bash
find /projeto -maxdepth 1 -type f
```

Tradução:

> Procure arquivos comuns no próprio diretório `/projeto` e no primeiro nível abaixo dele.

Resultado possível:

```text
/projeto/README.md
```

Arquivos dentro de `/projeto/src` não serão retornados.

---

# Quando utilizar `-maxdepth`?

É útil quando você deseja:

- Evitar buscas muito profundas;
- Analisar somente a raiz de um projeto;
- Listar apenas filhos diretos;
- Melhorar o desempenho;
- Reduzir resultados desnecessários;
- Evitar entrar em estruturas muito grandes.

---

# Exemplo em `/home`

```bash
find /home -maxdepth 2 -type f
```

Isso pode mostrar arquivos diretamente dentro dos diretórios pessoais dos usuários sem percorrer todos os projetos, caches e downloads internos.

---

# Exemplo em Pentest

```bash
find /home -maxdepth 3 -type f \
\( -iname "*.txt" -o -iname "*.bak" -o -iname "*.old" \) \
2>/dev/null
```

O limite de profundidade ajuda a reduzir o volume de resultados.

---

# A opção `-mindepth`

A opção:

```bash
-mindepth
```

define a profundidade mínima necessária para que um resultado seja considerado.

## Sintaxe

```bash
find DIRETORIO -mindepth N
```

---

# `-mindepth 1`

```bash
find /projeto -mindepth 1
```

O próprio diretório inicial:

```text
/projeto
```

não será exibido.

A busca começará a mostrar resultados a partir do primeiro nível.

Resultado:

```text
/projeto/README.md
/projeto/src
/projeto/src/main.c
/projeto/src/libs
/projeto/src/libs/helper.c
/projeto/tests
/projeto/tests/teste.c
```

---

# Por que isso é útil?

Imagine que você queira remover diretórios vazios dentro de um projeto, mas não queira que o próprio diretório inicial seja considerado.

```bash
find /projeto -mindepth 1 -type d -empty
```

Assim, `/projeto` não será retornado, mesmo que esteja vazio.

---

# `-mindepth 2`

```bash
find /projeto -mindepth 2
```

O `find` ignora:

```text
/projeto
/projeto/README.md
/projeto/src
/projeto/tests
```

e começa a mostrar objetos a partir do segundo nível.

Resultado possível:

```text
/projeto/src/main.c
/projeto/src/libs
/projeto/src/libs/helper.c
/projeto/tests/teste.c
```

---

# Combinando `-mindepth` e `-maxdepth`

Podemos criar uma faixa de profundidade.

```bash
find /projeto -mindepth 1 -maxdepth 2
```

Tradução:

> Mostre apenas os objetos localizados entre os níveis 1 e 2.

O diretório inicial não aparece.

Objetos abaixo do nível 2 também não aparecem.

---

# Exemplo prático

```bash
find /home -mindepth 2 -maxdepth 3 -type f
```

Esse comando procura arquivos entre os níveis 2 e 3 abaixo de `/home`.

---

# Comparação

| Opção | Função |
|---|---|
| `-maxdepth N` | Não descer além do nível `N` |
| `-mindepth N` | Não considerar resultados antes do nível `N` |

---

# A opção `-depth`

Por padrão, o `find` normalmente analisa um diretório antes de analisar seu conteúdo.

Exemplo simplificado:

```text
/projeto
/projeto/src
/projeto/src/main.c
```

Com:

```bash
-depth
```

o conteúdo é processado antes do diretório que o contém.

Resultado simplificado:

```text
/projeto/src/main.c
/projeto/src
/projeto
```

---

# Sintaxe

```bash
find DIRETORIO -depth
```

---

# Para que serve?

É especialmente útil quando uma ação depende de processar primeiro os objetos mais profundos.

Exemplo clássico:

- Remover arquivos;
- Depois remover os diretórios vazios que os continham.

Se o diretório fosse processado primeiro, ele ainda conteria arquivos e não poderia ser removido.

---

# Exemplo

```bash
find ./temporario -depth -print
```

Estrutura:

```text
temporario/
└── pasta/
    └── arquivo.txt
```

Ordem aproximada:

```text
./temporario/pasta/arquivo.txt
./temporario/pasta
./temporario
```

---

# Relação entre `-depth` e `-delete`

A ação:

```bash
-delete
```

faz com que o `find` utilize uma lógica de processamento em profundidade.

Isso é necessário para remover primeiro o conteúdo e depois os diretórios.

Exemplo:

```bash
find ./temporario -depth -type f -delete
```

Entretanto, normalmente não é necessário escrever `-depth` explicitamente quando `-delete` já está sendo utilizado no GNU `find`.

> [!warning]
> O uso de `-delete` exige muito cuidado. Sempre confira os resultados com `-print` antes.

---

# A opção `-prune`

A opção:

```bash
-prune
```

impede que o `find` entre em determinado diretório.

Ela significa, de forma aproximada:

> Não percorra o conteúdo desta árvore.

---

# Quando utilizar?

É útil para ignorar diretórios como:

```text
.git
node_modules
venv
cache
backup
proc
sys
```

---

# Exemplo simples

Imagine:

```text
/projeto
├── src
│   └── main.py
├── .git
│   ├── config
│   └── objects
└── README.md
```

Queremos pesquisar o projeto sem entrar em `.git`.

Comando:

```bash
find /projeto -path "/projeto/.git" -prune -o -print
```

---

# Explicando o comando

```bash
find /projeto \
-path "/projeto/.git" \
-prune \
-o \
-print
```

| Parte | Função |
|---|---|
| `find /projeto` | Inicia a busca |
| `-path "/projeto/.git"` | Identifica o diretório `.git` |
| `-prune` | Impede a entrada nesse diretório |
| `-o` | OU |
| `-print` | Mostra os outros resultados |

Fluxo simplificado:

```text
O caminho é /projeto/.git?
        │
       Sim
        │
      -prune
        │
Não entrar no diretório
```

Caso não seja `.git`:

```text
-path não corresponde
        │
       -o
        │
      -print
```

---

# Ignorando qualquer diretório `.git`

```bash
find . -path "*/.git" -prune -o -print
```

Esse comando evita entrar em qualquer diretório chamado `.git`.

---

# Procurar arquivos Python ignorando `.git`

```bash
find . \
-path "*/.git" -prune \
-o -type f -name "*.py" -print
```

Tradução:

> Ignore árvores `.git`; nos demais locais, mostre arquivos terminados em `.py`.

---

# Ignorando `node_modules`

```bash
find . \
-path "*/node_modules" -prune \
-o -type f -name "*.js" -print
```

Isso evita percorrer milhares de arquivos de dependências.

---

# Ignorando vários diretórios

```bash
find . \
\( -path "*/.git" -o -path "*/node_modules" -o -path "*/venv" \) \
-prune \
-o -type f -print
```

Tradução:

> Ignore `.git`, `node_modules` e `venv`; mostre os outros arquivos.

---

# Por que `-prune` parece complicado?

Porque ele normalmente precisa ser combinado com:

```text
-o
-print
```

Isso acontece devido ao modo como o `find` avalia expressões.

O padrão mental é:

```text
diretório a ignorar
        ↓
-prune
        ↓
OU
        ↓
condição dos resultados desejados
        ↓
-print
```

---

# `-prune` e `-depth`

Existe uma interação importante.

Quando:

```bash
-depth
```

está ativo, o `find` precisa entrar no diretório antes de processá-lo.

Nesse cenário, `-prune` não consegue impedir corretamente a descida, porque o conteúdo já foi visitado.

Portanto, evite combinar `-prune` com:

```bash
-depth
```

quando seu objetivo for impedir que o diretório seja percorrido.

Como `-delete` implica processamento em profundidade no GNU `find`, combinar exclusão e `-prune` também exige muito cuidado.

---

# A opção `-xdev`

A opção:

```bash
-xdev
```

impede que o `find` atravesse para outro sistema de arquivos.

## Sintaxe

```bash
find / -xdev
```

---

# O que é outro sistema de arquivos?

O diretório raiz pode conter diferentes sistemas de arquivos montados.

Exemplo:

```text
/
├── home
├── proc
├── sys
├── dev
├── media
│   └── disco-externo
└── mnt
    └── compartilhamento
```

Alguns desses caminhos podem pertencer a:

- Outra partição;
- Disco externo;
- NFS;
- Sistema de arquivos virtual;
- Container;
- Compartilhamento de rede.

---

# Exemplo sem `-xdev`

```bash
find / -type f
```

A busca pode atravessar:

```text
/proc
/sys
/dev
/mnt
/media
```

dependendo das montagens e permissões.

---

# Exemplo com `-xdev`

```bash
find / -xdev -type f
```

O `find` permanece no mesmo sistema de arquivos do diretório inicial.

---

# Uso em enumeração SUID

```bash
find / -xdev -type f -perm -4000 2>/dev/null
```

Isso procura arquivos SUID somente no sistema de arquivos onde `/` está localizado.

Vantagens:

- Menos ruído;
- Busca mais rápida;
- Evita compartilhamentos de rede;
- Evita discos externos;
- Evita algumas montagens desnecessárias.

Desvantagem:

- Pode ignorar resultados interessantes em outros sistemas de arquivos montados.

---

# A opção `-mount`

No GNU `find`, a opção:

```bash
-mount
```

possui finalidade equivalente a:

```bash
-xdev
```

Exemplo:

```bash
find / -mount -type f
```

Ela impede que a busca atravesse para outros sistemas de arquivos.

---

# Comparando `-xdev` e `-mount`

| Opção | Função |
|---|---|
| `-xdev` | Permanecer no mesmo sistema de arquivos |
| `-mount` | Equivalente a `-xdev` no GNU `find` |

`-xdev` costuma ser mais claro e bastante utilizado em scripts.

---

# Como visualizar os sistemas de arquivos montados

```bash
findmnt
```

ou:

```bash
mount
```

ou:

```bash
df -T
```

Esses comandos ajudam a compreender quais caminhos pertencem a sistemas de arquivos diferentes.

---

# Casos de uso em Administração Linux

## Procurar logs sem sair da partição

```bash
find /var -xdev -type f -name "*.log"
```

---

## Encontrar arquivos grandes sem entrar em montagens externas

```bash
find / -xdev -type f -size +1G 2>/dev/null
```

---

## Ignorar diretórios de dependências

```bash
find ./projeto \
\( -path "*/node_modules" -o -path "*/venv" \) \
-prune \
-o -type f -print
```

---

# Casos de uso em Shell Script

## Processar apenas arquivos do primeiro nível

```bash
find "$HOME/Downloads" -maxdepth 1 -type f -print
```

---

## Evitar o próprio diretório inicial

```bash
find "$diretorio" -mindepth 1 -maxdepth 1 -print
```

---

## Ignorar o diretório de backup

```bash
find "$diretorio" \
-path "$diretorio/backup" -prune \
-o -type f -print
```

---

# Casos de uso em Pentest e CTF

## Analisar arquivos nos diretórios pessoais sem descer excessivamente

```bash
find /home -maxdepth 3 -type f 2>/dev/null
```

---

## Procurar backups ignorando caches

```bash
find /home \
\( -path "*/.cache" -o -path "*/node_modules" \) \
-prune \
-o -type f \
\( -iname "*.bak" -o -iname "*.old" -o -iname "*.zip" \) \
-print 2>/dev/null
```

---

## Enumerar SUID apenas no sistema de arquivos principal

```bash
find / -xdev -type f -perm -4000 2>/dev/null
```

---

## Procurar arquivos em `/opt` até dois níveis

```bash
find /opt -maxdepth 2 -type f -ls 2>/dev/null
```

Isso pode revelar rapidamente:

- Scripts;
- Binários personalizados;
- Configurações;
- Backups.

---

# Como escolher a profundidade?

Pergunte:

1. Preciso analisar toda a árvore?
2. Sei aproximadamente onde o arquivo está?
3. O diretório possui milhares de subdiretórios?
4. Existem caches ou dependências?
5. Quero apenas filhos diretos?
6. Preciso evitar sistemas de arquivos montados?

Uma busca menor normalmente é:

- Mais rápida;
- Mais fácil de analisar;
- Menos ruidosa;
- Menos propensa a erros.

---

# Erros comuns

## Acreditar que `-maxdepth 1` mostra apenas arquivos

```bash
find . -maxdepth 1
```

mostra qualquer tipo de objeto no primeiro nível.

Para mostrar somente arquivos:

```bash
find . -maxdepth 1 -type f
```

---

## Esquecer que o diretório inicial está no nível 0

```bash
find . -maxdepth 0
```

mostra apenas:

```text
.
```

---

## Usar `-prune` sem `-o -print`

Exemplo incompleto:

```bash
find . -path "*/.git" -prune
```

Isso apenas identifica e poda o diretório `.git`.

Não define claramente o que deve ser mostrado nos outros caminhos.

Forma comum:

```bash
find . -path "*/.git" -prune -o -print
```

---

## Colocar `-print` no local errado

O comando:

```bash
find . -print -path "*/.git" -prune
```

pode imprimir o caminho antes de decidir podá-lo.

A ordem das expressões importa.

---

## Combinar `-prune` com `-depth`

Com processamento em profundidade, o conteúdo pode já ter sido visitado antes de o diretório ser podado.

---

## Usar `-xdev` e esquecer outras partições

`-xdev` reduz a busca, mas também pode deixar de encontrar arquivos em:

- `/home`, caso seja partição separada;
- `/var`, caso seja partição separada;
- Montagens NFS;
- Volumes adicionais.

Use apenas quando essa limitação fizer sentido.

---

# Mini cheatsheet

| Objetivo | Comando |
|---|---|
| Apenas o diretório inicial | `find . -maxdepth 0` |
| Primeiro nível | `find . -maxdepth 1` |
| Ignorar o diretório inicial | `find . -mindepth 1` |
| Entre níveis 1 e 2 | `find . -mindepth 1 -maxdepth 2` |
| Processar conteúdo primeiro | `find . -depth` |
| Ignorar `.git` | `find . -path "*/.git" -prune -o -print` |
| Ignorar `node_modules` | `find . -path "*/node_modules" -prune -o -print` |
| Não atravessar montagens | `find / -xdev` |
| Mesmo efeito no GNU | `find / -mount` |
| SUID no sistema principal | `find / -xdev -type f -perm -4000 2>/dev/null` |

---

# Resumo

As principais opções de controle do percurso são:

```text
-maxdepth N → profundidade máxima
-mindepth N → profundidade mínima
-depth      → processar conteúdo antes do diretório
-prune      → impedir a entrada em determinada árvore
-xdev       → não atravessar outros sistemas de arquivos
-mount      → equivalente a -xdev no GNU find
```

Essas opções ajudam a criar buscas:

- Mais rápidas;
- Mais específicas;
- Menos ruidosas;
- Mais seguras.

Na próxima parte estudaremos as ações executadas após um resultado ser encontrado:

- `-print`;
- `-print0`;
- `-printf`;
- `-ls`;
- `-exec`;
- `-execdir`;
- `-ok`;
- `-delete`;
- `-quit`.

---

# Ações do comando `find`

Até agora utilizamos o `find` principalmente para localizar objetos.

Exemplo:

```bash
find /home -type f -name "*.txt"
```

Nesse caso, o `find`:

1. Percorre `/home`;
2. Verifica quais objetos são arquivos;
3. Verifica quais nomes terminam em `.txt`;
4. Mostra os resultados.

Porém, o `find` não serve apenas para pesquisar.

Ele também pode executar ações sobre cada resultado encontrado.

Exemplos:

- Mostrar o caminho;
- Exibir informações detalhadas;
- Executar outro comando;
- Remover arquivos;
- Parar após o primeiro resultado;
- Produzir uma saída formatada;
- Enviar nomes com segurança para outros comandos.

As principais ações são:

```text
-print
-print0
-printf
-ls
-exec
-execdir
-ok
-okdir
-delete
-quit
```

---

# Ação padrão do `find`

Quando nenhuma ação é informada, o `find` normalmente utiliza:

```bash
-print
```

automaticamente.

Estes comandos são equivalentes:

```bash
find . -type f -name "*.txt"
```

```bash
find . -type f -name "*.txt" -print
```

Ambos mostram o caminho de cada resultado encontrado.

---

# A ação `-print`

A ação:

```bash
-print
```

exibe o caminho de cada objeto que corresponde às condições da busca.

## Sintaxe

```bash
find DIRETORIO EXPRESSÕES -print
```

## Exemplo

```bash
find /home -type f -name "*.txt" -print
```

Possível saída:

```text
/home/allan/anotacoes.txt
/home/allan/Documentos/lista.txt
/home/maria/lembretes.txt
```

---

# Quando usar `-print` explicitamente?

Embora seja a ação padrão, escrevê-la pode deixar comandos complexos mais claros.

Isso é especialmente útil quando usamos:

- `-prune`;
- `-o`;
- Parênteses;
- Outras ações;
- Vários grupos de expressões.

Exemplo:

```bash
find . \
-path "*/.git" -prune \
-o -type f -name "*.py" -print
```

Aqui, o `-print` deixa claro que apenas os arquivos Python fora de `.git` devem ser mostrados.

---

# O caractere de separação usado por `-print`

Normalmente, cada resultado termina com uma quebra de linha.

Representação:

```text
arquivo1
arquivo2
arquivo3
```

Isso funciona bem na maioria dos casos.

Porém, nomes de arquivos podem conter:

- Espaços;
- Tabulações;
- Aspas;
- Quebras de linha;
- Caracteres especiais.

Por isso, `-print` nem sempre é a forma mais segura de transmitir resultados para outro programa.

---

# O problema dos nomes com espaços

Imagine estes arquivos:

```text
relatorio final.txt
minhas notas.txt
arquivo.txt
```

Este comando é perigoso:

```bash
for arquivo in $(find . -type f -name "*.txt"); do
    echo "$arquivo"
done
```

A substituição de comandos separa a saída usando espaços e quebras de linha.

O arquivo:

```text
relatorio final.txt
```

pode ser interpretado como dois itens:

```text
./relatorio
final.txt
```

Esse comportamento produz erros.

---

# A ação `-print0`

A ação:

```bash
-print0
```

exibe cada caminho terminado por um caractere nulo:

```text
NUL
```

em vez de uma quebra de linha.

## Sintaxe

```bash
find DIRETORIO EXPRESSÕES -print0
```

O caractere nulo não pode aparecer dentro de um nome de arquivo Unix.

Por isso, ele funciona como um separador seguro.

---

# Exemplo com `xargs`

```bash
find . -type f -name "*.txt" -print0 |
xargs -0 ls -l
```

Separando:

| Parte | Função |
|---|---|
| `find ... -print0` | Gera nomes separados por NUL |
| `|` | Envia a saída para outro comando |
| `xargs -0` | Lê itens separados por NUL |
| `ls -l` | Exibe detalhes dos arquivos |

Essa combinação trata corretamente nomes contendo espaços e quebras de linha.

---

# Exemplo seguro em Shell Script

```bash
find . -type f -name "*.txt" -print0 |
while IFS= read -r -d '' arquivo; do
    printf 'Arquivo: %s\n' "$arquivo"
done
```

## Explicação

| Elemento | Função |
|---|---|
| `IFS=` | Evita remover espaços no início ou no fim |
| `read -r` | Não interpreta barras invertidas |
| `-d ''` | Usa o caractere NUL como delimitador |
| `"$arquivo"` | Preserva o nome exatamente como foi encontrado |

---

# `-print` versus `-print0`

| Ação | Separador | Uso |
|---|---|---|
| `-print` | Quebra de linha | Visualização comum |
| `-print0` | Caractere NUL | Integração segura com outros comandos |

Use:

```bash
-print
```

para ler os resultados no terminal.

Use:

```bash
-print0
```

quando os caminhos forem processados automaticamente.

---

# A ação `-ls`

A ação:

```bash
-ls
```

mostra informações detalhadas de cada resultado.

## Exemplo

```bash
find /home -type f -name "*.txt" -ls
```

Possível saída:

```text
123456 4 -rw-r--r-- 1 allan allan 842 Jul 31 10:20 /home/allan/notas.txt
```

A saída pode apresentar:

- Número do inode;
- Quantidade de blocos;
- Permissões;
- Quantidade de hard links;
- Proprietário;
- Grupo;
- Tamanho;
- Data;
- Caminho.

---

# Quando usar `-ls`?

É útil quando você deseja analisar rapidamente:

- Permissões;
- Proprietário;
- Grupo;
- Tamanho;
- Inode;
- Data de modificação.

Exemplo em enumeração:

```bash
find /opt -maxdepth 3 -type f -ls 2>/dev/null
```

Isso permite analisar os arquivos de `/opt` sem executar um `ls` separado para cada caminho.

---

# A ação `-printf`

A ação:

```bash
-printf
```

permite criar uma saída personalizada.

Ela é semelhante ao comando `printf`, mas utiliza diretivas específicas do GNU `find`.

> [!note]
> `-printf` é uma extensão comum do GNU `find` e pode não existir em todas as implementações Unix.

---

# Sintaxe

```bash
find DIRETORIO EXPRESSÕES -printf "FORMATO"
```

## Exemplo simples

```bash
find . -type f -printf "%p\n"
```

A diretiva:

```text
%p
```

representa o caminho encontrado.

O `\n` cria uma quebra de linha.

---

# Diretivas comuns de `-printf`

| Diretiva | Informação |
|---|---|
| `%p` | Caminho encontrado |
| `%f` | Nome do arquivo sem os diretórios anteriores |
| `%h` | Diretório que contém o arquivo |
| `%s` | Tamanho em bytes |
| `%m` | Permissões em formato octal |
| `%M` | Permissões em formato simbólico |
| `%u` | Nome do proprietário |
| `%U` | UID numérico |
| `%g` | Nome do grupo |
| `%G` | GID numérico |
| `%i` | Número do inode |
| `%n` | Quantidade de hard links |
| `%y` | Tipo do objeto |
| `%T+` | Data e hora da última modificação |
| `\n` | Nova linha |
| `\t` | Tabulação |
| `\0` | Caractere nulo |

---

# Exibir nome e tamanho

```bash
find . -type f -printf "%p %s bytes\n"
```

Possível saída:

```text
./notas.txt 842 bytes
./backup.zip 10485760 bytes
```

---

# Exibir permissões e proprietário

```bash
find /opt -type f -printf "%M %u:%g %p\n"
```

Possível saída:

```text
-rwxr-xr-x root:root /opt/backup.sh
-rw-r----- root:developers /opt/config.ini
```

---

# Exibir permissões em octal

```bash
find . -type f -printf "%m %p\n"
```

Possível saída:

```text
644 ./notas.txt
755 ./script.sh
600 ./senha.txt
```

---

# Exibir tipo do objeto

```bash
find . -printf "%y %p\n"
```

Possível saída:

```text
d .
f ./notas.txt
d ./scripts
f ./scripts/backup.sh
l ./python
```

Tipos comuns:

```text
f → arquivo comum
d → diretório
l → link simbólico
s → socket
p → pipe
b → dispositivo de bloco
c → dispositivo de caractere
```

---

# Exibir data de modificação

```bash
find . -type f -printf "%T+ %p\n"
```

Possível saída:

```text
2026-07-31+10:30:20.1234567890 ./notas.txt
2026-07-30+17:20:05.0000000000 ./backup.zip
```

---

# Saída em formato de tabela

```bash
find /opt -type f \
-printf "%-10m %-12u %-12g %10s %p\n"
```

Esse formato pode alinhar campos, dependendo da implementação.

Exemplo conceitual:

```text
755        root         root              812 /opt/backup.sh
640        root         developers       2048 /opt/config.ini
```

---

# `-printf` não adiciona nova linha automaticamente

Este comando:

```bash
find . -type f -printf "%p"
```

pode produzir:

```text
./a.txt./b.txt./c.txt
```

Por isso, normalmente adicionamos:

```text
\n
```

Exemplo correto:

```bash
find . -type f -printf "%p\n"
```

---

# A ação `-exec`

A ação:

```bash
-exec
```

executa um comando utilizando os resultados encontrados.

## Sintaxe geral

```bash
find DIRETORIO EXPRESSÕES -exec COMANDO {} \;
```

Exemplo:

```bash
find . -type f -name "*.txt" -exec ls -l {} \;
```

---

# Entendendo cada parte

```text
-exec ls -l {} \;
  │    │   │  │
  │    │   │  └── final da ação
  │    │   └──── substituído pelo caminho encontrado
  │    └──────── comando e opções
  └───────────── inicia a execução
```

---

# O marcador `{}`

O texto:

```text
{}
```

é substituído pelo caminho do resultado atual.

Imagine que o `find` encontre:

```text
./a.txt
./b.txt
```

O comando:

```bash
find . -type f -name "*.txt" -exec ls -l {} \;
```

funciona aproximadamente como:

```bash
ls -l ./a.txt
ls -l ./b.txt
```

---

# Por que usamos `\;`?

O ponto e vírgula encerra a ação `-exec`.

Porém, o shell também interpreta:

```text
;
```

como separador de comandos.

Para impedir que o shell o consuma antes de o `find` recebê-lo, escapamos:

```bash
\;
```

Também é possível usar aspas:

```bash
-exec comando {} ';'
```

A forma com barra invertida é mais comum:

```bash
-exec comando {} \;
```

---

# Exemplo com `file`

```bash
find /opt -type f -exec file {} \;
```

Possível saída:

```text
/opt/backup: ELF 64-bit LSB executable
/opt/config.ini: ASCII text
/opt/script.sh: Bourne-Again shell script
```

---

# Exemplo com `chmod`

```bash
find ./scripts -type f -name "*.sh" -exec chmod u+x {} \;
```

Tradução:

> Para cada arquivo `.sh` encontrado, adicione permissão de execução ao proprietário.

> [!warning]
> Comandos que alteram arquivos devem ser testados primeiro com uma ação segura, como `-print`.

---

# Exemplo com `grep`

```bash
find /var/www -type f -name "*.php" \
-exec grep -nH "password" {} \;
```

Isso pesquisa a palavra `password` dentro de cada arquivo PHP.

Entretanto, o próprio `grep` suporta pesquisa recursiva, então em alguns casos seria mais simples usar:

```bash
grep -Rni "password" /var/www
```

O `-exec` é útil quando precisamos combinar critérios específicos do `find` com outro comando.

---

# `-exec` com `\;`

Quando termina em:

```bash
\;
```

o comando é executado uma vez para cada resultado.

Exemplo:

```bash
find . -type f -exec ls -l {} \;
```

Se forem encontrados 1.000 arquivos, podem ser iniciadas 1.000 execuções de `ls`.

Isso pode ser lento.

---

# `-exec` com `+`

Também é possível terminar com:

```text
+
```

Exemplo:

```bash
find . -type f -exec ls -l {} +
```

Nesse formato, vários caminhos são agrupados em uma única chamada, respeitando os limites do sistema.

Funcionamento aproximado:

```bash
ls -l ./a.txt ./b.txt ./c.txt ...
```

---

# Comparando `\;` e `+`

| Final | Funcionamento |
|---|---|
| `\;` | Executa uma vez por resultado |
| `+` | Agrupa vários resultados por execução |

A forma com `+` costuma ser mais eficiente.

---

# Exemplo de eficiência

Menos eficiente:

```bash
find /var/log -type f -exec gzip {} \;
```

Mais eficiente quando o comando aceita vários arquivos:

```bash
find /var/log -type f -exec gzip {} +
```

> [!important]
> Nem todo comando ou uso deve receber vários arquivos ao mesmo tempo. Verifique como o programa trata seus argumentos.

---

# Posição de `{}` com `+`

Na forma:

```bash
-exec COMANDO {} +
```

o marcador `{}` normalmente precisa aparecer no final dos argumentos da ação.

Este exemplo funciona:

```bash
find . -type f -exec ls -l {} +
```

Mas uma construção que precise adicionar algo depois dos caminhos pode exigir `sh -c`.

---

# Usando `sh -c` com `-exec`

Exemplo:

```bash
find . -type f -name "*.txt" \
-exec sh -c 'for arquivo do
    printf "Processando: %s\n" "$arquivo"
done' sh {} +
```

## Por que existe o segundo `sh`?

Na execução:

```bash
sh -c 'script' NOME_ARGUMENTO arg1 arg2 ...
```

o primeiro argumento após o script se torna:

```text
$0
```

Por isso usamos:

```text
sh
```

como valor de `$0`.

Os arquivos começam em:

```text
$1
$2
...
```

---

# A ação `-execdir`

A ação:

```bash
-execdir
```

é semelhante a `-exec`.

A diferença é que o comando é executado a partir do diretório que contém o arquivo encontrado.

## Exemplo

```bash
find ./projeto -type f -name "*.txt" \
-execdir pwd \;
```

O comando `pwd` será executado nos diretórios dos arquivos correspondentes.

---

# Exemplo prático de `-execdir`

```bash
find ./fotos -type f -name "*.jpg" \
-execdir chmod 644 {} \;
```

O comando será executado dentro do diretório correspondente.

---

# Segurança de `-execdir`

`-execdir` pode reduzir certos riscos relacionados a caminhos completos e condições de corrida.

Porém, ele também exige cuidado com a variável:

```bash
PATH
```

Algumas implementações recusam `-execdir` quando o `PATH` contém componentes inseguros, como:

```text
.
```

ou entradas vazias.

Uma configuração mais segura pode utilizar caminhos absolutos:

```bash
PATH=/usr/bin:/bin
```

e comandos como:

```bash
/usr/bin/file
```

---

# `-exec` versus `-execdir`

| Ação | Diretório de execução |
|---|---|
| `-exec` | Diretório atual de onde o `find` foi iniciado |
| `-execdir` | Diretório que contém o resultado |

Use `-execdir` quando o contexto do diretório do arquivo for importante.

---

# A ação `-ok`

A ação:

```bash
-ok
```

funciona de maneira semelhante a `-exec`, mas solicita confirmação antes de executar o comando.

## Exemplo

```bash
find . -type f -name "*.bak" -ok rm {} \;
```

O `find` perguntará antes de remover cada arquivo.

Possível prompt:

```text
< rm ... ./config.bak > ?
```

O formato exato pode variar.

---

# Quando usar `-ok`?

É útil para operações manuais e potencialmente destrutivas.

Exemplos:

- Remover arquivos;
- Alterar permissões;
- Mover resultados;
- Substituir arquivos.

---

# Limitações de `-ok`

Como exige interação, normalmente não é indicado para:

- Scripts automáticos;
- Execuções sem terminal;
- Processamento de muitos arquivos;
- Jobs agendados.

---

# A ação `-okdir`

A ação:

```bash
-okdir
```

combina o comportamento de:

```text
-execdir
```

com confirmação interativa.

Ela executa o comando a partir do diretório do resultado, mas pergunta antes.

---

# A ação `-delete`

A ação:

```bash
-delete
```

remove os objetos correspondentes.

## Exemplo

```bash
find ./temporario -type f -name "*.tmp" -delete
```

Isso remove todos os arquivos `.tmp` encontrados.

> [!danger]
> `-delete` remove arquivos diretamente.
>
> Não envia os objetos para a lixeira.

---

# Regra de segurança para `-delete`

Antes de executar:

```bash
find ./temporario -type f -name "*.tmp" -delete
```

execute:

```bash
find ./temporario -type f -name "*.tmp" -print
```

Confira cuidadosamente todos os resultados.

Somente depois substitua:

```bash
-print
```

por:

```bash
-delete
```

---

# A ordem das expressões importa

Este comando é perigoso:

```bash
find . -delete -name "*.tmp"
```

O `find` avalia expressões da esquerda para a direita.

A exclusão pode ocorrer antes de o teste de nome ser aplicado como você imaginava.

Forma correta:

```bash
find . -type f -name "*.tmp" -delete
```

Primeiro filtre.

Depois execute a ação.

---

# Remover arquivos vazios

```bash
find ./logs -type f -empty -delete
```

---

# Remover diretórios vazios

```bash
find ./projeto -mindepth 1 -type d -empty -delete
```

O uso de:

```bash
-mindepth 1
```

evita considerar o próprio diretório inicial.

---

# Remover arquivos antigos

Teste:

```bash
find /tmp/minha-app -type f -mtime +7 -print
```

Depois:

```bash
find /tmp/minha-app -type f -mtime +7 -delete
```

---

# `-delete` e diretórios não vazios

O `find` não remove um diretório que ainda contenha objetos não removidos.

Para remover uma árvore inteira, filtros e ordem precisam ser analisados cuidadosamente.

Não utilize `-delete` como substituto automático de:

```bash
rm -rf
```

sem compreender quais objetos serão correspondidos.

---

# `-delete` implica processamento em profundidade

No GNU `find`, `-delete` ativa uma forma de processamento semelhante a:

```bash
-depth
```

O conteúdo é considerado antes do diretório.

Isso é necessário para remover arquivos antes de seus diretórios.

Essa interação também significa que `-delete` e `-prune` não combinam bem para excluir árvores da busca.

---

# A ação `-quit`

A ação:

```bash
-quit
```

encerra o `find` imediatamente após ser executada.

## Exemplo

```bash
find / -type f -name "config.php" -print -quit 2>/dev/null
```

Esse comando mostra o primeiro `config.php` encontrado e encerra a busca.

---

# Quando usar `-quit`?

É útil quando:

- Basta encontrar um resultado;
- Você quer apenas confirmar que algo existe;
- A árvore é muito grande;
- Deseja melhorar o desempenho;
- Está usando o `find` em uma condição de Shell Script.

---

# Verificar se existe um arquivo

```bash
resultado=$(find /opt -type f -name "backup.sh" -print -quit 2>/dev/null)

if [[ -n "$resultado" ]]; then
    printf 'Encontrado: %s\n' "$resultado"
else
    printf 'Arquivo não encontrado.\n'
fi
```

---

# Forma sem armazenar todos os resultados

```bash
if find /opt -type f -name "backup.sh" -print -quit 2>/dev/null |
   grep -q .; then
    echo "Encontrado."
else
    echo "Não encontrado."
fi
```

---

# Cuidado com o código de retorno

A ausência de resultados não significa necessariamente que o comando `find` falhou.

Normalmente:

```bash
find /diretorio -name "inexistente"
```

pode terminar com código `0` mesmo sem mostrar nada, desde que a execução em si não tenha encontrado um erro fatal.

Por isso, para testar a existência de resultado, examine a saída.

---

# Ações podem ser condições

No `find`, muitas ações também possuem um resultado lógico.

Por exemplo:

```bash
-print
```

normalmente retorna verdadeiro depois de imprimir.

Já:

```bash
-exec comando {} \;
```

pode ser considerado verdadeiro ou falso conforme o código de retorno do comando executado.

Isso se torna importante quando combinamos ações com:

- `-a`;
- `-o`;
- `!`;
- Parênteses.

Esses operadores serão estudados na próxima parte.

---

# Exemplo com `-exec` como teste

```bash
find . -type f -exec grep -q "senha" {} \; -print
```

Fluxo aproximado:

```text
É arquivo?
    ↓
Executar grep
    ↓
grep encontrou "senha"?
    ↓
Sim → -print
Não → não imprimir
```

Assim, apenas os arquivos em que o `grep` encontrou o texto serão mostrados.

Uma alternativa geralmente mais eficiente pode usar:

```bash
grep -Rl "senha" .
```

Mas o exemplo demonstra como o resultado lógico de `-exec` participa da expressão do `find`.

---

# Casos de uso em Administração Linux

## Mostrar arquivos grandes com detalhes

```bash
find /var -type f -size +100M -ls 2>/dev/null
```

---

## Compactar logs antigos

Primeiro:

```bash
find /var/log/minha-app -type f -name "*.log" -mtime +7 -print
```

Depois, caso esteja correto:

```bash
find /var/log/minha-app -type f -name "*.log" -mtime +7 \
-exec gzip {} +
```

---

## Corrigir permissões de scripts

```bash
find ./scripts -type f -name "*.sh" -exec chmod 750 {} +
```

---

# Casos de uso em Shell Script

## Processar nomes com segurança

```bash
find "$diretorio" -type f -print0 |
while IFS= read -r -d '' arquivo; do
    printf 'Processando: %s\n' "$arquivo"
done
```

---

## Encontrar apenas o primeiro arquivo

```bash
primeiro=$(find "$diretorio" -type f -name "*.conf" -print -quit)
```

---

## Executar um comando por lote

```bash
find "$diretorio" -type f -name "*.txt" \
-exec wc -l {} +
```

---

# Casos de uso em Pentest e CTF

## Analisar tipos de arquivos em `/opt`

```bash
find /opt -type f -exec file {} \; 2>/dev/null
```

---

## Exibir permissões e proprietários

```bash
find /opt /usr/local/bin -type f \
-printf "%M %u:%g %p\n" 2>/dev/null
```

---

## Procurar arquivos contendo uma palavra

```bash
find /var/www -type f \
\( -name "*.php" -o -name "*.conf" -o -name "*.env" \) \
-exec grep -nHi "password" {} + 2>/dev/null
```

Esse comando:

1. Pesquisa arquivos específicos;
2. Agrupa os resultados;
3. Executa `grep`;
4. Mostra linhas contendo `password`.

---

## Inspecionar scripts pertencentes ao root

```bash
find /opt /usr/local/bin \
-type f \
-user root \
\( -name "*.sh" -o -name "*.py" \) \
-exec ls -l {} \; \
-exec file {} \; \
2>/dev/null
```

---

# Erros comuns

## Usar `{}` fora de `-exec`

O marcador:

```text
{}
```

possui significado especial dentro de ações como:

```bash
-exec
-execdir
-ok
-okdir
```

Ele não é uma variável normal do Bash.

---

## Esquecer de finalizar `-exec`

Errado:

```bash
find . -type f -exec ls -l {}
```

Correto:

```bash
find . -type f -exec ls -l {} \;
```

ou:

```bash
find . -type f -exec ls -l {} +
```

---

## Não colocar aspas em `{}`

Em uma chamada direta de `-exec`, o `find` entrega cada caminho como argumento separado, portanto isto normalmente funciona:

```bash
-exec ls -l {} \;
```

Não é necessário escrever:

```bash
-exec ls -l "{}" \;
```

As aspas podem ser usadas, mas não resolvem problemas internos de comandos construídos com `sh -c`.

---

## Usar `-exec sh -c` com dados dentro do código

Evite construções como:

```bash
find . -type f -exec sh -c 'comando "{}"' \;
```

Inserir caminhos diretamente em código interpretado por shell pode causar erros ou injeção de comandos com nomes maliciosos.

Prefira passar os caminhos como argumentos:

```bash
find . -type f \
-exec sh -c 'for arquivo do
    printf "%s\n" "$arquivo"
done' sh {} +
```

---

## Usar `\;` quando `+` seria melhor

Isto:

```bash
find . -type f -exec ls -l {} \;
```

cria um processo por arquivo.

Quando o comando aceita vários caminhos, prefira:

```bash
find . -type f -exec ls -l {} +
```

---

## Usar `-delete` sem testar

Sempre siga:

```text
filtros + -print
        ↓
conferir resultados
        ↓
filtros + -delete
```

---

## Esquecer que a ordem importa

Seguro:

```bash
find . -type f -name "*.tmp" -delete
```

Perigoso:

```bash
find . -delete -type f -name "*.tmp"
```

---

## Utilizar `-print` com `xargs` sem proteção

Problemático:

```bash
find . -type f -print | xargs rm
```

Mais seguro:

```bash
find . -type f -print0 | xargs -0 rm
```

Ainda melhor para simples exclusão, depois de validar:

```bash
find . -type f -delete
```

---

# Boas práticas

1. Use `-print` antes de ações destrutivas.
2. Prefira `-exec ... {} +` quando o comando aceitar vários arquivos.
3. Use `-print0` com `xargs -0`.
4. Preserve nomes com espaços e caracteres especiais.
5. Evite construir comandos inseguros com `sh -c`.
6. Utilize caminhos absolutos em operações sensíveis.
7. Use `-quit` quando só precisar do primeiro resultado.
8. Leia o comando da esquerda para a direita.
9. Coloque filtros antes das ações.
10. Teste em um diretório pequeno antes de executar em `/`.

---

# Mini cheatsheet

| Objetivo | Comando |
|---|---|
| Mostrar caminhos | `find . -type f -print` |
| Separar por NUL | `find . -type f -print0` |
| Detalhes dos resultados | `find . -type f -ls` |
| Saída personalizada | `find . -type f -printf "%M %u:%g %p\n"` |
| Executar uma vez por arquivo | `find . -type f -exec comando {} \;` |
| Executar em lotes | `find . -type f -exec comando {} +` |
| Executar no diretório do arquivo | `find . -type f -execdir comando {} \;` |
| Pedir confirmação | `find . -type f -ok comando {} \;` |
| Remover correspondências | `find . -type f -name "*.tmp" -delete` |
| Parar no primeiro resultado | `find . -type f -print -quit` |
| Usar com `xargs` com segurança | `find . -type f -print0 \| xargs -0 comando` |

---

# Resumo

As ações determinam o que acontece com os resultados encontrados.

```text
-print    → mostra o caminho
-print0   → mostra usando NUL como separador
-printf   → cria saída personalizada
-ls       → mostra informações detalhadas
-exec     → executa outro comando
-execdir  → executa no diretório do resultado
-ok       → pede confirmação antes de executar
-okdir    → executa no diretório e pede confirmação
-delete   → remove o resultado
-quit     → encerra a busca
```

Uma regra importante é:

```text
primeiro filtrar
        ↓
depois executar a ação
```

Na próxima parte estudaremos:

- `-a`;
- `-o`;
- `!`;
- `-not`;
- Parênteses `\(` e `\)`;
- Precedência;
- Ações implícitas;
- Por que alguns comandos aparentemente corretos produzem resultados errados.

---

# Operadores do comando `find`

O `find` não trabalha apenas com filtros isolados.

Também podemos combinar várias condições utilizando operadores lógicos.

Os principais são:

```text
-a
-o
!
-not
\( \)
```

Eles representam:

| Operador | Significado |
|---|---|
| `-a` | AND — E |
| `-o` | OR — OU |
| `!` | NOT — NÃO |
| `-not` | Mesmo significado de `!` |
| `\( \)` | Agrupamento de expressões |

Esses operadores permitem construir buscas mais complexas.

---

# O operador AND

O operador:

```bash
-a
```

significa:

```text
AND
```

ou:

```text
E
```

Ele exige que as duas condições sejam verdadeiras.

---

# Exemplo

```bash
find . -type f -a -name "*.txt"
```

Tradução:

> Procure objetos que sejam arquivos comuns **e** tenham o nome terminado em `.txt`.

---

# AND implícito

Na maioria das vezes, não precisamos escrever:

```bash
-a
```

O `find` considera automaticamente que expressões colocadas lado a lado estão ligadas por AND.

Por isso, estes comandos são equivalentes:

```bash
find . -type f -a -name "*.txt"
```

```bash
find . -type f -name "*.txt"
```

A segunda forma é mais comum.

---

# Como pensar no AND

Considere:

```bash
find /var/www -type f -user www-data -name "*.php"
```

Internamente:

```text
-type f
AND
-user www-data
AND
-name "*.php"
```

O objeto precisa atender a todas as condições.

---

# Exemplo prático

Estrutura:

```text
index.php        → arquivo, usuário www-data
config.php       → arquivo, usuário root
uploads          → diretório, usuário www-data
readme.txt       → arquivo, usuário www-data
```

Comando:

```bash
find . -type f -user www-data -name "*.php"
```

Resultado:

```text
./index.php
```

Por quê?

| Objeto | Arquivo? | www-data? | Termina em `.php`? | Resultado |
|---|:---:|:---:|:---:|:---:|
| `index.php` | Sim | Sim | Sim | Mostrar |
| `config.php` | Sim | Não | Sim | Ignorar |
| `uploads` | Não | Sim | Não | Ignorar |
| `readme.txt` | Sim | Sim | Não | Ignorar |

---

# O operador OR

O operador:

```bash
-o
```

significa:

```text
OR
```

ou:

```text
OU
```

Ele exige que pelo menos uma das condições seja verdadeira.

---

# Exemplo simples

```bash
find . -name "*.zip" -o -name "*.tar"
```

Tradução:

> Procure objetos cujo nome termine em `.zip` ou `.tar`.

Possível resultado:

```text
./backup.zip
./site.tar
```

---

# Como o OR funciona

Fluxo simplificado:

```text
O nome termina em .zip?
        │
       Sim
        │
      Mostrar
```

Se não:

```text
O nome termina em .tar?
        │
       Sim
        │
      Mostrar
```

Caso nenhuma condição seja verdadeira:

```text
Ignorar
```

---

# O operador NOT

O operador:

```bash
!
```

inverte o resultado de uma expressão.

Também pode ser escrito como:

```bash
-not
```

---

# Exemplo

```bash
find . ! -name "*.txt"
```

Tradução:

> Mostre objetos cujo nome não termine em `.txt`.

---

# Outro exemplo

```bash
find . -type f ! -name "*.log"
```

Tradução:

> Procure arquivos comuns que não terminem em `.log`.

---

# `!` versus `-not`

Estes comandos são equivalentes:

```bash
find . ! -name "*.txt"
```

```bash
find . -not -name "*.txt"
```

A forma com:

```bash
!
```

é mais curta.

A forma:

```bash
-not
```

pode ser mais fácil de ler em comandos longos.

---

# Atenção ao shell

Em alguns shells interativos, o caractere:

```text
!
```

pode ser utilizado para expansão de histórico.

Por isso, dependendo do ambiente, pode ser necessário escrever:

```bash
\!
```

ou utilizar:

```bash
-not
```

Exemplo:

```bash
find . -type f -not -name "*.txt"
```

No Bash não interativo, essa expansão normalmente não ocorre da mesma forma.

---

# Agrupamento com parênteses

Os parênteses permitem agrupar expressões.

No `find`, escrevemos:

```bash
\( EXPRESSÕES \)
```

---

# Por que existe a barra invertida?

O shell interpreta os caracteres:

```text
(
)
```

como sintaxe própria.

Para impedir que o shell os processe antes de o `find` recebê-los, usamos:

```bash
\(
\)
```

Também seria possível usar aspas:

```bash
'('
')'
```

Mas a forma mais comum é:

```bash
\(
\)
```

---

# Exemplo de agrupamento

```bash
find . -type f \
\( -name "*.zip" -o -name "*.tar" \)
```

Tradução:

> Procure objetos que sejam arquivos comuns e cujo nome termine em `.zip` ou `.tar`.

Lógica:

```text
-type f
AND
(
    -name "*.zip"
    OR
    -name "*.tar"
)
```

---

# Por que os parênteses são importantes?

Observe este comando:

```bash
find . -type f -name "*.bak" -o -name "*.zip"
```

Muitas pessoas interpretam assim:

```text
É arquivo
AND
(
    termina em .bak
    OR
    termina em .zip
)
```

Mas não é necessariamente assim que o `find` avalia.

Devido à precedência dos operadores, ele interpreta como:

```text
(
    -type f
    AND
    -name "*.bak"
)
OR
-name "*.zip"
```

Isso significa que:

```text
-type f
```

aplica-se apenas ao primeiro lado.

---

# Consequência prática

Imagine existir um diretório chamado:

```text
arquivos.zip
```

O comando:

```bash
find . -type f -name "*.bak" -o -name "*.zip"
```

pode retornar:

```text
./backup.bak
./arquivos.zip
```

Mesmo que:

```text
./arquivos.zip
```

seja um diretório.

Isso acontece porque o segundo teste:

```bash
-name "*.zip"
```

não está necessariamente protegido pelo:

```bash
-type f
```

---

# Forma correta

```bash
find . -type f \
\( -name "*.bak" -o -name "*.zip" \)
```

Agora:

```text
-type f
```

aplica-se a todo o grupo.

---

# O comando dos backups corrigido

O comando:

```bash
find / -type f \
-name "*.bak" \
-o -name "*.old" \
-o -name "*.zip" \
-o -name "*.tar" \
-o -name "*.gz" \
2>/dev/null
```

possui um problema lógico.

A forma correta é:

```bash
find / -type f \
\( \
    -name "*.bak" \
    -o -name "*.old" \
    -o -name "*.zip" \
    -o -name "*.tar" \
    -o -name "*.gz" \
\) 2>/dev/null
```

---

# Tradução correta

```text
Procure em /
AND
considere apenas arquivos comuns
AND
(
    nome termina em .bak
    OU
    nome termina em .old
    OU
    nome termina em .zip
    OU
    nome termina em .tar
    OU
    nome termina em .gz
)
```

---

# Precedência dos operadores

Assim como em matemática, alguns operadores são avaliados antes de outros.

No `find`, a ordem geral é aproximadamente:

```text
1. Parênteses
2. NOT
3. AND
4. OR
```

Ou seja:

```text
\( \)
```

possui alta prioridade porque força o agrupamento.

Depois:

```text
!
```

Depois:

```text
-a
```

ou AND implícito.

Por último:

```text
-o
```

---

# Tabela de precedência

| Prioridade | Operador |
|---:|---|
| 1 | `\( ... \)` |
| 2 | `!` ou `-not` |
| 3 | `-a` ou AND implícito |
| 4 | `-o` |

---

# Comparação com matemática

Expressão matemática:

```text
2 + 3 × 4
```

A multiplicação acontece primeiro:

```text
2 + 12 = 14
```

Para alterar a ordem:

```text
(2 + 3) × 4 = 20
```

No `find`, os parênteses possuem função semelhante.

---

# Exemplo sem agrupamento

```bash
find . -type f -name "*.txt" -o -name "*.md"
```

Interpretação real aproximada:

```text
(
    -type f
    AND
    -name "*.txt"
)
OR
-name "*.md"
```

---

# Exemplo com agrupamento

```bash
find . -type f \
\( -name "*.txt" -o -name "*.md" \)
```

Interpretação:

```text
-type f
AND
(
    -name "*.txt"
    OR
    -name "*.md"
)
```

---

# Agrupando tipos diferentes

Podemos procurar arquivos ou links simbólicos.

```bash
find . \
\( -type f -o -type l \)
```

Tradução:

> Mostre objetos que sejam arquivos comuns ou links simbólicos.

---

# Agrupando nomes e tipos

```bash
find . \
\( -type f -name "*.txt" \) \
-o \
\( -type d -name "backup" \)
```

Tradução:

> Mostre arquivos `.txt` ou diretórios chamados `backup`.

---

# Negando um grupo

```bash
find . \
! \( -name "*.txt" -o -name "*.log" \)
```

Tradução:

> Mostre objetos que não terminem em `.txt` nem `.log`.

Lógica:

```text
NOT
(
    termina em .txt
    OR
    termina em .log
)
```

---

# Excluir vários diretórios

```bash
find . \
! -path "*/.git/*" \
! -path "*/node_modules/*" \
-type f
```

Tradução:

> Procure arquivos fora de `.git` e `node_modules`.

Esse comando filtra os resultados, mas ainda pode entrar nesses diretórios.

Para impedir a travessia, utilize `-prune`.

---

# Combinando `-prune`

```bash
find . \
\( -path "*/.git" -o -path "*/node_modules" \) \
-prune \
-o -type f -print
```

Lógica:

```text
(
    caminho é .git
    OU
    caminho é node_modules
)
AND
-prune
OR
(
    é arquivo
    AND
    -print
)
```

---

# Curto-circuito lógico

O `find` utiliza uma avaliação semelhante a curto-circuito.

Isso significa que algumas partes podem não ser avaliadas quando o resultado já é conhecido.

---

# AND com curto-circuito

Em:

```text
A AND B
```

se `A` for falso, não é necessário avaliar `B`.

Exemplo:

```bash
find . -type f -name "*.txt"
```

Se o objeto for um diretório:

```text
-type f → falso
```

O `find` não precisa verificar o nome para decidir o resultado do AND.

---

# OR com curto-circuito

Em:

```text
A OR B
```

se `A` for verdadeiro, não é necessário avaliar `B`.

Exemplo:

```bash
find . -name "*.zip" -o -name "*.tar"
```

Se o nome terminar em `.zip`, o segundo teste pode não precisar ser avaliado.

---

# Por que isso importa?

Porque ações também participam da expressão.

Exemplo:

```bash
find . -type f -print -o -type d -print
```

O comportamento depende dos resultados lógicos e da ordem de avaliação.

Comandos complexos devem ser agrupados claramente.

---

# Ação padrão e operadores

Quando nenhuma ação é informada, o `find` adiciona um comportamento semelhante a:

```bash
-print
```

Mas isso pode produzir surpresas quando usamos `-o`.

---

# Exemplo surpreendente

```bash
find . -name "*.txt" -o -name "*.md"
```

Normalmente os resultados correspondentes são impressos porque não foi informada outra ação.

Agora observe:

```bash
find . -name "*.txt" -print -o -name "*.md"
```

Nesse caso:

- Arquivos `.txt` executam `-print`;
- Para arquivos `.md`, o segundo lado pode ser verdadeiro;
- Mas não existe uma ação de impressão associada ao segundo lado.

Resultado:

```text
Os arquivos .md podem não ser mostrados.
```

---

# Forma correta

```bash
find . \
\( -name "*.txt" -o -name "*.md" \) \
-print
```

Agora o `-print` aplica-se ao grupo completo.

---

# Outro exemplo

Problemático:

```bash
find . -type f -name "*.bak" -delete -o -name "*.old" -delete
```

Melhor:

```bash
find . -type f \
\( -name "*.bak" -o -name "*.old" \) \
-delete
```

Primeiro agrupamos os nomes.

Depois aplicamos a ação.

---

# AND explícito versus implícito

Estas formas são equivalentes:

```bash
find . -type f -name "*.txt"
```

```bash
find . -type f -a -name "*.txt"
```

A forma implícita é mais comum.

Use `-a` explicitamente apenas quando ajudar na leitura de uma expressão complexa.

---

# Negação aplicada a uma expressão

```bash
find . -type f ! -name "*.txt"
```

Interpretação:

```text
-type f
AND
NOT -name "*.txt"
```

---

# Negação aplicada a um grupo

```bash
find . -type f \
! \( -name "*.txt" -o -name "*.log" \)
```

Interpretação:

```text
-type f
AND
NOT
(
    -name "*.txt"
    OR
    -name "*.log"
)
```

---

# Exemplo: backups e arquivos compactados

```bash
find /home /opt /var/backups \
-type f \
\( \
    -iname "*.bak" \
    -o -iname "*.old" \
    -o -iname "*.backup" \
    -o -iname "*.zip" \
    -o -iname "*.tar" \
    -o -iname "*.tar.gz" \
    -o -iname "*.tgz" \
\) \
2>/dev/null
```

---

# Exemplo: arquivos de configuração

```bash
find /var/www /opt /home \
-type f \
\( \
    -iname "*.conf" \
    -o -iname "*.cfg" \
    -o -iname "*.ini" \
    -o -iname "*.env" \
    -o -iname "*.yml" \
    -o -iname "*.yaml" \
    -o -iname "*.json" \
\) \
2>/dev/null
```

---

# Exemplo: excluir arquivos de log

```bash
find /var/www \
-type f \
! -name "*.log"
```

---

# Exemplo: arquivos recentes, grandes e compactados

```bash
find /home \
-type f \
-size +10M \
-mtime -7 \
\( \
    -iname "*.zip" \
    -o -iname "*.tar" \
    -o -iname "*.gz" \
\)
```

Tradução:

> Procure arquivos maiores que 10 MiB, modificados nos últimos sete dias e cujo nome termine em `.zip`, `.tar` ou `.gz`.

---

# Exemplo: arquivos do root graváveis

```bash
find / \
-type f \
-user root \
\( -writable -o -perm -002 \) \
2>/dev/null
```

Tradução:

> Procure arquivos pertencentes ao root que sejam graváveis pelo usuário atual ou possuam escrita para outros usuários.

---

# Exemplo: arquivos SUID ou SGID

```bash
find / \
-type f \
-perm /6000 \
2>/dev/null
```

Também poderíamos representar a lógica de forma mais explícita:

```bash
find / -type f \
\( -perm -4000 -o -perm -2000 \) \
2>/dev/null
```

---

# Exemplo: diretórios graváveis sem Sticky Bit

```bash
find / \
-type d \
-perm -002 \
! -perm -1000 \
2>/dev/null
```

Tradução:

> Procure diretórios graváveis por outros usuários que não possuam Sticky Bit.

---

# Exemplo em Shell Script

```bash
#!/usr/bin/env bash

diretorio=${1:-.}

find "$diretorio" \
-type f \
\( -name "*.txt" -o -name "*.md" \) \
-print0 |
while IFS= read -r -d '' arquivo; do
    printf 'Documento: %s\n' "$arquivo"
done
```

---

# Exemplo com ação diferente para cada grupo

```bash
find . \
\( -type f -name "*.txt" -printf "TXT: %p\n" \) \
-o \
\( -type f -name "*.sh" -printf "SCRIPT: %p\n" \)
```

Nesse caso:

- Arquivos `.txt` recebem uma saída;
- Scripts `.sh` recebem outra saída.

---

# Como construir comandos complexos

Use esta ordem mental:

```text
1. Onde procurar?
2. Qual tipo de objeto?
3. Quais filtros obrigatórios?
4. Quais alternativas?
5. O que deve ser negado?
6. Quais partes precisam de parênteses?
7. Qual ação será executada?
```

---

# Modelo de construção

```bash
find DIRETORIO \
FILTROS_OBRIGATORIOS \
\( ALTERNATIVA_1 -o ALTERNATIVA_2 \) \
NEGAÇÕES \
AÇÃO
```

---

# Exemplo preenchido

```bash
find /var/www \
-type f \
-user www-data \
\( -name "*.php" -o -name "*.conf" \) \
! -path "*/cache/*" \
-print
```

---

# Como ler esse comando

```text
Procure em /var/www

E

considere apenas arquivos

E

pertencentes ao usuário www-data

E

cujo nome termine em .php ou .conf

E

que não estejam em caminhos de cache

E

mostre os resultados
```

---

# Erros comuns

## Não agrupar alternativas

Problemático:

```bash
find . -type f -name "*.bak" -o -name "*.zip"
```

Correto:

```bash
find . -type f \
\( -name "*.bak" -o -name "*.zip" \)
```

---

## Esquecer de escapar os parênteses

Errado:

```bash
find . ( -name "*.txt" -o -name "*.md" )
```

O shell pode gerar erro de sintaxe.

Correto:

```bash
find . \( -name "*.txt" -o -name "*.md" \)
```

---

## Colocar `-print` em apenas um lado

Problemático:

```bash
find . -name "*.txt" -print -o -name "*.md"
```

Correto:

```bash
find . \
\( -name "*.txt" -o -name "*.md" \) \
-print
```

---

## Confiar em comandos longos sem traduzi-los

Antes de executar, leia o comando em português.

Se não conseguir explicar cada grupo, ainda não está pronto para executá-lo.

---

## Misturar filtros e ações sem agrupamento

Problemático:

```bash
find . -name "*.bak" -delete -o -name "*.old" -print
```

Mais claro:

```bash
find . -type f \
\( -name "*.bak" -o -name "*.old" \) \
-print
```

Depois de conferir:

```bash
find . -type f \
\( -name "*.bak" -o -name "*.old" \) \
-delete
```

---

# Boas práticas

1. Agrupe alternativas com `\( ... \)`.
2. Coloque os filtros comuns antes do grupo.
3. Aplique a ação depois do grupo.
4. Use indentação em comandos multilinha.
5. Traduza o comando antes de executá-lo.
6. Teste com `-print` antes de `-delete`.
7. Evite depender da ação implícita em expressões complexas.
8. Use `-print` explicitamente quando houver `-o`.
9. Lembre que AND possui prioridade maior que OR.
10. Prefira clareza a comandos excessivamente curtos.

---

# Formatação recomendada

Em vez de:

```bash
find / -type f \( -name "*.bak" -o -name "*.old" -o -name "*.zip" \) 2>/dev/null
```

prefira:

```bash
find / \
-type f \
\( \
    -name "*.bak" \
    -o -name "*.old" \
    -o -name "*.zip" \
\) \
2>/dev/null
```

Essa forma facilita:

- Leitura;
- Manutenção;
- Detecção de erros;
- Inclusão de novos filtros;
- Documentação no Obsidian.

---

# Mini cheatsheet

| Objetivo | Sintaxe |
|---|---|
| AND explícito | `EXP1 -a EXP2` |
| AND implícito | `EXP1 EXP2` |
| OR | `EXP1 -o EXP2` |
| NOT | `! EXP` |
| NOT alternativo | `-not EXP` |
| Agrupar | `\( EXP \)` |
| Arquivo TXT ou MD | `-type f \( -name "*.txt" -o -name "*.md" \)` |
| Excluir logs | `-type f ! -name "*.log"` |
| Negar grupo | `! \( -name "*.txt" -o -name "*.log" \)` |
| SUID ou SGID | `-type f -perm /6000` |

---

# Resumo

Os operadores lógicos do `find` permitem combinar expressões.

```text
-a   → AND
-o   → OR
!    → NOT
-not → NOT
\( \) → agrupamento
```

A regra mais importante desta parte é:

```text
AND possui prioridade maior que OR.
```

Por isso, alternativas devem ser agrupadas:

```bash
find . -type f \
\( -name "*.bak" -o -name "*.zip" \)
```

Outra regra importante:

```text
filtros primeiro
ação depois
```

Na próxima parte vamos concluir a anotação com:

- Exemplos reais para Linux;
- Exemplos para Shell Script;
- Exemplos para Pentest e CTF;
- Performance;
- Portabilidade;
- Erros comuns gerais;
- Checklist;
- Cheatsheet completo do `find`.

	