---
title: 'Model cross validate: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu modelu krzyżowego sprawdzania poprawności w usłudze Azure Machine Learning do sprawdzania poprawności krzyżowej oszacowań parametrów dla modeli klasyfikacji lub regresji przez partycjonowanie danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 7550bb7c6bbf7602245f9a9f1ac006ce693b36a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477650"
---
# <a name="cross-validate-model"></a>Krzyżowa weryfikacja modelu

W tym artykule opisano, jak używać modułu Cross Validate Model w projektancie usługi Azure Machine Learning (wersja zapoznawcza). *Krzyżowe sprawdzanie poprawności* jest techniką często używaną w uczeniu maszynowym do oceny zmienności zestawu danych i niezawodności dowolnego modelu przeszkolonego za pomocą tych danych.  

Moduł Cross Validate Model przyjmuje jako wejście zestaw danych oznaczony etykietą, wraz z nieprzeszkolonym modelem klasyfikacji lub regresji. Dzieli zestaw danych na pewną liczbę*podzbiorów (fałdy),* tworzy model na każdym krotnie, a następnie zwraca zestaw statystyk dokładności dla każdego krotnie. Porównując statystyki dokładności dla wszystkich zagęszków, można interpretować jakość zestawu danych. Następnie można zrozumieć, czy model jest podatny na różnice w danych.  

Cross Validate Model zwraca również przewidywane wyniki i prawdopodobieństwa dla zestawu danych, dzięki czemu można ocenić niezawodność prognoz.  

### <a name="how-cross-validation-works"></a>Jak działa krzyżowa walidacja

1. Krzyżowa walidacja losowo dzieli dane szkoleniowe na fałdy. 

   Algorytm domyślnie 10 zagięci, jeśli wcześniej nie partycjonowane zestawu danych. Aby podzielić zestaw danych na inną liczbę zagęszków, można użyć modułu [Partycja i Próbka](partition-and-sample.md) i wskazać liczbę zagęszków do użycia.  

2.  Moduł odstawi dane w złożeniu 1 do użycia do sprawdzania poprawności. (Jest to czasami nazywane *krotnie wstrzymania*.) Moduł używa pozostałych zagęszków do szkolenia modelu. 

    Na przykład jeśli utworzysz pięć razy, moduł generuje pięć modeli podczas sprawdzania poprawności krzyżowej. Moduł trenuje każdy model przy użyciu czterech piątych danych. Testuje każdy model na pozostałej jednej piątej.  

3.  Podczas testowania modelu dla każdego krotnie moduł ocenia wiele statystyk dokładności. Statystyki używane przez moduł zależą od typu ocenianego modelu. Różne statystyki są używane do oceny modeli klasyfikacji w porównaniu do modeli regresji.  

4.  Po zakończeniu procesu tworzenia i oceny dla wszystkich zagięć, Cross Validate Model generuje zestaw metryk wydajności i wyniki uzyskane dla wszystkich danych. Przejrzyj te dane, aby zobaczyć, czy pojedynczy krotnie ma wysoką lub niską dokładność. 

### <a name="advantages-of-cross-validation"></a>Zalety krzyżowej walidacji

Innym i powszechnym sposobem oceny modelu jest podzielenie danych na zestaw szkoleń i testów przy użyciu [podziału danych,](split-data.md)a następnie sprawdzenie poprawności modelu na danych szkoleniowych. Ale krzyżowa walidacja oferuje pewne zalety:  

-   Krzyżowa walidacja używa więcej danych testowych.

    Krzyżowa walidacja mierzy wydajność modelu z określonymi parametrami w większej przestrzeni danych. Oznacza to, że krzyżowe sprawdzanie poprawności używa całego zestawu danych szkolenia dla szkolenia i oceny, zamiast części. Z drugiej strony, jeśli sprawdź poprawność modelu przy użyciu danych generowanych z losowego podziału, zazwyczaj oceniasz model tylko na 30 procent lub mniej dostępnych danych.  

    Jednak ponieważ krzyżowa sprawdzanie poprawności pociągów i sprawdza poprawność modelu wiele razy w większym zestawie danych, jest znacznie bardziej obciążają obliczeniowo. Trwa znacznie dłużej niż sprawdzanie poprawności na losowym podziału.  

-   Krzyżowe sprawdzanie poprawności ocenia zarówno zestaw danych, jak i model.

    Krzyżowe sprawdzanie poprawności nie po prostu zmierzyć dokładność modelu. Daje również pewne pojęcie o tym, jak reprezentatywny jest zestaw danych i jak wrażliwe modelu może być na różnice w danych.  

## <a name="how-to-use-cross-validate-model"></a>Jak korzystać z modelu Cross Validate

Sprawdzanie poprawności krzyżowej może zająć dużo czasu, aby uruchomić, jeśli zestaw danych jest duży.  Dlatego można użyć Cross Validate Model w początkowej fazie tworzenia i testowania modelu. W tej fazie można ocenić dobroć parametrów modelu (przy założeniu, że czas obliczeń jest dopuszczalny). Następnie można trenować i oceniać model przy użyciu ustalonych parametrów za pomocą [train model](train-model.md) i [oceń modelu](evaluate-model.md) modułów.

