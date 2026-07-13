<!-- Gerado por npm run agent:handoff. Nao editar manualmente. -->
# Implementacoes em andamento

Resumo operacional gerado de `.agents/continue.ia`.

## FT-014 - Topologia explicita de repositorio aplicacao e artefato

Objetivo: Eliminar ambiguidade entre root do repositorio, root da aplicacao e root do artefato publicado; corrigir caminhos e validacoes para que a fonte interna nao vaze ao pacote distribuivel.

<table>
<thead><tr><th>Etapa</th><th>Tarefa</th><th>Status</th></tr></thead>
<tbody>
<tr>
<td rowspan="3">Diagnostico arquitetural</td>
<td>Reler AGENTS, subordinados e RCF aplicaveis</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Mapear repositorio, fonte distribuivel e dist</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Confirmar inconsistencias de caminho e paridade</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td rowspan="4">Contrato e estrutura</td>
<td>Declarar os tres roots e sua precedencia</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Canonizar AGENTS.md em raiz, fonte e distribuicao</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Corrigir main do manifesto para a raiz publicada</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Tornar obrigatoria a paridade da fonte normativa distribuivel</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td rowspan="3">Validacao</td>
<td>Regenerar indice e artefato</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Verificar manifesto, roots e ausencia de src publicado</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Executar verificacao integral e diff estrutural</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td rowspan="3">Fechamento</td>
<td>Atualizar memoria canonica e handoff derivado</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Compactar contexto operacional</td>
<td><span style="color:#15803d">&#9679;</span> concluído</td>
</tr>
<tr>
<td>Commitar, publicar e convergir dev para main</td>
<td><span style="color:#64748b">&#9679;</span> pendente</td>
</tr>
</tbody>
</table>
