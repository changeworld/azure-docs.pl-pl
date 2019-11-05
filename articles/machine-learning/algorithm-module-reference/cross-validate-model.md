---
title: 'Model krzyżowego sprawdzania poprawności: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu wielovalidate model w ramach usługi Azure Machine Learning do krzyżowego sprawdzania poprawności szacunków parametrów dla modeli klasyfikacji lub regresji przez Partycjonowanie danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5eea61ee8284010531e80e17bf1110ab470d04c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512849"
---
# <a name="cross-validate-model"></a>Model krzyżowego sprawdzania poprawności

W tym artykule opisano sposób korzystania z modułu **weryfikacji krzyżowej** w programie Azure Machine Learning Designer (wersja zapoznawcza). *Wzajemne sprawdzanie poprawności* jest ważnym sposobem często używanym w uczeniu maszynowym do oceny zmienności zestawu danych i niezawodności dowolnego modelu przeszkolonego przy użyciu tych danych.  

Moduł **sprawdzania poprawności krzyżowej** przyjmuje jako wejściowy zestaw danych, wraz z niesprawdzonym modelem klasyfikacji lub regresji. Dzieli zestaw danych na pewną liczbę podzestawów (*zgięcia*), kompiluje model dla każdego zgięcia, a następnie zwraca zestaw statystyk dokładności dla każdego złożenia. Porównując statystyki dokładności dla wszystkich zgięciów, można interpretować jakość zestawu danych i zrozumieć, czy model jest podatny na różnice w danych.  

Funkcja weryfikacji krzyżowej zwraca również przewidywane wyniki i prawdopodobieństwa dla zestawu danych, dzięki czemu można ocenić niezawodność prognoz.  

### <a name="how-cross-validation-works"></a>Jak działa sprawdzanie krzyżowe

1. Krzyżowe sprawdzanie poprawności dzieli dane szkoleniowe na wiele partycji, nazywane również *zgięciami*. 

    + Algorytm domyślnie przyjmuje wartość 10, jeśli zestaw danych nie został wcześniej podzielony na partycje. 
    + Aby podzielić zestaw danych na inną liczbę zagięć, można użyć [partycji i przykładowego](partition-and-sample.md) modułu i wskazać, ile zagięć ma być używanych.  

2.  Moduł ustawia dane w zgięciu 1 do użycia na potrzeby walidacji (jest to czasami nazywane *wstrzymania zgięciem*) i używa pozostałych zagięć do uczenia modelu. 

    Jeśli na przykład utworzysz pięć zagięć, moduł wygeneruje pięć modeli podczas weryfikacji krzyżowej, każdy model przeszkolony przy użyciu 4/5 danych i przetestowany w pozostałej 1/5.  

3.  Podczas testowania modelu dla każdego złożenia są oceniane różne statystyki dokładności. Używane statystyki są zależne od typu ocenianego modelu. Różne statystyki są używane do obliczania modeli klasyfikacji a modeli regresji.  

4.  Po zakończeniu procesu kompilowania i oceny dla wszystkich zgięcia **model krzyżowy** umożliwia wygenerowanie zestawu metryk wydajności i wyników oceny dla wszystkich danych. Należy przejrzeć te metryki, aby sprawdzić, czy pojedyncze zgięcie ma szczególnie wysoką lub niską dokładność 

### <a name="advantages-of-cross-validation"></a>Zalety krzyżowego sprawdzania poprawności

Inny i typowy sposób oceny modelu polega na podzieleniu danych na zestawienie szkoleniowe i testowe przy użyciu danych z [podziałem](split-data.md), a następnie zweryfikowaniu modelu na danych szkoleniowych. Jednak wzajemne sprawdzanie poprawności zapewnia pewne korzyści:  

-   Wzajemne sprawdzanie poprawności używa większej ilości danych testowych.

     Wzajemne sprawdzanie poprawności mierzy wydajność modelu z określonymi parametrami w większym obszarze danych. Oznacza to, że krzyżowe sprawdzanie poprawności używa całego zestawu danych szkoleniowych dla szkolenia i oceny, a nie z części. Z drugiej strony, jeśli sprawdzasz poprawność modelu przy użyciu danych wygenerowanych z losowego podziału, zazwyczaj szacujesz model tylko na 30% lub mniej dostępnych danych.  

     Jednak ze względu na to, że wzajemne sprawdzanie poprawności pociąga i weryfikuje model wielokrotnie w większym zestawie danych, jest znacznie bardziej obliczeniowe i trwa znacznie dłużej niż sprawdzanie poprawności losowego podziału.  

-   Wzajemne sprawdzanie poprawności oblicza zestaw danych, a także model.

     Wzajemne sprawdzanie poprawności nie mierzy dokładnej dokładności modelu, ale daje również pewne pomysły dotyczące reprezentatywnego zestawu danych oraz sposobu, w jaki model może być zróżnicowany w danych.  

## <a name="how-to-use-cross-validate-model"></a>Jak używać modelu weryfikacji krzyżowej

Jeśli zestaw danych jest duży, proces sprawdzania poprawności może zająć dużo czasu.  Z tego względu można użyć **modelu weryfikacji krzyżowej** w początkowej fazie kompilowania i testowania modelu, aby oszacować dobrą liczbę parametrów modelu (przy założeniu, że czas obliczeń jest dopuszczalny), a następnie nauczyć i oszacować model przy użyciu nawiązane parametry z [modelem uczenia](train-model.md) i [szacowanie modułów modelowych](evaluate-model.md) .

W tym scenariuszu nauczysz się i testujesz model przy użyciu **modelu krzyżowego sprawdzania poprawności**.

