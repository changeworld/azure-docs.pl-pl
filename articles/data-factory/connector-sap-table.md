---
title: Kopiowanie danych z tabeli SAP przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z tabeli programu SAP do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827749"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopiowanie danych z tabeli SAP przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z tabeli programu SAP. Aby uzyskać więcej informacji, zobacz [omówienie działania kopiowania](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z tabeli programu SAP do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako źródła lub ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik tabeli SAP obsługuje:

- Kopiowanie danych z tabeli programu SAP w:

  - Oprogramowanie SAP ERP i przeniesieniu jej głównym składnikiem (SAP ECC) w wersji 7.01 lub nowszym (w ostatnich SAP obsługi pakietu stosu wydana po 2015).
  - SAP Business Warehouse (SAP BW) w wersji 7.01 lub nowszej.
  - SAP S/4HANA.
  - Innych produktów SAP Business Suite wersji 7.01 lub nowszej.

- Kopiowanie danych z zarówno tabeli przezroczyste programu SAP, puli tabeli, tabeli klastrowanego i widoku.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego lub zabezpieczenia sieci komunikacji (SNC), jeśli skonfigurowano SNC.
- Łączenie z serwera aplikacji SAP lub serwer komunikatów SAP.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika systemu SAP tabeli, należy:

- Konfigurowanie własnego środowiska integration runtime (wersja 3.17 lub nowszej). Aby uzyskać więcej informacji, zobacz [tworzenie i konfigurowanie własnego środowiska integration runtime](create-self-hosted-integration-runtime.md).

