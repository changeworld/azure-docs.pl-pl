---
title: Kopiowanie danych ze źródeł dostępu Microsoft Access przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z źródeł dostępu do obsługiwanych magazynów danych z programu Microsoft Access w ramach programu w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: jingwang
ms.openlocfilehash: e587621cd420fab564ec4fa09ea131d0ff16fcdd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680692"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Kopiowanie danych z i do magazynów danych programu Microsoft Access przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z magazynu danych programu Microsoft Access. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik programu Microsoft Access jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z programu Microsoft Access source można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika programu Microsoft Access, należy wykonać następujące:

- Skonfiguruj samodzielny Integration Runtime. Aby uzyskać szczegółowe informacje, zobacz artykuł [Integration Runtime samodzielny](create-self-hosted-integration-runtime.md) .
- Zainstaluj sterownik Microsoft Access ODBC dla magazynu danych na maszynie Integration Runtime.

>[!NOTE]
>Wersja Microsoft Access 2016 sterownika ODBC nie współpracuje z tym łącznikiem. Zamiast tego użyj sterownika w wersji 2013 lub 2010.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Microsoft Access Connector.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi Microsoft Access:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **MicrosoftAccess** | Tak |
| Przekształcon | Parametry połączenia ODBC z wyjątkiem części poświadczenia. Można określić parametry połączenia lub użyć systemu DSN (nazwa źródła danych) skonfigurowanego na maszynie Integration Runtime (w związku z tym nadal należy określić część poświadczeń w połączonej usłudze).<br>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md).| Tak |
| authenticationType | Typ uwierzytelniania używany do nawiązywania połączenia z magazynem danych programu Microsoft Access.<br/>Dozwolone wartości to: **podstawowe** i **anonimowe**. | Tak |
| Uż | Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania podstawowego. | Nie |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| poświadczenia | Część poświadczeń dostępu do parametrów połączenia określona w formacie wartości właściwości specyficznej dla sterownika. Oznacz to pole jako element SecureString. | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Tak |

**Przykład:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
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

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Microsoft Access.

Aby skopiować dane z programu Microsoft Access, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **MicrosoftAccessTable** | Tak |
| tableName | Nazwa tabeli w programie Microsoft Access. | Nie dla źródła (Jeśli określono "zapytanie" w źródle aktywności);<br/>Tak dla ujścia |

**Przykład**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez program Microsoft Access source.

### <a name="microsoft-access-as-source"></a>Microsoft Access jako źródło

Aby skopiować dane z magazynu danych zgodnego z programem Microsoft Access, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **MicrosoftAccessSource** | Tak |
| query | Użyj zapytania niestandardowego do odczytywania danych. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
