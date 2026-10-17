# Prompt: site de lanchonete

Prompts para criar o site de uma lanchonete com o [Vibe Coding Toolkit](https://github.com/soumatheusgomes/vibe-coding-toolkit). Segue o mesmo caminho do prompt das plaquinhas NFC: base primeiro, MVP depois.

Antes de começar: crie um repositório vazio (por exemplo, `site-lanchonete`), clone e abra o agente (Claude Code ou Codex) dentro dessa pasta.

Troque os campos entre colchetes antes de colar. Se ainda não tiver alguma informação, deixe o colchete: o agente vai marcar como pendente.

---

## Passo 1: criar a base do projeto

```text
Leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/11-next-project-bootstrap.md e execute o setup neste diretório.

Projeto: site da lanchonete "[NOME DA LANCHONETE]", em [BAIRRO, CIDADE].

Sobre a casa: [ex.: lanchonete de bairro aberta desde 2015, famosa pelo X-tudo e pelo pastel de feira, ambiente simples e atendimento de família]. O site precisa transmitir essa personalidade real, não a de uma rede genérica.

Problema: hoje o cliente só encontra a lanchonete pelo Instagram ou pelo boca a boca. Não existe um lugar com o cardápio atualizado, preços, horário e endereço, e a maioria dos pedidos chega no WhatsApp com perguntas repetidas ("tem o quê hoje?", "até que horas abre?", "entrega aqui?"). Poucos clientes satisfeitos deixam avaliação no Google.

Público: moradores e trabalhadores da região, que acessam pelo celular, muitas vezes com pressa e internet fraca. Interface 100% em português do Brasil, mobile-first.

Fluxos principais do MVP:
1. Abertura: nome, uma frase curta com a cara da casa, foto real de um lanche ou da fachada, indicação clara de "aberto agora" ou "fechado, abre às X" calculada pelo horário de funcionamento (fuso America/Sao_Paulo), e botões para "Ver cardápio" e "Pedir no WhatsApp".
2. Cardápio: categorias (ex.: lanches, porções, pastéis, bebidas, sobremesas), cada item com nome, descrição curta, preço e foto opcional. Navegação rápida entre categorias, busca simples por nome e marcação de itens esgotados ou "mais pedido".
3. Montar pedido: o cliente adiciona itens com quantidade e observação (ex.: "sem cebola"), vê o subtotal, escolhe retirada ou entrega, informa nome, endereço quando for entrega, forma de pagamento (Pix, dinheiro com troco para quanto, cartão na entrega/retirada). Validação com Zod.
4. Envio pelo WhatsApp: mostrar um resumo do pedido e, ao confirmar, abrir https://wa.me/[55DDDNUMERO]?text=<mensagem codificada> com a mensagem organizada (itens, observações, total, retirada/entrega, endereço, pagamento). A taxa de entrega é informada pela lanchonete na conversa, e o site deixa isso claro.
5. Informações da casa: horário de funcionamento por dia da semana, endereço com link para abrir no Google Maps, área de entrega aproximada, telefone, Instagram e formas de pagamento aceitas.
6. Avaliação no Google: no final da página, um bloco de agradecimento com chamada direta ("Comeu bem? Conta pra gente no Google") e botão que abre o link de avaliação da lanchonete. O link fica em variável de ambiente pública (NEXT_PUBLIC_GOOGLE_REVIEW_URL). O mesmo botão aparece na tela de confirmação depois que o pedido é enviado para o WhatsApp.

Direção visual (importante: o site não pode ter cara de gerado por IA):
- Partir da identidade real da lanchonete: [CORES DA MARCA / LOGO / ESTILO, ex.: vermelho e amarelo, letreiro pintado à mão]. Se não houver, propor uma direção própria em docs/design.md inspirada em lanchonete brasileira de bairro (placa de cardápio, quadro de giz, toalha xadrez, letreiro), e não em startup de tecnologia.
- Evitar os padrões típicos de site gerado por IA: gradiente roxo/azul, fundo escuro com brilhos, cards todos iguais com sombra e borda arredondada, ícones genéricos em cada seção, emojis decorativos, títulos como "Bem-vindo ao nosso mundo de sabores" ou "Experiência gastronômica única", seções de "Nossos valores" e depoimentos inventados.
- Textos curtos, diretos e no jeito de falar da casa, como o dono escreveria num cartaz. Nada de frases de marketing vazias. Preços sempre visíveis.
- Tipografia com personalidade (uma fonte de título marcante e uma fonte de texto bem legível, via next/font), hierarquia forte e layout que lembre um cardápio de verdade, com o cardápio como protagonista.
- Fotos reais da lanchonete em public/ quando eu fornecer. Enquanto não houver, usar espaços reservados neutros e marcados como pendentes, sem fotos de banco de imagem genéricas nem imagens geradas por IA.
- Pouca animação: só transições discretas que ajudam a navegar.

Restrições e decisões:
- MVP sem pagamento online, sem login, sem painel administrativo e sem integração com iFood. O pedido é fechado pelo WhatsApp.
- MVP sem banco de dados: cardápio, preços e horários ficam em um arquivo de dados tipado no código, fácil de editar por quem não programa (documentar em docs/onboarding.md como trocar preço, marcar item esgotado e mudar horário). Registrar a decisão e a justificativa no PRODUCT.md e em docs/architecture.md; um painel para editar o cardápio entra como evolução futura.
- Número do WhatsApp e link de avaliação em variáveis de ambiente públicas documentadas no .env.example, sem dados reais versionados.
- O carrinho pode ficar salvo no navegador (localStorage) para o cliente não perder o pedido se fechar a página; nenhum dado do cliente é enviado para servidor.
- SEO local básico: título e descrição com nome, bairro e cidade, dados estruturados schema.org do tipo Restaurant (endereço, horário, telefone, faixa de preço) e imagem de compartilhamento para WhatsApp/Instagram.
- Acessível (contraste, teclado, rótulos, tamanho de toque confortável) e leve em celular com internet fraca.
- Cardápio e preços podem começar com exemplos reversíveis marcados como "a definir" no PRODUCT.md, até eu enviar os reais.

Critérios de aceitação do MVP: pelo celular, o cliente vê se a lanchonete está aberta, navega pelo cardápio com preços, monta um pedido com observações, escolhe retirada ou entrega e pagamento, abre o WhatsApp com a mensagem completa e correta e, no fim da página e após o pedido, encontra o botão que abre a avaliação no Google. Sem erros no console, com lint, typecheck, testes e build passando.
```

Ao final o agente entrega a base configurada e indica a próxima tarefa do MVP. Assim como no outro prompt, ele **não** implementa o site inteiro nesse passo.

---

## Passo 2: implementar o MVP

```text
Leia PRODUCT.md, AGENTS.md e docs/ (principalmente docs/design.md) e implemente o MVP descrito, nesta ordem: dados do cardápio e horários, abertura com status "aberto agora", cardápio com categorias e busca, carrinho com observações, formulário de pedido com Zod, envio pelo WhatsApp com resumo, informações da casa e bloco de avaliação no Google no final da página e na confirmação do pedido. Siga a direção visual do docs/design.md e revise cada tela contra a lista do que evitar para não parecer site gerado por IA. Respeite os padrões do projeto (350 linhas por arquivo, componentes pequenos). Crie testes para o cálculo de aberto/fechado (incluindo virada de dia e dia fechado), para o subtotal do carrinho e para a montagem da mensagem do WhatsApp (acentos, quebras de linha e caracteres especiais). Ao final rode lint, typecheck, testes e build, abra o site em uma porta livre, percorra o fluxo completo em tamanho de celular, confira o console e atualize a documentação responsável. Depois faça de 3 a 5 ciclos de code review focados em comportamento, acessibilidade, validação de entradas e fidelidade à direção visual.
```

---

## O que separar antes de rodar

- Nome, endereço, telefone/WhatsApp e horários da lanchonete.
- Cardápio com preços (pode ser foto do cardápio impresso; peça ao agente para transcrever).
- Fotos reais dos lanches e da fachada. É o que mais ajuda o site a não parecer genérico.
- Link de avaliação do Google: no Perfil da Empresa no Google, procure "Pedir avaliações" e copie o link.
