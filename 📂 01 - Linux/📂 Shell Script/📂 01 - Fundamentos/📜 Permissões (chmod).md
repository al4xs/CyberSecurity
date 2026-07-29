
---
# Permissões (chmod)

## O que é?

O comando `chmod` (**change mode**) é utilizado para alterar as permissões de acesso de arquivos e diretórios no Linux.

As permissões determinam quem pode:

- Ler um arquivo.
- Escrever em um arquivo.
- Executar um arquivo ou acessar um diretório.

---

# Como visualizar as permissões

Utilize o comando:

```bash
ls -l
```

Exemplo:

```text
-rwxr-xr-- 1 allan users 1520 Jun 20 10:30 script.sh
```

A primeira coluna representa as permissões.

```text
-rwxr-xr--
```

---

# Entendendo as permissões

```
-rwxr-xr--
```

| Parte | Significado |
|--------|-------------|
| `-` | Tipo do arquivo |
| `rwx` | Permissões do proprietário (Owner) |
| `r-x` | Permissões do grupo (Group) |
| `r--` | Permissões dos outros usuários (Others) |

---

# Tipos de arquivo

| Símbolo | Tipo |
|---------|------|
| `-` | Arquivo |
| `d` | Diretório |
| `l` | Link simbólico |

---

# Tipos de permissão

| Símbolo | Nome | Valor |
|---------|------|-------|
| `r` | Read (Leitura) | 4 |
| `w` | Write (Escrita) | 2 |
| `x` | Execute (Execução) | 1 |

---

# Usuários

As permissões são divididas em três categorias.

| Grupo | Representação |
|--------|---------------|
| Proprietário | User (u) |
| Grupo | Group (g) |
| Outros usuários | Others (o) |

---

# Método Numérico

Cada permissão possui um valor.

| Permissão | Valor |
|-----------|------:|
| r | 4 |
| w | 2 |
| x | 1 |

Somando os valores obtemos a permissão desejada.

## Exemplos

### rwx

```
4 + 2 + 1 = 7
```

### rw-

```
4 + 2 = 6
```

### r-x

```
4 + 1 = 5
```

### r--

```
4
```

### ---

```
0
```

---

# Permissões mais comuns

| Número | Permissão |
|---------|------------|
| 777 | rwxrwxrwx |
| 755 | rwxr-xr-x |
| 750 | rwxr-x--- |
| 700 | rwx------ |
| 644 | rw-r--r-- |
| 600 | rw------- |

---

# Utilizando chmod

## Dar permissão de execução

```bash
chmod +x script.sh
```

Agora o arquivo poderá ser executado.

---

## Remover permissão de execução

```bash
chmod -x script.sh
```

---

## Definir permissões usando números

```bash
chmod 755 script.sh
```

Resultado

```text
rwxr-xr-x
```

---

```bash
chmod 644 arquivo.txt
```

Resultado

```text
rw-r--r--
```

---

```bash
chmod 600 segredo.txt
```

Resultado

```text
rw-------
```

---

# Método Simbólico

Também é possível utilizar letras.

| Letra | Significado |
|--------|-------------|
| u | Usuário (Owner) |
| g | Grupo |
| o | Outros |
| a | Todos |

Operadores

| Operador | Ação |
|-----------|------|
| + | Adiciona |
| - | Remove |
| = | Define exatamente |

---

## Exemplos

Adicionar execução ao proprietário.

```bash
chmod u+x script.sh
```

Adicionar leitura ao grupo.

```bash
chmod g+r arquivo.txt
```

Remover escrita de outros usuários.

```bash
chmod o-w arquivo.txt
```

Dar leitura para todos.

```bash
chmod a+r arquivo.txt
```

---

# chmod em diretórios

As permissões possuem um significado diferente em diretórios.

| Permissão | Função |
|-----------|--------|
| r | Listar arquivos |
| w | Criar, remover ou renomear arquivos (dependendo das demais permissões) |
| x | Entrar (acessar) o diretório |

Exemplo

```bash
chmod 755 projetos
```

---

# Alterando permissões recursivamente

Para alterar um diretório e todo seu conteúdo:

```bash
chmod -R 755 projetos
```

O parâmetro `-R` significa **recursivo**.

---

# Exemplos práticos

## Tornar um script executável

```bash
chmod +x backup.sh
```

Executando:

```bash
./backup.sh
```

---

## Proteger um arquivo confidencial

```bash
chmod 600 senhas.txt
```

Somente o proprietário poderá ler e escrever.

---

## Criar um diretório compartilhado

```bash
chmod 775 compartilhado
```

---

# Boas práticas

- Utilize `755` para scripts executáveis.
- Utilize `644` para arquivos comuns.
- Utilize `600` para arquivos sensíveis.
- Evite utilizar `777`, pois concede acesso total a qualquer usuário.

---

# Erros comuns

## Esquecer de dar permissão de execução

```bash
./script.sh
```

Erro

```text
Permission denied
```

Solução

```bash
chmod +x script.sh
```

---

## Utilizar 777 sem necessidade

```bash
chmod 777 arquivo
```

Essa permissão permite leitura, escrita e execução para qualquer usuário, aumentando o risco de alterações indevidas.

---

# Resumo

## Visualizar permissões

```bash
ls -l
```

---

## Tornar executável

```bash
chmod +x script.sh
```

---

## Remover execução

```bash
chmod -x script.sh
```

---

## Definir permissões

```bash
chmod 755 script.sh
```

---

## Alterar permissões recursivamente

```bash
chmod -R 755 pasta
```

---

# Tabela de referência

| Comando              | Descrição                                 |
| -------------------- | ----------------------------------------- |
| `chmod +x arquivo`   | Adiciona permissão de execução            |
| `chmod -x arquivo`   | Remove permissão de execução              |
| `chmod 755 arquivo`  | Define permissões rwxr-xr-x               |
| `chmod 644 arquivo`  | Define permissões rw-r--r--               |
| `chmod 600 arquivo`  | Apenas o proprietário pode ler e escrever |
| `chmod -R 755 pasta` | Altera permissões recursivamente          |
| `ls -l`              | Exibe as permissões dos arquivos          |