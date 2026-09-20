<div align="center">

<a href="https://git.io/typing-svg">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=45&pause=1000&color=8A2BE2&center=true&vCenter=true&width=800&height=120&lines=CTF+Writeup%3A+Overheard+at+Breakfast;OSINT+%26+Investigation;Data+Correlation;Threat+Intelligence" alt="Typing SVG" />
</a>

<p align="center">
  <img src="https://img.shields.io/badge/PLATAFORMA-TRYHACKME-E53935?style=for-the-badge&logo=tryhackme&logoColor=white" alt="Plataforma">
  <img src="https://img.shields.io/badge/FOCO-OSINT_%7C_INVESTIGAÇÃO-8A2BE2?style=for-the-badge" alt="Foco">
  <img src="https://img.shields.io/badge/STATUS-CONCLUÍDO-00E676?style=for-the-badge" alt="Status">
</p>

---

*Documentação da resolução do CTF focado inteiramente em OSINT e análise de vazamento de informações.*

</div>

<br>

## 📖 Sobre o projeto

Documentação da resolução do CTF **Overheard at Breakfast** da plataforma TryHackMe. Este laboratório foca inteiramente em **OSINT (Open-Source Intelligence)** e análise de vazamento de informações em comunicações aparentemente inofensivas.

---

## 🔍 O que é OSINT?

**OSINT (Inteligência de Fontes Abertas)** é o processo de coletar, analisar e correlacionar informações públicas sobre um alvo (pessoa, empresa ou sistema) sem interagir diretamente com ele ou quebrar barreiras de segurança.

No contexto de cibersegurança e SOC, o OSINT é crucial na fase de reconhecimento. Os atacantes não precisam invadir um banco de dados se a vítima expõe as suas senhas, e-mails ou padrões de comportamento em redes sociais, fóruns públicos ou perfis de plataformas conectadas. Neste CTF, a exploração não envolveu nenhum *exploit* técnico, apenas a correlação lógica de fragmentos de conversas soltas na internet.

---

<img width="100%" alt="Fluxograma da Investigação" src="https://github.com/user-attachments/assets/f1f5e098-ae89-4c72-8898-cb00f3ff8799" />

---

## 🔄 Fluxo do Ataque (Kill Chain)

O diagrama acima detalha a seguinte progressão investigativa:

### 1️⃣ Análise da Comunicação Interceptada
A investigação começou por analisar um *log* de *chat*. Na conversa, o alvo (Lambo) menciona casualmente que abandonou as redes sociais, mas relembra o uso de uma ferramenta gratuita que vinculava perfis e começava com a letra **"G"**. Junto a isso, o endereço de e-mail do alvo foi identificado: `lambobytelotushotel@gmail.com`.

### 2️⃣ Correlação de Plataforma (Gravatar)
Cruzando a dica de "ferramenta de perfil com G" e o endereço de e-mail, foi possível deduzir que o alvo estava a referir-se ao **Gravatar** (*Globally Recognized Avatar*) — um serviço massivamente utilizado na *web* que atrela imagens de perfil e descrições a *hashes* de e-mail.

### 3️⃣ Enumeração de Perfil
Utilizando ferramentas de busca focadas no Gravatar com o e-mail descoberto, o perfil público de "Lambo" foi localizado. Apesar de o alvo achar que havia apagado os seus rastros digitais, o perfil antigo continuava ativo na plataforma.

### 4️⃣ Extração e Decodificação de Dados
Na descrição (*bio*) do perfil do Gravatar, o alvo deixou uma mensagem irónica sobre "*hashes* seguirem-te por lugares inesperados", acompanhada de uma *string* codificada: `VEHNe1MzY3J1VF9Qcm9maWwzX0g0c19iMzNuX0lkZW50aWZpZWR9`.

Identificando o padrão como **Base64**, o texto foi copiado para um arquivo local (`email`) via terminal (`nano`) e descodificado utilizando o comando:
```bash
base64 -d email
```
---
## 🚨 Principais Impactos (Business Impact)

O vazamento não intencional de informações em plataformas secundárias representa um risco significativo para a OpSec (Segurança Operacional) de indivíduos e empresas:

- **Mapeamento de Identidade (Doxing):** Serviços como o Gravatar, que centralizam avatares em múltiplos fóruns e plataformas baseados num e-mail, permitem que um analista de inteligência reconstrua a presença digital de um alvo, vinculando *usernames* separados à mesma pessoa real.
- **Falta de Higiene Digital:** A suposição de que apagar contas principais de redes sociais garante o anonimato é uma falha comum. Perfis antigos, esquecidos em plataformas de terceiros, frequentemente mantêm informações históricas sensíveis acessíveis.
- **Vazamento de *Hashes* (MD5):** O modelo de funcionamento de alguns serviços (como a versão inicial do Gravatar) baseava-se em *hashes* MD5 simples dos e-mails. Se um invasor capturar esse *hash*, pode utilizar ataques de dicionário para descobrir o e-mail original, facilitando campanhas de *Phishing* direcionadas.

---

## 🛡️ Recomendações de Mitigação e Boas Práticas (OpSec)

Para mitigar a pegada digital e prevenir a exploração via OSINT, devem ser adotadas as seguintes práticas de Higiene Cibernética:

1. **Gestão do Ciclo de Vida da Identidade Digital:**
   Auditar periodicamente plataformas onde o e-mail profissional ou pessoal foi registrado. Quando um serviço deixar de ser utilizado, o perfil deve ser encerrado ou os dados devem ser ofuscados de forma proativa.

2. **Isolamento de E-mails (Compartimentação):**
   Não utilizar o mesmo endereço de e-mail para contas cruciais (como trabalho ou serviços bancários) e para registros em plataformas públicas secundárias, fóruns ou subscrições. A utilização de *aliases* de e-mail (ex: *SimpleLogin* ou *Hide My Email*) ajuda a compartimentar as identidades.

3. **Treino de *Security Awareness*:**
   Educar as equipes sobre os riscos associados à partilha excessiva de detalhes (mesmo triviais) em canais de comunicação, uma vez que fragmentos de conversas (*breadcrumbs*) podem ser correlacionados por adversários para construir perfis de ataque precisos.

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
