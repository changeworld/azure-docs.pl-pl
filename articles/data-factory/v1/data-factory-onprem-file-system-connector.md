---
title: Kopiowanie danych do/z systemu plików przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak kopiować dane do lokalnego systemu plików i z niego korzystając z usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d298c83c0c1a0f33f28644e2e467ad5035300221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265937"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopiowanie danych do lokalnego systemu plików i z niego przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-file-system-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-file-system.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik systemu plików w wersji 2](../connector-file-system.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych do/z lokalnego systemu plików. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z lokalnego systemu plików** do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Dane można kopiować z następujących magazynów danych **do lokalnego systemu plików:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Działanie kopiowania nie powoduje usunięcia pliku źródłowego po pomyślnym skopiowaniu go do miejsca docelowego. Jeśli chcesz usunąć plik źródłowy po pomyślnej kopii, utwórz działanie niestandardowe, aby usunąć plik i użyć działania w potoku.

## <a name="enabling-connectivity"></a>Włączanie łączności
Usługa Data Factory obsługuje łączenie się z lokalnym systemem plików i z niego za pośrednictwem **bramy zarządzania danymi.** Aby usługa Data Factory została zainstalowana w środowisku lokalnym, należy zainstalować bramę zarządzania danymi w środowisku lokalnym, aby połączyć się z dowolnym obsługiwanym lokalnym magazynem danych, w tym z systemem plików. Aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy, zobacz [Przenoszenie danych między źródłami lokalnymi a chmurą](data-factory-move-data-between-onprem-and-cloud.md)za pomocą bramy zarządzania danymi . Oprócz bramy zarządzania danymi nie trzeba instalować żadnych innych plików binarnych, aby komunikować się z lokalnym systemem plików i z niego. Bramę zarządzania danymi należy zainstalować i używać, nawet jeśli system plików znajduje się na maszynie Wirtualnej usługi Azure IaaS. Aby uzyskać szczegółowe informacje o bramie, zobacz [Brama zarządzania danymi](data-factory-data-management-gateway.md).

