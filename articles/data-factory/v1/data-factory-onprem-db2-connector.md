---
title: Przenoszenie danych z bazy danych DB2 przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane z lokalnej bazy danych DB2 za pomocą działania kopiowania w fabryce danych Azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 72c88ef10bf1df217ec6e24ac744d0b30386b4a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824018"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Przenoszenie danych z bazy danych DB2 za pomocą działania kopiowania w fabryce danych Azure
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-onprem-db2-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-db2.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznik DB2 w wersji 2](../connector-db2.md).


W tym artykule opisano, jak można użyć działania kopiowania w usłudze Azure Data Factory Aby skopiować dane z lokalnej bazy danych DB2 w magazynie danych. Można skopiować danych do dowolnego magazynu, który jest wymieniony jako obsługiwany obiekt sink w [działania przenoszenia danych usługi Data Factory](data-factory-data-movement-activities.md#supported-data-stores-and-formats) artykułu. Ten temat opiera się na usługi Data Factory artykuł przedstawia omówienie przenoszenie danych za pomocą działania kopiowania, która zawiera listę kombinacji magazynu obsługiwanych danych. 

Usługa Data Factory obsługuje obecnie tylko przenosi dane z bazy danych programu DB2 do [obsługiwanego magazynu danych ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Przenoszenie danych z innych danych są przechowywane do bazy danych DB2, baza danych nie jest obsługiwana.

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Data Factory obsługuje łączenie z bazą danych DB2 w środowisku lokalnym za pomocą [bramy zarządzania danymi](data-factory-data-management-gateway.md). Aby uzyskać instrukcje krok po kroku do skonfigurowania bramy potoku danych do przenoszenia danych, zobacz [przenoszenie danych ze środowiska lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

Brama jest wymagana, nawet wtedy, gdy programu DB2 znajduje się na maszynie Wirtualnej IaaS platformy Azure. Bramę można zainstalować na tych samych maszyn wirtualnych IaaS do przechowywania danych. Jeżeli brama może połączyć się z bazą danych, można zainstalować bramy na innej maszynie Wirtualnej.

Brama zarządzania danymi udostępnia wbudowanego sterownika bazy danych DB2, więc nie trzeba ręcznie zainstalować sterownik, aby skopiować dane z bazy danych DB2.

> [!NOTE]
> Aby uzyskać porady na temat rozwiązywania problemów, połączenia i problemy z bramy, zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) artykułu.


## <a name="supported-versions"></a>Obsługiwane wersje
Łącznik DB2 fabryka danych obsługuje następujące platformy programu IBM DB2 i wersji przy użyciu Menedżera dostępu programu SQL Distributed architektury bazy danych relacyjnych (DRDA) w wersjach 9, 10 i 11:

* IBM DB2 w przypadku wersji 11.1 z/OS
* IBM DB2 w przypadku wersji 10.1 z/OS
* IBM DB2 i (AS400) w wersji 7.2
* IBM DB2 i (AS400) w wersji 7.1
* IBM DB2 dla systemów Linux, UNIX i Windows (LUW) w wersji 11
* IBM DB2 for LUW wersji 10.5
* IBM DB2 for LUW wersji 10.1

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie "nie znaleziono pakietu odpowiadającego żądaniu wykonania instrukcji SQL. SQLSTATE 51002 SQLCODE =-805, = "przyczyną jest potrzebny pakiet nie został utworzony dla zwykłego użytkownika w systemie operacyjnym. Aby rozwiązać ten problem, wykonaj te instrukcje dla danego typu serwera bazy danych DB2:
> - Bazy danych DB2 for i (AS400): Umożliwia użytkownikowi zasilania, utwórz kolekcję dla zwykłego użytkownika przed uruchomieniem działania kopiowania. Aby utworzyć kolekcję, użyj polecenia: `create collection <username>`
> - Bazy danych DB2 z/OS lub LUW: Użyj konta z wysokim poziomie uprawnień — użytkownika zaawansowanego lub administratora, który ma urzędy pakietu BIND, BINDADD, przydział wykonania do publicznego uprawnienia i — raz uruchomić kopię. Potrzebny pakiet jest tworzony automatycznie podczas kopiowania. Możesz później, przejdź do zwykłego użytkownika dla uruchomień kolejnych kopii.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania do przenoszenia danych z magazynu danych programu DB2 lokalnie przy użyciu różnych narzędzi i interfejsów API: 

- Najprostszym sposobem utworzenia potoku jest używanie kreatora kopiowania usługi Azure Data Factory. Szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania, zobacz [samouczka: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md). 
- Umożliwia także narzędzia do tworzenia potoku, w tym witryny Azure portal, programu Visual Studio, programu Azure PowerShell, szablonu usługi Azure Resource Manager, interfejsu API platformy .NET i interfejsu API REST. Aby uzyskać instrukcje krok po kroku utworzysz potok z działaniem kopiowania, zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Utwórz połączone usługi, aby połączyć dane wejściowe i wyjściowe magazynów danych z fabryką danych.
2. Utwórz zestawy danych reprezentujące dane wejściowe i wyjściowe operacji kopiowania. 
3. Tworzenie potoku za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. 

Korzystając z Kreatora kopiowania, definicji JSON usługi fabryka danych, połączonej usługi, zestawy danych i potoku jednostki są tworzone automatycznie dla Ciebie. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET), zdefiniujesz jednostki usługi Data Factory przy użyciu formatu JSON. Przykład kodu JSON: Kopiuj dane z bazy danych DB2 do usługi Azure Blob storage zawiera definicji JSON dla jednostek fabryki danych, które są używane w celu skopiowania danych z lokalnego magazynu danych programu DB2.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory, które są specyficzne dla magazynu danych programu DB2.

## <a name="db2-linked-service-properties"></a>Właściwości usługi połączonej bazy danych DB2
W poniższej tabeli wymieniono właściwości kodu JSON, które są specyficzne dla usługi połączonej bazy danych DB2.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **type** |Ta właściwość musi być równa **OnPremisesDb2**. |Yes |
| **server** |Nazwa serwera bazy danych DB2. |Tak |
| **database** |Nazwa bazy danych DB2. |Yes |
| **schema** |Nazwa schematu w bazie danych DB2. Ta właściwość jest rozróżniana wielkość liter. |Nie |
| **authenticationType** |Typ uwierzytelniania, który służy do łączenia z bazą danych DB2. Możliwe wartości to: Anonimowe, podstawowe i Windows. |Tak |
| **Nazwa użytkownika** |Nazwa konta użytkownika, jeśli używasz uwierzytelniania podstawowe lub Windows. |Nie |
| **Hasło** |Hasło dla konta użytkownika. |Nie |
| **gatewayName** |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych DB2 w środowisku lokalnym. |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak **struktury**, **dostępności**i **zasad** dla zestawu danych JSON, są podobne dla wszystkich typów na zestaw danych (Azure SQL, usłudze Azure Blob storage, Azure Table storage i tak dalej).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcji dla zestawu danych typu **RelationalTable**, który zawiera zestaw danych DB2, ma następującą właściwość:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **tableName** |Nazwa tabeli w wystąpieniu bazy danych DB2, które połączona usługa przywołuje. Ta właściwość jest rozróżniana wielkość liter. |Nie (Jeśli **zapytania** właściwości działania kopiowania typu **RelationalSource** zostanie określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać listę sekcje i właściwości, które są dostępne do definiowania działania kopiowania, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Skopiuj właściwości działań, takich jak **nazwa**, **opis**, **dane wejściowe** tabeli, **generuje** tabeli i **zasad**, są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w **typeProperties** różnią się w sekcji działania dla każdego typu działania. Właściwości różnią się w zależności od typów danych źródła i ujścia dla działania kopiowania.

Działanie kopiowania, gdy źródłem jest typu **RelationalSource** (w tym bazy danych DB2), następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **Zapytanie** |Użyj niestandardowego zapytania, aby odczytywać dane. |Ciąg zapytania SQL. Na przykład: `"query": "select * from "MySchema"."MyTable""` |Nie (Jeśli **tableName** określono właściwości zestawu danych) |

> [!NOTE]
> Nazwy schematu i tabeli jest rozróżniana wielkość liter. W instrukcji zapytania, ujmij nazwy właściwości przy użyciu "" (podwójne cudzysłowy).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Przykład kodu JSON: Kopiowanie danych z bazy danych DB2 w usłudze Azure Blob storage
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W przykładzie pokazano, jak skopiować dane z bazy danych DB2 do magazynu obiektów Blob. Jednakże, można skopiować danych do [typ ujścia magazynu wszelkie obsługiwane dane](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

Przykład obejmuje następujących jednostek usługi Data Factory:

- Bazy danych DB2 połączonej usługi typu [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Usługi Azure Blob storage połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [obiektu blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties)
- A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) właściwości

Przykład kopiuje dane z wyniku kwerendy w bazie danych DB2 do obiektu blob platformy Azure co godzinę. Właściwości kodu JSON, które są używane w przykładzie są opisane w sekcjach definicji jednostki.

Pierwszym krokiem należy zainstalować i skonfigurować bramę data gateway. Instrukcje znajdują się w [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**Usługi połączonej bazy danych DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Obiekt Blob połączona usługa Azure storage**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Wejściowy zestaw danych DB2**

Przykład przyjęto założenie, że utworzono tabelę w bazie danych DB2 o nazwie "MyTable", który ma kolumnę z etykietą "timestamp" w danych szeregów czasowych.

**Zewnętrznych** właściwość jest ustawiona na wartość "true". To ustawienie informuje usługa Data Factory, że ten zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory. Należy zauważyć, że **typu** właściwość jest ustawiona na **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob co godzinę, ustawiając **częstotliwość** właściwość "Hour" i **interwał** właściwości na wartość 1. **FolderPath** właściwości dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i godzinę części czas rozpoczęcia.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Potok dla działania kopiowania**

Potoku zawierającego działanie kopiowania skonfigurowany do używania określonych danych wejściowych i wyjściowych zestawów danych, które jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **RelationalSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **zapytania** właściwość wybiera dane z tabeli "Orders".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Mapowanie typu dla bazy danych DB2
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje automatyczne konwersje z typu źródła do ujścia typu przy użyciu następujących podejście dwuetapowe:

1. Konwersji z typu natywnego źródłowego na typ architektury .NET
2. Przekonwertowanie z .NET do typu natywnego obiektu sink

Następujące mapowania są używane podczas działania kopiowania konwertuje dane z bazy danych DB2 typ architektury .NET:

| Typ bazy danych DB2 | Typ .NET framework |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Dziesiętna |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |DateTime |
| Time |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binarny |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Obiekt blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Dziesiętna |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |DateTime |
| Time |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby dowiedzieć się, jak mapowania kolumn w zestawie danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Powtarzalnego odczytu z źródeł relacyjnych
Podczas kopiowania danych z relacyjnego magazynu danych, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować ponownych prób **zasad** właściwości zestawu danych ponownie uruchomić wycinek, gdy wystąpi awaria. Upewnij się, że niezależnie od tego, ile razy ponownego uruchomienia wycinka i niezależnie od tego, jak ponownie uruchomić wycinek jest do odczytu tych samych danych. Aby uzyskać więcej informacji, zobacz [Repeatable odczytuje dane ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Więcej informacji na temat kluczowych czynników wpływających na wydajność działania kopiowania i sposobów na optymalizację wydajności [wydajności i działania kopiowania przewodnika dostrajania](data-factory-copy-activity-performance.md).
