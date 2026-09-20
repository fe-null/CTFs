<div align="center">

<a href="https://git.io/typing-svg">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=45&pause=1000&color=FF0000&center=true&vCenter=true&width=800&height=120&lines=CTF+Writeup%3A+Room+404;Web+Exploitation;Source+Code+Disclosure;Red+Team+Operations" alt="Typing SVG" />
</a>

<p align="center">
  <img src="https://img.shields.io/badge/PLATAFORMA-TRYHACKME-E53935?style=for-the-badge&logo=tryhackme&logoColor=white" alt="Plataforma">
  <img src="https://img.shields.io/badge/FOCO-WEB_EXPLOITATION-1E88E5?style=for-the-badge" alt="Foco">
  <img src="https://img.shields.io/badge/STATUS-CONCLUÍDO-00E676?style=for-the-badge" alt="Status">
</p>

---

*Documentação da exploração de falhas de versionamento web e extração de código-fonte.*

</div>

<br>

## 📖 Sobre o projeto

Documentação da resolução do CTF **Room 404** da plataforma TryHackMe. Este laboratório demonstra na prática os riscos da vulnerabilidade de **Source Code Disclosure** (Exposição de Código-Fonte), especificamente focada no vazamento de diretórios de versionamento de código (`.git`) em ambientes web de produção ou *staging*.

---

<img width="100%" alt="Demonstração do Fluxo de Ataque" src="https://github.com/user-attachments/assets/bc2764ff-3038-4ff8-bd87-872c188103f8" />

---

## 🛠️ Ferramentas Utilizadas

| Ferramenta | Função |
|---|---|
| **Feroxbuster** | *Fuzzing* e enumeração agressiva de diretórios para descobrir *endpoints* ocultos no servidor web. |
| **git-dumper** | Ferramenta especializada para fazer o download e reconstruir um diretório `.git` exposto na web, consolidando os arquivos de volta ao seu estado original. |

---

## 🔄 Fluxo do Ataque (Kill Chain)

O diagrama documenta a seguinte cadeia de exploração:

### 1️⃣ Reconhecimento e Enumeração
A investigação iniciou com o acesso à aplicação web baseada no portal "Byte Lotus". Para descobrir rotas ocultas que não estavam visíveis na página principal, foi executada uma varredura de diretórios (*fuzzing*) utilizando o **Feroxbuster**.

### 2️⃣ Descoberta do Diretório Oculto
A varredura identificou a existência do caminho `/.git/` rodando no servidor alvo (porta 8080). Acessando essa URL diretamente pelo navegador, foi possível visualizar a página "Index of /.git/", confirmando que o diretório de versionamento do projeto estava indevidamente exposto para acesso público.

### 3️⃣ Extração do Repositório (Exploração)
Uma pasta `.git` armazena todo o histórico de versões, *commits* e o código-fonte de um projeto. Para extrair esses dados de forma automatizada, foi utilizado o seguinte comando no terminal:
```bash
git-dumper [http://10.66.167.118:8080/.git](http://10.66.167.118:8080/.git) loot
```
## 🚨 Principais Impactos (Business Impact)

A exposição do diretório `.git` não é apenas um vazamento de ficheiros estáticos, mas o comprometimento completo de toda a linha do tempo do projeto. Os principais riscos para as organizações incluem:

- **Vazamento de Credenciais (Secrets Leakage):** Desenvolvedores frequentemente incluem chaves de API, palavras-passe de bases de dados ou *tokens* de acesso no código durante os testes. Mesmo que sejam apagados posteriormente, permanecem acessíveis no histórico de *commits* extraído.
- **Roubo de Propriedade Intelectual:** Todo o código-fonte proprietário, lógica de negócio e algoritmos exclusivos ficam expostos à mercê de concorrentes ou agentes maliciosos.
- **Mapeamento Facilitado para Novos Ataques (*Whitebox Testing*):** Com o código-fonte na posse do atacante, este pode realizar auditorias de código avançadas para descobrir falhas de injeção (SQLi), vulnerabilidades lógicas, *endpoints* de administração ou falhas de controlo de acesso (IDOR), que dificilmente seriam detetadas num ataque "às cegas".

---

## 🛡️ Recomendações de Mitigação

Para prevenir a vulnerabilidade de *Source Code Disclosure*, as seguintes práticas de *Hardening* e *DevSecOps* devem ser adotadas:

1. **Bloqueio de Acesso no Servidor Web (Imediato):**
   Configurar o servidor web (Nginx, Apache, IIS) para negar liminarmente qualquer requisição externa a diretórios ou ficheiros ocultos (que comecem com `.`, como `.git`, `.env` ou `.htpasswd`).
   * *Exemplo Nginx:* `location ~ /\.git { deny all; }`
   * *Exemplo Apache:* `RedirectMatch 404 /\.git`

2. **Pipeline de Deploy Seguro (CI/CD):**
   O processo de *deploy* deve transferir apenas os ficheiros necessários para a execução da aplicação (os artefactos de *build* ou binários). A pasta `.git` nunca deve ser copiada para o servidor de *staging* ou produção.

3. **Gestão Segura de Segredos:**
   Nunca "hardcodar" (escrever diretamente) chaves de API ou palavras-passe no código-fonte, mesmo de forma temporária. Utilizar cofres de senhas nativos das plataformas Cloud (ex: *AWS Secrets Manager*, *HashiCorp Vault*) ou variáveis de ambiente controladas.

4. **Monitorização e *Scanning* Contínuo:**
   Implementar ferramentas de segurança na esteira de desenvolvimento (SAST/DAST) para alertar automaticamente as equipas caso repositórios sensíveis estejam prestes a ser expostos em ambientes públicos.
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
