---
title: Przenoszenie danych z lokalnym systemem plików HDFS | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z lokalnym systemem plików HDFS, za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4ae5b3b9016af0d35e40d66d527e51230e0f11ce
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527221"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Przenoszenie danych z lokalnym systemem plików HDFS, za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-hdfs-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-hdfs.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika systemu plików HDFS w wersji 2](../connector-hdfs.md).

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z lokalnym systemem plików HDFS. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z systemu plików HDFS, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Data factory obsługuje obecnie tylko przenosi dane z lokalnym systemem plików HDFS do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych na lokalnym systemem plików HDFS.

> [!NOTE]
> Działanie kopiowania nie powoduje usunięcia pliku źródłowego, po pomyślnym są kopiowane do lokalizacji docelowej. Jeśli zachodzi potrzeba usunięcia pliku źródłowego po kopiowania zakończonego powodzeniem, należy utworzyć niestandardowe działanie, aby usunąć plik i użyć działania w potoku. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Włączanie połączenia
Usługa Data Factory obsługuje nawiązywania połączenia z lokalnym systemem plików HDFS, przy użyciu bramy zarządzania danymi. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy. Za pomocą bramy, aby połączyć z systemu plików HDFS, nawet wtedy, gdy jest ona hostowana na maszynie Wirtualnej IaaS platformy Azure.

> [!NOTE]
> Upewnij się, że brama zarządzania danymi można uzyskać dostęp do **wszystkich** [nazwa węzła serwera]: [nazwa węzła portu] i [serwery węzła danych]: [danych węzła port] klastra Hadoop. Domyślna [nazwa węzła port] to 50070, a domyślna [danych węzła port] to 50075.

