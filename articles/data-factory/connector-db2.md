---
title: Kopiowanie danych z bazy danych DB2 przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z bazy danych DB2 do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: 797db8d0dd321676a3fa436a328a9981a3d3ca3b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312043"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiowanie danych z bazy danych DB2 przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-onprem-db2-connector.md)
> * [Bieżąca wersja](connector-db2.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych programu DB2. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych DB2, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik DB2 obsługuje następujące platformy programu IBM DB2 i wersji za pomocą rozproszonej architektury bazy danych relacyjnych (DRDA) SQL dostęp do Menedżera (SQLAM) w wersji 9, 10 i 11:

* IBM DB2 w przypadku 11.1 z/OS
* IBM DB2 w przypadku 10.1 z/OS
* IBM DB2 for i 7.3
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie z informacją "nie znaleziono pakietu odpowiadającego żądaniu wykonania instrukcji SQL. SQLSTATE = 51002 SQLCODE =-805 ", powodem jest wymagany pakiet nie został utworzony dla zwykłego użytkownika w tych systemach operacyjnych. Wykonaj te instrukcje, zgodnie z danego typu serwera bazy danych DB2:
> - Bazy danych DB2 for i (AS400): let użytkownik zaawansowany, utwórz kolekcję dla użytkownika logowania przed rozpoczęciem korzystania z działania kopiowania. Polecenie: `create collection <username>`
> - Bazy danych DB2 z/OS lub LUW: Użyj konta z wysokim poziomie uprawnień — użytkownika zaawansowanego lub administratora przy użyciu pakietu urzędy i BIND, BINDADD, uprawnienia GRANT wykonania do publicznej — jednokrotne uruchomienie działania kopiowania, a następnie wymagany pakiet jest tworzony automatycznie podczas kopiowania. Możesz później, przejdź do zwykłego użytkownika dla uruchomień kolejnych kopii.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć kopiowania danych z bazy danych DB2, który nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Aby dowiedzieć się więcej na temat środowisk Self-Hosted integration Runtime, zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykułu. Infrastruktura Integration Runtime zapewnia wbudowane sterownik bazy danych DB2, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika podczas kopiowania danych z bazy danych DB2.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi Data Factory określonych łącznik DB2.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych DB2 połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Db2** | Yes |
| server |Nazwa serwera bazy danych DB2. Możesz określić numer portu, zgodnie z nazwą serwera, rozdzielone średnikami, np. `server:port`. |Yes |
| database |Nazwa bazy danych DB2. |Yes |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych DB2.<br/>Dozwolone wartości to: **Podstawowe**. |Yes |
| username |Określ nazwę użytkownika do łączenia z bazą danych DB2. |Yes |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych DB2.

Aby skopiować dane z bazy danych DB2, należy ustawić właściwość typu zestawu danych na **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w bazie danych DB2. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło bazy danych DB2.

### <a name="db2-as-source"></a>Bazy danych DB2 jako źródło

Aby skopiować dane z bazy danych DB2, należy ustawić typ źródłowego w działaniu kopiowania, aby **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **RelationalSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Mapowanie dla bazy danych DB2 — typ danych

Podczas kopiowania danych z bazy danych DB2, następujące mapowania są używane do typów danych tymczasowych usługi Azure Data Factory z typów danych DB2. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ bazy danych DB2 | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| Date |Datetime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| Numeric |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
