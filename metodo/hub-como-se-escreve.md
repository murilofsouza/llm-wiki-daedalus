# A camada do cliente — como se escreve
Updated: 2026-08-03

O material que o cliente lê é uma **camada própria**, escrita à mão, que vive no vault ao lado das
specs e é publicada por script. Esta página é a voz dela, o que fica de fora, e o que garante que a
tradução não fique atrás da fonte.

*(No projeto de origem essa camada se chama `Hub/`. O nome é do projeto; a camada é do método.)*

## A voz: uma proposta única no presente

- **Sem data de decisão interna, sem cronologia de revisão** ("decidido em", "voltou à mesa", "a versão
  anterior desta spec") e **sem atribuir cada ideia a quem a pediu** ("o briefing de vocês", "vocês
  pediram").

  O bug não é de estilo: **o material cresce por acréscimo**, e cada rodada empilha marca de tempo até
  o documento **narrar o próprio histórico em vez da proposta**. Uma limpeza completa já foi necessária
  uma vez.

- **Decisão fechada é estado (✅), não evento datado.** A versão do material aparece **uma vez**, no
  rodapé da capa.

- **O que fica:** número medido (**com o viewport ao lado**), regra, limiar e pendência do cliente. Sai
  a moldura temporal em volta deles — o argumento não perde força e o ruído cai por metade.

- **Toda afirmação carrega o status epistêmico: medido, ou hipótese.** Não é ressalva de rodapé — é
  rótulo por afirmação, visível (um selo, um par de ícones), com a régua declarada numa página.
  **Persona, jornada e funil são hipótese** até um dado dizer o contrário; contagem, tempo e taxa
  medidos são fato, com a fonte ao lado.

  O bug que isso evita não é o cliente ser enganado — é ele **decidir** com base numa hipótese
  apresentada com a mesma tipografia de um número medido, e a equipe não conseguir mais separar as
  duas seis semanas depois. Um material de pesquisa sem esse rótulo é um material onde **a parte mais
  forte empresta autoridade para a mais fraca**. E o custo é assimétrico: rotular custa um selo,
  desfazer uma decisão tomada em cima de hipótese custa a fase.

- **Bloco de rodada não se edita, se dissolve.** Uma seção que existe *porque* houve uma rodada ("o que
  o briefing acrescentou") não tem razão de ser sem a cronologia: as decisões dela vão para onde
  pertencem **por assunto**.

- **Nada anterior ao kickoff.** A fase exploratória é referência interna. A reunião de kickoff pode ser
  citada; a data dela, não.

- **Wikilink para doc interno só na linha `Bastidores`**, que o script remove antes de publicar. No
  corpo, **aborta a publicação** — é a proteção contra vazar documento interno para o cliente.

- **O índice lateral é alimentado só pelos `<h2>`.** Item que não precisa de entrada no índice é `<h3>`.
  Uma página com 21 itens em `<h2>` gerou um índice **maior que a tabela que ele deveria resumir**.

## Lint avisa, invariante aborta — e por que a divisão

| | Aborta | Avisa |
|---|---|---|
| **O que é** | consistência mecânica: um fato contra outro fato | voz e tradução: julgamento de quem escreve |
| **Exemplos** | wikilink interno no corpo · H1 ≠ nome do arquivo · tabela-resumo sem a seção | data no corpo · decisão datada · fonte que mudou depois da tradução |
| **Por que assim** | há uma resposta certa, e nada impedia de voltar | **há caso legítimo** — a data de uma medição é informação — e **aviso que dispara à toa treina a ignorar o aviso** |

Reler uma fonte é trabalho de **tradução**, não de sintaxe: bloquear o commit obrigaria a fazer esse
trabalho no meio de outro. Ver [invariantes-de-publicacao](invariantes-de-publicacao.md) para a régua de
entrada de cada checagem.

## O anti-padrão: publicar a nota interna crua

Existe um terceiro modo, e ele é o atalho que se toma quando a camada parece burocracia: **apontar o
gerador direto para a nota interna** e publicar o corpo dela, tirando só o frontmatter e desmanchando
os links. Sem camada, sem tradução, sem hash — e funciona no primeiro dia.

O que ele custa, medido num projeto que fez exatamente isso:

- **Vaza o nome do doc interno.** Um `[[08 — Auditoria de SEO Técnico]]` não some: o desmanchador de
  link o transforma no **texto literal**, e o cliente lê o nome do arquivo interno. Onde a camada
  existe, wikilink interno no corpo **aborta a publicação** — aqui ele é convertido e publicado.
- **Vaza o vocabulário interno pelo título.** "Personas (**provisórias**)", "Documento-base
  (**Passo 0**)" — a nomenclatura de trabalho vira cabeçalho de seção na página do cliente.
- **Publica a contradição.** Uma decisão revertida cuja versão errada seguia viva na nota estava, por
  consequência, **no ar para o cliente** — sem que nada no caminho pudesse acusar.
- **Não há o que verificar.** Não existe `fontes_conferidas` porque não existe tradução para ficar
  velha. Parece economia de verificação; é ausência do objeto verificável.

**A regra:** publicar a nota interna crua não é "a camada mais simples", é **não ter camada** — e o
preço é pago em vazamento, não em tempo. Se a camada não couber no orçamento, o caminho honesto é
publicar **menos** (uma página escrita para o cliente) em vez de publicar o interno inteiro.

## Por que a camada paga o passo a mais

O fluxo: **spec (vault) → camada do cliente (vault) → conteúdo gerado → site.** Os dois últimos passos
são gerados, de uma direção, com invariantes que abortam. O primeiro é **reescrita à mão** — e é de lá
que veio todo desencontro que o projeto acumulou.

**Não porque seja manual.** A tradução é o valor da camada: gerá-la da spec vazaria jargão ou exigiria
tanta anotação que seria o mesmo trabalho. E ela **precisa citar as specs por wikilink**, o que só
existe no vault — num repo, a linha `Bastidores` não tem como apontar para nada.

**O que faltava era saber quando a fonte andou depois da tradução**, e é isso que o hash de fonte
conferida fecha. O passo a mais nunca foi a camada: era rodar o sync à mão, e um hook de pre-commit
matou isso.

**O custo real da camada é outro: o vault não é versionado** — edição na camada do cliente não tem
histórico até o sync rodar.

## As três direções de verificação

Não é uma. São três — e a segunda **só existe se o projeto tiver código**. Projeto que entrega pesquisa,
escopo ou layout liga a primeira e a terceira, e **remove a segunda com uma linha dizendo por quê**;
declarar `codigo_conferido` sem código é campo vazio que a próxima varredura lê como pendência.

```
spec ──hash──▶ camada do cliente ──gerado──▶ conteúdo ──▶ site
 │                                              (verificado por construção)
 └──hash──▶ código construído
```

1. **spec → camada do cliente** (`fontes_conferidas`) — cada página declara suas fontes e guarda o hash
   de cada uma. Hash diferente = a fonte andou depois da tradução.
2. **spec → código** (`codigo_conferido`) — cada spec declara os arquivos que ela governa, com o hash de
   cada um. **A direção que faltava:** a validação com o cliente acontece no construído, a ponte leva da
   tela para a página do cliente, e a spec — que é o documento que vira produto — ficava fora do
   circuito. Nada acusava quando o construído passava na frente dela. Cinco divergências desse tipo
   foram achadas à mão numa varredura de quatro agentes; **achar à mão não escala e não se repete.**
3. **camada → publicado** — gerada, de uma direção. O conteúdo do repo é **gerado**: editar lá é apagado
   no próximo sync.

### Detalhes que custaram caro

- **Hash do corpo, não do arquivo.** Frontmatter é metadado. Hashear o arquivo inteiro fazia mudança de
  `tags`, de `status` — e, depois que a checagem irmã existiu, **a atualização do próprio hash** —
  parecer mudança de conteúdo: o bookkeeping de um verificador disparava o outro.

- **Hash e não data de modificação.** O vault vive em sincronização de nuvem, que mexe em `mtime` ao
  baixar arquivo. O aviso dispararia à toa.

- **Trocar o hash é o último passo, nunca o primeiro.** O aviso existe para forçar a **leitura da spec
  contra o construído**; bumpar antes de conferir compra silêncio e perde a verificação. Numa rodada,
  conferir 9 arquivos achou **12 afirmações velhas** que nenhuma leitura sem gatilho teria achado.

- **A camada de dado fica fora do `codigo_conferido` de propósito.** O arquivo de conteúdo é
  compartilhado por todas as telas: declará-lo faria **toda** mudança de conteúdo sinalizar **as 13
  specs**, e aviso que dispara à toa treina a ignorar o aviso. **Declara-se estrutura** — página e
  componente.

- **Ausência de declaração é informação.** Quatro specs não declaram nada porque **não têm construído** —
  e são também as que a validação com o cliente não alcança por esse caminho. Spec sem declaração
  significa *"não existe construído a conferir"*, não *"esqueceram de declarar"*. Isso precisa estar
  escrito, senão a próxima varredura a lê como lacuna.

---

← [SCHEMA](SCHEMA.md) · [invariantes-de-publicacao](invariantes-de-publicacao.md) · [regra-de-chegada](regra-de-chegada.md)
