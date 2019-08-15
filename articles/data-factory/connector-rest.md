---
title: Kopiowanie danych z źródła REST przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o kopiowaniu danych z chmury lub lokalnego źródła REST do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 8c7c8faad70022ba985a4041fd578becbaf70078
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966862"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego REST przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z punktu końcowego REST. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

Różnica między tym łącznikiem REST, [łącznika http](connector-http.md) i [łącznikiem tabeli sieci Web](connector-web-table.md) :

- **Łącznik REST** obsługujący kopiowanie danych z interfejsów API RESTful; 
- **Łącznik http** jest ogólny do pobierania danych z dowolnego punktu końcowego http, np. do pobrania pliku. Przed udostępnieniem tego łącznika REST może wystąpić potrzeba użycia łącznika HTTP do kopiowania danych z interfejsu API RESTful, który jest obsługiwany, ale mniej funkcjonalny jest porównywany z łącznikiem REST.
- **Łącznik tabeli sieci Web** wyodrębnia zawartość tabeli z strony html.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane można kopiować ze źródła REST do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

W przypadku tego ogólnego łącznika REST obsługiwane są następujące funkcje:

- Pobieranie danych z punktu końcowego REST przy użyciu metod **Get** i **post** .
- Pobieranie danych przy użyciu jednego z następujących uwierzytelnień: **Anonimowe**, **podstawowe**, nazwa **główna usługi AAD**i **zarządzane tożsamości dla zasobów platformy Azure**.
- **[Podział na strony](#pagination-support)** w interfejsach API REST.
- Kopiowanie odpowiedzi JSON [w formacie REST jako-is](#export-json-response-as-is) lub analizowanie jej przy użyciu [mapowania schematu](copy-activity-schema-and-type-mapping.md#schema-mapping). Obsługiwany jest tylko ładunek odpowiedzi w formacie **JSON** .

> [!TIP]
> Aby przetestować żądanie pobrania danych przed skonfigurowaniem łącznika REST w Data Factory, Dowiedz się więcej na temat specyfikacji interfejsu API dla wymagań dotyczących nagłówka i treści. Aby sprawdzić poprawność, można użyć narzędzi, takich jak program Poster lub przeglądarka sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania jednostek Data Factory, które są specyficzne dla łącznika REST.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi REST są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** musi być ustawiona na wartość **RestService**. | Tak |
| url | Podstawowy adres URL usługi REST. | Tak |
| enableServerCertificateValidation | Określa, czy podczas nawiązywania połączenia z punktem końcowym ma być weryfikowany certyfikat SSL po stronie serwera. | Nie<br /> (wartość domyślna to **true**) |
| authenticationType | Typ uwierzytelniania używany do nawiązywania połączenia z usługą REST. Dozwolone wartości to **Anonymous**, **Basic**, **AadServicePrincipal** i **ManagedServiceIdentity**. Zapoznaj się z odpowiednimi sekcjami poniżej, aby uzyskać więcej właściwości i przykładów. | Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, ta właściwość używa Azure Integration Runtime domyślnego. |Nie |

### <a name="use-basic-authentication"></a>Użyj uwierzytelniania podstawowego

Ustaw właściwość **AuthenticationType** na wartość **podstawowa**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika, która ma być używana w celu uzyskania dostępu do punktu końcowego REST. | Tak |
| password | Hasło użytkownika (wartość **username** ). Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |

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

### <a name="use-aad-service-principal-authentication"></a>Użyj uwierzytelniania podstawowego usługi AAD

Ustaw właściwość **AuthenticationType** na wartość **AadServicePrincipal**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji Azure Active Directory. | Tak |
| servicePrincipalKey | Określ klucz aplikacji Azure Active Directory. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure Portal. | Tak |
| aadResourceId | Określ zasób usługi AAD, którego żądasz, na potrzeby autoryzacji, `https://management.core.windows.net`np.| Tak |

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

### <a name="managed-identity"></a>Korzystanie z tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure

Ustaw właściwość **AuthenticationType** na wartość **ManagedServiceIdentity**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| aadResourceId | Określ zasób usługi AAD, którego żądasz, na potrzeby autoryzacji, `https://management.core.windows.net`np.| Tak |

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

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych REST. 

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z usługi REST, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** zestawu danych musi być ustawiona na wartość **RestResource**. | Tak |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **Get** (default) i **post**. | Nie |
| additionalHeaders | Dodatkowe nagłówki żądań HTTP. | Nie |
| Elemencie requestbody | Treść żądania HTTP. | Nie |
| paginationRules | Zasady dzielenia na strony w celu redagowania żądań kolejnych stron. Szczegółowe informacje znajdują się w sekcji [Obsługa podziału na strony](#pagination-support) . | Nie |

**Przykład 1: Korzystanie z metody get z podziałem na strony**

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

**Przykład 2: Korzystanie z metody post**

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

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło REST.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>RESZTA jako źródło

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **RestSource**. | Tak |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Ta wartość jest przekroczeniem limitu czasu w celu uzyskania odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. Wartość domyślna to **00:01:40**.  | Nie |
| requestInterval | Czas oczekiwania przed wysłaniem żądania na następną stronę. Wartość domyślna to **00:00:01** |  Nie |

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

## <a name="pagination-support"></a>Obsługa stronicowania

Zwykle interfejs API REST ogranicza swój rozmiar ładunku odpowiedzi pojedynczego żądania w ramach rozsądnej liczby; w celu zwrócenia dużej ilości danych dzieli wynik na wiele stron i wymaga, aby wywołujący wysyłali kolejne żądania, aby uzyskać następną stronę wyniku. Zwykle żądanie jednej strony jest dynamiczne i składa się z informacji zwracanych z odpowiedzi poprzedniej strony.

Ten ogólny łącznik REST obsługuje następujące wzorce stronicowania: 

* Bezwzględny lub względny adres URL (wartość właściwości) w bieżącej treści odpowiedzi
* Bezwzględny lub względny adres URL = wartość nagłówka w bieżących nagłówkach odpowiedzi
* Parametr zapytania następnego żądania = wartość właściwości w bieżącej treści odpowiedzi
* Wartość nagłówka kolejnego żądania w nagłówkach bieżącego odpowiedzi
* Nagłówek następnego żądania = wartość właściwości w bieżącej treści odpowiedzi
* Nagłówek następnego żądania = wartość nagłówka w bieżących nagłówkach odpowiedzi

**Reguły stronicowania** są zdefiniowane jako słownik w zestawie danych, który zawiera jedną lub więcej par klucz-wartość z uwzględnieniem wielkości liter. Konfiguracja zostanie użyta do wygenerowania żądania rozpoczynającego się od drugiej strony. Łącznik przestanie iterację, gdy pobiera kod stanu HTTP 204 (brak zawartości) lub którekolwiek wyrażenie wykryto w "paginationRules" zwraca wartość null.

**Obsługiwane klucze** w regułach dzielenia na strony:

| Klucz | Opis |
|:--- |:--- |
| AbsoluteUrl | Wskazuje adres URL, na który ma zostać wystawione następne żądanie. Może to być **bezwzględny adres URL lub względny adres URL**. |
| QueryParameters. *request_query_parameter* LUB QueryParameters [' request_query_parameter '] | "request_query_parameter" jest zdefiniowany przez użytkownika, który odwołuje się do jednej nazwy parametru zapytania w następnym adresie URL żądania HTTP. |
| Nagłówka. *request_header* LUB nagłówki ["request_header"] | "request_header" jest zdefiniowany przez użytkownika, który odwołuje się do jednej nazwy nagłówka w następnym żądaniu HTTP. |

**Obsługiwane wartości** w regułach dzielenia na strony:

| Value | Opis |
|:--- |:--- |
| Nagłówka. *response_header* LUB nagłówki ["response_header"] | "response_header" jest zdefiniowany przez użytkownika, który odwołuje się do jednej nazwy nagłówka w bieżącej odpowiedzi HTTP, wartość, która zostanie użyta do wygenerowania następnego żądania. |
| Wyrażenie wykryto rozpoczynające się od "$" (reprezentujące element główny treści odpowiedzi) | Treść odpowiedzi powinna zawierać tylko jeden obiekt JSON. Wyrażenie wykryto powinno zwracać pojedynczą wartość pierwotną, która zostanie użyta do wygenerowania następnego żądania. |

**Przykład:**

Interfejs API programu Graph w serwisie Facebook zwraca odpowiedź w następującej strukturze, w której przypadku adres URL następnej strony jest reprezentowany w ***stronicowaniu. Next***:

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

Odpowiednia konfiguracja `paginationRules` zestawu danych REST jest następująca:

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

## <a name="export-json-response-as-is"></a>Eksportuj odpowiedź JSON jako-is

Za pomocą tego łącznika REST można wyeksportować odpowiedź JSON interfejsu API REST w taki sam sposób, jak w przypadku różnych magazynów opartych na plikach. Aby uzyskać taką kopię schematu niezależny od, Pomiń sekcję "struktura" (nazywanąrównież schematem) w temacie zestaw danych i schemat w działaniu kopiowania.

## <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z punktu końcowego REST do obiektu sink tabelarycznych, zapoznaj się z [mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).
