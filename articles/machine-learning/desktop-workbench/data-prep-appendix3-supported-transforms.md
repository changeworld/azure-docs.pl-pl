---
title: Używanie przekształceń danych podczas przygotowywania danych w usłudze Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera pełną listę przekształceń dostępne podczas przygotowywania danych usługi Azure Machine Learning.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: c47d9bc72ad1d197b5030076456f9dc9efc422bc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644997"
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Używanie przekształceń danych podczas przygotowywania danych w usłudze Azure Machine Learning

A *Przekształcanie* w usłudze Azure Machine Learning wykorzystuje dane w danym formacie, wykonuje operacje na danych (np. zmiana typu danych) i następnie generuje dane do nowego formatu. Każdy przekształcenia ma swój własny interfejs i zachowanie. Przez łączenie łańcuchowe wielu przekształceń za pomocą kroków przepływu danych, można wykonywać złożone i powtarzalne przekształcenia na podstawie posiadanych danych. Jest to podstawowe funkcje przygotowywania danych.

Dostępne są następujące przekształcenia, które są dostępne w usłudze Azure Machine Learning. 

## <a name="column-selection"></a>Wybór kolumn 
Wiele przekształceń na tej liście działać względem pojedynczej kolumny lub wiele. Aby wybrać wiele kolumn, użyj klawisza Ctrl. Aby wybrać zakres kolumn, przy użyciu klawisza Shift.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Przekształca z menu głównego lub nagłówek siatki 
Przekształca dostępu z poziomu opcji przekształceń, w menu głównym. Przekształcenia można również wybrać, klikając prawym przyciskiem myszy nazwę kolumny w siatce danych. Jeśli wybrano wiele kolumn, kliknij prawym przyciskiem myszy dowolny z nich zawiera menu przekształcenia.

Menu kontekstowe wyświetla tylko prawidłowe przekształceń dla wybranego typu danych. Menu główne oferuje wszystkie przekształcenia, ale powoduje wyłączenie tych, które nie są istotne do wybranych kolumn.

Mały podzbiór kontekstowych transformacji jest dostępna przez kliknięcie prawym przyciskiem myszy komórkę. Te przekształcenia są Kopiuj i Zamień, okna i filtrowania. Są to dane typu obsługujące dzięki opcji dla numeru kolumny dla kolumny typu string różnią się od.

## <a name="derive-column-by-example"></a>Tworzenie kolumny pochodnej według przykładu
Użyj tej transformacji, aby utworzyć nową kolumnę jako utworów zależnych w przynajmniej jednej z istniejących kolumn. Transformacja wygląda w danych wejściowych (wybrane) kolumny i przykład, biorąc pod uwagę, a następnie Określa żądaną produktu wyjściowego w nowej kolumnie. 

Aby użyć tej transformacji, wybierz co najmniej jedną kolumnę. Dodaj nowy (pusty) kolumny pochodnej według przykładu. Wpisz na przykład mają być wyświetlane w kolumnie pochodnych (przy założeniu, że jest on uzyskiwany z innych kolumn) i "W przykładzie" technologii polegające na próbie wypełnienia w innych komórek w kolumnie. 

Przykłady skomplikowane może być konieczne zapewniają więcej niż jednym z przykładów. Aby to zrobić, wybierz inną komórkę, a następnie wpisz inny przykład.

Technologia "Według przykładu" używa wybranych kolumn próbuje określić znaczenia przykładem. Jeśli nie wybrano kolumn po wywołaniu tej transformacji, używane są wszystkie komórki dla bieżącego wiersza. Wybieranie wymaganych kolumn prowadzi do bardziej precyzyjne wyniki.

Przed wywołaniem na przekształcenie, które można wybrać kolumny. Po otwarciu edytora przekształcenie pola wyboru w górnej części każda kolumna wskazują, jakie kolumny są zaznaczone jako dane wejściowe. Można dodać lub usunąć kolumny z zaznaczenia za pomocą pola wyboru w nagłówkach kolumn.

