---
title: Zamiast ETL, projektowanie ELT do usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zamiast ETL zaprojektować wyodrębniania, obciążenia i przekształcenie (ELT) proces ładowania danych lub magazyn danych SQL Azure.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5ceb8cfd8efea66dbf17b8c522316b9a010e437d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799451"
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Projektowanie wyodrębniania, obciążenia i przekształcenie (ELT) dla usługi Azure SQL Data Warehouse

Zamiast wyodrębniania, przekształcania i ładowania (ETL) należy zaprojektować wyodrębniania, obciążenia i przekształcenie (ELT) proces ładowania danych do usługi Azure SQL Data Warehouse. W tym artykule przedstawiono sposoby projektowania ELT procesu, który przenosi dane do magazynu danych Azure.

## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnienie, obciążenia, i przekształcenie (ELT) jest procesem, za pomocą którego dane są przenoszone z systemu źródłowego do docelowego magazynu danych. Ten proces odbywa się regularnie, na przykład co godzinę lub codziennie, aby pobrać nowo wygenerować danych do magazynu danych. Idealny można pobrać danych ze źródła do magazynu danych jest opracowanie procesu ELT z użyciem aparatu PolyBase, aby załadować dane do usługi SQL Data Warehouse.

ELT ładuje najpierw, a następnie przekształca danych, podczas gdy wyodrębniania, przekształcania i ładowania (ETL) przekształca danych przed załadowaniem go. Wykonywanie ELT zamiast ETL jest zapisywany koszt udostępniania zasobów do przekształcania danych, przed jego załadowaniem. Podczas korzystania z usługi SQL Data Warehouse, ELT wykorzystuje system MPP do wykonywania transformacji.

Choć wiele zmian wykonywania ELT do usługi SQL Data Warehouse, poniżej przedstawiono podstawowe kroki:  

1. Wyodrębnij źródło danych do plików tekstowych.
2. Grunt dane do magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Store.
3. Przygotowanie danych do załadowania.
2. Ładowanie danych do tabel przemieszczania przy użyciu programu PolyBase usługi SQL Data Warehouse.
3. Przekształć dane.
4. Wstawianie danych do tabel w środowisku produkcyjnym.


