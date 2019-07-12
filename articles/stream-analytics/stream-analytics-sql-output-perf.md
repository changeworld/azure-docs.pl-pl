---
title: Usługa Azure Stream Analytics dane wyjściowe usługi Azure SQL Database
description: Dowiedz się więcej o wyprowadzanie danych SQL Azure z usługi Azure Stream Analytics i osiągnięcia wyższej przepływności zapisu.
services: stream-analytics
author: chetanmsft
ms.author: chetang
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: e0775bff1e7bdeeaf2c544fd815c2ce3bf129eae
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620852"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Usługa Azure Stream Analytics dane wyjściowe usługi Azure SQL Database

W tym artykule omówiono wskazówki, aby osiągnąć lepszą wydajność, przepływność zapisu, podczas ładowania danych do bazy danych Azure SQL przy użyciu usługi Azure Stream Analytics.

Danych wyjściowych SQL w usłudze Azure Stream Analytics obsługuje pisanie równolegle jako opcję. Ta opcja umożliwia [pełni równoległe](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) zadania topologii, w której wiele partycji danych wyjściowych będą zapisywane do tabeli docelowej w sposób równoległy. Włączenie tej opcji w usłudze Azure Stream Analytics jednak może nie być wystarczające do osiągnięcia wyższej przepustowości, ponieważ jest ona zależna znacznie na konfiguracji bazy danych SQL Azure i schemat tabeli. Wybór indeksów, klastrowanie klucza, współczynnik wypełnienia indeksu i kompresji mają wpływ na czas ładowania tabel. Aby uzyskać więcej informacji na temat optymalizowania swojej bazy danych SQL Azure, aby poprawić zapytania i załadować oparte na wewnętrzne testy porównawcze wydajności, zobacz [wskazówki dotyczące wydajności bazy danych SQL](../sql-database/sql-database-performance-guidance.md). Nie gwarantuje kolejności zapisu, podczas zapisywania równolegle z bazą danych SQL Azure.

Poniżej przedstawiono niektóre konfiguracje w ramach każdej usługi, który może zwiększyć ogólną przepływność rozwiązania.

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

