# Aula 10 — Testes da CLARA multiagente

Grupo: Bruno Ramos, João Luiz, Marcos Vinicius, Vitor Rodrigo
Data dos testes: 20/10/2026
Ambiente: n8n 2.27.4 em Docker · Groq `openai/gpt-oss-120b` (CLARA) e `openai/gpt-oss-20b` (sub-agentes) · OpenAI `text-embedding-3-small` (embeddings, no lugar do Ollama — Quadro 2 da apostila, autorizado pelo professor)

Pré-condição: base `convenios` carregada pelo formulário `/form/base-convenios`, que respondeu
"3 arquivo(s) indexado(s) em 3 trecho(s)." Cada cenário rodou numa sessão nova do chat
(o 4 e o 5 continuam a sessão do 3), com intervalo de 25 s entre mensagens por causa do limite de tokens por minuto do Groq.
O sub-agente acionado foi conferido no registro de cada execução (ferramentas chamadas pela CLARA).

## Passo 3 — Cenários (seção 6.2)

| # | Mensagem(ns) enviada(s) | Resposta obtida | Sub-agente acionado | Esperado | OK? |
|---|---|---|---|---|---|
| 1 | "oi" | "Olá! Eu sou a CLARA, assistente virtual do Núcleo de Convênios. Como posso ajudar você hoje?" | nenhum | Nenhum; apresenta-se como CLARA | Sim |
| 2 | "Qual o horário de atendimento e o e-mail de vocês?" | "Nosso horário de atendimento presencial e por telefone é de segunda a sexta, das 8 h às 17 h. Para contato por e-mail, utilize: atendimento@nucleo-convenios.example." | INFO | INFO; seg. a sex., 8h às 17h, e o e-mail do Núcleo | Sim |
| 3 | "Quero saber sobre um convênio." | "Claro! Para poder consultar os detalhes do convênio, preciso que você me informe o número dele no formato NNN/AAAA (por exemplo, 047/2026)." | nenhum | Nenhum; pede o número no formato NNN/AAAA | Sim |
| 4 | (sessão do 3) "É o 047/2026. Qual o objeto e o valor?" | "O convênio 047/2026 tem como objeto a 'Pesquisa Aplicada em Tecnologias Sustentáveis' e o valor global acordado é de R$ 250.000,00." | CONVENIOS | CONVENIOS; "Pesquisa Aplicada em Tecnologias Sustentáveis" e R$ 250.000,00 | Sim |
| 5 | (mesma sessão) "Quem assina pela fundação?" | "Pela Fundação, o convênio 047/2026 é assinado por Fernanda Lopes Azevedo." | CONVENIOS | CONVENIOS ou nenhum; Fernanda Lopes Azevedo, sem pedir o número de novo | Sim |
| 6 | "E o convênio 099/2026, qual o valor?" | "Não encontrei nenhum registro para o convênio 099/2026 na nossa base de dados. [...]" — não citou outros convênios | CONVENIOS | CONVENIOS; não há registro, sem citar outros convênios | Sim |
| 7 | "Preciso falar com uma pessoa: meu pedido de aditivo do convênio 021/2026 está parado há um mês." → "Sou Carlos Mendes, carlos.mendes@exemplo.example" | 1ª: pediu só nome completo e e-mail. 2ª: "Chamado registrado com sucesso! Protocolo: CH-20261020-203827. A equipe [...] retornará no próximo dia útil." | nenhum → ATENDIMENTO | ATENDIMENTO; protocolo CH-…, nova linha na tabela; pede só nome e e-mail | Sim |
| 8 | "Meu nome é Maria." → "Você lembra meu nome?" | "Olá, Maria! [...]" → "Sim, Maria, lembro do seu nome. Em que posso ajudar você hoje?" | nenhum | Nenhum; responde Maria, pela memória | Sim |

Protocolo gravado no cenário 7: CH-20261020-203827 — conferido na Data table `chamados`: sim (protocolo, nome "Carlos Mendes", e-mail e descrição "pedido de aditivo do convênio 021/2026 está parado há um mês.", tirada da primeira mensagem).

## Passo 4 — Ajustes

Nenhum prompt precisou de ajuste: os prompts da apostila passaram nos 8 cenários.

Houve uma falha na primeira rodada, corrigida no workflow (não no prompt):

| # do cenário | Nó alterado | Configuração anterior | Configuração nova | Resultado do novo teste |
|---|---|---|---|---|
| 4 e 5 | Inserir na base (ingestão) e Base de convenios (CLARA) — Simple Vector Store | Versão 1.1 do nó, em que a Memory Key `convenios` é prefixada com o ID do workflow: a ingestão gravava em `<id-ingestão>__convenios` e a CLARA lia `<id-clara>__convenios`, vazia. Resultado: "Não encontrei nenhum registro para o convênio 047/2026" | Versão 1.3 do nó, em que a Memory Key `convenios` é compartilhada entre os workflows | Cenário 4: objeto e R$ 250.000,00 corretos; cenário 5: Fernanda Lopes Azevedo. Os demais cenários repetiram o resultado da 1ª rodada |
