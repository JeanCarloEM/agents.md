# agents-governance

Governanca operacional portavel para agentes IA. `./` organiza o repositorio e a governanca ativa; `src/` contem a fonte interna distribuivel; `dist/` e a raiz do artefato publicado. `src/` nao e a raiz da aplicacao: em Web Page Like, a aplicacao coincide com `dist/` e com o `/` percebido pelo usuario.

## Contratos de Scripts

O contrato tipado reutilizável fica em `.agents/core/contracts.md`; os metaarquivos de CLI e contexto ficam em `.agents/meta/`. O índice `.agents/meta/index.json` relaciona scripts e contextos mínimos (`build`, `release`, `publish`, `maintenance`, `update`, `validation` ou `ia`). Especializações do consumidor pertencem a `agents.local.md`, `.agents/local/` ou `.agents/hooks/` e não são sobrescritas por `agents:update`.

## Operacao

- `npm run clean`: remove `dist/`, `index.json` e `handoff.md` gerados.
- `npm run check`: executa a verificacao local completa.
- `npm run release -- <versao>`: gera `dist/`, `release-note.txt` e `agents-v<versao>.zip`; sem versão, infere somente quando a evidência é determinística.
- `npm run release:trigger -- <versao>`: cria o gatilho transitório `release` para o workflow técnico.
- `npm run release:publish -- <versao>`: executa o ciclo completo de release e aguarda a comprovação remota.
- `npm run agent:status`: resume capacidades canonicas.
- `npm run agent:filter -- --run <comando> [args]`: entrega a saída do comando em JSONL compacto e ordenado para IA.
- `npm run agent:index`: gera `index.json` minificado a partir de `src/`.
- `npm run agent:dist`: gera `dist/`, `dist/package.json`, `dist/release.json` e pacote `agents-v<versao>.zip`.
- `npm run agent:verify`: valida scripts, indexador e dist.
- `npm run agent:agents`: executa atualizacao automatica da governanca operacional pela superficie filtrada, mesclando somente scripts/dependencias declarados em `agentsGovernance` no `package.json` anfitriao.

### Evolução upstream de AGENTS.md

`./AGENTS.md` na raiz rege este repositório construtor; `./src/AGENTS.md` é a aplicação-fonte distribuível e não a sincroniza automaticamente. Em um consumidor, `npm run agent:upstream:check -- --offline` identifica o estado sem rede. A configuração local opcional `.agents/upstream.json` ou `package.json.agentsUpstream` declara `role` (`consumer`, `constructor` ou `dual`), `upstreamRepository`, candidato, limites e cache; candidato não é destino autoritativo.

- `agent:upstream:prepare -- <evidence.json>` sanitiza e grava proposta revisável em extensão local.
- `agent:upstream:publish -- <proposal.json> --authorize` verifica destino, duplicação e token externo antes de criar issue; sem `--authorize`, nenhuma ação externa ocorre.
- `agent:upstream:assess -- <proposal.json>` produz grau e resposta concisa para mantenedor; `agent:upstream:apply-assessment` exige autorização e pode notificar colaboradores somente por opção explícita.
- `agent:test:upstream` verifica sanitização e template sem depender de rede.

### Inbox construtora de issues

`.github/workflows/issues-inbox.yml` recebe somente eventos `issues` de abertura, edição, reabertura ou rotulagem. O payload é sanitizado antes de criar `.agents/local/upstream/inbox/`; o workflow publica essa inbox como artefato por 30 dias e não inclui credenciais ou cabeçalhos.

- `agent:inbox:event -- <evento.json>` valida, sanitiza e indexa um evento localmente.
- `agent:inbox:evaluate -- <registro.json>` produz `rejected`, `not_recommended`, `recommended` ou `highly_recommended`, sem efeito externo.
- `agent:inbox:process -- <evento.json> --role constructor` encadeia indexação e avaliação; `--authorize` é obrigatório para comentário e label.
- `agent:inbox:fetch -- <numero> --role constructor` permite a execução manual; `--dry-run` não emite efeito remoto.
- `agent:inbox:apply -- <avaliacao.json> --role constructor --authorize` comenta recusas e não-recomendações; nos graus recomendados adiciona somente o label configurado e uma justificativa técnica curta. Aceite, fechamento, alteração de fonte e release permanecem decisões humanas.
- `agent:test:inbox` testa sanitização, classificação e índice idempotente sem rede.

