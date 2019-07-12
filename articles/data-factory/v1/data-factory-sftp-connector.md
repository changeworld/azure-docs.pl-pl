---
title: Przenoszenie danych z serwera SFTP przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z lokalnego lub z serwera SFTP chmury przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d35c4f410c29bba7848dde53d206cdd2ccd980ca
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836152"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Przenoszenie danych z serwera SFTP przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-sftp-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sftp.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [SFTPconnector w wersji 2](../connector-sftp.md).

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z serwera SFTP lokalna/w chmurze do obsługiwanego magazynu danych ujścia. W tym artykule opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania i listę magazynów danych obsługiwanych jako źródła/ujścia.

Data factory obsługuje obecnie tylko przenosi dane z serwera SFTP do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do serwera SFTP. Obsługuje ona zarówno lokalnie i w chmurze serwerów SFTP.

> [!NOTE]
> Działanie kopiowania nie powoduje usunięcia pliku źródłowego, po pomyślnym są kopiowane do lokalizacji docelowej. Jeśli zachodzi potrzeba usunięcia pliku źródłowego po kopiowania zakończonego powodzeniem, należy utworzyć niestandardowe działanie, aby usunąć plik i użyć działania w potoku.

## <a name="supported-scenarios-and-authentication-types"></a>Obsługiwane scenariusze i typy uwierzytelniania
Można użyć tego łącznika SFTP do skopiowania danych z **zarówno w chmurze serwery SFTP i SFTP w środowisku lokalnym**. **Podstawowe** i **SshPublicKey** typy uwierzytelniania są obsługiwane w przypadku łączenia się z serwerem SFTP.

Podczas kopiowania danych z serwera SFTP w środowisku lokalnym, należy zainstalować bramę zarządzania danymi w lokalnych środowiska/Azure maszyny Wirtualnej. Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) szczegółowe informacje na temat bramy. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać szczegółowe instrukcje dotyczące instalowania bramy i korzystania z niego.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane ze źródła SFTP przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

