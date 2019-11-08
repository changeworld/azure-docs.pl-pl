---
title: Zamiast ETL, projekt ELT
description: Zamiast ETL Zaprojektuj proces wyodrębniania, ładowania i przekształcania (ELT) na potrzeby ładowania danych lub Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 11/07/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9220bf0cf94eaae6ddc945e83deac2a6041158d2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748508"
---
# <a name="data-loading-strategies-for-azure-sql-data-warehouse"></a>Strategie ładowania danych dla Azure SQL Data Warehouse

Tradycyjne magazyny danych SMP używają procesu wyodrębniania, transformacji i ładowania (ETL) do ładowania danych. Azure SQL Data Warehouse to architektura wysoce równoległego przetwarzania (MPP), która korzysta z skalowalności i elastyczności zasobów obliczeniowych i magazynu. Wykorzystanie procesu wyodrębniania, ładowania i przekształcania (ELT) może korzystać z funkcji MPP i wyeliminować zasoby konieczne do przekształcenia danych przed ich załadowaniem. Chociaż SQL Data Warehouse obsługuje wiele metod ładowania, w tym popularne opcje SQL Server, takie jak BCP i interfejs API usługi SQL docelowa elementu BulkCopy, najszybszym i najbardziej skalowalnym sposobem ładowania danych jest użycie podstawowych tabel zewnętrznych i [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza).  Korzystając z instrukcji Base i COPY, można uzyskać dostęp do zewnętrznych danych przechowywanych w usłudze Azure Blob Storage lub Azure Data Lake Store za pośrednictwem języka T-SQL. Aby zapewnić największą elastyczność podczas ładowania do SQL Data Warehouse, zalecamy użycie instrukcji COPY. 

> [!NOTE]  
> Instrukcja COPY jest obecnie w publicznej wersji zapoznawczej. Aby przekazać opinię, Wyślij wiadomość e-mail na następującą listę dystrybucyjną: sqldwcopypreview@service.microsoft.com.
>
        
 
> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnij, Załaduj i Przekształć (ELT) to proces polegający na tym, że dane są wyodrębniane z systemu źródłowego, ładowane do magazynu danych, a następnie przekształcane. 

Podstawowe kroki implementowania ELT dla SQL Data Warehouse są następujące:

1. Wyodrębnij dane źródłowe do plików tekstowych.
2. Wydziel dane do usługi Azure Blob Storage lub Azure Data Lake Store.
3. Przygotuj dane do załadowania.
4. Załaduj dane do tabel przemieszczania SQL Data Warehouse z użyciem polecenia Base lub COPY. 
5. Przekształć dane.
6. Wstaw dane do tabel produkcyjnych.


