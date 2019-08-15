---
title: Kopiowanie danych z programu DB2 przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o kopiowaniu danych z programu DB2 do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 49f86a6a8858fd0ef3085ed571f3348d33f70c8d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966585"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiowanie danych z programu DB2 przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-db2-connector.md)
> * [Bieżąca wersja](connector-db2.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych programu DB2. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane z bazy danych programu DB2 można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W przypadku tego łącznika bazy danych DB2 obsługiwane są następujące platformy i wersje programu IBM DB2 z użyciem rozproszonej architektury relacyjnej bazy (DRDA) (SQLAM) w wersji 9, 10 i 11:

* IBM DB2 dla systemu z/OS 11,1
* IBM DB2 dla systemu z/OS 10,1
* IBM DB2 dla i 7,3
* IBM DB2 dla i 7,2
* IBM DB2 dla i 7,1
* IBM DB2 for LUW 11
* IBM DB2 dla LUW 10,5
* IBM DB2 dla LUW 10,1

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie informujący o tym, że nie znaleziono pakietu odpowiadającego żądaniu wykonania instrukcji SQL. SQLSTATE = 51002 SQLCODE =-805 ", przyczyna to wymagany pakiet nie jest tworzony dla normalnego użytkownika w tym systemie operacyjnym. Postępuj zgodnie z tymi instrukcjami według typu serwera bazy danych DB2:
> - DB2 for i (systemu AS400): zezwól użytkownikom na tworzenie kolekcji dla użytkownika logowania przed użyciem działania kopiowania. Dotyczące`create collection <username>`
> - DB2 dla systemu z/OS lub LUW: Użyj konta o wysokim poziomie uprawnień — Użytkownicy zaawansowani lub Administratorzy z organami urzędów i BIND, BINDADD, UDZIELą uprawnień do wykonania publicznych. Aby uruchomić działanie kopiowania raz, wymagany pakiet jest automatycznie tworzony podczas kopiowania. Następnie można wrócić do normalnego użytkownika w przypadku kolejnych przebiegów kopiowania.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime udostępnia wbudowany sterownik programu DB2, dlatego nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z programu DB2.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika bazy danych DB2.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi połączonej z bazą danych DB2:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć ustawioną wartość: **Db2** | Tak |
| server |Nazwa serwera bazy danych DB2. Możesz określić numer portu następujący po nazwie serwera rozdzielany średnikiem, np. `server:port`. |Tak |
| database |Nazwa bazy danych DB2. |Tak |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych programu DB2.<br/>Dozwolona wartość to: **Podstawowa**. |Tak |
| username |Określ nazwę użytkownika w celu nawiązania połączenia z bazą danych programu DB2. |Tak |
| password |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu DB2.

Aby skopiować dane z bazy danych DB2, należy ustawić Właściwość Type zestawu danychna relacyjny. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **Obiekt relacyjny** | Tak |
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

### <a name="db2-as-source"></a>DB2 jako źródło

Aby skopiować dane z programu DB2, ustaw typ źródła w działaniu Copy na **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi mieć ustawioną wartość: **RelationalSource** | Tak |
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

## <a name="data-type-mapping-for-db2"></a>Mapowanie typu danych dla programu DB2

Podczas kopiowania danych z programu DB2 następujące mapowania są używane z typów danych DB2 do Azure Data Factory danych pośrednich. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

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
| Rzeczywiste |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |Datetime |
| Liczby |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
