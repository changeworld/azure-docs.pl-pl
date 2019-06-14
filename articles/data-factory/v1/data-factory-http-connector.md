---
title: Przenoszenie danych ze źródła HTTP — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane ze środowiska lokalnego lub w chmurze źródło HTTP przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f7e070788d2fc11addcafc30d9f232f194f44782
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318482"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Przenoszenie danych ze źródła HTTP przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-http-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-http.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Azure Data Factory, zobacz [łącznik protokołu HTTP w wersji 2](../connector-http.md).


W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do przenoszenia danych ze środowiska lokalnego lub w chmurze punkt końcowy HTTP do obsługiwanego magazynu danych ujścia. W tym artykule opiera się na [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md), który ma ogólne omówienie przenoszenie danych za pomocą działania kopiowania. Artykuł zawiera również listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia.

Data Factory obsługuje obecnie tylko przenosi dane ze źródła HTTP do innych magazynów danych. Program nie obsługuje przenoszenia danych z innych magazynów danych do miejsca docelowego protokołu HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Obsługiwane scenariusze i typy uwierzytelniania

Można użyć tego łącznika protokołu HTTP do pobierania danych z *zarówno w chmurze, jak i punktu końcowego HTTP/Https w środowisku lokalnym* przy użyciu protokołu HTTP **UZYSKAĆ** lub **WPIS** metody. Obsługiwane są następujące typy uwierzytelniania: **Anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, i **ClientCertificate**. Należy zanotować różnicę między tego łącznika i [łącznik Tabela sieci Web](data-factory-web-table-connector.md). Łącznik Tabela sieci Web wyodrębnia zawartość tabeli z kodu HTML, strony sieci Web.

