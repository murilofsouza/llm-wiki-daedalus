# Fases, agentes e rastreio
Updated: 2026-08-03

Quem faz cada fase, com que profundidade, e como uma superfície é acompanhada da spec ao QA.

**Esta é a página mais adaptável do método.** O mapeamento de fase → agente é *forma*; os agentes em si
são de domínio, e um projeto de outra natureza troca a lista inteira. O que não muda é a regra de
profundidade, a regra de fan-out, e o que o rastreio precisa registrar.

## Profundidade é decisão calibrada por frente — não decisão de escopo

Duas frentes do mesmo projeto podem ter profundidades opostas de propósito, e isso se **escreve**:

| Frente | Profundidade | Por quê |
|---|---|---|
| Auditoria técnica + mapa do sistema atual | **100%**, item a item, inventário real | é a base de tudo o que vem depois; erro aqui propaga por todas as fases seguintes |
| Benchmark de concorrentes e referências | **médio** — os padrões mais comuns e o que é característico | exaustão não paga: o número de padrões distintos é pequeno |

Sem isso escrito, "fizemos benchmark" e "fizemos auditoria" parecem o mesmo compromisso — e a diferença
aparece na cobrança, não no plano.

## Fase → agente → fan-out

**Sequencial entre fases** (há dependência real); **paralelo dentro de cada** (fan-out de um agente por
item). Essa é a forma, e é a parte que não muda.

O que muda é a **lista**. A tabela abaixo é o pipeline de **um redesign que vai até o ar** — o caso mais
longo, e por isso o exemplo. **Ele não é o formato de projeto**: o mesmo método atende um projeto que
entrega pesquisa e escopo, ou pesquisa e layout, e para ali.

| Fase | Agente(s) | Fan-out | Saída |
|---|---|---|---|
| 1 — Descoberta | um por site analisado + um de auditoria técnica | ✅ | pastas de pesquisa e de experiência |
| 2 — UX & Oportunidades | um, que **consolida** | consolida | pasta de experiência |
| 3 — Specs de tela | um por tela | ✅ | pasta de telas + design system |
| 4 — Wireframe navegável | um por tela (gêmeas juntas) + build no repo | ✅ | pasta de telas + o repo do entregável |
| 5 — UI / Design System | **tela por tela, com validação a cada tela** | sequencial por tela | design system + a coluna de UI do rastreio |
| 6 — Desenvolvimento | por template | por template | o repo do produto |
| 7 — QA & Performance | consolida os critérios das specs numa suíte | por superfície | pasta de entrega |
| 8 — Go-live & Monitoramento | runbook + janela de monitoramento | sequencial | pasta de entrega |

Duas notas que a tabela não mostra:

- **Uma frente pode ser contínua e cruzar as fases** (no exemplo, SEO). Frente contínua não entra como
  linha da tabela — entra como nota, senão a tabela mente sobre a ordem.
- **A fase 5 é sequencial de propósito**, não por falta de paralelismo: validação a cada tela só
  funciona em série.

## Onde o projeto termina — e o que sai com ele

O pipeline acima tem quatro pontos de parada legítimos, e **cada um desliga uma parte do método**. Decida
qual é o seu **no começo**: descobrir na fase 3 que não haverá implementação é descobrir que metade das
verificações escritas nunca teve alvo.

| Termina em… | O que continua valendo | O que **sai**, e não é lacuna |
|---|---|---|
| **Pesquisa e escopo** | estrutura, regra de chegada, fonte canônica, camada do cliente e suas invariantes | rastreio por superfície · verificação de entregável · a direção **spec → código** |
| **Layout / wireframe** | tudo acima + [verificacao-de-entregavel](verificacao-de-entregavel.md) e o rastreio | a coluna de implementação · a direção spec → código, se o construído é o próprio entregável e não há produto depois |
| **UI entregue a outro time** | tudo acima + a linha de validação com data | QA, go-live e monitoramento — são do time que recebe, e **quem entrega escreve o que eles precisam saber**, não o runbook deles |
| **No ar** | o pipeline inteiro | — |

**A regra por trás:** verificação sem alvo é pior que verificação ausente. Um `codigo_conferido` num
projeto sem código, ou uma coluna de template num projeto de layout, é campo vazio que a próxima
varredura lê como pendência — e treina a ignorar campo vazio. **O que não se aplica se remove, com uma
linha dizendo por quê** — a mesma regra do [`_arquivo/`](estrutura-de-projeto.md).

