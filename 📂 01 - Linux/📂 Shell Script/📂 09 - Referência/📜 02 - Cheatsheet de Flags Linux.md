
---

# Cheatsheet de Flags Linux

> Consulta rápida das flags mais comuns utilizadas em comandos Linux e Unix.

---

# `-a`

| Item | Informação |
|------|------------|
| **Significado** | **All** (Todos) |
| **Uso comum** | Mostrar todos os arquivos, incluindo os ocultos |
| **Comandos comuns** | `ls`, `cp`, `grep` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
ls -a
```

**Saída**

```text
.
..
.bashrc
.profile
Documentos
Downloads
```

---

# `-A`

| Item | Informação |
|------|------------|
| **Significado** | **Almost All** (Quase todos) |
| **Uso comum** | Mostrar arquivos ocultos, exceto `.` e `..` |
| **Comandos comuns** | `ls` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Exemplo

```bash
ls -A
```

**Saída**

```text
.bashrc
.profile
Documentos
Downloads
```

---

# `-l`

| Item | Informação |
|------|------------|
| **Significado** | **Long** (Formato longo) |
| **Uso comum** | Exibir informações detalhadas sobre arquivos |
| **Comandos comuns** | `ls` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
ls -l
```

**Saída**

```text
drwxr-xr-x 2 user user 4096 Jul 20 10:30 Documentos
-rw-r--r-- 1 user user  842 Jul 20 09:10 notas.txt
```

---

# `-h`

| Item | Informação |
|------|------------|
| **Significado** | **Human Readable** (Legível para humanos) |
| **Uso comum** | Exibir tamanhos em KB, MB, GB etc. |
| **Comandos comuns** | `ls`, `du`, `df` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
ls -lh
```

**Saída**

```text
-rw-r--r-- 1 user user 1.5K notas.txt
-rw-r--r-- 1 user user 2.3M backup.tar
```

---

# Convenções Mais Comuns

| Letra | Inglês | Português |
|-------|---------|------------|
| `a` | All | Todos |
| `c` | Create / Count | Criar / Contar |
| `d` | Directory | Diretório |
| `e` | Expression | Expressão |
| `f` | Force | Forçar |
| `h` | Help / Human Readable | Ajuda / Legível para humanos |
| `i` | Interactive | Interativo |
| `l` | Long | Formato longo |
| `n` | Number | Número |
| `o` | Output | Saída |
| `p` | Preserve / Parents | Preservar / Pais |
| `q` | Quiet | Silencioso |
| `r` | Recursive | Recursivo |
| `s` | Silent | Silencioso |
| `t` | Target / Time | Destino / Tempo |
| `u` | Update | Atualizar |
| `v` | Verbose | Modo detalhado |
| `w` | Word | Palavra |
| `x` | Execute | Executar |

---

# `-r`

| Item | Informação |
|------|------------|
| **Significado** | **Recursive / Reverse** (Recursivo / Inverter) |
| **Uso comum** | Processar diretórios recursivamente ou inverter a ordenação, dependendo do comando |
| **Comandos comuns** | `cp`, `rm`, `grep`, `sort` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
cp -r projetos/ backup/
```

**Saída**

```text
Diretório "projetos" copiado recursivamente para "backup/".
```

---

# `-R`

| Item | Informação |
|------|------------|
| **Significado** | **Recursive** (Recursivo) |
| **Uso comum** | Executar operações recursivamente em diretórios |
| **Comandos comuns** | `ls`, `chmod`, `chown` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Exemplo

```bash
chmod -R 755 site/
```

**Saída**

```text
Permissões alteradas para todos os arquivos e diretórios de "site/".
```

---

# `-i`

