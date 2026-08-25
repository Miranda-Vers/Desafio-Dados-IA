# Uso de IA

Usei o Claude (Anthropic) para me ajudar a estruturar o notebook do Nível 1 —
organização das células, redação dos prompts V1/V2 e da função de validação da
saída da LLM.

Um ponto em que precisei corrigir o rumo: a primeira versão sugerida calculava
o "5x a mediana" dentro do próprio prompt da LLM (pedindo pra ela decidir se
era atípico). Isso vai contra a regra do desafio de que cálculo é pandas e LLM
é interpretação — reescrevi para que a Regra 2 seja 100% determinística em
pandas, e a LLM só recebe o resultado já calculado para interpretar.

Todo o código foi revisado e entendido linha a linha antes de entrar no
notebook.
