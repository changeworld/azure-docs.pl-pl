---
title: Mapowanie schematu w działaniu kopiowania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu działania kopiowania w usłudze Azure Data Factory mapowania schematów i typy danych ze źródła danych do ujścia danych podczas kopiowania danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875801"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapowanie schematu w działaniu kopiowania

W tym artykule opisano, jakie działania kopiowania w usłudze Azure Data Factory mapowanie schematu i mapowanie typu danych ze źródła danych do ujścia danych podczas wykonywania kopii danych.

## <a name="schema-mapping"></a>mapowanie schematu

Odnosi się to mapowanie kolumn podczas kopiowania danych ze źródła do ujścia. Działanie kopiowania domyślnie **mapowania danych źródła do ujścia przy użyciu nazw kolumn**. Można określić [jawnego mapowania](#explicit-mapping) Dostosowywanie mapowania kolumn, zgodnie z potrzebami. W szczególności działania kopiowania:

1. Odczytywać dane ze źródła i określić schematu źródłowego
2. Umożliwia mapowanie kolumny domyślne mapowanie kolumn według nazwy lub zastosować mapowanie kolumn jawne, jeśli określony.
3. Zapisywanie danych do ujścia

### <a name="explicit-mapping"></a>Jawnego mapowania

Można określić kolumn do mapowania w działaniu kopiowania -> `translator`  ->  `mappings` właściwości. W poniższym przykładzie zdefiniowano działania kopiowania w potoku w celu skopiowania danych z tekstu rozdzielanego do usługi Azure SQL Database.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

Następujące właściwości są wspierane w ramach `translator`  ->  `mappings` -> obiekt z `source` i `sink`:

| Właściwość | Opis                                                  | Wymagane |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nazwa kolumny źródła lub ujścia.                           | Yes      |
| Liczba porządkowa  | Indeks kolumny. Zacznij od 1. <br>Zastosuj i wymagany, gdy za pomocą rozdzielany tekst bez nagłówków wiersza. | Nie       |
| ścieżka     | Wyrażenie ścieżki JSON dla każdego pola, do wyodrębnienia lub mapy. Mają zastosowanie dla danych hierarchicznych np. bazy danych MongoDB REST.<br>W przypadku pól w obiekcie głównym ścieżkę JSON zaczyna się od głównego $; w przypadku pól wewnątrz tablicy wybranej przez `collectionReference` właściwości JSON ścieżka zaczyna się od elementu tablicy. | Nie       |
| type     | Fabryka danych tymczasowych typ danych kolumny źródła lub ujścia. | Nie       |
| Kultury  | Kultura kolumny źródła lub ujścia. <br>Są stosowane, gdy typ jest `Datetime` lub `Datetimeoffset`. Wartość domyślna to `en-us`. | Nie       |
| format   | Format ciągu ma być używany, gdy typem jest `Datetime` lub `Datetimeoffset`. Zapoznaj się [Custom Date and Time Format Strings](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) w sposób formatowania daty/godziny. | Nie       |

Następujące właściwości są wspierane w ramach `translator`  ->  `mappings` oprócz obiektu z `source` i `sink`:

| Właściwość            | Opis                                                  | Wymagane |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Obsługiwane tylko wtedy, gdy dane hierarchiczne, np. bazy danych MongoDB REST jest źródła.<br>Jeśli chcesz wykonać iterację i ekstrakcję danych z obiektów **wewnątrz pola tablicy** przy użyciu tego samego wzorca i przekonwertować każdego wiersza dla każdego obiektu, określ ścieżkę JSON tej tablicy cross-zastosować. | Nie       |

### <a name="alternative-column-mapping"></a>Mapowanie kolumn alternatywnego

Można określić kopiowania activity -> `translator`  ->  `columnMappings` do mapowania danych tabelarycznych w kształcie. W tym przypadku "strukturę" sekcja jest wymagana do wejściowe i wyjściowe zestawy danych. Obsługuje mapowanie kolumny **mapowania wszystkich lub podzestawu kolumn w zestawie danych źródłowych "strukturę" do wszystkich kolumn w zestawie danych ujścia "strukturę"**. Dostępne są następujące warunki błędów, które powoduje wyjątek:

* Zapytanie, że wynik nie jest nazwa kolumny, które jest określone w sekcji "strukturę" wejściowy zestaw danych magazynu danych źródłowych.
* Magazyn danych ujścia (Jeśli za pomocą wstępnie zdefiniowany schemat) nie ma nazwę kolumny, który jest określony w sekcji "strukturę" wyjściowego zestawu danych.
* Mniejszą liczbę kolumn lub większą liczbę kolumn "Struktura" zestaw danych ujścia niż określony w mapowaniu.
* Zduplikowane mapowania.

W poniższym przykładzie wejściowego zestawu danych ma strukturę i wskazuje do tabeli w bazie danych Oracle w środowisku lokalnym.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

W tym przykładzie wyjściowy zestaw danych ma strukturę i wskazuje z tabelą w Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Następujący kod JSON definiuje działania kopiowania w potoku. Kolumny ze źródła mapowane na kolumny w ujściu przy użyciu **translator** -> **columnMappings** właściwości.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Jeśli używasz składni `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` do określenia mapowania kolumn, nadal możliwe jest jako-to.

### <a name="alternative-schema-mapping"></a>Alternatywny schemat mapowania

Można określić kopiowania activity -> `translator`  ->  `schemaMapping` do mapowania miedzy hierarchiczne ukształtowane dane i tabelarycznym kształcie, np. skopiuj z REST bazy danych MongoDB do pliku tekstowego i kopiowanie danych z bazy danych Oracle do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Następujące właściwości są obsługiwane w działaniu kopiowania `translator` sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type translator aktywności kopiowania musi być równa: **TabularTranslator** | Yes |
| schemaMapping | Kolekcji pary klucz wartość, która reprezentuje relację mapowania **po stronie źródła do ujścia po stronie**.<br/>- **Klucz:** reprezentuje źródło. Aby uzyskać **tabelaryczne źródła**, określ nazwę kolumny, zgodnie z definicją w strukturze zestawu danych; w przypadku **źródła w hierarchiczny**, określa wyrażenie ścieżki JSON dla każdego pola wyodrębnić i mapy.<br>- **Wartość:** reprezentuje ujścia. Dla **tabelarycznych ujścia**, określ nazwę kolumny, zgodnie z definicją w strukturze zestawu danych; w przypadku **hierarchiczne ujścia**, określa wyrażenie ścieżki JSON dla każdego pola wyodrębnić i mapy. <br>W przypadku danych hierarchicznych, dla pola w obiekcie głównym ścieżkę JSON zaczyna się od głównego $; w przypadku pól wewnątrz tablicy wybranej przez `collectionReference` właściwości JSON ścieżka zaczyna się od elementu tablicy.  | Yes |
| collectionReference | Jeśli chcesz wykonać iterację i ekstrakcję danych z obiektów **wewnątrz pola tablicy** przy użyciu tego samego wzorca i przekonwertować każdego wiersza dla każdego obiektu, określ ścieżkę JSON tej tablicy cross-zastosować. Ta właściwość jest obsługiwana tylko wtedy, gdy źródło danych hierarchicznych. | Nie |

**Przykład: kopiowanie danych z bazy danych MongoDB do bazy danych Oracle:**

Na przykład, jeśli masz bazy danych MongoDB dokumentów o następującej zawartości:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

i chcesz skopiować do tabeli usługi Azure SQL w następującym formacie, spłaszczając dane wewnątrz tablicy *(order_pd i order_price)* i cross join ze wspólnymi informacjami głównymi *(liczba, Data i miasta)* :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Skonfiguruj reguły mapowania schematu jako następujący przykładowy kod JSON aktywności kopiowania:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapowanie typu danych

Działanie kopiowania wykonuje operację typów źródła do ujścia typy mapowania przy użyciu następujących podejść krok 2:

1. Konwertowanie na typy danych tymczasowych usługi Azure Data Factory typy natywne źródła
2. Konwertuj z typów danych tymczasowych usługi Azure Data Factory na typ ujścia natywne

Można znaleźć mapowania między typem natywnym typowi przejściowym w sekcji "Mapowanie typu danych" w każdym temacie łącznika.

### <a name="supported-data-types"></a>Obsługiwane typy danych

Usługa Data Factory obsługuje następujące typy danych tymczasowych: Możesz określić poniższe wartości podczas konfigurowania informacji o typie w [struktury zestawu danych](concepts-datasets-linked-services.md#dataset-structure-or-schema) konfiguracji:

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Zakres czasu

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
