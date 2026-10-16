# Prompt: site de plaquinhas NFC personalizadas

Prompts para testar o [Vibe Coding Toolkit](https://github.com/soumatheusgomes/vibe-coding-toolkit) em um repositório novo.

Antes de começar: crie um repositório vazio (por exemplo, `plaquinhas-nfc`), clone e abra o agente (Claude Code ou Codex) dentro dessa pasta.

---

## Passo 1: criar a base do projeto

Cole este bloco inteiro no agente e troque `55XXXXXXXXXXX` pelo seu WhatsApp (DDI + DDD + número, só dígitos).

```text
Leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/11-next-project-bootstrap.md e execute o setup neste diretório.

Projeto: "Plaquinhas NFC", loja online de plaquinhas NFC personalizadas para pequenos negócios (restaurantes, salões, lojas, profissionais autônomos).

Problema: o pequeno empreendedor quer que o cliente dele avalie no Google, pague via Pix ou acesse o site/Instagram só aproximando o celular ou lendo um QR code no balcão, mas não sabe configurar NFC nem criar a arte. Hoje o pedido é feito por conversa solta no WhatsApp, com idas e vindas e informações faltando.

Público: donos de pequenos negócios no Brasil, pouco técnicos, que compram pelo celular. Interface 100% em português do Brasil, mobile-first.

Fluxos principais do MVP:
1. Vitrine: página inicial explicando o que é a plaquinha NFC (aproximar o celular abre um link), exemplos de uso e modelos disponíveis (ex.: avaliação Google, Pix, link/site, Instagram, cardápio), com fotos, tamanhos e preço "a partir de".
2. Personalização do adesivo: o cliente escolhe modelo, tamanho, cor/fundo, escreve o texto (nome do negócio, chamada como "Avalie a gente!") e pode indicar que vai enviar o logo. Mostrar uma pré-visualização simples da arte que muda conforme as escolhas.
3. Conteúdo do NFC: o cliente informa o destino que será gravado no chip: link de avaliação do Google, site personalizado, Instagram, cardápio ou outro link. Validar se é uma URL válida e dar uma ajuda curta de como achar o link de avaliação do Google.
4. QR code opcional na plaquinha: o cliente escolhe se quer QR code impresso e para onde ele aponta: chave Pix (tipo da chave + chave + nome do recebedor) ou um link. Mostrar pré-visualização do QR code gerado no navegador.
5. Formulário de pedido: nome, telefone/WhatsApp, cidade/CEP, quantidade, observações, mais tudo o que foi escolhido nas etapas anteriores. Validação com Zod no cliente.
6. Envio pelo WhatsApp: ao confirmar, montar uma mensagem organizada e legível com todas as informações do pedido e abrir https://wa.me/55XXXXXXXXXXX?text=<mensagem codificada> para o cliente enviar ao vendedor. Mostrar um resumo antes de abrir o WhatsApp.
7. Páginas de apoio: perguntas frequentes (o NFC funciona em iPhone e Android? precisa de app? dá para trocar o link depois?) e contato.

Restrições e decisões:
- MVP sem pagamento online, sem login e sem painel administrativo. O pedido é fechado pelo WhatsApp.
- MVP sem banco de dados: o catálogo de modelos fica em um arquivo de configuração tipado no código e o pedido não é salvo no servidor (vai só na mensagem do WhatsApp). Registre essa decisão e a justificativa no PRODUCT.md e em docs/architecture.md; persistência de pedidos entra como evolução futura.
- Número do WhatsApp configurável por variável de ambiente pública (ex.: NEXT_PUBLIC_WHATSAPP_NUMBER), documentada no .env.example, sem número real versionado.
- Upload de logo fica fora do MVP: o cliente avisa no formulário que vai mandar o logo pelo WhatsApp.
- A geração do QR code Pix no site é só pré-visualização; a arte final é feita pelo vendedor. Não armazenar dados do cliente nem chaves Pix.
- Acessível (teclado, contraste, rótulos nos campos) e rápido em celular.
- Preços e textos do catálogo podem ser exemplos reversíveis, marcados como "a definir" no PRODUCT.md.

Critérios de aceitação do MVP: o cliente consegue, pelo celular, escolher um modelo, personalizar o adesivo, informar o link do NFC, configurar o QR code opcional, preencher seus dados e abrir o WhatsApp com a mensagem completa e correta, sem nenhum erro no console, com lint, typecheck, testes e build passando.
```

Ao final o agente entrega a base configurada (Next.js, documentação, lint, testes) e indica a próxima tarefa do MVP. Ele **não** implementa o site inteiro nesse passo, porque o prompt 11 só monta a base.

---

## Passo 2: implementar o MVP

Depois que o passo 1 terminar, cole na mesma pasta:

```text
Leia PRODUCT.md, AGENTS.md e docs/ e implemente o MVP descrito, fluxo por fluxo, na ordem: vitrine, personalização do adesivo com pré-visualização, conteúdo do NFC, QR code opcional (Pix ou link), formulário com Zod e envio pelo WhatsApp com resumo. Use os padrões e limites do projeto (350 linhas por arquivo, componentes pequenos). Crie testes para a validação do formulário e para a montagem da mensagem do WhatsApp (incluindo acentos, quebras de linha e caracteres especiais). Ao final rode lint, typecheck, testes e build, abra o site em uma porta livre, confira o fluxo completo no tamanho de celular e atualize a documentação responsável. Depois faça de 3 a 5 ciclos de code review focados em comportamento, acessibilidade e validação de entradas.
```

---

## Dicas

- Se o agente fizer perguntas no meio, responda o que souber. Se não souber, peça para ele usar uma hipótese reversível e anotar no `PRODUCT.md`.
- Para publicar depois, a Vercel tem plano gratuito e funciona bem com Next.js. O setup **não** publica nada sozinho.
- Ideias para uma segunda versão: salvar pedidos em banco, upload do logo, painel para acompanhar pedidos, pagamento via Pix com confirmação automática.
