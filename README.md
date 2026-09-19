# 🕵️‍♂️ CTF Writeup: Basic Pentesting

![Dificuldade](https://img.shields.io/badge/dificuldade-fácil-brightgreen) ![Plataforma](https://img.shields.io/badge/plataforma-TryHackMe-red) ![Área](https://img.shields.io/badge/área-Pentest-blue)

Documentação da resolução do CTF **Basic Pentesting**. Este laboratório foca na exploração de vulnerabilidades e falhas de configuração comuns em serviços web e SMB, resultando na obtenção de acesso inicial, descoberta de informações sensíveis e movimentação lateral.

---

## 🔍 Informações do Alvo

- **Nome da Máquina:** Basic Pentesting (NetBIOS: BASIC2)
- **IP do Alvo:** 10.65.157.91
- **Sistema Operacional:** Linux (Ubuntu)

---

## 🗺️ Fluxograma da Investigação

<!-- INSIRA O SEU MOCKUP / FLUXOGRAMA AQUI -->
*(Mockup do fluxo de ataque será inserido manualmente aqui)*

---

## 👣 Fluxo do Ataque (Kill Chain)

Abaixo detalho a progressão investigativa e de exploração:

### 1️⃣ Reconhecimento & Enumeração
A investigação começou com o escaneamento de portas (Nmap), revelando serviços ativos de SSH (22), HTTP (80, 8080) e Samba/SMB (139, 445). A exploração HTTP através do Gobuster encontrou o diretório oculto `/development/`. Lá, notas textuais expuseram o uso do Apache Struts 2.5.12 e mencionaram os usuários "J" (jan) e "K" (kay), alertando sobre uma senha fraca pertencente a "J". Paralelamente, o acesso anônimo ao SMB permitiu ler o arquivo `staff.txt`, confirmando esses nomes de usuário.

### 2️⃣ Acesso Inicial (Brute Force SSH)
Com a informação de que o usuário **jan** possuía uma senha fraca, foi executado um ataque de força bruta contra o serviço SSH usando a ferramenta `hydra` e a wordlist *rockyou.txt*. A credencial `jan:armando` foi descoberta com sucesso, viabilizando o acesso inicial ao servidor.

### 3️⃣ Movimentação Lateral & Enumeração Local
Ao obter o acesso inicial, iniciei a enumeração do sistema local. Encontrei a pasta do usuário **kay** configurada incorretamente com permissões de leitura global (`/home/kay`), o que me permitiu explorá-la. Dentro da pasta oculta `.ssh`, localizei a chave privada `id_rsa` do usuário e a transferi para minha máquina atacante.

### 4️⃣ Extração e Decodificação da Chave SSH
A chave `id_rsa` estava protegida por uma "passphrase". Para quebrar essa proteção de forma offline sem interagir com o servidor alvo:
- Converti o arquivo da chave para um hash compatível com ferramentas de cracking usando o `ssh2john`.
- Realizei a quebra da senha utilizando o *John the Ripper* e a wordlist *rockyou.txt*, descobrindo a passphrase: `beeswax`.

### 5️⃣ Impacto Final e Pós-Exploração
Ajustei as permissões da chave privada na minha máquina (`chmod 600`) e estabeleci conexão via SSH fornecendo a passphrase (`beeswax`). Consegui obter acesso sob a identidade de **kay**. Esse novo nível de acesso permitiu a leitura do arquivo confidencial `pass.bak` na raiz da sua pasta pessoal, revelando seu conteúdo (`heresareallystrongpasswordthatfollowsthepasswordpolicy$$`) e concluindo o objetivo.

---

## 🛠️ Recomendações Defensivas
- **Políticas de Senha:** Aplicar políticas de senhas fortes e auditar periodicamente senhas no arquivo `/etc/shadow`.
- **Serviços SMB:** Restringir o acesso anônimo a compartilhamentos SMB para evitar vazamento de nomes de usuários.
- **Permissões de Diretório:** Impedir que diretórios residenciais (`/home`) tenham permissão de leitura global (outros usuários).
- **Auditoria de Arquivos:** Auditar regularmente notas, arquivos de configuração esquecidos e dados sensíveis em servidores web públicos.
