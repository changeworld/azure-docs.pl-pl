---
title: 'Normalizuj dane: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Normalizuj dane w usłudze Azure Machine Learning przekształcić zestaw danych za pomocą *normalizacji*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a740c81aa165e221bca19987c7b3c62da56b0402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477531"
---
# <a name="normalize-data-module"></a>Normalizowanie modułu danych

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do przekształcania zestawu danych poprzez *normalizację*.

Normalizacja jest techniką często stosowaną jako część przygotowania danych do uczenia maszynowego. Celem normalizacji jest zmiana wartości kolumn liczbowych w zestawie danych w celu użycia wspólnej skali, bez zniekształcania różnic w zakresach wartości lub utraty informacji. Normalizacja jest również wymagana dla niektórych algorytmów do prawidłowego modelowania danych.

Załóżmy na przykład, że wejściowy zestaw danych zawiera jedną kolumnę z wartościami od 0 do 1, a inną kolumnę o wartościach od 10 000 do 100 000. Duża różnica w *skali* liczb może powodować problemy podczas próby połączenia wartości jako operacji podczas modelowania.

*Normalizacja* pozwala uniknąć tych problemów, tworząc nowe wartości, które utrzymują ogólny rozkład i współczynniki w danych źródłowych, zachowując wartości w skali stosowane we wszystkich kolumnach liczbowych używanych w modelu.

Ten moduł oferuje kilka opcji przekształcania danych liczbowych:

- Można zmienić wszystkie wartości na skalę 0-1 lub przekształcić wartości, reprezentując je jako rangi percentyla, a nie wartości bezwzględne.
- Normalizację można zastosować do pojedynczej kolumny lub do wielu kolumn w tym samym zestawie danych.
- Jeśli trzeba powtórzyć potoku lub zastosować te same kroki normalizacji do innych danych, można zapisać kroki jako transformacji normalizacji i zastosować go do innych zestawów danych, które mają ten sam schemat.

> [!WARNING]
> Niektóre algorytmy wymagają, aby dane zostały znormalizowane przed szkoleniem modelu. Inne algorytmy wykonują własne skalowanie danych lub normalizacji. W związku z tym po wybraniu algorytmu uczenia maszynowego do użycia w tworzeniu modelu predykcyjnego, należy przejrzeć wymagania dotyczące danych algorytmu przed zastosowaniem normalizacji do danych szkoleniowych.

##  <a name="configure-normalize-data"></a>Konfigurowanie normalizacji danych

Można zastosować tylko jedną metodę normalizacji w czasie przy użyciu tego modułu. W związku z tym ta sama metoda normalizacji jest stosowana do wszystkich kolumn, które można wybrać. Aby użyć różnych metod normalizacji, należy użyć drugiego wystąpienia **normalizuj dane**.

1. Dodaj moduł **Normalizuj dane** do potoku. Moduł w usłudze Azure Machine Learning w obszarze **Transformacja danych**można znaleźć w kategorii **Skalowanie i Zmniejszanie.**

2. Połącz zestaw danych zawierający co najmniej jedną kolumnę wszystkich liczb.

3. Użyj selektora kolumn, aby wybrać kolumny liczbowe do normalizacji. Jeśli nie wybierzesz poszczególnych kolumn, domyślnie **wszystkie** kolumny typów liczbowych w danych wejściowych są uwzględniane, a ten sam proces normalizacji jest stosowany do wszystkich wybranych kolumn. 

    Może to prowadzić do dziwnych wyników, jeśli dołączysz kolumny numeryczne, które nie powinny być znormalizowane! Zawsze dokładnie sprawdzaj kolumny.

    Jeśli nie zostaną wykryte żadne kolumny liczbowe, sprawdź metadane kolumny, aby sprawdzić, czy typ danych kolumny jest obsługiwanym typem liczbowym.

    > [!TIP]
    > Aby upewnić się, że kolumny określonego typu są dostarczane jako dane wejściowe, spróbuj użyć modułu [Wybierz kolumny w zestawie danych](./select-columns-in-dataset.md) przed **normalizacją danych**.

4. **Użyj 0 dla kolumn stałych, gdy zaznaczone**: Wybierz tę opcję, gdy dowolna kolumna liczbowa zawiera pojedynczą niezmienną wartość. Gwarantuje to, że takie kolumny nie są używane w operacjach normalizacji.

5. Z listy rozwijanej **Metoda transformacji** wybierz jedną funkcję matematyczną, która ma być stosowana do wszystkich wybranych kolumn. 
  
    - **Zscore**: Konwertuje wszystkie wartości na wynik z.
    
      Wartości w kolumnie są przekształcane przy użyciu następującej formuły:  
  
      ![normalizacja za pomocą wyników&#45;z](media/module/aml-normalization-z-score.png)
  
      Średnie i standardowe odchylenie są obliczane dla każdej kolumny oddzielnie. Stosuje się odchylenie standardowe populacji.
  
    - **MinMax**: Min-max normalizer liniowo przeskaluje każdą funkcję do interwału [0,1].
    
      Ponowne skalowanie do interwału [0,1] odbywa się przez przesunięcie wartości każdej operacji, tak aby minimalna wartość wynosi 0, a następnie dzielenie przez nową wartość maksymalną (która jest różnicą między wartościami pierwotnymi maksymalnymi i minimalnymi).
      
      Wartości w kolumnie są przekształcane przy użyciu następującej formuły:  
  
      ![normalizacja za pomocą funkcji min&#45;max](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistyka**: Wartości w kolumnie są przekształcane przy użyciu następującej formuły:

      ![formuła normalizacji za pomocą funkcji logistycznej](media/module/aml-normalization-logistic.png "AML_normalization-logistyczny")  
  
    - **LogNormal:** Ta opcja konwertuje wszystkie wartości na skalę logarytmalną.
  
      Wartości w kolumnie są przekształcane przy użyciu następującej formuły:
  
      ![dziennik formuły&#45;rozkład normalny](media/module/aml-normalization-lognormal.png "AML_normalization-logarytmalne")
    
      Tutaj μ i σ są parametrami rozkładu, obliczonymi empirycznie na podstawie danych jako maksymalne prawdopodobieństwo oszacowania, dla każdej kolumny oddzielnie.  
  
    - **TanH:** Wszystkie wartości są konwertowane na styczne hiperboliczne.
    
      Wartości w kolumnie są przekształcane przy użyciu następującej formuły:
    
      ![normalizacji za pomocą funkcji tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Prześlij potok lub kliknij dwukrotnie moduł **Normalizuj dane** i wybierz pozycję **Uruchom zaznaczone**. 

## <a name="results"></a>Wyniki

Moduł **Normalizuj dane** generuje dwa wyjścia:

- Aby wyświetlić przekształcone wartości, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**.

    Domyślnie wartości są przekształcane w miejscu. Jeśli chcesz porównać przekształcone wartości z wartościami oryginalnymi, użyj modułu [Dodaj kolumny,](./add-columns.md) aby ponownie połączyć zestawy danych i wyświetlić kolumny obok siebie.

- Aby zapisać transformację, aby można było zastosować tę samą metodę normalizacji do innego zestawu danych, wybierz moduł i wybierz **pozycję Zarejestruj zestaw danych** na karcie **Wyjścia** w prawym panelu.

    Następnie można załadować zapisane przekształcenia z grupy **Przekształcanie** lewego okienka nawigacji i zastosować je do zestawu danych z tym samym schematem za pomocą [zastosuj transformację](apply-transformation.md).  


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 