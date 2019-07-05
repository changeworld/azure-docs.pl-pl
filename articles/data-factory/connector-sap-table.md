---
title: Kopiowanie danych z tabeli SAP przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z tabeli SAP do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: jingwang
ms.openlocfilehash: e54a69b6c2b48e50c089f8b6b7458cf91133dd85
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443299"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Kopiowanie danych z tabeli SAP przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z tabeli programu SAP. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z tabeli SAP do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP tabeli obsługuje:

- Kopiowanie danych z tabeli SAP w:

    - **SAP ECC** wersją 7.01 lub nowszy (w ostatnich SAP pomocy technicznej pakietu stosie wydana po roku 2015)
    - **System SAP BW** za pomocą wersji 7.01 lub nowszej
    - **SAP S/4HANA**
    - **Innych produktów SAP Business Suite** za pomocą wersji 7.01 lub nowszej 

- Kopiowanie danych z obu **tabeli przezroczyste SAP** i **widoku**.
- Kopiowanie danych przy użyciu **uwierzytelnianie podstawowe** lub **SNC** (zabezpieczenia komunikacji sieciowej) Jeśli SNC jest skonfigurowany.
- Nawiązywanie połączenia z **serwera aplikacji** lub **serwer komunikatów**.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP tabeli, należy:

- Skonfiguruj własne środowisko IR za pomocą wersji 3.17 lub nowszej. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.

