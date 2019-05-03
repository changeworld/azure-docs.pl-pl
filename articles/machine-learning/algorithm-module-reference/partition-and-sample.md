---
title: 'Partycja i próbka: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu partycja i próbka w usłudze Azure Machine Learning, przeprowadzić próbkowania w zestawie lub do tworzenia partycji z zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 72f9e11e3582d804eecc7479ea079276564bd12f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029283"
---
# <a name="partition-and-sample-module"></a>Partycja i próbka modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Używaj tego modułu do wykonania próbkowania w zestawie lub do tworzenia partycji z zestawu danych.

Próbkowanie jest ważnym narzędziem w usługi machine learning, ponieważ dzięki temu można zmniejszyć rozmiar zestawu danych przy zachowaniu tej samej stosunek wartości. Ten moduł obsługuje kilka powiązanych zadań, które są ważne w usłudze machine learning: 

- Dzielenie danych na wiele podsekcje w tej samej wielkości. 

    Można na przykład partycje dla krzyżowego sprawdzania poprawności lub przypisać przypadków do grup losowych.

- Oddzielanie danych w grupach, a następnie pracę z danymi do określonej grupy. 

    Po losowo przypisywania przypadków do różnych grup, może być konieczne zmodyfikowanie funkcje, które są skojarzone z tylko jedną grupę.

- Pobieranie próbek. 

    Można wyodrębnić procent danych, zastosuj losowego próbkowanie lub wybierz kolumnę do użycia dla równoważenia zestawu danych, a następnie wykonać stratyfikowana próbkowania na jego wartości.

- Tworzenie mniejszych zestawu danych testowych. 

    Jeśli masz dużą ilość danych, możesz chcieć użyć tylko pierwszy *n* wierszy podczas konfigurowania eksperymentu, a następnie przełączasz się przy użyciu pełnego zestawu danych podczas tworzenia modelu. Próbkowanie umożliwia również tworzenie s mniejszy zestaw danych do użycia w trakcie opracowywania.

## <a name="configure-partition-and-sample"></a>Konfigurowanie partycja i próbka

Ten moduł obsługuje wiele metod, dzieląc dane na partycje lub dla pobierania próbek. Najpierw wybierz metodę, a następnie dodatkowe opcje set wymagane przez metodę.

- Główny
- Próbkowanie
- Przypisz do złożeń
- Wybierz zwijania

### <a name="get-top-n-rows-from-a-dataset"></a>Uzyskać N PIERWSZYCH wierszy z zestawu danych

Użyj tego trybu, można pobrać tylko pierwszy *n* wierszy. Ta opcja jest przydatna, jeśli chcesz przetestować eksperymentów na niewielkiej liczby wierszy które nie potrzebują danych ze zrównoważonym lub próbkowane w dowolny sposób.

1. Dodaj **partycja i próbka** modułu do eksperymentu w interfejsie i Połącz z zestawu danych.  

2. **Tryb partycji lub zablokowanego przykładowe**: Ustaw tę opcję na **Head**.

3. **Liczba wierszy, aby wybrać**: Wpisz liczbę wierszy do zwrócenia.

    Liczba wierszy, które określisz musi być nieujemną liczbą całkowitą. Jeśli liczba wybranych wierszy jest większy niż liczba wierszy w zestawie danych, zwracany jest cały zestaw danych.

4. Uruchom eksperyment.

Moduł danych wyjściowych jednego zestawu danych, zawierającą określoną liczbę wierszy. Wiersze zawsze są odczytywane z początku zestawu danych.

### <a name="create-a-sample-of-data"></a>Utwórz przykładowe dane

Ta opcja obsługuje pobieranie próbek losowych lub uporządkować losowego pobierania próbek. Jest to przydatne, jeśli chcesz utworzyć mniejszy zestaw reprezentatywnej próbki danych do testowania.

1. Dodaj **partycja i próbka** modułu do eksperymentu i Połącz z zestawu danych.

2. **Tryb partycji lub zablokowanego przykładowe**: Ustaw tę opcję na **próbkowania**.

