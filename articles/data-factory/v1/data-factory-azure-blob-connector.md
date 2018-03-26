---
title: Kopiowanie danych do/z magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak skopiować dane obiektów blob w fabryce danych Azure. Użyj naszej próbki: kopiowanie danych do i z magazynu obiektów Blob Azure i bazy danych SQL Azure.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fe7b419a2f4bbc3ae4aa69dad1e3b3e0957cc848
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopiowanie danych do i z magazynu obiektów Blob Azure przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-azure-blob-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-azure-blob-storage.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika magazynu obiektów Blob Azure w wersji 2](../connector-azure-blob-storage.md).


W tym artykule opisano sposób korzystania działanie kopiowania w fabryce danych Azure, aby skopiować dane do i z magazynu obiektów Blob Azure. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

## <a name="overview"></a>Przegląd
Dane należy skopiować z dowolnego źródła obsługiwanych magazynu danych do magazynu obiektów Blob Azure lub z magazynu obiektów Blob Azure żadnych obsługiwanych ujścia magazynu danych. Poniższa tabela zawiera listę magazynów danych są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania. Na przykład można przenieść dane **z** bazy danych programu SQL Server lub bazy danych Azure SQL **do** magazynu obiektów blob platformy Azure. I dane należy skopiować **z** magazynu obiektów blob Azure **do** magazyn danych SQL Azure lub kolekcji usługi Azure DB rozwiązania Cosmos. 

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane należy skopiować **z magazynu obiektów Blob Azure** do następujących danych przechowuje:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących baz danych **do magazynu obiektów Blob Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]
 
> [!IMPORTANT]
> Działanie kopiowania obsługuje kopiowanie danych z/do ogólnego przeznaczenia kont usługi Azure Storage i magazynu obiektów Blob aktywny/Superpaska. Działanie obsługuje **czytania z bloku, Dołącz lub stronicowe**, ale obsługuje **zapisywania tylko blokowe obiekty BLOB**. Usługa Azure Premium Storage nie jest obsługiwany jako zbiorniku, ponieważ nie jest obsługiwana przez stronicowych obiektów blob.
> 
> Aktywność kopiowania nie powoduje usunięcia danych ze źródła, po dane pomyślnie są kopiowane do lokalizacji docelowej. Jeśli chcesz usunąć źródło danych po pomyślnym kopiowania, tworzenia [działania niestandardowego](data-factory-use-custom-activities.md) usunąć dane i użyć działania w potoku. Na przykład zobacz [usuwania obiektów blob lub folderu przykładem w witrynie GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity). 

