# Busca de Padrões em Texto

## Por que precisamos buscar padrões com rapidez?
Quando você pressiona Ctrl+F para buscar uma palavra em um documento de texto, espera resultados instantâneos, não é? Mas se o algoritmo de busca for ineficiente, você terá que esperar vários segundos – ou até minutos – para operações que deveriam ser imediatas.

A busca eficiente de padrões é crítica em aplicações reais como:

- **Análise de DNA**: Identificar sequências específicas em genomas com bilhões de nucleotídeos, essencial para pesquisas médicas e diagnósticos.

- **Sistemas antivírus**: Detectar assinaturas de malware em tempo real em arquivos sendo verificados, onde atrasos podem comprometer a segurança.

- **Indexação de mecanismos de busca**: O Google processa bilhões de páginas web e precisa encontrar termos específicos em frações de segundo.

- **Compressão de dados**: Algoritmos como LZ77 precisam identificar padrões repetidos para substituí-los por referências mais curtas.

Se a busca de padrões for lenta, as consequências são graves:

1. Em sequenciadores de DNA, resultados atrasados podem impactar decisões médicas urgentes.

2. Em sistemas de segurança, ameaças podem passar despercebidas enquanto o sistema ainda está analisando dados anteriores.

3. Em indexadores web, sites não seriam atualizados com frequência suficiente, resultando em informações desatualizadas.

Por isso, algoritmos capazes de localizar padrões em grandes volumes de dados com eficiência são fundamentais para sistemas modernos.

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

- A ideia seria percorrer o texto para assim comparar as palavras até encontrar a correta.
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

Em especial para algoritmos de busca em textos grandes, como aqueles usados em bancos de dados genômicos com mais de 3 bilhões de pares de bases, analisar a eficiência é fundamental para garantir que o programa funcione em um tempo razoável.

??? Atividade
No algoritmo de força bruta que acabamos de ver, quantas vezes o algoritmo percorre o texto (no laço principal)?

::: Gabarito
O algoritmo percorre o texto uma vez para cada posição possível de início de
comparação, ou seja, aproximadamente _n_ vezes, onde _n_ é o tamanho do texto.
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

Quando usamos o método de **força bruta**, precisamos comparar caractere por
caractere a cada posição no texto. Para um genoma humano de 3 bilhões de bases e um padrão de interesse de 100 nucleotídeos, isso significaria até 300 bilhões de comparações!

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
Não necessariamente. Duas palavras diferentes podem ter o mesmo hash. Por exemplo, "BAC" e "CAB" teriam o mesmo hash (6) usando nossa função de soma.
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

Imagine que estamos analisando uma sequência de DNA e queremos encontrar um gene específico de 4 nucleotídeos. Vamos percorrer o texto olhando sempre 4 letras de cada vez.

**Texto de exemplo:**

```
DNA:     A  C  G  T  C  A
Índices: 0  1  2  3  4  5
```

Agora aplicamos uma janela de tamanho 4:

```
Trecho 1: A C G T
```

Depois deslizamos a janela para frente:

```
Trecho 2:   C G T C
```

Depois deslizamos novamente:

```
Trecho 3:     G T C A
```

A cada passo:

- O primeiro caractere sai (à esquerda).
- Um novo caractere entra (à direita).
- O resto da janela permanece igual.

## Calculando o hash de forma eficiente

Agora que entendemos o que muda a cada passo, podemos pensar em como atualizar o hash
rapidamente.

Se o hash é a soma dos valores das letras:

```
Hash("ACGT") = valor(A) + valor(C) + valor(G) + valor(T)
Hash("CGTC") = valor(C) + valor(G) + valor(T) + valor(C)
```

Note que para obter o novo hash:

- Subtraímos o valor do "A" (que saiu da janela).
- Somamos o valor do "C" (que entrou na janela).

Assim, não precisamos recalcular tudo! Só uma subtração e uma soma.

??? CheckPoint

Se o hash do trecho "ACGT" for 31, e os valores forem:

- A = 1
- C = 3
- G = 7
- T = 20
- C = 3

Qual seria o hash do trecho "CGTC" usando a atualização eficiente?

::: Gabarito

Hash("CGTC") = Hash("ACGT") - valor( A ) + valor( C ) = 31 - 1 + 3 = 33
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

Saiu: 'E' 
Entrou: ' ' (espaço)
:::
???