3. **Częstotliwość próbkowania**: Wpisz wartość z zakresu od 0 do 1. Ta wartość Określa procent wierszy z zestawu danych źródłowych, które powinny zostać uwzględnione w wyjściowy zestaw danych.

    Na przykład, jeśli chcesz tylko połowę oryginalnego zestawu danych, wpisz `0.5` do wskazania, że częstotliwość próbkowania powinien wynosić 50%.

    Wiersze wejściowego zestawu danych są przesuwane i selektywnie wprowadzane do wyjściowego zestawu danych, zgodnie z określonym współczynnik.

4. **Inicjator losowy dla pobierania próbek**: Opcjonalnie możesz wpisać całkowitą jako wartość początkową.

    Ta opcja jest ważne, jeśli chcesz, aby wierszy, które mają być podzielona taki sam sposób za każdym razem, gdy. Wartość domyślna to 0, znaczenia, począwszy od inicjatora jest generowany na podstawie zegara systemowego. Może to prowadzić do nieco różne wyniki w każdym razem, gdy uruchomienie eksperymentu.

5. **Podziel stratyfikowana dla pobierania próbek**: Wybierz tę opcję, jeśli jest to ważne, że wiersze w zestawie danych należy podzielić równomiernie przez niektóre kolumny klucza przed pobraniem próbek.

    Dla **stratyfikacji kolumny klucza dla pobierania próbek**, wybierz jeden *kolumny strata* używana podczas dzielenia zestawu danych. Wiersze w zestawie danych są podzielone następująco:

    1. Wszystkie wiersze danych wejściowych są pogrupowane według wartości w kolumnie określonej strata (stratyfikowana).

    2. Wiersze są przesuwane w każdej grupie.

    3. Każda grupa selektywnie jest dodawany do wyjściowego zestawu danych w celu spełnienia określony współczynnik.


6. Uruchom eksperyment.

    Po wybraniu tej opcji moduł danych wyjściowych jednego zestawu danych zawierającego reprezentatywnej próbki danych. Pozostała, niepróbkowanych część zestawu danych nie znajdują się dane wyjściowe. 

## <a name="split-data-into-partitions"></a>Podział danych na partycje

Użyj tej opcji, jeśli chcesz podzielić zestawu danych na podzbiory danych. Ta opcja jest również przydatne, gdy chcesz utworzyć niestandardowe liczbę złożeń dla krzyżowego sprawdzania poprawności lub podziału wierszy na kilka grup.

1. Dodaj **partycja i próbka** modułu do eksperymentu i Połącz z zestawu danych.

2. Aby uzyskać **partycji lub tryb próbki**, wybierz opcję **przypisać do złożeń**.

3. **Użyj zastąpienia, w przypadku użycia partycjonowania**: Wybierz tę opcję, jeśli ma zostać ponownie umieszczona w puli wierszy do ponownego wykorzystania potencjalnych próbkowanych wiersz. W rezultacie tym samym wierszu, mogą zostać przypisani do kilku złożeń.

    Jeśli nie używasz zastąpienia (opcja domyślna), próbkowanych wiersz nie jest ponownie umieszczana w puli wierszy do ponownego wykorzystania potencjalnych. W rezultacie każdy wiersz można przypisać do zwijania tylko jeden.

4. **Podziel losowego**:  Wybierz tę opcję, jeśli chcesz, aby wierszy, które mają być losowo przydzielani do złożeń.

    Jeśli nie zaznaczysz tej opcji, wiersze są przypisywane do złożeń przy użyciu metody działanie okrężne.

5. **Inicjator losowy**: Opcjonalnie możesz wpisać całkowitą jako wartość początkową. Ta opcja jest ważne, jeśli chcesz, aby wierszy, które mają być podzielona taki sam sposób za każdym razem, gdy. W przeciwnym razie wartość domyślna 0 oznacza, że losową uruchamianie inicjatora będą używane.