W tym scenariuszu zarówno pociągu i przetestować model przy użyciu cross validate modelu.

1. Dodaj moduł Cross Validate Model do potoku. Można go znaleźć w projektancie usługi Azure Machine Learning, w **kategorii Ocena & ocena modelu.** 

2. Połącz dane wyjściowe dowolnego modelu klasyfikacji lub regresji. 

    Na przykład jeśli używasz **dwóch klas promowane drzewo decyzji** do klasyfikacji, należy skonfigurować model z parametrów, które chcesz. Następnie przeciągnij łącznik z portu **modelu nieprzeszkolonego** klasyfikatora do pasującego portu Cross Validate Model. 

    > [!TIP] 
    > Nie trzeba trenować modelu, ponieważ cross-validate model automatycznie trenuje modelu w ramach oceny.  
3.  Na **porcie Zestawu danych** modelu krzyżowego sprawdzania poprawności połącz dowolny zestaw danych szkolenia z etykietami.  

4.  W prawym panelu Cross Validate Model kliknij pozycję **Edytuj kolumnę**. Wybierz pojedynczą kolumnę zawierającą etykietę klasy lub przewidywalną wartość. 

5. Ustaw wartość dla **Random seed** parametr, jeśli chcesz powtórzyć wyniki krzyżowego sprawdzania poprawności w kolejnych uruchamia na tych samych danych.  

6. Prześlij potok.

7. Zobacz sekcję [Wyniki,](#results) aby uzyskać opis raportów.

## <a name="results"></a>Wyniki

Po zakończeniu wszystkich iteracji Cross Validate Model tworzy wyniki dla całego zestawu danych. Tworzy również metryki wydajności, których można użyć do oceny jakości modelu.

### <a name="scored-results"></a>Wyniki z wynikiem

Pierwsze dane wyjściowe modułu zawiera dane źródłowe dla każdego wiersza, wraz z niektórych przewidywanych wartości i powiązanych prawdopodobieństw. 

Aby wyświetlić wyniki, w potoku kliknij prawym przyciskiem myszy moduł Cross Validate Model. Wybierz **pozycję Wizualizuj wyniki ocenione**.

| Nazwa nowej kolumny      | Opis                              |
| -------------------- | ---------------------------------------- |
| Etykiety z wynikami        | Ta kolumna jest dodawana na końcu zestawu danych. Zawiera przewidywaną wartość dla każdego wiersza. |
| Prawdopodobieństwo zdobycia bramki | Ta kolumna jest dodawana na końcu zestawu danych. Wskazuje szacowane prawdopodobieństwo wartości w **etykietach punktowane .** |
| Liczba złóż          | Wskazuje indeks oparty na wartości zerowej krotnie, do którym przypisano każdy wiersz danych podczas sprawdzania poprawności krzyżowej. |

 ### <a name="evaluation-results"></a>Wyniki oceny

Drugi raport jest pogrupowany według fałd. Należy pamiętać, że podczas wykonywania Cross Validate Model losowo dzieli dane szkolenia na *n* folds (domyślnie 10). W każdej iteracji nad zestawem danych Model sprawdzania poprawności krzyżowej używa jednego krotnie jako zestawu danych sprawdzania poprawności. Używa pozostałych fałd *n-1* do treningu modelu. Każdy z *n* modeli jest testowany na danych we wszystkich innych fałdach.

W tym raporcie fałdy są wymienione według wartości indeksu, w porządku rosnącym.  Aby zamówić w dowolnej innej kolumnie, można zapisać wyniki jako zestaw danych.

Aby wyświetlić wyniki, w potoku kliknij prawym przyciskiem myszy moduł Cross Validate Model. Wybierz **opcję Wizualizuj wyniki oceny według krotnie**.


|Nazwa kolumny| Opis|
|----|----|
|Liczba zagięcia| Identyfikator dla każdej krotnie. Jeśli utworzono pięć razy, będzie pięć podzbiorów danych, ponumerowane od 0 do 4.
|Liczba przykładów w składanym|Liczba wierszy przypisanych do każdej krotnie. Powinny być mniej więcej równe. |


Moduł zawiera również następujące metryki dla każdego zagięcia, w zależności od typu ocenianego modelu: 

+ **Modele klasyfikacji**: Precyzja, przywołanie, wynik F, AUC, dokładność  

+ **Modele regresji**: Średni błąd bezwzględny, średni błąd kwadratowy, względny błąd bezwzględny, względny błąd kwadratowy i współczynnik oznaczania


## <a name="technical-notes"></a>Uwagi techniczne  

+ Jest najlepszym rozwiązaniem do normalizacji zestawów danych przed użyciem ich do sprawdzania poprawności krzyżowej. 

+ Model sprawdzania poprawności krzyżowej jest znacznie bardziej intensywny pod względem obliczeniowym i trwa dłużej niż w przypadku zweryfikowanego modelu przy użyciu losowo podzielonego zestawu danych. Powodem jest, że Cross Validate model pociągów i sprawdza poprawność modelu wiele razy.

+ Nie ma potrzeby dzielenia zestawu danych na zestawy szkoleniowe i testowe podczas korzystania z krzyżowego sprawdzania poprawności do pomiaru dokładności modelu. 


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 

