# gh‑multi — Guia Completo para Várias Contas GitHub

> Gere identidades diferentes (pessoal, testes, trabalho, …) num único computador,
> usando um simples script Bash + GitHub CLI.

---

## 🗂️ Conteúdo do repositório

| Ficheiro              | Para quê?                                                   |
|-----------------------|-------------------------------------------------------------|
| **`gh-multi.sh`**     | Script executável com os comandos `add`, `list`, `new`, `switch`. |
| **`ssh-config.example`** | Snippet de aliases/keys para colar em `~/.ssh/config`.          |
| **`README.md`**       | **Este guia**.                                              |
| **`LICENSE`**         | MIT.                                                        |

---

## 🚀 Passo 0 — Pré‑requisitos

```bash
# Instalar Git e GitHub CLI (via Homebrew)
brew install git gh
```

---

## ① Gerar chaves SSH (uma por conta)

```bash
# Conta oficial
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_dcs        -C "diogo-costa-silva@github"

# Conta de testes
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_dipedilans -C "dipedilans@github"
```

*Usa uma pass‑phrase; o macOS Keychain lembrará por ti.*

---

## ② Configurar aliases SSH

1. Copia **`ssh-config.example`** para `~/.ssh/config`.
2. Ajusta os caminhos se usares nomes de ficheiro diferentes.
3. Testa:

```bash
ssh -T git@github-dcs
ssh -T git@github-dipedilans
```

Deves ver “You’ve successfully authenticated”.

---

## ③ Autenticar o GitHub CLI para cada alias

```bash
gh auth login --hostname github-dcs
gh auth login --hostname github-dipedilans
```

> Escolhe **SSH** como método; o `gh` adiciona a *public key* se faltar.

---

## ④ Instalar o script gh‑multi

```bash
chmod +x gh-multi.sh
mkdir -p ~/bin
cp gh-multi.sh ~/bin/
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc      # ou reinicia o terminal
```

---

## ⑤ Registar as contas

```bash
gh-multi.sh add dcs        "Diogo Costa"          "12345678+diogo-costa-silva@users.noreply.github.com" github-dcs
gh-multi.sh add dipedilans "Diogo Costa (testes)" "12345678+dipedilans@users.noreply.github.com"       github-dipedilans
gh-multi.sh list   # confirma tabela
```

---

## ⑥ Workflow diário — comandos essenciais

| Queremos…        | Comando                                          |
|------------------|--------------------------------------------------|
| Listar contas    | `gh-multi.sh list`                               |
| **Novo repositório** | `gh-multi.sh new <alias> <repo> [--private \| --public]` |
| Mudar repo de conta | `gh-multi.sh switch <alias>`                  |

### Exemplo completo

```bash
# 1. Criar repo privado na conta de testes
mkdir hello-dual && cd $_
gh-multi.sh new dipedilans hello-dual --private

# 2. Tornar público e migrar para conta oficial
gh repo edit dipedilans/hello-dual --visibility public
gh-multi.sh switch dcs
git push -u origin main
```

---

## ⑦ Adicionar uma terceira conta (ex.: trabalho)

1. Gerar nova chave + alias SSH (`github-work`).  
2. `gh auth login -h github-work` (SSH).  
3. `gh-multi.sh add work "Diogo • Work" "NOREPLY_EMAIL" github-work`.

---

## ⑧ Alterar visibilidade depois

```bash
gh repo edit <owner>/<repo> --visibility private   # ou --public
```

---

## ⑨ Ideias extra

* Assinar commits com chaves GPG diferentes (`user.signingkey`).
* Criar templates de projecto (`git init --template`).
* Adicionar autocomplete Zsh para `gh-multi.sh`.

---

### Licença

Código disponibilizado sob licença [MIT](LICENSE).
