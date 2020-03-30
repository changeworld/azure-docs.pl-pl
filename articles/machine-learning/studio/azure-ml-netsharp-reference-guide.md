---
title: Niestandardowe sieci neuronowe netto#
titleSuffix: ML Studio (classic) - Azure
description: Przewodnik składniowy języka specyfikacji sieci neuronowych w sieciach sieci neuronowych. Dowiedz się, jak tworzyć niestandardowe modele sieci neuronowych w usłudze Azure Machine Learning Studio (klasyczne).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: c1912e670a9cf1c178b58cefbd33171f15be2483
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218246"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio-classic"></a>Przewodnik po języku specyfikacji sieci neuronowej w sieci Net# dla usługi Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Net# to język opracowany przez firmę Microsoft, który jest używany do definiowania złożonych architektur sieci neuronowych, takich jak głębokie sieci neuronowe lub sploty dowolnych wymiarów. Za pomocą złożonych struktur można usprawnić uczenie się danych, takich jak obraz, wideo lub dźwięk.

W następujących kontekstach można użyć specyfikacji architektury Net#:

+ Wszystkie moduły sieci neuronowych w programie Microsoft Azure Machine Learning Studio (klasyczny): [Wieloklasowa sieć neuronowa,](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network) [dwuklasowa sieć neuronowa](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network)i [regresja sieci neuronowej](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Funkcje sieci neuronowej w programie Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) i [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)dla języka języka R i [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) dla języka Python.


W tym artykule opisano podstawowe pojęcia i składnię potrzebne do opracowania niestandardowej sieci neuronowej przy użyciu sieci Net#:

+ Wymagania dotyczące sieci neuronowych i jak zdefiniować podstawowe składniki
+ Składnia i słowa kluczowe języka specyfikacji Net#
+ Przykłady niestandardowych sieci neuronowych utworzonych przy użyciu sieci #



## <a name="neural-network-basics"></a>Podstawy sieci neuronowych

Struktura sieci neuronowej składa się z węzłów, które są zorganizowane w warstwy i ważonych połączeń (lub krawędzi) między węzłami. Połączenia są kierunkowe, a każde połączenie ma węzeł źródłowy i węzeł docelowy.

Każda warstwa trainable (warstwa ukryta lub wyjściowa) ma jeden lub więcej **pakietów połączeń**. Pakiet połączeń składa się z warstwy źródłowej i specyfikacji połączeń z tej warstwy źródłowej. Wszystkie połączenia w danym pakiecie współdzielą warstwy źródła i docelowe. W sieci#pakiet połączeń jest uważany za należący do warstwy docelowej pakietu.

Net# obsługuje różne rodzaje pakietów połączeń, które umożliwiają dostosowanie sposobu, w jaki dane wejściowe są mapowane do ukrytych warstw i mapowane na wyjścia.

Pakiet domyślny lub standardowy to **pełny pakiet,** w którym każdy węzeł w warstwie źródłowej jest połączony z każdym węzłem w warstwie docelowej.

Ponadto Net# obsługuje następujące cztery rodzaje zaawansowanych pakietów połączeń:

+ **Filtrowane pakiety**. Predykat można zdefiniować przy użyciu lokalizacji węzła warstwy źródłowej i węzła warstwy docelowej. Węzły są połączone zawsze, gdy predykat jest True.

+ **Wiązki konwolucyjne**. Można zdefiniować małe dzielnice węzłów w warstwie źródłowej. Każdy węzeł w warstwie docelowej jest połączony z jednym sąsiedztwem węzłów w warstwie źródłowej.

+ **Łączenie pakietów** i **pakietów normalizacji odpowiedzi**. Są one podobne do wiązek splotowych, ponieważ użytkownik definiuje małe dzielnice węzłów w warstwie źródłowej. Różnica polega na tym, że ciężary krawędzi w tych wiązkach nie są możliwe do przetrenania. Zamiast tego wstępnie zdefiniowana funkcja jest stosowana do wartości węzła źródłowego w celu określenia wartości węzła docelowego.


## <a name="supported-customizations"></a>Obsługiwane dostosowania

