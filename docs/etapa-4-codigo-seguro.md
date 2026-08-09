# Etapa 4 — Código Seguro e Testes de Segurança

O grupo optou pelo **caminho descritivo**: as práticas abaixo são apresentadas como pseudocódigo e testes descritos textualmente, sem uma implementação executável do CompraKi.

## Prática 1 — Controle de autorização no servidor para ações administrativas

**Risco e requisito relacionados:** R12 (Elevation of Privilege) / RS01 — o sistema deve revalidar, no servidor, a permissão do usuário antes de executar ações administrativas como a aprovação de reembolsos (ver [Etapa 2](etapa-2-riscos-nist.md) e [Etapa 3](etapa-3-arquitetura-segura.md)).

**Testes de segurança (definidos antes da implementação):**

| Teste | Entrada ou ação | Resultado esperado |
|---|---|---|
| TS01 | Administrador autenticado solicita a aprovação de um reembolso | A solicitação é permitida e a aprovação é registrada em log de auditoria |
| TS02 | Cliente comum (sem role de administrador) monta manualmente uma requisição para o mesmo endpoint de aprovação de reembolso | A solicitação é recusada com erro de autorização e a tentativa é registrada em log |

**Implementação (pseudocódigo):**

```
function aprovarReembolso(pedidoId, usuarioAutenticado):
    if usuarioAutenticado.role != "administrador":
        registrarTentativaNaoAutorizada(usuarioAutenticado, pedidoId)
        retornar erro 403 "Acesso não autorizado"

    pedido = buscarPedido(pedidoId)
    pedido.status = "reembolsado"
    registrarAuditoria(usuarioAutenticado, pedidoId, "reembolso aprovado")
    retornar sucesso
```

**Resultado esperado:** a verificação da role ocorre inteiramente no servidor, a cada chamada, de forma que nenhuma requisição — mesmo montada manualmente fora da interface — consiga aprovar um reembolso sem uma conta administrativa autenticada.

**Referência OWASP:** OWASP Cheat Sheet Series — Authorization Cheat Sheet (toda solicitação sensível deve ser autorizada no lado do servidor, independentemente do que a interface exibe ou oculta).

## Prática 2 — Verificação de propriedade do recurso (prevenção de IDOR)

**Risco e requisito relacionados:** R07 (Information Disclosure) / RS02 — o sistema deve validar, no servidor, que o usuário autenticado é o dono do dado solicitado antes de retornar informações pessoais ou de pagamento (ver [Etapa 2](etapa-2-riscos-nist.md) e [Etapa 3](etapa-3-arquitetura-segura.md)).

**Testes de segurança (definidos antes da implementação):**

| Teste | Entrada ou ação | Resultado esperado |
|---|---|---|
| TS01 | Cliente autenticado solicita os dados do próprio pedido (ex.: `GET /pedidos/123`, onde 123 pertence a ele) | O sistema retorna os dados do pedido normalmente |
| TS02 | O mesmo cliente altera manualmente o identificador na URL para acessar um pedido de outro usuário (ex.: `GET /pedidos/124`, pertencente a outra pessoa) | O sistema recusa o acesso e registra a tentativa |

**Implementação (pseudocódigo):**

```
function obterPedido(pedidoId, usuarioAutenticado):
    pedido = buscarPedido(pedidoId)

    if pedido == nulo:
        retornar erro 404 "Pedido não encontrado"

    if pedido.usuarioId != usuarioAutenticado.id:
        registrarTentativaNaoAutorizada(usuarioAutenticado, pedidoId)
        retornar erro 403 "Acesso não autorizado"

    retornar pedido
```

**Resultado esperado:** o servidor sempre verifica se o recurso solicitado pertence ao usuário autenticado antes de retornar qualquer dado, de forma que identificadores sequenciais ou previsíveis não sejam suficientes para acessar dados de outros usuários.

**Referência OWASP:** OWASP Cheat Sheet Series, na parte de controle de acesso — prevenção de Insecure Direct Object References (IDOR), relacionada ao OWASP API Security Top 10 (API1:2023 — Broken Object Level Authorization), já citado na Etapa 3 para o risco R07.
