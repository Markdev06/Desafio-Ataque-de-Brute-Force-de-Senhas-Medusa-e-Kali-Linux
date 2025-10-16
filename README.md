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

