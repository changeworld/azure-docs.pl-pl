---
title: Kopiowanie danych do i z usługi Azure Blob Storage | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak kopiować dane obiektów blob w usłudze Azure Data Factory. Skorzystaj z naszego przykładu: Jak skopiować dane do i z usługi Azure Blob Storage i Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 85832abeb9908dd891e3f35a0368bc35c7816a6e
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528224"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Blob Storage przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-azure-blob-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-blob-storage.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika usługi Azure Blob Storage w wersji 2](../connector-azure-blob-storage.md).


W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z usługi Azure Blob Storage. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

## <a name="overview"></a>Omówienie
Możesz skopiować dane z obsługiwanego źródłowego magazynu danych do usługi Azure Blob Storage lub Azure Blob Storage do dowolnego obsługiwanego magazynu danych ujścia. Poniższa tabela zawiera listę magazynów danych obsługiwanych jako źródła lub wychwytywanie przez działanie kopiowania. Na przykład dane można przenosić z **z** bazy danych programu SQL Server lub usługi Azure SQL database **do** usługi Azure blob storage. A może kopiować dane **z** usługi Azure blob storage **do** usługi Azure SQL Data Warehouse lub kolekcji usługi Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z usługi Azure Blob Storage** się następujące dane są przechowywane:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących magazynów danych **do usługi Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Działanie kopiowania obsługuje kopiowanie danych z i do konta usługi Azure Storage ogólnego przeznaczenia i magazynu obiektów Blob z warstwy gorąca/chłodna. Działanie obsługuje **odczytu z bloku, Dołącz lub stronicowe obiekty BLOB**, ale obsługuje **zapisywania tylko blokowe obiekty BLOB**. Usługa Azure Premium Storage nie jest obsługiwany jako ujście, ponieważ jest ona objęta stronicowych obiektów blob.
>
> Działanie kopiowania nie powoduje usunięcia danych ze źródła, po pomyślnie kopiowane są dane do lokalizacji docelowej. Jeśli potrzebujesz usunąć źródło danych po kopiowania zakończonego powodzeniem, należy utworzyć [niestandardowe działanie](data-factory-use-custom-activities.md) do usunięcia danych, a następnie użyć działania w potoku. Aby uzyskać przykład, zobacz [usuwania obiektów blob lub folderu przykład w witrynie GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Rozpoczęcie pracy
Utworzysz potok z działaniem kopiowania, które przenosi dane z usługi Azure Blob Storage przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Ten artykuł zawiera [wskazówki](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) do tworzenia potoku w celu kopiowania danych z lokalizacji usługi Azure Blob Storage do innej lokalizacji magazynu obiektów Blob platformy Azure. Samouczek dotyczący tworzenia potoku w celu skopiowania danych z usługi Azure Blob Storage do usługi Azure SQL Database, zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków.
2. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych. Na przykład jeśli kopiujesz dane z usługi Azure blob storage do usługi Azure SQL database, utworzysz dwie połączone usługi, aby połączyć Twoje konto usługi Azure storage i Azure SQL database z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla usługi Azure Blob Storage, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. W tym przykładzie wymienione w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob oraz folder, który zawiera dane wejściowe. I utwórz inny zestaw danych, aby określić tabelę SQL w bazie danych Azure SQL, która przechowuje dane skopiowane z magazynu obiektów blob. Aby uzyskać właściwości zestawu danych, które są specyficzne dla usługi Azure Blob Storage, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. W przykładzie, o których wspomniano wcześniej możesz użyć BlobSource jako źródła i SqlSink jako obiekt sink dla działania kopiowania. Podobnie jeśli kopiujesz z usługi Azure SQL Database do magazynu obiektów Blob platformy Azure, użyjesz SqlSource i BlobSink w działaniu kopiowania. Właściwości działania kopiowania, które są specyficzne dla usługi Azure Blob Storage, zobacz [właściwości działania kopiowania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje na temat korzystania z magazynu danych jako źródła lub ujścia kliknij link w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Aby uzyskać przykłady przy użyciu definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z usługi Azure Blob Storage, zobacz [JSON przykłady](#json-examples-for-copying-data-to-and-from-blob-storage  ) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory określonej usłudze Azure Blob Storage.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Istnieją dwa typy połączonych usług, które służy do łączenia usługi Azure Storage do usługi Azure data factory. Oto one: **AzureStorage** połączoną usługę i **AzureStorageSas** połączoną usługę. Połączona usługa Azure Storage udostępnia usługi data factory za pomocą globalnego dostępu do usługi Azure Storage. Natomiast połączonej usługi Azure Storage SAS (Shared Access Signature) usługa udostępnia usługi data factory z dostępem ograniczonym/czasowo do usługi Azure Storage. Nie istnieją żadne inne różnice między tymi dwoma usługami połączone. Wybierz połączonej usługi, która odpowiada Twoim potrzebom. Poniższe sekcje zawierają więcej szczegółów na tych dwóch połączonych usług.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych lub wyjściowych w usłudze Azure Blob Storage, należy ustawić właściwość typu zestawu danych na: **AzureBlob**. Ustaw **linkedServiceName** właściwości zestawu danych do nazwy usługi Azure Storage lub Azure Storage SAS połączoną usługę.  Określ właściwości typu zestawu **kontenera obiektów blob** i **folderu** w magazynie obiektów blob.

Aby uzyskać pełną listę sekcje JSON & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

Usługa Data factory obsługuje następujące wartości typu .NET zgodne ze specyfikacją CLS, na podstawie udostępnienie informacji o typie w "structure" dla źródeł danych schematu przy odczycie, np. obiektów blob platformy Azure: Int16, Int32, Int64, pojedynczy, Double, dziesiętny, ciąg [], Bool, Byte, identyfikator Guid, Datetime, Datetimeoffset, Timespan. Podczas przenoszenia danych z magazynu danych źródłowych do magazynu danych ujścia usługi Data Factory automatycznie wykonuje konwersje typów.

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji, itp., formatu danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **AzureBlob** zestawu danych ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do kontenera i folderu w magazynie obiektów blob. Przykład: myblobcontainer\myblobfolder\ |Yes |
| fileName |Nazwa obiektu blob. Nazwa pliku jest opcjonalny i wielkość liter.<br/><br/>Jeśli określisz parametr filename, aktywności (w tym kopiowania) działa na konkretny obiekt Blob.<br/><br/>Jeśli nie określono nazwy pliku, kopiowania obejmuje wszystkie obiekty BLOB w ścieżce folderu dla wejściowego zestawu danych.<br/><br/>Gdy **fileName** nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie została określona w ujścia działania nazwę wygenerowanego pliku będzie znajdować się w następujących tego formatu: `Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest opcjonalną właściwością. Służy do określania folderPath dynamiczne i nazwę pliku do danych szeregów czasowych. Na przykład folderPath mogą być parametryzowane za każdą godzinę danych. Zobacz [przy użyciu sekcji właściwości partitionedBy](#using-partitionedby-property) szczegółowe informacje i przykłady. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="using-partitionedby-property"></a>Przy użyciu właściwości partitionedBy
Jak wspomniano w poprzedniej sekcji, można określić folderPath dynamiczne i nazwę pliku dla danych szeregów czasowych z **partitionedBy** właściwości [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md).

Aby uzyskać więcej informacji na temat zestawów danych serii czasu, planowanie i wycinków, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) i [planowanie i wykonywanie](data-factory-scheduling-and-execution.md) artykułów.

#### <a name="sample-1"></a>Przykład 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W tym przykładzie {wycinek} jest zastępowana wartością zmiennej systemowej SliceStart fabryki danych w formacie (YYYYMMDDHH) określona. Parametru SliceStart odnosi się do rozpoczęcie wycinka. FolderPath różni się dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Przykład 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

W tym przykładzie rok, miesiąc, dzień i godzina SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i nazwę pliku.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań. Natomiast właściwości dostępnych w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia. Jeśli chcesz przenieść dane z usługi Azure Blob Storage, należy ustawić typ źródła w działaniu kopiowania, aby **BlobSource**. Podobnie, jeśli dane są przenoszone do usługi Azure Blob Storage, należy ustawić typ ujścia w działaniu kopiowania, aby **BlobSink**. Ta sekcja zawiera listę obsługiwanych przez BlobSource i BlobSink właściwości.

**BlobSource** obsługuje następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. |TRUE, False (wartość domyślna) |Nie |

**BlobSink** obsługuje następujące właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest BlobSource lub systemu plików. |<b>PreserveHierarchy</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają automatycznie wygeneruje nazwę. <br/><br/><b>MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu Blob jest określony, nazwa pliku scalonego będzie określoną nazwą; w przeciwnym razie może być nazwą pliku generowanych automatycznie. |Nie |

**BlobSource** obsługuje również te dwie właściwości zgodności z poprzednimi wersjami.

* **treatEmptyAsNull**: Określa, czy mają być traktowane null lub pusty ciąg jako wartość null.
* **skipHeaderLineCount** -Określa, ile wierszy muszą zostać pominięte. Dotyczy tylko kiedy wejściowego zestawu danych używa formatu TextFormat.

Podobnie **BlobSink** obsługuje następujące właściwości dla zgodności z poprzednimi wersjami.

* **blobWriterAddHeader**: Określa, czy należy dodać nagłówek definicje kolumn podczas zapisywania do wyjściowego zestawu danych.

Zestawy danych obsługuje teraz następujące właściwości, które implementują tę samą funkcjonalność: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Poniższa tabela zawiera wskazówki na temat korzystania z nowej właściwości zestawu danych, zamiast tych właściwości źródła/ujścia obiektu blob.

| Właściwości działania kopiowania | Właściwości zestawu danych |
|:--- |:--- |
| skipHeaderLineCount na BlobSource |skipLineCount, jak i firstRowAsHeader. Najpierw zostaną pominięte wiersze, a następnie odczytania pierwszy wiersz jako nagłówek. |
| treatEmptyAsNull na BlobSource |treatEmptyAsNull dla wejściowego zestawu danych |
| blobWriterAddHeader na BlobSink |firstRowAsHeader na wyjściowy zestaw danych |

Zobacz [Określanie formatu TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) sekcji, aby uzyskać szczegółowe informacje na temat tych właściwości.

### <a name="recursive-and-copybehavior-examples"></a>przykładów rekurencyjnych i copyBehavior
W tej sekcji opisano wynikowe zachowania operacji kopiowania różne kombinacje wartości cyklicznych i copyBehavior.

| cykliczne | copyBehavior | Wynikowe zachowania |
| --- | --- | --- |
| true |preserveHierarchy |Do folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródło<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Do folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File5 |
| true |mergeFiles |Do folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + 5 plików zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie plik |
| false |preserveHierarchy |Do folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy |Do folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/><br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |mergeFiles |Do folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie. Wygenerowany automatycznie nazwę plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Przewodnik: Kopiowanie danych z magazynu obiektów Blob za pomocą Kreatora kopiowania
Przyjrzyjmy się jak szybko skopiować dane z usługi Azure blob storage. W tym przewodniku magazyny danych w źródłowym i docelowym typu: Azure Blob Storage. Potok w tym przewodniku kopiuje dane z folderu do innego folderu, w tym samym kontenerze obiektów blob. W tym instruktażu jest celowo proste wyświetlić właściwości lub ustawienia w przypadku używania magazynu obiektów Blob jako źródła lub ujścia.

### <a name="prerequisites"></a>Wymagania wstępne
1. Tworzenie ogólnego przeznaczenia **konta usługi Azure Storage** Jeśli nie masz jeszcze takiego. Magazyn obiektów blob jest używany jako zarówno **źródła** i **docelowy** magazynu danych w tym przewodniku. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md).
2. Utwórz kontener obiektów blob o nazwie **adfblobconnector** na koncie magazynu.
4. Utwórz folder o nazwie **wejściowych** w **adfblobconnector** kontenera.
5. Utwórz plik o nazwie **emp.txt** za pomocą następujących zawartości i przekaż go do **wejściowych** folderu za pomocą narzędzi takich jak [Eksploratora usługi Azure Storage](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Tworzenie fabryki danych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób** w lewym górnym rogu, kliknij **rozwiązania inteligentne + analiza**i kliknij przycisk **usługi Data Factory**.
3. W **nowa fabryka danych** okienka:  
    1. Wprowadź **ADFBlobConnectorDF** dla **nazwa**. Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: `*Data factory name “ADFBlobConnectorDF” is not available`, Zmień nazwę fabryki danych (na przykład yournameADFBlobConnectorDF) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Dla grupy zasobów wybierz **Użyj istniejącej** wybierz istniejącą grupę zasobów (lub) wybierz **Utwórz nową** wprowadź nazwę grupy zasobów.
    4. Wybierz **lokalizację** fabryki danych.
    5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.
    6. Kliknij pozycję **Utwórz**.
3. Po zakończeniu tworzenia zostanie wyświetlony blok **Fabryka danych**, jak pokazano na poniższej ilustracji:  ![Strona główna fabryki danych](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Kreator kopiowania
1. Na stronie głównej fabryki danych kliknij **kopiowanie danych** Kafelek, aby uruchomić **kopiowania danych kreatora** w osobnej karcie.  
    
    > [!NOTE]
    > Jeśli widzisz, że przeglądarka sieci web jest zablokowana w "Autoryzowanie...", wyłącz/Usuń zaznaczenie **Blokuj pliki cookie innych firm i dane witryn** ustawienie (lub) pozostaw je włączone i Utwórz wyjątek dla **login.microsoftonline.com**, a następnie spróbuj ponownie uruchomić kreatora.
2. Na stronie **Właściwości**:
    1. Wprowadź **CopyPipeline** dla **Nazwa zadania**. Nazwa zadania jest nazwę potoku w fabryce danych.
    2. Wprowadź **opis** dla zadania (opcjonalnie).
    3. Dla **tempa zadania lub harmonogram zadań**, Zachowaj **regularnie uruchamiana zgodnie z harmonogramem** opcji. Jeśli chcesz uruchomić to zadanie tylko raz zamiast wykonywania wielokrotnie zgodnie z harmonogramem, wybierz opcję **uruchom raz teraz**. Jeśli zostanie wybrana, **uruchom raz teraz** opcji [potoku jednorazowego](data-factory-create-pipelines.md#onetime-pipeline) zostanie utworzony.
    4. Zachować ustawienia dla **wzorzec cyklicznie**. To zadanie jest uruchamiane codziennie od godziny rozpoczęcia i zakończenia, który określisz w następnym kroku.
    5. Zmiana **Data / Godzina rozpoczęcia** do **2017-04-21**.
    6. Zmiana **Data / Godzina zakończenia** do **2017-04-25**. Można wpisać datę zamiast przeglądanie za pomocą kalendarza.
    8. Kliknij przycisk **Dalej**.
        ![Narzędzie kopiowania — strona właściwości](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na stronie **Magazyn danych źródłowych** kliknij kafelek **Azure Blob Storage**. Ta strona służy do określania magazynu danych źródłowych do zadania kopiowania. Możesz użyć istniejącej połączonej usługi magazynu danych lub określić nowy magazyn danych. Aby użyć istniejącej połączonej usługi, należy wybrać **z istniejących POŁĄCZONYCH usług** i wybierz odpowiednią połączoną usługę.
    ![Narzędzie kopiowania — strona magazynu danych źródłowych](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na stronie **Określanie konta usługi Azure Blob Storage**:
    1. Zachowaj nazwę generowanych automatycznie **nazwa połączenia**. Nazwa połączenia jest nazwa połączonej usługi typu: Azure Storage.
    2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
    3. Wybierz swoją subskrypcję platformy Azure lub zachować te dane **Zaznacz wszystko** dla **subskrypcji platformy Azure**.
    4. Wybierz **konto usługi Azure Storage** z listy kont usługi Azure Storage dostępnych w ramach wybranej subskrypcji. Możesz również ręcznie wprowadzić ustawienia konta magazynu, wybierając **ręcznie wprowadzić** opcja dla **Metoda wyboru konta**.
    5. Kliknij przycisk **Dalej**.  
        ![Narzędzie kopiowania — określanie konta usługi Azure Blob storage](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na stronie **Wybieranie pliku lub folderu wejściowego**:
    1. Double-click **adfblobcontainer**.
    2. Wybierz **wejściowych**i kliknij przycisk **wybierz**. W tym przewodniku należy wybrać folderu danych wejściowych. Możesz też wybrać opcję Plik emp.txt z folderu zamiast tego.
        ![Narzędzie kopiowania — Wybieranie pliku lub folderu wejściowego](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na **Wybieranie pliku lub folderu wejściowego** strony:
    1. Upewnij się, że **pliku lub folderu** ustawiono **adfblobconnector/input**. Jeśli pliki znajdują się w podfolderach, na przykład 2017/04/01 2017/04/02 i tak dalej, wprowadź adfblobconnector/input / {year} / {month} / {day} dla pliku lub folderu. Po naciśnięciu klawisza TAB poza pole tekstowe, zobaczysz trzy list rozwijanych, aby wybrać formatów dla roku (rrrr), miesiąc (MM) i dnia (rrrr).
    2. Nie należy ustawiać **skopiuj plik rekursywnie**. Wybierz tę opcję, aby rekursywnie przechodzenia przez katalogi plików do skopiowania do miejsca docelowego.
    3. Nie **kopia binarna** opcji. Wybierz tę opcję, aby wykonać kopia binarna pliku źródłowego do miejsca docelowego. Nie wybieraj w ramach tego przewodnika, aby zobaczyć więcej opcji w następnej strony.
    4. Upewnij się, że **typ kompresji** ustawiono **Brak**. Wybierz wartość dla tej opcji, jeśli kompresji w jednym z obsługiwanych formatów plików źródłowych.
    5. Kliknij przycisk **Dalej**.
    ![Narzędzie kopiowania — Wybieranie pliku lub folderu wejściowego](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na stronie **Ustawienia formatu pliku** są wyświetlane ograniczniki i schemat wykrywany automatycznie przez kreatora w ramach analizy pliku.
    1. Potwierdź następujące opcje:  
        a. **Format pliku** ustawiono **format tekstu**. Możesz zobaczyć wszystkie obsługiwane formaty na liście rozwijanej. Na przykład: JSON, Avro, ORC, Parquet.
       b. **Ogranicznik kolumny** ustawiono `Comma (,)`. Możesz zobaczyć inne ograniczniki kolumny obsługiwane przez usługę Data Factory na liście rozwijanej. Można również określić ogranicznik niestandardowy.
       c. **Ogranicznik wiersza** ustawiono `Carriage Return + Line feed (\r\n)`. Możesz zobaczyć innych ograniczników wiersza obsługiwane przez usługę Data Factory na liście rozwijanej. Można również określić ogranicznik niestandardowy.
       d. **Pominąć licznik wierszy** ustawiono **0**. Jeśli chcesz, aby kilka wierszy do pominięcia w górnej części pliku, należy wprowadzić numer w tym miejscu.
       e. **Pierwszy wiersz danych zawiera nazwy kolumn** nie jest ustawiona. Jeśli pliki źródłowe zawierają nazwy kolumn w pierwszym wierszu, wybierz tę opcję.
       f. **Traktowanie wartości puste kolumny jako wartości null** ustawiono opcję.
    2. Rozwiń **Zaawansowane ustawienia** będzie dostępna opcja zaawansowanej.
    3. W dolnej części strony, zobacz **Podgląd** danych z pliku emp.txt.
    4. Kliknij przycisk **SCHEMATU** karty u dołu, aby wyświetlić schemat, który Kreator kopiowania wywnioskować, analizując dane w pliku źródłowym.
    5. Po przejrzeniu ograniczników i wyświetleniu podglądu danych kliknij przycisk **Dalej**.
    ![Narzędzie kopiowania — ustawienia formatu pliku](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na **strony magazyn danych docelowych**, wybierz opcję **usługi Azure Blob Storage**i kliknij przycisk **dalej**. Używasz usługi Azure Blob Storage jako oba źródła do docelowego magazynu danych w tym przewodniku.  
    ![Narzędzie kopiowania — wybierz docelowy magazyn danych](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na **określanie konta usługi Azure Blob storage** strony:  
    1. Wprowadź **AzureStorageLinkedService** dla **nazwa połączenia** pola.
    2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
    3. Wybierz swoją **subskrypcję** platformy Azure.
    4. Wybierz swoje konto usługi Azure storage.
    5. Kliknij przycisk **Dalej**.
10. Na **Wybieranie wyjściowego pliku lub folderu** strony:  
    1. Określ **ścieżka folderu** jako **adfblobconnector/output / {year} / {month} / {day}**. Wprowadź **kartę**.
    1. Aby uzyskać **roku**, wybierz opcję **rrrr**.
    1. Aby uzyskać **miesiąca**, upewnij się, że jest ustawiona na **MM**.
    1. Aby uzyskać **dzień**, upewnij się, że jest ustawiona na **dd**.
    1. Upewnij się, że **typ kompresji** ustawiono **Brak**.
    1. Upewnij się, że **skopiuj zachowanie** ustawiono **scalania plików**. Jeśli plik wyjściowy o takiej samej nazwie już istnieje, Nowa zawartość jest dodawany do tego samego pliku, na końcu.
    1. Kliknij przycisk **Dalej**.
       ![Narzędzie kopiowania — Wybieranie pliku lub folderu wyjściowego](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na **ustawienia formatu pliku** strony, przejrzyj ustawienia, a następnie kliknij przycisk **dalej**. Jednym z dodatkowych opcji w tym miejscu jest dodanie nagłówka do pliku wyjściowego. Jeśli wybierzesz tę opcję, wiersz nagłówka dodaje się nazwy kolumn ze schematu źródła. Podczas wyświetlania schematu dla źródła, można zmienić domyślne nazwy kolumn. Na przykład możesz zmienić pierwszej kolumny, imię i nazwisko drugą kolumnę. Następnie plik wyjściowy jest generowany przy użyciu nagłówka o tych nazwach jako nazwy kolumn.
    ![Narzędzie kopiowania — ustawienia formatu pliku dla miejsca docelowego](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na **ustawienia wydajności** strony, upewnij się, że **jednostki w chmurze** i **równoległe kopie** są ustawione na **automatycznie**i kliknij przycisk Dalej. Aby uzyskać szczegółowe informacje o tych ustawieniach, zobacz [skopiuj dostrajania przewodnik dotyczący wydajności działania i](data-factory-copy-activity-performance.md#parallel-copy).
    ![Narzędzie kopiowania — ustawienia wydajności](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na **Podsumowanie** strony, sprawdź wszystkie ustawienia (właściwości zadania, ustawienia źródłowe i docelowe i skopiuj ustawienia), a następnie kliknij przycisk **dalej**.
    ![Narzędzie kopiowania — strona podsumowania](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Przejrzyj informacje na stronie **Podsumowanie** i kliknij przycisk **Zakończ**. Kreator utworzy dwie połączone usługi, dwa zestawy danych (wejściowy i wyjściowy) i jeden potok w fabryce danych (z której został uruchomiony Kreator kopiowania).
    ![Narzędzie kopiowania — strona wdrożenia](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorować potok (zadanie kopiowania)

1. Kliknij łącze `Click here to monitor copy pipeline` na **wdrożenia** strony.
2. Powinien zostać wyświetlony **monitorowanie i Zarządzanie aplikacją** w osobnej karcie.  ![Monitorowanie aplikacji i zarządzanie](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Zmiana **start** czas u góry, aby `04/19/2017` i **zakończenia** czas na `04/27/2017`, a następnie kliknij przycisk **Zastosuj**.
4. Powinien zostać wyświetlony pięć okien działania w **okien działania** listy. **WindowStart** czasy powinny obejmować wszystkie dni od początku potoku do zakończenia potoku.
5. Kliknij przycisk **Odśwież** przycisku **okien działania** listy kilka razy, aż zostanie wyświetlony stan wszystkich okien działania jest ustawiony na gotowe.
6. Teraz Sprawdź, czy pliki wyjściowe są generowane w folderze wyjściowym adfblobconnector kontenera. Powinny zostać wyświetlone następującą strukturę folderów w folderze danych wyjściowych:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Aby uzyskać szczegółowe informacje o monitorowaniu i zarządzaniu fabryki danych, zobacz [monitorowanie i zarządzanie nimi w potoku usługi Data Factory](data-factory-monitor-manage-app.md) artykułu.

### <a name="data-factory-entities"></a>Jednostek usługi fabryka danych
Teraz przejdź do karty z strona główna fabryki danych. Należy zauważyć, że istnieją dwie połączone usługi, dwa zestawy danych i jednego potoku w fabryce danych teraz.

![Strona główna fabryki danych przy użyciu jednostek](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kliknij przycisk **tworzenie i wdrażanie** można uruchomić edytora fabryki danych.

![Edytor fabryki danych](media/data-factory-azure-blob-connector/data-factory-editor.png)

Powinny zostać wyświetlone następujące jednostki usługi Data Factory w fabryce danych:

- Dwie połączone usługi. Jeden dla źródła i jeden z nich dla miejsca docelowego. Usługi połączone odnoszą się do tego samego konta usługi Azure Storage, w tym przewodniku.
- Dwa zestawy danych. Wejściowy zestaw danych i zestaw danych wyjściowych. W tym przewodniku zarówno używać tego samego kontenera obiektów blob, ale odnoszą się do różnych folderów (dane wejściowe i wyjściowe).
- Potok. Potok zawiera działanie kopiowania, które wykorzystuje obiektu blob źródła i ujścia obiektu blob do skopiowania danych z lokalizacji obiektu blob platformy Azure do innej lokalizacji obiektu blob platformy Azure.

Poniższe sekcje zawierają więcej informacji na temat tych jednostek.

#### <a name="linked-services"></a>Połączone usługi
Powinien zostać wyświetlony dwóch połączonych usług. Jeden dla źródła i jeden z nich dla miejsca docelowego. W tym przewodniku zarówno definicje wyglądają tak samo, z wyjątkiem nazwy. **Typu** połączonej usługi jest równa **AzureStorage**. Najważniejsze właściwości definicji usługi połączonej jest **connectionString**, używany przez usługę Data Factory do łączenia się z kontem usługi Azure Storage w czasie wykonywania. Ignoruj właściwości hubName w definicji.

##### <a name="source-blob-storage-linked-service"></a>Źródłowy obiekt blob połączonej usługi storage
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Docelowy obiekt blob połączonej usługi storage

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Aby uzyskać więcej informacji na temat połączoną usługę Azure Storage, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.

#### <a name="datasets"></a>Zestawy danych
Istnieją dwa zestawy danych: wejściowy zestaw danych i zestaw danych wyjściowych. Typ zestawu danych jest ustawiony na **AzureBlob** dla obu.

Wejściowy zestaw danych wskazuje **wejściowych** folderu **adfblobconnector** kontenera obiektów blob. **Zewnętrznych** właściwość jest ustawiona na **true** dla tego zestawu danych jako danych nie jest generowany przez potok z działaniem kopiowania, które przyjmuje tego zestawu danych jako dane wejściowe.

Wyjściowy zestaw danych wskazuje **dane wyjściowe** folderze tego samego kontenera obiektów blob. Wyjściowy zestaw danych używa również rok, miesiąc i dzień **SliceStart** zmienna systemowa, można dynamicznie obliczyć ścieżki dla pliku wyjściowego. Aby uzyskać listę funkcje i zmienne systemowe, obsługiwany przez usługę Data Factory, zobacz [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md). **Zewnętrznych** właściwość jest ustawiona na **false** (wartość domyślna), ponieważ ten zestaw danych jest generowany przez potok.

Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez zestaw danych obiektów Blob platformy Azure, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.

##### <a name="input-dataset"></a>Wejściowy zestaw danych

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Wyjściowy zestaw danych

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Potok
Ten potok ma tylko jedno działanie. **Typu** działania jest równa **kopiowania**. We właściwościach typu dla działania istnieją dwie sekcje — jedną dla źródła, a jeden z nich ujścia. Typ źródła jest ustawiony **BlobSource** jako działanie kopiuje dane z magazynu obiektów blob. Typ ujścia jest ustawiona na **BlobSink** jako działanie kopiowania danych do magazynu obiektów blob. Działanie kopiowania może InputDataset-z4y jako dane wejściowe i OutputDataset-z4y jako dane wyjściowe.

Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez BlobSource i BlobSink zobacz [właściwości działania kopiowania](#copy-activity-properties) sekcji.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Przykłady JSON do kopiowania danych z magazynu obiektów Blob
W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak kopiować dane do i z usługi Azure Blob Storage i Azure SQL Database. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Przykład kodu JSON: Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL
Poniższy przykład pokazuje:

1. Połączonej usługi typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [BlobSource](#copy-activity-properties) i [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Kopiuje przykładowe szeregów czasowych danych z platformy Azure blob do usługi Azure SQL tabeli co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure SQL:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Połączona usługa Azure Storage:**

```json
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
Usługa Azure Data Factory obsługuje dwa typy usługi Azure Storage, połączone usługi: **AzureStorage** i **AzureStorageSas**. Dla pierwszą z nich należy określić parametry połączenia, który zawiera klucz konta, a dla późniejszy go, należy określić identyfikator Uri sygnatury dostępu współdzielonego (SAS). Zobacz [połączonych usług](#linked-service-properties) sekcji, aby uzyskać szczegółowe informacje.

**Usługa Azure wejściowy zestaw danych obiektów Blob:**

Dane są pobierane z nowy obiekt blob co godzinę (frequency: godzina, interwał: 1). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc i dzień godziny rozpoczęcia, a nazwa pliku używa godzinę część czas rozpoczęcia. "external": ustawienia "true" Data Factory informuje, że w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w usłudze data factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
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
**Usługi Azure SQL wyjściowy zestaw danych:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w bazie danych Azure SQL. Tworzenie tabeli w bazie danych Azure SQL z taką samą liczbę kolumn, zgodnie z oczekiwaniami plik obiektów Blob CSV będzie zawierał. Nowe wiersze są dodawane do tabeli, co godzinę.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
**Działanie kopiowania w potoku za pomocą obiektu Blob źródła i ujścia SQL:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **BlobSource** i **ujścia** ustawiono typ **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z bazy danych SQL Azure, do obiektów Blob platformy Azure
Poniższy przykład pokazuje:

1. Połączonej usługi typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) i [BlobSink](#copy-activity-properties).

Przykładowy skrypt kopiuje dane szeregów czasowych z tabeli usługi Azure SQL do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure SQL:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Połączona usługa Azure Storage:**

```json
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
Usługa Azure Data Factory obsługuje dwa typy usługi Azure Storage, połączone usługi: **AzureStorage** i **AzureStorageSas**. Dla pierwszą z nich należy określić parametry połączenia, który zawiera klucz konta, a dla późniejszy go, należy określić identyfikator Uri sygnatury dostępu współdzielonego (SAS). Zobacz [połączonych usług](#linked-service-properties) sekcji, aby uzyskać szczegółowe informacje.

**Usługa Azure SQL wejściowy zestaw danych:**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w tabeli SQL Azure i zawiera kolumnę o nazwie "timestampcolumn" w danych szeregów czasowych.

Ustawienie "external": "true" informuje usługa Data Factory, w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w usłudze data factory.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
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

**Działanie kopiowania w potoku za pomocą programu SQL źródła i ujścia obiektu Blob:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **SqlSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **SqlReaderQuery** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
