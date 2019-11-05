---
title: 'Wybór funkcji oparty na filtrowaniu: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu wyboru funkcji opartego na filtrze w usłudze Azure Machine Learning, aby identyfikować funkcje w zestawie danych z największą potęgą predykcyjną.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ac1421c93f1a4ca42d7f1d94bb898c423c380a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517971"
---
# <a name="filter-based-feature-selection"></a>Wybór funkcji oparty na filtrze

W tym artykule opisano, jak używać modułu [wyboru funkcji opartego na filtrze](filter-based-feature-selection.md) w programie Azure Machine Learning Designer (wersja zapoznawcza), aby identyfikować kolumny w wejściowym zestawie danych, które mają największą potęgę predykcyjną. 

Ogólnie rzecz biorąc, *wybór funkcji* odnosi się do procesu stosowania testów statystycznych do danych wejściowych, z uwzględnieniem określonych danych wyjściowych, aby określić, które kolumny są bardziej predykcyjne dla danych wyjściowych. Moduł [wyboru funkcji opartej na filtrze](filter-based-feature-selection.md) udostępnia wiele algorytmów wyboru funkcji do wyboru, w tym metod korelacji, takich jak korelacja Pearsona i wartości chi-kwadrat. 

W przypadku korzystania z modułu [wyboru funkcji opartego na filtrze](filter-based-feature-selection.md) należy podać zestaw danych, określić kolumnę, która zawiera etykietę lub zmienną zależną, a następnie określić pojedynczą metodę do użycia podczas mierzenia ważności funkcji.

Moduł wyprowadza zestaw danych, który zawiera kolumny najlepszych funkcji, zgodnie z możliwością predykcyjną. Powoduje także wyprowadzanie nazw funkcji i ich ocen z wybranej metryki.  

### <a name="what-is-filter-based-feature-selection-and-why-use-it"></a>Co to jest wybór funkcji oparty na filtrach i dlaczego go używać?  

Ten moduł dla wyboru funkcji jest nazywany "opartym na filtrowaniu", ponieważ jest używana wybrana Metryka do identyfikowania nieistotnych atrybutów i odfiltrowania nadmiarowych kolumn z modelu.  Wybierasz jedną miarę statystyczną, która będzie odpowiadała danym, a moduł oblicza ocenę dla każdej kolumny funkcji. Kolumny są zwracane w rankingu według ich wyników funkcji. 

Po wybraniu odpowiednich funkcji można poprawić dokładność i wydajność klasyfikacji. 

Zwykle używane są tylko kolumny z najlepszymi wynikami w celu utworzenia modelu predykcyjnego. Kolumny z nieprawidłowymi wynikami wyboru funkcji mogą pozostać w zestawie danych i ignorowane podczas kompilowania modelu.  

### <a name="how-to-choose-a-feature-selection-metric"></a>Jak wybrać metrykę wyboru funkcji

**Wybór funkcji oparty na filtrze** zapewnia różne metryki do oceny wartości informacji w każdej kolumnie.  Ta sekcja zawiera ogólny opis każdej metryki i sposób jej zastosowania. Dodatkowe wymagania dotyczące korzystania z każdej z metryk są podane w sekcji [Uwagi techniczne](#technical-notes) i [instrukcje](#how-to-configure-filter-based-feature-selection) dotyczące konfigurowania poszczególnych modułów.

-   **Korelacja Pearsona**  

     Statystyka korelacji Pearsona lub współczynnik korelacji Pearsona są również znane w modelach statystycznych jako wartość `r`. Dla każdej dwóch zmiennych zwraca wartość wskazującą siłę korelacji

     Współczynnik korelacji Pearsona jest obliczany przez przejęcie kowariancji dwóch zmiennych i podzielenie przez iloczyn standardowych odchyleń. Nie ma to wpływ na współczynnik zmian skali w dwóch zmiennych.  

-   **Chi kwadratowy**  

     Dwukierunkowy test chi-kwadrat jest metodą statystyczną, która określa, jak blisko oczekiwanych wartości są rzeczywiste wyniki. Metoda zakłada, że zmienne są losowo i rysowane z odpowiednich próbek zmiennych niezależnych. Wynikowa Statystyka chi-kwadrat wskazuje, jak daleko wynika z oczekiwanego (losowego) wyniku.  


> [!TIP]
> Jeśli potrzebujesz innej opcji niestandardowej metody wyboru funkcji, użyj modułu [skryptu języka R](execute-r-script.md) . 
##  <a name="how-to-configure-filter-based-feature-selection"></a>Jak skonfigurować wybór funkcji oparty na filtrze

Wybierasz standardową metrykę statystyczną, a moduł oblicza korelację między parą kolumn: kolumną etykieta i kolumną funkcji.

1.  Dodaj moduł **wyboru funkcji oparty na filtrze** do potoku. Można go znaleźć w kategorii **wybór funkcji** w projektancie.

2. Połącz zestaw danych wejściowych zawierający co najmniej dwie kolumny, które są potencjalnymi funkcjami.  

    Aby mieć pewność, że kolumna powinna zostać przeanalizowana i wygenerowane wyniki funkcji, użyj modułu [Edytowanie metadanych](edit-metadata.md) , aby ustawić atrybut **isfeature** . 

    > [!IMPORTANT]
    > Upewnij się, że kolumny, które są udostępniane jako dane wejściowe, są potencjalnymi funkcjami. Na przykład kolumna, która zawiera pojedynczą wartość nie ma wartości informacji.
    >
    > Jeśli wiesz, że istnieją kolumny, które mogłyby wprowadzać złe funkcje, możesz je usunąć z wybranej kolumny. Można również użyć modułu [Edytowanie metadanych](edit-metadata.md) , aby oznaczyć je jako **kategorii**. 
3.  W przypadku **metody oceny funkcji**należy wybrać jedną z następujących ustalonych metod statystycznych do użycia podczas obliczania wyników.  

    | Metoda              | Wymagania                             |
    | ------------------- | ---------------------------------------- |
    | Korelacja Pearsona | Etykieta może być tekstem lub cyfrą. Funkcje muszą być liczbowe. |
    Chi kwadratowy| Etykiety i funkcje mogą być tekstowe lub liczbowe. Ta metoda służy do obliczania ważności funkcji dla dwóch kolumn kategorii.|

    > [!TIP]
    > Jeśli zmienisz wybraną metrykę, wszystkie pozostałe zaznaczenia zostaną zresetowane, więc pamiętaj, aby ustawić tę opcję jako pierwsze!).
