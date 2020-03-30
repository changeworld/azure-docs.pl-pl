---
title: 'Wynik SVD Recommender: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Score SVD Recommender w usłudze Azure Machine Learning, aby uzyskać ocenę prognoz rekomendacji dla zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/10/2020
ms.openlocfilehash: 82c3454ad4c8db3a9b19084f5b6ece988cc86b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455982"
---
# <a name="score-svd-recommender"></a>Wynik modułu poleceń SVD

W tym artykule opisano, jak używać modułu Score SVD Recommender w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Ten moduł służy do tworzenia prognoz przy użyciu modelu rekomendacji wyszkolonych na podstawie algorytmu rozkładu pojedynczej wartości (SVD).

Rekomendator SVD może generować dwa różne rodzaje prognoz:

- [Przewidywanie ocen dla danego użytkownika i elementu](#prediction-of-ratings)
- [Polecaj użytkownikom elementy](#recommendations-for-users)

Podczas tworzenia drugiego typu prognoz, można działać w jednym z następujących trybów:

- **Tryb produkcji** uwzględnia wszystkich użytkowników lub towary. Jest to zwykle używane w usłudze sieci web.

  Możesz tworzyć wyniki dla nowych użytkowników, a nie tylko dla użytkowników widzianych podczas szkolenia. Aby uzyskać więcej informacji, zobacz [uwagi techniczne](#technical-notes). 

- **Tryb oceny** działa na zredukowanym zestawie użytkowników lub elementów, które mogą być oceniane. Jest on zazwyczaj używany podczas operacji potoku.

Aby uzyskać więcej informacji na temat algorytmu polecania SVD, zobacz papier badawczy [Matrix technik faktoryzacji dla systemów rekomendatora](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Jak skonfigurować score SVD Recommender

Ten moduł obsługuje dwa typy prognoz, każdy z różnymi wymaganiami. 

###  <a name="prediction-of-ratings"></a>Przewidywanie ocen

Podczas przewidywania klasyfikacji model oblicza, jak użytkownik będzie reagować na określony element, biorąc pod uwagę dane szkoleniowe. Dane wejściowe do oceniania musi zapewnić zarówno użytkownika i elementu do stawki.

1. Dodaj do potoku wyszkolony model rekomendacji i podłącz go do **zalecanego przez przeszkolony SVD**. Model należy utworzyć przy użyciu modułu [Train SVD Recommender.](train-SVD-recommender.md)

2. Dla **rodzaju przewidywania recommender**, wybierz **Przewidywanie klasyfikacji**. Nie są wymagane żadne inne parametry.

3. Dodaj dane, dla których chcesz dokonać prognoz, i połącz je z **zestawem danych, aby uzyskać punkt.**

   Aby model przewidywał oceny, wejściowy zestaw danych musi zawierać pary elementu użytkownika.

   Zestaw danych może zawierać opcjonalną trzecią kolumnę ocen dla pary elementu użytkownika w pierwszej i drugiej kolumnie. Ale trzecia kolumna zostanie zignorowana podczas przewidywania.

4. Prześlij potok.

### <a name="results-for-rating-predictions"></a>Wyniki prognoz ratingowych 

Wyjściowy zestaw danych zawiera trzy kolumny: użytkowników, elementy i przewidywaną klasyfikację dla każdego użytkownika wejściowego i elementu.

###  <a name="recommendations-for-users"></a>Zalecenia dla użytkowników 

Aby polecić elementy dla użytkowników, należy podać listę użytkowników i elementów jako dane wejściowe. Na podstawie tych danych model wykorzystuje swoją wiedzę na temat istniejących elementów i użytkowników do generowania listy elementów z prawdopodobnym odwołania do każdego użytkownika. Można dostosować liczbę zwróconych zaleceń. Można też ustawić próg dla liczby poprzednich zaleceń, które są wymagane do wygenerowania rekomendacji.

1. Dodaj do potoku wyszkolony model rekomendacji i podłącz go do **zalecanego przez przeszkolony SVD**.  Model należy utworzyć przy użyciu modułu [Train SVD Recommender.](train-svd-recommender.md)

2. Aby polecić elementy dla listy użytkowników, ustaw **rodzaj przewidywania rekomendatora** na **Zalecenie elementu**.

3. W przypadku **wyboru zalecanego towaru**należy wskazać, czy używasz modułu oceniania w produkcji, czy do oceny modelu. Wybierz jedną z następujących wartości:

    - **Z pozycji Wszystkie elementy:** Wybierz tę opcję, jeśli konfigurujesz potok do użycia w usłudze sieci web lub w środowisku produkcyjnym.  Ta opcja umożliwia *tryb produkcji*. Moduł zawiera zalecenia ze wszystkich elementów widocznych podczas szkolenia.

    - **Z elementów ocenionych (do oceny modelu)**: Wybierz tę opcję, jeśli tworzysz lub testujesz model. Ta opcja umożliwia *tryb oceny*. Moduł zawiera zalecenia tylko z tych elementów w zestawie danych wejściowych, które zostały ocenione.
    
    - **Z elementów bez oceny (aby zaproponować użytkownikom nowe elementy):** Wybierz tę opcję, jeśli chcesz, aby moduł wydawał rekomendacje tylko z tych elementów w zestawie danych szkoleniowych, które nie zostały ocenione. 

4. Dodaj zestaw danych, dla którego chcesz dokonać prognoz, i połącz go z **zestawem danych, aby uzyskać punkt.**

    - W polu **Od wszystkich elementów**wejściowy zestaw danych powinien składać się z jednej kolumny. Zawiera identyfikatory użytkowników, dla których należy sformułować zalecenia.

      Zestaw danych może zawierać dodatkowe dwie kolumny identyfikatorów i ocen elementów, ale te dwie kolumny są ignorowane. 

    - W przypadku **elementów ocenionych (do oceny modelu)** wejściowy zestaw danych powinien składać się z par elementu użytkownika. Pierwsza kolumna powinna zawierać identyfikator użytkownika. Druga kolumna powinna zawierać odpowiednie identyfikatory elementów.

      Zestaw danych może zawierać trzecią kolumnę ocen elementów użytkownika, ale ta kolumna jest ignorowana.

    - W przypadku **elementów nieoznaczonych (aby zaproponować użytkownikom nowe elementy),** wejściowy zestaw danych powinien składać się z par elementu użytkownika. Pierwsza kolumna powinna zawierać identyfikator użytkownika. Druga kolumna powinna zawierać odpowiednie identyfikatory elementów.

     Zestaw danych może zawierać trzecią kolumnę ocen elementów użytkownika, ale ta kolumna jest ignorowana.

5. **Maksymalna liczba elementów zalecanych użytkownikowi:** Wprowadź liczbę elementów do zwrócenia dla każdego użytkownika. Domyślnie moduł zaleca pięć elementów.

6. **Minimalny rozmiar puli rekomendacji na użytkownika:** Wprowadź wartość, która wskazuje, ile wcześniejszych zaleceń jest wymaganych. Domyślnie ten parametr jest ustawiony na 2, co oznacza, że co najmniej dwóch innych użytkowników poleciło element.

   Tej opcji należy używać tylko w przypadku oceniania w trybie oceny. Opcja ta nie jest dostępna, jeśli wybierzesz **opcję Spośród wszystkich elementów** lub **Z elementów nieocenionych (aby zaproponować użytkownikom nowe elementy).**

7.  W przypadku **elementów nieoznaczonych (aby zaproponować użytkownikom nowe elementy),** użyj trzeciego portu wejściowego o nazwie **Dane szkoleniowe,** aby usunąć elementy, które zostały już ocenione z wyników prognozowania.

    Aby zastosować ten filtr, podłącz oryginalny zestaw danych szkoleniowych do portu wejściowego.

8. Prześlij potok.

### <a name="results-of-item-recommendation"></a>Wyniki rekomendacji pozycji

Oceniony zestaw danych zwrócony przez Score SVD Recommender zawiera listę zalecanych elementów dla każdego użytkownika:

- Pierwsza kolumna zawiera identyfikatory użytkownika.
- Generowane są dodatkowe kolumny, w zależności od wartości ustawionej dla **maksymalnej liczby elementów zalecanych użytkownikowi.** Każda kolumna zawiera zalecany element (według identyfikatora). Zalecenia są uporządkowane według koligacji elementu użytkownika. Element o najwyższym koligacji jest umieszczany w kolumnie **Pozycja 1**.

> [!WARNING]
> Nie można ocenić tego zestawu danych według oceny przy użyciu [oceniać relecatora](evaluate-recommender.md) modułu.


##  <a name="technical-notes"></a>Uwagi techniczne

Jeśli masz potok z polecacza SVD i przenieść model do produkcji, należy pamiętać, że istnieją kluczowe różnice między przy użyciu rekomendatora w trybie oceny i przy użyciu go w trybie produkcyjnym.

Ocena z definicji wymaga prognoz, które można zweryfikować pod względem *prawdy w* zestawie testów. Podczas oceny polecający, należy przewidzieć tylko elementy, które zostały ocenione w zestawie testów. Ogranicza to możliwe wartości, które są przewidywane.

Podczas operacjonalizacji modelu zazwyczaj można zmienić tryb przewidywania, aby zalecenia oparte na wszystkich możliwych elementów, aby uzyskać najlepsze prognozy. Dla wielu z tych prognoz, nie ma odpowiedniej prawdy gruntu. Tak więc dokładność zalecenia nie można zweryfikować w taki sam sposób, jak podczas operacji potoku.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
