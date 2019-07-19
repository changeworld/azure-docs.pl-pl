---
title: Skonfiguruj transformację źródłową w funkcji przepływu danych mapowania Azure Data Factory
description: Dowiedz się, jak skonfigurować transformację źródłową w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: f6aed5d2ac1c4672d8d8868fe127ead053512e42
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314833"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Przekształcanie źródła na potrzeby mapowania przepływu danych 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformacja źródła konfiguruje źródło danych dla przepływu danych. Przepływ danych może zawierać więcej niż jedną transformację źródłową. Podczas projektowania przepływów danych zawsze zaczyna się od transformacji źródłowej.

Każdy przepływ danych wymaga co najmniej jednego przekształcenia źródła. Dodaj dowolną liczbę źródeł, aby dokończyć przekształcenia danych. Można przyłączyć te źródła wraz z przekształceniem sprzężenia lub przekształceniem Unii.

> [!NOTE]
> Podczas debugowania przepływu danych dane są odczytywane ze źródła przy użyciu ustawienia próbkowania lub limitów źródła debugowania. Aby zapisać dane w ujścia, należy uruchomić przepływ danych z działania przepływu danych potoku. 

![Opcje przekształcania źródłowego na karcie Ustawienia źródła](media/data-flow/source.png "Źródło")

Skojarz transformację źródła przepływu danych z dokładnie jednym Data Factory zestawem danych. Zestaw danych definiuje kształt i lokalizację danych, które mają być zapisywane lub odczytywane. Możesz użyć symboli wieloznacznych i list plików w źródle, aby współpracować z więcej niż jednym plikiem jednocześnie.

## <a name="data-flow-staging-areas"></a>Obszary tymczasowe przepływu danych

Przepływ danych działa z *tymczasowymi* zestawami datadataset, które znajdują się na platformie Azure. Te zestawy danych są używane do przemieszczania podczas przekształcania danych. 

Data Factory ma dostęp do niemal 80 łączników natywnych. Aby dołączyć dane z innych źródeł w przepływie danych, należy użyć narzędzia działania kopiowania do przygotowania tych danych w jednym z obszarów tymczasowych zestawu danych przepływu danych.

## <a name="options"></a>Opcje

Wybierz opcje schematu i próbkowania dla danych.

### <a name="schema-drift"></a>Odchylenia od schematu
[Dryfowanie schematu](concepts-data-flow-schema-drift.md) to możliwość natywnego obsługi elastycznych schematów w przepływach danych bez konieczności jawnego definiowania zmian w kolumnach.

* Wybierz opcję **Zezwalaj na dryfowanie schematu** , jeśli kolumny źródłowe będą często zmieniane. To ustawienie umożliwia przepływ wszystkich przychodzących pól źródłowych do przepływu przez przekształcenia do ujścia.

* Wybranie opcji **wnioskowanie typów kolumn** z przekazaniem spowoduje wyszukanie funkcji ADF w celu zdefiniowania typów danych dla każdej wykrytej nowej kolumny. Po wyłączeniu tej funkcji ADF przyjmie ciąg.

### <a name="validate-schema"></a>Weryfikuj schemat

Jeśli przychodzące wersje danych źródłowych nie są zgodne ze zdefiniowanym schematem, przepływ danych nie zostanie uruchomiony.

![Ustawienia źródła publicznego, pokazując opcje sprawdzania poprawności schematu, Zezwalaj na dryfowanie schematu i próbkowanie](media/data-flow/source1.png "Źródło publiczne 1")

### <a name="sample-the-data"></a>Przykładowe dane
Włącz **próbkowanie** , aby ograniczyć liczbę wierszy ze źródła. Użyj tego ustawienia, gdy testujesz lub przykładowe dane ze źródła do celów debugowania.

## <a name="define-schema"></a>Definiuj schemat

Jeśli pliki źródłowe nie są jednoznacznie wpisane (na przykład pliki płaskie, a nie pliki parquet), Zdefiniuj typy danych dla każdego pola w transformacji źródłowej.  

![Ustawienia transformacji źródła na karcie Zdefiniuj schemat](media/data-flow/source2.png "Źródło 2")

Można później zmienić nazwy kolumn w przekształceniu SELECT. Zmień typy danych przy użyciu transformacji kolumn pochodnych. W przypadku źródeł o jednoznacznie określonym typie można modyfikować typy danych w późniejszej transformacji SELECT. 

![Typy danych w przekształcaniu SELECT](media/data-flow/source003.png "typy danych")

### <a name="optimize-the-source-transformation"></a>Optymalizuj transformację źródłową

Na karcie **Optymalizacja** dla transformacji źródłowej może zostać wyświetlony typ partycji **źródłowej** . Ta opcja jest dostępna tylko wtedy, gdy źródło jest Azure SQL Database. Dzieje się tak, ponieważ Data Factory próbuje nawiązać połączenie równolegle, aby uruchamiać duże zapytania względem źródła SQL Database.

![Ustawienia partycji źródłowej](media/data-flow/sourcepart3.png "partycjonowanie")

Nie ma potrzeby partycjonowania danych w źródle SQL Database, ale partycje są przydatne w przypadku dużych zapytań. Partycję można oprzeć na kolumnie lub zapytaniu.

### <a name="use-a-column-to-partition-data"></a>Używanie kolumny do partycjonowania danych

Z tabeli źródłowej wybierz kolumnę do partycjonowania. Należy również ustawić liczbę partycji.

### <a name="use-a-query-to-partition-data"></a>Używanie zapytania do partycjonowania danych

Możesz wybrać opcję partycjonowania połączeń na podstawie zapytania. Wystarczy wprowadzić zawartość predykatu WHERE. Na przykład wprowadź Year > 1980.

