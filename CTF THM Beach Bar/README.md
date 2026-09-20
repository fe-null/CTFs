<div align="center">

<a href="https://git.io/typing-svg">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=45&pause=1000&color=FF0000&center=true&vCenter=true&width=800&height=120&lines=CTF+Writeup%3A+Beach+Bar;Web+Exploitation+%26+RCE;Insecure+Deserialization;Privilege+Escalation+(Root)" alt="Typing SVG" />
</a>

<p align="center">
  <img src="https://img.shields.io/badge/PLATAFORMA-TRYHACKME-E53935?style=for-the-badge&logo=tryhackme&logoColor=white" alt="Plataforma">
  <img src="https://img.shields.io/badge/FOCO-PENTESTING_%7C_WEB-1E88E5?style=for-the-badge" alt="Foco">
  <img src="https://img.shields.io/badge/STATUS-CONCLUÍDO-00E676?style=for-the-badge" alt="Status">
</p>

---

*Documentação tática: Exploração de processamento inseguro de arquivos, acesso inicial (Reverse Shell) e escalonamento para Root.*

</div>

<br>

## 📖 Sobre o projeto

Documentação da cadeia de ataque (*Kill Chain*) executada para comprometer a máquina **Beach Bar** da plataforma TryHackMe. O foco desta investigação detalha o caminho de invasão desde o mapeamento da superfície de ataque web e quebra de autenticação, passando pela exploração de vulnerabilidade crítica no processamento de arquivos YAML (RCE), até culminar no escalonamento de privilégios total no ambiente Linux (Root).

---

<img width="100%" alt="Fluxograma da Investigação" src="https://github.com/user-attachments/assets/505b687b-f92d-4b82-b523-bf75b4a4350c" />

---

## 🛠️ Ferramentas Utilizadas

A resolução deste laboratório demonstra proficiência na utilização das seguintes ferramentas padrão da indústria:

| Ferramenta | Função |
|---|---|
| **Nmap** | Reconhecimento agressivo de rede e varredura de portas/serviços HTTP expostos na infraestrutura. |
| **FFuF / Gobuster** | *Fuzzing* e enumeração de diretórios para descoberta de rotas ocultas e pontos de entrada (ex: página de *login* administrativa). |
| **Metasploit / Netcat** | Estabelecimento e estabilização de *Reverse Shells* via VPN, garantindo controle persistente sobre o alvo. |
| **LinPEAS** | Script de enumeração automatizada de *pós-exploração* para identificar vetores de escalonamento de privilégios no Linux. |

---

## 🔄 Fluxo do Ataque (Kill Chain)

O diagrama acima documenta a seguinte progressão tática:

### 1️⃣ Reconhecimento & Enumeração (Recon)
A varredura inicial revelou o serviço HTTP rodando a aplicação web. A partir daí, foi executada uma técnica de *Directory Bruteforce* para mapear a estrutura do site, revelando áreas restritas do sistema, incluindo o painel de autenticação administrativo.

### 2️⃣ Exploração Web & Bypass de Autenticação
Com a página de *login* identificada, o vetor de ataque focou-se em obter acesso à área autenticada. Através do contorno das proteções de acesso e da quebra das credenciais da aplicação, foi possível assumir o controlo sobre o painel de gestão administrativa.

### 3️⃣ Processamento Inseguro de Arquivos (Exploração YAML)
Dentro do painel administrativo, o sistema permitia a manipulação de configurações baseadas em ficheiros estruturados (`playlist.yml`). O sistema realizava o processamento (*parsing*) deste ficheiro de forma insegura, criando uma brecha crítica de injeção.

### 4️⃣ Acesso Inicial (RCE & Reverse Shell)
Explorando a falha de manipulação do ficheiro YAML (potencial falha de *Insecure Deserialization*), foi possível forçar a aplicação web a executar comandos arbitrários no servidor (*Remote Code Execution* - RCE). O *payload* injetado forçou uma conexão reversa de volta para a máquina atacante, garantindo acesso direto ao terminal do servidor como um utilizador de baixos privilégios (ex: `www-data`).

