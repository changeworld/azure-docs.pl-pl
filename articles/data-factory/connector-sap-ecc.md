---
title: Kopiowanie danych z SAP ECC za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z SAP ECC do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827767"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopiowanie danych z SAP ECC za pomocą usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z SAP Enterprise centralnej składnika (ECC). Aby uzyskać więcej informacji, zobacz [omówienie działania kopiowania](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z SAP ECC do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP ECC obsługuje:

- Kopiowanie danych z SAP ECC na oprogramowanie SAP NetWeaver w wersji 7.0 i nowszych.
- Kopiowanie danych z żadnych obiektów udostępnianych przez usługi SAP ECC OData, takich jak:

  - SAP tabel lub widoków.
  - Obiekty [BAPI] interfejsu programowania aplikacji biznesowych.
  - Ekstraktory danych.
  - Dane lub dokumenty pośredni (dokumentów Idoc) wysłana do SAP integracji procesu (PI), który mogą odebrać jako obiekt OData za pomocą karty względną.

- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

>[!TIP]
>Aby skopiować dane z SAP ECC za pośrednictwem SAP tabeli lub widoku, należy użyć [tabeli SAP](connector-sap-table.md) łącznik, który jest szybsze i bardziej skalowalne.

## <a name="prerequisites"></a>Wymagania wstępne

Ogólnie rzecz biorąc SAP ECC udostępnia jednostki za pośrednictwem usługi OData za pośrednictwem bramy SAP. Aby użyć tego łącznika SAP ECC, należy:

- **Konfigurowanie bramy SAP**. W przypadku serwerów z wersjami oprogramowania SAP NetWeaver później niż w wersji 7.4 bramy SAP jest już zainstalowana. We wcześniejszych wersjach należy zainstalować osadzone bramy SAP lub system Centrum bramy SAP przed udostępnianie danych SAP ECC za pośrednictwem usługi OData. Aby skonfigurować bramę SAP, zobacz [Przewodnik instalacji](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktywować i skonfigurować usługę SAP OData**. Należy aktywować usługę OData za pomocą TCODE SICF w ciągu kilku sekund. Można również skonfigurować, obiekty, które muszą być udostępniane. Aby uzyskać więcej informacji, zobacz [wskazówki krok po kroku](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika SAP ECC.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi SAP ECC połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| `type` | `type` Właściwość musi być równa `SapEcc`. | Tak |
| `url` | Adres URL usługi SAP ECC OData. | Yes |
| `username` | Nazwa użytkownika, używany do łączenia z SAP ECC. | Nie |
| `password` | Hasło zwykłego tekstu, używany do łączenia z SAP ECC. | Nie |
| `connectVia` | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli magazyn danych jest publicznie dostępny), można użyć własnego środowiska integration runtime lub Azure integration runtime. Jeśli nie określisz środowiska uruchomieniowego, `connectVia` korzysta z domyślnego środowiska uruchomieniowego integracji platformy Azure. | Nie |

### <a name="example"></a>Przykład

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP ECC.

Aby skopiować dane z SAP ECC, należy ustawić `type` właściwości zestawu danych na `SapEccResource`.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| `path` | Ścieżka jednostki SAP ECC OData. | Tak |

### <a name="example"></a>Przykład

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez źródło SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC jako źródło

Aby skopiować dane z SAP ECC, należy ustawić `type` właściwość `source` sekcji działanie kopiowania w celu `SapEccSource`.

Następujące właściwości są obsługiwane w działaniu kopiowania `source` sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| `type` | `type` Właściwości działania kopiowania `source` sekcji musi być równa `SapEccSource`. | Yes |
| `query` | Opcje zapytania OData do filtrowania danych. Na przykład:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Łącznik SAP ECC kopiuje dane z połączonych adresu URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Aby uzyskać więcej informacji, zobacz [części adresu URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

### <a name="example"></a>Przykład

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

## <a name="data-type-mappings-for-sap-ecc"></a>Mapowanie typu danych dla SAP ECC

Przy kopiowaniu danych z SAP ECC, następujące mapowania są używane z typów danych OData dla danych SAP ECC do typów danych tymczasowych usługi Azure Data Factory. Aby dowiedzieć się, jak działania kopiowania mapuje typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych OData | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Złożone typy danych nie są obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