### Atualização segura da governança

`agents:update` usa o manifesto versionado recebido no ZIP do release ou na branch primária como definição completa do núcleo gerenciado. O estado local anterior é consultado apenas para converter formatos e remover caminhos antes gerenciados; ele não conserva arquivo que a origem deixou de declarar. `agents.local.md`, `.agents/local/`, `.agents/hooks/` e adaptadores declarados nunca entram no lock, no plano de limpeza ou na sobrescrita.

Cada alteração estrutural do formato traz um descritor de linguagem, marcador de variação e conversor histórico. Configurações equivalentes devem preferir o mesmo parser e descritor para manter transições verificáveis.
- `npm run agent:handoff`: gera [handoff.md](handoff.md) a partir de `.agents/continue.ia`.

## Release

- `.github/workflows/release.yml`: executa release manual ou por commit contendo apenas `release` no root.
- Somente o arquivo `release` no root funciona como gatilho transitório; o workflow remove o arquivo e cria commit `release:`. `publish` fica reservado à Publicação de Conteúdo e este repositório não a aplica.
- `dist/release-note.txt` e o pacote versionado sao gerados localmente por `agent:release` antes da publicacao do GitHub Release marcado como latest.
- Release publicado em `dev` converge a branch primária (`main`, senão `master`); conflito de merge interrompe o workflow.

### Convergência manual de `dev` para `main`

Use este procedimento somente após concluir a FT, com o worktree limpo e a validação integral aprovada. O caminho padrão é fast-forward; não use `--force`, rebase de `main` publicado ou descarte de alterações para contornar divergência.

```powershell
git switch dev
git pull --ff-only origin dev
npm run agent:verify
git switch main
git pull --ff-only origin main
git merge --ff-only dev
git push origin main
git switch dev
git merge-base --is-ancestor main dev
```

O último comando deve retornar sucesso: `main` está no mesmo commit de `dev` ou é ancestral dele. Se o fast-forward falhar, interrompa a publicação, revise a divergência, realize merge normal somente quando ela for compatível, resolva conflito explicitamente, execute novamente `npm run agent:verify` e só então envie `main`.

### Publicação assistida

`release:publish` exige versão explícita, branch `dev`, worktree limpo e workflow presente. O comando atualiza `package.json`, valida o artefato, cria commits separados de preparação e artefato e envia o commit exclusivo `release`; o GitHub Actions cria tag, asset e GitHub Release. Com GitHub CLI autenticado, o comando também acompanha o workflow e confirma a convergência `dev`/primária; sem ele, retorna após enviar o gatilho remoto.

```powershell
# Confere o plano sem alterar arquivos, Git ou GitHub.
npm run release:publish -- 0.0.2 --dry-run

# Publica e acompanha o workflow até a confirmação remota.
npm run release:publish -- 0.0.2

# Envia o gatilho, mas deixa a observação remota para outro operador.
npm run release:publish -- 0.0.2 --no-watch
```

O comando interrompe antes de escrever quando houver alteração local, tag existente, branch incorreta ou dependência remota ausente. Um release já preparado manualmente deve ser concluído ou removido antes de usar o ciclo all-in-one.

## Normas

- [RCF.md](RCF.md): contrato material do projeto.
- [AGENTS.md](AGENTS.md): governanca operacional aplicavel a este workspace.
- [src/AGENTS.md](src/AGENTS.md): fonte do artefato normativo distribuivel.
- [src/.agents/core/update/scenario.md](src/.agents/core/update/scenario.md): contrato de atualizacao automatica.
- [src/.agents/scenarios/web/page-like/scenario.md](src/.agents/scenarios/web/page-like/scenario.md): cenario Web Page Like.
- [src/.agents/scenarios/release/scenario.md](src/.agents/scenarios/release/scenario.md): cenario Release.
- [src/.agents/scenarios/content-publication/scenario.md](src/.agents/scenarios/content-publication/scenario.md): cenario Publicação de Conteúdo.
