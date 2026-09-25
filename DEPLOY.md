# Publicar o "Detetive dos Grafos" no Firebase Hosting

Guia completo para colocar o jogo no ar (plano **gratuito Spark**) com o
**placar da turma em tempo real**, sem nenhum login por parte dos alunos.

> ✅ **Já está publicado:** <https://jogografos.web.app>
> (ID do projeto: `jogografos` · número: `803774518733`)
>
> **Onde rodar os comandos:** tudo roda direto no **terminal WSL/Ubuntu**, na
> pasta `~/Projetos/jogografo`. O Node.js foi instalado **dentro do Linux** via
> **nvm** — não use o Node do Windows, ele quebra por conflito de caminhos
> (`\\wsl.localhost\...`).
>
> Se abrir um terminal novo e o `firebase`/`node` "sumir", carregue o nvm:
> ```bash
> source ~/.bashrc          # ou: export NVM_DIR="$HOME/.nvm"; . "$NVM_DIR/nvm.sh"
> node --version            # tem que apontar para ~/.nvm/... (Linux)
> which firebase            # ~/.nvm/versions/node/.../bin/firebase
> ```

---

## Visão geral dos arquivos

| Arquivo | Papel |
|---|---|
| `detetive_grafos.html` | **Fonte de verdade** do jogo. É aqui que você edita. |
| `public/index.html` | Cópia que o Hosting publica. Regere a cada deploy (veja abaixo). |
| `firebase.json` | Configuração do Hosting + Firestore. |
| `.firebaserc` | Aponta para o projeto Firebase (`default: jogografos`). |
| `firestore.rules` | Regras de segurança (placar público, resto bloqueado). |
| `firestore.indexes.json` | Índices do Firestore (vazio — não precisamos). |

O Hosting serve a pasta `public/`, então **o jogo publicado é o
`public/index.html`**. Sempre que mudar o `detetive_grafos.html`, copie por
cima antes de publicar:

```bash
cp detetive_grafos.html public/index.html
```

---

## Reimplantar depois de uma alteração (o caso mais comum)

Depois de editar o jogo:

```bash
cd ~/Projetos/jogografo
cp detetive_grafos.html public/index.html
firebase deploy --only hosting
```

Se mudar o `firestore.rules`:

```bash
firebase deploy --only firestore:rules
```

Para publicar tudo de uma vez (hosting + regras + índices):

```bash
firebase deploy
```

A URL continua a mesma: <https://jogografos.web.app>.

---

## Como usar em aula

1. Abra a URL <https://jogografos.web.app> e teste você mesmo.
2. **Defina um código de sala de 4 dígitos** (ex.: `4271`) e passe de viva voz
   para a turma.
3. Cada aluno abre a URL, digita o **nome** e o **código da sala**, e joga. O
   placar da turma atualiza em tempo real para todos com o mesmo código.
4. Turmas/datas diferentes = códigos diferentes → os placares não se misturam,
   mesmo reusando o mesmo site.
5. Para zerar o placar de uma sala: abra **🏆 Placar da turma** e clique em
   **🗑️ Resetar placar da sala** (pede confirmação antes de apagar).

> Aluno que entrar **sem** código joga normalmente, só não aparece no placar.

---

## Estrutura dos dados e das regras

- Cada aluno grava um documento em `salas/{codigo}/leaderboard/{alunoId}`,
  com `{ name, score, level, totalLevels, streak, updatedAt }`.
- `alunoId` é um id anônimo gerado no navegador (localStorage), então o mesmo
  aluno mantém sua linha no placar entre partidas, sem login.
- As `firestore.rules` liberam leitura/escrita **públicas só** nesse caminho,
  exigindo código de 4 dígitos e validando tipos/tamanho dos campos. Todo o
  resto do banco fica bloqueado.

---

# Recriar tudo do zero (referência)

Você já fez isto uma vez. Guardado aqui caso precise refazer em outra máquina
ou montar um projeto novo.

