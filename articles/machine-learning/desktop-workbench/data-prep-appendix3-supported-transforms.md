---
title: "Użyć transformacji danych w celu przygotowania danych w usłudze Azure Machine Learning | Dokumentacja firmy Microsoft"
description: "Ten artykuł zawiera pełną listę przekształceń dostępne w celu przygotowania danych usługi Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9cd6e6cdf8d8dc2b1872d6f7b76821122fd427fa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Przekształcenia danych użycia w celu przygotowania danych w usłudze Azure Machine Learning

A *przekształcenie* w usłudze Azure Machine Learning pobiera dane w danym formacie, wykonuje operacje na danych (np. zmiana typu danych) i następnie tworzy danych do nowego formatu. Przekształcanie poszczególnych ma swój własny interfejs i zachowania. Przez łańcucha kilka przekształceń ze sobą za pośrednictwem czynności przepływu danych, można wykonać przekształcenia złożone i powtarzalne na podstawie danych. Jest to podstawowe funkcje przygotowywania danych.

Poniżej przedstawiono dostępne w usłudze Azure Machine Learning przekształcenia. 

## <a name="column-selection"></a>Wybór kolumny 
Wiele przekształcenia na tej liście działają w jednej kolumnie lub wiele. Aby wybrać wiele kolumn, użyj klawisza Ctrl. Aby wybrać zakres kolumn, użyj klawisza Shift.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Przekształca z poziomu menu głównego lub nagłówek siatki 
Przekształca dostępu z opcją transformacje w menu głównym. Transformacje można również wybrać, klikając prawym przyciskiem myszy nazwę kolumny w siatce danych. Jeśli wybrano wiele kolumn, klikając ich prawym przyciskiem myszy zawiera menu transformacji.

W menu kontekstowym wyświetlane są tylko prawidłowe przekształceń dla typu danych wybranego. Menu głównego oferuje wszystkie transformacje, ale powoduje wyłączenie tych, które są nieodpowiednie dla wybranych kolumn.

Mały podzbiór kontekstowe transformacji jest dostępna przez kliknięcie prawym przyciskiem myszy komórki. Widoczne mogą podlegać kopiowaniu, Zamień i filtrowania. Są dane typu obsługujący, więc opcje dla numeru kolumny są inne niż dla kolumny typu string.

## <a name="derive-column-by-example"></a>Kolumna przykładzie pochodzi
Użyj tej transformacji, aby utworzyć nową kolumnę jako pochodną co najmniej jeden z istniejących kolumn. Transformacja analizuje kolumny wejściowe (wybrane) i podanym przykładzie, a następnie określa żądanego wyniku w nowej kolumnie. 

Aby użyć tej transformacji, wybierz co najmniej jedną kolumnę. Dodaj nową kolumnę pochodnych (pusty) przykładzie. Wpisz na przykład mają być wyświetlane w kolumnie pochodnych (przy założeniu, że pochodzi od innych kolumn), a "W przykładzie" technologii próbuje Wypełnij wszystkie komórki w kolumnie. 

Przykłady skomplikowane konieczne może być zapewniają więcej niż jednym z przykładów. Aby to zrobić, wybierz inną komórkę, a następnie wpisz inny przykład.

Technologia "Według przykładu" używa wybranych kolumn próbę ustalić znaczenie przykładem. Jeśli nie wybrano kolumn po wywołaniu tej transformacji, używane są wszystkie komórki dla bieżącego wiersza. Wybieranie wymaganych kolumn prowadzi do bardziej dokładne wyniki.

Można wybrać kolumny przed wywołaniem transformacji. Po otwarciu edytora przekształcenia pola wyboru w górnej części każdej kolumny wskazują, jakie kolumny są zaznaczone w danych wejściowych. Można dodać lub usunąć kolumny z zaznaczenia za pomocą pola wyboru w nagłówkach kolumn.

