---
title: Kopiowanie danych do/z usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: df8fe611c762421f3a963340b24df74a80a20160
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621738"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-azure-sql-data-warehouse-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika usługi Azure SQL Data Warehouse w wersji 2](../connector-azure-sql-data-warehouse.md).

W tym artykule opisano sposób korzystania działanie kopiowania w fabryce danych Azure, aby przenieść dane z usługi Azure SQL Data Warehouse. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.  

> [!TIP]
> Aby uzyskać najlepszą wydajność, użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse. [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) sekcja zawiera szczegółowe informacje. Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane należy skopiować **z usługi Azure SQL Data Warehouse** do następujących danych przechowuje:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących baz danych **Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Podczas kopiowania danych z serwera SQL lub bazy danych SQL Azure do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, fabryki danych może automatycznie tworzyć tabeli w usłudze SQL Data Warehouse przy użyciu schematu tabeli w magazynie źródła danych. Zobacz [automatycznego tworzenia tabel](#auto-table-creation) szczegółowe informacje.

## <a name="supported-authentication-type"></a>Obsługiwany typ uwierzytelniania
Azure SQL Data Warehouse łącznika Obsługa uwierzytelniania podstawowego.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z usługi Azure SQL Data Warehouse przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok, który kopiuje dane z magazynu danych SQL Azure jest za pomocą Kreatora kopiowania danych. Zobacz [samouczek: ładowanie danych do usługi SQL Data Warehouse z fabryką danych](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **fabryki danych**. Fabryka danych może zawierać co najmniej jeden potoków. 
2. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych. Na przykład jeśli kopiujesz danych z magazynu obiektów blob platformy Azure do usługi Azure SQL data warehouse, Utwórz dwa połączone usługi, aby połączyć Twoje konto magazynu Azure i usługi Azure SQL data warehouse z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji. 
3. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. W tym przykładzie wymienionych w ostatnim kroku tworzenia zestawu danych, aby określić folder, który zawiera dane wejściowe i kontener obiektów blob. I Utwórz innego elementu dataset, aby określić tabeli w magazynie danych Azure SQL, która przechowuje dane skopiowane z magazynu obiektów blob. Dla właściwości zestawu danych, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. W przykładzie wspomniano wcześniej używasz BlobSource jako źródło i SqlDWSink jako zbiorniku dla działania kopiowania. Podobnie magazyn danych SQL Azure są kopiowane do magazynu obiektów Blob Azure, należy użyć SqlDWSource i BlobSink w przypadku działania kopiowania. Właściwości działania kopiowania, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [skopiować właściwości działania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje dotyczące sposobu używania magazynu danych jako źródło lub zbiorniku kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładów z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych do/z usługi Azure SQL Data Warehouse, zobacz [przykłady JSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania określonych jednostek fabryki danych Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla usługi Azure SQL Data Warehouse połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **AzureSqlDW** |Yes |
| Parametry połączenia |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Data Warehouse właściwości connectionString. Obsługiwane jest tylko uwierzytelnianie podstawowe. |Yes |

> [!IMPORTANT]
> Skonfiguruj [zapory bazy danych SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) i serwer bazy danych do [Zezwalaj usługom Azure na dostęp do serwera](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ponadto jeśli kopiujesz danych Azure SQL Data Warehouse z poza tym Azure z lokalnych źródeł danych z bramą fabryki danych, należy skonfigurować odpowiedni zakres adresów IP dla komputera, który wysyła dane do usługi Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

Sekcja typeProperties jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcja dla zestawu danych typu **AzureSqlDWTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w bazie danych Azure SQL Data Warehouse, odnoszący się do połączonej usługi. |Yes |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden parametr wejściowy i tworzy tylko jedno wyjście.

Właściwości, które są dostępne w sekcji typeProperties działania różnić się z każdym typem działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

### <a name="sqldwsource"></a>SqlDWSource
Jeśli źródło jest typu **SqlDWSource**, są dostępne w następujących właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatniej instrukcji SQL musi być instrukcji SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** określono dla SqlDWSource, odbywa się działanie kopii tego zapytania względem źródła magazynu danych SQL Azure umożliwiają pobieranie danych.

Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury zestawu danych JSON służą do skonstruowania zapytania w celu uruchomienia usługi Azure SQL Data Warehouse. Przykład: `select column1, column2 from mytable`. Jeśli definicji zestawu danych nie ma on struktury, wszystkie kolumny są wybierane w tabeli.

#### <a name="sqldwsource-example"></a>Przykład SqlDWSource

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**Definicja procedury składowanej:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że dane określonych wycinek jest wyczyszczone. Aby uzyskać więcej informacji, zobacz [sekcji powtarzalności](#repeatability-during-copy). |Instrukcja zapytania. |Nie |
| allowPolyBase |Wskazuje, czy do użycia zamiast mechanizmu BULKINSERT PolyBase (jeśli jest to wymagane). <br/><br/> **Przy użyciu programu PolyBase jest zalecanym sposobem ładowanie danych do usługi SQL Data Warehouse.** Zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sekcji dla ograniczenia i szczegółów. |True <br/>Wartość FAŁSZ (ustawienie domyślne) |Nie |
| Usługi |Grupy właściwości, które można określić, kiedy **allowPolybase** właściwość jest ustawiona na **true**. |&nbsp; |Nie |
| rejectValue |Określa liczbę lub odsetek wierszy, które można odrzucić przed zapytanie nie powiedzie się. <br/><br/>Dowiedz się więcej o opcjach Odrzuć PolyBase **argumenty** sekcji [Tworzenie tabeli zewnętrznej (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tematu. |0 (domyślnie), 1, 2... |Nie |
| dla właściwości rejectType |Określa, czy opcja rejectValue jest określona jako wartość literału lub wartość procentowa. |Wartość (ustawienie domyślne), wartość procentowa |Nie |
| rejectSampleValue |Określa liczbę wierszy do pobrania przed PolyBase ponownie oblicza procent odrzuconych wierszy. |1, 2, … |Tak, jeśli **dla właściwości rejectType** jest **procent** |
| useTypeDefault |Określa sposób obsługi brakujących wartości w rozdzielane pliki tekstowe, jeśli PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej o tej właściwości z sekcji argumenty w [utworzyć EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Wartość true, False (ustawienie domyślne) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. |Zakres czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |

#### <a name="sqldwsink-example"></a>Przykład SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Użyj programu PolyBase, aby załadować dane do magazynu danych SQL Azure
Przy użyciu **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** jest wydajny sposób ładowania dużych ilości danych do magazynu danych SQL Azure z wysokiej przepływności. Przy użyciu programu PolyBase zamiast domyślnego mechanizmu BULKINSERT widać duże korzyści w przepływności. Zobacz [skopiuj numer odwołania wydajności](data-factory-copy-activity-performance.md#performance-reference) z szczegółowe porównanie. Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](data-factory-load-sql-data-warehouse.md).

* Jeśli źródło danych jest w **obiektów Blob platformy Azure lub usługi Azure Data Lake Store**i format jest zgodny z PolyBase, można skopiować bezpośrednio do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase. Zobacz **[bezpośrednich kopii przy użyciu programu PolyBase](#direct-copy-using-polybase)** ze szczegółami.
* Jeśli Twoje źródła magazynu danych i format nie jest początkowo obsługiwana przez aparat PolyBase, możesz użyć **[przemieszczane kopiowania przy użyciu programu PolyBase](#staged-copy-using-polybase)** funkcji zamiast tego. Udostępnia również możesz lepszą przepustowość automatycznie konwersji danych do formatu zgodnego PolyBase i przechowywanie danych w magazynie obiektów Blob platformy Azure. Następnie ładuje dane do usługi SQL Data Warehouse.

Ustaw `allowPolyBase` właściwości **true** jak pokazano w poniższym przykładzie dla fabryki danych Azure, skopiuj dane do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase. Podczas allowPolyBase jest ustawiona na wartość true, można określić za pomocą właściwości specyficzne dla programu PolyBase `polyBaseSettings` grupy właściwości. zobacz [SqlDWSink](#SqlDWSink) sekcji, aby uzyskać więcej informacji o właściwościach, które mogą korzystać z usługi.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Bezpośrednie kopiowania przy użyciu programu PolyBase
Aparat PolyBase magazynu danych SQL obsługuje bezpośrednio obiektów Blob platformy Azure i usługi Azure Data Lake Store (przy użyciu nazwy głównej usługi) jako źródło i wymagania format określonego pliku. Jeśli źródło danych spełnia kryteria opisane w tej sekcji, możesz bezpośrednio skopiować z magazynu danych źródła do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase. W przeciwnym razie można użyć [przemieszczane kopiowania przy użyciu programu PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Aby skopiować dane z usługi Data Lake Store SQL Data Warehouse wydajnie, Dowiedz się więcej o [fabryki danych Azure ułatwia nawet wygodny do ujawniania wgląd w dane dotyczące danych podczas korzystania z usługi Data Lake Store z usługi SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Jeśli nie zostały spełnione wymagania, fabryki danych Azure sprawdza ustawienia i automatycznie powraca do mechanizmu BULKINSERT przenoszenia danych.

1. **Źródło połączona usługa** jest typu: **AzureStorage** lub **AzureDataLakeStore z uwierzytelnianiem główna usługi**.  
2. **Wejściowy zestaw danych** jest typu: **AzureBlob** lub **AzureDataLakeStore**i wpisz w formacie `type` właściwości **OrcFormat**, **ParquetFormat**, lub **TextFormat** z następujących konfiguracji:

   1. `rowDelimiter` musi być **\n**.
   2. `nullValue` ustawiono **pusty ciąg** (""), lub `treatEmptyAsNull` ustawiono **true**.
   3. `encodingName` ustawiono **utf-8**, która jest **domyślne** wartość.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, i `skipLineCount` nie zostały określone.
   5. `compression` może być **bez kompresji**, **GZip**, lub **Deflate**.

    ```JSON
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",     
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",       
           "nullValue": "",           
           "encodingName": "utf-8"    
       },
       "compression": {  
           "type": "GZip",  
           "level": "Optimal"  
       }  
    },
    ```

3. Brak nie `skipHeaderLineCount` w obszarze **BlobSource** lub **AzureDataLakeStore** dla działania kopiowania w potoku.
4. Brak nie `sliceIdentifierColumnName` w obszarze **SqlDWSink** dla działania kopiowania w potoku. (PolyBase gwarantuje, że wszystkie dane są aktualizowane lub nic nie jest aktualizowana w jednym przebiegu. Aby osiągnąć **powtarzalności**, można użyć `sqlWriterCleanupScript`).
5. Brak nie `columnMapping` używane w skojarzonych w kopii działania.

### <a name="staged-copy-using-polybase"></a>Kopiuj przygotowanego przy użyciu programu PolyBase
Źródło danych nie spełnia kryteriów wprowadzone w poprzedniej sekcji, umożliwia kopiowanie danych za pośrednictwem tymczasowego przemieszczania magazynu obiektów Blob Azure (nie może być magazyn w warstwie Premium). W takim przypadku fabryki danych Azure automatycznie dokonuje przekształcenia danych spełnia wymagania dotyczące formatu danych PolyBase, a następnie użyj programu PolyBase, aby załadować dane do usługi SQL Data Warehouse i na ostatnich oczyszczania tymczasowego danych z magazynu obiektów Blob. Zobacz [przemieszczane kopiowania](data-factory-copy-activity-performance.md#staged-copy) szczegółowe informacje na temat jak kopiowanie danych za pośrednictwem tymczasowych obiektów Blob platformy Azure działa na ogół.

> [!NOTE]
> Podczas kopiowania danych z danych z lokalnego magazynu do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase i przemieszczania, jeśli wersja bramy zarządzania danymi znajduje się poniżej 2.4 środowiska JRE (Java Runtime Environment) jest wymagana na komputerze bramy, służący do przekształcania danych źródłowych właściwego formatu. Sugerują, że uaktualnienie bramy do najnowszej wersji w celu uniknięcia takiej zależności.
>

Aby użyć tej funkcji, należy utworzyć [połączonej usługi magazynu Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) odwołujący się do konta magazynu Azure, które ma magazynu tymczasowego obiektu blob, następnie określ `enableStaging` i `stagingSettings` właściwości dla działania kopiowania, jak pokazano w poniższym kodzie:

```json
"activities":[  
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Najlepsze rozwiązania w sytuacji, gdy przy użyciu programu PolyBase
Poniższe sekcje zawierają dodatkowe wskazówki te, które są wymienione w [najlepsze rozwiązania dotyczące usługi Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Uprawnienia wymagane bazy danych
Aby użyć programu PolyBase, wymaga użytkownika używane do ładowania danych do usługi SQL Data Warehouse [uprawnienia "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) w docelowej bazie danych. Jest jednym ze sposobów osiągnięcia, które można dodać tego użytkownika jako członka roli "db_owner". Dowiedz się, jak to zrobić, postępując [w tej sekcji](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Rozmiar wiersza i danych typu ograniczenia
Program Polybase obciążenia są ograniczone do ładowania wierszy, zarówno mniejszy niż **1 MB** i nie można załadować VARCHR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX). Zapoznaj się [tutaj](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Jeśli masz dane źródłowe z wierszami o rozmiarze większym niż 1 MB, można podzielić tabel źródłowych w pionie na kilka małych sieci, gdy największy rozmiar wiersza dla każdego z nich nie przekracza limit. Mniejsze tabele następnie można załadować przy użyciu programu PolyBase i scalane w usłudze Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Klasa zasobów magazynu danych SQL
Aby uzyskać najlepsze możliwe przepływności, należy wziąć pod uwagę można przypisać większą klasa zasobów do użytkownika używane do ładowania danych do usługi SQL Data Warehouse przy użyciu programu PolyBase. Dowiedz się, jak to zrobić, postępując [zmienić przykład klasy zasobów użytkownika](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName w magazynie danych SQL Azure
Poniższa tabela zawiera przykłady dotyczące sposobu określania **tableName** właściwość w zestawie danych JSON dla różnych kombinacji nazwy schematu i tabeli.

| Schemat bazy danych | Nazwa tabeli | Właściwość tableName JSON |
| --- | --- | --- |
| dbo |MyTable |MyTable lub dbo. MyTable lub [dbo]. [MyTable] |
| dbo1 |MyTable |dbo1. MyTable lub [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] lub [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Jeśli zostanie wyświetlony następujący błąd, może to być problem z wartość określona dla właściwości tableName. Poniższa tabela dla poprawne sposobu na określenie wartości dla właściwości tableName JSON.  

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumn z wartościami domyślnymi
Obecnie funkcja PolyBase w fabryce danych akceptuje tylko taką samą liczbę kolumn w tabeli docelowej. Przykład tabeli z kolumnami cztery i jeden z nich jest zdefiniowana z wartością domyślną. Dane wejściowe nadal powinien zawierać cztery kolumny. Udostępnia zestaw danych wejściowych 3 kolumny spowoduje uzyskanie błąd podobny do następującego:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Wartość NULL jest specjalny rodzaj wartości domyślnej. W przypadku wartości Null kolumny danych wejściowych (w obiekcie blob) dla tej kolumny może być pusta (nie może być brakuje wejściowy zestaw danych). Program PolyBase wstawia wartość NULL w przypadku ich w magazynie danych SQL Azure.  

## <a name="auto-table-creation"></a>Automatyczne tworzenie tabeli
Jeśli używasz kreatora kopiowania, aby skopiować dane z serwera SQL lub bazy danych SQL Azure do usługi Azure SQL Data Warehouse, tabeli, która odpowiada tabela źródłowa nie istnieje w magazynie docelowym fabryki danych może automatycznie tworzyć tabeli w magazynie danych przy użyciu schematu tabeli źródłowej.

Fabryka danych tworzy tabeli w magazynie docelowym o takiej samej nazwie tabeli w magazynie źródła danych. Typy danych kolumn są wybierane w oparciu następujące mapowania typu. W razie potrzeby wykonuje konwersje typów, aby rozwiązać wszelkie niezgodności między magazynami źródłowym i docelowym. Używa okrężnego tabeli dystrybucji.

| Typ kolumny źródłowej bazy danych SQL | Typ kolumny docelowej magazynu danych SQL (limit rozmiaru) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| bitowe | bitowe |
| Decimal | Decimal |
| Liczbowy | Decimal |
| Liczba zmiennoprzecinkowa | Liczba zmiennoprzecinkowa |
| oszczędność pieniędzy | oszczędność pieniędzy |
| Real | Real |
| SmallMoney | SmallMoney |
| Binarny | Binarny |
| varbinary | Varbinary (maksymalnie 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Tekst | Varchar (maksymalnie 8000) |
| NText | NVarChar (maksymalnie 4000) |
| Image (Obraz) | VarBinary (maksymalnie 8000) |
| UniqueIdentifier | UniqueIdentifier |
| char | char |
| NChar | NChar |
| VarChar | VarChar (maksymalnie 8000) |
| NVarChar | NVarChar (maksymalnie 4000) |
| Xml | Varchar (maksymalnie 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapowanie typu dla usługi Azure SQL Data Warehouse
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, wykonuje działanie kopiowania automatyczne konwersje z typów źródła do zbiornika typów o następujące podejście krok 2:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych do i z usługi Azure SQL Data Warehouse, następujące mapowania są używane z typu SQL typ architektury .NET i na odwrót.

Mapowanie jest taka sama jak [mapowanie typu danych serwera SQL dla ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Typ aparatu bazy danych programu SQL Server | Typ programu .NET framework |
| --- | --- |
| bigint |Int64 |
| Binarne |Byte[] |
| bitowe |Wartość logiczna |
| char |Ciąg, Char] |
| data |DateTime |
| Data/godzina |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atrybut FILESTREAM (varbinary(max)) |Byte[] |
| Liczba zmiennoprzecinkowa |O podwójnej precyzji |
| Obraz |Byte[] |
| int |Int32 |
| oszczędność pieniędzy |Decimal |
| nchar |Ciąg, Char] |
| ntext |Ciąg, Char] |
| numeryczne |Decimal |
| nvarchar |Ciąg, Char] |
| rzeczywiste |Pojedyncze |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Obiekt * |
| tekst |Ciąg, Char] |
| time |Zakres czasu |
| sygnatura czasowa |Byte[] |
| tinyint |Bajtów |
| Unikatowy identyfikator |Identyfikator GUID |
| varbinary |Byte[] |
| varchar |Ciąg, Char] |
| xml |Xml |

Można również mapować kolumn z zestawu źródła danych do kolumn z zestawu danych zbiornika w definicji działania kopiowania. Aby uzyskać więcej informacji, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Przykłady JSON do kopiowania danych z magazynu danych SQL
Poniższe przykłady zapewniają definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych do i z usługi Azure SQL Data Warehouse i magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Przykład: Kopiowanie danych z magazynu danych SQL Azure do obiektów Blob platformy Azure
Przykład definiuje następujące jednostek fabryki danych:

1. Połączonej usługi typu [AzureSqlDW](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [SqlDWSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje szeregów czasowych (co godzinę, codziennie, itp.) dane z tabeli w bazie danych Azure SQL Data Warehouse do obiektu blob co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Usługa Azure SQL Data Warehouse połączonej usługi:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Magazyn obiektów Blob Azure połączonej usługi:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Usługa Azure SQL Data Warehouse wejściowy zestaw danych:**

Przykładzie przyjęto założenie, utworzono tabelę "MyTable" w magazynie danych SQL Azure i zawiera kolumnę o nazwie "timestampcolumn" dla czasu serii danych.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Działanie kopiowania w potoku z SqlDWSource i BlobSink:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **SqlDWSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **SqlReaderQuery** właściwości wybiera dane w ostatniej godziny do skopiowania.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
> [!NOTE]
> W tym przykładzie **sqlReaderQuery** dla SqlDWSource został określony. Działanie kopiowania uruchamia to zapytanie względem źródła magazynu danych SQL Azure umożliwiają pobieranie danych.
>
> Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).
>
> Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury zestawu danych JSON służą do skonstruowania zapytania (Wybierz Kolumna1, Kolumna2 z mytable) w celu uruchomienia usługi Azure SQL Data Warehouse. Jeśli definicji zestawu danych nie ma on struktury, wszystkie kolumny są wybierane w tabeli.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Przykład: Kopiowanie danych z obiektu Blob Azure do usługi Azure SQL Data Warehouse
Przykład definiuje następujące jednostek fabryki danych:

1. Połączonej usługi typu [AzureSqlDW](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [SqlDWSink](#copy-activity-properties).

Kopie próbki szeregów czasowych danych (co godzinę, codziennie, itp.) z platformy Azure blob do tabeli w usłudze Azure SQL Data Warehouse bazy danych co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Usługa Azure SQL Data Warehouse połączonej usługi:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Magazyn obiektów Blob Azure połączonej usługi:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure wejściowego zestawu danych obiektów Blob:**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu korzysta rok, miesiąc i dzień część czas rozpoczęcia, a nazwa pliku godzina część czas rozpoczęcia. "external": ustawienie "prawda" usługi fabryka danych informuje, że w tej tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Usługa Azure SQL Data Warehouse wyjściowy zestaw danych:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w usłudze Azure SQL Data Warehouse. Tworzenie tabeli w magazynie danych SQL Azure z taką samą liczbę kolumn zgodnie z oczekiwaniami pliku Blob CSV zawiera. Nowe wiersze są dodawane do tabeli co godzinę.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Działanie kopiowania w potoku z BlobSource i SqlDWSink:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **BlobSource** i **zbiornika** ustawiono typ **SqlDWSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
Aby uzyskać wskazówki, zobacz zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](data-factory-load-sql-data-warehouse.md) i [ładowanie danych z fabryką danych Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artykułu w dokumentacji usługi Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