??? Atividade

Sabendo que o hash da primeira janela foi calculado, como podemos calcular o
hash da nova janela de forma eficiente?

::: Gabarito

Subtraímos o valor do caractere que saiu ('E') e somamos o valor do
caractere que entrou (' ').

Hash_novo = Hash_antigo - valor('E') + valor(' ')
:::
???

## Resumo

Com o conceito de janela deslizante e atualização de hash eficiente,
conseguimos percorrer todo o texto atualizando o hash em tempo constante ($O(1)$).

A complexidade total passa a ser $O(n)$, muito melhor do que $O(n
\times m)$ do algoritmo de força bruta!


---

## O Algoritmo de Rabin-Karp

Agora que entendemos os conceitos fundamentais de hash e janela deslizante, vamos ver como o algoritmo de Rabin-Karp combina essas ideias de forma inteligente.

## Aprimorando o conceito de hash

Na nossa abordagem inicial, usamos um hash simples: a soma dos valores das letras. Mas esse tipo de hash tem um problema sério.

??? Atividade

Considere as palavras "ABC" e "CBA". Calcule o hash de cada uma delas usando nossa regra anterior (A=1, B=2, C=3).

::: Gabarito
- Hash("ABC") = A(1) + B(2) + C(3) = 6
- Hash("CBA") = C(3) + B(2) + A(1) = 6
:::
???

Veja que duas palavras diferentes têm o mesmo hash! Isso significa que teríamos muitas colisões, tornando o algoritmo menos eficiente.

Em sistemas bioinformáticos que analisam DNA, essas colisões poderiam fazer com que sequências diferentes fossem erroneamente identificadas como iguais.

O algoritmo de Rabin-Karp usa uma função hash mais sofisticada, que leva em consideração tanto o valor quanto a posição de cada caractere.

## Hash polinomial

A função hash polinomial trata a string como um número em uma base específica (vamos usar a base 256, que é o número de caracteres ASCII possíveis).

Para a string "ABC":
- Hash("ABC") = A × 256² + B × 256¹ + C × 256⁰

Ou de forma mais geral, para uma string de comprimento m:
- Hash(s) = s[0] × b^(m-1) + s[1] × b^(m-2) + ... + s[m-1] × b^0

Onde:
- s[i] é o valor ASCII do caractere na posição i
- b é a base (vamos usar 256)

Esse método é semelhante a como interpretamos números decimais:
- O número 123 significa 1×10² + 2×10¹ + 3×10⁰

??? Atividade

Considere uma versão simplificada do hash polinomial, usando base 10 em vez de 256, e os valores A=1, B=2, C=3.

Calcule o hash polinomial de "ABC":

::: Gabarito
Hash("ABC") = A × 10² + B × 10¹ + C × 10⁰ = 1 × 100 + 2 × 10 + 3 × 1 = 100 + 20 + 3 = 123
:::
???

??? Atividade

Agora, calcule o hash polinomial de "CBA" usando a mesma regra:

::: Gabarito
Hash("CBA") = C × 10² + B × 10¹ + A × 10⁰ = 3 × 100 + 2 × 10 + 1 × 1 = 300 + 20 + 1 = 321
:::
???

Observe que agora "ABC" e "CBA" têm hashes diferentes! O hash polinomial é muito mais eficaz para distinguir strings diferentes.

## Atualizando o hash polinomial de forma eficiente

A beleza do hash polinomial é que também podemos atualizá-lo de forma eficiente quando a janela desliza!

Imagine um sistema de vigilância de rede que analisa pacotes de dados em tempo real, procurando por assinaturas de ataques. Com milhões de pacotes por segundo, precisamos de atualizações instantâneas.

Se já calculamos o hash da janela atual: s[i]...s[i+m-1]
E queremos calcular o hash da próxima janela: s[i+1]...s[i+m]

Podemos fazer isso em tempo constante:

1. Removemos a contribuição do primeiro caractere: h_atual - s[i] × b^(m-1)
2. Multiplicamos o resultado por b para "deslocar" os valores
3. Adicionamos o novo caractere: s[i+m]

Em fórmula:
h_novo = (h_atual - s[i] × b^(m-1)) × b + s[i+m]

??? Checkpoint

