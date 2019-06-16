---
title: Kopiowanie danych do/z systemu plików przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do i z systemu plików w środowisku lokalnym za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 40086924731876dc44d9651ca46814149dba52f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122449"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopiowanie danych do i z systemu plików w środowisku lokalnym za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-onprem-file-system-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-file-system.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika systemu plików w wersji 2](../connector-file-system.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych do/z lokalnego systemu plików. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z lokalnym systemem plików** się następujące dane są przechowywane:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących magazynów danych **systemu plików w środowisku lokalnym**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Działanie kopiowania nie powoduje usunięcia pliku źródłowego, po pomyślnym są kopiowane do lokalizacji docelowej. Jeśli zachodzi potrzeba usunięcia pliku źródłowego po kopiowania zakończonego powodzeniem, należy utworzyć niestandardowe działanie, aby usunąć plik i użyć działania w potoku.

## <a name="enabling-connectivity"></a>Włączanie połączenia
Usługa Data Factory obsługuje łączenie z lokalnym systemem plików za pośrednictwem i **bramy zarządzania danymi**. Należy zainstalować bramę zarządzania danymi w środowisku lokalnych usługi Data Factory nawiązać połączenie z dowolnego obsługiwanego lokalnego magazynu danych, w tym system plików. Aby dowiedzieć się o bramy zarządzania danymi i instrukcje krok po kroku na temat konfigurowania bramy, zobacz [przenoszenie danych między źródłami lokalnymi i w chmurze przy użyciu bramy zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md). Niezależnie od bramą zarządzania danymi żadne inne pliki binarne muszą być zainstalowane do komunikowania się z lokalnym systemem plików i. Należy zainstalować i używać bramy zarządzania danymi, nawet jeśli system plików znajduje się w maszynie Wirtualnej IaaS platformy Azure. Aby uzyskać szczegółowe informacje na temat bramy, zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md).

