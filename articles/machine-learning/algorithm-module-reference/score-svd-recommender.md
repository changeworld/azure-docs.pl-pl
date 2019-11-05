---
title: 'Polecający wskaźnik SVD: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać modułu polecania "Ocena SVD" w usłudze Azure Machine Learning, aby wypróbować przewidywania rekomendacji dla zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 25de69873857512a70d6417973d4a85883ac6455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517919"
---
# <a name="score-svd-recommender"></a>Polecający wskaźnik SVD

W tym artykule opisano sposób korzystania z modułu **polecania "Score SVD** " w programie Azure Machine Learning Designer (wersja zapoznawcza). Ten moduł służy do tworzenia prognoz przy użyciu przeszkolonego modelu rekomendacji na podstawie algorytmu SVD (Single Value dekompozycji).

Zalecenie SVD może generować dwa różne rodzaje prognoz:

- [Prognozowanie klasyfikacji dla danego użytkownika i elementu](#predict-ratings)

- [Zalecane elementy dla danego użytkownika](#recommend)

Podczas tworzenia drugiego typu prognoz można działać w trybie *produkcyjnym* lub w *trybie oceny*.

- **Tryb produkcyjny** traktuje wszystkich użytkowników lub elementy i jest zazwyczaj używany w usłudze sieci Web.

    Możesz tworzyć wyniki dla nowych użytkowników, nie tylko dla użytkowników oglądanych podczas uczenia się. Aby uzyskać więcej informacji, zobacz [tę sekcję](#technical-notes). 

- **Tryb oceny** działa na ograniczonym zestawie użytkowników lub elementów, które mogą być oceniane, i jest zazwyczaj używany podczas potoku.

Aby uzyskać więcej informacji na temat algorytmu z zaleceniem SVD, zobacz dokument [dotyczący badań: Matrix factorization — techniki dla systemów zalecanych](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

                                                                                                                                    


## <a name="how-to-configure-score-svd-recommender"></a>Jak skonfigurować zalecany polecający wskaźnik SVD

Ten moduł obsługuje różne typy zaleceń, z których każdy ma inne wymagania. Kliknij link do typu danych i typ zalecenia, które chcesz utworzyć.

+ [Klasyfikacja predykcyjna](#predict-ratings)
+ [Zalecane elementy](#recommend)

###  <a name="predict-ratings"></a>Klasyfikacja predykcyjna

Podczas przewidywania klasyfikacji model oblicza, w jaki sposób dany użytkownik reaguje na określony element, pod kątem danych szkoleniowych. W związku z tym dane wejściowe dla oceny muszą zapewnić zarówno użytkownika, jak i element do oceny.

1. Dodaj przeszkolony model rekomendacji do potoku, a następnie połącz go z **polecanym rozwiązaniem SVD**.  Należy utworzyć model przy użyciu [zalecenia uczenia SVD](train-SVD-recommender.md).

2. **Rodzaj przewidywania polecania**: wybierz opcję **prognozowanie klasyfikacji**. Nie są wymagane żadne dalsze parametry.

3. Dodaj dane, dla których chcesz dokonać prognoz, i połącz je z **zestawem danych w celu oceny**.

    Aby przewidzieć klasyfikację, wejściowy zestaw danych musi zawierać pary User-Item.

    Zestaw danych może zawierać opcjonalną trzecią kolumnę klasyfikacji dla pary elementu User-Item w pierwszej i drugiej kolumnie, ale trzecia kolumna zostanie zignorowana podczas przewidywania.

4. Uruchamianie potoku.

### <a name="results-for-rating-predictions"></a>Wyniki prognoz klasyfikacji 

Wyjściowy zestaw danych zawiera trzy kolumny zawierające użytkownika, element oraz klasyfikację przewidywaną dla każdego użytkownika i elementu wejściowego.

###  <a name="recommend"></a>Rekomenduj 

Aby zalecać elementy dla użytkowników, należy podać listę użytkowników i elementów jako dane wejściowe. Z tych danych model używa swojej wiedzy na temat istniejących elementów i użytkowników w celu wygenerowania listy elementów z prawdopodobnym odwołaniem do każdego użytkownika. Można dostosować liczbę zwracanych zaleceń i ustawić próg liczby wcześniejszych zaleceń, które są wymagane w celu wygenerowania rekomendacji.

1. Dodaj przeszkolony model rekomendacji do potoku, a następnie połącz go z **polecanym rozwiązaniem SVD**.  Należy utworzyć model przy użyciu [zalecenia uczenia SVD](train-svd-recommender.md).

2. Aby zalecać elementy dla danej listy użytkowników, należy ustawić opcję **przewidywanie typu predykcyjnego** na **element zalecenie**.

3. **Zalecany wybór elementu**: wskaż, czy używasz modułu oceniania w środowisku produkcyjnym, czy do oceny modelu, wybierając jedną z następujących wartości:

    - **Z wszystkich elementów**: Wybierz tę opcję, jeśli konfigurujesz potok do użycia w usłudze sieci Web lub w środowisku produkcyjnym.  Ta opcja włącza **tryb produkcyjny**, a moduł wykonuje zalecenia ze wszystkich elementów widzianych podczas szkolenia.

    - **Z elementów ocenianych (na potrzeby oceny modelu)** : Wybierz tę opcję, jeśli tworzysz lub testujesz model. Ta opcja włącza **Tryb oceny**, a moduł wykonuje zalecenia tylko z tych elementów w wejściowym zestawie danych, który został sklasyfikowany.
    
    - **Z niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)** : Wybierz tę opcję, a moduł wykonuje zalecenia tylko z tych elementów w zestawie danych szkoleniowych, które nie zostały ocenione. 

4. Dodaj zestaw danych, dla którego chcesz dokonać prognoz, a następnie połącz go z **zestawem danych w celu oceny**.

    - Dla **wszystkich elementów**wejściowy zestaw danych powinien składać się z jednej kolumny zawierającej identyfikatory użytkowników, dla których należy wykonać zalecenia.

        Zestaw danych może zawierać dodatkowe dwie kolumny identyfikatorów i ocen elementów, ale te dwie kolumny są ignorowane. 

    - W przypadku **elementów ocenianych (dla oceny modelu)** wejściowy zestaw danych powinien zawierać **pary elementu User-Item**. Pierwsza kolumna powinna zawierać identyfikator **użytkownika** . Druga kolumna powinna zawierać odpowiednie identyfikatory **elementów** .

        Zestaw danych może zawierać trzecią kolumnę klasyfikacji elementów użytkownika, ale ta kolumna jest ignorowana.

    - Dla **elementów niesklasyfikowanych (w celu zaproponowania nowych elementów użytkownikom)** wejściowy zestaw danych powinien składać się z **par elementów użytkownika**. Pierwsza kolumna powinna zawierać identyfikator **użytkownika** . Druga kolumna powinna zawierać odpowiednie identyfikatory **elementów** .

        Zestaw danych może zawierać trzecią kolumnę klasyfikacji elementów użytkownika, ale ta kolumna jest ignorowana.

5. **Maksymalna liczba elementów, które mają być zalecane dla użytkownika**: wpisz liczbę elementów do zwrócenia dla każdego użytkownika. Domyślnie jest zalecane pięć elementów.

6. **Minimalny rozmiar puli rekomendacji na użytkownika**: wpisz wartość wskazującą, ile wcześniejszych rekomendacji są wymagane.  Domyślnie ten parametr ma wartość 2, co oznacza, że element musi być zalecany przez co najmniej dwóch innych użytkowników.

    Tej opcji należy używać tylko wtedy, gdy ocenia się tryb oceny. Opcja jest niedostępna, jeśli wybrano opcję **spośród wszystkich elementów** lub **niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)** .

7.  W przypadku **niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)** Użyj trzeciego portu wejściowego o nazwie **dane szkoleniowe**, aby usunąć elementy, które zostały już ocenione z wyników przewidywania.

    Aby zastosować ten filtr, Połącz oryginalny zestaw danych szkoleniowych z portem wejściowym.

8. Uruchamianie potoku.

### <a name="results-of-item-recommendation"></a>Wyniki zalecenia dotyczącego elementu

Wynikowy zestaw danych zwracany przez **zalecaną** listę zalecanych elementów dla każdego użytkownika.

- Pierwsza kolumna zawiera identyfikatory użytkowników.
- Jest generowanych kilka dodatkowych kolumn, w zależności od wartości ustawionej dla **maksymalnej liczby elementów, które mają być zalecane dla użytkownika**. Każda kolumna zawiera zalecany element (według identyfikatora). Zalecenia są uporządkowane według koligacji elementu użytkownika, z elementem o najwyższej koligacji umieszczonym w kolumnie, **element 1**.

> [!WARNING]
> Ten wynikowy zestaw danych nie może być oceniany przy użyciu modułu [oceny polecania](evaluate-recommender.md) .


##  <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera odpowiedzi na niektóre często zadawane pytania dotyczące korzystania z programu polecania do tworzenia prognoz.  

###  <a name="production-use-of-the-svd-recommender"></a>Wykorzystanie w środowisku produkcyjnym SVD

Jeśli masz potok z zaleceniem SVD i przenosisz model do środowiska produkcyjnego, weź pod uwagę te kluczowe różnice w przypadku korzystania z programu polecania w trybie oceny i w trybie produkcyjnym:

- Ocena zgodnie z definicją wymaga przewidywania, które mogą być zweryfikowane względem *prawdy* w zestawie testów. W związku z tym podczas oceny zalecenia musi on przewidzieć tylko elementy, które zostały sklasyfikowane w zestawie testów. Taka konieczność ogranicza możliwe wartości, które są przewidywane.

    Jednak podczas operacjonalizować modelu zwykle zmienia się tryb prognozowania, aby zalecenia były wykonywane na podstawie wszystkich możliwych elementów, aby uzyskać najlepsze przewidywania. W przypadku wielu z tych prognoz nie ma odpowiedniej rzeczywistości, dlatego nie można zweryfikować jego dokładności w taki sam sposób jak w przypadku potoku.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
