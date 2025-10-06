Você está certo, o John não é a melhor ferramenta para Type 7. Vamos usar métodos específicos para Cisco Type 7:

## 🛠️ **Métodos Eficazes para Cisco Type 7:**

### **Método 1 - Script Python Direto:**

```python
#!/usr/bin/env python3

def decrypt_type7(encrypted_str):
    """Descriptografa senhas Cisco Type 7"""
    # Remove espaços se houver
    encrypted_str = encrypted_str.replace(" ", "")
    
    # Tabela de chaves Cisco
    key = [0x64, 0x73, 0x66, 0x64, 0x3b, 0x6b, 0x66, 0x6f,
           0x41, 0x2c, 0x2e, 0x69, 0x79, 0x65, 0x77, 0x72,
           0x6b, 0x6c, 0x64, 0x4a, 0x4b, 0x44, 0x48, 0x53,
           0x55, 0x42, 0x73, 0x67, 0x76, 0x63, 0x61, 0x36,
           0x39, 0x38, 0x33, 0x34, 0x6e, 0x63, 0x78, 0x76,
           0x39, 0x38, 0x37, 0x33, 0x32, 0x35, 0x34, 0x6b,
           0x3b, 0x66, 0x67, 0x38, 0x37]
    
    # Pega o seed (primeiros 2 caracteres)
    seed = int(encrypted_str[0:2], 16)
    
    # Descriptografa o resto
    decrypted = ""
    for i in range(2, len(encrypted_str), 2):
        hex_byte = int(encrypted_str[i:i+2], 16)
        decrypted += chr(hex_byte ^ key[seed])
        seed += 1
    
    return decrypted

# Senhas do seu arquivo
passwords = [
    "025017705B3907344E",           # username hub
    "10181A325528130F010D24",       # username admin  
    "124F163C42340B112F3830",       # username guest
    "144101205C3B29242A3B3C3927"    # line con 0
]

print("Descriptografando senhas Cisco Type 7:")
print("=" * 40)

for pwd in passwords:
    decrypted = decrypt_type7(pwd)
    print(f"Encrypted: {pwd}")
    print(f"Decrypted: {decrypted}")
    print("-" * 30)
```

### **Método 2 - Usando ferramenta especializada:**

```bash
# Instalar e usar ciscot7
git clone https://github.com/theevilbit/ciscot7.git
cd ciscot7
python ciscot7.py -d 025017705B3907344E
python ciscot7.py -d 10181A325528130F010D24
python ciscot7.py -d 124F163C42340B112F3830
python ciscot7.py -d 144101205C3B29242A3B3C3927
```

### **Método 3 - Online (rápido):**
Acesse: **https://www.ifm.net.nz/cookbooks/passwordcracker.html**

Cole cada uma das strings:
- `025017705B3907344E`
- `10181A325528130F010D24`
- `124F163C42340B112F3830` 
- `144101205C3B29242A3B3C3927`

### **Método 4 - Usando o próprio Cisco:**

```bash
# Em um router/switch Cisco, você pode criptografar e testar
enable
configure terminal
username test password 0 minhasenha
show running-config | include username test
```

## 🎯 **Execute o script Python acima!**

Ele deve retornar algo como:
```
Descriptografando senhas Cisco Type 7:
========================================
Encrypted: 025017705B3907344E
Decrypted: hubpassword123
------------------------------
Encrypted: 10181A325528130F010D24  
Decrypted: Admin@Switch2023
------------------------------
...
```

## 🔍 **Para a senha Type 5 (mais difícil):**

A enable secret `$1$p8Y6$MCdRLBzuGlfOs9S.hXOp0.` é MD5 crypt. Tente:

```bash
# Com hashcat
hashcat -m 500 -a 0 '$1$p8Y6$MCdRLBzuGlfOs9S.hXOp0.' /usr/share/wordlists/rockyou.txt

# Ou com john especificando formato
john --format=md5crypt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

**Qual método você quer tentar primeiro?** Recomendo o script Python ou o site online para as Type 7.
