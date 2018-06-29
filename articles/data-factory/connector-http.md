---
title: Kopiowanie danych ze źródła HTTP przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane ze źródła HTTP chmurze lub lokalnie do zbiornika obsługiwanych magazynów danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 05/22/2018
ms.author: jingwang
ms.openlocfilehash: a27d90006d31c83b5ebe6cfc4a8d97969743a91e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049862"
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego HTTP przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-http-connector.md)
> * [Bieżąca wersja](connector-http.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z punktu końcowego HTTP. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Dane ze źródła HTTP należy skopiować wszystkie obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik HTTP obsługuje:

- Pobieranie danych z punktu końcowego protokołu HTTP/s przy użyciu protokołu HTTP **UZYSKAĆ** lub **POST** metody.
- Podczas pobierania danych przy użyciu następujących uwierzytelnienia: **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, i  **ClientCertificate**.
- Kopiowanie odpowiedzi HTTP jako — jest lub za pomocą analizy [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

Różnica między tego łącznika i [łącznika sieci Web w tabeli](connector-web-table.md) jest, że jego służy do wyodrębniania zawartości tabeli ze strony sieci web w formacie HTML.

>[!TIP]
>Aby przetestować żądania HTTP dla pobierania przed skonfigurowaniem łącznika HTTP w ADF danych, Dowiedz się od specyfikacji interfejsu API, nagłówek i wymagania dotyczące treści i użyj narzędzi, takich jak Postman lub przeglądarki sieci web do sprawdzania poprawności.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika HTTP.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Usługa HTTP połączone obsługuje następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **HttpServer**. | Yes |
| url | Podstawowy adres URL do serwera sieci Web | Yes |
| enableServerCertificateValidation | Określ, czy włączyć weryfikacji certyfikatu serwera SSL podczas nawiązywania połączenia punkt końcowy HTTP. Jeśli serwer HTTPS korzysta z certyfikatu z podpisem własnym, ustaw wartość false. | Nie, domyślna to true |
| Typ authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to: **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, **ClientCertificate**. <br><br> Odpowiednio można znaleźć w sekcjach poniżej tej tabeli na więcej właściwości i przykłady JSON dla tych typów uwierzytelniania. | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

### <a name="using-basic-digest-or-windows-authentication"></a>Uwierzytelnianie podstawowe, szyfrowane lub systemu Windows

Ustaw dla właściwości "authenticationType" **podstawowe**, **szyfrowanego**, lub **Windows**i określ następujące właściwości wraz z właściwości ogólnych opisanych w poprzedniej sekcja:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika do uzyskania dostępu punkt końcowy HTTP. | Yes |
| hasło | Hasło dla użytkownika (userName). Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

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

Aby uwierzytelnianie ClientCertificate, ustaw właściwość "authenticationType" **ClientCertificate**, a następnie określ następujące właściwości wraz z właściwości ogólnych opisanych w poprzedniej sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| embeddedCertData | Dane certyfikatu kodowany w standardzie Base64. | Określ `embeddedCertData` lub `certThumbprint`. |
| certThumbprint | Odcisk palca certyfikatu, który jest zainstalowany na komputerze środowiska uruchomieniowego integracji Self-hosted magazynu certyfikatów. Ma zastosowanie tylko wtedy, gdy określono siebie typu środowiska uruchomieniowego integracji w connectVia. | Określ `embeddedCertData` lub `certThumbprint`. |
| hasło | Hasło skojarzone z certyfikatem. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

Jeśli certyfikat został zainstalowany w magazynie osobistym komputera lokalnego "certThumbprint" jest używany do uwierzytelniania, należy udzielić uprawnień do odczytu do środowiska uruchomieniowego integracji Self-hosted:

1. Uruchom program Microsoft Management Console (MMC). Dodaj **certyfikaty** przystawki przeznaczonego **komputera lokalnego**.
2. Rozwiń węzeł **certyfikaty**, **osobistych**i kliknij przycisk **certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat z magazynu osobistego i wybierz **wszystkie zadania** -> **Zarządzaj kluczami prywatnymi...**
3. Na **zabezpieczeń** , Dodaj konto użytkownika, pod którą jest uruchomiona Usługa Host środowiska uruchomieniowego integracji (DIAHostService) z dostępem do odczytu do certyfikatu.

**Przykład 1: użycie certThumbprint**

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

**Przykład 2: przy użyciu embeddedCertData**

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych protokołu HTTP.

Aby skopiować dane z protokołu HTTP, ustaw właściwość Typ zestawu danych do **HttpFile**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **HttpFile** | Yes |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Gdy ta właściwość nie jest określona, tylko adres URL określony w definicji połączonej usługi jest używany. | Nie |
| requestMethod | Metoda HTTP.<br/>Dozwolone wartości to **uzyskać** (ustawienie domyślne) lub **Post**. | Nie |
| additionalHeaders | Dodatkowych nagłówków żądania HTTP. | Nie |
| requestBody | Treść żądania HTTP. | Nie |
| Format | Jeśli chcesz **pobierania danych z punktu końcowego HTTP jako — jest** bez analizowania go i kopiowania do magazynu opartych na plikach, Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować zawartości odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [formacie tekstowym](supported-file-formats-and-compression-codecs.md#text-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Obsługiwane poziomy: **optymalna** i **najszybciej**. |Nie |

**Przykład 1: użycie metody Get (ustawienie domyślne)**

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

**Przykład 2: przy użyciu metody Post**

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

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło HTTP.

### <a name="http-as-source"></a>HTTP jako źródło

Aby skopiować dane z protokołu HTTP, należy ustawić typ źródła w przypadku działania kopiowania do **HttpSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **HttpSource** | Yes |
| httpRequestTimeout | Limit czasu (TimeSpan) dla żądania HTTP można uzyskać odpowiedzi. Limit czasu jest uzyskanie odpowiedzi nie limitu czasu można odczytać danych odpowiedzi.<br/> Wartość domyślna to: 00:01:40  | Nie |

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
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