Chociaż bramy można zainstalować na tym samym komputerze w środowisku lokalnym lub maszynie Wirtualnej platformy Azure jako systemu plików HDFS, zaleca się zainstalowanie bramy na osobne maszyny/Azure IaaS VM. Posiadanie bramy na osobnej maszynie zmniejszenie rywalizacji o zasoby i zwiększa wydajność. Podczas instalowania bramy na osobnym komputerze, komputer powinien móc dostęp do komputera za pomocą systemu plików HDFS.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane ze źródła systemu plików HDFS przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Przykład definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych systemu plików HDFS, zobacz [przykład kodu JSON: Kopiowanie danych z lokalnym systemem plików HDFS do usługi Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonego w systemie plików hdfs:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Połączona usługa łączy magazyn danych do usługi data factory. Tworzenie połączonej usługi typu **systemu plików Hdfs** do łączenia z lokalnym systemem plików HDFS z fabryką danych. Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla systemu plików HDFS połączoną usługę.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **Hdfs** |Yes |
| Url |Adres URL do systemu plików HDFS |Yes |
| Element authenticationType |Anonimowe lub Windows. <br><br> Aby użyć **uwierzytelnianie Kerberos** łącznika systemu plików HDFS można znaleźć w [w tej sekcji](#use-kerberos-authentication-for-hdfs-connector) odpowiednio skonfigurować swoje środowisko lokalne. |Yes |
| userName |Uwierzytelnianie nazwy użytkownika dla Windows. Dla uwierzytelniania Kerberos, określ `<username>@<domain>.com`. |Tak (dla uwierzytelniania Windows) |
| hasło |Hasło do uwierzytelniania Windows. |Tak (dla uwierzytelniania Windows) |
| gatewayName |Nazwa bramy, która powinna być używana w usłudze Data Factory, połączyć się z systemu plików HDFS. |Yes |
| encryptedCredential |[Nowe AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) poświadczeń dostępu do danych wyjściowych. |Nie |

### <a name="using-anonymous-authentication"></a>Przy użyciu uwierzytelniania anonimowego

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Przy użyciu uwierzytelniania Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **FileShare** (w tym system plików HDFS w zestawie danych) ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do folderu. Przykład: `myfolder`<br/><br/>Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Na przykład: w przypadku folder\subfolder, należy określić folder\\\\podfolder i d:\samplefolder, określ d:\\\\folder_przykładowy.<br/><br/>Można połączyć tę właściwość z **partitionBy** mieć folderu ścieżki, w oparciu o wycinek rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** chcącym tabeli do odwoływania się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku byłoby w następującym tego formatu: <br/><br/>`Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwa_pliku danych szeregów czasowych. Przykład: folderPath, sparametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można jednocześnie używać nazwy pliku i obiektu fileFilter.

### <a name="using-partionedby-property"></a>Przy użyciu właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić folderPath dynamiczne i nazwę pliku dla danych szeregów czasowych z **partitionedBy** właściwości [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md).

Aby dowiedzieć się więcej na temat zestawów danych serii czasu, planowanie i wycinków, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md), [planowanie i wykonywanie](data-factory-scheduling-and-execution.md), i [tworzenie potoków](data-factory-create-pipelines.md) artykułów.

#### <a name="sample-1"></a>Przykład 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {wycinek} jest zastępowana wartością zmiennej systemowej SliceStart fabryki danych w formacie (YYYYMMDDHH) określona. Parametru SliceStart odnosi się do rozpoczęcie wycinka. FolderPath różni się dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

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
W tym przykładzie rok, miesiąc, dzień i godzina SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i nazwę pliku.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości, które są dostępne w sekcji typeProperties działania zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Działanie kopiowania, gdy źródłem jest typu **FileSystemSource** w sekcji typeProperties dostępne są następujące właściwości:

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. |Wartość true, False (domyślnie) |Nie |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykuł na temat szczegółów.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z lokalnym systemem plików HDFS do obiektów Blob platformy Azure
Ten przykład pokazuje, jak skopiować dane z lokalnym systemem plików HDFS w usłudze Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.  

Przykład zawiera definicje JSON dotyczące następujących jednostek usługi Data Factory. Te definicje umożliwia tworzenie potoku w celu kopiowania danych z systemu plików HDFS do usługi Azure Blob Storage za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Połączonej usługi typu [OnPremisesHdfs](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z lokalnym systemem plików HDFS do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem należy skonfigurować bramę zarządzania danymi. Zgodnie z instrukcjami w [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**System plików HDFS połączoną usługę:** W tym przykładzie użyto uwierzytelniania Windows. Zobacz [systemu plików HDFS połączoną usługę](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Połączona usługa Azure Storage:**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**System plików HDFS wejściowy zestaw danych:** Ten zestaw danych odwołuje się do folderu systemu plików HDFS DataTransfer/UnitTest /. Potok kopiuje wszystkie pliki w tym folderze, do miejsca docelowego.

Ustawienie "external": "true" informuje usługa Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Działanie kopiowania w potoku za pomocą systemu plików źródła i ujścia obiektu Blob:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do używania tych danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **FileSystemSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **zapytania** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Uwierzytelnianie Kerberos dla łącznika systemu plików HDFS
Dostępne są dwie opcje do skonfigurowania w lokalnym środowisku tak, aby korzystać z uwierzytelniania Kerberos w łączniku systemu plików HDFS. Można wybrać jeden lepiej odpowiada jego potrzebom tej sprawy.
* Opcja 1: [Dołączanie maszyny bramy obszaru Kerberos](#kerberos-join-realm)
* Opcja 2: [Włącz wzajemnego zaufania między domeną Windows i protokół Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opcja 1: Dołączanie maszyny bramy obszaru Kerberos

#### <a name="requirement"></a>Wymaganie:

* Na maszynie bramy należy do dołączenia do obszaru protokołu Kerberos i nie można dołączyć do dowolnej domeny Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować:

**Na maszynie bramy:**

1.  Uruchom **Ksetup** narzędzie, aby skonfigurować serwer Centrum dystrybucji KLUCZY Kerberos i obszaru.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszaru Kerberos różni się od domeny Windows. Można to osiągnąć przez ustawienie obszaru protokołu Kerberos i dodanie serwera KDC w następujący sposób. Zastąp *REALM.COM* z własnego obszaru odpowiednich zgodnie z potrzebami.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Uruchom ponownie** maszyny po wykonaniu tych poleceń 2.

2.  Sprawdź konfigurację przy użyciu **Ksetup** polecenia. Dane wyjściowe powinny być takie jak:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**W usłudze Azure Data Factory:**

* Konfigurowanie przy użyciu łącznika systemu plików HDFS **uwierzytelniania Windows** wraz z Twoja nazwa główna protokołu Kerberos i hasło, aby połączyć się ze źródłem danych systemu plików HDFS. Sprawdź [właściwości usługi połączonej systemu plików HDFS](#linked-service-properties) sekcji Szczegóły konfiguracji.

### <a name="kerberos-mutual-trust"></a>Opcja 2: Włącz wzajemnego zaufania między domeną Windows i protokół Kerberos

#### <a name="requirement"></a>Wymaganie:
*   Na maszynie bramy należy przyłączyć do domeny Windows.
*   Musisz mieć uprawnienie można zaktualizować ustawień kontrolera domeny.

#### <a name="how-to-configure"></a>Jak skonfigurować:

> [!NOTE]
> Zamień REALM.COM i AD.COM w tym samouczku poniższy własnego obszaru odpowiednich a kontrolerem domeny, zgodnie z potrzebami.

**Na serwerze Centrum dystrybucji KLUCZY:**

1. Edytuj konfigurację Centrum dystrybucji KLUCZY w **krb5.conf** pliku, aby umożliwić Centrum dystrybucji KLUCZY zaufania domeny Windows odwołujące się do następującego szablonu konfiguracji. Domyślnie konfiguracji znajduje się w **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log

           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true

           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }

           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM

           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Uruchom ponownie** Usługa Centrum dystrybucji KLUCZY, po przeprowadzeniu konfiguracji.

2. Przygotowanie podmiotem zabezpieczeń o nazwie **krbtgt/REALM.COM\@AD.COM** na serwerze Centrum dystrybucji KLUCZY za pomocą następującego polecenia:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. W **hadoop.security.auth_to_local** konfigurację usługi systemu plików HDFS Dodaj `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenie **Ksetup** polecenia, aby dodać wpis obszaru:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Ustanowienia zaufania z domeny Windows do obszaru protokołu Kerberos. [hasło] jest hasłem do podmiotu zabezpieczeń **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Wybierz algorytm szyfrowania używany w protokole Kerberos.

    1. Przejdź do Menedżera serwera > Zarządzanie zasadami grupy > domeny > Obiekty zasad grupy > domyślny lub aktywnej domeny zasad i edycji.

    2. W **Edytor zarządzania zasadami grupy** okna podręcznego, przejdź do pozycji Konfiguracja komputera > zasady > Ustawienia Windows > Ustawienia zabezpieczeń > Zasady lokalne > Opcje zabezpieczeń i konfigurowanie **sieci zabezpieczenia: Konfigurowanie typów szyfrowania dozwolonych dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, którego chcesz użyć, gdy nawiązać Centrum dystrybucji KLUCZY. Często można po prostu wybierz odpowiednie opcje.

        ![Config typów szyfrowania dla protokołu Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Użyj **Ksetup** polecenie, aby określić algorytm szyfrowania dla określonego obszaru.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Utwórz mapowanie między konta domeny i nazwy głównej protokołu Kerberos, aby można było używać nazwy głównej protokołu Kerberos w domenie Windows.

    1. Uruchamianie narzędzi administracyjnych > **użytkownicy usługi Active Directory i komputery**.

    2. Konfiguruj funkcje zaawansowane, klikając **widoku** > **zaawansowane funkcje**.

    3. Znajdź konto, do której chcesz utworzyć mapowania i kliknij prawym przyciskiem myszy, aby wyświetlić **mapowań nazw** > kliknij **nazwy protokołu Kerberos** kartę.

    4. Dodaj nazwę główną z obszaru.

        ![Mapowanie tożsamości zabezpieczeń](media/data-factory-hdfs-connector/map-security-identity.png)

**Na maszynie bramy:**

* Uruchom następujące polecenie **Ksetup** polecenia, aby dodać wpis obszaru.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**W usłudze Azure Data Factory:**

* Konfigurowanie przy użyciu łącznika systemu plików HDFS **uwierzytelniania Windows** wraz z Twojego konta domeny lub nazwy głównej protokołu Kerberos do połączenia ze źródłem danych systemu plików HDFS. Sprawdź [właściwości usługi połączonej systemu plików HDFS](#linked-service-properties) sekcji Szczegóły konfiguracji.

> [!NOTE]
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
