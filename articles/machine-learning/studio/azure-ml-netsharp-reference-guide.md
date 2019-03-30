---
title: 'Tworzenie niestandardowych sieci neuronowych przy użyciu Net #'
titleSuffix: Azure Machine Learning Studio
description: 'Przewodnik po składni języka specyfikacji sieci neuronowych Net #. Dowiedz się, jak tworzyć modele sieci neuronowych niestandardowej w usłudze Azure Machine Learning Studio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: 891b2988d04a3cf2f7c6676a837bc1ee199f4d16
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651495"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio"></a>Przewodnik po języku specyfikacji sieci neuronowych Net # dla usługi Azure Machine Learning Studio

NET # to opracowany przez firmę Microsoft, który jest używany do definiowana architektur złożonych sieci neuronowych, takich jak głębokich sieciach neuronowych lub splotowi wymiary dowolnego języka. Złożone struktury umożliwia poprawę nauki o danych, takich jak obraz, wideo lub audio.

Można użyć specyfikacji Net # architektury w tych kontekstach:

+ Wszystkie moduły sieć neuronowa firmy Microsoft Azure Machine Learning Studio: [Sieć Neuronowa wieloklasowej](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [sieci neuronowych Two-Class](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), i [regresji sieci neuronowych](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Funkcje sieci neuronowych w Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) i [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)dla języka R i [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) dla języka Python.


W tym artykule opisano podstawowe pojęcia i potrzebne do tworzenia niestandardowych sieci neuronowych, przy użyciu Net # składni:

+ Wymagania dotyczące sieci neuronowe oraz sposób definiowania podstawowe składniki
+ Składni i słów kluczowych języka specyfikacji Net #
+ Przykładów niestandardowych sieci neuronowych utworzone za pomocą Net #



## <a name="neural-network-basics"></a>Podstawowe informacje o sieci neuronowych

Struktura sieci neuronowych składa się z węzłów, które są organizowane w warstwy i ważona połączenia (lub krawędzi) między węzłami. Połączenia są kierunkowe, a każde połączenie ma węzeł źródłowy oraz węzeł docelowy.

Każda warstwa trainable (ukryty lub warstwę danych wyjściowych) ma jeden lub kilka **pakiety połączenia**. Pakiet połączenia składa się z warstwy źródłowej i określenie połączenia z tym warstwy źródłowej. Wszystkich połączeń z danym pakietem Udostępnij źródłowe i docelowe warstwy. W Net # pakietu połączenia jest traktowane jako należące do warstwy docelowej przez pakiet.

NET # obsługuje różne rodzaje połączenia pakietów, które można dostosować sposób dane wejściowe są mapowane na ukrytych warstw i mapowane na dane wyjściowe.

Domyślne lub standardowy pakiet jest **pełny pakiet**, w którym każdego węzła w warstwie źródłowej jest podłączony do każdego węzła w warstwie docelowej.

Ponadto Net # obsługuje cztery rodzaje pakietów zaawansowane połączenie:

+ **Filtrowane pakiety**. Predykat można zdefiniować przy użyciu lokalizacji warstwy węzeł źródłowy i węzeł warstwy docelowej. Węzły są połączone w każdym przypadku, gdy predykatu ma wartość True.

+ **Pakiety splotowe**. Można zdefiniować małych kluby węzłów z warstwy źródłowej. Każdy węzeł w warstwie docelowej jest połączony co otoczenia węzłów z warstwy źródłowej.

+ **Buforowanie pakietów** i **pakiety normalizacji odpowiedzi**. Te przypominają splotowe pakietów w tym, że użytkownik definiuje małych kluby węzłów z warstwy źródłowej. Różnica polega na tym, że wagi krawędzie w te pakiety nie są trainable. Zamiast tego wstępnie zdefiniowana funkcja jest stosowane do wartości węzeł źródła, aby określić wartość węzła docelowego.


## <a name="supported-customizations"></a>Obsługiwane dostosowania

Architektura modele sieci neuronowych, które możesz utworzyć w usłudze Azure Machine Learning Studio można dostosować często przy użyciu Net #. Możesz:

+ Tworzenie warstwy ukryte i kontrolować liczbę węzłów w każdej warstwie.
+ Określ, jak mają być połączone ze sobą warstwy.
+ Zdefiniować struktury łączności specjalnych, takich jak splotowi i wagi udostępnianie pakietów.
+ Określ Aktywacja innej funkcji.

Aby uzyskać szczegółowe informacje o składni specyfikacja języka, zobacz [specyfikacji struktury](#structure-specifications).

Definiowanie sieci neuronowych dla niektórych typowych maszyny uczenia zadań, od jednostronny złożone, zobacz [przykłady](#examples-of-net-usage).

## <a name="general-requirements"></a>Wymagania ogólne

+ Musi istnieć dokładnie jeden wyjściowy warstwy, co najmniej jednej warstwy wejściowych i zero lub więcej ukrytych warstw.
+ Każda warstwa ma stałą liczbę węzłów, koncepcyjnie rozmieszczone w prostokątny tablicę dowolnego wymiarów.
+ Warstwy danych wejściowych nie może mieć skojarzone uczonego parametrów i reprezentuje punkt, w którym dane wystąpienia wprowadza sieci.
+ Trainable warstwy (warstwy ukrytych i dane wyjściowe) mieć skojarzone parametry przeszkolonych, znane jako wagi oraz tendencje.
+ Węzły źródłowe i docelowe muszą być w oddzielne warstwy.
+ Połączenia muszą być acykliczne; innymi słowy nie może być łańcuch połączeń, co prowadzi do węzła źródłowej.
+ Warstwa danych wyjściowych nie może być warstwy źródłowej pakietu połączenia.

## <a name="structure-specifications"></a>Specyfikacje struktury

Specyfikacja struktury sieci neuronowych składa się z trzy sekcje: **deklaracji stałej**, **warstwy deklaracji**, **deklaracji połączenia**. Istnieje również opcjonalny **udostępnianie deklaracji** sekcji. Sekcje można określić w dowolnej kolejności.

## <a name="constant-declaration"></a>Deklaracji stałej

Deklaracja stałej jest opcjonalne. Zapewnia środek do definiowania wartości używane w innych miejscach w definicji sieci neuronowych. Instrukcji deklaracji składa się z identyfikatora następuje znak równości i wyrażenie wartości.

Na przykład następująca instrukcja definiuje stałą `x`:

`Const X = 28;`

Aby zdefiniować dwa lub więcej stałych jednocześnie, należy ująć identyfikator nazwy i wartości w nawiasach klamrowych i rozdzielając je średnikami. Na przykład:

`Const { X = 28; Y = 4; }`

Po prawej stronie wyrażenia przypisania, każdy może być liczba całkowita, liczba rzeczywista, wartość logiczna (True lub False) lub wyrażenie matematyczne. Na przykład:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklaracja warstwy

Deklaracja warstwa jest wymagana. Definiuje rozmiar i źródło warstwy, łącznie z połączenia pakiety i ich atrybuty. Instrukcji deklaracji zaczyna się od nazwy warstwy (dane wejściowe, ukryte lub danych wyjściowych) następuje wymiary warstwy (krotki dodatnie liczby całkowite). Na przykład:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Produkt wymiarów jest liczba węzłów w warstwie. W tym przykładzie istnieją dwa wymiary [5,20], co oznacza, że nie ma 100 węzłów na warstwie.
+ Warstwy mogą być deklarowane w dowolnej kolejności, z jednym wyjątkiem: Jeśli nie zdefiniowano więcej niż jednej warstwy wejściowych, kolejność, w którym są one zadeklarowane musi być zgodna z kolejność funkcji w danych wejściowych.

Aby określić, że liczba węzłów w warstwie ustalana automatycznie, użyj `auto` — słowo kluczowe. `auto` — Słowo kluczowe ma różne efekty w zależności od warstwy:

+ W deklaracji warstwy wejściowych liczba węzłów jest liczba funkcji w danych wejściowych.
+ W deklaracji ukrytej warstwie według następującego, liczba węzłów to numer, który jest określony przez wartość parametru **Liczba ukrytych węzłów**.
+ W deklaracji warstwy danych wyjściowych liczba węzłów to 2 dwuklasowych klasyfikacji, 1 dla regresji i różne liczby węzłów danych wyjściowych dla klasyfikacji wieloklasowej.

Na przykład następująca definicja sieci umożliwia rozmiar wszystkich warstw określone automatycznie:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Deklaracja warstwy trainable warstwy (warstwy ukryte lub wyjściowe) może opcjonalnie obejmować dane wyjściowe funkcji (nazywanych również funkcję aktywacji), która domyślnie **sigmoid** dla modeli klasyfikacji i  **liniowy** dla modele regresji. Nawet wtedy, gdy użytkownik korzysta z domyślnych można jawnie stanu aktywacji funkcji, jeśli to konieczne w celu uściślenia.

Obsługiwane są następujące funkcje danych wyjściowych:

+ sigmoid
+ Liniowy
+ softmax
+ rlinear
+ Kwadrat
+ sqrt
+ srlinear
+ ABS
+ TANH
+ brlinear

Na przykład następująca deklaracja używa **softmax** funkcji:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklaracja połączenia

Natychmiast po definiowania trainable warstwy, należy zadeklarować połączeń między warstwami, które zostały zdefiniowane. Deklaracja pakietu połączenia zaczyna się od słowa kluczowego `from`, a następnie według nazwy rodzaj połączenia pakietu, aby utworzyć i warstw źródła pakietu.

Obecnie są obsługiwane pięć rodzajów pakietów połączenia:

+ **Pełna** pakiety, wskazywanym przez słowo kluczowe `all`
+ **Filtrowane** pakiety, wskazywanym przez słowo kluczowe `where`, a następnie wyrażenie predykatu
+ **Splotowe** pakiety, wskazywanym przez słowo kluczowe `convolve`, a następnie atrybuty konwolucji
+ **Buforowanie** pakiety, wskazywanym przez słowa kluczowe **maksymalnej liczby puli** lub **oznacza puli**
+ **Normalizacja odpowiedzi** pakiety, wskazywanym przez słowo kluczowe **norm odpowiedzi**

## <a name="full-bundles"></a>Pełne pakiety

Pakiet pełne połączenie obejmuje połączenie z każdego węzła w warstwie źródła do każdego węzła w warstwie docelowej. Jest to domyślny typ połączenia sieciowego.

## <a name="filtered-bundles"></a>Filtrowane pakiety

Specyfikacja pakietu połączenia filtrowanego obejmuje predykatu, wyrażona syntaktycznie, bardzo podobnie jak C# wyrażenia lambda. W poniższym przykładzie zdefiniowano dwa filtrowanych pakietów:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ W predykacie dla `ByRow`, `s` parametr reprezentujący indeks do tablicy prostokątny węzłów wejściowych warstwy `Pixels`, i `d` parametr reprezentujący indeks do tablicy węzłów w ukrytej warstwie `ByRow`. Typem obu metod `s` i `d` jest spójną kolekcją liczb całkowitych o długości dwóch. Model `s` zakresów za pośrednictwem wszystkich par liczb całkowitych z `0 <= s[0] < 10` i `0 <= s[1] < 20`, i `d` zakresów za pośrednictwem wszystkich par liczb całkowitych, za pomocą `0 <= d[0] < 10` i `0 <= d[1] < 12`.

+ Po prawej stronie wyrażenie predykatu występuje warunek. W tym przykładzie dla każdej wartości `s` i `d` taki sposób, że warunek ma wartość True, jest krawędź z węzła źródłowego warstwy do węzła warstwy docelowej. W związku z tym, to wyrażenie filtru wskazuje, że pakiet zawiera połączenia z węzła zdefiniowane przez `s` do węzła zdefiniowane przez `d` we wszystkich przypadkach, gdzie s [0] jest równa d [0].

Opcjonalnie można określić zbiór wag dla filtrowanego pakietu. Wartość **wagi** atrybut musi być spójna Kolekcja o długości, która jest zgodna z liczbą połączeń definiowane przez pakiet wartości zmiennoprzecinkowych. Domyślnie generowany losowo wagi.

Wartości wag są pogrupowane według indeksu węzła docelowego. Oznacza to, jeśli pierwszy węzeł docelowy jest połączony z węzłów źródłowych K, pierwszy `K` elementy **wagi** krotki są wag dla pierwszego węzła docelowego w kolejności indeksowania źródła. To samo dotyczy dla pozostałych węzłów docelowego.

Istnieje możliwość określenia wagi bezpośrednio jako wartości stałych. Na przykład jeśli znasz wag wcześniej, można je określić jako stałe przy użyciu następującej składni:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Pakiety splotowe

Dane szkoleniowe po jednorodnych struktury splotowe połączenia są często używane się funkcje wysokiego poziomu danych. Na przykład na ilustracji, dane audio lub wideo, wymiary przestrzenne lub danych czasowych może być dość jednolite.

Pakiety splotowe stosować prostokątny **jądra** , są przesuwać za pośrednictwem wymiarów. Zasadniczo każdy jądra definiuje zestaw wagi stosowane w lokalnym kluby nazywane **aplikacji jądra**. Każda aplikacja jądra odnosi się do węzła w warstwie źródła, które są określone jako **węźle centralnym**. Wagi jądra są współużytkowane przez wiele połączeń. W pakiecie splotowe każdy jądra jest prostokątnym zestawem i wszystkie aplikacje jądra mają taki sam rozmiar.

Pakiety splotowe obsługują następujące atrybuty:

**InputShape** definiuje wymiarach warstwy źródłowej na potrzeby tego splotowe pakietu. Wartość musi być spójna kolekcja dodatnie liczby całkowite. Iloczyn liczby całkowite musi być równa liczby węzłów w warstwie źródła, ale w przeciwnym razie nie trzeba ją wymiarowością zadeklarowany dla warstwy źródłowej. Długość tego krotki staje się **specifikaci** wartość splotowe pakietu. Zazwyczaj specifikaci odnosi się do liczby argumentów lub argumentów operacji, które można wykonać funkcji.

Aby zdefiniować kształt i lokalizacje jądra, należy użyć atrybutów **KernelShape**, **Stride**, **dopełnienie**, **LowerPad**i  **UpperPad**:

+ **KernelShape**: definiuje (wymagane), o wymiarach każdego jądra dla splotowe pakietu. Wartość musi być spójna kolekcja dodatnimi liczbami całkowitymi o długości, która jest równa liczbie argumentów pakietu. Każdy składnik to krotki musi być większa niż odpowiadający składnik **InputShape**.

+ **STRIDE**: (opcjonalnie) określa krok przewijania ilości konwolucji (rozmiar jeden krok dla każdego wymiaru), jest odległość między węzłami centralnej. Wartość musi być spójna kolekcja dodatnimi liczbami całkowitymi o długości, która jest liczba argumentów pakietu. Każdy składnik to krotki musi być większa niż odpowiadający składnik **KernelShape**. Wartość domyślna to krotki ze wszystkimi składnikami równa jeden.

+ **Udostępnianie**: (opcjonalnie) określa grubość udostępniania dla każdego wymiaru konwolucji. Wartość może być pojedynczą wartością logiczną lub krotki wartości logicznych o długości, która jest liczba argumentów pakietu. Pojedynczą wartość logiczną jest rozszerzony do krotki o długości poprawne ze wszystkimi składnikami równa określonej wartości. Wartość domyślna to spójną kolekcją, która składa się ze wszystkich wartości True.

+ **MapCount**: (opcjonalnie) definiuje liczbę funkcji mapy dla splotowe pakietu. Wartość może być jednym dodatnią liczbą całkowitą lub krotki dodatnich liczb całkowitych o długości, która jest liczba argumentów pakietu. Jedną wartość całkowitą jest rozszerzony do krotki o długości poprawne ze składnikami pierwszy równa określonej wartości, a wszystkie pozostałe składniki równa jeden. Wartość domyślna to jeden. Całkowita liczba funkcji mapy jest wynikiem składniki spójnej kolekcji. Wyprowadzenie to całkowita liczba składników decyduje o tym, jak wartości map funkcji są zgrupowane w węzłach docelowego.

+ **Wagi**: (opcjonalnie) Określa początkowy wag dla pakietu. Wartość musi być spójna Kolekcja wartości zmiennoprzecinkowych o długości, która jest liczba przypadków jądra liczba wagi za pośrednictwem jądra, zgodnie z definicją w dalszej części tego artykułu. Generowany losowo domyślnych wag.

Istnieją dwa zestawy właściwości, które kontrolują dopełnienie, właściwości jest wzajemnie wykluczających się:

+ **Dopełnienie**: (opcjonalnie) określa czy dane wejściowe powinny dopełniane przy użyciu **domyślny schemat dopełnienie**. Wartość może być pojedynczą wartość logiczną lub może być krotka z wartościami logicznymi o długości, która jest liczba argumentów pakietu.

    Pojedynczą wartość logiczną jest rozszerzony do krotki o długości poprawne ze wszystkimi składnikami równa określonej wartości.

    Jeśli wartości dla wymiaru ma wartość PRAWDA, źródło logicznie jest dopełniana w tym wymiarze z komórek o wartości zero do obsługi aplikacji dodatkowe jądra, której centralna węzły jądra imię i nazwisko, w tym wymiarze węzłów imię i nazwisko, w tym wymiar z warstwy źródłowej. W związku z węzłów "fikcyjny" w każdym wymiarze jest określana automatycznie, aby dokładnie dopasować `(InputShape[d] - 1) / Stride[d] + 1` jądra na warstwę wypełniony źródła.

    Jeśli wartości dla wymiaru ma wartość False, jądra są zdefiniowane tak, aby liczba węzłów na każdej stronie, która pozostawiono takie same (maksymalnie różnica 1). Wartość domyślna tego atrybutu to krotki ze wszystkimi składnikami równa False.

+ **UpperPad** i **LowerPad**: (opcjonalnie) Podaj większą kontrolę nad wielkość uzupełnienia do użycia. **Ważne:** Te atrybuty mogą być zdefiniowane wtedy i tylko wtedy, gdy **dopełnienie** powyższej właściwości jest ***nie*** zdefiniowane. Wartość musi być wartości całkowite krotki o długości, które są liczby argumentów pakietu. Jeśli te atrybuty są określone, "fikcyjny" węzłów są dodawane do dolnym i górnym numerem zakończenia każdego wymiaru warstwy danych wejściowych. Liczba węzłów dodanych do punktów końcowych dolnym i górnym numerem w każdym wymiarze jest określana przez **LowerPad**[i] i **UpperPad**[i] odpowiednio.

    Aby upewnić się, że jądra odpowiadają tylko dla węzłów "członu real", a nie węzłów "fikcyjny", muszą być spełnione następujące warunki:
  - Każdy składnik **LowerPad** musi być mniejsza niż `KernelShape[d]/2`.
  - Każdy składnik **UpperPad** musi być większa niż `KernelShape[d]/2`.
  - Wartość domyślna z tych atrybutów jest krotki ze wszystkimi składnikami równa 0.

    Ustawienie **dopełnienie** = true zezwala na tyle dopełnienie potrzeb jest zapewnienie "Centrum" jądra wewnątrz "prawdziwy" danych wejściowych. Spowoduje to zmianę matematyczne nieco dotyczących przeprowadzania obliczeń rozmiar danych wyjściowych. Ogólnie rzecz biorąc, rozmiar danych wyjściowych *D* jest obliczana jako `D = (I - K) / S + 1`, gdzie `I` jest rozmiar wejściowy `K` jądra, ma rozmiar `S` jest stride, i `/` jest dzielenie liczby całkowitej (zaokrąglony w kierunku zera ). Jeśli ustawisz UpperPad = [1, 1], rozmiar danych wejściowych `I` jest faktycznie 29 i w związku z tym `D = (29 - 5) / 2 + 1 = 13`. Jednak, gdy **dopełnienie** = true, zasadniczo `I` pobiera zaktualizowany w przez `K - 1`; dlatego `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Określając wartości dla **UpperPad** i **LowerPad** uzyskać większą kontrolę nad uzupełnienie niż w przypadku po prostu **dopełnienie** = true.

Aby uzyskać więcej informacji na temat splotowych sieci i aplikacji zobacz następujące artykuły:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Buforowanie pakietów

A **buforowanie pakietu** stosuje podobne do łączności z splotowe geometrii, ale używa funkcji wstępnie zdefiniowane wartości węzła źródłowego do uzyskania wartości węzła docelowego. Z tego powodu buforowania pakiety mają nie trainable stanu (wagi lub odchyleń). Korzystanie z puli, pakiety obsługuje wszystkie splotowe atrybutów, z wyjątkiem **udostępniania**, **MapCount**, i **wagi**.

Zazwyczaj jądra podsumowane według sąsiadujących jednostek buforowania nie pokrywają się. Jeśli krok [d] jest równa KernelShape [d] w każdym wymiarze, warstwy uzyskane jest tradycyjnych lokalnych warstwy buforowania, która jest powszechnie wykorzystywane w splotowych sieci neuronowych. Każdy węzeł docelowy oblicza maksymalną lub średniej działania jego jądra z warstwy źródłowej.

Poniższy przykład ilustruje buforowania pakietu:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Liczba ról pakietu jest 3: oznacza to, że długość kolekcje `InputShape`, `KernelShape`, i `Stride`.
+ Liczba węzłów w warstwie źródłowej jest `5 * 24 * 24 = 2880`.
+ Jest to tradycyjne lokalne warstwy buforowania, ponieważ **KernelShape** i **Stride** są takie same.
+ Liczba węzłów w warstwie docelowej jest `5 * 12 * 12 = 1440`.

Aby uzyskać więcej informacji na temat warstwy buforowania zobacz następujące artykuły:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sekcji 3.4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Pakiety normalizacji odpowiedzi

**Normalizacja odpowiedzi** jest systemem normalizacji lokalnej, która została wprowadzona przez Geoffrey Hinton i inni w dokumencie [Klasyfikacja sieci ImageNet przy użyciu Splotowych sieci neuronowych głębokiego](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Odpowiedź normalizacji jest używana do pomocy Generalizacja w sieci neuronowych. Gdy jeden neuronu jest uruchamiana na poziomie aktywacji bardzo duże, warstwy normalizacji lokalnego odpowiedzi pomija poziom aktywacji otaczające grupy neuronów. Jest to wykonywane przy użyciu trzech parametrów (`α`, `β`, i `k`) i splotowe struktury (lub kształtu otoczenia). Każdy neuronu warstwy docelowej **y** odpowiada neuronu **x** z warstwy źródłowej. Poziom aktywacji **y** znajduje się za pomocą następującego wzoru gdzie `f` jest poziom aktywacji neuronu, i `Nx` jądro (lub zestaw, który zawiera grupy neuronów w otoczeniu z **x**), zgodnie z następującą strukturę splotowe:

![Formuła splotowe struktury](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Pakiety normalizacji odpowiedzi obsługuje wszystkie splotowe atrybutów, z wyjątkiem **udostępniania**, **MapCount**, i **wagi**.

+ Jeśli jądro zawiera grupy neuronów w tej samej tablicy jako ***x***, schemat normalizacji jest określany jako **takie same mapy normalizacji**. Określenie tej samej normalizacji mapy, pierwszy Współrzędna w **InputShape** musi mieć wartość 1.

+ Jeśli jądro zawiera grupy neuronów w tym samym miejscu przestrzenne ***x***, ale grupy neuronów znajdują się w innych map, nazywa się schemat normalizacji **na mapuje normalizacji**. Tego typu odpowiedzi normalizacji implementuje formę poprzecznego niemożność wykonania przez typ w rzeczywistych grupy neuronów, tworzenie konkurencję w odniesieniu do poziomów big Data aktywacji wśród wyjść neuronu obliczane na różnych mapowań. Aby zdefiniować między normalizacji map, pierwszy współrzędnych musi być liczbą całkowitą, większa niż i większa niż liczba map, a pozostałe współrzędne musi mieć wartość 1.

Ponieważ pakiety normalizacji odpowiedzi dotyczą wstępnie zdefiniowana funkcja wartości węzła źródłowego do określenia wartości węzła docelowego, muszą oni bez trainable stanu (wagi lub odchyleń).

> [!NOTE]
> Węzły w warstwie docelowej odpowiadają grupy neuronów, które są centralnej węzłami jądra. Na przykład jeśli `KernelShape[d]` jest nieparzysta, `KernelShape[d]/2` odnosi się do węzła centralnej jądra. Jeśli `KernelShape[d]` jest parzysta, wynosi węźle centralnym `KernelShape[d]/2 - 1`. W związku z tym jeśli `Padding[d]` jest wartość False, pierwszy i ostatni `KernelShape[d]/2` węzły nie mają odpowiednich węzłów warstwy docelowej. Aby uniknąć tej sytuacji, należy zdefiniować **dopełnienie** jako [ma wartość true, true na wartość true,...].

Oprócz cztery atrybuty opisanego wcześniej pakiety normalizacji odpowiedzi obsługują następujące atrybuty:

+ **Alfa**: (wymagane) określa wartość zmiennoprzecinkową, która odpowiada `α` poprzednie formuły.
+ **W wersji beta**: (wymagane) określa wartość zmiennoprzecinkową, która odpowiada `β` poprzednie formuły.
+ **Przesunięcie**: (opcjonalnie) określa wartość zmiennoprzecinkową, która odpowiada `k` poprzednie formuły. Jego wartość domyślna to 1.

W poniższym przykładzie zdefiniowano pakietu normalizacji odpowiedzi, za pomocą tych atrybutów:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ Warstwa źródłowa zawiera pięć mapy, z których każdy wymiar aof 12 x 12, sumowanie w węzłach 1440.
+ Wartość **KernelShape** wskazuje, że jest to ten sam normalizacji warstwę mapy, gdzie otoczenia jest prostokąt 3 x 3.
+ Wartość domyślna **dopełnienie** ma wartość FAŁSZ, dlatego warstwy docelowej ma tylko 10 węzłów, w każdym wymiarze. Aby dołączyć jednego węzła w warstwie docelowej, która odnosi się do każdego węzła w warstwie źródłowej, należy dodać dopełnienie = [true, true, true]; i zmień rozmiar RN1 [5, 12, 12].

## <a name="share-declaration"></a>Udostępnianie deklaracji

NET # obsługuje opcjonalnie zdefiniowanie kilka pakietów za pomocą wag udostępnionego. Mogą być udostępniane wagi wszelkie dwa pakiety, jeśli ich struktur są takie same. Następująca składnia określa pakiety za pomocą wag udostępnione:

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

Na przykład następująca deklaracja udziału określa nazwy warstw, wskazujący, że powinna być udostępniana odchyleń i wagi:

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

+ Funkcje wejściowe są podzielone na partycje w taki sam rozmiar warstwy danych wejściowych.
+ Ukryte warstwy należy obliczyć wyższy poziom funkcji na dwie warstwy danych wejściowych.
+ Deklaracja udziału określa, że *H1* i *H2* muszą być obliczone w taki sam sposób, z ich odpowiednich danych wejściowych.

Alternatywnie to może być określona za pomocą dwóch osobnych deklaracji udziału w następujący sposób:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Można użyć krótkiej formy, tylko wtedy, gdy warstwy zawiera pojedynczy pakiet. Ogólnie rzecz biorąc jest możliwe udostępnianie tylko wtedy, gdy odpowiednia struktura jest taka sama, co oznacza, że mają one ten sam rozmiar, ten sam geometrii splotowe i tak dalej.

## <a name="examples-of-net-usage"></a>Przykłady użycia Net #

W tej sekcji przedstawiono kilka przykładów sposobu korzystania z Net # Dodaj ukrytych warstw, definiowania sposobu interakcji z innymi warstwami ukrytych warstw i twórz splotowych sieci.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Zdefiniuj prostą niestandardowych sieci neuronowych: Przykład "Hello World"

Ten prosty przykład przedstawia sposób tworzenia modelu sieci neuronowej, który ma jedną warstwę ukryte.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

W przykładzie pokazano niektóre podstawowe polecenia w następujący sposób:

+ Pierwszy wiersz definiuje warstwę danych wejściowych (o nazwie `Data`). Kiedy używasz `auto` — słowo kluczowe, sieć neuronowa automatycznie uwzględnia wszystkich kolumn funkcji w danych wejściowych przykładach.
+ Drugi wiersz tworzy ukrytej warstwie. Nazwa `H` jest przypisany do warstwy ukryte, która zawiera węzły 200. Ta warstwa podłączona do warstwy danych wejściowych.
+ Trzeci wiersz definiuje warstwę danych wyjściowych (o nazwie `Out`), który zawiera 10 węzłów danych wyjściowych. Sieć neuronowa służy do klasyfikowania, czy jeden węzeł danych wyjściowych klasy. Słowo kluczowe **sigmoid** wskazuje, że funkcja danych wyjściowych jest stosowana do warstwy danych wyjściowych.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Zdefiniuj wiele warstw ukryte: przykład vision komputera

Poniższy przykład pokazuje, jak zdefiniować nieco bardziej złożonych sieci neuronowych, za pomocą różnych warstw ukrytych niestandardowych.

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

Ten przykład ilustruje kilka funkcji w języku specyfikacji sieci neuronowych:

+ Struktura zawiera dwie warstwy danych wejściowych, `Pixels` i `MetaData`.
+ `Pixels` Warstwa jest warstwą źródła dla dwóch pakietów połączenie, za pomocą warstwy docelowej `ByRow` i `ByCol`.
+ Warstwy `Gather` i `Result` są warstwy docelowej w wielu pakietów połączenia.
+ Warstwa danych wyjściowych, `Result`, jest warstwą docelowego w dwóch pakietów połączenia; jedna z drugiego poziomu ukrytej warstwie według następującego `Gather` jako warstwy docelowej, a druga z warstwą danych wejściowych `MetaData` jako warstwy docelowej.
+ Ukryte warstwy `ByRow` i `ByCol`, określ filtrowane łączności za pomocą wyrażeń predykatu. Mówiąc ściślej, węzeł w `ByRow` w [x, y] jest podłączony do węzłów w `Pixels` , jest mają równe węzeł koordynować pierwszego indeksu w pierwszym współrzędnych, x. Podobnie, węzeł w `ByCol` w [x, y] jest podłączony do węzłów w `Pixels` , mieć drugi indeks koordynować jednego węzła przez drugi współrzędnych, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Zdefiniuj splotowej sieci wieloklasowej klasyfikacji: przykład rozpoznawanie cyfr

Definicji następującej sieci jest przeznaczona do rozpoznaje numery, a przykład ilustruje kilka zaawansowanych technik dostosowywania sieci neuronowych.

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

+ Struktura zawiera jedną warstwę danych wejściowych, `Image`.
+ Słowo kluczowe `convolve` wskazuje, że warstwy o nazwie `Conv1` i `Conv2` to splotowe warstwy. Każda z tych deklaracji warstwy następuje lista atrybutów konwolucji.
+ Net trzecią ukrył warstwy, `Hid3`, która pełni jest połączona z drugą warstwę ukryte, `Conv2`.
+ Warstwa danych wyjściowych, `Digit`, jest podłączony tylko do trzeciego ukrytej warstwie `Hid3`. Słowo kluczowe `all` wskazuje, czy warstwa danych wyjściowych jest w pełni połączone `Hid3`.
+ Liczby argumentów konwolucji to trzy: długość kolekcje `InputShape`, `KernelShape`, `Stride, and `udostępniania ".
+ Liczba wag na jądra jest `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. lub `26 * 50 = 1300`.
+ Węzłów w ukrytej warstwie według następującego każdego można obliczyć w następujący sposób:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Całkowita liczba węzłów można obliczyć przy użyciu zadeklarowane wymiarach warstwy [50, 5, 5], w następujący sposób: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Ponieważ `Sharing[d]` ma wartość FAŁSZ tylko w przypadku `d == 0`, liczba jądra jest `MapCount * NodeCount\[0] = 10 * 5 = 50`.

## <a name="acknowledgements"></a>Potwierdzenia

Język Net # dostosowywania architektury sieci neuronowych został opracowany w firmie Microsoft przez Shon Katzenberger (Architekt, uczenie maszynowe) i Alexey Kamenev (inżynier oprogramowania, Microsoft Research). Jest ona używana wewnętrznie do uczenia maszynowego projektów i aplikacje, począwszy od wykrywania obrazu do analizy tekstu. Aby uzyskać więcej informacji, zobacz [neuronowe w usłudze Azure Machine Learning studio — wprowadzenie do Net #](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