- Aby utworzyć potok umożliwia także następujących narzędzi: **Program Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**, i **interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. Aby uzyskać przykłady kodu JSON skopiować dane z serwera SFTP do usługi Azure Blob Storage, zobacz [przykład kodu JSON: Kopiowanie danych z serwera SFTP do usługi Azure blob](#json-example-copy-data-from-sftp-server-to-azure-blob) dalszej części tego artykułu.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla połączonej usługi FTP.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type | Właściwość type musi być równa `Sftp`. |Tak |
| host | Nazwa lub adres IP serwera SFTP. |Tak |
| port |Port, na którym nasłuchuje serwer SFTP. Wartość domyślna to: 21 |Nie |
| authenticationType |Określ typ uwierzytelniania. Dozwolone wartości: **Podstawowe**, **SshPublicKey**. <br><br> Zapoznaj się [uwierzytelnianie podstawowe użycie](#using-basic-authentication) i [przy użyciu protokołu SSH uwierzytelnianie klucza publicznego](#using-ssh-public-key-authentication) odpowiednio sekcje więcej właściwości i przykłady kodu JSON. |Yes |
| skipHostKeyValidation | Określ, czy pominąć sprawdzanie poprawności klucza hosta. | Nie. Wartość domyślna: false |
| hostKeyFingerprint | Określ odcisk palca klucza hosta. | Tak, jeśli `skipHostKeyValidation` jest ustawiona na wartość false.  |
| gatewayName |Nazwa bramy zarządzania danymi, aby nawiązać połączenie z lokalnym serwerem SFTP. | Tak, jeśli kopiowanie danych z lokalnego serwera SFTP. |
| encryptedCredential | Zaszyfrowane poświadczenia dostępu do serwera SFTP. Wygenerowany automatycznie po określeniu w kreatorze kopiowania lub okno podręczne ClickOnce uwierzytelniania podstawowego (nazwa użytkownika i hasło) lub uwierzytelniania klucz publiczny SSH (nazwa użytkownika i ścieżki klucza prywatnego lub zawartości). | Nie. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera SFTP. |

### <a name="using-basic-authentication"></a>Przy użyciu uwierzytelniania podstawowego

Aby użyć uwierzytelniania podstawowego, ustaw `authenticationType` jako `Basic`, a następnie określ następujące właściwości oprócz tych ogólnych wprowadzone w ostatniej sekcji łącznika SFTP:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| username | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| password | Hasło dla użytkownika (nazwa użytkownika). | Yes |

#### <a name="example-basic-authentication"></a>Przykład: Uwierzytelnianie podstawowe
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Przykład: Podstawowe uwierzytelnianie za pomocą zaszyfrowanych poświadczeń

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Przy użyciu uwierzytelniania klucza publicznego SSH

Aby użyć uwierzytelniania klucza publicznego SSH, ustaw `authenticationType` jako `SshPublicKey`, a następnie określ następujące właściwości oprócz tych ogólnych wprowadzone w ostatniej sekcji łącznika SFTP:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| username |Użytkownik, który ma dostęp do serwera SFTP |Yes |
| privateKeyPath | Określ ścieżkę bezwzględną do pliku klucza prywatnego może dostęp do tej bramy. | Wybierz opcję `privateKeyPath` lub `privateKeyContent`. <br><br> Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera SFTP. |
| privateKeyContent | Ciąg serializacji treści klucza prywatnego. Kreator kopiowania można odczytać pliku klucza prywatnego i automatycznie wyodrębnić zawartość klucza prywatnego. Jeśli używane są wszystkie inne narzędzia/zestawu SDK, należy użyć właściwości privateKeyPath. | Wybierz opcję `privateKeyPath` lub `privateKeyContent`. |
| passPhrase | Określ — dostęp próbny frazy/hasło do odszyfrowania klucza prywatnego, jeśli plik klucza, który jest chroniony przez frazę. | Tak, czy plik klucza prywatnego jest chroniony przez frazę. |

> [!NOTE]
> Łącznik SFTP obsługuje klucz RSA/DSA OpenSSH. Upewnij się, że zawartość pliku klucza, który rozpoczyna się od "---BEGIN [RSA/DSA] PRIVATE KEY---". Jeśli plik klucza prywatnego jest plikiem ppk format, użyj narzędzia Putty konwersji ppk formacie OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Przykład: Uwierzytelnianie klucz publiczny SSH przy użyciu filePath klucza prywatnego

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Przykład: Klucz publiczny SSH uwierzytelniania za pomocą prywatnego klucza zawartości

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów w zestawie danych.

**TypeProperties** sekcji różni się dla każdego typu zestawu danych. Zawiera on informacje, które są specyficzne dla typu zestawu danych. Zestaw danych o typie sekcji typeProperties **udział plików** zestawu danych ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka podrzędna w folderze. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz definicje usługi i zestaw danych próbka połączone przykłady.<br/><br/>Można połączyć tę właściwość z **partitionBy** mieć folderu ścieżki, w oparciu o wycinek rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** chcącym tabeli do odwoływania się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku byłoby w następującym tego formatu: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| fileFilter |Określ filtr służący do wybierają podzbiór plików w ścieżce folderu, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykłady 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych w udziale plików. Ta właściwość nie jest obsługiwana przy użyciu systemu plików HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwa_pliku danych szeregów czasowych. Na przykład folderPath sparametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ czy używać trybu binarnego transferu. Wartość true na false kodów ASCII i binarny trybu. Wartość domyślna: True. Ta właściwość jest używana tylko w przypadku, gdy skojarzonej połączonej usługi, typ jest typem: FtpServer. |Nie |

> [!NOTE]
> Nie można jednocześnie używać nazwy pliku i obiektu fileFilter.

### <a name="using-partionedby-property"></a>Przy użyciu właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić dynamiczny folderPath, nazwa_pliku danych szeregów czasowych z partitionedBy. Aby to zrobić za pomocą makra usługi Data Factory i zmienna systemowa SliceStart, SliceEnd wskazujące logiczne okres dla wycinka danych.

Aby dowiedzieć się o zestawy danych serii czasu, planowanie i wycinków, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md), [planowanie i wykonywanie](data-factory-scheduling-and-execution.md), i [tworzenie potoków](data-factory-create-pipelines.md) artykułów.

#### <a name="sample-1"></a>Przykład 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {wycinek} jest zastępowana wartością zmiennej systemowej SliceStart fabryki danych w formacie (YYYYMMDDHH) określona. Parametru SliceStart odnosi się do rozpoczęcie wycinka. FolderPath różni się dla każdego wycinka. Przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Przykład 2:

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
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości w sekcji typeProperties działania zależą od każdego typu działania. Właściwości typu różnią się w zależności od tego, jakiego typu źródła i ujścia dla działania kopiowania.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykuł na temat szczegółów.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z serwera SFTP do obiektów blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują sposób kopiowania danych ze źródła SFTP do usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Nie obejmuje instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku.

Przykład obejmuje następujących jednostek fabryki danych:

* Połączonej usługi typu [sftp](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z serwera SFTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Usługa SFTP połączone**

W tym przykładzie użyto uwierzytelniania podstawowego z nazwą użytkownika i hasła w postaci zwykłego tekstu. Można również użyć jednej z następujących sposobów:

* Uwierzytelnianie podstawowe z zaszyfrowanymi poświadczeniami
* Uwierzytelnianie klucza publicznego SSH

Zobacz [FTP połączoną usługę](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Połączona usługa Azure Storage**

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
**Wejściowy zestaw danych SFTP**

Ten zestaw danych odwołuje się do folderu SFTP `mysharedfolder` i plik `test.csv`. Potok kopiuje plik do miejsca docelowego.

Ustawienie "external": "true" informuje usługa Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Potok z działaniem kopiowania**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **FileSystemSource** i **ujścia** ustawiono typ **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* [Samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania.
