---
title: Kopiowanie danych do/z bazy danych rozwiązania Cosmos Azure przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanej źródłowej baz danych do bazy danych Azure rozwiązania Cosmos (lub) z bazy danych rozwiązania Cosmos sklepów zbiornika obsługiwane przy użyciu fabryki danych.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
ms.openlocfilehash: 92b45c1038fd099926360dc80802ababf0e8ee93
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052770"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Kopiowanie danych do lub z bazy danych rozwiązania Cosmos Azure przy użyciu fabryki danych Azure

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-azure-documentdb-connector.md)
> * [Bieżąca wersja](connector-azure-cosmos-db.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z i do bazy danych Azure rozwiązania Cosmos (interfejsu API SQL). Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych Azure rozwiązania Cosmos do żadnych obsługiwanych ujścia magazynu danych lub skopiować dane z dowolnego źródła obsługiwanych magazynu danych do bazy danych Azure rozwiązania Cosmos. Lista magazynów danych obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Azure DB rozwiązania Cosmos obsługuje:

- Rozwiązania cosmos DB [interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Importowania/eksportowania dokumentów JSON jako — jest lub kopiowanie danych z i do zestawu danych tabelarycznych np. Baza danych SQL, pliki CSV itp.

Aby skopiować dokumenty jako — jest do/z pliki w formacie JSON lub innej kolekcji rozwiązania Cosmos bazy danych, zobacz [dokumentów JSON importu/eksportu](#importexport-json-documents).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do bazy danych Azure rozwiązania Cosmos.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla usługi Azure DB rozwiązania Cosmos połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **CosmosDb**. | Yes |
| Parametry połączenia |Określ informacje potrzebne do łączenia z bazą danych Azure DB rozwiązania Cosmos. Należy zauważyć, że trzeba określić informacje o bazie danych w parametrach połączenia jako poniżej próbki. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych z bazy danych Azure rozwiązania Cosmos.

Aby skopiować dane z/do bazy danych Azure rozwiązania Cosmos, ustaw właściwość Typ zestawu danych do **DocumentDbCollection**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **DocumentDbCollection** |Yes |
| collectionName |Nazwa kolekcji dokumentów DB rozwiązania Cosmos. |Yes |

**Przykład:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schemat fabryka danych

Dla magazynów danych bez schematu, takie jak bazy danych Azure rozwiązania Cosmos działanie kopiowania wnioskuje schemat w jednym z następujących sposobów. W związku z tym chyba że chcesz [importu/eksportu dokumentów JSON jako — jest](#importexport-json-documents), najlepszym rozwiązaniem jest zdefiniowanie struktury danych w **struktury** sekcji.

*. Jeśli określisz struktury danych za pomocą **struktury** tej struktury Schema honoruje właściwości w definicji zestawu danych, usługi fabryka danych. W takim przypadku wiersza nie zawiera wartości dla kolumny, będzie należy podać dla niego wartość null.
*. Jeśli nie określisz struktury danych za pomocą **struktury** właściwości w definicji zestawu danych, usługi fabryka danych z wnioskuje schemat za pomocą pierwszego wiersza w danych. W takim przypadku jeśli pierwszy wiersz zawiera pełną schematu, niektóre kolumny będą niedostępne w wyniku operacji kopiowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez bazy danych Azure rozwiązania Cosmos źródłowy i odbiorczy właściwości.

### <a name="azure-cosmos-db-as-source"></a>Azure DB rozwiązania Cosmos jako źródło

Aby skopiować dane z bazy danych rozwiązania Cosmos platformy Azure, należy ustawić typ źródła w przypadku działania kopiowania do **DocumentDbCollectionSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **DocumentDbCollectionSource** |Yes |
| query |Określ zapytanie DB rozwiązania Cosmos można odczytać danych.<br/><br/>Przykład: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, która zostanie wykonana instrukcja SQL: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znaki specjalne w celu wskazania, że dokument jest zagnieżdżony i jak do flattern zestaw wyników.<br/><br/>Na przykład, jeśli zapytanie DB rozwiązania Cosmos zwraca wynik zagnieżdżonych `"Name": {"First": "John"}`, działanie kopiowania określi nazwy kolumny jako "Name.First" o wartości "Jan" po nestedSeparator kropki. |Nie (domyślnie jest kropka `.`) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
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
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure DB rozwiązania Cosmos jako odbioru

Aby skopiować dane do bazy danych rozwiązania Cosmos Azure, należy ustawić typ ujścia w działaniu kopiowania do **DocumentDbCollectionSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania: **DocumentDbCollectionSink** |Yes |
| nestingSeparator |Wymagany jest znak specjalny w nazwa kolumny źródłowej, aby wskazać zagnieżdżonych dokumentu. <br/><br/>Na przykład `Name.First` w zestawie danych wyjściowych struktury generuje następującą strukturę JSON w dokumencie DB rozwiązania Cosmos:`"Name": {"First": "[value maps to this column from source]"}` gdy nestedSeparator jest kropka. |Nie (domyślnie jest kropka `.`) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Dokumentów JSON Import/Eksport

Korzystając z tego łącznika DB rozwiązania Cosmos, można łatwo

* Zaimportuj dokumentów JSON z różnych źródeł do rozwiązania Cosmos bazy danych, w tym obiektów Blob platformy Azure, Azure Data Lake Store i innych magazynach opartych na plikach obsługiwane przez usługi fabryka danych Azure.
* Wyeksportuj dokumentów JSON z collecton rozwiązania Cosmos bazy danych do różnych magazynów opartych na plikach.
* Skopiuj dokumentów między dwie kolekcje DB rozwiązania Cosmos w postaci — jest.

Do osiągnięcia tych kopii niezależny od schematu:

* Gdy narzędzie kopii danych i sprawdź **"wyeksportować w postaci-ma pliki w formacie JSON lub kolekcji rozwiązania Cosmos bazy danych"** opcji.
* Podczas tworzenia działania nie określać w sekcji "structure" (alias schemat) w opublikowanych DB rozwiązania Cosmos ani właściwości "nestingSeparator" dla bazy danych rozwiązania Cosmos źródło/ujście w przypadku działania kopiowania. Podczas importowania z / eksportowanie do pliki JSON w odpowiedniego pliku magazynu zestawu danych, określ typ formatu jako "JsonFormat" i konfiguracji "filePattern" prawidłowo (zobacz [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format) sekcji, aby uzyskać szczegółowe informacje), następnie należy określać struktury" "(alias schemat) sekcji i Pomiń pozostałe ustawienia formatu.

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
