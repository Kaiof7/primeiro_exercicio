# Prompt: site de orçamento da Vidraçaria e Serralharia Santa Maria

Prompts para criar o site com o [Vibe Coding Toolkit](https://github.com/soumatheusgomes/vibe-coding-toolkit). Mesmo caminho dos outros: o passo 1 monta a base com o Prompt 11 do toolkit e o passo 2 implementa o site.

Antes de começar: crie um repositório vazio (por exemplo, `santa-maria-orcamento`), clone e abra o agente (Claude Code ou Codex) dentro dessa pasta.

Troque os campos entre colchetes antes de colar. O que você ainda não tiver pode ficar entre colchetes: o agente marca como pendente e usa um valor de exemplo.

---

## Passo 1: criar a base do projeto

```text
Leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/11-next-project-bootstrap.md e execute o setup neste diretório.

Projeto: site de orçamento online da VIDRAÇARIA E SERRALHARIA SANTA MARIA, em [CIDADE/UF].

Problema: hoje os clientes pedem orçamento pelo WhatsApp e mandam as informações aos poucos (nome, endereço, medidas, produto). Isso gasta muito tempo e deixa as conversas bagunçadas. O site precisa juntar tudo num formulário só, mostrar ao cliente uma faixa de preço estimada e mandar para o WhatsApp da empresa UMA mensagem já organizada.

Público: moradores de [CIDADE/UF] e região que chegam quase sempre pelo link do WhatsApp ou do Instagram, no celular, muitas vezes com internet fraca e pouca familiaridade com sites. Interface 100% em português do Brasil, mobile-first.

## 1. Catálogo de produtos
- Cards com foto, nome e "a partir de R$ X/m²". X é o preço mínimo do m² na opção mais barata do produto: preço base "só o material" + acrescimo_min (ex.: Espelho → a partir de R$ 250,00/m²).
- Duas categorias: "Vidros e Espelhos" e "Serralheria e Esquadrias".
- Clicar em um produto leva ao formulário com categoria e produto já selecionados e rola até ele.
- Todos os produtos, preços, fotos e acréscimos ficam em UM arquivo de configuração (produtos.json), para trocar preço ou foto sem mexer no código. Validar o arquivo com um schema Zod nos testes e no build: preço ausente, negativo ou produto duplicado deve falhar com mensagem clara, em vez de quebrar o site em produção.
- Dados da empresa (nome, WhatsApp, telefone, e-mail, Instagram, endereço, horário, link do Google Drive, link de avaliação, link do mapa) ficam em um segundo arquivo de configuração (empresa.json), também validado, para que nada disso fique espalhado pelo código.

## 2. Tabela de preços (preço base por m²)
O preço base fica no produtos.json e NÃO aparece para o cliente. Ele vê só a faixa estimada e o "a partir de".

Vidros e Espelhos (cada um com a opção "Com instalação", quando houver preço):
| Produto            | Só o material | Com instalação |
|--------------------|---------------|----------------|
| Espelho            | 230           | 250            |
| Vidro fumê         | 230           | 250            |
| Vidro incolor      | 170           | 190            |
| Vidro fantasia     | 150           | 180            |
| Vidro incolor 6mm  | 230           | —              |

Produto sem preço com instalação (Vidro incolor 6mm) não mostra a caixa "Com instalação". Se ela estiver marcada e o cliente trocar para esse produto, desmarcar e avisar "Este produto não tem opção com instalação pelo site; combine na conversa".

Serralheria e Esquadrias:
| Produto                      | Preço base/m² |
|------------------------------|---------------|
| Portão de ferro              | 350           |
| Portão de alumínio branco    | 550           |
| Portão de alumínio preto     | 700           |
| Portão de alumínio madeirado | 750           |
| Janela de vidro              | 550           |
| Janela de alumínio           | 550           |
| Box de vidro                 | 380           |
| Box acrílico                 | 290           |
| Box pia de vidro             | 500           |
| Box pia de acrílico          | 400           |
| Grade de ferro               | 280           |
| Grade de alumínio            | 480           |

## 3. Cálculo da estimativa (feito pelo próprio site, ao vivo)
- O cliente informa largura e altura em CENTÍMETROS (números inteiros ou com vírgula, ex.: 80,5).
- Área (m²) = (largura_cm × altura_cm) ÷ 10.000
- Preço mínimo do m² = preço base + acrescimo_min (padrão 20)
- Preço máximo do m² = preço base + acrescimo_max (padrão 50)
  (acrescimo_min e acrescimo_max ficam no produtos.json, com valor geral e possibilidade de valor próprio por produto.)
- Se "Com instalação" estiver marcada, o cálculo usa o preço base com instalação.
- Estimativa = de (área × preço mínimo × quantidade) até (área × preço máximo × quantidade).
- Precisão: calcular com a área exata e arredondar apenas o valor final para centavos (meio para cima). Fazer as contas em centavos inteiros para evitar erro de ponto flutuante. A área exibida tem 2 casas decimais, mas não é arredondada antes da multiplicação.
- Mostrar, atualizando enquanto o cliente digita:
  - Área: 1,00 m²
  - Valor do m²: R$ 250,00 a R$ 280,00
  - Estimativa total: R$ 250,00 a R$ 280,00
- Formato brasileiro com Intl.NumberFormat pt-BR (R$ 1.234,56), área com 2 casas decimais e vírgula.
- Aviso fixo junto da estimativa: "Valor estimado. O preço final é confirmado depois da medição no local."
- Limites: largura ou altura vazia, zero, negativa ou não numérica → não calcula e mostra "Informe largura e altura". Medida acima de [1000] cm ou quantidade acima de [50] → não calcula e mostra "Para medidas ou quantidades maiores, fale com a gente pelo WhatsApp". Quantidade mínima 1, só números inteiros.

Exemplos que precisam bater (viram testes automatizados):
- Espelho, 100×100 cm, 1 unidade → 1,00 m² → R$ 250,00 a R$ 280,00
- Espelho com instalação, 100×100 → R$ 270,00 a R$ 300,00
- Espelho, 200×150 cm → 3,00 m² → R$ 750,00 a R$ 840,00
- Portão de alumínio branco, 300×200 cm → 6,00 m² → R$ 3.420,00 a R$ 3.600,00
- Quantidade 2 → valores dobram (Espelho 100×100, 2 unidades → R$ 500,00 a R$ 560,00)
- Medida vazia, zero ou negativa → não calcula e mostra "Informe largura e altura"
- Área quebrada: Vidro incolor, 33×33 cm → 0,11 m² exibido; estimativa R$ 20,69 a R$ 23,96 (área exata 0,1089 × 190 e × 220)
- Vidro incolor 6mm não aceita "Com instalação"

## 4. Formulário de orçamento
- Nome (obrigatório)
- WhatsApp (obrigatório, com máscara (00) 00000-0000, aceitando também fixo (00) 0000-0000)
- Endereço: CEP com preenchimento automático pelo ViaCEP (https://viacep.com.br), rua, número, complemento, bairro e cidade. Se o ViaCEP falhar, demorar mais de alguns segundos ou não achar o CEP, avisar e deixar o cliente preencher à mão, sem travar o formulário. Bairro e cidade obrigatórios; o resto opcional.
- Categoria e produto (listas que vêm do produtos.json; trocar a categoria filtra os produtos)
- "Com instalação" (caixa de marcar, só para vidros e espelhos que têm esse preço)
- Largura e altura em cm (obrigatórias), quantidade (padrão 1)
- Observações (campo livre, com limite de tamanho razoável)
- Link de destaque "📷 Ver fotos de referência dos nossos trabalhos", que abre a pasta do Google Drive em nova aba (rel="noopener"): [LINK DA PASTA DO DRIVE]. Logo abaixo: "Gostou de algum modelo? Tire um print e mande na conversa do WhatsApp junto com o orçamento."
- Validação com Zod, mensagens de erro em português ao lado de cada campo, foco no primeiro campo com erro ao tentar enviar. Teclado numérico no celular para CEP, WhatsApp e medidas (inputmode).
- Guardar o rascunho do formulário no navegador (localStorage) para o cliente não perder o que digitou se sair para o Drive ou para o WhatsApp e voltar. Nada é enviado para servidor.

## 5. Envio pelo WhatsApp
Botão "Enviar orçamento pelo WhatsApp", que abre https://wa.me/[55DDDNUMERO]?text=<mensagem codificada com encodeURIComponent> com esta mensagem:

*Novo pedido de orçamento — Santa Maria*
Nome: …
WhatsApp: …
Endereço: …
Produto: Espelho (com instalação)
Medidas: 100 x 100 cm (1,00 m²) — Qtd: 1
Estimativa: R$ 270,00 a R$ 300,00
Obs: …

- Linhas sem conteúdo (ex.: Obs vazia, complemento vazio) são omitidas, sem "undefined" ou campos em branco.
- Antes de abrir o WhatsApp, mostrar um resumo para o cliente conferir.
- Depois do envio, mostrar o lembrete: "Se tiver print de um modelo ou foto do local, mande na conversa que abriu."

## 6. Fale Conosco
- Botão do WhatsApp, telefone clicável (tel:), e-mail [E-MAIL] (mailto:) e Instagram [@…]
- Endereço da oficina: [ENDEREÇO], com mapa do Google Maps incorporado por iframe de incorporação (sem chave de API), carregado sob demanda e com link "Abrir no Google Maps" como alternativa.
- Horário de atendimento: [SEG–SEX 8h–18h, SÁB 8h–12h]
- Botão flutuante do WhatsApp visível em todas as páginas, sem cobrir botões do formulário nem o botão de envio no celular.

## 7. Avaliação no Google
- Seção com o título "Gostou do nosso serviço? Avalie a gente no Google ⭐" e botão que abre em nova aba: [LINK DE AVALIAÇÃO DO PERFIL DA EMPRESA NO GOOGLE]
- O mesmo link também fica no rodapé.

## Restrições e decisões
- Site 100% estático, sem backend e sem banco de dados. Usar a base Next.js do toolkit com exportação estática (output: "export"), para publicar de graça em Vercel, Netlify ou GitHub Pages. Não instalar Drizzle, PostgreSQL, Pino nem pacotes de servidor que o site não usa; aplicar o perfil leve de lint sem persistência. Registrar essa decisão e a justificativa no PRODUCT.md e em docs/architecture.md.
- A função de cálculo e a montagem da mensagem do WhatsApp ficam em módulos separados, puros e sem dependência de React (ex.: src/lib/estimativa.ts e src/lib/mensagem-whatsapp.ts), cobertos por testes Vitest com todos os exemplos da seção 3.
- Visual: verde-escuro (#1F4A3D) e bege-claro (#F3EEE6), tipografia serifada nos títulos e sem serifa legível no texto (via next/font), combinando com a tabela de preços impressa da empresa. Logo: [ARQUIVO]. Conferir contraste mínimo WCAG AA dessas cores em textos e botões. Registrar a direção visual em docs/design.md.
- O site deve parecer de uma vidraçaria de verdade, não de modelo genérico: fotos reais dos trabalhos, textos curtos e diretos, sem gradientes chamativos, sem cards todos iguais cheios de ícones genéricos, sem frases de marketing vazias e sem depoimentos inventados.
- Imagens em WebP, com tamanho adequado ao celular e carregamento sob demanda (lazy loading); como a exportação estática não otimiza imagens no servidor, gerar as versões WebP antes e documentar como converter fotos novas. Enquanto não houver fotos reais, usar espaços reservados neutros marcados como pendentes, sem imagens geradas por IA.
- Contatos, links e número do WhatsApp vêm do empresa.json. Não versionar dados pessoais de clientes.
- Acessível: rótulos em todos os campos, navegação por teclado, foco visível, área de toque confortável, mensagens de erro anunciadas para leitores de tela.
- SEO local básico: título e descrição com nome, serviço e cidade, dados estruturados schema.org do tipo LocalBusiness (endereço, telefone, horário) e imagem de compartilhamento para WhatsApp.
- Rodapé com aviso curto de privacidade: os dados digitados não ficam salvos no site, vão só na mensagem que o próprio cliente envia pelo WhatsApp.
- Não publicar automaticamente. Escrever em docs/setup.md (ou docs/onboarding.md) o passo a passo para publicar de graça na Vercel, e alternativas na Netlify e no GitHub Pages, além de como trocar preços, fotos e contatos.

Critérios de aceitação do MVP: no celular, o cliente escolhe um produto no catálogo, cai no formulário com ele selecionado, preenche o CEP e vê o endereço completado (ou preenche à mão se o ViaCEP falhar), informa medidas e quantidade, vê a área e a faixa de preço corretas ao vivo, confere o resumo e abre o WhatsApp com a mensagem completa e organizada. Todos os exemplos da seção 3 passam nos testes. Sem erros no console, com lint, typecheck, testes e build estático passando.
```

Ao final o agente entrega a base configurada e indica a próxima tarefa do MVP. Ele **não** implementa o site inteiro nesse passo, porque o Prompt 11 só monta a base.

---

## Passo 2: implementar o MVP

```text
Leia PRODUCT.md, AGENTS.md e docs/ e implemente o MVP descrito, nesta ordem: produtos.json e empresa.json com schemas Zod; módulo de cálculo da estimativa com testes de todos os exemplos (escreva os testes primeiro e veja falharem); módulo da mensagem do WhatsApp com testes (acentos, quebras de linha, linhas vazias omitidas, caracteres especiais); catálogo por categoria; formulário com máscara, ViaCEP com tratamento de falha, validação e rascunho salvo; estimativa ao vivo; resumo e envio pelo WhatsApp; Fale Conosco com mapa e botão flutuante; seção de avaliação no Google e rodapé. Respeite os padrões do projeto (350 linhas por arquivo, componentes pequenos) e a direção visual do docs/design.md. Ao final rode lint, typecheck, testes e o build estático, sirva a pasta exportada em uma porta livre e percorra o fluxo completo em tamanho de celular (incluindo CEP inválido, ViaCEP fora do ar, medida vazia e produto sem instalação), conferindo console e contraste. Atualize a documentação responsável e o passo a passo de publicação. Depois faça de 3 a 5 ciclos de code review focados em exatidão do cálculo, validação de entradas, acessibilidade e experiência no celular.
```

---

## O que separar antes de rodar

- Número do WhatsApp, telefone, e-mail, Instagram, endereço e horário da oficina.
- Logo em boa resolução (de preferência SVG ou PNG com fundo transparente).
- Fotos reais dos trabalhos para os cards do catálogo.
- Link da pasta do Google Drive com as fotos de referência (compartilhada como "qualquer pessoa com o link pode ver").
- Link de avaliação do Google: no Perfil da Empresa no Google, procure "Pedir avaliações" e copie o link.
