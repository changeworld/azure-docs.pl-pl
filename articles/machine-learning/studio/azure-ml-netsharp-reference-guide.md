---
title: 'NET # Neuronowej sieci specyfikacji języka Guide - Azure Machine Learning | Dokumentacja firmy Microsoft'
description: 'Specyfikacja języka, wraz z przykładami sposobu tworzenia modelu sieci neuronowej niestandardowej przy użyciu Net # sieci składni Net # neuronowej'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: cfd1454b-47df-4745-b064-ce5f9b3be303
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/01/2018
ms.openlocfilehash: 2571420e3497e85ec1bf37b340015b061e8c91d4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833917"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Przewodnik po sieci # sieci neuronowej specyfikacji języka dla usługi Azure Machine Learning

NET # jest językiem, który został opracowany przez firmę Microsoft, który służy do definiowania architektury sieci neuronowej. Przy użyciu Net # do definiowania struktury sieci neuronowej umożliwia zdefiniowanie struktury złożonych, takie jak sieci neuronowe głębokość lub splotowi dowolnego wymiarów, które są znane, aby poprawić uczenie na danych, takich jak obraz, audio i wideo.

Specyfikacja architektura Net # można użyć w tych sytuacjach:

+ Wszystkie moduły sieci neuronowej w usłudze Microsoft Azure Machine Learning Studio: [sieci Neuronowej Multiklasa](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [sieci Neuronowej Two-Class](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), i [regresji sieci Neuronowej](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Funkcje sieci neuronowej w MicrosoftML: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) i [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)dla języka R i [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) dla języka Python.


W tym artykule opisano podstawowe pojęcia i składni konieczne jest opracowanie niestandardowej sieci neuronowej przy użyciu Net #: 

+ Wymagania dotyczące sieci neuronowej oraz sposób definiowania podstawowych składników
+ Składnia i słów kluczowych języka specyfikacji Net #
+ Przykłady sieci neuronowej niestandardowe utworzone przy użyciu Net # 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="neural-network-basics"></a>Podstawowe informacje o sieci neuronowej

Struktura sieci neuronowej składa się z węzłów, które są podzielone na warstwy i ważoną połączeń (lub krawędzi) między węzłami. Połączenia są kierunkowe, a każde połączenie ma węzeł źródłowy i węzeł docelowy.  

Każda warstwa trainable (ukryty lub warstwę danych wyjściowych) ma co najmniej jeden **pakietów połączenia**. Pakiet połączenia składa się z warstwy źródła i specyfikacja połączeń z tym warstwy źródła. Wszystkich połączeń w danym pakietu współużytkowanie tej samej warstwie źródła i tej samej warstwie docelowego. W Net # pakietu połączenia jest traktowane jako należące do warstwy docelowej pakietu.

NET # obsługuje różne rodzaje połączenia pakietów, które można dostosować sposób dane wejściowe są mapowane do warstwy ukryte i mapowane na dane wyjściowe.

Domyślne lub standardowy pakietu jest **pełny pakiet**, w który każdego węzła w warstwie źródłowej jest podłączony do każdego węzła w docelowym warstwy.

Ponadto Net # obsługuje następujące cztery rodzaje pakietów zaawansowanego połączenia:

+ **Filtrowane pakiety**. Użytkownik może zdefiniować predykat przy użyciu lokalizacji węzeł warstwy źródłowego i docelowego węzła warstwy. Węzły są połączone, przy każdym predykat ma wartość True.

+ **Pakiety convolutional**. Użytkownik może zdefiniować małych klubów węzły w warstwie źródła. Każdy węzeł w warstwie docelowego jest podłączony do otoczenie jednego z węzłów w warstwie źródła.

+ **Buforowanie pakiety** i **pakiety normalizacji odpowiedzi**. Te są podobne do convolutional pakiety w użytkownika definiuje małych klubów węzły w warstwie źródła. Różnica polega na wag krawędzi w tych pakietów nie są trainable. Zamiast tego funkcja wstępnie zdefiniowane jest stosowany do wartości węzła źródłowego, aby ustalić wartość węzła docelowego.


## <a name="supported-customizations"></a>Obsługiwane dostosowania

Architektura sieci neuronowej modeli, które możesz utworzyć w usłudze Azure Machine Learning można często dostosować za pomocą Net #. Możesz:

+ Tworzenie warstwy ukryte i sterują liczbą węzłów w każdej warstwie.
+ Określ, jak mają być połączone ze sobą warstwy.
+ Zdefiniuj struktury łączności specjalnych, takich jak splotowi i wagi udostępnianie pakietów.
+ Określ inną aktywacji funkcji.

Szczegółowe informacje o składni języka specyfikacji, zobacz [specyfikacji struktury](#Structure-specifications).  

Przykłady Definiowanie sieci neuronowe dla niektórych typowych maszyny uczenia zadania z jednostronny zbyt skomplikowane, zobacz [przykłady](#Examples-of-Net#-usage).

## <a name="general-requirements"></a>Wymagania ogólne

+ Musi to być dokładnie jeden wyjściowy warstwy, co najmniej jedną warstwę wejściowego i zero lub więcej warstwy ukryte. 
+ Każda warstwa ma stała liczba węzłów, koncepcyjnie ułożone w tablicy regularnej dowolnego wymiarów. 
+ Warstwy wejściowy nie może mieć skojarzone przeszkolone parametrów i reprezentują punktu, w którym dane wystąpienia wprowadza sieci. 
+ Skojarzony przeszkolone parametrów, znany jako obciążeń i odchyleń trainable warstwy (warstwy ukryte i wyjścia). 
+ Węzły źródłowe i docelowe muszą być w oddzielnych warstw. 
+ Połączenia muszą być acykliczne; innymi słowy nie może być łańcuch połączeń, co prowadzi do węzła początkowego źródła.
+ Warstwa danych wyjściowych nie może być warstwę źródła pakietu połączenia.  

## <a name="structure-specifications"></a>Specyfikacje — struktura

Specyfikacja struktury sieci neuronowej składa się z trzech części: **deklaracji stałej**, **warstwy deklaracji**, **deklaracji połączenia**. Istnieje również opcjonalny **udostępnianie deklaracji** sekcji. Sekcji można określić w dowolnej kolejności.

## <a name="constant-declaration"></a>Deklaracja stałej

Deklaracja stałej jest opcjonalna. Umożliwia określenie wartości używane w innym miejscu w definicji sieci neuronowej. Instrukcja deklaracji składa się z identyfikatorem następuje znak równości i wyrażenie wartości.

Na przykład następująca instrukcja definiuje stałą `x`:  

`Const X = 28;`

Aby zdefiniować co najmniej dwa stałe jednocześnie, ujmij identyfikator nazwy i wartości w nawiasach klamrowych i oddziel je średnikami. Na przykład:

`Const { X = 28; Y = 4; }`

Prawa strona wyrażenia każdego przypisania może być liczbą całkowitą, liczba rzeczywista, wartość logiczna (True lub False) lub wyrażeniu matematycznym. Na przykład:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklaracja warstwy

Deklaracja warstwy jest wymagana. Definiuje rozmiar i źródło warstwy, łącznie z pakietów połączenia i ich atrybutów. Instrukcja deklaracji zaczyna się od nazwy warstwy (dane wejściowe, ukryty lub dane wyjściowe), następuje wymiary warstwy (krotki dodatnie liczby całkowite). Na przykład:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;  
```

+ Produkt wymiarów jest liczba węzłów w warstwie. W tym przykładzie są dwóch wymiarów [5,20], co oznacza, że istnieją 100 węzły w warstwie.
+ Warstw mogą być deklarowane w dowolnej kolejności, z jednym wyjątkiem: Jeśli zdefiniowano więcej niż jedną warstwę wejściowych, kolejność, w którym jest zadeklarowany musi zgodna z kolejnością funkcji w danych wejściowych.

Aby określić, że liczba węzłów w warstwie ustalana automatycznie, użyj `auto` — słowo kluczowe. `auto` — Słowo kluczowe ma wpływ różne, w zależności od warstwy:

+ W deklaracji wejściowych warstwy liczba węzłów to liczba funkcji w danych wejściowych.
+ W deklaracji ukrytej warstwie liczba węzłów to numer, który jest określony przez wartość parametru **liczba węzłów**.
+ W deklaracji warstwy danych wyjściowych liczba węzłów to 2 dwuklasowych klasyfikacji, 1 regresji i równe liczba węzłów wyjściowych wieloklasowej klasyfikacji.

Na przykład następujące definicji sieci umożliwia rozmiar wszystkich warstw określone automatycznie:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;  
```

Deklaracja warstwy trainable warstwy (warstwy ukryte lub wyjściowe) można opcjonalnie dołączyć dane wyjściowe funkcji (nazywanych również funkcję aktywacji), domyślnie to **sigmoid** dla modeli klasyfikacji i **liniowej** dla modeli regresji. Nawet wtedy, gdy użytkownik korzysta z domyślnych, można jawnie stanu funkcji aktywacji, w razie potrzeby dla uzyskania przejrzystości.

Obsługiwane są następujące funkcje związane z danych wyjściowych:

+ sigmoid
+ liniowy
+ softmax
+ rlinear
+ kwadratowe
+ sqrt
+ srlinear
+ ABS
+ TANH
+ brlinear

Na przykład następujące oświadczenie używa **softmax** funkcji:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklaracja połączenia

Natychmiast po zdefiniowaniu trainable warstwy, należy zadeklarować połączeń między warstwami, które zostały zdefiniowane. Deklaracja pakietu połączenia rozpoczyna się od słowa kluczowego `from`, a następnie według nazwy rodzaju połączenia pakietu, aby utworzyć i warstw źródła pakietu.

Obecnie są obsługiwane pięć rodzajów pakietów połączenia:

+ **Pełna** pakiety wskazuje słowo kluczowe `all`
+ **Filtrowane** pakiety wskazane słowem kluczowym `where`, a następnie wyrażenie predykatu
+ **Convolutional** pakiety wskazane słowem kluczowym `convolve`, a następnie atrybuty konwolucji
+ **Buforowanie** pakiety wskazane przez słowa kluczowe **maksymalnej liczby puli** lub **oznacza puli**
+ **Odpowiedź normalizacji** pakiety wskazane słowem kluczowym **normy odpowiedzi**

## <a name="full-bundles"></a>Pełna pakietów

Pakiet pełne połączenie obejmuje połączenie z każdego węzła w warstwie źródła do każdego węzła w docelowym warstwy. Jest to domyślny typ połączenia sieciowego.

## <a name="filtered-bundles"></a>Pakiety filtrowane

Specyfikacja pakietu połączenia filtrowanego obejmuje predykatu, wyrażona składnię, bardzo podobnie jak wyrażenia lambda C#. W poniższym przykładzie zdefiniowano dwa filtrowanych pakietów:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  
```

+ W predykacie dla `ByRow`, `s` parametr reprezentujący indeks w tablicy regularnej węzłów wejściowych warstwy `Pixels`, i `d` parametr reprezentujący indeks w tablicy węzłów w ukrytej warstwie `ByRow`. Typ zarówno `s` i `d` jest krotka liczb całkowitych o długości dwa. Koncepcyjnie `s` zakresów przez wszystkie pary liczb całkowitych z `0 <= s[0] < 10` i `0 <= s[1] < 20`, i `d` zakresów przez wszystkie pary liczb całkowitych, z `0 <= d[0] < 10` i `0 <= d[1] < 12`. 

+ Po prawej stronie wyrażenie predykatu istnieje warunek. W tym przykładzie dla każdej wartości `s` i `d` tak, aby warunek ma wartość True, jest krawędź z węzła źródłowego warstwy do węzła docelowego warstwy. W związku z tym to wyrażenie filtru wskazuje, że pakiet zawiera połączenia z węzła zdefiniowane przez `s` do węzła zdefiniowane przez `d` we wszystkich przypadkach, gdy s [0] jest taki sam, d [0].

Opcjonalnie można określić zestaw wag dla filtrowanego pakietu. Wartość **wagi** atrybut musi być krotki liczącej zmiennoprzecinkowych wartości o długości, która jest zgodna z liczbą połączeń zdefiniowanych w pakiecie. Domyślnie generowany losowo wagi.

Wartości wag są pogrupowane według indeksu węzła docelowego. Oznacza to, jeśli pierwszy węzeł docelowy jest podłączony do węzłów źródła K, pierwszy `K` elementy **wagi** wag dla pierwszego węzła docelowego, w kolejności indeksu źródła są spójnej kolekcji. To samo dotyczy docelowej z pozostałych węzłów.

Istnieje możliwość określenia wagi bezpośrednio jako wartości stałych. Na przykład jeśli znasz wag wcześniej, można je określić jako stałe przy użyciu następującej składni:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Pakiety convolutional

Jeśli dane szkoleniowe jednorodnych struktury, convolutional połączeń często są używane do informacje wysokiego poziomu funkcji danych. Na przykład w obrazie, audio i wideo, danych przestrzennych lub danych czasowych wymiary może być dość uniform.  

Pakiety convolutional stosować prostokątne **jądra** który są przesuwać za pośrednictwem wymiary. Zasadniczo każda jądra definiuje zestaw wagi stosowane w lokalnym klubów, określany jako **aplikacji jądra**. Każda aplikacja jądra odpowiada węzła w warstwie źródła, które są określone jako **centralnej węzła**. Wag jądra są współużytkowane przez wiele połączeń. W pakiecie convolutional każdy jądra jest prostokątny i wszystkie aplikacje jądra mają taki sam rozmiar.  

Pakiety convolutional obsługuje następujące atrybuty:

**InputShape** definiuje wymiary warstwy źródłowej na potrzeby tego convolutional pakietu. Wartość musi być krotka dodatnimi liczbami całkowitymi. Liczba węzłów w warstwie źródła musi być równa iloczyn liczb całkowitych, ale w przeciwnym razie nie trzeba wymiarowością zadeklarowany dla warstwy źródła. Długość tej krotki staje się **argumentów** wartość convolutional pakietu. Zwykle argumentów odnosi się do liczby argumentów lub argumentów operacji, które można wykonać funkcji.

Aby zdefiniować kształt i lokalizacje jądra, należy użyć atrybutów **KernelShape**, **Stride**, **Padding**, **LowerPad**, i **UpperPad**:   

+ **KernelShape**: (wymagane) definiuje wymiary poszczególnych jądra dla convolutional pakietu. Wartość musi być krotka dodatnie liczby całkowite o długości, która jest równa liczbie argumentów pakietu. Każdy element tej spójnej kolekcji musi być nie większy niż odpowiadający składnik **InputShape**. 

+ **STRIDE**: (opcjonalnie) definiuje zmienną rozmiary krok konwolucji (rozmiar krok dla każdego wymiaru), to odległość między węzłami centralnej. Wartość musi być krotka dodatnie liczby całkowite o długości, która jest argumentów pakietu. Każdy element tej spójnej kolekcji musi być nie większy niż odpowiadający składnik **KernelShape**. Wartość domyślna to krotka ze wszystkimi składnikami równa jeden. 

+ **Udostępnianie**: (opcjonalnie) definiuje wagi udostępniania dla każdego wymiaru konwolucji. Wartość może być pojedynczą wartością logiczną lub krotka z wartościami logicznymi o długości, która jest argumentów pakietu. Pojedynczą wartość logiczną jest rozszerzony do krotność właściwą długość ze wszystkimi składnikami równa określonej wartości. Wartość domyślna to spójnej kolekcji, która składa się ze wszystkich wartości True. 

+ **MapCount**: (opcjonalnie) definiuje liczbę funkcji mapy dla convolutional pakietu. Wartość może być jednym dodatnią liczbą całkowitą lub krotka dodatnie liczby całkowite o długości, która jest argumentów pakietu. Jedną wartość całkowitą jest rozszerzony do krotność właściwą długość składnikom pierwszy równa określonej wartości i pozostałe składniki równa jeden. Domyślna wartość to jeden. Całkowita liczba map funkcji jest produktem składników spójnej kolekcji. Factoring to całkowita liczba składników określa sposób grupowania funkcji wartości mapy w węzłach docelowego. 

+ **Wagi**: (opcjonalnie) definiuje początkowej wag dla pakietu. Wartość musi być krotki liczącej zmiennoprzecinkową liczba wag wartości o długości, która jest liczba jądra na jądra, zgodnie z definicją w dalszej części tego artykułu. Losowo generowane są domyślnych wag.  

Istnieją dwa zestawy właściwości sterujące dopełnienie, właściwości są wzajemnie:

+ **Dopełnienie**: Określa (opcjonalnie) określa, czy dane wejściowe powinny dopełniane przy użyciu **domyślny schemat dopełnienie**. Wartość może być pojedynczą wartość logiczna lub może być krotka z wartościami logicznymi o długości, która jest argumentów pakietu. 

    Pojedynczą wartość logiczną jest rozszerzony do krotność właściwą długość ze wszystkimi składnikami równa określonej wartości. 
    
    Jeśli wartość wymiaru ma wartość PRAWDA, źródło logicznie jest uzupełniana tego wymiaru o wartości zero komórek do obsługi aplikacji jądra dodatkowe, taki sposób, że węzły centralnej imię i nazwisko jądra w tym wymiarze są węzłów imię i nazwisko, w tym wymiarze w warstwie źródła. W związku z tym "zastępczego" węzłów każdego wymiaru jest określana automatycznie, aby dokładnie dopasować `(InputShape[d] - 1) / Stride[d] + 1` jądra do warstwy wypełniony źródła. 
    
    Jeśli wartość wymiaru ma wartość False, jądra są zdefiniowane tak, aby liczba węzłów na każdej stronie, które pozostało się takie same (maksymalnie różnica 1). Wartość domyślna tego atrybutu to krotka ze wszystkimi składnikami równa False.

+ **UpperPad** i **LowerPad**: (opcjonalnie) Podaj większą kontrolę nad wielkość uzupełnienia do użycia. **Ważne:** te atrybuty mogą być zdefiniowane w przypadku i tylko wtedy, gdy **Padding** właściwość powyżej jest ***nie*** zdefiniowane. Wartości powinny być wyliczaną krotki o długości będących argumentów pakietu. Jeśli te atrybuty są określone, węzły "zastępczego" są dodawane do zakończenia dolna i górna każdego wymiaru wejściowych warstwy. Liczba węzłów dodanych do dolna i górna punktów końcowych w każdy wymiar jest określana przez **LowerPad**[i] i **UpperPad**[i] odpowiednio. 

    Aby upewnić się, że jądra odpowiadają tylko dla węzłów "rzeczywiste", a nie do węzłów "zastępczego", muszą być spełnione następujące warunki:
      - Każdy składnik **LowerPad** musi być mniejsza niż `KernelShape[d]/2`. 
      - Każdy składnik **UpperPad** musi być większa niż `KernelShape[d]/2`. 
      - Wartość domyślna tych atrybutów to krotka ze wszystkimi składnikami równa 0. 

    Ustawienie **Padding** = true umożliwia tyle dopełnienie odpowiednio do zachowania "center" jądra wewnątrz "rzeczywistym" danych wejściowych. Spowoduje to zmianę matematyczne nieco dla przetwarzania rozmiar danych wyjściowych. Ogólnie rzecz biorąc, rozmiar danych wyjściowych *D* jest obliczana jako `D = (I - K) / S + 1`, gdzie `I` rozmiar wejściowe `K` rozmiar jądra `S` jest stride, i `/` jest dzielenie liczby całkowitej (round kierunku zera ). Jeśli ustawisz UpperPad = [1, 1], rozmiar wejściowy `I` jest skutecznie 29 i w związku z tym `D = (29 - 5) / 2 + 1 = 13`. Jednakże, gdy **Padding** zasadniczo = true, `I` pobiera upadku przez `K - 1`; dlatego `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Określenie wartości **UpperPad** i **LowerPad** uzyskać większą kontrolę nad uzupełnienie niż w przypadku po prostu **Padding** = true.

Aby uzyskać więcej informacji o sieciach convolutional i aplikacjach zobacz następujące artykuły: 

+ [http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
+ [http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
+ [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Korzystanie z puli pakietów

A **buforowanie pakietu** stosuje geometry, podobnie jak convolutional połączenie, ale używa funkcji wstępnie zdefiniowanych wartości węzła źródłowego do uzyskania wartości węzła docelowego. W związku z tym korzystanie z puli pakietów ma bez trainable stanu (wag lub odchyleń). Korzystanie z puli pakietów obsługuje wszystkie atrybuty convolutional z wyjątkiem **udostępniania**, **MapCount**, i **wagi**.

Zazwyczaj jądra podsumowane według sąsiadujących jednostki buforowania nie pokrywają się. Jeśli krok [d] jest równa KernelShape [d] każdego wymiaru, warstwy uzyskane jest tradycyjnych lokalnego warstwy korzystanie z puli, która jest często stosowanych w convolutional sieci neuronowej. Każdy węzeł docelowy oblicza maksymalną lub średniej działania jego jądra w warstwie źródła.  

Poniższy przykład przedstawia buforowania pakietu: 

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }  
```

+ Liczba argumentów pakietu wynosi 3: oznacza to, że długość krotki `InputShape`, `KernelShape`, i `Stride`. 
+ Liczba węzłów w warstwie źródła jest `5 * 24 * 24 = 2880`. 
+ To tradycyjnej warstwy buforowania lokalnego, dlatego **KernelShape** i **Stride** są takie same. 
+ Liczba węzłów w warstwie docelowy to `5 * 12 * 12 = 1440`.

Aby uzyskać więcej informacji na temat warstwy buforowania zobacz następujące artykuły:  

+ [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sekcji 3.4)
+ [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
+ [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Pakiety normalizacji odpowiedzi

**Odpowiedź normalizacji** jest schemat normalizacji lokalnej, która została wprowadzona przez Geoffrey Hinton i inni, w tym dokumencie [ImageNet Classiﬁcation z głębokie sieci neuronowe Convolutional](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). 

Normalizacji odpowiedzi jest używany do pomocy generalizacji w sieci neuronowej. Po jednej neuronu jest uruchamiane na poziomie bardzo duże aktywacji, warstwy normalizacji lokalnego odpowiedzi pomija poziom aktywacji otaczające grupy neuronów. Odbywa się przy użyciu trzech parametrów (`α`, `β`, i `k`) i struktura convolutional (lub otoczenie kształt). Każdy neuronu warstwy docelowej **y** odpowiada neuronu **x** w warstwie źródła. Poziom aktywacji **y** znajduje się według poniższego wzoru, gdzie `f` poziom aktywacji neuronu, i `Nx` jądro (lub zestawu, który zawiera grupy neuronów w otoczeniu z **x**), zgodnie z następującą strukturę convolutional:  

![Formuła convolutional — struktura](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Pakiety normalizacji odpowiedzi obsługuje wszystkie convolutional atrybutów, z wyjątkiem **udostępniania**, **MapCount**, i **wagi**.  

+ Jeśli jądra zawiera grupy neuronów w tej samej tablicy jako ***x***, schemat normalizacji jest określany jako **sam mapy normalizacji**. Określenie tej samej normalizacji mapy, pierwszy współrzędnych **InputShape** musi mieć wartość 1.

+ Jeśli jądra zawiera grupy neuronów w tym samym miejscu przestrzennych ***x***, ale grupy neuronów inne mapy, nosi nazwę schematu normalizacji **w poprzek mapuje normalizacji**. Ten typ odpowiedzi normalizacji implementuje formę penetracji niemożność wykonania przez typ w rzeczywistych grupy neuronów, tworzenie konkurencji poziomów big aktywacji wśród wyjść neuronu obliczonych na różnych mapy. Do definiowania na normalizacji mapy, pierwszy współrzędnych musi być liczbą całkowitą więcej niż jeden oraz nie większa niż liczba mapy, a pozostałe współrzędne musi mieć wartość 1.

Ponieważ pakiety normalizacji odpowiedzi dotyczą funkcji wstępnie zdefiniowanej wartości węzła źródłowego, aby ustalić wartość węzła docelowego, mają one żadnego trainable stanu (wag lub odchyleń).

> [!NOTE]
> Węzły w warstwie docelowej odpowiadają grupy neuronów, które są węzłami centralnej jądra. Na przykład jeśli `KernelShape[d]` jest nieparzysta, następnie `KernelShape[d]/2` odpowiada węzła centralnej jądra. Jeśli `KernelShape[d]` jest parzysta, centralnej węzeł jest w `KernelShape[d]/2 - 1`. W związku z tym jeśli `Padding[d]` jest wartość FAŁSZ, pierwszy i ostatni `KernelShape[d]/2` węzły nie mają odpowiadających im węzłów warstwy docelowej. Aby tego uniknąć, należy zdefiniować **Padding** jako [true ma wartość true, true,...].

Oprócz czterech właściwości opisanych wcześniej pakiety normalizacji odpowiedzi obsługuje następujące atrybuty:

+ **Alpha**: (wymagane) określa wartość zmiennoprzecinkowa, który odpowiada `α` w poprzednim formuły. 
+ **W wersji beta**: (wymagane) określa wartość zmiennoprzecinkowa, który odpowiada `β` w poprzednim formuły. 
+ **Przesunięcie**: Określa (opcjonalnie) wartość zmiennoprzecinkowa, który odpowiada `k` w poprzednim formuły. Domyślnie 1.

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

+ Warstwa źródłowa zawiera pięć maps, każdy z wymiarów aof 12 x 12, sumowanie w węzłach 1440. 
+ Wartość **KernelShape** wskazuje, że jest tej samej warstwie normalizacji mapy, gdzie otoczenie jest prostokąt 3 x 3. 
+ Wartość domyślna **Padding** ma wartość False, w związku z tym warstwy docelowej ma tylko 10 węzłów w każdego wymiaru. Aby dołączyć jeden węzeł warstwy docelowej, umożliwiająca każdego węzła w warstwie źródłowej, należy dodać dopełnienie = [true, true, true]; i zmień rozmiar RN1 [5, 12, 12].  

## <a name="share-declaration"></a>Deklaracja udziału

NET # obsługuje opcjonalnie Definiowanie kilka pakietów o wagach udostępnionego. Może być udostępniony wag żadnych dwa pakiety, jeśli ich struktur są takie same. Pakiety o wagach udostępnionego są zdefiniowane w następującej składni:  

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

Na przykład następujące oświadczenie udziału określa nazwy warstw, wskazującą, czy należy udostępnić zarówno wagi i odchyleń:  

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

+ Funkcje wejściowe są dzielone na taki sam rozmiar warstwy wejściowego. 
+ Ukryte warstwy należy obliczyć wyższej poziomu funkcji na dwóch warstwach wejściowego. 
+ Deklaracja udziału określa, że *H1* i *H2* należy obliczyć w taki sam sposób ich odpowiednich danych wejściowych.  

Alternatywnie to może być określony z dwa osobne deklaracje udziału w następujący sposób:  

```Net#
share { Data1 => H1, Data2 => H2 } // share weights  
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases  
```

Krótka forma można użyć tylko w przypadku warstwy zawierać pojedynczy pakiet. Ogólnie rzecz biorąc jest możliwe udostępnianie tylko wtedy, gdy odpowiednia struktura jest taki sam, co oznacza, że mają one sam rozmiar, tej samej geometrii convolutional i tak dalej.  

## <a name="examples-of-net-usage"></a>Przykłady użycia Net #

Ta sekcja zawiera kilka przykładów sposobu użycia Net # dodać warstwy ukryte, zdefiniuj sposób interakcji z innych warstw warstwy ukryte i kompilacji convolutional sieci.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Zdefiniuj proste niestandardowe sieci neuronowej: przykładu "Hello World"

Ten prosty przykład przedstawia sposób tworzenia modelu sieci neuronowej, który zawiera jedną warstwę ukryte.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;  
```

Przykład przedstawiono niektóre podstawowe polecenia w następujący sposób:  

+ Pierwszy wiersz definiuje warstwę wejściowych (o nazwie `Data`). Jeśli używasz `auto` — słowo kluczowe, sieci neuronowej automatycznie zawiera wszystkie kolumny funkcji w przykładach wejściowego. 
+ Drugi wiersz tworzy ukrytej warstwie. Nazwa `H` jest przypisany do ukrytych warstwy, która ma 200 węzłów. Ta warstwa podłączona do wprowadzania warstwy.
+ Trzeci wiersz definiuje warstwę danych wyjściowych (o nazwie `O`), który zawiera 10 węzłów wyjściowych. Użycie sieci neuronowej klasyfikacji istnieje jeden węzeł danych wyjściowych dla klasy. Słowo kluczowe **sigmoid** wskazuje, że funkcja danych wyjściowych jest stosowane do warstwy danych wyjściowych.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Zdefiniuj wielu warstw ukrytych: przykład vision komputera

W poniższym przykładzie pokazano sposób definiowania nieco bardziej skomplikowane sieci neuronowej z wielu warstw ukrytych niestandardowych.  

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

W tym przykładzie przedstawiono kilka funkcji sieci neuronowe specyfikacji języka:

+ Struktura zawiera dwie warstwy wejściowych, `Pixels` i `MetaData`.
+ `Pixels` Warstwa jest warstwą źródła dla pakietów dwa połączenia, z warstwami docelowego `ByRow` i `ByCol`.
+ Warstwy `Gather` i `Result` są warstwy docelowej w wielu pakietów połączenia.
+ Warstwa danych wyjściowych `Result`, to warstwa docelowego w dwóch pakietów połączenia; jedna z drugiego poziomu ukrytej warstwie `Gather` jako warstwy docelowej, a druga z warstwą wejściowych `MetaData` jako warstwy docelowej.
+ Ukryte warstwy `ByRow` i `ByCol`, określ filtrowane łączności za pomocą wyrażeń predykatu. Mówiąc ściślej, węzeł w `ByRow` w [x, y] jest podłączony do węzłów w `Pixels` czy jest mają taki sam, jak węzeł koordynować indeks pierwszego elementu pierwszy współrzędnych, x. Podobnie, węzeł w `ByCol` w [x, y] jest podłączony do węzłów w `Pixels` czy mieć drugi indeks koordynować w obrębie jednego węzła na drugi współrzędnych, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definiowanie sieci convolutional wieloklasowej klasyfikacji: przykład rozpoznawanie cyfr

Definicja następującej sieci jest przeznaczona do rozpoznaje liczb, i zastosowano niektórych zaawansowanych technik dostosowywania sieci neuronowej.  

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

+ Struktura zawiera jedną warstwę wejściowych, `Image`.
+ Słowo kluczowe `convolve` wskazuje, że warstwy o nazwie `Conv1` i `Conv2` są convolutional warstwy. Każdy z tych deklaracji warstwy następuje listę atrybutów konwolucji.
+ Sieci ma trzeciego ukryte warstwy, `Hid3`, która pełni jest połączona z drugiego ukrytej warstwie `Conv2`.
+ Warstwa danych wyjściowych `Digit`, jest podłączony tylko do trzeciego ukrytej warstwie `Hid3`. Słowo kluczowe `all` wskazuje, że warstwa danych wyjściowych podłączona do `Hid3`.
+ Liczby argumentów konwolucji jest trzy: długość krotki `InputShape`, `KernelShape`, `Stride, and `udostępniania ". 
+ Liczba wag na jądra jest `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. lub `26 * 50 = 1300`.
+ Węzły w każdym ukrytej warstwie można obliczyć w następujący sposób:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5` `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Całkowita liczba węzłów, może zostać obliczona przy użyciu zadeklarowane wymiarach warstwy [50, 5, 5], w następujący sposób: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Ponieważ `Sharing[d]` ma wartość FAŁSZ tylko w przypadku `d == 0`, liczba jądra jest `MapCount * NodeCount\[0] = 10 * 5 = 50`. 

## <a name="acknowledgements"></a>Potwierdzenia

Dostosowywanie architektury sieci neuronowe języka Net # został opracowany w firmie Microsoft przez Shon Katzenberger (architektów, Machine Learning) i Alexey Kamenev (inżynier oprogramowania, Microsoft Research). Jest ona używana wewnętrznie do machine learning projektów i aplikacje od obrazu wykrywania Analiza tekstu. Aby uzyskać więcej informacji, zobacz [sieci Neuronowej na uczenie Maszynowe Azure — wprowadzenie do Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
