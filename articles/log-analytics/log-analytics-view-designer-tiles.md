---
title: Podręcznik kafelków Projektant widoków w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Przy użyciu projektanta widoków analizy dzienników, można tworzyć widoki niestandardowe w portalu Azure, w których są wyświetlane różne wizualizacje danych w obszarze roboczym analizy dzienników. W tym artykule jest przewodnik odwołanie do ustawienia dla kafelków, które są dostępne w niestandardowych widoków.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: f341cb9430c7750909c1fc1f50c15f0620e74366
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29947320"
---
# <a name="reference-guide-to-view-designer-tiles-in-log-analytics"></a>Podręcznik projektanta widoku kafelków w analizy dzienników
Przy użyciu projektanta widoków Analiza dzienników Azure, można tworzyć widoki niestandardowe w portalu Azure, która przedstawia różne wizualizacje danych w obszarze roboczym analizy dzienników. W tym artykule jest przewodnik odwołanie do ustawienia dla kafelków, które są dostępne w niestandardowych widoków.

Aby uzyskać więcej informacji na temat Projektant widoków zobacz:

* [Wyświetl projektanta](log-analytics-view-designer.md): zawiera omówienie projektanta widoków i procedur tworzenia i edytowania widoków niestandardowych.
* [Odwołanie do części wizualizacji](log-analytics-view-designer-parts.md): zawiera przewodnik odwołanie do ustawienia dla części wizualizacji, które są dostępne w niestandardowych widoków.


W poniższej tabeli opisano dostępne Kafelki Projektant widoków:  

