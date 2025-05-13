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

A ideia do algoritmo de força bruta para resolver esse problema seria:

1. Percorrer o texto caractere por caractere
2. Para cada posição, verificar se a sequência de caracteres a partir dali corresponde ao padrão "garrafa"
3. Se houver correspondência, registrar a posição

Usando essa abordagem, encontraríamos "garrafa" na posição em que aparece na quarta linha do texto.

:::

???

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
Considere a complexidade do algoritmo de força bruta. Se o texto tem tamanho n e o padrão tem tamanho m:

Quantas posições potenciais precisamos verificar no texto?
Para cada posição, no pior caso, quantas comparações de caracteres fazemos?
Qual seria a complexidade total do algoritmo em termos de n e m?

::: Gabarito

Precisamos verificar aproximadamente n posições no texto (mais precisamente, n-m+1 posições).
Para cada posição, no pior caso, fazemos m comparações de caracteres.
A complexidade total do algoritmo é O(n × m), pois para cada uma das n posições, podemos precisar fazer até m comparações.

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

Hash("ABC") = A × b² + B × b¹ + C × b⁰

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

??? Desafio

Vamos verificar isso. Consegue encontrar duas strings diferentes de 3 letras que teriam o mesmo hash polinomial usando base 10? (Use apenas as letras A=1, B=2, C=3, D=4, E=5)

::: Gabarito
É mais difícil, mas ainda possível. Por exemplo:

- Hash("AAB") = 1 × 10² + 1 × 10¹ + 2 × 10⁰ = 100 + 10 + 2 = 112
- Hash("BAC") = 2 × 10² + 1 × 10¹ + 3 × 10⁰ = 200 + 10 + 3 = 213

Essas duas strings têm hashes diferentes. Na verdade, com apenas 5 letras e 3 posições, é difícil encontrar colisões com o hash polinomial e base 10.
Com mais caracteres e strings mais longas, as colisões eventualmente aconteceriam, mas são muito menos frequentes que no hash por soma.

:::
???


## Rolling Hash Polinomial: O Coração do Rabin-Karp

Agora vem a parte mais legal: podemos aplicar o conceito de rolling hash também ao hash polinomial!

??? Atividade
Temos o texto "ABCDE" e queremos mover nossa janela de tamanho 3. Se já calculamos o hash polinomial de "ABC" como 123 (usando base 10), como poderíamos calcular o hash de "BCD" de forma eficiente?

Dica: Pense no que acontece matematicamente quando removemos 'A' do início e adicionamos 'D' ao final.
::: Gabarito
Quando passamos de "ABC" para "BCD":

Primeiro, removemos a contribuição do 'A':

- Hash("ABC") = A × 10² + B × 10¹ + C × 10⁰ = 123
- Sem o A: Hash - (A × 10²) = 123 - (1 × 100) = 123 - 100 = 23


Agora, precisamos "deslocar" o que sobrou para abrir espaço para o novo dígito:

- 23 × 10 = 230


Finalmente, adicionamos o novo dígito 'D':

230 + D = 230 + 4 = 234



Verificando: 
- B × 10² + C × 10¹ + D × 10⁰ 
- = 2 × 100 + 3 × 10 + 4 × 1 
- = 200 + 30 + 4 = 234 ✓

A fórmula geral seria:

- h_novo = (h_atual - primeiro_caractere × b^(m-1)) × b + novo_caractere

:::
???
Com esta abordagem, podemos atualizar eficientemente o hash à medida que deslizamos a janela pelo texto, com tempo constante por atualização.

## O algoritmo de Rabin-Karp

Agora, estamos prontos para entender o algoritmo completo de Rabin-Karp:

- Calcular o hash do padrão que estamos buscando
- Calcular o hash da primeira janela no texto (do mesmo tamanho do padrão)

Para cada posição do texto:

- Comparar o hash da janela atual com o hash do padrão
- Se forem iguais, verificar caractere por caractere (para evitar falsos positivos devido a colisões)
- Atualizar o hash para a próxima janela usando o rolling hash




Vamos visualizar esse processo:

Texto: "GTATACGT"
Padrão: "ACGT"
Base: 10

