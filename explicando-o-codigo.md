# Explicando o código do Simulador

O mapea a cidade, com seus locais e estradas, como um Grafo, permitindo, assim, transformar o problema de deslocamento do robô em um problema de "Caixeiro Viajante", ou seja, de busca pela melhor rota para cumprir com o objetivo de entregar e coletar encomendas presentes nas localidades. 

As estradas são mapeadas como **arestas** do grafo e as localidades são os **nós** ou **vértices** do grafo.

A aresta contém, neste caso, as informações relacionadas a quais **nós** está ligada, por isso, um par de informações é fornecido, "_local1-local2_", para definir a aresta. Já a localidade é definida por seu nome, por exemplo, "Alice's House" e por coordenadas do centro do nó, por exemplo, "_{x: 50, y: 100}_".

A estrutura `roads` (ver código a seguir) é um vetor que permite criar as arestas do grafo. Já a estrutura `location` é um **objeto literal Javscript**, formado pela chave (_key_) "Nome da Localidade" e o valor (_value_)"Coordenadas da localidade", sendo este último o objeto literal contendo as coordenadas do **centro do _nó_** .


## Entrada de Dados do Sistema

Os **dados de entrada** de nosso sistema são as estuturas que definem os nós e as arestas do grafo e que permitirão a criação do grafo, ou seja, a representação em memória das informações que devem se usadas no processamento da resposta do sistema.

As respostas para o problema tratado no sistema são as **rotas otimizadas para entrega e recebimento** das encomendas.


```javascript

//Estruturas básicas para construção do grafo: 

const roads = [
    "Alice's House-Bob's House", "Alice's House-Cabin",
    "Alice's House-Post Office", "Bob's House-Town Hall",
    "Daria's House-Ernie's House", "Daria's House-Town Hall",
    "Ernie's House-Grete's House", "Grete's House-Farm",
    "Grete's House-Shop", "Marketplace-Farm",
    "Marketplace-Post Office", "Marketplace-Shop",
    "Marketplace-Town Hall", "Shop-Town Hall"
];

const locations = {
    "Alice's House": {x: 50, y: 100},
    "Bob's House":   {x: 200, y: 50},
    "Cabin":         {x: 50, y: 300},
    "Post Office":   {x: 200, y: 200},
    "Town Hall":     {x: 350, y: 100},
    "Daria's House": {x: 500, y: 100},
    "Ernie's House": {x: 650, y: 250},
    "Grete's House": {x: 550, y: 400},
    "Farm":          {x: 350, y: 450},
    "Shop":          {x: 350, y: 300},
    "Marketplace":   {x: 200, y: 350}
};

```

### Construindo o grafo usando a função `buildGraphP(edges)`

A construção do grafo é feita pela função `buildGraphP(edges)` (ver código a seguir), que recebe os pontos as arestas (`edges`) do grafo. 


```javascript
function buildGraph(edges) {
    let graph = Object.create(null);
    function addEdge(from, to) {
    // Adiciona uma conexão bidirecional ao grafo
    if (graph[from] == null) {
        graph[from] = [to];
        } else {
        graph[from].push(to);
        }
    }

    for (let [from, to] of edges.map(r => r.split("-"))) {
        addEdge(from, to);
        addEdge(to, from); // Torna o grafo bidirecional
    }
    return graph;
}
```

As funções mais importantes, neste código, são as `map()` e `split()`, pois é através dela que o programa transforma o _array_  de _strings_ `roads`, em um grafo. 

No nosso caso, usamos o tipo `Object`, do Javascript, para criação de nosso grafo. O objeto **graph** criado possui como _key_ o nome da **localidade de origem (`from`)** e um vetor para as **localidades de destino (`to`)**.

A iteração de _arrays_ usando o `for ... of ...` é uma das vantagens do Javscript. Ele permite "percorrer" todo o `array`, colocando cada elemento deste em uma variável. Usamos isso, em nosso código, no trecho:

