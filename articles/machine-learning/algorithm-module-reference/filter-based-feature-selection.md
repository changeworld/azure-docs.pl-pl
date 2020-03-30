---
title: 'Wybór funkcji oparty na filtrze: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z modułu wyboru funkcji opartych na filtrze w usłudze Azure Machine Learning, aby zidentyfikować funkcje w zestawie danych o największej mocy predykcyjnej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c009a98931240e92527035e51fdce3f1c92f5212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477599"
---
# <a name="filter-based-feature-selection"></a>Wybór funkcji oparty na filtrze

W tym artykule opisano, jak używać modułu wyboru funkcji opartych na filtrze w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Ten moduł pomaga zidentyfikować kolumny w zestawie danych wejściowych, które mają największą moc predykcyjną. 

Ogólnie rzecz *biorąc, wybór funkcji* odnosi się do procesu stosowania testów statystycznych do danych wejściowych, biorąc pod uwagę określone dane wyjściowe. Celem jest określenie, które kolumny są bardziej predykcyjne danych wyjściowych. Moduł wyboru funkcji oparty na filtrze udostępnia wiele algorytmów wyboru funkcji do wyboru. Moduł zawiera metody korelacji, takie jak korelacja Pearsona i wartości chi-kwadrat. 

Korzystając z modułu Wyboru funkcji opartych na filtrze, należy podać zestaw danych i zidentyfikować kolumnę zawierającą etykietę lub zmienną zależną. Następnie należy określić jedną metodę, która ma być używana w pomiarze ważności operacji.

Moduł wyprowadza zestaw danych, który zawiera najlepsze kolumny funkcji, zgodnie z danymi o mocy predykcyjnej. Wyprowadza również nazwy funkcji i ich wyniki z wybranej metryki.  

## <a name="what-filter-based-feature-selection-is"></a>Jaki jest wybór funkcji oparty na filtrze  

Ten moduł do wyboru funkcji jest nazywany "oparty na filtrze", ponieważ używasz wybranej metryki, aby znaleźć nieistotne atrybuty. Następnie odfiltrowuj nadmiarowe kolumny z modelu. Wybierz jedną miarę statystyczną, która odpowiada danym, a moduł oblicza wynik dla każdej kolumny funkcji. Kolumny są zwracane w rankingu według ich wyników funkcji. 

Wybierając odpowiednie funkcje, można potencjalnie poprawić dokładność i wydajność klasyfikacji. 

Zazwyczaj używasz tylko kolumny z najlepszymi wynikami do tworzenia modelu predykcyjnego. Kolumny ze słabymi wynikami wyboru funkcji można pozostawić w zestawie danych i zignorować podczas tworzenia modelu.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Jak wybrać metrykę wyboru funkcji