### 5️⃣ Escalonamento de Privilégios (Root)
Com a ligação inicial estabilizada, a fase de Pós-Exploração iniciou-se com uma intensa enumeração interna do sistema Linux (utilizando o *LinPEAS*). A exploração focou-se na identificação de configurações incorretas — como binários com SUID, tarefas *Cron* ou regras permissivas no `sudo`. A exploração eficaz deste vetor permitiu a elevação lateral e vertical até assumir a identidade do utilizador `root`, resultando no comprometimento total da infraestrutura.

---

## 🚨 Principais Impactos (Business Impact)

Vulnerabilidades que combinam falhas web lógicas e de infraestrutura geram um risco gravíssimo para as operações empresariais. Os cenários documentados refletem os seguintes impactos:

- **Comprometimento Total do Servidor:** A transição de um simples utilizador do servidor *web* para `root` significa que o atacante tem permissão para ler, modificar, destruir dados em disco, ou infetar a máquina com *Ransomware*, sem qualquer limitação do sistema operativo.
- **Remote Code Execution (RCE) Silencioso:** O processamento inseguro de dados formatados (como o YAML) é letal. Permite que um atacante execute instruções diretamente no núcleo operacional do servidor sem necessidade de escrever binários no disco, burlando vários antivírus e firewalls.
- **Quebra de Confidencialidade (Painel Admin):** Políticas de acesso e senhas fracas expõem o "coração" da aplicação. O comprometimento administrativo dá aos atacantes acesso direto à base de dados de clientes, transações e lógicas de negócio cruciais.

---

## 🛡️ Recomendações de Mitigação e Boas Práticas (OpSec)

Para garantir a resiliência desta infraestrutura (*Hardening*), recomendo as seguintes intervenções técnicas:

1. **Validação Rigorosa de Arquivos (Evitar Desserialização Insegura):**
   Garantir que as bibliotecas que processam ficheiros YAML (ou XML/JSON) estão configuradas para o modo *Safe Load*, impedindo-as de executar *scripts* contidos dentro da estrutura formatada do ficheiro.

2. **Fortificação de Interfaces Administrativas:**
   Implementar autenticação multifator (MFA/2FA) obrigatória para todas as áreas sensíveis da aplicação. Utilizar mecanismos de *Rate-Limiting* para impedir a enumeração de diretórios e ataques de força bruta (*Brute Force*).

3. **Princípio do Menor Privilégio (*PoLP*) na Infraestrutura:**
   Auditar os privilégios do utilizador de serviço da aplicação *web* (`www-data`). Este utilizador jamais deverá ter permissões para executar binários sensíveis no sistema como *root* (revogar configurações de SUID incorretas e limpar o ficheiro `/etc/sudoers`).

---

## ⚖️ Aviso Ético e Legal (Disclaimer)

> [!WARNING]
> **Operações Estritamente Educacionais**<br>Todos os testes de intrusão, técnicas de exploração e ferramentas documentados neste repositório foram executados de forma **ética**, em ambientes de laboratório isolados e legalmente autorizados (como os laboratórios da plataforma TryHackMe). 
> 
> O conteúdo aqui partilhado tem fins estritamente educacionais, focados na defesa cibernética e no reforço da segurança de infraestruturas. A utilização desautorizada destas técnicas em redes corporativas, servidores públicos ou sistemas privados sem o consentimento explícito e documentado (*Rules of Engagement*) é um crime cibernético.

<br>

<div align="center">
  <p><code>Desenvolvido com dedicação por Felipe Augusto.</code></p>
  <a href="https://github.com/fe-null">
    <img src="https://skillicons.dev/icons?i=github,linux,kali,bash,python" alt="Tech Stack" />
  </a>
</div>
