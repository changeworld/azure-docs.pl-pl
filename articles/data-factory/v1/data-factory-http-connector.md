---
title: Przenoszenie danych ze źródła HTTP — Azure
description: Dowiedz się, jak przenosić dane z lokalnego lub chmurowego źródła HTTP przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e668f44bbc3d2e381edeb80c568a41355584a4ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260425"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Przenoszenie danych ze źródła HTTP przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-http-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-http.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Azure Data Factory, zobacz [łącznik HTTP w wersji 2](../connector-http.md).


W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnego lub chmurowego punktu końcowego HTTP do obsługiwanego magazynu danych ujścia. W tym artykule omówiono [polecenie Przenieś dane przy użyciu funkcji Aktywność kopiowania,](data-factory-data-movement-activities.md)która przedstawia ogólny przegląd przenoszenia danych przy użyciu działania kopiowania. W tym artykule wymieniono również magazyny danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze.

Usługa Data Factory obsługuje obecnie tylko przenoszenie danych ze źródła HTTP do innych magazynów danych. Nie obsługuje przenoszenia danych z innych magazynów danych do miejsca docelowego HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Obsługiwane scenariusze i typy uwierzytelniania

Za pomocą tego łącznika HTTP można pobierać dane zarówno z *chmury, jak i lokalnego punktu końcowego HTTP/S* przy użyciu metod HTTP **GET** lub **POST.** Obsługiwane są następujące typy uwierzytelniania: **Anonimowy**, **Podstawowy,** **Skrót,** **Windows**i **ClientCertificate**. Zwróć uwagę na różnicę między tym łącznikiem a [łącznikiem tabeli sieci Web](data-factory-web-table-connector.md). Łącznik tabeli sieci Web wyodrębnia zawartość tabeli ze strony html.