Moduł wybór funkcji oparty na filtrze zawiera wiele metryk do oceny wartości informacji w każdej kolumnie. Ta sekcja zawiera ogólny opis każdej metryki i sposobu jej zastosowania. Dodatkowe wymagania dotyczące używania każdej metryki można znaleźć w [uwagach technicznych](#technical-notes) i w [instrukcjach](#how-to-configure-filter-based-feature-selection) konfigurowania każdego modułu.

-   **Korelacja Pearsona**  

    Statystyka korelacji Pearsona lub współczynnik korelacji Pearsona są również znane w modelach statystycznych jako `r` wartość. Dla dowolnych dwóch zmiennych zwraca wartość, która wskazuje siłę korelacji.

    Współczynnik korelacji Pearsona jest obliczany przez przyjęcie kowariancji dwóch zmiennych i podzielenie przez iloczyn ich odchyleń standardowych. Zmiany skali w dwóch zmiennych nie wpływają na współczynnik.  

-   **Chi kwadrat**  

    Dwukierunkowy test chi-kwadrat jest metodą statystyczną, która mierzy, jak blisko oczekiwanych wartości są do rzeczywistych wyników. Metoda zakłada, że zmienne są losowe i pobierane z odpowiedniej próbki zmiennych niezależnych. Wynikowa statystyka chi-kwadrat wskazuje, jak daleko są wyniki od oczekiwanego (losowego) wyniku.  


> [!TIP]
> Jeśli potrzebujesz innej opcji dla metody wyboru operacji niestandardowej, użyj modułu [Wykonaj skrypt R.](execute-r-script.md) 

## <a name="how-to-configure-filter-based-feature-selection"></a>Jak skonfigurować wybór funkcji opartych na filtrze

Wybierz standardową metrykę statystyczną. Moduł oblicza korelację między parą kolumn: kolumną etykiety a kolumną obiektową.

1.  Dodaj moduł wyboru funkcji opartych na filtrze do potoku. Można go znaleźć w kategorii **Wybór funkcji** w projektancie.

2. Połącz wejściowy zestaw danych zawierający co najmniej dwie kolumny, które są potencjalnymi funkcjami.  

    Aby upewnić się, że kolumna jest analizowana i generowany jest wynik funkcji, użyj modułu [Edytuj metadane,](edit-metadata.md) aby ustawić atrybut **IsFeature.** 

    > [!IMPORTANT]
    > Upewnij się, że kolumny, które udostępniasz jako dane wejściowe, są potencjalnymi funkcjami. Na przykład kolumna, która zawiera pojedynczą wartość nie ma wartości informacyjnej.
    >
    > Jeśli wiesz, że niektóre kolumny będą tworzyć złe funkcje, możesz usunąć je z zaznaczenia kolumny. Można również użyć modułu [Edytuj metadane,](edit-metadata.md) aby oznaczyć je jako **kategoryczne**. 
3.  W przypadku **metody oceniania funkcji**wybierz jedną z następujących ustalonych metod statystycznych, które mają być używane do obliczania wyników.  

    | Metoda              | Wymagania                             |
    | ------------------- | ---------------------------------------- |
    | Korelacja Pearsona | Etykieta może być tekstem lub numerem. Operacje muszą być numeryczne. |
    Chi kwadrat| Etykiety i funkcje mogą być tekstowe lub numeryczne. Ta metoda służy do obliczania ważności funkcji dla dwóch kolumn kategorii.|

    > [!TIP]
    > Jeśli zmienisz wybraną metrykę, wszystkie inne zaznaczenia zostaną zresetowane. Więc pamiętaj, aby najpierw ustawić tę opcję.
4.  Wybierz opcję **Działaj tylko na kolumnach elementów,** aby wygenerować wynik tylko dla kolumn, które wcześniej były oznaczone jako obiekty. 

    Jeśli wyczyścisz tę opcję, moduł utworzy wynik dla dowolnej kolumny, która w przeciwnym razie spełnia kryteria, do liczby kolumn określonych w **Liczba żądanych funkcji**.  

5.  W **polu Kolumna Docelowa**wybierz **opcję Uruchom selektor kolumny,** aby wybrać kolumnę etykiety według nazwy lub indeksu. (Indeksy są oparte na jednej).  
    Kolumna etykiety jest wymagana dla wszystkich metod, które obejmują korelację statystyczną. Moduł zwraca błąd czasu projektowania, jeśli wybierzesz żadną kolumnę etykiety lub wiele kolumn etykiet. 

6.  W polu **Liczba żądanych operacji**wprowadź liczbę kolumn elementów, które mają zostać zwrócone w rezultacie:  

    - Minimalna liczba obiektów, które można określić jest jeden, ale zaleca się zwiększenie tej wartości.  

    - Jeśli określona liczba żądanych operacji jest większa niż liczba kolumn w zestawie danych, zwracane są wszystkie operacje. Zwracane są nawet funkcje z zerowymi wynikami.  

    - Jeśli określisz mniej kolumn wyników niż kolumnach obiektów, operacje są klasyfikowane według malejącego wyniku. Zwracane są tylko najważniejsze funkcje. 

7.  Prześlij potok lub wybierz moduł Wyboru operacji opartych na filtrze, a następnie wybierz pozycję **Uruchom zaznaczone**.


## <a name="results"></a>Wyniki

Po zakończeniu przetwarzania:

+ Aby wyświetlić pełną listę analizowanych kolumn funkcji i ich wyników, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**.  

+ Aby wyświetlić zestaw danych na podstawie kryteriów wyboru obiektu, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**. 

Jeśli zestaw danych zawiera mniej kolumn niż oczekiwano, sprawdź ustawienia modułu. Sprawdź również typy danych kolumn dostarczonych jako dane wejściowe. Na przykład jeśli **ustawisz liczbę żądanych obiektów** na 1, wyjściowy zestaw danych zawiera tylko dwie kolumny: kolumnę etykiety i najbardziej uszeregowę kolumnę funkcji.


##  <a name="technical-notes"></a>Uwagi techniczne  

### <a name="implementation-details"></a>Szczegóły implementacji

Jeśli używasz korelacji Pearsona na operacji numerycznej i kategorycznej, wynik operacji jest obliczany w następujący sposób:  

1.  Dla każdego poziomu w kolumnie kategorycznej oblicz średnią warunkową kolumny numerycznej.  

2.  Skorelować kolumnę środków warunkowych z kolumną numeryczną.  

### <a name="requirements"></a>Wymagania  

-   Wynik wyboru funkcji nie może być generowany dla żadnej kolumny, która jest oznaczona jako kolumna **Etykieta** lub **Wynik.**  

-   Jeśli spróbujesz użyć metody oceniania z kolumną typu danych, której metoda nie obsługuje, moduł spowoduje wyświetlenie błędu. Lub zerowy wynik zostanie przypisany do kolumny.  

-   Jeśli kolumna zawiera wartości logiczne (prawda/fałsz), są one przetwarzane jako `True = 1` i `False = 0`.  

-   Kolumna nie może być funkcją, jeśli została oznaczona jako **Etykieta** lub **Wynik**.  

### <a name="how-missing-values-are-handled"></a>Jak obsługiwane są brakujące wartości  

-   Nie można określić jako kolumny docelowej (etykiety) żadnej kolumny, w której brakuje wszystkich wartości.  

-   Jeśli kolumna zawiera brakujące wartości, moduł ignoruje je podczas obliczania wyniku dla kolumny.  

-   Jeśli kolumna wyznaczona jako kolumna obiektowa zawiera wszystkie brakujące wartości, moduł przypisuje wynik zerowy.   


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 

