---
title: 'Partycja i próbka: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu partycji i przykładowego w usłudze Azure Machine Learning do wykonywania próbkowania w zestawie danych lub tworzenia partycji z zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477514"
---
# <a name="partition-and-sample-module"></a>Moduł partycji i przykładu

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Użyj partycji i przykładu modułu do wykonywania próbkowania na zestawie danych lub do tworzenia partycji z zestawu danych.

Próbkowanie jest ważnym narzędziem w uczeniu maszynowym, ponieważ pozwala zmniejszyć rozmiar zestawu danych przy zachowaniu tego samego stosunku wartości. Ten moduł obsługuje kilka powiązanych zadań, które są ważne w uczeniu maszynowym: 

- Dzielenie danych na wiele podsekcji o tym samym rozmiarze. 

  Partycje można używać do sprawdzania poprawności krzyżowej lub przypisywania spraw do losowych grup.

- Oddzielanie danych na grupy, a następnie praca z danymi z określonej grupy. 

  Po losowym przypisaniu spraw do różnych grup może być konieczne zmodyfikowanie obiektów skojarzonych tylko z jedną grupą.

- Pobierania próbek. 

  Można wyodrębnić procent danych, zastosować losowe próbkowanie lub wybrać kolumnę do równoważenia zestawu danych i wykonać próbkowanie stratified na jego wartości.

- Tworzenie mniejszego zestawu danych do testowania. 

  Jeśli masz dużo danych, można użyć tylko pierwsze *wiersze n* podczas konfigurowania potoku, a następnie przełączyć się do korzystania z pełnego zestawu danych podczas tworzenia modelu. Można również użyć próbkowania do utworzenia mniejszego zestawu danych do użycia w programowaniu.

## <a name="configure-the-module"></a>Konfigurowanie modułu

Ten moduł obsługuje następujące metody dzielenia danych na partycje lub do próbkowania. Najpierw wybierz metodę, a następnie ustaw dodatkowe opcje, których wymaga metoda.

- Head
- Próbkowanie
- Przypisywanie do zagęszków
- Wybrań zgieł

### <a name="get-top-n-rows-from-a-dataset"></a>Pobierz wiersze TOP N z zestawu danych

Użyj tego trybu, aby uzyskać tylko pierwsze *n* wierszy. Ta opcja jest przydatna, jeśli chcesz przetestować potoku na małej liczbie wierszy i nie trzeba danych, które mają być zrównoważone lub próbkowane w jakikolwiek sposób.

1. Dodaj **partycję i przykładowy** moduł do potoku w interfejsie i połącz zestaw danych.  

1. **Tryb partycji lub próbki:** Ustaw tę opcję na **Head**.

1. **Liczba wierszy do wybrania**: Wprowadź liczbę wierszy do zwrócenia.

   Liczba wierszy musi być nieujemną liczbą całkowitą. Jeśli liczba wybranych wierszy jest większa niż liczba wierszy w zestawie danych, zwracany jest cały zestaw danych.

1. Prześlij potok.

Moduł wyprowadza pojedynczy zestaw danych, który zawiera tylko określoną liczbę wierszy. Wiersze są zawsze odczytywane od góry zestawu danych.

### <a name="create-a-sample-of-data"></a>Tworzenie próbki danych

Ta opcja obsługuje proste losowe pobieranie próbek lub stratyfikowane losowe pobieranie próbek. Jest to przydatne, jeśli chcesz utworzyć mniejszy reprezentatywny zestaw danych próbki do testowania.

1. Dodaj **partycję i przykładowy** moduł do potoku i połącz zestaw danych.

1. **Tryb partycji lub próbki:** Ustaw tę opcję na **Próbkowanie**.

