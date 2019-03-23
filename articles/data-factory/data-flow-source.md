---
title: Mapowanie przekształceń źródła przepływu danych w usłudze Azure Data Factory
description: Mapowanie przekształceń źródła przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 54302f97913fd01dc8f8e4a8d987a407c8bdf9a7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369177"
---
# <a name="mapping-data-flow-source-transformation"></a>Mapowanie przekształceń źródła przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie źródła umożliwia skonfigurowanie źródła danych, które mają być używane do przenoszenia danych do przepływu danych. Masz więcej niż jednego źródła przekształcenia w jednym przepływ danych. Zawsze zaczynają się projektowania przepływu danych przy użyciu transformacji źródła.

> [!NOTE]
> Każdy przepływ danych wymaga co najmniej jeden Przekształcenie źródła. Dodaj dowolną liczbę dodatkowych źródeł, ile jest potrzebne do ukończenia przekształceń danych. Możesz dołączyć do tych źródeł, wraz z Join lub przekształcania Unii. Podczas debugowania przepływu danych w sesji debugowania, ze źródła przy użyciu ustawienia próbkowania lub debugowania źródła limity będą odczytywane dane. Jednak żadne dane nie będą zapisywane do ujścia, do momentu wykonania przepływu danych z działania przepływu danych w potoku. 

![Opcje przekształcania źródła](media/data-flow/source.png "źródła")

Każde przekształcenie źródła przepływ danych musi być skojarzony z dokładnie jeden zestaw danych fabryki danych. Zestaw danych określa kształt i lokalizację danych do zapisu lub odczytu. Może używać symboli wieloznacznych i pliku listy w źródle, pracować z więcej niż jeden plik w czasie gdy przy użyciu plikowych źródeł.

## <a name="data-flow-staging-areas"></a>Przepływ danych — obszarów tymczasowych

Przepływ danych w programach "tymczasową" zestawy danych, które są wszystkie na platformie Azure. Te zestawy danych przepływu danych są używane dla danych przejściowych do wykonania przekształceń danych. Fabryka danych ma dostęp do niemal 80 różnych natywne łączniki. Aby dołączyć dane z tych innych źródeł na przepływ danych, etap pierwszy tych danych do jednej z tych obszarów tymczasowych przepływ danych zestawu danych za pomocą działania kopiowania.

## <a name="options"></a>Opcje

### <a name="allow-schema-drift"></a>Zezwalaj na kilka schematu
Wybierz Zezwalaj dryfu schematu, jeśli kolumny źródłowe zmieni się często. To ustawienie umożliwia wszystkie przychodzące pola ze źródła do przepływu za pośrednictwem przekształceń do ujścia.

### <a name="validate-schema"></a>Sprawdzanie poprawności schematu

![Publiczne źródło](media/data-flow/source1.png "publiczne źródło 1")

Przychodzące wersji źródła danych nie jest zgodny ze schematem zdefiniowane, następnie wykonanie przepływu danych zakończy się niepowodzeniem.

### <a name="sampling"></a>Próbkowanie
Użyj próbkowania, aby ograniczyć liczbę wierszy ze źródła.  Jest to przydatne podczas testowania lub próbkowanie danych ze źródła do debugowania.

## <a name="define-schema"></a>Definiowanie schematu

![Źródło przekształcenia](media/data-flow/source2.png "źródła 2")

Dla typów plików źródłowych, które nie są silnie typizowane (czyli pliki proste w przeciwieństwie do plików Parquet) należy zdefiniować typy danych dla każdego pola, w tym miejscu w Przekształcenie źródła. Następnie można zmienić nazwy kolumn w wybierz transformacji i typy danych w transformacji kolumny nie pochodzącej ze. 

![Źródło przekształcenia](media/data-flow/source003.png "typy danych")

Silnie typizowane źródeł można modyfikować typy danych w kolejnych przekształcania wybierz. 

### <a name="optimize"></a>Optymalizacja

![Źródła partycji](media/data-flow/sourcepart.png "partycjonowania")

