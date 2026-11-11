# Checklist dos 31 itens — com veredito de aplicabilidade

Legenda de aplicabilidade: ✅ **Aplicar** · ⚠️ **Condicional** (depende do tipo/stack) · 🚫 **Não aplicar por padrão** · 🏗️ **Já vem da plataforma**
Tipos: A = página estática/artifact · B = front + Supabase · C = backend próprio

## Grupo F — Frontend (vale para A, B e C)

| # | Item do vídeo | Veredito | Como verificar / o que pedir |
|---|---|---|---|
| 1 | Skeleton no carregamento | ✅ | Toda área que espera dados mostra um esqueleto com o formato final, nunca tela branca ou só spinner. |
| 2 | Imagens em WebP/AVIF | ✅ | `<picture>` com AVIF/WebP + fallback; tamanhos responsivos (`srcset`). Em artifacts, converter antes de embutir (data URI pesa no limite de 16 MB). |
| 4 | Debounce em busca e autocompletar | ✅ | 250–400 ms antes de filtrar/chamar API; cancelar requisição anterior (`AbortController`). |
| 13 | Streaming da resposta da IA | ✅ (se usa IA) | Usar `stream: true` / SSE e renderizar token a token. A primeira palavra deve aparecer em < 1 s. |
| 14 | Atualização otimista (não esperar o servidor) | ✅ | Like, toggle, edição simples: atualiza a UI na hora e reverte se a API falhar. Não usar em pagamentos/ações irreversíveis. |
| 15 | Lazy loading de imagens fora da tela | ✅ | `loading="lazy"` + `decoding="async"`; a imagem principal (LCP) **não** leva lazy. |
| 21 | Scripts de terceiros depois da página | ✅ | Chat, analytics, pixels com `defer`/`async` ou carregados após `load`/interação. |
| 22 | Dividir o JavaScript por página | ⚠️ B/C | Code splitting por rota (`React.lazy`, `import()` dinâmico, Next faz sozinho). Em artifact de arquivo único não se aplica. |
| 23 | Virtualizar listas longas | ✅ | Acima de ~200 linhas: `@tanstack/react-virtual` ou `content-visibility: auto`. Muito comum nas nossas tabelas/dashboards. |
| 26 | Evitar re-render desnecessário | ⚠️ B/C (React) | `memo`, `useMemo`, `useCallback`, estado próximo de onde é usado, não recriar objetos em props. Medir com React Profiler antes. |
| 29 | Hospedar fontes e limitar os pesos | ⚠️ | Limitar a 2–3 pesos e usar `font-display: swap` vale sempre. Auto-hospedar vale em B/C; em **artifacts** só Google Fonts é permitido, então não se aplica. |

## Grupo R — Rede e API

| # | Item do vídeo | Veredito | Como verificar / o que pedir |
|---|---|---|---|
| 3 | Evitar chamadas duplicadas na API | ✅ | Vários componentes pedindo o mesmo dado → uma requisição compartilhada (React Query/SWR ou um cache simples por chave). |
| 5 | Paginar os resultados da API | ✅ B/C | Nunca `select *` sem limite. Supabase: `.range(from, to)`; preferir paginação por cursor em tabelas grandes. |
| 12 | Timeout nas APIs externas | ✅ B/C | Toda chamada externa com timeout (ex. 5–10 s) + mensagem de erro amigável + retry com backoff quando idempotente. |
| 16 | Requisições independentes em paralelo | ✅ | `Promise.all` em vez de `await` em sequência quando uma não depende da outra. |
| 11 | Remover dependências não usadas | ✅ B/C | `npx depcheck`; trocar libs pesadas (moment → date-fns/dayjs, lodash inteiro → import pontual). |

## Grupo D — Banco de dados (B e C)

| # | Item do vídeo | Veredito | Como verificar / o que pedir |
|---|---|---|---|
| 8 | Corrigir consultas N+1 | ✅ | Loop que faz 1 query por item → um join/`in()`. No Supabase, usar embed de relacionamento: `select('*, autor(nome)')`. |
| 17 | Índices nas consultas mais comuns | ✅ | Índice nas colunas de `where`, `order by`, FKs e colunas usadas em políticas RLS. Conferir com `explain analyze` e os advisors do Supabase. |
| 24 | Pedir só as colunas necessárias | ✅ | Trocar `select('*')` pela lista de colunas, principalmente com colunas JSON/texto grande. |
| 25 | Pool de conexões | ⚠️ C / serverless | Em Edge Functions/serverless, usar o pooler do Supabase (Supavisor, porta 6543, modo transaction). Quem usa só `supabase-js` via REST já está coberto. |

## Grupo I — Infraestrutura (principalmente C)

| # | Item do vídeo | Veredito | Como verificar / o que pedir |
|---|---|---|---|
| 6 | Estáticos por CDN | 🏗️ | Vercel/Netlify/claude.ai já servem por CDN. Só agir se houver servidor próprio servindo arquivos. |
| 7 | Tarefas lentas para uma fila | ⚠️ C | E-mail, PDF, processamento de planilha, chamadas longas de IA → fila (Supabase Queues/pgmq ou job em background) e a UI mostra "processando". Em A/B simples, não se aplica. |
| 9 | Compressão Brotli ou gzip | 🏗️ | Plataformas já comprimem. Conferir o header `content-encoding` só se houver servidor próprio. |
| 10 | Cache no Redis dos dados mais consultados | 🚫 | Overkill para o nosso porte. Antes: cache no cliente (React Query), cache HTTP, view materializada. Redis só com volume real e pedido explícito. |
| 19 | Servidor perto do banco | ⚠️ B/C | Região das funções (Vercel/Edge Functions) = região do projeto Supabase. Decisão de configuração, vale conferir uma vez por projeto. |
| 20 | Renderizar no servidor ou no build | ⚠️ C (Next.js) | SSG/ISR para páginas públicas, SSR para dados por usuário. Não se aplica a artifacts nem a SPAs Vite simples. |
| 27 | Cache dos estáticos no navegador | 🏗️ | Builds com hash no nome (Vite/Next) + `Cache-Control: immutable` já resolvem. Em artifacts não é configurável. |
| 28 | Distribuir tráfego entre várias instâncias | 🚫 | Feito pela plataforma (autoscaling). Não é tarefa de código para os nossos apps. |

## Grupo T — Medição e testes

| # | Item do vídeo | Veredito | Como verificar / o que pedir |
|---|---|---|---|
| 18 | Rastrear requisições lentas com OpenTelemetry | 🚫 (versão leve ✅) | OTel completo é overkill. Versão leve: logs do Supabase/Vercel, `console.time` nas rotas, advisors de performance. |
| 30 | Monitorar a velocidade dos usuários reais | ⚠️ B/C | Vercel Speed Insights ou a lib `web-vitals` enviando LCP/INP/CLS. Em artifacts, não se aplica. |
| 31 | Testar no celular e em rede lenta | ✅ | Sempre, antes de publicar: DevTools com CPU 4x e "Slow 4G", e abrir num celular de verdade. |

## Resumo

- **Aplicar sempre (18):** 1, 2, 3, 4, 5, 8, 11, 12, 13, 14, 15, 16, 17, 21, 23, 24, 31 + versão leve do 18
- **Condicional (8):** 7, 19, 20, 22, 25, 26, 29, 30 — depende de ser React/Next, ter backend próprio ou serverless
- **Já vem da plataforma (3):** 6, 9, 27 — só confirmar
- **Não aplicar por padrão (2):** 10 (Redis), 28 (várias instâncias)