Architektura modeli sieci neuronowych, które można utworzyć w usłudze Azure Machine Learning Studio (klasyczny) można znacznie dostosować przy użyciu net#. Możesz:

+ Tworzenie ukrytych warstw i kontrolowanie liczby węzłów w każdej warstwie.
+ Określ sposób łączenia warstw ze sobą.
+ Zdefiniuj specjalne struktury łączności, takie jak sploty i pakiety podziału wagi.
+ Określ różne funkcje aktywacji.

Aby uzyskać szczegółowe informacje na temat składni języka specyfikacji, zobacz [Specyfikacja struktury](#structure-specifications).

Przykłady definiowania sieci neuronowych dla niektórych typowych zadań uczenia maszynowego, od simplex do złożonych, zobacz [Przykłady](#examples-of-net-usage).

## <a name="general-requirements"></a>Wymagania ogólne

+ Musi istnieć dokładnie jedna warstwa wyjściowa, co najmniej jedna warstwa wejściowa i zero lub więcej warstw ukrytych.
+ Każda warstwa ma stałą liczbę węzłów, koncepcyjnie rozmieszczone w prostokątnej tablicy dowolnych wymiarów.
+ Warstwy wejściowe nie mają skojarzonych parametrów przeszkolonych i reprezentują punkt, w którym dane wystąpienia wchodzą do sieci.
+ Warstwy trainable (warstwy ukryte i wyjściowe) mają skojarzone parametry przeszkolone, znane jako wagi i odchylenia.
+ Węzły źródłowe i docelowe muszą znajdować się w oddzielnych warstwach.
+ Połączenia muszą być acykliczne; innymi słowy nie może istnieć łańcuch połączeń prowadzących z powrotem do początkowego węzła źródłowego.
+ Warstwa wyjściowa nie może być warstwą źródłową pakietu połączeń.

## <a name="structure-specifications"></a>Specyfikacje konstrukcji

Specyfikacja struktury sieci neuronowej składa się z trzech sekcji: **deklaracji stałej,** **deklaracji warstwy,** **deklaracji połączenia.** Istnieje również opcjonalna sekcja **deklaracji udziału.** Sekcje można określić w dowolnej kolejności.

## <a name="constant-declaration"></a>Stała deklaracja

Stała deklaracja jest opcjonalna. Zapewnia środki do definiowania wartości używanych w innych miejscach w definicji sieci neuronowej. Instrukcja deklaracji składa się z identyfikatora, po którym następuje znak równości i wyrażenie wartości.

Na przykład następująca instrukcja `x`definiuje stałą:

`Const X = 28;`

Aby zdefiniować dwie lub więcej stałych jednocześnie, należy ująć nazwy identyfikatorów i wartości w nawiasach klamrowych i oddzielać je za pomocą średników. Przykład:

`Const { X = 28; Y = 4; }`

Po prawej stronie każdego wyrażenia przypisania może być liczba całkowita, liczba rzeczywista, wartość logiczna (Prawda lub Fałsz) lub wyrażenie matematyczne. Przykład:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklaracja warstwy

Wymagana jest deklaracja warstwy. Definiuje rozmiar i źródło warstwy, w tym jej pakiety połączeń i atrybuty. Instrukcja deklaracji rozpoczyna się od nazwy warstwy (dane wejściowe, ukryte lub wyjściowe), po której następują wymiary warstwy (krotka dodatnich liczby całkowitych). Przykład:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Produktem wymiarów jest liczba węzłów w warstwie. W tym przykładzie istnieją dwa wymiary [5,20], co oznacza, że w warstwie znajduje się 100 węzłów.
+ Warstwy mogą być zadeklarowane w dowolnej kolejności, z jednym wyjątkiem: Jeśli zdefiniowano więcej niż jedną warstwę wejściową, kolejność, w jakiej są one zadeklarowane, musi być zgodna z kolejnością operacji w danych wejściowych.

Aby określić, że liczba węzłów w warstwie `auto` zostanie określona automatycznie, użyj słowa kluczowego. Słowo `auto` kluczowe ma różne efekty, w zależności od warstwy:

+ W deklaracji warstwy wejściowej liczba węzłów jest liczbą obiektów w danych wejściowych.
+ W deklaracji warstwy ukrytej liczba węzłów jest liczbą określoną przez wartość parametru **Liczba ukrytych węzłów**.
+ W deklaracji warstwy wyjściowej liczba węzłów wynosi 2 dla klasyfikacji dwuklasowej, 1 dla regresji i równa liczbie węzłów wyjściowych dla klasyfikacji wieloklasowej.

Na przykład następująca definicja sieci umożliwia automatyczne określenie rozmiaru wszystkich warstw:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Deklaracja warstwy dla warstwy trainable (warstw ukrytych lub wyjściowych) może opcjonalnie zawierać funkcję wyjściową (zwaną również funkcją aktywacji), która domyślnie **jest sigmoidowa** dla modeli klasyfikacji i **liniowa** dla modeli regresji. Nawet jeśli używasz wartości domyślnej, można jawnie podać funkcję aktywacji, w razie potrzeby dla jasności.

Obsługiwane są następujące funkcje wyjściowe:

+ Sigmoid
+ Liniowe
+ softmax (softmax)
+ rlinear
+ Placu
+ Sqrt
+ srlinear
+ Abs
+ Tanh
+ brlinear

Na przykład następująca deklaracja używa funkcji **softmax:**

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklaracja połączenia

Natychmiast po zdefiniowaniu warstwy trainable należy zadeklarować połączenia między zdefiniowanymi warstwami. Deklaracja pakietu połączenia rozpoczyna `from`się od słowa kluczowego, po którym następuje nazwa warstwy źródłowej pakietu i rodzaj pakietu połączenia do utworzenia.

Obecnie obsługiwanych jest pięć rodzajów pakietów połączeń:

+ **Pełne** pakiety, oznaczone słowem kluczowym`all`
+ **Filtrowane** pakiety, oznaczone słowem `where`kluczowym, po którym następuje wyrażenie predykatu
+ **Wiązki konwolucyjne,** oznaczone `convolve`słowem kluczowym, po którym następowały atrybuty splotu
+ **Pakiety łączenia,** wskazane przez słowa kluczowe **max puli** lub **średniej puli**
+ **Pakiety normalizacji odpowiedzi,** wskazane przez **normę odpowiedzi** słowa kluczowego

## <a name="full-bundles"></a>Pełne zestawy

Pełny pakiet połączeń zawiera połączenie z każdego węzła w warstwie źródłowej do każdego węzła w warstwie docelowej. Jest to domyślny typ połączenia sieciowego.

## <a name="filtered-bundles"></a>Filtrowane pakiety

Specyfikacja pakietu filtrowanego połączenia zawiera predykat, wyrażony syntaktycznie, podobnie jak wyrażenie lambda języka C#. Poniższy przykład definiuje dwa filtrowane pakiety:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ W predykacie `ByRow` `s` dla , jest parametrem reprezentującym indeks do prostokątnej tablicy `Pixels`węzłów `d` warstwy wejściowej i jest parametrem reprezentującym indeks `ByRow`do tablicy węzłów warstwy ukrytej, . Typ obu `s` i `d` jest krotka liczby całkowite o długości dwóch. Koncepcyjnie, `s` zakresy na wszystkich parach `0 <= s[1] < 20`liczby `d` całkowitej z `0 <= s[0] < 10` i , i waha `0 <= d[0] < 10` `0 <= d[1] < 12`się na wszystkie pary liczby całkowite, z i .

+ Po prawej stronie wyrażenia predykatu znajduje się warunek. W tym przykładzie dla `s` `d` każdej wartości i takie, że warunek jest True, istnieje krawędź z węzła warstwy źródłowej do węzła warstwy docelowej. W związku z tym to wyrażenie filtru wskazuje, `s` że pakiet zawiera `d` połączenie z węzła zdefiniowanego przez węzeł zdefiniowany przez we wszystkich przypadkach, gdy s[0] jest równa d[0].

Opcjonalnie można określić zestaw wag dla filtrowanego pakietu. Wartość atrybutu **Wagi** musi być krotka wartości zmiennoprzecinkowych o długości, która odpowiada liczbie połączeń zdefiniowanych przez pakiet. Domyślnie wagi są generowane losowo.

Wartości wagi są pogrupowane według indeksu węzła docelowego. Oznacza to, że jeśli pierwszy węzeł docelowy jest `K` podłączony do węzłów źródłowych K, pierwsze elementy **krotki Wagi** są wagi dla pierwszego węzła docelowego, w kolejności indeksu źródłowego. To samo dotyczy pozostałych węzłów docelowych.

Możliwe jest określenie wag bezpośrednio jako wartości stałych. Na przykład, jeśli wcześniej poznano wagi, można określić je jako stałe przy użyciu tej składni:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Wiązki konwolucjoniskralne

Gdy dane szkoleniowe mają jednorodną strukturę, połączenia splotowe są powszechnie używane do uczenia się funkcji wysokiego poziomu danych. Na przykład w danych obrazu, audio lub wideo wymiarowość przestrzenna lub czasowa może być dość jednolita.

Wiązki splotowe wykorzystują prostokątne **jądra,** które są przesuwane przez wymiary. Zasadniczo każde jądro definiuje zestaw odważników stosowanych w lokalnych dzielnicach, określanych jako **aplikacje jądra**. Każda aplikacja jądra odpowiada węzłowi w warstwie źródłowej, który jest określany jako **węzeł centralny**. Wagi jądra są współużytkowane przez wiele połączeń. W pakiecie splotowym każde jądro jest prostokątne, a wszystkie aplikacje jądra mają ten sam rozmiar.

Wiązki splotowe obsługują następujące atrybuty:

**InputShape** definiuje wymiarowość warstwy źródłowej na potrzeby tego pakietu splotu. Wartość musi być krotka dodatnich liczby całkowite. Produkt liczb całkowitych musi być równy liczbie węzłów w warstwie źródłowej, ale w przeciwnym razie nie musi być zgodny z wymiarowością zadeklarowanej dla warstwy źródłowej. Długość tej krotki staje się wartością **arity** dla wiązki splotowej. Zazwyczaj arity odnosi się do liczby argumentów lub argumentów, które funkcja może podjąć.

Aby zdefiniować kształt i lokalizacje jąder, użyj atrybutów **KernelShape**, **Stride**, **Padding**, **LowerPad**i **UpperPad**:

+ **KernelShape**: (wymagane) Definiuje wymiarowość każdego jądra dla pakietu splotu. Wartość musi być krotka dodatnich liczby całkowite o długości, która jest równa arity pakietu. Każdy składnik tej krotki nie może być większy niż odpowiedni składnik **InputShape**.

+ **Krok**: (opcjonalnie) Definiuje przesuwane rozmiary kroków splotu (rozmiar jednego kroku dla każdego wymiaru), czyli odległość między węzłami centralnymi. Wartość musi być krotka dodatnich liczby całkowite o długości, która jest arity pakietu. Każdy składnik tej krotki nie może być większy niż odpowiedni składnik **KernelShape**. Wartość domyślna jest krotka ze wszystkimi składnikami równa jeden.

+ **Udostępnianie**: (opcjonalnie) Definiuje podział wagi dla każdego wymiaru splotu. Wartość może być pojedynczą wartością logiczną lub krotą wartości logicznych o długości, która jest wartością pakietu. Pojedyncza wartość logiczna jest rozszerzana, aby była krotkią o prawidłowej długości ze wszystkimi komponentami równymi określonej wartości. Wartość domyślna jest krotka, która składa się ze wszystkich True wartości.

+ **MapCount**: (opcjonalnie) Definiuje liczbę map elementów dla pakietu splotowego. Wartość może być pojedynczą dodatnią całkowitej liczby lub krotki dodatnich liczby całkowite o długości, która jest arity pakietu. Pojedyncza wartość całkowita jest rozszerzana, aby być krotka o prawidłowej długości z pierwszych składników równych określonej wartości i wszystkie pozostałe składniki równe jednemu. Wartość domyślna to jedna. Całkowita liczba map operacji jest iloczynem składników krotki. Faktoring tej całkowitej liczby w składnikach określa sposób grupowania wartości mapy operacji w węzłach docelowych.

+ **Wagi**: (opcjonalnie) Definiuje początkowe wagi dla pakietu. Wartość musi być krotka wartości zmiennoprzecinkowych o długości, która jest liczbą jąder razy liczba wag na jądro, zgodnie z definicją w dalszej części tego artykułu. Domyślne wagi są generowane losowo.

Istnieją dwa zestawy właściwości, które kontrolują dopełnienie, właściwości są wzajemnie się wykluczają:

+ **Dopełnienie**: (opcjonalnie) Określa, czy dane wejściowe powinny być wyściełane przy użyciu **domyślnego schematu dopełniania**. Wartość może być pojedynczą wartością logiczną lub krotka wartości logicznych o długości, która jest arity pakietu.

    Pojedyncza wartość logiczna jest rozszerzana, aby była krotkią o prawidłowej długości ze wszystkimi komponentami równymi określonej wartości.

    Jeśli wartością wymiaru jest Prawda, źródło jest logicznie wyściełane w tym wymiarze za pomocą komórek o zerowej wartości do obsługi dodatkowych aplikacji jądra, tak aby węzły centralne pierwszego i ostatniego jądra w tym wymiarze były pierwszymi i ostatnimi węzłami w tym wymiarze. wymiaru w warstwie źródłowej. W związku z tym liczba węzłów "manekina" w każdym wymiarze jest określana automatycznie, aby zmieścić dokładnie `(InputShape[d] - 1) / Stride[d] + 1` jądra w wyściełanej warstwie źródłowej.

    Jeśli wartość dla wymiaru jest False, jądra są definiowane tak, aby liczba węzłów po każdej stronie, które są pominięte jest taka sama (do różnicy 1). Domyślną wartością tego atrybutu jest krotka ze wszystkimi składnikami równymi False.

+ **UpperPad** i **LowerPad**: (opcjonalnie) Zapewniają większą kontrolę nad ilością dopełnienia do użycia. **Ważne:** Te atrybuty można zdefiniować, jeśli i tylko wtedy, **gdy dopełnienie** właściwość powyżej ***nie*** jest zdefiniowana. Wartości powinny być krotek wartości całkowitej o długościach, które są arity pakietu. Po określeniu tych atrybutów węzły "manekina" są dodawane do dolnej i górnej części każdego wymiaru warstwy wejściowej. Liczba węzłów dodanych do dolnych i górnych końcówek w każdym wymiarze jest określana odpowiednio przez **LowerPad**[i] i **UpperPad**[i].

    Aby upewnić się, że jądra odpowiadają tylko "rzeczywistym" węzłom, a nie "fikcyjnym" węzłom, muszą być spełnione następujące warunki:
  - Każdy element **LowerPad** musi być `KernelShape[d]/2`znacznie mniejszy niż .
  - Każdy element **UpperPad** nie może `KernelShape[d]/2`być większy niż .
  - Domyślną wartością tych atrybutów jest krotka ze wszystkimi składnikami równymi 0.

    Ustawienie **Padding** = true pozwala na tyle dopełnienie, jak jest to potrzebne do utrzymania "centrum" jądra wewnątrz "prawdziwe" wejście. Spowoduje to zmianę nieco matematyki do obliczania rozmiaru danych wyjściowych. Ogólnie rzecz biorąc, *D* rozmiar wyjściowy `D = (I - K) / S + 1`D `I` jest obliczany `K` jako , gdzie `S` jest rozmiar `/` wejściowy, jest rozmiarem jądra, jest krokiem i jest podziałem całkowitym (okrągły w kierunku zera). Jeśli ustawisz UpperPad = [1, `I` 1], rozmiar wejściowy `D = (29 - 5) / 2 + 1 = 13`jest skutecznie 29, a zatem . Jednak, gdy **Padding** = `I` true, zasadniczo dostaje podbił przez `K - 1`; stąd `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Określając wartości dla **UpperPad** i **LowerPad** masz znacznie większą kontrolę nad dopełnieniem niż jeśli po prostu **ustawisz Padding** = true.

Aby uzyskać więcej informacji na temat sieci splotowych i ich zastosowań, zobacz następujące artykuły:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Pakiety pulowe

**Pakiet puli** stosuje geometrię podobną do łączności splotowej, ale używa wstępnie zdefiniowanych funkcji do wartości węzła źródłowego w celu uzyskania wartości węzła docelowego. W związku z tym łączenie pakietów nie mają stanu trainable (wagi lub uprzedzeń). Pakiety pulowe obsługują wszystkie atrybuty splotowe z wyjątkiem **udostępniania,** **mapcount**i **wag.**

Zazwyczaj jądra podsumowane przez sąsiednie jednostki buforowania nie nakładają się na siebie. Jeśli Stride[d] jest równa KernelShape[d] w każdym wymiarze, uzyskana warstwa jest tradycyjną lokalną warstwą buforowania, która jest często stosowana w splotowych sieciach neuronowych. Każdy węzeł docelowy oblicza maksymalną lub średnią działań jego jądra w warstwie źródłowej.

Poniższy przykład ilustruje pakiet puli:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Arity pakietu jest 3: to znaczy długość krotek `InputShape`, `KernelShape`i `Stride`.
+ Liczba węzłów w warstwie `5 * 24 * 24 = 2880`źródłowej to .
+ Jest to tradycyjna warstwa buforowania lokalnego, ponieważ **KernelShape** i **Stride** są równe.
+ Liczba węzłów w warstwie `5 * 12 * 12 = 1440`docelowej to .

Aby uzyskać więcej informacji na temat buforowania warstw, zobacz następujące artykuły:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)(Sekcja 3.4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Pakiety normalizacji odpowiedzi

**Normalizacja odpowiedzi** jest lokalnym schematem normalizacji, który został po raz pierwszy wprowadzony przez Geoffreya Hintona, et al, w artykule [ImageNet Classification with Deep Convolutional Neural Networks](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Normalizacja odpowiedzi służy do wspomagania uogólnienia w sieciach nerwowych. Kiedy jeden neuron jest wypalanie na bardzo wysokim poziomie aktywacji, warstwy normalizacji lokalnej odpowiedzi hamuje poziom aktywacji otaczających neuronów. Odbywa się to za`α`pomocą `β`trzech `k`parametrów ( , , i ) i struktury splotowej (lub kształtu sąsiedztwa). Każdy neuron w warstwie **docelowej y** odpowiada **neuron** x w warstwie źródłowej. Poziom aktywacji **y** jest podany przez `f` następującą formułę, gdzie `Nx` jest poziom aktywacji neuronu, i jest jądrem (lub zestawem, który zawiera neurony w sąsiedztwie **x**), zgodnie z definicją następującej struktury splotowej:

![wzór dla struktury splotowej](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Pakiety normalizacji odpowiedzi obsługują wszystkie atrybuty splotowe z wyjątkiem **udostępniania,** **mapcount**i **wag.**

+ Jeśli jądro zawiera neurony na tej samej mapie co ***x,*** schemat normalizacji jest określany jako **ta sama normalizacja mapy.** Aby zdefiniować tę samą normalizację mapy, pierwsza współrzędna w **InputShape** musi mieć wartość 1.

+ Jeśli jądro zawiera neurony w tej samej pozycji przestrzennej co ***x***, ale neurony znajdują się na innych mapach, schemat normalizacji jest nazywany **przez normalizację map.** Ten typ normalizacji odpowiedzi implementuje formę bocznego hamowania zainspirowany typem występującym w prawdziwych neuronach, tworząc konkurencję dla dużych poziomów aktywacji wśród wyjść neuronów obliczonych na różnych mapach. Aby zdefiniować normalizację map, pierwsza współrzędna musi być liczbą całkowitą większą niż jedna i nie większą niż liczba map, a reszta współrzędnych musi mieć wartość 1.

Ponieważ pakiety normalizacji odpowiedzi stosuje wstępnie zdefiniowaną funkcję do wartości węzła źródłowego w celu określenia wartości węzła docelowego, nie mają stanu trainable (wagi lub odchylenia).

> [!NOTE]
> Węzły w warstwie docelowej odpowiadają neuronom, które są centralnymi węzłami jąder. Na przykład `KernelShape[d]` jeśli jest `KernelShape[d]/2` nieparzyste, a następnie odpowiada węzła centralnego jądra. Jeśli `KernelShape[d]` jest parzyste, `KernelShape[d]/2 - 1`węzeł centralny znajduje się w punkcie . W związku `Padding[d]` z tym jeśli jest `KernelShape[d]/2` False, pierwszy i ostatni węzły nie mają odpowiednich węzłów w warstwie docelowej. Aby uniknąć tej sytuacji, zdefiniuj **Padding** jako [true, true, ..., true].

Oprócz czterech atrybutów opisanych wcześniej, pakiety normalizacji odpowiedzi obsługują również następujące atrybuty:

+ **Alfa**: (wymagane) Określa wartość zmiennoprzecinkową, która odpowiada `α` w poprzedniej formule.
+ **Beta**: (wymagane) Określa wartość zmiennoprzecinkową, która odpowiada `β` w poprzedniej formule.
+ **Przesunięcie**: (opcjonalnie) Określa wartość zmiennoprzecinkowa, która odpowiada `k` poprzedniej formule. Domyślnie wartość 1.

Poniższy przykład definiuje pakiet normalizacji odpowiedzi przy użyciu tych atrybutów:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ Warstwa źródłowa zawiera pięć map, z których każda ma wymiar aof 12x12, w sumie w 1440 węzłach.
+ Wartość **KernelShape** wskazuje, że jest to ta sama warstwa normalizacji mapy, gdzie sąsiedztwo jest prostokątem 3x3.
+ Domyślną wartością **dopełnienia** jest False, w związku z tym warstwa docelowa ma tylko 10 węzłów w każdym wymiarze. Aby uwzględnić jeden węzeł w warstwie docelowej, który odpowiada każdemu węzłowi w warstwie źródłowej, dodaj dopełnienie = [true, true, true]; i zmienić rozmiar RN1 na [5, 12, 12].

## <a name="share-declaration"></a>Deklaracja akcji

Net# opcjonalnie obsługuje definiowanie wielu pakietów z współużytkowaniami. Wagi dowolnych dwóch wiązek mogą być współdzielone, jeśli ich struktury są takie same. Następująca składnia definiuje pakiety z wagami współdzielonymi:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

Na przykład następująca deklaracja udziału określa nazwy warstw, wskazując, że zarówno wagi, jak i odchylenia powinny być współużytkowane:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ Funkcje wejściowe są podzielone na dwie warstwy wejściowe o równych rozmiarach.
+ Ukryte warstwy obliczają następnie operacje wyższego poziomu na dwóch warstwach wejściowych.
+ Deklaracja udziału określa, że *H1* i *H2* muszą być obliczane w taki sam sposób z ich odpowiednich danych wejściowych.

Alternatywnie można to określić za pomocą dwóch oddzielnych deklaracji udziału w następujący sposób:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Krótkiego formularza można używać tylko wtedy, gdy warstwy zawierają pojedynczy pakiet. Ogólnie rzecz biorąc udostępnianie jest możliwe tylko wtedy, gdy odpowiednia struktura jest identyczna, co oznacza, że mają ten sam rozmiar, taką samą geometrię splotową i tak dalej.

## <a name="examples-of-net-usage"></a>Przykłady użycia net#

W tej sekcji przedstawiono kilka przykładów, w jaki sposób można użyć net# do dodawania ukrytych warstw, definiowania sposobu interakcji ukrytych warstw z innymi warstwami i tworzenia sieci splotowych.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Zdefiniuj prostą niestandardową sieć neuronową: przykład "Hello World"

W tym prostym przykładzie pokazano, jak utworzyć model sieci neuronowej, który ma jedną ukrytą warstwę.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

Przykład ilustruje niektóre podstawowe polecenia w następujący sposób:

+ Pierwszy wiersz definiuje warstwę wejściową (o nazwie `Data`). Podczas korzystania `auto` ze słowa kluczowego sieć neuronowa automatycznie zawiera wszystkie kolumny funkcji w przykładach wejściowych.
+ Druga linia tworzy warstwę ukrytą. Nazwa `H` jest przypisana do warstwy ukrytej, która ma 200 węzłów. Warstwa ta jest w pełni połączona z warstwą wejściową.
+ Trzecia linia definiuje warstwę wyjściową (nazwaną `Out`), która zawiera 10 węzłów wyjściowych. Jeśli sieć neuronowa jest używana do klasyfikacji, istnieje jeden węzeł wyjściowy na klasę. **Sigmoid** słowa kluczowego wskazuje, że funkcja wyjściowa jest stosowana do warstwy wyjściowej.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definiowanie wielu ukrytych warstw: przykład widzenia komputerowego

W poniższym przykładzie pokazano, jak zdefiniować nieco bardziej złożoną sieć neuronową z wieloma niestandardowymi ukrytymi warstwami.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

W tym przykładzie przedstawiono kilka funkcji języka specyfikacji sieci neuronowych:

+ Struktura składa się z `Pixels` `MetaData`dwóch warstw wejściowych i .
+ Warstwa `Pixels` jest warstwą źródłową dla dwóch wiązek połączeń, z warstwami docelowymi `ByRow` i `ByCol`.
+ Warstwy `Gather` `Result` i są warstwami docelowymi w wielu pakietach połączeń.
+ Warstwa wyjściowa `Result`, jest warstwą docelową w dwóch pakietach połączeń; jeden z warstwą `Gather` ukrytą drugiego poziomu jako warstwą `MetaData` docelową, a drugi z warstwą wejściową jako warstwą docelową.
+ Ukryte warstwy `ByRow` `ByCol`i , określ przefiltrowane łączności przy użyciu wyrażeń predykatu. Dokładniej rzecz ujmując, węzeł w `ByRow` [x, y] jest połączony z węzłami, w `Pixels` których mają pierwszą współrzędne indeksu równą pierwszej współrzędnej węzła, x. Podobnie węzeł w `ByCol` [x, y] jest połączony z `Pixels` węzłami, w których mają drugą współrzędne indeksu w obrębie jednej z drugiej współrzędnej węzła, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definiowanie sieci splotowej dla klasyfikacji wieloklasowej: przykład rozpoznawania cyfr

Definicja następującej sieci jest przeznaczona do rozpoznawania liczb i ilustruje niektóre zaawansowane techniki dostosowywania sieci neuronowej.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ Struktura ma jedną warstwę `Image`wejściową, .
+ Słowo `convolve` kluczowe wskazuje, że `Conv1` `Conv2` warstwy nazwane i są warstwami splotowymi. Po każdej z tych deklaracji warstw następuje lista atrybutów splotu.
+ Sieć ma trzecią ukrytą `Hid3`warstwę, która jest w `Conv2`pełni połączona z drugą ukrytą warstwą, .
+ Warstwa wyjściowa , `Digit`jest połączona tylko `Hid3`z trzecią warstwą ukrytą, . Słowo `all` kluczowe wskazuje, że warstwa `Hid3`wyjściowa jest w pełni połączona z programem .
+ Arity splotu jest trzy: długość krotek `InputShape`, `KernelShape`, `Stride`, `Sharing`i .
+ Liczba odważników na jądro `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`wynosi . Lub `26 * 50 = 1300`.
+ Węzły w każdej warstwie ukrytej można obliczyć w następujący sposób:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Całkowitą liczbę węzłów można obliczyć przy użyciu deklarowanej wymiarowości warstwy [50, 5, 5], 5], w następujący sposób:`MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Ponieważ `Sharing[d]` jest false `d == 0`tylko dla , liczba `MapCount * NodeCount\[0] = 10 * 5 = 50`jąder jest .

## <a name="acknowledgements"></a>Podziękowania

Język Net# do dostosowywania architektury sieci neuronowych został opracowany w firmie Microsoft przez Shona Katzenbergera (Architekt, Uczenie maszynowe) i Aleksieja Kameneva (Inżynier oprogramowania, Microsoft Research). Jest on używany wewnętrznie dla projektów uczenia maszynowego i aplikacji, począwszy od wykrywania obrazu do analizy tekstu. Aby uzyskać więcej informacji, zobacz [Sieci neuronowe w studio usługi Azure Machine Learning — wprowadzenie do sieci#](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
