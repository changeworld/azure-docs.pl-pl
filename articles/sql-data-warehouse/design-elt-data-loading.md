---
title: Zamiast ETL, projektowanie procesu ELT dla usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zamiast ETL projektowania procesu wyodrębniania, ładowania i przekształcania (ELT) do ładowania danych lub usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 05/10/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fa688f40f8eb968f2c388601b387e4f584951a91
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595605"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Projektowanie PolyBase ładowaniem strategię dla usługi Azure SQL Data Warehouse

Tradycyjne magazyny danych SMP użyć procesu wyodrębniania, przekształcania i ładowania (ETL) do ładowania danych. Usługa Azure SQL Data Warehouse jest architekturę masowego przetwarzania równoległego (MPP), która przyjmuje zalet skalowalność i elastyczność zasoby obliczeniowe i magazynowe. Przy użyciu procesu wyodrębniania, ładowania i przekształcania (ELT) mogą korzystać z zalet MPP i wyeliminować zasobów wymaganych do przekształcania danych przed załadowaniem. Usługa SQL Data Warehouse obsługuje wiele metod ładowania, włącznie z opcjami innych technologii Polybase, takich jak narzędzia BCP i interfejsu API SQL elementu BulkCopy, to najszybszy i najbardziej skalowalny sposób załadować Data jest za pomocą programu PolyBase.  Program PolyBase to technologia, która uzyskuje dostęp do zewnętrznych danych przechowywanych w usłudze Azure Blob storage lub Azure Data Lake Store za pomocą języka T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnij obciążenia, i Przekształć (ELT) to proces, za pomocą którego dane są wyodrębniane z systemu źródłowego, załadowane do magazynu danych i następnie przekształcane. 

Podstawowe kroki wdrażania ELT PolyBase usługi SQL Data Warehouse są:

1. Wyodrębnianie danych źródłowych do plików tekstowych.
2. Grunt dane do usługi Azure Blob storage lub Azure Data Lake Store.
3. Przygotuj dane dotyczące ładowania.
4. Załaduj dane do SQL Data Warehouse przy użyciu technologii PolyBase tabel przemieszczania. 
5. Przekształcanie danych.
6. Wstawianie danych do tabel produkcyjnych.


