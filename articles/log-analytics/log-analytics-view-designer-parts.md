---
title: Podręcznik części projektanta widoków Azure Log Analytics | Dokumentacja firmy Microsoft
description: Przy użyciu projektanta widoków analizy dzienników, można tworzyć widoki niestandardowe w portalu Azure, w których są wyświetlane różne wizualizacje danych w obszarze roboczym analizy dzienników. W tym artykule jest przewodnik odwołanie do ustawienia dla części wizualizacji, które są dostępne w niestandardowych widoków.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: b560022f1f727949021a599e1ab342551f64c084
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129136"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Podręcznik projektanta widoków części wizualizacji analizy dzienników
Przy użyciu projektanta widoków Analiza dzienników Azure, można tworzyć widoki niestandardowe w portalu Azure, która przedstawia różne wizualizacje danych z obszaru roboczego analizy dzienników. W tym artykule jest przewodnik odwołanie do ustawienia dla części wizualizacji, które są dostępne w niestandardowych widoków.

Aby uzyskać więcej informacji na temat Projektant widoków zobacz:

* [Wyświetl projektanta](log-analytics-view-designer.md): zawiera omówienie projektanta widoków i procedur tworzenia i edytowania widoków niestandardowych.
* [Kafelek odwołanie](log-analytics-view-designer-tiles.md): zawiera odwołanie do ustawienia dla każdego kafelka dostępne w niestandardowych widoków.


W poniższej tabeli opisano dostępne typy kafelka Projektant widoków:

