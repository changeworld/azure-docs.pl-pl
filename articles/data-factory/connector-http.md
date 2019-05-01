---
title: Kopiowanie danych ze źródła HTTP przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z chmury lub środowisku lokalnym źródło HTTP do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: f25b0f2c7b5e3148bae778c4b50a3f0bd0c148da
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875944"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego HTTP przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-http-connector.md)
> * [Bieżąca wersja](connector-http.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z punktu końcowego HTTP. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

Różnica między ten łącznik protokołu HTTP [REST łącznika](connector-rest.md) i [łącznik Tabela sieci Web](connector-web-table.md) są:

- **Łącznik REST** specjalnie do obsługi kopiowania danych z interfejsów API RESTful; 
- **Łącznik protokołu HTTP** ogólnego do pobierania danych z dowolnego punktu końcowego HTTP, np. Aby pobrać plik. Zanim łącznik REST staje się dostępna, może się zdarzyć na potrzeby kopiowania danych z interfejsu API RESTful, co jest obsługiwane, ale mniej funkcjonalności, porównanie z łącznika REST łącznik protokołu HTTP.
- **Łącznik Tabela sieci Web** wyodrębnia tabelę zawartości z sieci Web w formacie HTML.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Można skopiować danych ze źródła HTTP, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

Można użyć tego łącznika protokołu HTTP, aby:

- Pobierania danych z punktu końcowego HTTP/Https przy użyciu protokołu HTTP **UZYSKAĆ** lub **WPIS** metody.
- Pobieranie danych przy użyciu jednej z następujących uwierzytelnienia: **Anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, lub **ClientCertificate**.
- Skopiuj odpowiedź HTTP jako — jest lub go przeanalizować przy użyciu [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Aby przetestować żądania HTTP do pobierania danych, aby skonfigurować łącznik protokołu HTTP w usłudze Data Factory, informacje na temat specyfikacji interfejsu API dla nagłówka i treści wymagania. Narzędzia, takie jak Postman lub przeglądarki sieci web służy do sprawdzania poprawności.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które służy do definiowania jednostek usługi Data Factory, które są specyficzne dla łącznik protokołu HTTP.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane przez usługę połączoną HTTP:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **HttpServer**. | Yes |
| url | Podstawowy adres URL do serwera sieci web. | Yes |
| enableServerCertificateValidation | Określ, czy włączyć obsługę weryfikacji certyfikatu SSL serwera podczas łączenia z punktu końcowego HTTP. Jeśli serwer protokołu HTTPS używa certyfikatu z podpisem własnym, należy ustawić tę właściwość na **false**. | Nie<br /> (wartość domyślna to **true**) |
| Element authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, i **ClientCertificate**. <br><br> Zobacz sekcje pod tą tabelą, aby uzyskać więcej właściwości i przykłady kodu JSON dla tych typów uwierzytelniania. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime. Jeśli nie zostanie określony, ta właściwość używa domyślnego środowiska Azure Integration Runtime. |Nie |

### <a name="using-basic-digest-or-windows-authentication"></a>Uwierzytelnianie podstawowe, szyfrowane lub Windows

Ustaw **authenticationType** właściwości **podstawowe**, **szyfrowanego**, lub **Windows**. Oprócz ogólne właściwości, które są opisane w poprzedniej sekcji określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika na potrzeby dostępu do punktu końcowego HTTP. | Yes |
| password | Hasło dla użytkownika ( **userName** wartości). Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Przykład**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Przy użyciu uwierzytelniania ClientCertificate

Aby użyć uwierzytelniania ClientCertificate, ustaw **authenticationType** właściwości **ClientCertificate**. Oprócz ogólne właściwości, które są opisane w poprzedniej sekcji określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| embeddedCertData | Dane zakodowane w formacie Base64 certyfikatu. | Wybierz opcję **embeddedCertData** lub **certthumbprint, aby**. |
| certThumbprint | Odcisk palca certyfikatu, który jest zainstalowany na magazyn certyfikatów Self-Hosted Integration Runtime tego komputera. Ma zastosowanie tylko wtedy, gdy typ samodzielnie hostowanego środowiska Integration Runtime określono w **connectVia** właściwości. | Wybierz opcję **embeddedCertData** lub **certthumbprint, aby**. |
| password | Hasło, które ma skojarzony z certyfikatem. Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

Jeśli używasz **certthumbprint, aby** dla uwierzytelniania i certyfikat został zainstalowany w magazynie osobistym komputera lokalnego, udziel uprawnień do odczytu Self-Hosted Integration Runtime:

1. Otwórz program Microsoft Management Console (MMC). Dodaj **certyfikaty** przystawki przeznaczonego dla **komputera lokalnego**.
2. Rozwiń **certyfikaty** > **osobistych**, a następnie wybierz pozycję **certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat w magazynie osobistym, a następnie wybierz **wszystkie zadania** > **Zarządzaj kluczami prywatnymi**.
3. Na **zabezpieczeń** pozycję Dodaj konto użytkownika, pod którym działa usługa hosta Integration Runtime (DIAHostService), z dostępem do odczytu do certyfikatu.

**Przykład 1: Za pomocą certthumbprint, aby**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: Za pomocą embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. 

- Dla **Parquet i format tekstu rozdzielanego**, można znaleźć [zestawu danych formatu Parquet i tekst rozdzielany](#parquet-and-delimited-text-format-dataset) sekcji.
- Dla innych formatów, takich jak **format ORC/Avro/JSON/dane binarne**, można znaleźć [innych zestawu danych w formacie](#other-format-dataset) sekcji.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet i zestaw danych, format tekstu rozdzielanego

Aby skopiować dane z protokołu HTTP w **Parquet lub format tekstu rozdzielanego**, można znaleźć [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuł na format oparty na zestawie danych i obsługiwane Ustawienia. Następujące właściwości są obsługiwane w przypadku protokołu HTTP w ramach `location` ustawienia w formacie na podstawie zestawu danych:

| Właściwość    | Opis                                                  | Wymagane |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Właściwość type w obszarze `location` w zestawie danych musi być równa **HttpServerLocation**. | Yes      |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane.       | Nie       |

> [!NOTE]
> Obsługiwany rozmiar ładunku żądania HTTP jest około 500 KB. Jeśli rozmiar ładunku, które mają być przekazane do punktu końcowego usługi sieci web jest większy niż 500 KB, należy wziąć pod uwagę dzielenia na partie ładunku na mniejsze fragmenty.

> [!NOTE]
> **HttpFile** typ zestawu danych w formacie Parquet/tekstu opisane w następnej sekcji nadal jest obsługiwany jako — jest dla działania kopiowania/wyszukiwania zgodności z poprzednimi wersjami. Zaleca się użyć tego nowego modelu idąc dalej, a ADF tworzenia interfejsu użytkownika zostało przełączone do generowania te nowe typy.

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Innym formacie zestawu danych

Aby skopiować dane z protokołu HTTP w **format ORC/Avro/JSON/dane binarne**, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwości zestawu danych musi być równa **HttpFile**. | Yes |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie jest określona, używana jest tylko adres URL, który jest określony w definicji połączonej usługi. | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **uzyskać** (ustawienie domyślne) i **wpis**. | Nie |
| additionalHeaders | Dodatkowe nagłówki żądania HTTP. | Nie |
| requestBody | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz pobrać dane z punktu końcowego HTTP jako — jest bez analizy, a następnie skopiuj je do sklepu oparte na plikach, Pomiń **format** sekcji w definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować zawartości odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. W obszarze **format**ustaw **typu** jedną z następujących wartości właściwości. Aby uzyskać więcej informacji, zobacz [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format), [format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [Avro format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Obsługiwane typy: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Obsługiwane poziomy:  **Optymalne** i **najszybszy**. |Nie |

> [!NOTE]
> Obsługiwany rozmiar ładunku żądania HTTP jest około 500 KB. Jeśli rozmiar ładunku, które mają być przekazane do punktu końcowego usługi sieci web jest większy niż 500 KB, należy wziąć pod uwagę dzielenia na partie ładunku na mniejsze fragmenty.

**Przykład 1: Za pomocą metody Get (ustawienie domyślne)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Przykład 2: Za pomocą metody Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które obsługuje źródło HTTP.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP jako źródło

- Na potrzeby kopiowania z **Parquet i format tekstu rozdzielanego**, można znaleźć [Parquet i źródło format tekstu rozdzielanego](#parquet-and-delimited-text-format-source) sekcji.
- Na potrzeby kopiowania z innych formatów, takich jak **format ORC/Avro/JSON/dane binarne**, można znaleźć [innego formatu źródła](#other-format-source) sekcji.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet i źródło format tekstu rozdzielanego

Aby skopiować dane z protokołu HTTP w **Parquet lub format tekstu rozdzielanego**, można znaleźć [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuł na temat źródła działania kopiowania oparta na format i Obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku protokołu HTTP w ramach `storeSettings` ustawienia źródła kopiowania oparta na format:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość type w obszarze `storeSettings` musi być równa **HttpReadSetting**. | Yes      |
| requestMethod            | Metoda HTTP. <br>Dozwolone wartości to **uzyskać** (ustawienie domyślne) i **wpis**. | Nie       |
| addtionalHeaders         | Dodatkowe nagłówki żądania HTTP.                             | Nie       |
| requestBody              | Treść żądania HTTP.                               | Nie       |
| requestTimeout           | Limit czasu ( **TimeSpan** wartość) dla żądania HTTP można uzyskać odpowiedzi. Ta wartość jest limit czasu można uzyskać odpowiedzi nie limitu czasu można odczytać danych odpowiedzi. Wartość domyślna to **00:01:40**. | Nie       |
| MaxConcurrentConnections | Liczba połączeń połączyć się z magazynu magazynu jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie       |

> [!NOTE]
> Dla formatu Parquet/rozdzielany tekst **HttpSource** źródło działania kopiowania typu opisane w następnej sekcji nadal jest obsługiwany jako — dotyczy zgodności z poprzednimi wersjami. Zaleca się użyć tego nowego modelu idąc dalej, a ADF tworzenia interfejsu użytkownika zostało przełączone do generowania te nowe typy.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSetting",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Inne źródła formatu

Aby skopiować dane z protokołu HTTP w **format ORC/Avro/JSON/dane binarne**, następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość źródła działania kopiowania musi być równa **HttpSource**. | Yes |
| httpRequestTimeout | Limit czasu ( **TimeSpan** wartość) dla żądania HTTP można uzyskać odpowiedzi. Ta wartość jest limit czasu można uzyskać odpowiedzi nie limitu czasu można odczytać danych odpowiedzi. Wartość domyślna to **00:01:40**.  | Nie |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).
