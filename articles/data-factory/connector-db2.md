---
title: Kopiowanie danych z bazy danych DB2 przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z bazy danych DB2 do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: 11647c231db3ff7beb2fed641dc72ff339b2b45a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046245"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiowanie danych z bazy danych DB2 przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-onprem-db2-connector.md)
> * [Bieżąca wersja](connector-db2.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z bazy danych DB2. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych DB2 żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik DB2 obsługuje następujące platformy IBM DB2 i wersje z dostępu SQL Distributed relacyjnej bazy danych architektury DRDA () Manager (SQLAM) w wersji 9, 10 i 11:

* IBM DB2 dla 11.1 z/OS
* IBM DB2 dla z/OS 10.1
* IBM DB2 dla i 7.2
* IBM DB2 dla i 7.1
* IBM DB2 LUW 11
* IBM DB2 dla LUW 10.5
* IBM DB2 dla LUW 10.1

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie stwierdzający "nie znaleziono pakietu odpowiadający żądania wykonania instrukcji SQL. SQLSTATE = 51002 SQLCODE =-805 ", powodem jest wymagana pakietu nie został utworzony dla zwykłego użytkownika na takie systemu operacyjnego. Wykonaj te instrukcje, w zależności od typu serwera bazy danych DB2:
> - Bazy danych DB2 dla i (AS400): umożliwia użytkownikowi zasilania utworzyć kolekcję dla logowania użytkownika przed użyciem działania kopiowania. Polecenie: `create collection <username>`
> - Bazy danych DB2 z/OS lub LUW: raz uruchomić działanie kopiowania za pomocą konta z wysokim poziomie uprawnień — użytkownik zaawansowany lub administrator z urzędów pakietu i powiązania BINDADD, uprawnienia GRANT wykonania do publicznego —, a następnie uruchomienie pakietu jest tworzony automatycznie podczas kopiowania. Możesz później, przejdź do zwykłego użytkownika dla sekwencji kolejnych kopii.

## <a name="prerequisites"></a>Wymagania wstępne

Umożliwia kopiowanie danych z bazy danych DB2, który nie jest dostępny publicznie, należy skonfigurować środowisko uruchomieniowe Self-hosted integracji. Informacje na temat środowisk uruchomieniowych siebie integracji, zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu. Środowiska uruchomieniowego integracji oferuje wbudowane sterownik bazy danych DB2, dlatego nie trzeba ręcznie zainstalowania sterownika podczas kopiowania danych z bazy danych DB2.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika bazy danych DB2.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Dla bazy danych DB2 połączone usługi, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **bazy danych Db2** | Yes |
| serwer |Nazwa serwera bazy danych DB2. |Yes |
| baza danych |Nazwa bazy danych DB2. |Yes |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych DB2.<br/>Dozwolone wartości to: **podstawowe**. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika do połączenia z bazą danych DB2. |Yes |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych DB2.

Aby skopiować dane z bazy danych DB2, ustaw właściwość Typ zestawu danych do **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w bazie danych DB2. | Nie (Jeśli określono parametr "zapytania" w źródle działania) |

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

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło bazy danych DB2.

### <a name="db2-as-source"></a>Bazy danych DB2 jako źródło

Aby skopiować dane z bazy danych DB2, należy ustawić typ źródła w przypadku działania kopiowania do **RelationalSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **RelationalSource** | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Na przykład: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nie (Jeśli określono parametr "Nazwa_tabeli" w zestawie danych) |

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

## <a name="data-type-mapping-for-db2"></a>Typ danych mapowania dla bazy danych DB2

Podczas kopiowania danych z bazy danych DB2, następujące mapowania są używane z typów danych DB2 do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ bazy danych DB2 | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BigInt |Int64 |
| Binarny |Byte[] |
| Obiekt blob |Byte[] |
| char |Ciąg |
| CLOB |Ciąg |
| Date |Data/godzina |
| DB2DynArray |Ciąg |
| DbClob |Ciąg |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| podwójne |podwójne |
| Liczba zmiennoprzecinkowa |podwójne |
| Grafika |Ciąg |
| Liczba całkowita |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Ciąg |
| LongVarGraphic |Ciąg |
| Liczbowy |Decimal |
| Real |Pojedyncze |
| SmallInt |Int16 |
| Time |Zakres czasu |
| Sygnatura czasowa |DateTime |
| VarBinary |Byte[] |
| VarChar |Ciąg |
| VarGraphic |Ciąg |
| Xml |Byte[] |


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
