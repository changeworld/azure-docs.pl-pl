---
title: Przenoszenie danych ze źródła HTTP - Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z lokalną ani źródła HTTP chmury przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 16b181631d8d91ad8137e57564792789903bccf2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621623"
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Przenoszenie danych ze źródła HTTP przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-http-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-http.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika HTTP w wersji 2](../connector-http.md).


Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure do przenoszenia danych z punktu końcowego HTTP na lokalnej/w chmurze w magazynie danych obsługiwanych ujścia. W tym artykule opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przepływu danych działanie kopiowania i listę magazynów danych są obsługiwane jako źródła/sink.

Fabryka danych aktualnie obsługuje tylko przenoszenie danych ze źródła HTTP do innych magazynów danych, ale nie przenoszenia danych z innych danych przechowuje do miejsca docelowego protokołu HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Obsługiwane scenariusze i typy uwierzytelniania
Można użyć tego łącznika HTTP do pobierania danych z **zarówno w chmurze, jak i dla lokalnego punktu końcowego protokołu HTTP/s** przy użyciu protokołu HTTP **UZYSKAĆ** lub **POST** metody. Obsługiwane są następujące typy uwierzytelniania: **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, i  **ClientCertificate**. Różnice między tego łącznika i [łącznika sieci Web w tabeli](data-factory-web-table-connector.md) jest: drugie służy do wyodrębniania zawartości tabeli ze strony sieci web w formacie HTML.

Podczas kopiowania danych z lokalnego punktu końcowego HTTP, należy zainstalować bramę zarządzania danymi w lokalnym środowisku/Azure maszyny Wirtualnej. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane ze źródła HTTP przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

- Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. Dla przykładów JSON można skopiować danych z źródła HTTP do magazynu obiektów Blob Azure, zobacz [przykłady JSON](#json-examples) części w tym artykule.

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla protokołu HTTP elementy JSON połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type | Właściwość type musi mieć ustawioną: `Http`. | Yes |
| url | Podstawowy adres URL do serwera sieci Web | Yes |
| Typ authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to: **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, **ClientCertificate**. <br><br> Odpowiednio można znaleźć w sekcjach poniżej tej tabeli na więcej właściwości i przykłady JSON dla tych typów uwierzytelniania. | Yes |
| enableServerCertificateValidation | Określ, czy włączyć weryfikacji certyfikatu serwera SSL, jeśli źródło jest serwer sieci Web HTTPS. Jeśli serwer HTTPS korzysta z certyfikatu z podpisem własnym, ustaw wartość false. | Nie, domyślna to true |
| gatewayName | Nazwa bramy zarządzania danymi do łączenia się z lokalnym źródłem HTTP. | Tak, jeśli kopiowanie danych z lokalnego źródła HTTP. |
| encryptedCredential | Zaszyfrowane poświadczenia można uzyskać dostępu do punktu końcowego HTTP. Wygenerowany automatycznie podczas konfigurowania informacji o uwierzytelnianiu w kreatorze kopiowania lub w oknie podręcznym ClickOnce. | Nie. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera HTTP. |

Zobacz [przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md) szczegółowe informacje na temat ustawiania poświadczeń dla źródła danych łącznika HTTP lokalnymi.

### <a name="using-basic-digest-or-windows-authentication"></a>Uwierzytelnianie podstawowe, szyfrowane lub systemu Windows

Ustaw `authenticationType` jako `Basic`, `Digest`, lub `Windows`i określ następujące właściwości poza łącznika HTTP ogólnego z nich wprowadzono powyżej:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| nazwa użytkownika | Nazwa użytkownika do uzyskania dostępu punkt końcowy HTTP. | Yes |
| hasło | Hasło dla użytkownika (username). | Yes |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Przykład: uwierzytelnianie podstawowe, szyfrowane lub systemu Windows

```JSON
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

Aby uwierzytelnianie podstawowe, należy ustawić `authenticationType` jako `ClientCertificate`i określ następujące właściwości poza łącznika HTTP ogólnego z nich wprowadzono powyżej:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| embeddedCertData | Zawartość algorytmem Base64 danych binarnych z pliku wymiany informacji osobistych (PFX). | Określ `embeddedCertData` lub `certThumbprint`. |
| certThumbprint | Odcisk palca certyfikatu, który został zainstalowany na komputerze bramy magazynu certyfikatów. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego źródła HTTP. | Określ `embeddedCertData` lub `certThumbprint`. |
| hasło | Hasło skojarzone z certyfikatem. | Nie |

Jeśli używasz `certThumbprint` dla uwierzytelniania i certyfikat jest zainstalowany w magazynie osobistym komputera lokalnego, należy udzielić uprawnień do odczytu do usługi bramy:

1. Uruchom program Microsoft Management Console (MMC). Dodaj **certyfikaty** przystawki przeznaczonego **komputera lokalnego**.
2. Rozwiń węzeł **certyfikaty**, **osobistych**i kliknij przycisk **certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat z magazynu osobistego i wybierz **wszystkie zadania**->**Zarządzaj kluczami prywatnymi...**
3. Na **zabezpieczeń** , Dodaj konto użytkownika, pod którą jest uruchomiona usługa hosta bramy zarządzania danymi z dostępem do odczytu do certyfikatu.  

#### <a name="example-using-client-certificate"></a>Przykład: przy użyciu certyfikatu klienta
Łącza usługi to połączone fabrykę danych z lokalnego serwera sieci web HTTP. Używa certyfikatu klienta, który jest instalowany na komputerze z zainstalowana brama zarządzania danymi.

```JSON
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

#### <a name="example-using-client-certificate-in-a-file"></a>Przykład: przy użyciu certyfikatu klienta w pliku
Łącza usługi to połączone fabrykę danych z lokalnego serwera sieci web HTTP. Za pomocą pliku certyfikatu klienta na komputer i zainstalowana brama zarządzania danymi.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **Http** ma następujące właściwości

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Określony typ zestawu danych. należy wybrać opcję `Http`. | Yes |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ścieżka nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. <br><br> Aby skonstruować dynamicznego adresu URL, można użyć [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md), np. "relativeUrl": "$$Text.Format (" / Moje/raport? miesiąca = {0: yyyy}-{0:MM} & formatowaniu = csv ", SliceStart)". | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **UZYSKAĆ** lub **POST**. | Nie. Wartość domyślna to `GET`. |
| additionalHeaders | Dodatkowych nagłówków żądania HTTP. | Nie |
| requestBody | Treść żądania HTTP. | Nie |
| Format | Jeśli chcesz po prostu **pobierają dane z punktu końcowego HTTP jako — jest** bez podczas analizowania, Pomiń ten ustawienia formatu. <br><br> Jeśli chcesz przeanalizować zawartości odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="example-using-the-get-default-method"></a>Przykład: używających metody GET (ustawienie domyślne)

```JSON
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

### <a name="example-using-the-post-method"></a>Przykład: przy użyciu metody POST

```JSON
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
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działania z drugiej strony zależą od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Obecnie, gdy źródła w przypadku działania kopiowania jest typu **HttpSource**, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| httpRequestTimeout | Limit czasu (TimeSpan) dla żądania HTTP można uzyskać odpowiedzi. Limit czasu jest uzyskanie odpowiedzi nie limitu czasu można odczytać danych odpowiedzi. | Nie. Wartość domyślna: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md) artykuł na temat szczegółów.

## <a name="json-examples"></a>Przykłady JSON
Poniższy przykład Podaj definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych ze źródła HTTP do magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Przykład: Kopiowanie danych ze źródła HTTP do magazynu obiektów Blob Azure
Rozwiązanie fabryki danych dla tego przykładu zawiera następujące jednostek fabryki danych:

1. Połączonej usługi typu [HTTP](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [Http](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [HttpSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Próbka kopiuje dane ze źródła HTTP do obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

### <a name="http-linked-service"></a>Usługa HTTP połączone
W tym przykładzie jest używana usługa HTTP połączone przy użyciu uwierzytelniania anonimowego. Zobacz [HTTP połączona usługa](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

```JSON
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

### <a name="http-input-dataset"></a>Zestaw danych wejściowych HTTP
Ustawienie **zewnętrznych** do **true** usługi fabryka danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

```JSON
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

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1).

```JSON
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

### <a name="pipeline-with-copy-activity"></a>W potoku z działanie kopiowania

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **HttpSource** i **zbiornika** ustawiono typ **BlobSink**.

Zobacz [HttpSource](#copy-activity-properties) listę obsługiwanych przez HttpSource właściwości.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
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
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
