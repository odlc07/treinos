# Deploy do zero + sincronização

Do nada até o app funcionando no iPhone. Mesmo caminho do `studyhub`, num repositório
próprio — assim o ícone na tela de início abre direto nos treinos.

---

## Parte 1 — Criar o repositório no GitHub

1. Acesse <https://github.com/new>.
2. **Repository name:** `treinos`
3. **Public** — obrigatório para o GitHub Pages em conta grátis.
   É seguro: o código não contém nenhum dado seu. Os treinos ficam no navegador e no
   Gist secreto; o token nunca entra no repositório.
4. **Não** marque "Add a README", ".gitignore" nem licença. O repositório precisa
   nascer vazio, senão o primeiro `push` dá conflito.
5. **Create repository**.

---

## Parte 2 — Enviar os arquivos

No terminal, uma linha de cada vez:

```bash
cd "C:\Users\marco\OneDrive\Documentos\Projetos VSCode\treinos"
git init
git add .
git commit -m "Treinos: corrida, natacao e bike com quilometragem de equipamento"
git branch -M main
git remote add origin https://github.com/odlc07/treinos.git
git push -u origin main
```

No `git push` abre uma janela do navegador pedindo login (o Git Credential Manager).
Autorize — só na primeira vez.

**Se der erro:**

| Erro | Causa | Solução |
|---|---|---|
| `remote origin already exists` | rodou `git remote add` duas vezes | `git remote set-url origin <URL>` |
| `failed to push some refs` | o repositório foi criado com README | `git pull --rebase origin main` e repita o push |
| `Authentication failed` | login cancelado | rode o push de novo e complete no navegador |

---

## Parte 3 — Ligar o GitHub Pages

1. No repositório: aba **Settings**.
2. Menu lateral: **Pages**.
3. **Source:** `Deploy from a branch`
4. **Branch:** `main` · pasta `/ (root)` → **Save**.
5. Espere ~1 minuto e recarregue. Vai aparecer no topo:
   *"Your site is live at `https://odlc07.github.io/treinos/`"*

---

## Parte 4 — Pôr o ícone no iPhone

1. Abra `https://odlc07.github.io/treinos/` no **Safari**
   (tem que ser o Safari — pelo Chrome o atalho não vira app de verdade).
2. Botão de compartilhar → **Adicionar à Tela de Início**.
3. Abra pelo ícone. Deve abrir em tela cheia, sem barra de endereço.

Já dá para usar: cadastre seus tênis e bicicletas em **Equipamentos** e comece a
marcar treino no **+ Treino**.

> **Antes de qualquer coisa, leia a Parte 7.** Sem sincronização, o histórico vive
> só no Safari daquele aparelho.

---

## Parte 5 — Gerar o token do GitHub

O token é o que permite a página ler e escrever no Gist onde ficam seus dados.

1. Acesse <https://github.com/settings/tokens/new?scopes=gist&description=Treinos>
   (o link já vem com o escopo certo marcado).
2. **Expiration:** `No expiration`, ou uma data bem à frente.
3. **Confira que só `gist` está marcado.** Com só esse escopo, o token não alcança
   seus repositórios.
4. **Generate token** e copie o valor (`ghp_...`).
   **Ele só aparece uma vez** — se fechar a página sem copiar, gere outro.

---

## Parte 6 — Conectar o primeiro aparelho

1. No app, aba **Ajustes**.
2. Cole o token no campo **Token do GitHub**.
3. Deixe **ID do Gist** vazio e clique em **Criar**.
4. Aparece um alerta com o ID do gist. **Anote esse ID** — você vai precisar dele nos
   outros aparelhos. Ele não é secreto.
5. Clique em **Conectar**.
6. Confira o indicador no canto superior direito: deve dizer `sincronizado HH:MM`.

Use um **gist separado** do da Frequência. São arquivos diferentes (`treinos.json` e
`frequencia.json`), mas misturar os dois num gist só complica o backup à toa.

---

## Parte 7 — Conectar os outros aparelhos

1. Abra a mesma URL no outro aparelho e adicione à tela de início.
2. **Ajustes** → **gere um segundo token ali mesmo**, pelo link da Parte 5.

   > Não mande o token do PC por WhatsApp/e-mail para si mesmo. É uma credencial;
   > deixá-la numa conversa é criar uma cópia que você não controla. Um token por
   > aparelho é grátis, leva 30 segundos, e permite revogar só o do celular se você
   > perder o aparelho.

3. Cole esse token e **o mesmo ID de gist** da Parte 6.
4. **Conectar**.

Marque um treino no celular, volte para a aba do PC, e ele aparece — a página puxa da
nuvem toda vez que você volta para ela, e envia 1,5 s depois de qualquer alteração.

---

## Parte 8 — Atualizar o app depois

Sempre que mexer em qualquer arquivo:

```bash
cd "C:\Users\marco\OneDrive\Documentos\Projetos VSCode\treinos"
git add .
git commit -m "descricao do que mudou"
git push
```

O Pages republica em ~1 minuto. Se o navegador insistir na versão antiga, force o
recarregamento com `Ctrl+Shift+R` (no celular, feche e reabra o app).

Seus dados **não** são afetados por atualizações do código — eles vivem no
`localStorage` e no Gist, não no repositório.

---

## Onde cada coisa mora

| O quê | Onde | Público? |
|---|---|---|
| Código da página | repositório `treinos` | sim (e tudo bem) |
| Seus treinos e equipamentos | Gist secreto + `localStorage` | não listado, mas quem tiver a URL lê |
| Token | só no `localStorage` de cada aparelho | nunca sai daí, exceto para `api.github.com` |

Para revogar um token: **Settings → Developer settings → Personal access tokens →
Tokens (classic)** → `Delete`. O aparelho revogado passa a mostrar
`erro: token inválido ou revogado`, e os dados dele continuam salvos localmente.

---

## Se o Safari limpar os dados

O iOS apaga o `localStorage` de sites que você não abre há semanas — mas **não** o de
apps adicionados à tela de início, que é o motivo de usar o ícone em vez do favorito.
Ainda assim, a rede de proteção é a sincronização (Parte 6) ou um
**Ajustes → Backup → Baixar backup** de vez em quando.

Para restaurar: **Ajustes → Importar arquivo** (ou cole o JSON na caixa de texto).
Importar substitui tudo que estiver no aparelho.
