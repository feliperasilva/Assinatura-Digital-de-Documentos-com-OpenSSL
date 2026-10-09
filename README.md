# Roteiro de Prática: Assinatura Digital de Documentos com OpenSSL

## 1. Tema

Assinatura digital de documentos utilizando o OpenSSL no Linux.

## 2. Objetivo

Compreender como funciona a assinatura digital, utilizando chaves criptográficas para assinar um documento e verificar sua autenticidade e integridade.

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

Proteger o arquivo da chave privada:

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

Esse comando gera uma assinatura digital para o documento utilizando a chave privada.

## 5. Resultados esperados

Ao final desta etapa, deverão existir os seguintes arquivos:

- `documento.txt` — documento original.
- `chave_privada.pem` — chave privada utilizada para assinar.
- `chave_publica.pem` — chave pública utilizada para verificar a assinatura.
- `assinatura.bin` — arquivo contendo a assinatura digital do documento.

A assinatura permite verificar se o documento permaneceu íntegro e se corresponde à assinatura produzida pela chave privada utilizada.
