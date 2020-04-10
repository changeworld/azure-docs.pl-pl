---
title: Przenoszenie danych z serwera FTP przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenosić dane z serwera FTP przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eeeb122d240d8c3eae4ebe1650f67cf0e4b9dac6
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992049"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Przenoszenie danych z serwera FTP przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-ftp-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-ftp.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik FTP w wersji 2](../connector-ftp.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z serwera FTP. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Dane z serwera FTP można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [obsługiwanych magazynów danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Usługa Data Factory obsługuje obecnie tylko przenoszenie danych z serwera FTP do innych magazynów danych, ale nieprzekazowy przenoszenie danych z innych magazynów danych na serwer FTP. Obsługuje zarówno lokalne, jak i chmurowe serwery FTP.

> [!NOTE]
> Działanie kopiowania nie powoduje usunięcia pliku źródłowego po pomyślnym skopiowaniu go do miejsca docelowego. Jeśli chcesz usunąć plik źródłowy po pomyślnej kopii, utwórz działanie niestandardowe, aby usunąć plik i użyj działania w potoku.

## <a name="enable-connectivity"></a>Włącz łączność
Jeśli przenosisz dane z lokalnego serwera FTP do magazynu danych w chmurze **(na** przykład do magazynu obiektów Blob platformy Azure), zainstaluj i użyj bramy zarządzania danymi. Brama zarządzania danymi jest agentem klienta zainstalowanym na komputerze lokalnym i umożliwia usługom w chmurze łączenie się z zasobem lokalnym. Aby uzyskać szczegółowe informacje, zobacz [Brama zarządzania danymi](data-factory-data-management-gateway.md). Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy i korzystania z niej, zobacz [Przenoszenie danych między lokalizacjami lokalnymi a chmurą](data-factory-move-data-between-onprem-and-cloud.md). Brama służy do łączenia się z serwerem FTP, nawet jeśli serwer znajduje się na platformie Azure infrastruktury jako usługi (IaaS) maszyny wirtualnej (VM).

Istnieje możliwość zainstalowania bramy na tym samym komputerze lokalnym lub maszynie wirtualnej IaaS jako serwer FTP. Zaleca się jednak zainstalowanie bramy na oddzielnym komputerze lub maszynie wirtualnej IaaS, aby uniknąć rywalizacji o zasoby i zapewnić lepszą wydajność. Po zainstalowaniu bramy na oddzielnym komputerze urządzenie powinno mieć dostęp do serwera FTP.

