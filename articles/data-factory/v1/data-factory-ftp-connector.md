---
title: Przenoszenie danych z serwera FTP przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z serwera FTP przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7799eeb65a109a6ff517d50bb9603fe83f80ed14
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Przenoszenie danych z serwera FTP przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-ftp-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-ftp.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznik FTP w wersji 2](../connector-ftp.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z serwera FTP. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Wszystkie obsługiwanych ujścia magazynu danych można skopiować danych z serwera FTP. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryka danych aktualnie obsługuje tylko przenoszenia danych z serwera FTP do innych magazynów danych, ale nie przenoszenia danych z innych danych są przechowywane na serwerze FTP. Obsługuje ona zarówno lokalnie i w chmurze, serwerami FTP.

> [!NOTE]
> Aktywność kopiowania nie powoduje usunięcia pliku źródłowego, po pomyślnym jest kopiowany do lokalizacji docelowej. Jeśli musisz usunąć pliku źródłowego po pomyślnym kopiowania, tworzenia działań niestandardowych do usuwania pliku i użyj działania w potoku. 

## <a name="enable-connectivity"></a>Włącz połączenie
Jeśli przenosisz dane z **lokalnymi** serwer FTP do chmury danych magazynu (na przykład do magazynu obiektów Blob platformy Azure), zainstalować i używać bramy zarządzania danymi. Brama zarządzania danymi jest agent klienta, który jest zainstalowany na komputerze lokalnym, i udostępnia usługi w chmurze na łączenie z zasobami lokalnymi. Aby uzyskać więcej informacji, zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md). Instrukcje krok po kroku na ustawienie bramy w górę i korzystania z niego, zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md). Do nawiązywania połączenia z serwerem FTP będą używać bramy, nawet jeśli serwer znajduje się w infrastrukturze Azure jako usługa (IaaS) maszynę wirtualną (VM).

Użytkownik może zainstalować bramę na tym samym komputera lokalnego lub maszyn wirtualnych IaaS co na serwerze FTP. Jednak zaleca się zainstalowanie bramy na osobnym komputerze lub maszynie Wirtualnej IaaS, aby uniknąć rywalizacji i w celu zapewnienia lepszej wydajności. Po zainstalowaniu bramy na osobnym komputerze maszynie powinno być możliwe dostęp do serwera FTP.

## <a name="get-started"></a>Rozpoczęcie pracy
Można utworzyć potoku o działanie kopiowania, który przenosi dane ze źródła FTP przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania fabryki danych**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) Przewodnik Szybki.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy można użyć narzędzia i interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania.
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzia lub interfejsów API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON. Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z magazynu danych FTP, zobacz [przykład JSON: kopiowanie danych z serwera FTP do obiektów blob platformy Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) sekcji tego artykułu.

> [!NOTE]
> Aby uzyskać więcej informacji o obsługiwanych formatów plików i kompresji do użycia, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md).

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do FTP.

## <a name="linked-service-properties"></a>Połączona usługa właściwości
W poniższej tabeli opisano specyficzne dla usługi FTP połączone elementy JSON.

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| type |Ustaw tę wartość na SerwerFTP. |Yes |&nbsp; |
| host |Określ nazwę lub adres IP serwera FTP. |Yes |&nbsp; |
| Typ authenticationType |Określ typ uwierzytelniania. |Yes |Basic anonimowe |
| nazwa użytkownika |Określ użytkownika, który ma dostęp do serwera FTP. |Nie |&nbsp; |
| hasło |Określ hasło dla użytkownika (username). |Nie |&nbsp; |
| encryptedCredential |Określ zaszyfrowane poświadczenia dostępu do serwera FTP. |Nie |&nbsp; |
| gatewayName |Określ nazwę bramy w brama zarządzania danymi do nawiązania połączenia lokalnego serwera FTP. |Nie |&nbsp; |
| port |Określ port, na którym nasłuchuje serwer FTP. |Nie |21 |
| enableSsl |Określ, czy używać FTP za pośrednictwem kanału SSL/TLS. |Nie |true |
| enableServerCertificateValidation |Określ, czy w celu włączenia weryfikacji certyfikatu serwera SSL, gdy używasz FTP za pośrednictwem kanału SSL/TLS. |Nie |true |

>[!NOTE]
>Łącznik FTP obsługuje podczas uzyskiwania dostępu do serwera FTP za pomocą szyfrowania lub żaden jawne szyfrowania SSL/TLS; nie obsługuje on niejawne szyfrowania SSL/TLS.

