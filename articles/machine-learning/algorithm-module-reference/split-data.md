---
title: 'Dane podzielone: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z modułu Podziel dane w usłudze Azure Machine Learning, aby podzielić zestaw danych na dwa różne zestawy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9eba6f2c47629b708dde4a5a2888b76dbd24b4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455897"
---
# <a name="split-data-module"></a>Moduł Podziału danych

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Moduł Podziel dane służy do dzielenia zestawu danych na dwa odrębne zestawy.

Ten moduł jest przydatny, gdy trzeba podzielić dane na zestawy szkoleniowe i testowe. Można również dostosować sposób dzielenia danych. Niektóre opcje obsługują randomizację danych. Inne są dostosowane do określonego typu danych lub typu modelu.

## <a name="configure-the-module"></a>Konfigurowanie modułu

> [!TIP]
> Przed wybraniem trybu podziału przeczytaj wszystkie opcje, aby określić typ podziału, którego potrzebujesz.
> Jeśli zmienisz tryb podziału, wszystkie inne opcje mogą zostać zresetowane.

1. Dodaj moduł **Podziel dane** do potoku w projektancie. Ten moduł można znaleźć w obszarze **Transformacja danych**, w **przykładowej i podzielonej** kategorii.

1. **Tryb dzielenia:** Wybierz jeden z następujących trybów, w zależności od typu posiadanych danych i sposobu ich dzielenia. Każdy tryb dzielenia ma różne opcje.

   - **Podziel wiersze:** Użyj tej opcji, jeśli chcesz podzielić dane na dwie części. Można określić procent danych do umieszczenia w każdym podziale. Domyślnie dane są dzielone 50/50.

     Można również losowo zaznaczyć wiersze w każdej grupie i użyć próbkowania stratified. W próbkowaniu stratified należy wybrać pojedynczą kolumnę danych, dla których mają być podzielone wartości równo między dwa zestawy danych wynik.  

   - **Podział wyrażenia regularnego:** Wybierz tę opcję, jeśli chcesz podzielić zestaw danych, testując pojedynczą kolumnę dla wartości.

     Na przykład, jeśli analizujesz tonację, możesz sprawdzić obecność określonej nazwy produktu w polu tekstowym. Następnie można podzielić zestaw danych na wiersze z docelową nazwą produktu i wierszami bez docelowej nazwy produktu.

   - **Podział wyrażenia względnego:** Użyj tej opcji, gdy chcesz zastosować warunek do kolumny liczbowej. Numer może być polem daty/godziny, kolumną zawierającą kwoty wiekowe lub dolarowe, a nawet wartością procentową. Na przykład można podzielić zestaw danych na podstawie kosztu towarów, grupować osoby według przedziałów wiekowych lub oddzielać dane według daty kalendarzowej.

### <a name="split-rows"></a>Podziel wiersze

1. Dodaj moduł [Podziel dane](./split-data.md) do potoku w projektancie i połącz zestaw danych, który chcesz podzielić.
  
1. W **trybie dzielenia**wybierz opcję **Podziel wiersze**. 

1. **Ułamek wierszy w pierwszym wyjściowym zestawie danych:** Użyj tej opcji, aby określić, ile wierszy przejdzie do pierwszego (lewego) wyjścia. Wszystkie pozostałe wiersze zostaną wprowadzone do drugiego (po prawej stronie) wyjścia.

   Współczynnik reprezentuje procent wierszy wysłanych do pierwszego zestawu danych wyjściowych, więc należy wprowadzić liczbę dziesiętną z 0 do 1.
     
   Na przykład jeśli wprowadzisz **0,75** jako wartość, zestaw danych zostanie podzielony 75/25. W tym podziale 75 procent wierszy zostanie wysłanych do pierwszego zestawu danych wyjściowych. Pozostałe 25 procent zostanie wysłane do drugiego zestawu danych wyjściowych.
  
1. Wybierz opcję **Randomized split,** jeśli chcesz losowo wybrać dane w dwóch grupach. Jest to preferowana opcja podczas tworzenia zestawów danych szkoleniowych i testowych.

1. **Losowy materiał siewny:** Wprowadź nieujemną wartość całkowitą, aby rozpocząć sekwencję pseudolosów wystąpień, które mają być używane. Ten domyślny materiał siewny jest używany we wszystkich modułach, które generują liczby losowe. 

   Określenie materiału siewnego sprawia, że wyniki są powtarzalne. Jeśli chcesz powtórzyć wyniki operacji podziału, należy określić materiał siewny dla generatora liczb losowych. W przeciwnym razie losowy materiał siewny jest domyślnie ustawiony na **0**, co oznacza, że początkowa wartość materiału siewnego jest otrzymywana z zegara systemowego. W rezultacie rozkład danych może być nieco inny za każdym razem, gdy wykonujesz podział. 