```javascript
for (let [from, to] of ...)
```

Para entendermos melhor este trecho, podemos observar uma iteração mais simples, para um vetor chamado `a`, que tem cada um de seus elementos mostrados no **Console** do Javscript. Vejamos o código para fazer isso:

```javascript
let a = ["a","b","c","d","e"];
for(let item of a){
    console.log(`Valor: ${item} \n`);
}

//Saída do Código:
// 
//Valor: a
//Valor: b
//Valor: c
//Valor: d
//Valor: e

```

Veja que a saída é, exatamente, o que se queria, ou seja, o valor de cada elemento do vetor impresso no **Console**.

Porém, se tivermos um vetor de _strings_ do tipo `["a-b","c-d","e"]`, e quiséssemos separar os elementos unidos por "_-_", o que deveríamos fazer? Neste caso, podemos fazer a separação dos elementos usando a função `split()`passando como parêmetro o separador "_-_", que estamos usando. 

Como essa função devolve um _array_ de _strings_, podemos separar os elementos do _array_, usando uma operação chamada **Desconstrução de _Array_ (_Array Desconstructing_)**. que permite separar os elementos de um _array_ em uma ou mais variáveis. Por exemplo:

```javascript
let a,b;
[a,b] = [10,20];
```
No código acima, os valores de `a` e `b` serão, respectivamente, `10` e `20`. 

Aplicando esta desconstrução em nosso _array_ `a`, temos:

```javascript
let a = ["a-b","c-d","e"];
for(let [item1,item2] of a.split("-")){
    console.log(`Valor: ${item1} e o próximo é o ${item2} \n`);
}

//Saída: 
// Valor: a e o próximo é o b
// Valor: c e o próximo é o d
// Valor: e e o próximo é o undefined

```

Como o último elemento "e", não possui um par separado por "-", a variável `item2` recebe o valor `undefined`.

Agora, voltemos ao nosso grafo. Iremos usar a função `split("-")`,  divide a _string_ em substrings, usando o separador fornecido como parâmetro par a função, no caso, o **"-"**. Assim, por exemplo, a _string_ "Alice's House-Bob's House", vai ser dividida em duas _strings_, "Alice's House" e "Bob's House". O valor de saída da função `split()` é, portanto, um _array_ de _strings_ `["Alice's House", "Bob's House"]`. 

Já a função `map()` vai receber os valores de entrada da função e aplicar uma transformação, uma regra, para que seja criado um novo _array_ a partir desta regra. No nosso caso, a regra é dada pela função anônima `r => r.split("-")`, que pode ser expressa da seguinte forma:

```javascript
function (r){
    return r.split("-")
}
```

A saída da função `edges.map(r => r.split("-")))`, seria um _array_ e _arrays_, como mostrado a seguir.

```javascript
[
  ["Alice's House", "Bob's House"], // Índice 0
  ["Alice's House", "Cabin"],       // Índice 1
  ["Alice's House", "Post Office"], // Índice 2
  // ... e assim por diante
]
```

Assim, a desconstrução `[from, to] = ["",""]` poderia acontecer e cada elemento do _array_ formado pelos pares de nomes seria decomposto em duas variáveis, no caso, `from` e `to`, que são as ligações de um **nó** do grafo, para outro **nó**, do mesmo grafo.

E para que serve a função `addEdge(from,to)`?

### Criando a lista de adjacência dos **nós**

A função `addEdge(from,to)` serve para que cada elemento do grafo chamado `graph` possua a lista de seus **vizinhos**, seus **nós adjacentes**. Chamamos esta lista de **lista de adjacência** do **nós**. Por exemplo,

```javascript
addEdge(from,to); //Onde from = "Alice's House e to = "Bob's House", cria:

//graph["Alice's House"] = "Bob's House"
// Ou ainda,
// graph = {
//     "Bob's House": : ["Alice's House"]
// }
```

