# Prompt: site mostruário para lanchonetes

Prompts para criar, com o [Vibe Coding Toolkit](https://github.com/soumatheusgomes/vibe-coding-toolkit), um site de demonstração de lanchonete. Ele serve de mostruário para vender sites a donos de lanchonete: o cliente vê como o site dele ficaria e, depois de fechar negócio, você troca nome, cores, cardápio e contatos para entregar a versão real.

Mesmo caminho dos outros prompts: base primeiro, MVP depois.

Antes de começar: crie um repositório vazio (por exemplo, `demo-lanchonete`), clone e abra o agente (Claude Code ou Codex) dentro dessa pasta. Troque `[SEU WHATSAPP]` pelo seu número (55 + DDD + número, só dígitos).

---

## Passo 1: criar a base do projeto

```text
Leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/11-next-project-bootstrap.md e execute o setup neste diretório.

Projeto: "Demo Lanchonete", site de demonstração de uma lanchonete fictícia, usado como mostruário de vendas.

Objetivo: eu vendo sites para lanchonetes. Este site mostra ao dono da lanchonete, no celular dele e durante a conversa de venda, como o site dele ficaria: cardápio com preços, pedido pelo WhatsApp, horário, localização e botão de avaliação no Google. Depois de fechar a venda, eu copio o projeto e troco os dados para o cliente real. O site precisa impressionar pela cara de lanchonete de verdade, não de modelo genérico.

Público:
- Quem decide: donos de lanchonetes, hamburguerias e pastelarias de bairro no Brasil, pouco técnicos, que vão ver a demo no celular, muitas vezes ao meu lado ou por um link que eu mando no WhatsApp.
- Quem usaria a versão real: clientes da lanchonete, no celular, com pressa e internet fraca.
Interface 100% em português do Brasil, mobile-first.

Lanchonete fictícia: criar uma marca inventada e crível, com nome, bairro e cidade fictícios, história curta e cardápio realista com preços de mercado brasileiro (lanches, porções, pastéis, bebidas, sobremesas). Não usar nome, logo ou dados de nenhum estabelecimento real. Telefone, endereço e links da lanchonete são fictícios.

Fluxos principais do MVP:
1. Abertura: nome da lanchonete, frase curta com a cara da casa, foto de destaque, status "aberto agora" ou "fechado, abre às X" calculado pelo horário de funcionamento (fuso America/Sao_Paulo) e botões "Ver cardápio" e "Fazer pedido".
2. Cardápio: categorias com navegação rápida, busca por nome, itens com nome, descrição, preço e foto, marcação de "mais pedido" e "esgotado".
3. Montar pedido: adicionar itens com quantidade e observação ("sem cebola"), subtotal, retirada ou entrega, nome, endereço na entrega e forma de pagamento (Pix, dinheiro com troco, cartão na entrega/retirada). Validação com Zod.
4. Pedido pelo WhatsApp em modo demonstração: mostrar o resumo e a mensagem exatamente como chegaria no WhatsApp da lanchonete. Como a lanchonete é fictícia, o botão final abre https://wa.me/[SEU WHATSAPP] com a mensagem do pedido precedida de uma linha "Pedido de teste feito no site de demonstração", para que o dono veja na prática a mensagem chegando.
5. Informações da casa: horário por dia da semana, endereço com mapa ou link para o Google Maps, área de entrega, telefone, Instagram e formas de pagamento.
6. Avaliação no Google: no final da página e na confirmação do pedido, um bloco com chamada direta ("Comeu bem? Conta pra gente no Google") e botão de avaliação. Na demo, o botão abre um aviso curto explicando que na versão real ele leva direto para a página de avaliação da lanchonete no Google.
7. Chamada de venda discreta: uma faixa pequena no topo ou no rodapé, que não atrapalhe a demonstração, com algo como "Site de demonstração. Quer um assim para a sua lanchonete?" e botão para falar comigo no WhatsApp (https://wa.me/[SEU WHATSAPP] com mensagem pronta de interesse).
8. Troca de estilo para a demonstração: dois ou três estilos visuais prontos (ex.: lanchonete tradicional de bairro, hamburgueria artesanal, pastelaria/lanche rápido) que eu possa alternar na hora da conversa por um seletor discreto ou por parâmetro na URL (?estilo=...), mudando cores, tipografia e foto de destaque sem mudar o conteúdo. Registrar no PRODUCT.md se isso ficar para depois do MVP.

Personalização para clientes reais (requisito central):
- Todos os dados da lanchonete (nome, frase, história, cores, logo, fotos, cardápio, preços, horários, endereço, telefone, WhatsApp, Instagram, link de avaliação do Google) ficam em um único arquivo de configuração tipado e validado com Zod, mais a pasta de imagens.
- Uma variável de ambiente (ex.: NEXT_PUBLIC_DEMO_MODE) liga e desliga o modo demonstração: com ela desligada, somem a faixa de venda, o seletor de estilo e o aviso do Google, o pedido vai para o WhatsApp da lanchonete e o botão de avaliação abre o link real.
- Documentar em docs/onboarding.md o passo a passo para transformar a demo no site de um cliente: copiar o projeto, trocar o arquivo de configuração, trocar as imagens, desligar o modo demonstração, conferir e publicar.

Direção visual (o site não pode ter cara de gerado por IA):
- Cada estilo deve parecer uma lanchonete brasileira de verdade (cardápio de parede, quadro de giz, letreiro, embalagem de papel), e não startup de tecnologia. Registrar a direção de cada estilo em docs/design.md.
- Evitar os padrões típicos de site gerado por IA: gradiente roxo/azul, fundo escuro com brilhos, cards todos iguais com sombra e borda arredondada, ícones genéricos em cada seção, emojis decorativos, títulos como "Bem-vindo ao nosso mundo de sabores" ou "Experiência gastronômica única", seções de "Nossos valores" e depoimentos inventados.
- Textos curtos, diretos e no jeito de falar de dono de lanchonete, como num cartaz. Preços sempre visíveis.
- Tipografia com personalidade (fonte de título marcante e fonte de texto legível, via next/font) e o cardápio como protagonista.
- Fotos: usar fotos de comida com licença gratuita para uso comercial (ex.: Unsplash ou Pexels), escolhidas para parecerem de lanchonete brasileira real, salvas localmente em public/ e otimizadas com next/image, com fonte e licença registradas em docs/setup.md. Não usar imagens geradas por IA nem fotos com marcas visíveis.
- Pouca animação: só transições discretas que ajudam a navegar.

Restrições e decisões:
- Sem pagamento online, login, painel administrativo ou integração com iFood.
- Sem banco de dados: todo o conteúdo vem do arquivo de configuração. Registrar a decisão e a justificativa no PRODUCT.md e em docs/architecture.md.
- Meu WhatsApp e o modo demonstração em variáveis de ambiente documentadas no .env.example, sem número real versionado.
- O carrinho pode ficar no localStorage; nenhum dado de quem testar a demo é enviado para servidor.
- SEO: na demo, marcar a página como noindex para não aparecer no Google como se fosse uma lanchonete real. Deixar preparado (e ligado quando o modo demonstração estiver desligado) título e descrição com nome, bairro e cidade, dados estruturados schema.org do tipo Restaurant e imagem de compartilhamento para WhatsApp.
- Acessível (contraste, teclado, rótulos, toque confortável) e leve em celular com internet fraca.

Critérios de aceitação do MVP: no celular, um dono de lanchonete consegue ver o status de aberto/fechado, navegar pelo cardápio, montar um pedido, ver a mensagem formatada e recebê-la no WhatsApp de demonstração, encontrar o botão de avaliação no Google com a explicação, e falar comigo pela faixa de venda. Trocar o arquivo de configuração e desligar o modo demonstração gera um site de cliente sem nenhum vestígio da demo. Sem erros no console, com lint, typecheck, testes e build passando.
```

Ao final o agente entrega a base configurada e indica a próxima tarefa do MVP. Ele **não** implementa o site inteiro nesse passo.

---

## Passo 2: implementar o MVP

```text
Leia PRODUCT.md, AGENTS.md e docs/ (principalmente docs/design.md) e implemente o MVP descrito, nesta ordem: arquivo de configuração da lanchonete fictícia com schema Zod, abertura com status "aberto agora", cardápio com categorias e busca, carrinho com observações, formulário de pedido com Zod, envio pelo WhatsApp em modo demonstração, informações da casa, bloco de avaliação no Google, faixa de venda e troca de estilos. Garanta que desligar NEXT_PUBLIC_DEMO_MODE remova tudo que é da demo. Siga a direção visual do docs/design.md e revise cada tela contra a lista do que evitar para não parecer site gerado por IA. Respeite os padrões do projeto (350 linhas por arquivo, componentes pequenos). Crie testes para o cálculo de aberto/fechado (virada de dia e dia fechado), para o subtotal do carrinho, para a montagem da mensagem do WhatsApp (acentos, quebras de linha, caracteres especiais e a linha de pedido de teste) e para a validação do arquivo de configuração. Ao final rode lint, typecheck, testes e build, abra o site em uma porta livre, percorra o fluxo completo em tamanho de celular com o modo demonstração ligado e desligado, confira o console e atualize a documentação responsável. Depois faça de 3 a 5 ciclos de code review focados em comportamento, acessibilidade, validação de entradas e fidelidade à direção visual.
```

---

## Para a hora da venda

- Publique a demo na Vercel (plano gratuito) e mande o link no WhatsApp do cliente.
- Na conversa, troque os estilos com `?estilo=...` para mostrar que o site se adapta à cara da lanchonete dele.
- Peça para o dono fazer um pedido de teste: a mensagem chegando no seu WhatsApp convence mais que qualquer explicação.
- Fechou? Siga o passo a passo do `docs/onboarding.md` gerado pelo agente: copie o projeto, troque a configuração e as fotos, desligue o modo demonstração e publique.
