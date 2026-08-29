# 🏖️ CTF Writeup: Beach Bar

![Status](https://img.shields.io/badge/status-concluído-brightgreen)
![Plataforma](https://img.shields.io/badge/plataforma-TryHackMe-red)
![Área](https://img.shields.io/badge/área-Pentest-blue)

Documentação do fluxo de exploração do CTF **Beach Bar** da plataforma **TryHackMe**. Este projeto detalha o caminho de invasão desde o reconhecimento inicial até o comprometimento total do servidor (Root), com foco na exploração de vulnerabilidades web e escalonamento de privilégios em ambiente Linux.

> ⚠️ **Aviso**: As técnicas demonstradas aqui foram aplicadas em um ambiente de laboratório isolado e legal. A utilização dessas técnicas em alvos reais sem autorização é crime.

---

## 📑 Sumário

- [Sobre o projeto](#-sobre-o-projeto)
- [Fluxograma](#-fluxograma)
- [Ferramentas utilizadas](#-ferramentas-utilizadas)
- [Fluxo do ataque](#-fluxo-do-ataque)
- [Aprendizados](#-aprendizados)

---

## 📖 Sobre o projeto

Este writeup ilustra a cadeia de ataque (Kill Chain) executada para comprometer a máquina alvo. A documentação visualiza a progressão do acesso: mapeamento da superfície de ataque web, bypass de autenticação, exploração de vulnerabilidade no processamento de arquivos (playlists em formato YAML) e, finalmente, a elevação de privilégios no sistema operacional.

---

## 🗺️ Fluxograma

![Fluxograma de Exploração - Beach Bar](Beach%20Bar.jpg)
*(Mapa mental criado durante o CTF ilustrando a ramificação do ataque)*

---

## 🛠️ Ferramentas utilizadas

| Ferramenta | Função |
|---|---|
| **Nmap** | Reconhecimento de rede e varredura de portas/serviços HTTP expostos |
| **FFuF / Gobuster** | Enumeração de diretórios e descoberta de rotas web (ex: login, admin) |
| **Metasploit / Netcat** | Estabelecimento de reverse shell via VPN e controle de payload |
| **LinPEAS** | Enumeração automatizada para escalonamento de privilégios no Linux |

---

## 🔄 Fluxo do ataque

O diagrama documenta as seguintes etapas, na ordem em que ocorrem:

### 1️⃣ Reconhecimento (Recon)
A varredura inicial com Nmap revelou o serviço HTTP rodando a aplicação web. A partir daí, foi feita a enumeração de diretórios bruteforce para descobrir a estrutura do site e os pontos de entrada, como a página de login.

### 2️⃣ Exploração Web & Autenticação
Com a página de login identificada nas rotas web, o ataque focou em obter acesso à área autenticada. Isso envolveu técnicas para contornar ou adivinhar as credenciais de acesso para entrar no painel de administração da aplicação.

### 3️⃣ Vulnerabilidade em Manipulação de Arquivos
Dentro do painel administrativo, foi identificada uma funcionalidade de gerenciamento de dados envolvendo arquivos YAML (como o `playlist.yml` visto no fluxo de execução). O sistema processava o arquivo de forma insegura, abrindo brechas para injeção de comandos.

### 4️⃣ Acesso Inicial (Reverse Shell)
Explorando o processamento do arquivo de playlist, foi possível forçar a aplicação a executar código remoto (RCE). Um payload foi disparado, estabelecendo uma conexão reversa para a máquina atacante. Isso garantiu acesso ao terminal do servidor alvo como um usuário de baixo privilégio.

### 5️⃣ Escalonamento de Privilégios (Root)
Com o acesso shell estabilizado, iniciou-se a fase de pós-exploração. A enumeração interna do Linux buscou falhas de configuração (como binários com SUID, regras permissivas de `sudo` ou tarefas Cron). A exploração dessas configurações permitiu elevar o nível de acesso até o usuário `root`, assumindo controle total da máquina.

---

## 💡 Aprendizados

- Metodologia eficiente de enumeração web e descoberta de diretórios administrativos.
- Riscos críticos no processamento de arquivos serializados ou estruturados (como YAML) quando o input do usuário não é devidamente sanitizado.
- Uso de ferramentas em ambiente laboratorial para obter e estabilizar shells reversos.
- Técnicas avançadas de enumeração local em sistemas Linux para encontrar rotas de escalonamento de privilégios.