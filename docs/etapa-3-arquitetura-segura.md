# Etapa 3 — Projeto de uma Arquitetura Segura

Esta etapa transforma os riscos e controles definidos na [Etapa 2](etapa-2-riscos-nist.md) em requisitos de segurança e decisões de arquitetura para o CompraKi, priorizando os três riscos de maior prioridade identificados na priorização geral (seção 13.6 da Etapa 2): R12, R07 e R03.

## 18.1 Requisitos de segurança

| ID | Risco de origem | Requisito de segurança | Critério de verificação |
|---|---|---|---|
| RS01 | R12 | O sistema deverá revalidar, no servidor, a permissão (role) do usuário antes de executar qualquer ação administrativa, como a aprovação de reembolsos, independentemente da interface utilizada para fazer a requisição | A ação deverá ser recusada com HTTP 403 quando solicitada por uma conta sem privilégio administrativo, mesmo que a requisição seja montada manualmente fora da interface |
| RS02 | R07 | O sistema deverá validar, no servidor, que o usuário autenticado é o dono do dado solicitado antes de retornar qualquer informação pessoal ou de pagamento pela API | Uma requisição para dados de outro usuário deverá ser recusada com HTTP 403/404, mesmo alterando manualmente o identificador do recurso na URL ou no corpo da requisição |
| RS03 | R03 | O sistema deverá recalcular o valor total do pedido no servidor, a partir do catálogo de preços vigente, ignorando qualquer valor de total enviado pelo cliente, no momento da confirmação do pagamento | O pagamento deverá ser processado pelo valor calculado no servidor, mesmo que o cliente envie um valor de total diferente na requisição |

## 18.2 Vulnerabilidades catalogadas

| Risco | Vulnerabilidade ou categoria | Referência utilizada | Relação com o sistema |
|---|---|---|---|
| R12 | Falha de controle de acesso a funções (autorização ausente no servidor) | CWE-862 (Missing Authorization); OWASP Top 10 A01:2021 – Broken Access Control | Permite que qualquer cliente autenticado execute uma função administrativa (aprovar reembolso) porque o servidor não revalida a permissão antes de executar a ação |
| R07 | Referência insegura a objeto (IDOR) | CWE-639 (Authorization Bypass Through User-Controlled Key); OWASP API Security Top 10 API1:2023 – Broken Object Level Authorization | Permite acessar dados pessoais e de pagamento de outros usuários apenas alterando um identificador em uma requisição |
| R03 | Confiança em validação feita no lado do cliente | CWE-602 (Client-Side Enforcement of Server-Side Security) | Permite que o cliente altere o valor do pedido porque a validação definitiva do valor não ocorre no servidor no momento do pagamento |

Os três mapeamentos cobrem os três riscos de maior prioridade da Etapa 2, mostrando que, apesar de originarem de categorias STRIDE diferentes (Elevation of Privilege, Information Disclosure e Tampering), compartilham a mesma causa raiz arquitetural: falta de revalidação no servidor.