- Passo 1: Calculamos o hash do padrão

Hash("ACGT") = A×10³ + C×10² + G×10¹ + T×10⁰ = 1×1000 + 3×100 + 7×10 + 20×1 = 1000 + 300 + 70 + 20 = 1390

- Passo 2: Calculamos o hash da primeira janela

Hash("GTAT") = G×10³ + T×10² + A×10¹ + T×10⁰ = 7×1000 + 20×100 + 1×10 + 20×1 = 7000 + 2000 + 10 + 20 = 9030
Hash diferente do padrão, continuamos.

- Passo 3: Usamos rolling hash para atualizar e verificar cada janela

Hash("TATA") = (9030 - 7×10³)×10 + A = (9030 - 7000)×10 + 1 = 2030×10 + 1 = 20301
Hash diferente do padrão, continuamos.
Hash("ATAC") = (20301 - 2×10³)×10 + C = (20301 - 2000)×10 + 3 = 18301×10 + 3 = 183013
Hash diferente do padrão, continuamos.
Hash("TACG") = (183013 - 1×10³)×10 + G = (183013 - 1000)×10 + 7 = 182013×10 + 7 = 1820137
Hash diferente do padrão, continuamos.
Hash("ACGT") = (1820137 - 20×10³)×10 + T = (1820137 - 20000)×10 + 20 = 1800137×10 + 20 = 18001390
Hash diferente do padrão?! Mas o padrão é "ACGT"! Isso poderia indicar um overflow ou um erro de cálculo.

Para implementações reais, usamos um módulo grande para evitar overflow, fazendo hash % q em cada cálculo.
Independentemente, quando encontramos um match de hash, verificamos caractere por caractere para confirmar:

## Resumo

Quando o **Rabin-Karp** é melhor?

O algoritmo de Rabin-Karp tem algumas características interessantes:

- Complexidade média: O(n + m) quando bem implementado
- Complexidade no pior caso: O(n × m) quando há muitas colisões

O algoritmo se destaca especialmente em:

- Detecção de plágio: sistemas que precisam verificar se um documento contém trechos copiados de vários outros documentos.
- Busca de múltiplos padrões: quando precisamos encontrar vários padrões diferentes no mesmo texto, o Rabin-Karp é muito eficiente, pois podemos reaproveitar o hash da janela atual para comparar com os hashes de todos os padrões.
- Sistemas de segurança de rede: busca de múltiplas assinaturas de malware em pacotes de dados simultaneamente.


---

## Comparando com outros algoritmos:

Comparado a outros algoritmos de busca de padrões:


| Algoritmo              | Melhor caso                | Caso médio                      | Pior caso            |
|------------------------|----------------------------|---------------------------------|----------------------|
| **Knuth–Morris–Pratt** | {red}($O(n + m)$)          | $O(n + m)$                      | {green}($O(n + m)$)  |
| **Boyer–Moore**        | {green}($O(n / m)$) *(sublinear)*   | $O(n + m)$                      | {red}($O(n × m)$)  |
| **Rabin–Karp**         | {red}($O(n + m)$)          | $O(n + m)$ *(esperado)*         | {red}($O(n × m)$)    |


Cada algoritmo tem seu lugar, dependendo do cenário:

- **KMP**: Garantia de desempenho constante, ideal quando precisamos de tempo previsível
- **Boyer-Moore**: Extremamente rápido para padrões longos e alfabetos grandes
- **Rabin-Karp**: Ideal para buscar múltiplos padrões simultaneamente

## Conclusão

O algoritmo de Rabin-Karp nos mostra como ideias matemáticas (hash polinomial) e técnicas algorítmicas (janela deslizante) podem se combinar para resolver problemas complexos de forma eficiente.

Embora não seja o algoritmo mais rápido para todos os cenários, sua capacidade de buscar múltiplos padrões simultaneamente o torna uma ferramenta valiosa no arsenal de qualquer cientista da computação ou engenheiro de software.

Os conceitos que aprendemos aqui - hash, janela deslizante, rolling hash - são aplicáveis a muitos outros problemas além da busca de padrões, demonstrando como ideias fundamentais podem ter amplas aplicações.