Como o grafo é bidirecional, além de colocarmos que `graph["Alice's House"] = "Bob's House"`, colocamos que `graph["Bob's House"] = "Alice's House"`. Desta forma, podemos acessar todos os caminhos que partem de "Alice's House" ou de "Bob's House", simplesmente acessando `graph["Alice's House"]` ou `graph["Bob's House"]`, pois cada entrada (_key_) do objeto guarda a lista de todos os seus vizinhos (_adjacências_). Vejamos como fica a lista de elementos desse objeto `graph`, ao final das iterações:

```javascript
{
  "Alice's House": ["Bob's House", "Cabin", "Post Office"],
  "Bob's House": ["Alice's House", "Town Hall"],
  "Cabin": ["Alice's House"],
  "Post Office": ["Alice's House", "Marketplace"],
  // ...
}
```


> **E para que serve o teste `if (graph[from] == null) {...} else {...}`?**
> Para que seja criado, na primeira vez, uma entrada para o objeto `graph` que seja do tipo `chave : []`. Por exemplo, `{"Alice's House": ["Bob's House"]}`
>
> Já o `else`, tratará as situações onde o elemento do objeto já foi inserido e ele precisa ter o valor do _array_ atualizado. Por este motivo, dentro do `else`, usamos a função `push()`, que nos permite inserir um elemento no vetor/lista de adjacências.
>
> **Por que isso é importante?**
> Se você tentasse fazer direto `graph[from].push(to)` sem o `if`, na primeira vez que aparecesse um local novo, o JavaScript daria um erro fatal. Ele tentaria fazer um "push" em algo que não existe (`undefined`), quebrando o seu programa.

**E agora, o que faremos?**
Vamos ver como funciona a segunda parte do código ("Parte 2").


## Entendendo a Parte 2 de nosso código

### O Conceito: Uma "Fotografia" do Mundo (State)

A classe `VillageState` não é o robô nem o mapa. Ela é uma **fotografia** (um _print_) de como as coisas estão em um dado momento. Vejamos o **construtor** (inicializador) dessa classe:

```javascript
constructor(place, parcels) {
    this.place = place;    // Onde o robô está (ex: "Post Office")
    this.parcels = parcels; // Lista de pacotes que ainda precisam ser entregues
}
```
Onde `place` é apenas uma _string_ dizendo o local atual do robô e  `parcels`é  um _array_ de objetos. Cada objeto tem `{place, address}` (onde o pacote está agora e para onde ele deve ir).

### O Método `move(destination)` - O Cérebro da Imutabilidade

Esta é a função mais importante e complexa dessa parte. Ela define a regra do jogo: "O que acontece quando o robô tenta ir para `destination`?".

#### Passo A: Verificação de Segurança

```javascript
if (!roadGraph[this.place].includes(destination)) {
    return this; 
}
```

  * Ele olha no grafo (`roadGraph`) se existe uma estrada direta do lugar atual (`this.place`) para o destino.
  * Se **não** existir estrada, o robô ignora o comando e retorna `this` (o estado antigo, nada muda).

#### Passo B: A Magia da Imutabilidade (Criando o Novo Estado)

Se o movimento for válido, o código **não altera** o estado atual. Ele cria um **novo**.

```javascript
let parcels = this.parcels.map(p => {
    if (p.place != this.place) return p;
    return {place: destination, address: p.address};
}).filter(p => p.place != p.address);
```

Aqui ocorrem duas operações cruciais com as encomendas (`parcels`):

1.  **Transporte (`.map`)**:

      * O código percorre todas as encomendas.
      * `if (p.place != this.place)`: Se a encomenda **não** está onde o robô está, ela fica onde está (o robô não a "pegou").
      * `return {place: destination...}`: Se a encomenda está no mesmo local do robô, ela "viaja" com ele. O novo local dela passa a ser o `destination` do robô.