- Pobierz **64-bitowych [SAP .NET łącznika 3.0](https://support.sap.com/en/product/connectors/msnet.html)**  z witryny sieci Web firmy SAP i zainstaluj go na maszynie własne środowisko IR (). Podczas instalowania w oknie kroki opcjonalne ustawienia upewnij się, możesz wybrać **zainstalować zestawy GAC** opcji. 

    ![Instalowanie łącznika systemu SAP platformy .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP on używany w łączniku tabelę SAP fabryki danych musi mieć następujące uprawnienia: 

    - Autoryzacja RFC. 
    - Uprawnienia do działania "Execute" obiekt autoryzacji "S_SDSAUTH".

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika SAP tabeli.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi SAP Business Warehouse Otwórz Centrum połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapTable** | Tak |
| server | Nazwa serwera, na którym znajduje się wystąpienie SAP.<br/>Zastosowanie, jeśli chcesz się połączyć **serwera aplikacji SAP**. | Nie |
| systemNumber | Numer systemu systemu SAP.<br/>Zastosowanie, jeśli chcesz się połączyć **serwera aplikacji SAP**.<br/>Dozwolone wartości: liczba dziesiętna dwucyfrowy reprezentowane jako ciąg. | Nie |
| messageServer | Nazwa hosta serwera SAP wiadomości.<br/>Zastosowanie, jeśli chcesz się połączyć **SAP Message Server**. | Nie |
| messageServerService | Nazwa usługi lub port numer serwera wiadomości.<br/>Zastosowanie, jeśli chcesz się połączyć **SAP Message Server**. | Nie |
| systemId | SystemID system SAP, w którym znajduje się tabela.<br/>Zastosowanie, jeśli chcesz się połączyć **SAP Message Server**. | Nie |
| logonGroup | Grupa logowania dla systemu SAP.<br/>Zastosowanie, jeśli chcesz się połączyć **SAP Message Server**. | Nie |
| clientId | Identyfikator klienta klienta w systemie SAP.<br/>Dozwolone wartości: liczba dziesiętna trzy cyfry, reprezentowane jako ciąg. | Yes |
| language | Język, który używa systemu SAP. | Nie (wartość domyślna to **EN**)|
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Yes |
| password | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| sncMode | Wskaźnik aktywacji SNC dostęp do serwera SAP, w którym znajduje się tabela.<br/>Zastosowanie, jeśli chcesz użyć SNC nawiązać połączenia z serwerem SAP.<br/>Dozwolone wartości to: **0** (wyłączony, domyślny) lub **1** (dalej). | Nie |
| sncMyName | Nazwy SNC inicjatora dostęp do serwera SAP, w którym znajduje się tabela.<br/>Gdy stosowane `sncMode` znajduje się na. | Nie |
| sncPartnerName | Komunikacja partnera SNC nazwa dostęp do serwera SAP, w którym znajduje się tabela.<br/>Gdy stosowane `sncMode` znajduje się na. | Nie |
| sncLibraryPath | Biblioteka produktu zewnętrznych zabezpieczeń dostępu do serwera SAP gdzie znajduje się tabela.<br/>Gdy stosowane `sncMode` znajduje się na. | Nie |
| sncQop | Jakość SNC ochrony.<br/>Gdy stosowane `sncMode` znajduje się na. <br/>Dozwolone wartości to: **1** (uwierzytelnianie) **2** (integralność) **3** (prywatność) **8** (opcja domyślna), **9** (maksimum). | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Tak |

**Przykład 1: łączenie z serwerem aplikacji SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
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

**Przykład 2: łączenie z serwerem komunikat SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
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

**Przykład 3: łączenie się za pomocą SNC**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli SAP.

Aby skopiować dane z i do Centrum Otwórz programu SAP BW, następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | Właściwość type musi być równa **SapTableResource**. | Yes |
| tableName | Nazwa tabeli SAP, aby skopiować dane z. | Tak |

**Przykład:**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło tabeli SAP.

### <a name="sap-table-as-source"></a>SAP tabeli jako źródła

Aby skopiować dane z tabeli SAP, następujące właściwości są obsługiwane.

| Właściwość                         | Opis                                                  | Wymagane |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | Właściwość type musi być równa **SapTableSource**.         | Yes      |
| Liczba wierszy                         | Liczba wierszy, które mają zostać pobrane.                              | Nie       |
| rfcTableFields                   | Pola, aby skopiować z tabeli SAP. Na przykład `column0, column1`. | Nie       |
| rfcTableOptions                  | Funkcje umożliwiające filtrowanie wierszy w tabeli SAP. Na przykład `COLUMN0 EQ 'SOMEVALUE'`. Zobacz więcej opis pod tą tabelą. | Nie       |
| customRfcReadTableFunctionModule | Niestandardowe RFC funkcja moduł, który może służyć do odczytywania danych z tabeli SAP.<br>Można użyć niestandardowego modułu funkcji RFC do zdefiniowania, jak dane są pobierane z systemu SAP i powrót do usługi ADF. While, należy pamiętać, że niestandardowego modułu funkcji musi mieć podobny interfejs implementowany (import, export, tabel), podobnie jak/SAPDS/RFC_READ_TABLE2, który jest domyślnie używany przez usługę ADF. | Nie       |
| partitionOption                  | Mechanizm partycji można odczytać z tabeli SAP. Obsługiwane opcje: <br/>- **Brak**<br/>- **PartitionOnInt** (normalne liczby całkowitej lub liczby całkowitej wartości zero Dopełnienie z lewej strony, takie jak 0000012345)<br/>- **PartitionOnCalendarYear** (4 cyfr w formacie "YYYY")<br/>- **PartitionOnCalendarMonth** (6 cyfr w formacie "YYYYMM")<br/>- **PartitionOnCalendarDate** (8 cyfr w formacie "RRRRMMDD") | Nie       |
| partitionColumnName              | Nazwa kolumny w celu podzielenia danych.                | Nie       |
| partitionUpperBound              | Maksymalna wartość kolumny określone w `partitionColumnName` który będzie używany do partycjonowania postępowania. | Nie       |
| partitionLowerBound              | Minimalna wartość kolumny określone w `partitionColumnName` który będzie używany do partycjonowania postępowania. | Nie       |
| maxPartitionsNumber              | Maksymalna liczba partycji, aby podzielić dane na.     | Nie       |

>[!TIP]
>- Jeśli tabela SAP ma dużej ilości danych, takich jak kilka miliardów wierszy, należy użyć `partitionOption` i `partitionSetting` można podzielić dane na małych partycji, w którym to przypadku dane są odczytywane przez partycje i każdej partycji danych są pobierane z serwera SAP za pośrednictwem jednego pojedynczego Wywołanie RFC.<br/>
>- Biorąc `partitionOption` jako `partitionOnInt` na przykład liczbę wierszy w poszczególnych partycjach jest obliczana na podstawie (razem wierszy objętych między *partitionUpperBound* i *partitionLowerBound*) /*maxPartitionsNumber*.<br/>
>- Jeśli chcesz przeprowadzić dalsze partycje równolegle w celu przyspieszenia kopiowania, zaleca się `maxPartitionsNumber` jako wiele wartości `parallelCopies` (Dowiedz się więcej z [osobną kopię](copy-activity-performance.md#parallel-copy)).

W `rfcTableOptions`, np. następujące typowe SAP operatory zapytania można użyć do filtrowania wiersze: 

| Operator | Opis |
| :------- | :------- |
| EQ | Równa się |
| NE | Nie równa się |
| LT | Mniejsze niż |
| LE | Mniejsze niż lub równe |
| GT | Większe niż |
| GE | Większe niż lub równe |
| NP. | W takich jak "Emma %" |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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

## <a name="data-type-mapping-for-sap-table"></a>Mapowanie typu danych dla tabeli SAP

Podczas kopiowania danych z tabeli SAP, następujące mapowania są używane z typów danych tabeli SAP do typów danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ ABAP SAP | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| C (ciąg) | String |
| Czy mogę (liczba całkowita) | Int32 |
| F (zmiennoprzecinkowego) | Double |
| D (Data) | String |
| T (czas) | String |
| P (BCD spakowane, waluty, Decimal, ilość) | Decimal |
| N (liczbowe od) | String |
| X (binarnych i Raw) | String |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
