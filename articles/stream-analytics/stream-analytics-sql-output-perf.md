---
title: Usługa Azure Stream Analytics dane wyjściowe usługi Azure SQL Database
description: Dowiedz się więcej o wyprowadzanie danych SQL Azure z usługi Azure Stream Analytics i osiągnięcia wyższej przepływności zapisu.
services: stream-analytics
author: chetang
ms.author: chetang
manager: katicad
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/21/2018
ms.openlocfilehash: 623d03c96866392ef245fb924cbf6600e7850ffe
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47057976"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Usługa Azure Stream Analytics dane wyjściowe usługi Azure SQL Database

W tym artykule omówiono wskazówki, aby osiągnąć lepszą wydajność, przepływność zapisu, podczas ładowania danych do bazy danych Azure SQL przy użyciu usługi Azure Stream Analytics.

Danych wyjściowych SQL w usłudze Azure Stream Analytics obsługuje pisanie równolegle jako opcję. Ta opcja umożliwia [pełni równoległe](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) zadania topologii, w której wiele partycji danych wyjściowych będą zapisywane do tabeli docelowej w sposób równoległy. Włączenie tej opcji w usłudze Azure Stream Analytics jednak może nie być wystarczające do osiągnięcia wyższej przepustowości, ponieważ jest ona zależna znacznie na konfiguracji bazy danych SQL Azure i schemat tabeli. Wybór indeksów, klastrowanie klucza, współczynnik wypełnienia indeksu i kompresji mają wpływ na czas ładowania tabel. Aby uzyskać więcej informacji na temat optymalizowania swojej bazy danych SQL Azure, aby poprawić zapytania i załadować oparte na wewnętrzne testy porównawcze wydajności, zobacz [wskazówki dotyczące wydajności bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance).

Poniżej przedstawiono niektóre konfiguracje w ramach każdej usługi, który może zwiększyć ogólną przepływność rozwiązania.

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

- **Dziedziczenie Partitioning** — ten SQL danych wyjściowych konfiguracji opcji umożliwia dziedziczenie schematu partycjonowania w poprzednim kroku zapytania lub danych wejściowych. Dzięki włączeniu tej opcji, pisanie tabeli opartej na dyskach i równoważy [pełni równoległe](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) topologii dla zadania powinny być widoczne lepsze przepustowości. Ta partycjonowanie już automatycznie się dzieje w przypadku wielu innych [generuje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). Tabela blokowanie (TABLOCK) również jest wyłączona dla zbiorcze operacje wstawiania wprowadzone przy użyciu tej opcji.

> [!NOTE] 
> W przypadku więcej niż 8 partycji danych wejściowych dziedziczenie schemat partycjonowania danych wejściowych nie może być odpowiednim wyborem. Ten limit górny zaobserwowano dla tabeli zawierającej kolumnę jednej tożsamości, a indeks klastrowany. Na podstawie schematu i wybór indeksów, Twoje uwagi mogą się różnić.

- **Wielkość partii** — konfiguracja danych wyjściowych SQL pozwala określić maksymalny rozmiar partii w danych wyjściowych SQL usługi Azure Stream Analytics, oparte na rodzaju obciążenia/tabeli docelowej. Rozmiar partii to maksymalną liczbę rekordów, które są wysyłane z każdego zbiorczego Wstaw transakcję. W klastrowanych indeksach magazynu kolumn, batch rozmiary wokół [100 tysięcy](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) umożliwiają więcej przetwarzania równoległego, minimalnego rejestrowania i blokowanie optymalizacji. W tabelach opartej na dyskach 10 (ustawienie domyślne) lub niższego może być optymalne rozwiązanie zgodnie z wyższym rozmiary partii mogą wyzwalać przekazanie blokady podczas operacji wstawiania zbiorczego.

- **Wprowadź komunikat dostrajania** — jeśli zostały zoptymalizowane przy użyciu odziedziczyć rozmiar partycjonowania i usługi batch, zwiększenie liczby zdarzeń wejściowych na komunikat na partycję pomaga dalsze wzrostu przepływność zapisu. Komunikat wejściowy dostrajania umożliwia rozmiary partii w ramach usługi Azure Stream Analytics jako zgodnie z podanym rozmiarem partii, zwiększając w ten sposób przepływności. Można to osiągnąć przy użyciu [kompresji](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-inputs) lub jednostka SKU Premium usługi Centrum EventHub większe rozmiary wiadomości.

## <a name="sql-azure"></a>Usługi SQL Azure

- **Podzielona na partycje tabeli i indeksy** — przy użyciu [partycjonowane](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) tabeli SQL i Indeksy podzielone na partycje w tabeli za pomocą tej samej kolumnie jako klucz partycji (na przykład PartitionId) mogą znacznie zmniejszyć rywalizacji między partycje podczas zapisu. Dla tabeli partycjonowanej, musisz utworzyć [funkcja partycji](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) i [schemat partycji](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) na podstawowej grupy plików. Spowoduje to również zwiększyć dostępność istniejących danych podczas, gdy nowe dane są ładowane. Limit operacji We/Wy dziennika napotkać na podstawie liczby partycji, które można zwiększyć, zmieniając jednostki SKU.

- **Należy unikać unikatowy naruszenia klucza** — Jeśli [wielu komunikaty ostrzegawcze naruszenie klucza](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output) w dzienniku aktywności usługi Azure Stream Analytics upewnij się, zadanie nie ma wpływ na naruszenia unikatowego ograniczenia, które mogą mieć miejsce w przypadku odzyskiwania. Można tego uniknąć, ustawiając [Ignoruj\_DUP\_klucz](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output) opcji na indeksów.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory i tabelach w pamięci

- **W pamięci tabeli jako tabeli tymczasowej** — [tabelach w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) umożliwienia ładowania danych bardzo dużej szybkości, ale dane muszą mieszczą się w pamięci. Testy porównawcze ładowanie zbiorcze show z tabeli w pamięci do tabeli na dysku jest około 10 razy szybciej niż bezpośrednio zbiorcze wstawianie za pomocą jednego elementu zapisującego do opartej na dyskach tabeli z kolumną tożsamości i indeks klastrowany. Aby korzystać z tego wydajności wstawiania zbiorczego, skonfiguruj [zadanie kopiowania za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database) który kopiuje dane z tabeli w pamięci do tabeli na dysku.

## <a name="summary"></a>Podsumowanie

Podsumowując, za pomocą funkcji partycjonowanych danych wyjściowych w usłudze Azure Stream Analytics dla danych wyjściowych SQL wyrównany przetwarzanie równoległe zadania za pomocą tabeli partycjonowanej w usługach SQL Azure powinien zapewnić przepływności znaczne ulepszenia. Korzystanie z usługi Azure Data Factory do organizowania przenoszenia danych z tabeli w pamięci do tabel na dysku może dać przyrosty przepływności o rząd wielkości. Jeśli to możliwe, poprawy gęstość komunikat może być również czynnikiem w ulepszaniu ogólną przepływność.
