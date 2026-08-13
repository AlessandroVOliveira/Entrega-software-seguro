# Etapa 2 — Análise, Priorização e Tratamento de Riscos com o NIST CSF

Esta etapa continua a análise da [Etapa 1](etapa-1-ameacas-stride.md), transformando as ameaças STRIDE (T01–T12) e os casos de abuso (CA01–CA06) do CompraKi em riscos avaliados, priorizados e tratados. O sistema, o repositório, as ameaças e os casos de abuso são os mesmos já descritos na Etapa 1.

## 13.1 Critérios de probabilidade

| Valor | Classificação | Critério |
|---|---|---|
| 1 | Baixa | O evento depende de condições incomuns, acesso muito específico ou grande capacidade técnica |
| 2 | Média-baixa | O evento é possível, mas depende de uma vulnerabilidade ou condição específica |
| 3 | Média-alta | O evento é plausível e pode ocorrer em situações comuns de uso ou ataque |
| 4 | Alta | O evento pode ocorrer com facilidade, frequência ou durante condições previsíveis do sistema |

## 13.2 Critérios de impacto

| Valor | Classificação | Critério |
|---|---|---|
| 1 | Baixo | Causa pequeno transtorno e pode ser corrigido rapidamente |
| 2 | Moderado | Causa interrupção ou inconsistência limitada, com possibilidade de recuperação |
| 3 | Alto | Causa prejuízo relevante aos usuários, ao negócio, à administração ou à privacidade |
| 4 | Muito alto | Pode afetar muitos usuários, comprometer operações críticas ou causar prejuízo grave |

## 13.3 Cálculo e classificação

A pontuação de cada risco é calculada como **Probabilidade × Impacto**, e classificada conforme a tabela abaixo:

| Pontuação | Nível do risco |
|---|---|
| 1 a 3 | Baixo |
| 4 a 7 | Médio |
| 8 a 11 | Alto |
| 12 a 16 | Crítico |

A pontuação orienta a comparação entre riscos, mas a priorização final (seção 13.6) também considera gravidade das consequências, número de usuários afetados, importância do ativo e possibilidade de recuperação.

## 13.4 Registro de riscos

Cada ameaça relevante da Etapa 1 originou um risco correspondente, mantendo o mesmo identificador de categoria STRIDE.

| ID | Origem STRIDE | Evento de risco | Vulnerabilidade ou condição | Probabilidade | Impacto | Pontuação | Nível |
|---|---|---|---|---|---|---|---|
| R01 | Spoofing (T01) | Atacante acessa a conta de um cliente usando credenciais roubadas ou reutilizadas e realiza compras fraudulentas | Ausência de autenticação multifator; sistema não detecta login de dispositivo ou local incomum | 3 | 3 | 9 | Alto |
| R02 | Spoofing (T02) | Atacante cria uma conta de vendedor falsa se passando por marca conhecida para enganar compradores | Cadastro de vendedor sem verificação de identidade ou documentação | 3 | 2 | 6 | Médio |
| R03 | Tampering (T03) | Usuário intercepta e altera o valor do pedido antes da confirmação do pagamento | Validação do valor do pedido ocorre apenas no cliente, sem revalidação no servidor | 3 | 3 | 9 | Alto |
| R04 | Tampering (T04) | Vendedor ou atacante altera preço ou descrição de um anúncio já aprovado, sem nova validação | Ausência de reaprovação automática após edição de um anúncio publicado | 2 | 2 | 4 | Médio |
| R05 | Repudiation (T05) | Cliente nega ter realizado ou recebido uma compra para tentar obter reembolso indevido | Ausência de registro de auditoria confiável de confirmação de entrega | 3 | 2 | 6 | Médio |
| R06 | Repudiation (T06) | Vendedor nega ter combinado um prazo ou condição informada por mensagem a um cliente | Mensagens sem registro imutável ou confiável da conversa | 2 | 1 | 2 | Baixo |
| R07 | Information Disclosure (T07) | Dados pessoais e de pagamento de múltiplos usuários são expostos por falha de autorização no acesso ao banco de dados | Falha de controle de acesso (ex.: referência insegura a objeto) na API que consulta dados de usuário | 2 | 4 | 8 | Alto |
| R08 | Information Disclosure (T08) | Vendedor acessa dados de pedidos e clientes de outros vendedores | Falha de segregação de dados por identificador do vendedor no painel | 2 | 3 | 6 | Médio |
| R09 | Denial of Service (T09) | O checkout fica indisponível ou degradado em datas de alta demanda devido a um grande volume de requisições | Ausência de limitação de requisições (rate limiting) e de proteção contra automação no checkout | 3 | 3 | 9 | Alto |
| R10 | Denial of Service (T10) | A performance da busca de produtos é degradada para todos os usuários por scraping agressivo | Ausência de limitação de requisições na API de busca | 3 | 1 | 3 | Baixo |
| R11 | Elevation of Privilege (T11) | Vendedor obtém acesso a funções reservadas a administradores por falha de autorização | Falha na separação de permissões entre as roles de vendedor e administrador | 1 | 4 | 4 | Médio |
| R12 | Elevation of Privilege (T12) | Cliente comum executa ações administrativas, como aprovar reembolsos próprios, manipulando a API de pedidos | Verificação de permissão (role) feita apenas na interface, não em todas as chamadas do servidor | 2 | 4 | 8 | Alto |

