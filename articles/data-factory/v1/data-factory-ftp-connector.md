---
title: Przenoszenie danych z serwera FTP za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z serwera FTP za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4aba7aadbe92b6c4f0ab417785e230bb6a6823df
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523430"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Przenoszenie danych z serwera FTP za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-ftp-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-ftp.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznik FTP w wersji 2](../connector-ftp.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z serwera FTP. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z serwera FTP do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Usługa Data Factory obsługuje obecnie tylko przenosi dane z serwera FTP do innych magazynów danych, ale nie przenosi dane z innych danych są przechowywane na serwerze FTP. Obsługuje ona zarówno lokalnie i w chmurze serwerów FTP.

> [!NOTE]
> Działanie kopiowania nie powoduje usunięcia pliku źródłowego, po pomyślnym są kopiowane do lokalizacji docelowej. Jeśli zachodzi potrzeba usunięcia pliku źródłowego po kopiowania zakończonego powodzeniem, Utwórz niestandardowe działanie, aby usunąć ten plik, a następnie użyć działania w potoku.

## <a name="enable-connectivity"></a>Włącz połączenia
Jeśli chcesz przenieść dane z **lokalnych** serwera FTP do chmury dane magazynu (na przykład do magazynu obiektów Blob platformy Azure), zainstalować i używać bramy zarządzania danymi. Brama zarządzania danymi to agent klienta, który jest zainstalowany na maszynie lokalnej i umożliwia usług cloud services, połączyć się z zasobami lokalnymi. Aby uzyskać więcej informacji, zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md). Instrukcje krok po kroku na temat ustawień dotyczących Konfigurowanie bramy i korzystania z niego, zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md). Aby nawiązać połączenie z serwerem FTP będą używać bramy, nawet jeśli serwer znajduje się na infrastrukturę platformy Azure jako usługa (IaaS) maszyny wirtualnej (VM).

Istnieje możliwość zainstalować bramę na tym samym komputerze lokalnym lub maszynie Wirtualnej IaaS jako serwer FTP. Jednak zaleca się zainstalowanie bramy na osobnym komputerze lub maszynie Wirtualnej IaaS, aby uniknąć rywalizacji i zapewnienia lepszej wydajności. Po zainstalowaniu bramy na osobnym komputerze, maszyna powinna mieć możliwość dostępu do serwera FTP.

## <a name="get-started"></a>Rozpoczęcie pracy
Utworzysz potok z działaniem kopiowania, które przenosi dane ze źródła FTP przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania usługi Data Factory**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i **Interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych FTP, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z serwera FTP do usługi Azure blob](#json-example-copy-data-from-ftp-server-to-azure-blob) dalszej części tego artykułu.

> [!NOTE]
> Szczegółowe informacje na temat obsługiwanych formatów plików i kompresji do użycia, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do protokołu FTP.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
W poniższej tabeli opisano specyficzne dla usługi FTP, połączone elementy JSON.

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| type |Ustaw tę pozycję na SerwerFTP. |Yes |&nbsp; |
| host |Określ nazwę lub adres IP serwera FTP. |Yes |&nbsp; |
| Element authenticationType |Określ typ uwierzytelniania. |Yes |Podstawowe i anonimowe |
| nazwa użytkownika |Określ użytkownika, który ma dostęp do serwera FTP. |Nie |&nbsp; |
| hasło |Określ hasło dla użytkownika (nazwa użytkownika). |Nie |&nbsp; |
| encryptedCredential |Określ zaszyfrowane poświadczenia dostępu do serwera FTP. |Nie |&nbsp; |
| gatewayName |Określ nazwę bramy w bramy zarządzania danymi, aby nawiązać połączenie z serwerem FTP w środowisku lokalnym. |Nie |&nbsp; |
| port |Określ port, na którym nasłuchuje serwer FTP. |Nie |21 |
| enableSsl |Określ, czy należy użyć serwera FTP za pośrednictwem kanału SSL/TLS. |Nie |true |
| enableServerCertificateValidation |Określ, czy włączyć weryfikacji certyfikatu SSL serwera w przypadku korzystania z protokołu FTP za pośrednictwem kanału SSL/TLS. |Nie |true |

>[!NOTE]
>Łącznik FTP obsługuje uzyskiwania dostępu do serwera FTP za pomocą szyfrowania lub żaden jawne szyfrowania SSL/TLS; nie obsługuje on niejawne szyfrowania SSL/TLS.

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>EncryptedCredential na użytek uwierzytelniania i bramę

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
Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md). Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów w zestawie danych.

