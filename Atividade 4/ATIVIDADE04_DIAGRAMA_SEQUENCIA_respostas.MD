# Atividade 4: Diagramas de sequência

**Projeto de referência:** VazaApp  
**Caso de uso:** UC-021: Favoritar sugestão

## Exercício 16

O caso de uso explica o que a pessoa precisa realizar e quais resultados o sistema deve oferecer. A sequência detalha a colaboração entre objetos para executar um cenário desse atendimento.

Um desenho de mensagens não substitui o texto, que também registra condições, regras e caminhos que podem não aparecer naquele cenário.

## Exercício 17

| Camada | Participante e função |
|---|---|
| Ator | Cliente informa origem, destino e valor. |
| Interface | FormularioTransferencia apresenta campos e resultado. |
| Aplicação | EfetuarTransferencia organiza a operação. |
| Domínio | Conta verifica saldo e débito permitido; Transferencia representa a movimentação validada. |
| Persistência | MovimentoRepository recupera contas e efetiva débito, crédito e registro na mesma transação. |

Assim, o formulário não decide se o cliente pode movimentar dinheiro. A aplicação consulta os objetos responsáveis e só confirma depois da gravação completa.

## Exercício 18

A sequência acompanha os oito passos do enunciado, desde a solicitação até o aviso final. O controller recebe as ações da tela; o service organiza a consulta e o registro; o repository acessa os dados.

![Diagrama do exercício 18](Diagramas%20Sequencias/exercicio18.svg)

## Exercício 19

A disponibilidade depende do período inteiro solicitado. Pela RN05, qualquer sobreposição impede a reserva. O `alt` permite mostrar o resultado dessa decisão.

Uma consulta inicial pode ficar desatualizada. Por isso, a gravação condicionada à ausência de conflito precisa ser indivisível: entre duas confirmações concorrentes, somente uma pode ocupar o período.

![Diagrama do exercício 19](Diagramas%20Sequencias/exercicio19.svg)

## Exercício 20

Cada passagem pelo `loop` trata um produto: consulta o estoque e, quando disponível, acrescenta o item e calcula seu subtotal. Um produto indisponível é informado e não entra no pedido.

Esse fragmento expressa a repetição para qualquer quantidade de produtos. Copiar mensagens manualmente deixaria o desenho preso a uma quantidade específica.

![Diagrama do exercício 20](Diagramas%20Sequencias/exercicio20.svg)

## Exercício 21

A tela acumula decisões comerciais e acesso a recursos externos. Uma mudança no desconto, por exemplo, acabaria alterando a apresentação.

| Participante | Trabalho atribuído |
|---|---|
| TelaPedido | Capturar a solicitação e apresentar a resposta. |
| PedidoController | Encaminhar os dados para a aplicação. |
| FinalizarPedido | Conduzir a operação e tratar seus resultados. |
| ClienteCadastro | Avaliar se o cliente pode comprar. |
| Pedido | Conferir os itens contra os saldos, aplicar desconto e calcular o total. |
| UnidadeTrabalho | Consultar dados e gravar pedido e baixa de estoque numa transação. |
| NotificadorEmail | Entregar a confirmação depois do commit. |

Falha ou disputa pelo estoque desfaz a gravação. Se apenas o email falhar, a compra continua confirmada e a notificação fica pendente de reenvio.

![Diagrama do exercício 21](Diagramas%20Sequencias/exercicio21.svg)

## Exercício 22

Eu procuraria a justificativa do bloqueio e confirmaria seu significado com quem definiu o problema.

| Hipótese | Decisão após a verificação |
|---|---|
| Mensagem inserida por engano | Excluir do diagrama. |
| Necessidade omitida | Completar requisito e fluxo do caso de uso. |
| Política comercial não registrada | Escrever a regra, suas condições e sua ligação com o requisito. |
| Controle técnico de concorrência | Explicar a finalidade e usar um nome como adquirirTravaTemporaria. |

Suspender o acesso de alguém muda o comportamento do negócio e exige respaldo na análise. Uma trava temporária pode ser uma solução interna, desde que não invente essa suspensão.

## Exercício 23

### Regras e requisitos

| Regra de negócio | Requisito funcional relacionado |
|---|---|
| RN01: agendar apenas em vaga livre. | RF01: consultar vagas por especialidade, profissional e data; RF02: efetuar agendamento. |
| RN02: o profissional não pode ter duas consultas ativas no mesmo horário. | RF02: recusar uma confirmação conflitante. |
| RN03: cancelar com antecedência mínima de 24 horas. | RF03: verificar o prazo e cancelar. |
| RN04: manter o registro de consultas e cancelamentos. | RF04: registrar as operações sem apagar o histórico. |

