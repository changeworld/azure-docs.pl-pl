---
title: Kopiowanie danych z programu SAP ECC przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z programu SAP ECC do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 04/26/2018
ms.author: jingwang
ms.openlocfilehash: 95b0440dbec1a13096928bdefd31383d07a1f86d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618576"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Kopiowanie danych z programu SAP ECC przy użyciu fabryki danych Azure

Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure można skopiować danych z programu SAP ECC (SAP składnikiem przedsiębiorstwa). Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [działanie kopiowania w wersji 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z programu SAP ECC żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP ECC obsługuje:

- Kopiowanie danych z programu SAP ECC na SAP NetWeaver w wersji 7.0 lub nowszym. 
- Kopiowanie danych z wszystkich obiektów udostępnianych przez usługi SAP ECC OData (np. widoków tabel SAP, BAPI, ekstraktory danych itp.) lub wysyłane do PI SAP, otrzymania jako OData przy użyciu kart względną danych/Idoc.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne

Ogólnie rzecz biorąc SAP ECC przedstawia jednostek za pośrednictwem usługi OData za pośrednictwem bramy SAP. Aby użyć tego łącznika SAP ECC, musisz:

- **Skonfiguruj bramę SAP**. W przypadku serwerów z programem SAP NetWeaver wyższą wersję niż 7.4 bramą SAP jest już zainstalowana. W przeciwnym razie należy zainstalować poniższe bramy lub bramy Centrum przed ujawnieniem danych SAP ECC za pośrednictwem usług OData. Dowiedz się, jak skonfigurować bramę SAP [Przewodnik instalacji](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktywowanie i konfigurowanie usługi SAP OData**. Można aktywować usługi OData za pomocą TCODE SICF w sekundach. Można również skonfigurować, które obiekty musi być widoczne. Poniżej przedstawiono przykładowe [wskazówki krok po kroku](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Wprowadzenie

Można utworzyć potoku o aktywności kopiowania przy użyciu zestawu .NET SDK, zestaw SDK Python, programu Azure PowerShell, interfejsu API REST lub szablonu usługi Azure Resource Manager. Zobacz [samouczek działania kopiowania](quickstart-create-data-factory-dot-net.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika SAP ECC.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla programu SAP ECC połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **SapEcc** | Yes |
| url | Adres url usługi SAP ECC OData. | Yes |
| nazwa użytkownika | Nazwa użytkownika używana do łączenia się SAP ECC. | Nie |
| hasło | Hasło w postaci zwykłego tekstu używany do łączenia z SAP ECC. | Nie |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP ECC.

Aby skopiować dane z programu SAP ECC, ustaw właściwość Typ zestawu danych do **SapEccResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| ścieżka | Ścieżka obiektu SAP ECC OData. | Yes |

**Przykład**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC jako źródło

Aby skopiować dane z programu SAP ECC, należy ustawić typ źródła w przypadku działania kopiowania do **SapEccSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **SapEccSource** | Yes |
| query | Opcje zapytania OData do filtrowania danych. Przykład: "$select = nazwa, opis i $top = 10".<br/><br/>Łącznik SAP ECC kopiuje dane z połączonego adresu URL: (adres url określony w połączonej usłudze) / (określona ścieżka w zestawie danych)? (zapytanie określone w źródle działania kopiowania). Zapoznaj się [składniki adresu URL OData](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>Mapowanie typu danych dla programu SAP ECC

Podczas kopiowania danych z programu SAP ECC, następujące mapowania są używane z typów danych OData dla danych SAP ECC do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych OData | Typ danych tymczasowych fabryki danych |
|:--- |:--- |:--- |
| Edm.Binary | Ciąg |
| Edm.Boolean | wartość logiczna |
| Edm.Byte | Ciąg |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | O podwójnej precyzji |
| Edm.Single | Pojedyncze |
| Edm.Guid | Ciąg |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Ciąg |
| Edm.Time | Zakres czasu |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Złożone typy danych nie są obecnie obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
