---
title: Kopiowanie danych z programu SAP BW przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z programu SAP Business Warehouse do zbiornika obsługiwanych magazynów danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: f5af46017dede0deac92ff5a7f35f17e083590d6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618610"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopiowanie danych z programu SAP Business Warehouse przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-sap-business-warehouse-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-sap-business-warehouse.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z programu SAP Business Warehouse (BW). Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [łącznika programu SAP BW w wersji 1](v1/data-factory-sap-business-warehouse-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z programu SAP Business Warehouse żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP Business Warehouse obsługuje:

- SAP Business Warehouse **wersja 7.x**.
- Kopiowanie danych z **InfoCubes i QueryCubes** (łącznie z BEx zapytań) przy użyciu zapytania MDX.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP Business Warehouse, musisz:

- Konfigurowanie środowiska uruchomieniowego integracji Self-hosted. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Zainstaluj **biblioteki SAP NetWeaver** na komputerze środowiska uruchomieniowego integracji. Biblioteka SAP Netweaver można uzyskać od administratora SAP, lub bezpośrednio z [Centrum pobierania oprogramowania SAP](https://support.sap.com/swdc). Wyszukaj **1025361 # Uwaga SAP** można pobrać lokalizację pobierania najnowszej wersji. Upewnij się, że wybrano **64-bitowe** SAP NetWeaver biblioteki, która odpowiada instalacji środowiska uruchomieniowego integracji. Następnie zainstaluj wszystkie pliki zawarte w zestawie SDK programu SAP NetWeaver RFC zgodnie z Uwaga SAP. Biblioteka programu SAP NetWeaver znajduje się również w narzędziach klienckich SAP instalacji.

> [!TIP]
> Umieść wyodrębniony z zestawu SDK RFC NetWeaver w folderze system32 bibliotek DLL.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do SAP Business Warehouse łącznika.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla programu SAP Business magazynu (BW) połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **SapBw** | Yes |
| serwer | Nazwa serwera, na którym znajduje się wystąpienie programu SAP BW. | Yes |
| systemNumber | Numer systemu systemu SAP BW.<br/>Dozwolone wartości: dwucyfrowe liczbę dziesiętną reprezentowany jako ciąg. | Yes |
| clientId | Identyfikator klienta w systemie SAP W klienta.<br/>Dozwolone wartości: trzycyfrowa liczba dziesiętna reprezentowany jako ciąg. | Yes |
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Yes |
| hasło | Hasło dla użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Środowisko uruchomieniowe integracji Self-hosted jest wymagana, jak wspomniano w [wymagania wstępne](#prerequisites). |Yes |

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych z programu SAP BW.

Aby skopiować dane z programu SAP BW, ustaw właściwość Typ zestawu danych do **RelationalTable**. Wpisz RelationalTable, gdy nie ma żadnych właściwości specyficzne dla typu obsługiwane dla programu SAP BW zestawu danych.

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

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło programu SAP BW.

### <a name="sap-bw-as-source"></a>Programu SAP BW jako źródło

Aby skopiować dane z programu SAP BW, należy ustawić typ źródła w przypadku działania kopiowania do **RelationalSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **RelationalSource** | Yes |
| query | Określa zapytanie MDX, które można odczytać danych z wystąpienia programu SAP BW. | Yes |

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

Podczas kopiowania danych z programu SAP BW, następujące mapowania są używane z programu SAP BW typów danych do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych SAP BW | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| ACCP | Int |
| CHAR | Ciąg |
| CLNT | Ciąg |
| CURR | Decimal |
| CUKY | Ciąg |
| DEC | Decimal |
| FLTP | O podwójnej precyzji |
| INT1 | Bajtów |
| INT2 | Int16 |
| INT4 | Int |
| JĘZYK | Ciąg |
| LCHR | Ciąg |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| NIEPRZETWORZONE | Byte[] |
| RAWSTRING | Byte[] |
| CIĄG | Ciąg |
| JEDNOSTKA | Ciąg |
| DATS | Ciąg |
| NUMC | Ciąg |
| TIMS | Ciąg |


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
