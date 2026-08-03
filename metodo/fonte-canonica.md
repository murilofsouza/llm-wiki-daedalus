# Fonte canônica e propagação de número
Updated: 2026-08-03

Duas coisas, que são a mesma: **onde um fato mora** e **o que acontece quando ele muda**. É a página
que o projeto pagou mais caro para escrever.

## A forma: uma tabela de fonte canônica por tema

No `SCHEMA` da wiki do cliente, uma tabela `tema → canônico`. Nada mais, e nada de prosa:

| Tema | Canônico |
|---|---|
| Fase do projeto | o `roadmap`, e **só ele** |
| Decisões vigentes | o registro de estado — os datados são registro do evento |
| Pendências | o registro interno (superset) · a camada do cliente é espelho |
| Escopo | o contrato de escopo — **o que não está lá não existe** |
| Números medidos (cada classe) | o doc que mediu |

A tabela vale porque é **curta e exaustiva**: quem vai escrever um número procura o tema, acha o dono,
e cita. Tabela com prosa vira a nona página de wiki e ninguém consulta.

## Número medido não se copia — cita-se com a fonte

Uma contagem de chamadas da home apareceu como referência de densidade em toda spec de tela, em quatro
páginas da camada do cliente e no contrato: **11 arquivos**. A fase do projeto chegou a oito.

O que se copia é a **referência**; o valor mora num lugar. Quando o valor muda, o custo é editar um
arquivo em vez de encontrar onze.

## Reverter é editar o estado vigente

Ver [regra-de-chegada](regra-de-chegada.md#2-chegou-decisão). Aqui só a consequência: **uma verdade em
dois arquivos, com o desmentido no arquivo novo, é duas fontes** — e a que vence é a que a próxima
pessoa abrir primeiro.

## Ache o gerador, não o sintoma

A linha de uma feature descartada foi apagada **duas vezes** e voltou, porque o **mapa de
oportunidades** — o doc-índice que os outros consomem — continuava mandando reconstruí-la. Corrigir os
docs derivados é trabalho que se repete até alguém corrigir a origem.

Quando um erro aparece num doc derivado, a pergunta é *que doc produziu isso?*, não *onde mais isso
aparece?* — as duas se fazem, nessa ordem.

## As cinco regras de varredura

### 1. Varra pelo padrão, não pelo achado — e o padrão certo é o vizinho estável

Buscar o termo que mudou ("Mídia") nomeou **4 arquivos**. Buscar `Colunas ▾` — um pedaço do diagrama
ASCII da navegação, que se repete em várias specs e **não mudou** — achou **11**, duas delas que
ninguém tinha visto.

O termo que mudou está, por definição, escrito de N formas ("a Home mede", "home nova medida =",
"88 chamadas em ASCII parecem 88 linhas"). O vizinho estável está escrito de uma.

### 2. Achado aponta onde dói, não onde termina

Corrigir apenas os dois lugares que o achado nomeava deixou o número velho em **cinco outros** — a
auditoria de SEO sozinha tinha **sete ocorrências**. O achado é a amostra, não a lista.

**E varra todo doc vigente.** Poupe só o histórico: log, planos fechados, `_arquivo/`. Ali o número
velho é correto.

### 3. Número republicado tem cauda, e a cauda mora no mesmo arquivo

A primeira passada na densidade atualizou **a tabela** e deixou os mesmos valores em **seis outros
pontos da própria spec**: tabela de posições, projeção da barra fixa, checklist do comercial e as duas
linhas do resumo.

**A tabela é onde se olha primeiro; a prosa em volta é onde a afirmação velha sobrevive.** Um bloco
novo obriga a reler toda afirmação da tela, não só a tabela — uma nota afirmava "zero item novo, então
a densidade é menor", virou falsa com dois itens acrescentados, e era texto que o cliente lia.

### 4. Depois de mudar estrutura, verifique bloco a bloco — `grep` só acha número

Varredura por dígito corrige contagem desatualizada e deixa passar bloco **descrito** errado ("os dois
slots laterais ficam empilhados", quando viraram um por coluna). São **dois defeitos diferentes** e só
um deles é achável por busca.

### 5. Ausência num `grep` é hipótese, não achado

Um valor passado como `slot:` não aparece numa busca por `nome="…"` — e a varredura **acusa falta onde
não há**. Confirme a forma de passagem antes de reportar divergência.

## Número em registro de medição não se substitui, se anota

Um log de medição é log **do que foi medido**: trocar o número antigo pelo novo faz o documento afirmar
que mediu o que não mediu.

O tratamento é **por natureza da frase**:

| A frase… | Faça |
|---|---|
| afirma **estado atual** | remeça e substitua |
| usa o número como **acessório** de outra afirmação ("sem regressão: N chamadas") | tire o número |
| é **medição datada** | mantenha e marque como superada |

## Estado tem fonte única, e não mora no `src/`

Quando o estado do projeto esteve em quatro lugares, o cabeçalho do site anunciou "etapa 3 → 4" durante
**a etapa 4 inteira**. A cadeia certa é uma: frontmatter do doc canônico → manifest gerado → toda peça
que exibe.

E o agravante que vale como regra própria: **busca no vault não alcança texto que mora no código.** Uma
string hardcoded num componente é invisível para toda varredura de documentação — é o pior lugar
possível para um fato que a documentação afirma.

## Não renumere lista que outro doc referencia

Os itens de uma seção eram citados de **cinco páginas**; inserir um no meio quebra as referências **em
silêncio** — nada falha, os números só passam a apontar para o item errado. **Sub-item com letra**
(`3b`, `6b`) resolve sem mexer na numeração.

## Métrica com divisor que cresce engana

Acrescentar um bloco **derrubou** o indicador de anúncio por dobra (0,38 → 0,36): as faixas continuaram
as mesmas e **o divisor aumentou**. Publicar só esse número é verdade enganosa.

Vão **duas colunas**: uma só com o numerador restrito (*anúncio por dobra*) e uma com o total
(*comercial por dobra*). Vale para qualquer razão cujo denominador é o tamanho da página.

## O limite honesto: auditoria acha contradição, não engano compartilhado

Quando o vault, a camada do cliente, as specs e o construído afirmam **a mesma coisa errada**, não há
divergência a detectar e **nenhuma varredura pega**. Foi assim que um menu listou o canal do cliente
como irmão de um dos programas dele por semanas.

**O detector é conversa com quem sabe, não busca.** Uma auditoria de sincronia promete **coerência
entre superfícies**; não promete acerto sobre o mundo. Prometer o segundo é o que faz a equipe parar de
perguntar.

---

← [SCHEMA](SCHEMA.md) · [regra-de-chegada](regra-de-chegada.md) · [invariantes-de-publicacao](invariantes-de-publicacao.md)