2.  **Entrega (`.filter`)**:

      * Imediatamente após o movimento, o `.filter` verifica: "O local atual da encomenda é igual ao endereço de entrega?".
      * Se `p.place == p.address`, significa que ela foi entregue. O `filter` a remove da lista.
      * Só sobram na lista as encomendas que ainda **não** chegaram ao destino.

#### Passo C: Retorno

```javascript
return new VillageState(destination, parcels);
```

Ele retorna uma **nova instância** de `VillageState` com:

    1. O robô no novo lugar (`destination`);
    2. A lista de encomendas atualizada (movidas e filtradas).

> **Por que isso é importante?** 
> Isso diz respeito ao conceito de **Imutabilidade**. Ao retornar um `new VillageState` em vez de alterar o antigo, você preserva o histórico. Se você quisesse criar um botão "Desfazer" (Undo), seria muito fácil, pois o estado anterior ainda existe na memória intacto.

-----

### O Método Estático `random()`

Este método é apenas um auxiliar para começar o jogo. Ele cria um estado do zero.

```javascript
VillageState.random = function(parcelCount = 5) {
    // Gera estado inicial com número especificado de encomendas
    let parcels = [];
    for (let i = 0; i < parcelCount; i++) {
        let address = randomPick(Object.keys(roadGraph)); // Destino aleatório
        let place;
        do {
            place = randomPick(Object.keys(roadGraph)); // Origem aleatória
        } while (place == address); // Garante que origem ≠ destino
        parcels.push({place, address});
    }
    return new VillageState("Post Office", parcels); // Robô começa no correio
};
```

**O que esse código faz?**
Cria um estado inicial aleatório para iniciar a simulação. Vejamos, a seguir,o que o código faz, passo a passo. 

1. Ele cria um _array_ vazio de encomendas;
2. Faz um loop `parcelCount` vezes (padrão 5);
3. O loop `do...while`:
      * Escolhe um endereço de entrega (`address`);
      * Escolhe um local de origem (`place`);
      * Continua escolhendo a origem **enquanto** (`while`) ela for igual ao destino. Isso **evita** criar uma encomenda que já nasce entregue (ex: Levar da "Alice's House" para a "Alice's House");
4. Retorna um `VillageState` inicial com o robô sempre no "Post Office".

## Entendendo a **Parte 3**

Se a **Parte 1** era o Mapa e a **Parte 2** era o Estado (memória), a **Parte 3** é a **Tomada de Decisão**. É aqui que definimos a **Inteligência Artificial (IA)** dos robôs. Ela responde à pergunta: *"Dado onde estou e onde estão os pacotes, para onde devo ir agora?"*

Aqui temos uma evolução de complexidade, do robô mais "ignorante" para o mais "inteligente". Vamos analisar uma por uma das estratégias, nas próximas seções deste texto.



### O Robô Aleatório (`randomRobot`)

Este é o nível zero de inteligência.

```javascript
function randomRobot(state) {
    return {direction: randomPick(roadGraph[state.place])};
}
```

* **Lógica:** Ele olha para as conexões do local atual (`roadGraph[state.place]`) e escolhe uma aleatoriamente.
* **Comportamento:** É como uma "caminhada aleatória" (_random walk_). Ele eventualmente vai entregar as encomendas por pura sorte, mas é extremamente ineficiente. Não usa sua "**memória**" para saber se já veio de um determinado local, por exemplo.

### O Robô de Rota Fixa (`routeRobot(state,memory)`)

Este robô é um pouco melhor, mas não pensa. Ele segue um roteiro predefinido.

```javascript
const mailRoute = [
    "Alice's House", "Cabin", "Alice's House", "Bob's House",
    "Town Hall", "Daria's House", "Ernie's House",
    "Grete's House", "Shop", "Grete's House", "Farm",
    "Marketplace", "Post Office"
];
function routeRobot(state, memory) {
    if (memory.length == 0) {
        memory = mailRoute; // Recarrega a rota se acabou
    }
    return {direction: memory[0], memory: memory.slice(1)};
}
```

