# Estrutura de projeto — pastas, numeração e divisão de doc
Updated: 2026-08-03

Como as pastas de um projeto se organizam, e **por quê**. A pergunta que esta página responde não é
"quais pastas": é *o que decide se uma pasta ainda serve depois de seis meses*.

## A regra que gera as pastas

**O número é ordem de leitura. O nome é assunto — nunca fase.**

O bug: uma pasta chamada `1 — Descoberta` é nome de **fase**, e fase é algo que **termina**. Quando a
descoberta terminou, a pasta congelou no vocabulário e o conteúdo continuou chegando, até acumular
**oito naturezas diferentes** no mesmo lugar — pesquisa, análise de dado, pergunta a terceiro,
viabilidade de fornecedor. Vinte docs, porque ela virou o destino de tudo o que *parecia* pesquisa.

`1 Pesquisa` é assunto: um doc de pesquisa nascido na última fase pertence lá. É o mesmo nome
funcionando dois anos depois.

**O número não é o número da fase.** Uma pasta pode cobrir duas fases (`3 Telas` cobre a spec e o
wireframe; `7 Entrega` cobre QA e go-live). Se o número virar índice de fase, ele volta a ser nome de
fase por outro caminho.

**A fase vive no `roadmap`, e só lá.** O estado do projeto já esteve escrito à mão em **oito lugares**,
e um deles passou uma semana afirmando que a fase do wireframe ainda não havia começado — com o
wireframe no ar. Todos os outros docs **apontam** para o `roadmap`; nenhum afirma a fase.

## Os três arquivos da raiz do projeto

Só três, e cada um existe porque a alternativa falhou:

| Arquivo | Função | O bug que o criou |
|---|---|---|
| **nota-projeto** | o mapa: uma linha por pasta | sem ele, descobrir o que existe é `ls` recursivo |
| **`roadmap`** | fonte única da fase, e o que cada fase abre | a fase em oito lugares, um deles uma semana errado |
| **`PENDENCIAS`** | registro único do que está aberto, **com dono e o que trava** | as pendências estavam em quatro lugares — duas páginas da camada do cliente, uma seção "Em aberto" com nove bullets densos **dentro de uma página de wiki**, e a tabela de ações de cada ata. Nenhum era uma lista de trabalho, e a mais completa estava escondida |

`PENDENCIAS` é o registro **interno e superset** — inclui o que não vai para o cliente (decisões
nossas, bloqueios de ambiente, pesquisa de custo). A camada do cliente é **espelho**, em linguagem de
negócio, e só do que precisa de resposta dele.

## A forma mínima — todo projeto tem esta

Antes de qualquer conjunto numerado, a base. Modelo **Cliente > Projeto** (em produto próprio, o cliente
é o produto). Sub-projeto só quando a iniciativa é claramente distinta — objetivo, ciclo de vida ou
público diferentes; senão mantém flat.

```
{{Cliente}}/
├── {{Cliente}}.md      ← hub de navegação
├── wiki/               ← nível do cliente, compartilhada por todos os projetos dele
├── _Shared/            ← docs de trabalho cross-projeto
├── log.md              ← append-only, uma entrada por sessão
└── {{Projeto}}/
    ├── {{Projeto}}.md  ← nota-projeto: uma linha por pasta
    ├── roadmap         ← fonte única da fase
    ├── PENDENCIAS      ← o que está aberto, com dono e o que trava
    ├── Briefings/  Specs/  Plans/  Meetings/  Decisions/  Assets/
    └── _arquivo/       ← com o motivo escrito
```

**Nenhum doc solto na raiz do projeto além dos três** — cada um nasce na pasta certa, com frontmatter
por tipo. `Specs/` é entregável de cliente; `Plans/` é como o trabalho foi feito (`open/`, `closed/`,
`_INDEX`).

Projeto maior acrescenta a camada numerada abaixo. Projeto pequeno para aqui, e isso é completo.

