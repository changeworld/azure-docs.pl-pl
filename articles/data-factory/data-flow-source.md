---
title: Transformacja źródła w mapowaniu danych mapowania — Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak skonfigurować transformację źródłową w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 16bc4c2651d5571bce823aa9c69f823d7fede8af
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801621"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Przekształcanie źródła na potrzeby mapowania przepływu danych 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformacja źródła konfiguruje źródło danych dla przepływu danych. Podczas projektowania przepływów danych pierwszy krok zawsze skonfiguruje transformację źródłową. Aby dodać źródło, kliknij pole **Dodaj źródło** na kanwie przepływu danych.

Każdy przepływ danych wymaga co najmniej jednego przekształcenia źródła, ale w razie potrzeby można dodać dowolną liczbę źródeł do ukończenia transformacji danych. Można przyłączyć te źródła wraz z przyłączaniem, wyszukiwaniem lub przekształceniem Unii.

Każda transformacja źródła jest skojarzona z dokładnie jednym Data Factory zestawem danych. Zestaw danych definiuje kształt i lokalizację danych, które mają być zapisywane lub odczytywane. W przypadku korzystania z zestawu danych opartego na plikach można używać symboli wieloznacznych i list plików w źródle, aby współpracowały z więcej niż jednym plikiem naraz.

## <a name="supported-connectors-in-mapping-data-flow"></a>Obsługiwane łączniki w mapowaniu przepływu danych

Mapowanie przepływu danych odbywa się zgodnie z podejściem wyodrębniania, ładowania, przekształcania (ELT) i współdziała z *tymczasowymi* zestawami danych, które są wszystkie na platformie Azure. Obecnie następujące zestawy danych mogą być używane w transformacji źródłowej:
    
* Azure Blob Storage
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Data Warehouse
* Azure SQL Database

Azure Data Factory ma dostęp do ponad 80 łączników natywnych. Aby dołączyć dane z innych źródeł w przepływie danych, Użyj działania kopiowania w celu załadowania tych danych do jednego z obsługiwanych obszarów tymczasowych.

## <a name="source-settings"></a>Ustawienia źródła

Po dodaniu źródła skonfiguruj go za pomocą karty **Ustawienia źródła** . W tym miejscu możesz wybrać lub utworzyć zestaw danych, do którego prowadzi punkty źródłowe. Możesz również wybrać opcje schematu i próbkowania dla danych.

![Karta Ustawienia źródła](media/data-flow/source1.png "Karta Ustawienia źródła")

**Dryf schematu:** [Dryfowanie schematu](concepts-data-flow-schema-drift.md) to zdolność fabryki danych do natywnej obsługi elastycznych schematów w przepływach danych bez konieczności jawnego definiowania zmian w kolumnach.

* Zaznacz pole **Zezwalaj na dryf schematu** , jeśli kolumny źródłowe będą często zmieniane. To ustawienie umożliwia przepływ wszystkich przychodzących pól źródłowych do przepływu przez przekształcenia do ujścia.

* Wybór **typów kolumn z dryfem** spowoduje, że Fabryka danych będzie wykrywać i definiować typy danych dla każdej wykrytej nowej kolumny. Po wyłączeniu tej funkcji wszystkie kolumny z przeznaczeniem będą typu String.

**Weryfikuj schemat:** Jeśli wybrano opcję Weryfikuj schemat, przepływ danych nie zostanie uruchomiony, jeśli przychodzące dane źródłowe nie są zgodne ze zdefiniowanym schematem zestawu danych.

**Pomiń liczbę wierszy:** Pole Pomiń liczbę wierszy określa liczbę wierszy, które mają być ignorowane na początku zestawu danych.

**Sond** Włącz próbkowanie, aby ograniczyć liczbę wierszy ze źródła. Użyj tego ustawienia, gdy testujesz lub przykładowe dane ze źródła do celów debugowania.

Aby sprawdzić, czy źródło jest prawidłowo skonfigurowane, Włącz tryb debugowania i Pobierz Podgląd danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Gdy tryb debugowania jest włączony, konfiguracja limitu wierszy w ustawieniach debugowania spowoduje zastąpienie ustawienia próbkowania w źródle podczas wyświetlania podglądu danych.

## <a name="file-based-source-options"></a>Opcje źródła na podstawie plików

Jeśli używasz zestawu danych opartego na plikach, takiego jak Azure Blob Storage lub Azure Data Lake Storage, karta **Opcje źródła** umożliwia zarządzanie sposobem odczytywania plików przez źródło.

![Opcje źródła](media/data-flow/sourceOPtions1.png "Opcje źródła")