## 13.5 Justificativas

**R01 — Sequestro de conta de cliente:** a probabilidade foi classificada como média-alta (3) porque ataques de credential stuffing são comuns e não dependem de uma vulnerabilidade específica do CompraKi, apenas do reuso de senhas pelos usuários — uma prática frequente. O impacto foi classificado como alto (3) porque a vítima sofre prejuízo financeiro direto e exposição de dados pessoais, mas o efeito fica restrito à conta comprometida, sem afetar a plataforma como um todo.

**R02 — Conta de vendedor falsa:** a probabilidade foi classificada como média-alta (3) porque criar uma conta de vendedor exige apenas preencher um formulário, sem necessidade de conhecimento técnico. O impacto foi classificado como moderado (2) porque afeta principalmente os clientes que compraram daquele vendedor específico, e é parcialmente recuperável por meio de reembolso e suspensão da conta.

**R03 — Alteração do valor do pedido:** a probabilidade foi classificada como média-alta (3) porque ferramentas de interceptação de requisições (proxy, ferramentas de desenvolvedor do navegador) são de fácil acesso, tornando a condição de exploração plausível em situações comuns de uso. O impacto foi classificado como alto (3) porque gera prejuízo financeiro direto e pode se repetir em múltiplos pedidos até ser corrigido.

**R04 — Alteração de catálogo após aprovação:** a probabilidade foi classificada como média-baixa (2) porque depende de um vendedor especificamente mal-intencionado, uma condição menos genérica que um ataque externo. O impacto foi classificado como moderado (2) porque afeta compradores individuais de forma pontual e é recuperável via moderação e reembolso.

**R05 — Negação de compra para reembolso indevido:** a probabilidade foi classificada como média-alta (3) porque fraudes de reembolso por negação de recebimento são um padrão comum e recorrente em e-commerce, não exigindo capacidade técnica. O impacto foi classificado como moderado (2) porque o prejuízo é pontual por pedido e recuperável, embora possa se acumular se o padrão se repetir com frequência.

**R06 — Negação de combinação por mensagem:** a probabilidade foi classificada como média-baixa (2) porque depende de uma disputa específica surgir entre cliente e vendedor. O impacto foi classificado como baixo (1) porque o transtorno é pontual e pode ser mediado pela equipe de suporte, sem prejuízo financeiro direto à plataforma.

**R07 — Exposição do banco de dados de usuários:** a probabilidade foi classificada como média-baixa (2) porque depende da existência de uma falha específica de autorização (como uma referência insegura a objeto), e não de uma condição genérica do sistema. O impacto foi classificado como muito alto (4) porque pode expor dados pessoais e de pagamento de muitos usuários simultaneamente, com implicações legais relacionadas à LGPD e dano reputacional severo.

