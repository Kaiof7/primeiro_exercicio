# Prompt: marketplace de serviços sob demanda ("Uber de serviços")

Prompts para planejar e iniciar, com o [Vibe Coding Toolkit](https://github.com/soumatheusgomes/vibe-coding-toolkit), um app que conecta clientes a profissionais de serviços (encanador, eletricista, diarista, montador de móveis etc.).

Diferente dos outros prompts, este projeto começa pelo **planejamento**: modelo de negócio, preço, matching e arquitetura precisam estar decididos antes do código. Por isso são três passos:

1. **Planejar** (sem código), seguindo o Prompt 04 do toolkit (brainstorm até plano). O resultado são documentos versionados no repositório.
2. **Criar a base** na stack escolhida e adotar o toolkit com o Prompt 12, que funciona com qualquer stack (o Prompt 11 é só para Next.js).
3. **Implementar o MVP** etapa por etapa, seguindo o roadmap do passo 1.

Antes de começar: crie um repositório **privado** (por exemplo, `servicos-ja`), clone e abra o agente (Claude Code ou Codex) dentro dessa pasta. Troque os campos entre colchetes.

---

## Passo 1: planejar o produto e a arquitetura (sem código)

```text
Leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/04-brainstorm-to-plan.md e siga as etapas 1 a 3 (esclarecer, planejar, pedir aprovação) para o pedido abaixo. NÃO escreva código de aplicação neste passo: a entrega são documentos de produto e arquitetura neste repositório. Leia também, do mesmo repositório, templates/product.md.template e templates/project-docs/ (architecture, quality, setup) para usar a mesma estrutura de documentação.

## Contexto
Quero criar um marketplace de serviços sob demanda, no estilo Uber, que conecta clientes a profissionais (encanador, eletricista, diarista, montador de móveis etc.). O cliente descreve o problema e o app:
1. Classifica o tipo de serviço.
2. Calcula o preço automaticamente com base no serviço, na distância e no horário.
3. Encontra um profissional disponível e qualificado.
4. Agenda um horário que sirva para os dois, ou chama alguém para agora (modo urgente).

- Mercado inicial: [Fortaleza-CE]
- Categorias do MVP: [encanador e eletricista]
- Prazo desejado para o MVP: [12] semanas, com [1] desenvolvedor (eu)
- Meu perfil: desenvolvedor avançado, foco em back-end e engenharia de dados. Pode pular o básico; detalhe mais front-end mobile, pagamentos e operação.
- Stack preferida: [Python/FastAPI, PostgreSQL + PostGIS, Redis, React Native com Expo]
- Orçamento de infraestrutura no MVP: [até R$ 500/mês]

Personas: cliente (pessoa física que precisa de um conserto), profissional (autônomo, muitas vezes MEI, que usa o celular o dia todo e quer previsibilidade de renda) e operador (eu ou uma pequena equipe que aprova profissionais, resolve disputas e acompanha métricas).

## Problema central
Diferente de uma corrida, o escopo de um conserto é incerto: o preço só fica claro depois que o profissional vê o problema. Proponha um modelo de precificação que combine:
- Cardápio de serviços padronizados com preço fixo (ex.: trocar torneira, instalar chuveiro, trocar tomada).
- Taxa de visita técnica para casos indefinidos, abatida do serviço se o cliente aprovar o orçamento.
- Componente de deslocamento (distância e tempo, por rota e não em linha reta).
- Adicional para urgência e para horários fora do comercial, fins de semana e feriados.
- Fluxo de mudança de escopo no local: o profissional propõe um orçamento revisado pelo app, o cliente aprova ou recusa, e tudo fica registrado.
- Opcional: o cliente envia foto ou vídeo e uma IA estima a categoria e uma faixa de preço, sempre com nível de confiança e caindo para visita técnica quando a confiança for baixa.

## Entregas (cada uma em um documento, com a estrutura do toolkit)
1. PRODUCT.md: problema, personas, proposta de valor, escopo do MVP e o que fica fora, jornadas principais (cliente e profissional), métricas de sucesso (tempo até aceite, taxa de aceite, cancelamento, recompra, nota média, GMV, take rate) e critérios de aceitação.
2. docs/business-model.md: como o app ganha dinheiro (comissão, taxa de serviço do cliente, assinatura do profissional, destaque pago) com números de referência do mercado brasileiro e fontes datadas; unit economics de um pedido típico (GMV, receita líquida, custo de pagamento, CAC, LTV) em uma tabela com premissas explícitas.
3. docs/pricing.md: fórmula explícita com todas as variáveis, valores mínimos, arredondamento, validade do orçamento e taxas de cancelamento e de não comparecimento; pelo menos 3 exemplos calculados (serviço do cardápio em horário comercial, visita técnica com orçamento revisado, urgência à noite). Valores em centavos inteiros. Separar o que é parâmetro configurável do que é regra fixa.
4. docs/matching.md: algoritmo de escolha do profissional (filtros duros: especialidade, raio, disponibilidade, verificação; pontuação: distância/ETA, nota, taxa de aceite, cancelamentos, carga recente e justiça na distribuição), com pseudocódigo, pesos iniciais, timeout de oferta, ofertas em sequência ou em paralelo (trade-off com recomendação), e o que fazer quando ninguém aceita.
5. docs/scheduling.md: modelo de disponibilidade (janelas recorrentes, exceções, bloqueios), duração estimada por serviço mais deslocamento e folga, cruzamento das disponibilidades do cliente e do profissional, reserva temporária do horário para evitar agendamento duplo, e o fluxo completo do modo "agora" como máquina de estados (solicitado → ofertado → aceito → a caminho → em serviço → concluído / cancelado / expirado), com timeouts.
6. docs/architecture.md: serviços do MVP (recomendar começar com monólito modular e justificar), modelo de dados com o DDL do PostgreSQL das tabelas principais (usuários, profissionais, especialidades, serviços do cardápio, disponibilidade, pedidos, ofertas, orçamentos, pagamentos, avaliações, disputas), índices geoespaciais PostGIS, filas e tarefas assíncronas (Redis), atualização de localização em tempo real (WebSocket ou polling, com trade-off), notificações push, armazenamento de fotos e vídeos, observabilidade e eventos para análise de dados. Pagamentos no Brasil: Pix e cartão via gateway com split de pagamento para marketplace (comparar opções como Pagar.me, Mercado Pago, Asaas, Iugu e Stripe, conferindo na documentação atual), retenção do valor até a conclusão sem que a plataforma faça custódia irregular de recursos, liberação ao profissional, estornos e chargeback. Diagramas em Mermaid. Decisões importantes como ADRs curtos (contexto, opções, decisão, consequências).
7. docs/trust-safety.md: verificação de profissionais (documento, selfie, CPF/CNPJ, antecedentes, comprovação de experiência), avaliações dos dois lados, disputas com prazos e evidências, garantia do serviço (ex.: 30 dias), seguro, segurança do cliente em casa (compartilhar dados do profissional, botão de ajuda) e política de banimento.
8. docs/roadmap.md: MVP em [12] semanas dividido em etapas com entregas verificáveis, começando por uma etapa de validação barata (ex.: atendimento "concierge" por WhatsApp com back-office simples, para medir demanda e preço antes do app completo). Cada etapa com critério de pronto e métrica observável.
9. docs/risks.md: riscos legais (vínculo trabalhista e a regulação de trabalho por plataforma em discussão no Brasil, LGPD com geolocalização, fotos e dados de terceiros, Código de Defesa do Consumidor, responsabilidade por danos, emissão de nota fiscal e ISS), operacionais (no-show, fraude, conluio para fechar fora do app, qualidade) e de mercado (problema do ovo e da galinha: como conseguir profissionais primeiro, densidade por bairro, concorrentes locais e GetNinjas). Para cada risco: probabilidade, impacto, mitigação e sinal de alerta.

## Como trabalhar
- Seja direto e técnico. Use tabelas, fórmulas, pseudocódigo e DDL em vez de texto corrido.
- Em cada trade-off importante, apresente as opções lado a lado, recomende uma e diga o que faria você mudar de ideia.
- Números de mercado com fonte e data; quando for estimativa, marque como estimativa e explique a premissa. Não invente estatísticas.
- Legislação e regras de pagamento mudam: consulte fontes oficiais atuais, cite a data da consulta e marque o que precisa de validação com advogado ou contador.
- Antes de começar, faça no máximo 3 perguntas, só se faltar algo essencial. Para o resto, assuma hipóteses razoáveis, registre-as em uma seção "Hipóteses" do PRODUCT.md e siga.
- Depois de escrever os documentos, revise a consistência entre eles (ex.: a fórmula do pricing.md bate com as tabelas do architecture.md e com os exemplos; o roadmap cobre tudo que está no escopo do MVP) e apresente um resumo com as decisões principais e o que precisa da minha aprovação. Não comece a implementar.
```

No final o agente apresenta as decisões principais e espera a sua aprovação. Leia os documentos com calma, peça ajustes e só siga para o passo 2 quando estiver de acordo.

---

## Passo 2: criar a base e adotar o toolkit

Depois de aprovar o planejamento, cole:

```text
Com base no PRODUCT.md e nos documentos de docs/ já aprovados, crie a base mínima do projeto na stack definida em docs/architecture.md, sem implementar funcionalidades ainda: estrutura do monólito modular em Python/FastAPI com gerenciador de dependências atual (ex.: uv), PostgreSQL + PostGIS e Redis via Docker Compose, migrations (ex.: Alembic) com as tabelas do DDL aprovado, configuração por variáveis de ambiente com .env.example, endpoint de saúde, testes com pytest, lint e formatação (ex.: Ruff) e tipagem (ex.: mypy ou pyright), e o app mobile React Native com Expo apenas com a navegação inicial. Confira versões e comandos atuais via Context7 e documentação oficial. Rode os checks e suba tudo localmente para provar que funciona.

Em seguida, leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/12-existing-project-setup.md e execute integralmente o bloco da seção "Prompt" neste projeto, começando pelo brainstorming. Preserve a stack e os documentos aprovados; integre AGENTS.md, CLAUDE.md, docs de qualidade e setup, skills e ferramentas aplicáveis a Python e React Native, e comandos canônicos de verificação. Não publique, não contrate serviços e não crie contas em gateways de pagamento.
```

---

## Passo 3: implementar o MVP por etapas

Repita este bloco para cada etapa do `docs/roadmap.md`, trocando o número da etapa:

```text
Leia PRODUCT.md, AGENTS.md e docs/ e implemente a etapa [1] do docs/roadmap.md. Escreva os testes antes do código nas regras críticas da etapa (ex.: fórmula de preço com todos os exemplos do docs/pricing.md, pontuação e timeouts do matching, transições da máquina de estados do pedido, conflitos de agenda, valores em centavos). Use dados fictícios; nenhuma integração real de pagamento sem eu autorizar. Respeite os padrões do projeto e mantenha arquivos pequenos e com uma responsabilidade. Ao final rode lint, tipos, testes e o ambiente local, demonstre o critério de pronto da etapa, atualize os documentos que mudaram (inclusive ADRs novos) e faça de 3 a 5 ciclos de code review focados no risco da etapa (segurança e pagamentos, concorrência e condições de corrida, exatidão de cálculos, dados pessoais). Termine com o resumo do que foi entregue, as pendências e o prompt para a próxima etapa.
```

---

## O que mudou em relação ao seu prompt original

- **Integra o toolkit:** o passo 1 usa o Prompt 04 (esclarecer → planejar → aprovar) e a estrutura de documentos do toolkit; o passo 2 usa o Prompt 12, que se adapta a Python e React Native; o passo 3 implementa por etapas com testes e code review.
- **Entrega em arquivos, não só em conversa:** cada item vira um documento versionado, que os próximos passos usam como referência.
- **Lacunas preenchidas:** personas, métricas de sucesso, unit economics, mudança de escopo no local, cancelamento e não comparecimento, máquina de estados do pedido, reserva de horário contra agendamento duplo, split de pagamento no Brasil, CDC, nota fiscal e ISS, e validação barata antes do app completo.
- **Menos risco de números inventados:** o prompt exige fonte e data, separa estimativa de dado e marca o que precisa de advogado ou contador.
- **Tirei o "Você é um arquiteto sênior...":** o contexto concreto (mercado, prazo, equipe, stack, orçamento) orienta mais o agente do que um papel.
