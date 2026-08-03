# Verificação de entregável
Updated: 2026-08-03

Como se **confere** um entregável construído — wireframe, protótipo, tela. Não é sobre a documentação
nem sobre a camada do cliente: é o que a spec pode prometer e o que só a coisa construída responde.

Estas regras estavam enterradas numa página de convenções de front-end, entre gotchas de framework. São
método: valem para qualquer stack.

> **O *como* medir é do stack** (subir o build, contornar o gate de acesso, qual ferramenta) e mora na
> wiki do cliente. Aqui está **o que** se mede e **o que invalida a medição**.

**As quatro primeiras regras valem para qualquer coisa construída** — inclusive um layout que é o
entregável final, sem produto depois. As duas últimas (discriminador, estado global) são de código: num
projeto que entrega layout elas não têm alvo, e o que não tem alvo se remove. A página inteira **não se
aplica** a projeto que termina em pesquisa e escopo — ver
[SCHEMA](SCHEMA.md#o-que-vale-em-todo-projeto-e-o-que-depende-de-haver-construção).

## Densidade não se valida em texto

**Estrutura e escopo fecham em Markdown; densidade percebida só no HTML renderizado.** O orçamento de
primeira dobra parte das **alturas medidas** na página construída, no viewport real do público, com
folga declarada — nunca do diagrama ASCII.

**E calibrar por padding é proibido.** Se o orçamento só fecha raspando 2 px de espaçamento, **o alvo
está errado** — o número foi ajustado para caber, o que o torna inútil como régua. O padding é a última
coisa que se mexe, não a primeira.

## Conteúdo de exemplo plausível, nunca lorem ipsum

Título curto demais **mente a favor** do layout; longo demais, contra. Texto de exemplo tem de vir do
domínio real — nomes, lugares e comprimentos que o produto vai receber.

Corolário: **reticências ficam proibidas** (alinham destruindo informação) e **limite rígido de
caracteres no painel também** — briga com quem publica em dia de fechamento. Contador, se entrar, é
orientação.

## Variante que muda de forma por breakpoint não aparece no ASCII

Um card que vira horizontal no mobile é **economia certa** numa faixa de 18 itens e **defeito** onde a
imagem é o argumento. As duas decisões parecem idênticas no diagrama. **Só a régua na página construída
pega.**

## Tipo novo é variante do template, não tela nova

Três tipos de arquivo diferentes saíram **do mesmo conjunto de componentes** — o que muda é quais
módulos têm dado.

**E componente novo ali é sinal de premissa errada, não de necessidade.** Das três variantes, a única
mudança em componente compartilhado foi **um rótulo opcional**. Quando a variante pede componente novo, o
que está errado costuma ser a premissa de que ela é uma tela.

Consequência prática no rastreio: **a variante não ganha arquivo, ganha linha** — ver
[fases-e-agentes](fases-e-agentes.md).

## Discriminador com um só valor é convite à regressão

Um `TipoMidia = "video" | "podcast"` sobreviveu a **três remodelagens do produto** porque "custa nada
deixar" — e cada rodada conservadora trouxe o `podcast` de volta, **com rota, copy e template**. O tipo
não era extensibilidade: era um convite escrito no código.

**Quando o segundo valor morre, o tipo morre com ele.** Se um dia houver outro, ele nasce como entidade
nova, não como string reaproveitada. Vale igualmente para taxonomia, tipo de conteúdo no CMS e union de
TypeScript.

## Um estado global, uma fonte — e uma implementação da peça que o mostra

O selo de "ao vivo" tinha **três implementações** (duas pastilhas inline e um componente) e **duas fontes
de verdade**: a tela lia a query string, a faixa e o cabeçalho liam um booleano fixo. Efeito:
`?estado=offline` **não apagava a faixa**.

E o ponto que generaliza:

> **Critério de aceite que passa por inspeção de código e nunca por verificação não é exercitável.**

O critério exigia que a faixa desaparecesse. Ele "passava" quando alguém lia o código e concluía que
passaria. Nunca foi executado, porque não *podia* ser.

**Estado que atravessa superfícies é lido do dado pelo próprio componente**, não recebido por prop de
cada tela. Uma fonte, uma implementação da peça — e aí o critério vira comando.

## Checklist

Antes de dar um entregável por conferido:

- [ ] densidade medida no **construído**, no viewport do público, sem calibrar por padding
- [ ] conteúdo de exemplo do domínio real
- [ ] cada variante de breakpoint aberta na régua, não só no ASCII
- [ ] nenhum componente novo entrou por causa de variante
- [ ] nenhum discriminador com um valor só
- [ ] todo critério de aceite **executável** — existe um passo que o faz falhar

---

← [SCHEMA](SCHEMA.md) · [fases-e-agentes](fases-e-agentes.md) · [fonte-canonica](fonte-canonica.md)
