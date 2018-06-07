---
title: Przenoszenie danych z PostgreSQL przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z bazy danych PostgreSQL przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1d566a719abaf09146965a677868d363d33d10e5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622768"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Przenoszenia danych z PostgreSQL przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-onprem-postgresql-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-postgresql.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [PostgreSQL łącznika w wersji 2](../connector-postgresql.md).


W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z lokalną bazą danych PostgreSQL. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Można skopiować danych z lokalnego magazynu danych PostgreSQL żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Fabryka danych obecnie obsługuje przenoszenia danych z bazy danych programu PostgreSQL do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do bazy danych programu PostgreSQL. 

## <a name="prerequisites"></a>Wymagania wstępne

Usługi fabryka danych obsługuje połączenia ze źródłami PostgreSQL lokalnymi przy użyciu bramy zarządzania danymi. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy.

Wymagana jest brama, nawet wtedy, gdy baza danych programu PostgreSQL znajduje się w maszynie Wirtualnej platformy Azure IaaS. Możesz zainstalować bramę na tej samej maszyny Wirtualnej IaaS do przechowywania danych lub w innej maszyny Wirtualnej tak długo, jak bramy można połączyć z bazą danych.

> [!NOTE]
> Zobacz [rozwiązywania problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy/połączenia problemy związane z.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Brama zarządzania danymi do łączenia z bazą danych PostgreSQL, można zainstalować [Ngpsql dostawcy danych PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) z wersją między 2.0.12 i 3.1.9 na tym samym systemie co brama zarządzania danymi. PostgreSQL wersji 7.4 i nowszej jest obsługiwana.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z magazynu danych PostgreSQL lokalnymi przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych. 
- Można także użyć następujących narzędzi można utworzyć potoku: 
    - Azure Portal
    - Visual Studio
    - Azure PowerShell
    - Szablon usługi Azure Resource Manager
    - Interfejs API .NET
    - Interfejs API REST

     Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Przykładowy z definicji JSON dla jednostek fabryki danych, które służą do kopiowania danych z lokalnego magazynu danych PostgreSQL [przykład JSON: kopiowanie danych z PostgreSQL do obiektów Blob platformy Azure](#json-example-copy-data-from-postgresql-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej w magazynie danych PostgreSQL:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla usługi PostgreSQL połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **OnPremisesPostgreSql** |Yes |
| serwer |Nazwa serwera PostgreSQL. |Yes |
| baza danych |Nazwa bazy danych PostgreSQL. |Yes |
| Schemat |Nazwa schematu w bazie danych. Nazwa schematu jest rozróżniana wielkość liter. |Nie |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych PostgreSQL. Możliwe wartości to: anonimowe, podstawowe i systemu Windows. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych PostgreSQL. |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów w zestawie danych.

Sekcja typeProperties jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **RelationalTable** (w tym zestawie danych PostgreSQL) ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych PostgreSQL, odnoszący się do połączonej usługi. Właściwość tableName jest rozróżniana wielkość liter. |Nie (Jeśli **zapytania** z **RelationalSource** jest określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania różnić się z każdym typem działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Jeśli źródło jest typu **RelationalSource** (która obejmuje PostgreSQL), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nie (Jeśli **tableName** z **dataset** jest określona) |

> [!NOTE]
> Nazwy schematu i tabeli jest rozróżniana wielkość liter. Umieść je w `""` (podwójne cudzysłowy) w zapytaniu.  

**Przykład:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Przykład JSON: kopiowanie danych z PostgreSQL do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych z bazy danych PostgreSQL do magazynu obiektów Blob Azure. Jednak można skopiować danych do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.   

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje krok po kroku.

Przykład zawiera następujące obiekty fabryki danych:

1. Połączonej usługi typu [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku kwerendy w bazie danych PostgreSQL do obiektu blob co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

Pierwszym krokiem należy skonfigurować bramę zarządzania danymi. Instrukcje znajdują się w [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**PostgreSQL połączonej usługi:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
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
**Magazyn obiektów Blob Azure połączonej usługi:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
    }
    }
}
```
**Wejściowy zestaw danych PostgreSQL:**

Przykładzie przyjęto założenie, utworzysz tabelę "MyTable" PostgreSQL i zawiera kolumnę o nazwie "timestamp" dla czasu serii danych.

Ustawienie `"external": true` usługi fabryka danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**W potoku z działania kopiowania:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **RelationalSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **zapytania** właściwości wybiera danych z tabeli public.usstates w bazie danych programu PostgreSQL.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Mapowanie typu dla PostgreSQL
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu działanie kopiowania przeprowadza automatyczne konwersje z typów źródła do zbiornika typów o następujące podejście krok 2:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych do PostgreSQL, następujące mapowania są używane z PostgreSQL typu na typ .NET.

| Typ bazy danych PostgreSQL | Aliasy PostgresSQL | Typ programu .NET framework |
| --- | --- | --- |
| abstime | |Data/godzina | &nbsp;
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bitowe [(n)] | |Byte [], ciąg | &nbsp;
| bit zróżnicowanie [(n)] |varbit |Byte [], ciąg |
| wartość logiczna |wartość logiczna |Wartość logiczna |
| box | |Byte [], ciąg |&nbsp;
| bytea | |Byte [], ciąg |&nbsp;
| znak [(n)] |char [(n)] |Ciąg |
| znak zróżnicowanie [(n)] |varchar [(n)] |Ciąg |
| CID | |Ciąg |&nbsp;
| CIDR | |Ciąg |&nbsp;
| koło | |Byte [], ciąg |&nbsp;
| data | |Data/godzina |&nbsp;
| DateRange | |Ciąg |&nbsp;
| podwójnej precyzji |FLOAT8 |O podwójnej precyzji |
| inet | |Byte [], ciąg |&nbsp;
| intarry | |Ciąg |&nbsp;
| int4range | |Ciąg |&nbsp;
| int8range | |Ciąg |&nbsp;
| liczba całkowita |int, int4 |Int32 |
| Interwał [pola] [(p)] | |Zakres czasu |&nbsp;
| json | |Ciąg |&nbsp;
| jsonb | |Byte[] |&nbsp;
| wiersz | |Byte [], ciąg |&nbsp;
| lseg | |Byte [], ciąg |&nbsp;
| macaddr | |Byte [], ciąg |&nbsp;
| oszczędność pieniędzy | |Decimal |&nbsp;
| numeryczne [(p, s)] |decimal [(p, s)] |Decimal |
| numrange | |Ciąg |&nbsp;
| Identyfikator OID | |Int32 |&nbsp;
| ścieżka | |Byte [], ciąg |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| punkt | |Byte [], ciąg |&nbsp;
| wielokąta | |Byte [], ciąg |&nbsp;
| rzeczywiste |FLOAT4 |Pojedyncze |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| numer seryjny |serial4 |Int32 |
| tekst | |Ciąg |&nbsp;

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