**TypeProperties** sekcji różni się dla każdego typu zestawu danych. Zawiera on informacje, które są specyficzne dla typu zestawu danych. **TypeProperties** sekcji dla zestawu danych typu **FileShare** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Podrzędną do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz definicje usługi i zestaw danych próbka połączone przykłady.<br/><br/>Można połączyć tę właściwość z **partitionBy** mają ścieżki folderu oparte na początku wycinka i zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** chcącym tabeli do odwoływania się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Gdy **fileName** nie jest określona dla wyjściowego zestawu danych, nazwę wygenerowanego pliku znajduje się w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr, który ma być używany do Wybierz podzbiór plików w **folderPath**, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **obiektu fileFilter** jest odpowiednie dla wejściowego zestawu danych w udziale plików. Ta właściwość nie jest obsługiwana przy użyciu pliku System (HDFS, Hadoop Distributed). |Nie |
| partitionedBy |Można określić dynamiczny **folderPath** i **fileName** danych szeregów czasowych. Na przykład można określić **folderPath** , jest sparametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz skopiować pliki, ponieważ są one między magazynami oparte na plikach (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**, i są obsługiwane poziomy **optymalna** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy do używania trybu binarnego transferu. Wartości są względem niego spełnione w trybie binarnym (jest to wartość domyślna), a wartość false dla ASCII. Tej właściwości należy używać tylko przypadku typu skojarzonej połączonej usługi typu: FtpServer. |Nie |

> [!NOTE]
> **Nazwa pliku** i **obiektu fileFilter** nie mogą być używane jednocześnie.

### <a name="use-the-partionedby-property"></a>Użyj właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, możesz określić dynamiczny **folderPath** i **fileName** danych szeregów czasowych z **partitionedBy** właściwości.

Aby dowiedzieć się o zestawy danych serii czasu, planowanie i wycinków, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md), [planowanie i wykonywanie](data-factory-scheduling-and-execution.md), i [tworzenia potoków](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Przykład 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {wycinek} jest zastępowana wartością zmiennej systemowej usługi Data Factory SliceStart, w formacie określonym (YYYYMMDDHH). Parametru SliceStart odnosi się do rozpoczęcie wycinka. Ścieżka folderu różni się dla każdego wycinka. (Na przykład wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104).

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
W tym przykładzie rok, miesiąc, dzień i godzina SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez **folderPath** i **fileName** właściwości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [tworzenia potoków](data-factory-create-pipelines.md). Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działań, z drugiej strony, zależą od każdy typ działania. Właściwości typu różnią się w zależności od tego, jakiego typu źródła i ujścia dla działania kopiowania.

W działaniu kopiowania, gdy źródłem jest typu **FileSystemSource**, następująca właściwość jest dostępna w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. |Wartość true, False (domyślnie) |Nie |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z serwera FTP do obiektu Blob platformy Azure
Ten przykład pokazuje, jak skopiować dane z serwera FTP do usługi Azure Blob storage. Jednak możesz skopiować dane bezpośrednio do ujścia, o których wspomniano w [obsługiwane magazyny danych i formatów](data-factory-data-movement-activities.md#supported-data-stores-and-formats), za pomocą działania kopiowania w fabryce danych.

W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Połączonej usługi typu [SerwerFTP](#linked-service-properties)
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [udziału plików](#dataset-properties)
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [obiektu blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties)
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Przykład kopiuje dane z serwera FTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

### <a name="ftp-linked-service"></a>Połączona usługa FTP

W tym przykładzie używa uwierzytelniania podstawowego z nazwą użytkownika i hasła w postaci zwykłego tekstu. Można również użyć jednej z następujących sposobów:

* Uwierzytelnianie anonimowe
* Uwierzytelnianie podstawowe z zaszyfrowanymi poświadczeniami
* FTP za pośrednictwem protokołu SSL/TLS (FTPS)

Zobacz [FTP połączoną usługę](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

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
### <a name="ftp-input-dataset"></a>Wejściowy zestaw danych FTP

Ten zestaw danych odwołuje się do folderu FTP `mysharedfolder` i plik `test.csv`. Potok kopiuje plik do miejsca docelowego.

Ustawienie **zewnętrznych** do **true** informuje usługa Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana, na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Działania kopiowania w potoku za pomocą systemu plików ujścia źródła i obiektów blob

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **FileSystemSource**i **ujścia** ustawiono typ **BlobSink**.

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
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

* Informacje na temat kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w fabryce danych i różne sposoby, aby zoptymalizować ją, zobacz [skopiuj dostrajania przewodnik dotyczący wydajności działania i](data-factory-copy-activity-performance.md).

* Aby uzyskać szczegółowe instrukcje tworzenia potoku za pomocą działania kopiowania, zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
