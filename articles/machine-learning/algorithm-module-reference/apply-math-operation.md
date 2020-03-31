---
title: Stosowanie operacji matematycznej
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Zastosuj operację matematyczną w usłudze Azure Machine Learning, aby zastosować operację matematyczną do wartości kolumn w potoku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456747"
---
# <a name="apply-math-operation"></a>Stosowanie operacji matematycznej

W tym artykule opisano moduł projektanta usługi Azure Machine Learning (wersja zapoznawcza).

Operacja Zastosuj matematycznie służy do tworzenia obliczeń, które są stosowane do kolumn liczbowych w wejściowym zestawie danych. 

Obsługiwane operacje matematyczne obejmują typowe funkcje arytmetyczne, takie jak mnożenie i dzielenie, funkcje trygonometryczne, różne funkcje zaokrąglania i specjalne funkcje używane w naukach o danych, takie jak gamma i funkcje błędów.  

Po zdefiniowaniu operacji i uruchomieniu potoku wartości są dodawane do zestawu danych. W zależności od sposobu konfigurowania modułu można:

+ Dołącz wyniki do zestawu danych. Jest to szczególnie przydatne podczas weryfikacji wyniku operacji.
+ Zastąp wartości kolumn nowymi, obliczonymi wartościami.
+ Wygeneruj nową kolumnę dla wyników i nie pokazuj oryginalnych danych. 

Poszukaj operacji, której potrzebujesz w następujących kategoriach:  