Podczas kopiowania danych z lokalnego punktu końcowego HTTP należy zainstalować bramę zarządzania danymi w środowisku lokalnym lub na maszynie Wirtualnej platformy Azure. Aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy, zobacz [Przenoszenie danych między lokalizacjami lokalnymi a chmurą](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Wprowadzenie

Można utworzyć potok, który ma działanie kopiowania, aby przenieść dane ze źródła HTTP przy użyciu różnych narzędzi lub interfejsów API:

- Najprostszym sposobem utworzenia potoku jest użycie kreatora Kopiowania danych. Aby uzyskać szybki instruktaż tworzenia potoku za pomocą Kreatora kopiowania danych, zobacz [Samouczek: Tworzenie potoku za pomocą kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

- Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager,** **interfejs API .NET**lub **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku, który ma działanie kopiowania, zobacz [samouczek Działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Dla przykładów JSON, które kopiują dane ze źródła HTTP do magazynu obiektów Blob platformy Azure, zobacz [przykłady JSON](#json-examples).

## <a name="linked-service-properties"></a>Połączone właściwości usługi

W poniższej tabeli opisano elementy JSON, które są specyficzne dla usługi połączonej HTTP:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type | Właściwość **typu** musi być ustawiona na **Http**. | Tak |
| url | Podstawowy adres URL serwera sieci web. | Tak |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to **Anonimowe,** **Podstawowe,** **Skrótowe,** **Windows**i **ClientCertificate**. <br><br> Więcej właściwości i przykładów JSON dla tych typów uwierzytelniania można znaleźć w kolejnych sekcjach tego artykułu. | Tak |
| enableServerCertificateWwadacja | Określa, czy włączyć sprawdzanie poprawności certyfikatu SSL serwera, jeśli źródłem jest serwer sieci web HTTPS. Gdy serwer HTTPS używa certyfikatu z podpisem własnym, ustaw to na **false**. | Nie<br /> (wartość domyślna jest **prawdziwa)** |
| nazwa bramy | Nazwa wystąpienia bramy zarządzania danymi, które ma być używane do łączenia się z lokalnym źródłem HTTP. | Tak, jeśli kopiujesz dane z lokalnego źródła HTTP |
| encryptedCredential | Zaszyfrowane poświadczenia dostępu do punktu końcowego HTTP. Wartość jest automatycznie generowana podczas konfigurowania informacji uwierzytelniania w kreatorze kopiowania lub przy użyciu okna dialogowego **ClickOnce.** | Nie<br /> (dotyczy tylko kopiowania danych z lokalnego serwera HTTP) |

Aby uzyskać szczegółowe informacje na temat ustawiania poświadczeń dla lokalnego źródła danych łącznika HTTP, zobacz [Przenoszenie danych między źródłami lokalnymi a chmurą przy użyciu bramy zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Korzystanie z uwierzytelniania podstawowego, skrótowego lub uwierzytelniania systemu Windows

Ustaw **authenticationType** na **Basic**, **Digest**lub **Windows**. Oprócz ogólnych właściwości łącznika HTTP opisanych w poprzednich sekcjach należy ustawić następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| userName | Nazwa użytkownika, która ma być używana do uzyskiwania dostępu do punktu końcowego HTTP. | Tak |
| hasło | Hasło użytkownika (**nazwa użytkownika**). | Tak |

**Przykład: korzystanie z uwierzytelniania podstawowego, skrótowego lub uwierzytelniania systemu Windows**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Korzystanie z uwierzytelniania ClientCertificate

Aby użyć uwierzytelniania podstawowego, ustaw **authenticationType** na **ClientCertificate**. Oprócz ogólnych właściwości łącznika HTTP opisanych w poprzednich sekcjach należy ustawić następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| embeddedCertData | Zakodowana w formacie Base64 zawartość danych binarnych pliku PFX. | Określ **osadzonyDertData** lub **certThumbprint** |
| certThumbprint | Odcisk palca certyfikatu zainstalowanego w magazynie certyfikatów komputera bramy. Stosowanie jest stosowane tylko podczas kopiowania danych z lokalnego źródła HTTP. | Określ **osadzonyDertData** lub **certThumbprint** |
| hasło | Hasło skojarzone z certyfikatem. | Nie |

Jeśli do uwierzytelniania jest używany **drukarz certThumbprint,** a certyfikat jest zainstalowany w magazynie osobistym komputera lokalnego, udziel uprawnień do odczytu do usługi bramy:

1. Otwórz program Microsoft Management Console (MMC). Dodaj przystawkę **Certyfikaty,** która jest przeznaczona dla **komputera lokalnego**.
2. Rozwiń pozycję **Certyfikaty** > **osobiste**, a następnie wybierz pozycję **Certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat z magazynu osobistego, a następnie wybierz pozycję **Wszystkie zadania** >**Zarządzaj kluczami prywatnymi**.
3. Na karcie **Zabezpieczenia** dodaj konto użytkownika, na którym jest uruchomiona usługa hosta bramy zarządzania danymi, z dostępem do odczytu do certyfikatu.  

**Przykład: używanie certyfikatu klienta**

Ta połączona usługa łączy fabrykę danych z lokalnym serwerem sieci Web HTTP. Używa certyfikatu klienta zainstalowanego na komputerze z zainstalowaną bramą zarządzania danymi.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**Przykład: używanie certyfikatu klienta w pliku**

Ta połączona usługa łączy fabrykę danych z lokalnym serwerem sieci Web HTTP. Używa pliku certyfikatu klienta na komputerze, na który zainstalowano bramę zarządzania danymi.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Niektóre sekcje pliku JSON zestawu danych, takie jak struktura, dostępność i zasady, są podobne dla wszystkich typów zestawów danych (usługa Azure SQL Database, magazyn obiektów Blob platformy Azure, magazyn tabel platformy Azure).

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych. Sekcja **typeProperties** zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **Http** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | **Typ** zestawu danych musi być ustawiony na **Http**. | Tak |
| względnyUrl | Względny adres URL do zasobu, który zawiera dane. Gdy ścieżka nie jest określona, używany jest tylko adres URL określony w definicji połączonej usługi. <br><br> Aby skonstruować dynamiczny adres URL, można użyć [funkcji fabryki danych i zmiennych systemowych](data-factory-functions-variables.md). Przykład: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**. | Nie |
| wniosekMetoda | Metoda HTTP. Dozwolone wartości to **GET** i **POST**. | Nie <br />(domyślnie jest **GET)** |
| dodatkowePozyty | Dodatkowe nagłówki żądań HTTP. | Nie |
| requestBody (Ciało) | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz *pobrać dane z punktu końcowego HTTP w stanie— jest* bez analizowania, pomiń ustawienie **formatu.** <br><br> Jeśli chcesz przeanalizować zawartość odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Aby uzyskać więcej informacji, zobacz [Format tekstu, format](data-factory-supported-file-and-compression-formats.md#text-format) [JSON,](data-factory-supported-file-and-compression-formats.md#json-format) [format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [format Orka](data-factory-supported-file-and-compression-formats.md#orc-format)i [format Parkietu](data-factory-supported-file-and-compression-formats.md#parquet-format). |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

**Przykład: Użycie metody GET (domyślna)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Przykład: Użycie metody POST**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md). 

Właściwości, które są dostępne w sekcji **typeProperties** działania różnią się z każdego typu działania. W przypadku działania kopiowania właściwości różnią się w zależności od typów źródeł i pochłaniacze.

Obecnie, gdy źródło w działania kopiowania jest typu **HttpSource,** obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan)** dla żądania HTTP, aby uzyskać odpowiedź. Jest to limit czasu, aby uzyskać odpowiedź, a nie limit czasu do odczytu danych odpowiedzi. | Nie<br />(wartość domyślna: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji

Zobacz [formaty plików i kompresji w usłudze Azure Data Factory, aby](data-factory-supported-file-and-compression-formats.md) uzyskać więcej informacji.

## <a name="json-examples"></a>Przykłady JSON

Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Przykłady pokazują, jak skopiować dane ze źródła HTTP do magazynu obiektów blob platformy Azure. Jednak dane mogą być kopiowane *bezpośrednio* z dowolnego źródła do dowolnego ujścia, [które są obsługiwane](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

**Przykład: Kopiowanie danych ze źródła HTTP do magazynu obiektów blob platformy Azure**

Rozwiązanie Data Factory dla tego przykładu zawiera następujące jednostki fabryki danych:

*   Połączona usługa typu [HTTP](#linked-service-properties).
*   Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [Http](#dataset-properties).
*   Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   [Potok,](data-factory-create-pipelines.md) który ma działanie kopiowania, który używa [HttpSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane ze źródła HTTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach, które są zgodne z próbkami.

### <a name="http-linked-service"></a>Usługa połączona HTTP

W tym przykładzie użyto usługi połączonej HTTP z uwierzytelnianiem anonimowym. Zobacz [http połączone usługi](#linked-service-properties) dla różnych typów uwierzytelniania, którego można użyć.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Usługa połączona z magazynem platformy Azure

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Zestaw danych wejściowych HTTP

Ustawienie na **wartość true** **z zewnątrz** informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure

Dane są zapisywane w nowym obiekcie blob co godzinę **(częstotliwość:** **godzina**, **interwał**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Potok, który używa działania kopiowania

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **HttpSource,** a typ **ujścia** jest ustawiony na **BlobSink**.

Aby uzyskać listę właściwości, które obsługuje **httpsource,** zobacz [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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

> [!NOTE]
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Działanie kopiowania) w usłudze Azure Data Factory i na różne sposoby jej optymalizacji, zobacz [przewodnik o wydajności działania kopiowania i dostrajaniu.](data-factory-copy-activity-performance.md)
