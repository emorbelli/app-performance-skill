---
name: app-performance
description: Audita e corrige a performance de apps e páginas criados com Claude (vibe coding) — artifacts HTML, dashboards, apps React/Next.js, backends em Supabase/Postgres e integrações com IA. Use sempre que o usuário pedir para deixar um app mais rápido, revisar performance, dizer que algo está lento/travando/demorando para carregar, pedir checklist de performance antes de publicar, ou ao construir um app novo que vai ter listas grandes, busca, imagens, banco de dados ou chamadas de IA — mesmo que não diga "performance".
---

# App Performance — checklist de 31 pontos, filtrado para a nossa realidade

App lento perde usuário, e quase sempre a causa é uma decisão técnica, não falta de servidor.
Esta skill transforma uma lista de 31 otimizações (fonte: reel de @gustagoat.ai, nov/2026) em um
processo prático, **já filtrado pelo que faz sentido no tipo de coisa que a gente cria**.

## Passo 0 — Identificar o tipo de criação

Antes de qualquer coisa, classifique o projeto. Isso decide quais itens valem:

| Tipo | Exemplos | Itens que valem |
|---|---|---|
| **A. Página estática / artifact** | Dashboard HTML de um arquivo, relatório, página publicada no claude.ai | Só frontend (grupo F) + testes (grupo T) |
| **B. App frontend + BaaS** | React/Vite/Next.js + Supabase, sem servidor próprio | F + D (banco) + T, e parte de I |
| **C. App com backend próprio** | API Node/Python, Edge Functions, jobs, integrações | Todos os grupos aplicáveis |
| **+ IA** | Qualquer um acima que chama um LLM | Somar streaming da resposta (13), timeout (12) e fila para chamadas longas (7) |

Na dúvida, pergunte ao usuário ou deduza pelo código (package.json, presença de `supabase/`, servidor, etc.).

## Passo 1 — Auditoria (não mude nada ainda)

Percorra o checklist em [references/checklist.md](references/checklist.md) **apenas nos grupos que valem para o tipo**.
Para cada item, procure evidência no código e marque:

- ✅ **OK** — já está feito (cite arquivo:linha)
- ❌ **Problema** — não está feito e deveria (cite onde e qual o impacto)
- ➖ **Não se aplica** — explique em uma linha por quê

Entregue a auditoria como tabela, ordenada por **impacto percebido pelo usuário** (o que ele sente primeiro: tela branca, busca travando, lista engasgando, resposta de IA demorando).

## Passo 2 — Correção priorizada

Corrija em lotes, do maior impacto para o menor, nesta ordem padrão:

1. **Percepção** (barato e muda tudo): skeleton, atualização otimista, streaming da IA
2. **Rede no front**: debounce, deduplicar chamadas, paralelizar, paginar, timeouts
3. **Peso da página**: imagens WebP/AVIF + lazy loading, code splitting, scripts de terceiros adiados, dependências não usadas, fontes
4. **Banco**: N+1, índices, selecionar só as colunas, pool de conexões
5. **Infra** (só tipo C): fila, cache, região do servidor, SSR/SSG

Regras ao corrigir:
- Uma mudança por vez, com o "antes/depois" explicado em uma frase.
- Não adicione infraestrutura nova (Redis, balanceador, OpenTelemetry) sem o usuário pedir — veja a seção "Não aplicar por padrão".
- Não quebre comportamento: depois de cada lote, rode o app/testes e confira que a tela continua funcionando.
- Em artifacts de arquivo único, mantenha tudo inline e respeite as restrições de CDN do artifact.

## Passo 3 — Validar

- Teste com CPU e rede limitadas (DevTools → Performance: CPU 4x slowdown; Network: "Slow 4G").
- Meça antes/depois: tempo até o primeiro conteúdo, tamanho total transferido, nº de requisições, tempo da query mais lenta.
- Para Supabase, rode os advisors de performance (`get_advisors` com tipo `performance` no MCP do Supabase) e trate os avisos de índice faltando / RLS lenta.

## Não aplicar por padrão (overkill para o nosso porte)

Estes itens do vídeo são válidos para apps grandes, mas **não** os implemente sem pedido explícito:

- **Cache em Redis** → antes, use cache HTTP, cache no cliente (React Query/SWR), `stale-while-revalidate` ou views materializadas no Postgres.
- **Distribuir tráfego entre várias instâncias** → Vercel, Netlify, Supabase e claude.ai já escalam e balanceiam sozinhos. Não é algo que se "pede pro Claude" no código.
- **Rastrear com OpenTelemetry** → para nosso porte, use os logs do Supabase / da Vercel e `console.time` nas rotas suspeitas. Só adote OTel se já houver um backend de observabilidade.

## Já resolvido pela plataforma (só confirmar, não implementar)

- **CDN** e **compressão Brotli/gzip** → Vercel, Netlify, Cloudflare, Supabase Storage e artifacts do claude.ai já entregam. Só confira se não há um servidor próprio servindo estáticos sem compressão.
- **Cache de estáticos no navegador** → automático em builds com hash no nome (Vite/Next). Em artifacts não é configurável.

## Formato de entrega

```
## Auditoria de performance — <nome do app> (tipo <A/B/C>)
| # | Item | Status | Evidência / impacto |
...
## Plano de correção (ordem de impacto)
1. ...
## Corrigido nesta rodada
- ... (arquivo:linha) — antes → depois
## Não aplicado e por quê
- ...
```
