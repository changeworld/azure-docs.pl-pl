---
title: Przenoszenie danych z serwera SFTP przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenosić dane z lokalnego lub chmurowego serwera SFTP przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3f78934fb11dd4f9e34bf27d565d471d47f250b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265807"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Przenoszenie danych z serwera SFTP przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-sftp-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sftp.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [SFTPconnector w wersji 2](../connector-sftp.md).

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnego/chmurowego serwera SFTP do obsługiwanego magazynu danych ujścia. W tym artykule opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z aktywnością kopiowania i listą magazynów danych obsługiwanych jako źródła/pochłaniacze.

Fabryka danych obsługuje obecnie tylko przenoszenie danych z serwera SFTP do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych na serwer SFTP. Obsługuje zarówno lokalne, jak i chmurowe serwery SFTP.

> [!NOTE]
> Działanie kopiowania nie powoduje usunięcia pliku źródłowego po pomyślnym skopiowaniu go do miejsca docelowego. Jeśli chcesz usunąć plik źródłowy po pomyślnej kopii, utwórz działanie niestandardowe, aby usunąć plik i użyć działania w potoku.

## <a name="supported-scenarios-and-authentication-types"></a>Obsługiwane scenariusze i typy uwierzytelniania
Za pomocą tego łącznika SFTP można kopiować dane zarówno z **serwerów SFTP w chmurze, jak i z lokalnych serwerów SFTP**. **Typy** uwierzytelniania Basic i **SshPublicKey** są obsługiwane podczas łączenia się z serwerem SFTP.

Podczas kopiowania danych z lokalnego serwera SFTP należy zainstalować bramę zarządzania danymi w środowisku lokalnym/maszynie wirtualnej platformy Azure. Szczegółowe informacje na temat bramy można znaleźć w [bramie zarządzania danymi.](data-factory-data-management-gateway.md) Zobacz [przenoszenie danych między lokalizacjami lokalnymi a artykułami w chmurze,](data-factory-move-data-between-onprem-and-cloud.md) aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy i korzystania z niej.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane ze źródła SFTP przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

- Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania. Dla przykładów JSON, aby skopiować dane z serwera SFTP do usługi Azure Blob Storage, zobacz [JSON Przykład: Kopiowanie danych z serwera SFTP do usługi Azure obiekt blob](#json-example-copy-data-from-sftp-server-to-azure-blob) sekcji tego artykułu.

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej z ftp.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type | Właściwość typu musi `Sftp`być ustawiona na . |Tak |
| host | Nazwa lub adres IP serwera SFTP. |Tak |
| port |Port, na którym nasłuchuje serwer SFTP. Wartość domyślna to: 21 |Nie |
| authenticationType |Określ typ uwierzytelniania. Dozwolone wartości: **Basic**, **SshPublicKey**. <br><br> Zapoznaj się [z sekcjami Korzystanie z uwierzytelniania podstawowego](#using-basic-authentication) i [Korzystanie z klucza publicznego SSH](#using-ssh-public-key-authentication) na większej liczbie właściwości i przykładów JSON. |Tak |
| skipHostKeyValidation (SkipHostKeyValidation) | Określ, czy należy pominąć sprawdzanie poprawności klucza hosta. | Nie. Wartość domyślna: false |
| hostKeyFingerprint | Określ odcisk palca klucza hosta. | Tak, `skipHostKeyValidation` jeśli jest ustawiona na false.  |
| nazwa bramy |Nazwa bramy zarządzania danymi w celu nawiązania połączenia z lokalnym serwerem SFTP. | Tak, jeśli kopiowanie danych z lokalnego serwera SFTP. |
| encryptedCredential | Zaszyfrowane poświadczenia dostępu do serwera SFTP. Generowane automatycznie po określeniu uwierzytelniania podstawowego (nazwa użytkownika + hasło) lub uwierzytelniania SshPublicKey (nazwa _nazwa + ścieżka klucza prywatnego lub zawartość) w kreatorze kopiowania lub w oknie dialogowym ClickOnce. | Nie. Stosowanie tylko podczas kopiowania danych z lokalnego serwera SFTP. |

### <a name="using-basic-authentication"></a>Korzystanie z uwierzytelniania podstawowego

Aby użyć uwierzytelniania `authenticationType` `Basic`podstawowego, ustaw jako i określ następujące właściwości oprócz ogólnych właściwości łącznika SFTP wprowadzonych w ostatniej sekcji:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| nazwa użytkownika | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| hasło | Hasło użytkownika (nazwa użytkownika). | Tak |

#### <a name="example-basic-authentication"></a>Przykład: uwierzytelnianie podstawowe
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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Przykład: uwierzytelnianie podstawowe przy użyciu zaszyfrowanych poświadczeń

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

### <a name="using-ssh-public-key-authentication"></a>Korzystanie z uwierzytelniania klucza publicznego SSH

Aby użyć uwierzytelniania klucza `authenticationType` `SshPublicKey`publicznego SSH, ustaw jako i określ następujące właściwości oprócz ogólnych właściwości łącznika SFTP wprowadzonych w ostatniej sekcji:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera SFTP |Tak |
| ścieżka klucza prywatnego | Określ ścieżkę bezwzględną do pliku klucza prywatnego, do który brama może uzyskać dostęp. | Określ `privateKeyPath` albo `privateKeyContent`. <br><br> Stosowanie tylko podczas kopiowania danych z lokalnego serwera SFTP. |
| prywatny KluczWłasny | Szeregowany ciąg zawartości klucza prywatnego. Kreator kopiowania może automatycznie odczytać plik klucza prywatnego i wyodrębnić zawartość klucza prywatnego. Jeśli używasz innego narzędzia/zestawu SDK, należy użyć właściwości privateKeyPath. | Określ `privateKeyPath` albo `privateKeyContent`. |
| Hasło | Określ frazę pass/hasło, aby odszyfrować klucz prywatny, jeśli plik klucza jest chroniony przez frazę przebiegu. | Tak, jeśli plik klucza prywatnego jest chroniony przez frazę przebiegu. |

> [!NOTE]
> Złącze SFTP obsługuje klucz RSA/DSA OpenSSH. Upewnij się, że zawartość pliku klucza zaczyna się od "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Jeśli plik klucza prywatnego jest plikiem w formacie PPK, użyj narzędzia Putty do konwersji z .ppk na format OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Przykład: Uwierzytelnianie SshPublicKey przy użyciu pliku klucza prywatnegoPath

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Przykład: uwierzytelnianie SshPublicKey przy użyciu zawartości klucza prywatnego

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
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych.

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych. Zawiera informacje, które są specyficzne dla typu zestawu danych. Sekcja typeProperties dla zestawu danych typu **FileShare** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Podwładna do folderu. Użyj znaku ucieczki \ ' dla znaków specjalnych w ciągu. Zobacz przykłady połączonych definicji usługi i zestawów danych, aby zapoznać się z przykładami.<br/><br/>Tę właściwość można połączyć z **partitionBy,** aby ścieżki folderów były oparte na data-godzinach rozpoczęcia/zakończenia plasterka. |Tak |
| fileName |Określ nazwę pliku w **folderPath,** jeśli chcesz, aby tabela odwoływała się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt`(Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| Filefilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wiele `?` znaków) i (pojedynczy znak).<br/><br/>Przykłady 1:`"fileFilter": "*.log"`<br/>Przykład 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter ma zastosowanie do wejściowego zestawu danych FileShare. Ta właściwość nie jest obsługiwana przez system PLIKÓW HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia folderu dynamicznegoPath, nazwa pliku dla danych szeregów czasowych. Na przykład folderPath sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy używany jest tryb transferu binarnego. True dla trybu binarnego i fałszywe ASCII. Wartość domyślna: True. Tej właściwości można używać tylko wtedy, gdy skojarzony typ połączonej usługi jest typu: FtpServer. |Nie |

> [!NOTE]
> nazwa pliku i fileFilter nie mogą być używane jednocześnie.

### <a name="using-partionedby-property"></a>Korzystanie z właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić folder dynamicznyPath, nazwa pliku dla danych szeregów czasowych z partitionedBy. Można to zrobić za pomocą makr Fabryka danych i zmiennej systemowej SliceStart, SliceEnd, które wskazują logiczny okres dla danego plasterka danych.

Aby dowiedzieć się więcej o zestawach danych szeregów czasowych, planowaniu i plasterkach, zobacz [Tworzenie zestawów danych,](data-factory-create-datasets.md) [Planowanie & wykonywanie](data-factory-scheduling-and-execution.md)i Tworzenie [artykułów Potoki.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Próbka 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {Slice} jest zastępowany wartością zmiennej systemowej Data Factory SliceStart w określonym formacie (RRRRMMDDHH). SliceStart odnosi się do czasu rozpoczęcia plasterka. Ścieżka folderu jest inna dla każdego plasterka. Przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Próbka 2:

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
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Mając na uwadze, właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania Kopiowania właściwości typu różnią się w zależności od typów źródeł i pochłaniacze.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykuł o szczegółach.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Przykład JSON: kopiowanie danych z serwera SFTP do obiektu blob platformy Azure
Poniższy przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane ze źródła SFTP do usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Nie zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Aby uzyskać instrukcje krok po kroku, zobacz [przenoszenie danych między lokalizacjami lokalnymi a artykułami w chmurze.](data-factory-move-data-between-onprem-and-cloud.md)

Próbka ma następujące jednostki fabryki danych:

* Połączona usługa typu [sftp](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane z serwera SFTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą SFTP**

W tym przykładzie użyto uwierzytelniania podstawowego z nazwą użytkownika i hasłem w postaci zwykłego tekstu. Można również użyć jednego z następujących sposobów:

* Uwierzytelnianie podstawowe przy użyciu zaszyfrowanych poświadczeń
* Uwierzytelnianie za pomocą klucza publicznego SSH

Zobacz [sekcję usługi połączonej FTP,](#linked-service-properties) aby uzyskać różne typy uwierzytelniania, których można użyć.

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
**Usługa połączona usługi Usługi Azure Storage**

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

Ten zestaw danych odnosi się do `mysharedfolder` folderu i pliku `test.csv`SFTP . Potok kopiuje plik do miejsca docelowego.

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

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

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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

**Potok z działaniem Kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **FileSystemSource** i typ **ujścia** jest ustawiony na **BlobSink**.

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

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* [Kopiuj samouczek działania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) dla krok po kroku instrukcje dotyczące tworzenia potoku z copy activity.