Podczas kopiowania danych z punktu końcowego HTTP w środowisku lokalnym, należy zainstalować bramę zarządzania danymi w środowisku lokalnym lub Maszynie wirtualnej platformy Azure. Aby dowiedzieć się o bramy zarządzania danymi i instrukcje krok po kroku dotyczące sposobu konfigurowania bramy, zobacz [przenoszenie danych między lokalizacjami lokalnymi i w chmurze](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Rozpoczęcie pracy

Można utworzyć potoku, który ma działanie kopiowania może służyć do przenoszenia danych ze źródła HTTP przy użyciu różnych narzędzi lub interfejsów API:

- Najprostszym sposobem utworzenia potoku jest użycie Kreatora kopiowania danych. Aby uzyskać szybki Przewodnik tworzenia potoku za pomocą Kreatora kopiowania danych, zobacz [samouczka: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

- Można również użyć następujących narzędzi do utworzenia potoku: **witryny Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **usługi Azure Resource Manager Szablon**, **interfejsu API platformy .NET**, lub **interfejsu API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku, który zawiera działania kopiowania, zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Aby JSON przykłady kopiowania danych ze źródła HTTP do usługi Azure Blob storage, zobacz [JSON przykłady](#json-examples).

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

W poniższej tabeli opisano elementy JSON, które są specyficzne dla protokołu HTTP, połączone usługi:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type | **Typu** właściwość musi być równa **Http**. | Yes |
| url | Podstawowy adres URL do serwera sieci web. | Yes |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, i **ClientCertificate**. <br><br> Można znaleźć w kolejnych sekcjach, w tym artykule, aby więcej właściwości i przykłady kodu JSON dla tych typów uwierzytelniania. | Yes |
| enableServerCertificateValidation | Określa, czy włączyć obsługę weryfikacji certyfikatu SSL serwera, jeśli źródło jest serwerem sieci web protokołu HTTPS. Gdy serwer protokołu HTTPS używa certyfikatu z podpisem własnym, ustaw tę opcję na **false**. | Nie<br /> (wartość domyślna to **true**) |
| gatewayName | Nazwa wystąpienia bramy zarządzania danymi na potrzeby połączenia ze źródłem HTTP w środowisku lokalnym. | Tak, jeśli kopiujesz dane ze źródła HTTP w środowisku lokalnym |
| encryptedCredential | Zaszyfrowane poświadczenia umożliwiające dostęp do punktu końcowego HTTP. Wartość jest generowana automatycznie podczas konfigurowania informacji o uwierzytelnianiu za pomocą Kreatora kopiowania lub za pomocą **ClickOnce** okno dialogowe. | Nie<br /> (dotyczy tylko wtedy, gdy kopiowanie danych z lokalnego serwera HTTP) |

Aby uzyskać szczegółowe informacje dotyczące ustawiania poświadczeń dla źródła danych łącznik protokołu HTTP w środowisku lokalnym, zobacz [przenoszenie danych między źródłami lokalnymi i w chmurze przy użyciu bramy zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Uwierzytelnianie podstawowe, szyfrowane lub Windows

Ustaw **authenticationType** do **podstawowe**, **szyfrowanego**, lub **Windows**. Oprócz ogólnego właściwości łącznika HTTP opisanych w poprzednich sekcjach ustaw następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| userName | Nazwa użytkownika na potrzeby dostępu do punktu końcowego HTTP. | Yes |
| password | Hasło dla użytkownika (**username**). | Yes |

**Przykład: Uwierzytelnianie podstawowe, szyfrowane lub Windows**

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

### <a name="using-clientcertificate-authentication"></a>Przy użyciu uwierzytelniania ClientCertificate

Aby użyć uwierzytelniania podstawowego, ustaw **authenticationType** do **ClientCertificate**. Oprócz ogólnego właściwości łącznika HTTP opisanych w poprzednich sekcjach ustaw następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| embeddedCertData | Zawartość algorytmem Base64 dane binarne z pliku PFX. | Wybierz opcję **embeddedCertData** lub **certthumbprint, aby** |
| certThumbprint | Odcisk palca certyfikatu, który został zainstalowany na maszynie bramy magazynu certyfikatów. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego źródła HTTP. | Wybierz opcję **embeddedCertData** lub **certthumbprint, aby** |
| password | Hasło, które ma skojarzony z certyfikatem. | Nie |

Jeśli używasz **certthumbprint, aby** dla uwierzytelniania i certyfikat został zainstalowany w magazynie osobistym komputera lokalnego, udziel uprawnień odczytu do usługi bramy:

1. Otwórz program Microsoft Management Console (MMC). Dodaj **certyfikaty** przystawki przeznaczonego dla **komputera lokalnego**.
2. Rozwiń **certyfikaty** > **osobistych**, a następnie wybierz pozycję **certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat w magazynie osobistym, a następnie wybierz **wszystkie zadania** >**Zarządzaj kluczami prywatnymi**.
3. Na **zabezpieczeń** pozycję Dodaj konto użytkownika w ramach której działa Usługa Host bramy zarządzania danymi, z dostępem do odczytu do certyfikatu.  

**Przykład: Za pomocą certyfikatu klienta**

Ta połączona usługa łączy fabryki danych na lokalnym serwerze sieci web HTTP. Używa certyfikatu klienta, który jest zainstalowany na komputerze, na którym zainstalowano bramę zarządzania danymi.

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

**Przykład: W pliku przy użyciu certyfikatu klienta**

Ta połączona usługa łączy fabryki danych na lokalnym serwerze sieci web HTTP. Używa pliku certyfikatu klienta na komputerze, na którym zainstalowano bramę zarządzania danymi.

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

Niektóre sekcje pliku JSON zestawu danych, takie jak struktury, dostępność i zasady, są podobne dla wszystkich typów na zestaw danych (Azure SQL Database, Azure Blob storage, Azure Table storage).

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych. **TypeProperties** sekcja zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcji dla zestawu danych z **Http** typu ma następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** zestawu danych musi być równa **Http**. | Yes |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ścieżka nie jest określona, używana jest tylko adres URL, który jest określony w definicji połączonej usługi. <br><br> Aby skonstruować dynamiczny adres URL, możesz użyć [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md). Przykład: **relativeUrl**: **$$Text.Format ("/ my/raportu? miesiąc = {0: yyyy}-{0:MM} & fmt = csv", SliceStart)** . | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **UZYSKAĆ** i **WPIS**. | Nie <br />(wartość domyślna to **UZYSKAĆ**) |
| additionalHeaders | Dodatkowe nagłówki żądania HTTP. | Nie |
| requestBody | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz *pobierają dane z punktu końcowego HTTP jako — jest* bez podczas analizowania, Pomiń **format** ustawienie. <br><br> Jeśli chcesz przeanalizować zawartości odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Aby uzyskać więcej informacji, zobacz [format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Avro format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

**Przykład: Metoda GET (ustawienie domyślne)**

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

**Przykład: Za pomocą metody POST**

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

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [tworzenia potoków](data-factory-create-pipelines.md). 

Właściwości, które są dostępne w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań. Właściwości różnią się w zależności od tego, jakiego rodzaju źródłami i ujściami dla działania kopiowania.

Obecnie, gdy źródło w działaniu kopiowania jest **HttpSource** wpisz następujące właściwości są obsługiwane:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| httpRequestTimeout | Limit czasu ( **TimeSpan** wartość) dla żądania HTTP można uzyskać odpowiedzi. Limit czasu jest sposobem uzyskania odpowiedzi, a nie limitu czasu można odczytać danych odpowiedzi. | Nie<br />(wartość domyślna: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji

Zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) Aby uzyskać więcej informacji.

## <a name="json-examples"></a>Przykłady JSON

W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W przykładach pokazano, jak kopiować dane ze źródła HTTP w usłudze Azure Blob storage. Jednak dane mogą być kopiowane *bezpośrednio* z dowolnego źródła do dowolnego ujścia [obsługiwanych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

**Przykład: Kopiowanie danych ze źródła HTTP do usługi Azure Blob storage**

Rozwiązanie usługi fabryka danych dla tego przykładu zawiera następujących jednostek usługi Data Factory:

*   Połączonej usługi typu [HTTP](#linked-service-properties).
*   Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Dane wejściowe [dataset](data-factory-create-datasets.md) typu [Http](#dataset-properties).
*   Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   A [potoku](data-factory-create-pipelines.md) zawierającego działanie kopiowania, które używa [HttpSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane ze źródła HTTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w poniższych sekcjach przykładów.

### <a name="http-linked-service"></a>Usługa HTTP połączone

W tym przykładzie korzysta z usługi HTTP połączone przy użyciu uwierzytelniania anonymous. Zobacz [HTTP połączoną usługę](#linked-service-properties) dla różnych typów uwierzytelniania, można użyć.

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

### <a name="azure-storage-linked-service"></a>Połączona usługa magazynu Azure

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

### <a name="http-input-dataset"></a>Wejściowy zestaw danych protokołu HTTP

Ustawienie **zewnętrznych** do **true** usługi Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowane przez działanie w usłudze data factory.

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

Dane są zapisywane do nowego obiektu blob, co godzinę (**częstotliwość**: **godzinę**, **interwał**: **1**).

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

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **HttpSource** i **ujścia** ustawiono typ **BlobSink**.

Aby uzyskać listę właściwości, **HttpSource** obsługuje, zobacz [HttpSource](#copy-activity-properties).

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
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Aby uzyskać informacje dotyczące kluczowych czynników wpływających na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i zoptymalizować ją na różne sposoby, zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](data-factory-copy-activity-performance.md).