| Item | Informação |
|------|------------|
| **Significado** | **Interactive** (Interativo) |
| **Uso comum** | Solicitar confirmação antes de executar ações potencialmente destrutivas |
| **Comandos comuns** | `rm`, `cp`, `mv` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
rm -i arquivo.txt
```

**Saída**

```text
rm: remover arquivo regular 'arquivo.txt'?
```

---

# `-I`

| Item | Informação |
|------|------------|
| **Significado** | **Ignore / Interactive** (Ignorar / Interativo especial) |
| **Uso comum** | Ignorar arquivos ou solicitar uma única confirmação em operações grandes, dependendo do comando |
| **Comandos comuns** | `grep`, `rm` |
| **Importância** | ⭐⭐⭐☆☆ |

### Exemplo

```bash
grep -I "senha" backup.bin
```

**Saída**

```text
Arquivos binários são ignorados durante a busca.
```

---

# `-n`

| Item | Informação |
|------|------------|
| **Significado** | **Number / Numeric** (Número / Numérico) |
| **Uso comum** | Exibir numeração de linhas ou utilizar valores numéricos |
| **Comandos comuns** | `cat`, `grep`, `head`, `tail` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Exemplo

```bash
grep -n "erro" sistema.log
```

**Saída**

```text
15: Erro ao iniciar o serviço.
42: Erro de autenticação.
```

---

# Combinações Mais Utilizadas

| Comando | Expansão |
|----------|----------|
| `cp -r` | `-r` Recursive (Recursivo) |
| `cp -ri` | `-r` Recursive (Recursivo) + `-i` Interactive (Interativo) |
| `rm -ri` | `-r` Recursive (Recursivo) + `-i` Interactive (Interativo) |
| `chmod -R` | `-R` Recursive (Recursivo) |
| `grep -rn` | `-r` Recursive (Recursivo) + `-n` Number (Número da linha) |
| `grep -rIn` | `-r` Recursive (Recursivo) + `-I` Ignore Binary Files (Ignorar arquivos binários) + `-n` Number (Número da linha) |

---

# `-v`

| Item | Informação |
|------|------------|
| **Significado** | **Verbose** (Modo detalhado) |
| **Uso comum** | Exibir informações detalhadas durante a execução |
| **Comandos comuns** | `cp`, `mv`, `curl`, `ssh`, `tar` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
cp -v arquivo.txt backup/
```

**Saída**

```text
'arquivo.txt' -> 'backup/arquivo.txt'
```

---

# `-c`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `grep`, `tar`, `bash`, `wc` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Count** (Contar) | `grep`, `wc` |
| **Create** (Criar) | `tar` |
| **Command** (Executar comando) | `bash`, `sh` |

### Exemplo

```bash
grep -c "erro" sistema.log
```

**Saída**

```text
12
```

---

# `-f`

