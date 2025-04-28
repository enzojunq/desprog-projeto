# Busca de Padrões em Texto

## Por que precisamos buscar padrões com rapidez?
Imagine que você esteja procurando uma agulha em um palheiro — mas que este palheiro seja do tamanho de um estádio de futebol lotado. Quanto mais rápido você encontrar essa agulha, menos tempo você perderá e mais eficiente será sua busca.

A busca eficiente de padrões é essencial em diversas situações do cotidiano e da tecnologia:

- **Encontrar um nome na lista de contatos do celular**: digitar as primeiras letras traz o contato desejado instantaneamente, mesmo com centenas de entradas.

- **Pesquisar um termo em conversas de aplicativos de mensagem**: localizar rapidamente onde alguém mencionou “endereço” para resgatar informações importantes.

- **Procurar uma receita específica em um bloco de anotações**: ao buscar “bolo de cenoura”, sem ter que rolar manualmente por todas as receitas.

- **Filtrar ocorrências em códigos-fonte**: IDEs destacam todas as referências de uma variável ou função em projetos com milhares de linhas de código.

Se a busca de padrões for lenta, corremos o risco de:

1. Obter informações desatualizadas, tornando decisões menos precisas.

2. Sobrecarregar recursos computacionais, degradando a experiência do usuário.

3. Atrasar processos críticos, como segurança e atendimento, comprometendo a eficácia geral.

Por isso, aprender algoritmos capazes de localizar padrões em grandes volumes de dados de maneira rápida e confiável é fundamental para qualquer sistema moderno.

??? Checkpoint

Considere o seguinte texto:

```

Era uma vez uma pequena cidade à beira-mar.
Nessa cidade, uma jovem chamada Luna sonhava em viajar pelo mundo.
Ela lia livros sobre aventuras, desenhava mapas e colecionava histórias antigas.
Um dia, enquanto caminhava pela praia, Luna encontrou uma garrafa com uma carta dentro.
A carta dizia: "Venha encontrar o tesouro escondido além do horizonte."

```

Seu objetivo é encontrar todas as posições onde a palavra **garrafa**
aparece no texto acima.

Considere que:

- Não diferencie letras maiúsculas de minúsculas (ou seja, "Garrafa" e
  "garrafa" contam).
- Considere apenas aparições em que "garrafa" é uma palavra isolada.

::: Gabarito

Uma maneira possível de implementar essa busca seria utilizando o método de
força bruta, verificando cada posição do texto caractere por caractere:

```c

#include <stdio.h>
#include <string.h>

int main() {
   char texto[] = "Era uma vez uma pequena cidade à beira-mar. Nessa
                  cidade, uma jovem chamada Luna sonhava em viajar pelo mundo. Ela lia livros
                  sobre aventuras, desenhava mapas e colecionava histórias antigas. Um dia,
                  enquanto caminhava pela praia, Luna encontrou uma garrafa com uma carta
                  dentro. A carta dizia: 'Venha encontrar o tesouro escondido além do
                  horizonte.'";
   char padrao[] = "garrafa";

   int n = strlen(texto);
   int m = strlen(padrao);

   for (int i = 0; i <= n - m; i++) {
         int encontrou = 1;
         for (int j = 0; j < m; j++) {
            if (texto[i + j] != padrao[j]) {
               encontrou = 0;
               break;
            }
         }
         if (encontrou) {
            printf("Encontrado na posição: %d\n", i);
         }
   }

   return 0;
}

```

:::

???

## Por que é importante analisar a complexidade de um algoritmo?

Quando pensamos em resolver problemas com algoritmos, não basta saber que eles
funcionam: é essencial entender **o quão rápido ou lento** eles são.

Analisar a complexidade de tempo de um algoritmo nos ajuda a prever:

- Se ele funcionará bem para pequenos dados, mas mal para dados grandes.
- Se ele pode se tornar inviável conforme o tamanho do problema cresce.
- Se precisamos procurar uma solução melhor.

Em especial para algoritmos de busca em textos grandes, analisar a eficiência
é fundamental para garantir que o programa funcione em um tempo razoável.

??? Atividade
Quantas vezes o algoritmo percorre o texto (no laço principal)?

