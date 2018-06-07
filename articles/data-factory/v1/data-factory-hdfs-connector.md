---
title: Przenoszenia danych z lokalnego systemu plików HDFS | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z lokalnego systemu plików HDFS przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b8a35e19628ebbe75d3f1890a6d64ab777519698
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621551"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Przenoszenia danych z lokalnego systemu plików HDFS przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-hdfs-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-hdfs.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika systemu plików HDFS w wersji 2](../connector-hdfs.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z lokalnego systemu plików HDFS. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Można skopiować danych z systemu plików HDFS żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryka danych aktualnie obsługuje tylko dane przenoszenie, z lokalnego systemu plików HDFS do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do lokalnego systemu plików HDFS.

> [!NOTE]
> Aktywność kopiowania nie powoduje usunięcia pliku źródłowego, po pomyślnym jest kopiowany do lokalizacji docelowej. Jeśli musisz usunąć pliku źródłowego po pomyślnym kopii tworzenia działań niestandardowych do usuwania pliku i użyć działania w potoku. 

## <a name="enabling-connectivity"></a>Włączenie łączności
Usługi fabryka danych obsługuje nawiązywania połączenia z lokalnym systemem plików HDFS przy użyciu bramy zarządzania danymi. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy. Użyj bramy do nawiązania połączenia systemu plików HDFS nawet wtedy, gdy jest ona hostowana w maszynie Wirtualnej platformy Azure IaaS.

> [!NOTE]
> Upewnij się, że dostęp brama zarządzania danymi do **wszystkie** [nazwa węzła serwera]: [nazwa węzła portu] i [serwery danych węzła]: [port węzeł danych] klastra usługi Hadoop. Domyślne [nazwa węzła port] jest 50070, a domyślna [port węzeł danych] jest 50075.

