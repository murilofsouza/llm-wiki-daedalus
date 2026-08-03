# Invariantes de publicação
Updated: 2026-08-03

**Invariante mora no gargalo, não em prosa.** O script de publicação é o único ponto por onde todo
conteúdo passa, e ele já pode abortar. Uma regra escrita numa página de convenções depende de alguém
ler a página.

A justificativa dura, medida: **três auditorias seguidas do mesmo projeto acharam lote novo de
contradição em material já auditado.** O que tem verificação automática não volta; **o que depende de
leitura, volta sempre.**

## A régua para uma checagem entrar

Duas condições, e as duas são obrigatórias:

1. **Consequência real** — o defeito já quebrou uma vez, foi corrigido à mão, e nada impedia de voltar.
2. **Forma clara** — dá para decidir olhando o texto, sem entender a frase.

E um teste antes de ligar: **rode contra o corpus inteiro e leve a zero falso positivo.** Duas checagens
nasceram acusando texto correto e foram reescritas até dar zero. **Checagem que grita em linha correta
envenena as outras** — quem aprende a ignorar um aviso ignora os quatro.

**Nada de heurística de tom** (voz passiva, tamanho de frase). Aviso que dispara à toa treina a ignorar
o aviso, e o custo cai sobre as checagens que estavam certas.

## O que aborta

### A URL sai do nome do arquivo; o título publicado sai do H1 — e os dois batem

Divergir dá **três nomes para a mesma página**: o do arquivo, o da URL e o que o cliente lê. Aconteceu:
uma página chamada "Pontos para Alinhar" foi servida em `/decisoes-a-tomar`, com **29 links escritos num
terceiro nome**.

**Exceção precisa de razão escrita**, numa lista de config (`EXCECOES_DE_TITULO`). Ficar lá é ato
deliberado — **exceção sem razão transforma a regra em aviso, e aviso não protege**.

### Nenhum literal de slug de seção no código

Havia um `slug === "telas"` decidindo se um atalho aparecia. Renomear a pasta **apagou o atalho em
silêncio** — sem erro de build, sem lint, sem teste falhando. **Derive da fonte de dados que já
existe.**

### Tabela-resumo ↔ seção — só quando a tabela espelha o índice

Linha de tabela com id de item exige a seção correspondente, e vice-versa. O bug: uma tabela-resumo
prometia um item `6b` que não existia, e os dois pedidos ficaram **dentro de um `<details>` recolhido**
do item 6.

**O gatilho é o sub-id com letra** (`3b`, `5c`, `12b`), que é o sinal de que a tabela espelha o índice do
documento. Lista numerada de **ações** usa `1..N` sem letra e não tem relação com os títulos da página —
as duas que existiam davam falso positivo antes desse filtro. **Limitação aceita e escrita:** tabela-índice
que use só números inteiros passa sem checagem. Limitação registrada é decisão; limitação não escrita é
bug latente.

### Wikilink para doc interno fora da linha de bastidores

Ver [hub-como-se-escreve](hub-como-se-escreve.md). É a única invariante cuja razão é vazamento, não
consistência — e é a mais barata de todas.

### Hard wrap no corpo — candidata com as exclusões declaradas

**Consequência real:** parágrafo quebrado em ~110 colunas renderiza `<br>` por quebra num vault com
*strict line breaks* desligado, e o cliente lê o material **picado no meio da frase**. Aconteceu num
projeto real, nas páginas que o cliente lê — a pior superfície possível para um defeito que não é de
conteúdo.

**Forma clara**, mas só depois de excluir o que quebra de propósito: frontmatter · bloco de código ·
linha de tabela · item de lista (continuação junta na linha do item) · quebra explícita (dois espaços ou
`\`). Fora dessas, **duas linhas não vazias seguidas dentro de um parágrafo é o defeito** — é estrutural,
não heurística.

**Antes de ligar, a régua acima vale:** rode contra o corpus inteiro e leve a zero falso positivo. E note
que **a mesma regra é oposta num repo git** — lá o hard wrap está certo (ver
[estrutura-de-projeto](estrutura-de-projeto.md#largura-de-linha-é-do-store-não-do-texto)). A checagem é
do gerador que publica **do vault**, não do repo.

## Detalhes de implementação que custaram caro

- **Detector de link tem de olhar o rótulo, não só o alvo.** Um bug de regex gerou **213 wikilinks com
  alias duplicado** (`Decisões a toma|Decisões pendentes`). O **alvo resolvia**, então nem o detector de
  link quebrado nem o lint de voz pegavam — e o lixo foi publicado **na capa**.

- **Parágrafo colado no `---` vira título.** Sem linha em branco no meio, markdown lê como *setext
  heading*: o parágrafo **inteiro** renderiza como `<h2>` e entra no índice lateral.

- **Hash no YAML vai entre aspas, sempre.** Não é só o caso óbvio de hash só-de-dígitos: `5e070871` tem a
  forma `<dígitos>e<dígitos>` e o YAML lê como **notação científica** — o expoente estoura para
  `Infinity`, que chega ao script como `null`. Três specs nasceram com **aviso eterno** por isso, e o
  sintoma é o pior possível num verificador: aviso que ninguém consegue silenciar trocando o hash.

- **Verificador que não acha o alvo avisa; nunca sai em silêncio.** Errar o nível de pasta na busca fazia
  a função **retornar sem conferir nada** — o pior modo de falhar. Alvo ausente é aviso explícito
  ("nenhuma spec foi conferida"), não sucesso vazio.

## O que foi tentado e descartado — e por que fica registrado

**Checagem de número canônico.** A ideia: fixar um valor por contagem (páginas de autor, edições,
galerias) e acusar divergência. Implementada capturando o dígito adjacente ao rótulo, o que resolve as
três formas de escrita (`~70`, `cerca de 70`, `**~70**`). Rodada contra o corpus inteiro: **7 acusações,
todas falsas.**

O motivo é semântico e **não tem saída textual**: o mesmo substantivo carrega contagens legítimas
diferentes. "11 páginas de autor" são as **ativas**, contra as ~70 que **existem**. "7 edições" é quanto
**cada leitor baixa**, não o tamanho do acervo. "24 galerias" é o que **cabe numa página**. Separar total
de recorte exige entender a frase, não casar um padrão.

**A classe de defeito é real** — a densidade de uma tela já teve quatro valores simultâneos. Mas ela se
pega comparando **documento com documento sobre o mesmo fato**, que é trabalho de auditoria, não de lint
de linha. Ver [fonte-canonica](fonte-canonica.md).

**Isto fica escrito para ninguém reimplementar.** Tentativa descartada sem registro é tentativa que
volta — o custo de escrever o parágrafo é menor que o de repetir a implementação.

## O que é config, não regra

A regra é do método; a lista é do projeto. Cada item abaixo vive em configuração declarada, com o motivo
ao lado (ver a tabela em [SCHEMA](SCHEMA.md#o-que-é-config-do-projeto-não-regra-do-método)):

- exceções de título, com a razão de cada uma;
- conjuntos de termos que uma enumeração não pode partir;
- os padrões de voz vetados naquele material.

---

← [SCHEMA](SCHEMA.md) · [hub-como-se-escreve](hub-como-se-escreve.md) · [fonte-canonica](fonte-canonica.md)