E o que **não** depende de haver implementação, em nenhum dos quatro casos: a estrutura de pastas, a
regra de chegada, a fonte canônica, a voz da camada do cliente e as invariantes que protegem a
publicação dela. É o núcleo do método — projeto de pesquisa pura tem as cinco.

**Os agentes ficam definidos num lugar reutilizável** (`~/.claude/agents/` na implementação de
referência), não dentro do projeto. Parametrizá-los para um domínio novo é trabalho declarado, não
adaptação silenciosa — os agentes do primeiro projeto carregam o vocabulário dele.

## O rastreio: uma linha por superfície

Uma spec por template; **uma linha por superfície**. A tabela tem colunas do tipo
`spec → UI → validado → template/dev → QA → trava`.

Por que ela existe: a fase de UI é tela por tela com validação a cada tela, e **sem a tabela não há onde
registrar** *"esta superfície foi validada em DD/MM, com estas decisões"*. Antes dela, **três superfícies
chegaram a estar no ar sem spec** sem que nada acusasse.

### As regras da tabela

- **"Validado" recebe data, não ✅.** Um check não diz **quando** nem **com quem** — e é exatamente isso
  que se precisa saber seis semanas depois, quando o cliente pede uma mudança que contradiz o que ele
  aprovou.

- **A variante não ganha arquivo, ganha linha.** É o que resolve a tensão entre "faltam telas" e "menos
  arquivos" — ver [verificacao-de-entregavel](verificacao-de-entregavel.md#tipo-novo-é-variante-do-template-não-tela-nova).

- **A coluna "Trava" é a ponte para o registro de pendências** — é ela que faz o rastreio valer. Sem a
  trava, a tabela diz o estado e não diz o que fazer.

- **A tabela é onde os dois vocabulários se encontram.** O nome que uma superfície tem **no desenho** e o
  nome que ela tem **em quem a implementa** raramente são o mesmo — e num projeto real **não se
  encontravam em documento nenhum**: a spec nomeava um, o repo nomeava outro, e a tradução vivia na
  cabeça de quem construiu. Uma coluna resolve. **Só existe se houver implementação** — projeto que
  entrega layout não tem essa coluna, e forçá-la é inventar trabalho.

- **A spec é o destino canônico de qualquer achado que mude o que se constrói** — ver
  [regra-de-chegada](regra-de-chegada.md#a-spec-é-o-destino-canônico-de-qualquer-achado-que-mude-o-que-se-constrói).

## Frentes paralelas: dono exclusivo por diretório

**Quem constrói relata divergência de especificação e não corrige.** A correção é ação própria, com a
spec aberta.

O bug: uma spec **regrediu** ao ser reescrita a partir de versão antiga, por quem estava construindo e
achou mais rápido consertar de passagem. Fan-out sem dono exclusivo por diretório produz exatamente isso
— dois agentes com a mesma boa intenção e contextos diferentes.

## "A validar com dados" tem de ser honesta

Marcar uma afirmação como *a validar* é dívida, e dívida tem duas saídas legítimas:

- **O dado chegou** → a marca **sai** e o número entra **com a fonte**.
- **O dado não chegou** → a marca **fica**, e **diz de que dado depende**.

O que não é legítimo é a marca genérica que sobrevive a tudo: ela vira ressalva decorativa e ninguém sabe
mais o que destravaria.

**O caso registrado, nas duas direções.** Num projeto, o analytics chegou **depois** de quatro fases
entregues: **nenhuma decisão estrutural foi invalidada** — as quatro apostas de arquitetura se
confirmaram. Mudou **onde** colocar as coisas dentro das telas, não a arquitetura. Vale
registrar que a aposta qualitativa se sustentou — é o que calibra a próxima. Na outra direção, uma segunda
fonte seguiu bloqueada, e era a única que respondia três perguntas específicas: **isso ficou escrito, com
as três perguntas nomeadas.**

Limite conhecido que vale declarar no começo: **research quantitativo depende de acesso**, e acesso é
pendência de terceiro. Escrever isso como hipótese é o que permite tratá-lo como fato quando virar.

---

← [SCHEMA](SCHEMA.md) · [verificacao-de-entregavel](verificacao-de-entregavel.md) · [estrutura-de-projeto](estrutura-de-projeto.md)
