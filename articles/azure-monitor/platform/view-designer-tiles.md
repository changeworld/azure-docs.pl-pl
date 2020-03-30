---
title: Przewodnik po kafelkach widoku projektanta w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Za pomocą projektanta widoku w usłudze Azure Monitor, można utworzyć widoki niestandardowe, które są wyświetlane w witrynie Azure portal i zawierają wiele wizualizacji na danych w obszarze roboczym usługi Log Analytics. Ten artykuł jest przewodnikiem po ustawieniach kafelków dostępnych w widokach niestandardowych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: 0320be3a2cfbb96367799577a6e56bcf5da87dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658510"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Przewodnik po przewodniku po kafelkach widoku projektanta w usłudze Azure Monitor
Korzystając z projektanta widoku w usłudze Azure Monitor, można utworzyć wiele widoków niestandardowych w witrynie Azure portal, które mogą ułatwić wizualizację danych w obszarze roboczym usługi Log Analytics. Ten artykuł jest przewodnikiem po ustawieniach kafelków dostępnych w widokach niestandardowych.

Aby uzyskać więcej informacji na temat widoku projektanta, zobacz:

* [Projektant widoku:](view-designer.md)Zawiera omówienie projektanta widoku i procedur tworzenia i edytowania widoków niestandardowych.
* [Odwołanie do części wizualizacji:](view-designer-parts.md)Zawiera przewodnik po ustawieniach części wizualizacji, które są dostępne w widokach niestandardowych.


Dostępne kafelki projektanta widoku są opisane w poniższej tabeli:  

