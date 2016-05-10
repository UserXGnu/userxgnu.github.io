---
layout: post
title: "C - Checking/Treating user input "
date: 2016-03-13 11:10:00 -0300
categories:
    - Coding
    - C
---

## Do que se trata

Então, muitas linguagems oferencem suporte a tratamento de exceções e features desse tipo
que facilitam pra tatar a entrada do usuário. Nesse post iremos falar sobre formas tratar
a entrada do usuário sob alguns aspectos.

#### nota:
> Esse post é resultado de endagações que surgiram durante uma das 
> [#ViradaHacker]'s que acontecem no [#RaulHackerClube]  do [LampiãoSec]. 
> Com base nessas endagações sobre como tratar a entrada do usuário em C, 
> fizemos então um pequeno processo investigativo ali na hora e chegamos 
> a essas conclusões, portanto, devem existir formas mais faceis ou
> eficientes de se fazer isso...não me julguem =-D 

[#ViradaHacker]: http://lampiaosec.github.io/virada-hacker
[#RaulHackerClube]: http://raulhc.cc
[LampiãoSec]: http://lampiaosec.github.io/

# Codigo base

Vamos levar em consideração pra o nosso propósito o codigo a seguir:

~~~ c
    #include <stdio.h>
    #include <stdlib.h>
    
    int
    main (void) {
        
        char c;
        int d;
        printf ("Digite um char e um int (separado por espaço): ");
        scanf ("%c %d", &c, &d);

        printf ("Caractere: %c\nNúmero: %d\n", c, d);

        return 0;
    }
~~~

Agora, considere que este é um progroma em cujo os valores de entrada do usuário precisam
ser, necessariamente, um char e um int por qualquer razão que dependeria da aplicação em si.
Como podemos garantir que o usuário digitou de fato um char e um int e n dois int's ou dois chars
e ainda mais, se digitou os dois valores e não apenas um?

# Solução

Vejo muitos codigos na internet fazendo a utilização do **scanf()** mas realmente poucos com 
uma checagem de seu retorno. Nesse sentido é importante observar a manpage:

#### nota:

> Return value:
>
>   On  success, these functions return the number of input items successfully
>   matched and assigned; this can be fewer than provided for, or even zero, in the
>   event of an early matching failure.
>
>   The value EOF is returned if the end of input is reached before either the
>   first successful conversion or a matching failure occurs. EOF is also
>   returned if a read error occurs, in which case the error indicator for the
>   stream (see ferror(3)) is set, and errno is set to indicate the error."


Então podemos solucionar parte do problema alterando o codigo pra ficar mais ou menos assim:

~~~ c
    #include <stdio.h>
    #include <stdlib.h>
    
    int
    main (void) {
        
        char c;
        int d;
        printf ("Digite um char e um int (separado por espaço): ");
        if (scanf ("%c %d", &c, &d) != 2) {
            printf ("Error getting user input\n");
            exit (1);
        }

        printf ("Caractere: %c\nNúmero: %d\n", c, d);

        return 0;
    }
~~~

Então, assim verificamos se a quantidade correta de valores foi passada pelo usuário.

## Mas ainda não acabou

Mas ainda existe o problema de não conseguir saber se o usuário passou os valores dos
tipos esperados pela aplicação. Pra solucionar esse problema, verifiquei o funcionamento
da função atoi()

#### protótipo:

~~~ c
#include <stdlib.h>

       int atoi(const char *nptr);

       /*
        * The atoi() function converts the initial portion 
        * of the string pointed to  by  nptr  to  int.
       /*

~~~

Para solucionar esse problema então, podemos tentar converter cada valor de entrada pra
inteiro, utilizando essa função, caso ela retorne um valor inteiro valido, então o usuário
digitou um número e não um char, mas caso ela retorne zero, de erro, então saberemos que o
usuário entrou com um caracetere válido. O mesmo serve para o inteiro utilizando a função
itoa (), que faz o inverso, converte um inteiro uma string. Vejamos como ficaria:

~~~ c
#include <stdio.h>
#include <stdlib.h>
    
    int
    main (void) {
        
        char c;
        int d;
        printf ("Digite um char e um int (separado por espaço): ");
        if (scanf ("%c %d", &c, &d) != 2) {
            printf ("Error getting user input\n");
            exit (1);
        }
   
        if (atoi (&c) != 0) {
            printf ("Please, the first value must be a valid character [!!]\n");
            exit (1);
        }

        printf ("Caractere: %c\nNúmero: %d\n", c, d);
        return 0;
    }
~~~

Caso scanf () retorne um numero diferente da quantidade de parametros, o usuário não passou
um int como segundo parametro, pois um char pode receber um inteiro <= 127 e nesse caso, o scanf
retorna corretamente mas um inteiro não pode ser passado como caractere, o scanf() não consegue
associar e então retorna o valor da quantidade de parametros que conseguiu associar.

# Considerações

Agradecendo ai à galera do [LampiãoSec](http://lampiaosec.github.io "LampiãoSec") e à galera
que compareceu e vem comparecendo nas viradas hacker. Qualquer contribuição será bem vista e
qualquer crítica bem aceita.


