# 🕵️‍♂️ CTF Writeup: Overheard at Breakfast

![Status](https://img.shields.io/badge/status-concluído-brightgreen)
![Plataforma](https://img.shields.io/badge/plataforma-TryHackMe-red)
![Área](https://img.shields.io/badge/área-OSINT-blue)

Documentação da resolução do CTF **Overheard at Breakfast** (TryHackMe). Este laboratório foca inteiramente em **OSINT (Open-Source Intelligence)** e análise de vazamento de informações em comunicações aparentemente inofensivas.

---

## 🔍 O que é OSINT?

**OSINT (Inteligência de Fontes Abertas)** é o processo de coletar, analisar e correlacionar informações públicas sobre um alvo (pessoa, empresa ou sistema) sem interagir diretamente com ele ou quebrar barreiras de segurança. 

No contexto de cibersegurança e SOC, o OSINT é crucial na fase de reconhecimento. Os atacantes não precisam hackear um banco de dados se a vítima expõe suas senhas, e-mails ou padrões de comportamento em redes sociais, fóruns públicos ou perfis de plataformas conectadas. Neste CTF, a exploração não envolveu nenhum *exploit* técnico, apenas a correlação lógica de fragmentos de conversas soltas na internet.

---

## 🗺️ Fluxograma da Investigação

![Fluxograma da Investigação - Overheard at Breakfast](Overheard%20at%20Breakfast.jpg)
*(Mapa mental criado no Obsidian ilustrando a trilha de migalhas deixada pelo alvo)*

---

## 🔄 Fluxo do Ataque (Kill Chain)

O diagrama acima detalha a seguinte progressão investigativa:

### 1️⃣ Análise da Comunicação Interceptada
A investigação começou analisando um log de chat[cite: 1]. Na conversa, o alvo (Lambo) menciona casualmente que abandonou as redes sociais, mas relembra o uso de uma ferramenta gratuita que vinculava perfis e começava com a letra **"G"**[cite: 1]. Junto a isso, o endereço de e-mail do alvo foi identificado: `lambobytelotushotel@gmail.com`[cite: 1].

### 2️⃣ Correlação de Plataforma (Gravatar)
Cruzando a dica de "ferramenta de perfil com G" e o endereço de e-mail, foi possível deduzir que o alvo estava se referindo ao **Gravatar** (Globally Recognized Avatar)[cite: 1] — um serviço massivamente utilizado na web que atrela imagens de perfil e descrições a hashes de e-mail.

### 3️⃣ Enumeração de Perfil
Utilizando ferramentas de busca focadas no Gravatar com o e-mail descoberto, o perfil público de "Lambo" foi localizado[cite: 1]. Apesar de o alvo achar que havia apagado seus rastros digitais, o perfil antigo continuava ativo na plataforma[cite: 1].

### 4️⃣ Extração e Decodificação de Dados
Na descrição (bio) do perfil do Gravatar, o alvo deixou uma mensagem irônica sobre "hashes seguirem você por lugares inesperados", acompanhada de uma string codificada: `VEHNe1MzY3J1VF9Qcm9maWwzX0g0c19iMzNuX0lkZW50aWZpZWR9`[cite: 1].

Identificando o padrão como **Base64**, o texto foi copiado para um arquivo local (`email`) via terminal (`nano`) e decodificado utilizando o comando:
```bash
base64 -d email