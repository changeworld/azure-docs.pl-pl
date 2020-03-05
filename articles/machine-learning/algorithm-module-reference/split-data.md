---
title: 'Podziel dane: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Split Data w Azure Machine Learning, aby podzielić zestaw danych na dwa różne zestawy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d889cd3325784f564d03e5d75dde1ec760c66804
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268539"
---
# <a name="split-data-module"></a>Moduł Split Data

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Użyj tego modułu, aby podzielić zestaw danych na dwa różne zestawy.

Ten moduł jest szczególnie przydatny, gdy trzeba podzielić dane na zestawy szkoleniowe i testowe. Można dostosować sposób, w jaki dane są dzielone. Niektóre opcje obsługują generowanie losowe danych. inne są dostosowane do określonego typu danych lub typu modelu.

## <a name="how-to-configure"></a>Jak skonfigurować

> [!TIP]
> Przed wybraniem trybu dzielenia, Przeczytaj wszystkie opcje, aby określić typ potrzebnego podziału.
> W przypadku zmiany trybu podziału można zresetować wszystkie inne opcje.

1. Dodaj moduł **Split Data (podział danych** ) do potoku w projektancie. Ten moduł można znaleźć w obszarze **Przekształcanie danych**w kategorii **przykład i Podziel** .

2. **Tryb dzielenia**: Wybierz jeden z następujących trybów, w zależności od typu danych i sposobu, w jaki chcesz go podzielić. Każdy tryb dzielenia ma inne opcje. Aby uzyskać szczegółowe instrukcje i przykłady, kliknij poniższe tematy. 

    - **Podziel wiersze**: Użyj tej opcji, jeśli chcesz podzielić dane na dwie części. Możesz określić wartość procentową danych do umieszczenia w poszczególnych podzielonych, ale domyślnie dane są podzielone na 50-50.

        Możesz również losowo wybrać wiersze w każdej grupie i użyć próbkowania stratified. W przypadku próbkowania stratified należy wybrać pojedynczą kolumnę danych, dla której wartości mają być rozdzielane równo między dwa zestawy danych wynikowych.  

    - **Podział wyrażenia regularnego**  Wybierz tę opcję, jeśli chcesz podzielić zestaw danych przez testowanie pojedynczej kolumny dla wartości.

        Na przykład jeśli analizujesz tonacji, możesz sprawdzić obecność nazwy konkretnego produktu w polu tekstowym, a następnie podzielić zestaw danych na wiersze z docelową nazwą produktu, a nie.

    - **Podzielone wyrażenie względne**: Użyj tej opcji, jeśli chcesz zastosować warunek do kolumny liczb. Liczba może być polem daty/godziny, kolumną zawierającą kwoty wieku lub dolara, a nawet wartością procentową. Na przykład możesz chcieć podzielić zestaw danych w zależności od kosztu elementów, grupować osoby według zakresów wieku lub oddzielić dane według daty kalendarzowej.

### <a name="split-rows"></a>Podziel wiersze

1.  Dodaj moduł [Split Data (podział danych](./split-data.md) ) do potoku w projektancie, a następnie połącz zestaw danych, który chcesz podzielić.
  
2.  W obszarze **tryb dzielenia**wybierz polecenie **Podziel wiersze**. 

3.  **Część wierszy z pierwszego wyjściowego zestawu danych**. Użyj tej opcji, aby określić liczbę wierszy, które są przełączane do pierwszych (po lewej stronie). Wszystkie pozostałe wiersze będą przechodzić do drugiego (prawostronnego) danych wyjściowych.

    Współczynnik reprezentuje procent wierszy wysyłanych do pierwszego wyjściowego zestawu danych, dlatego należy wpisać liczbę dziesiętną z zakresu od 0 do 1.
     
     Na przykład, jeśli wpiszesz 0,75 jako wartość, zestaw danych zostałby podzielony przy użyciu współczynnika 75:25, z 75% wierszy wysyłanych do pierwszego wyjściowego zestawu danych i 25% wysłanych do drugiego wyjściowego zestawu danych.
  
4. Wybierz opcję **Losowa Split (Wyodrębnij** ), jeśli chcesz losowo wybierać dane w dwóch grupach. Jest to preferowana opcja podczas tworzenia szkoleń i testów zestawów danych.

5.  **Losowy inicjator**: wpisz nieujemną wartość całkowitą, aby zainicjować sekwencję pseudolosowych wystąpień do użycia. Ten domyślny inicjator jest używany we wszystkich modułach generujących liczby losowe. 

     Określenie inicjatora sprawia, że wyniki są zwykle odtwarzalne. Jeśli trzeba powtórzyć wyniki operacji podziału, należy określić inicjator dla generatora liczb losowych. W przeciwnym razie losowy inicjator jest domyślnie ustawiony na wartość 0, co oznacza, że początkowa wartość inicjatora jest uzyskiwana z zegara systemowego. W związku z tym Dystrybucja danych może być nieco inna przy każdym wykonaniu podziału. 

