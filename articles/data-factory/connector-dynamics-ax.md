---
title: Kopiowanie danych z systemu Dynamics AX przy użyciu usługi Azure Data Factory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z systemu Dynamics AX do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: 05bd4fdd220b47b11dfed9857dbc8dbe25b236df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61347783"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory-preview"></a>Kopiowanie danych z systemu Dynamics AX przy użyciu usługi Azure Data Factory (wersja zapoznawcza)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych ze źródła Dynamics AX. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z systemu Dynamics AX do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

W szczególności ten łącznik Dynamics AX obsługuje kopiowanie danych z systemu Dynamics AX przy użyciu **protokołu OData** z **uwierzytelniania jednostki usługi**.

>[!TIP]
>Można również użyć tego łącznika do skopiowania danych z **Dynamics 365 Finance and Operations**. Zapoznaj się Dynamics 365 [Obsługa protokołu OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) i [metodę uwierzytelniania](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które służy do definiowania jednostek usługi Data Factory, które są specyficzne dla łącznika Dynamics AX.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki:

1. Zarejestruj jednostki aplikacji w usłudze Azure Active Directory (Azure AD), postępując zgodnie z [Zarejestruj swoją aplikację z dzierżawy usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przejdź do systemu Dynamics AX i udzielić tej usługi głównej odpowiednie uprawnienia dostępu do usługi Dynamics AX.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla systemu Dynamics AX połączoną usługę:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **systemu Dynamics AX**. |Yes |
| url | Dynamics AX (lub Dynamics 365 Finance oraz Operations) wystąpienia punktu końcowego OData. |Yes |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure Portal. | Yes |
| aadResourceId | Określ zasób usługi AAD, który żąda autoryzacji. Na przykład, jeśli jest adres URL usługi Dynamics `https://sampledynamics.sandbox.operations.dynamics.com/data/`, odpowiadający jej zasób usługi AAD jest zazwyczaj `https://sampledynamics.sandbox.operations.dynamics.com`. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej) możesz wybrać środowisko IR Azure lub własnego środowiska Integration Runtime. Jeśli nie zostanie określona, używana jest domyślna Azure Integration Runtime. |Nie |

**Przykład**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych Dynamics AX.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z systemu Dynamics AX, należy ustawić **typu** właściwości zestawu danych na **DynamicsAXResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwości zestawu danych musi być równa **DynamicsAXResource**. | Yes |
| path | Ścieżka do jednostki Dynamics AX OData. | Yes |

**Przykład**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które obsługuje źródła Dynamics AX.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX jako źródło

Aby skopiować dane z systemu Dynamics AX, należy ustawić **źródła** typ w działaniu kopiowania, aby **DynamicsAXSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość źródła działania kopiowania musi być równa **DynamicsAXSource**. | Yes |
| query | Opcje zapytania OData do filtrowania danych. Przykład: `"?$select=Name,Description&$top=5"`.<br/><br/>**Uwaga**: Łącznik kopiuje dane z połączonych adresu URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Aby uzyskać więcej informacji, zobacz [części adresu URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md##supported-data-stores-and-formats).
