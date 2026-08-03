# A regra de chegada — para entrada nova não virar fonte concorrente
Updated: 2026-08-03

O que faz um vault de projeto virar zona **não é volume**: é pasta com nome de fase, que termina, em
vez de assunto, que persiste — e isso a [estrutura](estrutura-de-projeto.md) resolve. O que sobra é a
**rota de qualquer coisa nova**. São três perguntas.

## As três perguntas

### 1. Chegou pesquisa ou dado novo?

Atualiza o documento **do assunto**. Não abre arquivo.

**Ganha arquivo próprio só se for assunto novo** — e aí ele **nasce declarado** na linha `Bastidores`
da página do cliente que ele alimenta. Senão a camada do cliente não sabe que ele existe, e a fonte
fica órfã na primeira auditoria.

### 2. Chegou decisão?

Vai no **§Vigente** do registro de estado das decisões, empurrando o anterior para **§Histórico**.
**Nunca um arquivo de decisão novo.**

O bug, em três camadas:

- Uma decisão (`DEC-007`) nasceu **só para corrigir um item** de outra (`DEC-005`) — e mesmo depois de
  escrita, **o item errado continuou onde estava**.
- O registro datado mais citado do projeto tinha **157 ocorrências em 38 arquivos** e **três de cinco
  itens furados**. Outro tinha **dois de cinco itens falsos**, com o aviso de que eram falsos morando
  em *outro arquivo*.
- **Uma verdade em dois arquivos, com o desmentido no arquivo novo, não é correção** — é a mesma
  mecânica que fez uma feature descartada ressuscitar três vezes.

**Os registros datados continuam existindo, e devem.** Cada um é o registro do **evento**: a decisão
como foi tomada, com a razão de então. História não se edita. **O que vale agora é a página de estado**,
e é a única que se lê para saber o que vale.

**A entrada declara onde a decisão é executada.** É isso que transforma auditoria em *percorrer os
ponteiros* — sem o ponteiro, conferir se a decisão virou realidade é busca livre.

### 3. Mudou algo que o cliente lê?

A página da camada do cliente, com o **hash da fonte conferida bumpado no mesmo commit**. A publicação
avisa se você esquecer — ver [hub-como-se-escreve](hub-como-se-escreve.md).

## A quarta rota: nenhum item nasce em ata

**Ata registra fala.** Pendência mora no registro único, com dono e o que trava. Quando as ações
ficavam na tabela de cada ata, a lista de trabalho do projeto era a união de N atas — e ninguém a
mantinha.

## A spec é o destino canônico de qualquer achado que mude o que se constrói

A rota que as três perguntas não cobrem, e a que não é óbvia: uma pesquisa foi **feita, respondida e publicada
para o cliente** — e a spec que vira código **não soube**. Nada acusava, porque o achado tinha
percorrido dois dos três destinos.

Então: todo achado que muda *o que se constrói* passa pela spec, mesmo quando já foi comunicado. As
outras duas rotas (assunto e cliente) não substituem essa.

## O fluxo inteiro, numa linha

```
entrada nova ─┬─ pesquisa/dado ──▶ doc do assunto  (arquivo novo só se assunto novo, e declarado)
              ├─ decisão ────────▶ §Vigente do estado  (o datado é história, não fonte)
              ├─ o cliente lê ───▶ página do cliente  (+ hash da fonte no mesmo commit)
              └─ muda o construído ─▶ a spec  ← esta é a que se esquece
```

---

← [SCHEMA](SCHEMA.md) · [estrutura-de-projeto](estrutura-de-projeto.md) · [fonte-canonica](fonte-canonica.md)
