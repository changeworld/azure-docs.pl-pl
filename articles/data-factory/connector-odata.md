---
title: Kopiowanie danych ze źródeł OData przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych ze źródeł OData do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: e70cd6d7745b2754bdda6992c587c5c72643869b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912505"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopiowanie danych ze źródła strumieniowego OData przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-odata-connector.md)
> * [Bieżąca wersja](connector-odata.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych ze źródła strumieniowego OData. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik OData jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Możesz skopiować dane ze źródła danych OData do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

W przypadku tego łącznika OData obsługuje:

- OData w wersji 3,0 i 4,0.
- Kopiowanie danych przy użyciu jednego z następujących uwierzytelnień: **anonimowe**, **podstawowe**, **Windows**, nazwa **główna usługi AAD**i **zarządzane tożsamości dla zasobów platformy Azure**.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpocznij

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania jednostek Data Factory, które są specyficzne dla łącznika OData.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi OData są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** musi być ustawiona na wartość **OData**. |Tak |
| url | Główny adres URL usługi OData. |Tak |
| authenticationType | Typ uwierzytelniania używany do nawiązywania połączenia ze źródłem danych OData. Dozwolone wartości to **Anonymous**, **Basic**, **Windows**, **AadServicePrincipal**i **ManagedServiceIdentity**. Uwierzytelnianie OAuth oparte na użytkowniku nie jest obsługiwane. | Tak |
| userName | Określ **nazwę użytkownika** , jeśli używasz uwierzytelniania podstawowego lub systemu Windows. | Nie |
| hasło | Określ **password** dla użytkownika, konto określone dla **userName**. Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Można również [odwołać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| servicePrincipalId | Określ identyfikator klienta aplikacji Azure Active Directory. | Nie |
| aadServicePrincipalCredentialType | Określ typ poświadczeń, który ma być używany do uwierzytelniania nazwy głównej usługi. Dozwolone wartości to: `ServicePrincipalKey` lub `ServicePrincipalCert`. | Nie |
| servicePrincipalKey | Określ klucz aplikacji Azure Active Directory. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| servicePrincipalEmbeddedCert | Określ certyfikat kodowany algorytmem Base64 aplikacji zarejestrowanej w Azure Active Directory. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| servicePrincipalEmbeddedCertPassword | Określ hasło certyfikatu, jeśli certyfikat jest zabezpieczony hasłem. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md).  | Nie|
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure Portal. | Nie |
| aadResourceId | Określ zasób usługi AAD, którego żądasz do autoryzacji.| Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określona, używana jest domyślna Azure Integration Runtime. |Nie |

**Przykład 1: korzystanie z uwierzytelniania anonimowego**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: używanie uwierzytelniania podstawowego**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
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

**Przykład 3: używanie uwierzytelniania systemu Windows**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**Przykład 4: używanie uwierzytelniania klucza jednostki usługi**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Przykład 5: używanie uwierzytelniania certyfikatu jednostki usługi**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych OData.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z protokołu OData, ustaw właściwość **Type** zestawu danych na **ODataResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** zestawu danych musi być ustawiona na wartość **ODataResource**. | Tak |
| Ścieżka | Ścieżka do zasobu OData. | Tak |

**Przykład**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło OData.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData jako źródło

Aby skopiować dane z usługi OData, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **ODataSource**. | Tak |
| query | Opcje zapytania OData dotyczące filtrowania danych. Przykład: `"$select=Name,Description&$top=5"`.<br/><br/>**Uwaga**: Łącznik OData kopiuje dane ze połączonego adresu URL: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`. Aby uzyskać więcej informacji, zobacz [składniki URL usługi OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz źródła z wpisaną `RelationalSource`, jest ono nadal obsługiwane w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="data-type-mapping-for-odata"></a>Mapowanie typu danych dla usługi OData

Podczas kopiowania danych z protokołu OData następujące mapowania są używane między typami danych OData i Azure Data Factory pośrednimi typami danych. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych OData | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Data i godzina |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Pojedyncze |
| Edm.Guid | Identyfikator GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Ciąg |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Złożone typy danych OData (takie jak **Object**) nie są obsługiwane.


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md##supported-data-stores-and-formats).