::: Gabarito
O algoritmo percorre o texto uma vez para cada posição possível de início de
comparação, ou seja, aproximadamente _n_ vezes.
:::
???

??? Atividade
Quantas comparações de caracteres são feitas em cada posição?

::: Gabarito
Em cada posição, podem ser feitas até _m_ comparações, onde _m_ é o tamanho do padrão.
:::
???

??? Atividade
Qual é a complexidade total do algoritmo em termos de _n_ (tamanho do
texto) e _m_ (tamanho do padrão)?

::: Gabarito
A complexidade total do algoritmo é $O(n \times m)$, pois para cada
posição no texto (cerca de _n_), fazemos até _m_ comparações de
caracteres.
:::
???

## Existe uma forma de comparar mais rápido?

Quando usamos o método de força bruta, precisamos comparar caractere por
caractere a cada posição no texto. Isso pode ser muito lento para textos
grandes.

Mas será que existe uma maneira de comparar pedaços do texto de forma mais
rápida, sem olhar cada letra?

Uma ideia seria resumir o conteúdo de um trecho em um valor mais simples, como
um número. Se conseguíssemos transformar palavras diferentes em números
diferentes, poderíamos comparar apenas esses números!

Esse tipo de "resumo" de informações é chamado de **hash**.

Se o hash de dois trechos for diferente, sabemos imediatamente que os trechos
também são diferentes, sem precisar olhar letra por letra.

??? Atividade
Vamos praticar com um tipo simples de hash: atribua um número a cada letra,
onde:

- A = 1, B = 2, C = 3, ..., Z = 26.

O hash de uma palavra é a soma dos valores das suas letras.

**Exemplo:** BAD → B(2) + A(1) + D(4) = 7

Agora calcule:

- O hash da palavra **CAB**
- O hash da palavra **ACE**
- O hash da palavra **BED**

::: Gabarito

- CAB = C(3) + A(1) + B(2) = 6
- ACE = A(1) + C(3) + E(5) = 9
- BED = B(2) + E(5) + D(4) = 11
  :::
  ???

??? Atividade
Se duas palavras tiverem **hashes diferentes**, o que podemos afirmar sobre
as palavras?
::: Gabarito
Se os hashes forem diferentes, temos certeza de que as palavras também são
diferentes.
:::
???

??? Atividade
Se duas palavras tiverem **hashes iguais**, podemos afirmar que as palavras
são iguais?
::: Gabarito
Não necessariamente. Duas palavras diferentes podem ter o mesmo hash.
:::
???

!!! Aviso
Quando dois trechos diferentes geram o mesmo hash, chamamos isso de
**colisão**. Mesmo que o hash bata, ainda pode ser necessário comparar
caractere por caractere para confirmar se as palavras são realmente iguais.
!!!

## Problema: recalcular tudo a cada passo?

Agora que conhecemos o conceito de hash, surge uma ideia natural:

Para encontrar o padrão no texto, podemos calcular o hash de cada trecho de
tamanho \( m \) e comparar com o hash do padrão.

Uma primeira abordagem seria:

- Para cada nova posição no texto, calcular o hash inteiro do novo trecho (somando todos os caracteres de novo).
- Comparar o hash obtido com o hash do padrão.

**Mas será que isso é rápido?**

??? Checkpoint
Se recalculássemos o hash inteiro em cada posição, qual seria a complexidade
do algoritmo, em termos de _n_ (tamanho do texto) e _m_ (tamanho
do padrão)?
::: Gabarito
Precisaríamos calcular a soma de _m_ caracteres para cada uma das _n_ posições, resultando novamente em uma complexidade de $O(n \times m)$.
:::
???

Ou seja, se simplesmente recalculássemos o hash a cada passo, **não teríamos
ganho nenhum em relação à busca força bruta**!

Portanto, precisamos de uma solução melhor: **aproveitar o que já foi
calculado** na janela anterior.

## Entendendo o conceito de janela deslizante

Antes de falarmos em hash, precisamos entender bem o que é uma **janela deslizante** (também
chamada de sliding window).

Imagine que estamos olhando o texto aos poucos, sempre de pedaço em pedaço.

