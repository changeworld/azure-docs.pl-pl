---
title: Przenieść dane z bazy danych DB2 przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane z bazy danych DB2 lokalnie za pomocą działania kopiowania fabryki danych Azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fc4ce0a2ae33e99ecede371d9f17fb9a63851f64
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622027"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Przenieść dane z bazy danych DB2 za pomocą działania kopiowania fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-onprem-db2-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-db2.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznik DB2 w wersji 2](../connector-db2.md).


W tym artykule opisano, jak używasz działanie kopiowania w fabryce danych Azure można skopiować danych z lokalną bazą danych DB2 w magazynie danych. Można skopiować danych do dowolnego magazynu, który jest wymieniony jako obsługiwany zbiornika w [działań przepływu danych fabryki danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) artykułu. W tym temacie opiera się na artykułu fabryki danych, które zawiera omówienie przepływu danych za pomocą działania kopiowania i wyświetla kombinacje magazynu obsługiwane dane. 

Fabryka danych aktualnie obsługuje tylko przenoszenia danych z bazy danych DB2 do [obsługiwanych ujścia magazynu danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Przechowuje przenoszenia danych z innych danych do bazy danych DB2 bazy danych nie jest obsługiwana.

## <a name="prerequisites"></a>Wymagania wstępne
Fabryka danych obsługuje nawiązywania połączenia z lokalną bazą danych DB2 przy użyciu [brama zarządzania danymi](data-factory-data-management-gateway.md). Aby uzyskać instrukcje krok po kroku do konfigurowania potoku bramy danych w celu przenoszenia danych, zobacz [przenoszenia danych z lokalnymi do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

Wymagana jest brama, nawet jeśli programu DB2 znajduje się na maszynie Wirtualnej Azure IaaS. Bramę można zainstalować na tej samej maszyny Wirtualnej IaaS do przechowywania danych. Jeśli bramy można połączyć z bazą danych, można zainstalować bramę na innej maszynie Wirtualnej.

Brama zarządzania danymi zawiera wbudowane sterownik bazy danych DB2, dzięki czemu nie trzeba ręcznie zainstaluj sterownik na skopiowanie danych z bazy danych DB2.

> [!NOTE]
> Aby uzyskać wskazówki dotyczące rozwiązywania problemów dotyczących połączenia i problemy bramy, zobacz [rozwiązywania problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) artykułu.


## <a name="supported-versions"></a>Obsługiwane wersje
Łącznik DB2 fabryki danych obsługuje następujące platformy IBM DB2 i wersje Menedżera dostępu programu SQL Distributed relacyjnej bazy danych architektury DRDA () w wersji 9, 10 lub 11:

* IBM DB2 w wersji 11.1 z/OS
* IBM DB2 w wersji 10.1 z/OS
* IBM DB2 (AS400) i wersji 7.2
* IBM DB2 i (AS400) w wersji 7.1
* IBM DB2 dla systemu Linux, UNIX i systemu Windows (LUW) w wersji 11
* IBM DB2 dla LUW wersji 10.5
* IBM DB2 dla LUW wersji 10.1

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie "nie znaleziono pakietu odpowiadający żądania wykonania instrukcji SQL. SQLSTATE 51002 SQLCODE =-805, = "przyczyną jest potrzebny pakiet nie został utworzony dla zwykłego użytkownika w systemie operacyjnym. Aby rozwiązać ten problem, wykonaj następujące instrukcje dla danego typu serwera bazy danych DB2:
> - Bazy danych DB2 dla i (AS400): umożliwia użytkownikowi zasilania utworzyć kolekcję dla zwykłego użytkownika przed uruchomieniem działanie kopiowania. Aby utworzyć kolekcję, użyj polecenia: `create collection <username>`
> - Bazy danych DB2 z/OS lub LUW: Użyj konta z wysokim poziomie uprawnień — użytkownik zaawansowany lub administrator urzędów pakietu i powiązania, BINDADD, PRZYZNAĆ uprawnienia publiczne — do skopiowania raz EXECUTE. Potrzebny pakiet jest tworzony automatycznie podczas kopiowania. Możesz później, przejdź do zwykłego użytkownika dla sekwencji kolejnych kopii.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania do przenoszenia danych z magazynu danych programu DB2 lokalnie przy użyciu różnych narzędzi i interfejsów API: 

- Najprostszym sposobem, aby utworzyć potok jest użycie Kreatora kopiowania fabryki danych Azure. Szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania, zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md). 
- Umożliwia także narzędzia do tworzenia potoku, w tym portalu Azure, programu Visual Studio, programu Azure PowerShell szablonu usługi Azure Resource Manager, interfejsu API programu .NET i interfejsu API REST. Aby uzyskać instrukcje utworzyć potok z działania kopiowania, zobacz [samouczek działanie kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz połączone usługi, aby połączyć dane wejściowe i wyjściowe magazyny danych z fabryką danych.
2. Tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utworzyć potok aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z Kreatora kopiowania, definicje JSON dla fabryki danych połączone usługi, zestawy danych i jednostek potoku są tworzone automatycznie dla Ciebie. Korzystając z narzędzia lub interfejsów API (z wyjątkiem interfejsu API programu .NET), należy zdefiniować jednostek fabryki danych w formacie JSON. [Przykład JSON: kopiowanie danych z bazy danych DB2 do magazynu obiektów Blob Azure](#json-example-copy-data-from-db2-to-azure-blob) zawiera definicje JSON dla jednostek fabryki danych, które są używane do kopiowania danych z magazynu danych programu DB2 lokalnie.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek fabryki danych, które są specyficzne dla magazynu danych programu DB2.

## <a name="db2-linked-service-properties"></a>Właściwości usługi połączone bazy danych DB2
W poniższej tabeli wymieniono właściwości JSON, które są specyficzne dla usługi bazy danych DB2 połączone.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **type** |Ta właściwość musi mieć ustawioną **OnPremisesDb2**. |Yes |
| **server** |Nazwa serwera bazy danych DB2. |Yes |
| **Bazy danych** |Nazwa bazy danych DB2. |Yes |
| **schema** |Nazwa schematu bazy danych DB2. Ta właściwość jest rozróżniana wielkość liter. |Nie |
| **authenticationType** |Typ uwierzytelniania używany do łączenia z bazą danych DB2. Możliwe wartości to: anonimowe, podstawowe i systemu Windows. |Yes |
| **Nazwa użytkownika** |Nazwa konta użytkownika, jeśli korzystasz z uwierzytelniania podstawowego lub systemu Windows. |Nie |
| **Hasło** |Hasło dla konta użytkownika. |Nie |
| **gatewayName** |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych DB2. |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje, takich jak **struktury**, **dostępności**i **zasad** dla zestawu danych JSON, są podobne dla wszystkich typów zestawu danych (Azure SQL, magazyn obiektów Blob platformy Azure, Magazyn tabel Azure itd.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcja dla zestawu danych typu **RelationalTable**, w tym zestawie danych DB2, ma następującą właściwość:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **TableName** |Nazwa tabeli w wystąpieniu bazy danych DB2 odnoszący się do połączonej usługi. Ta właściwość jest rozróżniana wielkość liter. |Nie (Jeśli **zapytania** właściwości działania kopiowania typu **RelationalSource** jest określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać listę sekcji i właściwości, które są dostępne do definiowania działania kopiowania, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Kopiuj właściwości działania, takie jak **nazwa**, **opis**, **dane wejściowe** tabeli, **generuje** tabeli, i **zasad**, są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w **typeProperties** sekcji działania różne dla każdego typu działania. Dla działania kopiowania właściwości różnią się w zależności od typów źródeł danych i sink.

Dla działania kopiowania, gdy źródłem jest typu **RelationalSource** (która obejmuje bazy danych DB2), są dostępne w następujących właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **Zapytania** |Użyj niestandardowych zapytania, aby odczytać danych. |Ciąg zapytania SQL. Na przykład: `"query": "select * from "MySchema"."MyTable""` |Nie (Jeśli **tableName** określono właściwości zestawu danych) |

> [!NOTE]
> Nazwy schematu i tabeli jest rozróżniana wielkość liter. W instrukcji zapytania, ujmij nazw właściwości, za pomocą "" (podwójne cudzysłowy).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Przykład JSON: kopiowanie danych z bazy danych DB2 do magazynu obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przykład przedstawia sposób kopiowania danych z bazy danych DB2 do magazynu obiektów Blob. Jednak dane mogą być kopiowane do [wszystkie obsługiwane dane przechowywane typ ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania fabryki danych Azure.

Przykład zawiera następujące jednostek fabryki danych:

- Bazy danych DB2 połączonej usługi typu [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Połączonej usługi typu magazynu obiektów Blob platformy Azure [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- A [potoku](data-factory-create-pipelines.md) z działania kopiowania, która używa [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) właściwości

Przykład kopiuje dane z wyniku kwerendy w bazie danych DB2 do obiektów blob platformy Azure co godzinę. Właściwości JSON, które są używane w przykładowym są opisane w następnych sekcjach definicje jednostki.

Pierwszym krokiem Zainstaluj i skonfiguruj bramę danych. Instrukcje znajdują się w [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**Bazy danych DB2 połączone usługi**

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

**Azure Blob połączoną usługą magazynu**

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

Próbka przyjęto założenie, że utworzono tabelę w bazie danych DB2 o nazwie "MyTable", która zawiera kolumnę z etykietą "timestamp" dla czasu danych w serii.

**Zewnętrznych** właściwość jest ustawiona na wartość "true". Usługi fabryka danych z tego ustawienia informuje o tym, że ten zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych. Zwróć uwagę, że **typu** właściwość jest ustawiona na **RelationalTable**.


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

Dane są zapisywane do nowego obiektu blob co godzinę, ustawiając **częstotliwość** dla właściwości "Godzina" i **interwał** właściwości na wartość 1. **FolderPath** właściwości dla obiektu blob dynamicznie jest obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godzinę części czas rozpoczęcia.

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

**Potoku dla działania kopiowania**

Potok zawiera działanie kopiowania skonfigurowanym do używania określonych danych wejściowych i wyjściowych zestawów danych, które jest zaplanowane co godzinę. W definicji JSON dla potoku **źródła** ustawiono typ **RelationalSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **zapytania** właściwości wybiera danych z tabeli "Zamówienia".

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
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania przeprowadza automatyczne konwersje typu źródłowego do zbiornika typu przy użyciu następujących rozwinięcie:

1. Konwertować z typu natywnego źródła do typu .NET
2. Konwertować z typu .NET do typu sink natywnego

Następujące mapowania są używane, gdy działanie kopiowania konwertuje dane z bazy danych DB2 typu na typ architektury .NET:

| Typ bazy danych DB2 | Typ programu .NET framework |
| --- | --- |
| SmallInt |Int16 |
| Liczba całkowita |Int32 |
| BigInt |Int64 |
| Real |Pojedyncze |
| O podwójnej precyzji |O podwójnej precyzji |
| Liczba zmiennoprzecinkowa |O podwójnej precyzji |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Liczbowy |Decimal |
| Date |DateTime |
| Time |Zakres czasu |
| Sygnatura czasowa |DateTime |
| Xml |Byte[] |
| char |Ciąg |
| VarChar |Ciąg |
| LongVarChar |Ciąg |
| DB2DynArray |Ciąg |
| Binarny |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Grafika |Ciąg |
| VarGraphic |Ciąg |
| LongVarGraphic |Ciąg |
| CLOB |Ciąg |
| Obiekt blob |Byte[] |
| DbClob |Ciąg |
| SmallInt |Int16 |
| Liczba całkowita |Int32 |
| BigInt |Int64 |
| Real |Pojedyncze |
| O podwójnej precyzji |O podwójnej precyzji |
| Liczba zmiennoprzecinkowa |O podwójnej precyzji |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Liczbowy |Decimal |
| Date |DateTime |
| Time |Zakres czasu |
| Sygnatura czasowa |DateTime |
| Xml |Byte[] |
| char |Ciąg |

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Informacje na temat mapowanie kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Odczyty powtarzalne źródłami relacyjnymi
Po skopiowaniu danych z magazynu danych relacyjnych należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować retry **zasad** właściwości dla obiektu dataset ponownie uruchomić wycinek, gdy wystąpi błąd. Upewnij się, że te same dane jest odczytu, niezależnie od tego, ile razy wycinek zostanie uruchomiona ponownie, i niezależnie od tego, jak ponownie uruchomić wycinka. Aby uzyskać więcej informacji, zobacz [Repeatable odczytuje z źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Więcej informacji na temat kluczowych czynników wpływających na wydajność działania kopiowania i sposobów w celu optymalizacji wydajności w [wydajności działania kopiowania i dostrajania przewodnik](data-factory-copy-activity-performance.md).
