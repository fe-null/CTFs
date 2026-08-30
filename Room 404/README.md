<div align="center">

# 🚪 CTF Writeup: Room 404

![Status](https://img.shields.io/badge/status-concluído-brightgreen)
![Plataforma](https://img.shields.io/badge/plataforma-TryHackMe-red)
![Área](https://img.shields.io/badge/área-Web_Exploitation-blue)

</div>

<br>

## 📖 Sobre o projeto

Documentação da resolução do CTF **Room 404** da plataforma TryHackMe. Este laboratório demonstra na prática os riscos da vulnerabilidade de **Source Code Disclosure** (Exposição de Código-Fonte), especificamente focada no vazamento de diretórios de versionamento de código (`.git`) em ambientes web de produção ou *staging*.

---

## 🗺️ Fluxograma da Exploração

<img width="2856" height="1074" alt="Image" src="https://github.com/user-attachments/assets/2894cab7-c8a5-4b0e-a5da-4fe937ae65ef" />

---

## 🛠️ Ferramentas Utilizadas

| Ferramenta | Função |
|---|---|
| **Feroxbuster** | Fuzzing e enumeração agressiva de diretórios para descobrir endpoints ocultos no servidor web. |
| **git-dumper** | Ferramenta especializada para fazer o download e reconstruir um diretório `.git` exposto na web, consolidando os arquivos de volta ao seu estado original. |

---

## 🔄 Fluxo do Ataque (Kill Chain)

O diagrama documenta a seguinte cadeia de exploração:

### 1️⃣ Reconhecimento e Enumeração
A investigação iniciou com o acesso à aplicação web baseada no portal "Byte Lotus". Para descobrir rotas ocultas que não estavam visíveis na página principal, foi executada uma varredura de diretórios (fuzzing) utilizando o Feroxbuster.

### 2️⃣ Descoberta do Diretório Oculto
A varredura identificou a existência do caminho `/.git/` rodando no servidor alvo (porta 8080). Acessando essa URL diretamente pelo navegador, foi possível visualizar a página "Index of /.git/", confirmando que o diretório de versionamento do projeto estava indevidamente exposto para acesso público.

### 3️⃣ Extração do Repositório (Exploração)
Uma pasta `.git` armazena todo o histórico de versões, commits e o código-fonte de um projeto. Para extrair esses dados de forma automatizada, foi utilizado o seguinte comando no terminal:
```bash
git-dumper http://10.66.167.118:8080/.git loot
```
Isso forçou o download de toda a estrutura do repositório remoto, reconstruindo os arquivos na máquina local dentro de uma pasta chamada `loot`.

### 4️⃣ Análise de Artefatos (Pós-Exploração)
Acessando a nova pasta `loot`, o comando `ls` revelou a estrutura original do código do desenvolvedor: `app.js`, `index.html` e `README.md`. 

### 5️⃣ Captura da Flag
Inspecionando o conteúdo do arquivo `README.md` através do comando `cat`, foi identificada uma nota interna alertando que aquele era um ambiente de *staging* (testes) e que a pasta não deveria ir para produção. Logo abaixo do aviso interno, a flag final do desafio foi exposta: `THM{byt3_l0tus_n3v3r_f0rg3ts}`.

---

## 💡 Aprendizados

- **Riscos de Versionamento em Produção:** O vazamento da pasta `.git` é uma falha crítica. Ela não apenas expõe o código-fonte atual, mas todo o histórico do projeto. Se um desenvolvedor comitar uma senha ou chave de API e depois apagar em um commit futuro, o atacante ainda consegue recuperar o dado analisando o histórico baixado.
- **Importância do Controle de Acesso:** Servidores web (como Apache e Nginx) devem ser configurados rigorosamente para bloquear qualquer requisição externa a diretórios e arquivos ocultos (que começam com `.`, como `.git`, `.env` ou `.htpasswd`).
- **Automação de Extração:** O uso do `git-dumper` agiliza o trabalho de Red Team, permitindo reconstruir projetos inteiros mesmo quando o servidor desativa a listagem de diretórios (Directory Listing), tornando a exploração rápida e letal.
