# Etapa 7 — DevSecOps e Vídeo Final

## Pipeline DevSecOps proposto

O pipeline abaixo integra as atividades de segurança já produzidas nas Etapas 1 a 6, organizadas ao longo do ciclo de desenvolvimento do CompraKi.

| Momento | Atividade de segurança | Evidência produzida | Condição para continuar |
|---|---|---|---|
| Planejamento | Modelagem de ameaças com STRIDE e análise de riscos (Etapas 1 e 2) | Tabela de ameaças (T01–T12) e registro de riscos (R01–R12) | Riscos prioritários identificados e sem pendência de revisão |
| Requisitos e arquitetura | Definição de requisitos de segurança e decisões de arquitetura (Etapa 3) | Diagrama da arquitetura segura e tabela de requisitos (RS01–RS03) | Requisitos rastreáveis a riscos críticos ou altos |
| Implementação segura | Aplicação das práticas de código seguro (Etapa 4) | Pseudocódigo e testes de segurança descritos para cada prática | Testes de segurança definidos e sem reprovação |
| Testes automatizados | Execução dos testes de segurança definidos na Etapa 4 | Resultado dos testes (aprovado/reprovado) | Nenhum teste de segurança reprovado |
| Análise de código e dependências | Revisão de código e verificação de dependências vulneráveis | Relatório de dependências analisadas | Nenhuma dependência crítica conhecida sem correção |
| Teste dinâmico (DAST) | Varredura com OWASP ZAP no ambiente de teste (Etapa 5) | Relatório do ZAP e análise dos achados | Nenhum achado crítico sem análise e correção proposta |
| Implantação | Deploy controlado, com revisão de configuração e segredos | Checklist de implantação | Nenhum segredo ou credencial exposto no repositório ou na configuração |
| Monitoramento e resposta | Aplicação das regras de detecção definidas na Etapa 6 | Logs e alertas configurados | Incidentes tratados conforme a resposta inicial definida em cada regra |

## Condições que impedem a continuidade do pipeline

O pipeline não deve avançar para a etapa seguinte quando qualquer uma das condições abaixo ocorrer:

1. **Teste de segurança reprovado** — por exemplo, se a revalidação do valor do pedido no servidor (RS03) não bloquear uma tentativa de alteração indevida no teste TS02 da Etapa 4.
2. **Achado crítico não analisado** — se a varredura com o ZAP (Etapa 5) apontar um alerta de risco alto ou crítico sem análise e correção propostas.
3. **Segredo encontrado no repositório** — como uma chave de API, senha ou token de acesso versionado por engano no código ou na configuração.
4. **Dependência conhecida como vulnerável** sem atualização disponível ou aplicada, identificada na análise de dependências.