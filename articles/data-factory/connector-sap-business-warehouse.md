---
title: Kopiowanie danych z systemu SAP BW przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z rozwiązania SAP Business Warehouse do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: jingwang
ms.openlocfilehash: 9a0abcd70b4aeb2369604bafa924136122206e0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60309117"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopiowanie danych z rozwiązania SAP Business Warehouse przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Bieżąca wersja](connector-sap-business-warehouse.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z SAP Business Warehouse (BW). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Można kopiować dane z rozwiązania SAP Business Warehouse, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP Business Warehouse obsługuje:

- SAP Business Warehouse **wersji 7.x**.
- Kopiowanie danych z **informacji (infocube) i QueryCubes** (w tym BEx zapytania) przy użyciu zapytań MDX.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP Business Warehouse, należy:

- Skonfiguruj środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.
- Zainstaluj **biblioteki SAP NetWeaver** na komputerze środowisko Integration Runtime. Można uzyskać biblioteki SAP Netweaver, od administratora SAP lub bezpośrednio z [SAP Software Download Center](https://support.sap.com/swdc). Wyszukaj **SAP Note #1025361** można pobrać lokalizacji pobierania dla najnowszej wersji. Upewnij się, że wybierasz **64-bitowych** biblioteki SAP NetWeaver, która jest zgodna z instalacją środowiska Integration Runtime. Następnie zainstaluj wszystkie pliki zawarte w zestawie SDK SAP NetWeaver RFC zgodnie z SAP Note. Biblioteka oprogramowania SAP NetWeaver znajduje się również w instalacji narzędzi klienckich SAP.

>[!TIP]
>Aby rozwiązać problem z łącznością SAP BW, upewnij się, że:
>- Obowiązują wszystkie biblioteki zależności wyodrębnione z zestawu SDK RFC NetWeaver w folderze %windir%\system32. Zwykle ma on icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, pliku librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Wymagane porty używane do łączenia się z serwerem SAP są włączone na komputerze własne środowisko IR, które są zazwyczaj port 3300 i 3201.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika SAP Business Warehouse.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane przez oprogramowanie SAP Business Warehouse (BW), połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapBw** | Yes |
| server | Nazwa serwera, na którym znajduje się wystąpienie systemu SAP BW. | Yes |
| systemNumber | Numer systemu systemu SAP BW.<br/>Dozwolone wartości: liczba dziesiętna dwucyfrowy reprezentowane jako ciąg. | Yes |
| clientId | Identyfikator klienta klienta w SAP W systemie.<br/>Dozwolone wartości: liczba dziesiętna trzy cyfry, reprezentowane jako ciąg. | Yes |
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Yes |
| password | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Yes |

**Przykład:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP BW.

Aby skopiować dane z systemu SAP BW, należy ustawić właściwość typu zestawu danych na **RelationalTable**. Gdy nie ma żadnych właściwości specyficzne dla typu obsługiwane w przypadku zestawu danych SAP BW wpisz RelationalTable.

**Przykład:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez system SAP BW źródło.

### <a name="sap-bw-as-source"></a>System SAP BW jako źródło

Aby skopiować dane z systemu SAP BW, należy ustawić typ źródła w działaniu kopiowania, aby **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **RelationalSource** | Yes |
| query | Określa zapytanie MDX, które można odczytać danych z wystąpienia systemu SAP BW. | Yes |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Mapowanie typu danych dla programu SAP BW

Podczas kopiowania danych z systemu SAP BW, następujące mapowania są używane do typów danych tymczasowych usługi Azure Data Factory z typów danych SAP BW. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych SAP BW | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| UNIT | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