### <a name="use-anonymous-authentication"></a>Uwierzytelnianie anonimowe

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Użyj nazwy użytkownika i hasła w postaci zwykłego tekstu dla uwierzytelniania podstawowego

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Użyj portu, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>EncryptedCredential używany do uwierzytelniania i bramy

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md). Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów w zestawie danych.

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych. Zawiera informacje specyficzne dla typu zestawu danych. **TypeProperties** sekcja dla zestawu danych typu **FileShare** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Podrzędną do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [próbki połączone definicje usługi i zestawu danych](#sample-linked-service-and-dataset-definitions) przykłady.<br/><br/>Możesz łączyć tej właściwości z **partitionBy** ścieżki folderu oparte na początku wycinka i kończyć daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** aby tabela do odwoływania się do określonego pliku w folderze. Jeśli nie określono żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Gdy **fileName** nie jest określony dla wyjściowego zestawu danych, nazwę wygenerowanego pliku znajduje się w następującym formacie: <br/><br/>Dane. <Guid>.txt (przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| obiektu fileFilter |Określ filtr służący do wybierania podzbioru plików w **folderPath**, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **obiektu fileFilter** dotyczy wejściowy zestaw danych z udziału plików. Ta właściwość nie jest obsługiwana z Hadoop Distributed pliku System (HDFS). |Nie |
| partitionedBy |Służy do określania dynamicznym **folderPath** i **fileName** czasu serii danych. Na przykład można określić **folderPath** który jest sparametryzowana dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz skopiować pliki są one między magazynów opartych na plikach (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**, i są obsługiwane poziomy **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy ma być używany tryb transferu binarne. Wartości są względem niego spełnione trybie binarnym (jest to wartość domyślna), a wartość false dla ASCII. Ta właściwość jest używana tylko w przypadku typu skojarzonej połączonej usługi typu: SerwerFTP. |Nie |

> [!NOTE]
> **Nazwa pliku** i **obiektu fileFilter** nie mogą być używane jednocześnie.

### <a name="use-the-partionedby-property"></a>Użyj właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić dynamicznym **folderPath** i **fileName** czasu serii danych z **partitionedBy** właściwości.

Informacje na temat zestawów danych serii czasu, planowanie i wycinków, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md), [planowania i wykonywania](data-factory-scheduling-and-execution.md), i [tworzenie potoków](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Przykład 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {wycinka} zostanie zastąpiony wartością zmiennej systemowej fabryki danych SliceStart, w formacie określonym (YYYYMMDDHH). SliceStart odnosi się uruchomienie wycinka. Ścieżka folderu jest różne dla każdego wycinka. (Na przykład wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104).

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
W tym przykładzie rok, miesiąc, dzień i czas SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez **folderPath** i **fileName** właściwości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md). Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działania z drugiej strony, zależą od każdego typu działania. Dla działania kopiowania właściwości typu się różnić w zależności od typów źródeł i sink.

W przypadku działania kopiowania, gdy źródłem jest typu **FileSystemSource**, jest dostępna w następujących właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z podfoldery lub tylko określonego folderu. |Wartość true, False (ustawienie domyślne) |Nie |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Przykład JSON: kopiowanie danych z serwera FTP do obiektów Blob platformy Azure
W tym przykładzie pokazano, jak skopiować dane z serwera FTP do magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane bezpośrednio do wychwytywanie w [obsługiwane formaty i magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats), za pomocą działania kopiowania w fabryce danych.  

Poniższe przykłady zapewniają definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Połączonej usługi typu [SerwerFTP](#linked-service-properties)
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [udziału plików](#dataset-properties)
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Przykład kopiuje dane z serwera FTP do obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

### <a name="ftp-linked-service"></a>Połączona usługa FTP

W tym przykładzie używane uwierzytelnianie podstawowe, przy użyciu nazwy użytkownika i hasła w postaci zwykłego tekstu. Można także użyć jednej z następujących sposobów:

* Uwierzytelnianie anonimowe
* Uwierzytelnianie podstawowe z zaszyfrowane poświadczenia
* FTP over SSL/TLS (FTPS)

Zobacz [FTP połączona usługa](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

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
### <a name="ftp-input-dataset"></a>Zestaw danych wejściowych FTP

Ten zestaw danych odwołuje się do folderu FTP `mysharedfolder` i plik `test.csv`. Potok kopiuje plik do lokalizacji docelowej.

Ustawienie **zewnętrznych** do **true** informuje usługi fabryka danych z zestawu danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczane, na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Działanie kopiowania w potoku z zbiornika źródła i obiektów blob systemu plików

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **FileSystemSource**i **zbiornika** ustawiono typ **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
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
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

* Informacje na temat kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w fabryce danych i zoptymalizować ją na różne sposoby, zobacz [skopiuj wydajności działania i dostrajania przewodnik](data-factory-copy-activity-performance.md).

* Aby uzyskać instrukcje tworzenia potoku z działaniem kopiowania, zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