Aby uzyskać bardziej szczegółowy opis **Tworzenie kolumn pochodnych według przykładu** przekształcenia oraz więcej przykładów, zobacz [Utwórz kolumnę pochodną według przykładu odwołanie](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Podziel kolumnę według przykładu
Przekształcenia przyjmuje istniejącą kolumnę, a za pomocą aparatu "Według przykładu", próbuje podziału kolumny do *n* innych kolumn. Podziel automatycznego można uruchamiać na kolejne wygenerowanych kolumn.

Aby uzyskać bardziej szczegółowy opis **Podziel kolumnę według przykładu** przekształcenia oraz więcej przykładów, zobacz [Podziel kolumnę według przykładu odwołanie](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Rozwijanie danych JSON

Ta transformacja umożliwia dodawanie wielu kolumn, rozwijając kolumnę prawidłowy tekst JSON.

Aby uzyskać bardziej szczegółowy opis **rozwiń JSON** przekształcenia oraz więcej przykładów, zobacz [dokumentacji rozwiń JSON](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Łączenie kolumn według przykładu

Ta transformacja dodaje nową kolumnę, łącząc wartości z wielu kolumn. 

Aby uzyskać bardziej szczegółowy opis **łączenie kolumn według przykładu** przekształcenia oraz więcej przykładów, zobacz [łączenie kolumn według przykładu odwołania](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplikuj kolumnę
Ta transformacja sprawia, że dokładna kopia składa się co najmniej jeden z wybranych kolumn i zapewnia każdej nowej nazwy pochodzące z oryginalnej nazwy kolumny.

## <a name="text-clustering"></a>Tekst klastrowania 
Ta transformacja jest projektowane w celu niezgodne wartości, które powinny być takie same i grupować je razem.  

Ta transformacja wartości w jednej kolumnie są analizowane pod kątem podobieństwa i pogrupowane w klastrach. Dla każdego klastra jest canonical wartość, która jest wartością, która zastępuje wszystkie wystąpienia w klastrze oraz wszystkie wartości wystąpienia. Pełne klastrów można je usunąć i edytowane kanoniczną wartość. Można usunąć wystąpienia z danego klastra. Można zmienić filtr próg wynik podobieństwa, który został użyty do grupy wystąpień w klastrze.

Domyślnie ta transformacja zamienia wszystkie wartości wystąpienia klastra canonical wartość dla tego klastra, tworząc nową kolumnę w celu uwzględnienia nowych wartości. Wynik podobieństwa dla każdego wystąpienia można również dodać do nowej kolumny, (które mogą mieć nazwę), do użytku w dalszej części przepływu danych.

## <a name="replace-values"></a>Zastąp wartości
Użyj tej transformacji, aby zastąpić inną jeden ciąg. Ciąg źródłowy może być ciąg częściowego lub pełnego komórki, a zastąpienie można zastosować do jednej kolumny lub wiele. Ciąg wyszukiwania obsługuje wyszukiwanie dla znaków specjalnych oraz jak zwykłe znaki. 

## <a name="replace-na-values"></a>Zamień NA wartości
Użyj tej transformacji, aby zamienić różne rodzaje NA (n/d, nazwa, wartość null, NaN, itp.), lub aby zastąpić puste ciągi pojedynczą wartość, aby były zgodne. Ta transformacja obsługuje jeden lub wiele kolumn, a jest to wyświetlane tylko po wybraniu kolumny. Nie jest obecny w menu głównym przekształceniem, gdy nie wybrano kolumn.

## <a name="replace-missing-values"></a>Zastąp wartości Brak
Przekształcenia zamienia jedną wartość brakujące dane i obsługuje jeden lub wiele kolumn. Ta transformacja znajduje się tylko po wybraniu kolumny. Nie jest obecny w menu głównym przekształceniem, gdy nie wybrano kolumn.

## <a name="replace-error-values"></a>Zastępować wartości błędów
Przekształcenia Zamienia błędy pojedynczą wartość i obsługuje jeden lub wiele kolumn. Ta transformacja znajduje się tylko po wybraniu kolumny. Nie jest obecny w menu głównym przekształceniem, gdy nie wybrano kolumn.

## <a name="trim-string"></a>TRIM ciąg
Ta transformacja eliminuje początkowe i końcowe "" odstęp (w tym miejsca do magazynowania, karty itp.) z co najmniej jedną kolumnę.

## <a name="adjust-precision"></a>Dostosuj dokładności
Ta transformacja Ustawia liczbę miejsc dziesiętnych kolumny liczbowej.

## <a name="rename-column"></a>Zmień nazwę kolumny
Ta Transformacja zmienia nazwę wybranej kolumny. Można także wywoływać je bezpośrednio w nagłówku kolumny, klikając nazwę kolumny.

## <a name="remove-column"></a>Usuń kolumnę
Ta transformacja eliminuje wybranych kolumn i działa na pojedynczej kolumny lub wiele. 

## <a name="keep-column"></a>Zachowaj kolumnę
Ta transformacja przechowuje tylko zaznaczonych kolumn i działa na pojedynczej kolumny lub wiele.

## <a name="handle-path-column"></a>Kolumna ścieżki uchwytu
Podczas importowania pliku kolumny ścieżki jest automatycznie dodawany do zestawu danych przez **Dodaj źródło danych** funkcji. Zawiera ona w pełni kwalifikowana nazwa pliku, który wchodzi w skład ścieżkę do zestawu danych. Ta transformacja dodaje lub usuwa dodatkowe kolumny z zestawu danych.

## <a name="convert-field-type-to-numeric"></a>Konwertuj typ pola na liczbowy
Ta transformacja zmieni typ kolumny na liczbowy. Można określić separator danych nie jest liczbą całkowitą. Domyślnie ta transformacja nie Monituj o separatora, a więc **Edytuj** element menu, aby wywołać edytora. Ta transformacja działa na pojedynczej kolumny lub wiele.

## <a name="convert-field-type-to-date"></a>Konwertuj typ pola daty
Ta transformacja ustawia typ kolumny na datę. Domyślny format daty/godziny jest używany, ale może być zastąpiona przy użyciu `strftime` dyrektywy. Można również dołączana wartości typu time ze stałej dacie.

Domyślnie ta transformacja nie Monituj o formacie, a więc **Edytuj** element menu wynikowe krok, aby wywołać edytora. Ta transformacja działa na pojedynczej kolumny lub wiele.

Tylko daty między 9-22 — 1677 i 4-11-2262 można przekonwertować na typ date.

## <a name="convert-field-type-to-boolean"></a>Konwertuj typ pola na wartość logiczną
Ta transformacja zmiany typu kolumny PRAWDA/FAŁSZ. Obsługuje mapowanie wiele wartości na wartość PRAWDA lub FAŁSZ, a te mapowania można edytować. Obsługuje ona również stałą, do którego można mapować wartości, które nie istnieją w tabeli mapowania PRAWDA/FAŁSZ. Ta transformacja działa na pojedynczej kolumny lub wiele.

## <a name="convert-field-type-to-string"></a>Konwertuj typ pola na ciąg
Przekształcenia zmienia typ kolumny na ciąg i jego działanie na pojedynczej kolumny lub wiele.

## <a name="convert-unix-timestamp-to-datetime"></a>Konwertuj sygnatura czasowa systemu Unix do daty/godziny
Ta transformacja rozumie z formatem znacznika czasu systemu Unix, nawet jeśli jest przedstawiana jako ciąg w danych. Sygnatura czasowa konwertuje poprawnie na typ Data, w ramach przygotowania danych.

## <a name="filter"></a>Filtr
Ta transformacja obsługuje filtrowanie wierszy na podstawie wartości w jednej lub wielu kolumn. Warunki filtru zależą od typu danych poszczególnych kolumn, co pozwala na filtrowanie kolumn ciąg "contains" lub "nie zawiera." Kolumny liczbowe można filtrować według "większe niż" lub "poniżej" warunki.

Gdy **filtru** transformacji jest wywoływana z poziomu menu głównego, lub z prawym przyciskiem myszy nagłówek kolumny, jest dostępna opcja rozwidlić wierszy przechodzenia do innego przepływu danych. Przepływ danych głównych będzie kontynuowane z użyciem filtrowane **w** wierszy i nowego przepływu danych jest tworzony zawierającą wszystkie wiersze, które zostały przefiltrowane **się**.

## <a name="use-first-row-as-headers"></a>Użyj pierwszego wiersza jako nagłówków
Ta transformacja zamienia pierwszy wiersz z zestawu danych jako nazw kolumn. Jeśli niektóre kolumny nie ma danych w pierwszym wierszu, nazwa jest generowane automatycznie. Przy użyciu tej transformacji oznacza, że importowanie danych rozpoczyna się od wiersza 2 przy najbliższej sposobności. W zależności od **pomijania wierszy** ustawienia importu można uruchomić jeszcze dalej w dół w zestawie danych. Umożliwia także ją usunąć podwyższenia poziomu oraz używanie generowanych automatycznie tylko nazwy.

## <a name="join"></a>Join
Ta transformacja jest używany do łączenia dwóch przepływów danych ze sobą. Można wybrać, które dane wyjściowe sprzężenia, może być skutkiem: pomyślne wiersze z sprzężenia "po lewej stronie" kończy się niepowodzeniem wiersze z sprzężenia lub "prawo" kończy się niepowodzeniem wiersze z sprzężenia.

**Sprzężenia** transformacja rozpoczyna się od przepływu danych jednego i wybiera ten przepływ danych po jednej stronie sprzężenia. Go następnie monit o wybranie innego przepływ danych dla strony sprzężenia. Po wybraniu dwóch przepływów Przekształcenie wymaga pojedynczej kolumny na każdej stronie sprzężenia do wybrania odbędzie się. Jeśli sprzężenia potrzebuje więcej niż jedną kolumnę, należy utworzyć kolumny pochodnej, przed rozpoczęciem transformacji, aby utworzyć nową, połączonych kolumnę ma być używany tylko w przypadku sprzężenia. Transformacja próbuje zasugerować klucze sprzężenia i, jeśli to możliwe, automatyczne generowanie kolumn pochodnych.

Po zakończeniu sprzężenia, widok diagramu Sankeya sprzężenia jest widoczne. Szerokość linii względem siebie reprezentuje liczbę wierszy przechodzenia przez tę część przepływu sprzężenia. Za pomocą panelu po prawej stronie, można wybrać pomyślne wiersze po lewej stronie, kończy się niepowodzeniem, wiersze lub niepowodzenie wierszy wyłącznie po prawej stronie. Możesz również wybrać pojedynczej gałęzi.

## <a name="append-rows"></a>Dołącz wiersze
Ta transformacja dołącza dane z innego przepływu danych do bieżącego. Jest on mapowany kolumny według pozycji, aby dodać nowe wiersze na końcu.

## <a name="append-columns"></a>Dołącz kolumny
Ta transformacja dołącza nowe kolumny z innej przepływu danych do bieżącego. Dodaje nowe kolumny z prawej strony. Nie próbuje wiersz w górę dane w wierszach.

## <a name="summarize"></a>Podsumowanie
Ta transformacja oblicza wartości zagregowanych dla kombinacji unikatowe wartości w co najmniej jeden z zaznaczonych kolumn. 

Agregacje, obsługiwane są: liczba, SUM, MIN, MAX, średnia, odchylenie i odchylenie standardowe. Lista wartości zagregowanych dla danej kolumny jest filtrowana w celu agregacji, które są stosowane do typu danych. Wartości zagregowane, które nie są stosowane są wyłączone. Na przykład nie jest możliwe do obliczenia średniej w kolumnie ciągu, ale istnieje możliwość obliczenia wartości minimalna i maksymalna.

Po udostępnieniu edytora przeciągnij z nagłówka kolumny do panelu u góry po lewej, gdzie są wyświetlane kolumny, które będą agregowane. Ten panel jest hierarchiczne, więc możecie zagnieżdżonych wartości zagregowanych. Panel Edytor w prawym górnym rogu jest używany do wybierania agregacji, które się do kolumny. Jedną kolumnę, może być agregowany jeden lub więcej razy. Po wybraniu co najmniej jedną wartość zagregowaną siatki w prawym dolnym rogu podglądy danych w postaci agregacji. 

Ta transformacja jest odpowiednikiem `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Usuń duplikaty
Ta transformacja usuwa cały wiersz, gdy istnieją zduplikowane wartości w co najmniej jeden z zaznaczonych kolumn. Jeśli żadna kolumna nie są wybrane, tylko wiersze usuwane są te, gdzie wszystkie wartości w kolumnach są takie same.

## <a name="sort"></a>Sortuj
Dane są sortowane tej transformacji. Sortowanie może odbywać się przy pojedynczej kolumny lub wiele, a każda kolumna można sortować (ustawienie domyślne) rosnąco lub Malejąco (które można zmienić za pomocą edytora).

Ta transformacja jest odpowiednikiem `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Przekształcenia danych wyjściowych
Przekształcenia następujące dane wyjściowe. Może mieć wiele bloków zapisu w jednym przepływie zapisać danych w różnych punktach.

### <a name="write-to-csv"></a>Zapis do pliku CSV
Ta transformacja zapisuje dane w postaci pliku CSV z bieżącym punkcie przepływu danych. Kontroluje lokalizacji (lokalnym lub zdalnym) i różne ustawienia w całym pliku.

### <a name="write-to-parquet"></a>Zapisać Parquet
Ta transformacja zapisuje dane w postaci Parquet z bieżącym punkcie przepływu danych. Kontroluje lokalizacji (lokalnym lub zdalnym) i różne ustawienia w całym pliku.

## <a name="script-based-transforms"></a>Przekształcenia opartych na skryptach
Następujące przekształcenia Użyj skryptu (Python), aby wykonać funkcje, których brakuje w produkcie podstawowym. 

>[!NOTE]
>Przed użyciem dowolnego z tych przekształceń, przeczytaj [rozszerzalność przy użyciu języka Python](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Dodaj kolumnę (skrypt)
Ta transformacja dodaje kolumnę z danymi przy użyciu wyrażenia języka Python.
Aby uzyskać więcej informacji, zobacz [kod przykładowy w języku Python pochodząca z nowymi kolumnami](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtrowanie zaawansowane (skrypt)
Użyć tej transformacji, aby zapisać filtr poziomu wierszy języka Python.
Aby uzyskać więcej informacji, zobacz [przykładowe wyrażenia filtru](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Przekształcanie przepływu danych (skrypt)
Ta transformacja dotyczy całego zestawu danych języka Python.
Aby uzyskać więcej informacji, zobacz [przykład przekształcania danych przepływ przekształcenia](data-prep-appendix7-sample-transform-data-flow-python.md).

Ta transformacja również można zastosować Python partycji danych.
Aby uzyskać więcej informacji, zobacz [przykład przekształcania danych przepływ przekształcenia](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Zapisać przepływ danych (skrypt)
Ta transformacja używany jest język Python zapisać całego zestawu danych.
Aby uzyskać więcej informacji, zobacz [Python próbki dla pochodząca z nowymi kolumnami](data-prep-appendix9-sample-destination-connections-python.md).