4.  Wybierz opcję **Pracuj w przypadku kolumn funkcji tylko** w celu wygenerowania oceny tylko dla tych kolumn, które zostały wcześniej oznaczone jako funkcje. 

    Jeśli wyłączysz tę opcję, moduł utworzy ocenę dla każdej kolumny, która w przeciwnym razie spełnia kryteria, do liczby kolumn określonych w polu **Liczba żądanych funkcji**.  

5.  Dla **kolumny Target**kliknij **Selektor kolumny uruchamiania** , aby wybrać kolumnę etykieta według nazwy lub indeksu (indeksy są oparte na jednym z nich).  

     Kolumna etykieta jest wymagana dla wszystkich metod, które obejmują korelację statystyczną. Moduł zwraca błąd czasu projektowania, jeśli nie wybrano kolumny etykiety ani kolumn z wieloma etykietami. 

6.  W przypadku **liczby żądanych funkcji**wpisz liczbę kolumn funkcji, które mają zostać zwrócone w wyniku.  

     - Minimalna liczba funkcji, które można określić, to 1, ale zalecamy zwiększenie tej wartości.  

     - Jeśli określona liczba żądanych funkcji jest większa niż liczba kolumn w zestawie danych, zostaną zwrócone wszystkie funkcje, nawet te z wynikami zerowymi.  

    - Jeśli określisz mniejszą liczbę kolumn wyników niż kolumny funkcji, funkcje są klasyfikowane według malejącego wyniku i zwracane są tylko najważniejsze funkcje. 

7.  Uruchom potok lub wybierz moduł [wyboru funkcji oparty na filtrze](filter-based-feature-selection.md) , a następnie kliknij przycisk **Uruchom wybrane**.


## <a name="results"></a>Wyniki

Po zakończeniu przetwarzania:

+ Aby wyświetlić pełną listę przeanalizowanych kolumn funkcji i ich ocen, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **funkcje**, a następnie kliknij polecenie **Wizualizuj**.  

+ Aby wyświetlić zestaw danych, który jest generowany na podstawie kryteriów wyboru funkcji, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **zestaw danych**, a następnie kliknij polecenie **Wizualizuj**. 

Jeśli zestaw danych zawiera mniejszą liczbę kolumn niż oczekiwano, sprawdź ustawienia modułu i typy danych kolumn dostarczonych jako dane wejściowe. Jeśli na przykład ustawisz **liczbę żądanych funkcji** na 1, wyjściowy zestaw danych zawiera tylko dwie kolumny: kolumnę Label oraz kolumnę o największej rangi.


##  <a name="technical-notes"></a>Uwagi techniczne  

### <a name="implementation-details"></a>Szczegóły implementacji

Jeśli używasz korelacji Pearsona dla funkcji liczbowej i etykiety kategorii, wynik funkcji jest obliczany w następujący sposób:  

1.  Dla każdego poziomu w kolumnie kategorii Obliczanie średniej warunkowej kolumny liczbowej.  

2.  Skorelowanie kolumny warunkowego z kolumną liczbową.  

### <a name="requirements"></a>Wymagania  

-   Nie można wygenerować wyniku wyboru funkcji dla żadnej kolumny, która jest oznaczona jako **etykieta** lub jako kolumna **oceny** .  

-   Jeśli próbujesz użyć metody oceniania z kolumną typu danych nieobsługiwanego przez metodę, moduł zgłosi błąd lub do kolumny zostanie przypisany wynik zerowy.  

-   Jeśli kolumna zawiera wartości logiczne (true/false), są one przetwarzane jako prawdziwe = 1 i false = 0.  

-   Kolumna nie może być funkcją, jeśli została wyoznaczona jako **etykieta** lub **wynik**.  

### <a name="how-missing-values-are-handled"></a>Jak są obsługiwane brakujące wartości  

-   Nie można określić jako kolumny docelowej (etykieta) żadnej kolumny, która ma wszystkie brakujące wartości.  

-   Jeśli kolumna zawiera brakujące wartości, są one ignorowane podczas obliczania wyniku dla kolumny.  

-   Jeśli kolumna wyznaczony jako kolumna funkcji ma wszystkie brakujące wartości, zostanie przypisany wynik zero.   


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 