## <a name="source-file-management"></a>Zarządzanie plikiem źródłowym

Wybierz pozycję Ustawienia, aby zarządzać plikami w źródle. 

![Nowe ustawienia źródła](media/data-flow/source2.png "Nowe ustawienia")

* **Ścieżka symboli**wieloznacznych: Z kontenera źródłowego wybierz serię plików, które pasują do wzorca. To ustawienie zastępuje dowolny plik w definicji zestawu danych.

Przykłady symboli wieloznacznych:

* ```*```Reprezentuje dowolny zestaw znaków
* ```**```Reprezentuje zagnieżdżanie katalogów cyklicznych
* ```?```Zamienia jeden znak
* ```[]```Dopasowuje jeden z więcej znaków w nawiasach

* ```/data/sales/**/*.csv```Pobiera wszystkie pliki CSV w obszarze/Data/Sales
* ```/data/sales/20??/**```Pobiera wszystkie pliki w 20-wieku
* ```/data/sales/2004/*/12/[XY]1?.csv```Pobiera wszystkie pliki CSV w 2004 w grudniu, zaczynając od X lub Y poprzedzone przez 2-cyfrową liczbę

Kontener musi być określony w zestawie danych. Ścieżka symbolu wieloznacznego musi zawierać również ścieżkę folderu z folderu głównego.

* **Lista plików**: To jest zestaw plików. Utwórz plik tekstowy, który zawiera listę plików ścieżek względnych do przetworzenia. Wskaż ten plik tekstowy.
* **Kolumna do przechowywania nazwy pliku**: Zapisz nazwę pliku źródłowego w kolumnie w danych. Wprowadź tutaj nową nazwę, aby zapisać ciąg nazw plików.
* **Po zakończeniu**: Wybierz, aby nic nie robić z plikiem źródłowym po uruchomieniu przepływu danych, usuń plik źródłowy lub Przenieś plik źródłowy. Ścieżki do przenoszenia są względne.

Aby przenieść pliki źródłowe do innej lokalizacji po przetworzeniu, najpierw wybierz pozycję "Przenieś" dla operacji na pliku. Następnie ustaw katalog "z". Jeśli nie używasz symboli wieloznacznych dla ścieżki, ustawienie "od" będzie takie samo jak folder źródłowy.

Jeśli masz wieloznaczną ścieżkę źródłową, np.:

```/data/sales/20??/**/*.csv```

Możesz określić wartość "od" jako

```/data/sales```

I "do" jako

```/backup/priorSales```

W takim przypadku wszystkie podkatalogi w/Data/Sales, które były źródłem są przenoszone względem/backup/priorSales.

### <a name="sql-datasets"></a>Zestawy danych SQL

Jeśli źródło jest w SQL Database lub SQL Data Warehouse, dostępne są dodatkowe opcje zarządzania plikami źródłowymi.

* **Zapytanie**: Wprowadź zapytanie SQL dla źródła. To ustawienie przesłania każdą tabelę, która została wybrana w zestawie danych. Należy zauważyć, że klauzule **order by** nie są obsługiwane w tym miejscu, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanej przez użytkownika. **SELECT * FROM udfGetData ()** to format UDF w języku SQL, który zwraca tabelę. To zapytanie spowoduje utworzenie tabeli źródłowej, której można użyć w przepływie danych.
* **Rozmiar wsadu**: Wprowadź rozmiar partii, aby podzielić duże ilości danych na odczyt.
* **Poziom izolacji**: Domyślne źródła danych SQL w mapowaniu strumieniowego ADF są odczytywane jako niezatwierdzone. Poziom izolacji można zmienić tutaj na jedną z następujących wartości:
* Odczytaj zatwierdzone
* Odczytaj niezatwierdzone
* Odczyt powtarzalny
* Serializable
* Brak (Ignoruj poziom izolacji)

![Poziom izolacji](media/data-flow/isolationlevel.png "Poziom izolacji")

> [!NOTE]
> Operacje na plikach są uruchamiane tylko wtedy, gdy rozpoczyna się przepływ danych z uruchomienia potoku (debugowania lub przebiegu wykonywania) używającego działania wykonywania przepływu danych w potoku. Operacje na plikach *nie są* uruchamiane w trybie debugowania przepływu danych.

### <a name="projection"></a>Rzut

Podobnie jak w przypadku schematów w zestawach danych, projekcja w źródle definiuje kolumny, typy i formaty dane z danych źródłowych. 

![Ustawienia na karcie projekcja](media/data-flow/source3.png "Projekcja")

Jeśli plik tekstowy nie ma zdefiniowanego schematu, wybierz pozycję **Wykryj typ danych** , aby Data Factory próbkować i wywnioskować typy danych. Wybierz opcję **Definiuj domyślny format** , aby automatycznie wykrywać domyślne formaty danych. 

Można modyfikować typy danych kolumny w późniejszej transformacji pochodnej kolumny. Użyj przekształcenia SELECT, aby zmodyfikować nazwy kolumn.

![Ustawienia dla domyślnych formatów danych](media/data-flow/source2.png "Formaty domyślne")

### <a name="add-dynamic-content"></a>Dodaj zawartość dynamiczną

Po kliknięciu wewnątrz pól w panelu ustawień zostanie wyświetlone hiperłącze "Dodaj zawartość dynamiczną". Po kliknięciu tutaj zostanie uruchomiony program Expression Builder. Jest to miejsce, w którym można ustawić wartości ustawień dynamicznie przy użyciu wyrażeń, statycznych wartości literałów lub parametrów.

![Parametry](media/data-flow/params6.png "Parametry")

## <a name="next-steps"></a>Następne kroki

Zacznij tworzyć [transformację pochodną kolumny](data-flow-derived-column.md) i [przekształcenie SELECT](data-flow-select.md).
