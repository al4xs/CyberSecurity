
---

# Cheatsheet de Flags Linux

> Consulta rápida das flags mais comuns utilizadas em comandos Linux e Unix.

---

# Índice

## A

- [`-a`](#-a)
- [`-A`](#-a-1)

## C

- [`-c`](#-c)

## D

- [`-d`](#-d)

## E

- [`-e`](#-e)

## F

- [`-f`](#-f)

## H

- [`-h`](#-h)

## I

- [`-i`](#-i)
- [`-I`](#-i-1)

## L

- [`-l`](#-l)
- [`-L`](#-l-1)

## N

- [`-n`](#-n)

## O

- [`-o`](#-o)

## P

- [`-p`](#-p)
- [`-P`](#-p-1)

## Q

- [`-q`](#-q)

## R

- [`-r`](#-r)
- [`-R`](#-r-1)

## S

- [`-s`](#-s)

## T

- [`-t`](#-t)

## U

- [`-u`](#-u)

## V

- [`-v`](#-v)

## W

- [`-w`](#-w)

## X

- [`-x`](#-x)

---

# `-a`

| Item | Informação |
|------|------------|
| Significado | **All** |
| Uso comum | Mostrar todos os arquivos, incluindo ocultos |
| Comandos | `ls`, `cp`, `grep` |
| Importância | ⭐⭐⭐⭐⭐ |

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
| Significado | **Almost All** |
| Uso comum | Lista arquivos ocultos, exceto `.` e `..` |
| Comandos | `ls` |
| Importância | ⭐⭐⭐⭐☆ |

### Exemplo

```bash
ls -A
```

---

# `-f`

| Item | Informação |
|------|------------|
| Significado | **Force** |
| Uso comum | Forçar uma operação |
| Comandos | `rm`, `cp`, `mv` |
| Importância | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
rm -f arquivo.txt
```

⚠ Ignora confirmações e erros simples.

---

# `-v`

| Item | Informação |
|------|------------|
| Significado | **Verbose** |
| Uso comum | Exibir detalhes da execução |
| Comandos | `cp`, `mv`, `curl`, `ssh` |
| Importância | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
cp -v arquivo.txt backup/
```

Saída

```text
'arquivo.txt' -> 'backup/arquivo.txt'
```

---

# Combinações Mais Utilizadas

| Comando     | Significado                                |
| ----------- | ------------------------------------------ |
| `ls -lah`   | Long + All + Human Readable                |
| `rm -rf`    | Recursive + Force                          |
| `cp -av`    | Archive + Verbose                          |
| `grep -rin` | Recursive + Ignore Case + Número da linha  |
| `find -L`   | Seguir links simbólicos                    |
| `curl -LO`  | Seguir redirect + Salvar com nome original |
| `tar -czvf` | Create + gzip + Verbose + File             |

---

# Convenções

| Letra | Geralmente significa |
|--------|----------------------|
| `a` | All |
| `c` | Count / Create |
| `d` | Directory |
| `e` | Expression |
| `f` | Force |
| `h` | Help / Human |
| `i` | Interactive |
| `l` | Long |
| `n` | Number |
| `o` | Output |
| `p` | Preserve |
| `q` | Quiet |
| `r` | Recursive |
| `s` | Silent |
| `t` | Target / Time |
| `u` | Update |
| `v` | Verbose |
| `w` | Word |
| `x` | Execute |

---

# Combinações Opostas

| Flag | Oposta |
|------|--------|
| `-v` | `-q` |
| `-L` | `-P` |
| `-i` | `-f` |

---

# Mais utilizadas

⭐⭐⭐⭐⭐

```
-a
-l
-h
-r
-f
-i
-v
```

⭐⭐⭐⭐☆

```
-p
-o
-L
-q
-s
-n
```

⭐⭐⭐☆☆

```
-A
-I
-P
-d
-t
-u
-w
-x
```