1. **Częstotliwość pobierania próbek**: Wprowadź wartość z 0 do 1. ta wartość określa procent wierszy z źródłowego zestawu danych, które powinny być uwzględnione w wyjściowym zestawie danych.

   Na przykład jeśli chcesz tylko połowę oryginalnego `0.5` zestawu danych, wprowadź, aby wskazać, że częstotliwość próbkowania powinna wynosić 50 procent.

   Wiersze wejściowego zestawu danych są tasowane i wybiórczo umieszczane w wyjściowym zestawie danych, zgodnie z określonym współczynnikiem.

1. **Losowy materiał siewny**do pobierania próbek : Opcjonalnie wprowadź całkowitą wartość do użycia jako wartość materiału siewnego.

   Ta opcja jest ważna, jeśli wiersze mają być dzielone w ten sam sposób za każdym razem. Wartość domyślna to **0**, co oznacza, że początkowy materiał siewny jest generowany na podstawie zegara systemowego. Ta wartość może prowadzić do nieco innych wyników przy każdym uruchomieniu potoku.

1. **Podział warstwowy do pobierania próbek:** Wybierz tę opcję, jeśli ważne jest, aby wiersze w zestawie danych były podzielone równomiernie przez niektóre kolumny klucza przed próbkowaniem.

   W polu **Kolumna klucza Stratification do pobierania próbek**wybierz pojedynczą *kolumnę warstwy,* która będzie używana podczas dzielenia zestawu danych. Wiersze w zestawie danych są następnie podzielone w następujący sposób:

   1. Wszystkie wiersze wejściowe są zgrupowane (stratyfikowane) według wartości w określonej kolumnie strata.

   1. Wiersze są tasowane w każdej grupie.

   1. Każda grupa jest selektywnie dodawana do wyjściowego zestawu danych, aby spełnić określony współczynnik.


1. Prześlij potok.

   Za pomocą tej opcji moduł wyprowadza pojedynczy zestaw danych, który zawiera reprezentatywne próbkowanie danych. Pozostała, niespróbkowana część zestawu danych nie jest wyprowadzana. 

## <a name="split-data-into-partitions"></a>Dzielenie danych na partycje

Użyj tej opcji, jeśli chcesz podzielić zestaw danych na podzbiory danych. Ta opcja jest również przydatna, gdy chcesz utworzyć niestandardową liczbę zagęszków do sprawdzania poprawności krzyżowej lub podzielić wiersze na kilka grup.

1. Dodaj **partycję i przykładowy** moduł do potoku i połącz zestaw danych.

1. W przypadku **trybu partycji lub przykładu**wybierz pozycję **Przypisz do zagięcia**.

1. **Użyj zastępowania w partycjonowaniu:** Wybierz tę opcję, jeśli chcesz, aby próbkowane wierszy zostały ponownie umieszczone w puli wierszy do potencjalnego ponownego użycia. W rezultacie ten sam wiersz może być przypisany do kilku fałd.

   Jeśli nie używasz wymiany (opcja domyślna), próbkowane wiersz nie jest umieszczany z powrotem w puli wierszy do potencjalnego ponownego użycia. W rezultacie każdy wiersz można przypisać tylko do jednego krotnie.

1. **Losowo podzielony:** Wybierz tę opcję, jeśli chcesz, aby wiersze były losowo przypisywane do zagęszonych.

   Jeśli ta opcja nie zostanie zaznaczona, wiersze zostaną przypisane do zagięcia za pomocą metody okrężnego.

1. **Losowy materiał siewny**: Opcjonalnie wprowadź całkowitą wartość, która ma być używana jako wartość materiału siewnego. Ta opcja jest ważna, jeśli wiersze mają być dzielone w ten sam sposób za każdym razem. W przeciwnym razie wartość domyślna **0** oznacza, że zostanie użyty losowy początkowy materiał siewny.

