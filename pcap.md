Vou te ajudar a encontrar senhas FTP em um arquivo .pcap! Aqui estão os métodos:

## 🔍 **Métodos para Extrair Senhas FTP do PCAP:**

### **Método 1 - Usando Wireshark (GUI):**
1. **Abra o arquivo .pcap no Wireshark**
2. No filtro, digite:
   ```
   ftp || ftp-data
   ```
3. **Procure por pacotes com comandos:**
   - `USER` - mostra o usuário
   - `PASS` - mostra a senha
4. **Ou use o filtro direto:**
   ```
   ftp.request.command == "PASS" || ftp.request.command == "USER"
   ```

### **Método 2 - Usando tshark (terminal):**
```bash
# Filtrar apenas comandos de login FTP
tshark -r arquivo.pcap -Y "ftp" -T fields -e ftp.request.command -e ftp.request.arg

# Mostrar usuários e senhas especificamente
tshark -r arquivo.pcap -Y "ftp.request.command == 'USER' || ftp.request.command == 'PASS'" -T fields -e ftp.request.command -e ftp.request.arg

# Versão mais detalhada
tshark -r arquivo.pcap -Y "ftp" | grep -E "USER|PASS"
```

### **Método 3 - Usando strings + grep:**
```bash
# Extrair todas as strings e procurar por padrões FTP
strings arquivo.pcap | grep -i "user\|pass"

# Procurar especificamente por comandos FTP
strings arquivo.pcap | grep -E "USER |PASS "

# Busca mais abrangente
strings arquivo.pcap | grep -B2 -A2 "ftp"
```

### **Método 4 - Usando NetworkMiner:**
1. Baixe e abra o NetworkMiner
2. Importe o arquivo .pcap
3. Vá na aba **Credentials**
4. Procure por entradas FTP

### **Método 5 - Script Python com scapy:**
```python
from scapy.all import *

def extract_ftp_credentials(pcap_file):
    packets = rdpcap(pcap_file)
    
    for packet in packets:
        if packet.haslayer(Raw):
            payload = packet[Raw].load.decode('utf-8', errors='ignore')
            if 'USER' in payload:
                print(f"Usuário encontrado: {payload}")
            if 'PASS' in payload:
                print(f"Senha encontrada: {payload}")

extract_ftp_credentials('arquivo.pcap')
```

## 🎯 **Comandos Mais Eficazes:**

### **Comando 1 - Filtro direto:**
```bash
tshark -r arquivo.pcap -Y "ftp" -V | grep -A5 -B5 "PASS\|USER"
```

### **Comando 2 - Extrair todas as credenciais:**
```bash
tshark -r arquivo.pcap -Y "ftp.request.command in {'USER','PASS'}" -T fields -e frame.time -e ip.src -e ip.dst -e ftp.request.command -e ftp.request.arg
```

### **Comando 3 - Busca completa:**
```bash
tshark -r arquivo.pcap -Y "ftp" | grep -E "REQUEST: USER|REQUEST: PASS"
```

## 💡 **Dicas Importantes:**

1. **FTP é texto claro** - as senhas não são criptografadas
2. **Procure por sequências:** Primeiro `USER username` depois `PASS senha`
3. **Verifique múltiplas tentativas** - pode haver várias credenciais testadas
4. **Não esqueça do FTP passivo** - procure também por `ftp-data`

## 🚀 **Comando Rápido Tudo-em-um:**
```bash
echo "=== CREDENCIAIS FTP ENCONTRADAS ==="
tshark -r arquivo.pcap -Y "ftp.request.command == 'USER' || ftp.request.command == 'PASS'" -T fields -e ftp.request.command -e ftp.request.arg 2>/dev/null
```

**Execute esses comandos no terminal e me mostre o resultado!** Qualquer saída com `USER` ou `PASS` provavelmente contém as credenciais que você precisa.