Samouczek ładowania, zobacz [przy użyciu technologii PolyBase, aby załadować dane z usługi Azure blob storage do usługi Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Aby uzyskać więcej informacji, zobacz [ładowanie wzorców blogu](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnianie danych źródłowych do plików tekstowych

Pobieranie danych z systemu źródłowego, zależy od lokalizacji magazynu.  Celem jest przeniesienie danych do programu PolyBase obsługiwane rozdzielanych plików tekstowych. 

### <a name="polybase-external-file-formats"></a>Formaty plików zewnętrznych technologii PolyBase

Program PolyBase ładowania danych z UTF-8 i UTF-16 zakodowane rozdzielanych plików tekstowych. Oprócz rozdzielanych plików tekstowych ładuje z formatów plików usługi Hadoop RC pliku ORC i Parquet. Program PolyBase również mogą ładować dane z Snappy plików skompresowanych i Gzip. Program PolyBase aktualnie nie obsługuje rozszerzonych ASCII, format stałej szerokości i zagnieżdżone formatów, takich jak WinZip, JSON i XML. Jeśli eksportujesz z programu SQL Server, możesz użyć [narzędzia wiersza polecenia bcp](/sql/tools/bcp-utility) do eksportowania danych do rozdzielanych plików tekstowych. Parquet, do mapowania typów danych usługi SQL DW jest następująca:

| **Typ danych parquet** |                      **SQL Data Type**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        ciąg         |                            nchar                             |
|        ciąg         |                           nvarchar                           |
|        ciąg         |                             char                             |
|        ciąg         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date        | ((1) obciążenia jako int i rzutowania na datę </br> (2) [korzystania z łącznika usługi Azure Databricks SQL DW](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse) z </br> spark.conf.set( "spark.sql.parquet.writeLegacyFormat", "true" ) </br> (**aktualizacji wkrótce**) |
|        decimal        | [Używanie łącznika usługi Azure Databricks SQL DW](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse) z </br> spark.conf.set( "spark.sql.parquet.writeLegacyFormat", "true" ) </br> (**aktualizacji wkrótce**) |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Przejście dane do usługi Azure Blob storage lub Azure Data Lake Store

Aby znaleźć danych w usłudze Azure storage, można przenieść go do [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md) lub [usługi Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). W jednej z tych lokalizacji dane powinny znajdować się w plikach tekstowych. Program PolyBase może ładować z jednej z tych lokalizacji.

Narzędzia i usługi, które służy do przenoszenia danych do usługi Azure Storage:

- [Usługa Azure ExpressRoute](../expressroute/expressroute-introduction.md) usługa zwiększa przepustowość sieci, wydajności i stabilności. ExpressRoute to usługa, która kieruje dane za pomocą dedykowanego połączenia prywatnego na platformie Azure. Połączenia ExpressRoute nie kierują danych za pośrednictwem publicznej sieci internet. Połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem publicznej sieci internet.
- [Narzędzie AZCopy](../storage/common/storage-moving-data.md) przenosi dane do usługi Azure Storage za pośrednictwem publicznej sieci internet. Ta funkcja działa, gdy Twoje dane są mniej niż 10 TB. Aby wykonać obciążenia w regularnych odstępach czasu za pomocą narzędzia AZCopy, przetestować szybkość sieci, aby sprawdzić, czy jest dopuszczalne. 
- [Usługa Azure Data Factory (ADF)](../data-factory/introduction.md) ma bramę, którą można zainstalować na serwerze lokalnym. Następnie można utworzyć potoku w celu przeniesienia danych z serwera lokalnego do usługi Azure Storage. Aby użyć usługi Data Factory, usługa SQL Data Warehouse, zobacz [ładowanie danych do usługi SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Przygotowywanie danych do załadowania

Może być konieczne przygotowanie i czyszczenia danych na koncie usługi storage przed załadowaniem do usługi SQL Data Warehouse. Przygotowywania danych można wykonać, gdy dane są w źródle, jak wyeksportować dane do plików tekstowych lub po umieszczeniu danych w usłudze Azure Storage.  Najłatwiej można pracować z danymi tak wcześnie w procesie, jak to możliwe.  

### <a name="define-external-tables"></a>Definiowanie tabel zewnętrznych

Przed załadowaniem danych, należy zdefiniować tabele zewnętrzne w magazynie danych. Program PolyBase używa tabel zewnętrznych do definiowania i uzyskać dostęp do danych w usłudze Azure Storage. Tabela zewnętrzna jest podobny do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje danych przechowywanych poza w magazynie danych. 

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, w formacie plików tekstowych i definicji tabeli. Poniżej przedstawiono tematów składni języka T-SQL, które będą potrzebne:
- [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [TWORZENIE ZEWNĘTRZNEJ TABELI](/sql/t-sql/statements/create-external-table-transact-sql)

Aby uzyskać przykład tworzenia obiektów zewnętrznych, zobacz [Tworzenie tabel zewnętrznych](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) krok samouczka ładowania.

### <a name="format-text-files"></a>Pliki w formacie tekstowym

Po zdefiniowaniu zewnętrznych obiektów, konieczne jest dopasowanie wierszy plików tekstowych z tabeli zewnętrznej i definicję formatu pliku. W definicji tabeli muszą być dopasowane dane w poszczególnych wierszach pliku tekstowego.
Aby sformatować tekst pliki:

- Twoje dane pochodzą ze źródła danych nierelacyjnych, należy przekształcić go w wiersze i kolumny. Dane ze źródła relacyjnych lub nierelacyjnych, czy dane musi zostać przekształcone wyrównać definicje kolumn dla tabeli, w którym planujesz załadować dane. 
- Formatowanie danych w pliku tekstowym w celu zapewnienia zgodności z kolumn i typy danych w tabeli docelowej SQL Data Warehouse. Niespójność między typami danych w plikach tekstowych zewnętrznych i tabeli magazynu danych powoduje, że wierszy, które mają zostać odrzucone podczas ładowania.
- Oddzielne pola w pliku tekstowym z terminatorem.  Pamiętaj użyć znaku lub sekwencji znaków, która nie znajduje się w źródle danych. Użyj terminator określony za pomocą [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. Załaduj dane do magazynu danych SQL przy użyciu technologii PolyBase tabel przemieszczania

Jest najlepszym rozwiązaniem, aby załadować dane do tabeli przejściowej. Tabele przemieszczania umożliwiają obsługi błędów bez zakłócania tabel produkcyjnych. Tabeli przejściowej daje również możliwość użycia MPP magazynu danych SQL dla przekształceń danych, zanim Wstawianie danych do tabel produkcyjnych.

### <a name="options-for-loading-with-polybase"></a>Opcje ładowania przy użyciu technologii PolyBase

Aby załadować dane przy użyciu technologii PolyBase, można użyć dowolnej z tych opcji ładowania:

- [Program PolyBase przy użyciu języka T-SQL](load-data-from-azure-blob-storage-using-polybase.md) działa dobrze, gdy dane są już w usłudze Azure Blob storage lub Azure Data Lake Store. Zapewnia największą kontrolę nad procesem ładowania, ale wymaga również Definiowanie danych zewnętrznych obiektów. Inne metody definiowania tych obiektów w tle jako mapowanie tabel źródłowych do tabel docelowych.  Do organizowania obciążeń języka T-SQL, używając usługi Azure Data Factory, SSIS lub usługi Azure functions. 
- [Program PolyBase przy użyciu funkcji SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) działa dobrze, gdy źródło danych jest w programie SQL Server, SQL Server w środowisku lokalnym lub w chmurze. SSIS określa źródła do docelowego mapowania tabel, a także organizuje obciążenia. Jeśli masz już pakiety usług SSIS, można zmodyfikować pakiety do pracy z nowego miejsca docelowego magazynu danych. 
- [Program PolyBase z usługi Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) to kolejne narzędzie aranżacji.  Definiuje potoku i harmonogram zadań. 
- [Program PolyBase z usługi Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) przesyła dane z tabeli SQL Data Warehouse do Databricks dataframe i/lub zapisuje dane z usługi Databricks ramkę danych do tabeli SQL Data Warehouse przy użyciu technologii PolyBase.

### <a name="non-polybase-loading-options"></a>Opcje ładowania non-PolyBase

Jeśli dane nie jest zgodny z programem PolyBase, możesz użyć [bcp](/sql/tools/bcp-utility) lub [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). narzędzia BCP ładuje bezpośrednio do usługi SQL Data Warehouse bez pośrednictwa usługi Azure Blob storage i jest przeznaczona tylko dla małych obciążeń. Pamiętaj, że wydajność ładowania tych opcji jest znacznie wolniejsze niż programu PolyBase. 


## <a name="5-transform-the-data"></a>5. Przekształcanie danych

Gdy dane znajdują się w tabeli przemieszczania, należy wykonać przekształceń, które wymaga obciążenie. Następnie przenieś dane do tabeli produkcyjnej.


## <a name="6-insert-the-data-into-production-tables"></a>6. Wstawianie danych do tabel produkcyjnych

INSERT INTO... Instrukcja SELECT przenosi dane z tabeli tymczasowej do tabeli stałe. 

Podczas projektowania procesu ETL, spróbuj uruchomiony jest proces, na przykład mały test. Spróbuj wyodrębniania 1000 wierszy z tabeli do pliku, przenieś go do platformy Azure, a następnie spróbuj, załadowanie go do tabeli przejściowej. 


## <a name="partner-loading-solutions"></a>Partner podczas ładowania rozwiązań

Ma wielu partnerów, ładowania rozwiązań. Aby dowiedzieć się więcej, zobacz listę w naszym [rozwiązania partnerów](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące ładowania, zobacz [wskazówki dotyczące ładowania danych](guidance-for-loading-data.md).


