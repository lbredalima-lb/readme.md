[Readme.txt](https://github.com/user-attachments/files/34547546/Readme.txt)
# readme.md
Entregar de projeto 

# Laboratório de Força Bruta com Medusa e Kali Linux

## 📌 Sobre o projeto
Projeto prático do desafio da DIO: simulação de ataques de força bruta em ambiente controlado usando Kali Linux + Medusa, contra Metasploitable 2 e DVWA, com foco em documentação e mitigação.

## 🎯 Objetivos
- Simular ataques de força bruta em FTP, formulário web (DVWA) e SMB
- Utilizar Medusa para automação de tentativas
- Documentar comandos, wordlists e resultados
- Propor medidas de mitigação

## 🖥️ Ambiente do laboratório

- VirtualBox com rede **Host-Only**
- Ferramentas: Medusa, Nmap, wordlists próprias

| Kali Linux | Atacante |
| Metasploitable 2 | 192.168.56.102 | Alvo (FTP/SMB) |





## ⚔️ Ataques Simulados executados

* primeiro Foi feito um portscan usando o NMAP
** comando utilizado nmap -sV -p 21,22,88,445,139 192.168.56.102
***o comando "-p" delimita as portas que você quer escanear 
*** e o " -sV " é para descobrir a versão da porta 

*após isto escolher uma porta aberta, no caso a FTP foi escolhida, porém a conexão pede login e senha 

* para o ataque de força Bruta, deve ser criado a WORDLIST, você próprio pode criar ou pegar no GitHub 
** comando utilizado  medusa -h 192.168.56.102 -U users.txt -P pass.txt -M ftp -t 6
** detalhe que deixar as letras minusculas ou maiusculas da diferença no comando
*** na atividade eu errei uma linha de comando das senhas e para ajustar de maneira mais simples, fui nos documentos do Kali linux, achei a users.txt e pass.txt e atualizei as senhas e usuarios diretamente nos arquivos 

*** o medusa informa qual usuário e senha teve sucesso e foi validado o acesso 


### 2. Formulário Web – DVWA

**Acesso ao DVWA:**
- URL: `http://192.168.56.102/dvwa/login.php`
- Credenciais padrão: `admin` / `password`
- Nível de segurança: **Low** (configurado em DVWA Security)

**Objetivo:** simular ataque de força bruta no formulário de login do DVWA.

*Comandos
 medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \ 
-m PAGE '/dvwa/login.pho'
-m FORM 'username=^USER^&password=^PASS^&Login=Login \
-m 'FAIL=Login failed' -t 6


#MITIGAÇÃO

*1. FTP (porta 21)
Por que é vulnerável: protocolo em texto claro + credenciais fracas → brute force trivial.

Substituir FTP por SFTP/FTPS (transferência criptografada).

Desabilitar login anônimo (anonymous_enable=NO no vsftpd.conf).

Política de senhas fortes (mín. 12 caracteres, complexidade, rotação).

Bloqueio por tentativas falhas — usar fail2ban com regra para vsftpd:

*2. Formulário Web / DVWA (login)
Por que é vulnerável: ausência de rate limit + mensagens de erro genéricas ausentes.

Rate limiting / throttling por IP e por usuário (ex.: Nginx limit_req, mod_evasive).

Bloqueio temporário após N falhas (lockout progressivo com backoff exponencial).

CAPTCHA / prova de trabalho após poucas tentativas (reCAPTCHA, hCaptcha ou Turnstile).

MFA (TOTP) para contas privilegiadas.

Mensagens de erro genéricas ("Usuário ou senha inválidos") — evita enumeração.

Token CSRF em todos os formulários (DVWA já tem em nível Medium+ — documentar diferença entre níveis é um ótimo diferencial!).

WAF (ModSecurity + OWASP CRS) para bloquear padrões automatizados.

Monitoramento/alertas para picos de 401/403 (SIEM ou Fail2ban com filtro apache-auth).

Senhas com hash forte (bcrypt/argon2) e política mínima.


*3. SMB (porta 445/139) — caso adicione ao projeto
Desabilitar SMBv1 (protocolo legado e inseguro).

Exigir SMB signing (server signing = mandatory no Samba / GPO no Windows).

Restringir por firewall (bloquear 445/139 para fora da LAN).

Desabilitar contas locais não usadas e remover Guest.

Lockout policy (ex.: 5 tentativas → 30 min) — mitiga password spraying.

Monitorar Event ID 4625 (Windows) / logs auth.log (Samba) para falhas em massa.

Não expor SMB à internet — usar VPN.

Segmentação de rede e princípio do menor privilégio em shares.