Aby uzyskać bardziej szczegółowy opis **pochodnych kolumny przykładzie** przekształcenia wraz z większej liczby próbek, zobacz [pochodzi kolumny przez odwołanie przykład](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Podział kolumny według przykładu
Tej transformacji przyjmuje istniejącej kolumny i przy użyciu aparatu "Według przykładu", próbuje podziału kolumny w *n* innych kolumn. Podziel automatycznie można uruchamiać na kolejnych wygenerowanego kolumn.

Aby uzyskać bardziej szczegółowy opis **podzielona kolumna przykładzie** przekształcenia wraz z większej liczby próbek, zobacz [kolumny podzielone przez odwołanie przykład](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Rozwijanie danych JSON

Tej transformacji umożliwia dodanie wiele kolumn, rozwijając kolumny za pomocą prawidłowego tekstu JSON.

Aby uzyskać bardziej szczegółowy opis **rozwiń JSON** przekształcenia wraz z większej liczby próbek, zobacz [odwołanie rozwiń JSON](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Łączenie kolumn w przykładzie

Tej transformacji dodaje nową kolumnę przez połączenie wartości z wielu kolumn. 

Aby uzyskać bardziej szczegółowy opis **łączenie kolumn, na przykład** przekształcenia wraz z większej liczby próbek, zobacz [łączenie kolumn przez odwołanie przykład](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplicate Column
Tej transformacji zostanie utworzona kopia dokładnie jeden lub więcej wybranych kolumn oraz zapewnia każda nowa nazwa pochodzi od oryginalnej nazwy kolumny.

## <a name="text-clustering"></a>Tekst klastra 
Tej transformacji umożliwia niespójne wartości, które powinny być takie same i grupować je razem.  

Z tej transformacji wartości w jednej kolumnie są przeanalizowane pod kątem podobieństwa i grupować w klastrach. Dla każdego klastra jest canonical wartość, która jest wartość, która zastępuje wszystkie wystąpienia w klastrze oraz wszystkie wystąpienia wartości. Zakończenie klastrów można je usunąć i edytować wartości kanonicznej. Można usunąć wystąpienia z danego klastra. Można zmienić podobieństwa filtr próg wynik, który został użyty do grupy wystąpień w klastrze.

Domyślnie tej transformacji zastępuje wszystkie wartości wystąpienia klastra wartości kanonicznej dla tego klastra, tworzenie nowej kolumny zawiera nowe wartości. Wynik podobieństwa dla każdego wystąpienia można również dodać do nowej kolumny, (tj. można go nazwać) do użycia w dalszej części przepływu danych.

## <a name="replace-values"></a>Zastąp wartości
Umożliwia zastępowanie jednego ciągu innym tej transformacji. Ciąg źródłowy może być ciąg częściowe lub pełne komórki, a zastąpienie można zastosować do jednej kolumny lub wiele. Ciąg wyszukiwania obsługuje wyszukiwanie znaki specjalne także jak w przypadku regularnego znaków. 

## <a name="replace-na-values"></a>Zamień NA wartości
Za pomocą tej transformacji można zastąpić różne rodzaje NA (Brak, NA, null, NaN, itp.), lub zastąpić puste ciągi pojedynczą wartość, aby były zgodne. Tej transformacji obsługuje jedną lub wiele kolumn, a jest to wymienione tylko po wybraniu kolumny. Nie jest dostępny w menu głównym przekształcenie gdy nie wybrano kolumn.

## <a name="replace-missing-values"></a>Zastąp brakujące wartości
Tej transformacji zamienia jedną wartość brakujące dane i obsługuje jednego lub wielu kolumn. Tej transformacji znajduje się tylko w przypadku wybrania kolumny. Nie jest dostępny w menu głównym przekształcenie gdy nie wybrano kolumn.

## <a name="replace-error-values"></a>Zastąp wartości błędów
Tej transformacji zastępuje błędy pojedynczą wartość i obsługuje jednego lub wielu kolumn. Tej transformacji znajduje się tylko w przypadku wybrania kolumny. Nie jest dostępny w menu głównym przekształcenie gdy nie wybrano kolumn.

## <a name="trim-string"></a>TRIM ciąg
Tej transformacji usuwa początkowe i końcowe znaki "odstępy" (w tym spacje, tabulatory, itp.) z co najmniej jedną kolumnę.

## <a name="adjust-precision"></a>Dostosuj dokładności
Tej transformacji Ustawia liczbę miejsc dziesiętnych kolumny liczbowej.

## <a name="rename-column"></a>Zmień nazwę kolumny
Tej transformacji zmienia nazwę zaznaczonej kolumny. Można także wywoływać jego wbudowany w nagłówku kolumny, klikając nazwę kolumny.

## <a name="remove-column"></a>Usuń kolumnę
Tej transformacji Usuwa zaznaczone kolumny i działa w jednej kolumnie lub wiele. 

## <a name="keep-column"></a>Zachowaj kolumny
Tej transformacji temu wybranych kolumn i działa w jednej kolumnie lub wiele.

## <a name="handle-path-column"></a>Obsługa kolumny
Podczas importowania pliku, ścieżka kolumny jest automatycznie dodawany do zestawu danych przez **Dodaj źródło danych** funkcji. Zawiera pełną nazwę pliku, który wchodzi w skład ścieżkę do zestawu danych. Tej transformacji dodaje lub usuwa dodatkowe kolumny z zestawu danych.

## <a name="convert-field-type-to-numeric"></a>Konwertuj typ pola na liczbowe
Tej transformacji zmienia typ kolumny liczbowe. Można określić separatora dla danych nie jest liczbą całkowitą. Domyślnie tej transformacji nie Monituj separatora, należy więc **Edytuj** element menu do wywołania edytora. Tej transformacji działa w przypadku zaznaczenia jednej kolumny lub wiele.

## <a name="convert-field-type-to-date"></a>Konwertowanie typu pola datę
Tej transformacji zmienia typ kolumny na typ date. Domyślny format daty/godziny jest używany, ale może być zastąpiona przy użyciu `strftime` dyrektywy. Można również dołączy wartości typu time ze stałym daty.

Domyślnie tej transformacji nie Monituj o formacie, należy więc **Edytuj** element menu na etapie wynikowe do wywołania edytora. Tej transformacji działa w przypadku zaznaczenia jednej kolumny lub wiele.

Tylko daty między 9-22 — 1677 i 4-11-2262 można przekonwertować na typ date.

## <a name="convert-field-type-to-boolean"></a>Konwertuj typ pola na wartość logiczną
Tej transformacji zmiany kolumny typu PRAWDA/FAŁSZ. Obsługuje ona mapowanie wielu wartości na wartość PRAWDA lub FAŁSZ, a można edytować te mapowania. Obsługuje ona również stałą, do którego można mapować wartości, które nie istnieją w tabeli mapowania PRAWDA/FAŁSZ. Tej transformacji działa w przypadku zaznaczenia jednej kolumny lub wiele.

## <a name="convert-field-type-to-string"></a>Konwertuj typ pola na ciąg
Tej transformacji zmienia typ kolumny do ciągu i działa w jednej kolumnie lub wiele.

## <a name="convert-unix-timestamp-to-datetime"></a>Przekonwertować na typ DateTime sygnatury czasowej systemu Unix
Tej transformacji rozumie format sygnatury czasowej systemu Unix, nawet jeśli jest reprezentowany jako ciąg w danych. Sygnatura czasowa konwertuje poprawnie z typem daty w przygotowaniu danych.

## <a name="filter"></a>Filtr
Tej transformacji obsługuje filtrowanie wierszy na podstawie wartości w jednej lub wielu kolumn. Warunki filtru zależą od typu danych kolumn, więc kolumnach typu ciąg można filtrować według "zawiera" lub "nie zawiera." Kolumny liczbowe można filtrować według "większe niż" lub "poniżej" warunki.

Gdy **filtru** transformacji jest wywoływana z poziomu menu głównego, lub z prawym przyciskiem myszy nagłówek kolumny, jest dostępna opcja do rozwidlania wierszy niepowodzenie do innego przepływu danych. Przepływ danych głównych będzie kontynuowane przy użyciu filtrowane **w** wierszy i nowy przepływ danych utworzono, która zawiera wszystkie wiersze, które zostały przefiltrowane **limit**.

## <a name="use-first-row-as-headers"></a>Użyj pierwszego wiersza jako nagłówków
Tej transformacji zamienia pierwszy wiersz z zestawu danych jako nazwy kolumny. Jeśli niektóre kolumny nie ma danych w pierwszym wierszu, nazwę został wygenerowany automatycznie. Przy użyciu tej transformacji oznacza, że import danych zaczyna się w wierszu 2 co najmniej. W zależności od **wierszy pomijania** ustawienie importu można uruchomić nawet dalsze w dół w zestawie danych. Można go usunąć podwyższenia poziomu i używanie generowanych automatycznie nazw tylko.

## <a name="join"></a>Join
Tej transformacji jest używany do przyłączenia dwóch przepływów danych ze sobą. Można wybrać, których dane wyjściowe mają być skutkiem: pomyślnie wiersze z sprzężenia, "po lewej stronie" Niepowodzenie wiersze z sprzężenie lub "prawa" Niepowodzenie wiersze z sprzężenia.

**Sprzężenia** przekształcenia zaczyna się od przepływu danych jednego i zaznacza ten przepływ danych po jednej stronie sprzężenia. Go następnie wyświetla monit o wybranie innego przepływu danych dla strony sprzężenia. Po wybraniu dwóch przepływów Przekształcenie wymaga pojedynczej kolumny z każdej strony sprzężenia, należy wybrać do połączenia. Sprzężenie wymaga więcej niż jedną kolumnę, utworzyć pochodnej kolumny przed rozpoczęciem przekształcenia można utworzyć nowy, połączonych kolumny do użycia tylko w celu utworzenia sprzężenia. Transformacja próbuje Sugeruj klucze sprzężenia i, jeśli to możliwe, automatycznie wygenerować kolumny pochodnej.

Po ukończeniu sprzężenia, widok diagramu Sankey sprzężenia jest widoczne. Szerokość linii względem siebie reprezentuje liczbę wierszy, Nawigacja po część przepływu sprzężenia. Za pomocą panelu po prawej stronie, możesz wybrać pomyślne wierszy, niepowodzeniem wierszy lub do prawej, niepowodzeniem wierszy wyłącznie po lewej stronie. Można również wybrać jeden oddział.

## <a name="append-rows"></a>Dołącz wiersze
Tej transformacji dołącza dane z innego przepływu danych do bieżącej. Mapowania kolumn za pomocą pozycji, aby dodać nowe wiersze na końcu.

## <a name="append-columns"></a>Dołącz kolumny
Tej transformacji dołącza nowe kolumny z innej przepływ danych do bieżącej. Dodaje nowe kolumny do prawej. Nie próbować wiersz w górę danych wierszy.

## <a name="summarize"></a>Podsumowanie
Tej transformacji oblicza agregacji dla kombinacji unikatowe wartości w co najmniej jeden z zaznaczonych kolumn. 

Agregacje, obsługiwane są: liczba, SUM, MIN, MAX, średnia, odchylenie i odchylenie standardowe. Lista wartości zagregowanych dla podanej kolumny są filtrowane do agregacji, które są stosowane do typu danych. Wartości zagregowane, które nie są stosowane są wyłączone. Na przykład nie jest możliwe do obliczenia średniej kolumny typu string, ale istnieje możliwość obliczeniowe MIN i MAX.

Jeśli Edytor jest dostępny, przeciągnij z nagłówka kolumny do panelu u góry po lewej, gdzie są wyświetlane kolumny, które mają być łączone. Ten panel to hierarchiczna, tak zrobisz zagnieżdżonych wartości zagregowanych. Panel Edytor u góry po prawej służy do wybierania który agregacji do zastosowania do kolumny. Pojedyncza kolumna może agregować jeden lub więcej razy. Po co najmniej jedną wartość zagregowaną została wybrana, siatki w prawym dolnym rogu Wyświetla podgląd danych w postaci agregacji. 

Tej transformacji jest odpowiednikiem `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Usuń duplikaty
Tej transformacji usuwa cały wiersz, jeśli istnieją zduplikowane wartości w co najmniej jeden z zaznaczonych kolumn. W przypadku kolumn nie są wybrane, wiersze tylko usunięte są te, gdy wszystkie wartości w kolumnach są takie same.

## <a name="sort"></a>Sortuj
Dane są sortowane w tej transformacji. Sortowanie może odbywać się przez pojedynczą kolumnę lub wiele, a każda kolumna można sortować (ustawienie domyślne) rosnąco lub Malejąco (które można zmienić w edytorze).

Tej transformacji jest odpowiednikiem `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Przekształcenia danych wyjściowych
Dane wyjściowe następujące przekształcenia. Może mieć wiele bloków zapisu w jednym przepływie zapisać danych w różnych punktach.

### <a name="write-to-csv"></a>Zapis do pliku CSV
Tej transformacji zapisuje dane w postaci CSV z bieżącego punktu w przepływie danych. Steruje lokalizacji (lokalnego lub zdalnego) oraz różne ustawienia wokół pliku.

### <a name="write-to-parquet"></a>Zapisać Parquet
Tej transformacji zapisuje dane w postaci Parquet od bieżącego punktu w przepływie danych. Steruje lokalizacji (lokalnego lub zdalnego) oraz różne ustawienia wokół pliku.

## <a name="script-based-transforms"></a>Transformacje opartych na skryptach
Następujące przekształcenia użyć skryptu (Python) do wykonywania funkcji, które nie istnieje w produkcie podstawowym. 

>[!NOTE]
>Przed użyciem dowolnej z tych transformacje odczytu [rozszerzalność przy użyciu języka Python](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Dodaj kolumnę (skrypt)
Tej transformacji dodaje kolumnę z danymi przy użyciu wyrażenia języka Python.
Aby uzyskać więcej informacji, zobacz [Python przykładowy kod wyprowadzanie nowe kolumny](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtru zaawansowanego (skrypt)
Użyj tej transformacji do zapisania Python filtru poziomu wiersza.
Aby uzyskać więcej informacji, zobacz [przykładowe wyrażenia filtru](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Przekształć przepływu danych (skrypt)
Tej transformacji dotyczy Python cały zestaw danych.
Aby uzyskać więcej informacji, zobacz [przykład przekształcania danych przepływ przekształcenia](data-prep-appendix7-sample-transform-data-flow-python.md).

Tej transformacji również może dotyczyć Python partycji danych.
Aby uzyskać więcej informacji, zobacz [przykład przekształcania danych przepływ przekształcenia](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Pisanie przepływu danych (skrypt)
Tej transformacji używa języka Python, aby zapisać cały zestaw danych.
Aby uzyskać więcej informacji, zobacz [Python próbki dla wynikających z nowymi kolumnami](data-prep-appendix9-sample-destination-connections-python.md).