Considere a string "ABCDE" e uma janela de tamanho 3. Se o hash de "ABC" for 123 (como calculado anteriormente), qual seria o hash de "BCD" usando a fórmula de atualização?

Considere:
- A = 1, B = 2, C = 3, D = 4, E = 5
- Base = 10

::: Gabarito

Aplicando a fórmula:
h_novo = (h_atual - A × 10²) × 10 + D
h_novo = (123 - 1 × 100) × 10 + 4
h_novo = (123 - 100) × 10 + 4
h_novo = 23 × 10 + 4
h_novo = 230 + 4
h_novo = 234

Que é exatamente B × 10² + C × 10¹ + D × 10⁰ = 2 × 100 + 3 × 10 + 4 × 1 = 200 + 30 + 4 = 234
:::
???

## O problema do overflow

Na prática, quando lidamos com strings longas, os valores do hash podem ficar extremamente grandes e causar overflow (estouro de memória). Em um sequenciador de DNA analisando genomas completos, esses números poderiam facilmente ultrapassar o limite das variáveis numéricas.

Para evitar isso, o algoritmo de Rabin-Karp usa uma técnica chamada **módulo**.

Basicamente, aplicamos o operador módulo % a um número primo grande (vamos chamar de q):

Hash(s) = (s[0] × b^(m-1) + s[1] × b^(m-2) + ... + s[m-1] × b^0) % q

??? Atividade

Considere q = 13 (um número primo pequeno para simplificar). Calcule o hash de "ABC" usando base 10 e depois aplique o módulo:

::: Gabarito
Hash("ABC") = (A × 10² + B × 10¹ + C × 10⁰) % 13
            = (1 × 100 + 2 × 10 + 3 × 1) % 13
            = (100 + 20 + 3) % 13
            = 123 % 13
            = 6
:::
???

A fórmula de atualização também precisa ser modificada para incluir o módulo:

h_novo = ((h_atual - s[i] × b^(m-1) % q + q) × b + s[i+m]) % q

O termo "+ q" é adicionado para garantir que o resultado da subtração não seja negativo antes de aplicar o módulo.

## Implementando o algoritmo de Rabin-Karp

Vamos juntar todos os conceitos que aprendemos até agora e implementar o algoritmo completo.

A ideia central do Rabin-Karp é:

1. Calcular o hash do padrão
2. Calcular o hash da primeira janela no texto
3. Para cada posição do texto:

   **a.** Comparar o hash da janela atual com o hash do padrão

   **b.** Se forem iguais, verificar caractere por caractere (para evitar falsos positivos devido a colisões)

   **c.** Atualizar o hash para a próxima janela

??? Atividade

Vamos traçar manualmente a execução do algoritmo de Rabin-Karp para encontrar o padrão "DNA" no texto "GGATCGDNAACGT":

Para simplificar, vamos usar:
- Base b = 10
- Módulo q = 11
- Valores: A = 1, C = 3, D = 4, G = 7, N = 14, T = 20

Primeiro, calculamos o hash do padrão "DNA":
- Hash("DNA") = (D × 10² + N × 10¹ + A × 10⁰) % 11
              = (4 × 100 + 14 × 10 + 1 × 1) % 11
              = (400 + 140 + 1) % 11
              = 541 % 11
              = 2

::: Gabarito

1. Hash do padrão "DNA" = 2

2. Hash da primeira janela "GGA":
   - Hash("GGA") = (7 × 100 + 7 × 10 + 1 × 1) % 11
   - = (700 + 70 + 1) % 11
   - = 771 % 11
   - = 1
   - Hash diferente do padrão, continuamos.

3. Atualizamos o hash para a próxima janela "GAT":
   - h_novo = ((1 - 7 × 100 % 11 + 11) × 10 + 20) % 11
   - = ((1 - 700 % 11 + 11) × 10 + 20) % 11
   - = ((1 - 0 + 11) × 10 + 20) % 11
   - = (12 × 10 + 20) % 11
   - = (120 + 20) % 11
   - = 140 % 11
   - = 8
   - Hash diferente do padrão, continuamos.

4. [Continue o processo para as demais janelas...]

5. Para a janela "DNA" que começa na posição 5:
   - Hash = 2
   - Como o hash é igual ao hash do padrão, verificamos caractere por caractere:
   - "DNA" == "DNA" → Encontrado na posição 5!
:::
???

## Quando o Rabin-Karp é melhor?

