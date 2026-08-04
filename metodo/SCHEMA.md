# Método de projeto — o mapa
Updated: 2026-08-04

O **workflow de projeto**: como um projeto se organiza, por onde entra coisa nova, o que é fonte única,
como o material do cliente fala e o que a publicação verifica sozinha.

É a parte de **boilerplate** deste repo — o que se lê para montar um projeto, não um registro de projeto
já feito. Vale para trabalho de cliente e para produto próprio (aí o "cliente" é o produto).

**Leia esta página primeiro; abra as demais sob demanda.** Mesma função do `SCHEMA.md` de uma wiki de
projeto — e a mesma regra: quem abre uma página tem de conseguir ler ela inteira numa sessão.

> **De onde ele vem.** Cada regra aqui foi paga por um defeito real num projeto de redesign completo,
> e chega **com o bug que a originou**. Isso não é anedota: **regra sem razão o próximo dilui**, porque
> não sabe o que ela custou. O exemplo está ali como prova, não como escopo.
>
> **E o bug entra sem nome.** Este repo é público: a prova é a situação e o número — *"a contagem chegou
> a viver em 11 arquivos"* —, nunca o cliente, a cidade ou o concorrente. Regra nova que só se sustenta
> nomeando alguém não entra; se o número não bastar para convencer, a regra não estava madura.

## Comece por aqui

