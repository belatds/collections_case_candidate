# Case Técnico — Cientista de Dados Sênior (Cobrança & CRM)

## Contexto

Você acaba de entrar no time de dados de uma fintech de crédito ao trabalhador. Quando um
cliente atrasa um pagamento, ele entra na **fila de cobrança** e passa a receber lembretes por
WhatsApp pedindo a regularização. Cada mensagem enviada custa **R$ 1,00** ao provedor, **inclusive
as que falham** (número inválido, cliente bloqueou o remetente, telefone inalcançável).

Nos últimos três meses (junho a agosto de 2026) o time de operações disparava cerca de
**25 mil mensagens por mês**, sem critério muito definido de quem contatar, quando e com qual texto.
Sua gestora acaba de comunicar que, a partir de setembro, o **orçamento mensal de WhatsApp é de
R$ 10.000** (ou seja, no máximo 10.000 tentativas de envio no mês).

Sua missão: **decidir como gastar esses R$ 10.000 em setembro para maximizar o valor recuperado,
reduzindo ao máximo as tentativas falhas.**

## Regras do jogo

| Regra | Valor |
|---|---|
| Custo por tentativa de envio (entregue ou não) | R$ 1,00 |
| Orçamento de setembro/2026 | R$ 10.000 (≤ 10.000 tentativas) |
| Janela de envio | Todos os dias, das **9h às 21h** (hora de envio de 9 a 20) |
| Máximo por cliente | 1 mensagem por dia |
| Elegibilidade | Do dia em que o cliente entra em cobrança até 60 dias de atraso |
| Templates disponíveis | `friendly_reminder`, `urgent_reminder`, `discount_offer` (quita com 15% de desconto), `pix_link` |
| Receita | Valor efetivamente pago pelo cliente em até 72h após a mensagem |

O sistema de disparo exclui automaticamente clientes que já quitaram a dívida (essas mensagens
não são enviadas nem cobradas).

## Dados

Você recebe dois arquivos (dicionário completo em `data_dictionary.md`):

1. **`whatsapp_collections_history.csv`** — ~75 mil mensagens enviadas de 1/jun a 31/ago/2026 para
   ~11,7 mil clientes. Uma linha por tentativa de envio, com o estado do cliente naquele momento
   (saldo devedor, salário, dia do pagamento, número de transações anteriores, recência de login etc.),
   o status de entrega, a interação do cliente e se houve pagamento em 72h.

2. **`collections_queue_sep2026.csv`** — os ~10,7 mil clientes que estarão em cobrança em setembro:
   os que ainda estão em aberto em 1/set e os que têm vencimento previsto ao longo do mês
   (`in_collections_since` é a data em que passam a ser elegíveis). Para esses clientes você tem os
   mesmos atributos, mas **nenhum resultado**: é a sua base para planejar.

Os dados são sintéticos, mas foram construídos para se comportar como dados reais de cobrança:
há sinais claros, há ruído e há armadilhas. Não existe uma "resposta certa" escondida — queremos ver
como você pensa.

## O que entregar

1. **Análise exploratória e diagnóstico** (notebook): o que explica entrega, interação e pagamento?
   Onde o dinheiro foi desperdiçado nos últimos três meses?
2. **Modelo(s)**: a abordagem que você julgar adequada para estimar o valor esperado de uma mensagem
   (para um cliente, num dia e hora, com um template). Justifique escolhas, validação e métricas.
3. **Plano de setembro** — arquivo `plan.csv` com no máximo 10.000 linhas e as colunas:

   ```
   customer_id,send_date,send_hour,template
   C000002,2026-09-02,19,pix_link
   ```
   (`send_date` em `AAAA-MM-DD`, `send_hour` inteiro de 9 a 20.)
   Nós vamos **executar o seu plano num simulador** que reproduz o comportamento dos clientes e
   comparar receita recuperada e taxa de falha com outros planos.
4. **Apresentação de até 25 minutos** para a gestora (não técnica) e para o time de dados (técnico),
   no formato que você achar mais adequado, com o quanto você espera recuperar, com qual taxa de falha,
   quais são as alavancas e como você provaria em produção que o plano funciona.

## Como avaliamos

- Manipulação de dados e rigor analítico (joins, agregações, leakage, confundimento).
- Qualidade da modelagem e honestidade na validação.
- Raciocínio de otimização sob restrição de orçamento: quem contatar, quando, com o quê, quantas vezes.
- Pensamento de negócio: relação entre custo, falha, interação e receita; fidelidade do cliente
  (o histórico de transações ajuda ou atrapalha?); o que medir depois.
- Clareza de comunicação para públicos diferentes.

Você terá um prazo de 7 dias para montar a apresentação. A apresentação terá 50 minutos
(25 de exposição + 25 de perguntas). Pode usar a linguagem e as bibliotecas que preferir.
Em caso de dúvida sobre as regras, tome uma premissa razoável e declare-a.