O algoritmo de Rabin-Karp tem algumas características interessantes:

- **Melhor caso**: O(n + m) - quando não há colisões e o padrão não aparece no texto
- **Caso médio**: O(n + m) - esperado com uma boa função hash
- **Pior caso**: O(n × m) - quando há muitas colisões ou quando o padrão aparece muitas vezes

O algoritmo se destaca especialmente em aplicações reais como:

1. **Detecção de plágio**: sistemas que precisam verificar se um documento contém trechos copiados de vários outros documentos.

2. **Sistemas de segurança de rede**: busca de múltiplas assinaturas de malware em pacotes de dados simultaneamente.

3. **Busca em bancos de dados genômicos**: identificação de múltiplas sequências de interesse em genomas completos.

??? Atividade

Considere os seguintes cenários. Em quais deles você acredita que o algoritmo de Rabin-Karp seria uma boa escolha?

**a)** Um antivírus verificando um arquivo contra uma única assinatura de malware.

**b)** Um sistema de detecção de intrusão verificando pacotes de rede contra milhares de assinaturas de ataques conhecidos.

**c)** Um verificador de plágio comparando um trabalho acadêmico a um banco de dados de artigos publicados.

**d)** Buscar um pequeno padrão binário (4 bits) em um arquivo de executável.

::: Gabarito

**a)** Não seria a melhor escolha. Para buscar um único padrão curto, outros algoritmos como KMP ou Boyer-Moore são geralmente mais eficientes.

**b)** Excelente escolha! O Rabin-Karp é ideal para buscar múltiplos padrões simultaneamente, pois pode reutilizar o hash da janela atual para comparar com os hashes de todos os padrões.

**c)** Excelente escolha! Este é um caso clássico onde o Rabin-Karp se destaca, pois podemos buscar muitos trechos potencialmente plagiados de uma só vez.

**d)** Não seria ideal. Com um padrão muito curto (4 bits) e um alfabeto pequeno (apenas 0 e 1), haveria muitas colisões, reduzindo a eficiência.
:::
???

## Comparando com outros algoritmos

Conforme vimos na tabela de comparação:

| Algoritmo              | Melhor caso                | Caso médio                      | Pior caso            |
|------------------------|----------------------------|---------------------------------|----------------------|
| **Knuth–Morris–Pratt** | {red}($O(n + m)$)          | $O(n + m)$                      | {green}($O(n + m)$)  |
| **Boyer–Moore**        | {green}($O(n / m)$) *(sublinear)*   | $O(n + m)$                      | {red}($O(n × m)$)  |
| **Rabin–Karp**         | {red}($O(n + m)$)          | $O(n + m)$ *(esperado)*         | {red}($O(n × m)$)    |

O Rabin-Karp não é o algoritmo mais rápido em todos os cenários, mas tem suas vantagens específicas:

- **Knuth-Morris-Pratt (KMP)**: Tem garantia de desempenho O(n + m) mesmo no pior caso, mas é mais complexo de implementar.
- **Boyer-Moore**: Pode ser extremamente rápido no melhor caso (sublinear!), pulando grandes partes do texto, mas pode degradar no pior caso.
- **Rabin-Karp**: Tem implementação relativamente simples e pode ser facilmente estendido para buscar múltiplos padrões.

??? Atividade

Para cada um dos cenários a seguir, escolha o algoritmo que você acredita ser mais adequado:

**a)** Um editor de texto que precisa buscar uma única palavra em um documento.

**b)** Um sistema de detecção de intrusão que precisa verificar pacotes de rede contra milhares de assinaturas de ataques conhecidos.

**c)** Um verificador de plágio que compara um documento a um banco de dados de textos.

**d)** Um sistema médico que precisa garantir tempo de resposta consistente ao buscar padrões em exames de imagem.

::: Gabarito

a) **Boyer-Moore** seria a melhor escolha. Em textos naturais, ele frequentemente atinge desempenho sublinear e é excelente para buscas de palavras únicas.

b) **Rabin-Karp** seria a melhor escolha. A capacidade de verificar múltiplos padrões simultaneamente (reutilizando o hash da janela atual) o torna ideal para este cenário.

c) **Rabin-Karp** novamente seria excelente. Detecção de plágio envolve busca de múltiplos trechos potencialmente copiados, onde o Rabin-Karp se destaca.

