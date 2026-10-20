# Aula 10 — Testes da CLARA multiagente

Grupo: _(nomes)_
Data dos testes: _(dd/mm/aaaa)_
Ambiente: n8n _(versão)_ · Groq `openai/gpt-oss-120b` (CLARA) e `openai/gpt-oss-20b` (sub-agentes) · Ollama `nomic-embed-text`

Pré-condição: base `convenios` carregada pelo formulário `/form/base-convenios`, com a mensagem
"3 arquivo(s) indexado(s) em 3 trecho(s)". Cada cenário roda numa sessão nova do chat
(exceto 4 e 5, que continuam a sessão do 3).

## Passo 3 — Cenários (seção 6.2)

| # | Mensagem(ns) enviada(s) | Resposta obtida | Sub-agente no painel Logs | Esperado | OK? |
|---|---|---|---|---|---|
| 1 | "oi" | | | Nenhum; apresenta-se como CLARA | |
| 2 | "Qual o horário de atendimento e o e-mail de vocês?" | | | INFO; seg. a sex., 8h às 17h, e atendimento@nucleo-convenios.example | |
| 3 | "Quero saber sobre um convênio." | | | Nenhum; pede o número no formato NNN/AAAA | |
| 4 | (sessão do 3) "É o 047/2026. Qual o objeto e o valor?" | | | CONVENIOS; "Pesquisa Aplicada em Tecnologias Sustentáveis" e R$ 250.000,00 | |
| 5 | (mesma sessão) "Quem assina pela fundação?" | | | CONVENIOS ou nenhum; Fernanda Lopes Azevedo, sem pedir o número de novo | |
| 6 | "E o convênio 099/2026, qual o valor?" | | | CONVENIOS; não há registro desse número, sem citar outros convênios | |
| 7 | "Preciso falar com uma pessoa: meu pedido de aditivo do convênio 021/2026 está parado há um mês." → nome e e-mail | | | ATENDIMENTO; protocolo CH-…, nova linha na tabela `chamados`; pede só nome e e-mail | |
| 8 | "Meu nome é Maria." → "Você lembra meu nome?" | | | Nenhum; responde Maria, pela memória | |

Protocolo gravado no cenário 7: _(CH-AAAAMMDD-HHMMSS)_ — conferido na Data table `chamados`: _(sim/não)_

## Passo 4 — Ajustes de prompt

Preencher só se algum cenário falhou. Se nenhum falhou, escrever "Nenhum ajuste necessário".

| # do cenário | Nó / prompt alterado | Instrução anterior | Instrução nova | Resultado do novo teste |
|---|---|---|---|---|
| | | | | |
