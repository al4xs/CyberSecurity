
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

