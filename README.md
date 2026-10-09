# Roteiro de Prática: Assinatura Digital e Certificado Autoassinado com OpenSSL

## 1. Tema

Assinatura digital de documentos e criação de um certificado digital autoassinado utilizando OpenSSL no Linux.

## 2. Objetivo

Compreender o funcionamento da assinatura digital, gerar chaves criptográficas, criar um certificado autoassinado e verificar a integridade de um documento.

## 3. Materiais necessários

- Computador com Linux.
- Terminal.
- OpenSSL instalado.

Para verificar se o OpenSSL está instalado, execute:

```bash
openssl version
```

## 4. Procedimentos

### Passo 1 — Criar uma pasta para a prática

```bash
mkdir pratica-assinatura
cd pratica-assinatura
```

### Passo 2 — Criar um documento de teste

```bash
echo "Documento de teste para assinatura digital." > documento.txt
```

### Passo 3 — Gerar a chave privada

```bash
openssl genpkey -algorithm RSA \
-out chave_privada.pem \
-pkeyopt rsa_keygen_bits:2048
```

Proteger a chave privada:

```bash
chmod 600 chave_privada.pem
```

### Passo 4 — Gerar a chave pública

```bash
openssl pkey -in chave_privada.pem \
-pubout -out chave_publica.pem
```

### Passo 5 — Assinar o documento

```bash
openssl dgst -sha256 \
-sign chave_privada.pem \
-out assinatura.bin documento.txt
```

Esse comando cria uma assinatura digital do documento utilizando a chave privada.

### Passo 6 — Criar um certificado autoassinado

```bash
openssl req -new -x509 \
-key chave_privada.pem \
-out certificado.pem \
-sha256 -days 365
```

O OpenSSL solicitará informações como país, estado, cidade e nome da organização. Preencha os campos solicitados conforme necessário.

O arquivo `certificado.pem` será criado com validade de 365 dias e assinado pela própria chave privada.

**Observação:** um certificado autoassinado não é automaticamente confiável para outros computadores ou aplicações, pois não foi assinado por uma Autoridade Certificadora confiável.

### Passo 7 — Verificar o certificado

```bash
openssl x509 -in certificado.pem -text -noout
```

Esse comando exibe as informações do certificado, incluindo o titular, o emissor e o período de validade.

Como o certificado é autoassinado, o titular (*Subject*) e o emissor (*Issuer*) normalmente serão iguais.

### Passo 8 — Verificar a assinatura do documento

```bash
openssl dgst -sha256 \
-verify chave_publica.pem \
-signature assinatura.bin documento.txt
```

Se o documento não tiver sido alterado, o resultado esperado será:

```text
Verified OK
```

### Passo 9 — Testar a integridade do documento

Altere o conteúdo do documento:

```bash
echo "Conteúdo modificado." >> documento.txt
```

Execute novamente a verificação:

```bash
openssl dgst -sha256 \
-verify chave_publica.pem \
-signature assinatura.bin documento.txt
```

A verificação deverá falhar, pois o conteúdo do documento foi alterado depois da assinatura.

## 5. Resultados esperados

Ao concluir a prática, deverão existir os seguintes arquivos:

| Arquivo | Finalidade |
|---|---|
| `documento.txt` | Documento que será assinado. |
| `chave_privada.pem` | Chave privada utilizada para assinar o documento e gerar o certificado. |
| `chave_publica.pem` | Chave pública utilizada para verificar a assinatura. |
| `assinatura.bin` | Arquivo que contém a assinatura digital. |
| `certificado.pem` | Certificado digital autoassinado que contém a chave pública e os dados do titular. |

Espera-se que a assinatura seja validada quando o documento estiver intacto e que a verificação falhe após a alteração do conteúdo.
