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