1. Dodaj moduł **Odweryfikuj model** do potoku. Można go znaleźć w programie Azure Machine Learning Designer, w kategorii **ocena & model oceniania** . 

2. Połącz dane wyjściowe z dowolnym modelem **klasyfikacji** lub **regresji** . 

    Na przykład jeśli używasz **maszyny Bayesa z dwoma klasami** do klasyfikacji, skonfiguruj model przy użyciu żądanych parametrów, a następnie przeciągnij łącznik z wyciągniętego portu **modelu** klasyfikatora do zgodnego portu **krzyżowego sprawdzania poprawności Model**. 

    > [!TIP] 
    > Model nie musi być szkolony, ponieważ **model weryfikacji krzyżowej** automatycznie pociąga model jako część oceny.  
3.  W porcie **zestawu danych** **modelu krzyżowego sprawdzania poprawności**Połącz każdy zestaw danych szkolenia z etykietą.  

4.  W okienku **Właściwości** **modelu krzyżowego sprawdzania poprawności**kliknij pozycję **Uruchom selektor kolumny** i wybierz pojedynczą kolumnę zawierającą etykietę klasy lub wartość przewidywalną. 

5. Ustaw wartość parametru **losowego inicjatora** , jeśli chcesz, aby można było powtórzyć wyniki wzajemnej weryfikacji dla kolejnych przebiegów na tych samych danych.  

6.  Uruchamianie potoku.

7. Opis raportów można znaleźć w sekcji [Results (wyniki](#results) ).

    Aby pobrać kopię modelu do ponownego użycia później, kliknij prawym przyciskiem myszy dane wyjściowe modułu zawierającego algorytm (na przykład **Bayesa**), a następnie kliknij pozycję **Zapisz jako przeszkolony model**.

## <a name="results"></a>Wyniki

Po ukończeniu wszystkich iteracji **model krzyżowego sprawdzania poprawności** tworzy wyniki dla całego zestawu danych, a także metryki wydajności, których można użyć do oceny jakości modelu.

### <a name="scored-results"></a>Wyniki oceny

Pierwsze dane wyjściowe modułu zawierają dane źródłowe dla każdego wiersza, a także niektóre wartości przewidywane i powiązane prawdopodobieństwa. 

Aby wyświetlić te wyniki, w potoku kliknij prawym przyciskiem myszy moduł **modelu krzyżowego sprawdzania poprawności** , wybierz pozycję **wyniki oceny**i kliknij polecenie **Wizualizuj**.

| Nazwa nowej kolumny      | Opis                              |
| -------------------- | ---------------------------------------- |
| Etykiety z wynikami        | Ta kolumna jest dodawana na końcu zestawu danych i zawiera wartość przewidywaną dla każdego wiersza |
| Oceny prawdopodobieństwa | Ta kolumna jest dodawana na końcu zestawu danych i wskazuje szacowane prawdopodobieństwo wartości w **etykietach z wynikami**. |
| Numer zgięcia          | Wskazuje indeks (0), w którym zapisywany jest każdy wiersz danych podczas weryfikacji krzyżowej. |

 ### <a name="evaluation-results"></a>Wyniki oceny

Drugi raport jest pogrupowany według zgięcia. Należy pamiętać, że podczas wykonywania, przeprowadzenie **walidacji modelu** polega na losowo podzielić dane szkoleniowe na *n* zgięcia (domyślnie 10). W każdej iteracji w zestawie danych, **krzyżowe sprawdzanie poprawności modelu** używa jednego zgięcia jako zestawu danych walidacji i używa pozostałych zgięcia *n-1* do uczenia modelu. Każdy z modeli *n* jest testowany na dane we wszystkich innych zgięciach.

W tym raporcie zgięcia są wyświetlane według wartości indeksu w kolejności rosnącej.  Aby zamówić w dowolnej innej kolumnie, można zapisać wyniki jako zestaw danych.

Aby wyświetlić te wyniki, w potoku kliknij prawym przyciskiem myszy moduł **model sprawdzania krzyżowego** , wybierz pozycję **wyniki oceny według**złożenia i kliknij polecenie **Wizualizuj**.


|Nazwa kolumny| Opis|
|----|----|
|Numer zgięcia| Identyfikator dla każdego złożenia. Jeśli utworzono 5 operacji składania, będzie dostępnych 5 podzestawów danych z numerami 0 – 4.
|Liczba przykładów w złożeniu|Liczba wierszy przypisanych do każdego zgięcia. Powinny one być w przybliżeniu równe. |


Ponadto w zależności od typu modelu, który oceniasz, są uwzględniane następujące metryki. 

+ **Modele klasyfikacji**: dokładność, odwołanie, F-Score, AUC, dokładność  

+ **Modele regresji**: średni błąd bezwzględny, główny, średni, błąd względny, błąd bezwzględny, względny błąd kwadratowy i współczynnik wyznaczania


## <a name="technical-notes"></a>Uwagi techniczne  

+ Najlepszym rozwiązaniem jest ujednolicenie zestawów danych przed ich użyciem do weryfikacji krzyżowej. 

+ Ze względu na to, że model **krzyżowo sprawdza** się i sprawdza model wielokrotnie, jest znacznie bardziej obliczeniowy i trwa dłużej niż w przypadku zweryfikowania modelu przy użyciu losowo podzielonego zestawu danych. 

+ Nie ma potrzeby dzielenia zestawu danych na zestawy szkoleniowe i testowe w przypadku używania krzyżowego sprawdzania poprawności do mierzenia dokładności modelu. 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 