1. **Podział warstwowy:** Ustaw tę opcję na **Wartość True,** aby upewnić się, że dwa wyjściowe zestawy danych zawierają reprezentatywną próbkę wartości w *kolumnie warstwy* lub *kolumnie klucza stratyfikacji*. 

   W wyniku próbkowania stratowanego dane są dzielone w taki sposób, że każdy wyjściowy zestaw danych pobiera mniej więcej taki sam procent każdej wartości docelowej. Na przykład można upewnić się, że zestawy szkoleniowe i testowe są z grubsza zrównoważone w odniesieniu do wyniku lub innej kolumny (takiej jak płeć).

1. Prześlij potok.


## <a name="select-a-regular-expression"></a>Wybieranie wyrażenia regularnego

1. Dodaj moduł [Podziel dane](./split-data.md) do potoku i połącz go jako dane wejściowe z zestawem danych, który chcesz podzielić.  
  
1. W **trybie dzielenia**wybierz opcję **Podział wyrażenia regularnego**.

1. W polu **Wyrażenie regularne** wprowadź prawidłowe wyrażenie regularne. 
  
   Wyrażenie regularne należy wykonać składnię języka Python dla wyrażeń regularnych.

1. Prześlij potok.

   Na podstawie wyrażenia regularnego, które podasz, zestaw danych jest podzielony na dwa zestawy wierszy: wiersze z wartościami, które pasują do wyrażenia i wszystkie pozostałe wiersze. 

Poniższe przykłady pokazują, jak podzielić zestaw danych przy użyciu opcji **wyrażenia regularnego.** 

### <a name="single-whole-word"></a>Pojedyncze całe słowo 

W tym przykładzie umieszcza się w pierwszym `Gryphon` zestawie danych `Text`wszystkie wiersze, które zawierają tekst w kolumnie . Umieszcza inne wiersze w drugim wyjściu **split data**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Podciąg

W tym przykładzie wyszukuje określony ciąg w dowolnej pozycji w drugiej kolumnie zestawu danych. Pozycja jest oznaczona w tym miejscu przez wartość indeksu 1. W dopasowanie jest rozróżniana wielkość liter.

```text
(\1) ^[a-f]
```

Pierwszy wynikowy zestaw danych zawiera wszystkie wiersze, w których `a` `b`kolumna indeksu zaczyna się od jednego z następujących znaków: `c`, , , `d`, `e`, . `f` Wszystkie pozostałe wiersze są kierowane do drugiego wyjścia.

## <a name="select-a-relative-expression"></a>Zaznaczanie wyrażenia względnego

1. Dodaj moduł [Podziel dane](./split-data.md) do potoku i połącz go jako dane wejściowe z zestawem danych, który chcesz podzielić.
  
1. W **trybie dzielenia**wybierz opcję **Wyrażenie względne**.
  
1. W polu **Wyrażenia relacyjne** wprowadź wyrażenie, które wykonuje operację porównania w jednej kolumnie.

   Dla **kolumny numerycznej:**
   - Kolumna zawiera numery dowolnego typu danych liczbowych, w tym typy danych daty i godziny.
   - Wyrażenie może odwoływać się do maksymalnie jednej nazwy kolumny.
   - Użyj znaku ampersand, `&`, dla operacji AND. Użyj znaku rury, `|`dla operacji OR.
   - Obsługiwane są następujące operatory: `<`, `==` `!=`, `>` `<=`, `>=`, , .
   - Nie można grupować operacji `(` `)`przy użyciu i .
   
   W **przypadku kolumny Ciąg:**
   - Obsługiwane są następujące operatory: `==`, `!=`.

1. Prześlij potok.

   Wyrażenie dzieli zestaw danych na dwa zestawy wierszy: wiersze z wartościami spełniania warunku i wszystkie pozostałe wiersze.

Poniższe przykłady pokazują, jak podzielić zestaw danych przy użyciu opcji **Wyrażenie względne** w module **Podziel dane.**  

### <a name="calendar-year"></a>Rok kalendarzowy

Typowym scenariuszem jest podzielenie zestawu danych przez lata. Następujące wyrażenie wybiera wszystkie wiersze, w `Year` których wartości `2010`w kolumnie są większe niż .

```text
\"Year" > 2010
```

Wyrażenie daty musi uwzględniać wszystkie części daty, które są zawarte w kolumnie danych. Format dat w kolumnie danych musi być spójny. 

Na przykład w kolumnie daty, `mmddyyyy`która używa formatu, wyrażenie powinno być podobne do następującego:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Indeks kolumn

Poniższe wyrażenie pokazuje, jak można użyć indeksu kolumny, aby zaznaczyć wszystkie wiersze w pierwszej kolumnie zestawu danych, które zawierają wartości mniejsze lub równe 30, ale nie równe 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
