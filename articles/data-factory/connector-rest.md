---
title: Kopiowanie danych ze źródła REST przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z chmury lub środowisku lokalnym źródła REST do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: jingwang
ms.openlocfilehash: ee47f464c59bd9deed98671f19cfcc6d2c3c1b39
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762484"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego REST przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z punktu końcowego REST. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

Różnica między ten łącznik REST [łącznik protokołu HTTP](connector-http.md) i [łącznik Tabela sieci Web](connector-web-table.md) są:

- **Łącznik REST** specjalnie do obsługi kopiowania danych z interfejsów API RESTful; 
- **Łącznik protokołu HTTP** ogólnego do pobierania danych z dowolnego punktu końcowego HTTP, np. Aby pobrać plik. Zanim ten łącznik REST staje się dostępna, może się zdarzyć na potrzeby kopiowania danych z interfejsu API RESTful, co jest obsługiwane, ale mniej funkcjonalności, porównanie z łącznika REST łącznik protokołu HTTP.
- **Łącznik Tabela sieci Web** wyodrębnia tabelę zawartości z sieci Web w formacie HTML.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Można skopiować danych ze źródła REST, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

W szczególności ten ogólnego łącznika REST obsługuje:

- Pobieranie danych z punktu końcowego REST przy użyciu **UZYSKAĆ** lub **WPIS** metody.
- Trwa pobieranie danych przy użyciu jednej z następujących uwierzytelnienia: **Anonimowe**, **podstawowe**, **nazwy głównej usługi AAD**, i **zarządzanych tożsamości dla zasobów platformy Azure**.
- **[Podział na strony](#pagination-support)**  w interfejsach API REST.
- Kopiowanie odpowiedź REST JSON [jako — jest](#export-json-response-as-is) lub go przeanalizować przy użyciu [mapowanie schematu](copy-activity-schema-and-type-mapping.md#schema-mapping). Tylko ładunek odpowiedzi w **JSON** jest obsługiwana.

> [!TIP]
> Aby przetestować żądania pobierania danych, przed skonfigurowaniem łącznika REST w usłudze Data Factory, informacje na temat specyfikacji interfejsu API dla nagłówka i treści wymagania. Narzędzia, takie jak Postman lub przeglądarki sieci web służy do sprawdzania poprawności.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które służy do definiowania jednostek usługi Data Factory, które są specyficzne dla łącznika usługi REST.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla usługi REST połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **RestService**. | Yes |
| url | Podstawowy adres URL usługi REST. | Yes |
| enableServerCertificateValidation | Określa, czy sprawdzanie poprawności certyfikatu SSL po stronie serwera, podczas nawiązywania połączenia z punktem końcowym. | Nie<br /> (wartość domyślna to **true**) |
| Element authenticationType | Typ uwierzytelniania używany do łączenia z usługi REST. Dozwolone wartości to **anonimowe**, **podstawowe**, **AadServicePrincipal** i **ManagedServiceIdentity**. Zobacz do odpowiedniej sekcji poniżej więcej właściwości i przykłady. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime. Jeśli nie zostanie określony, ta właściwość używa domyślnego środowiska Azure Integration Runtime. |Nie |

### <a name="use-basic-authentication"></a>Stosuj uwierzytelnianie podstawowe

Ustaw **authenticationType** właściwości **podstawowe**. Oprócz ogólne właściwości, które są opisane w poprzedniej sekcji określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika na potrzeby dostępu do punktu końcowego REST. | Yes |
| hasło | Hasło dla użytkownika ( **userName** wartości). Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>Użyj uwierzytelniania jednostki usługi AAD

Ustaw **authenticationType** właściwości **AadServicePrincipal**. Oprócz ogólne właściwości, które są opisane w poprzedniej sekcji określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji usługi Azure Active Directory. | Yes |
| servicePrincipalKey | Określ klucz aplikacji usługi Azure Active Directory. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure Portal. | Yes |
| aadResourceId | Określ zasób usługi AAD żądasz, do autoryzacji, np. `https://management.core.windows.net`.| Yes |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Używać zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure

Ustaw **authenticationType** właściwości **ManagedServiceIdentity**. Oprócz ogólne właściwości, które są opisane w poprzedniej sekcji określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| aadResourceId | Określ zasób usługi AAD żądasz, do autoryzacji, np. `https://management.core.windows.net`.| Yes |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych REST. 

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z usługi REST, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwości zestawu danych musi być równa **RestResource**. | Yes |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie jest określona, używana jest tylko adres URL, który jest określony w definicji połączonej usługi. | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **uzyskać** (ustawienie domyślne) i **wpis**. | Nie |
| additionalHeaders | Dodatkowe nagłówki żądania HTTP. | Nie |
| requestBody | Treść żądania HTTP. | Nie |
| paginationRules | Zasady podziału na strony do redagowania dalej żądania strony. Zapoznaj się [dzielenia na strony pomocy technicznej](#pagination-support) sekcją Szczegóły na temat. | Nie |

**Przykład 1: Za pomocą metody Get z podziałem na strony**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**Przykład 2: Za pomocą metody Post**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które obsługuje źródła REST.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST jako źródło

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość źródła działania kopiowania musi być równa **RestSource**. | Yes |
| httpRequestTimeout | Limit czasu ( **TimeSpan** wartość) dla żądania HTTP można uzyskać odpowiedzi. Ta wartość jest limit czasu można uzyskać odpowiedzi nie limitu czasu można odczytać danych odpowiedzi. Wartość domyślna to **00:01:40**.  | Nie |
| requestInterval | Czas oczekiwania przed wysłaniem żądania do następnej strony. Wartość domyślna to **00:00:01** |  Nie |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Obsługa dzielenia na strony

Normalnie interfejs API REST ograniczyć jej rozmiar ładunku odpowiedzi pojedynczego żądania w rozsądnym numer; trochę czasu, aby zwrócić dużą ilość danych, dzieli wynik na wielu stronach i wymaga wywołań do wysyłania kolejnych żądań można uzyskać następnej strony wyników. Zazwyczaj żądania na jednej stronie jest dynamiczne i złożone informacje zwrócone z odpowiedzi poprzedniej strony.

Ten ogólny łącznik REST obsługuje następujące wzorce dzielenia na strony: 

* Kolejne żądanie bezwzględny lub względny adres URL = wartość właściwości w bieżącej treści odpowiedzi
* Kolejne żądanie bezwzględny lub względny adres URL = wartość nagłówka w bieżących nagłówkach odpowiedzi
* Parametr zapytania w kolejnym żądaniu = wartość właściwości w bieżącej treści odpowiedzi
* Parametr zapytania w kolejnym żądaniu = wartość nagłówka w bieżących nagłówkach odpowiedzi
* Nagłówek żądania dalej = wartość właściwości w bieżącej treści odpowiedzi
* Nagłówek żądania dalej = wartość nagłówka w bieżących nagłówkach odpowiedzi

**Zasady podziału na strony** są definiowane jako słownik w zestawie danych, które zawierają jeden lub więcej liter pary klucz wartość. Konfiguracja będzie służyć do generowania żądań, zaczynając od drugiej strony. Łącznik zostanie zatrzymane, iteracja po otrzyma kod stanu HTTP 204 (Brak zawartości) lub dowolne wyrażenie JSONPath w "paginationRules" zwraca wartość null.

**Obsługiwane klucze** w regułach dzielenia na strony:

| Klucz | Opis |
|:--- |:--- |
| AbsoluteUrl | Określa adres URL następnego żądania. Może być **bezwzględny adres URL lub względny adres URL**. |
| QueryParameters.*request_query_parameter* OR QueryParameters['request_query_parameter'] | "request_query_parameter" jest zdefiniowana przez użytkownika odwołujące się jedną nazwę parametru zapytania w następnym adresu URL żądania HTTP. |
| Nagłówki. *request_header* lub nagłówków [request_header] | "request_header" jest zdefiniowana przez użytkownika które odwołują się do jednej nazwy nagłówka w następnym żądaniu HTTP. |

**Obsługiwane wartości** w regułach dzielenia na strony:

| Wartość | Opis |
|:--- |:--- |
| Nagłówki. *response_header* lub nagłówków [response_header] | "response_header" jest zdefiniowana przez użytkownika które odwołują się do jednej nazwy nagłówka w bieżącej odpowiedzi HTTP, wartość, która będzie służyć do następnego żądania. |
| Wyrażenie JSONPath, rozpoczynając od "$" (co stanowi główny treści odpowiedzi) | Treść odpowiedzi może zawierać tylko jeden obiekt JSON. Wyrażenie JSONPath powinna zwracać pojedynczej wartości pierwotnych, który będzie używany do wysyłania następnego żądania. |

**Przykład:**

Interfejs API Graph serwisu Facebook zwraca odpowiedź w następującej struktury, w którym wielkość następny adres URL jest reprezentowana w ***paging.next***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Odpowiedniej konfiguracji zestawu danych REST szczególnie `paginationRules` jest następująca:

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>Eksportuj odpowiedź w formacie JSON jako — jest

Ten łącznik REST umożliwia eksportowanie odpowiedź interfejsu API REST w formacie JSON jako — jest do różnych magazynów opartych na plikach. Uzyskanie kopii takich niezależny od schematów, Pomiń "strukturę" (nazywane również *schematu*) sekcji zestaw danych i mapowanie schematu w działaniu kopiowania.

## <a name="schema-mapping"></a>mapowanie schematu

Aby skopiować dane z punktu końcowego REST, do ujścia tabelarycznych, zapoznaj się [mapowanie schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).