- Pobierz 64-bitowych [łącznika systemu SAP dla programu Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) z witryny firmy SAP i zainstaluj go na maszynie Self-Hosted integration runtime. Podczas instalacji upewnij się, możesz wybrać **zainstalować zestawy GAC** opcji **opcjonalne kroki instalacji** okna.

  ![Instalowanie łącznika systemu SAP dla platformy .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP, który jest używany w łączniku SAP fabryki danych w tabeli musi mieć następujące uprawnienia:

  - Autoryzacja przy użyciu miejsc docelowych zdalnego wywołania funkcji (RFC).
  - Uprawnienia do wykonania działania S_SDSAUTH obiekt autoryzacji.

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika SAP w tabeli.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi SAP BW Open Centrum połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| `type` | `type` Właściwość musi być równa `SapTable`. | Tak |
| `server` | Nazwa serwera, na którym znajduje się wystąpienie SAP.<br/>Służy do nawiązywania połączenia z serwerem aplikacji SAP. | Nie |
| `systemNumber` | Numer systemu systemu SAP.<br/>Służy do nawiązywania połączenia z serwerem aplikacji SAP.<br/>Dozwolone wartości: Dwucyfrową liczbą dziesiętną reprezentowane jako ciąg. | Nie |
| `messageServer` | Nazwa hosta serwera SAP wiadomości.<br/>Służy do nawiązywania połączenia z serwerem SAP wiadomości. | Nie |
| `messageServerService` | Nazwa usługi lub port numer serwera wiadomości.<br/>Służy do nawiązywania połączenia z serwerem SAP wiadomości. | Nie |
| `systemId` | Identyfikator systemu SAP, w którym znajduje się tabela.<br/>Służy do nawiązywania połączenia z serwerem SAP wiadomości. | Nie |
| `logonGroup` | Grupa logowania dla systemu SAP.<br/>Służy do nawiązywania połączenia z serwerem SAP wiadomości. | Nie |
| `clientId` | Identyfikator klienta w systemie SAP.<br/>Dozwolone wartości: Liczba dziesiętna trzycyfrowy reprezentowane jako ciąg. | Tak |
| `language` | Język używany w systemie SAP.<br/>Wartość domyślna to `EN`.| Nie |
| `userName` | Nazwa użytkownika, który ma dostęp do serwera SAP. | Tak |
| `password` | Hasło użytkownika. Oznacz to pole z `SecureString` typ, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| `sncMode` | Wskaźnik aktywacji SNC dostęp do serwera SAP, w którym znajduje się tabela.<br/>Użyj, jeśli chcesz połączyć się z serwerem SAP za pomocą SNC.<br/>Dozwolone wartości to `0` (domyślnie wyłączone) lub `1` (dalej). | Nie |
| `sncMyName` | Nazwy SNC inicjatora dostęp do serwera SAP, w którym znajduje się tabela.<br/>Stosuje się, gdy `sncMode` znajduje się na. | Nie |
| `sncPartnerName` | Nazwa SNC partnera komunikacji dostęp do serwera SAP, w którym znajduje się tabela.<br/>Stosuje się, gdy `sncMode` znajduje się na. | Nie |
| `sncLibraryPath` | Biblioteka produktu zewnętrznych zabezpieczeń dostępu do serwera SAP gdzie znajduje się tabela.<br/>Stosuje się, gdy `sncMode` znajduje się na. | Nie |
| `sncQop` | Poziom SNC jakości ochrony do zastosowania.<br/>Stosuje się, gdy `sncMode` znajduje się na. <br/>Dozwolone wartości to `1` (uwierzytelnianie) `2` (integralność) `3` (prywatność) `8` (opcja domyślna), `9` (maksimum). | Nie |
| `connectVia` | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko integration runtime jest wymagany, jak wspomniano wcześniej w [wymagania wstępne](#prerequisites). |Tak |

**Przykład 1: Połącz z serwerem aplikacji SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
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

### <a name="example-2-connect-to-an-sap-message-server"></a>Przykład 2: Połącz z serwerem SAP wiadomości

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Przykład 3: Połącz przy użyciu SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości definiujące zestawy danych, zobacz [zestawów danych](concepts-datasets-linked-services.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli SAP.

Aby skopiować dane z i do usługi SAP BW Open Centrum połączone, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| `type` | `type` Właściwość musi być równa `SapTableResource`. | Tak |
| `tableName` | Nazwa tabeli SAP, aby skopiować dane z. | Tak |

### <a name="example"></a>Przykład

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości definiujące działania zobacz [potoki](concepts-pipelines-activities.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez źródło tabeli SAP.

### <a name="sap-table-as-a-source"></a>SAP tabeli jako źródła

Aby skopiować dane z tabeli programu SAP, obsługiwane są następujące właściwości:

| Właściwość                         | Opis                                                  | Wymagane |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` Właściwość musi być równa `SapTableSource`.         | Tak      |
| `rowCount`                         | Liczba wierszy, które mają zostać pobrane.                              | Nie       |
| `rfcTableFields`                   | Pól (kolumn) do skopiowania z tabeli SAP. Na przykład `column0, column1`. | Nie       |
| `rfcTableOptions`                  | Opcje filtrowania wierszy w tabeli programu SAP. Na przykład `COLUMN0 EQ 'SOMEVALUE'`. Zobacz też tabeli operatora zapytania SAP w dalszej części tego artykułu. | Nie       |
| `customRfcReadTableFunctionModule` | Niestandardowe RFC funkcja moduł, który może służyć do odczytywania danych z tabeli programu SAP.<br>Aby zdefiniować sposób dane są pobierane z systemu SAP i powrót do usługi Data Factory, można użyć niestandardowego modułu funkcji RFC. Moduł funkcji niestandardowe musi mieć interfejs implementowany (import, export, tabel) podobny do `/SAPDS/RFC_READ_TABLE2`, który jest domyślnym interfejsem używane przez fabrykę danych. | Nie       |
| `partitionOption`                  | Mechanizm partycji, który można odczytać z tabeli programu SAP. Obsługiwane opcje: <ul><li>`None`</li><li>`PartitionOnInt` (normalne liczby całkowitej lub liczby całkowite z zerową Dopełnienie z lewej strony, takie jak `0000012345`)</li><li>`PartitionOnCalendarYear` (4 cyfr w formacie "YYYY")</li><li>`PartitionOnCalendarMonth` (6 cyfr w formacie "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 cyfr w formacie "RRRRMMDD")</li></ul> | Nie       |
| `partitionColumnName`              | Nazwa kolumny używana w celu podzielenia danych.                | Nie       |
| `partitionUpperBound`              | Maksymalna wartość kolumny określone w `partitionColumnName` posłuży kontynuować z podziałem na partycje. | Nie       |
| `partitionLowerBound`              | Minimalna wartość kolumny określone w `partitionColumnName` posłuży kontynuować z podziałem na partycje. | Nie       |
| `maxPartitionsNumber`              | Maksymalna liczba partycji, aby podzielić dane na.     | Nie       |

>[!TIP]
>Jeśli tabela SAP ma dużą ilość danych, takich jak kilka miliard wierszy, należy użyć `partitionOption` i `partitionSetting` można podzielić dane na mniejsze partycje. W tym przypadku dane są odczytywane na partycję i każdej partycji danych są pobierane z serwera SAP za pomocą jednego wywołania RFC.<br/>
<br/>
>Biorąc `partitionOption` jako `partitionOnInt` na przykład liczbę wierszy w poszczególnych partycjach jest obliczane na podstawie tej formuły: (razem wierszy objętych między `partitionUpperBound` i `partitionLowerBound`) /`maxPartitionsNumber`.<br/>
<br/>
>Aby uruchomić partycje równolegle w celu przyspieszenia kopiowania, zdecydowanie zalecamy wprowadzanie `maxPartitionsNumber` wielu wartości `parallelCopies` właściwości. Aby uzyskać więcej informacji, zobacz [równoległych kopii](copy-activity-performance.md#parallel-copy).

W `rfcTableOptions`, można użyć następujących typowych operatorów zapytań SAP do filtrowania wiersze:

| Operator | Opis |
| :------- | :------- |
| `EQ` | Równa się |
| `NE` | Nie równa się |
| `LT` | Mniejsze niż |
| `LE` | Mniejsze niż lub równe |
| `GT` | Większe niż |
| `GE` | Większe niż lub równe |
| `LIKE` | Podobnie jak w `LIKE 'Emma%'` |

### <a name="example"></a>Przykład

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapowanie typu danych dla tabeli SAP

Przy kopiowaniu danych z tabeli programu SAP, następujące mapowania są używane z typów danych tabeli SAP do typów danych tymczasowych usługi Azure Data Factory. Aby dowiedzieć się, jak działania kopiowania mapuje typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ ABAP SAP | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| `C` (String) | `String` |
| `I` (Liczba całkowita) | `Int32` |
| `F` (Zmiennoprzecinkowego) | `Double` |
| `D` (Data) | `String` |
| `T` (Czas) | `String` |
| `P` (BCD pakowane, waluty, Decimal, ilość) | `Decimal` |
| `N` (Liczbowe od) | `String` |
| `X` (Binarnych i Raw) | `String` |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
