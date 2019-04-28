---
title: Kopiowanie danych z Netezza za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Netezza do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 02/01/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 9bf90c9d3ce593ba5bf6339cd9cec31bb49f14f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399931"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopiowanie danych z Netezza za pomocą usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych ze Netezza. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z Netezza do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

Usługa Azure Data Factory udostępnia wbudowane sterowników, aby włączyć łączność. Nie trzeba ręcznie zainstalować dowolnego sterownika, aby użyć tego łącznika.

## <a name="get-started"></a>Rozpoczęcie pracy

Można utworzyć potoku, który używa działania kopiowania przy użyciu zestawu .NET SDK, zestaw SDK języka Python, programu Azure PowerShell, interfejsu API REST lub szablonu usługi Azure Resource Manager. Zobacz [samouczka działania kopiowania](quickstart-create-data-factory-dot-net.md) instrukcje krok po kroku dotyczące sposobu tworzenia potoku, który zawiera działania kopiowania.

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które służy do definiowania jednostek usługi Data Factory, które są specyficzne dla łącznika Netezza.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Usługi połączone Netezza obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **Netezza**. | Yes |
| Parametry połączenia | Ciąg połączenia ODBC, aby nawiązać połączenie Netezza. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Można również wprowadzić hasło w usłudze Azure Key Vault i ściągania `pwd` konfiguracji poza parametry połączenia. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. Możesz samodzielnie hostowanego produktu Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określona, używana jest domyślna Azure Integration Runtime. |Nie |

Typowe parametry połączenia jest `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. W poniższej tabeli opisano więcej właściwości, które można ustawić:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| SecurityLevel | Poziom zabezpieczeń (SSL/TLS), sterownik używany dla połączenia z magazynem danych. Przykład: `SecurityLevel=preferredSecured`. Obsługiwane są następujące wartości:<br/>- **Tylko niezabezpieczone** (**onlyUnSecured**): Sterownik nie używa protokołu SSL.<br/>- **Preferowane niezabezpieczone (preferredUnSecured) (ustawienie domyślne)**: Jeśli serwer umożliwia wybranie, sterownik nie używa protokołu SSL. <br/>- **Preferowane zabezpieczone (preferredSecured)**: Jeśli serwer umożliwia wybranie, sterownik używa protokołu SSL. <br/>- **Tylko zabezpieczane (onlySecured)**: Sterownik nie połączyć, o ile nie jest dostępne połączenie SSL. | Nie |
| PlikCertyfikatuUrzędu | Pełna ścieżka do certyfikatu SSL, który jest używany przez serwer. Przykład: `CaCertFile=<cert path>;`| Tak, jeśli jest włączony protokół SSL |

**Przykład**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przechowywanie haseł w usłudze Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych Netezza.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md).

Aby skopiować dane z Netezza, ustaw **typu** właściwości zestawu danych na **NetezzaTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **NetezzaTable** | Yes |
| tableName | Nazwa tabeli. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które obsługuje źródła Netezza.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza jako źródło

Aby skopiować dane z Netezza, ustaw **źródła** typ w działaniu kopiowania, aby **NetezzaSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość źródła działania kopiowania musi być równa **NetezzaSource**. | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Przykład: `"SELECT * FROM MyTable"` | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
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
