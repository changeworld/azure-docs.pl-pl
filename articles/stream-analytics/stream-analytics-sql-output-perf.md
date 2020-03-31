---
title: Dane wyjściowe usługi Azure Stream Analytics do bazy danych SQL usługi Azure
description: Dowiedz się więcej o wyprowadzaniu danych do platformy SQL Azure z usługi Azure Stream Analytics i osiąganiu wyższych współczynników przepływności zapisu.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: f68f973882af28d80b3a27bc4591c5ee932404a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443602"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Dane wyjściowe usługi Azure Stream Analytics do bazy danych SQL usługi Azure

W tym artykule omówiono wskazówki, aby osiągnąć lepszą wydajność przepływności zapisu podczas ładowania danych do bazy danych SQL Azure przy użyciu usługi Azure Stream Analytics.

Dane wyjściowe SQL w usłudze Azure Stream Analytics obsługuje pisanie równolegle jako opcja. Ta opcja umożliwia [w pełni równoległe](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologii zadań, gdzie wiele partycji wyjściowych są zapisywane do tabeli docelowej równolegle. Włączenie tej opcji w usłudze Azure Stream Analytics może jednak nie wystarczyć do osiągnięcia wyższej przepływności, ponieważ zależy to znacznie od konfiguracji bazy danych sql azure i schematu tabeli. Wybór indeksów, klucz klastrowania, współczynnik wypełnienia indeksu i kompresji mają wpływ na czas ładowania tabel. Aby uzyskać więcej informacji na temat optymalizacji bazy danych sql azure w celu zwiększenia wydajności zapytań i ładowania na podstawie wewnętrznych testów porównawczych, zobacz [wskazówki dotyczące wydajności bazy danych SQL](../sql-database/sql-database-performance-guidance.md). Kolejność zapisów nie jest gwarantowana podczas pisania równolegle do bazy danych SQL Azure Database.

Oto kilka konfiguracji w ramach każdej usługi, które mogą pomóc poprawić ogólną przepustowość rozwiązania.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Dziedzicz partycjonowanie** — ta opcja konfiguracji wyjściowej SQL umożliwia dziedziczenie schematu partycjonowania poprzedniego kroku kwerendy lub danych wejściowych. Po włączeniu tej funkcji zapisywanie do tabeli opartej na dysku i posiadanie [w pełni równoległej](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologii dla zadania, spodziewaj się lepszej przepływności. To partycjonowanie już automatycznie odbywa się w przypadku wielu innych [wyjść](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Blokowanie tabeli (TABLOCK) jest również wyłączone dla wstawień zbiorczych wykonanych za pomocą tej opcji.

> [!NOTE] 
> Gdy istnieje więcej niż 8 partycji wejściowych, dziedziczenie schematu partycjonowania wejściowego może nie być odpowiednim wyborem. Ten górny limit zaobserwowano w tabeli z pojedynczą kolumną tożsamości i indeksem klastrowanym. W takim przypadku należy rozważyć użycie [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 w zapytaniu, aby jawnie określić liczbę modułów zapisu danych wyjściowych. Na podstawie schematu i wyboru indeksów, uwagi mogą się różnić.

- **Rozmiar partii** — konfiguracja danych wyjściowych SQL umożliwia określenie maksymalnego rozmiaru partii w danych wyjściowych SQL usługi Azure Stream Analytics na podstawie charakteru tabeli docelowej/obciążenia. Rozmiar partii to maksymalna liczba rekordów wysyłanych z każdą transakcją wstawiania zbiorczego. W indeksach klastrowanego magazynu kolumn rozmiary partii około [100K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) umożliwiają większą równoległość, minimalne rejestrowanie i blokowanie optymalizacji. W tabelach opartych na dysku 10K (domyślnie) lub niższe może być optymalne dla rozwiązania, jak wyższe rozmiary partii może wywołać eskalacji blokady podczas wstawia zbiorczych.

- **Dostrajanie komunikatów wejściowych** — jeśli zoptymalizowano przy użyciu dziedziczenia partycjonowania i rozmiaru partii, zwiększenie liczby zdarzeń wejściowych na wiadomość na partycję pomaga w dalszym ciągu wypychanie przepływności zapisu. Dostrajanie komunikatów wejściowych umożliwia rozmiary partii w usłudze Azure Stream Analytics do określonego rozmiaru partii, poprawiając w ten sposób przepływność. Można to osiągnąć za pomocą [kompresji](stream-analytics-define-inputs.md) lub zwiększenie rozmiarów komunikatów wejściowych w EventHub lub Blob.

## <a name="sql-azure"></a>Usługi SQL Azure

- **Tabela partycjonowana i indeksy** — przy użyciu [partycjonowane](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) tabeli SQL i indeksy podzielone na partycje w tabeli z tej samej kolumny jako klucz partycji (na przykład PartitionId) może znacznie zmniejszyć rywalizacji między partycjami podczas zapisów. W przypadku tabeli podzielonej na partycje należy utworzyć [funkcję partycji](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) i [schemat partycji](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) w grupie plików PRIMARY. Zwiększy to również dostępność istniejących danych podczas ładowania nowych danych. Limit we/wy dziennika może zostać osiągnięty na podstawie liczby partycji, które można zwiększyć, uaktualniając jednostkę SKU.

- **Unikaj unikatowych naruszeń klucza** — jeśli w dzienniku aktywności usługi Azure Stream Analytics zostanie wyświetlonych [wiele komunikatów o naruszeniach praw kluczowych,](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) upewnij się, że na twoje zadanie nie mają wpływu unikatowe naruszenia ograniczeń, które mogą mieć miejsce podczas odzyskiwania. Można tego uniknąć, ustawiając opcję [IGNORUJ\_KLAWISZ DUP\_](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) na indeksach.

## <a name="azure-data-factory-and-in-memory-tables"></a>Fabryka danych platformy Azure i tabele w pamięci

- **Tabela w pamięci jako tabela tymczasowa** — [tabele w pamięci](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) umożliwiają bardzo szybkie ładowanie danych, ale dane muszą zmieścić się w pamięci. Testy porównawcze pokazują, że ładowanie zbiorcze z tabeli w pamięci do tabeli opartej na dysku jest około 10 razy szybsze niż bezpośrednie wstawianie zbiorcze przy użyciu pojedynczego modułu zapisującego do tabeli opartej na dysku z kolumną tożsamości i indeksem klastrowanym. Aby wykorzystać tę wydajność wstawiania zbiorczego, skonfiguruj [zadanie kopiowania przy użyciu usługi Azure Data Factory,](../data-factory/connector-azure-sql-database.md) która kopiuje dane z tabeli w pamięci do tabeli opartej na dysku.

## <a name="avoiding-performance-pitfalls"></a>Unikanie pułapek wydajności
Zbiorcze wstawianie danych jest znacznie szybsze niż ładowanie danych z pojedynczymi wstawiani, ponieważ unika się powtarzającego się obciążenia związane z przesyłaniem danych, analizowaniem instrukcji wstawiania, uruchamianiem instrukcji i wystawianiem rekordu transakcji. Zamiast tego bardziej wydajna ścieżka jest używana do aparatu magazynu do przesyłania strumieniowego danych. Koszt instalacji tej ścieżki jest jednak znacznie wyższy niż pojedyncza instrukcja wstawiania w tabeli opartej na dysku. Próg rentowności wynosi zazwyczaj około 100 wierszy, po przekroju którego ładowanie zbiorcze jest prawie zawsze bardziej wydajne. 

Jeśli szybkość zdarzeń przychodzących jest niska, można łatwo utworzyć rozmiary partii niższe niż 100 wierszy, co sprawia, że zbiorcze wstawianie nieefektywne i zużywa zbyt dużo miejsca na dysku. Aby obejść to ograniczenie, możesz wykonać jedną z następujących czynności:
* Utwórz wyzwalacz zamiast [tego,](/sql/t-sql/statements/create-trigger-transact-sql) aby używać prostej płytki dla każdego wiersza.
* Użyj tabeli tymczasowej w pamięci, jak opisano w poprzedniej sekcji.

Inny taki scenariusz występuje podczas zapisywania w indeksie magazynu kolumn nieklastrowanych (NCCI), gdzie mniejsze wstawia zbiorcze można utworzyć zbyt wiele segmentów, które mogą ulec awarii indeksu. W takim przypadku zalecenie jest użycie indeksu klastrowanego magazynu kolumn zamiast.

## <a name="summary"></a>Podsumowanie

Podsumowując, z funkcją danych wyjściowych podzielonych na partycje w usłudze Azure Stream Analytics dla danych wyjściowych SQL wyrównana równoległość zadania z tabelą podzieloną na partycje w usłudze SQL Azure powinna zapewnić znaczące ulepszenia przepływności. Wykorzystanie usługi Azure Data Factory do organizowania przenoszenia danych z tabeli w pamięci do tabel opartych na dysku może dać zysk przepływności o rząd wielkości. Jeśli to możliwe, poprawa gęstości wiadomości może być również głównym czynnikiem poprawy ogólnej przepływności.