## 1. Ferramentas (uma vez por máquina)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc
nvm install --lts
npm install -g firebase-tools
firebase --version
```

## 2. Login (interativo — abre o navegador)

```bash
firebase login
```

Entre com sua conta Google e autorize. No WSL, se o navegador não
abrir/retornar sozinho, use `firebase login --no-localhost` (ele mostra um
link e pede um código de verificação; cole o código de volta no terminal —
faça rápido, o código expira em segundos).

Conferir: `firebase login:list`.

> ⚠️ **Termos de Serviço:** a criação de projeto via CLI falha com
> `Callers must accept Terms of Service` se sua conta Google nunca aceitou os
> termos do Google Cloud/Firebase. Resolva criando o **primeiro** projeto pelo
> painel <https://console.firebase.google.com/> (marque os termos quando
> pedir). Depois disso a CLI passa a funcionar.

## 3. Criar o projeto

Pelo painel (mais garantido, e já aceita os termos):
<https://console.firebase.google.com/> → **Criar projeto** → nome
`Detetive dos Grafos` → pode desativar o Analytics → anote o **ID do projeto**.

Ou por CLI (só funciona depois de aceitar os termos):

```bash
firebase projects:create SEU-ID --display-name "Detetive dos Grafos"
```

Aponte o repositório para ele (atualiza o `.firebaserc`):

```bash
firebase use SEU-ID
```

## 4. Habilitar as APIs necessárias

Se você criou o projeto pelo painel, provavelmente já estão ligadas. Se o
deploy/Firestore reclamar de API desativada, habilite pelo link que o próprio
erro mostra, ou pelo console em **APIs & Services**. As que usamos:
`firestore.googleapis.com`, `firebasehosting.googleapis.com`,
`firebase.googleapis.com`, `cloudresourcemanager.googleapis.com`.

## 5. Criar o banco Firestore

Pelo painel: **Build → Firestore Database → Create database → Production mode**,
região `southamerica-east1` (São Paulo).

Ou por CLI:

```bash
firebase firestore:databases:create "(default)" --location southamerica-east1
```

## 6. Registrar o app Web e pegar a `firebaseConfig`

```bash
firebase apps:create WEB "Detetive dos Grafos Web"
firebase apps:sdkconfig WEB            # imprime apiKey, appId, etc.
```

Abra `detetive_grafos.html`, ache o bloco **🔥 BRIDGE FIREBASE** e substitua o
objeto `firebaseConfig` pelos valores impressos. Depois:

```bash
cp detetive_grafos.html public/index.html
```

> Essas chaves são **públicas por natureza** (vão no HTML do navegador). Quem
> protege o banco são as `firestore.rules`.

## 7. Publicar

```bash
firebase deploy --only hosting,firestore:rules
```

A CLI mostra a `Hosting URL` ao final — essa é a URL da turma.

---

## Trocar de projeto / usar outro nome no futuro

1. Crie o novo projeto (painel ou `firebase projects:create novo-id ...`).
2. `firebase use novo-id`.
3. Provisione o Firestore no novo projeto (passo 5).
4. Gere a nova `firebaseConfig` (passo 6) e cole no `detetive_grafos.html`.
5. `cp detetive_grafos.html public/index.html` e publique (passo 7).

Gerenciar projetos:

```bash
firebase use            # projeto ativo
firebase projects:list  # todos os seus projetos
```

---

## Solução de problemas

- **`node`/`firebase` "não encontrado" num terminal novo:** o nvm não carregou.
  Rode `source ~/.bashrc` e confirme `which node` apontando para `~/.nvm/...`.
  Se `which node` mostrar `/mnt/c/...` (Node do Windows), esse é o problema —
  o nvm precisa vir antes no PATH.
- **O placar diz "ainda não configurado":** a `firebaseConfig` está com
  placeholders `COLE_...`, ou o aluno entrou sem código de sala.
- **Nada aparece no placar / escrita bloqueada:** confirme que as regras foram
  publicadas (`firebase deploy --only firestore:rules`) e que o código tem
  **exatamente 4 dígitos** (as regras exigem isso).
- **`Callers must accept Terms of Service`:** aceite os termos criando um
  projeto pelo painel (veja o passo 2/3).
- **API desativada (403):** habilite a API pelo link do erro e aguarde ~1 min.
- **`Permission denied` no deploy:** `firebase login --reauth` e confira
  `firebase use`.
- **Custos:** o placar usa pouquíssimas leituras/escritas; para uma turma cabe
  folgado no plano gratuito **Spark**. Sem cartão de crédito.
