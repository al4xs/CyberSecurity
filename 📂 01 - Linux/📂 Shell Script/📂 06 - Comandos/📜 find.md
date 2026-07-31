
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

