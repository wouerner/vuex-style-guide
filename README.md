# vuex-style-guide
## Objetivos
Tenho por interesse criar um guia com remendações gerais de como criar uma estrutura com Vuex, mais corente, menos complicada e mais intuitiva.

## Comunicação Backend (Ajax)
A ferramenta recomendada é o [axios](https://github.com/axios/axios).

## Pastas
Devemos colocar todas as estruturas do Vuex dentro de uma pasta unica pasta (store) dentro do sistema. 
  - Isso é motivado pela caracteristica centralizadora da arquitetura de controle de estado do Vuex.
Usar namespace e modulos parece ser a melhor alternativa para dividir os dados. [doc](https://vuex.vuejs.org/guide/modules.html)
Recomendamos os uso da configuração (namespaced: true) dentro dos modulos para facilitar a localização das "actions"
Apesar de ser possivel criar "states" em escopo global com Vuex e modulos, optamos por deixar tudo dentro de modulos, se existir alguma necessidade/problemas criamos o "modulo global" que poderia simular o escopo global.

```bash
src
 ├── App.vue
 ├── components
 ├── main.js
 ├── router.js
 ├── store # <-- Todas as estruturas do Vuex (Actions, Mutations, Types, Getters)
 │   ├── eventos  # <-- Modulos
 │   │   ├── actions.js
 │   │   ├── getters.js
 │   │   ├── index.js
 │   │   ├── mutations.js
 │   │   └── types.js
 │   ├── global   # <-- Modulos Global, contendo versão do produto, variaveis de cortes, etc
 │   │   ├── actions.js
 │   │   ├── getters.js
 │   │   ├── index.js
 │   │   ├── mutations.js
 │   │   └── types.js
 │   └── usuario   # <-- Modulos
 │       ├── actions.js
 │       ├── getters.js
 │       ├── index.js
 │       ├── mutations.js
 │       └── types.js
 └── store.js
```

## Constantes globais.
Não usar o Vuex para constantes, exemplo: PI = 3.14, APP_NAME = 'Vuex Style Guide', preferir abaordar essas constantes com outra estrategia. 

## Actions 
### Nomes
- sync[*]Action : Ação para sincronizar os dados com entidade externa ao SPA (Bancos de dados, APIs, etc), removendo todos os dados anteriores.
- set[*]Action: Ação para os dados com entidade externa ao SPA (Bancos de dados, APIs, etc)
- remove[*]Action: Remove parte ou todo o objeto/array do "state"
- update[*]Action: Atualiza parte do state correpondente, precisa manter dados anteriores

exemplos :

- Ruim : 
```javascript
import * as types from './types'                                                                                                                               
                                                                                                                                                                   
export const dadosMenu = ({ commit }) => {                                                                                                                             
  commit(types.SET_REGISTROS_TABELA, {})                                                                                                                           
}                                                                                                                                                                     
                                                                                                                                                                       
export const usuario = ({ commit }, params) => {                                                                                                                     
  commit(types.SET_USUARIO, params)                                                                                                                                
}
```

- Bom : 
```javascript
import * as types from './types'                                                                                                                               
                                                                                                                                                                   
export const synUsuarioAction = ({ commit }, params) => {                                                                                                                             
  commit(types.SET_USUARIO, {})
  // ... ajax call
  commit(types.SET_USUARIO, { //..DADOS })
}                                                                                                                                                                     
                                                                                                                                                                       
export const setUsuarioAction = ({ commit }, params) => {                                                                                                                     
  // ... ajax call
  commit(types.SET_USUARIO, { //..DADOS })                                                                                                                                
}

export const updateUsuarioAction = ({ commit }, params) => {                                                                                                                     
  // ... ajax call
  commit(types.UPDATE_USUARIO, { //..DADOS })                                                                                                                                
}

export const removeUsuarioAction = ({ commit }, params) => {                                                                                                                     
  // ... ajax call
  commit(types.REMOVE_USUARIO, params)                                                                                                                                
}

```

Uma action não é obrigada a fazer um "commit", mas vamos convercionar que sim, essa e unica forma de registrar dentro do plugin Vue no navergador.
Actions devem sempre chamar outras actions com "dispatch", preferivel ao retornar uma promise e deixar a cargo do componente, isso quebra o isolamente da action, e deixa ela quase sem possibildiade de ser reaproveitada

## Mutations
### Types
- SET_[*]: Usado para sinalizar uma alteração dentro do State.
- UPDATE_[*]: Usado para atualizar um pedaço do object/array dentro do state
  - Atenção para esse tipo de "mutation" devemos usar "Vue.set()"
- REMOVE_[*]: Usado para remove elementos do "state"
  - Atenção para esse tipo de "mutation" devemos usar "Vue.delete()"
  
 ```javascript
export const SET_USUARIO = 'SET_USUARIO'     
export const UPDATE_USUARIO = 'UPDATE_USUARIO'     
export const REMOVE_USUARIO = 'REMOVE_USUARIO' 
```

## Commit
- Sempre tratar erros nos ajax para não deixar o "state" incosistente, com dados desatualizados, então lembre dos retornos dos axios (.then, .catch)

## Atomico
- Sempre que possivel deixe a camada de "actions" menos complexa, cuidado apenas da parte assincrona, deixa a maior para das transformações para as "mutations".
- Tenha em mente que o "state" deveria ser compartilhado para todos os componentes, nomes pouco genericos, e muito logica atrapalham.
