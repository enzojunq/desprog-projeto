# Busca de Padrões em Texto - Algoritmo de Rabin-Karp

## Busca de Padrões em Texto: Por que é tão importante otimizar?
Imagine que você precisa encontrar, em segundos, todas as ocorrências de uma palavra ou sequência de caracteres em documentos enormes ou em bases de dados complexas. Situações reais em que a velocidade de busca faz toda a diferença:

- **Ctrl+F em grandes arquivos**: Seu editor ou navegador deve responder instantaneamente a pesquisas, mesmo em textos de centenas de páginas.

- **Análise de genomas**: Laboratórios mapeiam trilhões de nucleotídeos para localizar sequências associadas a doenças. Cada segundo conta em diagnósticos e pesquisas médicas.

- **Detecção de malware**: Antivírus monitoram arquivos e pacotes de dados em tempo real. Um algoritmo lento pode permitir que uma ameaça se espalhe antes de ser identificada.

- **Indexação de buscadores**: Mecanismos como o Google varrem a web constantemente; atrasos na indexação resultam em informação desatualizada para milhões de usuários.

Quando empregamos métodos ingênuos de comparação “caractere por caractere” (força bruta), acabamos com um custo inviável para grandes volumes de dados. É aqui que entram algoritmos de busca baseados em hashing e janela deslizante, capazes de reduzir drasticamente o tempo de execução.

Neste handout, vamos explorar o Rabin–Karp, que une:

1.	**Hash polinomial** para resumir trechos de texto em valores numéricos;
2.	**Rolling hash** para atualizar esse valor em tempo constante ao mover a janela de busca.

Vamos começar com algo bem simples:

??? Checkpoint

Considere o seguinte texto:

```txt
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

Uma abordagem geral seria percorrer o texto e comparar, posição por posição, se encontramos a sequência de caracteres que corresponde ao padrão buscado.

:::

???

Essa abordagem intuitiva de verificar posição por posição é exatamente o que chamamos de **algoritmo de força bruta** para busca de padrões. Vamos formalizar como isso funciona:

## Janela Deslizante: O conceito fundamental

Antes de continuar, precisamos entender um conceito crucial chamado **janela deslizante** (sliding window).
Imagine que estamos analisando um texto e queremos encontrar um padrão específico. A janela deslizante nos permite olhar apenas para um trecho de tamanho fixo do texto por vez, movendo essa "janela" um caractere de cada vez.

Veja como funciona essa janela:


:nova_janela

Essa técnica é fundamental tanto para o algoritmo de força bruta quanto para o Rabin-Karp, que veremos a seguir.
??? Atividade
Considere o texto "ATGCATGCA" e uma janela de tamanho 3. Quais seriam as primeiras 4 janelas conforme percorremos o texto?
::: Gabarito

- ATG (posição 0-2)
- TGC (posição 1-3)
- GCA (posição 2-4)
- CAT (posição 3-5)

:::
???

## O algoritimo de força bruta

O algoritmo mais simples para busca de padrões é o de força bruta. Ele funciona assim:

Para cada possível posição no texto:

Compara o padrão caractere por caractere com o trecho atual do texto
Se todos os caracteres coincidirem, encontrou uma ocorrência do padrão



Vamos analisar um exemplo de como a busca por força bruta funciona ao tentar encontrar o padrão "ACGT" no texto "GTATACGT":


:bruta

??? Atividade
Considere a complexidade do algoritmo de força bruta. Se o **texto tem tamanho n** e o **padrão tem tamanho m**, responda:

1. Quantas posições potenciais precisamos verificar no texto?
2. Para cada posição, no pior caso, quantas comparações de caracteres fazemos?
3. Qual seria a complexidade total do algoritmo em termos de n e m?

::: Gabarito

1. Precisamos verificar aproximadamente n posições no texto (mais precisamente, n-m+1 posições).
2. Para cada posição, no pior caso, fazemos m comparações de caracteres.
3. A complexidade total do algoritmo é O(n × m), pois para cada uma das n posições, podemos precisar fazer até m comparações.

:::
???

## Como podemos melhorar o algoritmo de Força Bruta?

O algoritmo de força bruta funciona, mas é ineficiente para textos longos ou quando precisamos fazer muitas buscas. Imagine um genoma humano com 3 bilhões de bases - comparar caractere por caractere seria extremamente lento!
Uma ideia brilhante seria: e se pudéssemos comparar trechos inteiros de uma só vez, em vez de caractere por caractere?
É aqui que entra o conceito de **hash** - uma forma de resumir um trecho de texto em um valor numérico.

??? Atividade
Vamos criar um tipo simples de hash: atribua um número a cada letra, onde A = 1, B = 2, C = 3, ..., Z = 26. 

Como podemos calcular:

- O hash da palavra CAB
- O hash da palavra ACE
- O hash da palavra BED

!!! Dica
Não é só "grudar" os números das letras! Pense em como combinar matematicamente os valores para criar um único número que represente a palavra.
!!!

::: Gabarito

O hash é calculado através da soma do valor de cada uma das letras:

- CAB = C(3) + A(1) + B(2) = 6
- ACE = A(1) + C(3) + E(5) = 9
- BED = B(2) + E(5) + D(4) = 11

:::
???
??? Atividade
Se duas palavras tiverem hashes diferentes, o que podemos afirmar sobre
as palavras?
::: Gabarito
Se os hashes forem diferentes, temos certeza de que as palavras também são diferentes.
:::
???
??? Atividade
Se duas palavras tiverem hashes iguais, podemos afirmar que as palavras
são iguais?
::: Gabarito
Não necessariamente. Duas palavras diferentes podem ter o mesmo hash. Por exemplo, "BAC" e "CAB" teriam o mesmo hash (6) usando nossa função de soma. Quando isso acontece, chamamos de colisão de hash.
:::
???
!!! Aviso
Quando dois trechos diferentes geram o mesmo hash, chamamos isso de
**colisão**. Mesmo que o hash bata, ainda é necessário comparar
caractere por caractere para confirmar se as palavras são realmente iguais.
!!!

## Entendendo o Rolling Hash

Uma vez que entendemos os conceitos de janela deslizante e hash, podemos combiná-los para criar algo ainda mais poderoso: o **rolling hash** (ou hash deslizante).

A ideia é: se já calculamos o hash de uma janela, podemos calcular o hash da próxima janela de forma eficiente, sem precisar recalcular tudo do zero.

??? Atividade
Imagine que temos o texto "ABCDE" e uma janela de tamanho 3. Já calculamos o hash da janela "ABC".

Se usarmos o método de soma simples (A=1, B=2, C=3, D=4, E=5), o hash de "ABC" seria 1 + 2 + 3 = 6.

Como poderíamos calcular o hash da próxima janela "BCD" de forma mais eficiente, aproveitando o cálculo anterior?
::: Gabarito
Podemos:

Remover a contribuição do caractere que saiu (A = 1)
Adicionar a contribuição do caractere que entrou (D = 4)

Assim:

Hash("BCD") = Hash("ABC") - valor(A) + valor(D) = 6 - 1 + 4 = 9

Que é exatamente igual a B(2) + C(3) + D(4) = 9
:::
???

??? Atividade
Agora, para o texto "ABCDE", se o hash de "BCD" é 9, qual seria o hash de "CDE" usando o rolling hash?
::: Gabarito
Hash("CDE") = Hash("BCD") - valor(B) + valor(E) = 9 - 2 + 5 = 12

Que é exatamente igual a C(3) + D(4) + E(5) = 12
:::
???
Com essa técnica de rolling hash, conseguimos atualizar o hash em tempo constante a cada passo, tornando o processo muito mais eficiente!

## Hash Polinomial: Uma solução ainda melhor

O **hash polinomial** é uma função hash mais sofisticada que considera tanto o valor quanto a posição de cada caractere na string.
A ideia é tratar a string como um número em uma base específica (geralmente 256, que é o número de caracteres ASCII).
Para a string "ABC":

**Hash("ABC") = A × b² + B × b¹ + C × b⁰**

Onde b é a base (como 256 ou 10 para exemplos mais simples).
Isso é semelhante a como interpretamos números decimais.


??? Atividade

Considere uma versão simplificada do hash polinomial, usando base 10 e os valores A=1, B=2, C=3.
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

Observe que agora "ABC" (123) e "CBA" (321) têm hashes diferentes! O hash polinomial é muito mais eficaz para distinguir strings diferentes, embora ainda possam ocorrer colisões (isso é matematicamente inevitável para qualquer função hash).

## Colisões no Hash Polinomial

Embora o hash polinomial reduza significativamente as colisões em comparação com o hash por soma, é importante entender que colisões ainda podem ocorrer. Vamos ver alguns exemplos concretos:

**Exemplo 1**: Colisão em base 2

Usando base 2 (binária) e os valores a=1, b=2, c=3:

- Hash("aba") = 1 × 2² + 2 × 2¹ + 1 × 2⁰ = 1 × 4 + 2 × 2 + 1 × 1 = 4 + 4 + 1 = 9
- Hash("aac") = 1 × 2² + 1 × 2¹ + 3 × 2⁰ = 1 × 4 + 1 × 2 + 3 × 1 = 4 + 2 + 3 = 9

Veja que "aba" e "aac" são strings completamente diferentes, ambas com 3 caracteres, mas resultam no mesmo valor de hash: 9!

**Exemplo 2**: Colisão em base 5

Se usarmos base 5:

- Hash("aba") = 1 × 5² + 2 × 5¹ + 1 × 5⁰ = 1 × 25 + 2 × 5 + 1 × 1 = 25 + 10 + 1 = 36
- Hash("aaf") = 1 × 5² + 1 × 5¹ + 6 × 5⁰ = 1 × 25 + 1 × 5 + 6 × 1 = 25 + 5 + 6 = 36

Novamente, duas strings diferentes ("aba" e "aaf") com o mesmo hash (36).

É interessante notar que, conforme aumentamos a base, as colisões se tornam mais raras. Para bases maiores como 10 ou 256 (usadas em implementações reais), colisões entre strings curtas são muito menos prováveis, mas ainda possíveis teoricamente.



## Rolling Hash Polinomial: O Coração do Rabin-Karp

Agora vem a parte mais legal: podemos aplicar o conceito de rolling hash também ao hash polinomial!

Se já calculamos o hash da primeira janela, podemos atualizar eficientemente o hash para a próxima janela sem recalcular tudo do zero. Vamos explorar como fazer isso passo a passo para esse novo modelo:

??? Atividade 1: Removendo a contribuição do primeiro caractere
Se Hash("ABC") = A×10² + B×10¹ + C×10⁰ = 123, qual seria o resultado se removêssemos a contribuição do caractere 'A'?

::: Gabarito
A contribuição do 'A' é A×10². Como A=1, temos 1×100 = 100.

- Removendo esta contribuição: 123 - 100 = 23

- Restou B×10¹ + C×10⁰ = 2×10 + 3×1 = 23
:::
???

??? Atividade 2: Ajustando as posições
Depois de remover a contribuição do primeiro caractere, temos B×10¹ + C×10⁰ = 23.

Mas na nova janela "BCD", o 'B' deveria estar na posição 10² e o 'C' na posição 10¹.
Como podemos fazer esse ajuste matematicamente?

::: Gabarito
Precisamos multiplicar tudo por 10 para "deslocar" os caracteres uma posição para a esquerda:
(B×10¹ + C×10⁰) × 10 = B×10² + C×10¹

Em termos do valor: 23 × 10 = 230

Agora temos B na posição correta (10²) e C na posição correta (10¹).
:::
???

??? Atividade 3: Adicionando o novo caractere
Depois de ajustar as posições, temos B×10² + C×10¹ = 230.
Como adicionamos a contribuição do novo caractere 'D' que entrou na janela?

::: Gabarito
O novo caractere 'D' entra na posição 10⁰ (à direita). Como D=4, sua contribuição é 4×1 = 4.

- Adicionando ao resultado anterior: 230 + 4 = 234

- Agora temos o hash completo: B×10² + C×10¹ + D×10⁰ = 234
:::
???

??? Atividade 4: Verificando o resultado
Vamos verificar se nosso método de atualização está correto, calculando o hash de "BCD" do zero:
Hash("BCD") = B×10² + C×10¹ + D×10⁰ = ?

::: Gabarito
Hash("BCD") = 2×10² + 3×10¹ + 4×10⁰ = 200 + 30 + 4 = 234

Este resultado é exatamente igual ao que obtivemos através da atualização do hash anterior! 
:::
???

??? Atividade 5: Generalizando a fórmula
Com base nos passos que fizemos, podemos generalizar uma fórmula para atualizar o hash polinomial quando a janela desliza. Se:

- $h_{\text{atual}}$ é o hash da janela atual

- $primeiro_{\text{caractere}}$ é o caractere que sai da janela

- $novo_{\text{caractere}}$ é o caractere que entra na janela

- $b$ é a base (10 em nosso exemplo)

- $m$ é o tamanho da janela (3 em nosso exemplo)

Como seria a fórmula completa?
::: Gabarito
A fórmula geral para o rolling hash polinomial é:

$$h_{\text{novo}} \;=\;\bigl(h_{\text{atual}} \;-\; primeiro_{\text{caractere}}\times b^{\,m-1}\bigr)\times b \;+\; novo_{\text{caractere}}$$

Explicando cada parte:

- $primeiro_{\text{caractere}}\times b^{\,m-1}$: contribuição do caractere que sai (na posição mais à esquerda)

- $h_{\text{atual}} - (primeiro_{\text{caractere}}\times b^{\,m-1})$: removendo esta contribuição

- $(h_{\text{atual}} - primeiro_{\text{caractere}} \times b^{\,m-1}) \times b$: deslocando tudo uma posição para a esquerda

- $novo_{\text{caractere}}$: adicionando o novo caractere na posição mais à direita

:::
???

??? Atividade 6: Aplicando na prática
Suponha que já calculamos o hash polinomial de "CDE" como 345 (com base 10). Usando a fórmula que derivamos, calcule o hash de "DEF" se F=6.
::: Gabarito
Aplicando a fórmula:

$$h_{\text{novo}} = (h_{\text{atual}} - primeiro_{\text{caractere}} \times b^{m-1}) \times b + novo_{\text{caractere}}$$

Temos:

- $h_{\text{atual}}$ = 345

- $primeiro_{\text{caractere}} = C = 3$

- $b = 10$

- $m = 3$

- $novo_{\text{caractere}} = F = 6$

Substituindo os números:

- $h_{\text{novo}} = (345 - 3 \times 10^2) \times 10 + 6$

- $= (345 - 300) \times 10 + 6$

- $= 45 \times 10 + 6$

- $= 450 + 6$

- $= 456$

Vamos verificar calculando diretamente:

Hash("DEF") = D×10² + E×10¹ + F×10⁰ 

= 4×100 + 5×10 + 6×1 = 400 + 50 + 6 = 456 ✓

:::
???
Com esta abordagem, conseguimos atualizar o hash em tempo constante a cada passo, tornando o algoritmo muito mais eficiente.

## O algoritmo de Rabin-Karp

Agora, estamos prontos para entender o algoritmo completo de Rabin-Karp:

- Calcular o hash do padrão que estamos buscando
- Calcular o hash da primeira janela no texto (do mesmo tamanho do padrão)

Para cada posição do texto:

- Comparar o hash da janela atual com o hash do padrão
- Se forem iguais, verificar caractere por caractere (para evitar falsos positivos devido a colisões)
- Atualizar o hash para a próxima janela usando o rolling hash

??? Atividade
Agora tente você mesmo!

- Seu objetivo é demonstrar passo a passo o que o algorítmo faria para encontrar o padrão “ACGT” pelo seu hash.

- Considere o texto “GTATACGT”.

Tente ao menos começar, caso não consiga, há uma dica abaixo.

::: Dica
A dica é, você deve começar calculando o hash da combinação que está procurando (lembre-se da base que estamos utilizando)!

1. Hash do padrão "ACGT"
$$
1\cdot10^3 + 3\cdot10^2 + 7\cdot10^1 + 20\cdot10^0
= 1000 + 300 + 70 + 20
= \mathbf{1390}
$$

::: Gabarito
Após calcular o hash da combinação que estamos procurando, o **Rabin-Karp** fará os passos abaixo:

2. Hash da 1ª janela "GTAT"
$$
7\cdot10^3 + 20\cdot10^2 + 1\cdot10^1 + 20\cdot10^0
= 7000 + 2000 + 10 + 20
= \mathbf{9030}
$$

3. Rolling hash

    - "TATA"
$$
(9030 - 7\cdot10^3)\times10 + 1
= (9030 - 7000)\times10 + 1
$$
$$
= 2030\times10 + 1
= \mathbf{20301}
$$

    - "ATAC"
$$
(20301 - 20\cdot10^3)\times10 + 3
= (20301 - 20000)\times10 + 3
$$
$$
= 301\times10 + 3
= \mathbf{3013}
$$

    - "TACG"
$$
(3013 - 1\cdot10^3)\times10 + 7
= (3013 - 1000)\times10 + 7
$$
$$
= 2013\times10 + 7
= \mathbf{20137}
$$

    - "ACGT"
$$
(20137 - 20\cdot10^3)\times10 + 20
= (20137 - 20000)\times10 + 20
$$
$$
= 137\times10 + 20
= \mathbf{1390}
$$

4. Comparação final
    - Neste último passo o hash volta a ser 1390, igual ao do padrão → possível match.
    - Para confirmar, fazemos a verificação caractere a caractere: "ACGT" == "ACGT".

:::

???


Vamos visualizar esse processo:

Texto: "GTATACGT"
Padrão: "ACGT"
Base: 10

![](rk01.png)

---

:rk


## Análise de Complexidade: O Problema das Colisões

O Rabin-Karp é eficiente, mas sua performance **depende diretamente da quantidade de colisões** de hash que ocorrem durante a busca.

## Melhor Caso vs Pior Caso

**Melhor caso (poucas colisões):** 
- Rolling hash atualizado em O(1) para cada posição
- Poucas verificações caractere por caractere
- **Complexidade: O(n + m)**

**Pior caso (muitas colisões):**
- Hash coincide em todas as posições → verificação caractere por caractere sempre
- **Complexidade: O(n × m)** - igual ao força bruta!


**Por que as Colisões são Problemáticas?**
O ponto central é: sempre que há colisão de hash, precisamos verificar caractere por caractere. Se isso acontece muito, perdemos toda a vantagem do rolling hash.

| Caso | Complexidade | Quando ocorre |
|------|-------------|---------------|
| **Melhor/Médio** | O(n + m) | Poucas colisões |
| **Pior** | O(n × m) | Muitas colisões |

A **escolha da função hash** é crucial para manter a eficiência!


## Hora do conhecimento final!

??? Checkpoint
O que a **detecção de plágio**, uma **busca de múltiplos padrões** e um **sistema de segurança de rede** tem em comum?


::: Gabarito
Ops... achou mesmo que seria tão fácil assim? Antes de ver o gabarito abaixo pense um pouco mais! Você acabou de chegar na última questão...
::: Gabarito
Essas são algumas das aplicações que o **Rabin-Karp** se destacaria de outros algorítmos, veja mais abaixo:
- **Detecção de plágio**: sistemas que precisam verificar se um documento contém trechos copiados de vários outros documentos.
- **Busca de múltiplos padrões**: quando precisamos encontrar vários padrões diferentes no mesmo texto, o Rabin-Karp é muito eficiente, pois podemos reaproveitar o hash da janela atual para comparar com os hashes de todos os padrões.
- **Sistemas de segurança de rede**: busca de múltiplas assinaturas de malware em pacotes de dados simultaneamente.
:::
???
