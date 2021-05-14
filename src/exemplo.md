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
foi recentemente contratado pela Lojas Americanas e tem como objetivo fazer com que a pesquisa [[celularmotorola]] e outras semelhantes - [[liquidificadorarno]], [[livroharrypotter]] - não deem uma mensagem de erro pedindo para a pessoa que digitou usar outros termos, mas sim adaptar para que a própria loja interprete o que poderia ser e retorne resultados, assim como ocorre na Amazon.

Para você mostrar suas habilidades como novo desenvolvedor, você pode usar nosso algoritmo,
que resolve o _Problema da Quebra em Palavras_.

Tá, vamos explicar como funciona como uma linguagem mais de programador.

## Ideia Geral do Algoritmo

A ideia deste algoritmo é que dado o resultado de uma digitação - como uma palavra (sendo válida ou não) - e um banco com palavras, o algoritmo irá dizer se essa entrada pode ser segmentada em uma frase, resultado de junção de palavras do dicionário, espaçadas entre si.​ Caso a entrada possa ser segmentada o algoritmo retorna **{green}(True)**, caso não possa retorna **{red}(False)**.

Vamos entender melhor... Supondo que temos um banco de palavras interno:

```py
{
    "liquidificador", "celular", "arno", "motorola",
    "livro", "geladeira", "ar", "condicionado", "harry",
    "potter", "fone", "ouvido", "xiaomi", "samsung", "sony",
    "playstation", "monitor", "smartphone", "galaxy",
    "moto", "panela", "copo", "talher", "vidro", "vermelho",
    "sam", "sung"
}
```

??? Exercício 2

Considerando as seguintes strings de entrada e considerando o banco de palavras acima, o que, para cada uma dela, o algoritmo retornaria?

-   **[[samsunggalaxy]]**
-   **[[fonedeouvido]]**
-   **[[argeladeira]]**
-   **[[panelar]]**

:::Gabarito

-   [[samsunggalaxy]]: {green}(True)
-   [[fonedeouvido]]: {red}(False)
-   [[argeladeira]]: {green}(True)
-   [[panelar]]: {red}(False)

???

??? Exercício 3

Considerando as strings que retonaram **{green}(True)**, como elas ficariam depois de serem corrigidas, ou seja, depois de os espaços serem colocados?

:::Gabarito

-   [[argeladeira]] : ` ar geladeira`

Para o próximo caso, temos uma particularidade que pode acontecer em muitos casos. No nosso
banco de palavras, temos os termos [[sam]], [[sung]], assim como [[samsung]]. O que um código
que retornaria as opções faria é simplesmente mostraria as duas possibilidades, como:

` samsung galaxy` e ` sam sung galaxy`

???

Note que não é apenas repartir as palavras, elas devem ser repartidas nos pontos certos, para que toda ela seja segmentada da maneira correta!

Para conseguir repartir a string de entrada nesse locais corretos, o algoritmo vai pegando cada letra da esquerda para a direira e vai formando substrings.
Se o algoritmo checar que a parte esquerda da string está no banco de palavras, resta descobrir se a parte direita que restou também pode ser segmentada em outras palavras presentes no banco de palavras.
Ou seja, a partir do momento em que o algoritmo encontra a palavra da esquerda, ele deve resolver o mesmo problema, muito similar ao problema original, porém só para a parte da direta da string, então um problema menor.

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

Para deixar de uma maneira compreensível, vamos usar python, que é uma implementação real
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

O que o código está fazendo é percorrer um for com o tamanho da string e ir analisando passo a passo se a substring analisada está contida no dicionário de palavras que foi passado, que seria o nosso _banco de palavras_.

Ou seja, se tiver uma string [[motorola]], o código irá analisar:

1. [[m]] **and** recursão de [[otorola]]
2. [[mo]] **and** recursão de [[torola]]
3. [[mot]] **and** recursão de [[orola]]

e assim sucessivamente...

Porém essa solução tem um problema...

Esta versão do algoritmo tem subproblemas sobrepostos e subestrutura ótima. Ou seja, analisa-se a mesma substring mais de um vez e soluciona problemas a partir da resolução de subproblemas, tendo assim uma complexidade horrível.

Para ilustrar melhor, observe a seguinte árvore que mostra as substrings analisadas no algoritmo:

![](recursion.png)

??? Exercício 5

Já vimos em outro momento da disciplina que, quando temos um problema com essa situação da recursividade, já que utilizá-la pode demorar muito por não ser tão eficiente, podíamos usar uma estratégia melhor como alternativa. Qual era essa estratégia?

!!! Dica
Lembre-se de uma das nossas APS!
!!!

:::Gabarito
Usamos isso na APS 3, de DNA. A solução é utilizar a **programação dimâmica**, pois com ela se evita de repetir o serviço que já foi feito, ou seja, a substring que já foi analisada não precisa ser analisada novamente, é só utilizar o valor que está armazenada numa matriz que construímos.
???

## Algoritmo de Programação Dinâmica

A ideia de usar a porgramação dinâmica é memoizar a solução de cada subproblema para que não precisemos resolvê-lo várias vezes e possamos usar os resultados pré-calculados para resolver o problema.

??? Exercício 6

Sabendo do que foi exposto acima, quais seriam os passos para implementar a versão de programação dinâmica para este algoritmo?

!!! Dica
Os passos são gerais e bem parecidos com os da APS 3.
!!!

:::Gabarito

Para a implementação é necessário realizar os seguintes passos:

1. Criar um Array 2D de tamanho nxn onde n é o tamanho da string;
2. Inicializar os elementos do array com 0. Cada elemento no array contém um valor booleano se substring s[i: j] está presente no dicionário ou não;
3. Antes de chamar a recursiva verificar se a substring já foi analisada antes, verificando pela posição no array;
4. Caso não estiver no array ainda, faz a recursiva para analisar essa substring e preenche o espaço no array;
5. Se já esta no array, utiliza-se a resposta que já existe e não calcula de novo, diminuindo o trabalho do algoritmo.

???

Pensando nesses passos, podemos desenvolver o seguinte pseudocódigo para a versão de programação dinâmica do problema de quebra de palavras.

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

??? Exercício 7

Qual é a complexidade da versão de Programação Dinâmica do algoritmo que resolve o problema de quebra de palavras?

:::Gabarito

A complexidade é $O(n^2)$.

???

---

## Desafio

??? Exercício 8

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