| Item | Informação |
|------|------------|
| **Significado** | **Force** (Forçar) |
| **Uso comum** | Executar uma operação sem solicitar confirmação |
| **Comandos comuns** | `rm`, `cp`, `mv`, `grep` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
rm -f arquivo.txt
```

**Saída**

```text
Arquivo removido sem solicitar confirmação.
```

---

# `-p`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `mkdir`, `cp`, `ssh`, `scp` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Parents** (Criar diretórios pais) | `mkdir` |
| **Preserve** (Preservar atributos) | `cp` |
| **Port** (Porta de conexão) | `ssh`, `scp` |

### Exemplo 1

```bash
mkdir -p projetos/bash/scripts
```

**Saída**

```text
Diretórios criados, incluindo os diretórios intermediários.
```

### Exemplo 2

```bash
ssh -p 2222 usuario@192.168.0.10
```

**Saída**

```text
Conectando à porta 2222...
```

---

# Combinações Mais Utilizadas

| Comando | Expansão |
|----------|----------|
| `cp -pv` | `-p` Preserve (Preservar atributos) + `-v` Verbose (Modo detalhado) |
| `rm -fv` | `-f` Force (Forçar) + `-v` Verbose (Modo detalhado) |
| `mkdir -p` | `-p` Parents (Criar diretórios pais) |
| `tar -cvf` | `-c` Create (Criar) + `-v` Verbose (Modo detalhado) + `-f` File (Arquivo) |
| `grep -cv` | `-c` Count (Contar ocorrências) + `-v` Invert Match (Inverter correspondência)¹ |

> **¹ Observação:** No comando `grep`, a flag `-v` possui um significado diferente do convencional: **Invert Match** (Inverter correspondência), exibindo as linhas que **não** correspondem ao padrão pesquisado.

---

# `-P`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `cp`, `mv`, `pwd` |
| **Importância** | ⭐⭐⭐☆☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **No Dereference** (Não seguir links simbólicos) | `cp`, `mv` |
| **Physical** (Caminho físico) | `pwd` |

### Exemplo

```bash
pwd -P
```

**Saída**

```text
/home/user/projetos
```

---

# `-o`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `sort`, `find`, `ssh`, `curl` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Output** (Arquivo de saída) | `curl` |
| **OR** (Operador lógico OU) | `find` |
| **Option** (Opção de configuração) | `ssh` |
| **Output Order** (Ordenação da saída) | `sort` |

### Exemplo

```bash
curl -o pagina.html https://example.com
```

**Saída**

```text
Arquivo salvo como "pagina.html".
```

---

# `-e`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `echo`, `grep`, `sed` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Enable Escapes** (Interpretar sequências de escape) | `echo` |
| **Expression** (Expressão) | `grep`, `sed` |

### Exemplo

```bash
echo -e "Linha 1\nLinha 2"
```

**Saída**

```text
Linha 1
Linha 2
```

---

# `-d`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `ls`, `grep`, `tar`, `cut` |
| **Importância** | ⭐⭐⭐☆☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Directory** (Diretório) | `ls` |
| **Directories** (Processar diretórios) | `grep` |
| **Delimiter** (Delimitador) | `cut` |
| **Difference** (Comparação) | Alguns utilitários |

### Exemplo

```bash
cut -d ":" -f1 /etc/passwd
```

**Saída**

```text
root
daemon
bin
sys
```

---

# `-L`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `find`, `cp`, `curl`, `ls` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Follow Symbolic Links** (Seguir links simbólicos) | `find`, `cp` |
| **Follow Redirects** (Seguir redirecionamentos HTTP) | `curl` |
| **Dereference Links** (Exibir o destino do link) | `ls` |

### Exemplo

```bash
find -L . -name "*.sh"
```

**Saída**

```text
./scripts/backup.sh
./scripts/install.sh
```

---

# Combinações Mais Utilizadas

| Comando | Expansão |
|----------|----------|
| `curl -LO` | `-L` Follow Redirects (Seguir redirecionamentos) + `-O` Remote Name (Salvar com o nome original) |
| `find -L` | `-L` Follow Symbolic Links (Seguir links simbólicos) |
| `cut -d ":"` | `-d` Delimiter (Delimitador) |
| `grep -e` | `-e` Expression (Expressão) |
| `echo -e` | `-e` Enable Escapes (Interpretar sequências de escape) |
| `pwd -P` | `-P` Physical (Caminho físico) |

---

# `-s`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `grep`, `curl`, `du`, `ln` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Silent** (Modo silencioso) | `curl` |
| **Summarize** (Resumir) | `du` |
| **Suppress Messages** (Ocultar mensagens) | `grep` |
| **Symbolic** (Link simbólico) | `ln` |

### Exemplo

```bash
curl -s https://example.com
```

**Saída**

```text
<html>...</html>
```

---

# `-t`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `ls`, `tar`, `sort` |
| **Importância** | ⭐⭐⭐☆☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Time** (Ordenar por data/hora) | `ls` |
| **File** (Arquivo no terminal) | `tar` |
| **Temporary Directory** (Diretório temporário) | Alguns utilitários |

### Exemplo

```bash
ls -lt
```

**Saída**

```text
Arquivos ordenados pela data de modificação.
```

---

# `-u`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `sort`, `touch`, `ls` |
| **Importância** | ⭐⭐⭐☆☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Unique** (Único) | `sort` |
| **Access Time** (Tempo de acesso) | `ls` |
| **Update Access Time** (Atualizar tempo de acesso) | `touch` |

### Exemplo

```bash
sort -u nomes.txt
```

**Saída**

```text
Lista ordenada sem valores duplicados.
```

---

# `-w`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `grep`, `wc`, `fmt` |
| **Importância** | ⭐⭐⭐☆☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Word** (Palavra inteira) | `grep` |
| **Width** (Largura) | `fmt` |
| **Words** (Contar palavras) | Alguns utilitários |

### Exemplo

```bash
grep -w "root" /etc/passwd
```

**Saída**

```text
root:x:0:0:root:/root:/bin/bash
```

---

# `-x`

| Item | Informação |
|------|------------|
| **Uso comum** | O significado depende do comando |
| **Comandos comuns** | `chmod`, `find`, `bash` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Significados

| Significado | Comandos |
|-------------|----------|
| **Execute** (Executar) | `chmod` |
| **One File System** (Mesmo sistema de arquivos) | `find` |
| **Debug Mode** (Modo de depuração) | `bash` |

### Exemplo

```bash
bash -x script.sh
```

**Saída**

```text
+ echo "Iniciando..."
+ mkdir backup
+ cp arquivo.txt backup/
```

---

# Combinações Mais Utilizadas

| Comando | Expansão |
|----------|----------|
| `ls -lah` | `-l` Long (Formato longo) + `-a` All (Todos) + `-h` Human Readable (Legível para humanos) |
| `ls -lt` | `-l` Long (Formato longo) + `-t` Time (Ordenar por data) |
| `rm -rf` | `-r` Recursive (Recursivo) + `-f` Force (Forçar) |
| `cp -av` | `-a` Archive (Modo arquivo) + `-v` Verbose (Modo detalhado) |
| `grep -rin` | `-r` Recursive (Recursivo) + `-i` Ignore Case (Ignorar maiúsculas/minúsculas) + `-n` Number (Número da linha) |
| `grep -rw` | `-r` Recursive (Recursivo) + `-w` Word (Palavra inteira) |
| `find -L` | `-L` Follow Symbolic Links (Seguir links simbólicos) |
| `curl -LO` | `-L` Follow Redirects (Seguir redirecionamentos) + `-O` Remote Name (Salvar com o nome original) |
| `tar -czvf` | `-c` Create (Criar) + `-z` Gzip (Compactar com gzip) + `-v` Verbose (Modo detalhado) + `-f` File (Arquivo) |
| `chmod -R 755` | `-R` Recursive (Recursivo) |

---

# Flags por Comando

## `ls`

| Flag | Função |
|------|--------|
| `-a` | Mostrar arquivos ocultos |
| `-A` | Mostrar ocultos, exceto `.` e `..` |
| `-l` | Formato longo |
| `-h` | Tamanho legível |
| `-R` | Listagem recursiva |
| `-t` | Ordenar por data |
| `-u` | Utilizar tempo de acesso |

---

## `grep`

| Flag | Função |
|------|--------|
| `-r` | Busca recursiva |
| `-i` | Ignorar maiúsculas/minúsculas |
| `-n` | Mostrar número da linha |
| `-c` | Contar ocorrências |
| `-v` | Inverter correspondência |
| `-w` | Palavra inteira |
| `-e` | Expressão |
| `-I` | Ignorar arquivos binários |

---

## `find`

| Flag | Função |
|------|--------|
| `-L` | Seguir links simbólicos |
| `-P` | Não seguir links simbólicos |
| `-o` | Operador lógico OU |

---

## `cp`

| Flag | Função |
|------|--------|
| `-a` | Modo arquivo |
| `-r` | Copiar recursivamente |
| `-p` | Preservar atributos |
| `-v` | Modo detalhado |
| `-f` | Forçar cópia |

---

## `rm`

| Flag | Função |
|------|--------|
| `-r` | Remover recursivamente |
| `-f` | Forçar remoção |
| `-i` | Solicitar confirmação |

---

## `curl`

| Flag | Função |
|------|--------|
| `-L` | Seguir redirecionamentos |
| `-O` | Salvar com o nome original |
| `-o` | Definir nome do arquivo |
| `-s` | Modo silencioso |

---

## `tar`

| Flag | Função |
|------|--------|
| `-c` | Criar arquivo |
| `-z` | Compactar com gzip |
| `-v` | Modo detalhado |
| `-f` | Especificar arquivo |

---

## `chmod`

| Flag | Função |
|------|--------|
| `-R` | Alterar permissões recursivamente |

---

# Prioridade de Estudo

| Nível | Flags |
|--------|-------|
| ⭐⭐⭐⭐⭐ Essenciais | `-a` `-l` `-h` `-r` `-f` `-i` `-v` |
| ⭐⭐⭐⭐☆ Muito comuns | `-A` `-c` `-n` `-o` `-L` `-p` `-s` `-x` |
| ⭐⭐⭐☆☆ Úteis | `-P` `-d` `-e` `-I` `-t` `-u` `-w` |

---
