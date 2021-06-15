# Problema da Quebra em Palavras

O problema tratado nessa aula é muito relevante para tratar algumas questões
do dia a dia das empresas e é valorizado em entrevistas de codificação e também
_hackatons_ pelo mundo. Mas vamos analisar para entender melhor o que é.

## Entendendo o problema

??? Exercício 1

Faça uma pesquisa em três sites com o texto [[celularmotorola]]. É isso mesmo,
com todas as letras juntas, porque isso tem tudo a ver com o que nosso algoritmo
realiza.

1.  Primeiro, pesquise no [Google](https://www.google.com.br)
2.  Agora, no campo de busca das [Lojas Americanas](https://www.americanas.com.br/)
3.  Por último, no site da [Amazon](https://www.amazon.com.br/)

Quais as diferenças você notou entre as pesquisas?

::: Demonstração

1. Google

![](google-2.png)

2. Americanas

![](americanas-2.png)

3. Amazon

![](amazon-2.png)

O Google e a Amazon retornam sugestões para sua pesquisa, que foi digitada
de forma incorreta, enquanto no site das Lojas Americanas há apenas um retorno
de erro de digitação, sem apresentar nenhuma sugestão ao possível cliente.

???

Ok. Mas qual a relação disso com o nosso algoritmo?

Bom, o nome já é bem intuitivo com relação a esse problema. Porém, vamos supor que você
foi recentemente contratado pela Lojas Americanas e tem como objetivo fazer com que a pesquisa [[celularmotorola]] e outras semelhantes - [[arcondicionado]], [[celulargalaxy]] - não deem uma mensagem de erro pedindo para a pessoa que digitou usar outros termos, mas sim adaptar para que a própria loja interprete o que poderia ser e retorne resultados, assim como ocorre na Amazon.

Para você mostrar suas habilidades como novo desenvolvedor, você pode usar nosso algoritmo,
que resolve o _Problema da Quebra em Palavras_.

É importante ressaltar que este algoritmo é uma **simplificação**: ele não quebra o texto em si, ou seja, não devolve a sugestão do que a pessoa quis escrever, ele apenas analisa se o texto pode ser quebrado ou não. Porém, essa simplificação foi feita por questões didáticas, obter a versão que devolve a quebra de palavras em si exige modificações simples, mantendo a mesma complexidade.

Tá, vamos explicar como funciona com uma linguagem mais de programador.

## Ideia Geral do Algoritmo

A ideia geral da resolução do problema é que dado um _texto de entrada_ e um banco com **palavras** válidas, o algoritmo irá dizer se esse _texto_ pode ser segmentado em uma frase, a qual é resultado de uma junção de **palavras** do dicionário. Caso a entrada possa ser segmentada, o algoritmo retorna **{green}(True)**. Caso contrário, devolve **{red}(False)**

Vamos entender melhor... Supondo que temos um banco de palavras interno:

```py
{
    "liquidificador", "celular", "geladeira",
    "ar", "condicionado", "fone", "ouvido",
    "samsung", "panela", "sam", "sung", "galaxy",
}
```

??? Exercício 2

Considerando os seguintes textos de entrada e considerando o banco de palavras acima, o que, para cada uma delas, o algoritmo retornaria?

-   **[[samsunggalaxy]]**
-   **[[fonedeouvido]]**
-   **[[argeladeira]]**
-   **[[panelar]]**

:::Gabarito

-   [[samsunggalaxy]]: {green}(True), pois no dicionário há as palavras [[samsung]] e [[galaxy]], por exemplo.
-   [[fonedeouvido]]: {red}(False). Nesse caso, temos algo muito interessante... Intuitivamente, nós sabemos que o texto [[fonedeouvido]] é quebrável em 3 palavras: [[fone]], [[de]], [[ouvido]]. Porém, no contexto pede-se que seja considerado o **banco de palavras** que foi dado. Como a palavra [[de]] não está neste banco, tem-se que nesse caso o texto não pode ser quebrado.
-   [[argeladeira]]: {green}(True), pois no dicionário há as palavras [[ar]] e [[geladeira]].
-   [[panelar]]: {red}(False), pois há apenas uma palavra no dicionário: [[panela]] e não possui [[r]].

???

??? Exercício 3

Considerando os textos que retonaram **{green}(True)**, como as frases formadas pela quebra dos textos de entrada ficariam depois de serem corrigidas, ou seja, depois de os espaços serem colocados?

:::Gabarito

-   [[argeladeira]] : ` ar geladeira`

Para o próximo caso, temos uma particularidade que pode acontecer em muitos casos. No nosso
banco de palavras, temos os termos [[sam]], [[sung]], assim como [[samsung]]. O que um código
que retornaria as opções faria é simplesmente mostraria as duas possibilidades, como:

` samsung galaxy` e ` sam sung galaxy`

!!! Lembrete
Essas frases seriam formadas num código mais aprofundado, que consegue retornar as possibilidades de frases. No nosso caso, a ideia é retornar **True** ou **False**, pois estamos lidando com uma simplificação, ou um início de modelagem.
!!!

???

Note que não é apenas repartir os textos, eles devem ser repartidos nos pontos certos, para que ele todo seja segmentado da maneira correta!

Para conseguir repartir a string de entrada nesses locais corretos, o algoritmo vai pegando cada letra da esquerda para a direira e vai formando substrings.
Se o algoritmo checar que a parte esquerda da string está no banco de palavras, resta descobrir se a parte direita que restou também pode ser segmentada em outras palavras presentes no banco.
Ou seja, a partir do momento em que o algoritmo encontra a substring da esquerda, ele deve resolver o mesmo problema, _muito similar ao problema original_, porém só para a parte da direta da string, então um **problema menor**.

??? Exercício 4

Das estratégias de programação que aprendemos na matéria de Desafios da Programação, qual delas podemos utilizar para que se revolva problemas menores que o original?

:::Gabarito

A estratégia a ser usada neste caso é a **recursividade**, pois o intuito dessa ferramenta é justamente resolver versões menores do problema, para assim chegar na resolução do problema original.

???

## Algoritmo Recursivo

Considerando os pontos citados acima, pode ser relevante analisar a lógica de como essa ideia irá funcionar. Para isso, o pseudocódigo pode ser analisado, sendo que o da forma recusiva tem a seguinte estrutura:

```py
funcao word_break(string, dicionario):
    pega o tamanho da string
    faz o passo da recursão

    i = 0
    enquanto eu não analisar a string toda:
        se string[0:i] tá no dicionário:
            se word_break[i:tamanho]: # se o resto da string também pode ser subdivida
                devolve true
        i+=1

    devolve false
```

Para deixar de uma maneira compreensível, vamos usar Python, que é uma implementação real
dessa ideia do pseucódigo:

```py
def word_break_recursive(string, dictionary):
    n = len(string)
    if n == 0:
        return True
    for i in range(1,n + 1):
        if dictionary.__contains__(string[0: i]) and word_break_recursive(string[i: n], dictionary):
            return True

    return False
```

!!! Dica
Se quiser rodar esse código e fazer um teste, use [esse site](https://replit.com/languages/python3), dá para compilar o arquivo online!
!!!

O que o código está fazendo é percorrer um **_for_** com o tamanho da string e ir analisando passo a passo se a substring analisada está contida no dicionário de palavras que foi passado, que seria o nosso _banco de palavras_.

Ou seja, se temos uma string [[motorola]], o código irá analisar:

1. [[m]] **and** recursão de [[otorola]]
2. [[mo]] **and** recursão de [[torola]]
3. [[mot]] **and** recursão de [[orola]]

e assim sucessivamente...

## Modificando o Algoritmo Recursivo

Agora que já entendemos como a versão recursiva desse algoritmo funciona, vamos fazer uma modificação para que possamos devolver mais a ideia deste algoritmo.

Na versão recursiva apresentada, todas as vezes que chamamos a função, passamos um string diferente para ela, que é uma substring gerada a partir do argumento que foi passado anteriormente.

??? Exercício 5

Existe alguma maneira de fazer com que em todas as chamadas seja passada a mesma string, porém em cada chamada analisar uma parte diferente dessa string? Se sim, escreva como ficaria os parâmetros dessa função.

!!! Dica
Pense: a ideia é analisar as mesmas substrings, igual antes, porém queremos sempre passar a mesma string original. De que forma podemos pegar as substrings dessa maior?
!!!

:::Gabarito
Sim, é possível sempre passar a mesma string e cada vez analisar uma parte diferente, basta que passemos a partir de qual posição e até qual posição da string se que analisar naquela chamada.
Portanto, ficaria:

```py
def word_break_recursive(string, start, end, dictionary)
```

???

Sabendo que agora as coisas serão feitas da forma apresentada acima, vamos desenvolver...

??? Exercício 6

Na versão anterior, tem-se que a primeira coisa a se fazer é obter _n_, o tamanho da string de entrada. Visto que agora a string é sempre a mesma, como esse valor pode ser obtido?

:::Gabarito
Esse valor pode ser obtido da seguindo forma:

```py
n = len(string[start:end+1])
```

???

??? Exercício 7

E o _for_, como ficaria depois da modificação para adaptá-lo a esta nova forma?

!!! Dica
Esse exercício é só a linha do for, não o seu conteúdo interno.
!!!

:::Gabarito
O _for_ ficaria da seguinte forma:

```py
for i in range(start, end+1)
```

???

É importante ressaltar que em ambos os exercícios existem o `+1` ao relacionar com o `end` porque, no Python, ao fazer um slice, o código pega os itens ignorando o último.
Por exemplo: se eu dou um slice da string `motorola` para pegar do índice 0 ao 4, o que ele me retorna é `moto`, ignorando a letra que está no índice 4, o `r`.

??? Exercício 8

Dentro do _for_... como seria agora a chamada recursiva?

:::Gabarito
A chamada recursiva agora é:

```py
word_break_recursive(string, i, n, dictionary)
```

???

Juntando tudo... a função usando os novos parâmetros é o seguinte código:

```py
def word_break_recursive(string, start, end, dictionary):
    n = len(string[start:end+1])
    if n == 0:
        return True
    for i in range(start, end+1):
        if dictionary.__contains__(string[start: i]) and word_break_recursive(string, i, n, dictionary):
            return True
    return False
```

Observe que a lógica é muito parecida com a anterior, apenas obedecendo a condição proposta nesse tópico, ou seja, passando para as chamadas das funções sempre o mesmo texto de entrada. Assim funciona igual a anterior, quebrando e analisando o texto de entrada da mesma forma.

Por exemplo: se temos o texto de entrada [[abcde]], será analisado:

1. [[a]] **and** recursão de [[bcde]], ou seja, essa recursão vai quebrar esse [[bcde]] em textos menores para verificar se está dentro do banco, como em [[cde]], [[de]], [[e]].
2. [[ab]] **and** recursão de [[cde]]
3. [[abc]] **and** recursão de [[de]]

e assim sucessivamente novamente...

Porém essa solução tem um problema que é possível verificar nesse segundo exemplo com o [[cde]].

Esta versão do algoritmo tem subproblemas sobrepostos e subestrutura ótima. Ou seja, analisa-se a mesma substring mais de um vez e soluciona problemas a partir da resolução de subproblemas, tendo assim uma complexidade horrível. No exemplo acima, é possível ver que o [[cde]] é analisado tanto no **passo 1**, quando faz parte da recursão da _string da direita_ quanto no passo 2, ao ser a própria _string da direita_.

Para ilustrar melhor, observe a seguinte árvore que mostra as substrings analisadas no algoritmo:

![](recursion.png)

Como é possível analisar, as mesmas strings são analisadas várias vezes e isso faz com que o código vá ficando lento e acaba não sendo tão eficiente quanto poderia ser, ao evitar essas repetições.

??? Exercício 9

Já vimos em outro momento da disciplina que, quando temos um problema com essa situação da recursividade, já que utilizá-la pode demorar muito por não ser tão eficiente, podíamos usar uma estratégia melhor como alternativa. Qual era essa estratégia?

!!! Dica
Lembre-se de uma das nossas APS!
!!!

:::Gabarito
Usamos isso na APS 3, de DNA. A solução é utilizar a **programação dinâmica**, pois com ela se evita de repetir o serviço que já foi feito, ou seja, a substring que já foi analisada não precisa ser analisada novamente, é só utilizar o valor que está armazenada numa matriz que construímos.
???

## Algoritmo de Programação Dinâmica

A ideia de usar a programação dinâmica é memoizar a solução de cada subproblema para que não precisemos resolvê-lo várias vezes e possamos usar os resultados pré-calculados para resolver o problema.

??? Exercício 10

Sabendo do que foi exposto acima, quais seriam os passos para implementar a versão de programação dinâmica para este algoritmo?

!!! Dica
Os passos são gerais e bem parecidos com os da APS 3.
Não precisa fazer código nesse exercício! Escreva apenas o passo a passo.
!!!

:::Gabarito

Para a implementação é necessário realizar os seguintes passos:

1. Criar um Array 2D de tamanho nxn onde n é o tamanho da string;
2. Inicializar os elementos do array com 0. Cada elemento no array contém um valor booleano se substring s[i: j] está presente no dicionário ou não;
3. Antes de chamar a recursiva verificar se a substring já foi analisada antes, verificando pela posição no array;
4. Caso não estiver no array ainda, faz a recursiva para analisar essa substring e preenche o espaço no array;
5. Se já esta no array, utiliza-se a resposta que já existe e não calcula de novo, diminuindo o trabalho do algoritmo.

???

Pensando nesses passos, podemos desenvolver um pseudocódigo para a versão de programação dinâmica do problema de quebra de palavras.

Vamos contruir juntos!

??? Exercício 11

Os passos 1 e 2 da atividade acima são passos da memoização, agora estamos mais interessados na função que coloca em prática a programação dinâmica em si. Para começar função, a primeira coisa a se fazer é implementar dentro da função o passo 3. Portanto, desenvolva um pseudocódigo com a ideia desse passo.

:::Gabarito

```py
se a substring de s está no dicionario:
    matriz[começo][final] = 1
    devolve true
```

???

??? Exercício 12

Agora, desenvolva em texto ou em pseudocódico, caso você já esteja confortável com a ideia, de como aplicar os passos 4 e 5 para o algoritmo de quebra de palavras.

:::Gabarito

Deve-se percorrer a string de entrada pedacinho por pedacinho dela e ir checando se esses pedacinhos já foram analisados também. Se já tiverem sido analisados retorna o que se tem de resposta, caso contrário deve-se realiazar a analise chamando a recursiva para assim preencher o espaço correspondente na matriz.

???

Juntando tudo o que foi visto, temos que o pseudocódigo para o algoritmo de quebra de palavras utilizando-se da programação dinâmica fica da seguinte forma:

```py
function wordBreak(s, começo, final, dicionario):
    se a substring de s está no dicionario:
        matriz[começo][final] = 1
        devolve true

    agora, vamos percorrer a s, do começo ao final (i = começo e soma 1 até o final):
        se matriz[i][começo] já tiver sido analisado:
            devolvo o valor analisado
        caso contrário:
            matriz[i][começo] = wordBreak(s, começo, i, dicionario) #chamo a recursiva
            se essa recursiva devolver true:
                analiso o resto da string (recursiva de novo)
```

??? Exercício 13

Qual é a complexidade da versão de Programação Dinâmica do algoritmo que resolve o problema de quebra de palavras?

:::Gabarito

A complexidade é $O(n^2)$.

???

---

## Desafio

??? Exercício 14

Apesar de nosso algoritmo ter uma ótima vantagem ao se ter um banco de palavras bem grande,
pela forma que ele resolve, ele não é tão bom quanto poderia ser, como uma inteligência artificial. Ele não deixa de ser bastante útil mas poderia ser mais desenvolvido
em alguns sentidos. O que você acha que falta para ser melhor?

!!!Dica
Pense se alguém digitar [[samsungalaxy]]. O que o algoritmo irá retornar? Por que isso acontece?
!!!

::: Gabarito

O ponto que o algoritmo poderia melhorar, apesar de ele já ser bastante útil, é integrar uma inteligência, ou até o seu passo a passo ser um pouco diferente de forma a poder formar mais substrings, que possam fazer sentido.

Por exemplo, [[samsungalaxy]] iria retornar {red}(False), porém [[samsunggalaxy]] iria retornar {green}(True). Isso ocorre porque sua lógica é feita de forma que, ao identificar
uma substring que já foi analisada, por exemplo [[samsung]], agora ele vai analisar apenas o que sobrou, nesse caso [[alaxy]]. Assim, se fosse uma aplicação nas lojas americanas por exemplo, ainda poderia dar um erro, sendo que uma inteligência poderia muito bem identificar que era ` samsung galaxy`

???
