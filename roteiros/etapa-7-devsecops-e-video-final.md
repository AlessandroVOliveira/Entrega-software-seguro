# Etapa 7 — DevSecOps e Vídeo Final

Esta etapa integra o que foi produzido nas Etapas 1 a 6 em um pipeline DevSecOps proposto para o CompraKi. Como o sistema não foi implementado, o pipeline é descritivo — não há automação real configurada — mas cada linha da tabela abaixo corresponde a um artefato que já existe no repositório, produzido em uma etapa anterior.

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

## Roteiro do vídeo final

Duração alvo: 5 a 8 minutos. Cada integrante apresenta a parte que produziu ao longo da disciplina, seguindo a mesma divisão por categoria STRIDE usada desde a Etapa 1.

| Tempo | Bloco | Responsável | Conteúdo a apresentar |
|---|---|---|---|
| 0:00–0:30 | Abertura | P1 (Ariessa) | Nome do sistema (CompraKi), o que é (marketplace de e-commerce) e por que foi escolhido |
| 0:30–2:00 | Ameaças STRIDE e casos de abuso | Todos (cada pessoa apresenta sua categoria: Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege) | Uma ameaça e o caso de abuso correspondente à categoria de cada um, em poucas frases |
| 2:00–3:00 | Riscos prioritários | P6 (Douglas) | Os 3 riscos de maior prioridade (R12, R07, R03) e por que ficaram no topo da priorização |
| 3:00–4:00 | Arquitetura segura | P1 (Ariessa) e P2 (Alessandro) | Os requisitos de segurança (RS01–RS03), o diagrama da arquitetura segura e as 3 decisões de arquitetura |
| 4:00–5:00 | Código seguro | P3 (Valdir) e P4 (Leonardo) | As duas práticas de código seguro (controle de autorização e prevenção de IDOR) e os testes definidos antes da implementação |
| 5:00–6:00 | Verificação de vulnerabilidades | P5 (Eric) | O ambiente testado (OWASP Juice Shop), a ferramenta (ZAP) e os 3 achados analisados |
| 6:00–7:00 | Detecção de intrusões e pipeline DevSecOps | P6 (Douglas) | As 3 regras de detecção e como o pipeline DevSecOps integra todas as etapas |
| 7:00–8:00 | Aprendizados e encerramento | Todos | O que cada um aprendeu de mais relevante durante o trabalho |

A gravação, edição e publicação do vídeo ficam a cargo do grupo — este roteiro apenas organiza o conteúdo e o tempo de cada bloco.