**Ścieżka symboli wieloznacznych:** Użycie wzorca wieloznacznego spowoduje, że ADF będzie przełączać pętlę do każdego pasującego folderu i pliku w ramach pojedynczego przekształcenia źródła. Jest to efektywny sposób przetwarzania wielu plików w ramach pojedynczego przepływu. Dodaj wiele symboli wieloznacznych wzorców ze znakiem +, który pojawia się po umieszczeniu wskaźnika myszy na istniejącym wzorcu symboli wieloznacznych.

Z kontenera źródłowego wybierz serię plików, które pasują do wzorca. W zestawie danych można określić tylko kontener. Ścieżka symbolu wieloznacznego musi zawierać również ścieżkę folderu z folderu głównego.

Przykłady symboli wieloznacznych:

* ```*```Reprezentuje dowolny zestaw znaków
* ```**```Reprezentuje zagnieżdżanie katalogów cyklicznych
* ```?```Zamienia jeden znak
* ```[]```Dopasowuje jeden z więcej znaków w nawiasach

* ```/data/sales/**/*.csv```Pobiera wszystkie pliki CSV w obszarze/Data/Sales
* ```/data/sales/20??/**```Pobiera wszystkie pliki w 20-wieku
* ```/data/sales/2004/*/12/[XY]1?.csv```Pobiera wszystkie pliki CSV w 2004 w grudniu, zaczynając od X lub Y poprzedzone znakiem dwucyfrowym

**Ścieżka katalogu głównego partycji:** Jeśli masz partycjonowane foldery w źródle plików o ```key=value``` formacie (na przykład Year = 2019), możesz przypisać najwyższy poziom tego drzewa folderów partycji do nazwy kolumny w strumieniu danych przepływu danych.

Najpierw ustaw symbol wieloznaczny, aby uwzględnić wszystkie ścieżki, które są folderami partycjonowanymi oraz pliki liści, które chcesz odczytać.

![Ustawienia pliku źródłowego partycji](media/data-flow/partfile2.png "Ustawienie pliku partycji")

Użyj ustawienia ścieżka katalogu głównego partycji, aby określić, jaki jest najwyższy poziom struktury folderów. Gdy przeglądasz zawartość danych za pośrednictwem wersji zapoznawczej, zobaczysz, że na AUTOMATYCZNYm ekranie zostaną dodane rozpoznane partycje znalezione na każdym z poziomów folderów.

![Ścieżka katalogu głównego partycji](media/data-flow/partfile1.png "Podgląd ścieżki katalogu głównego partycji")

**Lista plików:** To jest zestaw plików. Utwórz plik tekstowy, który zawiera listę plików ścieżek względnych do przetworzenia. Wskaż ten plik tekstowy.

**Kolumna do przechowywania nazwy pliku:** Zapisz nazwę pliku źródłowego w kolumnie w danych. Wprowadź tutaj nazwę nowej kolumny, aby zapisać ciąg nazw plików.

**Po zakończeniu:** Wybierz, aby nic nie robić z plikiem źródłowym po uruchomieniu przepływu danych, usuń plik źródłowy lub Przenieś plik źródłowy. Ścieżki do przenoszenia są względne.

Aby przenieść pliki źródłowe do innej lokalizacji po przetworzeniu, najpierw wybierz pozycję "Przenieś" dla operacji na pliku. Następnie ustaw katalog "z". Jeśli nie używasz symboli wieloznacznych dla ścieżki, ustawienie "od" będzie takie samo jak folder źródłowy.

Jeśli masz ścieżkę źródłową z symbolem wieloznacznym, składnia będzie wyglądać następująco:

```/data/sales/20??/**/*.csv```

Możesz określić wartość "od" jako

```/data/sales```

I "do" jako

```/backup/priorSales```

W takim przypadku wszystkie pliki, które zostały objęte usługą/Data/Sales, są przenoszone do/backup/priorSales.

> [!NOTE]
> Operacje na plikach są uruchamiane tylko wtedy, gdy rozpoczyna się przepływ danych z uruchomienia potoku (debugowania lub przebiegu wykonywania) używającego działania wykonywania przepływu danych w potoku. Operacje na plikach *nie są* uruchamiane w trybie debugowania przepływu danych.

**Filtruj według ostatniej modyfikacji:** Można filtrować, które pliki są przetwarzane, określając zakres dat, po którym były ostatnio modyfikowane. Wszystkie daty i godziny są w formacie UTC. 

### <a name="add-dynamic-content"></a>Dodaj zawartość dynamiczną

