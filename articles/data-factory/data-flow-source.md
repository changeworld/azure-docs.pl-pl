---
title: Konfigurowanie Przekształcenie źródła w funkcji mapowania przepływu danych usługi Azure Data Factory
description: Dowiedz się, jak skonfigurować źródło przekształcenia w mapowanie przepływu danych.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: dc0a6e008c7a1f4fb414f6d8adad3a94abc7a6b2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792333"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Przekształcenie źródła dla mapowania przepływu danych 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie źródła umożliwia skonfigurowanie źródła danych dla przepływu danych. Przepływ danych może zawierać więcej niż jeden Przekształcenie źródła. Podczas projektowania danych przepływów, zawsze zaczyna się od Przekształcenie źródła.

Każdy przepływ danych wymaga co najmniej jedno źródło przekształcenia. Co jest niezbędne do ukończenia przekształceń danych, należy dodać dowolną liczbę źródeł. Możesz dołączyć do tych źródeł, wraz z przekształcenie sprzężenie lub union transformacji.

> [!NOTE]
> Podczas debugowania przepływu danych, dane są odczytywane ze źródła przy użyciu ustawienia próbkowania lub limity źródła debugowania. Można zapisać danych do ujścia, należy uruchomić przepływ danych z potoku przepływu danych działania. 

![Przekształcenie opcje na karcie Ustawienia źródła źródła](media/data-flow/source.png "źródła")

Z dokładnie jeden zestaw danych fabryki danych, należy skojarzyć swoją transformację przepływ danych źródłowych. Zestaw danych określa kształt i lokalizację danych, przeznaczonych do zapisu lub odczytu. Symbole wieloznaczne oraz plik listy w źródle służy do pracy z więcej niż jeden plik jednocześnie.

## <a name="data-flow-staging-areas"></a>Przepływ danych — obszarów tymczasowych

Przepływ danych współpracuje z *przemieszczania* zestawów danych, które są wszystkie na platformie Azure. Na użytek tych zestawów danych przemieszczania, gdy masz transformowanie danych. 

Fabryka danych ma dostęp do niemal 80 natywne łączniki. Aby dołączyć dane z tych innych źródeł danych, należy użyć narzędzia działania kopiowania do przygotowania danych w jednym z przepływem danych zestawu danych tymczasowych obszarów.

## <a name="options"></a>Opcje

Wybierz opcje schematu i pobierania danych.

### <a name="allow-schema-drift"></a>Zezwalaj na kilka schematu
Wybierz **Zezwalaj dryfu schematu** Jeśli kolumny źródłowe zmieni się często. To ustawienie umożliwia wszystkie pola źródłowego przychodzący przepływ przekształcenia do ujścia.

### <a name="validate-schema"></a>Sprawdzanie poprawności schematu

Jeśli przychodzący wersji źródła danych nie jest zgodny zdefiniowanego schematu, przepływ danych zakończy się niepowodzeniem do uruchomienia.

![Ustawienia źródła publicznych, przedstawiający opcje Zezwalaj schematu dryfu, próbkowania i schematu sprawdzania poprawności](media/data-flow/source1.png "publiczne źródło 1")

### <a name="sample-the-data"></a>Przykładowe dane
Włącz **próbkowania** ograniczenia liczby wierszy ze źródła. Użyj tego ustawienia podczas testowania i przykładowe dane ze źródła do debugowania.

## <a name="define-schema"></a>Definiowanie schematu

W przypadku plików źródłowych nie są silnie typizowane (na przykład, pliki proste zamiast plików Parquet), należy zdefiniować typy danych dla każdego pola, w tym miejscu w Przekształcenie źródła.  

![Przekształcenie ustawienia na karcie Zdefiniuj schemat źródła](media/data-flow/source2.png "źródła 2")

Nazwy kolumn w transformacji wybierz można później zmienić. Umożliwia także zmieniać typów danych przekształcania kolumn pochodnych. Silnie typizowane źródeł można modyfikować typy danych w później wybierz transformacji. 

![Typy danych w transformacji wybierz](media/data-flow/source003.png "typy danych")

### <a name="optimize-the-source-transformation"></a>Optymalizowanie Przekształcenie źródła

