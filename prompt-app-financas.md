# Prompt: aplicativo de finanças pessoais

Prompts para criar, com o [Vibe Coding Toolkit](https://github.com/soumatheusgomes/vibe-coding-toolkit), um aplicativo de finanças pessoal, só para você, com gráficos, dados do Nubank, login com código de autenticação e um assistente de IA que explica para onde o dinheiro está indo.

Mesmo caminho dos outros prompts, mas em três passos, porque o projeto é maior: base, MVP (login, importação e gráficos) e depois a IA.

Antes de começar: crie um repositório **privado** (por exemplo, `minhas-financas`), clone e abra o agente (Claude Code ou Codex) dentro dessa pasta.

---

## Passo 1: criar a base do projeto

```text
Leia https://raw.githubusercontent.com/soumatheusgomes/vibe-coding-toolkit/main/docs/prompts/11-next-project-bootstrap.md e execute o setup neste diretório.

Projeto: "Minhas Finanças", aplicativo web de finanças pessoais de um único usuário (eu), com gráficos de entradas, gastos e saldo a partir dos dados da minha conta e do meu cartão Nubank, protegido por senha e código de autenticação, e com um assistente de IA que me ajuda a entender para onde meu dinheiro está indo.

Problema: hoje eu não sei direito quanto gasto por mês, em quê, nem quais gastos estão crescendo. O app do Nubank mostra a lista de transações, mas não me dá uma visão clara por categoria, comparação entre meses, orçamento nem conselhos sobre onde cortar.

Público: só eu. Uso principalmente no celular, às vezes no computador. Interface 100% em português do Brasil, valores em reais, mobile-first.

## 1. Acesso seguro (um único usuário)
- Sem cadastro aberto: o app tem um único usuário, criado por um comando de configuração inicial no terminal (ou por uma tela de primeiro acesso que só funciona enquanto não existir usuário).
- Login com senha forte (hash com argon2id ou equivalente atual) mais código de autenticação de 6 dígitos de um app autenticador (TOTP, compatível com Google Authenticator, Authy, Microsoft Authenticator). Na configuração inicial, mostrar o QR code para cadastrar no app autenticador e gerar códigos de recuperação de uso único, exibidos uma única vez e guardados só como hash.
- Avaliar pela documentação atual e registrar em docs/architecture.md se vale oferecer também passkey (biometria do celular) como forma de entrada mais prática, mantendo o TOTP.
- Sessão em cookie seguro (HttpOnly, Secure, SameSite), com expiração por inatividade e botão "Sair de todos os dispositivos".
- Limite de tentativas de login e de código, com bloqueio temporário e registro das tentativas (data, IP aproximado, sucesso ou falha) numa tela de "Atividade de acesso".
- Todas as páginas e rotas de dados exigem sessão válida, conferida no servidor. Nenhum dado financeiro é exposto em rota pública.
- Usar biblioteca de autenticação mantida e atual para sessão e TOTP (consultar Context7 e documentação oficial), em vez de criptografia feita à mão.

## 2. Dados do Nubank
O Nubank não tem API pública para pessoa física. Não usar bibliotecas não oficiais que fazem login com a minha senha do Nubank: elas quebram com frequência, violam os termos de uso e colocam minha conta em risco.

MVP: importação de arquivos que o próprio Nubank exporta.
- Extrato da conta (NuConta) em OFX ou CSV, exportado pelo app do Nubank.
- Fatura do cartão de crédito em CSV, enviada pelo Nubank por e-mail.
- Tela de importação: escolher o arquivo, ver uma prévia das transações reconhecidas, confirmar. Detectar automaticamente o tipo de arquivo.
- Não duplicar transações ao importar o mesmo período duas vezes (usar o identificador da transação do OFX quando existir, ou uma impressão digital de data + valor + descrição + conta).
- Mostrar um resumo depois de importar: quantas novas, quantas repetidas ignoradas, período coberto.
- Guardar só o necessário: data, descrição, valor, tipo (entrada/saída), conta (NuConta ou cartão), parcela quando houver, categoria. Não guardar o arquivo original depois de importado.
- Documentar em docs/onboarding.md o passo a passo para exportar o extrato e a fatura no app do Nubank.

Evolução futura (registrar no PRODUCT.md, sem implementar agora): sincronização automática pelo Open Finance Brasil por um agregador autorizado (ex.: Pluggy ou Belvo), comparando custo, plano gratuito e requisitos atuais na documentação oficial.

## 3. Categorias
- Categorias padrão em português: Alimentação, Mercado, Transporte, Moradia, Contas (luz, água, internet, celular), Saúde, Educação, Lazer, Compras, Assinaturas, Transferências, Investimentos, Salário, Outras entradas, Outros.
- Categorização automática por regras simples (ex.: descrição contém "UBER" → Transporte; "IFOOD" → Alimentação), com regras editáveis por mim.
- Quando eu corrigir a categoria de uma transação, perguntar se quero criar uma regra para as próximas parecidas.
- Transferências entre minhas próprias contas e pagamento da fatura do cartão não contam como gasto (para não contar o mesmo gasto duas vezes).
- Compras parceladas: cada parcela conta no mês em que é cobrada, com indicação "parcela 3 de 10".

## 4. Painel e gráficos
Tela inicial com filtro de período (este mês, mês passado, últimos 3, 6 e 12 meses, personalizado) e de conta (NuConta, cartão, tudo):
- Cartões de resumo: entradas, gastos, saldo do período e comparação com o período anterior (ex.: "gastos 12% maiores que no mês passado").
- Gastos por categoria (gráfico de rosca ou barras horizontais), tocando na categoria para ver as transações dela.
- Evolução mensal de entradas x gastos (barras lado a lado) nos últimos 12 meses.
- Evolução do saldo acumulado (linha).
- Maiores gastos do período e estabelecimentos onde mais gastei.
- Assinaturas e gastos recorrentes detectados (mesmo valor e descrição parecida todo mês), com total mensal.
- Fatura do cartão: valor por mês e parcelas futuras já comprometidas.
- Orçamento por categoria: eu defino um limite mensal e vejo uma barra de progresso, com aviso visual ao passar de 80% e de 100%.
- Lista de transações com busca, filtros e edição de categoria e de observação.
Gráficos legíveis no celular, com valores em R$ no formato brasileiro, cores consistentes por categoria, legenda clara, e sempre com uma alternativa em texto ou tabela para acessibilidade.

## 5. Assistente de IA (fica para o passo 3, mas a arquitetura já prevê)
- Usar a API do Claude (Anthropic), com o modelo e o SDK atuais conferidos na documentação oficial no momento da implementação. Chave da API só em variável de ambiente no servidor, nunca no navegador nem no repositório.
- Funções:
  - Resumo do mês em linguagem simples: para onde o dinheiro foi, o que mudou em relação aos meses anteriores e o que chama atenção.
  - Perguntas livres num chat, como "quanto gastei com iFood nos últimos 3 meses?", "quais assinaturas eu poderia cancelar?", "por que meus gastos subiram em setembro?".
  - Sugestões práticas de economia e de orçamento por categoria, com base nos meus dados reais.
  - Sugestão de categoria para transações que as regras não reconheceram, que eu aceito ou recuso.
- Os números vêm sempre do banco de dados, calculados pelo código. A IA não inventa nem calcula totais de cabeça: ela recebe ferramentas (tool use) que consultam os dados agregados no servidor (totais por categoria, por mês, por estabelecimento, recorrentes, transações filtradas) e usa os resultados para explicar. Toda resposta com valores indica o período e de onde vieram.
- Privacidade: mandar para a IA só o necessário para a pergunta, de preferência dados agregados; nunca mandar senha, códigos, número completo de conta ou cartão, CPF ou dados de terceiros que apareçam em descrições de transferências (mascarar nomes de pessoas em PIX). Registrar em docs/architecture.md exatamente quais dados saem do app e para onde.
- Controle de custo: limite de uso mensal configurável, contagem de tokens por conversa, resposta clara quando o limite for atingido. O app funciona normalmente sem a chave da IA, só escondendo o assistente.
- A IA não dá recomendação de investimento específica nem faz nenhuma ação no banco: só analisa e explica.

## Restrições e decisões
- Base Next.js do toolkit com PostgreSQL + Drizzle (persistência necessária), Zod, Pino, Vitest e ESLint com os quality gates. Gráficos com uma biblioteca de gráficos para React mantida e leve (ex.: Recharts), escolhida conferindo a documentação atual.
- Valores sempre em centavos inteiros no banco e no código, nunca em ponto flutuante. Datas no fuso America/Sao_Paulo.
- Rodar primeiro localmente: PostgreSQL via Docker Compose, app acessível no meu computador e no celular pela rede de casa. Documentar como fazer backup e restaurar o banco.
- Publicação na internet é opcional e fica para depois: se eu decidir publicar, documentar uma opção com HTTPS e banco gerenciado com criptografia em repouso, e o que muda na segurança. Não publicar automaticamente.
- Segurança: segredos só em variáveis de ambiente (.env fora do Git, .env.example sem valores reais); cabeçalhos de segurança (CSP, HSTS quando publicado, X-Frame-Options); proteção contra CSRF nas ações de escrita; validação com Zod de toda entrada, inclusive dos arquivos importados; limite de tamanho de arquivo; logs sem dados financeiros nem segredos. Rodar a auditoria de dependências e registrar o resultado em docs/quality.md.
- Dados de exemplo: criar um script de "dados fictícios" (seed) com transações inventadas mas realistas, para testar os gráficos sem usar meus dados reais. Nunca versionar extratos reais; adicionar *.ofx, *.csv de extrato e pastas de importação ao .gitignore.
- Visual limpo, sóbrio e confiável, com bom contraste, modo claro e escuro, números grandes e legíveis no celular. Evitar a cara de painel genérico gerado por IA: nada de gradiente roxo, brilhos, emojis decorativos ou cards todos iguais sem hierarquia. Registrar a direção visual em docs/design.md.
- Exportar meus dados (transações e categorias em CSV) e apagar tudo (com confirmação dupla e código de autenticação).

Critérios de aceitação do MVP (passo 2): só eu consigo entrar, com senha e código do app autenticador; importo um extrato OFX e uma fatura CSV do Nubank sem duplicar transações; as transações são categorizadas automaticamente e posso corrigir e criar regras; o painel mostra resumo, gastos por categoria, evolução mensal, saldo, recorrentes e orçamento com valores corretos; tudo funciona bem no celular. Sem erros no console, com lint, typecheck, testes e build passando. A IA entra no passo 3.
```

Ao final o agente entrega a base configurada e indica a próxima tarefa. Ele **não** implementa o app inteiro nesse passo.

---

## Passo 2: implementar o MVP (acesso, importação e gráficos)

```text
Leia PRODUCT.md, AGENTS.md e docs/ e implemente o MVP descrito, nesta ordem: schema do banco (usuário, sessões, tentativas de login, códigos de recuperação, contas, transações, categorias, regras, orçamentos) com migrations; configuração inicial do único usuário com senha, TOTP e códigos de recuperação; login, sessão, limite de tentativas e tela de atividade de acesso; proteção de todas as rotas; leitores de OFX e CSV do Nubank com detecção de duplicadas; tela de importação com prévia; categorias, regras automáticas e correção manual; script de dados fictícios; painel com filtros, resumo, gráficos, recorrentes, fatura e orçamento; lista de transações; exportar e apagar dados. Escreva os testes antes do código nas partes críticas: leitura dos arquivos (use arquivos de exemplo fictícios no formato real do Nubank), duplicadas, regras de categoria, exclusão de transferências internas e pagamento de fatura, parcelas, cálculos do painel em centavos, TOTP, limite de tentativas e bloqueio de rotas sem sessão. Respeite os padrões do projeto (350 linhas por arquivo, componentes pequenos). Ao final rode lint, typecheck, testes e build, suba o banco com Docker, rode o seed, abra o app em uma porta livre e percorra o fluxo completo em tamanho de celular: configurar acesso, entrar com o código, importar, categorizar e ver os gráficos. Tente também acessar páginas e rotas de dados sem login e confirme que são bloqueadas. Atualize a documentação responsável. Depois faça de 3 a 5 ciclos de code review, com pelo menos um focado só em segurança (autenticação, sessão, CSRF, validação de entradas, vazamento de dados em logs) e um em exatidão dos cálculos.
```

---

## Passo 3: adicionar o assistente de IA

Antes, crie uma chave de API em https://console.anthropic.com, defina um limite de gasto mensal no próprio console e coloque a chave no `.env` do projeto (nunca no código).

```text
Leia PRODUCT.md, AGENTS.md e docs/ e implemente o assistente de IA descrito na seção 5 do PRODUCT.md. Consulte a documentação atual da API do Claude (Anthropic) e do SDK para TypeScript via Context7 e fontes oficiais, e escolha o modelo atual adequado registrando o motivo em docs/architecture.md. Implemente no servidor, com a chave só em variável de ambiente: ferramentas (tool use) que consultam os dados agregados do banco (totais por categoria e por mês, comparação entre períodos, estabelecimentos, recorrentes, transações filtradas e orçamento), o resumo do mês, o chat de perguntas e a sugestão de categoria. A IA nunca calcula totais por conta própria; os números vêm das ferramentas. Mascare nomes de pessoas e dados sensíveis antes de enviar e registre exatamente o que sai do app. Adicione limite de uso mensal configurável, contagem de tokens, tratamento de erro e de limite atingido, e esconda o assistente quando não houver chave. Teste com os dados fictícios: as ferramentas com testes automatizados, a montagem do contexto sem dados sensíveis, e respostas reais para perguntas como "onde mais gastei este mês?" e "quais assinaturas eu poderia cancelar?", conferindo que os valores citados batem com o painel. Ao final rode lint, typecheck, testes e build, confira no celular e atualize a documentação. Depois faça de 3 a 5 ciclos de code review focados em privacidade dos dados enviados à IA, exatidão dos números nas respostas, custo e segurança da chave.
```

---

## Cuidados importantes

- **Repositório privado** e nunca suba extratos reais para o GitHub. O prompt já manda colocar esses arquivos no `.gitignore`.
- **Nubank sem API pública:** no começo você exporta o extrato no app e importa no seu. A sincronização automática pelo Open Finance fica para depois, por um serviço autorizado, que costuma ser pago.
- **IA paga por uso:** a API do Claude cobra pelo que você usa. Defina um limite de gasto no console da Anthropic além do limite que o próprio app vai ter.
- **Teste com dados fictícios primeiro.** Só importe seus extratos reais depois que o login com código estiver funcionando.