| Requisito não funcional proposto | Verificação sugerida |
|---|---|
| RNF01: consistência | Duas confirmações simultâneas da mesma vaga produzem no máximo uma consulta, sem gravação parcial. |
| RNF02: controle de acesso | Um paciente não consegue cancelar a consulta de outro. |
| RNF03: desempenho | Disponibilidade respondida em até 3 segundos em 95% das consultas, com 30 usuários simultâneos e 5.000 registros. |

São critérios propostos para aprovação, ainda sem testes de implementação.

### Atores e casos de uso

O paciente é o ator identificado. Os casos de uso são Consultar vagas, Agendar consulta e Cancelar consulta. O profissional pertence à agenda; não há interação dele descrita no problema.

### Agendar consulta

| Campo | Especificação |
|---|---|
| Objetivo e ator | O paciente obtém uma consulta confirmada. |
| Pré-condições | Paciente identificado e agenda cadastrada para o profissional e a especialidade. |
| Pós-condição de sucesso | Consulta e operação registradas; vaga ocupada. |
| Garantia em falha | Nenhuma confirmação ou registro parcial. |

| Passo | Fluxo principal |
|---|---|
| P1 | Paciente abre o agendamento. |
| P2 | Sistema pede especialidade, profissional e data. |
| P3 | Paciente informa os filtros. |
| P4 | Sistema apresenta as vagas. |
| P5 | Paciente seleciona o horário e confirma. |
| P6 | Sistema verifica a vaga e registra consulta e histórico com exclusividade. |
| P7 | Sistema informa a confirmação. |

| Fluxo | Tratamento e destino |
|---|---|
| A1, P4: nenhuma vaga | Alterar filtros e retornar a P3 ou sair. |
| A2, P6: horário ocupado | Atualizar a lista e retornar a P4, sem consulta criada. |
| A3, antes de P6: desistência | Encerrar sem registro. |
| E1, P3 ou P5: entrada inválida | Explicar o erro e repetir o passo correspondente. |
| E2, P6: erro de persistência | Reverter a operação e encerrar sem confirmação. |

### Rastreabilidade e revisão

| Origem | Ligação com os casos de uso |
|---|---|
| RN01, RF01, RNF03 | Consultar vagas e apresentar opções no agendamento. |
| RN01, RN02, RF02, RNF01 | Agendar consulta, especialmente P6 e A2. |
| RN03, RF03 | Cancelar consulta, aceitando antecedência maior ou igual a 24 horas. |
| RN04, RF04, RNF01 | Agendar e cancelar, preservando o histórico. |
| RNF02 | Cancelar consulta, conferindo seu titular. |

| Item conferido | Ajuste incorporado |
|---|---|
| Condições e resultado | Declarados antes do fluxo. |
| Desvios | Cada alternativa e exceção tem origem e destino. |
| Disputa pela vaga | Restrição garantida durante a transação, não apenas na consulta. |
| Cancelamento e histórico | Exatamente 24 horas é permitido; cancelar não apaga a consulta. |

A especificação apresentada já inclui essas correções. Considerei cada horário uma vaga; duração e expediente ainda precisam ser definidos.

### Participantes

| Participante | Responsabilidade |
|---|---|
| Paciente | Escolher filtros e confirmar a vaga. |
| TelaConsulta | Apresentar opções e mensagens. |
| AgendarConsulta | Coordenar o atendimento e traduzir seus resultados. |
| Agenda | Avaliar disponibilidade e preparar a reserva. |
| AgendaRepository | Recuperar a agenda e registrar consulta e histórico com exclusividade e transação. |

O `alt` distingue disponibilidade e indisponibilidade. A segunda verificação protege contra mudanças ocorridas depois da consulta.

![Diagrama do exercício 23](Diagramas%20Sequencias/exercicio23.svg)

## Exercício 24

Usei o UC-021: Favoritar sugestão. A análise completa, o caso revisado e o diagrama estão no [exercício 24](exercicio24.md).

## Questão final

Desenhar a sequência também ajuda a avaliar a solução. No exercício 19, a separação entre consulta e gravação revelou a disputa pela sala, exigindo proteção na confirmação. No exercício 21, a ordem das mensagens mostrou que um erro no email não deve desfazer uma compra já registrada.