* **Conceito de Memória:** Perceba que agora usamos o segundo argumento `memory`. O robô precisa lembrar em que ponto do roteiro ele está.
* **Lógica:**
  1. Se a memória está vazia, ele carrega a rota completa (`mailRoute`);
  2. Ele pega o primeiro passo da memória (`memory[0]`) como direção;
  3. Ele retorna o restante da lista (`memory.slice(1)`) como a nova memória para o próximo turno;
  * **Analogia:** É como um ônibus de linha. Ele faz sempre o mesmo caminho, tenha passageiros ou não. É eficiente se houver muitas encomendas espalhadas uniformemente, mas ruim para encomendas específicas.


### A Ferramenta de Busca (`findRoute`)

Antes de explicar o robô inteligente, precisamos entender a ferramenta que ele usa. **Esta função é uma implementação clássica do algoritmo de Breadth-First Search (BFS) ou Busca em Largura.**

Como você já se interessou por algoritmos de _pathfinding_ antes, vai gostar de notar os detalhes aqui:

```javascript
function findRoute(graph, from, to) {
    let work = [{at: from, route: []}]; // A "Fila" (Queue)
    for (let i = 0; i < work.length; i++) {
        let {at, route} = work[i]; // Pega o próximo item da fila
        for (let place of graph[at]) { // Explora vizinhos
            if (place == to) return route.concat(place); // Achou!
            if (!work.some(w => w.at == place)) { // Evita ciclos/repetição
                work.push({at: place, route: route.concat(place)});
            }
        }
    }
}
```

* **O Objetivo:** Encontrar o caminho *mais curto* entre `from` e `to`.
* **Como funciona (BFS):**
  1. Começa onde o robô está;
  2. Olha todos os vizinhos imediatos (distância 1);
  3. Se não encontrou o destino, adiciona os vizinhos desses vizinhos na lista `work` (distância 2);
  4. O `!work.some(...)` é crucial: ele impede que o robô fique andando em círculos ou verifique o mesmo lugar duas vezes;
* **Resultado:** Retorna um array de strings (ex: `["Cabin", "Alice's House"]`) que é o caminho a seguir.


### O Robô Inteligente (`goalOrientedRobot`)

Este robô usa a função `findRoute` para tomar decisões racionais.

```javascript
function goalOrientedRobot({place, parcels}, route) {
    if (route.length == 0) { // Se não tenho plano, preciso criar um
        let parcel = parcels[0]; // Foca na primeira encomenda da lista
        
        if (parcel.place != place) {
            // Cenário A: Preciso buscar a encomenda
            route = findRoute(roadGraph, place, parcel.place);
        } else {
            // Cenário B: Já estou com ela, preciso entregar
            route = findRoute(roadGraph, place, parcel.address);
        }
    }
    // Executa o primeiro passo do plano
    return {direction: route[0], memory: route.slice(1)};
}
```

* **Comportamento:**
   1. Ele olha para a primeira encomenda da lista;
   2. Pergunta: "Eu já peguei essa encomenda?" (Ou seja, ela está no meu `place`?);
   3. **Se NÃO:** Ele calcula a rota mais curta até a *origem* da encomenda (para pegá-la);
   4. **Se SIM:** Ele calcula a rota mais curta até o *destino* da encomenda (para entregá-la);
   5. Ele armazena essa rota na `memory` e a segue passo a passo até terminar. Quando a rota acaba (`route.length == 0`), ele repete o processo para a próxima encomenda.

## Entendendo a **Parte 4**

A **Parte 4** é o **Controlador** (_Controller_) e a **Visão** (_View_) da aplicação. Se as partes anteriores eram as regras do tabuleiro e a inteligência dos jogadores, esta parte é o "Juiz" que organiza a partida e narra o que está acontecendo para a plateia.