d) **Knuth-Morris-Pratt (KMP)** é a única escolha garantida aqui. É o único destes algoritmos que mantém O(n + m) mesmo no pior caso, garantindo um tempo de resposta previsível.
:::
???


## Implementação

Vamos consolidar nosso entendimento com uma implementação do algoritmo:

```c
#include <stdio.h>
#include <string.h>

void rabinKarp(char texto[], char padrao[]) {
    int n = strlen(texto);  // Tamanho do texto
    int m = strlen(padrao); // Tamanho do padrão
    int b = 256;  // Base (número de caracteres possíveis)
    int q = 101;  // Número primo grande
    
    // Cálculo de b^(m-1) % q
    int h = 1;
    for (int i = 0; i < m - 1; i++) {
        h = (h * b) % q;
    }
    
    // Cálculo do hash do padrão e da primeira janela
    int p = 0;  // Hash do padrão
    int t = 0;  // Hash da janela atual
    
    for (int i = 0; i < m; i++) {
        p = (b * p + padrao[i]) % q;
        t = (b * t + texto[i]) % q;
    }
    
    // Busca do padrão
    for (int i = 0; i <= n - m; i++) {
        // Se os hashes são iguais, verificar caractere por caractere
        if (p == t) {
            int corresponde = 1;
            for (int j = 0; j < m; j++) {
                if (texto[i + j] != padrao[j]) {
                    corresponde = 0;
                    break;
                }
            }
            
            if (corresponde) {
                printf("Padrão encontrado na posição %d\n", i);
            }
        }
        
        // Atualizar o hash para a próxima janela
        if (i < n - m) {
            t = (b * (t - texto[i] * h) + texto[i + m]) % q;
            
            // Garantir que t seja positivo
            if (t < 0) {
                t = t + q;
            }
        }
    }
}

```

??? Atividade

Trace a execução do algoritmo acima para buscar o padrão "ola" no texto "ola mundo". Use base b = 10, módulo q = 7, e os valores numéricos das letras que definimos anteriormente.

::: Gabarito

Vamos traçar passo a passo:

1. Inicialização:
   - n = 9 (tamanho de "ola mundo")
   - m = 3 (tamanho de "ola")
   - b = 10
   - q = 7

2. Cálculo de h = b^(m-1) % q:
   - h = 10^2 % 7 = 100 % 7 = 2

3. Cálculo do hash do padrão:
   - p = 0
   - p = (10 * 0 + 15) % 7 = 15 % 7 = 1
   - p = (10 * 1 + 12) % 7 = 22 % 7 = 1
   - p = (10 * 1 + 1) % 7 = 11 % 7 = 4
   - Hash final de "ola" = 4

4. Cálculo do hash da primeira janela:
   - t = 0
   - t = (10 * 0 + 15) % 7 = 15 % 7 = 1
   - t = (10 * 1 + 12) % 7 = 22 % 7 = 1
   - t = (10 * 1 + 1) % 7 = 11 % 7 = 4
   - Hash da primeira janela "ola" = 4

5. Comparação para i = 0:
   - p = 4, t = 4 → Hashes iguais!
   - Verificar caractere por caractere: "ola" == "ola" → Encontrado na posição 0!

6. Atualização do hash para i = 1:
   - t = (10 * (4 - 15 * 2) + 32) % 7
   - = (10 * (4 - 30) + 32) % 7
   - = (10 * (-26) + 32) % 7
   - = (-260 + 32) % 7
   - = -228 % 7
   - = -4 + 7 = 3

7. E assim por diante...
:::
???


## Conclusão

O algoritmo de Rabin-Karp apresenta uma abordagem engenhosa para o problema de busca de padrões em textos:

1. Usa hashing para comparar rapidamente strings
2. Aplica o conceito de janela deslizante para atualizar o hash de forma eficiente
3. Lida com potenciais colisões verificando caractere por caractere quando necessário

Apesar de não ser o mais rápido em todos os cenários, ele se destaca na busca de múltiplos padrões simultaneamente e possui uma implementação relativamente simples.

A combinação de conceitos matemáticos (hashing polinomial e aritmética modular) com técnicas algorítmicas (janela deslizante) faz do Rabin-Karp um excelente exemplo de como ideias de diferentes áreas podem se unir para resolver problemas computacionais de forma eficiente.