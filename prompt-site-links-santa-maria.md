# Prompt: página de links da Vidraçaria e Serralharia Santa Maria

Prompts para criar, com o [Vibe Coding Toolkit](https://github.com/soumatheusgomes/vibe-coding-toolkit), uma página de links no estilo "link na bio" (como o Linktree), própria da oficina, para colocar na bio do Instagram, no status do WhatsApp, em cartões de visita e em plaquinhas com QR code.

Usa os mesmos dados e a mesma paleta do site de orçamento (`prompt-site-vidracaria-santa-maria.md`). Mesmo caminho dos outros: o passo 1 monta a base com o Prompt 11 do toolkit e o passo 2 implementa a página.

Antes de começar: crie um repositório vazio (por exemplo, `santa-maria-links`), clone e abra o agente (Claude Code ou Codex) dentro dessa pasta.

---

## Passo 1: criar a base do projeto

```text
Leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/11-next-project-bootstrap.md e execute o setup neste diretório.

Projeto: página de links ("link na bio") da VIDRAÇARIA E SERRALHARIA SANTA MARIA, em Boqueirão/PB. É uma página única, própria da oficina, que substitui serviços como o Linktree.

Problema: o cliente encontra a oficina pelo Instagram, por indicação ou por uma plaquinha, e precisa de um lugar só para chamar no WhatsApp, ligar, ver o horário, saber como chegar, ver os trabalhos e avaliar no Google. Páginas de links genéricas têm a cara do serviço, não da empresa, e mostram propaganda de terceiros.

Público: clientes e futuros clientes de Boqueirão/PB e região que tocam no link da bio do Instagram ou leem um QR code, quase sempre no celular, com pressa e muitas vezes com internet fraca. Interface 100% em português do Brasil, pensada primeiro para celular.

Objetivo principal: ser a página mais bonita e rápida possível no celular, passando confiança de empresa séria e local, e levando a pessoa em um toque para o WhatsApp.

## Dados da empresa (ficam em um único arquivo de configuração, empresa.json, validado com Zod)
- Nome: Vidraçaria e Serralharia Santa Maria
- Serviços: vidros e espelhos, box de vidro e acrílico, janelas, portões de ferro e alumínio, grades, esquadrias e serviço de chaveiro
- Cidade: Boqueirão/PB
- WhatsApp principal: 5583991058693, exibido como (83) 99105-8693. Também é o telefone de ligação principal.
- Segundo número: 5583991153515, exibido como (83) 99115-3515, com opção de ligar e de WhatsApp
- E-mail: não tem (não exibir)
- Instagram: @vidracaria_e_serralharia → https://www.instagram.com/vidracaria_e_serralharia/
- Endereço: Rua Presidente Epitácio Pessoa, 340, Centro, Boqueirão/PB, CEP 58450-000. Coordenadas: -7.4814216, -36.1315391.
- Mapa incorporado: https://www.google.com/maps?q=Chaveiro,+Vidra%C3%A7aria+e+Serralharia+Sta_Maria,+Boqueir%C3%A3o+-+PB&ll=-7.4814216,-36.1315391&z=17&output=embed
- Como chegar (abre o perfil da empresa no Google Maps): https://www.google.com/maps/place/Chaveiro,+Vidra%C3%A7aria+e+Serralharia+Sta_Maria+-+R.+Epit%C3%A1cio+Pessoa,+340+-+Centro,+Boqueir%C3%A3o+-+PB,+58450-000/@-7.4814216,-36.1315391,17z/data=!4m6!3m5!1s0x7a94d439dad49e5:0x516a971ae4291d0!8m2!3d-7.4814216!4d-36.1315391!16s%2Fg%2F11txm6nnjp
- Horário: segunda a sexta, 7h20 às 11h20 e 13h20 às 17h20; sábado, 7h às 12h; domingo fechado. Fuso America/Fortaleza.
- Avaliação no Google: https://g.page/r/CdCRQq5xqRYFEBM/review
- Site de orçamento online: pendente (será outro site; deixar vazio por enquanto)
- Pasta do Google Drive com fotos dos trabalhos: pendente (deixar vazio por enquanto)
- Logo: pendente. Até chegar, usar o nome da empresa escrito na fonte serifada dos títulos como marca, com espaço reservado para trocar pelo logo depois.
Campos vazios escondem o botão ou bloco correspondente, sem link quebrado. A ordem, o texto e a visibilidade de cada botão também ficam no arquivo de configuração, para eu mudar sem mexer no código.

## Conteúdo da página, de cima para baixo
1. Topo: logo (ou nome em fonte serifada), uma linha curta dizendo o que a oficina faz ("Vidros, espelhos, box, portões e grades em Boqueirão/PB") e um selo de status calculado pelo horário: "Aberto agora · fecha às 11h20", "Fechado · abre hoje às 13h20" ou "Fechado · abre segunda às 7h20".
2. Botão principal, maior e em destaque: "Pedir orçamento no WhatsApp", que abre https://wa.me/5583991058693 com a mensagem pronta "Olá! Vim pelo link do Instagram e gostaria de um orçamento."
3. Botões de ação, um embaixo do outro, com ícone e texto:
   - Orçamento online com estimativa de preço (só aparece quando o link do site de orçamento for preenchido)
   - Ligar: (83) 99105-8693 (tel:+5583991058693)
   - WhatsApp ou ligação no segundo número: (83) 99115-3515
   - Instagram @vidracaria_e_serralharia
   - Ver fotos dos nossos trabalhos (só aparece quando o link do Drive for preenchido)
   - Como chegar (Google Maps)
   - Avalie a gente no Google
   - Salvar contato: baixa um cartão de contato (.vcf) com nome da empresa, os dois telefones, endereço, Instagram e link da página, para o cliente salvar a oficina na agenda em um toque
   - Compartilhar esta página: usa o compartilhamento nativo do celular (Web Share API) e, onde não existir, copia o link e mostra "Link copiado"
4. Serviços: lista visual e compacta do que a oficina faz (vidros e espelhos, box, janelas, portões, grades, esquadrias, chaveiro), sem preços. Cada item pode levar ao WhatsApp com mensagem pronta citando o serviço (ex.: "Olá! Gostaria de um orçamento de box de vidro.").
5. Horário de atendimento completo, com o dia de hoje destacado.
6. Endereço com mapa incorporado (carregado só quando a pessoa rolar até ele) e botão "Como chegar".
7. Chamada para avaliação: "Gostou do nosso serviço? Avalie a gente no Google", com o botão de avaliação.
8. Rodapé: nome, cidade, "Também fazemos serviço de chaveiro" e o ano atual.

## Direção visual (foco principal do projeto)
- Paleta igual à do site de orçamento: verde-escuro (#1F4A3D) e bege-claro (#F3EEE6). Derivar no máximo uma ou duas variações (um verde mais claro para hover/estado e um tom de texto) e registrar a paleta em docs/design.md, conferindo contraste mínimo WCAG AA em textos e botões.
- Tipografia serifada elegante nos títulos e sem serifa legível no texto, via next/font, iguais às do site de orçamento para as duas páginas parecerem da mesma marca.
- Sensação de oficina caprichosa e local: materiais do próprio ofício como referência visual (reflexo suave de vidro, linhas finas de esquadria, textura discreta), sem exagero. A página deve parecer feita sob medida para a Santa Maria, não um modelo de Linktree.
- Botões grandes, fáceis de tocar com o polegar (altura mínima de 52px), com ícone, texto claro e estado de toque visível. O botão do WhatsApp é o mais chamativo.
- Ícones: um conjunto consistente e leve (ex.: lucide-react) e ícones oficiais de WhatsApp, Instagram e Google em SVG, sem pacotes pesados.
- Animações discretas: entrada suave dos botões em sequência e resposta ao toque, respeitando prefers-reduced-motion.
- Evitar a cara de site gerado por IA: nada de gradiente roxo/azul, fundo escuro com brilhos, emojis decorativos, cards todos iguais cheios de sombra, frases de marketing vazias ou depoimentos inventados. Textos curtos e diretos, no jeito de falar da oficina.
- Imagens (quando houver foto da fachada ou de trabalhos): WebP, tamanho adequado ao celular e carregamento sob demanda. Sem imagens geradas por IA.
- Antes de implementar, propor em docs/design.md a direção visual com a paleta, as fontes, o estilo dos botões e um esboço da ordem dos blocos.

## Restrições e decisões
- Página 100% estática, sem backend e sem banco de dados. Usar a base Next.js do toolkit com exportação estática (output: "export"), para publicar de graça na Vercel, Netlify ou GitHub Pages. Não instalar Drizzle, PostgreSQL, Pino nem pacotes de servidor; aplicar o perfil leve de lint sem persistência. Registrar a decisão no PRODUCT.md e em docs/architecture.md.
- Muito leve e rápida em celular com internet fraca: pouco JavaScript, fontes otimizadas, mapa carregado sob demanda. Meta: nota de desempenho, acessibilidade e boas práticas acima de 95 no Lighthouse em modo celular, registrada em docs/quality.md.
- Todos os links externos abrem em nova aba com rel="noopener noreferrer", menos tel: e o .vcf.
- Mensagens prontas do WhatsApp codificadas com encodeURIComponent e geradas por uma função separada e testada.
- O cálculo de "aberto/fechado" fica em uma função separada e testada (inclusive intervalo do almoço, sábado à tarde, domingo e virada de dia).
- O cartão de contato (.vcf) é gerado por uma função separada e testada (acentos, os dois telefones, endereço).
- SEO e compartilhamento: título e descrição com nome, serviços e cidade, dados estruturados schema.org do tipo LocalBusiness (endereço, telefones, horário, coordenadas, Instagram) e imagem de compartilhamento bonita para quando o link for enviado no WhatsApp e no Instagram, com as cores da marca.
- Ícone do site (favicon) e ícone para "adicionar à tela inicial" com as cores da marca.
- Acessível: navegação por teclado, foco visível, textos alternativos, rótulos claros nos botões e ordem de leitura lógica para leitores de tela.
- Sem rastreadores ou cookies de terceiros. Medição de cliques fica como evolução futura.
- Não publicar automaticamente. Documentar em docs/setup.md (ou docs/onboarding.md) o passo a passo para publicar de graça na Vercel, como colocar o link na bio do Instagram, como gerar um QR code da página para imprimir, e como trocar textos, links, ordem dos botões e logo pelo empresa.json.

Critérios de aceitação: no celular, a página abre rápido, mostra o status de aberto/fechado correto, e cada botão leva ao destino certo (WhatsApp com mensagem pronta, ligação, Instagram, mapa, avaliação no Google, contato salvo e compartilhamento). Botões com link vazio não aparecem. A página fica bonita e legível de 320px até tablet. Sem erros no console, com lint, typecheck, testes e build estático passando.
```

Ao final o agente entrega a base configurada e indica a próxima tarefa. Ele **não** implementa a página inteira nesse passo, porque o Prompt 11 só monta a base.

---

## Passo 2: implementar a página

```text
Leia PRODUCT.md, AGENTS.md e docs/ (principalmente docs/design.md) e implemente a página de links, nesta ordem: empresa.json com schema Zod; funções de horário (aberto/fechado), mensagens do WhatsApp e cartão de contato .vcf, com testes escritos primeiro; topo com status; botão principal do WhatsApp; lista de botões de ação (escondendo os que tiverem link vazio); serviços; horário; endereço com mapa sob demanda; chamada de avaliação; rodapé; metadados de SEO, imagem de compartilhamento e ícones. Siga a direção visual do docs/design.md com capricho: este projeto é sobre um front-end bonito no celular. Respeite os padrões do projeto (350 linhas por arquivo, componentes pequenos). Ao final rode lint, typecheck, testes e o build estático, sirva a pasta exportada em uma porta livre, confira a página em 320px, 375px, 414px e tablet, toque em cada botão e confirme o destino, confira console, contraste e a nota do Lighthouse em modo celular. Atualize a documentação responsável e o passo a passo de publicação. Depois faça de 3 a 5 ciclos de code review focados em acabamento visual no celular, acessibilidade, links corretos e desempenho.
```

---

## Depois de publicar

- Coloque o link da página na bio do Instagram, no lugar de qualquer Linktree.
- Gere um QR code da página e coloque no balcão, no cartão de visita, nos orçamentos impressos e até numa plaquinha NFC.
- Quando o site de orçamento estiver no ar, preencha o link dele no `empresa.json` e o botão "Orçamento online" aparece sozinho. O mesmo vale para o link do Drive e o logo.
