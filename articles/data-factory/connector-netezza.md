---
title: Kopiowanie danych z Netezza przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Netezza do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: f8c10e2200f830ea6e568e7b3fba1f0a6085cef2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059070"
---
# <a name="copy-data-from-netezza-using-azure-data-factory"></a>Kopiowanie danych z Netezza przy użyciu fabryki danych Azure 

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z Netezza. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z Netezza żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Fabryka danych Azure oferuje wbudowane sterowników, aby umożliwić łączność, w związku z tym nie trzeba ręcznie zainstalowania sterownika korzystania z tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

Można utworzyć potoku o aktywności kopiowania przy użyciu zestawu .NET SDK, zestaw SDK Python, programu Azure PowerShell, interfejsu API REST lub szablonu usługi Azure Resource Manager. Zobacz [samouczek działania kopiowania](quickstart-create-data-factory-dot-net.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do Netezza łącznika.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Netezza połączone usługi, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **Netezza** | Yes |
| Parametry połączenia | Parametry połączenia ODBC do nawiązania połączenia Netezza. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

Ciąg połączenia typowe jest `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Więcej właściwości, które można ustawić dla tej sprawy:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |:--- |
| SecurityLevel | Poziom zabezpieczeń (SSL/TLS) używanego przez sterownik do połączenia z magazynem danych. Na przykład `SecurityLevel=preferredSecured`. Obsługiwane są następujące wartości:<br/>-Tylko niezabezpieczone (**onlyUnSecured**): sterownik nie korzysta z protokołu SSL.<br/>- **Preferowany Unsecured (preferredUnSecured) (ustawienie domyślne)**: Jeśli serwer umożliwia wybranie, sterownik nie korzysta z protokołu SSL. <br/>- **Preferowany bezpieczne (preferredSecured)**: Jeśli serwer udostępnia wybór, sterownik używa protokołu SSL. <br/>- **Tylko bezpieczne (onlySecured)**: sterownik nie połączyć, chyba że dostępne są połączenia SSL | Nie |
| PlikCertyfikatuUrzędu | Pełna ścieżka do certyfikatu SSL, który jest używany przez serwer. Na przykład `CaCertFile=<cert path>;`| Tak, jeśli jest włączony protokół SSL |

**Przykład:**

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

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez zestaw danych Netezza właściwości.

Aby skopiować dane z Netezza, ustaw właściwość Typ zestawu danych do **NetezzaTable**. Nie ma dodatkowych właściwości określonego typu w tego typu dataset.

**Przykład**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez źródło Netezza właściwości.

### <a name="netezza-as-source"></a>Netezza jako źródło

Aby skopiować dane z Netezza, należy ustawić typ źródła w przypadku działania kopiowania do **NetezzaSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **NetezzaSource** | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Na przykład: `"SELECT * FROM MyTable"`. | Yes |

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
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
