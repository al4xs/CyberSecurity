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

# `-f`

| Item | Informação |
|------|------------|
| **Significado** | **Force** (Forçar) |
| **Uso comum** | Executar uma ação sem solicitar confirmação |
| **Comandos comuns** | `rm`, `cp`, `mv` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
rm -f arquivo.txt
```

---

# `-v`

| Item | Informação |
|------|------------|
| **Significado** | **Verbose** (Modo detalhado) |
| **Uso comum** | Mostrar detalhes da execução |
| **Comandos comuns** | `cp`, `mv`, `curl`, `ssh` |
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

# Combinações Mais Utilizadas

| Comando | Expansão |
|----------|----------|
| `ls -lah` | `-l` Long (Formato longo) + `-a` All (Todos) + `-h` Human Readable (Legível para humanos) |
| `rm -rf` | `-r` Recursive (Recursivo) + `-f` Force (Forçar) |
| `cp -av` | `-a` Archive (Modo arquivo) + `-v` Verbose (Modo detalhado) |
| `grep -rin` | `-r` Recursive (Recursivo) + `-i` Ignore Case (Ignorar maiúsculas/minúsculas) + `-n` Number (Número da linha) |
| `curl -LO` | `-L` Follow Redirects (Seguir redirecionamentos) + `-O` Remote Name (Salvar com o nome original) |
| `tar -czvf` | `-c` Create (Criar) + `-z` Gzip (Compactar com gzip) + `-v` Verbose (Modo detalhado) + `-f` File (Arquivo) |

---

# Convenções Mais Comuns

| Letra | Inglês | Português |
|-------|---------|------------|
| `a` | All | Todos |
| `c` | Create / Count | Criar / Contar |
| `d` | Directory | Diretório |
| `e` | Expression | Expressão |
| `f` | Force | Forçar |
| `h` | Help / Human | Ajuda / Legível para humanos |
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

# Prioridade de Estudo

| Nível | Flags |
|--------|-------|
| ⭐⭐⭐⭐⭐ Essenciais | `-a` `-l` `-h` `-r` `-f` `-i` `-v` |
| ⭐⭐⭐⭐☆ Muito comuns | `-p` `-o` `-L` `-q` `-s` `-n` |
| ⭐⭐⭐☆☆ Úteis | `-A` `-I` `-P` `-d` `-t` `-u` `-w` `-x` |