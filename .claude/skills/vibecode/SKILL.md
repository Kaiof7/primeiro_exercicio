---
name: vibecode
description: Cria ou melhora um prompt de projeto usando o Vibe Coding Toolkit (github.com/soumatheusgomes/vibe-coding-toolkit) como referência, no mesmo formato dos arquivos prompt-*.md deste repositório. Use quando o usuário digitar /vibecode, começar a mensagem com "/vibecode", ou pedir um prompt de site, app ou sistema "usando o vibe coding".
argument-hint: "[ideia do projeto ou prompt para ajeitar]"
---

# /vibecode: prompts com o Vibe Coding Toolkit

Pedido do usuário: $ARGUMENTS

Se o pedido estiver vazio, pergunte em uma frase o que ele quer construir (ou peça o prompt que quer ajeitar) e pare.

O resultado é sempre um **prompt pronto para colar** em um agente (Claude Code ou Codex) aberto em um repositório novo ou existente, salvo como arquivo neste repositório. Não implemente o projeto: escreva o prompt.

## 1. Entender o pedido

- Descubra o tipo: site ou app novo, projeto já existente, melhoria de um prompt que o usuário colou ("ajeite esse prompt"), ou tarefa pontual (lint, refatoração, revisão, segurança).
- Se faltar algo essencial que mude o prompt inteiro (ex.: projeto novo ou existente, para quem é, se é demo ou produto real), faça no máximo 3 perguntas curtas e pare. Para o resto, assuma hipóteses razoáveis e deixe campos entre colchetes, como `[CIDADE/UF]` ou `[55DDDNUMERO]`.
- Se o usuário já passou dados reais (nomes, telefones, links, endereço, preços), coloque-os no prompt. Limpe parâmetros de rastreamento de links (`utm_*`, `fbclid`, `igsh`, `g_ep`, `vrfl` e parecidos). Se um dado parecer inconsistente (ex.: número do endereço diferente do perfil do Google), aponte e pergunte.

## 2. Consultar o toolkit

Leia o catálogo atual antes de escolher, porque o toolkit muda:
- https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/README.md
- O prompt escolhido, para seguir o que ele espera receber.

Escolha do prompt base (confira os números no catálogo atual):
| Situação | Prompt do toolkit |
|---|---|
| Site ou app web novo (Next.js) | 11 — bootstrap de projeto Next |
| Projeto existente, ou stack que não é Next.js (Python, React Native etc.) depois de criar um esqueleto | 12 — setup de projeto existente |
| Ideia grande ou vaga que precisa de planejamento antes do código | 04 — brainstorm até plano (etapas 1 a 3, sem código) |
| Tarefas pontuais | 01 sanitização, 02 warnings, 03 code review, 08 quality gates, 09 arquivos grandes, 13 revisão de segurança |

Linha de entrada no prompt gerado:
`Leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/<arquivo>.md e execute o setup neste diretório.`
(Para o 12: "e execute integralmente o bloco da seção "Prompt" neste projeto, começando pelo brainstorming.")

## 3. Estrutura do arquivo

Use os arquivos `prompt-*.md` na raiz deste repositório como exemplos de tom e formato. Estrutura:

1. `# Prompt: <nome do projeto>` e uma introdução curta com o link do toolkit e o caminho em passos.
2. "Antes de começar": criar repositório (privado quando houver dados sensíveis), clonar, abrir o agente na pasta, trocar os campos entre colchetes.
3. **Passo 1** (bloco ```text): linha do toolkit + descrição do projeto com as seções:
   - Projeto, Problema (a dor real de hoje), Público (quem usa, em que aparelho, com que internet), objetivo principal.
   - Dados fixos (empresa, contatos, links, preços) em um arquivo de configuração tipado e validado com Zod (ex.: `empresa.json`, `produtos.json`), para o usuário mudar sem mexer no código. Campos vazios escondem o elemento, sem link quebrado.
   - Fluxos principais do MVP, numerados e concretos.
   - Regras de negócio com fórmulas explícitas e **exemplos que precisam bater** (viram testes). Confira você mesmo as contas dos exemplos antes de salvar.
   - Direção visual: paleta, tipografia, mobile-first e a lista do que evitar para não ter "cara de IA" (gradiente roxo/azul, brilhos, emojis decorativos, cards todos iguais, frases de marketing vazias, depoimentos inventados, imagens geradas por IA).
   - Restrições e decisões: o que fica fora do MVP; banco de dados ou não (com justificativa no PRODUCT.md e em docs/architecture.md); exportação estática quando não houver backend; segredos só em variáveis de ambiente com `.env.example`; valores em centavos inteiros; fuso horário correto; acessibilidade (contraste WCAG AA, teclado, rótulos); SEO local e imagem de compartilhamento quando for site público; privacidade/LGPD; não publicar automaticamente e documentar o passo a passo de publicação.
   - Critérios de aceitação verificáveis, terminando com "sem erros no console, com lint, typecheck, testes e build passando".
4. Nota de que o prompt base (ex.: o 11) só monta a base e não implementa tudo.
5. **Passo 2** (e **Passo 3** em projetos grandes): implementar em uma ordem definida, testes escritos antes do código nas regras críticas, padrões do projeto (350 linhas por arquivo), rodar checks, abrir em porta livre e percorrer o fluxo em tamanho de celular, atualizar a documentação, e de 3 a 5 ciclos de code review com focos ligados ao risco do projeto.
6. Seção final curta: o que separar antes de rodar, cuidados importantes ou como usar depois de publicar.

Ao melhorar um prompt do usuário: mantenha a estrutura e o conteúdo dele, integre o toolkit, preencha lacunas (casos de borda, erros de integração, limites, segurança, particularidades do Brasil como Pix, CEP, LGPD, nota fiscal) e acrescente no fim uma seção "O que mudou em relação ao seu prompt original".

## 4. Cuidados de conteúdo

- Prefira caminhos oficiais e seguros: nada de bibliotecas não oficiais que usam a senha do usuário em serviços de terceiros; integrações pagas ou reguladas ficam como decisão explícita.
- Números de mercado, leis e preços de serviços: peça ao agente fonte e data, e marque o que precisa de advogado ou contador.
- Quando houver IA no produto: chave só no servidor, números calculados pelo código (a IA explica, não calcula), dados sensíveis mascarados e limite de custo.
- Escreva em português do Brasil, frases diretas, sem jargão desnecessário.

## 5. Entregar

1. Salve como `prompt-<nome-curto>.md` na raiz do repositório. Se o arquivo já existir, atualize-o.
2. Se for repositório Git, faça commit com mensagem em português e push para o branch atual.
3. Responda ao usuário em português, curto:
   - onde está o arquivo e quais passos ele tem;
   - decisões que você tomou e lacunas que preencheu;
   - a lista dos campos entre colchetes que ele precisa completar;
   - avisos importantes (custos, segurança, limitações de integração).
   Não repita o prompt inteiro na resposta, a não ser que o usuário peça.