Aby użyć udziału pliku systemu Linux, należy zainstalować [Samba](https://www.samba.org/) na serwer z systemem Linux i zainstaluj bramę zarządzania danymi na serwerze Windows. Instalowanie bramy zarządzania danymi na serwerze z systemem Linux nie jest obsługiwane.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z systemu plików przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków.
2. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych. Na przykład jeśli kopiujesz dane z magazynu obiektów blob platformy Azure do lokalnego systemu plików, utworzysz dwie połączone usługi, aby połączyć systemu plików w środowisku lokalnym i konto usługi Azure storage z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla środowiska lokalnego systemu plików, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.
3. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. W tym przykładzie wymienione w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob oraz folder, który zawiera dane wejściowe. I utwórz inny zestaw danych, aby określić folder i nazwę pliku (opcjonalnie) w systemie plików. Dla właściwości zestawu danych, które są specyficzne dla środowiska lokalnego systemu plików, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. W przykładzie, o których wspomniano wcześniej możesz użyć BlobSource jako źródła i FileSystemSink jako obiekt sink dla działania kopiowania. Podobnie system plików w środowisku lokalnym są kopiowane do usługi Azure Blob Storage, należy użyć FileSystemSource i BlobSink w działaniu kopiowania. Właściwości działania kopiowania, które są specyficzne dla środowiska lokalnego systemu plików, zobacz [właściwości działania kopiowania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje na temat korzystania z magazynu danych jako źródła lub ujścia kliknij link w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Aby uzyskać przykłady przy użyciu definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z systemu plików, zobacz [JSON przykłady](#json-examples-for-copying-data-to-and-from-file-system) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do systemu plików:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Możesz połączyć i lokalnym systemem plików usługi Azure data factory przy użyciu **na lokalnym serwerze plików** połączoną usługę. Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla serwera plików dla lokalnej połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Upewnij się, że właściwość typu jest ustawiona na **OnPremisesFileServer**. |Tak |
| host |Określa ścieżkę katalogu głównego folderu, który chcesz skopiować. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [przykładowe połączone definicje usługi i zestaw danych](#sample-linked-service-and-dataset-definitions) przykłady. |Yes |
| userid |Określ identyfikator użytkownika, który ma dostęp do serwera. |Nie (Jeśli wybierzesz encryptedCredential) |
| password |Określ hasło dla użytkownika (nazwa użytkownika). |Nie (Jeśli wybierzesz encryptedCredential |
| encryptedCredential |Określ zaszyfrowane poświadczenia, które można uzyskać, uruchamiając polecenie cmdlet New-AzDataFactoryEncryptValue. |Nie (Jeśli chcesz określić identyfikator użytkownika i hasło w postaci zwykłego tekstu) |
| gatewayName |Określa nazwę bramy, która Data Factory powinna używać do łączenia się z serwerem plików lokalnych. |Tak |


### <a name="sample-linked-service-and-dataset-definitions"></a>Przykładowe połączona usługa i definicje zestawów danych
| Scenariusz | Hosting w definicji połączonej usługi | folderPath w definicji zestawu danych |
| --- | --- | --- |
| Folder lokalny na maszynie bramy zarządzania danymi: <br/><br/>Przykłady: D:\\ \* lub D:\folder\subfolder\\* |D:\\ \\ (dla danych bramy zarządzania w wersji 2.0 i nowsze wersje) <br/><br/> localhost (dla starszych niż danych bramy zarządzania w wersji 2.0) |. \\ \\ lub folderu\\\\podfolder (dla danych bramy zarządzania w wersji 2.0 i nowsze wersje) <br/><br/>D:\\ \\ lub D:\\\\folderu\\\\podfolder (dla bramy w wersji 2.0) |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\myserver\\udostępnianie\\ \* lub \\ \\myserver\\udostępnianie\\folderu\\podfolderów\\* |\\\\\\\\MYSERVER\\\\udostępniania |. \\ \\ lub folderu\\\\podfolderów |

>[!NOTE]
>Podczas tworzenia, za pośrednictwem interfejsu użytkownika, nie jest potrzebny do wprowadzenia podwójny ukośnik odwrotny (`\\`) aby wyjść, jak za pomocą formatu JSON, należy określić pojedynczy ukośnik odwrotny.

### <a name="example-using-username-and-password-in-plain-text"></a>Przykład: Przy użyciu nazwy użytkownika i hasła w postaci zwykłego tekstu

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

### <a name="example-using-encryptedcredential"></a>Przykład: Za pomocą encryptedcredential

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
Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md). Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów w zestawie danych.

W sekcji typeProperties różni się dla każdego typu zestawu danych. Zawiera informacje, takie jak lokalizacja i format danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **FileShare** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Określa podrzędna ścieżka do folderu. Użyj znaku ucieczki "\' dla znaków specjalnych w ciągu. Filtr z symbolami wieloznacznymi nie jest obsługiwana. Zobacz [przykładowe połączone definicje usługi i zestaw danych](#sample-linked-service-and-dataset-definitions) przykłady.<br/><br/>Można połączyć tę właściwość z **partitionBy** mieć folderu ścieżki, w oparciu o wycinek rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** chcącym tabeli do odwoływania się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Gdy **fileName** nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie została określona w ujścia działania nazwę wygenerowanego pliku znajduje się w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr służący do wybierają podzbiór plików w ścieżce folderu, a nie wszystkich plików. <br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: "obiektu fileFilter": "* .log"<br/>Przykład 2: "obiektu fileFilter": 2014 - 1-?. txt"<br/><br/>Należy zauważyć, że tego obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych w udziale plików. |Nie |
| partitionedBy |PartitionedBy służy do określania dynamiczne folderPath/nazwę pliku do danych szeregów czasowych. Przykładem jest folderPath sparametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można użyć nazwy pliku i obiektu fileFilter jednocześnie.

### <a name="using-partitionedby-property"></a>Przy użyciu właściwości partitionedBy
Jak wspomniano w poprzedniej sekcji, można określić folderPath dynamiczne i nazwę pliku dla danych szeregów czasowych z **partitionedBy** właściwości [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md).

Aby poznać szczegółowe informacje na temat zestawów danych szeregów czasowych, planowanie i wycinków, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md), [planowanie i wykonywanie](data-factory-scheduling-and-execution.md), i [tworzenia potoków](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Przykład 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W tym przykładzie {wycinek} jest zastępowana wartością zmiennej systemowej usługi Data Factory SliceStart w formacie (YYYYMMDDHH). SliceStart odnosi się do rozpoczęcie wycinka. FolderPath różni się dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Przykład 2:

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

W tym przykładzie rok, miesiąc, dzień i godzina SliceStart są wyodrębniane do oddzielnych zmiennych, korzystających z właściwości folderPath i nazwę pliku.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań. Natomiast właściwości dostępnych w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań.

Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia. Jeśli chcesz przenieść dane z lokalnego systemu plików, należy ustawić typ źródła w działanie kopiowania w celu **FileSystemSource**. Podobnie, jeśli dane są przenoszone do środowiska lokalnego systemu plików, należy ustawić typ ujścia w działaniu kopiowania, aby **FileSystemSink**. Ta sekcja zawiera listę obsługiwanych przez FileSystemSource i FileSystemSink właściwości.

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| recursive |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. |Wartość true, False (domyślnie) |Nie |

**FileSystemSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest BlobSource lub systemu plików. |**PreserveHierarchy:** Zachowuje hierarchii plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego, do folderu docelowego.<br/><br/>**FlattenHierarchy:** Wszystkie pliki z folderu źródłowego są tworzone w pierwszy poziom folderu docelowego. Pliki docelowe są tworzone z nazwą wygenerowany automatycznie.<br/><br/>**MergeFiles:** Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli określono nazwę pliku nazwy/obiekt blob, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest to nazwa pliku generowanych automatycznie. |Nie |

### <a name="recursive-and-copybehavior-examples"></a>przykładów rekurencyjnych i copyBehavior
W tej sekcji opisano wynikowe zachowania operacji kopiowania dla różnych kombinacji wartości dla właściwości cyklicznego i copyBehavior.

| wartość cyklicznej | wartość copyBehavior | Wynikowe zachowania |
| --- | --- | --- |
| true |preserveHierarchy |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródła:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File5 |
| true |mergeFiles |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + 5 plików zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie plik. |
| false |preserveHierarchy |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |mergeFiles |W przypadku folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie plik.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykuł na temat szczegółów.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Przykłady JSON na potrzeby kopiowania danych do i z systemu plików
W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak kopiować dane do i z systemu plików w środowisku lokalnym i usługą Azure Blob storage. Jednak może kopiować dane *bezpośrednio* z dowolnego źródła do dowolnego ujścia, na liście [obsługiwane źródłami i ujściami](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Przykład: Kopiowanie danych z lokalnego systemu plików do usługi Azure Blob storage
Ten przykład pokazuje, jak skopiować dane z lokalnego systemu plików do usługi Azure Blob storage. Przykład obejmuje następujących jednostek usługi Data Factory:

* Połączonej usługi typu [OnPremisesFileServer](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Poniższy przykład kopiuje dane szeregów czasowych z lokalnego systemu plików do magazynu obiektów Blob platformy Azure co godzinę. Właściwości kodu JSON, które są używane w tych przykładach są opisane w sekcjach po próbkach.

Pierwszym krokiem skonfigurować bramę zarządzania danymi zgodnie z instrukcjami w [przenoszenie danych między źródłami lokalnymi i w chmurze przy użyciu bramy zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md).

**Serwer plików w środowisku lokalnym połączoną usługę:**

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

Firma Microsoft zaleca używanie **encryptedCredential** właściwości zamiast **userid** i **hasło** właściwości. Zobacz [serwera plików połączoną usługę](#linked-service-properties) Aby uzyskać szczegółowe informacje na ten temat połączoną usługę.

**Połączona usługa Azure Storage:**

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

**Lokalnego pliku wejściowego zestawu danych systemu:**

Dane są pobierane z nowego pliku co godzinę. Właściwości folderPath i nazwa pliku są określane na podstawie czasu uruchomienia wycinka.

Ustawienie `"external": "true"` usługi Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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

**Usługa Azure Blob storage wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i godzinę części czas rozpoczęcia.

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

**Działanie kopiowania w potoku za pomocą systemu plików źródła i ujścia obiektu Blob:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **FileSystemSource**, i **ujścia** ustawiono typ **BlobSink**.

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
Poniższy przykład pokazuje:

* Połączonej usługi typu [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Połączonej usługi typu [OnPremisesFileServer](#linked-service-properties).
* Wejściowy zestaw danych o typie [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Wyjściowy zestaw danych o typie [FileShare](#dataset-properties).
* Potok z działaniem kopiowania, która używa [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) i [FileSystemSink](#copy-activity-properties).

Przykładowy skrypt kopiuje dane szeregów czasowych z tabeli usługi Azure SQL do systemu plików w środowisku lokalnym co godzinę. Właściwości kodu JSON, które są używane w tych przykładach są opisane w sekcjach po próbkach.

**Usługa Azure SQL Database połączoną usługę:**

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

**Serwer plików w środowisku lokalnym połączoną usługę:**

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

Firma Microsoft zaleca używanie **encryptedCredential** zamiast przy użyciu właściwości **userid** i **hasło** właściwości. Zobacz [System plików połączoną usługę](#linked-service-properties) Aby uzyskać szczegółowe informacje na ten temat połączoną usługę.

**Usługa Azure SQL wejściowy zestaw danych:**

Przykład założono, że utworzono tabelę "MyTable" w usłudze Azure SQL i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie ``“external”: ”true”`` usługi Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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

**W środowisku lokalnym plikiem systemu wyjściowego zestawu danych:**

Dane są kopiowane do nowego pliku co godzinę. FolderPath i nazwę pliku obiektu blob są określane na podstawie czasu uruchomienia wycinka.

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

**Działanie kopiowania w potoku za pomocą programu SQL źródła i ujścia systemu plików:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **SqlSource**i **ujścia** ustawiono typ **FileSystemSink**. Zapytanie SQL, który jest określony dla **SqlReaderQuery** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

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

Można również mapować kolumny z zestawu danych źródłowych do kolumn z zestaw danych ujścia w definicji działania kopiowania. Aby uzyskać więcej informacji, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
 Aby dowiedzieć się więcej na temat kluczowych czynników, które mają wpływ na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją, zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](data-factory-copy-activity-performance.md).