Suponha que nosso padrão tenha 3 letras. Vamos percorrer o texto olhando
sempre 3 letras de cada vez.

**Texto de exemplo:**

```
Texto:   A  B  C  D  E  F
Índices: 0  1  2  3  4  5
```

Agora aplicamos uma janela de tamanho 3:

```
Trecho 1: A B C
```

Depois deslizamos a janela para frente:

```
Trecho 2:   B C D
```

Depois deslizamos novamente:

```
Trecho 3:     C D E
```

E assim por diante.

A cada passo:

- O primeiro caractere sai (à esquerda).
- Um novo caractere entra (à direita).
- O resto da janela permanece igual.

## Calculando o hash de forma eficiente

Agora que entendemos o que muda a cada passo, podemos pensar em como atualizar o hash
rapidamente.

Se o hash é a soma dos valores das letras:

```
Hash("ABC") = valor(A) + valor(B) + valor(C)
Hash("BCD") = valor(B) + valor(C) + valor(D)
```

Note que:

- Subtraímos o valor do "A" (que saiu da janela).
- Somamos o valor do "D" (que entrou na janela).

Assim, não precisamos recalcular tudo! Só uma subtração e uma soma.

??? CheckPoint

Se o hash do trecho "ABC" for 6, e os valores forem:

- A = 1
- B = 2
- C = 3
- D = 4

Qual seria o hash do trecho "BCD" usando a atualização eficiente?

::: Gabarito

Hash("BCD") = Hash("ABC") - valor(A) + valor(D) = 6 - 1 + 4 = 9
:::
???

## Aplicando no texto real

Agora, vamos tentar aplicar o conceito de janela deslizante no texto que estamos estudando
desde o início.

**Texto:**

```
Era uma vez uma pequena cidade à beira-mar.
Nessa cidade, uma jovem chamada Luna sonhava em viajar pelo mundo.
Ela lia livros sobre aventuras, desenhava mapas e colecionava histórias antigas.
Um dia, enquanto caminhava pela praia, Luna encontrou uma garrafa com uma carta dentro.
A carta dizia: "Venha encontrar o tesouro escondido além do horizonte."
```

Nosso padrão é "garrafa" (7 caracteres). Vamos construir a sequência de
trechos da janela, um passo por vez!

??? Atividade

Qual é o primeiro trecho de 7 caracteres no texto? (Começando da posição 0.)

::: Gabarito

Trecho 1: "Era uma"
:::
???

??? Atividade

Se avançarmos 1 posição, qual seria o segundo trecho de 7 caracteres?

::: Gabarito

Trecho 2: "ra uma "
:::
???

??? Atividade

Qual caractere saiu da janela, e qual caractere entrou?

::: Gabarito

Saiu: 'E' Entrou: ' ' (espaço)
:::
???

??? Atividade

Sabendo que o hash da primeira janela foi calculado, como podemos calcular o
hash da nova janela de forma eficiente?

::: Gabarito

Subtraímos o valor do caractere que saiu ('E') e somamos o valor do
caractere que entrou (' ').
:::
???

## Resumo

Com o conceito de janela deslizante e atualização de hash eficiente,
conseguimos percorrer todo o texto atualizando o hash em tempo constante ($O(1)$).

A complexidade total passa a ser $O(n)$, muito melhor do que $O(n
\times m)$ do algoritmo de força bruta!

Essa técnica é chamada de **rolling hash** e é a base do algoritmo de **Rabin-Karp**, que estudaremos a seguir.


## Comparação de Algoritmos de Busca

| Algoritmo              | Melhor caso                | Caso médio                      | Pior caso            |
|------------------------|----------------------------|---------------------------------|----------------------|
| **Knuth–Morris–Pratt** | {red}($O(n + m)$)                 | $O(n + m)$                      | {green}($O(n + m)$)           |
| **Boyer–Moore**        | {green}($O(n / m)$) *(sublinear)*   | $O(n + m)$                      | {red}($O(n × m)$)           |
| **Rabin–Karp**         | {red}($O(n + m)$)                 | $O(n + m)$ *(esperado)*         | {red}($O(n × m)$)           |
