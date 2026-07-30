
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