| Typ widoku | Opis |
|:--- |:--- |
| [Listy zapytań](#list-of-queries-part) |Wyświetla listę dziennika zapytania wyszukiwania. Możesz wybrać każdego zapytania, aby wyświetlić wyniki. |
| [Liczba i listy](#number-and-list-part) |Nagłówek zawiera jeden numer, który pokazuje liczbę rekordów dziennika zapytania wyszukiwania. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie. |
| [Dwie liczb i listy](#two-numbers-and-list-part) |Nagłówek przedstawia dwóch liczb przedstawiających liczby rekordów dziennika oddzielne zapytania wyszukiwania. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie. |
| [Pierścień i listy](#donut-and-list-part) |Nagłówek przedstawia jeden numer sumującą wartości kolumny w zapytaniu dziennika. Pierścień graficznie wyświetla wyniki top trzy rekordów. |
| [Dwóch osiach czasu i listy](#two-timelines-and-list-part) |Nagłówek wyświetla wyniki dwa zapytania dziennika w czasie, gdy wykresy kolumnowe z objaśnienia wyświetlający jeden numer sumującą wartości kolumny w zapytaniu dziennika. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie. |
| [Informacje](#information-part) |Nagłówek Wyświetla tekst statyczny oraz opcjonalnie łącza. Lista zawiera jeden lub więcej elementów z tytułu statyczne i tekst. |
| [Wykres liniowy, objaśnienia i listy](#line-chart-callout-and-list-part) |Nagłówek przedstawia wykres liniowy z wielu serii z dziennika zapytanie dotyczące czasu i objaśnienie z podsumowaniem wartości. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie. |
| [Wykres liniowy i listy](#line-chart-and-list-part) |Nagłówek przedstawia wykres liniowy z wielu serii w wyniku zapytania dziennika wraz z upływem czasu. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie. |
| [Stos część wykresów wiersza](#stack-of-line-charts-part) |Wyświetla trzech oddzielnych z wykresami liniowymi, wielu serii w wyniku zapytania dziennika wraz z upływem czasu. |

Kolejne sekcje opisują typy kafelków i ich właściwości szczegółowo.

## <a name="list-of-queries-part"></a>Listę części zapytania
Listę części zapytania Wyświetla listę dziennika zapytania wyszukiwania. Możesz wybrać każdego zapytania, aby wyświetlić wyniki. Domyślnie widok zawiera pojedynczego zapytania i można wybrać **+ zapytania** można dodać dodatkowych zapytań.

![Lista Widok kwerendy](media/log-analytics-view-designer/view-list-queries.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Stanowisko |Tekst wyświetlany u góry widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Wstępnie wybrane filtry |Rozdzielana przecinkami lista właściwości, aby uwzględnić w okienku po lewej stronie filtru, po wybraniu zapytania. |
| Tryb renderowania |Widok początkowy jest wyświetlane, gdy wybrano zapytania. Po otwarciu kwerendy, można wybrać żadnych dostępnych widoków. |
| **Zapytania** | |
| Zapytanie wyszukiwania |Zapytania w celu uruchomienia. |
| Przyjazna nazwa | Opisowa nazwa jest wyświetlana. |

## <a name="number-and-list-part"></a>Liczba i listy części
Nagłówek zawiera jeden numer, który pokazuje liczbę rekordów dziennika zapytania wyszukiwania. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie.

![Lista Widok kwerendy](media/log-analytics-view-designer/view-number-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany u góry widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Tytuł** | |
| Legenda |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Zapytanie |Zapytania w celu uruchomienia nagłówka. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** | |
| Zapytanie |Zapytania w celu uruchomienia dla listy. Wyświetlane są dwa pierwsze właściwości pierwszych dziesięciu rekordów w wynikach. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. Paski są tworzone automatycznie oparte na względnej wartości kolumny liczbowej.<br><br>Użyj `Sort` polecenia w zapytaniu, aby posortować rekordy na liście. Aby uruchomić zapytanie i przywrócić wszystkie rekordy, można wybrać **zobaczyć wszystkie**. |
| Ukryj graf |Wybierz to łącze, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Nazwa i wartość separatora |Ogranicznik na potrzeby analizy właściwości text w wielu wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknij element na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby włączyć progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dwie liczby i listy
Nagłówek ma dwie liczb, zawierające liczbę rekordów dziennika oddzielne zapytania wyszukiwania. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie.

![Dwie liczby i widok listy](media/log-analytics-view-designer/view-two-numbers-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany u góry widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Tytuł nawigacji** | |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Tytuł** | |
| Legenda |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Zapytanie |Zapytania w celu uruchomienia nagłówka. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| **Lista** | |
| Zapytanie |Zapytania w celu uruchomienia dla listy. Wyświetlane są dwa pierwsze właściwości pierwszych dziesięciu rekordów w wynikach. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. Paski są tworzone automatycznie na podstawie względnej wartości liczbowe kolumny.<br><br>Użyj `Sort` polecenia w zapytaniu, aby posortować rekordy na liście. Aby uruchomić zapytanie i przywrócić wszystkie rekordy, można wybrać **zobaczyć wszystkie**. |
| Ukryj graf |Wybierz to łącze, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nazwa i wartość separatora |Ogranicznik na potrzeby analizy właściwości text w wielu wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknij element na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby włączyć progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="donut-and-list-part"></a>Część pierścień i listy
Nagłówek przedstawia jeden numer sumującą wartości kolumny w zapytaniu dziennika. Pierścień graficznie wyświetla wyniki top trzy rekordów.

![Widok pierścień i listy](media/log-analytics-view-designer/view-donut-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówek** | |
| Stanowisko |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst, który jest wyświetlany w obszarze tytuł u góry nagłówka. |
| **Pierścień** | |
| Zapytanie |Zapytanie, które mają działać pierścień. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Pierścień** |**> Center** |
| Tekst |Tekst, który jest wyświetlany w obszarze wartość w pierścień. |
| Operacja |Operacja do wykonania na wartość właściwości Podsumowując jako pojedyncza wartość.<ul><li>Sum: Dodaje wartości wszystkich rekordów.</li><li>Wartość procentowa: Stosunek rekordów zwróconych przez wartości **powoduje wartości używana podczas operacji centrum** całkowita liczba rekordów w zapytaniu.</li></ul> |
| Wartości wynikowe używane w środku |Opcjonalnie wybierz znak plus (+), aby dodać jedną lub więcej wartości. Wyniki zapytania są ograniczone do rekordów z wartościami właściwości, które określisz. Jeśli wartości nie zostaną dodane, wszystkie rekordy są uwzględnione w zapytaniu. |
| **Dodatkowe opcje** |**> Kolory** |
| Kolor 1<br>Kolor 2<br>Kolor 3 |Wybierz kolor dla każdej wartości, które są wyświetlane w pierścień. |
| **Dodatkowe opcje** |**> Mapowanie kolorów zaawansowane** |
| Wartość pola |Wpisz nazwę pola do wyświetlenia jako inny kolor, jeśli znajduje się on w pierścień. |
| Kolor |Wybierz kolor unikatowe pole. |
| **Lista** | |
| Zapytanie |Zapytania w celu uruchomienia dla listy. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| Ukryj graf |Wybierz to łącze, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nazwa i wartość separatora |Ogranicznik na potrzeby analizy właściwości text w wielu wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknij element na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby włączyć progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Dwie części osi czasu i listy
Nagłówek wyświetla wyniki dwa zapytania dziennika w czasie, gdy wykresy kolumnowe z objaśnienia wyświetlający jeden numer sumującą wartości kolumny w zapytaniu dziennika. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie.

![Wyświetlanie dwóch osiach czasu i listy](media/log-analytics-view-designer/view-two-timelines-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Tytuł nawigacji** | |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Najpierw wykresu<br>drugi wykresu** | |
| Legenda |Tekst, który jest wyświetlany w obszarze objaśnienia pierwszy serii. |
| Kolor |Kolor używany do kolumn w serii. |
| Zapytanie |Zapytania w celu uruchamiania dla pierwszej serii. Liczba rekordów przez każdego interwału czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja do wykonania na wartość właściwości Podsumowując jako pojedyncza wartość objaśnienia.<ul><li>Sum: Suma wartości ze wszystkich rekordów.</li><li>Średnia: Średniej wartości ze wszystkich rekordów.</li><li>Ostatnio przykładowe: wartość od ostatniego interwału, który znajduje się na wykresie.</li><li>Najpierw przykładowe: wartość od pierwszego interwału, który znajduje się na wykresie.</li><li>Liczba: Liczba wszystkich rekordów, które są zwracane przez zapytanie.</li></ul> |
| **Lista** | |
| Zapytanie |Zapytania w celu uruchomienia dla listy. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| Ukryj graf |Wybierz to łącze, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknij element na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby włączyć progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="information-part"></a>Część informacji
Nagłówek Wyświetla tekst statyczny oraz opcjonalnie łącza. Lista zawiera jeden lub więcej elementów z tytułu statyczne i tekst.

![Wyświetlanie informacji](media/log-analytics-view-designer/view-information.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Kolor |Kolor tła nagłówka. |
| **Nagłówek** | |
| Image (Obraz) |Plik obrazu, który jest wyświetlany w nagłówku. |
| Etykieta |Tekst, który jest wyświetlany w nagłówku. |
| **Nagłówek** |**> Link** |
| Etykieta |Tekst łącza. |
| Url |Adres Url dla łącza. |
| **Elementy informacji** | |
| Stanowisko |Tekst, który jest wyświetlany jako tytuł każdego elementu. |
| Zawartość |Tekst, który jest wyświetlany dla każdego elementu. |

## <a name="line-chart-callout-and-list-part"></a>Wykres liniowy, objaśnienia i listy
Nagłówek przedstawia wykres liniowy z wielu serii w wyniku zapytania dziennika przez czas i objaśnienie z podsumowaniem wartości. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie.

![Wykres liniowy, objaśnienia i widok listy](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówek** | |
| Stanowisko |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst, który jest wyświetlany w obszarze tytuł u góry nagłówka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytania w celu uruchomienia wykresu liniowego. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. To zapytanie zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x wykresu używa tego przedziału czasu. Jeśli zapytanie nie obejmuje *interwał* — słowo kluczowe, przedziały co godzinę używa osi x. |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres liniowy** |**> Objaśnienie** |
| Tytuł objaśnienia |Tekst, który jest wyświetlany powyżej wartości objaśnienia. |
| Nazwa serii. |Wartość właściwości serii użyć wartości objaśnienia. W przypadku serii nie są używane wszystkie rekordy z zapytania. |
| Operacja |Operacja do wykonania na wartość właściwości Podsumowując jako pojedyncza wartość objaśnienia.<ul><li>Średnia: Średniej wartości ze wszystkich rekordów.</li><li>Liczba: Liczba wszystkich rekordów, które są zwracane przez zapytanie.</li><li>Ostatnio przykładowe: wartość od ostatniego interwału, który znajduje się na wykresie.</li><li>Maksymalna liczba: Maksymalna wartość z interwałów, które znajdują się na wykresie.</li><li>Min: Wartość minimalna z interwałów, które znajdują się na wykresie.</li><li>Sum: Suma wartości ze wszystkich rekordów.</li></ul> |
| **Wykres liniowy** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link do użycia skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości zwracanej przez zapytanie. Te informacje są używane do wyświetlania etykiet wykresu, wskazujące typy wartości i, opcjonalnie, aby przekonwertować wartości. *Jednostki* typu określa kategorię jednostki i definiuje dostępnych *bieżącej jednostce* typu wartości. W przypadku wybrania wartości w *Konwertuj na*, wartości liczbowe są konwertowane z *bieżącej jednostce* typ *przekonwertować* typu. |
| Etykiety niestandardowej |Tekst, który jest wyświetlany obok etykiety osi y *jednostki* typu. Jeśli etykieta nie jest określona, tylko *jednostki* typ jest wyświetlany. |
| **Lista** | |
| Zapytanie |Zapytania w celu uruchomienia dla listy. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| Ukryj graf |Wybierz to łącze, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nazwa i wartość separatora |Ogranicznik na potrzeby analizy właściwości text w wielu wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknij element na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby włączyć progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="line-chart-and-list-part"></a>Część wiersza wykresu i listy
Nagłówek przedstawia wykres liniowy z wielu serii w wyniku zapytania dziennika wraz z upływem czasu. Na liście zostaną wyświetlone najwyższego dziesięć wyniki zapytania wykresu, który określa względne wartości liczbowej lub zmian w czasie.

![Widok wykresu i listy wiersza](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówek** | |
| Stanowisko |Tekst, który jest wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst, który jest wyświetlany w obszarze tytuł u góry nagłówka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytania w celu uruchomienia wykresu liniowego. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. To zapytanie zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x wykresu używa tego przedziału czasu. Jeśli zapytanie nie obejmuje *interwał* — słowo kluczowe, przedziały co godzinę używa osi x. |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres liniowy** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link do użycia skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości zwracanej przez zapytanie. Te informacje są używane do wyświetlania etykiet wykresu, wskazujące typy wartości i, opcjonalnie, aby przekonwertować wartości. *Jednostki* typu określa kategorię jednostki i definiuje dostępnych *bieżącej jednostce* typu wartości. W przypadku wybrania wartości w *Konwertuj na*, wartości liczbowe są konwertowane z *bieżącej jednostce* typ *przekonwertować* typu. |
| Etykiety niestandardowej |Tekst, który jest wyświetlany obok etykiety osi y *jednostki* typu. Jeśli etykieta nie jest określona, tylko *jednostki* typ jest wyświetlany. |
| **Lista** | |
| Zapytanie |Zapytania w celu uruchomienia dla listy. Liczba rekordów, które są zwracane przez zapytanie zostanie wyświetlona. |
| Ukryj graf |Wybierz to łącze, aby wyłączyć wykres z prawej strony kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresy przebiegu w czasie. |
| Operacja |Operacja do wykonania dla przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nazwa i wartość separatora |Ogranicznik na potrzeby analizy właściwości text w wielu wartości. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknij element na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> Tytuły kolumn** |
| Name (Nazwa) |Tekst, który jest wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst, który jest wyświetlany w górnej części drugiej kolumny. |
| **Lista** |**> Progi** |
| Włącz progi |Wybierz ten link, aby włączyć progów. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stos część wykresów wiersza
Stos wykres liniowy Wyświetla trzech oddzielnych z wykresami liniowymi, wielu serii w wyniku zapytania dziennika w czasie, jak pokazano poniżej:

![Stos wykresów liniowych](media/log-analytics-view-designer/view-stack-line-charts.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst, który jest wyświetlany w górnej części fragmentu. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wynik w nagłówku. |
| **Wykres 1<br>wykresu 2<br>wykresu 3** |**> Nagłówek** |
| Stanowisko |Tekst, który jest wyświetlany w górnej części wykresu. |
| Podtytuł |Tekst, który jest wyświetlany w polu Tytuł w górnej części wykresu. |
| **Wykres 1<br>wykresu 2<br>wykresu 3** |**Wykres liniowy** |
| Zapytanie |Zapytania w celu uruchomienia wykresu liniowego. Pierwszą właściwością jest wartość tekstową, a drugą właściwością jest wartość liczbowa. To zapytanie zazwyczaj używa *miary* — słowo kluczowe do podsumowania wyników. Jeśli zapytanie używa *interwał* — słowo kluczowe, osi x wykresu używa tego przedziału czasu. Jeśli zapytanie nie obejmuje *interwał* — słowo kluczowe, przedziały co godzinę używa osi x. |
| Kliknij przycisk przeglądania nawigacji | Akcja podejmowana, gdy kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres** |**> Oś y** |
| Użyj skali logarytmicznej |Wybierz ten link do użycia skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości zwracanej przez zapytanie. Te informacje są używane do wyświetlania etykiet wykresu, wskazujące typy wartości i, opcjonalnie, aby przekonwertować wartości. *Jednostki* typu określa kategorię jednostki i definiuje dostępnych *bieżącej jednostce* typu wartości. W przypadku wybrania wartości w *Konwertuj na*, wartości liczbowe są konwertowane z *bieżącej jednostce* typ *przekonwertować* typu. |
| Etykiety niestandardowej |Tekst, który jest wyświetlany obok etykiety osi y *jednostki* typu. Jeśli etykieta nie jest określona, tylko *jednostki* typ jest wyświetlany. |

## <a name="common-settings"></a>Typowe ustawienia
W poniższych sekcjach opisano ustawienia, które są wspólne dla kilku części wizualizacji.

### <a name="name-value-separator"></a>Nazwa i wartość separatora
Nazwa i wartość separatora jest ogranicznik na potrzeby analizy właściwości text w wyniku zapytania listy do wielu wartości. Jeśli zostanie określony ogranicznik, możesz podać nazwy dla każdego pola rozdzielonych tego samego ogranicznika w **nazwa** pole.

Rozważmy na przykład właściwość o nazwie *lokalizacji* który dostępnych wartości takich jak *41 budowania Redmond* i *12 budowania Bellevue*. Łączniki (-) można określić nazwę i wartość separatora i *budowania miast* dla nazwy. Takie podejście analizuje każdej wartości w dwie właściwości o nazwie *miasta* i *budynku*.

### <a name="click-through-navigation"></a>Kliknij przycisk przeglądania nawigacji
Kliknięć nawigacji definiuje, jakie działania zostaną wykonane po kliknięciu nagłówka lub listy elementu w widoku.  Spowoduje to otwarcie albo kwerendy w [dziennik wyszukiwania portalu](log-analytics-log-search-portals.md#log-search) lub uruchom innego widoku.

W poniższej tabeli opisano ustawienia kliknięć nawigacji.

| Ustawienie           | Opis |
|:--|:--|
| Rejestruj wyszukiwanie (automatycznie) | Dziennik wyszukiwania do uruchomienia po wybraniu elementu nagłówka.  To jest tym samym wyszukiwania dziennika, na podstawie elementu.
| Przeszukiwanie dzienników        | Dziennik wyszukiwania do uruchomienia po wybraniu elementu na liście.  Wpisz kwerendę do **zapytania nawigacji** pole.   Użyj *{wybranego elementu}* uwzględnienie składnia elementu wybranego użytkownika.  Na przykład, jeśli zapytanie zawiera kolumnę o nazwie *komputera* zapytanie nawigacji *{wybranego elementu}*, zapytania, takie jak *komputer = "Mój komputer"* jest uruchamiany po wybraniu komputer. Jeśli zapytanie nawigacji jest *typu = zdarzeń {wybranego elementu}*, zapytanie *typu = zdarzeń komputer = "Mój komputer"* jest uruchamiany. |
| Widok              | Widok do otwarcia po wybraniu elementu nagłówka lub element listy.  Wybierz nazwę widoku w obszarze roboczym w **nazwy widoku** pole. |



### <a name="sparklines"></a>Wykresy przebiegu w czasie
Wykres przebiegu w czasie jest wykres liniowy małych, która ilustruje wartość wpisu listy wraz z upływem czasu. Wizualizacja składników z listy można wybrać, czy ma być wyświetlany poziomy pasek wskazuje względne wartości liczbowej lub wykres przebiegu w czasie, co oznacza jego wartości w czasie.

W poniższej tabeli opisano ustawienia wykresy przebiegu w czasie:

| Ustawienie | Opis |
|:--- |:--- |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie zamiast poziomy pasek. |
| Operacja |Wykresy przebiegu w czasie są włączone, to działanie do wykonania na każdej właściwości w listy w celu obliczenia wartości dla przebiegu w czasie.<ul><li>Ostatnio przykładowe: ostatnią wartość serii przedziałach czasu.</li><li>Maksymalna liczba: Maksymalna wartość dla serii przedziałach czasu.</li><li>Min: Wartość minimalna dla serii przedziałach czasu.</li><li>Sum: Suma wartości serii dla interwału czasu.</li><li>Podsumowanie: Używa takie same `measure` polecenia kwerendy w nagłówku.</li></ul> |

### <a name="thresholds"></a>Progi
Przy użyciu progów, można wyświetlić kolorowe ikony obok każdego elementu listy. Progi umożliwiają szybkie wizualnej elementów, które przekracza określoną wartość, lub mieścić się w określonym zakresie. Na przykład można wyświetlić zieloną ikonę dla elementów z dozwolonej wartości, żółty, jeśli wartość znajduje się w zakresie, który wyświetla ostrzeżenie i czerwony, gdy przekracza wartość błędu.

Po włączeniu progi dla części, należy określić co najmniej jeden progów. Jeśli wartość elementu jest większa niż wartość progowa i niższa niż wartość progowa dalej, jest używany kolor dla tej wartości. Jeśli element jest większa niż największa wartość progowa, jest używany inny kolor. 

Każdy zestaw próg ma jeden próg o wartości **domyślne**. Jest to kolor, który jest ustawiony w przypadku przekroczenia żadnych innych wartości. Można dodawać i usuwać progów, wybierając **Dodaj** (+) lub **usunąć** (przycisk x).

W poniższej tabeli opisano ustawienia progów:

| Ustawienie | Opis |
|:--- |:--- |
| Włącz progi |Wybierz ten link do wyświetlania ikon koloru po lewej stronie każdej wartości. Ikona wskazuje wartość kondycji względem określonego progów. |
| Name (Nazwa) |Nazwa wartości progowej. |
| Próg |Wartość progu. Kolor kondycji dla każdego elementu listy jest ustawiona na kolor najwyższym wartość progową, która przekracza wartość elementu. W przypadku przekroczenia nie wartości progowych, jest używany domyślny kolor. |
| Kolor |Kolor, który wskazuje wartość progową. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) do obsługi zapytań w częściach wizualizacji.