Wszystkie ustawienia źródła można określić jako wyrażenia przy użyciu [języka wyrażenia przekształcenia przepływu danych mapowania](data-flow-expression-functions.md). Aby dodać zawartość dynamiczną, kliknij lub umieść kursor wewnątrz pól w panelu ustawień. Kliknij hiperlink, aby **dodać zawartość dynamiczną**. Spowoduje to uruchomienie konstruktora wyrażeń, w którym można ustawić wartości dynamicznie przy użyciu wyrażeń, statycznych wartości literałów lub parametrów.

![Parametry](media/data-flow/params6.png "Parametry")

## <a name="sql-source-options"></a>Opcje źródła SQL

Jeśli źródło jest w SQL Database lub SQL Data Warehouse, na karcie **Opcje źródła** są dostępne dodatkowe ustawienia specyficzne dla bazy danych SQL. 

**Klawiatur** Wybierz, czy chcesz wskazać źródło w tabeli (równoważnej ```Select * from <table-name>```), czy wprowadzić niestandardowe zapytanie SQL.

**Zapytanie**: W przypadku wybrania zapytania w polu wejściowym wprowadź zapytanie SQL dla źródła. To ustawienie przesłania każdą tabelę, która została wybrana w zestawie danych. Klauzule **order by** nie są obsługiwane w tym miejscu, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanej przez użytkownika. **SELECT * FROM udfGetData ()** to format UDF w języku SQL, który zwraca tabelę. To zapytanie spowoduje utworzenie tabeli źródłowej, której można użyć w przepływie danych.

**Rozmiar wsadu**: Wprowadź rozmiar partii, aby podzielić duże ilości danych na odczyt.

**Poziom izolacji**: Domyślne źródła SQL w mapowaniu przepływu danych są odczytywane jako niezatwierdzone. Poziom izolacji można zmienić tutaj na jedną z następujących wartości:
* Odczytaj zatwierdzone
* Odczytaj niezatwierdzone
* Odczyt powtarzalny
* Serializable
* Brak (Ignoruj poziom izolacji)

![Poziom izolacji](media/data-flow/isolationlevel.png "Poziom izolacji")

## <a name="projection"></a>Rzut

Podobnie jak w przypadku schematów w zestawach danych, projekcja w źródle definiuje kolumny, typy i formaty dane z danych źródłowych. W przypadku większości typów zestawu danych, takich jak SQL i Parquet, projekcja w źródle jest stała, aby odzwierciedlała schemat zdefiniowany w zestawie danych. Jeśli pliki źródłowe nie są jednoznacznie wpisane (na przykład pliki w formacie Flat CSV, a nie pliki parquet), można zdefiniować typy danych dla każdego pola w transformacji źródłowej.

![Ustawienia na karcie projekcja](media/data-flow/source3.png "Projekcja")

Jeśli plik tekstowy nie ma zdefiniowanego schematu, wybierz pozycję **Wykryj typ danych** , aby Data Factory próbkować i wywnioskować typy danych. Wybierz opcję **Definiuj domyślny format** , aby automatycznie wykrywać domyślne formaty danych. 

Typy danych kolumny można modyfikować w transformacjach kolumn pochodnych w dół i w dół. Użyj przekształcenia SELECT, aby zmodyfikować nazwy kolumn.

## <a name="optimize-the-source-transformation"></a>Optymalizuj transformację źródłową

Na karcie **Optymalizacja** dla transformacji źródłowej może zostać wyświetlony typ partycji **źródłowej** . Ta opcja jest dostępna tylko wtedy, gdy źródło jest Azure SQL Database. Dzieje się tak, ponieważ Data Factory próbuje nawiązać połączenie równolegle, aby uruchamiać duże zapytania względem źródła SQL Database.

![Ustawienia partycji źródłowej](media/data-flow/sourcepart3.png "partycjonowanie")

Nie ma potrzeby partycjonowania danych w źródle SQL Database, ale partycje są przydatne w przypadku dużych zapytań. Partycję można oprzeć na kolumnie lub zapytaniu.

### <a name="use-a-column-to-partition-data"></a>Używanie kolumny do partycjonowania danych

Z tabeli źródłowej wybierz kolumnę do partycjonowania. Należy również ustawić liczbę partycji.

### <a name="use-a-query-to-partition-data"></a>Używanie zapytania do partycjonowania danych

Możesz wybrać opcję partycjonowania połączeń na podstawie zapytania. Wprowadź zawartość predykatu WHERE. Na przykład wprowadź Year > 1980.

Aby uzyskać więcej informacji na temat optymalizacji w ramach mapowania przepływu danych, zobacz [Optymalizacja karty](concepts-data-flow-optimize-tab.md).

## <a name="next-steps"></a>Następne kroki

Zacznij tworzyć [transformację pochodną kolumny](data-flow-derived-column.md) i [przekształcenie SELECT](data-flow-select.md).
