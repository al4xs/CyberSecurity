
---
# `curl`

## O que é?

O `curl` (Client URL) é uma ferramenta de linha de comando utilizada para transferir dados entre um cliente e um servidor utilizando diversos protocolos.

Os protocolos mais comuns são:

- HTTP
- HTTPS
- FTP
- SFTP
- SCP
- SMB
- LDAP
- MQTT

Na prática, ele é muito utilizado para:

- Consumir APIs REST.
- Fazer requisições HTTP.
- Baixar arquivos.
- Enviar arquivos.
- Testar servidores.
- Automatizar tarefas.
- Realizar reconhecimento durante um Pentest.

---

# Sintaxe

```bash
curl [opções] URL
```

---

# Requisição GET

```bash
curl https://example.com
```

O conteúdo da página será exibido no terminal.

---

# Salvar saída em um arquivo

```bash
curl https://example.com -o pagina.html
```

## Explicação

```text
-o
```

Salva a resposta utilizando o nome informado.

---

# Manter o nome original

```bash
curl -O https://example.com/arquivo.zip
```

O arquivo será salvo utilizando o nome existente na URL.

---

# Exibir cabeçalhos

```bash
curl -I https://example.com
```

## Exemplo

```text
HTTP/2 200
Server: nginx
Content-Type: text/html
Content-Length: 648
```

---

# Exibir cabeçalhos e corpo

```bash
curl -i https://example.com
```

---

# Seguir redirecionamentos

```bash
curl -L http://example.com
```

Muito comum quando o servidor redireciona para HTTPS.

---

# Modo silencioso

```bash
curl -s https://example.com
```

Oculta a barra de progresso.

---

# Método POST

```bash
curl -X POST https://api.site.com/login
```

---

# Enviando dados

```bash
curl -X POST \
-d "usuario=admin&senha=1234" \
https://api.site.com/login
```

---

# Enviando JSON

```bash
curl -X POST \
-H "Content-Type: application/json" \
-d '{"usuario":"admin","senha":"1234"}' \
https://api.site.com/login
```

---

# Outros métodos HTTP

## PUT

```bash
curl -X PUT https://api.site.com/usuario/1
```

## DELETE

```bash
curl -X DELETE https://api.site.com/usuario/1
```

## PATCH

```bash
curl -X PATCH https://api.site.com/usuario/1
```

---

# Adicionando Headers

```bash
curl \
-H "Authorization: Bearer TOKEN" \
-H "User-Agent: Mozilla/5.0" \
https://api.site.com
```

---

# Autenticação Básica

```bash
curl -u usuario:senha https://example.com
```

---

# Ignorar certificado SSL

```bash
curl -k https://site.local
```

Muito utilizado em ambientes de laboratório.

---

# Download de arquivo

```bash
curl -O https://example.com/ferramenta.zip
```

---

# Continuar download interrompido

```bash
curl -C - -O arquivo.iso
```

---

# Mostrar apenas o código HTTP

```bash
curl -o /dev/null -s -w "%{http_code}\n" https://example.com
```

Saída:

```text
200
```

---

# Mostrar informações da resposta

```bash
curl -v https://example.com
```

Exibe:

- Headers enviados
- Headers recebidos
- Conexão TCP
- TLS
- Certificados

---

# Testando uma API

```bash
curl https://jsonplaceholder.typicode.com/users
```

---

# Enviando um arquivo

```bash
curl \
-F "arquivo=@foto.jpg" \
https://example.com/upload
```

---

# Enviando Cookies

```bash
curl \
-b cookies.txt \
https://example.com
```

---

# Salvando Cookies

```bash
curl \
-c cookies.txt \
https://example.com
```

---

# Utilizando Proxy

```bash
curl \
-x http://127.0.0.1:8080 \
https://example.com
```

Muito utilizado com Burp Suite.

---

# Alterando o User-Agent

```bash
curl \
-A "Mozilla/5.0" \
https://example.com
```

---

# Operadores mais utilizados

| Opção | Função |
|--------|--------|
| `-X` | Método HTTP |
| `-H` | Header |
| `-d` | Dados enviados |
| `-F` | Upload de arquivo |
| `-o` | Salvar arquivo |
| `-O` | Manter nome original |
| `-I` | Apenas cabeçalhos |
| `-i` | Cabeçalhos + corpo |
| `-L` | Seguir redirecionamentos |
| `-s` | Modo silencioso |
| `-v` | Modo verboso |
| `-k` | Ignorar SSL |
| `-u` | Usuário e senha |
| `-b` | Ler cookies |
| `-c` | Salvar cookies |
| `-A` | User-Agent |
| `-x` | Proxy |

---

# Casos de uso em Pentest

- Enumerar APIs.
- Testar autenticação.
- Manipular Headers HTTP.
- Testar métodos HTTP.
- Simular navegadores.
- Baixar arquivos.
- Testar upload.
- Trabalhar com Burp Suite.
- Automatizar requisições.

---

# Boas práticas

- Utilize `-L` quando houver redirecionamentos.
- Utilize `-v` para depuração.
- Utilize `-s` em scripts.
- Utilize `-H` para controlar os Headers.
- Utilize `-k` apenas em ambientes de teste.

---

# Resumo

## GET

```bash
curl URL
```

## POST

```bash
curl -X POST URL
```

## Enviar JSON

```bash
curl -H "Content-Type: application/json" -d '{}' URL
```

## Download

```bash
curl -O URL
```

## Cabeçalhos

```bash
curl -I URL
```

## Verboso

```bash
curl -v URL
```

## Proxy

```bash
curl -x http://127.0.0.1:8080 URL
```