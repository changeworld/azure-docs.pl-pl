---
title: Podręcznik informacyjny, kafelków Projektant widoków w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Przy użyciu projektanta widoku w usłudze Azure Monitor, można utworzyć niestandardowe widoki, które są wyświetlane w witrynie Azure portal i zawierają różne wizualizacje danych w obszarze roboczym usługi Log Analytics. Ten artykuł jest przewodnik odwołanie do ustawienia dla kafelków, które są dostępne w widoki niestandardowe.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 9c0283081bd7245b1b886ed82ba03130a7a3bf2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342041"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Podręcznik informacyjny projektanta widoku kafelków w usłudze Azure Monitor
Przy użyciu projektanta widoku w usłudze Azure Monitor, można tworzyć różne widoki niestandardowe w witrynie Azure portal, która pomoże Ci wizualizować dane w obszarze roboczym usługi Log Analytics. Ten artykuł jest przewodnik odwołanie do ustawienia dla kafelków, które są dostępne w widoki niestandardowe.

Aby uzyskać więcej informacji dotyczących projektanta widoków zobacz:

* [Wyświetl projektanta](view-designer.md): Omówienie projektanta widoków i procedur tworzenia i edytowania widoków niestandardowych.
* [Dokumentacja części wizualizacji](view-designer-parts.md): Przewodnik po odwołanie do ustawienia dla części wizualizacji, które są dostępne w widoki niestandardowe.


W poniższej tabeli opisano dostępnych kafelków Projektant widoków:  

