[English](README.en.md) | **Português**

# app-performance — Skill para Claude

Skill que audita e corrige a performance de apps criados com Claude (artifacts, dashboards, apps React/Next.js + Supabase, integrações com IA).

Baseada nas 31 otimizações apresentadas no reel de [@gustagoat.ai](https://www.instagram.com/reel/Dfby7sPgMPU/) (nov/2026), **filtradas pelo que se aplica ao nosso tipo de criação**:

| Veredito | Qtde | Itens |
|---|---|---|
| ✅ Aplicar sempre | 18 | skeleton, WebP/AVIF, deduplicar chamadas, debounce, paginação, N+1, remover deps, timeouts, streaming da IA, atualização otimista, lazy loading, requisições em paralelo, índices, scripts de terceiros adiados, virtualizar listas, só as colunas necessárias, testar em celular/rede lenta, rastreio leve de requisições lentas |
| ⚠️ Condicional | 8 | fila, servidor perto do banco, SSR/SSG, code splitting, pool de conexões, re-render, fontes, monitoramento de usuários reais |
| 🏗️ Já vem da plataforma | 3 | CDN, Brotli/gzip, cache de estáticos |
| 🚫 Não aplicar por padrão | 2 | Redis, várias instâncias / balanceamento |

Detalhes e justificativas em [`app-performance/references/checklist.md`](app-performance/references/checklist.md).

## Como instalar

**Claude Code** (pessoal, todos os projetos):

```bash
git clone https://github.com/emorbelli/app-performance-skill.git
cp -r app-performance-skill/app-performance ~/.claude/skills/
```

Ou por projeto: copie a pasta `app-performance/` para `.claude/skills/` do repositório.

**Claude.ai / Claude Desktop:** compacte a pasta `app-performance/` em `.zip` e envie em *Configurações → Capacidades → Skills*.

## Como usar

Basta pedir naturalmente:

- "deixa esse app mais rápido"
- "faz uma auditoria de performance antes de publicar"
- "a busca está travando / a tabela está lenta"

A skill classifica o projeto (artifact, front + Supabase ou backend próprio), audita só os itens que valem, corrige em ordem de impacto e valida em rede/CPU lentas.
