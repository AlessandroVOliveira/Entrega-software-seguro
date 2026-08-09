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

## 13.5 Justificativas

**R01 — Sequestro de conta de cliente:** a probabilidade foi classificada como média-alta (3) porque ataques de credential stuffing são comuns e não dependem de uma vulnerabilidade específica do CompraKi, apenas do reuso de senhas pelos usuários — uma prática frequente. O impacto foi classificado como alto (3) porque a vítima sofre prejuízo financeiro direto e exposição de dados pessoais, mas o efeito fica restrito à conta comprometida, sem afetar a plataforma como um todo.

**R02 — Conta de vendedor falsa:** a probabilidade foi classificada como média-alta (3) porque criar uma conta de vendedor exige apenas preencher um formulário, sem necessidade de conhecimento técnico. O impacto foi classificado como moderado (2) porque afeta principalmente os clientes que compraram daquele vendedor específico, e é parcialmente recuperável por meio de reembolso e suspensão da conta.
