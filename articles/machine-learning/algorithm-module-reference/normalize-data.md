---
title: 'Normalizacji danych: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu normalizacji danych w usłudze Azure Machine Learning do przekształcania zestawu danych za pośrednictwem *normalizacji*...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029448"
---
# <a name="normalize-data-module"></a>Normalizuj modułu danych

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do przekształcania zestawu danych za pośrednictwem *normalizacji*.

Normalizacja jest techniką, często stosowane w ramach przygotowania danych dla usługi machine learning. Celem normalizacji jest w celu zmiany wartości kolumny liczbowe w zestawie danych, aby użyć wspólnej skali bez wypaczającym różnice w zakresach wartości lub utraty informacji. Normalizacja jest również wymagana dla niektórych algorytmów do modelu danych poprawnie.

Załóżmy na przykład, że wejściowy zestaw danych zawiera jedną kolumnę z wartościami z zakresu od 0 do 1 i innej kolumny z wartościami z zakresu od 10 000 do 100 000. Wspaniałej różnica w *skalowania* liczb może powodować problemy podczas próby połączyć ze sobą wartości jako funkcje w procesie modelowania.

*Normalizacja* pozwala uniknąć tych problemów, tworząc nowe wartości obsługujące GDR i współczynników w danych źródłowych, przy jednoczesnym zachowaniu wartości w skali, stosowane we wszystkich kolumnach liczbowych użyte w modelu.

Ten moduł oferuje kilka opcji do przekształcania danych liczbowych:

- Można zmienić wszystkie wartości na 0 – 1 skalowania lub przekształcania wartości wyrażając je jako percentyl szereguje zamiast wartości bezwzględne.
- Normalizacja można zastosować do jednej kolumny lub do wielu kolumn w tym samym zestawie danych.
- Jeśli potrzebujesz – powtarzanie eksperymentu, i Zastosuj te same kroki normalizacji z innymi danymi, można zapisać kroki jako przekształcenia normalizacji i zastosować je do innych zestawów danych, które mają ten sam schemat.

> [!WARNING]
> Niektóre algorytmy wymagają, że dane będą normalizowane przed uczenia modelu. Inne algorytmy wykonać swoje własne dane, skalowanie lub normalizacji. W związku z tym w przypadku wybrania algorytmu do użycia w tworzeniu predykcyjnych modeli uczenia maszynowego, należy przejrzeć wymagania dotyczące danych algorytmu przed zastosowaniem normalizacji danych szkoleniowych.

##  <a name="configure-normalize-data"></a>Konfigurowanie normalizacji danych

Za pomocą tego modułu, można zastosować tylko jedną metodę normalizacji. W związku z tym ten sam metoda normalizacji jest stosowana do wszystkich kolumn, które można wybierać. Aby użyć normalizacji różnych metod, użyj drugiego wystąpienia **normalizacji danych**.

1. Dodaj **normalizacji danych** modułu do eksperymentu. Moduł w usłudze Azure Machine Learning można znaleźć w obszarze **przekształcania danych**w **skali i Zmniejsz** kategorii.

2. Połącz z zestawu danych, który zawiera co najmniej jedną kolumnę wszystkich liczb.

3. Użyj selektora kolumn, aby wybrać kolumny liczbowe do normalizacji. Jeśli nie wybierzesz poszczególnych kolumn, domyślnie **wszystkich** kolumny typ liczbowy w danych wejściowych są uwzględnione i ten sam proces normalizacji jest stosowany do wszystkich zaznaczonych kolumn. 

    Może to prowadzić do wyników dziwne, Jeśli dołączysz kolumny liczbowe, które nie powinny być znormalizowane! Zawsze dokładnie sprawdzić kolumn.

    Jeśli zostaną wykryte nie kolumny liczbowe, sprawdź metadanych kolumn, aby sprawdzić, czy typ danych kolumny jest obsługiwanego typu liczbowego.

    > [!TIP]
    > Aby upewnić się, że kolumny o typie określonym są udostępniane jako dane wejściowe, spróbuj użyć [Select Columns in Dataset](./select-columns-in-dataset.md) moduł przed **normalizacji danych**.

