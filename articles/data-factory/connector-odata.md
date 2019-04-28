---
title: Kopiowanie danych ze źródła danych OData przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kopiować dane ze źródła danych OData do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: jingwang
ms.openlocfilehash: eeab0311519d7e3410ddb625c1801101ad63ad18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400118"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopiowanie danych ze źródła danych OData przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-odata-connector.md)
> * [Bieżąca wersja](connector-odata.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych ze źródła OData. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Można skopiować danych ze źródła danych OData, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

W szczególności ten łącznik OData obsługuje:

- Protokołu OData w wersji 3.0 i 4.0.
- Kopiowanie danych przy użyciu jednej z następujących uwierzytelnienia: **Anonimowe**, **podstawowe**, **Windows**, **nazwy głównej usługi AAD**, i **zarządzanych tożsamości dla zasobów platformy Azure**.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które służy do definiowania jednostek usługi Data Factory, które są specyficzne dla łącznika usługi OData.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane przez usługę OData połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **OData**. |Yes |
| url | Główny adres URL usługi OData. |Yes |
| Element authenticationType | Typ uwierzytelniania używany do łączenia z źródła OData. Dozwolone wartości to **anonimowe**, **podstawowe**, **Windows**, **AadServicePrincipal**, i **ManagedServiceIdentity** . Użytkownik, na podstawie uwierzytelniania OAuth nie jest obsługiwane. | Yes |
| userName | Określ **userName** uwierzytelnianie Basic lub Windows. | Nie |
| hasło | Określ **hasło** dla użytkownika, konto określone dla **userName**. Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| servicePrincipalId | Określ identyfikator klienta aplikacji usługi Azure Active Directory. | Nie |
| aadServicePrincipalCredentialType | Określanie typu poświadczeń na potrzeby uwierzytelniania jednostki usługi. Dozwolone wartości to: `ServicePrincipalKey` lub `ServicePrincipalCert`. | Nie |
| servicePrincipalKey | Określ klucz aplikacji usługi Azure Active Directory. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| servicePrincipalEmbeddedCert | Określ certyfikat zakodowany w formacie base64 aplikacji zarejestrowanych w usłudze Azure Active Directory. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| servicePrincipalEmbeddedCertPassword | Jeśli certyfikat jest zabezpieczony hasłem, podaj hasło certyfikatu. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md).  | Nie|
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure Portal. | Nie |
| aadResourceId | Określ zasób usługi AAD, który żąda autoryzacji.| Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej) możesz wybrać środowisko IR Azure lub własnego środowiska Integration Runtime. Jeśli nie zostanie określona, używana jest domyślna Azure Integration Runtime. |Nie |

**Przykład 1: Przy użyciu uwierzytelniania anonimowego**

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

**Przykład 2: Przy użyciu uwierzytelniania podstawowego**

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

**Przykład 3: Przy użyciu uwierzytelniania Windows**

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

**Przykład 4: Przy użyciu klucza uwierzytelniania jednostki usługi**

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

**Przykład 5: Przy użyciu uwierzytelniania certyfikatu nazwy głównej usługi**

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

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych OData.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z OData, należy ustawić **typu** właściwości zestawu danych na **ODataResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwości zestawu danych musi być równa **ODataResource**. | Yes |
| ścieżka | Ścieżka do zasobu OData. | Yes |

**Przykład**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
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

Ta sekcja zawiera listę właściwości, które obsługuje źródła OData.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData jako źródło

Aby skopiować dane z OData, należy ustawić **źródła** typ w działaniu kopiowania, aby **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość źródła działania kopiowania musi być równa **RelationalSource**. | Yes |
| query | Opcje zapytania OData do filtrowania danych. Przykład: `"?$select=Name,Description&$top=5"`.<br/><br/>**Uwaga**: Łącznik OData kopiuje dane z połączonych adresu URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Aby uzyskać więcej informacji, zobacz [części adresu URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

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
                "type": "RelationalSource",
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Typ danych mapowania dla protokołu OData

Po skopiowaniu danych na podstawie OData następujące mapowania są używane między typami danych OData i typy danych tymczasowych usługi Azure Data Factory. Aby dowiedzieć się, jak działania kopiowania mapuje typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych OData | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData złożone typy danych (takich jak **obiektu**) nie są obsługiwane.


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md##supported-data-stores-and-formats).