## <a name="get-started"></a>Rozpoczęcie pracy
Można utworzyć potoku o działanie kopiowania, który przenosi dane z magazynu obiektów Blob Azure przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Ten artykuł zawiera [wskazówki](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) do tworzenia potoku można skopiować danych z lokalizacji magazynu obiektów Blob Azure do innej lokalizacji magazynu obiektów Blob Azure. Samouczek przedstawiający tworzenie potoku, aby skopiować dane z magazynu obiektów Blob Azure do bazy danych SQL Azure, zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **fabryki danych**. Fabryka danych może zawierać co najmniej jeden potoków. 
2. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych. Na przykład jeśli kopiujesz danych z magazynu obiektów blob platformy Azure do bazy danych Azure SQL, Utwórz dwa połączone usługi, aby połączyć z kontem magazynu platformy Azure i bazą danych Azure SQL z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla usługi Azure Blob Storage, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji. 
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. W tym przykładzie wymienionych w ostatnim kroku tworzenia zestawu danych, aby określić folder, który zawiera dane wejściowe i kontener obiektów blob. I Utwórz innego elementu dataset, aby określić tabeli SQL w bazie danych Azure SQL, która przechowuje dane skopiowane z magazynu obiektów blob. Dla właściwości zestawu danych, które są specyficzne dla usługi Azure Blob Storage, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. W przykładzie wspomniano wcześniej używasz BlobSource jako źródło i SqlSink jako zbiorniku dla działania kopiowania. Podobnie baza danych SQL Azure są kopiowane do magazynu obiektów Blob Azure, należy użyć SqlSource i BlobSink w przypadku działania kopiowania. Dla właściwości działania kopiowania, które są specyficzne dla usługi Azure Blob Storage, zobacz [skopiować właściwości działania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje dotyczące sposobu używania magazynu danych jako źródło lub zbiorniku kliknij łącze w poprzedniej sekcji dla magazynu danych.  

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Aby uzyskać przykłady z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych do/z magazynu obiektów Blob platformy Azure, zobacz [przykłady JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do magazynu obiektów Blob Azure.

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Istnieją dwa typy połączonych usług używanego do łączenia usługi Azure Storage do fabryki danych Azure. Są one: **AzureStorage** połączonej usługi i **element AzureStorageSas** połączonej usługi. Połączoną usługą magazynu Azure zapewnia usłudze fabryka danych z globalnego dostępu do magazynu Azure. Związana SAS magazynu Azure (Shared Access Signature) usługa udostępnia fabryka danych z ograniczonej/czas-powiązane z dostępem do magazynu Azure. Nie istnieją inne różnice między tych dwóch połączonych usług. Wybierz połączonej usługi, która odpowiada Twoim potrzebom. Poniższe sekcje zawierają więcej szczegółowych informacji na temat tych dwóch usług połączonych.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych lub wyjściowych w magazynie obiektów Blob Azure, należy ustawić właściwości type elementu dataset do: **AzureBlob**. Ustaw **linkedServiceName** właściwości zestawu danych do nazwy usługi Azure Storage lub SAS magazynu Azure połączonej usługi.  Określ typ właściwości zestawu danych **kontenera obiektów blob** i **folderu** w magazynie obiektów blob.

Aby uzyskać pełną listę sekcje JSON & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

Fabryka danych obsługuje następujące wartości typu .NET zgodne ze specyfikacją CLS, na podstawie udostępnienie informacji o typie w "strukturę" dla źródeł danych schematu na odczytu obiektów blob platformy Azure: Int16, Int32, Int64, jeden, Double, Decimal, Byte [], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Fabryka danych automatycznie wykonuje konwersje typów podczas przenoszenia danych z magazynu danych źródła do ujścia magazynu danych.

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji, itp., formatowania danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **AzureBlob** zestawu danych ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do kontenera i folderu w magazynie obiektów blob. Przykład: myblobcontainer\myblobfolder\ |Yes |
| fileName |Nazwa obiektu blob. Nazwa pliku jest opcjonalna i z uwzględnieniem wielkości liter.<br/><br/>Jeśli określono nazwę pliku, działania (w tym kopiowania) działa na konkretnego obiektu Blob.<br/><br/>Jeśli nie określono nazwy pliku, kopiowania obejmuje wszystkie obiekty BLOB w ścieżce folderu dla zestawu danych wejściowych.<br/><br/>Gdy **fileName** dla wyjściowego zestawu danych nie został określony i **preserveHierarchy** nie została określona w zbiornika działania nazwę wygenerowanego pliku będzie poniżej tego formatu: danych.<Guid>. txt (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest opcjonalna właściwość. Można go określić folderPath dynamiczne i nazwę pliku dla danych w serii. czas. Na przykład folderPath mogą nadać parametry dla każdej godziny danych. Zobacz [przy użyciu sekcji właściwości partitionedBy](#using-partitionedBy-property) szczegółowe informacje i przykłady. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="using-partitionedby-property"></a>Za pomocą właściwości partitionedBy
Jak wspomniano w poprzedniej sekcji, można określić folderPath dynamiczne i nazwę pliku dla czasu danych serii za pomocą **partitionedBy** właściwość [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md).

Aby uzyskać więcej informacji na zestawy danych serii czasu, planowanie i wycinków, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) i [planowanie i wykonanie](data-factory-scheduling-and-execution.md) artykułów.

#### <a name="sample-1"></a>Przykład 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W tym przykładzie {wycinka} zostanie zastąpiony wartością zmiennej systemowej SliceStart fabryki danych w formacie (YYYYMMDDHH) określona. SliceStart odnosi się uruchomienie wycinka. Element folderPath jest różne dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104

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

W tym przykładzie rok, miesiąc, dzień i czas SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i nazwę pliku.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań. Właściwości dostępne w **typeProperties** sekcji działania zależne od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink. Jeśli przenosisz dane z magazynu obiektów Blob platformy Azure, w przypadku działania kopiowania, aby ustawić typ źródła **BlobSource**. Podobnie jeśli przenosisz dane do magazynu obiektów Blob Azure, należy ustawić typ ujścia w działanie kopiowania do **BlobSink**. Ta sekcja zawiera listę obsługiwanych przez BlobSource i BlobSink właściwości.

**BlobSource** obsługuje następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. |TRUE, False (wartość domyślna) |Nie |

**BlobSink** obsługuje następujące właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Określa zachowanie kopiowania, gdy źródłem jest BlobSource lub systemu plików. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszym poziomem folderu docelowego. Pliki docelowe mają automatycznie wygeneruje nazwę. <br/><br/><b>MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli zostanie określona nazwa pliku/obiektów Blob, nazwa scalony plik jest określona nazwa; w przeciwnym razie będzie nazwa pliku wygenerowana automatycznie. |Nie |

**BlobSource** obsługuje także te dwie właściwości dla zgodności z poprzednimi wersjami.

* **treatEmptyAsNull**: Określa, czy traktować null lub pusty ciąg jako wartość null.
* **skipHeaderLineCount** — określa liczbę wierszy muszą pominięte. Dotyczy tylko gdy wejściowy zestaw danych używa TextFormat.

Podobnie **BlobSink** obsługuje następujące właściwości dla zgodności z poprzednimi wersjami.

* **blobWriterAddHeader**: Określa, czy mają zostać dodane podczas zapisywania wyjściowy zestaw danych nagłówka definicje kolumn.

Zestawy danych obsługuje teraz następujące właściwości, które implementuje te same funkcje: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Poniższa tabela zawiera wskazówki dotyczące przy użyciu nowych właściwości zestawu danych, zamiast tych właściwości źródło/ujście obiektu blob.

| Właściwości działania kopiowania | Właściwości zestawu danych |
|:--- |:--- |
| skipHeaderLineCount na BlobSource |skipLineCount i firstRowAsHeader. Wiersze są pomijane najpierw i Odczytaj pierwszego wiersza jako nagłówka. |
| treatEmptyAsNull na BlobSource |treatEmptyAsNull w zestawie danych wejściowych |
| blobWriterAddHeader na BlobSink |firstRowAsHeader w zestawie danych wyjściowych |

Zobacz [określenie TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) sekcji, aby uzyskać szczegółowe informacje na temat tych właściwości.    

### <a name="recursive-and-copybehavior-examples"></a>Przykłady cyklicznego i copyBehavior
W tej sekcji opisano efekty operacji kopiowania dla różnych kombinacji wartości cyklicznej i copyBehavior.

| Cykliczne | copyBehavior | Efekty |
| --- | --- | --- |
| true |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z tej samej struktury jako źródło<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1, utworzono o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie File5 |
| true |mergeFiles |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1, utworzono o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + pliku 5 zawartości są scalane w jeden plik o nazwie generowanych automatycznie |
| false |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 plik3, File4 i File5 nie są odczytywane. |
| false |flattenHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik2<br/><br/><br/>Subfolder1 plik3, File4 i File5 nie są odczytywane. |
| false |mergeFiles |Dla folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie. Nazwa wygenerowana automatycznie Plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są odczytywane. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Wskazówki: Kreator kopiowania Użyj kopiowanie danych do/z magazynu obiektów Blob
Oto jak szybko skopiować dane z magazynu obiektów blob platformy Azure. W tym przewodniku danych na serwerze źródłowym i docelowym są przechowywane typu: magazyn obiektów Blob Azure. Potok, w tym przewodniku kopiuje dane z folderu do innego folderu, w tym samym kontenerze obiektu blob. Ten przewodnik jest celowo proste wyświetlić ustawienia lub właściwości w przypadku używania magazynu obiektów Blob jako źródło lub obiekt sink. 

### <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz ogólnego przeznaczenia **konta magazynu Azure** Jeśli nie masz już. Użyj magazynu obiektów blob jako zarówno **źródła** i **docelowego** magazynu danych, w tym przewodniku. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
2. Tworzenie kontenera obiektów blob o nazwie **adfblobconnector** na koncie magazynu. 
4. Utwórz folder o nazwie **wejściowych** w **adfblobconnector** kontenera.
5. Utwórz plik o nazwie **emp.txt** z następujących zawartości i przekaż go do **wejściowych** folderu za pomocą narzędzi takich jak [Eksploratora usługi Storage platformy Azure](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>Tworzenie fabryki danych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób** z lewego górnego rogu, kliknij przycisk **analizy i analiza**i kliknij przycisk **fabryki danych**.
3. W **nowa fabryka danych** okienka:   
    1. Wprowadź **ADFBlobConnectorDF** dla **nazwa**. Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: `*Data factory name “ADFBlobConnectorDF” is not available`, Zmień nazwę fabryki danych (na przykład yournameADFBlobConnectorDF) i spróbuj ponownie utworzyć. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Dla grupy zasobów, wybierz **Użyj istniejącego** wybierz istniejącą grupę zasobów (lub) wybierz **Utwórz nowy** wprowadź nazwę grupy zasobów.
    4. Wybierz **lokalizację** fabryki danych.
    5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.
    6. Kliknij przycisk **Utwórz**.
3. Po zakończeniu tworzenia zobacz **fabryki danych** bloku, jak pokazano na poniższej ilustracji: ![strony głównej fabryki danych](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Kreator kopiowania
1. Na stronie głównej fabryki danych, kliknij przycisk **kopiowanie danych [Podgląd]** Kafelek, aby uruchomić **kreatora kopiowania danych** w osobnej karcie.    
    
    > [!NOTE]
    >    Jeśli zobaczysz, że przeglądarki sieci web jest zablokowany na "Autoryzowanie...", wyłącz/Usuń zaznaczenie pola wyboru **zablokować pliki cookie innych firm, a dane lokacji** ustawienie (lub) schowaj włączone i utworzyć wyjątek **login.microsoftonline.com**, a następnie spróbuj ponownie uruchomić kreatora.
2. Na stronie **Właściwości**:
    1. Wprowadź **CopyPipeline** dla **Nazwa zadania**. Nazwa zadania jest nazwą potok w fabryce danych.
    2. Wprowadź **opis** zadania (opcjonalnie).
    3. Dla **okresach zadań lub harmonogram zadań**, Zachowaj **regularnego uruchamiania zgodnie z harmonogramem** opcji. Jeśli chcesz uruchomić to zadanie tylko raz zamiast uruchamiania wielokrotnie zgodnie z harmonogramem, wybierz opcję **uruchom raz teraz**. Jeśli zostanie wybrana, **uruchom raz teraz** opcji [potoku jednorazowego](data-factory-create-pipelines.md#onetime-pipeline) jest tworzony. 
    4. Zachowaj ustawienia **wzorzec cykliczny**. To zadanie jest uruchamiane codziennie od godziny rozpoczęcia i zakończenia, które określisz w następnym kroku.
    5. Zmień **Data i godzina rozpoczęcia** do **2017-04/21**. 
    6. Zmień **Data i godzina zakończenia** do **04/25/2017**. Możesz wpisać datę zamiast przeglądania kalendarza.     
    8. Kliknij przycisk **Dalej**.
      ![Skopiuj narzędzie — strona właściwości](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png) 
3. Na stronie **Magazyn danych źródłowych** kliknij kafelek **Azure Blob Storage**. Ta strona służy do określania magazynu danych źródłowych do zadania kopiowania. Możesz użyć istniejącej połączonej usługi magazynu danych lub określić nowy magazyn danych. Aby korzystać z istniejącą połączoną usługę, należy wybrać **z ISTNIEJĄCYMI usługami POŁĄCZONEGO** i wybierz prawa połączonej usługi. 
    ![Skopiuj narzędzie — strona Sklepu źródła danych](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na stronie **Określanie konta usługi Azure Blob Storage**:
   1. Zachowaj nazwę automatycznie generowanej **nazwa połączenia**. Nazwa połączenia jest nazwa połączonej usługi typu: Magazyn Azure. 
   2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
   3. Wybierz subskrypcję platformy Azure lub pozostaw **Zaznacz wszystko** dla **subskrypcji platformy Azure**.   
   4. Wybierz **konto usługi Azure Storage** z listy kont usługi Azure Storage dostępnych w ramach wybranej subskrypcji. Można również ręcznie wprowadź ustawienia konta magazynu, wybierając **ręcznie wprowadzić** opcja dla **konta metodę wyboru**.
   5. Kliknij przycisk **Dalej**. 
      ![Skopiuj narzędzie — Określ konto magazynu obiektów Blob platformy Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na stronie **Wybieranie pliku lub folderu wejściowego**:
   1. Kliknij dwukrotnie **adfblobcontainer**.
   2. Wybierz **wejściowych**i kliknij przycisk **wybierz**. W tym przewodniku wybierz opcję folder wejściowy. Plik emp.txt można również wybrać w folderze zamiast tego. 
      ![Skopiuj narzędzie — Wybierz wejściowy plik lub folder](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na **wybierz wejściowy plik lub folder** strony:
    1. Upewnij się, że **pliku lub folderu** ustawiono **adfblobconnector/wprowadzania**. Jeśli pliki znajdują się w podfolderach, na przykład 2017/04/01, 2017/04/02 i tak dalej, wprowadź adfblobconnector/danych wejściowych / {year} / {month} / {day} dla pliku lub folderu. Po naciśnięciu klawisza TAB poza pola tekstowego, jest wyświetlany trzy listy rozwijanej, aby wybrać formaty roku (rrrr), miesiąc (MM) i dzień (dd). 
    2. Nie ustawiaj **skopiuj plik rekursywnie**. Wybierz tę opcję, aby rekursywnie przechodzenia przez foldery dla plików do skopiowania do miejsca docelowego. 
    3. Nie **binarne kopiowania** opcji. Wybierz tę opcję, aby wykonać kopię binarnego pliku źródłowego do docelowego. Nie należy wybierać w ramach tego przewodnika, dzięki czemu można wyświetlić więcej opcji w kolejnych stronach. 
    4. Upewnij się, że **typ kompresji** ustawiono **Brak**. Wybierz wartość dla tej opcji, jeśli kompresji w jednym z obsługiwanych formatów plików źródła. 
    5. Kliknij przycisk **Dalej**.
    ![Skopiuj narzędzie — Wybierz wejściowy plik lub folder](./media/data-factory-azure-blob-connector/chose-input-file-folder.png) 
7. Na stronie **Ustawienia formatu pliku** są wyświetlane ograniczniki i schemat wykrywany automatycznie przez kreatora w ramach analizy pliku. 
    1. Potwierdź następujące opcje:. **Format pliku** ustawiono **formacie tekstowym**. Widać obsługiwanych formatów na liście rozwijanej. Na przykład: JSON, Avro, ORC, Parquet.
        b. **Ogranicznik kolumny** ma ustawioną wartość `Comma (,)`. Widać inne ograniczniki kolumny obsługiwane przez fabrykę danych na liście rozwijanej. Można również określić niestandardowe ogranicznika.
        c. **Ogranicznik wiersza** ma ustawioną wartość `Carriage Return + Line feed (\r\n)`. Widać inne ograniczniki wiersza obsługiwane przez fabrykę danych na liście rozwijanej. Można również określić niestandardowe ogranicznika.
        d. **Pominąć licznik wierszy** ustawiono **0**. Jeśli chcesz kilka wierszy do pominięcia w górnej części pliku, wprowadź numer tutaj.
        e.  **Pierwszy wiersz danych zawiera nazwy kolumn** nie jest ustawiona. Jeśli pliki źródłowe zawierają nazwy kolumn w pierwszym wierszu, wybierz tę opcję.
        f. **Wartość pusta kolumna jest traktowana jako null** opcja jest ustawiona.
    2. Rozwiń węzeł **Zaawansowane ustawienia** do zaawansowanej opcji dostępnych w temacie.
    3. W dolnej części strony, zobacz **Podgląd** danych z pliku emp.txt.
    4. Kliknij przycisk **SCHEMATU** u dołu, aby wyświetlić schemat, który Kreator kopiowania wykryta przez dane w pliku źródłowym.
    5. Po przejrzeniu ograniczników i wyświetleniu podglądu danych kliknij przycisk **Dalej**.
    ![Skopiuj narzędzie — Ustawienia format pliku](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)  
8. Na **magazyn danych docelowej strony**, wybierz pozycję **magazyn obiektów Blob Azure**i kliknij przycisk **dalej**. Używasz magazynu obiektów Blob Azure jako zarówno źródłowe i docelowe magazyny danych w ramach tego przewodnika.    
    ![Skopiuj narzędzie — Wybierz miejsce docelowe magazynu danych](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na **Określ konto magazynu obiektów Blob platformy Azure** strony:
   1. Wprowadź **AzureStorageLinkedService** dla **nazwa połączenia** pola.
   2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
   3. Wybierz swoją **subskrypcję** platformy Azure.  
   4. Wybierz konto magazynu platformy Azure. 
   5. Kliknij przycisk **Dalej**.     
10. Na **wybierz dane wyjściowe pliku lub folderu** strony: 
    6. Określ **ścieżka folderu** jako **adfblobconnector/wyjścia / {year} / {month} / {day}**. Wprowadź **kartę**.
    7. Aby uzyskać **roku**, wybierz pozycję **rrrr**.
    8. Aby uzyskać **miesiąca**, upewnij się, że jest ustawiona na **MM**.
    9. Aby uzyskać **dzień**, upewnij się, że jest ustawiona na **dd**.
    10. Upewnij się, że **typ kompresji** ustawiono **Brak**.
    11. Upewnij się, że **skopiuj zachowanie** ustawiono **Scal pliki**. Jeśli plik wyjściowy o takiej samej nazwie już istnieje, Nowa zawartość jest dodawana do tego samego pliku na końcu.
    12. Kliknij przycisk **Dalej**.
    ![Skopiuj narzędzie — Wybierz dane wyjściowe pliku lub folderu](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na **ustawienia formatu pliku** strony, przejrzyj ustawienia i kliknij przycisk **dalej**. Jednym z dodatkowe opcje jest dodanie nagłówka do pliku wyjściowego. Jeśli wybrano tę opcję z nazwami kolumn zostanie dodany wiersz nagłówka ze schematu źródła. Podczas wyświetlania schematu źródła, można zmienić domyślne nazwy kolumn. Na przykład można zmienić pierwszej kolumny Imię i nazwisko drugą kolumnę. Następnie plik wyjściowy jest generowany przy użyciu nagłówka o tych nazwach jako nazwy kolumn. 
    ![Skopiuj narzędzie — Ustawienia format pliku dla miejsca docelowego](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na **ustawienia wydajności** strony, upewnij się, że **chmury jednostki** i **równoległe kopie** ustawiono **automatycznie**i kliknij przycisk Dalej. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [skopiuj wydajności działania i dostrajania przewodnik](data-factory-copy-activity-performance.md#parallel-copy).
    ![Skopiuj narzędzie — Ustawienia wydajności](media/data-factory-azure-blob-connector/copy-performance-settings.png) 
14. Na **Podsumowanie** strony, przejrzyj wszystkie ustawienia (właściwości zadania, ustawienia źródłowe i docelowe i ustawień kopii), a następnie kliknij przycisk **dalej**.
    ![Skopiuj narzędzie — strona podsumowania](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Przejrzyj informacje na stronie **Podsumowanie** i kliknij przycisk **Zakończ**. Kreator utworzy dwie połączone usługi, dwa zestawy danych (wejściowy i wyjściowy) i jeden potok w fabryce danych (z której został uruchomiony Kreator kopiowania).
    ![Skopiuj narzędzie - stronę wdrożenia](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitor potoku (zadanie kopiowania)

1. Kliknij łącze `Click here to monitor copy pipeline` na **wdrożenia** strony. 
2. Powinny pojawić się **monitorowanie i Zarządzanie aplikacją** w osobnej karcie.  ![Monitorowanie aplikacji i zarządzanie](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Zmień **start** czasu na górze do `04/19/2017` i **zakończenia** czas na `04/27/2017`, a następnie kliknij przycisk **Zastosuj**. 
4. Powinny pojawić się pięć okien działania w **okien działania** listy. **WindowStart** razy powinna obejmować wszystkie dni od początku potoku do potoku godziny zakończenia. 
5. Kliknij przycisk **Odśwież** przycisk dla **okien działania** listy kilka razy, aż zostanie wyświetlony stan wszystkich okien działania jest ustawiony na gotowe. 
6. Teraz Sprawdź, czy pliki wyjściowe są generowane w folderze wyjściowym adfblobconnector kontenera. Powinny pojawić się następujące struktury folderów w folderze wyjściowym: 
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25    
    ```
Aby uzyskać szczegółowe informacje o monitorowaniu i zarządzaniu nimi fabryk danych, zobacz [monitora i zarządzanie nimi potoku fabryki danych](data-factory-monitor-manage-app.md) artykułu. 
 
### <a name="data-factory-entities"></a>Obiekty fabryki danych
Teraz przejdź do karty na stronie głównej fabryki danych. Zwróć uwagę, że istnieją dwa połączone usługi, dwa zestawy danych i jeden potok w fabryce danych teraz. 

![Strona główna fabryka danych z jednostkami](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kliknij przycisk **tworzenie i wdrażanie** można uruchomić Edytor fabryki danych. 

![Edytor fabryki danych](media/data-factory-azure-blob-connector/data-factory-editor.png)

Następujące jednostek fabryki danych powinny być widoczne w fabryce danych: 

 - Dwa połączone usługi. Jeden dla źródła i drugą dla miejsca docelowego. Usługi połączonej odwoływać się do tego samego konta magazynu Azure w ramach tego przewodnika. 
 - Dwa zestawy danych. Zestaw wejściowy i wyjściowy zestaw danych. W tym przewodniku zarówno używać tego samego kontenera obiektów blob, ale odwołuje się do innych folderów (dane wejściowe i wyjściowe).
 - Potok. Potok zawiera działanie kopiowania, który używa źródła obiektów blob i sink obiektów blob można skopiować danych z lokalizacji obiektów blob platformy Azure do innej lokalizacji obiektu blob systemu Azure. 

Poniższe sekcje zawierają więcej informacji na temat tych jednostek. 

#### <a name="linked-services"></a>Połączone usługi
Powinny pojawić się dwa połączonych usług. Jeden dla źródła i drugą dla miejsca docelowego. W tym przewodniku zarówno definicje wyglądają tak samo, z wyjątkiem nazwy. **Typu** połączonej usługi jest ustawiony na wartość **AzureStorage**. Najważniejsze właściwości definicji połączonej usługi jest **connectionString**, który jest używany przez fabryki danych do łączenia się z kontem magazynu Azure w czasie wykonywania. Ignoruj właściwości hubName w definicji. 

##### <a name="source-blob-storage-linked-service"></a>Źródła obiektów blob połączoną usługą magazynu
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

##### <a name="destination-blob-storage-linked-service"></a>Docelowy obiekt blob połączoną usługą magazynu

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

Aby uzyskać więcej informacji na temat połączoną usługą magazynu Azure, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji. 

#### <a name="datasets"></a>Zestawy danych
Istnieją dwa zestawy danych: zestaw wejściowy i wyjściowy zestaw danych. Typ zestawu danych jest ustawiony na **AzureBlob** dla obu. 

Wejściowy zestaw danych wskazuje **wejściowych** folderu **adfblobconnector** kontenera obiektów blob. **Zewnętrznych** właściwość jest ustawiona na **true** dla tego zestawu danych jako dane nie jest generowany przez potok wraz z działanie kopiowania, który przyjmuje tego zestawu danych jako dane wejściowe. 

Wskazuje wyjściowy zestaw danych **dane wyjściowe** folder z tego samego kontenera obiektów blob. Wyjściowy zestaw danych używa również rok, miesiąc i dzień **SliceStart** zmienna można dynamicznie obliczyć ścieżki do pliku wyjściowego. Listę funkcje i zmienne systemu obsługiwane przez fabrykę danych, zobacz [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md). **Zewnętrznych** właściwość jest ustawiona na **false** (wartość domyślna), ponieważ ten zestaw danych jest generowany przez potok. 

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
Potok zawiera tylko jedno działanie. **Typu** działania jest ustawiony na wartość **kopiowania**.  We właściwościach typu działania istnieją dwie sekcje, jeden dla źródła i drugą dla obiekt sink. Typ źródłowy ma ustawioną wartość **BlobSource** jako działanie kopiuje dane z magazynu obiektów blob. Typ ujścia ma ustawioną wartość **BlobSink** jako działania kopiowania danych do magazynu obiektów blob. Działanie kopiowania przyjmuje jako dane wejściowe InputDataset-z4y i OutputDataset-z4y jako dane wyjściowe. 

Aby uzyskać więcej informacji o obsługiwanych przez BlobSource i BlobSink właściwości, zobacz [skopiować właściwości działania](#copy-activity-properties) sekcji. 

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
Poniższe przykłady zapewniają definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych do i z magazynu obiektów Blob Azure i bazy danych SQL Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Przykład JSON: Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL
Poniższy przykład przedstawia:

1. Połączonej usługi typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działania kopiowania, która używa [BlobSource](#copy-activity-properties) i [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Kopie próbki szeregów czasowych dane z usługi Azure blob do bazy danych SQL Azure tabeli co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Azure połączoną usługą SQL:**

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
**Połączonej usługi magazynu Azure:**

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
Fabryka danych Azure obsługuje dwa typy usług magazynu Azure połączony: **AzureStorage** i **element AzureStorageSas**. Dla pierwszego z nich Określ ciąg połączenia, który zawiera klucz konta i dla późniejszą, określ identyfikator Uri dostępu sygnatury dostępu Współdzielonego. Zobacz [połączonych usług](#linked-service-properties) sekcji, aby uzyskać szczegółowe informacje.  

**Azure wejściowego zestawu danych obiektów Blob:**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu korzysta rok, miesiąc i dzień część czas rozpoczęcia, a nazwa pliku godzina część czas rozpoczęcia. "external": ustawienie "prawda" fabryki danych informuje, że w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

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
**Azure SQL wyjściowy zestaw danych:**

Kopie przykładowych danych do tabeli o nazwie "MyTable" w bazie danych Azure SQL. Tworzenie tabeli w bazie danych Azure SQL z taką samą liczbę kolumn, zgodnie z oczekiwaniami pliku Blob CSV zawiera. Nowe wiersze są dodawane do tabeli co godzinę.

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
**Działanie kopiowania w potoku z obiektu Blob, źródłowy i odbiorczy SQL:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **BlobSource** i **zbiornika** ustawiono typ **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z bazy danych SQL Azure do obiektów Blob platformy Azure
Poniższy przykład przedstawia:

1. Połączonej usługi typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) i [BlobSink](#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z tabeli Azure SQL do obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Azure połączoną usługą SQL:**

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
**Połączonej usługi magazynu Azure:**

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
Fabryka danych Azure obsługuje dwa typy usług magazynu Azure połączony: **AzureStorage** i **element AzureStorageSas**. Dla pierwszego z nich Określ ciąg połączenia, który zawiera klucz konta i dla późniejszą, określ identyfikator Uri dostępu sygnatury dostępu Współdzielonego. Zobacz [połączonych usług](#linked-service-properties) sekcji, aby uzyskać szczegółowe informacje.  

**Azure SQL wejściowy zestaw danych:**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w języku SQL Azure i zawiera kolumnę o nazwie "timestampcolumn" dla czasu serii danych.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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
          "value": { "type": "DateTime",  "date": "SliceStart", "format": "yyyy" } },
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

**Działanie kopiowania w potoku z SQL źródłowy i odbiorczy obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **SqlSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **SqlReaderQuery** właściwości wybiera dane w ostatniej godziny do skopiowania.

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
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