Ela conecta a lógica matemática (Grafos/Estado) com a interface visual (HTML/Canvas). Vamos dividir em 3 pilares principais ("Game Loop", "O Gerenciamento de Estado Global" e "A Conexão com o Usuário (DOM)"), que serão discutidos a seguir.


### O "Game Loop" (O Motor da Simulação)

Em jogos e simulações, existe um ciclo infinito que roda repetidamente: **Atualizar Dados -\> Desenhar na Tela -\> Esperar -\> Repetir**. Aqui, essa função é a `runTurn()`.

Esta é a função mais crítica desta parte. Vamos ver o fluxo dela:

```javascript
function runTurn() {
    // 1. Condição de Parada (Vitória)
    if (currentState.parcels.length == 0) {
        // ... para a animação e avisa que acabou
        return; 
    }

    // 2. Cérebro: O robô decide o movimento
    let action = currentRobot(currentState, robotMemory);
    
    // 3. Física: O estado do mundo muda (Imutabilidade em ação)
    // Note que 'currentState' é substituído pelo 'nextState'
    let nextState = currentState.move(action.direction);
    
    // 4. Log: Atualiza texto na tela
    // ... lógica de logs ...

    // 5. Persistência: Atualiza as variáveis globais para o próximo turno
    currentState = nextState;
    robotMemory = action.memory;

    // 6. Renderização: Desenha o novo estado no Canvas
    drawVillage(currentState);

    // 7. O Loop Temporal: Agenda a próxima execução
    simulationTimeout = setTimeout(runTurn, animationSpeed);
}
```

**Por que `setTimeout` e não um `while` loop?**
Se você usasse um loop `while` ou `for` infinito, o navegador travaria. O JavaScript precisa de pausas para conseguir redesenhar a tela (renderizar o HTML/Canvas). O `setTimeout` diz: "Execute o próximo passo daqui a 800ms", dando tempo para o usuário ver a animação acontecer.

### O Gerenciamento de Estado Global

Diferente da classe `VillageState` (que é imutável e efêmera), aqui temos variáveis que **mudam** e persistem durante toda a vida da aplicação no navegador:

```javascript
let currentState = null;     // A "foto" atual do mundo
let currentRobot = null;     // Qual algoritmo está rodando (Random, Route ou Goal)
let robotMemory = [];        // A memória persistente do robô
let simulationTimeout = null; // O ID do timer (para poder pausar)
```

Essas variáveis funcionam como a "memória RAM" da simulação enquanto ela está rodando na página web.

### A Conexão com o Usuário (DOM)

Esta parte traduz os cliques do usuário em configurações de código.

**A função `startSimulation()`:**
Ela faz o papel de "Configuração Inicial". Descrevendo melhor: 

1. Lê o `<select>` do HTML para ver qual robô o usuário quer;
2. Define a função `currentRobot` correta (troca o cérebro do robô dinamicamente);
3. Chama `runTurn()` pela primeira vez para dar o "pontapé inicial".

**A função `stopSimulation()`:**

```javascript
clearTimeout(simulationTimeout);
```

Isso é como puxar o "freio de mão". Ela cancela o agendamento do próximo `runTurn`, congelando a simulação exatamente onde está.

**A função `logAction()`:**
Como você tem interesse em desenvolvimento web, note que ele usa `document.createElement` e `prepend`. Isso cria uma lista de logs onde as mensagens mais novas empurram as antigas para baixo, criando um histórico de ações legível.

## Entendendo a **Parte 5**

Esta parte é onde a mágica visual acontece. É aqui que traduzimos dados abstratos (strings e arrays) em pixels coloridos na tela.  A API nativa do Canvas (`ctx`) é um pouco mais "básica" que o `p5.js` (que facilita muito as coisas), mas o princípio é idêntico.