Aby zapoznać się z samouczkiem dotyczącym ładowania wieloczęściowego, zobacz [Korzystanie z bazy danych w celu załadowania z magazynu obiektów blob platformy Azure do Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Aby uzyskać więcej informacji, zobacz Blog dotyczący [ładowania wzorców](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnij dane źródłowe do plików tekstowych

Pobieranie danych z systemu źródłowego zależy od lokalizacji magazynu.  Celem jest przeniesienie danych do formatu Base i SKOPIOWAnie obsługiwanego tekstu lub plików CSV. 

### <a name="polybase-and-copy-external-file-formats"></a>Zewnętrzne i kopiowanie formatów plików zewnętrznych

Korzystając z instrukcji Base i COPY, można ładować dane z rozdzielonych plików tekstowych lub CSV z kodowaniem UTF-8 i UTF-16. Oprócz rozdzielonych plików tekstowych lub CSV, są one ładowane z formatów plików Hadoop, takich jak ORC i Parquet. Instrukcje Base i COPY mogą również ładować dane z skompresowanych plików gzip i przyciągania. Rozszerzony format ASCII, stała szerokość i zagnieżdżone formaty, takie jak WinZip lub XML, nie są obsługiwane. Jeśli eksportujesz z SQL Server, możesz użyć [narzędzia wiersza polecenia BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest) , aby wyeksportować dane do rozdzielanych plików tekstowych. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. wyląduj dane do usługi Azure Blob Storage lub Azure Data Lake Store

Aby wystawić dane w usłudze Azure Storage, można przenieść je do [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) lub [Azure Data Lake Store Gen2](../data-lake-store/data-lake-store-overview.md). W każdej lokalizacji dane powinny być przechowywane w plikach tekstowych. Instrukcji Base i COPY można załadować z jednej z lokalizacji.

Narzędzia i usługi, których można użyć do przenoszenia danych do usługi Azure Storage:

- Usługa [Azure ExpressRoute](../expressroute/expressroute-introduction.md) zwiększa przepustowość sieci, wydajność i przewidywalność. ExpressRoute to usługa, która przekierowuje dane za pomocą dedykowanego połączenia prywatnego z platformą Azure. Połączenia ExpressRoute nie kierują danych za pomocą publicznego Internetu. Połączenia oferują większą niezawodność, większe szybkości, krótsze opóźnienia oraz lepsze zabezpieczenia niż typowe połączenia przez publiczny Internet.
- [Narzędzie AzCopy](../storage/common/storage-moving-data.md) przenosi dane do usługi Azure Storage za pośrednictwem publicznego Internetu. To działa, jeśli rozmiar danych jest mniejszy niż 10 TB. Aby przeprowadzić regularne ładowanie w programie AZCopy, przetestuj szybkość sieci, aby sprawdzić, czy jest ona akceptowalna. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) zawiera bramę, którą można zainstalować na serwerze lokalnym. Następnie możesz utworzyć potok, aby przenieść dane z serwera lokalnego do usługi Azure Storage. Aby użyć Data Factory z SQL Data Warehouse, zobacz [ładowanie danych do SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Przygotuj dane do załadowania

Może być konieczne przygotowanie i oczyszczenie danych na koncie magazynu przed załadowaniem go do SQL Data Warehouse. Przygotowanie danych można wykonać, gdy dane są przechowywane w źródle, podczas eksportowania danych do plików tekstowych lub po utworzeniu danych w usłudze Azure Storage.  Najłatwiej pracujesz z danymi tak wcześnie w procesie, jak to możliwe.  

### <a name="define-external-tables"></a>Definiowanie tabel zewnętrznych

Jeśli korzystasz z bazy danych Base, musisz zdefiniować tabele zewnętrzne przed załadowaniem. Tabele zewnętrzne nie są wymagane przez instrukcję COPY. Baza danych używa tabel zewnętrznych w celu definiowania i uzyskiwania dostępu do nich w usłudze Azure Storage. Tabela zewnętrzna jest podobna do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje dane przechowywane poza magazynem danych. 

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, formatu plików tekstowych i definicji tabeli. Informacje o składni języka T-SQL, które są potrzebne, są następujące:
- [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

Podczas ładowania Parquet mapowanie typu danych za pomocą programu SQL DW to:

| **Parquet — typ danych** |                      **Typ danych SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        wartość logiczna        |                             bit                              |
|        double         |                            float                             |
|         float         |                             czasie rzeczywistym                             |
|        double         |                            finansowego                             |
|        double         |                          smallmoney                          |
|        ciąg         |                            nchar                             |
|        ciąg         |                           nvarchar                           |
|        ciąg         |                             delikatn                             |
|        ciąg         |                           varchar                            |
|        Binarny         |                            Binarny                            |
|        Binarny         |                          liczby                           |
|       sygnatura czasowa       |                             date                             |
|       sygnatura czasowa       |                        smalldatetime                         |
|       sygnatura czasowa       |                          datetime2                           |
|       sygnatura czasowa       |                           datetime                           |
|       sygnatura czasowa       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

Aby zapoznać się z przykładem tworzenia obiektów zewnętrznych, zobacz krok [Tworzenie tabel zewnętrznych](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) w samouczku ładowania.

### <a name="format-text-files"></a>Formatowanie plików tekstowych

W przypadku korzystania z bazy Base, zdefiniowane obiekty zewnętrzne muszą wyrównać wiersze plików tekstowych z definicją tabeli zewnętrznej i formatu pliku. Dane w każdym wierszu pliku tekstowego muszą być wyrównane z definicją tabeli.
Aby sformatować pliki tekstowe:

- Jeśli dane pochodzą z nierelacyjnego źródła, należy przekształcić je w wiersze i kolumny. Niezależnie od tego, czy dane pochodzą ze źródła relacyjnego, czy nierelacyjnego, dane muszą zostać przekształcone w celu dopasowania z definicjami kolumn dla tabeli, do której mają zostać załadowane dane. 
- Sformatuj dane w pliku tekstowym, aby wyrównać je do kolumn i typów danych w tabeli docelowej SQL Data Warehouse. Niezgodność między typami danych w zewnętrznych plikach tekstowych i tabeli magazynu danych powoduje odrzucenie wierszy podczas ładowania.
- Oddziel pola w pliku tekstowym z terminatorem.  Upewnij się, że używasz znaku lub sekwencji znaków, która nie została znaleziona w danych źródłowych. Użyj terminatora określonego przy użyciu parametru [Create External File Format](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase-or-the-copy-statement"></a>4. Załaduj dane do tabel przemieszczania SQL Data Warehouse przy użyciu bazy danych lub instrukcji COPY

Najlepszym rozwiązaniem jest załadowanie danych do tabeli przejściowej. Tabele przemieszczania umożliwiają obsługę błędów bez zakłócania pracy z tabelami produkcyjnymi. Tabela przemieszczania daje również możliwość użycia SQL Data Warehouse MPP na potrzeby transformacji danych przed wstawieniem danych do tabel produkcyjnych. Tabela musi być wstępnie utworzona podczas ładowania do tabeli przemieszczania z KOPIą.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opcje ładowania z użyciem instrukcji Base i COPY

Aby załadować dane za pomocą bazy danych Base, można użyć dowolnej z następujących opcji ładowania:

- Baza danych w języku [T-SQL](load-data-from-azure-blob-storage-using-polybase.md) działa prawidłowo, gdy dane są przechowywane w usłudze Azure Blob storage lub Azure Data Lake Store. Zapewnia ona największą kontrolę nad procesem ładowania, ale wymaga również zdefiniowania zewnętrznych obiektów danych. Inne metody definiują te obiekty w tle podczas mapowania tabel źródłowych do tabel docelowych.  Aby zorganizować obciążenia T-SQL, można użyć Azure Data Factory, SSIS lub Azure Functions. 
- [Baza](/sql/integration-services/load-data-to-sql-data-warehouse) danych programu SSIS działa dobrze, gdy dane źródłowe są w SQL Server, SQL Server lokalnie lub w chmurze. Program SSIS definiuje mapowania tabeli źródłowej do docelowej, a także organizuje obciążenie. Jeśli masz już pakiety SSIS, możesz zmodyfikować pakiety, aby współpracowały z nowym miejscem docelowym magazynu danych. 
- [Instrukcja "Base" i "Copy" z Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) to inne narzędzie aranżacji.  Definiuje potok i planuje zadania. 
- [Baza danych z Azure Databricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) transferuje dane z tabeli SQL Data Warehouse do Databases dataframes i/lub zapisuje dane z ramki Databases do tabeli SQL Data Warehouse przy użyciu bazy danych.

### <a name="other-loading-options"></a>Inne opcje ładowania

Oprócz instrukcji Base i COPY można użyć narzędzia [BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest) lub [interfejsu API SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP ładuje się bezpośrednio do SQL Data Warehouse bez przechodzenia przez usługę Azure Blob Storage i jest przeznaczony tylko do małych obciążeń. Należy pamiętać, że wydajność ładowania tych opcji jest mniejsza niż baza i instrukcja COPY. 


## <a name="5-transform-the-data"></a>5. Przekształć dane

Gdy dane są w tabeli przemieszczania, należy wykonać przekształcenia wymagane przez obciążenie. Następnie Przenieś dane do tabeli produkcyjnej.


## <a name="6-insert-the-data-into-production-tables"></a>6. Wstaw dane do tabel produkcyjnych

Wstaw do... Instrukcja SELECT przenosi dane z tabeli przemieszczania do trwałej tabeli. 

Podczas projektowania procesu ETL spróbuj uruchomić proces przy użyciu małego przykładu testowego. Spróbuj wyodrębnić 1000 wierszy z tabeli do pliku, przenieść go na platformę Azure, a następnie spróbować załadować go do tabeli przejściowej. 


## <a name="partner-loading-solutions"></a>Rozwiązania do ładowania partnerów

Wielu naszych partnerów ma załadowane rozwiązania. Aby dowiedzieć się więcej, zobacz listę naszych [partnerów rozwiązań](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące ładowania, zobacz [wskazówki dotyczące ładowania danych](guidance-for-loading-data.md).


