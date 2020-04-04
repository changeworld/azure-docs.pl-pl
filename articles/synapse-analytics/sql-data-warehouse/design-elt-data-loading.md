---
title: Zamiast ETL, zaprojektuj ELT
description: Wdrażanie elastycznych strategii ładowania danych dla puli sql synapse w ramach usługi Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 87b33e91076f8f7f31740795f0ec05cea49a1e83
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631202"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Strategie ładowania danych dla puli SQL Synapse

Tradycyjne pule SQL SMP używają procesu wyodrębniania, przekształcania i ładowania (ETL) do ładowania danych. Pula SQL synapse w ramach usługi Azure Synapse Analytics ma architekturę przetwarzania równoległego (MPP), która wykorzystuje skalowalność i elastyczność zasobów obliczeniowych i magazynowych.

Za pomocą wyodrębniania, ładowania i przekształcania (ELT) proces wykorzystuje MPP i eliminuje zasoby potrzebne do transformacji danych przed załadowaniem.

Podczas gdy pula SQL obsługuje wiele metod ładowania, w tym popularnych opcji programu SQL Server, takich jak [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) i [SQLBulkCopy API,](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)najszybszym i najbardziej skalowalnym sposobem ładowania danych jest tabele zewnętrzne PolyBase i [instrukcja COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (wersja zapoznawcza).

Za pomocą polybase i instrukcji COPY można uzyskać dostęp do danych zewnętrznych przechowywanych w magazynie obiektów Blob platformy Azure lub usłudze Azure Data Lake Store za pośrednictwem języka T-SQL. Aby uzyskać największą elastyczność podczas ładowania, zaleca się użycie instrukcji COPY.

> [!NOTE]  
> Instrukcja COPY jest obecnie w publicznej wersji zapoznawczej. Aby przekazać opinię, wyślij wiadomość e-mail na następującą listę dystrybucyjną: sqldwcopypreview@service.microsoft.com.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnij, Załaduj i Przekształć (ELT) to proces, w którym dane są wyodrębniane z systemu źródłowego, ładowane do puli SQL, a następnie przekształcane.

Podstawowe kroki wdrażania ELT to:

1. Wyodrębnij dane źródłowe do plików tekstowych.
2. Wyląduj dane w magazynie obiektów Blob platformy Azure lub usłudze Azure Data Lake Store.
3. Przygotuj dane do załadowania.
4. Załaduj dane do tabel przemieszczania za pomocą polybase lub polecenia KOPIUJ.
5. Przekształć dane.
6. Wstaw dane do tabel produkcyjnych.

Aby zapoznać się z samouczkiem ładowania bazy danych PolyBase, zobacz [Korzystanie z bazy danych PolyBase w celu załadowania danych z magazynu obiektów blob platformy Azure](load-data-from-azure-blob-storage-using-polybase.md).

Aby uzyskać więcej informacji, zobacz [Blog wczytywania wzorców](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnij dane źródłowe do plików tekstowych

Wyprowadzanie danych z systemu źródłowego zależy od lokalizacji magazynu.  Celem jest przeniesienie danych do PolyBase i copy obsługiwane rozdzielane tekstu lub plików CSV.

### <a name="polybase-and-copy-external-file-formats"></a>Formaty plików zewnętrznych PolyBase i COPY

Za pomocą polybase i instrukcji COPY można załadować dane z zakodowanych w formatach tekstowych lub plików CSV zakodowanych w formatach UTF-8 i UTF-16. Oprócz rozdzielonych plików tekstowych lub CSV ładuje się z formatów plików Hadoop, takich jak ORC i Parquet. PolyBase i copy instrukcji można również załadować dane z Gzip i Snappy skompresowane pliki.

Rozszerzone formaty ASCII, format o stałej szerokości i zagnieżdżone formaty, takie jak WinZip lub XML, nie są obsługiwane. W przypadku eksportowania z programu SQL Server można użyć [narzędzia wiersza polecenia bcp](/sql/tools/bcp-utility?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) do eksportowania danych do rozdzielonych plików tekstowych.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Wyląduj dane w magazynie obiektów Blob platformy Azure lub usłudze Azure Data Lake Store

Aby wyładowywać dane w magazynie platformy Azure, można przenieść je do [magazynu obiektów Blob platformy Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) lub usługi Azure Data Lake Store [Gen2.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) W obu lokalizacjach dane powinny być przechowywane w plikach tekstowych. PolyBase i COPY instrukcji można załadować z każdej lokalizacji.

Narzędzia i usługi, których można użyć do przenoszenia danych do usługi Azure Storage:

- [Usługa Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zwiększa przepływność sieci, wydajność i przewidywalność. Usługa ExpressRoute to usługa, która kieruje dane za pośrednictwem dedykowanego połączenia prywatnego na platformę Azure. Połączenia usługi ExpressRoute nie kierują danych przez publiczny Internet. Połączenia zapewniają większą niezawodność, większą szybkość, mniejsze opóźnienia i większe bezpieczeństwo niż typowe połączenia przez publiczny Internet.
- [Narzędzie AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) przenosi dane do usługi Azure Storage za pośrednictwem publicznego Internetu. To działa, jeśli rozmiary danych są mniejsze niż 10 TB. Aby regularnie wykonywać obciążenia za pomocą AZCopy, przetestuj szybkość sieci, aby sprawdzić, czy jest to dopuszczalne.
- [Usługa Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ma bramę, którą można zainstalować na serwerze lokalnym. Następnie można utworzyć potok, aby przenieść dane z serwera lokalnego do usługi Azure Storage. Aby użyć usługi Data Factory z analizą SQL, zobacz [Ładowanie danych dla usługi SQL Analytics](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Przygotowanie danych do załadunku

Przed załadowaniem może być konieczne przygotowanie i wyczyszczone dane na koncie magazynu. Przygotowanie danych można wykonać, gdy dane znajdują się w źródle, podczas eksportowania danych do plików tekstowych lub po tym, jak dane znajdują się w usłudze Azure Storage.  Najłatwiej jest pracować z danymi tak wcześnie, jak to możliwe.  

### <a name="define-external-tables"></a>Definiowanie tabel zewnętrznych

Jeśli używasz PolyBase, należy zdefiniować tabele zewnętrzne w puli SQL przed załadowaniem. Tabele zewnętrzne nie są wymagane przez copy instrukcji. PolyBase używa tabel zewnętrznych do definiowania danych w usłudze Azure Storage i uzyskiwania do nich dostępu.

Tabela zewnętrzna jest podobna do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje na dane przechowywane poza pulą SQL.

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, formatu plików tekstowych i definicji tabel. Artykuły referencyjne składni T-SQL, które będą potrzebne, to:

- [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Podczas ładowania parkietu mapowanie typu danych SQL jest następujące:

| **Typ danych parkietu** | **Typ danych SQL** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        wartość logiczna        |        bit        |
|        double         |       float       |
|         float         |       rzeczywiste        |
|        double         |       pieniędzy       |
|        double         |    smallmoney     |
|        ciąg         |       nchar       |
|        ciąg         |     nvarchar      |
|        ciąg         |       char        |
|        ciąg         |      varchar      |
|        binarny         |      binarny       |
|        binarny         |     varbinary     |
|       sygnatura czasowa       |       date        |
|       sygnatura czasowa       |   smalldatetime   |
|       sygnatura czasowa       |     datetime2     |
|       sygnatura czasowa       |     datetime      |
|       sygnatura czasowa       |       time        |
|         date          |       date        |
|        decimal        |      decimal      |

Na przykład tworzenia obiektów zewnętrznych, zobacz [Tworzenie tabel zewnętrznych](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) krok w samouczku ładowania.

### <a name="format-text-files"></a>Formatowanie plików tekstowych

Jeśli używasz PolyBase, zdefiniowane obiekty zewnętrzne muszą wyrównać wiersze plików tekstowych z definicją tabeli zewnętrznej i formatu pliku. Dane w każdym wierszu pliku tekstowego muszą być zgodne z definicją tabeli.
Aby sformatować pliki tekstowe:

- Jeśli dane pochodzą ze źródła nierelacyjnego, należy przekształcić je w wiersze i kolumny. Niezależnie od tego, czy dane pochodzą ze źródła relacyjnego, czy nierelacyjnego, dane muszą zostać przekształcone w taki sposób, aby były zgodne z definicjami kolumn dla tabeli, do której mają być ładowane dane.
- Formatowanie danych w pliku tekstowym w celu wyrównania z kolumnami i typami danych w tabeli docelowej. Niewspółosiowość między typami danych w zewnętrznych plikach tekstowych i tabeli puli SQL powoduje, że wiersze mają zostać odrzucone podczas ładowania.
- Rozdziel pola w pliku tekstowym za pomocą terminatora.  Pamiętaj, aby użyć znaku lub sekwencji znaków, których nie ma w danych źródłowych. Użyj terminatora określonego w formacie [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Załaduj dane za pomocą PolyBase lub instrukcji COPY

Najlepszym rozwiązaniem jest załadowanie danych do tabeli przemieszczania. Tabele przemieszczania umożliwiają obsługę błędów bez zakłócania tabel produkcyjnych. Tabela przemieszczania daje również możliwość użycia puli SQL MPP dla przekształceń danych przed wstawieniem danych do tabel produkcyjnych.

Tabela musi być wstępnie utworzona podczas ładowania do tabeli przemieszczania z KOPIĄ.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opcje ładowania za pomocą instrukcji PolyBase i COPY

Aby załadować dane za pomocą PolyBase, można użyć dowolnej z następujących opcji ładowania:

- [PolyBase z T-SQL](load-data-from-azure-blob-storage-using-polybase.md) działa dobrze, gdy dane znajdują się w magazynie obiektów Blob platformy Azure lub w magazynie usługi Azure Data Lake Store. Zapewnia największą kontrolę nad procesem ładowania, ale wymaga również definiowania obiektów danych zewnętrznych. Inne metody definiują te obiekty za kulisami podczas mapowania tabel źródłowych do tabel docelowych.  Aby zorganizować obciążenia T-SQL, można użyć usługi Azure Data Factory, SSIS lub azure funkcji.
- [PolyBase z SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) działa dobrze, gdy dane źródłowe są w programie SQL Server, lokalnie lub w chmurze. SSIS definiuje mapowania tabeli źródłowej do docelowej, a także organizuje obciążenie. Jeśli masz już pakiety SSIS, możesz zmodyfikować pakiety, aby pracować z nowym miejscem docelowym magazynu danych.
- [Instrukcja PolyBase i COPY z usługą Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) to kolejne narzędzie aranżacji.  Definiuje potok i planuje zadania.
- [PolyBase z usługą Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) przesyła dane z tabeli do dataframe i/lub zapisuje dane z dataframe Databricks do tabeli przy użyciu PolyBase.

### <a name="other-loading-options"></a>Inne opcje ładowania

Oprócz PolyBase i instrukcji COPY można użyć [bcp](https://docs.microsoft.com/sql/tools/bcp-utility?view=sql-server-ver15) lub [interfejsu API SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). bcp ładuje się bezpośrednio do bazy danych bez przechodzenia przez magazyn obiektów Blob platformy Azure i jest przeznaczony tylko dla małych obciążeń.

> [!NOTE]
> Uwaga: wydajność ładowania tych opcji jest wolniejsza niż PolyBase i copy instrukcji.

## <a name="5-transform-the-data"></a>5. Przekształcenie danych

Gdy dane są w tabeli przemieszczania, wykonaj przekształcenia, które wymaga obciążenia. Następnie przenieś dane do tabeli produkcyjnej.

## <a name="6-insert-the-data-into-production-tables"></a>6. Wstawianie danych do tabel produkcyjnych

Wkładka do ... Instrukcja SELECT przenosi dane z tabeli przemieszczania do stałej tabeli.

Podczas projektowania procesu ETL spróbuj uruchomić proces na małej próbce testowej. Spróbuj wyodrębnić 1000 wierszy z tabeli do pliku, przenieś go na platformę Azure, a następnie spróbuj załadować go do tabeli przemieszczania.

## <a name="partner-loading-solutions"></a>Rozwiązania do ładowania partnerów

Wielu naszych partnerów ma rozwiązania do ładowania. Aby dowiedzieć się więcej, zapoznaj się z listą naszych [partnerów rozwiązań.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące ładowania, zobacz [Wskazówki dotyczące ładowania danych](guidance-for-loading-data.md).