| Pergunta | Resposta |
|---|---|
| **Como se organizam as pastas de um projeto?** | [estrutura-de-projeto](estrutura-de-projeto.md) — número é ordem de leitura, nome é assunto |
| **Como se chama o arquivo?** | [estrutura-de-projeto](estrutura-de-projeto.md#a-mesma-regra-desce-um-nível-o-nome-do-arquivo-é-a-disciplina-do-documento) — a disciplina daquele documento, nunca a fase |
| **Chegou pesquisa / decisão / mudança que o cliente lê. Onde vai?** | [regra-de-chegada](regra-de-chegada.md) — três perguntas, três destinos |
| **Este número aparece em quantos lugares?** | [fonte-canonica](fonte-canonica.md) — número medido não se copia, cita-se com a fonte |
| **Como se escreve o material que o cliente lê?** | [hub-como-se-escreve](hub-como-se-escreve.md) — proposta única no presente |
| **O que a publicação verifica sozinha, e o que ela recusa publicar?** | [invariantes-de-publicacao](invariantes-de-publicacao.md) |
| **Como se confere um wireframe / entregável?** | [verificacao-de-entregavel](verificacao-de-entregavel.md) |
| **Quem faz cada fase, e com que profundidade?** | [fases-e-agentes](fases-e-agentes.md) |
| **O cliente lê a documentação num site?** | [portal-de-documentacao](portal-de-documentacao.md) — **capacidade opcional** |
| **O que é o padrão da wiki em si (ingest, query, lint)?** | [`guide.md`](../guide.md) · [`health-check.md`](../health-check.md) |

## Páginas

| Página | Conteúdo |
|---|---|
| [estrutura-de-projeto](estrutura-de-projeto.md) | pastas, numeração, **nome de arquivo**, os três arquivos da raiz, registros contínuos, `_arquivo/`, quando e como dividir um doc |
| [regra-de-chegada](regra-de-chegada.md) | a rota de qualquer coisa nova, para entrada nova não virar fonte concorrente |
| [fonte-canonica](fonte-canonica.md) | a tabela de fonte única; como um número medido se propaga sem deixar cópia velha atrás |
| [hub-como-se-escreve](hub-como-se-escreve.md) | a camada do cliente: voz, o que fica, o que sai, e as três direções de verificação |
| [invariantes-de-publicacao](invariantes-de-publicacao.md) | o que aborta a publicação, o que só avisa, e a régua para uma checagem entrar |
| [verificacao-de-entregavel](verificacao-de-entregavel.md) | como se confere um wireframe: densidade, conteúdo de exemplo, variantes, critério de aceite exercitável, **o que trava a medição** |
| [fases-e-agentes](fases-e-agentes.md) | fase → agente → fan-out, profundidade calibrada, rastreio por superfície |
| [portal-de-documentacao](portal-de-documentacao.md) | a superfície onde o cliente lê: o que ela exige, as decisões de desenho, o contrato de config, **o que só o build de produção lê** e a direção de evolução |

## O que este método não é

- **Não é o escopo de um projeto** — isso é a spec de escopo do projeto.
- **Não é o estado de um projeto** — isso é o `roadmap`, e só ele.
- **Não é a wiki de um cliente** — a wiki do cliente guarda `client` · `stack` · `conventions`
  (os gotchas *daquele* stack) e **linka** este método.
- **Não é o padrão da wiki-curadora** — isso é [`guide.md`](../guide.md). Este método é o que se
  documenta; o guide é como a documentação se mantém enxuta.

## O que vale em todo projeto, e o que depende de haver construção

**Nem todo projeto termina em código.** Alguns entregam pesquisa e escopo; outros, pesquisa e layout;
outros vão até o ar. O método é o mesmo — o que muda é **quanto dele liga**.

| Sempre | Só quando há construção |
|---|---|
| [estrutura-de-projeto](estrutura-de-projeto.md) | [verificacao-de-entregavel](verificacao-de-entregavel.md) — precisa de coisa construída para medir |
| [regra-de-chegada](regra-de-chegada.md) | o rastreio por superfície e a coluna de implementação ([fases-e-agentes](fases-e-agentes.md)) |
| [fonte-canonica](fonte-canonica.md) | a direção de verificação **spec → código** ([hub-como-se-escreve](hub-como-se-escreve.md)) |
| [hub-como-se-escreve](hub-como-se-escreve.md) + [invariantes-de-publicacao](invariantes-de-publicacao.md) — desde que exista camada publicada para o cliente | [portal-de-documentacao](portal-de-documentacao.md) — **só se o cliente lê a documentação num site**; quem entrega em documento ou apresenta em reunião não tem portal |

**Decida onde o projeto termina no começo**, e remova o que não se aplica com uma linha dizendo por quê.
Verificação sem alvo é pior que verificação ausente: campo vazio a próxima varredura lê como pendência.
Os quatro pontos de parada estão em
[fases-e-agentes](fases-e-agentes.md#onde-o-projeto-termina--e-o-que-sai-com-ele).

## Referenciado, nunca copiado

**Regra cross-cliente que aparecer duplicada numa wiki de cliente é bug.** O caminho óbvio — copiar
a estrutura para cada cliente novo — é exatamente a falha que este método existe para evitar: em seis
meses existem seis versões divergentes dele e nenhuma é a fonte. É a primeira das três regras abaixo
aplicada ao próprio método.

**As pastas não se copiam; a regra que as gera, sim.** O conjunto `0 Briefing … 7 Entrega` entra como
**default de redesign, explicitamente adaptável**. O que não se adapta é *número é ordem de leitura,
nome é assunto — nunca fase*.

### O que é config do projeto, não regra do método

O que varia por projeto vira **configuração declarada**, não prosa repetida. A regra fica aqui; o
valor mora no projeto:

| Config | O que é | Onde mora |
|---|---|---|
| Exceções de título | H1 que legitimamente difere do nome do arquivo, **com o motivo escrito** | config do sync do projeto |
| Termos que andam em par | conjuntos de nomes que uma enumeração não pode partir (subcategorias, cidades, formatos) | config do sync do projeto |
| Alvos da fase de descoberta | quais concorrentes, quais referências, o que é 100% e o que é médio | doc de método do projeto |
| Números medidos | densidade, contagens de acervo, baseline de audiência | o doc que mediu — citado com a fonte, nunca copiado |
| Padrões de varredura | o "vizinho estável" que acha as ocorrências naquele corpus | anotado onde a propagação foi feita |
| Regras de domínio | o que o negócio do cliente exige ou proíbe, e o que o stack dele impõe | wiki do cliente |
| Prefixo de rota da documentação | onde a camada do cliente é servida — **e é também nome de pasta**, então os dois se conferem | config do portal ([portal-de-documentacao](portal-de-documentacao.md)) |
| Forma do hash de verificação | `trim`, comprimento, sentinelas aceitas — diferença aqui produz **falso positivo em massa** | config do portal |
| Credencial (senha, segredo de sessão) | **não é config** — config vai versionada. Fica no código com razão escrita, ou em ambiente falhando fechado; o que não vale é não declarar qual | fora da config, declarado |

## Onde mora o quê

- **Prosa e protocolo vivem aqui**, inclusive a prosa *sobre* código — o conceito da camada do
  cliente, as invariantes como regra. Prosa dentro de um template de repo seria forkada por projeto e
  divergiria.
- **Código executável vive no template de repo** — o sync, a casca do portal de documentação, o
  pipeline. O critério de divisão é **o que o artefato é, não o assunto**.
- **`hub-sync` é pacote, nunca skill.** Ele aborta a publicação, no gargalo por onde todo conteúdo
  passa; skill é prosa e depende de leitura. A divisão que vale generalizar: o agente **escreve**, o
  sync **verifica**.
- **Os `protocols/` deste repo são release, não espelho.** A fonte que roda e evolui é a instalação
  do agente (`~/.claude/commands/`); o repo é atualizado em marco. Divergência com direção declarada
  é versão; sem direção declarada é bug — e foi assim que as três cópias ficaram para trás.

## As três regras que este padrão pagou para aprender

1. **Número medido não se copia — cita-se com a fonte.** Uma contagem de chamadas chegou a viver em
   11 arquivos, e a fase do projeto em oito.
2. **Reverter decisão é editar o estado vigente, não criar arquivo novo.** Uma decisão existia só
   para corrigir um item de outra — e o item errado ficou lá.
3. **Corrigir o sintoma não resolve: ache o gerador.** A mesma linha foi apagada duas vezes porque o
   doc-índice que a produzia continuava mandando reconstruí-la.

## Montar projeto novo

1. [estrutura-de-projeto](estrutura-de-projeto.md) — cria as pastas e os três arquivos da raiz.
2. Wiki no nível do **cliente**, `SCHEMA.md` primeiro, linkando este método (ver
   [`protocols/wiki-setup.md`](../protocols/wiki-setup.md)).
3. [fases-e-agentes](fases-e-agentes.md) — calibra profundidade por frente e escreve o doc de método
   *do projeto* (só o que é dele: alvos, agentes, o que é 100%).
4. [regra-de-chegada](regra-de-chegada.md) — cola as três perguntas no `SCHEMA` do cliente.
5. Se o projeto tem camada de cliente publicada: [hub-como-se-escreve](hub-como-se-escreve.md) e
   [invariantes-de-publicacao](invariantes-de-publicacao.md) antes da primeira página.

---

← [README](../README.md) · [guide](../guide.md)