O conceito principal aqui é o **Algoritmo do Pintor (Painter's Algorithm)**: desenhamos em camadas, de trás para frente. O que é desenhado por último fica por cima de tudo.

Vamos decompor as camadas dessa pintura, nas seções a seguir.

### O Elo Perdido: O Objeto `locations`

Antes de desenhar, o código assume que existe uma variável (que não estava no _snippet_ anterior, mas é essencial) chamada `locations`.

* **O Grafo (`roadGraph`)**: Diz *quem* conecta com *quem* (Lógica).
* **As Locações (`locations`)**: Diz *onde* (X, Y) cada lugar fica na tela (Geometria).

Seria algo assim:

```javascript
const locations = {
  "Alice's House": {x: 50, y: 100},
  "Bob's House":   {x: 200, y: 300},
  // ...
};
```

### Limpeza (`ctx.clearRect`)

```javascript
ctx.clearRect(0, 0, canvas.width, canvas.height);
```

No início de cada frame da animação, precisamos apagar o desenho anterior. Se não fizéssemos isso, o robô deixaria um "rastro" contínuo na tela, pintando tudo de verde até cobrir o mapa. É como apagar o quadro negro antes de começar uma nova aula.



### Camada 1: As Estradas (Fundo)

```javascript
ctx.strokeStyle = "#aaa"; // Cor cinza
ctx.lineWidth = 4;        // Espessura
for (let from in roadGraph) {
    for (let to of roadGraph[from]) {
        // ... desenha linha de 'from' até 'to'
    }
}
```

Aqui o código percorre o grafo. Para cada conexão, ele desenha uma linha.

* **`ctx.beginPath()`**: "Vou começar um novo desenho;
* **`ctx.moveTo(x1, y1)`**: Levanta a caneta e coloca no ponto de origem;
* **`ctx.lineTo(x2, y2)`**: Risca uma linha até o destino;
* **`ctx.stroke()`**: Efetivamente aplica a tinta na linha definida.

**Nota:** Isso desenha as linhas *atrás* de tudo, para que as casas e o robô fiquem *em cima* das ruas, e não riscados por elas.

### Camada 2: Os Locais (Nós do Grafo)

```javascript
for (let loc in locations) {
    // ... desenha círculo azul
    ctx.arc(x, y, 15, 0, Math.PI * 2); 
    ctx.fill();
    // ... desenha texto
}
```

Aqui desenhamos os "nós".

* **`ctx.arc(...)`**: É o comando nativo para círculos. Diferente do `circle()` do _p5.js_, aqui você define o ângulo inicial (0) e o final (`2 * PI`, ou seja, 360 graus);
* **`ctx.fillText(...)`**: Escreve o nome do lugar logo abaixo do círculo.

### Camada 3: As Encomendas (Objetos Dinâmicos)

Esta parte tem um detalhe de UI/UX interessante para visualização de dados.

```javascript
state.parcels.forEach((p, index) => {
    // ...
    const offset = index * 12; 
    ctx.fillRect(x + 10 + offset, y + 5, 15, 15);
    // ...
});
```

Como várias encomendas podem estar no mesmo lugar (ou "empilhadas" no robô), se desenhássemos todas na mesma coordenada **X/Y**, veríamos apenas um quadrado vermelho.

**O `offset`**: O código usa o índice do array (`index * 12`) para deslocar cada pacote levemente para a direita. Isso cria aquele efeito visual de "pilha" ou fila de pacotes, permitindo que você conte visualmente quantos pacotes estão ali.

### Camada 4: O Robô (O Protagonista)

```javascript
const robotLoc = locations[state.place];
// ... desenha círculo verde
// ... desenha a letra "R"
```

Finalmente, desenhamos o robô por cima de tudo. Ele precisa ser a última coisa desenhada para garantir que ele nunca seja escondido por uma estrada ou por uma casa.