Na **Optymalizacja** karta Przekształcenie źródła może zostać wyświetlony **źródła** partycji typu. Ta opcja jest dostępna tylko wtedy, gdy źródłem jest usługa Azure SQL Database. Jest to spowodowane usługi Data Factory stara się połączenia równolegle uruchamiać duże zapytania źródło bazy danych SQL.

![Ustawienia partycji źródła](media/data-flow/sourcepart2.png "partycjonowania")

Nie masz do partycjonowania danych w źródle bazy danych SQL, ale partycje są przydatne w przypadku dużych kwerend. Można utworzyć partycji kolumny lub zapytanie.

### <a name="use-a-column-to-partition-data"></a>Użyj kolumny do partycjonowania danych

Z tabeli źródłowej wybierz kolumnę do partycji na. Również ustawić maksymalną liczbę połączeń.

### <a name="use-a-query-to-partition-data"></a>Użyj zapytania do partycjonowania danych

Można podzielić połączenia na podstawie zapytania. Po prostu wprowadź zawartość predykat WHERE. Na przykład wprowadź rok > 1980.

## <a name="source-file-management"></a>Zarządzanie plikami źródła

Wybierz ustawienia do zarządzania plikami w źródle. 

![Nowe ustawienia źródła](media/data-flow/source2.png "nowe ustawienia")

* **Ścieżka symboli wieloznacznych**: Z folderu źródłowego wybierz serię plików, które pasują do wzorca. Ustawienie to zastępuje dowolny plik w definicji zestawu danych.
* **Lista plików**: Jest to zestaw plików. Utwórz plik tekstowy, który zawiera listę plików ścieżkę względną do przetworzenia. Wskaż to plik tekstowy.
* **Kolumny do przechowywania nazwy pliku**: Store nazwę pliku źródłowego w kolumnie w Twoich danych. Wprowadź nową nazwę do przechowywania ciągu nazwy pliku.
* **Po zakończeniu**: Wybierz nic nie rób z plikiem źródłowym, po danych przebiegi przepływu, usuń plik źródłowy lub Przenieś plik źródłowy. Ścieżki na czas przeprowadzki są względne.

### <a name="sql-datasets"></a>Zestawy danych SQL

Jeśli źródłem jest baza danych SQL lub SQL Data Warehouse, masz dodatkowe opcje zarządzania pliku źródłowym.

* **Zapytanie**: Wprowadź kwerendę SQL dla źródła. To ustawienie przesłania dowolnej tabeli, który został wybrany w zestawie danych. Należy pamiętać, że **Order By** klauzule nie są obsługiwane w tym miejscu. Ale ustawione pełną instrukcję SELECT FROM w tym miejscu.
* **Wielkość partii**: Wprowadź rozmiar partii, Podziel duże ilości danych do odczytu.

> [!NOTE]
> Operacje na plikach uruchamiane tylko wtedy, gdy przepływ danych jest uruchamiany z potoku uruchomienia (potoku debugowania lub wykonywania Uruchom), który używa działania wykonywania przepływu danych w potoku. Operacje na plikach *nie* uruchomić tryb debugowania przepływ danych.

### <a name="projection"></a>Projekcja

Podobnie jak schematy w zestawach danych projekcji w źródle definiuje kolumny danych, typy i formaty danych źródłowych. 

![Ustawienia na karcie projekcji](media/data-flow/source3.png "projekcji")

Jeśli plik tekstowy nie ma zdefiniowanego schematu, wybierz opcję **Wykryj typ danych** tak, aby usługi fabryka danych będzie przykładowe i wywnioskować typów danych. Wybierz **Zdefiniuj domyślny format** automatycznie formatuje dane domyślne. 

Można modyfikować typy danych kolumn w później — kolumny nie pochodzącej ze transformacji. Wybierz przekształcania umożliwia zmodyfikowanie nazw kolumn.

![Ustawienia domyślne formatów danych](media/data-flow/source2.png "domyślne formatów")

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij tworzenie [przekształcania kolumn pochodnych](data-flow-derived-column.md) i [wybierz przekształcania](data-flow-select.md).