- [Podstawowa (Basic)](#basic-math-operations)  
  
     Funkcje w kategorii **Basic** mogą służyć do manipulowania pojedynczą wartością lub kolumną wartości. Na przykład można uzyskać wartość bezwzględną wszystkich liczb w kolumnie lub obliczyć pierwiastek kwadratowy każdej wartości w kolumnie.  
  
-   [Porównanie](#comparison-operations)  
  
      Funkcje w **kategorii Porównaj** są używane do porównania: można wykonać porównanie wartości w dwóch kolumnach lub porównać każdą wartość w kolumnie z określoną stałą. Na przykład można porównać kolumny, aby ustalić, czy wartości były takie same w dwóch zestawach danych. Można też użyć stałej, takiej jak maksymalna dozwolona wartość, aby znaleźć wartości odstające w kolumnie numerycznej.  
  
-   [Operacje](#arithmetic-operations)  
  
     **Kategoria Operacje** obejmuje podstawowe funkcje matematyczne: dodawanie, odejmowanie, mnożenie i dzielenie. Można pracować z kolumnami lub stałymi. Na przykład można dodać wartość w kolumnie A do wartości w kolumnie B. Można też odjąć stałą, taką jak wcześniej obliczona średnia, od każdej wartości w kolumnie A.  
  
-   [Zaokrąglanie](#rounding-operations)  
  
     Kategoria **Zaokrąglanie** obejmuje wiele funkcji do wykonywania operacji, takich jak zaokrąglanie, sufit, podłoga i obcinanie do różnych poziomów precyzji. Można określić poziom dokładności zarówno dla liczb dziesiętnych, jak i liczb y 100.  
  
-   [Specjalne](#special-math-functions)  
  
     **Kategoria Specjalna** zawiera funkcje matematyczne, które są szczególnie używane w naukach o danych, takie jak całki eliptyczne i funkcja błędu Gaussa.  
  
-   [Trygonometryczne](#trigonometric-functions)  
  
     Kategoria **trygonometryczna** obejmuje wszystkie standardowe funkcje trygonometryczne. Na przykład można konwertować radiany na stopnie lub obliczać funkcje, takie jak styczne w radianach lub stopniach.
     Te funkcje są jednoaryczne, co oznacza, że przyjmują pojedynczą kolumnę wartości jako dane wejściowe, stosują funkcję trygonometryczną i zwracają kolumnę wartości jako wynik.  W związku z tym należy upewnić się, że kolumna wejściowa jest odpowiedni typ i zawiera odpowiedni rodzaj wartości dla określonej operacji.   

## <a name="how-to-configure-apply-math-operation"></a>Jak skonfigurować operację zastosuj matematykę  

Moduł **Zastosuj operację matematyczną** wymaga zestawu danych zawierającego co najmniej jedną kolumnę zawierającą tylko liczby. Liczby mogą być dyskretne lub ciągłe, ale muszą mieć typ danych liczbowych, a nie ciąg.

Tę samą operację można zastosować do wielu kolumn liczbowych, ale wszystkie kolumny muszą znajdować się w tym samym zestawie danych. 

Każde wystąpienie tego modułu może wykonać tylko jeden typ operacji w czasie. Aby wykonać złożone operacje matematyczne, może być konieczne połączenie kilku wystąpień **zastosuj operację matematyczną** modułu.  
  
1.  Dodaj zastosuj operację **matematyczną** moduł do potoku.

1. Połącz zestaw danych zawierający co najmniej jedną kolumnę liczbową.  

1.  Wybierz jedną lub więcej kolumn źródłowych, na których mają być wykonywane obliczenia.   
  
    - Każda wybrana kolumna musi być typem danych liczbowych. 
    - Zakres danych musi być prawidłowy dla wybranej operacji matematycznej. W przeciwnym razie może wystąpić błąd lub wynik NaN (nie liczba). Na przykład Ln(-1.0) jest nieprawidłową operacją `NaN`i powoduje wartość .
  
1.  Kliknij **pozycję Kategoria,** aby wybrać **typ** operacji matematycznej do wykonania.
    
1. Wybierz określoną operację z listy w tej kategorii.
  
1.  Ustaw dodatkowe parametry wymagane przez każdy typ operacji.  
  
1.  Użyj opcji **Tryb wyjściowy,** aby wskazać sposób generowania operacji matematycznej: 

    - **Dołącz**. Wszystkie kolumny używane jako dane wejściowe są zawarte w wyjściowym zestawie danych, a także jedna dodatkowa kolumna jest dołączana, która zawiera wyniki operacji matematycznej.
    - **Inplace**. Wartości w kolumnach używanych jako dane wejściowe są zastępowane nowymi wartościami obliczeniowymi. 
    - **WynikTylko**. Zwracana jest pojedyncza kolumna zawierająca wyniki operacji matematycznej.
  
1.  Prześlij potok.  
  
## <a name="results"></a>Wyniki

Jeśli wygenerujesz wyniki przy użyciu opcji **Dołącz** lub **ResultOnly,** nagłówki kolumn zwracanego zestawu danych wskazują operację i kolumny, które zostały użyte. Na przykład jeśli porównać dwie kolumny przy użyciu **Equals** operatora, wyniki będą wyglądać następująco:  
  
-   **Równa się (Col2_Col1),** co oznacza, że testowałeś Col2 przeciwko Col1.  
-   **Równa się (Col2_$10),** wskazując, że porównano kolumnę 2 ze stałą 10.  

Nawet jeśli używasz opcji **Inplace,** dane źródłowe nie są usuwane ani zmieniane; kolumna w oryginalnym zestawie danych jest nadal dostępna w projektancie. Aby wyświetlić oryginalne dane, można podłączyć moduł [Dodaj kolumny](add-columns.md) i połączyć go z danymi wyjściowymi Zastosuj **operację matematyczną**.  
    
## <a name="basic-math-operations"></a>Podstawowe operacje matematyczne 

Funkcje w kategorii **Basic** zwykle przyjmują pojedynczą wartość z kolumny, wykonują wstępnie zdefiniowaną operację i zwracają pojedynczą wartość. W przypadku niektórych funkcji można określić stałą lub kolumnę ustawioną jako drugi argument.  
  
 Usługa Azure Machine Learning obsługuje następujące funkcje w kategorii **Podstawowa:**  

### <a name="abs"></a>Abs

Zwraca wartość bezwzględną wybranych kolumn.  
  
### <a name="atan2"></a>Atan2

Zwraca styczną odwrotną czterokadłowiskową.  

Zaznacz kolumny zawierające współrzędne punktów. Dla drugiego argumentu, który odpowiada współrzędnej x, można również określić stałą.  

Odpowiada funkcji ATAN2 w Matlab.  

### <a name="conj"></a>Konw.

Zwraca koniugat dla wartości w wybranej kolumnie.  

### <a name="cuberoot"></a>CubeRoot (Wrót kostki)

Oblicza katalog główny modułu dla wartości w wybranej kolumnie.  

### <a name="doublefactorial"></a>DoubleFactorial (DoubleFactorial)  
 Oblicza podwójną tezy dla wartości w wybranej kolumnie. Podwójna teza jest przedłużeniem normalnej funkcji faktorialnej i jest oznaczona jako x!!.  

### <a name="eps"></a>Eps

Zwraca rozmiar odstępu między bieżącą wartością a następną najwyższą, podwójną precyzją. Odpowiada funkcji EPS w Matlab.  
  
### <a name="exp"></a>Exp

Zwraca e podniesiony do potęgi wartości w wybranej kolumnie. Jest to taka sama jak funkcja EXP programu Excel.  

### <a name="exp2"></a>Exp2 (exp2)

Zwraca wartość wykładniczą base-2 argumentów, rozwiązując dla y = x * 2<sup>t,</sup> gdzie t jest kolumną wartości zawierających wykładniki.  

W **polu Zestaw kolumn**wybierz kolumnę zawierającą wykładniki t.

W przypadku **Exp2** można określić drugi argument x, który może być stałą lub inną kolumną wartości. W **drugim typie argumentu**należy wskazać, czy mnożnik x zostanie podany jako stała, czy wartość w kolumnie.  

Na przykład jeśli wybierzesz kolumnę z wartościami {0,1,2,3,4,5} zarówno dla mnożnika, jak i wykładnika, funkcja zwróci {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Zwraca wykładnik ujemny dla wartości w wybranej kolumnie.  

### <a name="factorial"></a>Silnia
Zwraca wartość faktorialna dla wartości w wybranej kolumnie.  

### <a name="hypotenuse"></a>Hipotensja
Oblicza niedociśnienie dla trójkąta, w którym długość jednej strony jest określona jako kolumna wartości, a długość drugiej strony jest określona jako stała lub jako dwie kolumny.  

### <a name="ln"></a>Logarytm naturalny

Zwraca logarytm naturalny dla wartości w wybranej kolumnie.  

### <a name="lnplus1"></a>LnPlus1 ( LnPlus1 )

Zwraca logarytm naturalny plus jeden dla wartości w wybranej kolumnie.  

### <a name="log"></a>Log

Zwraca dziennik wartości w wybranej kolumnie, biorąc pod uwagę określoną podstawę.  

Podstawę (drugi argument) można określić jako stałą lub wybierając inną kolumnę wartości.  

### <a name="log10"></a>Log10

Zwraca podstawowe wartości 10 logarytmu dla wybranej kolumny.  

### <a name="log2"></a>Dziennik2

Zwraca podstawowe wartości logarytmów 2 dla wybranej kolumny.  

### <a name="nthroot"></a>NthRoot (NthRoot)
Zwraca n-ty katalog główny wartości, używając n, który określisz.  

Zaznacz kolumny, dla których chcesz obliczyć katalog główny, używając opcji **ColumnSet.**  

W **drugim typie argumentu**wybierz inną kolumnę zawierającą katalog główny lub określ stałę, która ma być używana jako katalog główny.  

Jeśli drugi argument jest kolumną, każda wartość w kolumnie jest używana jako wartość n dla odpowiedniego wiersza. Jeśli drugi argument jest stałą, wpisz wartość n w polu tekstowym **Drugi argument.**
### <a name="pow"></a>Pow

Oblicza X podniesiony do potęgi Y dla każdej z wartości w wybranej kolumnie.  

Najpierw wybierz kolumny, które zawierają **podstawową**, która powinna być zmiennoprzestawna, za pomocą opcji **ColumnSet.**  

W **drugim typie argumentu**wybierz kolumnę zawierającą wykładnik lub określ stałą, która ma być używana jako wykładnik.  

Jeśli drugi argument jest kolumną, każda wartość w kolumnie jest używana jako wykładnik dla odpowiedniego wiersza. Jeśli drugi argument jest stałą, wpisz wartość wykładnika w polu tekstowym **Drugi argument.**  

### <a name="sqrt"></a>Sqrt

Zwraca pierwiastek kwadratowy wartości w wybranej kolumnie.  

### <a name="sqrtpi"></a>SqrtPi (Polski)

Dla każdej wartości w wybranej kolumnie mnoży wartość przez pi, a następnie zwraca pierwiastek kwadratowy wyniku.  

### <a name="square"></a>Square

Kwadrata wartości w zaznaczonej kolumnie.  

## <a name="comparison-operations"></a>Operacje porównawcze  

Użyj funkcji porównania w projektancie usługi Azure Machine Learning w dowolnym momencie, aby przetestować dwa zestawy wartości względem siebie. Na przykład w potoku może być konieczne wykonać następujące operacje porównania:  

- Oceń kolumnę modelu wyników prawdopodobieństwa względem wartości progowej.
- Określ, czy dwa zestawy wyników są takie same. Dla każdego wiersza, który jest inny, dodaj false flagi, które mogą służyć do dalszego przetwarzania lub filtrowania.  

### <a name="equalto"></a>EqualTo ( EqualTo )

Zwraca wartość True, jeśli wartości są takie same.  

### <a name="greaterthan"></a>GreaterThan

Zwraca wartość True, jeśli wartości w **zestawie kolumn** są większe niż określona stała lub większe niż odpowiadające im wartości w kolumnie porównania.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Zwraca wartość True, jeśli wartości w **zestawie kolumn** są większe lub równe określonej stałej lub większe lub równe odpowiednim wartościom w kolumnie porównania.  

### <a name="lessthan"></a>LessThan

Zwraca wartość True, jeśli wartości w **zestawie kolumn** są mniejsze niż określona stała lub mniejsze niż odpowiadające im wartości w kolumnie porównania.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Zwraca wartość True, jeśli wartości w **zestawie kolumn** są mniejsze lub równe określonej stałej lub mniejsze lub równe odpowiadającym im wartościom w kolumnie porównania.  

### <a name="notequalto"></a>NotEqualTo ( NotEqualTo )

Zwraca wartość True, jeśli wartości w **zestawie kolumn** nie są równe kolumnie stałej lub porównania, i zwraca wartość False, jeśli są równe.  

### <a name="pairmax"></a>PairMax (Polski)

Zwraca wartość, która jest większa — wartość w **zestawie kolumn** lub wartość w kolumnie stałej lub porównania.  

### <a name="pairmin"></a>PairMin (ParMin)

Zwraca mniejszą wartość — wartość w **zestawie kolumn** lub wartość w kolumnie stała lub porównanie  
  
##  <a name="arithmetic-operations"></a>Operacje arytmetyczne   

Zawiera podstawowe operacje arytmetyczne: dodawanie i odejmowanie, dzielenie i mnożenie.  Ponieważ większość operacji są binarne, wymagające dwóch liczb, najpierw wybrać operację, a następnie wybrać kolumnę lub liczby do użycia w pierwszym i drugim argumentacji.

Kolejność wybierania kolumn do dzielenia i odejmowania może wydawać się sprzeczna z intuicją; jednak, aby ułatwić zrozumienie wyników, nagłówek kolumny zawiera nazwę operacji i kolejność, w jakiej kolumny były używane.

Operacja|Liczba 1|Liczba 2|Kolumna Wynik|Wartość wyniku|
----|----|----|----|----
|Dodawanie|1|5|Dodaj(Num2_Num1)| 4|
|Mnożenie|1|5|Wiele(Num2_Num1)|5|
|Odejmowanie|1|5|Odejmowanie(Num2_Num1)|4|
|Odejmowanie|0|1|Odejmowanie(Num2_Num1)|0|
|Dzielenie|1|5|Podziel(Num2_Num1)|5|
|Dzielenie|0|1|Podziel(Num2_Num1)|Nieskończoność|

### <a name="add"></a>Dodaj

Określ kolumny źródłowe za pomocą **zestawu kolumn,** a następnie dodaj do tych wartości liczbę określoną w **drugim argumentie**.  

Aby dodać wartości w dwóch kolumnach, wybierz kolumnę lub kolumny za pomocą **zestawu kolumn,** a następnie wybierz drugą kolumnę za pomocą **drugiego argumentu**.  

### <a name="divide"></a>Dzielenie

Dzieli wartości w **kolumnie ustawione** przez stałą lub przez wartości kolumn zdefiniowane w **drugim argumentie**.  Innymi słowy, najpierw wybierasz dzielnik, a następnie dywidendę. Wartość wyjściowa jest ilorazem.

### <a name="multiply"></a>Mnożenie

Mnoży wartości w **kolumnie ustawione** przez określone wartości stałe lub kolumny.  

### <a name="subtract"></a>Odejmowanie

Określ kolumnę wartości do działania *(minuend*), wybierając inną kolumnę, używając opcji **Zestaw kolumn.** Następnie określ liczbę do odjęcie *(subtrahend)* przy użyciu listy rozwijanej **Drugi argument.** Można wybrać stałą lub kolumnę wartości.

##  <a name="rounding-operations"></a>Operacje zaokrąglania 

Projektant usługi Azure Machine Learning obsługuje wiele operacji zaokrąglania. W przypadku wielu operacji należy określić ilość precyzji, która ma być używana podczas zaokrąglania. Można użyć statycznego poziomu precyzji, określonego jako stała, lub można zastosować dynamiczną wartość precyzji uzyskaną z kolumny wartości.  

- Jeśli używasz stałej, ustaw **typ precyzji** na **Stały,** a następnie wpisz liczbę cyfr jako liczbę całkowitą w polu **tekstowym Stała precyzja.** Jeśli wpiszesz nie-liczba całkowita, moduł nie zgłasza błąd, ale wyniki mogą być nieoczekiwane.  

- Aby użyć innej wartości precyzji dla każdego wiersza w zestawie danych, ustaw **typ precyzji** na **ColumnSet**, a następnie wybierz kolumnę zawierającą odpowiednie wartości dokładności.  

### <a name="ceiling"></a>Ceiling

Zwraca pułap dla wartości w **zestawie kolumn**.  

### <a name="ceilingpower2"></a>CeilingPower2

Zwraca kwadratowy sufit dla wartości w **zestawie kolumn**.  

### <a name="floor"></a>Floor

Zwraca podłogę dla wartości w **zestawie kolumn**, do określonej precyzji.  

### <a name="mod"></a>Mod

Zwraca ułamkową część wartości w **zestawie kolumn**, do określonej precyzji.  

### <a name="quotient"></a>Iloraz

Zwraca ułamkową część wartości w **zestawie kolumn**, do określonej precyzji.  

### <a name="remainder"></a>Reszta

Zwraca pozostałą część wartości w **zestawie kolumn**.  

### <a name="rounddigits"></a>Okrągłedigits

Zwraca wartości w **zestawie kolumn**, zaokrąglone przez regułę 4/5 do określonej liczby cyfr.  

### <a name="rounddown"></a>RoundDown

Zwraca wartości w **zestawie kolumn**, zaokrąglone w dół do określonej liczby cyfr.  

### <a name="roundup"></a>RoundUp

Zwraca wartości w **zestawie kolumn**, zaokrąglone w górę do określonej liczby cyfr.  

### <a name="toeven"></a>Toeven (toeven)

Zwraca wartości w **zestawie kolumn**, zaokrąglane do najbliższej całości, liczby parzyste.  

### <a name="toodd"></a>ToOdd (ToOdd)

Zwraca wartości w **zestawie kolumn**, zaokrąglane do najbliższej liczbą liczbą nieparzystą.  

### <a name="truncate"></a>Truncate

Obcina wartości w **ustawieniu kolumn,** usuwając wszystkie cyfry niedozwolone przez określoną dokładność.  
  
## <a name="special-math-functions"></a>Specjalne funkcje matematyczne

Ta kategoria obejmuje wyspecjalizowane funkcje matematyczne często używane w do nauki o danych. O ile nie zaznaczono inaczej, funkcja jest jednoarywna i zwraca określone obliczenia dla każdej wartości w wybranej kolumnie lub kolumnach.  

### <a name="beta"></a>Beta

Zwraca wartość funkcji beta Eulera.  

### <a name="ellipticintegrale"></a>EllipticIntegrale
Zwraca wartość niekompletnej eliptycznej całki.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Zwraca wartość kompletnej eliptycznej całki (K).  

### <a name="erf"></a>Erf

Zwraca wartość funkcji błędu.  

Funkcja błędu (zwana również funkcją błędu Gaussa) jest specjalną funkcją kształtu esicy, która jest używana w prawdopodobieństwie do opisania dyfuzji.  

### <a name="erfc"></a>Erfc ( Erfc )

Zwraca wartość funkcji błędu uzupełniającego.  

Erfc jest zdefiniowany jako 1 – erf(x).  

### <a name="erfscaled"></a>ErfScaled (erfscaled)

Zwraca wartość funkcji skalowany błąd.  

Skalowana wersja funkcji błędu może służyć do uniknięcia niedopełnienia arytmetycznego.  

### <a name="erfinverse"></a>ErfInverse (erfinverse)

Zwraca wartość funkcji odwrotnego rf.  

### <a name="exponentialintegralein"></a>WykładniczaIntegraleina

Zwraca wartość wykładniczej integralne Ei.  

### <a name="gamma"></a>Gamma

Zwraca wartość funkcji gamma.  

### <a name="gammaln"></a>GammaLn ( GammaLn )

Zwraca logarytm naturalny funkcji gamma.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Zwraca wartość wykończonej funkcji niepełnej gamma.  

Ta funkcja przyjmuje drugi argument, który może być podany jako stała lub kolumna wartości.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Zwraca wartość odwrotnej, zunifikowanej funkcji niepełnego gamma.  

Ta funkcja przyjmuje drugi argument, który może być podany jako stała lub kolumna wartości.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Zwraca wartość wykończonej funkcji niepełnej gamma.  

Ta funkcja przyjmuje drugi argument, który może być podany jako stała lub kolumna wartości.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Zwraca wartość odwrotnej uogólnionej funkcji niekompletnej gamma.

Ta funkcja przyjmuje drugi argument, który może być podany jako stała lub kolumna wartości.  

### <a name="polygamma"></a>PolyGamma

Zwraca wartość funkcji poligamimy.  

Ta funkcja przyjmuje drugi argument, który może być podany jako stała lub kolumna wartości.

##  <a name="trigonometric-functions"></a>Trygonometrycznych 

Ta kategoria iObejmuje większość ważnych funkcji trygonometrycznych i odwrotnych. Wszystkie funkcje trygonometryczne są emiaryczne i nie wymagają żadnych dodatkowych argumentów.  

### <a name="acos"></a>Acos

Oblicza arckozynę dla wartości kolumn.  

### <a name="acosdegree"></a>AcosDegree (AcosDegree)

Oblicza arckozynę wartości kolumn w stopniach.  

### <a name="acosh"></a>Acosh ( Acosh )

Oblicza łukboliczny arckozyny wartości kolumn.  

### <a name="acot"></a>Acot

Oblicza arccotangent wartości kolumn.  

### <a name="acotdegrees"></a>AcotDegrees (Stopnie)

Oblicza arccotangent wartości kolumn w stopniach.  

### <a name="acoth"></a>Pochówek

Oblicza hiperboliczny arccotangent wartości kolumn.  

### <a name="acsc"></a>z o.o.

Oblicza arccosecant wartości kolumn.  

### <a name="acscdegrees"></a>AcscDegrees (woj.

Oblicza arccosecant wartości kolumn w stopniach.  
### <a name="asec"></a>Asec

Oblicza arcsecant wartości kolumn.  

### <a name="asecdegrees"></a>AsecDegrees (AsecDegrees)

Oblicza arcsecant wartości kolumn w stopniach.  

### <a name="asech"></a>Asech ( asech )

Oblicza hiperboliczny arcsecant wartości kolumn.  

### <a name="asin"></a>Asin

Oblicza łuk wartości kolumn.  

### <a name="asindegrees"></a>AsinDegrees (woj.

Oblicza łuk wartości kolumn w stopniach.  

### <a name="asinh"></a>Asinh ( Asinh )

Oblicza hiperboliczny łuk dla wartości kolumn.  

### <a name="atan"></a>Atan

Oblicza arctangent wartości kolumn.  

### <a name="atandegrees"></a>AtanDegrees (Stopnie)

Oblicza arctangent wartości kolumn w stopniach.  

### <a name="atanh"></a>Atanh ( Atanh )

Oblicza hiperboliczny arctangent wartości kolumn.  

### <a name="cos"></a>Cos

Oblicza cosine wartości kolumny.  

### <a name="cosdegrees"></a>CosDegrees (CosDegrees)

Oblicza cosine dla wartości kolumn, w stopniach.  

### <a name="cosh"></a>Cosh

Oblicza cosine hiperboliczne dla wartości kolumn.  

### <a name="cot"></a>Cot

Oblicza cotangent dla wartości kolumny.  

### <a name="cotdegrees"></a>CotDegrees (CotDegrees)

Oblicza cotangent dla wartości kolumn, w stopniach.  

### <a name="coth"></a>Coth (coth)
Oblicza cotangent hiperboliczny dla wartości kolumn.  

### <a name="csc"></a>Csc

Oblicza cosecant dla wartości kolumn.  

### <a name="cscdegrees"></a>CscDegrees (Wydegrees)

Oblicza cosecant dla wartości kolumn, w stopniach.  

### <a name="csch"></a>Csch (wład)

Oblicza cosecans hiperboliczny dla wartości kolumn.  

### <a name="degreestoradians"></a>StopnieToRadians

Konwertuje stopnie na radiany.  

### <a name="sec"></a>S

Oblicza secant wartości kolumny.  

### <a name="asecdegrees"></a>aSecDegrees (Sekundy)

Oblicza secant dla wartości kolumn w stopniach.  

### <a name="asech"></a>aSech

Oblicza hiperboliczny secant wartości kolumn.  

### <a name="sign"></a>Znak

Zwraca znak wartości kolumny.  

### <a name="sin"></a>Sin

Oblicza sinus wartości kolumny.  

### <a name="sinc"></a>Sinc ( Sinc )

Oblicza wartość sinusoidy wartości kolumny.  

### <a name="sindegrees"></a>SinDegrees (wyssa,)

Oblicza sinus dla wartości kolumn w stopniach.  

### <a name="sinh"></a>Sinh

Oblicza sinus hiperboliczny wartości kolumn.  

### <a name="tan"></a>Tan

Oblicza styczne wartości kolumn.  

### <a name="tandegrees"></a>TanDegrees (właswoi)

Oblicza stycznej dla argumentu, w stopniach.  

### <a name="tanh"></a>Tanh

Oblicza hiperboliczny styczny wartości kolumn.  
  
## <a name="technical-notes"></a>Uwagi techniczne

Należy zachować ostrożność, gdy wybierzesz więcej niż jedną kolumnę jako drugi operator. Wyniki są łatwe do zrozumienia, jeśli operacja jest prosta, na przykład dodawanie stałej do wszystkich kolumn. 

Załóżmy, że zestaw danych ma wiele kolumn i dodać zestaw danych do siebie. W wynikach każda kolumna jest dodawana do siebie w następujący sposób:  
  
|Liczba 1|Liczba 2|Liczba 3|Dodaj(Num1_Num1)|Dodaj(Num2_Num2)|Dodaj(Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Jeśli konieczne jest wykonanie bardziej złożonych obliczeń, można wykonać wiele wystąpień **zastosuj operację matematyczną**. Na przykład można dodać dwie kolumny za pomocą jednego wystąpienia **Zastosuj operację matematyczną**, a następnie użyć innego wystąpienia **Zastosuj operację matematyczną,** aby podzielić sumę przez stałą, aby uzyskać średnią.  
  
Alternatywnie, użyj jednego z następujących modułów, aby wykonać wszystkie obliczenia naraz, przy użyciu skryptu SQL, R lub Python:
 
+ [Wykonywanie skryptu języka R](execute-r-script.md)
+ [Wykonywanie skryptu w języku Python](execute-python-script.md)
+ [Stosowanie przekształcenia SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