- **Dziedziczenie Partitioning** — ten SQL danych wyjściowych konfiguracji opcji umożliwia dziedziczenie schematu partycjonowania w poprzednim kroku zapytania lub danych wejściowych. Dzięki włączeniu tej opcji, pisanie tabeli opartej na dyskach i równoważy [pełni równoległe](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologii dla zadania powinny być widoczne lepsze przepustowości. Ta partycjonowanie już automatycznie się dzieje w przypadku wielu innych [generuje](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Tabela blokowanie (TABLOCK) również jest wyłączona dla zbiorcze operacje wstawiania wprowadzone przy użyciu tej opcji.

> [!NOTE] 
> W przypadku więcej niż 8 partycji danych wejściowych dziedziczenie schemat partycjonowania danych wejściowych nie może być odpowiednim wyborem. Ten limit górny zaobserwowano dla tabeli zawierającej kolumnę jednej tożsamości, a indeks klastrowany. W takim przypadku należy rozważyć użycie [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 w zapytaniu, aby jawnie określić liczbę modułów zapisywania danych wyjściowych. Na podstawie schematu i wybór indeksów, Twoje uwagi mogą się różnić.

- **Wielkość partii** — konfiguracja danych wyjściowych SQL pozwala określić maksymalny rozmiar partii w danych wyjściowych SQL usługi Azure Stream Analytics, oparte na rodzaju obciążenia/tabeli docelowej. Rozmiar partii to maksymalną liczbę rekordów, które są wysyłane z każdego zbiorczego Wstaw transakcję. W klastrowanych indeksach magazynu kolumn, batch rozmiary wokół [100 tysięcy](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) umożliwiają więcej przetwarzania równoległego, minimalnego rejestrowania i blokowanie optymalizacji. W tabelach opartej na dyskach 10 (ustawienie domyślne) lub niższego może być optymalne rozwiązanie zgodnie z wyższym rozmiary partii mogą wyzwalać przekazanie blokady podczas operacji wstawiania zbiorczego.

- **Wprowadź komunikat dostrajania** — jeśli zostały zoptymalizowane przy użyciu odziedziczyć rozmiar partycjonowania i usługi batch, zwiększenie liczby zdarzeń wejściowych na komunikat na partycję pomaga dalsze wzrostu przepływność zapisu. Komunikat wejściowy dostrajania umożliwia rozmiary partii w ramach usługi Azure Stream Analytics jako zgodnie z podanym rozmiarem partii, zwiększając w ten sposób przepływności. Można to osiągnąć przy użyciu [kompresji](stream-analytics-define-inputs.md) lub zwiększenie wielkości komunikat wejściowy w Centrum zdarzeń lub obiektu Blob.

## <a name="sql-azure"></a>Usługi SQL Azure

- **Podzielona na partycje tabeli i indeksy** — przy użyciu [partycjonowane](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) tabeli SQL i Indeksy podzielone na partycje w tabeli za pomocą tej samej kolumnie jako klucz partycji (na przykład PartitionId) mogą znacznie zmniejszyć rywalizacji między partycje podczas zapisu. Dla tabeli partycjonowanej, musisz utworzyć [funkcja partycji](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) i [schemat partycji](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) na podstawowej grupy plików. Spowoduje to również zwiększyć dostępność istniejących danych podczas, gdy nowe dane są ładowane. Limit operacji We/Wy dziennika napotkać na podstawie liczby partycji, które można zwiększyć, zmieniając jednostki SKU.

- **Należy unikać unikatowy naruszenia klucza** — Jeśli [wielu komunikaty ostrzegawcze naruszenie klucza](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) w dzienniku aktywności usługi Azure Stream Analytics upewnij się, zadanie nie ma wpływ na naruszenia unikatowego ograniczenia, które mogą mieć miejsce w przypadku odzyskiwania. Można tego uniknąć, ustawiając [Ignoruj\_DUP\_klucz](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) opcji na indeksów.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory i tabelach w pamięci

- **W pamięci tabeli jako tabeli tymczasowej** — [tabelach w pamięci](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) umożliwienia ładowania danych bardzo szybkie, ale dane muszą mieszczą się w pamięci. Testy porównawcze ładowanie zbiorcze show z tabeli w pamięci do tabeli na dysku jest około 10 razy szybciej niż bezpośrednio zbiorcze wstawianie za pomocą jednego elementu zapisującego do opartej na dyskach tabeli z kolumną tożsamości i indeks klastrowany. Aby korzystać z tego wydajności wstawiania zbiorczego, skonfiguruj [zadanie kopiowania za pomocą usługi Azure Data Factory](../data-factory/connector-azure-sql-database.md) który kopiuje dane z tabeli w pamięci do tabeli na dysku.

## <a name="avoiding-performance-pitfalls"></a>Unikanie problemów z wydajnością
Zbiorcze Wstawianie danych jest znacznie szybsze niż załadowanie danych za pomocą pojedynczych wstawień, ponieważ powtarzanych unika się obciążenie związane z przesyłania danych, analizy instrukcji insert, działająca instrukcja i wydawanie rekord transakcji. Zamiast tego ścieżką bardziej efektywne jest używany w aparacie magazynu do strumienia danych. Koszt instalacji ta ścieżka jest jednak znacznie wyższa niż pojedynczy instrukcję w tabeli na dysku. Próg rentowności jest zwykle około 100 wierszy, po przekroczeniu której zbiorcze ładowanie jest prawie zawsze więcej wydajne. 

Jeśli szybkość odbierania zdarzeń jest niska, jego można łatwo utworzyć rozmiary partii niższych niż 100 wierszy, które sprawia, że zbiorcze Wstawianie jest nieefektywne i zużywa zbyt dużej ilości miejsca na dysku. Aby obejść to ograniczenie, wykonaj jedną z następujących czynności:
* Utwórz INSTEAD OF [wyzwalacza](/sql/t-sql/statements/create-trigger-transact-sql) używać prostych insert dla każdego wiersza.
* Użyj tabeli tymczasowej w pamięci, zgodnie z opisem w poprzedniej sekcji.

Inny scenariusz taki występuje podczas zapisywania na nieklastrowany indeks magazynu kolumn (NCCI), w której mniejszych zbiorcze operacje wstawiania może tworzyć zbyt wiele segmentów, które mogą ulec awarii indeksu. W tym przypadku zaleca się użyć indeksu klastrowanego magazynu kolumn.

## <a name="summary"></a>Podsumowanie

Podsumowując, za pomocą funkcji partycjonowanych danych wyjściowych w usłudze Azure Stream Analytics dla danych wyjściowych SQL wyrównany przetwarzanie równoległe zadania za pomocą tabeli partycjonowanej w usługach SQL Azure powinien zapewnić przepływności znaczne ulepszenia. Korzystanie z usługi Azure Data Factory do organizowania przenoszenia danych z tabeli w pamięci do tabel na dysku może dać przyrosty przepływności o rząd wielkości. Jeśli to możliwe, poprawy gęstość komunikat może być również czynnikiem w ulepszaniu ogólną przepływność.