| Kafelek | Opis |
|:--- |:--- |
| [Numer](#number-tile) |Liczba rekordów w wyniku zapytania. |
| [Dwóch liczb](#two-numbers-tile) |Liczba rekordów z dwóch różnych zapytań. |
| [pierścień](#donut-tile) | Wykres opartą na kwerendzie, w Centrum podsumowania wartość. |
| [Wykres liniowy i objaśnienie](#line-chart-amp-callout-tile) | Wykres liniowy, na podstawie zapytania i objaśnienie z wartością podsumowania. |
| [Wykres liniowy](#line-chart-tile) |Wykres liniowy, opartą na kwerendzie. |
| [Dwóch osiach czasu](#two-timelines-tile) | Wykres kolumnowy z dwóch serii każdego na podstawie osobne zapytania. |

Kolejne sekcje opisują typy kafelków i ich właściwości szczegółowo.

## <a name="number-tile"></a>Liczba kafelka
**Numer** kafelka Wyświetla liczbę rekordów dziennika zapytania i etykiety.

![Liczba kafelka](media/log-analytics-view-designer/tile-number.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Kafelek** | |
| Legenda |Tekst, który jest wyświetlany w polu wartość. |
| Zapytanie |Zapytanie, do którego jest uruchamiany. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli Weryfikacja przepływu danych powinno być włączone dla fragmentu. Metoda ta umożliwia komunikat alternatywnego, jeśli dane są niedostępne. Podejście jest zwykle używane do udostępniania komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie jest uruchomione, aby określić, czy dane są dostępne dla widoku. Jeśli zapytanie nie przynosi efektów, zamiast wartości główne zapytanie zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlana, jeśli zapytanie weryfikacji przepływu danych zwraca żadnych danych. Jeśli podasz żaden komunikat *wykonywania oceny* jest wyświetlany komunikat o stanie. |


## <a name="two-numbers-tile"></a>Kafelek dwóch liczb
Ten Kafelek Wyświetla liczbę rekordy z dwóch różnych dziennika zapytań i etykiety dla każdego.

![Kafelek dwóch liczb](media/log-analytics-view-designer/tile-two-numbers.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Pierwszy Kafelek** | |
| Legenda |Tekst, który jest wyświetlany w polu wartość. |
| Zapytanie |Zapytanie, do którego jest uruchamiany. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| **Drugi kafelka** | |
| Legenda |Tekst, który jest wyświetlany w polu wartość. |
| Zapytanie |Zapytanie, do którego jest uruchamiany. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli Weryfikacja przepływu danych powinno być włączone dla fragmentu. Metoda ta umożliwia komunikat alternatywnego, jeśli dane są niedostępne. Podejście jest zwykle używane do udostępniania komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie jest uruchomione, aby określić, czy dane są dostępne dla widoku. Jeśli zapytanie nie przynosi efektów, zamiast wartości główne zapytanie zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlana, jeśli zapytanie weryfikacji przepływu danych zwraca żadnych danych. Jeśli podasz żaden komunikat *wykonywania oceny* jest wyświetlany komunikat o stanie. |


## <a name="donut-tile"></a>Pierścień kafelka
**Pierścień** kafelka zawiera jeden numer sumującą wartości kolumny w zapytaniu dziennika. Pierścień graficznie wyświetla wyniki top trzy rekordów.

![Pierścień kafelka](media/log-analytics-view-designer/tile-donut.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **pierścień** | |
| Zapytanie |Zapytanie jest wykonywane dla pierścień. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. To zapytanie zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. |
| **pierścień** |**> Center** |
| Tekst |Tekst, który jest wyświetlany w obszarze wartość w pierścień. |
| Operacja |Operacja, która jest wykonywana dla właściwości value Podsumowując jako pojedyncza wartość.<ul><li>Sum: Dodaj odpowiednie wartości wszystkich rekordów z wartością właściwości.</li><li>Wartość procentowa: Procent równy określonemu procentowi wartości w rekordach o wartości właściwości w porównaniu z wartościami równy określonemu procentowi wszystkie rekordy.</li></ul> |
| Wynik wartości używana podczas operacji Centrum |Opcjonalnie wybierz znak plus (+), aby dodać jedną lub więcej wartości. Wyniki zapytania są ograniczone do rekordów z wartościami właściwości, które określisz. Jeśli wartości nie zostaną dodane, wszystkie rekordy są uwzględnione w zapytaniu. |
| **pierścień** |**> Dodatkowe opcje** |
| Kolory |Kolor, który jest wyświetlany dla każdej z trzech właściwości top. Aby określić alternatywne kolory określone wartości właściwości, należy użyć *zaawansowane mapowanie kolorów*. |
| Mapowanie kolorów zaawansowane |Wyświetla kolor, który reprezentuje określone wartości właściwości. Jeśli podaną wartość znajduje się w pierwszych trzech, alternatywny kolor zostanie wyświetlony standardowy koloru. Jeśli właściwość nie ma trzy najważniejsze, kolor nie jest wyświetlana. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli Weryfikacja przepływu danych powinno być włączone dla fragmentu. Metoda ta umożliwia komunikat alternatywnego, jeśli dane są niedostępne. Podejście jest zwykle używane do udostępniania komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie jest uruchomione, aby określić, czy dane są dostępne dla widoku. Jeśli zapytanie nie przynosi efektów, zamiast wartości główne zapytanie zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlana, jeśli zapytanie weryfikacji przepływu danych zwraca żadnych danych. Jeśli podasz żaden komunikat *wykonywania oceny* jest wyświetlany komunikat o stanie. |


## <a name="line-chart-tile"></a>Kafelek wykresu wiersza
Ten Kafelek jest wykres liniowy, zawierający wiele serii w wyniku zapytania dziennika wraz z upływem czasu. 

![Kafelek wykresu i objaśnienie wiersza](media/log-analytics-view-designer/tile-line-chart.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, do którego jest uruchamiany wykresu liniowego. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. To zapytanie zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x używa tego przedziału czasu. Jeśli zapytanie nie używa *interwał* — słowo kluczowe, przedziały co godzinę używa osi x. |
| **Wykres liniowy** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link do użycia skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości zwróconych przez kwerendę. Te informacje jest używany do wyświetlania etykiet na wykresie wskazujący typy wartości i opcjonalnie do konwertowania wartości. **Typ jednostki** określa kategorię jednostki i definiuje **bieżący typ jednostki** wartości, które są dostępne. W przypadku wybrania wartości w **Konwertuj na** , a następnie są konwertowane wartości liczbowych **bieżącej jednostce** typ **przekonwertować** typu. |
| Etykiety niestandardowej |Tekst, który jest wyświetlany obok etykiety osi y *jednostki* typu. Jeśli etykieta nie jest określona, tylko *jednostki* typ jest wyświetlany. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli Weryfikacja przepływu danych powinno być włączone dla fragmentu. Metoda ta umożliwia komunikat alternatywnego, jeśli dane są niedostępne. Podejście jest zwykle używane do udostępniania komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie jest uruchomione, aby określić, czy dane są dostępne dla widoku. Jeśli zapytanie nie przynosi efektów, zamiast wartości główne zapytanie zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlana, jeśli zapytanie weryfikacji przepływu danych zwraca żadnych danych. Jeśli podasz żaden komunikat *wykonywania oceny* jest wyświetlany komunikat o stanie. |


## <a name="line-chart-and-callout-tile"></a>Kafelek wykresu i objaśnienie wiersza
Ten Kafelek jest zarówno linia, wykres ten przedstawia wielu serii z dziennika zapytanie dotyczące czasu i objaśnienie z podsumowaniem wartości. 

![Kafelek wykresu i objaśnienie wiersza](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, do którego jest uruchamiany wykresu liniowego. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. To zapytanie zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x używa tego przedziału czasu. Jeśli zapytanie nie używa *interwał* — słowo kluczowe, przedziały co godzinę używa osi x. |
| **Wykres liniowy** |**> Objaśnienie** |
| Tytuł objaśnienia | Tekst, który jest wyświetlany powyżej wartości objaśnienia. |
| Nazwa serii |Wartość właściwości serii, która ma być używany jako wartość objaśnienia. W przypadku serii nie są używane wszystkie rekordy z zapytania. |
| Operacja |Operacja, która jest wykonywana dla właściwości value Podsumowując jako pojedyncza wartość objaśnienia.<ul><li>Średnia: Średniej wartości ze wszystkich rekordów.</li><li>Liczba: Liczba wszystkich rekordów, które są zwracane przez zapytanie.</li><li>Ostatnio przykładowe: wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Maksymalna liczba: Maksymalna wartość interwałów, które znajdują się na wykresie.</li><li>Min: Minimalna wartość interwałów, które znajdują się na wykresie.</li><li>Sum: Suma wartości ze wszystkich rekordów.</li></ul> |
| **Wykres liniowy** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link do użycia skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości zwracanej przez zapytanie. Te informacje są używane do wyświetlania etykiet wykresu, wskazujące typy wartości i, opcjonalnie, aby przekonwertować wartości. *Jednostki* typu określa kategorię jednostki i definiuje dostępnych *bieżącej jednostce* typu wartości. W przypadku wybrania wartości w *Konwertuj na*, wartości liczbowe są konwertowane z *bieżącej jednostce* typ *przekonwertować* typu. |
| Etykiety niestandardowej |Tekst, który jest wyświetlany obok etykiety osi y *jednostki* typu. Jeśli etykieta nie jest określona, tylko *jednostki* typ jest wyświetlany. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli Weryfikacja przepływu danych powinno być włączone dla fragmentu. Metoda ta umożliwia komunikat alternatywnego, jeśli dane są niedostępne. Podejście jest zwykle używane do udostępniania komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie jest uruchomione, aby określić, czy dane są dostępne dla widoku. Jeśli zapytanie nie przynosi efektów, zamiast wartości główne zapytanie zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlana, jeśli zapytanie weryfikacji przepływu danych zwraca żadnych danych. Jeśli podasz żaden komunikat *wykonywania oceny* jest wyświetlany komunikat o stanie. |


## <a name="two-timelines-tile"></a>Kafelek dwóch osiach czasu
**Dwóch osiach czasu** kafelka wyświetla wyniki dwa zapytania dziennika w czasie, gdy wykresy kolumnowe. Objaśnienie jest wyświetlany dla każdej serii. 

![Kafelek dwóch osiach czasu](media/log-analytics-view-designer/tile-two-timelines.png)

| Ustawienie | Opis |
|:--- |:--- |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Opis |Tekst, który jest wyświetlany w obszarze Nazwa kafelka. |
| Pierwszy wykres | |
| Legenda |Tekst, który jest wyświetlany w obszarze objaśnienia pierwszy serii. |
| Kolor |Kolor, który jest używany dla kolumn w pierwszej serii. |
| Wykres zapytania |Zapytanie, do którego jest uruchamiany pierwszy serii. Liczba rekordów przez każdego interwału czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja, która jest wykonywana dla właściwości value Podsumowując jako pojedyncza wartość objaśnienia.<ul><li>Średnia: Średniej wartości ze wszystkich rekordów.</li><li>Liczba: Liczba wszystkich rekordów, które są zwracane przez zapytanie.</li><li>Ostatnio przykładowe: wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Maksymalna liczba: Maksymalna wartość interwałów, które znajdują się na wykresie.</li></ul> |
| **Drugi wykresu** | |
| Legenda |Tekst, który jest wyświetlany w obszarze objaśnienia drugi serii. |
| Kolor |Kolor, który jest używany dla kolumn w drugiej serii. |
| Wykres zapytania |Zapytanie, do którego jest uruchamiany drugi serii. Liczba rekordów przez każdego interwału czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja, która jest wykonywana dla właściwości value Podsumowując jako pojedyncza wartość objaśnienia.<ul><li>Średnia: Średniej wartości ze wszystkich rekordów.</li><li>Liczba: Liczba wszystkich rekordów, które są zwracane przez zapytanie.</li><li>Ostatnio przykładowe: wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Maksymalna liczba: Maksymalna wartość interwałów, które znajdują się na wykresie. |
| **Zaawansowane** |**> Weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli Weryfikacja przepływu danych powinno być włączone dla fragmentu. Metoda ta umożliwia komunikat alternatywnego, jeśli dane są niedostępne. Podejście jest zwykle używane do udostępniania komunikat okresie tymczasowych, gdy widok jest zainstalowany i dane będą dostępne. |
| Zapytanie |Zapytanie jest uruchomione, aby określić, czy dane są dostępne dla widoku. Jeśli zapytanie nie przynosi efektów, zamiast wartości główne zapytanie zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlana, jeśli zapytanie weryfikacji przepływu danych zwraca żadnych danych. Jeśli podasz żaden komunikat *wykonywania oceny* jest wyświetlany komunikat o stanie. |


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) do obsługi zapytań na kafelkach.
* Dodaj [części wizualizacji](log-analytics-view-designer-parts.md) do widoku niestandardowym.