**R08 — Acesso cruzado entre vendedores:** a probabilidade foi classificada como média-baixa (2) porque exige que um vendedor perceba e explore um padrão específico de identificadores previsíveis. O impacto foi classificado como alto (3) porque expõe dados pessoais de clientes de outros vendedores, embora o escopo fique limitado aos pedidos acessados por aquele vendedor.

**R09 — Sobrecarga do checkout:** a probabilidade foi classificada como média-alta (3) porque picos de tráfego são previsíveis em datas promocionais e ferramentas de automação de requisições são de fácil acesso. O impacto foi classificado como alto (3) porque a indisponibilidade do checkout interrompe diretamente as vendas durante o período do ataque, mesmo sendo recuperável após a mitigação.

**R10 — Degradação da busca por scraping:** a probabilidade foi classificada como média-alta (3) pela facilidade de automatizar requisições repetidas à busca. O impacto foi classificado como baixo (1) porque a degradação afeta a experiência de uso sem impedir totalmente compras ou pagamentos, sendo corrigível rapidamente com limitação de requisições.

**R11 — Vendedor acessa funções administrativas:** a probabilidade foi classificada como baixa (1) porque depende de uma falha grave e específica na separação de permissões, algo incomum quando controles básicos de autorização existem. O impacto foi classificado como muito alto (4) porque comprometeria funções administrativas inteiras, incluindo moderação e acesso a dados de todos os usuários.

**R12 — Escalonamento de privilégio via API de pedidos:** a probabilidade foi classificada como média-baixa (2) porque exige que o atacante identifique tecnicamente o endpoint administrativo e monte uma requisição manual, algo que exige algum conhecimento técnico, mas é viável. O impacto foi classificado como muito alto (4) porque permite fraude financeira direta (aprovação indevida de reembolsos) e representa perda de controle sobre um processo crítico.

## 13.6 Priorização

A pontuação por si só não decide a ordem final: riscos de mesma pontuação foram desempatados pela gravidade da consequência, pelo número de usuários potencialmente afetados e pela facilidade de recuperação.

| Ordem | Risco | Pontuação | Motivo da prioridade |
|---|---|---|---|
| 1 | R12 | 8 | Permite fraude financeira direta e automatizável (aprovação indevida de reembolso), com perda de controle sobre um processo crítico; mesmo com probabilidade média-baixa, a facilidade de repetição do ataque uma vez descoberto o eleva à prioridade máxima |
| 2 | R07 | 8 | Pode expor dados pessoais e de pagamento de muitos usuários simultaneamente, com implicações legais (LGPD) que vão além do prejuízo financeiro imediato |
| 3 | R03 | 9 | Fraude financeira direta e recorrente, decorrente de uma falha estrutural (falta de revalidação no servidor) que tende a se repetir em outros pontos do sistema |
| 4 | R01 | 9 | Ataque comum (credential stuffing) que afeta contas individuais repetidamente e é o risco mais provável de ocorrer no dia a dia da plataforma |
| 5 | R09 | 9 | Interrompe vendas diretamente durante o período do ataque, com maior urgência em datas promocionais previsíveis |
| 6 | R02 | 6 | Fraude contra clientes com dano reputacional, mas de escopo limitado a um vendedor por vez |
| 7 | R08 | 6 | Violação de privacidade entre vendedores, com escopo limitado aos pedidos acessados |
| 8 | R05 | 6 | Prejuízo financeiro recorrente, mas pontual por pedido e mais fácil de mitigar com confirmação de entrega |
| 9 | R04 | 4 | Afeta compradores individuais e é recuperável via moderação |
| 10 | R11 | 4 | Impacto muito alto, mas probabilidade baixa por depender de falha grave e específica de autorização |
| 11 | R10 | 3 | Apenas degrada a experiência de uso, sem impedir vendas ou expor dados |
| 12 | R06 | 2 | Menor pontuação e menor urgência entre todos os riscos identificados |

## 14.1 Estratégias de tratamento

Para cada risco, o grupo escolheu uma das quatro estratégias abaixo, justificada individualmente na seção 14.4:

| Estratégia | Descrição |
|---|---|
| Evitar | Eliminar a atividade ou condição que dá origem ao risco |
| Reduzir | Implementar medidas para diminuir a probabilidade ou o impacto do risco |
| Compartilhar | Atribuir parte da operação ou das consequências a um terceiro |
| Aceitar | Reconhecer e manter conscientemente o risco, com justificativa, aprovação e acompanhamento |

A estratégia predominante no CompraKi é **Reduzir**, já que a maioria dos riscos decorre de controles ausentes ou incompletos (validação apenas no cliente, ausência de MFA, ausência de segregação de dados) que podem ser corrigidos sem eliminar a funcionalidade correspondente. O risco R06 é tratado como **Aceitar**, por ter impacto baixo e custo de mitigação desproporcional ao benefício.

## 14.2 Funções do NIST CSF 2.0

| Função | Finalidade |
|---|---|
| Govern | Definir políticas, responsabilidades, prioridades e critérios de decisão |
| Identify | Conhecer ativos, dependências, vulnerabilidades e riscos |
| Protect | Implementar salvaguardas para reduzir a probabilidade ou o impacto |
| Detect | Identificar eventos suspeitos, falhas e possíveis incidentes |
| Respond | Conter, analisar, comunicar e tratar incidentes |
| Recover | Restaurar serviços e dados e reduzir os prejuízos causados |

As funções não são controles em si — por exemplo, Protect é a função, "impedir alteração indevida do valor do pedido" é o resultado esperado, e "revalidação do valor no servidor" é o controle concreto que entrega esse resultado. O mapeamento de cada risco para as funções relevantes está na seção 14.3, e os controles concretos estão detalhados no plano de tratamento (seção 14.4).

## 14.3 Mapeamento dos riscos para o NIST CSF

Apenas as funções efetivamente relacionadas a cada risco foram marcadas, evitando marcar todas automaticamente.

| Risco | Govern | Identify | Protect | Detect | Respond | Recover |
|---|---|---|---|---|---|---|
| R01 | | | X | X | X | |
| R02 | X | | X | | | |
| R03 | | | X | X | | |
| R04 | | | X | X | | |
| R05 | | | X | X | X | |
| R06 | X | | | | | |
| R07 | X | X | X | X | X | |
| R08 | | | X | X | | |
| R09 | | | X | X | | X |
| R10 | | | X | | | |
| R11 | | | X | X | | |
| R12 | | | X | X | X | |

## 14.4 Plano de tratamento