Na karcie Optymalizuj dla transformacji źródła zobaczysz dodatkowy typ partycjonowania o nazwie "Źródło". To spowoduje tylko światła w górę po wybraniu bazy danych SQL Azure jako źródło. Jest to spowodowane ADF będzie chcesz zrównoleglić połączenia do wykonywania dużych zapytań dotyczących źródła danych bazy danych SQL Azure.

Partycjonowanie danych w źródle danych SQL jest opcjonalne, ale jest przydatne w przypadku dużych kwerend. Dostępne są dwie opcje:

### <a name="column"></a>Kolumna

Wybierz kolumnę do partycji na z tabeli źródłowej. Należy również ustawić maksymalną liczbę połączeń.

### <a name="query-condition"></a>Warunek kwerendy

Opcjonalnie można podzielić połączenia na podstawie zapytania. Dla tej opcji po prostu umieść zawartość predykat WHERE. Czyli rok > 1980

## <a name="source-file-management"></a>Zarządzanie plikami źródła
![Nowe ustawienia źródła](media/data-flow/source2.png "nowe ustawienia")

* Ścieżka symboli wieloznacznych do pobrania szeregu pliki z folderu źródłowego, które pasują do wzorca. Spowoduje to zastąpienie dowolnego pliku ustawionym w definicji zestawu danych.
* Lista plików. Tak samo jak zestaw plików. Wskaż plik tekstowy, który utworzysz listę plików ścieżkę względną do przetworzenia.
* Kolumny do przechowywania nazwy pliku będzie przechowywać nazwę pliku ze źródła, w kolumnie w Twoich danych. Wprowadź nową nazwę do przechowywania ciągu nazwy pliku.
* Po zakończeniu (istnieje możliwość nic nie rób z plikiem źródłowym, po wykonaniu przepływu danych, usuń plik źródłowy lub Przenieś pliki źródłowe. Ścieżki do przeniesienia są ścieżki względne.

### <a name="sql-datasets"></a>Zestawy danych SQL

Gdy używasz usługi Azure SQL DB lub Azure SQL DW jako źródło, masz dodatkowe opcje.

* Zapytanie: Wprowadź kwerendę SQL dla źródła. Ustawianie zapytania spowoduje zastąpienie wszelkich tabelę, która została wybrana w zestawie danych. Należy pamiętać, że klauzuli Order By nie są obsługiwane w tym miejscu. Jednak można ustawić pełną instrukcję SELECT FROM tutaj.

* Rozmiar partii: Wprowadź rozmiar partii, Podziel duże ilości danych do odczytu rozmiar partii.

> [!NOTE]
> Ustawienia działania pliku będą wykonywane tylko wtedy, gdy przepływ danych jest wykonywana z potoku (debugowanie potoku lub uruchamianie wykonywania) przy użyciu działania wykonywania przepływu danych w potoku. Operacje na plikach nie są wykonywane w trybie debugowania przepływ danych.

### <a name="projection"></a>Projekcja

![Projekcja](media/data-flow/source3.png "projekcji")

Podobnie jak schematy w zestawach danych, rzutowania w źródle definiuje kolumny danych, typów danych i formatów danych ze źródła danych. Jeśli masz plik tekstowy bez zdefiniowanego schematu, kliknij "Wykrywaj typ danych" poprosić usługi ADF, aby spróbować przykładowy i wywnioskować typów danych. Można ustawić wartości domyślne formatów autowykrywania przy użyciu przycisku "Zdefiniować domyślny Format". Typy danych kolumn podczas kolejnych przekształcania kolumny nie pochodzącej ze można modyfikować. Nazwy kolumn mogą być modyfikowane przy użyciu transformacji wybierz.

![Domyślne formatów](media/data-flow/source2.png "domyślne formatów")

## <a name="next-steps"></a>Kolejne kroki

Rozpocząć tworzenie swojej Przekształcanie danych przy użyciu [kolumny nie pochodzącej ze](data-flow-derived-column.md) i [wybierz](data-flow-select.md).