## A linha do tempo — default de redesign, adaptável

Numeradas porque se leem em ordem:

```
0 Briefing e Método/   → como o projeto opera, briefing respondido
1 Pesquisa/            → o objeto: diagnóstico, inventário, auditorias
2 Experiência/          → o leitor: benchmark, personas, análise de UX
3 Telas/                → uma spec por template, uma linha de rastreio por superfície
4 Camadas/              → o que atravessa as telas
5 Design System/        → tokens, tipografia, grid, princípios de design
6 Especificações/       → o que o dev abre
7 Entrega/              → QA, performance, go-live
```

**Este conjunto é default, não lei.** Projeto de outra natureza troca os assuntos — o que não se
adapta é a regra de cima. Trocar `2 Experiência` por `2 Operação` é adaptação; chamar de
`2 — Análise (em andamento)` é reintroduzir o bug.

## Os registros contínuos, sem número

Não pertencem a fase nenhuma, então **não recebem número** — receber número seria mentir sobre ordem
de leitura:

`Decisions/` · `Meetings/` · `Plans/` · `Fontes/` (material bruto que entra) · `_arquivo/` ·
a camada publicada para o cliente.

## `_arquivo/` não é lixeira — arquivar exige escrever o motivo e o substituto

A prova de que a pasta não é lixeira: um doc foi descartado, e três dias depois um dado novo trouxe
exatamente os dois números que faltavam para reabri-lo. O doc arquivado virou pauta.

Sem o motivo escrito, **a pasta vira lixo por omissão** — ninguém sabe se o que está ali morreu ou só
está esperando. Um índice na própria pasta (`_POR-QUE-ESTA-AQUI`) responde, por arquivo: *por que saiu*
e *o que o substituiu*.

## A wiki fica no nível do cliente

Um cliente, uma wiki — compartilhada por todos os projetos dele. `SCHEMA.md` primeiro, demais páginas
sob demanda. Wiki por projeto duplicaria `client` e `stack` no segundo projeto do mesmo cliente, e a
segunda cópia é a que fica velha.

## Quando um doc se divide

**Split por ciclo de vida ou por público — nunca por tamanho.**

Tamanho é sintoma, não critério. Um arquivo de convenções tinha 24 KB, mas o motivo do split não foi o
peso: eram **três públicos num só lugar**. Um arquivo de stack se dividiu em dois porque descrevia
**dois sistemas com ciclos de vida opostos** — um em manutenção, um em construção ativa.

O pior sintoma dos dois casos foi o mesmo: **~15 princípios de design guardados entre regras de escape
de template e gotchas de linha de comando.** Eram insumo direto da fase de UI, e estavam onde ninguém ia
procurá-los. Conteúdo no lugar errado não é achado por quem precisa dele.

**Arquivo que se divide permanece como roteador.** Os 22 links que apontavam para o arquivo de
convenções e os 20 que apontavam para o de stack continuaram válidos: o original virou uma tabela
*"quero saber sobre… → ler"*, com a nota do split. Redirecionar custa quatro linhas; corrigir 42
links custa uma tarde e erra em silêncio.

## Doc com um único link de entrada em todo o vault é doc esquecido

E o sinal é de **conteúdo no lugar errado**, não de doc pouco lido.

O caso: um doc de método tinha exatamente um link apontando para ele, e vinha do índice que lista tudo
por obrigação. Nenhum doc de trabalho o consultava. A causa não era o assunto — era o recorte: ele
descrevia **só as três fases que já tinham acabado**, então acabou junto com elas. Reescrito para
cobrir o pipeline inteiro, voltou a ser consultado.

Ao achar um doc assim, a pergunta certa é *"que recorte deste conteúdo alguém precisaria hoje?"* — não
*"onde eu linko isto?"*.

---

← [SCHEMA](SCHEMA.md) · [regra-de-chegada](regra-de-chegada.md) · [fonte-canonica](fonte-canonica.md)
