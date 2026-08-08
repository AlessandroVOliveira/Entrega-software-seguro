# Etapa 1 — Casos de Abuso e Modelagem de Ameaças com STRIDE

## 8.1 Identificação do sistema

**Nome do sistema:** CompraKi

**Integrantes do grupo:**
- Ariessa Velasques Oliveira ([@AriessaVelasques](https://github.com/AriessaVelasques))
- Alessandro Velasques Oliveira ([@AlessandroVOliveira](https://github.com/AlessandroVOliveira))
- Valdir Guilherme Tavares ([@ValdirGuiTavares](https://github.com/ValdirGuiTavares))
- Leonardo Paulino ([@LeonardoPaulino77](https://github.com/LeonardoPaulino77))
- Eric Moraes ([@MoraesEric55](https://github.com/MoraesEric55))
- Douglas Israel ([@DougIsrael](https://github.com/DougIsrael))

**Endereço do repositório:** https://github.com/AlessandroVOliveira/Entrega-software-seguro

**Justificativa para a escolha do sistema:**

O CompraKi foi escolhido por ser um marketplace de e-commerce, ou seja, uma plataforma que conecta clientes e vendedores terceiros sob a administração de uma empresa central. Esse formato reúne três perfis de usuário com interesses e níveis de acesso diferentes (cliente, vendedor e administrador), o que gera uma variedade maior de ameaças e casos de abuso do que uma loja única controlada por um só tipo de usuário. Além disso, o domínio de e-commerce envolve operações sensíveis (pagamento, dados pessoais, avaliações, mensagens entre cliente e vendedor) e se alinha com o uso do OWASP Juice Shop — uma aplicação de e-commerce vulnerável usada para fins educacionais — na verificação prática de vulnerabilidades prevista na Etapa 5.

## 8.2 Descrição do sistema

O CompraKi é um marketplace de e-commerce: uma plataforma onde vendedores terceiros cadastram e vendem seus próprios produtos, e clientes compram diretamente pelo site ou aplicativo, com a plataforma intermediando o pagamento, a logística de contato e a resolução de disputas.

**Problema que o sistema resolve:** pequenos e médios vendedores não precisam construir sua própria loja virtual, sistema de pagamento ou base de clientes — usam a infraestrutura já existente do CompraKi. Para os clientes, a plataforma concentra vários vendedores em um único lugar, com um único checkout e um único canal de suporte.

**Quem utiliza o sistema:**
- **Clientes**: pessoas físicas que navegam pelo catálogo, compram produtos, avaliam vendedores/produtos e trocam mensagens com vendedores.
- **Vendedores**: pessoas físicas ou empresas que cadastram produtos, gerenciam pedidos recebidos e respondem a clientes.
- **Administradores**: equipe do CompraKi responsável por moderar anúncios, mediar disputas entre clientes e vendedores, e gerenciar a operação da plataforma.

**Principais funcionalidades:**
- cadastro e login de clientes e vendedores;
- catálogo de produtos com busca e filtros;
- carrinho de compras e checkout;
- processamento de pagamento (cartão ou pix simulado);
- avaliações de produtos e de vendedores;
- mensagens diretas entre cliente e vendedor;
- painel do vendedor (cadastro de produtos, gestão de pedidos);
- painel administrativo (moderação de anúncios, mediação de disputas, suspensão de contas).

**Informações armazenadas ou transmitidas:** dados de cadastro (nome, e-mail, CPF, endereço), credenciais de acesso, dados de pagamento, histórico de pedidos, mensagens entre clientes e vendedores, avaliações e comentários, catálogo de produtos e preços.

**Recursos que precisam ser protegidos:** contas de usuário (contra acesso indevido), dados de pagamento, integridade dos valores de pedidos e preços, autenticidade das avaliações, e as funções administrativas (que, se comprometidas, afetam toda a plataforma).

## 8.3 Usuários, ativos e pontos de interação

**Usuários e perfis de acesso:**
- Cliente (comprador): navega, compra, avalia e troca mensagens com vendedores.
- Vendedor: cadastra produtos, gerencia seus próprios pedidos e responde clientes.
- Administrador: modera anúncios, resolve disputas e gerencia contas.

**Principais elementos do sistema:**

| Elemento | Descrição | Ativo crítico? |
|---|---|---|
| Credenciais de login | Senha/token de autenticação de clientes, vendedores e administradores | Sim |
| Dados pessoais | Nome, CPF, endereço de entrega | Sim |
| Dados de pagamento | Número de cartão (tokenizado) ou dados de pix | Sim |
| Histórico de pedidos | Registro de compras, valores e status | Sim |
| Avaliações | Notas e comentários sobre produtos e vendedores | Não (mas sensível a manipulação) |
| Mensagens | Comunicação direta entre cliente e vendedor | Não (mas contém dados pessoais) |
| Catálogo de produtos | Descrições, preços e estoque cadastrados pelos vendedores | Sim |
| Banco de dados | Armazena usuários, pedidos, produtos e mensagens | Sim |
| API/Backend | Camada que processa regras de negócio e autorização | Sim |
| Aplicativo web/mobile | Interface usada por clientes e vendedores | Não |
| Serviço de pagamento externo | Processa a cobrança efetiva (integração de terceiros) | Sim |
| Painel administrativo | Interface usada pelos administradores para moderar e decidir disputas | Sim |

Os elementos marcados como ativo crítico são aqueles cujo acesso indevido, alteração, destruição ou indisponibilidade causaria prejuízo financeiro direto, violação de privacidade ou perda de confiança na plataforma — por isso recebem atenção prioritária na modelagem de ameaças (seção 8.5).