| Sąsiadująco | Opis |
|:--- |:--- |
| [Liczba](#number-tile) |Liczba rekordów w wyniku zapytania. |
| [Dwóch liczb](#two-numbers-tile) |Liczba rekordów z dwóch różnych zapytań. |
| [Wykres pierścieniowy](#donut-tile) | Wykres, który jest oparty na zapytaniu, z wartością podsumowania w Centrum. |
| Wykres liniowy i objaśnienie | Wykres liniowy, który jest oparty na zapytania i objaśnienie z wartością podsumowania. |
| [Wykres liniowy](#line-chart-tile) |Wykres liniowy, który jest oparty na zapytaniu. |
| [Dwie osie czasu](#two-timelines-tile) | Wykres kolumnowy z dwóch serii każdy na podstawie osobne zapytania. |

W kolejnych sekcjach opisano typy kafelków i ich właściwości szczegółowo.

> [!NOTE]
> Kafelki w widokach są oparte na [rejestrowania zapytań](../log-query/log-query-overview.md) w obszarze roboczym usługi Log Analytics. Aktualnie nie obsługuje [zasobów zapytania obejmujące wiele](../log-query/cross-workspace-query.md) do pobierania danych z usługi Application Insights.

## <a name="number-tile"></a>Kafelka z liczbą
**Numer** Kafelek Wyświetla liczbę rekordów z zapytanie dziennika i etykietę.

![Kafelka z liczbą](media/view-designer-tiles/tile-number.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części kafelka. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Kafelek** | |
| Legenda |Tekst, który jest wyświetlany w obszarze wartości. |
| Zapytanie |Zapytanie, które jest uruchamiane. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Jeśli weryfikacja przepływu danych powinno być włączone dla kafelka, wybierz ten link. To podejście oferuje komunikat alternatywnego, jeśli dane są niedostępne. Zazwyczaj można użyć metody, aby podać komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu ustalenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zostanie wyświetlony komunikat zamiast wartość główne zapytanie. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacji przepływu danych nie zwraca żadnych danych. Jeśli podasz żaden komunikat *przeprowadzanie oceny* jest wyświetlany komunikat o stanie. |


## <a name="two-numbers-tile"></a>Kafelek dwóch liczb
Ten Kafelek zawiera liczbę rekordów z dwóch różnych dziennika zapytań i etykiety dla każdego.

![Kafelek dwóch liczb](media/view-designer-tiles/tile-two-numbers.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części kafelka. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Pierwszy Kafelek** | |
| Legenda |Tekst, który jest wyświetlany w obszarze wartości. |
| Zapytanie |Zapytanie, które jest uruchamiane. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| **Drugi Kafelek** | |
| Legenda |Tekst, który jest wyświetlany w obszarze wartości. |
| Zapytanie |Zapytanie, które jest uruchamiane. Zostanie wyświetlona liczba rekordów, które są zwracane przez zapytanie. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Jeśli weryfikacja przepływu danych powinno być włączone dla kafelka, wybierz ten link. To podejście oferuje komunikat alternatywnego, jeśli dane są niedostępne. Zazwyczaj można użyć metody, aby podać komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu ustalenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zostanie wyświetlony komunikat zamiast wartość główne zapytanie. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacji przepływu danych nie zwraca żadnych danych. Jeśli podasz żaden komunikat *przeprowadzanie oceny* jest wyświetlany komunikat o stanie. |


## <a name="donut-tile"></a>Wykres pierścieniowy kafelka
**Pierścieniowy** Kafelek Wyświetla jeden numer, który podsumowuje kolumnę wartości w zapytaniu dziennika. Wykres pierścieniowy wyświetla w postaci graficznej wyniki pierwszych trzech rekordów.

![Wykres pierścieniowy kafelka](media/view-designer-tiles/tile-donut.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części kafelka. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Wykres pierścieniowy** | |
| Zapytanie |Zapytanie, które jest uruchamiane na wykres pierścieniowy. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. To zapytanie jest zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. |
| **Wykres pierścieniowy** |**> Center** |
| Text |Tekst, który jest wyświetlany w polu wartość w wykres pierścieniowy. |
| Operacja |Operacja, która jest wykonywana we właściwości wartość, aby podsumować go jako pojedyncza wartość.<ul><li>Suma: Dodaj wartości wszystkie rekordy z wartością właściwości.</li><li>Procent: Procent wartości sumowany rekordy z wartością właściwości, w porównaniu do sumowany wartości wszystkich rekordów.</li></ul> |
| Wartości wynikowe używane w środku |Opcjonalnie wybierz znak plus (+), aby dodać co najmniej jedną wartość. Wyniki zapytania są ograniczone do rekordów za pomocą wartości właściwości, które określisz. Jeśli żadne wartości nie zostaną dodane, wszystkie rekordy zostaną uwzględnione w zapytaniu. |
| **Wykres pierścieniowy** |**> Dodatkowe opcje** |
| Kolory |Kolor, który jest wyświetlany dla każdego z trzech najważniejszych właściwości. Aby określić alternatywne kolory dla konkretnych wartości właściwości, należy użyć *zaawansowane mapowanie kolorów*. |
| Zaawansowane mapowanie kolorów |Wyświetla kolor, który reprezentuje określone wartości właściwości. Jeśli wartość, którą można określić trzy pierwsze, alternatywny kolor jest wyświetlany zamiast Kolor standardowy. Jeśli właściwość nie ma trzy pierwsze, nie jest wyświetlany kolor. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Jeśli weryfikacja przepływu danych powinno być włączone dla kafelka, wybierz ten link. To podejście oferuje komunikat alternatywnego, jeśli dane są niedostępne. Zazwyczaj można użyć metody, aby podać komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu ustalenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zostanie wyświetlony komunikat zamiast wartość główne zapytanie. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacji przepływu danych nie zwraca żadnych danych. Jeśli podasz żaden komunikat *przeprowadzanie oceny* jest wyświetlany komunikat o stanie. |


## <a name="line-chart-tile"></a>Wiersz kafelka wykresu
Ten Kafelek jest wykres liniowy, który wyświetla wielu serii, w wyniku zapytania dziennika wraz z upływem czasu. 

![Wiersz kafelka wykresu i objaśnienie](media/view-designer-tiles/tile-line-chart.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części kafelka. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, które jest uruchamiane wykresu liniowego. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. To zapytanie jest zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x używa tego przedziału czasu. Jeśli zapytanie nie używa *interwał* — słowo kluczowe, odstępów godzinowych używa osi x. |
| **Wykres liniowy** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości zwracanych przez zapytanie. Informacja ta jest używana do wyświetlania etykiet na wykresie, wskazując typów wartości i opcjonalnie do konwertowania wartości. **Typ jednostki** określa kategorię jednostki i definiuje **bieżącego typu jednostki** wartości, które są dostępne. Jeśli zostanie wybrana wartość w **przekonwertować** , a następnie wartości numeryczne są konwertowane z **bieżącej jednostki** typ **przekonwertować** typu. |
| Etykieta niestandardowa |Tekst, który jest wyświetlany na osi y obok etykiety *jednostki* typu. Jeśli żadna etykieta nie zostanie określona, tylko *jednostki* typ jest wyświetlany. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Jeśli weryfikacja przepływu danych powinno być włączone dla kafelka, wybierz ten link. To podejście oferuje komunikat alternatywnego, jeśli dane są niedostępne. Zazwyczaj można użyć metody, aby podać komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu ustalenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zostanie wyświetlony komunikat zamiast wartość główne zapytanie. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacji przepływu danych nie zwraca żadnych danych. Jeśli podasz żaden komunikat *przeprowadzanie oceny* jest wyświetlany komunikat o stanie. |


## <a name="line-chart-and-callout-tile"></a>Wiersz kafelka wykresu i objaśnienie
Ten Kafelek zawiera zarówno wiersz, wykres ten przedstawia wiele serii z dziennika zapytanie dotyczące czasu i objaśnienie przy użyciu wartości podsumowania. 

![Wiersz kafelka wykresu i objaśnienie](media/view-designer-tiles/tile-line-chart-callout.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części kafelka. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, które jest uruchamiane wykresu liniowego. Pierwsza właściwość jest wartością tekstową i drugą właściwość jest wartością liczbową. To zapytanie jest zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x używa tego przedziału czasu. Jeśli zapytanie nie używa *interwał* — słowo kluczowe, odstępów godzinowych używa osi x. |
| **Wykres liniowy** |**> Objaśnienie** |
| Tytuł objaśnienia | Tekst, który jest wyświetlany powyżej wartości objaśnienia. |
| Nazwa serii |Wartość właściwości serii, która ma być używany jako wartość objaśnienia. Jeśli seria nie zostanie podany, używane są wszystkie rekordy z zapytania. |
| Operacja |Operacja, która jest wykonywana we właściwości wartość, aby podsumować go jako pojedyncze wartości objaśnienia.<ul><li>Średnia: Średnią wartości wszystkich rekordów.</li><li>Liczba: Liczba wszystkich rekordów, które są zwracane przez zapytanie.</li><li>Ostatnia próbka: Wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Maks.: Maksymalna wartość interwałów, które znajdują się na wykresie.</li><li>Minimalna: Minimalna wartość interwałów, które znajdują się na wykresie.</li><li>Suma: Suma wartości wszystkich rekordów.</li></ul> |
| **Wykres liniowy** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości, które mają zostać zwrócone przez zapytanie. Te informacje są używane do wyświetlania etykiet wykresu, wskazujące typy wartości i, opcjonalnie, aby konwertować wartości. *Jednostki* typ Określa kategorię jednostki i definiuje dostępnych *bieżącej jednostki* wpisz wartości. Jeśli zostanie wybrana wartość w *przekonwertować*, wartości numeryczne są konwertowane z *bieżącej jednostki* typ *przekonwertować* typu. |
| Etykieta niestandardowa |Tekst, który jest wyświetlany na osi y obok etykiety *jednostki* typu. Jeśli żadna etykieta nie zostanie określona, tylko *jednostki* typ jest wyświetlany. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Jeśli weryfikacja przepływu danych powinno być włączone dla kafelka, wybierz ten link. To podejście oferuje komunikat alternatywnego, jeśli dane są niedostępne. Zazwyczaj można użyć metody, aby podać komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu ustalenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zostanie wyświetlony komunikat zamiast wartość główne zapytanie. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacji przepływu danych nie zwraca żadnych danych. Jeśli podasz żaden komunikat *przeprowadzanie oceny* jest wyświetlany komunikat o stanie. |


## <a name="two-timelines-tile"></a>Kafelek dwie osie czasu
**Dwie osie czasu** Kafelek wyświetla wyniki dwóch zapytań log wraz z upływem czasu jako wykresy kolumnowe. Objaśnienie jest wyświetlany dla każdej serii. 

![Kafelek dwie osie czasu](media/view-designer-tiles/tile-two-timelines.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części kafelka. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| Pierwszy wykres | |
| Legenda |Tekst, który jest wyświetlany w polu objaśnienia dla pierwszej serii. |
| Kolor |Kolor, który jest używany dla kolumn w pierwszej serii. |
| Zapytanie wykresu |Zapytanie, które jest uruchamiane dla pierwszej serii. Liczba rekordów w poszczególnych przedziałach czasu jest reprezentowany przez kolumn wykresu. |
| Operacja |Operacja, która jest wykonywana we właściwości wartość, aby podsumować go jako pojedyncze wartości objaśnienia.<ul><li>Średnia: Średnią wartości wszystkich rekordów.</li><li>Liczba: Liczba wszystkich rekordów, które są zwracane przez zapytanie.</li><li>Ostatnia próbka: Wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Maks.: Maksymalna wartość interwałów, które znajdują się na wykresie.</li></ul> |
| **Drugi wykres** | |
| Legenda |Tekst, który jest wyświetlany w polu objaśnienia dla drugiej serii. |
| Kolor |Kolor, który jest używany dla kolumn w drugiej serii. |
| Zapytanie wykresu |Zapytanie, które jest uruchamiane dla drugiej serii. Liczba rekordów w poszczególnych przedziałach czasu jest reprezentowany przez kolumn wykresu. |
| Operacja |Operacja, która jest wykonywana we właściwości wartość, aby podsumować go jako pojedyncze wartości objaśnienia.<ul><li>Średnia: Średnią wartości wszystkich rekordów.</li><li>Liczba: Liczba wszystkich rekordów, które są zwracane przez zapytanie.</li><li>Ostatnia próbka: Wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Maks.: Maksymalna wartość interwałów, które znajdują się na wykresie. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Jeśli weryfikacja przepływu danych powinno być włączone dla kafelka, wybierz ten link. To podejście oferuje komunikat alternatywnego, jeśli dane są niedostępne. Zazwyczaj można użyć metody, aby podać komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu ustalenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zostanie wyświetlony komunikat zamiast wartość główne zapytanie. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacji przepływu danych nie zwraca żadnych danych. Jeśli podasz żaden komunikat *przeprowadzanie oceny* jest wyświetlany komunikat o stanie. |


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) do obsługi zapytań na kafelkach.
* Dodaj [części wizualizacji](view-designer-parts.md) do widoku niestandardowego.
