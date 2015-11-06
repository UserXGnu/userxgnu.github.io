---
layout: post
title: "C - Malloc() vs Alloca()"
date: 2015-11-05 11:10:00 -0300
categories:
    - Coding
    - C
---

## Prefácio

Bom, eu não poderia dizer qual das duas funções é melhor. Contúdo, posso fazer um review sobre 
alocação dinâmica e então ressaltar as diferenças entre as duas evidenciando, é claro, as vantagens
e as desvantagens de cada uma. Tendo dito isso, sigamos...

## malloc ()

A função malloc () esta presente no header stdlib.h. Ela faz parte do ansii C e portanto, estará presente
em qualquer compilador C de forma a garantir a compatibilidade do seu código.

# Protótipo:
{% highlight C %}
    #include <stdlib.h>
    
    void * malloc (size_t size);
    void * free (void * ptr);
{% endhighlight %}
    
A função malloc(), como você deve saber, alloca <size> bytes de memória no heap. A idéia é que se possa alocar
uma quantidade de bytes não definida por um tipo primitivo. Exemplo:

{% highlight C %}
    int i;      // 4 bytes;
    float f;    // 4 bytes;
    double d;   // 8 bytes;
    float f;    // 4 bytes;
    char c;     // 1 byte;
{% endhighlight%}

Então caso surja a necessidade de alocar algum espaço de memória diferente desses ou suas variações (long, short e etc...), é muito comum que se use o malloc para realizar essa alocação.


# OK, e dai?

Pois bem, existem algum fatores que devemos levar em consideração. Vamos lah...

 - Alocou, agora desalloque !!!
 C é uma linguagem que transfere a responsabilidade de manipular a memória para o programador, nesse caso, quando uma variável é alocada 
 dinâmicamente, isto é, no heap, após a função que alocou essa variável acabar, a variável não é desalocada como variáveis comuns. Nesse sentido, é importante que a referencia à memória alocada seja preservada de alguma forma para que futuramente o programador possa desalocar a variável. Caso se esqueça, e essa função que chama malloc for chamada várias vezes, um problema conhecido como memory leaking acontece. Nesse caso, a memória vai sendo alocada e alocada e não é liberada, dai o sistema começa a ficar mais lento, até que enfim, para de funcionar por estar com a memória entupida.
 
 -- OBS: 
{% highlight C %}
    free (void * ptr); // função que desaloca <ptr> da memória.
{% endhighlight%}

 - Bom, essa é a parte em que a função Alloca() entra em jogo.
{% highlight C %}
    #include <alloca.h>
    
    void * alloca (size_t size);
{% endhighlight%}

A função alloca(), aloca dinamicamente size bytes na memória, contudo, não utiliza o heap, neste caso, a variável é alocada na stack mesmo. O desempenho acaba sendo melhor por não utilizar o heap, contúdo, alguns cuidados devem ser tomados. Vamos lá...

1. Caso você vá utilizar a função alloca (), utiliza a checagem __libc_use_alloca (size), em que size é o tamanho em bytes que deseja alocar,
caso essa "função" retorne false, não aloque ou utilize malloc (), uma vez que se o retorno dessa função for false, significa que a quantidade de bytes que deseja alocar pode bypassar a checagem de stack overflow, uma vez que a stack, diferente do heap, não foi feita para alocação dinâmica e portanto há o risco de sobrescrever algum valor ou mesmo estourar o tamanho da stack.

2. Os bytes alocados dinamicamente com a função alloca() devem ser utilizados apenas dentro da função que os alocou.

# Por que tanta preocupação para usar essa função então ???

Quando a função alloca() é chamada por outra função, a função que a chama, possui uma stack própria chamada stack frame, neste caso, os bytes serão alocados no próprio stackframe da função que chamou alloca(), o que significa que quando a função se encerrar, o stackframe da função deixará de existir e os bytes alocados se perderão. A boa notícia, você não terá mais a responsabilidade de desalocar os bytes que alocou dinamicamente (não use free () para tentar desalocar esses bytes manualmente). A má nóticia é que vc deve ser cauteloso ao utilizar o alloca ().

# Conclusões:

A função alloca() possui um significativo melhor desempenho mas possui restrições como limite de tamanho a ser alocado, e perda da alocação com o final da funçao que a alocou. Além disso, a ela não foi implementada por todos os compiladores e não faz parte do ansii C, mesmo entre os compiladores que a implementaram, existem divergências de implementações o que pode gerar comportamentos inesperado caso compile o mesmo codigo utilizando-a em compiladores diferentes. (use GCC, be happy!).

Malloc(), força o programador a tomar cuidado com o que aloca no sentido de se preocupar em desalocar para evitar memory leaking. Possui o desempenho ligeiramente inferior à função alloca (), não possui limite de alicação e, por usar o heap, após a função que alocou os bytes acabar, os bytes permanecem lá, tudo que o programador precisa fazer é manter uma referência a eles até que chegue o momento de liberá-los.

## PLUS

 - Caso a sua alocação não possua uma característica crítica no que diz respeito a desempenho, e você optou pela utilização da função malloc (), ou vc esta manipulando strings utilizando alocação dinâmica com malloc(), considere a utilização da função <calloc()>, essa função funciona de forma análoga à malloc(), contudo, os bytes alocados são inicializados com "0", evitando a necessidade de incializá-los.
 
# Protótipo:
{% highlight C %}
    #include <alloca.h>
    
    void * calloc (size_t size);
{% endhighlight%}

 - Exemplo de substituição:
 
 {% highlight C %}
    char * c = (char *) malloc (32*sizeof(char));
    memset (c, 0x00, sizeof (c));
    
    // ==
    
    char * c = (char *) calloc (32, sizeof (char));
{% endhighlight%}

Bom galera, espero que tenham gostado e que eu tenha esclarecido ao menos um pouco essa questão da alocação dinâmica e qual a melhor
escolha de allocator caso-a-caso.

