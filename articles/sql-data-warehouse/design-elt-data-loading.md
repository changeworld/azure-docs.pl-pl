---
title: Zamiast ETL, projektowanie procesu ELT dla usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zamiast ETL projektowania procesu wyodrębniania, ładowania i przekształcania (ELT) do ładowania danych lub usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: d004ad1f24448da0c7404761ca0865826b3000b3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261285"
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Projektowanie wyodrębniania, ładowania i transformacji (ELT) dla usługi Azure SQL Data Warehouse

Zamiast wyodrębniania, przekształcania i ładowania (ETL) projektowania procesu wyodrębniania, ładowania i przekształcania (ELT) dotyczące ładowania danych do usługi Azure SQL Data Warehouse. W tym artykule przedstawiono sposoby Projektowanie procesu ELT, którego dane są przenoszone do magazynu danych platformy Azure.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnij obciążenia, i Przekształć (ELT) to proces, za pomocą którego dane są przenoszone z systemu źródłowego do docelowego magazynu danych. Ten proces odbywa się regularnie, na przykład co godzinę lub codziennie, można pobrać nowo wygenerowane dane do magazynu danych. Idealny sposób, aby pobrać dane ze źródła do magazynu danych jest opracowanie procesu ELT, który używa programu PolyBase do ładowania danych do usługi SQL Data Warehouse.

ELT ładuje się najpierw, a następnie przekształca dane, natomiast wyodrębniania, przekształcania i ładowania (ETL) przekształca dane przed załadowaniem. Wykonywanie ELT zamiast ETL zapisuje kosztów związanych z udostępnianiem zasobów do przekształcania danych przed ich załadowaniem. Korzystając z SQL Data Warehouse, ELT wykorzystuje system MPP do wykonywania transformacji.

Chociaż istnieje wiele zmian do implementowania procesu ELT dla usługi SQL Data Warehouse, poniżej przedstawiono podstawowe kroki:  

1. Wyodrębnianie danych źródłowych do plików tekstowych.
2. Grunt dane do usługi Azure Blob storage lub Azure Data Lake Store.
3. Przygotuj dane dotyczące ładowania.
2. Ładowanie danych do usługi SQL Data Warehouse tabel przemieszczania przy użyciu programu PolyBase.
3. Przekształcanie danych.
4. Wstawianie danych do tabel produkcyjnych.


