---
title: Przenoszenie danych z serwera SFTP przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z lokalnego lub serwera SFTP chmury przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9efb385e96e77b3cfc69556406bbc9777338b5d7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623472"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Przenoszenie danych z serwera SFTP przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-sftp-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-sftp.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [SFTPconnector w wersji 2](../connector-sftp.md).

Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure do przenoszenia danych z serwera SFTP na lokalnej/w chmurze w magazynie danych obsługiwanych ujścia. W tym artykule opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przepływu danych działanie kopiowania i listę magazynów danych są obsługiwane jako źródła/sink.

Fabryka danych aktualnie obsługuje tylko przenoszenie danych SFTP serwerze do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych na serwerze SFTP. Obsługuje ona zarówno lokalnie i w chmurze. serwery SFTP.

> [!NOTE]
> Aktywność kopiowania nie powoduje usunięcia pliku źródłowego, po pomyślnym jest kopiowany do lokalizacji docelowej. Jeśli musisz usunąć pliku źródłowego po pomyślnym kopii tworzenia działań niestandardowych do usuwania pliku i użyć działania w potoku. 

## <a name="supported-scenarios-and-authentication-types"></a>Obsługiwane scenariusze i typy uwierzytelniania
Ten łącznik SFTP umożliwia skopiowanie danych z **zarówno w chmurze SFTP serwerów i serwerów lokalnych SFTP**. **Podstawowe** i **parametry SshPublicKey** podczas łączenia się z serwerem SFTP są obsługiwane typy uwierzytelniania.

Podczas kopiowania danych z lokalnego serwera SFTP, należy zainstalować bramę zarządzania danymi w lokalnym środowisku/Azure maszyny Wirtualnej. Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) szczegółowe informacje dotyczące bramy. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać szczegółowe instrukcje dotyczące konfigurowania bramy i przy jego użyciu.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane ze źródła SFTP przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

- Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. Dla przykładów JSON można skopiować danych z użyciem protokołu SFTP serwera do magazynu obiektów Blob Azure, zobacz [przykład JSON: kopiowanie danych z serwera SFTP obiektów blob platformy Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) sekcji tego artykułu.

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis elementów JSON specyficzne dla połączonej usługi FTP.

