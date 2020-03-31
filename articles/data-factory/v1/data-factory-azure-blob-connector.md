---
title: Kopiowanie danych do/z usługi Azure Blob Storage
description: 'Dowiedz się, jak kopiować dane obiektów blob w usłudze Azure Data Factory. Skorzystaj z naszego przykładu: Jak skopiować dane do i z usługi Azure Blob Storage i usługi Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eab332f102b9e39981e2d8ed6e84f73fada87a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282135"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopiowanie danych do lub z usługi Azure Blob Storage przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-blob-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-blob-storage.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik usługi Azure Blob Storage w wersji 2](../connector-azure-blob-storage.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z usługi Azure Blob Storage. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

## <a name="overview"></a>Omówienie
Można skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do usługi Azure Blob Storage lub z usługi Azure Blob Storage do dowolnego obsługiwanego magazynu danych ujścia. Poniższa tabela zawiera listę magazynów danych obsługiwanych jako źródła lub pochłaniacze przez działanie kopiowania. Na przykład można przenieść dane **z** bazy danych programu SQL Server lub bazy danych SQL platformy Azure **do** magazynu obiektów blob platformy Azure. Można też skopiować dane **z** magazynu obiektów blob platformy Azure **do** magazynu danych SQL azure lub kolekcji usługi Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane z **usługi Azure Blob Storage** można skopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można **skopiować do usługi Azure Blob Storage:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Działanie kopiowania obsługuje kopiowanie danych z/do kont usługi Azure Storage ogólnego przeznaczenia i magazynu obiektów Blob gorąca/chłodna. Działanie obsługuje **odczytywanie z bloków, dołączania lub stron blob**, ale obsługuje **zapisywanie tylko blokować obiekty blob**. Usługa Azure Premium Storage nie jest obsługiwana jako obiekt sink, ponieważ jest wspierany przez obiekty blob strony.
>
> Działanie kopiowania nie powoduje usunięcia danych ze źródła po pomyślnym skopiowaniu danych do miejsca docelowego. Jeśli chcesz usunąć dane źródłowe po pomyślnej kopii, utwórz [działanie niestandardowe,](data-factory-use-custom-activities.md) aby usunąć dane i użyć działania w potoku. Na przykład zobacz [przykład usuwania obiektu blob lub folderu w usłudze GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane do/z usługi Azure Blob Storage przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Ten artykuł zawiera [przewodnik dotyczący](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) tworzenia potoku w celu skopiowania danych z lokalizacji usługi Azure Blob Storage do innej lokalizacji usługi Azure Blob Storage. Aby uzyskać samouczek dotyczący tworzenia potoku w celu skopiowania danych z usługi Azure Blob Storage do usługi Azure SQL Database, zobacz [Samouczek: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **fabryki danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z magazynu obiektów blob platformy Azure do bazy danych SQL platformy Azure utworzysz dwie połączone usługi, aby połączyć konto usługi Azure storage i bazę danych SQL platformy Azure z fabryką danych. Właściwości usługi połączone, które są specyficzne dla usługi Azure Blob Storage, zobacz [sekcję właściwości usługi połączone.](#linked-service-properties)
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. W przykładzie wymienionym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektu blob i folder zawierający dane wejściowe. I utworzyć inny zestaw danych, aby określić tabelę SQL w bazie danych SQL platformy Azure, która przechowuje dane skopiowane z magazynu obiektów blob. Właściwości zestawu danych, które są specyficzne dla usługi Azure Blob Storage, zobacz [sekcję właściwości zestawu danych.](#dataset-properties)
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W przykładzie wspomniano wcześniej, należy użyć BlobSource jako źródło i SqlSink jako ujście dla działania kopiowania. Podobnie, jeśli kopiujesz z usługi Azure SQL Database do usługi Azure Blob Storage, używasz SqlSource i BlobSink w działania kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla usługi Azure Blob Storage, zobacz [sekcję właściwości działania kopiowania.](#copy-activity-properties) Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładów z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do/z usługi Azure Blob Storage, zobacz [sekcję przykładów JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla usługi Azure Blob Storage.

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Istnieją dwa typy połączonych usług, których można użyć do połączenia usługi Azure Storage z fabryką danych platformy Azure. Są to: Usługa połączona **AzureStorage** i usługa połączona **AzureStorageSas.** Usługa połączona usługi Azure Storage zapewnia fabryce danych globalny dostęp do usługi Azure Storage. Natomiast usługa połączona z usługą Azure Storage SAS (Shared Access Signature) zapewnia fabryce danych ograniczony/czasowy dostęp do usługi Azure Storage. Nie ma innych różnic między tymi dwiema połączonymi usługami. Wybierz usługę połączony, która odpowiada Twoim potrzebom. Poniższe sekcje zawierają więcej szczegółów na temat tych dwóch połączonych usług.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych lub wyjściowych w magazynie obiektów Blob platformy Azure, należy ustawić właściwość typu zestawu danych na: **AzureBlob**. Ustaw właściwość **linkedServiceName** zestawu danych na nazwę usługi połączonej usługi Azure Storage lub Azure Storage SAS.  Właściwości typu zestawu danych określają **kontener obiektów blob** i **folder** w magazynie obiektów blob.

Aby uzyskać pełną listę sekcji JSON & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Fabryka danych obsługuje następujące wartości typu oparte na platformie CLS .NET do dostarczania informacji o typie w "strukturze" dla źródeł danych schematu przy odczytie, takich jak azure blob: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Usługa Data Factory automatycznie przeprowadza konwersje typów podczas przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia.

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji, formacie itp., danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **AzureBlob** dataset ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Ścieżka do kontenera i folderu w magazynie obiektów blob. Przykład: myblobcontainer\myblobfolder\ |Tak |
| fileName |Nazwa obiektu blob. nazwa pliku jest opcjonalna i rozróżniana wielkość liter.<br/><br/>Jeśli określisz nazwę pliku, działanie (w tym Kopiuj) działa na określonym blob.<br/><br/>Gdy nazwa pliku nie jest określona, Copy zawiera wszystkie obiekty Blob w folderPath dla wejściowego zestawu danych.<br/><br/>Jeśli **nazwa pliku** nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie jest określony w sink `Data.<Guid>.txt` działania, nazwa wygenerowanego pliku będzie w następującym formacie: (na przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest właściwością opcjonalną. Można go użyć do określenia dynamicznego folderPath i nazwy pliku dla danych szeregów czasowych. Na przykład folderPath może być sparametryzowany dla każdej godziny danych. Zobacz [Using partitionedBy sekcji właściwości,](#using-partitionedby-property) aby uzyskać szczegółowe informacje i przykłady. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="using-partitionedby-property"></a>Korzystanie z partitionedBy właściwości
Jak wspomniano w poprzedniej sekcji, można określić folder dynamicznyPath i nazwa pliku dla danych szeregów czasowych z **partitionedBy** właściwości, [funkcje fabryki danych i zmiennych systemowych](data-factory-functions-variables.md).

Aby uzyskać więcej informacji na temat zestawów danych szeregów czasowych, planowania i wycinków, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md) i [planowanie & artykułów wykonywania.](data-factory-scheduling-and-execution.md)

#### <a name="sample-1"></a>Przykład 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W tym przykładzie {Slice} jest zastępowany wartością zmiennej systemowej Data Factory SliceStart w określonym formacie (RRRRMMDDHH). SliceStart odnosi się do czasu rozpoczęcia plasterka. Ścieżka folderu jest inna dla każdego plasterka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104

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

W tym przykładzie rok, miesiąc, dzień i godzina SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i fileName.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań. Mając na uwadze, właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze. Jeśli przenosisz dane z usługi Azure Blob Storage, należy ustawić typ źródła w działaniu kopiowania na **BlobSource**. Podobnie w przypadku przenoszenia danych do usługi Azure Blob Storage, należy ustawić typ ujścia w działaniu kopiowania na **BlobSink**. Ta sekcja zawiera listę właściwości obsługiwanych przez BlobSource i BlobSink.

**Obiekt BlobSource** obsługuje następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda (wartość domyślna), Fałsz |Nie |

**BlobSink** obsługuje następujące właściwości **typuProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowanie kopiowania, gdy źródłem jest BlobSource lub FileSystem. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze względną ścieżką pliku docelowego do folderu docelowego.<br/><br/><b>SpłaszczHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie wygenerowaną nazwę. <br/><br/><b>MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu blob jest określona, scalona nazwa pliku będzie określona nazwa; w przeciwnym razie zostanie wygenerowana automatycznie nazwa pliku. |Nie |

**BlobSource** obsługuje również te dwie właściwości dla zgodności z powrotem.

* **treatEmptyAsNull**: Określa, czy ciąg null lub pusty mają być traktowane jako wartość null.
* **skipHeaderLineCount** — określa, ile wierszy należy pominąć. Ma zastosowanie tylko wtedy, gdy wejściowy zestaw danych używa TextFormat.

Podobnie **BlobSink** obsługuje następującą właściwość dla zgodności z powrotem.

* **blobWriterAddHeader**: Określa, czy podczas zapisywania do wyjściowego zestawu danych należy dodać nagłówek definicji kolumn.

Zestawy danych obsługują teraz następujące właściwości, które implementują te same funkcje: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Poniższa tabela zawiera wskazówki dotyczące korzystania z nowych właściwości zestawu danych zamiast tych właściwości źródła/ujścia obiektu blob.

| Kopiowanie działania, właściwość | Właściwość zestawu danych |
|:--- |:--- |
| skipHeaderLineCount w usłudze BlobSource |skipLineCount i firstRowAsHeader. Wiersze są pomijane najpierw, a następnie pierwszy wiersz jest odczytywany jako nagłówek. |
| treatEmptyAsNull na BlobSource |treatEmptyAsNull na wejściowym zestawie danych |
| blobWriterAddHeader na BlobSink |firstRowAsHeader na wyjściowym zestawie danych |

Zobacz [Określanie sekcji TextFormat,](data-factory-supported-file-and-compression-formats.md#text-format) aby uzyskać szczegółowe informacje na temat tych właściwości.

### <a name="recursive-and-copybehavior-examples"></a>przykłady rekursywne i copyBehavior
W tej sekcji opisano wynikowe zachowanie operacji Kopiowanie dla różnych kombinacji wartości cyklicznych i copyBehavior.

| Cykliczne | copyBehavior | Wynikowe działanie |
| --- | --- | --- |
| true |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z taką samą strukturą jak źródło<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5. |
| true |spłaszczyćHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku5 |
| true |scalanie plików |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 zawartość są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku |
| false |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |
| false |spłaszczyćHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku2<br/><br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |
| false |scalanie plików |Dla folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość file1 + File2 jest scalana w jeden plik o automatycznie wygenerowanej nazwie pliku. automatycznie wygenerowana nazwa pliku1<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Przewodnik: Kopiowanie danych do/z magazynu obiektów blob za pomocą kreatora kopiowania
Przyjrzyjmy się, jak szybko skopiować dane do/z magazynu obiektów blob platformy Azure. W tym instruktażu zarówno źródłowych, jak i docelowych magazynów danych typu: Usługi Azure Blob Storage. Potok w tym instruktażu kopiuje dane z folderu do innego folderu w tym samym kontenerze obiektów blob. Ten instruktaż jest celowo proste, aby pokazać ustawienia lub właściwości podczas korzystania z magazynu obiektów Blob jako źródło lub ujście.

### <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz ogólne konto **usługi Azure Storage,** jeśli jeszcze go nie masz. Magazyn obiektów blob służy jako **źródłowego** i **docelowego** magazynu danych w tym instruktażu. Jeśli nie masz konta magazynu platformy Azure, zobacz [artykuł Tworzenie konta magazynu,](../../storage/common/storage-account-create.md) aby uzyskać kroki, aby je utworzyć.
2. Utwórz kontener obiektów blob o nazwie **adfblobconnector** na koncie magazynu.
4. Utwórz folder o nazwie **dane wejściowe** w kontenerze **adfblobconnector.**
5. Utwórz plik o nazwie **emp.txt** z następującą zawartością i przekaż go do folderu **wejściowego** przy użyciu narzędzi, takich jak [Eksplorator usługi Azure Storage](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Tworzenie fabryki danych
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Kliknij **pozycję Utwórz zasób** w lewym górnym rogu, kliknij pozycję **Analiza + analiza**i kliknij pozycję **Fabryka danych**.
3. W okienku **Nowa fabryka danych:**  
    1. Wprowadź **ADFBlobConnectorDF** dla **nazwy**. Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony `*Data factory name “ADFBlobConnectorDF” is not available`błąd: , zmień nazwę fabryki danych (na przykład yournameADFBlobConnectorDF) i spróbuj utworzyć ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. W obszarze Grupa zasobów wybierz **pozycję Użyj istniejącej,** aby wybrać istniejącą grupę zasobów (lub) wybierz **pozycję Utwórz nowy,** aby wprowadzić nazwę grupy zasobów.
    4. Wybierz **lokalizację** fabryki danych.
    5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.
    6. Kliknij przycisk **Utwórz**.
3. Po zakończeniu tworzenia zostanie wyświetlony **blok Fabryka danych,** jak ![pokazano na poniższej ilustracji: Strona główna fabryki danych](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Kreator kopiowania
1. Na stronie głównej Fabryka danych kliknij kafelek **Kopiuj dane,** aby uruchomić **Kreatora kopiowania danych** na osobnej karcie.  

    > [!NOTE]
    > Jeśli widzisz, że przeglądarka internetowa utknęła w "Autoryzowaniu...", wyłącz/odznacz pole **wyboru Zablokuj pliki cookie i** ustawienia danych witryny (lub) włącz je i utwórz wyjątek dla **login.microsoftonline.com** a następnie spróbuj ponownie uruchomić kreatora.
2. Na stronie **Właściwości**:
    1. Wprowadź **polecenie CopyPipeline** for **Task name**. Nazwa zadania jest nazwą potoku w fabryce danych.
    2. Wprowadź **opis** zadania (opcjonalnie).
    3. W przypadku **rytmu zadania lub harmonogramu zadań**należy regularnie **uruchamiać zgodnie z harmonogramem.** Jeśli chcesz uruchomić to zadanie tylko raz, a nie uruchamiać go wielokrotnie zgodnie z harmonogramem, wybierz opcję **Uruchom teraz raz**. Jeśli wybierzesz opcję **Uruchom raz,** tworzony jest [potok jednorazowy.](data-factory-create-pipelines.md#onetime-pipeline)
    4. Zachowaj ustawienia **wzorca cyklicznego**. To zadanie jest uruchamiane codziennie między godzinami rozpoczęcia i zakończenia określonymi w następnym kroku.
    5. Zmień **datę rozpoczęcia** na **21.04.2017**.
    6. Zmień **datę zakończenia** na **25.04.2017**. Możesz wpisać datę zamiast przeglądać kalendarz.
    8. Kliknij przycisk **alej**.
        ![Narzędzie kopiowania — strona Właściwości](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na stronie **Magazyn danych źródłowych** kliknij kafelek **Azure Blob Storage**. Ta strona służy do określania magazynu danych źródłowych do zadania kopiowania. Możesz użyć istniejącej połączonej usługi magazynu danych lub określić nowy magazyn danych. Aby korzystać z istniejącej usługi połączonej, należy wybrać **z istniejących usług połączonych** i wybrać właściwą usługę połączony.
    ![Narzędzie kopiowania — strona magazynu danych źródłowych](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na stronie **Określanie konta usługi Azure Blob Storage**:
    1. Zachowaj automatycznie wygenerowaną nazwę **nazwy połączenia**. Nazwa połączenia jest nazwą połączonej usługi typu: Usługa Azure Storage.
    2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
    3. Wybierz subskrypcję platformy Azure lub zachowaj subskrypcję **wybierz wszystko** dla **platformy Azure.**
    4. Wybierz **konto usługi Azure Storage** z listy kont usługi Azure Storage dostępnych w ramach wybranej subskrypcji. Można również ręcznie wprowadzić ustawienia konta magazynu, wybierając opcję **Wprowadź ręcznie** dla metody **wyboru konta**.
    5. Kliknij przycisk **alej**.  
        ![Narzędzie kopiowania — określanie konta usługi Azure Blob Storage](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na stronie **Wybieranie pliku lub folderu wejściowego**:
    1. Kliknij dwukrotnie **adfblobcontainer**.
    2. Wybierz **pozycję Input**i kliknij przycisk **Wybierz**. W tym instruktażu należy wybrać folder wejściowy. Zamiast tego można wybrać plik emp.txt w folderze.
        ![Narzędzie kopiowania — wybieranie pliku lub folderu wejściowego](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na stronie **Wybierz plik wejściowy lub folder:**
    1. Upewnij się, że **plik lub folder** jest ustawiony na **adfblobconnector/input**. Jeśli pliki znajdują się w podfolderach, na przykład 2017/04/01, 2017/04/02 itd. Po naciśnięciu klawisza TAB poza polem tekstowym są widoczne trzy listy rozwijane, aby wybrać formaty dla roku (yyyy), miesiąca (MM) i dnia (dd).
    2. Nie należy **ustawiać pliku kopiowania cyklicznie**. Wybierz tę opcję, aby cyklicznie przechodzić przez foldery, aby pliki mają zostać skopiowane do miejsca docelowego.
    3. Nie należy **kopiować binarnym.** Wybierz tę opcję, aby wykonać binarną kopię pliku źródłowego do miejsca docelowego. Nie należy wybierać dla tego przewodnika, aby zobaczyć więcej opcji na następnych stronach.
    4. Upewnij się, że **typ kompresji** jest ustawiony na **Brak**. Wybierz wartość dla tej opcji, jeśli pliki źródłowe są skompresowane w jednym z obsługiwanych formatów.
    5. Kliknij przycisk **alej**.
    ![Narzędzie kopiowania — wybieranie pliku lub folderu wejściowego](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na stronie **Ustawienia formatu pliku** są wyświetlane ograniczniki i schemat wykrywany automatycznie przez kreatora w ramach analizy pliku.
    1. Potwierdź następujące opcje:  
        a. **Format pliku** jest ustawiony na **format tekstowy**. Na liście rozwijanej można wyświetlić wszystkie obsługiwane formaty. Na przykład: JSON, Avro, ORC, Parkiet.
       b. **Ogranicznik kolumny** jest `Comma (,)`ustawiony na . Na liście rozwijanej można wyświetlić inne ograniczniki kolumn obsługiwane przez fabrykę danych. Można również określić niestandardowy ogranicznik.
       d. **Ogranicznik wiersza** `Carriage Return + Line feed (\r\n)`jest ustawiony na . Na liście rozwijanej można wyświetlić inne ograniczniki wierszy obsługiwane przez fabrykę danych. Można również określić niestandardowy ogranicznik.
       d. **Liczba wierszy pomijania** jest ustawiona na **0**. Jeśli chcesz pominąć kilka wierszy w górnej części pliku, wprowadź numer w tym miejscu.
       e. **Pierwszy wiersz danych zawiera nazwy kolumn** nie jest ustawiony. Jeśli pliki źródłowe zawierają nazwy kolumn w pierwszym wierszu, wybierz tę opcję.
       f. Ustawiona jest **opcja traktuj pustą kolumnę jako null.**
    2. Rozwiń **ustawienia zaawansowane,** aby wyświetlić dostępną opcję zaawansowaną.
    3. U dołu strony zobacz **podgląd** danych z pliku emp.txt.
    4. Kliknij kartę **SCHEMAT** U dołu, aby wyświetlić schemat, który kreator kopiowania wywnioskował, patrząc na dane w pliku źródłowym.
    5. Po przejrzeniu ograniczników i wyświetleniu podglądu danych kliknij przycisk **Dalej**.
    ![Narzędzie kopiowania — ustawienia formatu pliku](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na **stronie Docelowy magazyn danych**wybierz pozycję **Azure Blob Storage**i kliknij przycisk **Dalej**. Używasz usługi Azure Blob Storage jako źródłowych i docelowych magazynów danych w tym instruktażu.  
    ![Narzędzie kopiowania - wybierz docelowy magazyn danych](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na **stronie Określ konto magazynu obiektów Blob platformy Azure:**  
    1. Wprowadź **usługę AzureStorageLinkedService** dla pola **Nazwa połączenia.**
    2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
    3. Wybierz swoją **subskrypcję** platformy Azure.
    4. Wybierz swoje konto magazynu platformy Azure.
    5. Kliknij przycisk **alej**.
10. Na stronie **Wybierz plik wyjściowy lub folder:**  
    1. określ **ścieżkę folderu** jako **adfblobconnector/output/{year}/{month}/{day}**. Wprowadź **TAB**.
    1. Na **rok**, wybierz **yyyy**.
    1. W **miesiącu,** upewnij się, że jest ustawiony na **MM**.
    1. Na **dzień,** potwierdź, że jest ustawiony na **dd**.
    1. Upewnij się, że **typ kompresji** jest ustawiony na **Brak**.
    1. Upewnij się, że **zachowanie kopiowania** jest ustawione na **Scalanie plików**. Jeśli plik wyjściowy o tej samej nazwie już istnieje, nowa zawartość jest dodawana do tego samego pliku na końcu.
    1. Kliknij przycisk **alej**.
       ![Narzędzie kopiowania — wybieranie pliku wyjściowego lub folderu](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na stronie **Ustawienia formatu pliku** przejrzyj ustawienia i kliknij przycisk **Dalej**. Jedną z dodatkowych opcji w tym miejscu jest dodanie nagłówka do pliku wyjściowego. Jeśli wybierzesz tę opcję, wiersz nagłówka zostanie dodany z nazwami kolumn ze schematu źródła. Podczas wyświetlania schematu źródła można zmienić nazwę domyślnych nazw kolumn. Na przykład można zmienić pierwszą kolumnę na Imię i drugą kolumnę na Nazwisko. Następnie plik wyjściowy jest generowany z nagłówkiem z tymi nazwami jako nazwami kolumn.
    ![Narzędzie kopiowania — ustawienia formatu pliku dla miejsca docelowego](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na stronie **Ustawienia wydajności** upewnij się, że **jednostki chmury** i kopie **równoległe** są ustawione na **Auto**, a następnie kliknij przycisk Dalej. Aby uzyskać szczegółowe informacje o tych ustawieniach, zobacz [Kopiowanie wydajności aktywności i dostrajania .](data-factory-copy-activity-performance.md#parallel-copy)
    ![Narzędzie kopiowania — ustawienia wydajności](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na stronie **Podsumowanie** przejrzyj wszystkie ustawienia (właściwości zadania, ustawienia źródła i miejsca docelowego oraz ustawienia kopiowania) i kliknij przycisk **Dalej**.
    ![Kopiuj narzędzie — strona Podsumowanie](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Przejrzyj informacje na stronie **Podsumowanie** i kliknij przycisk **Zakończ**. Kreator utworzy dwie połączone usługi, dwa zestawy danych (wejściowy i wyjściowy) i jeden potok w fabryce danych (z której został uruchomiony Kreator kopiowania).
    ![Narzędzie kopiowania — strona Wdrażanie](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorowanie potoku (zadanie kopiowania)

1. Kliknij łącze `Click here to monitor copy pipeline` na stronie **Wdrażanie.**
2. Powinna zostać wyświetlona **opcja Monitoruj i zarządzaj aplikacją** na osobnej karcie.  ![Monitorowanie aplikacji i zarządzanie nią](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Zmień czas **rozpoczęcia** w `04/19/2017` górnej i `04/27/2017` **końcowej** godzinie na , a następnie kliknij przycisk **Zastosuj**.
4. Na liście **AKTYWNOŚĆ SYSTEMU WINDOWS** powinno być widocznych pięć okien aktywności. **WindowStart** razy powinny obejmować wszystkie dni od czasu zakończenia potoku do potoku.
5. Kliknij przycisk **Odśwież** dla listy **SYSTEM WINDOWS działania** kilka razy, aż zobaczysz stan wszystkich okien aktywności jest ustawiona na Gotowe.
6. Teraz sprawdź, czy pliki wyjściowe są generowane w folderze wyjściowym kontenera adfblobconnector. W folderze wyjściowym powinna zostać wyświetlna następująca struktura folderów:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Aby uzyskać szczegółowe informacje na temat monitorowania fabryk danych i zarządzania nimi, zobacz [Monitorowanie i zarządzanie potokiem usługi Data Factory.](data-factory-monitor-manage-app.md)

### <a name="data-factory-entities"></a>Jednostki fabryki danych
Teraz wróć do karty ze stroną główną usługi Data Factory. Należy zauważyć, że istnieją dwie połączone usługi, dwa zestawy danych i jeden potok w fabryce danych teraz.

![Strona główna usługi Data Factory z encjami](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kliknij **pozycję Autor i wdrożyć,** aby uruchomić Edytor fabryki danych.

![Edytor fabryki danych](media/data-factory-azure-blob-connector/data-factory-editor.png)

W fabryce danych powinny zostać wyświetlene następujące jednostki fabryki danych:

- Dwie połączone usługi. Jeden dla źródła, a drugi dla miejsca docelowego. Obie połączone usługi odnoszą się do tego samego konta usługi Azure Storage w tym instruktażu.
- Dwa zestawy danych. Wejściowy zestaw danych i wyjściowy zestaw danych. W tym instruktażu oba używają tego samego kontenera obiektów blob, ale odnoszą się do różnych folderów (dane wejściowe i wyjściowe).
- Potok. Potok zawiera działanie kopiowania, które używa źródła obiektu blob i ujścia obiektu blob do kopiowania danych z lokalizacji obiektu blob platformy Azure do innej lokalizacji obiektu blob platformy Azure.

Poniższe sekcje zawierają więcej informacji na temat tych jednostek.

#### <a name="linked-services"></a>Połączone usługi
Powinny być widoczne dwie połączone usługi. Jeden dla źródła, a drugi dla miejsca docelowego. W tym instruktażu obie definicje wyglądają tak samo, z wyjątkiem nazw. **Typ** połączonej usługi jest ustawiony na **AzureStorage**. Najważniejszą właściwością definicji usługi połączonej jest **connectionString**, który jest używany przez usługę Data Factory do łączenia się z kontem usługi Azure Storage w czasie wykonywania. Ignoruj właściwość hubName w definicji.

##### <a name="source-blob-storage-linked-service"></a>Usługa połączona z magazynem obiektów blob źródłowyłączony
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

##### <a name="destination-blob-storage-linked-service"></a>Docelowa usługa połączona z magazynem obiektów blob

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

Aby uzyskać więcej informacji na temat usługi połączonej usługi Usługi Azure Storage, zobacz [sekcję Właściwości usługi połączone.](#linked-service-properties)

#### <a name="datasets"></a>Zestawy danych
Istnieją dwa zestawy danych: wejściowy zestaw danych i wyjściowy zestaw danych. Typ zestawu danych jest ustawiony na **AzureBlob** dla obu.

Wejściowy zestaw danych wskazuje folder **wejściowy** kontenera obiektów blob **adfblobconnector.** Właściwość **zewnętrzna** jest ustawiona na **true** dla tego zestawu danych, ponieważ dane nie są tworzone przez potok z działaniem kopiowania, które przyjmuje ten zestaw danych jako dane wejściowe.

Wyjściowy zestaw danych wskazuje folder **wyjściowy** tego samego kontenera obiektów blob. Wyjściowy zestaw danych używa również roku, miesiąca i dnia zmiennej systemowej **SliceStart** do dynamicznej oceny ścieżki dla pliku wyjściowego. Aby uzyskać listę funkcji i zmiennych systemowych obsługiwanych przez fabrykę danych, zobacz [Funkcje fabryki danych i zmienne systemowe](data-factory-functions-variables.md). Właściwość **zewnętrzna** jest ustawiona na **false** (wartość domyślna), ponieważ ten zestaw danych jest produkowany przez potok.

Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez zestaw danych obiektów blob platformy Azure, zobacz sekcja [właściwości zestawu danych.](#dataset-properties)

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
Potok ma tylko jedno działanie. **Typ** działania jest ustawiony na **Kopiuj**. We właściwościach typu dla działania istnieją dwie sekcje, jedna dla źródła, a druga dla ujścia. Typ źródła jest ustawiony na **BlobSource,** ponieważ działanie jest kopiowanie danych z magazynu obiektów blob. Typ ujścia jest ustawiona na **BlobSink** jako działanie kopiowanie danych do magazynu obiektów blob. Działanie kopiowania przyjmuje InputDataset-z4y jako dane wejściowe i OutputDataset-z4y jako dane wyjściowe.

Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez blobSource i BlobSink, zobacz [Kopiowanie właściwości działania](#copy-activity-properties) sekcji.

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Przykłady JSON dotyczące kopiowania danych do i z magazynu obiektów Blob
Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane do i z usługi Azure Blob Storage i usługi Azure SQL Database. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Przykład JSON: Kopiowanie danych z magazynu obiektów blob do bazy danych SQL
W poniższej próbce przedstawiono:

1. Połączona usługa typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Połączona usługa typu [AzureStorage](#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [blobSource](#copy-activity-properties) i [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych z obiektu blob platformy Azure do tabeli SQL platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą Azure SQL:**

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
**Usługa połączona z usługą Azure Storage:**

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
Usługa Azure Data Factory obsługuje dwa typy usług połączonych usługi Azure Storage: **AzureStorage** i **AzureStorageSas**. W przypadku pierwszego z nich należy określić parametry połączenia zawierające klucz konta, a dla późniejszego identyfikatora Uri (Sygnatura dostępu współdzielonego). Szczegółowe informacje można znaleźć w sekcji [Usługi połączone.](#linked-service-properties)

**Zestaw danych wejściowych obiektów Blob platformy Azure:**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa części roku, miesiąca i dnia czasu rozpoczęcia i nazwy pliku, która używa części godzinowej godziny rozpoczęcia. "external": ustawienie "true" informuje fabrykę danych, że tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

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
**Zestaw danych wyjściowych SQL platformy Azure:**

Przykładowy kopiuje dane do tabeli o nazwie "MyTable" w bazie danych SQL platformy Azure. Utwórz tabelę w bazie danych SQL platformy Azure z taką samą liczbą kolumn, jak oczekujesz pliku CSV obiektów blob. Nowe wiersze są dodawane do tabeli co godzinę.

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
**Działanie kopiowania w potoku ze źródłem obiektów Blob i ujściem SQL:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **BlobSource** i typ **ujścia** jest ustawiony na **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Przykład JSON: kopiowanie danych z usługi Azure SQL do obiektu blob platformy Azure
W poniższej próbce przedstawiono:

1. Połączona usługa typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Połączona usługa typu [AzureStorage](#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem Kopiowania, który używa [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) i [BlobSink](#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych z tabeli SQL platformy Azure do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą Azure SQL:**

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
**Usługa połączona z usługą Azure Storage:**

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
Usługa Azure Data Factory obsługuje dwa typy usług połączonych usługi Azure Storage: **AzureStorage** i **AzureStorageSas**. W przypadku pierwszego z nich należy określić parametry połączenia zawierające klucz konta, a dla późniejszego identyfikatora Uri (Sygnatura dostępu współdzielonego). Szczegółowe informacje można znaleźć w sekcji [Usługi połączone.](#linked-service-properties)

**Zestaw danych wejściowych SQL platformy Azure:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w usłudze Azure SQL i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

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

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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

**Działanie kopiowania w potoku ze źródłem SQL i ujściem obiektu Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **SqlSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

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
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