## <a name="get-started"></a>Rozpoczęcie pracy
Można utworzyć potok z działaniem kopiowania, który przenosi dane ze źródła FTP przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania fabryki danych**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **PowerShell**, **szablon usługi Azure Resource Manager,** **interfejs API .NET**i **interfejs API REST**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Podczas korzystania z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET) definiuje się te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych FTP, zobacz [przykład JSON: Kopiowanie danych z serwera FTP do usługi Azure w](#json-example-copy-data-from-ftp-server-to-azure-blob) tym artykule.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat obsługiwanych formatów plików i kompresji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla ftp.

## <a name="linked-service-properties"></a>Połączone właściwości usługi
W poniższej tabeli opisano elementy JSON specyficzne dla usługi połączonej ftp.

| Właściwość | Opis | Wymagany | Domyślne |
| --- | --- | --- | --- |
| type |Ustaw to na FtpServer. |Tak |&nbsp; |
| host |Określ nazwę lub adres IP serwera FTP. |Tak |&nbsp; |
| authenticationType |Określ typ uwierzytelniania. |Tak |Podstawowy, Anonimowy |
| nazwa użytkownika |Określ użytkownika, który ma dostęp do serwera FTP. |Nie |&nbsp; |
| hasło |Określ hasło użytkownika (nazwa użytkownika). |Nie |&nbsp; |
| encryptedCredential |Określ zaszyfrowane poświadczenia, aby uzyskać dostęp do serwera FTP. |Nie |&nbsp; |
| nazwa bramy |Określ nazwę bramy w bramie zarządzania danymi, aby połączyć się z lokalnym serwerem FTP. |Nie |&nbsp; |
| port |Określ port, na którym nasłuchuje serwer FTP. |Nie |21 |
| Enablessl |Określ, czy protokół FTP ma być używany przez kanał SSL/TLS. |Nie |true |
| enableServerCertificateWwadacja |Określ, czy podczas korzystania z kanału SSL/TLS należy włączyć sprawdzanie poprawności certyfikatu protokołu TLS/SSL serwera. |Nie |true |

>[!NOTE]
>Łącznik FTP obsługuje dostęp do serwera FTP bez szyfrowania lub jawnego szyfrowania SSL/TLS; nie obsługuje niejawnego szyfrowania SSL/TLS.

### <a name="use-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Używanie nazwy użytkownika i hasła w postaci zwykłego tekstu do uwierzytelniania podstawowego

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Używanie zaszyfrowanej funkcjiCredential do uwierzytelniania i bramy

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
Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md). Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych.

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych. Zawiera informacje, które są specyficzne dla typu zestawu danych. Sekcja **typeProperties** dla zestawu danych typu **FileShare** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Ścieżka podrzędna do folderu. Użyj znaku ucieczki \ ' dla znaków specjalnych w ciągu. Zobacz przykłady połączonych definicji usługi i zestawów danych, aby zapoznać się z przykładami.<br/><br/>Tę właściwość można połączyć z **partitionBy,** aby ścieżki folderów oparte na daty rozpoczęcia i zakończenia plasterka. |Tak |
| fileName |Określ nazwę pliku w **folderPath,** jeśli chcesz, aby tabela odwoływała się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli **nazwa pliku** nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego pliku jest w następującym formacie: <br/><br/>`Data.<Guid>.txt`(Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| Filefilter |Określ filtr, który ma być używany do wybierania podzbioru plików w **folderPath**, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wiele `?` znaków) i (pojedynczy znak).<br/><br/>Przykład 1:`"fileFilter": "*.log"`<br/>Przykład 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** ma zastosowanie do wejściowego zestawu danych FileShare. Ta właściwość nie jest obsługiwana przez system rozproszonych plików Hadoop (HDFS). |Nie |
| partitionedBy |Służy do określania **folderu dynamicznegoPath** i **fileName** dla danych szeregów czasowych. Na przykład można określić **folderPath,** który jest sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcje [Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz skopiować pliki w stanie, w jakim znajdują się między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to **GZip**, **Deflate**, **BZip2**i **ZipDeflate**, a obsługiwane poziomy są **optymalne** i **najszybsze.** Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy ma być używany tryb transferu binarnego. Wartości są prawdziwe dla trybu binarnego (jest to wartość domyślna) i false dla ASCII. Tej właściwości można używać tylko wtedy, gdy skojarzony typ połączonej usługi jest typu: FtpServer. |Nie |

> [!NOTE]
> **FileName** i **fileFilter** nie mogą być używane jednocześnie.

### <a name="use-the-partionedby-property"></a>Użyj właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić **folder dynamicznyPath** i **fileName** dla danych szeregów czasowych z **partitionedBy** właściwości.

Aby dowiedzieć się więcej o zestawach danych szeregów czasowych, planowaniu i plasterkach, zobacz [Tworzenie zestawów danych,](data-factory-create-datasets.md) [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md)oraz Tworzenie [potoków](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Przykład 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {Slice} jest zastępowany wartością zmiennej systemowej Data Factory SliceStart w określonym formacie (RRRRYMMDDHH). SliceStart odnosi się do czasu rozpoczęcia plasterka. Ścieżka folderu jest inna dla każdego plasterka. (Na przykład wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104).)

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
W tym przykładzie rok, miesiąc, dzień i godzina SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez **właściwości folderPath** i **fileName.**

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md). Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji **typeProperties** działania, z drugiej strony, różnią się w zależności od typu działania. W przypadku działania kopiowania właściwości typu różnią się w zależności od typów źródeł i pochłaniacze.

W działaniu kopiowania, gdy źródłem jest typ **FileSystemSource,** w sekcji **typeProperties** dostępna jest następująca właściwość:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, fałsz (domyślnie) |Nie |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z serwera FTP do obiektu Blob platformy Azure
W tym przykładzie pokazano, jak skopiować dane z serwera FTP do magazynu obiektów blob platformy Azure. Jednak dane mogą być kopiowane bezpośrednio do dowolnego zlewów podanych w [obsługiwanych magazynach danych i formatach,](data-factory-data-movement-activities.md#supported-data-stores-and-formats)przy użyciu działania kopiowania w fabryce danych.

Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)lub programu [PowerShell:](data-factory-copy-activity-tutorial-using-powershell.md)

* Powiązana usługa typu [FtpServer](#linked-service-properties)
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [FileShare](#dataset-properties)
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Przykładowy kopiuje dane z serwera FTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

### <a name="ftp-linked-service"></a>Usługa połączona z FTP

W tym przykładzie użyto uwierzytelniania podstawowego z nazwą użytkownika i hasłem w postaci zwykłego tekstu. Można również użyć jednego z następujących sposobów:

* Uwierzytelnianie anonimowe
* Uwierzytelnianie podstawowe przy użyciu zaszyfrowanych poświadczeń
* FTP przez SSL/TLS (FTPS)

Zobacz sekcję [usługi połączonej FTP,](#linked-service-properties) aby uzyskać różne typy uwierzytelniania, których można użyć.

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

Ten zestaw danych odnosi się `mysharedfolder` do `test.csv`folderu i pliku FTP . Potok kopiuje plik do miejsca docelowego.

Ustawienie na **wartość true** **z zewnątrz** informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

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

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest oceniana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny godziny godziny rozpoczęcia.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Działanie kopiowania w potoku ze źródłem systemu plików i ujściem obiektu blob

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **FileSystemSource,** a typ **ujścia** jest ustawiony na **BlobSink**.

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
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* Aby dowiedzieć się więcej o kluczowych czynnikach wpływających na wydajność przenoszenia danych (aktywność kopiowania) w fabryce danych oraz na różnych sposobach jej optymalizacji, zobacz [przewodnik Wydajność działania kopiowania i dostrajanie](data-factory-copy-activity-performance.md).

* Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku z działaniem kopiowania, zobacz [samouczek Kopiowania aktywności](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