| Właściwość | Opis | Wymagane |
| --- | --- | --- | --- |
| type | Właściwość type musi mieć ustawioną `Sftp`. |Yes |
| host | Nazwa lub adres IP serwera SFTP. |Yes |
| port |Port, na którym nasłuchuje serwer SFTP. Wartość domyślna to: 21 |Nie |
| Typ authenticationType |Określ typ uwierzytelniania. Dozwolone wartości: **podstawowe**, **parametry SshPublicKey**. <br><br> Zapoznaj się [uwierzytelnianie podstawowe Using](#using-basic-authentication) i [przy użyciu publicznego klucza uwierzytelniania SSH](#using-ssh-public-key-authentication) odpowiednio sekcje więcej właściwości i przykłady JSON. |Yes |
| skipHostKeyValidation | Określ, czy pominąć sprawdzanie poprawności klucza hosta. | Nie. Wartość domyślna: false |
| hostKeyFingerprint | Podaj odcisk palca klucza hosta. | Tak, jeśli `skipHostKeyValidation` ma wartość false.  |
| gatewayName |Nazwa bramy zarządzania danymi do nawiązywania połączenia z serwerem lokalnym SFTP. | Tak, jeśli kopiowanie danych z lokalnego serwera SFTP. |
| encryptedCredential | Zaszyfrowane poświadczenia dostępu do serwera SFTP. Wygenerowany automatycznie po określeniu w kreatorze kopiowania lub w oknie podręcznym ClickOnce uwierzytelnianie podstawowe (nazwy użytkownika i hasła) lub uwierzytelniania parametry SshPublicKey (nazwy użytkownika i ścieżki do klucza prywatnego lub zawartości). | Nie. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera SFTP. |

### <a name="using-basic-authentication"></a>Przy użyciu uwierzytelniania podstawowego

Aby uwierzytelnianie podstawowe, należy ustawić `authenticationType` jako `Basic`i określ następujące właściwości poza łącznika SFTP ogólnego z nich wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- | --- |
| nazwa użytkownika | Użytkownik, który ma dostęp do serwera SFTP. |Yes |
| hasło | Hasło dla użytkownika (username). | Yes |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Przykład: Uwierzytelnianie podstawowe z zaszyfrowanych poświadczeniach

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

Aby używać uwierzytelniania klucza publicznego SSH, ustaw `authenticationType` jako `SshPublicKey`i określ następujące właściwości poza łącznika SFTP ogólnego z nich wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- | --- |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera SFTP |Yes |
| privateKeyPath | Określ ścieżka bezwzględna do pliku klucza prywatnego może dostęp do tej bramy. | Określ `privateKeyPath` lub `privateKeyContent`. <br><br> Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera SFTP. |
| privateKeyContent | Ciąg serializacji zawartość klucza prywatnego. Kreator kopiowania można odczytać pliku klucza prywatnego i Wyodrębnij zawartość klucza prywatnego automatycznie. Jeśli używane są wszystkie inne narzędzie/pakiet SDK, należy użyć właściwości privateKeyPath. | Określ `privateKeyPath` lub `privateKeyContent`. |
| Hasło | Określ przebiegu frazy/hasło do odszyfrowania klucza prywatnego, jeśli plik klucza jest chroniony przez hasło. | Tak, jeśli hasło jest chroniony plik klucza prywatnego. |

> [!NOTE]
> Łącznik SFTP obsługuje klucz OpenSSH RSA/DSA. Upewnij się, że zawartość pliku klucza, który rozpoczyna się od "---BEGIN [RSA/DSA] klucza prywatnego---". Jeśli plik klucza prywatnego jest plik formatu ppk, użyj narzędzia Putty do przekonwertowania z ppk OpenSSH format.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Przykład: Parametry SshPublicKey uwierzytelnianie przy użyciu filePath klucza prywatnego

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Przykład: Parametry SshPublicKey uwierzytelniania za pomocą prywatnego klucza zawartości

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
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów w zestawie danych.

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych. Zawiera informacje specyficzne dla typu zestawu danych. TypeProperties sekcja dla zestawu danych typu **FileShare** zestawu danych ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Sub ścieżkę do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [próbki połączone definicje usługi i zestawu danych](#sample-linked-service-and-dataset-definitions) przykłady.<br/><br/>Możesz łączyć tej właściwości z **partitionBy** do folderu ścieżki oparte na wycinku rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** aby tabela do odwoływania się do określonego pliku w folderze. Jeśli nie określono żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku będzie poniżej tego formatu: <br/><br/>Dane. <Guid>.txt (przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| obiektu fileFilter |Określ filtr służący do wybierania podzbioru pliki w ścieżce folderu, a nie wszystkie pliki.<br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych z udziału plików. Ta właściwość nie jest obsługiwana z systemu plików HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwę pliku dla czasu serii danych. Na przykład folderPath sparametryzowana dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy używany tryb transferu binarnego. Wartość true dla trybie binarnym i wartość false ASCII. Wartość domyślna: wartość True. Ta właściwość jest używana tylko w przypadku typu skojarzonej połączonej usługi typu: SerwerFTP. |Nie |

> [!NOTE]
> Nie można jednocześnie używać nazwy pliku i obiektu fileFilter.

### <a name="using-partionedby-property"></a>Za pomocą właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić dynamiczne folderPath, nazwę pliku dla danych szeregów czasowych ze partitionedBy. Możesz to zrobić z makra fabryki danych i zmiennej systemowej SliceStart, SliceEnd sygnalizujące logicznej okresu wycinek podanych danych.

Informacje na temat zestawów danych serii czasu, planowanie i wycinków, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md), [planowanie i wykonanie](data-factory-scheduling-and-execution.md), i [tworzenie potoków](data-factory-create-pipelines.md) artykułów.

#### <a name="sample-1"></a>Przykład 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {wycinka} zostanie zastąpiony wartością zmiennej systemowej SliceStart fabryki danych w formacie (YYYYMMDDHH) określona. SliceStart odnosi się uruchomienie wycinka. Element folderPath jest różne dla każdego wycinka. Przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

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
W tym przykładzie rok, miesiąc, dzień i czas SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i nazwę pliku.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji typeProperties działania różnić się z każdym typem działania. Dla działania kopiowania właściwości typu różnią się w zależności od typów źródeł i sink.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md) artykuł na temat szczegółów.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z serwera SFTP obiektów blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych z użyciem protokołu SFTP źródła do magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje krok po kroku.

Przykład zawiera następujące obiekty fabryki danych:

* Połączonej usługi typu [sftp](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z serwera SFTP obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Usługa SFTP połączone**

W tym przykładzie używane uwierzytelnianie podstawowe z nazwy użytkownika i hasła w postaci zwykłego tekstu. Można także użyć jednej z następujących sposobów:

* Uwierzytelnianie podstawowe z zaszyfrowane poświadczenia
* Uwierzytelnianie klucza publicznego SSH

Zobacz [FTP połączona usługa](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

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
**Zestaw danych wejściowych SFTP**

Ten zestaw danych odwołuje się do folderu SFTP `mysharedfolder` i plik `test.csv`. Potok kopiuje plik do lokalizacji docelowej.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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

**W potoku z działanie kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **FileSystemSource** i **zbiornika** ustawiono typ **BlobSink**.

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

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* [Samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku do tworzenia potoku z działania kopiowania.