| Risco | Estratégia | Controles propostos | Funções relacionadas | Responsáveis | Evidências e verificação |
|---|---|---|---|---|---|
| R01 | Reduzir | Autenticação multifator (MFA) obrigatória no login; detecção de login a partir de dispositivo ou localização não reconhecida, com notificação ao usuário | Protect, Detect, Respond | Equipe de desenvolvimento (autenticação) | Teste automatizado bloqueando login sem o segundo fator; log de alertas de login suspeito |
| R02 | Reduzir | Verificação de documento (CPF/CNPJ) e aprovação manual do primeiro anúncio de novos vendedores | Govern, Protect | Equipe de operações e moderação | Checklist de aprovação de vendedor; taxa de contas suspensas por documentação inválida |
| R03 | Reduzir | Revalidação do valor total do pedido no servidor no momento do pagamento, ignorando valores recebidos do cliente | Protect, Detect | Equipe de backend e pagamentos | Teste automatizado que altera o valor via requisição direta e confirma a rejeição ou correção pelo servidor |
| R04 | Reduzir | Exigir reaprovação de moderação quando preço ou descrição de um anúncio já aprovado é alterado; manter histórico de versões do anúncio | Protect, Detect | Equipe de moderação e backend | Log de versões de anúncios; teste confirmando bloqueio de publicação sem reaprovação |
| R05 | Reduzir | Exigir confirmação de recebimento (código de entrega ou assinatura eletrônica) antes de liberar reembolsos automáticos; log de auditoria do status do pedido | Protect, Detect, Respond | Equipe de operações e logística | Relatório de pedidos com confirmação de entrega; casos de reembolso analisados manualmente quando não há confirmação |
| R06 | Aceitar | Aceitação formal do risco pela coordenação de atendimento, com mediação manual de disputas com base no histórico de mensagens disponível | Govern | Coordenação de atendimento ao cliente | Registro da decisão de aceitação; revisão semestral do volume de disputas relacionadas a mensagens |
| R07 | Reduzir | Corrigir o controle de acesso na API (verificar propriedade do recurso antes de retornar dados, eliminando a referência insegura a objeto); criptografar dados de pagamento e dados pessoais em repouso; política de proteção de dados alinhada à LGPD | Govern, Identify, Protect, Detect, Respond | Equipe de backend e segurança | Teste de penetração confirmando ausência da falha de autorização; relatório de criptografia aplicada; alertas de acesso anômalo configurados |
| R08 | Reduzir | Corrigir a consulta do painel do vendedor para filtrar sempre pelo identificador do vendedor autenticado, nunca aceitando identificador vindo do cliente sem validação; log de tentativas de acesso a pedidos de outros vendedores | Protect, Detect | Equipe de backend | Teste automatizado tentando acessar pedido de outro vendedor e confirmando bloqueio |
| R09 | Reduzir | Limitação de requisições (rate limiting) no endpoint de checkout; proteção anti-automação em picos de tráfego; plano de autoscaling para datas de alta demanda | Protect, Detect, Recover | Equipe de infraestrutura | Teste de carga simulando pico de requisições; métricas de disponibilidade do checkout durante o teste |
| R10 | Reduzir | Limitação de requisições na API de busca por usuário/IP | Protect | Equipe de backend | Métricas de requisições bloqueadas por excesso na API de busca |
| R11 | Reduzir | Revalidação de permissão (role) no servidor para toda rota do painel administrativo, independentemente da interface exibida; testes de autorização automatizados | Protect, Detect | Equipe de backend e segurança | Teste automatizado tentando acessar rota administrativa com conta de vendedor e confirmando bloqueio (HTTP 403) |
| R12 | Reduzir | Revalidação de role no servidor antes de aprovar qualquer reembolso; log de auditoria de todas as aprovações, com alerta quando aprovado por conta não administrativa | Protect, Detect, Respond | Equipe de backend e pagamentos | Teste automatizado tentando aprovar reembolso com conta de cliente e confirmando bloqueio; auditoria mensal de reembolsos aprovados |

## 14.5 Ordem inicial de implementação

A ordem prioriza controles que corrigem um mesmo padrão estrutural recorrente — validação e verificação de permissão feitas apenas no lado do cliente ou apenas na interface — e que, por isso, reduzem vários riscos de uma só vez:

1. **Revalidação no servidor de valores e permissões (R03, R11, R12):** corrige o mesmo padrão de falha (confiar em validação do lado do cliente ou da interface) presente em três riscos de pontuação alta ou média, incluindo o de maior prioridade (R12).
2. **Correção do controle de acesso e criptografia de dados sensíveis (R07):** trata o risco de maior impacto potencial (exposição de dados de muitos usuários, com implicações legais).
3. **Autenticação multifator para contas de cliente (R01):** mitiga o risco mais provável de ocorrer no dia a dia da plataforma.
4. **Rate limiting e proteção anti-automação no checkout (R09):** protege a disponibilidade em datas de alta demanda, previsíveis no calendário do negócio.
5. **Segregação de dados entre vendedores e verificação de identidade de novos vendedores (R08, R02):** controles de escopo mais restrito, mas que reduzem riscos de nível médio a alto.
6. **Demais controles (R04, R05, R10):** implementados na sequência, por terem impacto ou probabilidade menores que os controles anteriores.
7. **Aceitação formal do risco de mensagens (R06):** não exige implementação de controle técnico, apenas formalização da decisão de aceitar.

Esta ordem poderá ser revisada nas próximas etapas, especialmente na Etapa 3, ao detalhar a arquitetura segura.

## 14.6 Estimativa do risco residual

