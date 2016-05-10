---
layout: post
title: "Fat Pointers Part 1: Macros"
date: 2016-05-11 12:00:00 -0300
categories:
    - Coding
    - C
    - Hacks
---

1. Introdução
    1. A quem se destinam esses post's?
2. Macros
    1. O que são?
    2. Por quê usar?
    3. Como criar uma macro?
        1. Object-like macros
        2. Function-like macros
    4. Exemplos práticos
3. Conclusão


## 1. Instrodução

Bom, essa será uma sequencia de posts relacionados à fat pointers. Irei, primeiramente, falar sobre 
conteúdos basicos para dar ao leitor uma compreensão maior dos assunos necessários para sua utilização
bem como para ampliar os horizontes de possibilidades no que diz respeito às limitações da linguagem
e seu paradimga.
 

### 1. A quem se destinam esses post's?

Essa série é destinada à qualquer pessoa com conhecimento básico na linguágem de programação
C e esteja interessado em aprender algumas coisas interessantes que não são muito frisadas (algumas
vezes nem citadas) nos lívros de programação. Ou seja, fazer algumas brincadeiras explorando as 
potencialidades do C de forma esperta e útil.
        
##### Nota:
        Os exemplos de códigos expostos em toda a série não se destinam 
    à códigos de produção. Caso tenha interesse em desenvolver de forma 
    mais robusta utilizando tecnicas como as que mostrarei, o codigo 
    precisará ser melhor elaborado dando a ele maior escalabilidade e 
    mantenabilidade.
    

#### 2. Macros

#### 1. O que são macros?

Melhor que ler de mim, segue um treixo retirado da documentação do [gcc]

> "A macro is a fragment of code which has been given a name. Whenever the name is used, it is replaced by the contents of the macro"

Ou seja, você pode criar uma "label" e dar a ela um conjunto de instruções, toda vez que essa "label" for 
encontrada no código, será substituída pelas instruções atribuídas à ela e tudo isso, em tempo de compilaçao.

#### 2. Por quê usar macros?
Bom, existem situações em que é preciso digitar certos codigos repetitivamente mas não seria legal criar função para aquilo
porque poderia pesar no desempenho, então as macros entram ai pra ajudar. Além disso, existem situações mais peculiares em 
que as macros podem ser utilizadas, para criar camadas de abstração que serão resolvidas em tempo de compilação, como dito
anteriormente, não aumentando assim a carga do programa como um todo. (depende da implementação do que se quer abstrair). 
Muitas vezes, o uso das macros da ao desenvolvedor, um aumento na produtividade e liberdade criatíva.

#### 3. Como criar uma macro?
Então, existem dois tipos de macro, as que se parecem com as declarações de tipos como *int*, conhecidas como *object-like* 
macros e as que se parecem com as clarações de funções, conhecidas como *function-like* macros. Vamos dar um *zoom in* em 
cada um desses tipos pra entendermos na prática.

#### 1. *Object-like* macros
Essas são as mais simples, são feitas para determinar valores que iremos usar durante o programa em varias partes pra evitar 
que, caso precisemos alterar aquele dado, não precisemos alterar em cada parte que o utilizamos, porque se usarmos uma macro,
precisaremos apenas editar a macro.

Exemplo simples: 

~~~ c
    #define LINHAS       4
    #define COLUNAS      3

    int 
    main (void) {
        int matriz[LINHAS][COLUNAS];
    
        return 0;
    }
~~~

Acredito que ficou bem auto-explicativo mas só por ter certeza. A instruçõe *#define* é utilizada para definir a *label* quem
representar o conjunto te instruções associadas à ela, neste caso, foram apenas números, onde quer que essas *labels* sejam 
utilizadas serão substituidas por esses números.


#### 2. *Function-like* Macros
Nesse tipo, diferente do anterior, de fato um conjunto de instruções devem ser associadas à *label* pra que tomem seu lugar em
toda vez que a *label* aparecer no código. 

Exemplo simples:

~~~ c
    #include  <stdio.h>
    
    #define   SOMA(a, b)        a+b
    #define   PRINT(fmt, arg)        printf(fmt, arg)

    int 
    main (void) {
        int a = 1;
        int b = 2;

        PRINT("Soma dos valores: %d\n", SOMA(a,b));
        return 0;
    }
    
~~~

Como pode ver, as macros funcionam como funções mesmo, executam operações tal como as funções. Algumas vezes, chegam a ser até mais
úteis, considerando o fato de que n ocupam espaço na memória.


#### 4. Exemplos práticos

## Gerador de função beseado em no tipo indicdo

~~~ c
    #include <stdio.h>
    #define SOMA(type) \
    type soma(type a, type b) { \
        return a+b;\
    }

    SOMA(int);

    int 
    main(void) {
        int a = 2;
        int b = 3;

        printf ("Soma: %d\n", soma (a, b));
        return 0;
    }
~~~

Ao chamar essa macro *SOMA* e passar um tipo de dado pra como parâmetro (*int*, *char* e etc), uma função *soma()* será declarada
e ela será do tipo passado para macro e deverá receber dois parametros de mesmo tipo. Isso é muito útil quando se fala em estrutura 
de dádos. Imagine só você ter uma macro pra gerar uma árvore binária que irá armazenar elementos do tipo que você escolher. Se você
precisa de uma arvore binária para inteiros, ou para strings e etc, poderá criar uma apenas chamando a macro e pronto.


## For each, demonstração

~~~ c
    #include <stdio.h>
    #include <stdlib.h>
    #include <time.h>

    #define FOR_EACH(indice, quantidade, execute_isso)         for(int indice = 0; indice < quantidade; indice++) execute_isso

    int 
    main (int argc, char ** argv) {
        int vetor[4];

        srand(time(NULL));

        FOR_EACH(i, 4, {
            vetor[i] = rand() % 10;      
        });

        FOR_EACH(i, 4, {
            printf ("Posição: %d Valor: %d\n", i, vetor[i]);       
        });

        return 0;
    }
~~~

Nesse caso, o intuito dessa macro é percorrer o vetor e fazer, para cada elemento do vetor, o que foi passado como parâmetro em
*execute_isso*. 

#### 3. Conclusão

Espero ter ajudado a esclarecer o assunto, e agora devem estar se perguntando porque estou falando sobre macros num e o que isso
tudo tem a ver com fat pointers, na realidade...nada! Contúdo, fat pointer é uma técnica e não uma feature do compilador, por essa
razão, lidar com fat pointers acaba sendo bem custoso pro programador pela quantidade de codigo que cresce um pouco e as macros serão
úteis nesse sentido.  

Para aqueles que querem ficar mestres em macros e tudo mais, seguem alguns links que podem ajudar:

* [Object-Like Macros](https://gcc.gnu.org/onlinedocs/cpp/Object-like-Macros.html#Object-like-Macros "Documentação oficial do GCC")
* [Function-like Macros](https://gcc.gnu.org/onlinedocs/cpp/Function-like-Macros.html#Function-like-Macros "Documentação oficial do GCC")
* [Macro Arguments](https://gcc.gnu.org/onlinedocs/cpp/Macro-Arguments.html#Macro-Arguments "Documentação oficial do GCC")


[gcc]: https://gcc.gnu.org/onlinedocs/cpp/Macros.html
[LampiãoSec]: http://lampiaosec.github.io
[#ViradaHacker]: http://lampiaosec.github.io/virada-hacker
[#RaulHackerClube]: http://raulhc.cc