Aby użyć udziału plików systemu Linux, zainstaluj [sambę](https://www.samba.org/) na serwerze Linux i zainstaluj bramę zarządzania danymi na serwerze windows. Instalowanie bramy zarządzania danymi na serwerze Linux nie jest obsługiwane.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane do/z systemu plików przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **fabryki danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z magazynu obiektów blob platformy Azure do lokalnego systemu plików utworzysz dwie połączone usługi w celu połączenia lokalnego systemu plików i konta magazynu platformy Azure z fabryką danych. Właściwości usługi połączone, które są specyficzne dla lokalnego systemu plików, zobacz [sekcję właściwości usługi połączonej.](#linked-service-properties)
3. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. W przykładzie wymienionym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektu blob i folder zawierający dane wejściowe. I należy utworzyć inny zestaw danych, aby określić nazwę folderu i pliku (opcjonalnie) w systemie plików. Właściwości zestawu danych, które są specyficzne dla lokalnego systemu plików, zobacz sekcję [właściwości zestawu danych.](#dataset-properties)
4. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W przykładzie wspomniano wcześniej, należy użyć BlobSource jako źródło i FileSystemSink jako ujście dla działania kopiowania. Podobnie w przypadku kopiowania z lokalnego systemu plików do usługi Azure Blob Storage, należy użyć FileSystemSource i BlobSink w działania kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla lokalnego systemu plików, zobacz [sekcję właściwości działania kopiowania.](#copy-activity-properties) Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładów z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do/z systemu plików, zobacz sekcję [przykłady JSON](#json-examples-for-copying-data-to-and-from-file-system) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla systemu plików:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Lokalny system plików można połączyć z fabryką danych platformy Azure z usługą połączony z **lokalnym serwerem plików.** Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla lokalnej usługi połączonego serwera plików.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Upewnij się, że właściwość type jest ustawiona **na OnPremisesFileServer**. |Tak |
| host |Określa ścieżkę główną folderu, który chcesz skopiować. Użyj znaku ucieczki \ ' dla znaków specjalnych w ciągu. Zobacz [przykłady połączonych definicji usługi i zestawów danych,](#sample-linked-service-and-dataset-definitions) aby zapoznać się z przykładami. |Tak |
| userid |Określ identyfikator użytkownika, który ma dostęp do serwera. |Nie (jeśli wybierzesz encryptedCredential) |
| hasło |Określ hasło użytkownika (identyfikator użytkownika). |Nie (jeśli wybierzesz encryptedCredential |
| encryptedCredential |Określ zaszyfrowane poświadczenia, które można uzyskać, uruchamiając polecenie cmdlet New-AzDataFactoryEncryptValue. |Nie (jeśli zdecydujesz się określić identyfikator użytkownika i hasło w postaci zwykłego tekstu) |
| nazwa bramy |Określa nazwę bramy, której usługa Data Factory powinna używać do łączenia się z lokalnym serwerem plików. |Tak |


### <a name="sample-linked-service-and-dataset-definitions"></a>Przykładowe połączone definicje usług i zestawów danych
| Scenariusz | Host w definicji usługi połączonej | folderPath w definicji zestawu danych |
| --- | --- | --- |
| Folder lokalny na komputerze bramy zarządzania danymi: <br/><br/>Przykłady: D:\\ \* lub D:\folder\podfolder\\\* |D:\\ \\ (dla bramy zarządzania danymi 2.0 i nowszych wersji) <br/><br/> localhost (dla starszych wersji niż Brama zarządzania danymi 2.0) |. lub\\\\podfolder folderów (dla bramy zarządzania danymi 2.0 i nowszych wersji) \\ \\ <br/><br/>D:\\ \\ lub\\\\D: podfolder folderów\\\\(dla wersji bramy poniżej 2.0) |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\podfolder\\ \* \\ \\folderu\\udostępniania\\\\myserver\\lub folderu udostępniania myserver\\\* |\\\\\\\\udział myserver\\\\ |. lub podfolder folderu\\\\ \\ \\ |

>[!NOTE]
>Podczas tworzenia za pośrednictwem interfejsu użytkownika, nie trzeba wprowadzać`\\`podwójne ukośnik odwrotny ( ), aby uciec jak to zrobić za pośrednictwem JSON, określ pojedynczy ukośnik odwrotny.

### <a name="example-using-username-and-password-in-plain-text"></a>Przykład: używanie nazwy użytkownika i hasła w postaci zwykłego tekstu

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Przykład: Używanie encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md). Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych.

Sekcja typeProperties jest inna dla każdego typu zestawu danych. Zawiera informacje, takie jak lokalizacja i format danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **FileShare** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Określa ścieżkę podrzędną do folderu. Użyj znaku ucieczki '\' dla znaków specjalnych w ciągu. Filtr symboli wieloznacznych nie jest obsługiwany. Zobacz [przykłady połączonych definicji usługi i zestawów danych,](#sample-linked-service-and-dataset-definitions) aby zapoznać się z przykładami.<br/><br/>Tę właściwość można połączyć z **partitionBy,** aby ścieżki folderów były oparte na data-godzinach rozpoczęcia/zakończenia plasterka. |Tak |
| fileName |Określ nazwę pliku w **folderPath,** jeśli chcesz, aby tabela odwoływała się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli **nazwa pliku** nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie jest określony w ujściu działania, nazwa wygenerowanego pliku jest w następującym formacie: <br/><br/>`Data.<Guid>.txt`(Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| Filefilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików. <br/><br/>Dozwolone wartości to: `*` (wiele `?` znaków) i (pojedynczy znak).<br/><br/>Przykład 1: "fileFilter": "*.log"<br/>Przykład 2: "fileFilter": 2014-1-?. txt"<br/><br/>Należy zauważyć, że fileFilter ma zastosowanie do wejściowego zestawu danych FileShare. |Nie |
| partitionedBy |Za pomocą partitionedBy można określić dynamiczny folderPath/fileName dla danych szeregów czasowych. Przykładem jest folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można używać fileName i fileFilter jednocześnie.

### <a name="using-partitionedby-property"></a>Korzystanie z partitionedBy właściwości
Jak wspomniano w poprzedniej sekcji, można określić folder dynamicznyPath i nazwa pliku dla danych szeregów czasowych z **partitionedBy** właściwości, [funkcje fabryki danych i zmiennych systemowych](data-factory-functions-variables.md).

Aby dowiedzieć się więcej szczegółów na temat zestawów danych szeregów czasowych, planowania i wycinków, zobacz [Tworzenie zestawów danych,](data-factory-create-datasets.md) [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md)oraz Tworzenie [potoków](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Próbka 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W tym przykładzie {Slice} jest zastępowany wartością zmiennej systemowej Fabryki danych SliceStart w formacie (RRRRYMMDDHH). SliceStart odnosi się do czasu rozpoczęcia plasterka. Ścieżka folderu jest inna dla każdego plasterka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Próbka 2:

```JSON
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

W tym przykładzie rok, miesiąc, dzień i godzina SliceStart są wyodrębniane do oddzielnych zmiennych, które używane są właściwości folderPath i fileName.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań. Mając na uwadze, właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania.

W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze. W przypadku przenoszenia danych z lokalnego systemu plików typ źródła w działaniu kopiowania jest ustawiany na **FileSystemSource**. Podobnie w przypadku przenoszenia danych do lokalnego systemu plików typ ujścia w działaniu kopiowania jest ustawiony na **FileSystemSink**. Ta sekcja zawiera listę właściwości obsługiwanych przez FileSystemSource i FileSystemSink.

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, fałsz (domyślnie) |Nie |

**FileSystemSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowanie kopiowania, gdy źródłem jest BlobSource lub FileSystem. |**PreserveHierarchy:** Zachowuje hierarchię plików w folderze docelowym. Oznacza to, że względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak względna ścieżka pliku docelowego do folderu docelowego.<br/><br/>**SpłaszczyćHierarchii:** Wszystkie pliki z folderu źródłowego są tworzone na pierwszym poziomie folderu docelowego. Pliki docelowe są tworzone z automatyczniegenerowaną nazwą.<br/><br/>**Pliki scalania:** Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/nazwa obiektu blob jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. |Nie |

### <a name="recursive-and-copybehavior-examples"></a>przykłady rekursywne i copyBehavior
W tej sekcji opisano wynikowe zachowanie operacji Kopiowanie dla różnych kombinacji wartości dla właściwości cyklicznego i copyBehavior.

| wartość cykliczna | wartość copyBehavior | Wynikowe działanie |
| --- | --- | --- |
| true |preserveHierarchy |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z taką samą strukturą jak źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 |
| true |spłaszczyćHierarchy |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku5 |
| true |scalanie plików |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość File1 + File2 + File3 + File4 + File 5 jest scalana w jeden plik o automatycznie wygenerowanej nazwie pliku. |
| false |preserveHierarchy |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest pobierany. |
| false |spłaszczyćHierarchy |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest pobierany. |
| false |scalanie plików |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość file1 + File2 jest scalana w jeden plik o automatycznie wygenerowanej nazwie pliku.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatycznie wygenerowana nazwa pliku1<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest pobierany. |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykuł o szczegółach.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Przykłady JSON do kopiowania danych do i z systemu plików
Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane do i z lokalnego systemu plików i magazynu obiektów Blob platformy Azure. Można jednak skopiować dane *bezpośrednio* z dowolnego źródła do dowolnego ujścia wymienione w [obsługiwanych źródeł i pochłaniacze](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Przykład: Kopiowanie danych z lokalnego systemu plików do magazynu obiektów blob platformy Azure
W tym przykładzie pokazano, jak skopiować dane z lokalnego systemu plików do magazynu obiektów Blob platformy Azure. W przykładzie są dostępne następujące jednostki Data Factory:

* Połączona usługa typu [OnPremisesFileServer](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Poniższy przykład kopiuje dane szeregów czasowych z lokalnego systemu plików do magazynu obiektów Blob platformy Azure co godzinę. Właściwości JSON, które są używane w tych próbkach są opisane w sekcjach po próbkach.

W pierwszym kroku skonfiguruj bramę zarządzania danymi zgodnie z instrukcjami w aplikacji [Przenoszenie danych między źródłami lokalnymi a chmurą za pomocą bramy zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md).

**Usługa połączona z lokalnym serwerem plików:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Firma Microsoft zaleca użycie **właściwości encryptedCredential** zamiast **właściwości identyfikatora użytkownika** i **hasła.** Szczegółowe informacje na temat tej połączonej usługi można znaleźć w [usłudze połączonej serwera plików.](#linked-service-properties)

**Usługa połączona z usługą Azure Storage:**

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

**Lokalny zestaw danych wejściowych systemu plików:**

Dane są pobierane z nowego pliku co godzinę. Właściwości folderPath i fileName są określane na podstawie czasu rozpoczęcia plasterka.

Ustawienie `"external": "true"` informuje fabrykę danych, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
      ]
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

**Zestaw danych wyjściowych magazynu obiektów Blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny godziny godziny rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Działanie kopiowania w potoku ze źródłem systemu plików i ujściem obiektu Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **FileSystemSource,** a typ **ujścia** jest ustawiony na **BlobSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Przykład: Kopiowanie danych z usługi Azure SQL Database do lokalnego systemu plików
W poniższej próbce przedstawiono:

* Połączona usługa typu [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Połączona usługa typu [OnPremisesFileServer](#linked-service-properties).
* Wejściowy zestaw danych typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Wyjściowy zestaw danych typu [FileShare](#dataset-properties).
* Potok z działaniem kopiowania, który używa [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) i [FileSystemSink](#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych z tabeli SQL platformy Azure do lokalnego systemu plików co godzinę. Właściwości JSON, które są używane w tych próbkach są opisane w sekcjach po próbkach.

**Usługa połączona z usługą Azure SQL Database:**

```JSON
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

**Usługa połączona z lokalnym serwerem plików:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Firma Microsoft zaleca używanie **właściwości encryptedCredential** zamiast używania właściwości **identyfikatora użytkownika** i **hasła.** Szczegółowe informacje na temat tej połączonej usługi można znaleźć w [usłudze połączonej systemu plików.](#linked-service-properties)

**Zestaw danych wejściowych SQL platformy Azure:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w usłudze Azure SQL i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie ``“external”: ”true”`` informuje fabrykę danych, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

```JSON
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

**Lokalny zestaw danych wyjściowych systemu plików:**

Dane są kopiowane do nowego pliku co godzinę. FolderPath i fileName dla obiektu blob są określane na podstawie czasu rozpoczęcia plasterka.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
      ]
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

**Działanie kopiowania w potoku ze źródłem SQL i ujściem systemu plików:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **SqlSource,** a typ **ujścia** jest ustawiony na **FileSystemSink**. Kwerenda SQL, która jest określona dla **właściwości SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Można również mapować kolumny z źródłowego zestawu danych na kolumny z zestawu danych ujścia w definicji działania kopiowania. Aby uzyskać szczegółowe informacje, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
 Aby dowiedzieć się więcej o kluczowych czynnikach wpływających na wydajność przenoszenia danych (aktywność kopiowania) w usłudze Azure Data Factory i na różne sposoby jej optymalizacji, zobacz [przewodnik po wydajności działania kopiowania i dostrajaniu.](data-factory-copy-activity-performance.md)
