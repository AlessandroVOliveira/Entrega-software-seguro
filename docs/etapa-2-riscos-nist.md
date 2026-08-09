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
