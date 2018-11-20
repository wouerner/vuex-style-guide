# vuex-style-guide
## Ajax
A ferramenta recomendada e axios, mellhor até momento.

## Pastas
Devemos colocar todas a estrutura do Vuex () dentro de uma pasta unica dentro do sistema. Essa arquitetura nos induz a concentrar todo os dados, centralizados. 
Usar namespace e modulos parece ser a melhor alternativa para dividir os dados.

## Constantes globais.
Não usar o Vuex para constantes, exemplo: PI = 3.14, APP_NAME = 'Vuex Style Guide', preferir abaordar essas constantes com outra estrategia. 

## Actions 
### Nomes
- sync[*]Action : Ação para sincronizar os dados com entidade externa ao SPA (Bancos de dados, APIs, etc), removendo todos os dados anteriores.
- set[*]Action: Ação para os dados com entidade externa ao SPA (Bancos de dados, APIs, etc)
- remove[*]Action: Remove parte ou todo o objeto/array do "state"
- update[*]Action: Atualiza parte do state correpondente, precisa manter dados anteriores

Uma action não é obrigada a fazer um "commit", mas vamos convercionar que sim, essa e unica forma de registrar dentro do plugin Vue no navergador.
Actions devem sempre chamar outras actions com "dispatch", preferivel ao retornar uma promise e deixar a cargo do componente, isso quebra o isolamente da action, e deixa ela quase sem possibildiade de ser reaproveitada

## Mutations
### Types
- SET_[*]: Usado para sinalizar uma alteração dentro do State.
- UPDATE_[*]: Usado para atualizar um pedaço do object/array dentro do state
- REMOVE_[*]: Usado para remove elementos do "state"

## Commit
- Sempre tratar erros nos ajax para não deixar o "state" incosistente, com dados desatualizados, então lembre dos retornos dos axios (.then, .catch)

## Atomico
- Sempre que possivel deixe a camada de "actions" menos complexa, cuidado apenas da parte assincrona, deixa a maior para das transformações para as "mutations".
- Tenha em mente que o "state" deveria ser compartilhado para todos os componentes, nomes pouco genericos, e muito logica atrapalham.
