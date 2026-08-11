# Etapa 5 — Verificação de Vulnerabilidades

## Sistema e ambiente testado

Como o CompraKi não possui uma implementação real (o trabalho seguiu o caminho descritivo na Etapa 4), a verificação foi realizada sobre o **OWASP Juice Shop**, aplicação deliberadamente vulnerável recomendada pelo próprio enunciado para grupos sem sistema implementado. O Juice Shop também é um e-commerce, o que mantém coerência temática com o domínio do CompraKi.

## Ferramenta utilizada e configuração do teste

- **Ambiente testado:** OWASP Juice Shop (imagem oficial `bkimminich/juice-shop`), executado localmente via Docker, disponível em `http://localhost:3000`.
- **Ferramenta:** OWASP ZAP (imagem oficial `zaproxy/zap-stable`), também via Docker.
- **Tipo de varredura:** ZAP Baseline Scan (`zap-baseline.py`), que combina rastreamento automático (spider) do site com a análise passiva do tráfego capturado — sem exploração ativa de vulnerabilidades.
- **Comando executado:**
  ```
  docker run --name zap-scan -v "<pasta_de_saida>:/zap/wrk:rw" zaproxy/zap-stable \
    zap-baseline.py -t http://host.docker.internal:3000 -r report.html -J report.json -I
  ```

## Evidência da execução

O scan rastreou 158 URLs e classificou 59 verificações como aprovadas (PASS) e 8 categorias como alerta (WARN-NEW), sem nenhuma falha crítica (FAIL) identificada nesta varredura passiva. O relatório completo gerado pela ferramenta está versionado em [`evidencias/etapa-5/zap-report.html`](../evidencias/etapa-5/zap-report.html) (relatório HTML completo) e [`evidencias/etapa-5/zap-report.json`](../evidencias/etapa-5/zap-report.json) (dados estruturados usados na análise abaixo).

## Análise dos achados

| ID | Alerta ou achado | Evidência | Possível impacto | Relação com OWASP ou CWE | Correção proposta |
|---|---|---|---|---|---|
| A01 | Cross-Domain Misconfiguration (CORS excessivamente permissivo) | Cabeçalho `Access-Control-Allow-Origin: *` presente nas respostas de `/chunk-PX7UKXVL.js`, `/robots.txt` e `/sitemap.xml` | Permite que qualquer site de terceiros, executado no navegador da vítima, leia essas respostas via JavaScript; se a mesma configuração de CORS aberto se aplicar a endpoints que retornam dados de usuário (como no risco R07 do CompraKi), isso facilitaria a exfiltração de dados por um site malicioso | CWE-264 (Permissions, Privileges and Access Control) | Restringir o cabeçalho `Access-Control-Allow-Origin` a uma lista específica de domínios confiáveis, em vez de usar `*`, especialmente em qualquer endpoint que retorne dados sensíveis |
| A02 | Content Security Policy (CSP) Header Not Set | Nenhum cabeçalho `Content-Security-Policy` presente nas respostas de `http://host.docker.internal:3000/` e de outras páginas rastreadas | Na ausência de CSP, o navegador não tem uma camada adicional de defesa contra Cross-Site Scripting (XSS): se algum ponto da aplicação permitir injeção de script, ele será executado sem restrição de origem | CWE-693 (Protection Mechanism Failure); OWASP Cheat Sheet Series — Content Security Policy Cheat Sheet | Configurar o servidor de aplicação para enviar um cabeçalho `Content-Security-Policy` restritivo, definindo explicitamente as origens permitidas para scripts, estilos e outros recursos |
| A03 | Dangerous JS Functions (uso de `bypassSecurityTrustHtml`) | Ocorrência da função `bypassSecurityTrustHtml(` identificada em `http://host.docker.internal:3000/main.js` | Essa função do Angular desativa deliberadamente a sanitização automática de HTML; se o conteúdo passado a ela vier de uma fonte não confiável (entrada do usuário, por exemplo), abre uma via direta para XSS | CWE-749 (Exposed Dangerous Method or Function); OWASP Cheat Sheet Series — Cross Site Scripting Prevention Cheat Sheet | Evitar o uso de `bypassSecurityTrustHtml` com conteúdo proveniente de entrada do usuário; quando estritamente necessário, sanitizar o conteúdo manualmente antes de confiar nele, restringindo essa prática a conteúdo controlado pela própria aplicação |

## Achados descartados

Os demais alertas retornados pelo ZAP (por exemplo, `Cross-Origin-Embedder-Policy Header Missing`, `Cross-Origin-Opener-Policy Header Missing`, `Deprecated Feature Policy Header Set`, `Timestamp Disclosure - Unix` e os alertas informativos de cache) foram classificados como de risco baixo ou informativo pela própria ferramenta e não foram detalhados na tabela acima por representarem, isoladamente, um risco bem menor do que os três achados analisados — sem indicar, por si só, uma via clara de exploração.

## Relação com a análise de riscos

O achado A01 (CORS permissivo) se relaciona diretamente ao risco **R07 — exposição do banco de dados de usuários** (Etapa 2): uma configuração de CORS aberta em endpoints que retornam dados sensíveis reforça exatamente o tipo de exposição indevida de informação já identificado na modelagem STRIDE. Os achados A02 e A03 reforçam a importância de camadas de defesa contra XSS que, embora não tenham sido modeladas como uma ameaça STRIDE específica no CompraKi, complementam os controles de Tampering e Information Disclosure já propostos nas Etapas 2 e 3.