1. **Określ metodę partycjonowania:** Wskaż, w jaki sposób chcesz, aby dane były rozdzielane do każdej partycji, korzystając z następujących opcji:

   - **Partycja równomiernie:** Użyj tej opcji, aby umieścić taką samą liczbę wierszy w każdej partycji. Aby określić liczbę partycji wyjściowych, wprowadź liczbę całkowita w polu **Określ liczbę zagęszków, które mają być równomiernie podzielone.**

   - **Partycja o dostosowanych proporcjach:** Użyj tej opcji, aby określić rozmiar każdej partycji jako listę oddzieloną przecinkami.

     Załóżmy na przykład, że chcesz utworzyć trzy partycje. Pierwsza partycja będzie zawierać 50 procent danych. Pozostałe dwie partycje będą zawierać 25 procent danych. W polu **Lista proporcji oddzielonych przecinkami** wprowadź następujące liczby: **.5, .25, .25**.

     Suma wszystkich rozmiarów partycji musi dodać do dokładnie 1.

     Jeśli wprowadzisz liczby, które sumują się do *mniej niż 1,* zostanie utworzona dodatkowa partycja, aby pomieścić pozostałe wiersze. Na przykład, jeśli wprowadzisz wartości **.2** i **.3**, zostanie utworzona trzecia partycja, aby pomieścić pozostałe 50 procent wszystkich wierszy.
     
     Jeśli wprowadzisz liczby, które sumują się do *więcej niż 1,* błąd jest wywoływany podczas uruchamiania potoku.

1. **Podział warstwowy:** Wybierz tę opcję, jeśli wiersze mają być stratyfikowane podczas podziału, a następnie wybierz _kolumnę strata_.

1. Prześlij potok.

   Dzięki tej opcji moduł wyprowadza wiele zestawów danych. Zestawy danych są podzielone na partycje zgodnie z określonymi regułami.

### <a name="use-data-from-a-predefined-partition"></a>Używanie danych ze wstępnie zdefiniowanej partycji  

Użyj tej opcji, gdy zestaw danych został podzielony na wiele partycji, a teraz chcesz załadować każdą partycję z kolei do dalszej analizy lub przetwarzania.

1. Dodaj **partycji i przykład modułu** do potoku.

1. Podłącz moduł do danych wyjściowych poprzedniego wystąpienia **partition i sample**. To wystąpienie musi być używane **Przypisz do zagięcia** opcji do generowania pewnej liczby partycji.

1. **Tryb partycji lub próbki**: Wybierz **Pick Fold**.

1. **Określ, który z nich ma być próbkowane z**: Wybierz partycję, która ma być używana, wprowadzając jej indeks. Indeksy partycji są oparte na 1. Na przykład jeśli zestaw danych został podzielony na trzy części, partycje będą miały indeksy 1, 2 i 3.

   Jeśli wprowadzisz nieprawidłową wartość indeksu, zostanie zgłoszony błąd czasu projektowania: "Błąd 0018: Zestaw danych zawiera nieprawidłowe dane".

   Oprócz grupowania zestawu danych według zagęszonych, można podzielić zestaw danych na dwie grupy: fałd docelowy i wszystko inne. Aby to zrobić, wprowadź indeks pojedynczego krotnie, a następnie wybierz opcję **Wybierz uzupełnienie wybranego krotnie,** aby uzyskać wszystko oprócz danych w określonym krotnie.

1. Jeśli pracujesz z wieloma partycjami, należy dodać więcej wystąpień **partycji i przykładowego** modułu do obsługi każdej partycji.

   Na przykład moduł **Partycja i Próbka** w drugim wierszu jest ustawiony na **Przypisanie do zagięcia,** a moduł w trzecim wierszu jest ustawiony na **Pick Fold**.   

   ![Partycja i próbka](./media/module/partition-and-sample.png)

1. Prześlij potok.

   W przypadku tej opcji moduł wyprowadza pojedynczy zestaw danych, który zawiera tylko wiersze przypisane do tego fałdu.

> [!NOTE]
>  Nie można wyświetlać oznaczeń zagięcia bezpośrednio. Są one obecne tylko w metadanych.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 