Samouczek ładowania, zobacz [przy użyciu technologii PolyBase, aby załadować dane z usługi Azure blob storage do usługi Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Aby uzyskać więcej informacji, zobacz [ładowanie wzorców blogu](https://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Opcje ładowania przy użyciu technologii PolyBase

Program PolyBase to technologia, która uzyskuje dostęp do danych poza bazą danych za pomocą języka T-SQL. Jest najlepszym sposobem, aby załadować dane do usługi SQL Data Warehouse. Dzięki technologii PolyBase załadowaniu danych równolegle ze źródła danych bezpośrednio do węzłów obliczeniowych. 

Aby załadować dane przy użyciu technologii PolyBase, można użyć dowolnej z tych opcji ładowania.

- [Program PolyBase przy użyciu języka T-SQL](load-data-from-azure-blob-storage-using-polybase.md) działa dobrze, gdy dane są już w usłudze Azure Blob storage lub Azure Data Lake Store. Zapewnia największą kontrolę nad procesem ładowania, ale wymaga również Definiowanie danych zewnętrznych obiektów. Inne metody definiowania tych obiektów w tle jako mapowanie tabel źródłowych do tabel docelowych.  Do organizowania obciążeń języka T-SQL, używając usługi Azure Data Factory, SSIS lub usługi Azure functions. 
- [Program PolyBase przy użyciu funkcji SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) działa dobrze, gdy źródło danych jest w programie SQL Server, SQL Server w środowisku lokalnym lub w chmurze. SSIS określa źródła do docelowego mapowania tabel, a także organizuje obciążenia. Jeśli masz już pakiety usług SSIS, można zmodyfikować pakiety do pracy z nowego miejsca docelowego magazynu danych. 
- [Program PolyBase z usługi Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) to kolejne narzędzie aranżacji.  Definiuje potoku i harmonogram zadań. 
- [Program PolyBase z usługi Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) przesyła dane z tabeli SQL Data Warehouse do Databricks dataframe i/lub zapisuje dane z usługi Databricks ramkę danych do tabeli SQL Data Warehouse.

### <a name="polybase-external-file-formats"></a>Formaty plików zewnętrznych technologii PolyBase

Program PolyBase ładowania danych z UTF-8 i UTF-16 zakodowane rozdzielanych plików tekstowych. Oprócz rozdzielanych plików tekstowych ładuje z formatów plików usługi Hadoop RC pliku ORC i Parquet. Program PolyBase mogą ładować dane z Snappy plików skompresowanych i Gzip. Program PolyBase aktualnie nie obsługuje rozszerzonych ASCII, format stałej szerokości i zagnieżdżone formatów, takich jak WinZip, JSON i XML.

### <a name="non-polybase-loading-options"></a>Opcje ładowania non-PolyBase
Jeśli dane nie jest zgodny z programem PolyBase, możesz użyć [bcp](/sql/tools/bcp-utility) lub [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). narzędzia BCP ładuje bezpośrednio do usługi SQL Data Warehouse bez pośrednictwa usługi Azure Blob storage i jest przeznaczona tylko dla małych obciążeń. Pamiętaj, że wydajność ładowania tych opcji jest znacznie wolniejsze niż programu PolyBase. 


## <a name="extract-source-data"></a>Wyodrębnianie danych źródłowych

Pobieranie danych z systemu źródłowego, zależy od źródła.  Celem jest przeniesienie danych do rozdzielanych plików tekstowych. Jeśli używasz programu SQL Server, możesz użyć [narzędzia wiersza polecenia bcp](/sql/tools/bcp-utility) do eksportowania danych.  

## <a name="land-data-to-azure-storage"></a>Ładowanie danych do usługi Azure storage

Aby znaleźć danych w usłudze Azure storage, można przenieść go do [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md) lub [usługi Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). W jednej z tych lokalizacji dane powinny być przechowywane w plikach tekstowych. Program Polybase może ładować z jednej z tych lokalizacji.

Są to narzędzia i usługi, które służy do przenoszenia danych do usługi Azure Storage.

- [Usługa Azure ExpressRoute](../expressroute/expressroute-introduction.md) usługa zwiększa przepustowość sieci, wydajności i stabilności. ExpressRoute to usługa, która kieruje dane za pomocą dedykowanego połączenia prywatnego na platformie Azure. Połączenia ExpressRoute nie kierują danych za pośrednictwem publicznej sieci internet. Połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem publicznej sieci internet.
- [Narzędzie AZCopy](../storage/common/storage-moving-data.md) przenosi dane do usługi Azure Storage za pośrednictwem publicznej sieci internet. Ta funkcja działa, gdy Twoje dane są mniej niż 10 TB. Aby wykonać obciążenia w regularnych odstępach czasu za pomocą narzędzia AZCopy, przetestować szybkość sieci, aby sprawdzić, czy jest dopuszczalne. 
- [Usługa Azure Data Factory (ADF)](../data-factory/introduction.md) ma bramę, którą można zainstalować na serwerze lokalnym. Następnie można utworzyć potoku w celu przeniesienia danych z serwera lokalnego do usługi Azure Storage. Aby użyć usługi Data Factory, usługa SQL Data Warehouse, zobacz [ładowanie danych do usługi SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).

## <a name="prepare-data"></a>Przygotowywanie danych

Może być konieczne przygotowanie i czyszczenia danych na koncie usługi storage przed załadowaniem do usługi SQL Data Warehouse. Przygotowywania danych można wykonać, gdy dane są w źródle, jak wyeksportować dane do plików tekstowych lub po umieszczeniu danych w usłudze Azure Storage.  Najłatwiej można pracować z danymi tak wcześnie w procesie, jak to możliwe.  

### <a name="define-external-tables"></a>Definiowanie tabel zewnętrznych
Przed załadowaniem danych, należy zdefiniować tabele zewnętrzne w magazynie danych. Program PolyBase używa tabel zewnętrznych do definiowania i uzyskać dostęp do danych w usłudze Azure Storage. Tabela zewnętrzna jest podobnie jak zwykłą tabelę. Główna różnica polega na tabeli zewnętrznej punktami danych przechowywanych poza w magazynie danych. 

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

## <a name="load-to-a-staging-table"></a>Ładowanie do tabeli przejściowej
Aby uzyskać dane do magazynu danych, działa również na pierwszym załadowaniu danych do tabeli przejściowej. Za pomocą tabeli przejściowej, można obsługiwać błędy bez zakłócania tabel produkcyjnych i należy zapobiegać uruchamianiu jej wielu operacji wycofywania na tabeli produkcyjnej. Tabeli przejściowej daje również możliwość używać usługa SQL Data Warehouse do uruchamiania przekształcenia przed Wstawianie danych do tabel produkcyjnych.

Aby załadować dane przy użyciu języka T-SQL, należy uruchomić [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrukcję języka T-SQL. To polecenie umieszcza wynik instrukcję select do nowej tabeli. Po wybraniu instrukcji z tabeli zewnętrznej, importuje dane zewnętrzne. 

W poniższym przykładzie zewnętrznego Data jest tabeli zewnętrznej. Wszystkie wiersze są importowane do nowej tabeli o nazwie dbo. Data.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Przekształcanie danych
Gdy dane znajdują się w tabeli przemieszczania, należy wykonać przekształceń, które wymaga obciążenie. Następnie przenieś dane do tabeli produkcyjnej.

## <a name="insert-data-into-production-table"></a>Wstawianie danych do tabeli produkcyjnej

INSERT INTO... Instrukcja SELECT przenosi dane z tabeli tymczasowej do tabeli stałe. 

Podczas projektowania procesu ETL, spróbuj uruchomiony jest proces, na przykład mały test. Spróbuj wyodrębniania 1000 wierszy z tabeli do pliku, przenieś go do platformy Azure, a następnie spróbuj, załadowanie go do tabeli przejściowej. 

## <a name="partner-loading-solutions"></a>Partner podczas ładowania rozwiązań
Ma wielu partnerów, ładowania rozwiązań. Aby dowiedzieć się więcej, zobacz listę w naszym [rozwiązania partnerów](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać wskazówki dotyczące ładowania, zobacz [wskazówki dotyczące ładowania danych](guidance-for-loading-data.md).