| Risco | Nível inicial | Nível residual esperado | Condição para aceitar o residual |
|---|---|---|---|
| R01 | Alto | Baixo | Taxa de bloqueio de login suspeito acima de 95% em testes de simulação |
| R02 | Médio | Baixo | 100% dos novos vendedores com documento verificado antes da primeira venda |
| R03 | Alto | Baixo | Teste automatizado de revalidação do valor aprova em 100% dos casos de tentativa de alteração |
| R04 | Médio | Baixo | Nenhum anúncio alterado publicado sem reaprovação, confirmado em auditoria mensal |
| R05 | Médio | Baixo | Percentual de reembolsos aprovados sem confirmação de entrega abaixo de um limite definido pela equipe de operações |
| R06 | Baixo | Baixo (risco aceito, sem redução planejada) | Revisão semestral confirma que o volume de disputas de mensagens permanece dentro do limite aceitável |
| R07 | Alto | Médio | Teste de penetração não encontra falha crítica de autorização e a criptografia dos dados sensíveis é confirmada em auditoria |
| R08 | Médio | Baixo | Teste automatizado de segregação por vendedor aprova em 100% dos casos |
| R09 | Alto | Médio | Checkout mantém disponibilidade acima do SLA definido durante teste de carga simulando o pico real observado em datas promocionais |
| R10 | Baixo | Baixo | Limitação de requisições reduz o volume de scraping sem afetar usuários legítimos, conforme monitoramento |
| R11 | Médio | Baixo | Teste de autorização automatizado bloqueia 100% das tentativas de acesso indevido ao painel administrativo |
| R12 | Alto | Médio | Auditoria mensal de reembolsos não encontra nenhuma aprovação fora do fluxo administrativo |

## 15. Considerações finais

Os riscos considerados mais importantes foram R12 (escalonamento de privilégio via API de pedidos) e R07 (exposição do banco de dados de usuários), por permitirem, respectivamente, fraude financeira direta e automatizável, e exposição de dados de muitos usuários com implicações legais relacionadas à LGPD. R03 (alteração do valor do pedido) e R01 (sequestro de conta) também tiveram prioridade alta por decorrerem de falhas comuns e prováveis de ocorrer no uso normal da plataforma.

A priorização foi determinada principalmente pela combinação entre pontuação (probabilidade × impacto) e a gravidade das consequências: riscos com potencial de fraude financeira direta ou exposição de dados de muitos usuários foram priorizados mesmo quando a pontuação não era a mais alta entre todos.

A estratégia de tratamento predominante foi **Reduzir**, aplicada a 11 dos 12 riscos, já que a maioria decorre de controles ausentes ou incompletos que podem ser corrigidos sem eliminar funcionalidades. Apenas R06 foi tratado com a estratégia **Aceitar**, por seu baixo impacto.

As funções do NIST CSF 2.0 mais relevantes para o CompraKi foram **Protect** e **Detect**, presentes em praticamente todos os planos de tratamento, seguidas por **Respond** nos riscos de maior gravidade (R01, R05, R07, R12) — R09 fica de fora desse grupo por ter sua resposta concentrada em Recover, já que a mitigação é a normalização do serviço, não uma investigação de incidente — e **Govern** nos riscos que envolvem decisões de política (R02, R06, R07).

Os controles considerados essenciais foram: revalidação de valores e permissões no servidor (o controle que, isoladamente, mitiga o maior número de riscos prioritários), autenticação multifator, correção das falhas de controle de acesso (referências inseguras a objeto) e limitação de requisições no checkout.

A principal dificuldade do grupo foi estimar probabilidade e impacto de forma defensável sem uma implementação real do CompraKi, apoiando-se em como falhas semelhantes costumam se comportar em sistemas de e-commerce reais. Também foi desafiador diferenciar claramente ameaça, vulnerabilidade e risco ao longo do registro, e evitar marcar funções do NIST automaticamente para todos os riscos.

Como limitação, as estimativas de risco residual são projeções qualitativas — a redução real só poderá ser confirmada após a implementação, os testes e a coleta de evidências, o que ficará mais detalhado a partir da Etapa 3, quando os requisitos de segurança e a arquitetura forem formalizados.
