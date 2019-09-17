---
title: Kopiowanie danych ze źródła HTTP przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o kopiowaniu danych z chmury lub lokalnego źródła HTTP do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 5a4aa1640de3a090deceea690b21a40e49f8ce4c
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009097"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego HTTP przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-http-connector.md)
> * [Bieżąca wersja](connector-http.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z punktu końcowego HTTP. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

Różnica między tym łącznikiem HTTP, [łącznika REST](connector-rest.md) i [łącznikiem tabeli sieci Web](connector-web-table.md) :

- **Łącznik REST** obsługujący kopiowanie danych z interfejsów API RESTful; 
- **Łącznik http** jest ogólny do pobierania danych z dowolnego punktu końcowego http, np. do pobrania pliku. Przed udostępnieniem łącznika REST może wystąpić potrzeba użycia łącznika HTTP do kopiowania danych z interfejsu API RESTful, który jest obsługiwany, ale mniej funkcjonalny porównanie z łącznikiem REST.
- **Łącznik tabeli sieci Web** wyodrębnia zawartość tabeli z strony html.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik HTTP jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Możesz skopiować dane ze źródła HTTP do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

Tego łącznika protokołu HTTP można użyć do:

- Pobieranie danych z punktu końcowego HTTP/S przy użyciu metod **Get** lub **post** protokołu HTTP.
- Pobierz dane przy użyciu jednego z następujących uwierzytelnień: **Anonimowe**, **podstawowe**, **szyfrowane**, **Windows**lub **ClientCertificate**.
- Należy skopiować odpowiedź HTTP jako-is lub przeanalizować ją przy użyciu [obsługiwanych formatów plików i koderów](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Aby przetestować żądanie HTTP na potrzeby pobierania danych przed skonfigurowaniem łącznika HTTP w Data Factory, Dowiedz się więcej na temat specyfikacji interfejsu API dla wymagań dotyczących nagłówka i treści. Aby sprawdzić poprawność, można użyć narzędzi, takich jak program Poster lub przeglądarka sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania jednostek Data Factory, które są specyficzne dla łącznika HTTP.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi HTTP są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** musi być ustawiona na wartość **HttpServer**. | Tak |
| url | Podstawowy adres URL serwera sieci Web. | Tak |
| enableServerCertificateValidation | Określ, czy włączyć weryfikację certyfikatu protokołu SSL serwera podczas łączenia się z punktem końcowym HTTP. Jeśli serwer HTTPS używa certyfikatu z podpisem własnym, ustaw dla tej właściwości **wartość false**. | Nie<br /> (wartość domyślna to **true**) |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to **anonimowe**, **podstawowe**, **szyfrowane**, **Windows**i **ClientCertificate**. <br><br> Zapoznaj się z sekcjami w poniższej tabeli, aby uzyskać więcej właściwości i przykładów JSON dla tych typów uwierzytelniania. | Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określona, używana jest domyślna Azure Integration Runtime. |Nie |

### <a name="using-basic-digest-or-windows-authentication"></a>Korzystanie z uwierzytelniania podstawowego, szyfrowanego lub systemu Windows

Ustaw właściwość **AuthenticationType** na wartość **Basic**, **Digest**lub **Windows**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika, która ma być używana do uzyskiwania dostępu do punktu końcowego HTTP. | Tak |
| password | Hasło użytkownika (wartość **username** ). Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |

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

### <a name="using-clientcertificate-authentication"></a>Korzystanie z uwierzytelniania ClientCertificate

Aby użyć uwierzytelniania ClientCertificate, należy ustawić Właściwość AuthenticationType na **Kolekcja ClientCertificate**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| embeddedCertData | Dane certyfikatu zakodowane algorytmem Base64. | Określ wartość **embeddedCertData** lub **certThumbprint**. |
| certThumbprint | Odcisk palca certyfikatu, który został zainstalowany na własnym magazynie certyfikatów maszyny Integration Runtime. Ma zastosowanie tylko wtedy, gdy w właściwości **właściwością connectvia** jest określony typ samodzielny Integration Runtime. | Określ wartość **embeddedCertData** lub **certThumbprint**. |
| password | Hasło skojarzone z certyfikatem. Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

Jeśli używasz **certThumbprint** do uwierzytelniania, a certyfikat jest instalowany w magazynie osobistym komputera lokalnego, Udziel uprawnień do odczytu Integration Runtime samoobsługowego:

1. Otwórz program Microsoft Management Console (MMC). Dodaj przystawkę **Certyfikaty** , która jest przeznaczona dla **komputera lokalnego**.
2. Rozwiń węzeł **Certyfikaty** > **osobiste**, a następnie wybierz pozycję **Certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat ze sklepu osobistego, a następnie wybierz pozycję **wszystkie zadania** > **Zarządzaj kluczami prywatnymi**.
3. Na karcie **zabezpieczenia** Dodaj konto użytkownika, w ramach którego jest uruchomiona usługa hosta Integration Runtime (DIAHostService) z dostępem do odczytu do certyfikatu.

**Przykład 1: Korzystanie z certThumbprint**

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

**Przykład 2: Korzystanie z embeddedCertData**

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

- W przypadku **Parquet, rozdzielonego tekstu, JSON, Avro i formatu binarnego**zapoznaj się z sekcją [Parquet, rozdzielaną tekstem, JSON, Avro i binarny zestaw danych](#format-based-dataset) .
- W przypadku innych formatów, takich jak **Format Orc**, zapoznaj się z sekcją [innego formatu zestawu danych](#other-format-dataset) .

### <a name="format-based-dataset"></a>Parquet, rozdzielony tekst, JSON, Avro i binarny zestaw danych

Aby skopiować dane do i z **Parquet, rozdzielonego tekstu, JSON, Avro i formatu binarnego**, zapoznaj się z [formatem Parquet](format-parquet.md), [rozdzielanym formatem tekstu](format-delimited-text.md), [formatem Avro](format-avro.md) i artykułem [binarnym](format-binary.md) w formacie zestawu danych opartym na formacie i obsługiwanymi ustawieniami . Następujące właściwości są obsługiwane w przypadku protokołu HTTP `location` w obszarze Ustawienia w zestawie danych opartym na formacie:

| Właściwość    | Opis                                                  | Wymagane |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Właściwość `location` Type w elemencie DataSet musi być ustawiona na wartość **HttpServerLocation**. | Tak      |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane.       | Nie       |

> [!NOTE]
> Obsługiwany rozmiar ładunku żądania HTTP to około 500 KB. Jeśli rozmiar ładunku, który ma zostać przekazany do punktu końcowego sieci Web, jest większy niż 500 KB, należy rozważyć przetwarzanie wsadowe w mniejszych fragmentach.

> [!NOTE]
> Zestaw danych typu **HttpFile** o formacie Parquet/Text wymienionym w następnej sekcji jest nadal obsługiwany w przypadku działania kopiowania/wyszukiwania w celu zapewnienia zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu do przechodzenia do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania tych nowych typów.

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

### <a name="other-format-dataset"></a>Inny zestaw danych formatu

Aby skopiować dane z protokołu HTTP w **formacie Orc**, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** zestawu danych musi być ustawiona na wartość **HttpFile**. | Tak |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. | Nie |
| RequestMethod | Metoda HTTP. Dozwolone wartości to **Get** (default) i **post**. | Nie |
| additionalHeaders | Dodatkowe nagłówki żądań HTTP. | Nie |
| Elemencie requestbody | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz pobrać dane z punktu końcowego HTTP jako-is bez analizy, a następnie skopiować dane do magazynu opartego na plikach, Pomiń sekcję **Format** w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować zawartość odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. W obszarze **Format**ustaw właściwość **Type** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format)i [Format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Nie |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Obsługiwane typy: **Gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy: Optymalnai najszybsza. |Nie |

> [!NOTE]
> Obsługiwany rozmiar ładunku żądania HTTP to około 500 KB. Jeśli rozmiar ładunku, który ma zostać przekazany do punktu końcowego sieci Web, jest większy niż 500 KB, należy rozważyć przetwarzanie wsadowe w mniejszych fragmentach.

**Przykład 1: Przy użyciu metody get (domyślnie)**

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

**Przykład 2: Korzystanie z metody post**

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

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło HTTP.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP jako źródło

- Aby skopiować z **Parquet, rozdzielony tekst, JSON, Avro i format binarny**, zapoznaj się z sekcją [Parquet, rozdzielaną tekstem, JSON, Avro i binarną](#format-based-source) .
- Aby skopiować inne formaty, takie jak **Format Orc**, zapoznaj się z sekcją [inne źródło formatu](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, rozdzielany tekstem, JSON, Avro i format binarny

Aby skopiować dane z **Parquet, rozdzielonego tekstu, JSON, Avro i formatu binarnego**, zapoznaj się z [formatem Parquet](format-parquet.md), [rozdzielanym formatem tekstu](format-delimited-text.md), [formatem Avro](format-avro.md) i artykułem [binarnym w formacie](format-binary.md) źródłowym i obsługiwanym przez program. Ustawienia. Następujące właściwości są obsługiwane w przypadku protokołu HTTP `storeSettings` w obszarze Ustawienia źródła kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **HttpReadSetting**. | Tak      |
| RequestMethod            | Metoda HTTP. <br>Dozwolone wartości to **Get** (default) i **post**. | Nie       |
| addtionalHeaders         | Dodatkowe nagłówki żądań HTTP.                             | Nie       |
| Elemencie requestbody              | Treść żądania HTTP.                               | Nie       |
| requestTimeout           | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Ta wartość jest przekroczeniem limitu czasu w celu uzyskania odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. Wartość domyślna to **00:01:40**. | Nie       |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |

> [!NOTE]
> W przypadku formatu tekstu Parquet/Unlimited Źródło działania kopiowania typu **HttpSource** wymienione w następnej sekcji jest nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu do przechodzenia do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania tych nowych typów.

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

#### <a name="other-format-source"></a>Inne źródło formatowania

Aby skopiować dane z protokołu HTTP w **formacie Orc**, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **HttpSource**. | Tak |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Ta wartość jest przekroczeniem limitu czasu w celu uzyskania odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. Wartość domyślna to **00:01:40**.  | Nie |

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

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).
