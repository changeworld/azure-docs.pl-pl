---
title: Kopiowanie danych z SAP ECC przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z SAP ECC do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 04/26/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: d86264b632daa09a899fae28e73e117b16322617
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128121"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Kopiowanie danych z SAP ECC przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z SAP ECC (składnik centralne SAP Enterprise). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z SAP ECC do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP ECC obsługuje:

- Kopiowanie danych z SAP ECC na oprogramowanie SAP NetWeaver w wersji 7.0 lub nowszym. 
- Kopiowanie danych z obiektów udostępnianych przez usługi SAP ECC OData (np. widoki tabel SAP, BAPI, ekstraktory danych itp.), ani danych/dokumentów Idoc wysłana do SAP PI, odebrania jako obiekt OData za pomocą karty względną.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne

Ogólnie rzecz biorąc SAP ECC udostępnia jednostki za pośrednictwem usługi OData za pośrednictwem bramy SAP. Aby użyć tego łącznika SAP ECC, należy:

- **Konfigurowanie bramy SAP**. W przypadku serwerów z wersją oprogramowania SAP NetWeaver wyższej wersji 7.4 bramy SAP jest już zainstalowana. W przeciwnym razie należy zainstalować poniższe bramy lub bramy hub udostępnianie danych SAP ECC za pośrednictwem usługi OData. Dowiedz się, jak skonfigurować bramę SAP z [Przewodnik instalacji](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktywować i skonfigurować usługę SAP OData**. Usługi OData za pomocą TCODE SICF można aktywować w ciągu kilku sekund. Można również skonfigurować, które obiekty musi być udostępniane. Poniżej znajduje się przykładowy [wskazówki krok po kroku](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Wprowadzenie

Utworzysz potok za pomocą działania kopiowania przy użyciu zestawu .NET SDK, zestaw SDK języka Python, programu Azure PowerShell, interfejsu API REST lub szablonu usługi Azure Resource Manager. Zobacz [samouczka działania kopiowania](quickstart-create-data-factory-dot-net.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika SAP ECC.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku SAP ECC połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapEcc** | Yes |
| url | Adres url usługi SAP ECC OData. | Yes |
| username | Nazwa użytkownika, używany do łączenia z SAP ECC. | Nie |
| password | Hasło zwykłego tekstu, używany do łączenia z SAP ECC. | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP ECC.

Aby skopiować dane z SAP ECC, należy ustawić właściwość typu zestawu danych na **SapEccResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| path | Ścieżka jednostki SAP ECC OData. | Yes |

**Przykład**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC jako źródło

Aby skopiować dane z SAP ECC, należy ustawić typ źródła w działaniu kopiowania, aby **SapEccSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **SapEccSource** | Yes |
| query | Opcje zapytania OData do filtrowania danych. Przykład: "$select = nazwa, opis i $top = 10".<br/><br/>Łącznik SAP ECC kopiuje dane z połączonych adresu URL: (adres url określony w połączonej usłudze) / (ścieżka określona w zestawie danych)? (zapytanie określone w źródle działanie kopiowania). Zapoznaj się [części adresu URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

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

## <a name="data-type-mapping-for-sap-ecc"></a>Mapowanie typu danych dla SAP ECC

Podczas kopiowania danych z SAP ECC, następujące mapowania są używane z typów danych OData dla danych SAP ECC do typów danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych OData | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Edm.Binary | String |
| Edm.Boolean | Bool |
| Edm.Byte | String |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | String |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Złożone typy danych nie są obecnie obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
