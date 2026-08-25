# Decisões

## Trade-offs no Nível 1

- **Remover a duplicata exata em vez de "ignorar"**: optei por `drop_duplicates`
  logo no início do pipeline, e não um filtro específico dentro da Regra 1.
  Assim qualquer análise posterior (agregações, LLM) também já parte de dados
  limpos, em vez de cada regra ter que se defender de duplicatas por conta própria.
- **Data ausente vira `NaT`, não uma data inventada**: qualquer imputação (ex.:
  usar a data de outra operação do mesmo cliente) poderia criar ou esconder um
  alerta de fracionamento artificialmente. Prefiro manter a operação fora da
  Regra 1 e deixar isso registrado, a arriscar viés silencioso.
- **Regra 2 só para clientes com ≥4 operações**: com poucas operações a mediana
  fica instável (ex.: com 2 operações, "5x a mediana" quase nunca dispara ou
  dispara para qualquer coisa). Segui exatamente o critério do enunciado, mas
  vale registrar que esse corte é uma limitação conhecida — clientes novos com
  poucas operações passam sem checagem de valor atípico.
- **LLM em modo mock por padrão**: sem acesso à internet no ambiente em que
  montei este repositório, não dava para validar uma chamada real de ponta a
  ponta. Implementei a função `chamar_llm()` com a mesma interface que uma
  chamada real teria (prompt in, texto + tokens + tempo out), e um branch real
  comentado usando uma API compatível com OpenAI (Groq). Isso deixa claro para
  quem revisa exatamente onde a chamada real entraria, sem fingir uma execução
  que não aconteceu.

## Limitações

- Os prompts e o parecer não foram validados contra um provedor real de LLM —
  a comparação V1 vs V2 no notebook é baseada em como cada prompt tende a se
  comportar (formato aberto vs. formato forçado), não em uma execução A/B real.
- As regras são propositalmente simples (o próprio enunciado avisa que vão gerar
  falso positivo/negativo); não tratei casos como múltiplas contrapartes
  diferentes no mesmo dia, nem operações fracionadas ao longo de vários dias
  (fracionamento "lento").
- Não há testes automatizados além da célula de validação de regras.

## Nível 2 — o que eu faria

Com mais tempo, eu:
1. Extrairia a limpeza e as duas regras do Nível 1 para um módulo `regras.py`
   compartilhado entre os notebooks/scripts dos dois níveis, em vez de
   duplicar código.
2. Implementaria `nivel_2/tools.py` com as três funções pedidas
   (`historico_cliente`, `operacoes_do_dia`, `perfil_canal`), cada uma
   recebendo o DataFrame já tratado e devolvendo um dicionário serializável.
3. Em `nivel_2/agente.py`, usaria a SDK nativa do provedor (function calling)
   com um roteador simples: o agente só chama `operacoes_do_dia` quando o
   resumo agregado indicar concentração em uma data específica, e só chama
   `perfil_canal` quando houver mistura de canais atípica — para não virar
   "chamar tudo sempre".
4. Rodaria em lote sobre os 10 clientes mais sinalizados, salvando cada parecer
   em `outputs/lote/<cliente_id>.json`, com custo e latência por chamada.
5. Em `nivel_2/confronto.py`, definiria como critério de correspondência:
   cliente sinalizado pelas duas regras → esperado `nivel_risco = alto`;
   sinalizado por uma regra → esperado `medio`; nenhuma regra → esperado
   `baixo`. Reportaria a taxa de concordância e, principalmente, listaria os
   casos onde o agente discordou, com a justificativa dele ao lado — o
   objetivo não é bater 100% com a regra, é entender quando o agente está
   certo em discordar.
6. Validaria o resultado checando manualmente uma amostra dos casos de
   divergência (não só a taxa agregada), porque é onde mora o valor real dessa
   comparação.

## Nível 3

Não escolhi trilha por falta de tempo. Se fosse escolher, iria de **Trilha B
(servidor MCP)** por ser a que mais se conecta com o que já foi construído no
Nível 2 (as três ferramentas já existem, só faltaria expô-las via stdio em vez
de import direto).
