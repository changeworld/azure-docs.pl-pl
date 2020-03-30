---
title: Przewodnik po przewodniku po elementach widoku projektanta w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Za pomocą projektanta widoku w usłudze Azure Monitor, można utworzyć widoki niestandardowe, które są wyświetlane w witrynie Azure portal i zawierają wiele wizualizacji na danych w obszarze roboczym usługi Log Analytics. Ten artykuł jest przewodnikiem po ustawieniach części wizualizacji dostępnych w widokach niestandardowych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 7dc4164cac1998a02ae62d7682f9630aa4faa619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658561"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Przewodnik po przewodniku po wyświetlaniu części wizualizacji projektanta w usłudze Azure Monitor
Korzystając z projektanta widoku w usłudze Azure Monitor, można utworzyć wiele widoków niestandardowych w witrynie Azure portal, które mogą ułatwić wizualizację danych w obszarze roboczym usługi Log Analytics. Ten artykuł jest przewodnikiem po ustawieniach części wizualizacji dostępnych w widokach niestandardowych.

Aby uzyskać więcej informacji na temat widoku projektanta, zobacz:

* [Projektant widoku:](view-designer.md)Zawiera omówienie projektanta widoku i procedur tworzenia i edytowania widoków niestandardowych.
* [Odwołanie do kafelków:](view-designer-tiles.md)Zawiera odwołanie do ustawień każdego dostępnego kafelka w widokach niestandardowych.


Dostępne typy kafelków widoku projektanta są opisane w poniższej tabeli:

| Typ widoku | Opis |
|:--- |:--- |
| [Lista zapytań](#list-of-queries-part) |Wyświetla listę zapytań dziennika. Można wybrać każdą kwerendę, aby wyświetlić jej wyniki. |
| [Numer i lista](#number-and-list-part) |Nagłówek wyświetla pojedynczą liczbę, która pokazuje liczbę rekordów z kwerendy dziennika. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie. |
| [Dwie liczby i lista](#two-numbers-and-list-part) |Nagłówek wyświetla dwie liczby, które pokazują liczbę rekordów z oddzielnych zapytań dziennika. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie. |
| [Pączek i lista](#donut-and-list-part) |Nagłówek wyświetla pojedynczą liczbę, która podsumowuje kolumnę wartości w kwerendzie dziennika. Pączek graficznie wyświetla wyniki trzech pierwszych rekordów. |
| [Dwie osie czasu i lista](#two-timelines-and-list-part) |Nagłówek wyświetla wyniki dwóch zapytań dziennika w czasie jako wykresy kolumnowe, z objaśnienia, który wyświetla jedną liczbę, która podsumowuje kolumnę wartości w kwerendzie dziennika. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie. |
| [Informacje](#information-part) |Nagłówek wyświetla tekst statyczny i łącze opcjonalne. Na liście jest wyświetlany jeden lub więcej elementów o statycznym tytule i tekście. |
| [Wykres liniowy, objaśnienie i lista](#line-chart-callout-and-list-part) |Nagłówek wyświetla wykres liniowy z wieloma seriami z zapytania dziennika w czasie i objaśnieniem z sumaryzioną wartością. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie. |
| [Wykres liniowy i lista](#line-chart-and-list-part) |Nagłówek wyświetla wykres liniowy z wieloma seriami z zapytania dziennika w czasie. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie. |
| [Układ wykresów liniowych część](#stack-of-line-charts-part) |Wyświetla trzy oddzielne wykresy liniowe z wieloma seriami z zapytania dziennika w czasie. |

W następnych sekcjach opisano szczegółowo typy kafelków i ich właściwości.

> [!NOTE]
> Części w widokach są oparte na [kwerendach dziennika](../log-query/log-query-overview.md) w obszarze roboczym usługi Log Analytics. Obecnie nie obsługują one [kwerend między zasobami](../log-query/cross-workspace-query.md) w celu pobrania danych z usługi Application Insights.

## <a name="list-of-queries-part"></a>Lista kwerend część
Lista kwerend część wyświetla listę zapytań dziennika. Można wybrać każdą kwerendę, aby wyświetlić jej wyniki. Widok zawiera jedną kwerendę domyślnie i można wybrać **+ Kwerenda,** aby dodać dodatkowe zapytania.

![Lista zapytań widoku](media/view-designer-parts/view-list-queries.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł |Tekst wyświetlany u góry widoku. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Wstępnie wybrane filtry |Lista właściwości rozdzielanych przecinkami, które mają być uwzględnione w lewym okienku filtru po wybraniu kwerendy. |
| Tryb renderowania |Widok początkowy wyświetlany po wybraniu kwerendy. Po otwarciu kwerendy można wybrać dowolne dostępne widoki. |
| **Zapytania** | |
| Zapytanie wyszukiwania |Kwerenda do uruchomienia. |
| Przyjazna nazwa | Wyświetlana nazwa opisowa. |

## <a name="number-and-list-part"></a>Numer i część listy
Nagłówek wyświetla pojedynczą liczbę, która pokazuje liczbę rekordów z kwerendy dziennika. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie.

![Lista zapytań widoku](media/view-designer-parts/view-number-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany u góry widoku. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Użyj ikony |Wybierz to łącze, aby wyświetlić ikonę. |
| **Tytuł** | |
| Legenda |Tekst wyświetlany w górnej części nagłówka. |
| Zapytanie |Kwerenda do uruchomienia nagłówka. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Listy** | |
| Zapytanie |Kwerenda do uruchomienia dla listy. Zostaną wyświetlone pierwsze dwie właściwości pierwszych dziesięciu rekordów w wynikach. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. Paski są tworzone automatycznie, które są oparte na względnej wartości kolumny liczbowej.<br><br>Użyj `Sort` polecenia w kwerendzie, aby posortować rekordy na liście. Aby uruchomić kwerendę i zwrócić wszystkie rekordy, można wybrać **opcję Zobacz wszystkie**. |
| Ukryj wykres |Wybierz to łącze, aby wyłączyć wykres po prawej stronie kolumny numerycznej. |
| Włączanie wykresów przebiegu w przebiegu w przebiegu |Wybierz to łącze, aby wyświetlić wykres przebiegu w przebiegu w przebiegu zamiast paska poziomego. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Kolor |Kolor pasków lub wykresów przebiegu w przebiegu. |
| Separator nazw i wartości |Ogranicznik jednoznakowy do użycia do analizowanie właściwości text na wiele wartości. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Listy** |**tytuły kolumn>** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Listy** |**Progi>** |
| Włącz progi |Wybierz to łącze, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dwie liczby i część listy
Nagłówek ma dwie liczby, które wyświetlają liczbę rekordów z oddzielnych zapytań dziennika. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie.

![Widok listy dwie liczby &](media/view-designer-parts/view-two-numbers-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany u góry widoku. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Użyj ikony |Wybierz to łącze, aby wyświetlić ikonę. |
| **Nawigacja tytuł** | |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Tytuł** | |
| Legenda |Tekst wyświetlany w górnej części nagłówka. |
| Zapytanie |Kwerenda do uruchomienia nagłówka. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| **Listy** | |
| Zapytanie |Kwerenda do uruchomienia dla listy. Zostaną wyświetlone pierwsze dwie właściwości pierwszych dziesięciu rekordów w wynikach. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. Słupki są tworzone automatycznie na podstawie względnej wartości kolumny liczbowej.<br><br>Użyj `Sort` polecenia w kwerendzie, aby posortować rekordy na liście. Aby uruchomić kwerendę i zwrócić wszystkie rekordy, można wybrać **opcję Zobacz wszystkie**. |
| Ukryj wykres |Wybierz to łącze, aby wyłączyć wykres po prawej stronie kolumny numerycznej. |
| Włączanie wykresów przebiegu w przebiegu w przebiegu |Wybierz to łącze, aby wyświetlić wykres przebiegu w przebiegu w przebiegu zamiast paska poziomego. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Kolor |Kolor pasków lub wykresów przebiegu w przebiegu. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w przebiegu w przebiegu. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Separator nazw i wartości |Ogranicznik jednoznakowy do użycia do analizowanie właściwości text na wiele wartości. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Listy** |**tytuły kolumn>** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Listy** |**Progi>** |
| Włącz progi |Wybierz to łącze, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#thresholds). |

## <a name="donut-and-list-part"></a>Pączek i część listy
Nagłówek wyświetla pojedynczą liczbę, która podsumowuje kolumnę wartości w kwerendzie dziennika. Pączek graficznie wyświetla wyniki trzech pierwszych rekordów.

![Widok pączka i listy](media/view-designer-parts/view-donut-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Użyj ikony |Wybierz to łącze, aby wyświetlić ikonę. |
| **Nagłówka** | |
| Tytuł |Tekst wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst wyświetlany pod tytułem w górnej części nagłówka. |
| **Pączek** | |
| Zapytanie |Kwerenda do uruchomienia dla pierścienia. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Pączek** |**Centrum>** |
| Tekst |Tekst wyświetlany pod wartością wewnątrz pierścienia. |
| Operacja |Operacja do wykonania na właściwość wartość, aby podsumować go jako pojedynczą wartość.<ul><li>Suma: Dodaje wartości wszystkich rekordów.</li><li>Procent: stosunek rekordów zwracanych przez wartości w **wartościach wynik używanych w operacji centrum** do całkowitych rekordów w kwerendzie.</li></ul> |
| Wartości wyników używane w operacji center |Opcjonalnie wybierz znak plus (+), aby dodać jedną lub więcej wartości. Wyniki kwerendy są ograniczone do rekordów z wartościami właściwości, które określisz. Jeśli żadne wartości nie zostaną dodane, wszystkie rekordy są uwzględniane w kwerendzie. |
| **Opcje dodatkowe** |**kolory>** |
| Kolor 1<br>Kolor 2<br>Kolor 3 |Wybierz kolor dla każdej z wartości wyświetlanych w pierścieniu. |
| **Opcje dodatkowe** |**> Zaawansowane mapowanie kolorów** |
| Wartość pola |Wpisz nazwę pola, aby wyświetlić go jako inny kolor, jeśli jest on zawarty w pierścieniu. |
| Kolor |Wybierz kolor unikatowego pola. |
| **Listy** | |
| Zapytanie |Kwerenda do uruchomienia dla listy. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| Ukryj wykres |Wybierz to łącze, aby wyłączyć wykres po prawej stronie kolumny numerycznej. |
| Włączanie wykresów przebiegu w przebiegu w przebiegu |Wybierz to łącze, aby wyświetlić wykres przebiegu w przebiegu w przebiegu zamiast paska poziomego. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Kolor |Kolor pasków lub wykresów przebiegu w przebiegu. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w przebiegu w przebiegu. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Separator nazw i wartości |Ogranicznik jednoznakowy do użycia do analizowanie właściwości text na wiele wartości. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Listy** |**tytuły kolumn>** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Listy** |**Progi>** |
| Włącz progi |Wybierz to łącze, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Dwie osie czasu i część listy
Nagłówek wyświetla wyniki dwóch zapytań dziennika w czasie jako wykresy kolumnowe, z objaśnienia, który wyświetla jedną liczbę, która podsumowuje kolumnę wartości w kwerendzie dziennika. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie.

![Dwie osie czasu i widok listy](media/view-designer-parts/view-two-timelines-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Użyj ikony |Wybierz to łącze, aby wyświetlić ikonę. |
| **Nawigacja tytuł** | |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Pierwszy<br>wykres Drugi wykres** | |
| Legenda |Tekst wyświetlany pod objaśnieniami dla pierwszej serii. |
| Kolor |Kolor używany dla kolumn w serii. |
| Zapytanie |Kwerenda do uruchomienia dla pierwszej serii. Liczba rekordów w każdym przedziale czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja do wykonania na właściwość wartość, aby podsumować go jako pojedynczą wartość dla objaśnienia.<ul><li>Suma: Suma wartości ze wszystkich rekordów.</li><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Ostatnia próbka: wartość z ostatniego interwału, która jest uwzględniona na wykresie.</li><li>Pierwsza próbka: wartość z pierwszego interwału, która jest uwzględniona na wykresie.</li><li>Count: Liczba wszystkich rekordów, które są zwracane przez kwerendę.</li></ul> |
| **Listy** | |
| Zapytanie |Kwerenda do uruchomienia dla listy. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| Ukryj wykres |Wybierz to łącze, aby wyłączyć wykres po prawej stronie kolumny numerycznej. |
| Włączanie wykresów przebiegu w przebiegu w przebiegu |Wybierz to łącze, aby wyświetlić wykres przebiegu w przebiegu w przebiegu zamiast paska poziomego. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Kolor |Kolor pasków lub wykresów przebiegu w przebiegu. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w przebiegu w przebiegu. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Listy** |**tytuły kolumn>** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Listy** |**Progi>** |
| Włącz progi |Wybierz to łącze, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#thresholds). |

## <a name="information-part"></a>Część informacyjna
Nagłówek wyświetla tekst statyczny i łącze opcjonalne. Na liście jest wyświetlany jeden lub więcej elementów o statycznym tytule i tekście.

![Widok informacji](media/view-designer-parts/view-information.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Kolor |Kolor tła nagłówka. |
| **Nagłówka** | |
| Image (Obraz) |Plik obrazu, który jest wyświetlany w nagłówku. |
| Label |Tekst wyświetlany w nagłówku. |
| **Nagłówka** |**> Link** |
| Label |Tekst łącza. |
| Url |Adres URL łącza. |
| **Elementy informacyjne** | |
| Tytuł |Tekst wyświetlany dla tytułu każdego elementu. |
| Zawartość |Tekst wyświetlany dla każdego elementu. |

## <a name="line-chart-callout-and-list-part"></a>Wykres liniowy, objaśnienie i część listy
Nagłówek wyświetla wykres liniowy z wieloma seriami z zapytania dziennika w czasie i objaśnieniem z wartością podsumowaną. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie.

![Widok wykresu liniowego, objaśnienia i listy](media/view-designer-parts/view-line-chart-callout-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Użyj ikony |Wybierz to łącze, aby wyświetlić ikonę. |
| **Nagłówka** | |
| Tytuł |Tekst wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst wyświetlany pod tytułem w górnej części nagłówka. |
| **Wykres liniowy** | |
| Zapytanie |Kwerenda do uruchomienia dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. Ta kwerenda zwykle używa słowa kluczowego *miara* do podsumowania wyników. Jeśli kwerenda używa słowa kluczowego *interwału,* oś x wykresu używa tego przedziału czasu. Jeśli kwerenda nie zawiera słowa kluczowego *interwału,* oś x używa interwałów godzinowych. |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Wykres liniowy** |**Objaśnienie>** |
| Tytuł objaśnienia |Tekst wyświetlany nad wartością objaśnienia. |
| Nazwa serii |Wartość właściwości dla serii do użycia dla wartości objaśnienia. Jeśli nie podano żadnej serii, używane są wszystkie rekordy z kwerendy. |
| Operacja |Operacja do wykonania na właściwość wartość, aby podsumować go jako pojedynczą wartość dla objaśnienia.<ul><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Count: Liczba wszystkich rekordów, które są zwracane przez kwerendę.</li><li>Ostatnia próbka: wartość z ostatniego interwału, która jest uwzględniona na wykresie.</li><li>Max: Maksymalna wartość z interwałów, które są zawarte na wykresie.</li><li>Min: Minimalna wartość z interwałów uwzględnionych na wykresie.</li><li>Suma: Suma wartości ze wszystkich rekordów.</li></ul> |
| **Wykres liniowy** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz to łącze, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości, które mają być zwracane przez kwerendę. Te informacje są używane do wyświetlania etykiet wykresu wskazujących typy wartości i opcjonalnie do konwertowania wartości. Typ *jednostki* określa kategorię jednostki i definiuje dostępne wartości typu *bieżąca jednostka.* W przypadku wybrania wartości w *polu Konwertuj*na wartość liczbową wartości liczbowe zostaną przekonwertowane z typu *Bieżąca jednostka* na *typ Konwertuj.* |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *Jednostka.* Jeśli etykieta nie zostanie określona, wyświetlany jest tylko typ *jednostki.* |
| **Listy** | |
| Zapytanie |Kwerenda do uruchomienia dla listy. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| Ukryj wykres |Wybierz to łącze, aby wyłączyć wykres po prawej stronie kolumny numerycznej. |
| Włączanie wykresów przebiegu w przebiegu w przebiegu |Wybierz to łącze, aby wyświetlić wykres przebiegu w przebiegu w przebiegu zamiast paska poziomego. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Kolor |Kolor pasków lub wykresów przebiegu w przebiegu. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w przebiegu w przebiegu. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Separator nazw i wartości |Ogranicznik jednoznakowy do użycia do analizowanie właściwości text na wiele wartości. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Listy** |**tytuły kolumn>** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Listy** |**Progi>** |
| Włącz progi |Wybierz to łącze, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#thresholds). |

## <a name="line-chart-and-list-part"></a>Wykres liniowy i część listy
Nagłówek wyświetla wykres liniowy z wieloma seriami z zapytania dziennika w czasie. Lista wyświetla dziesięć najlepszych wyników z kwerendy, z wykresem wskazującym względną wartość kolumny liczbowej lub jej zmiany w czasie.

![Wykres liniowy i widok listy](media/view-designer-parts/view-line-chart-callout-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Użyj ikony |Wybierz to łącze, aby wyświetlić ikonę. |
| **Nagłówka** | |
| Tytuł |Tekst wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst wyświetlany pod tytułem w górnej części nagłówka. |
| **Wykres liniowy** | |
| Zapytanie |Kwerenda do uruchomienia dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. Ta kwerenda zwykle używa słowa kluczowego *miara* do podsumowania wyników. Jeśli kwerenda używa słowa kluczowego *interwału,* oś x wykresu używa tego przedziału czasu. Jeśli kwerenda nie zawiera słowa kluczowego *interwału,* oś x używa interwałów godzinowych. |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Wykres liniowy** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz to łącze, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości, które mają być zwracane przez kwerendę. Te informacje są używane do wyświetlania etykiet wykresu wskazujących typy wartości i opcjonalnie do konwertowania wartości. Typ *jednostki* określa kategorię jednostki i definiuje dostępne wartości typu *bieżąca jednostka.* W przypadku wybrania wartości w *polu Konwertuj*na wartość liczbową wartości liczbowe zostaną przekonwertowane z typu *Bieżąca jednostka* na *typ Konwertuj.* |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *Jednostka.* Jeśli etykieta nie zostanie określona, wyświetlany jest tylko typ *jednostki.* |
| **Listy** | |
| Zapytanie |Kwerenda do uruchomienia dla listy. Wyświetlana jest liczba rekordów zwracanych przez kwerendę. |
| Ukryj wykres |Wybierz to łącze, aby wyłączyć wykres po prawej stronie kolumny numerycznej. |
| Włączanie wykresów przebiegu w przebiegu w przebiegu |Wybierz to łącze, aby wyświetlić wykres przebiegu w przebiegu w przebiegu zamiast paska poziomego. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Kolor |Kolor pasków lub wykresów przebiegu w przebiegu. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w przebiegu w przebiegu. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Separator nazw i wartości |Ogranicznik jednoznakowy do użycia do analizowanie właściwości text na wiele wartości. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#sparklines). |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Listy** |**tytuły kolumn>** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Listy** |**Progi>** |
| Włącz progi |Wybierz to łącze, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Układ wykresów liniowych część
Stos wykresu liniowego wyświetla trzy oddzielne wykresy liniowe, z wieloma seriami z zapytania dziennika w czasie, jak pokazano poniżej:

![Stos wykresów liniowych](media/view-designer-parts/view-stack-line-charts.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz to łącze, aby utworzyć nową grupę w widoku, zaczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| **Wykres 1<br>Wykres<br>2 Wykres 3** |**Nagłówek>** |
| Tytuł |Tekst wyświetlany u góry wykresu. |
| Podtytuł |Tekst wyświetlany pod tytułem u góry wykresu. |
| **Wykres 1<br>Wykres<br>2 Wykres 3** |**Wykres liniowy** |
| Zapytanie |Kwerenda do uruchomienia dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga jest wartością liczbową. Ta kwerenda zwykle używa słowa kluczowego *miara* do podsumowania wyników. Jeśli kwerenda używa słowa kluczowego *interwału,* oś x wykresu używa tego przedziału czasu. Jeśli kwerenda nie zawiera słowa kluczowego *interwału,* oś x używa interwałów godzinowych. |
| Nawigacja w trybie click-through | Działanie podjęte po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [Ustawienia typowe](#click-through-navigation). |
| **Wykres** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz to łącze, aby użyć skali logarytmicznej dla osi y. |
| Jednostki |Określ jednostki dla wartości, które mają być zwracane przez kwerendę. Te informacje są używane do wyświetlania etykiet wykresu wskazujących typy wartości i opcjonalnie do konwertowania wartości. Typ *jednostki* określa kategorię jednostki i definiuje dostępne wartości typu *bieżąca jednostka.* W przypadku wybrania wartości w *polu Konwertuj*na wartość liczbową wartości liczbowe zostaną przekonwertowane z typu *Bieżąca jednostka* na *typ Konwertuj.* |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *Jednostka.* Jeśli etykieta nie zostanie określona, wyświetlany jest tylko typ *jednostki.* |

## <a name="common-settings"></a>Typowe ustawienia
W poniższych sekcjach opisano ustawienia, które są wspólne dla kilku części wizualizacji.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Separator nazw i wartości
Separator nazw i wartości jest jednoznakowym ogranicznikiem używanym do analizowanie właściwości tekstowej z kwerendy listy na wiele wartości. Jeśli określisz ogranicznik, można podać nazwy dla każdego pola, oddzielone tym samym ogranicznikiem w polu **Nazwa.**

Rozważmy na przykład właściwość o nazwie *Lokalizacja,* która zawierała wartości, takie jak *Redmond-Building 41* i *Bellevue-Building 12*. Można określić myślnik (-) dla separatora nazwy i wartości oraz *city-building* dla nazwy. To podejście analizuje każdą wartość na dwie właściwości o nazwie *Miasto* i *Budynek*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Nawigacja typu "kliknięcie"
Nawigacja klikania określa, jakie działania zostaną podjęte po kliknięciu nagłówka lub elementu listy w widoku.  Spowoduje to otwarcie kwerendy w [usłudze Log Analytics](../../azure-monitor/log-query/portals.md) lub uruchomienie innego widoku.

W poniższej tabeli opisano ustawienia nawigacji klikania.

| Ustawienie           | Opis |
|:--|:--|
| Wyszukiwanie w dzienniku (auto) | Kwerenda dziennika do uruchomienia po wybraniu elementu nagłówka.  Jest to ta sama kwerenda dziennika, na podstawie którego znajduje się element.
| Przeszukiwanie dzienników        | Kwerenda dziennika do uruchomienia po wybraniu elementu na liście.  Wpisz kwerendę w polu **Kwerenda nawigacji.**   Użyj *{wybranego elementu},* aby uwzględnić składnię wybranego elementu.  Jeśli na przykład kwerenda ma kolumnę o nazwie *Komputer,* a kwerendą nawigaccową jest *{zaznaczony element},* kwerenda taka jak *Komputer="Mój komputer"* jest uruchamiana po wybraniu komputera. Jeśli kwerendą nawigaccjowa jest *Type=Event {selected item}*, zostanie uruchomione zapytanie *Type=Event Computer="MyComputer".* |
| Widok              | Służy do otwierania po wybraniu elementu nagłówka lub elementu na liście.  Wybierz nazwę widoku w obszarze roboczym w polu **Nazwa widoku.** |



### <a name="sparklines"></a><a name="sparklines"></a>Sparklines
Wykres przebiegu w czasie to mały wykres liniowy, który z czasem ilustruje wartość wpisu listy. W przypadku części wizualizacji z listą można wybrać, czy ma być wyświetlany poziomy pasek, który wskazuje względną wartość kolumny liczbowej, czy wykres przebiegu w czasie, który wskazuje jego wartość w czasie.

W poniższej tabeli opisano ustawienia wykresów przebiegu w przebiegu w przebiegu:

| Ustawienie | Opis |
|:--- |:--- |
| Włączanie wykresów przebiegu w przebiegu w przebiegu |Wybierz to łącze, aby wyświetlić wykres przebiegu w przebiegu w przebiegu zamiast paska poziomego. |
| Operacja |Jeśli wykresy przebiegu w przebiegu są włączone, jest to operacja do wykonania na każdej właściwości na liście, aby obliczyć wartości dla wykresu przebiegu w przebiegu w przebiegu.<ul><li>Ostatnia próbka: ostatnia wartość dla serii w przedziale czasu.</li><li>Max: Maksymalna wartość serii w przedziale czasu.</li><li>Min: Minimalna wartość serii w przedziale czasu.</li><li>Suma: Suma wartości serii w przedziale czasu.</li><li>Podsumowanie: używa tego `measure` samego polecenia co kwerenda w nagłówku.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Progi
Za pomocą progów można wyświetlić kolorową ikonę obok każdego elementu na liście. Progi zapewniają szybki wizualny wskaźnik elementów, które przekraczają określoną wartość lub mieszczą się w określonym zakresie. Na przykład można wyświetlić zieloną ikonę dla elementów o dopuszczalnej wartości, żółty, jeśli wartość znajduje się w zakresie wskazującym ostrzeżenie, a czerwony, jeśli przekracza wartość błędu.

Po włączeniu progów dla części należy określić jeden lub więcej progów. Jeśli wartość towaru jest większa niż wartość progowa i niższa niż następna wartość progowa, używany jest kolor dla tej wartości. Jeśli element jest większy niż najwyższa wartość progowa, używany jest inny kolor. 

Każdy zestaw progów ma jeden próg z wartością **Default**. Jest to kolor, który jest ustawiony, jeśli nie zostaną przekroczone żadne inne wartości. Progi można dodawać lub usuwać, wybierając przycisk **Dodaj** (+) lub **Usuń** (x).

W poniższej tabeli opisano ustawienia progów:

| Ustawienie | Opis |
|:--- |:--- |
| Włącz progi |Wybierz to łącze, aby wyświetlić ikonę koloru po lewej stronie każdej wartości. Ikona wskazuje kondycję wartości względem określonych progów. |
| Nazwa |Nazwa wartości progowej. |
| Próg |Wartość progu. Kolor kondycji dla każdego elementu listy jest ustawiony na kolor najwyższej wartości progowej, która została przekroczona przez wartość elementu. Jeśli nie zostaną przekroczone żadne wartości progowe, używany jest kolor domyślny. |
| Kolor |Kolor wskazujący wartość progową. |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do obsługi zapytań w częściach wizualizacji.
