<div align="center">

<a href="https://git.io/typing-svg">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=45&pause=1000&color=8A2BE2&center=true&vCenter=true&width=800&height=120&lines=CTF+Writeup%3A+Lab+Pentest+Web;Recon+%26+Enumeration;Web+Exploitation;Privilege+Escalation" alt="Typing SVG" />
</a>

<p align="center">
  <img src="https://img.shields.io/badge/PLATAFORMA-LAB_LOCAL-E53935?style=for-the-badge&logo=linux&logoColor=white" alt="Plataforma">
  <img src="https://img.shields.io/badge/FOCO-WEB_PENTEST_%7C_EXPLOITATION-8A2BE2?style=for-the-badge" alt="Foco">
  <img src="https://img.shields.io/badge/STATUS-CONCLUÍDO-00E676?style=for-the-badge" alt="Status">
</p>

---

*Documentação da cadeia completa de invasão, exploração de vulnerabilidades web e escalação em ambiente de laboratório controlado.*

</div>

<br>

## 📖 Sobre o projeto

Documentação da resolução do laboratório prático de **Pentest Web** (Lab Udemy). Este laboratório reproduz um ambiente realista de testes de intrusão, cobrindo todo o ciclo de vida de uma exploração ofensiva: desde o reconhecimento de portas e enumeração de rotas até o ganho de acesso e execução remota de código (RCE).

---

## 🔍 O que é Pentest Web?

**Pentest Web (Teste de Intrusão em Aplicações Web)** é a prática de simular ataques controlados contra aplicações, APIs e servidores web para encontrar vulnerabilidades antes que agentes maliciosos possam explorá-las. 

Diferente de auditorias puramente teóricas, o Pentest Web envolve a exploração encadeada de falhas — tais como falhas de autenticação, injeção de parâmetros, execução de arquivos arbitrários e má configuração de permissões. O objetivo primordial é identificar o impacto real dessas brechas e orientar o time de desenvolvimento e segurança defensiva na remediação dos pontos críticos.

---

<!-- Substitua o link abaixo pela imagem do seu canvas/diagrama do Obsidian hospedada no GitHub -->
<img width="100%" alt="Fluxograma do Pentest Web" src="https://github.com/user-attachments/assets/lab-flow.png" />

---

## 🔄 Fluxo do Ataque (Kill Chain)

O diagrama acima detalha a seguinte progressão técnica:

### 1️⃣ Reconhecimento e Varredura de Portas (Recon)
A análise iniciou-se com a identificação do host na rede interna e a descoberta dos serviços ativos. Através do **Nmap**, foram mapeadas as portas abertas e banners de versões de serviço (como portas `80` e `8080`), delimitando a superfície externa de ataque:
```bash
nmap -sC -sV -p- -T4 <IP_ALVO>
```

### 2️⃣ Enumeração de Diretórios e Aplicações Web
Com os serviços HTTP mapeados, iniciou-se a fase de *fuzzing* e busca por recursos ocultos utilizando ferramentas de enumeração (`Gobuster` / `ffuf`). Foram identificados caminhos restritos, telas de autenticação (`/login`, `/admin`) e formulários de cadastro:
```bash
gobuster dir -u http://<IP_ALVO>/ -w /usr/share/wordlists/dirb/common.txt -x php,html,txt
```

### 3️⃣ Análise de Entradas e Vetores de Vulnerabilidade
As requisições foram interceptadas e analisadas via **Burp Suite** para compreender os mecanismos de manipulação de requisições:
* **Validação de Formulários:** Análise de parâmetros manipuláveis para verificar a presença de injeções (SQL Injection em campos de autenticação ou Command Injection em formulários de consulta).
* **Controle de Acesso / Upload:** Análise dos fluxos de envio de arquivos ou inclusão de parâmetros de rotas (`?page=`), verificando a ausência de sanitização e validação de extensão/MIME type.

### 4️⃣ Exploração e Obtenção de Acesso (RCE)
Após identificar a superfície vulnerável, foi construído o payload para exploração direta:
* Envio de arquivo/payload interpretável pelo servidor web (ou injeção direta de comandos) contornando restrições do cliente.
* Estabelecimento de uma **Reverse Shell** para comunicação interativa com o sistema operacional sob a conta de serviço do servidor web (`www-data`):
```bash
# Escuta na máquina atacante
nc -lvnp 4444
```

### 5️⃣ Pós-Exploração e Conclusão
Com o acesso shell obtido, foi feita a enumeração local dos arquivos do sistema, verificação de privilégios e recuperação dos artefatos/flags do laboratório, confirmando a invasão com sucesso.

---

## 🚨 Principais Impactos (Business Impact)

A presença de falhas críticas em aplicações expostas traz riscos severos para a infraestrutura corporativa:

- **Execução Remota de Código (RCE):** A falha em sanitizar dados de entrada ou validar uploads permite que o atacante execute comandos diretamente no sistema operacional do servidor, servindo como porta de entrada para a rede interna.
- **Quebra de Confidencialidade de Dados:** Vulnerabilidades de bypass de autenticação e injeção de parâmetros expõem bancos de dados, arquivos confidenciais do servidor (`/etc/passwd`, chaves SSH, configurações com senhas em texto puro).
- **Movimentação Lateral:** Uma vez comprometida a máquina onde a aplicação web reside, o atacante pode usá-la como pivô para atacar outros servidores e serviços internos da organização.

---

## 🛡️ Recomendações de Mitigação e Boas Práticas (AppSec)

Para mitigar os vetores explorados e fortalecer a postura de segurança da aplicação, devem ser adotadas as seguintes práticas defensivas:

1. **Validação Estrita e Parametrização:**
   Implementar consultas parametrizadas (*Prepared Statements*) e sanitização rigorosa de qualquer entrada do usuário, rejeitando caracteres especiais não autorizados em campos de busca ou formulários.

2. **Segurança no Upload e Inclusão de Arquivos:**
   Nunca permitir a execução de scripts dentro de diretórios de upload. Validar arquivos através de listas brancas (*whitelisting*) de extensões seguras e verificação de cabeçalhos binários (*magic bytes*), renomeando os arquivos com hashes aleatórios antes do armazenamento.

3. **Arquitetura de Menor Privilégio e Hardening:**
   Executar o servidor web sob usuários de baixa permissão, sem acesso a comandos administrativos ou binários sensíveis, além de aplicar regras de WAF (Web Application Firewall) para detectar tentativas de exploração conhecidas.

---

## ⚖️ Aviso Ético e Legal (Disclaimer)

> [!WARNING]
> **Operações Estritamente Educacionais**<br>Todos os testes de intrusão, técnicas de exploração e comandos documentados neste repositório foram executados de forma **ética**, em um ambiente de laboratório localmente controlado, sem qualquer conexão com redes públicas e com autorização para fins de estudo. 
> 
> O conteúdo aqui partilhado tem fins estritamente educacionais, focados na defesa cibernética e no reforço da segurança de infraestruturas web. A utilização desautorizada destas técnicas em redes corporativas, servidores públicos ou sistemas privados sem consentimento explícito (*Rules of Engagement*) constitui crime cibernético.

<br>

<div align="center">
  <p><code>Desenvolvido com dedicação por Felipe Augusto.</code></p>
  <a href="https://github.com/fe-null">
    <img src="https://skillicons.dev/icons?i=github,linux,kali,bash,python" alt="Tech Stack" />
  </a>
</div>