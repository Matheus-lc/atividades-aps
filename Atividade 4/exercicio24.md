# Exercício 24

**Projeto de referência:** VazaApp  
**Caso de uso:** UC-021: Favoritar sugestão

## Regras e requisitos

Escolhi favoritar uma sugestão porque essa função permite reencontrar um texto útil. Mesmo sendo uma ação curta, ela exige cuidado com acesso e repetição.

| Origem | Aplicação no caso |
|---|---|
| RN-003 | Somente o proprietário acessa e altera seus favoritos. |
| RN-014 | Cada usuário pode favoritar uma sugestão própria uma única vez. |
| RF-021 | Guardar uma sugestão própria na coleção de favoritos. |
| RNF-001 e RNF-002 | Validar acesso no servidor e isolar os dados de cada usuário. |
| RNF-003 | Apresentar confirmação ou falha em português, com clareza. |
| RNF-006 e RNF-007 | Gravar com integridade e registrar falhas técnicas sem dados pessoais. |

Assim, RN-003 e RN-014 orientam RF-021, realizado por UC-021; os RNFs definem a qualidade dessa execução.

## Caso de uso corrigido

O objetivo é guardar uma sugestão para consulta posterior. O ator é o Usuário, que inicia a ação ao marcar uma sugestão. A entrada é o identificador da sugestão. As pré-condições são conta ativa, sessão válida e sugestão própria existente; o servidor confere essas condições antes de autorizar a gravação.

| Passo | Fluxo principal |
|---|---|
| P1 | O usuário solicita favoritar uma sugestão. |
| P2 | O sistema valida a sessão, a existência e a propriedade da sugestão. |
| P3 | O sistema registra atomicamente um único vínculo entre usuário e sugestão. |
| P4 | Após a gravação, apresenta a sugestão como favorita. |

| Caminho | Origem e resultado |
|---|---|
| A1 | Em P3, o vínculo já existe, inclusive por solicitações simultâneas. Mantém o registro existente e segue para P4. |
| E1 | Em P2, sessão inválida, sugestão inexistente ou de outro usuário. Nega a operação e encerra sem revelar detalhes. |
| E2 | Em P3, a persistência falha. Preserva o estado anterior, informa a falha e encerra sem confirmar uma nova marcação. |

No sucesso existe exatamente um vínculo. Na falha, os favoritos anteriores são preservados. A sugestão e o histórico permanecem inalterados em todos os caminhos.

## Checklist e revisão

| Conferência | Ajuste incorporado |
|---|---|
| Acesso e objetivo | P2 impede a gravação sem autorização. |
| Repetição e concorrência | P3 cria somente se o vínculo não existir, com unicidade no armazenamento. |
| Alternativas e falhas | A1 retorna a P4; E1 e E2 encerram com resultado definido. |
| Confirmação e integridade | P4 só confirma gravação concluída ou vínculo já existente. |

Esses cuidados estão incorporados ao caso acima. A revisão é documental; os testes ainda não foram executados em uma aplicação.

## Diagrama de sequência

![Sequência do UC-021](Diagramas%20Sequencias/UC-021.svg)

## Responsabilidades

| Participante ou fragmento | Justificativa |
|---|---|
| Usuário | Solicita a marcação e recebe o resultado de P1 e P4. |
| TelaFavoritos | Encaminha a ação e mostra confirmação ou erro, conforme RNF-003. |
| FavoritoService | Coordena P2 a P4 e verifica a sessão antes da consulta. |
| PoliticaFavorito | Aplica a propriedade de RN-003 sem expor dados à interface. |
| FavoritoRepository | Consulta a propriedade mínima e cria o vínculo sem duplicação, conforme RN-014 e RNF-006. Em falha, registra diagnóstico protegido por RNF-007. |
| `break` e `alt` | Encerram E1 e distinguem E2, A1 e o sucesso novo. |

O armazenamento usa uma chave única por usuário e sugestão. Essa decisão concretiza RN-014; conferir a existência e depois inserir sem proteção permitiria duplicatas.

[Especificação do projeto](https://github.com/Matheus-lc/vazaapp-aps/blob/main/README.md#uc-021)
