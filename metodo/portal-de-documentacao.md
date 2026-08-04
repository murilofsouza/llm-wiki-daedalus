# O portal de documentação
Updated: 2026-08-03

A superfície onde o cliente **lê** a camada escrita para ele: navegação por seção, índice da página,
busca, selo de etapa, comentários, gate de acesso.

> **Esta página é capacidade opcional.** Vale só se o projeto publica documentação para o cliente.
> Projeto que entrega pesquisa e escopo em documento, ou que apresenta em reunião, **não tem portal** —
> e nada aqui se aplica. Ver [SCHEMA](SCHEMA.md#o-que-vale-em-todo-projeto-e-o-que-depende-de-haver-construção).

> **Aqui é a prosa; o código não mora aqui.** O critério de divisão do método é *o que o artefato é*:
> a decisão de desenho e o contrato de config ficam neste repo, os componentes e o motor ficam no
> template/pacote. Prosa dentro de um template seria forkada por projeto e divergiria.

## O que o portal exige para funcionar

Ele **não renderiza pasta de markdown solta**. Ele renderiza a saída de um gerador que leu a camada do
cliente, e essa camada tem forma:

| Exige | Por quê |
|---|---|
| **Seções e páginas na convenção de nome** — `N — Seção/` e `N.M Página` | o número é ordem de apresentação e o nome é a disciplina da página; é daí que saem a URL, a ordem da nav e o `H1` ([estrutura-de-projeto](estrutura-de-projeto.md)) |
| **Capa com o estado do projeto no frontmatter** | é a **fonte única** do selo — do cabeçalho e da capa. Sem ela, o estado volta a ser string no código, e busca no vault não alcança texto no `src/` |
| **A linha de bastidores declarando as fontes** | é a única onde wikilink interno é permitido, e é o que alimenta a verificação de frescor |
| **Um índice de seção quando a ordem não sai do número** | páginas sem prefixo numérico se ordenam pelos wikilinks do índice |

**Se a camada não existe, o portal não é o próximo passo** — o próximo passo é escrever a camada. Ver
[hub-como-se-escreve](hub-como-se-escreve.md), inclusive as bordas de densidade.

## As decisões de desenho, e o bug de cada uma

- **Escala de cor fechada, sem cor de marca.** Destaque é peso, sublinhado e tom. O material é peça de
  argumentação: cor de marca faz a conversa virar estética antes de a estrutura ser aceita. E a escala
  fechada tem efeito colateral bom — vale também para os wireframes, então é uma paleta e não duas.

- **A coluna de leitura é medida, não escolhida.** Num projeto ela era 720px e o corpo dava **92,7
  caracteres por linha** — teto prático ~85. Mexer só na largura não resolve: largura e corpo se somam
  na linha. A régua é medir a largura média do caractere **no estilo real**, não por regra de bolso.

- **O índice lateral é alimentado só pelos `<h2>`.** Item que não precisa de entrada é `<h3>`. Uma
  página com 21 itens em `<h2>` gerou um índice **maior que a tabela que ele deveria resumir**.

- **O gate de acesso não roda em desenvolvimento.** Ele existe para o material não ficar aberto na
  internet, e em `localhost` não há internet de onde alguém chegar. O que ele fazia em dev era outra
  coisa: **bloquear a própria verificação** — medir dobra e densidade passa por abrir a página num
  navegador, e uma rodada de validação terminou com três marcadores de "remedir" porque a medição
  dependia de alguém digitar senha. **O que trava a verificação trava o método.**

  E o registro do que foi **descartado** vale mais que a solução: havia dois contornos que
  funcionavam — servir o build estático à parte, e subir o servidor com um segredo de sessão
  descartável. Os dois eram infraestrutura que o risco não justificava. **O sinal de que se estava
  contornando em vez de resolver: o contorno pedia manipular segredo de sessão para medir a altura de
  uma página.** Quando a saída barata exige tocar em credencial, quase sempre existe uma mais barata
  que não exige.

- **A imagem da capa é opcional, e o placeholder é o default.** Projeto novo nasce sem a faixa e não
  quebra; a imagem entra depois. Quem trocar a imagem **reajusta a âncora do recorte para a nova** —
  ancorar onde a anterior ancorava corta o símbolo em tela estreita.

## O contrato de config

Um arquivo declarado ao lado do repo (`hub.config.json`) carrega **tudo o que varia**. A régua:
**nenhum literal de projeto no motor nem nos componentes**, porque é isso que força a próxima cópia a
ser um fork.

Cada grupo abaixo entrou porque **um segundo projeto provou que varia** — nenhum por antecipação:

| Grupo | Resolve |
|---|---|
| **identidade** | assinatura, rodapé, e a capa (arquivo opcional, dimensões, âncora do recorte, fundo do placeholder) |
| **rotas** | o prefixo sob o qual a documentação é servida, e onde a capa fica |
| **vault** | onde a camada do cliente mora, o nome do arquivo de capa, e a pasta de specs (**ou `null`**) |
| **prototipo** | se existe protótipo navegável a que a documentação faça atalho |
| **gate** | ligado ou não, nome do cookie, rotas liberadas |
| **sync** | exceções de título com o motivo, termos de bastidor do projeto, conjuntos que uma enumeração não pode partir, e a **forma do hash** (`trim`, comprimento, sentinelas aceitas) |

**Duas armadilhas que a config sozinha não resolve:**

1. **O prefixo de rota é config *e* nome de pasta**, porque o roteamento resolve por diretório. Config
   não renomeia pasta. Trocar um e esquecer o outro dá **404 em silêncio** — build verde, lint limpo, e
   todas as URLs apontando para pasta que não existe. **O gerador confere que os dois batem e aborta.**
2. **Credencial não é config.** Senha e segredo de sessão ficam **fora** do arquivo, porque config vai
   versionada. Um projeto pode mantê-los no código com a razão escrita (repo privado, e se vazasse o
   material vazaria junto); outro pode exigir ambiente e **falhar fechado**. As duas escolhas são
   defensáveis; o que não é defensável é não declarar qual foi.

## As costuras: o que o projeto implementa

Onde o portal precisa de algo que **é do projeto**, ele importa um módulo local em vez de adivinhar.
Todo projeto com portal tem esses módulos, e o que muda é o que eles devolvem:

| Costura | Contrato | Variante desligada |
|---|---|---|
| **ponte com o protótipo** | as telas, a seção delas, e a rota de cada uma | devolve vazio, e o portal não mostra atalho cruzado |
| **contagem de comentários** | `{ rota → quantos em aberto }`, e **nunca lança** | devolve vazio, e a nav só perde o número |

**"Nunca lança" é parte do contrato, não zelo.** Contador é enfeite útil; derrubar a navegação porque
ele falhou seria trocar o essencial pelo acessório.

E **a variante desligada não sinaliza falta** — não falta nada, não existe alvo. Verificação sem alvo é
pior que verificação ausente.

## A direção de evolução — declarada

O portal precisa **melhorar com o uso**, e melhoria descoberta num projeto tem de chegar aos outros.
Isso não sai de graça, e a divisão é por **o artefato precisa continuar melhorando ou não**:

| Artefato | Veículo | Alcança |
|---|---|---|
| componentes · escala · gate · motor do gerador + invariantes | **pacote** | quem já existe, por atualização de dependência |
| rotas · config · infra · conteúdo de exemplo | **scaffold** | só projeto novo — e isso fica declarado |

**Invariante nova nasce no pacote, nunca numa cópia** — numa cópia protege um projeto, num pacote
protege todos.

> **Estado hoje: o motor é cópia com direção declarada, e o pacote está pendente.** O gerador é o mesmo
> arquivo em dois projetos, um deles nomeado como fonte: **bug se corrige na fonte e se copia**, nunca
> ao contrário. Divergência com direção declarada é versão; **sem** direção declarada é bug — foi o que
> produziu a defasagem dos protocolos deste repo, e é o que esta linha existe para evitar.
>
> A extração só é honesta agora porque o contrato de config **sobreviveu a dois projetos**. Extrair com
> um consumidor é adivinhar o que é invariante.

---

← [SCHEMA](SCHEMA.md) · [hub-como-se-escreve](hub-como-se-escreve.md) · [invariantes-de-publicacao](invariantes-de-publicacao.md)