Samouczek ładowania, zobacz [Użyj programu PolyBase, aby załadować dane z magazynu obiektów blob platformy Azure do usługi Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Aby uzyskać więcej informacji, zobacz [ładowania blogu wzorce](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Opcje ładowania przy użyciu programu PolyBase

Program PolyBase jest technologia, która uzyskuje dostęp do danych poza bazy danych za pomocą języka T-SQL. Jest to najlepszy sposób, aby załadować dane do usługi SQL Data Warehouse. Przy użyciu programu PolyBase dane ładuje równolegle ze źródła danych bezpośrednio do węzłów obliczeniowych. 

Aby załadować dane przy użyciu programu PolyBase, można użyć dowolnej z tych opcji ładowania.

- [Program PolyBase z T-SQL](load-data-from-azure-blob-storage-using-polybase.md) działa poprawnie, gdy dane pochodzą z magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Store. Umożliwia sterowanie za pośrednictwem procesu ładowania, ale wymaga również do definiowania obiektów danych zewnętrznych. Inne metody Definiuj Mapowanie tabel źródłowych do tabel docelowych tych obiektów w tle.  Do organizowania obciążeń T-SQL, można użyć usługi fabryka danych Azure, SSIS lub usługę Azure functions. 
- [Program PolyBase z SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) działa poprawnie, gdy źródło danych jest w programie SQL Server, SQL Server lokalnie lub w chmurze. SSIS definiuje źródłowej do docelowej tabeli mapowania, a także organizuje obciążenia. Jeśli masz już pakiety usług SSIS, można zmodyfikować pakiety do pracy z nowego miejsca docelowego magazynu danych. 
- [Program PolyBase z fabryki danych Azure (ADF)](sql-data-warehouse-load-with-data-factory.md) jest inne narzędzie do aranżacji.  Definiuje potoku i harmonogramy zadań. 
- [Program PolyBase z Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) przeniesienia danych z tabeli SQL Data Warehouse Databricks dataframe i/lub zapisuje dane z Databricks dataframe do tabeli SQL Data Warehouse.

### <a name="polybase-external-file-formats"></a>Program PolyBase formatów plików zewnętrznych

Program PolyBase ładuje dane z UTF-8 i UTF-16 zakodowane rozdzielane pliki tekstowe. Oprócz plików tekstu rozdzielanego ładuje z formatów plików usługi Hadoop RC plików ORC i Parquet. Program PolyBase mogą ładować dane z Gzip i szałowe skompresowane pliki. Program PolyBase nie obsługuje obecnie rozszerzone ASCII, format stałej szerokości i zagnieżdżone formatów, takich jak WinZip, JSON i XML.

### <a name="non-polybase-loading-options"></a>Opcje ładowania non-PolyBase
Jeśli dane nie jest zgodny z PolyBase, możesz użyć [bcp](/sql/tools/bcp-utility) lub [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP ładuje bezpośrednio do usługi SQL Data Warehouse bez pośrednictwa magazynu obiektów Blob platformy Azure i jest przeznaczona tylko w przypadku małych obciążeń. Uwaga: wydajność obciążenia tych opcji jest znacznie mniejsza niż PolyBase. 


## <a name="extract-source-data"></a>Wyodrębnianie danych źródłowych

Pobieranie danych z systemu źródłowego zależy od źródła.  Celem jest przenoszenia danych do rozdzielane pliki tekstowe. Jeśli używasz programu SQL Server, możesz użyć [narzędzia wiersza polecenia bcp](/sql/tools/bcp-utility) do eksportowania danych.  

## <a name="land-data-to-azure-storage"></a>Ziemi danych do magazynu Azure

Aby trafić dane w magazynie Azure, należy przenieść go do [magazynu obiektów Blob Azure](../storage/blobs/storage-blobs-introduction.md) lub [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). W dowolnej lokalizacji dane powinny być przechowywane w plikach tekstowych. Program Polybase można załadować z dowolnej lokalizacji.

Są to narzędzia i usługi, który służy do przenoszenia danych do magazynu Azure.

- [Usługa Azure ExpressRoute](../expressroute/expressroute-introduction.md) usługa zwiększa przepustowość sieci, wydajność oraz przewidywalności. ExpressRoute to usługa, która przekierowuje dane za pomocą dedykowanego połączenia prywatnego na platformie Azure. Połączenia ExpressRoute kierować danych za pośrednictwem publicznej sieci internet. Połączenia oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem publicznej sieci internet.
- [Narzędzie AZCopy](../storage/common/storage-moving-data.md) przenosi dane do magazynu Azure za pośrednictwem publicznej sieci internet. To działanie, jeśli Twoje dane są mniej niż 10 TB. Aby wykonać obciążeń na bieżąco z narzędzia AZCopy, przetestować szybkość sieci, aby sprawdzić, czy jest dopuszczalne. 
- [Fabryka danych Azure (ADF)](../data-factory/introduction.md) ma bramę, którą można zainstalować na serwerze lokalnym. Następnie można utworzyć potoku do przenoszenia danych z serwera lokalnego do usługi Azure Storage. Aby używać fabryka danych z magazynu danych SQL, zobacz [ładowanie danych do usługi SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).

## <a name="prepare-data"></a>Przygotowywanie danych

Może być konieczne przygotowanie i czyszczenie danych na koncie magazynu przed załadowaniem go do usługi SQL Data Warehouse. Przygotowanie danych można wykonać zablokowaniu danych w źródle, eksportować dane w plikach tekstowych lub po danych w usłudze Azure Storage.  Najłatwiej można pracować z danymi w początkowej procesu, jak to możliwe.  

### <a name="define-external-tables"></a>Zdefiniuj tabel zewnętrznych
Przed załadowaniem danych, musisz zdefiniować tabele zewnętrzne w magazynie danych. Program PolyBase używa tabel zewnętrznych do definiowania i uzyskać dostęp do danych w usłudze Azure Storage. Zewnętrzna tabela jest podobny do zwykłą tabelę. Główna różnica polega na punktami tabeli zewnętrznej dane przechowywane poza hurtowni danych. 

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, w formacie plików tekstowych i definicji tabeli. To są tematy składni T-SQL, które będą potrzebne:
- [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql)

Na przykład tworzenie obiektów zewnętrznych, zobacz [tworzenia tabel zewnętrznych](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) krok samouczka ładowania.

### <a name="format-text-files"></a>Pliki w formacie tekstowym

Po zdefiniowaniu obiektów zewnętrznych, konieczne jest dopasowanie wiersze pliki tekstowe z tabeli zewnętrznej i Definicja formatu pliku. Dane w każdym wierszu pliku tekstowego, muszą być wyrównane z definicji tabeli.

Aby sformatować pliki tekstowe:

- Dane pochodzące ze źródłem danych nierelacyjnych, należy przekształcić w wierszy i kolumn. Czy dane ze źródła danych relacyjnych i nierelacyjnych danych musi zostać przekształcone wyrównać definicje kolumn dla tabeli, w którym planujesz do ładowania danych. 
- Formatowanie danych w pliku tekstowym, aby były wyrównane z typami danych i kolumn w tabeli docelowej usługi SQL Data Warehouse. Niespójność między typami danych w plikach tekstowych zewnętrznych i tabeli magazynu danych powoduje, że wierszy, które mają zostać odrzucone podczas ładowania.
- Oddzielne pola w pliku tekstowym z terminator.  Należy użyć znaku lub sekwencji znaków, które nie zostało odnalezione w źródle danych. Użyj terminator określono [utworzyć EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Ładowanie do tabeli przejściowej
Aby pobrać dane do magazynu danych, jej sprawdza się dobrze w pierwszym załadowaniu danych do tabeli tymczasowej. Za pomocą tabeli przemieszczania, może obsługiwać błędy bez zakłócania tabele produkcji, a nie jest uruchomiona wycofywania operacji względem tabeli produkcji. Tabeli przemieszczania daje również uruchomić przekształcenia przed Wstawianie danych do tabel w środowisku produkcyjnym za pomocą usługi SQL Data Warehouse.

Aby załadować T-SQL, uruchom [Tworzenie tabeli jako wybierz (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) instrukcji T-SQL. To polecenie wstawia wyniki instrukcję select do nowej tabeli. Po wybraniu instrukcji z tabeli zewnętrznej, importuje dane zewnętrzne. 

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
Gdy dane znajdują się w tabeli przemieszczania, wykonaj transformacje, których wymaga obciążenie. Następnie przenieść dane do tabeli produkcji.

## <a name="insert-data-into-production-table"></a>Wstawianie danych do tabeli produkcyjnej

INSERT INTO... Instrukcja SELECT przenosi dane z tabeli tymczasowej do tabeli trwałych. 

Podczas projektowania proces ETL, spróbuj uruchomiony proces na niewielką ilość próbki. Spróbuj wyodrębniania 1000 wierszy z tabeli do pliku, przenieś go do platformy Azure, a następnie spróbuj załadowanie go do tabeli tymczasowej. 

## <a name="partner-loading-solutions"></a>Rozwiązań partnerskich ładowania
Wiele naszych partnerów ma ładowania rozwiązania. Aby dowiedzieć się więcej, zobacz listę naszych [rozwiązania partnerów](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Kolejne kroki
Ładowania wskazówki, zobacz [wskazówki dotyczące ładowania danych](guidance-for-loading-data.md).


