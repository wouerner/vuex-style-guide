# vuex-style-guide
## Objetivos
Tenho por interesse criar um guia com remendações gerais de como criar uma estrutura com Vuex, mais corente, menos complicada e mais intuitiva.

## Comunicação Backend (Ajax)
A ferramenta recomendada é o [axios](https://github.com/axios/axios).

## Pastas
Recomendamos colocar todas as estruturas do Vuex dentro de uma pasta unica pasta com o nome "store" dentro da pasta "src". 
  - Isso é motivado pela caracteristica centralizadora da arquitetura de controle de estado do Vuex.

**Boa estrutura:**
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

## Modulos
Recomendamos usar modulos no Vuex (https://vuex.vuejs.org/guide/modules.html)
Não Recomendamos do "state" global dentro do Vuex, para cumprir essa função se necessario usamos o modulo "global" para conter todas as informações no "state", que não tenham um modulo especifico.  
Recomendamos usar ```namespaced: true ``` essa configuração facilita a localização e entedimento de onde estão as "actions".  

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
Não Recomendamos usar o Vuex para constantes, exemplo: PI = 3.14, APP_NAME = 'Vuex Style Guide', preferir abaordar essas constantes com outra estrategia. 

## Actions 
### Nomes
- sync[*]Action : Ação para sincronizar os dados com entidade externa ao SPA (Bancos de dados, APIs, etc), removendo todos os dados anteriores.
- set[*]Action: Ação para os dados com entidade externa ao SPA (Bancos de dados, APIs, etc)
- remove[*]Action: Remove parte ou todo o objeto/array do "state"
- update[*]Action: Atualiza parte do state correpondente, precisa manter dados anteriores

exemplos :

- Ruim : 
```javascript
# src/store/usuario/actions.js
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
# src/store/usuario/actions.js
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

Recomendamos que toda "action" tenha um commit, pois é nesse momento que podemos rastrear as alterações feitas no "state".  

Recomendamos não usar ```return new Promise() ``` dentro das "actions", eveitando assim compartilhar/delegar para outros componentes o tratamento do sucesso e erros do AJAX, usar dispatch seria a opção.

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
