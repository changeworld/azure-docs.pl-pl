---
title: Kopiowanie danych z bazy danych platformy Azure dla PostgreSQL przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z bazy danych platformy Azure dla PostgreSQL do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 04/28/2018
ms.author: jingwang
ms.openlocfilehash: 27b975df5d0161f0b64e02b62c89989a670731aa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330564"
---
# <a name="copy-data-from-azure-database-for-postgresql-using-azure-data-factory"></a>Kopiowanie danych z bazy danych platformy Azure dla PostgreSQL przy użyciu fabryki danych Azure 

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z bazy danych Azure dla PostgreSQL. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [działanie kopiowania w wersji 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych Azure dla PostgreSQL żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Fabryka danych Azure oferuje wbudowane sterowników, aby umożliwić łączność, w związku z tym nie trzeba ręcznie zainstalowania sterownika korzystania z tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do bazy danych Azure PostgreSQL łącznika.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Bazy danych Azure PostgreSQL połączone usługi, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **AzurePostgreSql** | Yes |
| Parametry połączenia | Parametry połączenia ODBC do łączenia z bazą danych Azure dla PostgreSQL. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

Ciąg połączenia typowe jest `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Więcej właściwości, które można ustawić dla tej sprawy:

| Właściwość | Opis | Opcje | Wymagane |
|:--- |:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda sterownik używa do szyfrowania danych przesyłanych między sterownika i serwer bazy danych. Na przykład `ValidateServerCertificate=<0/1/6>;`| 0 (brak szyfrowania) **(domyślna)** / 1 (SSL) / 6 (RequestSSL) | Nie |
| ValidateServerCertificate (wirtualnej karty Inteligentnej) | Określa, czy sterownik weryfikuje certyfikat, który jest wysyłany przez serwer bazy danych, jeśli jest włączone szyfrowanie SSL (metoda szyfrowania = 1). Na przykład `ValidateServerCertificate=<0/1>;`| 0 (wyłączone) **(domyślna)** / 1 (włączone) | Nie |

**Przykład:**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez bazę danych Azure dla zestawu danych PostgreSQL właściwości.

Aby skopiować dane z bazy danych Azure dla PostgreSQL, ustaw właściwość Typ zestawu danych do **AzurePostgreSqlTable**. Nie ma dodatkowych właściwości określonego typu w tego typu dataset.

**Przykład**

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez bazę danych Azure PostgreSQL źródła właściwości.

### <a name="azurepostgresqlsource-as-source"></a>AzurePostgreSqlSource jako źródło

Aby skopiować dane z bazy danych Azure dla PostgreSQL, należy ustawić typ źródła w przypadku działania kopiowania do **AzurePostgreSqlSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **AzurePostgreSqlSource** | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Na przykład: `"SELECT * FROM MyTable"`. | Yes |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
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
