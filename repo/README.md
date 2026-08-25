# Desafio Técnico — Prevenção à Lavagem de Dinheiro (PLD)

## O que é
Pipeline que combina regras determinísticas (pandas) com uma LLM para triagem de
operações financeiras suspeitas de um banco fictício.

## Como rodar
```bash
pip install -r requirements.txt
cp .env.example .env   # preencha LLM_API_KEY se for rodar a chamada real à LLM
jupyter notebook nivel_1/nivel_1.ipynb
```
O notebook já está commitado com as saídas executadas — não é necessário rodar de
novo para avaliar. A célula de LLM roda em modo `mock=True` por padrão (não exige
chave nem rede); para gerar um parecer com uma LLM real, defina `LLM_API_KEY` e
troque para `mock=False` na chamada de `chamar_llm`.

## O que foi concluído
- **Nível 1 — completo**: limpeza de dados com justificativa, normalização para
  BRL, agregações, Regra 1 (fracionamento), Regra 2 (valor atípico), validação das
  regras com caso positivo e negativo, e análise com LLM com saída estruturada e
  validada, incluindo comparação entre dois prompts.
- **Nível 2 e 3 — não implementados** por restrição de tempo. O plano de como eu
  atacaria cada parte está em `docs/DECISOES.md`.

## Estrutura
Segue a estrutura pedida no enunciado (`dados/`, `nivel_1/`, `nivel_2/`, `nivel_3/`,
`outputs/`, `docs/`).