| Kafelek | Opis |
|:--- |:--- |
| [Numer](#number-tile) |Liczba rekordów z kwerendy. |
| [Dwie liczby](#two-numbers-tile) |Liczba rekordów z dwóch różnych zapytań. |
| [Pączek](#donut-tile) | Wykres oparty na kwerendzie z wartością podsumowania w środku. |
| Wykres liniowy i objaśnienie | Wykres liniowy oparty na kwerendzie i objaśnienie z wartością podsumowania. |
| [Wykres liniowy](#line-chart-tile) |Wykres liniowy oparty na kwerendzie. |
| [Dwie osie czasu](#two-timelines-tile) | Wykres kolumnowy z dwiema seriami, każda oparta na osobnej kwerendzie. |

W następnych sekcjach opisano szczegółowo typy kafelków i ich właściwości.

> [!NOTE]
> Kafelki w widokach są oparte na [kwerendach dziennika](../log-query/log-query-overview.md) w obszarze roboczym usługi Log Analytics. Obecnie nie obsługują one [kwerend między zasobami](../log-query/cross-workspace-query.md) w celu pobrania danych z usługi Application Insights.

## <a name="number-tile"></a>Kafelek numer
Kafelek **Liczba** wyświetla zarówno liczbę rekordów z kwerendy dziennika, jak i etykietę.

![Kafelek numer](media/view-designer-tiles/tile-number.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Płytki** | |
| Legenda |Tekst wyświetlany pod wartością. |
| Zapytanie |Kwerenda, która jest uruchamiana. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz to łącze, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia, aby zapewnić komunikat w okresie przejściowym, gdy widok jest zainstalowany i dane stają się dostępne. |
| Zapytanie |Kwerenda, która jest uruchamiana w celu ustalenia, czy dane są dostępne dla widoku. Jeśli kwerenda zwraca żadnych wyników, zamiast wartości kwerendy głównej jest wyświetlany komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli kwerenda weryfikacyjna przepływu danych zwraca żadnych danych. Jeśli nie podasz żadnego komunikatu, zostanie wyświetlony komunikat o stanie *oceny wykonania.* |


## <a name="two-numbers-tile"></a>Płytka Dwie liczby
Ten kafelek wyświetla liczbę rekordów z dwóch różnych zapytań dziennika i etykiety dla każdego.

![Płytka Dwie liczby](media/view-designer-tiles/tile-two-numbers.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Pierwszy kafelek** | |
| Legenda |Tekst wyświetlany pod wartością. |
| Zapytanie |Kwerenda, która jest uruchamiana. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| **Druga płytka** | |
| Legenda |Tekst wyświetlany pod wartością. |
| Zapytanie |Kwerenda, która jest uruchamiana. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz to łącze, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia, aby zapewnić komunikat w okresie przejściowym, gdy widok jest zainstalowany i dane stają się dostępne. |
| Zapytanie |Kwerenda, która jest uruchamiana w celu ustalenia, czy dane są dostępne dla widoku. Jeśli kwerenda zwraca żadnych wyników, zamiast wartości kwerendy głównej jest wyświetlany komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli kwerenda weryfikacyjna przepływu danych zwraca żadnych danych. Jeśli nie podasz żadnego komunikatu, zostanie wyświetlony komunikat o stanie *oceny wykonania.* |


## <a name="donut-tile"></a>Płytka z pączkami
Kafelek **Donut** wyświetla pojedynczą liczbę, która podsumowuje kolumnę wartości w kwerendzie dziennika. Pączek graficznie wyświetla wyniki trzech pierwszych rekordów.

![Płytka z pączkami](media/view-designer-tiles/tile-donut.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Pączek** | |
| Zapytanie |Kwerenda, która jest uruchamiana dla pierścienia. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. Ta kwerenda zwykle używa słowa kluczowego *miara* do podsumowania wyników. |
| **Pączek** |**Centrum>** |
| Tekst |Tekst wyświetlany pod wartością wewnątrz pierścienia. |
| Operacja |Operacja, która jest wykonywana na właściwość wartość, aby podsumować go jako pojedynczą wartość.<ul><li>Suma: Dodaj wartości wszystkich rekordów z wartością właściwości.</li><li>Procent: Procent zsumowanych wartości z rekordów z wartością właściwości w porównaniu z sumowanymi wartościami wszystkich rekordów.</li></ul> |
| Wartości wyników używane w operacji center |Opcjonalnie wybierz znak plus (+), aby dodać jedną lub więcej wartości. Wyniki kwerendy są ograniczone do rekordów z wartościami właściwości, które określisz. Jeśli żadne wartości nie zostaną dodane, wszystkie rekordy są uwzględniane w kwerendzie. |
| **Pączek** |**> Dodatkowe opcje** |
| Kolory |Kolor wyświetlany dla każdej z trzech właściwości górnych. Aby określić alternatywne kolory dla określonych wartości właściwości, należy użyć *zaawansowanego mapowania kolorów*. |
| Zaawansowane mapowanie kolorów |Wyświetla kolor reprezentujący określone wartości właściwości. Jeśli określona wartość znajduje się w pierwszej trójce, zamiast koloru standardowego wyświetlany jest kolor alternatywny. Jeśli właściwość nie znajduje się w pierwszej trójce, kolor nie jest wyświetlany. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz to łącze, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia, aby zapewnić komunikat w okresie przejściowym, gdy widok jest zainstalowany i dane stają się dostępne. |
| Zapytanie |Kwerenda, która jest uruchamiana w celu ustalenia, czy dane są dostępne dla widoku. Jeśli kwerenda zwraca żadnych wyników, zamiast wartości kwerendy głównej jest wyświetlany komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli kwerenda weryfikacyjna przepływu danych zwraca żadnych danych. Jeśli nie podasz żadnego komunikatu, zostanie wyświetlony komunikat o stanie *oceny wykonania.* |


## <a name="line-chart-tile"></a>Kafelek wykresu liniowego
Ten kafelek jest wykresem liniowym, który wyświetla wiele serii z zapytania dziennika w czasie. 

![Wykres liniowy i kafelek objaśnień](media/view-designer-tiles/tile-line-chart.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Wykres liniowy** | |
| Zapytanie |Kwerenda uruchamiana dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. Ta kwerenda zwykle używa słowa kluczowego *miara* do podsumowania wyników. Jeśli kwerenda używa słowa kluczowego *interwału,* oś x używa tego przedziału czasu. Jeśli kwerenda nie używa słowa kluczowego *interwału,* oś x używa interwałów godzinowych. |
| **Wykres liniowy** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz to łącze, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości zwracanych przez kwerendę. Te informacje są używane do wyświetlania etykiet na wykresie wskazujących typy wartości i opcjonalnie do konwersji wartości. **Typ jednostki** określa kategorię jednostki i definiuje dostępne wartości **bieżącego typu jednostki.** Jeśli wybierzesz wartość w **opcji Konwertuj na** to wartości liczbowe zostaną przekonwertowane z typu **Bieżąca jednostka** na typ **Konwertuj.** |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *Jednostka.* Jeśli etykieta nie zostanie określona, wyświetlany jest tylko typ *jednostki.* |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz to łącze, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia, aby zapewnić komunikat w okresie przejściowym, gdy widok jest zainstalowany i dane stają się dostępne. |
| Zapytanie |Kwerenda, która jest uruchamiana w celu ustalenia, czy dane są dostępne dla widoku. Jeśli kwerenda zwraca żadnych wyników, zamiast wartości kwerendy głównej jest wyświetlany komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli kwerenda weryfikacyjna przepływu danych zwraca żadnych danych. Jeśli nie podasz żadnego komunikatu, zostanie wyświetlony komunikat o stanie *oceny wykonania.* |


## <a name="line-chart-and-callout-tile"></a>Wykres liniowy i kafelek objaśnień
Ten kafelek zawiera zarówno wykres liniowy, który wyświetla wiele serii z zapytania dziennika w czasie, jak i objaśnienie z podsumioną wartością. 

![Wykres liniowy i kafelek objaśnień](media/view-designer-tiles/tile-line-chart-callout.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Wykres liniowy** | |
| Zapytanie |Kwerenda uruchamiana dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. Ta kwerenda zwykle używa słowa kluczowego *miara* do podsumowania wyników. Jeśli kwerenda używa słowa kluczowego *interwału,* oś x używa tego przedziału czasu. Jeśli kwerenda nie używa słowa kluczowego *interwału,* oś x używa interwałów godzinowych. |
| **Wykres liniowy** |**Objaśnienie>** |
| Tytuł objaśnienia | Tekst wyświetlany nad wartością objaśnienia. |
| Nazwa serii |Wartość właściwości serii, która ma być używana jako wartość objaśnienia. Jeśli nie podano żadnej serii, używane są wszystkie rekordy z kwerendy. |
| Operacja |Operacja, która jest wykonywana na właściwość wartość, aby podsumować go jako pojedynczą wartość dla objaśnienia.<ul><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Count: Liczba wszystkich rekordów, które są zwracane przez kwerendę.</li><li>Ostatnia próbka: wartość ostatniego interwału, który jest uwzględniony na wykresie.</li><li>Max: Maksymalna wartość interwałów uwzględnionych na wykresie.</li><li>Min: Minimalna wartość interwałów uwzględnionych na wykresie.</li><li>Suma: Suma wartości ze wszystkich rekordów.</li></ul> |
| **Wykres liniowy** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz to łącze, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości, które mają być zwracane przez kwerendę. Te informacje są używane do wyświetlania etykiet wykresu wskazujących typy wartości i opcjonalnie do konwertowania wartości. Typ *jednostki* określa kategorię jednostki i definiuje dostępne wartości typu *bieżąca jednostka.* W przypadku wybrania wartości w *polu Konwertuj*na wartość liczbową wartości liczbowe zostaną przekonwertowane z typu *Bieżąca jednostka* na *typ Konwertuj.* |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *Jednostka.* Jeśli etykieta nie zostanie określona, wyświetlany jest tylko typ *jednostki.* |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz to łącze, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia, aby zapewnić komunikat w okresie przejściowym, gdy widok jest zainstalowany i dane stają się dostępne. |
| Zapytanie |Kwerenda, która jest uruchamiana w celu ustalenia, czy dane są dostępne dla widoku. Jeśli kwerenda zwraca żadnych wyników, zamiast wartości kwerendy głównej jest wyświetlany komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli kwerenda weryfikacyjna przepływu danych zwraca żadnych danych. Jeśli nie podasz żadnego komunikatu, zostanie wyświetlony komunikat o stanie *oceny wykonania.* |


## <a name="two-timelines-tile"></a>Kafelek Dwie osie czasu
**Kafelek Dwie osie czasu** wyświetla wyniki dwóch zapytań dziennika w czasie jako wykresy kolumnowe. Objaśnienie jest wyświetlane dla każdej serii. 

![Kafelek Dwie osie czasu](media/view-designer-tiles/tile-two-timelines.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| Pierwszy wykres | |
| Legenda |Tekst wyświetlany pod objaśnieniami dla pierwszej serii. |
| Kolor |Kolor używany dla kolumn w pierwszej serii. |
| Kwerenda wykresu |Kwerenda, która jest uruchamiana dla pierwszej serii. Liczba rekordów w każdym przedziale czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja, która jest wykonywana na właściwość wartość, aby podsumować go jako pojedynczą wartość dla objaśnienia.<ul><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Count: Liczba wszystkich rekordów, które są zwracane przez kwerendę.</li><li>Ostatnia próbka: wartość ostatniego interwału, który jest uwzględniony na wykresie.</li><li>Max: Maksymalna wartość interwałów uwzględnionych na wykresie.</li></ul> |
| **Drugi wykres** | |
| Legenda |Tekst wyświetlany pod objaśnieniami dla drugiej serii. |
| Kolor |Kolor używany dla kolumn w drugiej serii. |
| Kwerenda wykresu |Kwerenda, która jest uruchamiana dla drugiej serii. Liczba rekordów w każdym przedziale czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja, która jest wykonywana na właściwość wartość, aby podsumować go jako pojedynczą wartość dla objaśnienia.<ul><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Count: Liczba wszystkich rekordów, które są zwracane przez kwerendę.</li><li>Ostatnia próbka: wartość ostatniego interwału, który jest uwzględniony na wykresie.</li><li>Max: Maksymalna wartość interwałów uwzględnionych na wykresie. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz to łącze, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia, aby zapewnić komunikat w okresie przejściowym, gdy widok jest zainstalowany i dane stają się dostępne. |
| Zapytanie |Kwerenda, która jest uruchamiana w celu ustalenia, czy dane są dostępne dla widoku. Jeśli kwerenda zwraca żadnych wyników, zamiast wartości kwerendy głównej jest wyświetlany komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli kwerenda weryfikacyjna przepływu danych zwraca żadnych danych. Jeśli nie podasz żadnego komunikatu, zostanie wyświetlony komunikat o stanie *oceny wykonania.* |


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do obsługi zapytań na kafelkach.
* Dodaj [części wizualizacji](view-designer-parts.md) do widoku niestandardowego.
