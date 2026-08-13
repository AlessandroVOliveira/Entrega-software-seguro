# Etapa 6 — Monitoramento e Detecção de Intrusões

## O que é detecção de intrusões

Detecção de intrusões é o processo de observar eventos do sistema em operação (acessos, requisições, erros, mudanças de dados) para identificar comportamentos que indiquem um ataque em andamento ou já ocorrido — mesmo quando os controles preventivos falharam ou foram contornados. Um sistema de detecção de intrusões (IDS) não impede a ação por si só; ele reconhece o padrão suspeito e aciona um alerta para que uma resposta seja tomada.

## Diferença entre prevenir e detectar

Prevenir significa impedir que uma ação indevida aconteça (por exemplo, o controle de autorização no servidor da Etapa 4, que recusa uma requisição sem a permissão adequada). Detectar significa identificar que uma tentativa de ação indevida ocorreu — inclusive tentativas que os controles preventivos conseguiram bloquear, o que já é um sinal valioso de que alguém está testando os limites do sistema. As duas abordagens são complementares: a prevenção reduz a probabilidade de sucesso do ataque, e a detecção garante que, mesmo quando um controle falha ou é testado, a equipe seja avisada a tempo de agir.

## Eventos que deveriam ser registrados

Com base nos riscos identificados na Etapa 2, o CompraKi deveria registrar, no mínimo:

- tentativas de login (bem-sucedidas e malsucedidas), com IP, dispositivo e horário;
- aprovações de reembolso, com a conta que executou a ação e sua role no momento;
- alterações de valor ou status de pedidos;
- acessos a dados de pedidos, indicando o vendedor ou cliente autenticado e o recurso acessado;
- volume de requisições por IP/usuário aos endpoints críticos (checkout, busca, login);
- erros de autorização (HTTP 403) retornados pela API.

## Regras de detecção

| Risco observado | Fonte de dados | Condição de alerta | Resposta inicial |
|---|---|---|---|
| R01 — Sequestro de conta de cliente (credential stuffing) | Logs de autenticação | Cinco ou mais tentativas de login malsucedidas para a mesma conta em dez minutos, provenientes de três ou mais endereços IP distintos; ou login bem-sucedido a partir de um dispositivo/local nunca usado por aquele usuário, ocorrendo em até cinco minutos após uma sequência de tentativas falhas | Bloquear temporariamente novas tentativas de login para a conta, notificar o usuário por e-mail e exigir confirmação adicional (segundo fator) no próximo acesso |
| R12 — Escalonamento de privilégio via API de pedidos (reembolso indevido) | Logs de auditoria de aprovação de reembolso | Reembolso aprovado por qualquer conta cuja role não é administrador (alerta imediato, sem limiar de contagem); ou mais de dez reembolsos aprovados pela mesma conta administrativa em uma janela de uma hora, quando a média histórica dessa conta é de até dois por hora | Suspender temporariamente a conta envolvida, colocar o reembolso em análise manual antes de efetivá-lo e alertar a equipe de segurança |
| R09 — Sobrecarga do checkout (Denial of Service) | Logs de requisições do servidor/API (taxa de requisições por IP) | Mais de cem requisições ao endpoint de checkout em um minuto originadas do mesmo endereço IP, ou mais de mil requisições por minuto somando um conjunto de até dez IPs, quando a média histórica do horário é de até vinte requisições por minuto por IP | Acionar limitação de requisições (rate limiting) para os IPs envolvidos, alertar a equipe de infraestrutura e monitorar a disponibilidade do checkout até a normalização |

## O que deveria acontecer depois de um alerta

Após um alerta ser disparado, a equipe responsável deveria: (1) confirmar se o comportamento é de fato suspeito ou um falso positivo, verificando o contexto do evento; (2) aplicar a resposta inicial definida na regra correspondente; (3) registrar o incidente, mesmo que confirmado como falso positivo, para ajustar o limiar da regra no futuro; e (4) comunicar o incidente às pessoas responsáveis quando o risco associado for alto ou crítico (como R12), para que uma investigação mais profunda seja aberta.