4. **Użycie wartości 0 dla stałej kolumn po zaznaczeniu tej opcji**:  Wybierz tę opcję, jeśli dowolnej kolumny liczbowej zawiera pojedynczą wartość niezmiennych. Daje to gwarancję, że takich kolumn nie są używane w operacjach normalizacji.

5. Z **metoda przekształcania** listy rozwijanej wybierz pojedynczej funkcji matematycznych, aby zastosować do wszystkich zaznaczonych kolumn. 
  
    - **Zscore**: Konwertuje wszystkie wartości z wynikiem.
    
      Wartości w kolumnie są przekształcane, korzystając z następującego wzoru:  
  
      ![Korzystanie z normalizacji&#45;wyniki](media/module/aml-normalization-z-score.png)
  
      Średnie i standardowe odchylenie są obliczane osobno dla każdej kolumny. Odchylenie standardowe populacji jest używany.
  
    - **MinMax**: Normalizer minimum maksimum ponowne liniowe skalowanie każdej funkcji interwale [0,1].
    
      Ponowne skalowanie do interwale [0,1] odbywa się przez przesunięcie wartości każdej funkcji, tak aby minimalnej wartości to 0, a następnie podzielenie przez nową wartość maksymalna (co różni się od oryginalnej wartości maksymalny i minimalny).
      
      Wartości w kolumnie są przekształcane, korzystając z następującego wzoru:  
  
      ![za pomocą minimalną wartość normalizacji&#45;maksymalnej liczby funkcji](media/module/aml-normalization-minmax.png "AML_normalization minmax")  
  
    - **Logistycznej**: Wartości w kolumnie są przekształcane, korzystając z następującego wzoru:

      ![Formuła dla normalizacji przez funkcję logistycznej](media/module/aml-normalization-logistic.png "logistycznej AML_normalization")  
  
    - **Logarytmiczno-normalny**: Ta opcja przeprowadza konwersję wszystkich wartości logarytmu naturalnego skalowania.
  
      Wartości w kolumnie są przekształcane, korzystając z następującego wzoru:
  
      ![formula log&#45;normal distribution](media/module/aml-normalization-lognormal.png "AML_normalization-lognormal")
    
      W tym miejscu μ i σ są parametry dystrybucji obliczona empirically z danych jako maksymalną prawdopodobieństwo prognozy, dla każdej kolumny oddzielnie.  
  
    - **TanH**: Wszystkie wartości są konwertowane na tangens hiperboliczny.
    
      Wartości w kolumnie są przekształcane, korzystając z następującego wzoru:
    
      ![Normalizacja przy użyciu funkcji tanh](media/module/aml-normalization-tanh.png "AML_normalization tanh")

6. Uruchom eksperyment, lub kliknij dwukrotnie **normalizacji danych** modułu, a następnie wybierz pozycję **Uruchom wybrane**. 

## <a name="results"></a>Wyniki

**Normalizacji danych** moduł generuje dwa dane wyjściowe:

- Aby wyświetlić wartości przekształcone, kliknij prawym przyciskiem myszy moduł, wybierz **przekształcony zestaw danych**i kliknij przycisk **Visualize**.

    Domyślnie wartości są przekształcane w miejscu. Jeśli chcesz porównać przekształcone wartości do oryginalnych wartości, użyj [Dodaj kolumny](./add-columns.md) modułu, aby ponownie połączyć zestawy danych i wyświetlić kolumny obok siebie.

- Aby zapisać przekształcenie tej samej metody normalizacji można zastosować do innego podobnego zestawu danych, kliknij prawym przyciskiem myszy moduł, wybierz **funkcję przekształcenia**i kliknij przycisk **Zapisz jako przekształcenia**.

    Następnie możesz załadować zapisanych przekształcenia z **przekształca** grupy w okienku nawigacji po lewej stronie i zastosować je do zestawu danych za pomocą tego samego schematu za pomocą [. / zastosować przekształcenie](apply-transformation.md).  


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 