6. **Stratified**: Ustaw tę opcję na **wartość true** , aby upewnić się, że dwa wyjściowe zestawy danych zawierają reprezentatywną próbę wartości w kolumnie *strata* lub *kolumnie klucza stratyfikacji*. 

    W przypadku próbkowania stratified dane są dzielone w taki sposób, że każdy wyjściowy zestaw danych uzyskuje się w nierównej części każdej wartości docelowej. Można na przykład upewnić się, że zestawy szkoleniowe i testowe są w przybliżeniu zrównoważone w odniesieniu do wyniku, lub w odniesieniu do innej kolumny, takiej jak płeć.

7. Uruchamianie potoku.


## <a name="regular-expression-split"></a>Podział wyrażenia regularnego

1.  Dodaj moduł [Split Data](./split-data.md) do potoku, a następnie połącz go jako dane wejściowe z zestawem danych, który chcesz podzielić.  
  
2.  W obszarze **tryb dzielenia**wybierz pozycję **podział wyrażenia regularnego**.

3. W polu **wyrażenie regularne** wpisz prawidłowe wyrażenie regularne. 
  
   Wyrażenie regularne powinno przestrzegać składni wyrażeń regularnych języka Python.


4. Uruchamianie potoku.

    Na podstawie podanego wyrażenia regularnego zestaw danych jest podzielony na dwa zestawy wierszy: wiersze z wartościami, które pasują do wyrażenia i wszystkie pozostałe wiersze. 

W poniższych przykładach pokazano, jak podzielić zestaw danych przy użyciu opcji **wyrażenia regularnego** . 

### <a name="single-whole-word"></a>Pojedyncze całe słowo 

W tym przykładzie do pierwszego zestawu danych są wszystkie wiersze, które zawierają tekst `Gryphon` w kolumnie `Text`i umieszczane są inne wiersze w drugim danych wyjściowych z **podziałem**:

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Podciąg

Ten przykład wyszukuje określony ciąg w dowolnej pozycji w drugiej kolumnie zestawu danych, w tym miejscu według wartości indeksu 1. W dopasowaniu jest rozróżniana wielkość liter.

```text
(\1) ^[a-f]
```

Pierwszy wynikowy zestaw danych zawiera wszystkie wiersze, w których kolumna indeksu zaczyna się od jednego z następujących znaków: `a`, `b`, `c`, `d`, `e`, `f`. Wszystkie inne wiersze są kierowane do drugiego danych wyjściowych.

## <a name="relative-expression-split"></a>Podziel wyrażenie względne.

1. Dodaj moduł [Split Data](./split-data.md) do potoku, a następnie połącz go jako dane wejściowe z zestawem danych, który chcesz podzielić.
  
2. W obszarze **tryb dzielenia**wybierz pozycję **podział wyrażenia względnego**.
  
3. W polu tekstowym **wyrażenie relacyjne** wpisz wyrażenie, które wykonuje operację porównywania w pojedynczej kolumnie:

   Dla **kolumny liczbowej**:
   - Kolumna zawiera numery dowolnego typu danych liczbowych, w tym typy danych daty i godziny.
   - Wyrażenie może odwoływać się do maksymalnie jednej nazwy kolumny.
   - Użyj znaku handlowego "`&`" dla operacji i. Użyj `|` znak potoku dla operacji lub.
   - Obsługiwane są następujące operatory: `<`, `>`, `<=`, `>=`, `==`, `!=`.
   - Nie można grupować operacji przy użyciu `(` i `)`.
   
   Dla **kolumny String**:
   - Obsługiwane są następujące operatory: `==`, `!=`.

4. Uruchamianie potoku.

    Wyrażenie dzieli zestaw danych na dwa zestawy wierszy: wiersze z wartościami, które spełniają warunek i wszystkie pozostałe wiersze.

W poniższych przykładach pokazano, jak podzielić zestaw danych przy użyciu opcji **wyrażenie względne** w module **Split Data** :  

### <a name="using-calendar-year"></a>Przy użyciu roku kalendarzowego

Typowym scenariuszem jest dzielenie zestawu danych według lat. Poniższe wyrażenie wybiera wszystkie wiersze, w których wartości w kolumnie `Year` są większe niż `2010`.

```text
\"Year" > 2010
```

Wyrażenie Date musi uwzględniać wszystkie części daty, które są uwzględnione w kolumnie dane, a format dat w kolumnie danych musi być spójny. 

Na przykład w kolumnie daty używającej formatu `mmddyyyy`wyrażenie powinno wyglądać następująco:

```text
\"Date" > 1/1/2010
```

### <a name="using-column-indices"></a>Używanie indeksów kolumn

W poniższym wyrażeniu pokazano, jak można użyć indeksu kolumn, aby zaznaczyć wszystkie wiersze w pierwszej kolumnie zestawu danych, które zawierają wartości mniejsze niż lub równe 30, ale nie równe 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
