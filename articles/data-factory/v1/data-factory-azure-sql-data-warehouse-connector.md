---
title: Kopiowanie danych do i z usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: dfd0443dafbc4fcc221937f248bf6d2f292b528f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335404"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika usługi Azure SQL Data Warehouse w wersji 2](../connector-azure-sql-data-warehouse.md).

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z usługi Azure SQL Data Warehouse. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

> [!TIP]
> Aby uzyskać najlepszą wydajność, ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase. [Przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) sekcja zawiera szczegółowe informacje. Aby uzyskać wskazówki z przypadkami użycia, zobacz [ładowanie 1 TB w usłudze Azure SQL Data Warehouse z mniej niż 15 minut przy użyciu usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z usługi Azure SQL Data Warehouse** się następujące dane są przechowywane:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących magazynów danych **do usługi Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Podczas kopiowania danych z programu SQL Server lub usługi Azure SQL Database do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, Data Factory może automatycznie tworzyć tabeli w usłudze SQL Data Warehouse przy użyciu schematu tabeli w magazynie danych źródłowych. Zobacz [automatyczne tworzenie tabeli](#auto-table-creation) Aby uzyskać szczegółowe informacje.

## <a name="supported-authentication-type"></a>Obsługiwany typ uwierzytelniania
Usługa Azure SQL Data Warehouse łącznika obsługę uwierzytelniania podstawowego.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z usługi Azure SQL Data Warehouse przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku, który kopiuje dane z usługi Azure SQL Data Warehouse jest użycie Kreatora kopiowania danych. Zobacz [samouczka: Ładowanie danych do usługi SQL Data Warehouse przy użyciu usługi Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków. 
2. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych. Na przykład jeśli kopiujesz dane z usługi Azure blob storage do usługi Azure SQL data warehouse, utworzysz dwie połączone usługi, aby połączyć swoje konto magazynu platformy Azure i usługi Azure SQL data warehouse z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji. 
3. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. W tym przykładzie wymienione w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob oraz folder, który zawiera dane wejściowe. I utwórz inny zestaw danych, aby określić tabelę w magazynie danych Azure SQL, która przechowuje dane skopiowane z magazynu obiektów blob. Aby uzyskać właściwości zestawu danych, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. W przykładzie, o których wspomniano wcześniej możesz użyć BlobSource jako źródła i SqlDWSink jako obiekt sink dla działania kopiowania. Podobnie Azure SQL Data Warehouse są kopiowane do usługi Azure Blob Storage, należy użyć SqlDWSource i BlobSink w działaniu kopiowania. Właściwości działania kopiowania, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [właściwości działania kopiowania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje na temat korzystania z magazynu danych jako źródła lub ujścia kliknij link w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Aby uzyskać przykłady przy użyciu definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z usługi Azure SQL Data Warehouse, zobacz [JSON przykłady](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania określonych jednostek usługi fabryka danych Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis specyficzne dla usługi Azure SQL Data Warehouse, połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **AzureSqlDW** |Yes |
| connectionString |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Data Warehouse dla właściwości connectionString. Obsługiwane jest tylko uwierzytelnianie podstawowe. |Yes |

> [!IMPORTANT]
> Konfigurowanie [zapory bazy danych SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) a serwerem bazy danych do [Zezwalaj usługom Azure na dostęp do serwera](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ponadto jeśli dane są kopiowane do usługi Azure SQL Data Warehouse z zewnętrznej platformy Azure w tym z lokalnymi źródłami danych za pomocą bramy fabryki danych, należy skonfigurować odpowiedni zakres adresów IP dla maszyny, która wysyła dane do usługi Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

W sekcji typeProperties różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcji dla zestawu danych typu **AzureSqlDWTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w bazie danych Azure SQL Data Warehouse, których połączona usługa przywołuje. |Yes |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden zestaw danych wejściowych i tworzy tylko jedno wyjście.

Natomiast właściwości, które są dostępne w sekcji typeProperties działania zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

### <a name="sqldwsource"></a>SqlDWSource
Jeśli źródło jest typu **SqlDWSource**, następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** określono SqlDWSource uruchomieniu działania kopiowania to zapytanie względem źródła usługi Azure SQL Data Warehouse, aby uzyskać dane.

Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji strukturę danych JSON są używane w celu skonstruowania zapytania w celu uruchomienia usługi Azure SQL Data Warehouse. Przykład: `select column1, column2 from mytable`. Definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

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
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że po oczyszczeniu danych określonego wycinka. Aby uzyskać więcej informacji, zobacz [powtarzalność części](#repeatability-during-copy). |Instrukcja zapytania. |Nie |
| allowPolyBase |Wskazuje, czy do korzystania z technologii PolyBase (jeśli ma zastosowanie) zamiast mechanizmu BULKINSERT. <br/><br/> **Przy użyciu technologii PolyBase jest zalecany sposób ładowania danych do usługi SQL Data Warehouse.** Zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sekcji ograniczeń i szczegółów. |True <br/>FALSE (domyślnie) |Nie |
| polyBaseSettings |Grupa właściwości, które może być określony, gdy **allowPolybase** właściwość jest ustawiona na **true**. |&nbsp; |Nie |
| rejectValue |Określa liczbę lub wartość procentowa wierszy, które można odrzucić przed zapytanie nie powiedzie się. <br/><br/>Dowiedz się więcej na temat opcji odrzucania programu PolyBase w **argumenty** części [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tematu. |0 (domyślnie), 1, 2... |Nie |
| rejectType |Określa, czy opcja rejectValue jest określony jako wartość literału lub wartości procentowej. |Wartość (ustawienie domyślne), wartość procentowa |Nie |
| rejectSampleValue |Określa liczbę wierszy do pobrania przed programu PolyBase ponownie oblicza odsetek odrzuconych wierszy. |1, 2, … |Tak, jeśli **rejectType** jest **procent** |
| useTypeDefault |Określa sposób obsługi brakujących wartości w rozdzielanych plików tekstowych, jeśli funkcja PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej na temat tej właściwości z sekcji argumentów w [tworzenie EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Wartość true, False (domyślnie) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania. |TimeSpan<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |

#### <a name="sqldwsink-example"></a>SqlDWSink example

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase
Za pomocą **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** jest skuteczny sposób ładowania dużych ilości danych do usługi Azure SQL Data Warehouse o wysokiej przepływności. Widać duże korzyści przepływności przy użyciu programu PolyBase zamiast domyślnego mechanizmu BULKINSERT. Zobacz [skopiuj numer referencyjny wydajności](data-factory-copy-activity-performance.md#performance-reference) przy użyciu szczegółowego porównania. Aby uzyskać wskazówki z przypadkami użycia, zobacz [ładowanie 1 TB w usłudze Azure SQL Data Warehouse z mniej niż 15 minut przy użyciu usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Jeśli źródło danych znajduje się w **Azure blob Storage lub Azure Data Lake Store**i format jest zgodny z programem PolyBase, możesz bezpośrednio skopiować do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase. Zobacz **[bezpośrednich kopii przy użyciu technologii PolyBase](#direct-copy-using-polybase)** ze szczegółowymi informacjami.
* Usługi magazynu danych źródłowych i format nie jest pierwotnie obsługiwane przez program PolyBase, można użyć **[przygotowane kopiowania przy użyciu technologii PolyBase](#staged-copy-using-polybase)** są wyposażone w zamian. Zapewnia również możesz większą przepustowość przez automatyczne konwersji danych do formatu zgodnego z technologii PolyBase i przechowywanie danych w usłudze Azure Blob storage. Następnie ładuje dane do usługi SQL Data Warehouse.

Ustaw `allowPolyBase` właściwości **true** jak pokazano w poniższym przykładzie dla usługi Azure Data Factory, aby skopiować dane do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase. Podczas allowPolyBase jest ustawiona na wartość true, można określić właściwości określonych technologii PolyBase, za pomocą `polyBaseSettings` grupy właściwości. zobacz [SqlDWSink](#sqldwsink) sekcji, aby uzyskać szczegółowe informacje o właściwościach, korzystających z usługi.

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

### <a name="direct-copy-using-polybase"></a>Bezpośrednie kopiowania przy użyciu technologii PolyBase
Program PolyBase magazynu danych SQL bezpośrednio obsługują obiektów Blob platformy Azure i usługi Azure Data Lake Store (przy użyciu nazwy głównej usługi) jako źródło i z wymaganiami format określonego pliku. Jeśli źródło danych spełnia kryteria opisane w tej sekcji, możesz bezpośrednio skopiować z magazynu danych źródłowych do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase. W przeciwnym razie można użyć [przygotowane kopiowania przy użyciu technologii PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Aby skopiować dane z Data Lake Store do usługi SQL Data Warehouse wydajnie, Dowiedz się więcej z [usługi Azure Data Factory umożliwia jeszcze łatwiejsze i wygodne uzyskiwanie szczegółowych informacji z danych, korzystając z programu Data Lake Store z usługą SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Jeśli nie są spełnione wymagania, Azure Data Factory sprawdza ustawienia i automatycznie powraca do mechanizmu BULKINSERT w przypadku przenoszenia danych.

1. **Źródło połączoną usługę** typu: **AzureStorage** lub **AzureDataLakeStore przy użyciu uwierzytelniania jednostki usługi**.
2. **Wejściowego zestawu danych** typu: **AzureBlob** lub **AzureDataLakeStore**i format, wpisz w obszarze `type` właściwości jest **OrcFormat**, **ParquetFormat**, lub **TextFormat** skonfigurowane w następujący sposób:

   1. `rowDelimiter` musi być **\n**.
   2. `nullValue` ustawiono **pusty ciąg** (""), lub `treatEmptyAsNull` ustawiono **true**.
   3. `encodingName` ustawiono **utf-8**, czyli **domyślne** wartość.
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

3. Istnieje nie `skipHeaderLineCount` w obszarze **BlobSource** lub **AzureDataLakeStore** dla działania kopiowania w potoku.
4. Istnieje nie `sliceIdentifierColumnName` w obszarze **SqlDWSink** dla działania kopiowania w potoku. (Funkcja PolyBase gwarantuje, że wszystkie dane są aktualizowane lub nic nie zostanie zaktualizowany w jednym przebiegu. Aby osiągnąć **powtarzalności**, można użyć `sqlWriterCleanupScript`).
5. Istnieje nie `columnMapping` używany w kopii skojarzone w działanie.

### <a name="staged-copy-using-polybase"></a>Kopiowania przejściowego przy użyciu technologii PolyBase
Źródło danych nie spełnia kryteria, wprowadzona w poprzedniej sekcji, umożliwia kopiowanie danych za pośrednictwem tymczasowego przemieszczania usługi Azure Blob Storage (nie może być magazyn w warstwie Premium). W tym przypadku usługi Azure Data Factory automatycznie wykonuje przekształcenia na danych, które spełniają wymagania dotyczące formatu danych PolyBase, a następnie przy użyciu technologii PolyBase do ładowania danych do usługi SQL Data Warehouse i na ostatnich oczyszczania temp dane z magazynu obiektów Blob. Zobacz [kopiowania etapowego](data-factory-copy-activity-performance.md#staged-copy) Aby uzyskać szczegółowe informacje na temat kopiowania danych za pośrednictwem przejściowego magazynu obiektów Blob Azure działania ogólnie rzecz biorąc.

> [!NOTE]
> Podczas kopiowania danych z lokalnych do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase magazynu danych i przemieszczania, jeśli jest w wersji bramy zarządzania danymi poniżej 2.4 środowiska JRE (Java Runtime Environment) jest wymagany na maszynie bramy, która jest używana do przekształcania źródła dane do właściwego formatu. Sugerują, że uaktualnienie bramy do najnowszej wersji w celu uniknięcia takie zależności.
>

Aby użyć tej funkcji, należy utworzyć [połączonej usługi Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) odwołujący się do konta magazynu platformy Azure, które ma magazynu tymczasowego obiektu blob, a następnie wprowadź `enableStaging` i `stagingSettings` właściwości dla działania kopiowania, jak pokazano w Poniższy kod:

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

## <a name="best-practices-when-using-polybase"></a>Najlepsze rozwiązania w przypadku przy użyciu technologii PolyBase
Poniższe sekcje zawierają dodatkowe najlepsze rozwiązania z tymi, które są wymienione w [najlepsze rozwiązania dotyczące usługi Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Uprawnienia wymagane bazy danych
Aby korzystać z technologii PolyBase, wymaga użytkownika używana do ładowania danych do usługi SQL Data Warehouse ma [uprawnienie "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) do docelowej bazy danych. Jest jednym ze sposobów osiągnięcia, które można dodać tego użytkownika jako członka roli "db_owner". Dowiedz się, jak to zrobić, wykonując [w tej sekcji](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Ograniczenie typu rozmiar wiersza i danych
Obciążenia funkcji Polybase są ograniczone do ładowania wierszy, zarówno mniejszy niż **1 MB** i nie można załadować VARCHR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX). Zapoznaj się [tutaj](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Jeśli dane źródłowe z wierszami o rozmiarze większym niż 1 MB, warto podzielić tabel źródłowych w pionie kilka małych sieci, gdy największy rozmiar wiersza dla każdego z nich nie przekracza limit. Tabele mniejszych następnie może być załadowany, przy użyciu technologii PolyBase i scalane w usłudze Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Klasa zasobów SQL Data Warehouse
Aby osiągnąć najlepsze możliwe przepływność, należy wziąć pod uwagę można przypisać większą klasę zasobów do użytkownika używana do ładowania danych do usługi SQL Data Warehouse za pomocą programu PolyBase. Dowiedz się, jak to zrobić, wykonując [zmienić przykład klasy zasobów użytkownika](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>Właściwość tableName w usłudze Azure SQL Data Warehouse
Poniższa tabela zawiera przykłady dotyczące sposobu określania **tableName** właściwość w zestawie danych JSON dla różnych kombinacji nazwy schematu i tabeli.

| Schemat bazy danych | Nazwa tabeli | Właściwość JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable lub dbo. MyTable lub [dbo]. [MyTable] |
| dbo1 |MyTable |dbo1. MyTable lub [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] lub [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Jeśli zostanie wyświetlony następujący błąd, może to być problem z wartością, która została określona jako właściwość tableName. Zobacz tabelę w prawidłowy sposób określić wartości dla właściwości JSON tableName.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumny z wartościami domyślnymi
Obecnie funkcja PolyBase w usłudze Data Factory akceptuje tylko tę samą liczbę kolumn w tabeli docelowej. Załóżmy, że, zawierać tabelę z czterech kolumn i jeden z nich jest zdefiniowana za pomocą wartości domyślnej. Dane wejściowe nadal powinien zawierać cztery kolumny. Zapewnianie wejściowy zestaw danych 3-kolumnowy dałaby błąd podobny do następującego:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Wartość NULL jest specjalną forma wartości domyślnej. Jeśli kolumna ma wartość null, danych wejściowych (w obiekcie blob) dla tej kolumny może być pusta (nie może być brakuje wejściowego zestawu danych). Program PolyBase wstawia wartość NULL w przypadku ich w usłudze Azure SQL Data Warehouse.

## <a name="auto-table-creation"></a>Automatyczne tworzenie tabeli
Jeśli używasz kreatora kopiowania do skopiowania danych z programu SQL Server lub usługi Azure SQL Database do usługi Azure SQL Data Warehouse i tabelę, która odnosi się do tabeli źródłowej nie istnieje w magazynie docelowym, Data Factory może automatycznie tworzyć tabeli w magazynie danych, u SING schematu tabeli źródłowej.

Data Factory tworzy tabelę w magazynie docelowym o takiej nazwie tabeli w magazynie danych źródłowych. Typy danych w kolumnach są wybierane w oparciu o następujące mapowania typów. Jeśli to konieczne, wykonuje konwersje typów, aby naprawić wszelkie niezgodności między źródłowym i docelowym magazynami. Korzysta również okrężna Dystrybucja tabel.

| Typ kolumny bazy danych SQL źródła | Typ kolumny SQL DW docelowego (ograniczenie rozmiaru) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Decimal | Decimal |
| Numeric | Decimal |
| Float | Float |
| money | money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binarny | Binarny |
| Varbinary | Varbinary (maksymalnie 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (maksymalnie 8000) |
| NText | NVarChar (maksymalnie 4000) |
| Image | VarBinary (maksymalnie 8000) |
| UniqueIdentifier | UniqueIdentifier |
| char | char |
| NChar | NChar |
| VarChar | VarChar (maksymalnie 8000) |
| NVarChar | NVarChar (maksymalnie 4000) |
| Xml | Varchar (maksymalnie 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapowanie typu dla usługi Azure SQL Data Warehouse
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typów przy użyciu następujących podejść krok 2:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych do i z usługi Azure SQL Data Warehouse, następujące mapowania są używane z typu SQL na typ architektury .NET i na odwrót.

Mapowanie jest taka sama jak [mapowanie typu danych SQL Server, ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Typ aparatu bazy danych programu SQL Server | Typ .NET framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| Numeryczne |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| tekst |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| Varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

Można również mapować kolumny z zestawu danych źródłowych do kolumn z zestaw danych ujścia w definicji działania kopiowania. Aby uzyskać więcej informacji, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Kopiowanie danych do i z usługi SQL Data Warehouse JSON przykłady
W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak kopiować dane do i z usługi Azure SQL Data Warehouse i Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Przykład: Kopiowanie danych z usługi Azure SQL Data Warehouse do obiektów Blob platformy Azure
Przykładowa aplikacja definiuje następujących jednostek usługi Data Factory:

1. Połączonej usługi typu [AzureSqlDW](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [SqlDWSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy skrypt kopiuje danych szeregów czasowych (co godzinę, codziennie, itp.) z tabeli w bazie danych Azure SQL Data Warehouse do obiektu blob na godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Usługa Azure SQL Data Warehouse połączoną usługę:**

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
**Połączona usługa Azure Blob storage:**

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

Przykład przyjęto założenie, utworzono tabelę "MyTable" w usłudze Azure SQL Data Warehouse i zawiera kolumnę o nazwie "timestampcolumn" w danych szeregów czasowych.

Ustawienie "external": "true" informuje usługa Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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
**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

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

**Działanie kopiowania w potoku za pomocą SqlDWSource i BlobSink:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **SqlDWSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **SqlReaderQuery** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

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
> W tym przykładzie **sqlReaderQuery** dla SqlDWSource został określony. Działanie kopiowania uruchamia to zapytanie względem źródła usługi Azure SQL Data Warehouse, aby uzyskać dane.
>
> Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).
>
> Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji strukturę danych JSON są używane w celu skonstruowania zapytania (Wybierz Kolumna1, Kolumna2 z mytable) w celu uruchomienia usługi Azure SQL Data Warehouse. Definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Przykład: Kopiowanie danych z obiektów Blob platformy Azure do usługi Azure SQL Data Warehouse
Przykładowa aplikacja definiuje następujących jednostek usługi Data Factory:

1. Połączonej usługi typu [AzureSqlDW](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [SqlDWSink](#copy-activity-properties).

Kopie przykładowe szeregów czasowych danych (co godzinę, codziennie itp.) z blob Azure do tabeli w usłudze Azure SQL Data Warehouse, bazy danych co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Usługa Azure SQL Data Warehouse połączoną usługę:**

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
**Połączona usługa Azure Blob storage:**

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
**Usługa Azure wejściowy zestaw danych obiektów Blob:**

Dane są pobierane z nowy obiekt blob co godzinę (frequency: godzina, interwał: 1). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc i dzień godziny rozpoczęcia, a nazwa pliku używa godzinę część czas rozpoczęcia. "external": ustawienia "true" w usłudze Data Factory informuje, że ta tabela jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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

Przykład kopiuje dane do tabeli o nazwie "MyTable" w usłudze Azure SQL Data Warehouse. Tworzenie tabeli w usłudze Azure SQL Data Warehouse z taką samą liczbę kolumn, zgodnie z oczekiwaniami plik obiektów Blob CSV będzie zawierał. Nowe wiersze są dodawane do tabeli, co godzinę.

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
**Działanie kopiowania w potoku za pomocą BlobSource i SqlDWSink:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **BlobSource** i **ujścia** ustawiono typ **SqlDWSink**.

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
Aby uzyskać wskazówki, znajduje się w [ładowanie 1 TB w usłudze Azure SQL Data Warehouse z mniej niż 15 minut przy użyciu usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md) i [ładowanie danych za pomocą usługi Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artykułu w dokumentacji usługi Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