Gdy bramy można zainstalować na tym samym komputerze lokalnym lub maszyny Wirtualnej platformy Azure jako system plików HDFS, zaleca się zainstalować bramę na oddzielnych maszyny/Azure IaaS maszyny Wirtualnej. Wystąpienia bramy na osobnym komputerze ogranicza rywalizację zasobów, poprawia wydajność. Po zainstalowaniu bramy na osobnym komputerze maszynie powinno być możliwe dostęp do komputera z systemem plików HDFS.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane ze źródła systemu plików HDFS przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania.
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z magazynu danych systemu plików HDFS, zobacz [przykład JSON: kopiowanie danych z lokalnego systemu plików HDFS do obiektów Blob platformy Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do systemu plików HDFS:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Połączona usługa łączy magazynu danych z fabryki danych. Tworzenie połączonej usługi typu **Hdfs** do połączenia z lokalnym systemem plików HDFS z fabryką danych. Poniższa tabela zawiera opis specyficzne dla systemu plików HDFS elementy JSON połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **Hdfs** |Yes |
| Url |Adres URL do systemu plików HDFS |Yes |
| Typ authenticationType |Anonimowe lub Windows. <br><br> Umożliwia **uwierzytelnianie Kerberos** łącznika systemu plików HDFS, można znaleźć w temacie [w tej sekcji](#use-kerberos-authentication-for-hdfs-connector) do odpowiednio skonfigurowane w lokalnym środowisku. |Yes |
| userName |Uwierzytelnianie nazwy użytkownika dla systemu Windows. Aby uwierzytelnianie Kerberos, określ `<username>@<domain>.com`. |Tak (w przypadku uwierzytelniania systemu Windows) |
| hasło |Hasło dla uwierzytelniania systemu Windows. |Tak (w przypadku uwierzytelniania systemu Windows) |
| gatewayName |Nazwa bramy, która powinna być używana do nawiązania połączenia systemu plików HDFS usługi fabryka danych. |Yes |
| encryptedCredential |[Nowy AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) poświadczeń dostępu do danych wyjściowych. |Nie |

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

### <a name="using-windows-authentication"></a>Przy użyciu uwierzytelniania systemu Windows

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
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **FileShare** (w tym system plików HDFS w zestawie danych) ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do folderu. Przykład: `myfolder`<br/><br/>Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Na przykład: folder\subfolder, określ folder\\\\podfolderów i dla d:\samplefolder, określ d:\\\\folder_przykładowy.<br/><br/>Możesz łączyć tej właściwości z **partitionBy** do folderu ścieżki oparte na wycinku rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** aby tabela do odwoływania się do określonego pliku w folderze. Jeśli nie określono żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku będzie poniżej tego formatu: <br/><br/>Dane. <Guid>.txt (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwę pliku dla czasu serii danych. Przykład: folderPath sparametryzowana dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można jednocześnie używać nazwy pliku i obiektu fileFilter.

### <a name="using-partionedby-property"></a>Za pomocą właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić folderPath dynamiczne i nazwę pliku dla czasu danych serii za pomocą **partitionedBy** właściwość [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md).

Aby dowiedzieć się więcej na temat zestawów danych serii czasu, planowanie i wycinków, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md), [planowanie i wykonanie](data-factory-scheduling-and-execution.md), i [tworzenie potoków](data-factory-create-pipelines.md) artykułów.

#### <a name="sample-1"></a>Przykład 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {wycinka} zostanie zastąpiony wartością zmiennej systemowej SliceStart fabryki danych w formacie (YYYYMMDDHH) określona. SliceStart odnosi się uruchomienie wycinka. Element folderPath jest różne dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

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
W tym przykładzie rok, miesiąc, dzień i czas SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i nazwę pliku.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania różnić się z każdym typem działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Dla działania kopiowania, gdy źródłem jest typu **FileSystemSource** w sekcji typeProperties dostępne są następujące właściwości:

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. |Wartość true, False (ustawienie domyślne) |Nie |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md) artykuł na temat szczegółów.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Przykład JSON: kopiowanie danych z lokalnego systemu plików HDFS do obiektów Blob platformy Azure
W tym przykładzie pokazano, jak można skopiować danych z lokalnego systemu plików HDFS do magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.  

Przykład zawiera definicje JSON dla następujących jednostek fabryki danych. Te definicje umożliwia tworzenie potoku można skopiować danych z systemu plików HDFS do magazynu obiektów Blob Azure przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Połączonej usługi typu [OnPremisesHdfs](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z lokalnego systemu plików HDFS obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

Pierwszym krokiem należy skonfigurować bramę zarządzania danymi. Instrukcje w [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**System plików HDFS połączonej usługi:** w tym przykładzie używane uwierzytelnianie systemu Windows. Zobacz [HDFS połączona usługa](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

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

**Połączonej usługi magazynu Azure:**

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

**System plików HDFS wejściowy zestaw danych:** ten zestaw danych odwołuje się do folderu systemu plików HDFS DataTransfer/UnitTest /. Potok kopiuje wszystkie pliki w tym folderze, do miejsca docelowego.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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

**Działanie kopiowania w potoku z systemu plików źródłowy i odbiorczy obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania tych zestawów danych wejściowych i wyjściowych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **FileSystemSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **zapytania** właściwości wybiera dane w ostatniej godziny do skopiowania.

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
Dostępne są dwie opcje do skonfigurowania środowiska lokalnego tak, aby korzystał z uwierzytelniania Kerberos w systemie plików HDFS łącznika. Możesz wybrać ten, który najlepiej pasuje do sprawę.
* Opcja 1: [maszyna bramy sprzężenia obszaru Kerberos](#kerberos-join-realm)
* Opcja 2: [włączyć wzajemnego zaufania między domeną systemu Windows i protokół Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opcja 1: Dołącz maszynę bramy obszaru Kerberos

#### <a name="requirement"></a>Wymaganie:

* Maszyna bramy należy dołączyć obszaru Kerberos i nie można dołączyć do dowolnej domeny systemu Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować:

**Na komputerze bramy:**

1.  Uruchom **Ksetup** narzędzie, aby skonfigurować serwer Centrum dystrybucji KLUCZY protokołu Kerberos i obszar.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszaru Kerberos różni się od domeny systemu Windows. Można to osiągnąć przez ustawienie obszaru Kerberos i dodanie serwera Centrum dystrybucji KLUCZY w następujący sposób. Zastąp *REALM.COM* z własnych odpowiednich obszaru zgodnie z potrzebami.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Uruchom ponownie** komputer po wykonaniu tych poleceń 2.

2.  Sprawdź konfigurację z **Ksetup** polecenia. Dane wyjściowe powinny być takie jak:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**W fabryce danych Azure:**

* Skonfigurować za pomocą łącznika systemu plików HDFS **uwierzytelniania systemu Windows** wraz z nazwy głównej protokołu Kerberos i hasło, aby połączyć się ze źródłem danych systemu plików HDFS. Sprawdź [właściwości powiązanych z systemu plików HDFS](#linked-service-properties) sekcji Szczegóły konfiguracji.

### <a name="kerberos-mutual-trust"></a>Opcja 2: Włączanie wzajemnego zaufania między domeną systemu Windows i protokół Kerberos

#### <a name="requirement"></a>Wymaganie:
*   Maszyna bramy należy przyłączyć do domeny systemu Windows.
*   Wymagane jest uprawnienie można zaktualizować ustawień kontrolera domeny.

#### <a name="how-to-configure"></a>Jak skonfigurować:

> [!NOTE]
> Zamień REALM.COM i AD.COM w samouczku następujące własnych odpowiednich obszaru i kontroler domeny, zgodnie z potrzebami.

**Na serwerze Centrum dystrybucji KLUCZY:**

1.  Edytuje konfigurację Centrum dystrybucji KLUCZY w **krb5.conf** pliku, aby umożliwić Centrum dystrybucji KLUCZY zaufania domeny systemu Windows, które odnoszą się do następujących szablonu konfiguracji. Domyślnie konfiguracji znajduje się w **/etc/krb5.conf**.

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

  **Uruchom ponownie** Usługa Centrum dystrybucji KLUCZY po konfiguracji.

2.  Przygotowanie podmiot zabezpieczeń o nazwie **krbtgt/REALM.COM@AD.COM** w Centrum dystrybucji KLUCZY serwera przy użyciu następującego polecenia:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  W **hadoop.security.auth_to_local** konfigurację usługi systemu plików HDFS plików, dodawanie `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenie **Ksetup** polecenia, aby dodać wpis obszarów:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Ustanowienia zaufania z domeny systemu Windows do obszaru protokołu Kerberos. [hasło] jest hasłem dla podmiotu zabezpieczeń **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Wybierz algorytm szyfrowania używany w protokole Kerberos.

    1. Przejdź do Menedżera serwera > Zarządzanie zasadami grupy > domeny > Obiekty zasad grupy > domyślny lub zasady domeny Active i edycji.

    2. W **Edytor zarządzania zasadami grupy** okno podręczne, przejdź do pozycji Konfiguracja komputera > zasady > Ustawienia systemu Windows > Ustawienia zabezpieczeń > Zasady lokalne > Opcje zabezpieczeń i skonfigurować **sieci zabezpieczenia: Konfigurowanie typów szyfrowania dozwolone dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, którego chcesz użyć, gdy nawiązać Centrum dystrybucji KLUCZY. Zazwyczaj można po prostu wybierz wszystkie opcje.

        ![Typy szyfrowania konfiguracji dla protokołu Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Użyj **Ksetup** polecenie, aby określić algorytm szyfrowania, który ma być używany dla określonego obszaru.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Utwórz mapowania między konto domeny i główna protokołu Kerberos, aby używały nazwy głównej protokołu Kerberos w domenie systemu Windows.

    1. Uruchamianie narzędzi administracyjnych > **użytkownicy usługi Active Directory i komputery**.

    2. Skonfiguruj zaawansowane funkcje, klikając **widoku** > **zaawansowanych funkcji**.

    3. Zlokalizuj konto, do którego chcesz utworzyć mapowania i kliknij prawym przyciskiem myszy, aby wyświetlić **mapowania nazw** > kliknij **nazwy protokołu Kerberos** kartę.

    4. Dodaj podmiot zabezpieczeń z obszaru.

        ![Mapowania tożsamości zabezpieczeń](media/data-factory-hdfs-connector/map-security-identity.png)

**Na komputerze bramy:**

* Uruchom następujące polecenie **Ksetup** polecenia, aby dodać wpis obszaru.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**W fabryce danych Azure:**

* Skonfigurować za pomocą łącznika systemu plików HDFS **uwierzytelniania systemu Windows** wraz z Twojego konta domeny albo podmiot zabezpieczeń protokołu Kerberos do nawiązania połączenia ze źródłem danych systemu plików HDFS. Sprawdź [właściwości powiązanych z systemu plików HDFS](#linked-service-properties) sekcji Szczegóły konfiguracji.

> [!NOTE]
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
