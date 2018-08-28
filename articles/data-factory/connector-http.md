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
ms.devlang: na
ms.topic: conceptual
ms.date: 08/24/2018
ms.author: jingwang
ms.openlocfilehash: 5afb2fccd5c7b8ca306079941837d854c0b21349
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091721"
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego HTTP przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-http-connector.md)
> * [Bieżąca wersja](connector-http.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z punktu końcowego HTTP. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Można skopiować danych ze źródła HTTP, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik protokołu HTTP obsługuje:

- Pobieranie danych z punktu końcowego HTTP/HTTPS przy użyciu protokołu HTTP **UZYSKAĆ** lub **WPIS** metody.
- Pobieranie danych przy użyciu następujących uwierzytelnień: **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, i  **ClientCertificate**.
- Kopiowanie odpowiedź HTTP jako — jest lub ją za pomocą analizy [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

Różnica między tego łącznika i [łącznik Tabela sieci Web](connector-web-table.md) jest, że jego służy do wyodrębniania zawartości tabeli ze strony internetowej HTML.

>[!TIP]
>Aby przetestować żądania HTTP dla pobierania przed skonfigurowaniem łącznik protokołu HTTP w usłudze ADF danych, uczenie się na podstawie specyfikacji interfejsu API w nagłówku i wymagania dotyczące treści i użyć narzędzia, takie jak Postman lub przeglądarki sieci web do sprawdzania poprawności.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonych na łącznik protokołu HTTP.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku protokołu HTTP, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **HttpServer**. | Yes |
| url | Podstawowy adres URL do serwera sieci Web | Yes |
| enableServerCertificateValidation | Określ, czy włączyć weryfikacji certyfikatu SSL serwera podczas nawiązywania połączenia z punktem końcowym HTTP. Korzystając z serwera HTTPS jest certyfikat z podpisem własnym, ustaw tę opcję na wartość false. | Nie, wartość domyślna to true |
| Element authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to: **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, **ClientCertificate**. <br><br> Zapoznaj się sekcje poniżej tej tabeli na więcej właściwości i przykłady kodu JSON dla tych typów uwierzytelniania, odpowiednio. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

### <a name="using-basic-digest-or-windows-authentication"></a>Uwierzytelnianie podstawowe, szyfrowane lub Windows

Ustaw właściwość "authenticationType" **podstawowe**, **szyfrowanego**, lub **Windows**, a następnie określ następujące właściwości wraz z ogólne właściwości opisane w poprzedniej sekcja:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika do dostępu do punktu końcowego HTTP. | Yes |
| hasło | Hasło dla użytkownika (nazwa użytkownika). Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Przykład**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<username>",
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

Aby użyć uwierzytelniania ClientCertificate, ustaw właściwość "authenticationType" na **ClientCertificate**, a następnie określ następujące właściwości wraz z ogólne właściwości opisanych w poprzedniej sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| embeddedCertData | Dane certyfikatu zakodowany w formacie Base64. | Wybierz opcję `embeddedCertData` lub `certThumbprint`. |
| certthumbprint, aby | Odcisk palca certyfikatu, który jest zainstalowany w magazynie certyfikatów komputera środowiskiem Integration Runtime. Ma zastosowanie tylko wtedy, gdy w connectVia określono typ Self-Hosted Integration Runtime. | Wybierz opcję `embeddedCertData` lub `certThumbprint`. |
| hasło | Hasło skojarzone z tym certyfikatem. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

Jeśli certyfikat został zainstalowany w magazynie osobistym komputera lokalnego "certthumbprint, aby" jest używany do uwierzytelniania, musisz udzielić uprawnień do odczytu środowiskiem Integration Runtime:

1. Uruchom program Microsoft Management Console (MMC). Dodaj **certyfikaty** przystawki przeznaczonego dla **komputera lokalnego**.
2. Rozwiń **certyfikaty**, **osobistych**i kliknij przycisk **certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat w magazynie osobistym, a następnie wybierz pozycję **wszystkie zadania** -> **Zarządzaj kluczami prywatnymi...**
3. Na **zabezpieczeń** pozycję Dodaj konto użytkownika, w którym usługa hosta Integration Runtime (DIAHostService) działa z dostępem do odczytu do certyfikatu.

**Przykład 1: używanie certthumbprint, aby**

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

**Przykład 2: używanie embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych protokołu HTTP.

Aby skopiować dane z protokołu HTTP, należy ustawić właściwość typu zestawu danych na **HttpFile**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **HttpFile** | Yes |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie zostanie określony, używana jest tylko adres URL, które są określone w definicji połączonej usługi. | Nie |
| requestMethod | Metoda HTTP.<br/>Dozwolone wartości to **uzyskać** (ustawienie domyślne) lub **wpis**. | Nie |
| additionalHeaders | Dodatkowe nagłówki żądania HTTP. | Nie |
| RequestBody | Treść żądania HTTP. | Nie |
| Format | Jeśli chcesz **pobierania danych z punktu końcowego HTTP jako — jest** bez analizowania go i skopiuj do sklepu oparte na plikach, Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować zawartości odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy: **optymalna** i **najszybciej**. |Nie |

>[!NOTE]
>Obsługiwany rozmiar ładunku żądania HTTP jest około 500KB. Jeśli rozmiar ładunku, które mają być przekazane do punktu końcowego usługi sieci web jest większy niż to, należy wziąć pod uwagę do przetworzenia wsadowego na mniejsze części.

**Przykład 1: przy użyciu metody Get (ustawienie domyślne)**

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

**Przykład 2: za pomocą metody Post**

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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło HTTP.

### <a name="http-as-source"></a>HTTP jako źródło

Aby skopiować dane z protokołu HTTP, należy ustawić typ źródła w działaniu kopiowania, aby **HttpSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **HttpSource** | Yes |
| httpRequestTimeout | Limit czasu (przedział czasu) dla żądania HTTP można uzyskać odpowiedzi. Limit czasu jest sposobem uzyskania odpowiedzi, a nie limitu czasu można odczytać danych odpowiedzi.<br/> Wartość domyślna to: 00:01:40  | Nie |

**Przykład:**

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
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