6. **Określ metodę partycjonera**: Wskazują, jak mają być przydzielona do każdej partycji danych za pomocą tych opcji:

    - **Równomiernie podzielić**: Użyj tej opcji można umieścić równą liczbę wierszy w poszczególnych partycjach. Aby określić liczbę partycji danych wyjściowych, wpisz liczbę całkowitą w **Określ liczbę złożeń równomiernie podzielić na** pola tekstowego.

    - **Partycja o proporcjach dostosowane**: Użyj tej opcji, aby określić rozmiar poszczególnych partycji jako listę rozdzielonych przecinkami.

        Na przykład, jeśli chcesz utworzyć trzy partycje, przy użyciu pierwszej partycji zawierających 50% rozmiaru danych, a pozostałe dwie partycje każdego zawierającego 25% danych, kliknij **listę proporcje rozdzielonych przecinkami** pole tekstowe i Wpisz numery te: `.5, .25, .25`

        Suma wszystkich rozmiarów partycji należy dodać maksymalnie dokładnie 1.

        - Po wprowadzeniu numerów Dodawanie **mniej niż 1**, dodatkowe partycji jest utworzona w celu przechowywania pozostałych wierszy. Na przykład, jeśli wpiszesz wartości od.2 i.3, trzeci zostanie utworzona partycja zawierającą pozostałe 50 procent wszystkich wierszy.

        - Po wprowadzeniu numerów Dodawanie **więcej niż 1**, występuje błąd podczas uruchamiania eksperymentu.

7. **Podziel stratyfikowana**: Wybierz tę opcję, opcja chcącym wierszy, które mają być uporządkować podczas podziału, a następnie wybierz _kolumny strata_.

8. Uruchom eksperyment.

    Po wybraniu tej opcji moduł generuje wiele zestawów danych na partycje przy użyciu reguł, wskazana.

### <a name="use-data-from-a-predefined-partition"></a>Użyj danych z wstępnie zdefiniowanych partycji  

Ta opcja jest używana, gdy zostały podzielone zestawu danych na wiele partycji, a teraz chcą z kolei obciążenia każdej partycji w celu dalszej analizy lub przetwarzania.

1. Dodaj **partycja i próbka** modułu do eksperymentu.

2. Połącz go z danymi wyjściowymi poprzednie wystąpienie **partycja i próbka**. To wystąpienie ma zastosowanie tylko **przypisać do złożeń** opcję, aby wygenerować pewnej liczby partycji.

3. **Tryb partycji lub zablokowanego przykładowe**: Wybierz **wybierz zwijania**.

4. **Określ, który składa się pierwotnych z**: Wybierz partycję do użycia, wpisując jego indeksu. Indeksy partycji są oparte na 1. Na przykład jeśli zestaw danych jest podzielony na trzy części, partycji musi indeksów, 1, 2 i 3.

    Jeśli typ jest wartością nieprawidłowy indeks, zgłaszany jest błąd w czasie projektowania: "Błąd 0018: Zestaw danych zawiera nieprawidłowe dane."

    Oprócz zestawu danych są grupowane według złożeń, można oddzielić zestawu danych w dwóch grupach: zwijania docelowego, a wszystko inne. Aby to zrobić, wpisz indeks jednej zwijania, a następnie wybierz opcję **wybierz uzupełnienie wybranych zwijania**, aby uzyskać wszystko, ale dane w określonym zwijania.

5. Jeśli pracujesz z wielu partycji, należy dodać dodatkowe wystąpienia **partycja i próbka** modułu do obsługi poszczególnych partycji.

    Na przykład załóżmy, że wcześniej partycjonowane pacjentów do czterech złożeń przy użyciu wieku. Aby pracować z każdego z poszczególnych etapów, potrzebujesz cztery kopie **partycja i próbka** moduł, i w każdym, wybrania różnych etapów, jak pokazano poniżej. Jest ona nieprawidłowa do użycia **przypisać do złożeń** danych wyjściowych bezpośrednio.  

    [![Partycja i próbka](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Uruchom eksperyment.

    Po wybraniu tej opcji moduł danych wyjściowych jednego zestawu danych zawierającego tylko wiersze, które zostały przypisane do tego zwijania.

> [!NOTE]
>  Nie można wyświetlić nazwy etapów bezpośrednio; są one dostępne tylko w metadanych.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 